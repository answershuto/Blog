原文地址：https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e

译文：染陌 （[Github](https://github.com/answershuto)）

译文地址：[https://github.com/answershuto/Blog](https://github.com/answershuto/Blog)

转载请著名出处

我是一名hooks API的忠实粉丝，然而它对你的使用会有一些[奇怪的约束](https://reactjs.org/docs/hooks-rules.html)，所以我在本文中使用一个模型来把原理展示给那些想去使用新的API却难以理解它的规则的人。

## 警告：Hooks 还处于实验阶段

本文提到的 Hooks API 还处于实验阶段，如果你需要的是稳定的 React API 文档，可以从[这里](https://reactjs.org/)找到。

## 解密 Hooks 的工作方式

我发现一些同学苦苦思索新的 Hooks API 中的“魔法”，所以我打算尝试着去解释一下，至少从表层出发，它是如何工作的。

### Hooks 的规则

React 核心团队在[Hooks的提案](https://reactjs.org/docs/hooks-rules.html)中提出了两个在你使用Hooks的过程中必须去遵守的主要规则。

* 请不要在循环、条件或者嵌套函数中调用 Hooks
* 都有在 React 函数中才去调用 Hooks

后者我觉得是显而易见的，你需要用函数的方式把行为与组件关联起来才能把行为添加到组件。

然而对于前者，我认为它会让人产生困惑，因为这样使用 API 编程似乎显得不那么自然，但这就是我今天要套索的内容。

### Hooks 的状态管理都是依赖数组的

为了让大家产生一个更清晰的模型，让我们来看一下 Hooks 的简单实现可能是什么样子。

需要注意的是，这部分内容只是 API 的一种可能实现方法，以便读者更好地趣理解它。它并不是 API 实际在内部的工作方式，而且它只是一个提案，在未来都会有可能发生变化。

### 我们应该如何实现“useState()”呢？

让我们通过一个例子来理解状态可能是如何工作的。

首先让我们从一个组件开始：

[代码地址](https://gist.github.com/ryardley/411bc849c6a3b9533b830a94f3979ae6#file-hooks-state-example-js)

```javascript
/* 译:https://github.com/answershuto */
function RenderFunctionComponent() {
  const [firstName, setFirstName] = useState("Rudi");
  const [lastName, setLastName] = useState("Yardley");

  return (
    <Button onClick={() => setFirstName("Fred")}>Fred</Button>
  );
}
```

Hooks API 背后的思想是你可以将一个 setter 函数通过 Hook 函数的第二个参数返回，用该函数来控制 Hook 管理的壮体。

### 所以 React 能用这个做什么呢？

首先让我们解释一下它在 React 内部是如何工作的。在执行上下文去渲染一个特殊组件的时候，下面这些步骤会被执行。这意味着，数据的存储是独立于组件之外的。该状态不能与其他组件共享，但是它拥有一个独立的作用域，在该作用域需要被渲染时读取数据。

#### （1）初始化

创建两个空数组“setters”与“state”

设置指针“cursor”为 0

![](https://i.loli.net/2018/11/25/5bfa17e981620.png)

#### (2)首次渲染

首次执行组件函数

每当 useState() 被调用时，如果它是首次渲染，它会通过 push 将一个 setter 方法（绑定了指针“cursor”位置）放进 setters 数组中，同时，也会将另一个对应的状态放进 state 数组中去。

![](https://i.loli.net/2018/11/25/5bfa191b462e1.png)

#### (3)后续渲染

每次的后续渲染都会重置指针“cursor”的位置，并会从每个数组中读取对应的值。

![](https://i.loli.net/2018/11/25/5bfa24f2f28d1.png)

#### (4)处理事件

每个 setter 都会有一个对应的指针位置的引用，因此当触发任何 setter 调用的时候都会触发去改变状态数组中的对应的值。

![](https://i.loli.net/2018/11/25/5bfa25d400f21.png)

### 以及底层的实现

这是一段示例代码：

[代码地址](https://gist.github.com/ryardley/e83bad1985740ab33b18fc578ec1957a/raw/6f1c18e82aba632ab86a5f76c13ea449b4b9b057/hooks-state-pseudocode.js)

```javascript
let state = [];
let setters = [];
let firstRun = true;
let cursor = 0;

function createSetter(cursor) {
  return function setterWithCursor(newVal) {
    state[cursor] = newVal;
  };
}

/* 译:https://github.com/answershuto */
// This is the pseudocode for the useState helper
export function useState(initVal) {
  if (firstRun) {
    state.push(initVal);
    setters.push(createSetter(cursor));
    firstRun = false;
  }

  const setter = setters[cursor];
  const value = state[cursor];

  cursor++;
  return [value, setter];
}

/* 译:https://github.com/answershuto */
// Our component code that uses hooks
function RenderFunctionComponent() {
  const [firstName, setFirstName] = useState("Rudi"); // cursor: 0
  const [lastName, setLastName] = useState("Yardley"); // cursor: 1

  return (
    <div>
      <Button onClick={() => setFirstName("Richard")}>Richard</Button>
      <Button onClick={() => setFirstName("Fred")}>Fred</Button>
    </div>
  );
}

// This is sort of simulating Reacts rendering cycle
function MyComponent() {
  cursor = 0; // resetting the cursor
  return <RenderFunctionComponent />; // render
}

console.log(state); // Pre-render: []
MyComponent();
console.log(state); // First-render: ['Rudi', 'Yardley']
MyComponent();
console.log(state); // Subsequent-render: ['Rudi', 'Yardley']

// click the 'Fred' button

console.log(state); // After-click: ['Fred', 'Yardley']
```

### 为什么说顺序很重要呢？

如果我们基于一些外部条件或是说组件的状态去改变 Hooks 在渲染周期的顺序，那会发生什么呢？

让我们做一些 React 团队禁止去做的事情。

[代码地址](https://gist.github.com/ryardley/3093d3c440ea6bdcdfcbc058e03ce5ab/raw/c5cd60cfdb89a5b3f8c8e0854a97fa24934172cb/hooks-order-dont-do-this-example.jsx)

```javascript
let firstRender = true;

function RenderFunctionComponent() {
  let initName;
  
  if(firstRender){
    [initName] = useState("Rudi");
    firstRender = false;
  }
  const [firstName, setFirstName] = useState(initName);
  const [lastName, setLastName] = useState("Yardley");

  return (
    <Button onClick={() => setFirstName("Fred")}>Fred</Button>
  );
}
```

我们在条件语句中调用了 useState 函数，让我们看看它对整个系统造成的破坏。

### 糟糕组件的首次渲染

![](https://i.loli.net/2018/11/25/5bfa76ec78779.png)

到此为止，我们的变量 firstName 与 lastName 依旧包含了正确的数据，让我们继续去看一下第二次渲染会发生什么事情。

### 糟糕的第二次渲染

![](https://i.loli.net/2018/11/25/5bfa778cc19df.png)

现在 firstName 与 lastName 这两个变量全部被设置为“Rudi”，与我们实际的存储状态不符。

这个例子的用法显然是不正确的，但是它让我们知道了为什么我们必须使用 React 团队规定的规则去使用 Hooks。

```
React 团队制定了这个规则，是因为如果不遵循这套规则去使用 Hooks API会导致数据有问题。
```

### 思考 Hooks 维护了一些列的数组，所以你不应该去违反这些规则

所以你现在应该清除为什么你不应该在条件语句或者循环语句中使用 Hooks 了。因为我们维护了一个指针“cursor”指向一个数组，如果你改变了 render 函数内部的调用顺序，那么这个指针“cursor”将不会匹配到正确的数据，你的调用也将不会指向正确的数据或句柄。

因此，有一个诀窍就是你需要思考 Hooks 作为一组需要一个匹配一致的指针“cursor”去管理的数组(染陌译)。如果做到了这一点，那么采用任何的写法它都可以正常工作。

### 总结

希望通过上述的讲解，我已经给大家建立了一个关于 Hooks 的更加清晰的思维模型，以此可以去思考新的 Hooks API 底层到底做了什么事情。请记住，它真正的价值在于能够关注点聚集在一起，同时小心它的顺序，那使用 Hooks API 会很高的回报。

Hooks 是 React 组件的一个很有用的插件，这也佐证了为何大家为何对此感到如此兴奋。如果你脑海中形成了我上述的这种思维模型，把这种状态作为一组数组的存在，那么你就会发现在使用中不会打破它的规则。

我希望将来再去研究一下 useEffects useEffects 方法，并尝试将其与 React 的生命周期进行比较。

这篇文章是一篇在线文档，如果你想要参与贡献或者有任何有误的地方，欢迎联系我。

你可以在 Twitter 上面 fllow 我（Rudi Yardley）或者在[Github](https://github.com/ryardley)找到我。

染陌 译:https://github.com/answershuto