# mobx-react-lite 基于 Hook API 的使用

## 创建 store 组件

```
// HookStore.js
import React, {createContext} from 'react';
import {useLocalStore, observer} from 'mobx-react-lite';

export const MyContext = createContext(null);

export const Counter = observer((props) => {
  const store = useLocalStore(() => (
    {
      count: 1,
      get getCount() {
        return store.count;
      },
      handleCount() {
        store.count += 2;
      }
    }
  ));
  return (
    <MyContext.Provider value={store}>
      {props.children}
    </MyContext.Provider>
  );
});
```

## 使用 Counter 组件

子组件是class类组件时，通过 MyContext.Consumer 访问 store，函数组件通过 useContext Hook API 访问 store

```
import React from 'react';
import {Counter} from './store/HookStore';
import './App.scss';

function App() {
  return (
    <div className="App">
      <Counter>
        <HookDemo01></HookDemo01>
        <ClassCom></ClassCom>
      </Counter>
    </div>
  );
}

export default App;
```

## 类组件（class）连接 store

```
import React, { Component } from 'react';
import {Observer} from 'mobx-react-lite';
import {MyContext} from '../store/HookStore';
import {toJS} from 'mobx';

class CounterClass extends Component {
  render() {
    return (
      <div>
        <MyContext.Consumer>
          {
            store => {
              {/* 使用 Object.is 比较store是否发生变化，store的引用不变，导致不会重渲染，获取最新依赖值需使用 Observer 组件 */}
              console.log('store: ', toJS(store));
              return (
                <>
                  <p>counter2: {store.count}</p> {/* 这种方式在 class 组件中无效，不会引发重渲染 */}
                  <Observer>{() => <p>counter2: {store.count}</p>}</Observer>
                  {/* 必须这样写，Observer 组件是mobx-react-lite提供的唯一可在类组件中使用的组件 */}
                  <button onClick={() => store.handleCount()}>Counter Add</button>
                </>
              )
            }
          }
        </MyContext.Consumer>
      </div>
    )
  }
}

export default CounterClass;

```
## 函数组件连接 store

```
import React, {useState, useEffect, useContext} from 'react'
import {Observer, observer} from 'mobx-react-lite';
import {MyContext} from '../../store/HookStore';

const CounterFunction = (props) => {
  const [num, setNum] = useState(10);
  const store = useContext(MyContext); // 当组件上层最近的 <MyContext.Provider> 更新时，该 Hook 会触发重渲染，并使用最新传递给 MyContext provider 的 context value 值。

  useEffect(() => {
    console.log('num change', MyContext);
  }, [num]);

  return (
    <div>
      <p>num: {num}</p>
      <button onClick={() => setNum(num + 1)}>set num</button>

      {/* 深度监听 store 变化并进行重渲染，导致下面两行结果相同 */}
      <p>Count: {store.getCount}</p>
      <Observer>{() => <p>Count2: {store.getCount}</p>}</Observer>
      <button onClick={() => store.handleCount()}>Counter Add</button>
    </div>
  );
}

export default observer(CounterFunction);
```
