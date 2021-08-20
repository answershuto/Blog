## 写在前面

大家好，我是[染陌](https://github.com/answershuto)，这是我在 [全球开源技术峰会 GOTC](https://gotc.oschina.net/) 上的一个 topic ——《基于 Flutter 的 Web 渲染引擎「北海 Kraken」》。我主要从技术角度来分享 Kraken 的一些实现原理以及关键的技术特性，现在整理成文字版分享给大家。



Kraken Github：[https://github.com/openkraken/kraken](https://github.com/openkraken/kraken)



Kraken 官网：[https://openkraken.com/](https://openkraken.com/)



## 北海的技术背景

说到北海的技术背景就不得不提及跨端技术的演进，很多同学应该都比较熟悉跨端技术的历程了，我还是简单讲一些。

我们知道，浏览器是最成熟的天然跨平台方案。早在 PC 时代，浏览器已经成为了互联网的入口，大家都会习惯性通过浏览器来进行网页的浏览以汲取各种信息，当时我们把这种上网的方式叫做“冲浪”。然而到了移动时代，浏览器在移动设备上并没有一个抢眼的表现，反之因为内存大、弱网环境白屏久、传感器能力缺失（标准跟进慢）等问题使各种质疑不绝。

为了弥补上述浏览器在移动端的一些不足，出现了 Hybrid 技术，在 Web 之上通过容器的能力实现一些非标准化的超集，同时也通过 prefetch、离线包等各种技术来提升首屏的加载性能。

此后，出现了类 RN 的方案（典型代表 React Native），它的原理是通过 JS engine 将 Native 控件与前端生态实现一个桥接，通过 Web 开发业务逻辑提升效率，而向下通过 Native 控件渲染来提升性能及体验。但是这类方案的缺点是无法完全抹平两端的差异，没有解决一致性的问题，而最终将复杂度暴露给了开发者。



![](https://img.alicdn.com/imgextra/i3/O1CN01gqgaIi1UqeByZpOXY_!!6000000002569-0-tps-8000-4500.jpg)



Flutter 作为跨端届的新宠，这两年也获得了越来越多的关注，下面介绍一下 Flutter。



![](https://img.alicdn.com/imgextra/i2/O1CN01iZ2dAx1EzmEdGLS27_!!6000000000423-0-tps-8000-4500.jpg)



Flutter 的优点是性能好、由于其通过自绘渲染使得跨端一致性高，但是它也有它自身的缺点，比如生态自成一派，既不是前端也不是 Android/iOS。



这就是引出了一系列的问题。

* 首先，前端（JavaScript）或客户端（Swift / JAVA）转型都有一定成本，但是由于端侧的 GUI 体系大同小异。笔者站在一个前端开发者的视角去看语言上的学习成本并不会特别高，有 React 或者 Vue 等前端框架经验的同学可以通过简单的学习快速上手。对于一些小型的创业团队，确实可以小步快跑快速学习上手并开发，但当组织庞大到一定程度，这个转换的成本将会指数级上升。

* 其次，生态圈等待重新建设，一些 Flutter 开发者朋友或许觉得目前 Flutter 开发已经有挺多的 pub 可以直接使用了。但实际上生态圈不止于 Flutter pub，还有各种已有的基础链路，比如建设相关的 CI/CD，再比如搭建等等。这一系列的生态都需要重新建设，成本是非常大的。

* 再次，已有的非常多业务都是通过 JavaScript + 前端框架开发的前端项目，我们如果想把它们迁移成 Dart + Widget 成本无疑是非常庞大的。

  

在面对如此多的问题以及切换的高成本的同时，我们也期望通过 Flutter 给我们的业务带来更多的技术的可能性，同时改善 Web 容器在端上的一些性能及体验问题。那么，*引入一项新技术的第一步是解决引入这项新技术的成本问题*，所以我们积极探索一种将前端生态与 Flutter 结合起来的方案。



![](https://img.alicdn.com/imgextra/i1/O1CN01YS3crQ1xH2Fhip0ZX_!!6000000006417-0-tps-8000-4500.jpg)



于是产生了本次 topic 的主角——北海 Kraken。



Kraken 是一款高性能 Web 标准的自绘渲染引擎，具有高性能、易扩展、基于 Flutter 以及 遵守 Web 标准的特点。



![](https://img.alicdn.com/imgextra/i3/O1CN012T9Czg1pSvbwpQrc7_!!6000000005360-0-tps-8000-4500.jpg)



下面我列举了一些北海在阿里的一些应用场景，在 C 端 APP 或者 IoT 设备上，北海都有相关的落地。



![](https://img.alicdn.com/imgextra/i4/O1CN01t6jLXr1TtZ6IuMLN3_!!6000000002440-2-tps-1014-574.png)



## 北海的技术原理

在介绍 Kraken 的技术原理之前，我先演示一下如何开发一个 Kraken 应用。因为 Kraken 是基于 W3C 标准来开发的 Web 渲染引擎，所以上层是框架无关的，无论开发者使用的是 Vue 或者 React 还是 Rax 都可以在 Kraken 上进行一个应用开发。



以 Vue.js 开发为例，下面是我用 Vue 官方提供的 vue-cli 起的一个项目。具体的代码见[官方示例](https://github.com/openkraken/samples/tree/main/demos/hello-vue)。

可以看到的是，最左边是 Vue 的相关代码，右边分别是该应用在 Chrome（左）上跑的结果以及在 Kraken（右）上跑的结果，大家可以看到结果是完全一致的。 



![](https://img.alicdn.com/imgextra/i1/O1CN011QDzXY1T8qhPR8FGY_!!6000000002338-0-tps-8000-4500.jpg)



了解了如何开发一个 Kraken 应用 ，我们再来理解一下 Kraken 的技术原理。为了大家更好地理解，首先我们来比较一下 Flutter 于 Webview 的渲染流程。



WebView 的渲染流程相信大家非常熟悉了，面试中非常经典的题目就是一个 URL 输入如何最终渲染到屏幕上了。总的来说就是解析 HTML、JS 以及 CSS 文件，执行相应 JS 调用 DOM API，最终会生成 DOM Tree 以及 CSSOM Tree，然后会计算最终得到 Render Tree，经过 Layout 以及 Paint 流程生成一系列的 Layer，最终通过合成以及光栅化渲染到屏幕上。



再看 Flutter 这边，Flutter 经典的三棵树——Widget Tree、Element Tree 以及 RenderObject Tree。Widget Tree 对应到前端类似于前端框架这层，而 Element 与 DOM Tree，RenderObject Tree 与 Render Tree 分别对应，最终也会通过 Layout 以及 Paint 一系列计算生成 Layer，然后通过合成以及光栅化渲染到屏幕上。



![](https://img.alicdn.com/imgextra/i3/O1CN010q3VsK1nhvLVUgKFF_!!6000000005122-0-tps-8000-4500.jpg)



那么，我们再将前端框架加入到我们整个流程中进行一个更加直观的对比，这里还是以 Vue.js 为例。

Vue.js 会在运行时生成一系列 Vdom 产生 Vdom Tree，再通过 platfom 的抽象调用具体平台的 API。



![](https://img.alicdn.com/imgextra/i1/O1CN01HRszbU1FwOvmpSDkj_!!6000000000551-0-tps-8000-4500.jpg)



那么我们就会发现，只需要把我用红框圈出来的部分的流程进行互换，就可以实现我们最终想要实现的效果（上层 Web 开发，下层基于 Flutter 进行渲染）。



![](https://img.alicdn.com/imgextra/i3/O1CN01wyRRUf1MA2Q1IfQxB_!!6000000001393-0-tps-8000-4500.jpg)



基于以上设想，那么北海的渲染流程就出来了。



目前主流的前端框架都会将产物打成一个 JS Bundle，通过标准的 DOM API 去操作具体的视图，而 HTML 内一般只有一个根结点。在 Web 下，页面会先请求 HTML 文件，再解析 Script 标签去加载对应的 JS 文件。而 Kraken 的入口设计成了一个 JS 文件，这样做可以减少一次请求，加快首屏的渲染。



该 JS 文件会在 JS Engine 中执行，Kraken 的 runtime 通过 JS Engine Binding 的方式提供了一系列 Web 标准的 API 接口，调用相应 API 会执行相关逻辑并创建一系列需要发送给 Dart 层处理的指令，指令通过一个 struct 进行存储。C++ 通过 FFI 将相应的指令底层的 address 发送到 Dart 这边，Dart 处理相关指令并生成 Dom Tree。同样的，CSS 也会通过 Parser 生成对应的 CSSOM Tree，最终会结合生成 Flutter 的 RenderObject Tree，经过 Layout 以及 Paint 的一系列计算，生成对应的一系列 Layter，然后通过合成光栅化最终上屏显示。



同样的，在最新的实现中，我们考虑到了 SSR 应用的场景，所以加入了 HTML 为入口的北海应用开发方式，通过 HTML Parser 即可解析对应的 HTML 文件，后续流程是一样的。SSR 的支持也让首屏的秒开率更上一层楼。



![](https://img.alicdn.com/imgextra/i1/O1CN01WgTBJA1wDXcBX8MzI_!!6000000006274-0-tps-8000-4500.jpg)



那么了解了 Kraken 的整个渲染流程，那么我们如何基于 Flutter 去完成 Web 标准的渲染引擎的开发呢？



![](https://img.alicdn.com/imgextra/i3/O1CN01az6nMa1KbrFD2Jeri_!!6000000001183-0-tps-8000-4500.jpg)



那么要基于 Flutter 去做这个事情，就必须先了解 Flutter 的架构。



Flutter 最上层是 Dart 实现的 Framework，包含了响应式框架、官网组件库以及实现布局与绘制协议的部分。中间是 C++ 实现的 Flutter Engine，他是渲染流程的下半部分，提供了一些基础能力，以及将 layer 合成以及光栅化后输出。最下层的 Embedder 层，则负责具体 platform 的一些实现，以实现跨平台。



![](https://img.alicdn.com/imgextra/i2/O1CN01rG5Vgn1xMX0OZzFgf_!!6000000006429-0-tps-8000-4500.jpg)



不难发现，最 Dart Framework 的 Widget 是对 UI 的抽象，实现了一套响应式框架，对应到前端就是 Vue / React 等前端框架。而下方的布局协议，可以对应 W3C 的标准来实现一套基于前端标准的布局与绘制协议。



![](https://img.alicdn.com/imgextra/i1/O1CN01b5Gspu23opAVMte9P_!!6000000007303-0-tps-8000-4500.jpg)



那么我们就可以得出北海的架构设计。



先看左边，左边还是上面介绍的 Flutter 的整体架构，Flutter 的 Widget 能力可以通过插件的形式注册到 Kraken 中去，成为一个前端标准的 Tag，JS 可以动态化地调用及控制渲染。整个左侧的 Flutter 架构支撑了上层的 Flutter 生态，使 Flutter 生态也可以通过插件的形式融合到整个 Kraken 的渲染体系中去。



右边是 Kraken 的架构实现，Kraken 的实现并没有把实现侵入到 Flutter Engine 中去。在 Dart 层，通过实现 W3C 标准的一系列布局与渲染能力，为上层提供了一些列标准化的能力，比如 Element、CSS、以及各种 Web 标准的 Module 等。在上层 Kraken 的 runtime 通过 JS Engine Binding 的方式提供了一系列 Web 标准的 API 接口，调用相应 API 会执行相关逻辑并创建一系列需要发送给 Dart 层处理的指令，指令通过一个 struct 进行存储。C++ 通过 FFI 将相应的指令底层的 address 发送到 Dart 这边，最终 Dart 根据指令调用前面说的标准化能力，以完成对接。通过该实现，为上层的前端生态提供了支撑，凭借丰富的前端生态，开发者可以享受前端生态带来的高效的开发体验。



![](https://img.alicdn.com/imgextra/i3/O1CN013NHoul1frz7bP6XtK_!!6000000004061-0-tps-8000-4500.jpg)



## 关键技术特性

首屏的加载性能是一个 C 端场景的关键指标，长时间的白屏会极大地影响用户体验。

Kraken 在 首屏初始化时需要创建大量的节点，大量的时间耗费在通信上，所以优化首屏性能迫在眉睫。



![](https://img.alicdn.com/imgextra/i3/O1CN01QT1hfD1tjeXKngT2c_!!6000000005938-0-tps-8000-4500.jpg)



在上面技术原理部分我们知道，Kraken 需要通过 Bridge 来完成 C++（JS Engine） 与 Dart 之间的通信，以达到将指令传递到 Dart 层的目的，Bridge 的架构也进行了三个版本的演进。



最初的第一代方案，我们侵入了 Flutter Engine，使数据从 JS Engine 传递到 Flutter Engine 中，然后通过 native bingding 最终将数据发送给 Dart 层。这一代的方案非常明显的缺点是侵入了 Flutter Engine，开发时需要编译 Flutter Engine 需要耗费大量的时间。同时，对于 Kraken 的架构来说，侵入 Flutter Engine 也并不是一个合理的设计。



后来出现了 Dart FFI，可以实现 C++ 与 Dart 之间的高效通信，所以产生了第二代方案。第二代 Bridge 方案通过将 JSON 数据序列化后，通过 Dart FFI 将数据传递到 Dart 层，Dart 层再通过 JSON 的反序列化以拿到最终的数据。这代方案比起上一代方案可以解决侵入 Flutter Engine 的缺点，但是引入了字符串的拷贝以及 JSON 序列化反序列化的时间长的问题。



![](https://img.alicdn.com/imgextra/i4/O1CN01BQOO4G1sTKoGdkzcq_!!6000000005767-0-tps-8000-4500.jpg)



为了解决上述问题，于是产生了第三代 Bridge 方案。第三代 Bridge 方案通过共享内存的方式定义了一个标准的 40 Bytes 的 Struct 来存储指令，而通过 Dart FFI 传递的只是指令的地址，C++ 跟 Dart 两边都依赖地址来访问相关数据。这样做解决了 JSON 序列化反序列化的问题，节约了时间，并且少一次数据拷贝。同时，由于内存是 40 Bytes 对齐的，可以提高内存的访问效率。



![](https://img.alicdn.com/imgextra/i4/O1CN01RXM5ND1iCFspH6bb6_!!6000000004376-0-tps-8000-4500.jpg)



下面是一些实际线上页面带来的首屏收益。



![](https://img.alicdn.com/imgextra/i4/O1CN01lrXIQB1TZs4GnJp5Z_!!6000000002397-0-tps-8000-4500.jpg)



无限滚动的长列表是困扰前端开发者很久的历史性问题了，大量的 layout 导致页面卡顿，以及滚动时 Paint 的时间长导致滚动掉帧，页面的体验非常糟糕。社区也有非常多的前端的解决方案来处理该问题，而在 Kraken 上，我们也期望在容器层解决该问题。



![](https://img.alicdn.com/imgextra/i4/O1CN01WMIWOR28ZJq0aVIQ2_!!6000000007946-0-tps-8000-4500.jpg)



在 Android 跟 iOS 上也分别有 RecyclerView 以及 TableView 来解决该问题，他们的原理分别是在可视区域 viewport 外定义一块缓冲区域，当节点超过该区域时进行动态释放，进入该区域时动态创建，以及通过一系列节点进行属性替换的方式来保证节点数不爆炸。Flutter 中也提供了类似实现 Sliver，那么我们能否用 Sliver 赋能前端解决该问题呢？



![](https://img.alicdn.com/imgextra/i1/O1CN01PLfZmu1iTevs1s2aR_!!6000000004414-0-tps-8000-4500.jpg)



Kraken 定义了一个新的 `display` 属性 `sliver`，通过将节点的 `display` 属性设置为 `sliver`，则可以直接使用 Flutter 的 Sliver 能力，以达到节点超出可视及缓存区域后动态回收的一个能力。可以看到我们使用 1000 个卡片的 DEMO 进行测试，`sliver` 下比起 `block` 有明显的收益。



同时，该标准也已经在 [W3C 中文兴趣小组](https://github.com/w3c/chinese-ig/issues/239) 进行了讨论，期望在大家讨论充分以及达成共识后，尝试将此提案向 W3C 进行提交，反哺前端社区。



![](https://img.alicdn.com/imgextra/i4/O1CN01dmviLm1vSpDXh2Lem_!!6000000006172-0-tps-8000-4500.jpg)

![](https://img.alicdn.com/imgextra/i4/O1CN01tbtMpg1wQp6InJRGi_!!6000000006303-0-tps-8000-4500.jpg)



一个大前端团队往往既有客户端也有前端，会沉淀一系列的端上的能力。不同的需求会有不一样的技术选型，譬如说一个播放器往往是通过 Native 技术去开发的。我们期望将端上的能力（包括 Flutter Widget、Web、Native 以及三方 SDK 等）进行整合，融合成一个大前端的端开发体系，所以在 Kraken 内我们如何整合端上的这一系列能力呢？同时，我们也期望按需引入，能做到包体积的优化。在不同的业务域，我们期望可以快速地进行定制化开发，快速形成一套垂直业务域的领域能力。



![](https://img.alicdn.com/imgextra/i3/O1CN01GEkCRk1xwFtqAHU22_!!6000000006507-0-tps-8000-4500.jpg)



Kraken 提供了一套扩展能力来解决上述问题，通过渲染能力扩展接口，开发者可以将开发完成的符合标准的 Flutter Widget 以及 Native 的渲染能力快速集成到 Kraken 体系中去，最终通过 JavaScript 来提供一个动态化调用的能力。同样的，通过 MethodChannel，开发者可以通过该通道调用一些 Native 或者 Dart API 的能力，譬如说一些二方或者三方的 SDK 能力。



开发者可以通过扩展能力自定义业务域需要的能力，按需拔插以达到包体积优化的目的。同样的，注册到 kraken 的插件都可以通过 JavaScript 代码控制，提供了动态性。



![](https://img.alicdn.com/imgextra/i3/O1CN0144wmu91Y802tKb1lN_!!6000000003013-0-tps-8000-4500.jpg)



下面是一系列在 Kraken 内部扩展 Flutter Widget、Native API 以及 Native 播放器的 Demo。



![](https://img.alicdn.com/imgextra/i4/O1CN01cpbwnC1Od0hgjtT0K_!!6000000001727-0-tps-8000-4500.jpg)



下面是提升可交互性。在介绍 Kraken 的可交互性之前，我们先来看一下在 Web 下的一些交互问题。



在 Web 下开发富交互能力的应用时，前端开发者往往需要引入一个额外的 lib 来提供增强的手势能力（譬如说 Hammer.js 这样的手势库）。那么当前端开发者引入 lib 时，就会导致加载 index.html 以后，还需要额外的请求对应的 JS 库，造成一次额外的请求开销的同时延长了首屏的可交互时间。



当用户在屏幕上进行某个操作时，由于用户操作的方式可能是用户的手，也可能是 Apple Pencil 或者鼠标这样的设备。所以在 W3C 标准中，将用户操作可交互应用的触点抽象为一个 [pointer](https://www.w3.org/TR/pointerevents3/)，这些 pointer 会根据操作形成一个手势，分别是 down、move、up 三个过程，其中 move 可省略（譬如说 click）。



在 Web 中，需要将这一系列 pointer 给 dispatch 到 element tree 上，通过冒泡将这些 pointer 频繁地发送到 JS 层，然后 JS 再通过封装 Touch API 来完成对交互的识别。这样做带来几个问题，首先频繁地将 pointer 从 C++ 传递到 JS 带来了不必要的开销，此外封装标准的能力也会造成额外的开发成本，易用性并不突出。此时，如果使用社区的一些方案，也会导致非标准化使标准不对齐导致同个应用中的不同页面有不一致的交互体验。



![](https://img.alicdn.com/imgextra/i4/O1CN01HlKs9M1yZBZ0euNcX_!!6000000006592-0-tps-8000-4500.jpg)



为了解决上述问题，我们期望从标准化、易用性、标准化几个方面提供一套标准化的交互能力。通过封装底层的 pointer 来得到不同的手势能力，使开发者可以快速开发富交互的应用。



![](https://img.alicdn.com/imgextra/i2/O1CN0129WN1s1DtzcGM8ywr_!!6000000000275-0-tps-8000-4500.jpg)



下面是 Kraken 中增强交互能力的流程图。当用户进行某些交互操作以后，每一个触点的 pointer 会从 Native 传递到 Kraken 中，Pointer 会同时分发给 GestureManager（手势识别器管理类）以及 Scroll 识别器。GestureManager 会识别开发者通过 Web 标准的监听行为（EventTarget.addEventListener）来注册以及分发给对应的手势识别器，同样 Scroll 识别器也会被分发 pointer。这些识别器被加入到 Flutter 的竞争场进行手势竞争，以保证只触发某一个具体操作（交互可控）。Scroll 识别器会触发滚动区域的滚动操作，手势识别器则会通过标准的 Web 流程进行冒泡以及 dispatch，最终开发者通过监听事件完成自定义行为。



![](https://img.alicdn.com/imgextra/i1/O1CN01xPV6dp1mPJdjPkUK3_!!6000000004946-0-tps-8000-4500.jpg)



开发应用时，调试能力是必不可少的，前端开发效率高不止要归功于繁荣的生态，友好的开发调试体验一样是提升效率的神器。



![](https://img.alicdn.com/imgextra/i1/O1CN01PF1KQs1DbDNTNBg8Q_!!6000000000234-0-tps-8000-4500.jpg)



Kraken 抽象了 Inspector 以通过 Chrome DevTools Protocol 来对接 Chrome DevTools，提供了一系列跟前端开发 Web 应用完全一致的调试体验，无论开发者喜欢使用 Console.log 还是通过 JS Debugger ，都可以快速上手。



![](https://img.alicdn.com/imgextra/i4/O1CN01mw0qIS1nCK1Fb6C2r_!!6000000005053-0-tps-8000-4500.jpg)

![](https://img.alicdn.com/imgextra/i3/O1CN01LXun9524WKnIJBcAV_!!6000000007398-0-tps-8000-4500.jpg)



此外，Kraken 也通过支持 HMR 的所有标准的 Web API，来提供局部热更新的能力，使开发 Kraken 应用能跟 Web 下一致的局部热更新的调试体验，大大提升了开发者的开发调试体验。



![](https://img.alicdn.com/imgextra/i3/O1CN010eZQwr1QXdHpyDI3z_!!6000000001986-0-tps-8000-4500.jpg)



最后，Kraken 的所有代码都已经开源，Kraken 提供了开放的 TSC 机制期望所有开发者可以平等地交流以及决策，使 Kraken 可以更好地发展，也欢迎更多的开发者一起来共建 Kraken。



Kraken Github：[https://github.com/openkraken/kraken](https://github.com/openkraken/kraken)



Kraken 官网：[https://openkraken.com/](https://openkraken.com/)

