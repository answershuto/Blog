阿里巴巴历时 3 年自研开发的 **Web 渲染引擎** 北海（英文名：Kraken）正式开源，致力打造易扩展，跨平台，高性能的渲染引擎，并已在优酷、大麦、天猫等业务场景中使用。

官网：[https://openkraken.com](https://openkraken.com)

Github：[https://github.com/openkraken/kraken](https://github.com/openkraken/kraken)

## 背景

互联网业务如火如荼地发展离不开跨平台技术，而最成熟的跨平台技术就是大家熟悉的浏览器了，它与生俱来的跨平台能力、开放的标准以及强大的生态使它成为炙手可热的容器之一。而由于其本身不是为了性能而设计的，并且历史包袱重、兼容性、厂商更新慢等问题，浏览器在移动端的表现并不突出。尽管网络以及硬件的发展带来了足够多的性能红利，但是日益复杂的业务总能把已有的性能吃透。

过去也有很多对跨平台方案的探索与实践，新的技术方案也随着历史的浪潮不断地发展。从最早的 H5 方案到 Hybrid 方案，以及后来的 Weex/React Native 方案，到现在如火如荼的 Flutter。

![发展](https://img.alicdn.com/imgextra/i3/O1CN01yVz9zt1MhTIRP5B5O_!!6000000001466-2-tps-1711-569.png) 

Flutter 由于其精简的渲染管线，高效的布局渲染能力，以及自绘渲染的特性，一跃成为这两年跨端届的新宠。而在 Flutter 出现之前，主流的方案还是用 React Native（Weex）的，这套方案的底层调用了原生的 View。正是因为如此，导致这套方案很难保证完全的多端一致性，因为原生 View 本身就存在一些限制，有限的能力不能满足开发者所有的需求，所以在实现 W3C 标准时有些牵强。而 Flutter 基于更底层的 Skia 做自绘渲染，可以很好地保证多端一致性。

熟悉 Flutter 的同学肯定知道 Flutter 是用 Dart 语言以及 Widget 来开发的，虽说 Dart 语言对熟悉 JavaScript 的前端同学来说上手成本并不是很高，对于 Widget 这种基于状态驱动的开发模式也已经是非常熟悉，但是整体上与已有基建与前端生态割裂的矛盾是无法接受的。再者，动态化能力对于互联网业务来说简直就是刚需，而目前来看 Flutter for Web 并不理想。

毕竟，**引入一项新技术的第一步是解决引入这项新技术的成本问题**。所以我们积极探索一种向上对接前端生态，向下使用自绘渲染的跨平台方案。

于是诞生了这款基于 W3C 标准的高性能跨终端渲染引擎——北海（Kraken）。

![kraken](https://img.alicdn.com/imgextra/i4/O1CN01OQNfdd1WyYDzDGMhR_!!6000000002857-2-tps-1280-640.png) 

## 基于 W3C 标准

Kraken 最终要服务的用户还是前端开发者，那么如何降低前端开发者的学习熟悉成本以及如何将老项目快速地迁移到 Kraken 之上呢？我们并不想重新创造一套新的 DSL 作为研发框架来给开发者用，如果需要的话，那 Flutter 本身的 Widget + Dart 已经做得很不错了。前端开发者可能是用 [Rax](https://github.com/alibaba/rax)，也有可能是用 [Vue](https://github.com/vuejs/vue) 或是 [React](https://github.com/facebook/react) 的，我们都期望 Kraken 的用户能够做到“零成本”的快速接入。那么，就需要依赖一套开发者非常熟悉的标准来实现 Kraken。

<img src="https://img.alicdn.com/imgextra/i4/O1CN01Edx8Jk1ELTLfISD7H_!!6000000000335-2-tps-1000-1000.png" width="300px" />

W3C 标准是互联网最重要的标准之一，也是前端开发者非常熟悉的标准，基于 W3C 标准来实现渲染引擎，对于熟悉浏览器的前端开发者可以做到近乎“零成本”的快速上手。同时，我们可以摈弃一些沉重的历史包袱，使得 Kraken 的渲染效率更高。

## 强大的前端开发者生态

受益于基于 W3C 标准来开发，在 Kraken 上前端开发者完全可以使用目前熟悉的庞大的前端生态。

首先，在研发框架的选择上，无论开发者使用的是 [Rax](https://github.com/alibaba/rax) 或者 [Vue](https://github.com/vuejs/vue) ，还是 [React](https://github.com/facebook/react) 或者 [Angular](https://github.com/angular/angular) 的，都可以保证在 Kraken 上很好地完成渲染。

![undefined](https://img.alicdn.com/imgextra/i1/O1CN01rgLFTz1SOafG0kH1d_!!6000000002237-2-tps-1456-488.png) 

再次，前端拥有非常繁荣的生态，社区海量的 NPM 包都可以在 Kraken 项目上直接使用，大量成熟的模块保证了业务开发的效率。

<img src="https://img.alicdn.com/imgextra/i4/O1CN01Fxvi1F26lZA3iicyC_!!6000000007702-2-tps-1799-800.png" width="300px" />

除此之外，开发者平时在开发项目使用的各式各样的工具，都可以在 Kraken 项目上直接使用，无需任何额外的熟悉以及学习成本。

![undefined](https://img.alicdn.com/imgextra/i4/O1CN01VBYO1X1zj5iJjpwbc_!!6000000006749-2-tps-1324-834.png) 

通过对接了 Chrome DevTools Protocol，使开发者可以直接使用非常熟悉的 Chrome DevTools 来调试所开发的页面。无论开发者需要修改 CSS 样式，还是查看 DOM 结构，或者是通过断点调试 JavaScript 代码，都能保证跟 Web 开发一致的调试体验。


[embed: devtools.mp4](https://intranetproxy.alipay.com/skylark/lark/0/2021/mp4/99047/1618823007263-921c2fe0-4088-46f8-816f-991bab6bb72f.mp4)


## 渲染一致性

Kraken 的渲染能力是对其 W3C 标准的，所以可以保证跟 Web 渲染的结果完全一致。

下图是实际业务的截图，从左到右分别是 Kraken（iOS），Kraken（Android）以及 Web 版本，可以看出渲染结果是完全一致的。


[embed: youku.mp4](https://intranetproxy.alipay.com/skylark/lark/0/2021/mp4/99047/1618823026967-36f8ac4e-bb03-4288-9049-691d6fc38401.mp4)


## 比 Web 更好的体验与能力

那么到这里会有同学想问了，除了与目前前端开发一致的开发及调试体验，以及渲染一致性，那么最终到底能得到怎么样的能力，以及跟浏览器比，到底可以获得哪些收益呢？

### 无限滚动列表

在业务开发中，有时开发者会遇到一些无法用分页却又大量数据的「无限滚动列表」。在客户端开发中有 RecyclerView/UITableView 来实现滚动回收的布局容器，而 Web 标准上尽管也有很多前端侧的优化方案来处理这个问题，但也一直是个难题。Kraken 尝试在容器侧解决了此问题，增加 CSS Display 属性值——sliver。

当 Sliver 容器中的子元素滚动出该容器的 Viewport 时，可以将该子元素中用于渲染的 renderobject 回收以达到节省内存占用的目的。当子元素重新出现时，根据 DOM 描述重新生成 renderobject。

这是一个上万个节点的 demo，左边是 overflow: scroll 的容器，而右边是 display: sliver 的容器，可以看到 sliver 容器在「无限滚动列表」场景下滚动明显流畅很多。左上角有 FPS 的数据，可以看到 display: sliver 的容器 FPS 一直维持正常水平，而 overflow: scroll 的容器 FPS 明显下降。此外，在内存方面也有较大收益。

[embed: sliver.mp4](https://intranetproxy.alipay.com/skylark/lark/0/2021/mp4/99047/1618823042936-7022171d-5a38-4c6a-adf7-349e1c60cdba.mp4)

### 同步光栅化

在浏览器中，光栅化是异步进行的，进行惯性滚动时，会出现白屏，这个是 WebView 始终无法避免的问题。而借助 Flutter 足够高效的同步光栅化实现，Kraken 可以做到长列表快速滚动不白屏。

[embed: kraken-long-list.mp4](https://intranetproxy.alipay.com/skylark/lark/0/2021/mp4/99047/1618823065533-2f72ba63-db1b-436c-a22d-ee6c0bf9a187.mp4)

### 增强的手势能力

Kraken 通过对[常用手势](https://github.com/openkraken/kraken/issues/4)进行内置，使业务开发者使用手势能力的时候，再也不需要引入一个 JavaScript lib 以劫持 Touch event 来做开发了。

以轻扫手势“swipe”为例，开发者只需要通过以下方式就可以获得一个 element 上默认提供的手势能力。直接使用内置增强的手势能力，能够更快速地开发复杂的可交互应用。

```javascript
element.addEventLisenter('swipe',(gestureEvent) => {
	///...
})
```

增强的手势能力解决了 Web 下原本需要频繁传递事件的性能问题以及 JavaScript 处理手势占用 UI 线程的问题。此外，通过容器内部实现的竞争场能力，可以解决 Web 下手势穿透等问题。

而内置的标准化手势能力，也保证同个容器的不同应用下，手势交互能力的标准化以及统一性。

### 插件化能力

除了上面的那些超越 Web 的体验与能力以外，Kraken 非常重要的一个特性就是[插件化能力](https://github.com/openkraken/kraken/issues/5)，**插件化能力提供给前端工程师重新定义浏览器能力的机会**，开发者只需要编码一个 Flutter plugin，就可以扩展 Kraken 本身的能力。

通过插件化能力，开发者可以在内部实现许多自定义的标签（譬如说 Camera 或者自定义的视频播放器等），也可以基于性能的考虑将常用的业务组件（譬如说 Slider）下沉到容器层。由于浏览器厂商开发以及标准制定往往是滞后的，用插件化能力开发者可以快速地自定义各种渲染能力，使业务开发可以用到最新的或者增强的各种能力。

除了扩展渲染能力，开发者还可以扩展手势能力，扩展手势能力可以将以往需要在前端劫持 Touch Event 实现的能力下沉到容器本身，除了增强了交互体验，也给交互提供了更多可能性。


## 稳定性保障

渲染引擎非常复杂，经常出现改一个 Bug 牵一发而动全身，所以需要高覆盖率的自动化测试来保障渲染引擎的稳定性，每次修改后都需要保障已有的 case 没有问题。通过自动化测试来保障每个 case 与修改之前的结果做对比，如果有差异，可以通过 case 以及差异的 diff 来修改 Bug。

这套自动化测试系统保证了 Kraken 每次修改前后得到的 case 结果的一致性，以确保渲染引擎本身的稳定性。

目前已经有近 3000 个测试用例，未来还会根据更多的场景继续增加，以此来保证 Kraken 的稳定性。

![undefined](https://img.alicdn.com/imgextra/i1/O1CN01C1g4zs23nuL8UI5Yl_!!6000000007301-2-tps-2386-1354.png) 

## 业务落地

讲了那么多 Kraken 的能力，肯定有同学想知道 Kraken 在实际生产场景的表现如何。

目前 Kraken 在 C 端场景移动设备以及低性能 IoT 设备均有相关业务接入，完全可以使用在实际生产场景。

在优酷 APP 中，Kraken 已经落地了大量业务。以下方所示身份详情页为例，Kraken 改造后启动有了一个质的提升，相较于同个页面的原方案，首屏渲染提升28.4%，帧率提升 8.3%。

[embed: kraken_vs_prev.mp4](https://intranetproxy.alipay.com/skylark/lark/0/2021/mp4/99047/1618905293873-5dbd5fae-e3a7-4397-aedd-89fb3db32ac3.mp4)

在 IoT 设备上，我们的天猫 U 先业务在线下低性能的 IoT 设备上，Kraken 也有非常不错的表现。在线下相对较复杂的 Slider 等场景下，动画以及交互性能都表现较好，长时间运行应用，内存稳定并无明显增长，保证线下 IoT 应用的稳定性。

![天猫 U 先](https://img.alicdn.com/imgextra/i4/O1CN01KSgJAB1b67EHIp2pL_!!6000000003415-2-tps-834-626.png) 

## 社区协作机制

kraken 团队期望通过一种良好的社区协作机制，来与社区的众多开发者一起共建 Kraken 底层能力及生态。

kraken 团队期望通过[协作者](https://github.com/openkraken/kraken/blob/main/GOVERNANCE.zh-CN.md#%E5%8D%8F%E4%BD%9C%E8%80%85)的方式来参与 Kraken 功能迭代以及 issue 讨论等工作。同时，通过由一部分协作者组成的[技术委员会（TSC）](https://github.com/openkraken/kraken/blob/main/GOVERNANCE.zh-CN.md#%E6%8A%80%E6%9C%AF%E5%A7%94%E5%91%98%E4%BC%9A)来确定技术方向、发布以及定制标准等工作。

kraken 团队期望通过一种公平良好的协作机制，让社区的开发者能够更好地参与到对 Web 标准的容器技术的演进中去，让每个人的声音都能被听到，共同促进 Kraken 以及行业的发展。

查看更详细的协作机制可以移步[github](https://github.com/openkraken/kraken/blob/main/GOVERNANCE.zh-CN.md)。

## 未来展望

以往我们在做前端性能优化的时候，往往优化到浏览器层面就优化不动了，很难向下进行进一步的优化。而 Kraken 的出现，给予了前端工程师新的机会与挑战，它提供了前端工程师一个**重新定义浏览器的能力**的机会，拥有非常大的想象空间：

* 超越 Web 的能力，比肩 Native 的性能与体验。
* 比浏览器厂商更快地实现标准，站在标准的前沿定义问题，通过实现的能力去反推标准，促进行业的发展。
* 可以自顶向下看整个渲染链路的优化及体验，通过全链路的手段去优化性能以及定义一些新的渲染能力。
* 目前日益复杂的前端应用导致 JS bundle 的已经显得越来越臃肿，开发者也可以把常用能力抽象复用并下沉到容器层面，渲染与公用能力的复用不再只依赖于 NPM，可以通过下沉通用能力来做更多事情。
* 通过“云+端”的结合，也有机会去探索面向未来的新一代渲染技术。
* 基于 Kraken，探索更多的可能性......

最后，期望 Kraken 能给大家带来更好的体验及能力，也希望大家可以积极参与到 Kraken 项目中去，大家一起共建 Kraken 生态。

官网：[https://openkraken.com](https://openkraken.com)

Github：[https://github.com/openkraken/kraken](https://github.com/openkraken/kraken)