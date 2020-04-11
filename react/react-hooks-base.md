# React hooks 使用指南

## [Hook 简介](https://zh-hans.reactjs.org/docs/hooks-intro.html)

> Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

拓展一下以上这句话：

- Hook 是 React 16.8 的新增特性。
- 一直以来react提倡使用函数组件，但有时候需要再函数内使用 state，之前只能转换为 class 组件，现在可以直接使用hooks
- 约定 use 开头的 React Api 是 hooks，开发中也遵守这个规范。

随着在项目中使用了更多的hooks来开发组件，累积了一些日常使用中遇到的问题和思考，罗列在此。


## [Hooks的使用规则](https://zh-hans.reactjs.org/warnings/invalid-hook-call-warning.html)

- 只能在函数内部的最外层调用 Hook，不要在循环、条件判断或者子函数中调用
- 只能在 React 的函数组件中调用 Hook，不要在其他 JavaScript 函数中调用

这里如果使用违规，会在控制台报错 `Hooks can only be called inside the body of a function component`
会罗列出你可能触发警告的三个原因：

- 你的 React 和 React DOM 可能版本不匹配。
- 你可能打破了 Hook 的规则。
- 你可能在同一个应用中拥有多个 React 副本。

具体根据提示逐一排查。

## 使用Hooks的优势

- 使用hooks，如果业务变更，就不需要把函数组件修改成类组件。
- 告别了繁杂的this和合并了难以记忆的生命周期。
- 更好的完成状态之间的共享，解决原来class组件内部封装的问题，也解决了高阶组件和函数组件的嵌套过深。一个组件一个自己的state，一个组件内可以公用。
- 支持包装自己的Hooks(自定义Hooks)，是基于纯命令式的api。


## 内置API介绍理解

React一共内置了9种Hook。

- useState
- usEffect
- useContext
- useReducer
- useCallback
- useMemo
- useRef
- useImperativeHandle
- useLayoutEffect

从前到后简单说一下使用方法，注意事项。

我们使用官方的 [create-react-app](https://create-react-app.dev/) 快速创建一个工程demo

### useState

`useState` 的出现是 ： 在函数组件里面使用 `class` 的 `setState`

解决了我们当一个简单函数组件想要有自己的 `state` 的时候，之前只能转为class组件的麻烦。

🔧 使用方法：

```
import React, { useState } from 'react';

function Example() {
    // 声明一个名为“count”的新状态变量
    const [count, setCount] = useState(0);
    return (
      <div>
        <button onClick={() => setCount(count + 1)}>+</button>
        <h1>{count}</h1>
        <button onClick={() => setCount(count - 1)}>-</button>
      </div>
    )
}

export default Example;
```

💡 注意事项：

通过 `useState` 得到的状态 `count` ，在 Example 组件中的表现为一个常量，每一次通过 setCount 进行修改后，又重新通过 `useState` 获取到一个新的常量。

### usEffect

之前很多具有副作用的操作，例如网络请求，修改 UI 等，一般都是在 `class` 组件的 `componentDidMount` 或者 `componentDidUpdate` 等生命周期中进行操作。而在函数组件中是没有这些生命周期的概念的，只能 `return` 想要渲染的元素。但是现在，在函数组件中也有执行副作用操作的地方了，就是使用 `useEffect` 函数。

**语法**

```
useEffect(() => { doSomething });
```

**两个参数：**

- 第一个是一个函数，是在第一次渲染以及之后更新渲染之后会进行的副作用。

  - 这个函数可能会有返回值，倘若有返回值，返回值也必须是一个函数，会在组件被销毁时执行。

- 第二个参数是可选的，是一个数组，数组中存放的是第一个函数中使用的某些副作用属性。用来优化 useEffect

  - 如果使用此优化，请确保该数组包含外部作用域中随时间变化且 effect 使用的任何值。 否则，您的代码将引用先前渲染中的旧值。
  - 如果要运行 effect 并仅将其清理一次（在装载和卸载时），则可以将空数组（[]）作为第二个参数传递。 这告诉React你的 effect 不依赖于来自 props 或 state 的任何值，所以它永远不需要重新运行。

```
import { useState, useEffect } from 'react';
import ReactDOM from 'react-dom';

function Example() {
  const [count, setCount] = useState(0);

  // 对比 componentDidMount/componentDidUpdate
  useEffect(() => {
    // update
    document.title = `You clicked ${count} times`;
    // 对比 componentWillUnMount
    return function cleanup() {
    	document.title = 'app';
    }
  }, [count]);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

ReactDOM.render(<Example />, document.getElementById('root'));

```


## useRef

`useRef` 返回一个可变的 `ref` 对象，其 `.current` 属性初始化为传递的参数（initialValue）。返回的对象将持续整个组件的生命周期。

如果我们需要保存一些改变的值，就可能会用到 `useRef`

```
function TextInputChangeButton() {
	// 使用 useRef 创建 inputEl
	const inputEl = useRef(null);
	const [text, updateText] = useState('');
	// 使用 useRef 创建 textRef
	const textRef = useRef();
	useEffect(() => {
			// 将 text 值存入 textRef.current 中
			textRef.current = text;
			console.log('textRef.current：', textRef.current);
	});
	const onButtonClick = () => {
			// `current` points to the mounted text input element
			inputEl.current.value = "Hello, useRef";
	};
	return (
			<>
					{/* 保存 input 的 ref 到 inputEl */}
					<input ref={ inputEl } type="text" />
					<button onClick={ onButtonClick }>在 input 上展示文字</button>
					<br />
					<br />
					<input value={text} onChange={e => updateText(e.target.value)} />
			</>
	);
}
```
点击 在 `input` 上展示文字 按钮，就可以看到第一个 `input` 上出现 `Hello, useRef`；
在第二个 `input` 中输入内容，可以看到控制台打印出对应的内容。

## useMemo

useMemo 主要用于渲染过程优化，两个参数依次是计算函数（通常是组件函数）和依赖状态列表，当依赖的状态发生改变时，才会触发计算函数的执行。如果没有指定依赖，则每一次渲染过程都会执行该计算函数。

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

## useContext

`context` 是在外部 `create` ，内部 use 的 `state` ，它和全局变量的区别在于，如果多个组件同时 `useContext` ，那么这些组件都会 `rerender` ，如果多个组件同时 `useState` 同一个全局变量，则只有触发 `setState` 的当前组件 `rerender` 。

```
// import { useState, useContext, createContext } from 'react';

// 1. 使用 createContext 创建上下文
const UserContext = new createContext();

// 2. 创建 Provider
const UserProvider = props => {
  let [name, changeName] = useState('');
  return (
    <UserContext.Provider value={{ name, changeName }}>
      {props.children}
    </UserContext.Provider>
  );
};

const Form = () => {
  const { name, changeName } = useContext(UserContext); // 3. 使用 Context
  return (
    <Fragment>
      <h2>Name: {name}</h2>
      <input onChange={e => changeName(e.target.value)} />
    </Fragment>
  );
};

const ContextMain = () => (
  <div>
    <UserProvider>
      <Form />
    </UserProvider>
  </div>
);
```

## useReducer

看到 `useReducer`,肯定会想到 `Redux` ，没错它和 `Redux` 的工作方式是一样的。 `useReducer` `的出现是useState` 的替代方案，能够让我们更好的管理状态。
`useReducer` 一共可以接受三个参数并返回当前的 `state` 与其配套的 `dispatch` 。



举个 🌰：

```
function reducer(state, action) {
  switch (action.type) {
    case 'up':
      return { count: state.count + 1 };
    case 'down':
      return { count: state.count - 1 };
  }
}

function ReducerCount() {
  const [state, dispatch] = useReducer(reducer, { count: 1 })
  return (
    <div>
      <h3>{state.count}</h3>
      <button onClick={() => dispatch({ type: 'up' })}>加 + </button>
      <button onClick={() => dispatch({ type: 'down' })}>减 - </button>
    </div>
  );
}
```

## useCallback

useMemo 解决了值的缓存的问题，那么函数呢？

`useCallback` 可以认为是对依赖项的监听，把接受一个回调函数和依赖项数组，返回一个该回调函数的 memoized(记忆)版本，该回调函数仅在某个依赖项改变时才会更新。

下面这个 🌰 就是，当点击 count 的按钮时

```
const CallbackComponent = () => {
    let [count, setCount] = useState(1);
    let [num, setNum] = useState(1);

    const memoized = useCallback( () => {
        return num;
    },[count])
    return (
        <Fragment>
						<h2>记忆：{memoized()}</h2>
						<h3>原始：{num}</h3>
            <button onClick={() => {setCount(count + 1)}}> count+ </button>
            <button onClick={() => {setNum(num + 1)}}> num+ </button>
        </Fragment>
    )
}
```

## 自定义Hook

自定义 `hooks` 可以说成是一种约定而不是功能。当一个函数以 `use` 开头并且在函数内部调用其他 `hooks` ，那么这个函数就可以成为自定义 `hooks` ，比如说 `useSomething` 。

自定义 `Hooks` 可以封装状态，能够更好的实现状态共享。

我们来封装一个数字加减的Hook

```
const useCount = (num) => {
    let [count, setCount] = useState(num);
    return [count,()=>setCount(count + 1), () => setCount(count - 1)]
};
```
这个自定义Hook内部使用useState定义一个状态，返回一个数组，数组中有状态的值、状态++的函数，状态--的函数。

** 自定义hooks的使用方法 **

```
const CustomComp = () => {
    let [count, addCount, redCount] = useCount(1);
    return (
        <>
            <h1>{count}</h1>
            <button onClick={addCount}> +++ </button>
            <button onClick={redCount}> --- </button>
        </>
    )
}
```
主函数中使用解构赋值的方式接受这三个值使用，这是一种非常简单的自定义Hook。
如果项目大的话使用自定义Hook会抽离可以抽离公共代码，极大的减少我们的代码量，提高开发效率。


## Hooks 使用及编写规范

- 不要从常规 JavaScript 函数调用 Hooks;
- 不要在循环，条件或嵌套函数中调用 Hooks;
- 必须在组件的顶层调用 Hooks;
- 可以从 React 功能组件调用 Hooks;
- 可以从自定义 Hooks 中调用 Hooks;
- 自定义 Hooks 必须使用 use 开头，这是一种约定;

## 使用 React 提供的 [ESLint](https://www.npmjs.com/package/eslint-plugin-react-hooks) 插件

根据上一段所写，在 React 中使用 Hooks 需要遵循一些特定规则。但是在代码的编写过程中，可能会忽略掉这些使用规则，从而导致出现一些不可控的错误。这种情况下，我们就可以使用 React 提供的 ESLint 插件：[eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)。下面我们就看看如何使用吧。

**安装 ESLint 插件**

`$ npm install eslint-plugin-react-hooks --save`

复制代码在 .eslintrc 中使用插件

```
// Your ESLint configuration
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error", // Checks rules of Hooks
    "react-hooks/exhaustive-deps": "warn" // Checks effect dependencies
  }
}
```

> [React 官网](https://reactjs.org/docs/hooks-intro.html)
> [React Hooks FAQ](https://reactjs.org/docs/hooks-faq.html)