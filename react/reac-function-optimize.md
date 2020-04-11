# React 函数式组件性能优化指南

有过 React 函数式组件的实践，并且对 hooks 有过实践，对 useState、useCallback、useMemo API 至少看过文档，如果你有过对类组件的性能优化经历，那么这篇文章会让你有种熟悉的感觉。

## React 性能优化思路

我觉得 React 性能优化的理念的主要方向就是这两个：

- 减少重新 `render` 的次数。因为在 React 里最重(花时间最长)的一块就是 `reconction`(简单的可以理解为 diff)，如果不 `render` ，就不会 `reconction。`

- 减少计算的量。主要是减少重复计算，对于函数式组件来说，每次 `render` 都会重新从头开始执行函数调用。

在使用类组件的时候，使用的 React 优化 API 主要是： `shouldComponentUpdate` 和 `PureComponent` ，这两个 API 所提供的解决思路都是为了减少重新 render 的次数，主要是减少父组件更新而子组件也更新的情况，虽然也可以在 state 更新的时候阻止当前组件渲染，如果要这么做的话，证明你这个属性不适合作为 state，而应该作为静态属性或者放在 class 外面作为一个简单的变量 。

但是在函数式组件里面没有声明周期也没有类，那如何来做性能优化呢？

## React.memo

首先要介绍的就是` React.memo`，这个 API 可以说是对标类组件里面的 `PureComponent` ，这是可以减少重新 `render` 的次数的。

举个 🌰

`useMemo` 可以帮助我们优化子组件的渲染，比如这种场景：
在 A 组件中有两个子组件 B 和 C，当 A 组件中传给 B 的 `props` 发生变化时，A 组件状态会改变，重新渲染。此时 B 和 C 也都会重新渲染。其实这种情况是比较浪费资源的，现在我们就可以使用 `useMemo` 进行优化，B 组件用到的 `props` 变化时，只有 B 发生改变，而 C 却不会重新渲染。

```
const CompA = (props)  => {
	console.log('CompA', props)
	return <h2 style={{color: `red`}}>Comp A {props.text}</h2>
}

const CompB = (props)  => {
	console.log('CompB', props)
	return <h2 style={{color: `blue`}}>Comp B {props.text}</h2>
}

const CompAandB = (props)  => {
	const [count , setCount] = useState(0)
	const [a, setA] = useState('ExampleA');
	const [b, setB] = useState('ExampleB');

	function clickA() {
		setCount(count + 1)
		setA('修改后的 ExampleA' + count)
	}

	function clickB() {
		setCount(count + 1)
		setB('修改后的 ExampleB' + count)
	}

  // 利用useMemo进行优化：
	const exampleA = useMemo(() => <CompA text={ a } />, [a]);
  const exampleB = useMemo(() => <CompB text={ b } />, [b]);

	return (
		<div>
			{exampleA}
			{exampleB}
			<br />
			<button type="primary" onClick={clickA}>我是A按钮</button>
			<br />
			<button type="primary" onClick={clickB}>我是B按钮</button>
		</div>
	)
}
```
## React.memo 高级用法

默认情况下其只会对 `props` 的复杂对象做浅层对比(浅层对比就是只会对比前后两次 `props` 对象引用是否相同，不会对比对象里面的内容是否相同)，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。

```
function MyComponent(props) {
  /* 使用 props 渲染 */
}
function areEqual(prevProps, nextProps) {
  /*
  如果把 nextProps 传入 render 方法的返回结果与
  将 prevProps 传入 render 方法的返回结果一致则返回 true，
  否则返回 false
  */
}
exportdefault React.memo(MyComponent, areEqual);
```

如果你有在类组件里面使用过 `shouldComponentUpdate()`[2] 这个方法，你会对 `React.memo` 的第二个参数非常的熟悉，不过值得注意的是，如果 `props` 相等， `areEqual` 会返回 true；如果 `props` 不相等，则返回 false。这与 `shouldComponentUpdate` 方法的返回值相反。


## useCallback

`useCallback` 与 `useMemo` 这两个api，其实概念上还是很好理解的，一个是「缓存函数」， 一个是缓存「函数的返回值」。

hooks对「有没有变化」这个点其实很敏感。如果一个effect内部使用了某数据或者方法。若我们依赖项不加上它，那很容易由于闭包问题，导致数据或方法，都不是我们理想中的那个它。如果我们加上它，很可能又会由于他们的变动，导致effect疯狂的执行。真实开发的话，大家应该会经常遇到这种问题。

所以，在此建议：

- 在组件内部，那些会成为其他 `useEffect` 依赖项的方法，建议用 `useCallback` `包裹，或者直接编写在引用它的useEffect` 中。
- 己所不欲勿施于人，如果你的 function 会作为 props 传递给子组件，请一定要使用 `useCallback` 包裹，对于子组件来说，如果每次 `render` 都会导致你传递的函数发生变化，可能会对它造成非常大的困扰。同时也不利于 react 做渲染优化



> [React 官网](https://zh-hans.reactjs.org/docs/react-api.html#reactmemo)

> [写React Hooks前必读](https://zhuanlan.zhihu.com/p/113216415?utm_source=tuicool&utm_medium=referral)

> [浅谈React性能优化的方向](https://juejin.im/post/5d045350f265da1b695d5bf2#heading-0)

