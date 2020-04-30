# 函数式组件与类组件有何不同？

与React类组件相比，React函数式组件究竟有何不同？

除了回答class组件提供更多的特性，比如生命周期等，现在函数组件用于 `hooks` 也可以有类似的功能.

或者会有性能差异，但性能主要取决于代码的作用，不仅仅是组件的构成方式，在实践观察中，根据优化策略[不同](https://zh-hans.reactjs.org/docs/hooks-faq.html#are-hooks-slow-because-of-creating-functions-in-render)，但性能差异可以忽略不计。

React的函数式组件和类组件之间是否有任何根本上的区别？当然有 —— 在心智模型上。

这里将阐述它们之间的最大区别。 自推出函数式组件以来，它一直存在，但是经常被忽略：


> 函数式组件捕获了渲染所用的值。（Function components capture the rendered values.）


注意：这篇文章不是对函数式组件或者类组件的价值判断。我只是在阐述在React中这两种编程模型之间的区别。关于更广泛地采用函数式组件的问题，请查看 [Hooks FAQ](https://zh-hans.reactjs.org/docs/hooks-faq.html#adoption-strategy)。


## 举个组件 🌰

思考这个组件:

```
function ProfilePage(props) {
  const showMessage = () => {
    alert('Followed ' + props.user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return (
    <button onClick={handleClick}>Follow</button>
  );
}
```

它渲染了一个利用 `setTimeout` 来模拟网络请求，然后显示一个确认警告的按钮。例如，如果 `props.user` 是 `Dan` ，它会在三秒后显示 `Followed Dan`。非常简单。

（请注意，在上面的示例中我是否用了箭头函数或者函数声明并不重要。function handleClick()也将完全以同样方式有效。）

如果是类组件我们怎么写？一个简单的重构可能就象这样：

```
class ProfilePage extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  };

  handleClick = () => {
    setTimeout(this.showMessage, 3000);
  };

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}
```
通常我们认为，这两个代码片段是等效的。人们经常在这两种模式中自由的重构代码，但是很少注意到它们的含义：

![view](../assets/wtf.gif)

然而，这两个代码片段还是有略微的不同。 仔细的看看它们。现在看出他们的不同了吗？就我个人而言，我花了好一会儿才看明白这一点。

接下来的文章是“剧透”，如果你想自己搞明白，你可以查看这个 [live demo](https://codesandbox.io/s/pjqnl16lm7)。 本文的生育部分解释了这里面的差异以及阐述了为什么这很重要。

在我们继续之前，我想强调一点，我所描述的差异与 React Hooks 完全无关。上面的例子中甚至没有使用 Hooks！

它全部是关于 React 中函数式组件与类组件的区别的。如果你打算在你的 React 应用中更频繁地使用函数式组件，你可能需要理解它。


**我们将通过 React 应用程序中的一个常见错误来说明其中的不同。**

打开这个 [sandbox](https://codesandbox.io/s/pjqnl16lm7) 例子， 你将看到一个当前账号选择框以及两个上面 `ProfilePage` 的实现 —— 每个都渲染了一个 Follow 按钮。

尝试按照以下顺序来分别使用这两个按钮：

点击 其中某一个 `Follow` 按钮。
在3秒内 切换 选中的账号。
查看 弹出的文本。
你将看到一个奇特的区别:

当使用 函数式组件 实现的 `ProfilePage`, 当前账号是 `Dan` 时点击 `Follow` 按钮，然后立马切换当前账号到 `Sophie` ，弹出的文本将依旧是 `'Followed Dan'`。
当使用 类组件 实现的 `ProfilePage`, 弹出的文本将是 `'Followed Sophie'`

![bug](../assets/bug.gif)

在这个例子中，第一个行为是正确的。
如果我关注一个人，然后导航到了另一个人的账号，我的组件不应该混淆我关注了谁。 
在这里，类组件的实现很明显是错误的。

**所以为什么我们的例子中类组件会有这样的表现？**

让我们来仔细看看我们类组件中的 showMessage 方法：

```
class ProfilePage extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);`
  };
```

这个类方法从 `this.props.user` 中读取数据。在 React 中 Props 是不可变(immutable)的，所以他们永远不会改变。然而， **`this`** 是，而且永远是，可变 **`(mutable)`** 的。

事实上，这就是类组件 `this` 存在的意义。React本身会随着时间的推移而改变，以便你可以在渲染方法以及生命周期方法中得到最新的实例。

所以如果在请求已经发出的情况下我们的组件进行了重新渲染，`this.props` 将会改变。`showMessage` 方法从一个“过于新”的props中得到了 user。

这暴露了一个关于用户界面性质的一个有趣观察。如果我们说UI在概念上是当前应用状态的一个函数，那么事件处理程序则是渲染结果的一部分 —— 就像视觉输出一样。我们的事件处理程序“属于”一个拥有特定 `props` 和 `state` 的特定渲染。

然而，调用一个回调函数读取 `this.props` 的 `timeout` 会打断这种关联。我们的 `showMessage` 回调并没有与任何一个特定的渲染“绑定”在一起，所以它“失去”了正确的 `props` 。从 `this` 中读取数据的这种行为，切断了这种联系。

----

**让我们假设函数式组件不存在。我们将如何解决这个问题？**

我们想要以某种方式“修复”拥有正确 `props` 的渲染与读取这些 `props` 的 `showMessage` 回调之间的联系。在某个地方props被弄丢了。

一种方法是在调用事件之前读取this.props，然后将他们显式地传递到timeout回调函数中去：

```
class ProfilePage extends React.Component {
  showMessage = (user) => {
    alert('Followed ' + user);
  };

  handleClick = () => {
    const {user} = this.props;
    setTimeout(() => this.showMessage(user), 3000);
  };

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}
```
这种方法 会起作用。然而，这种方法使得代码明显变得更加冗长，并且随着时间推移容易出错。

- 如果我们需要的不止是一个props怎么办？
- 如果我们还需要访问 `state` 怎么办？
- 如果 showMessage 调用了另一个方法，然后那个方法中读取了 this.props.something 或者 this.state.something，我们又将遇到同样的问题。
然后我们不得不将 `this.props` 和 `this.state` 以函数参数的形式在被 `showMessage` 调用的每个方法中一路传递下去。

这样的做法破坏了类提供的工程学。同时这也很难让人去记住传递的变量或者强制执行，这也是为什么人们总是在解决bugs。

同样的，在 `handleClick` 中内联地写 `alert` 代码也无法解决问题。

我们希望以允许将其拆分为多个方法的方式来构造组织代码，但同时也能读取与某次组件调用形成的渲染结果对应的props和state。这个问题并不是React所独有的 —— 你可以在任何一个将数据放入类似 `this` 这样的可变对象中的UI库中重现它。

**或许，我们可以在构造函数中绑定方法？**

```
class ProfilePage extends React.Component {
  constructor(props) {
    super(props);
    this.showMessage = this.showMessage.bind(this);
    this.handleClick = this.handleClick.bind(this);
  }

  showMessage() {
    alert('Followed ' + this.props.user);
  }

  handleClick() {
    setTimeout(this.showMessage, 3000);
  }

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}
```
不，这没有解决任何问题。记住，我们面对的问题是我们从 `this.props `中读取数据太迟了——读取时已经不是我们所需要使用的上下文了！然而，如果我们能利用**JavaScript闭包的话问题将迎刃而解**。


通常来说我们会避免使用闭包，因为它会让我们难以想象一个可能会随着时间推移而变化的变量。但是在React中，props和state是不可变得！（或者说，在我们的强烈推荐中是不可变得。）这就消除了闭包的一个主要缺陷。

这就意味着如果你在一次特定的渲染中捕获那一次渲染所用的 `props` 或者 `state` ，你会发现他们总是会保持一致，就如同你的预期那样：

```
class ProfilePage extends React.Component {
  render() {
    // Capture the props!
    const props = this.props;

    // Note: we are *inside render*.
    // These aren't class methods.
    const showMessage = () => {
      alert('Followed ' + props.user);
    };

    const handleClick = () => {
      setTimeout(showMessage, 3000);
    };

    return <button onClick={handleClick}>Follow</button>;
  }
}
```
你在渲染的时候就已经“捕获”了 `props` ：

这样，在它内部的任何代码（包括showMessage）都保证可以得到这一次特定渲染所使用的props。
React再也不会“动我们的奶酪”。

然后我们可以在里面添加任意多的辅助函数，它们都会使用被捕获的props和state。闭包万岁！

----

上面的例子是正确的，但是看起来很奇怪。

如果你在render方法中定义各种函数，而不是使用class的方法，那么使用类的意义在哪里？

事实上，我们可以通过删除类的“包裹”来简化代码：

**读者注：实际是将 class 组件变形为 function 组件了🙂**

```
function ProfilePage(props) {
  const showMessage = () => {
    alert('Followed ' + props.user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return (
    <button onClick={handleClick}>Follow</button>
  );
}
```

就像上面这样，`props` 仍旧被捕获了 —— React将它们作为参数传递。

不同于 `this` ， `props` 对象本身永远不会被React改变。

如果你在函数定义中解构 `props` ，那将更加明显：

```
function ProfilePage({ user }) {
  const showMessage = () => {
    alert('Followed ' + user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return (
    <button onClick={handleClick}>Follow</button>
  );
}
```

当父组件使用不同的 `props` 来渲染 `ProfilePage` 时，React会再次调用 `ProfilePage` 函数。

但是我们点击的事件处理函数，“属于”具有自己的 `user` 值的上一次渲染，并且showMessage回调函数也能读取到这个值。它们都保持完好无损。

这就是为什么，在上面那个demo的函数式版本中，点击关注Sophie的账号，然后改变选择为Sunil仍旧会弹出`'Followed Sophie'`：

![bug](../assets/fix.gif)

这个行为展现是正确的.

现在我们明白了React中函数式组件和类组件之间的巨大差别：

> 函数式组件捕获了渲染所使用的值。

---

使用Hooks，同样的原则也适用于state。 看这个例子：

```
function MessageThread() {
  const [message, setMessage] = useState('');

  const showMessage = () => {
    alert('You said: ' + message);
  };

  const handleSendClick = () => {
    setTimeout(showMessage, 3000);
  };

  const handleMessageChange = (e) => {
    setMessage(e.target.value);
  };

  return (
    <>
      <input value={message} onChange={handleMessageChange} />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
}
```

（[这是演示demo](https://codesandbox.io/s/93m5mz9w24)。）

尽管这不是一个非常好的消息应用的UI，但它说明了同样的观点：
如果我发送一条特定的消息，组件不应该对实际发送的是哪条消息感到困惑。
这个函数组件的message变量捕获了“属于”返回了被浏览器调用的单击处理函数的那一次渲染。所以当我点击“发送”时message被设置为那一刻在input中输入的内容。

**读者注：在输入框输入一些文字，再点发送按钮，这个时候即使改变输入的文字，发出的数据也是第一次输入的文字，而不是在时间间隔内改变的文字。**

---

因此我们知道，在默认情况下React中的函数会捕获props和state。
但是如果我们想要读取的并不是第一次输入的数据，而是更改后的 `props` 和 `state` 值呢？

- 在class组件中，只需要通过读取 `this.props` 或者 `this.state`, 因为this本身时可变的.
  也就是一开始示例代码中认为class组件取值错误❌的时候。
- 在函数式组件中，你也可以拥有一个在所有的组件渲染帧中共享的可变变量。它被成为 `ref`;

默认情况下，React不会在函数式组件中为最新的props和state创造refs。在很多情况下，你并不需要它们，并且分配它们将是一种浪费。但是，如果你愿意，你可以这样手动地来追踪这些值：

```
function MessageThread() {
  const [message, setMessage] = useState('');
  const latestMessage = useRef('');

  const showMessage = () => {
    alert('You said: ' + latestMessage.current);
  };

  const handleSendClick = () => {
    setTimeout(showMessage, 3000);
  };

  const handleMessageChange = (e) => {
    setMessage(e.target.value);
    latestMessage.current = e.target.value;
  };

  return (
    <>
      <input value={message} onChange={handleMessageChange} />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
}
```

在输入框输入文字，按下按钮之后，即使再次改变输入框内的文字，最后获取的值也是改变后的最新文字。

只获取按钮触发之前值的 [demo1](https://codesandbox.io/s/93m5mz9w24?file=/src/index.js)

利用`ref` 获取最新值的 [demo2](https://codesandbox.io/s/ox200vw8k9?file=/src/index.js:84-624)

通常情况下，你应该避免在渲染期间读取或者设置 `refs` ，因为它们是可变得。

我们希望保持渲染的可预测性。然而，如果我们想要特定 `props` 或者 `state` 的最新值，

那么手动更新ref会有些烦人。我们可以通过使用一个 `effect` 来自动化实现它：

```
function MessageThread() {
  const [message, setMessage] = useState('');

  // Keep track of the latest value.
  const latestMessage = useRef('');
  useEffect(() => {
    latestMessage.current = message;
  });

  const showMessage = () => {
    alert('You said: ' + latestMessage.current);
  };

  const handleSendClick = () => {
    setTimeout(showMessage, 3000);
  };

  const handleMessageChange = (e) => {
    setMessage(e.target.value);
  };

  return (
    <>
      <input value={message} onChange={handleMessageChange} />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
}
```
[(这是demo。)](https://codesandbox.io/s/yqmnz7xy8x?file=/src/index.js:94-692)

我们在一个effect内部执行赋值操作以便让ref的值只会在DOM被更新后才会改变。这确保了我们的变量突变不会破坏依赖于可中断渲染的 [时间切片和 Suspense](https://zh-hans.reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html) 等特性。

通常来说使用这样的ref并不是非常地必要。捕获props和state通常是更好的默认值。

然而，在处理类似于intervals和订阅这样的[命令式API](https://overreacted.io/zh-hans/making-setinterval-declarative-with-react-hooks/)时，ref会十分便利。记住，你可以像这样跟踪任何值 —— 一个prop，一个state变量，整个props对象，或者甚至一个函数。

----
> 转载整理至[原文](https://overreacted.io/zh-hans/how-are-function-components-different-from-classes/)