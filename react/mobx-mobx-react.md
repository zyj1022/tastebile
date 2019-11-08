# React 状态管理方法之 mobx

这里介绍 mobx + mobx-react 结合使用的方法。

## 创建 store 方法

```
// store.js
import {observable, action} from 'mobx';

class Store {
  @observable
  count = 0;

  @action
  handleCount() {
    this.count += 1;
  }
}

export default new Store();
```

## 根组件通过 Provider 注入 store

```
import { Provider } from 'mobx-react';

function App() {
  return (
    <Provider store={store}>
      <div className="App">
        <ComponentClass></ComponentClass>
        <ComponentFun></ComponentFun>
      </div>
    </Provider>
  );
}

export default App;
```

## 类组件（class）访问 store 方法

```
import React, { Component } from 'react';
import {inject, observer} from 'mobx-react';
import {toJS} from 'mobx'; // 查看store中原始JavaScript数据结构

@inject('store')
@observer
class ComponentClass extends Component {
  handleClick = () => {
    this.props.store.handleCount();
  };

  render() {
    console.log(toJS(this.props.store));
    return (
      <div>
        count: {this.props.store.count}
        <button onClick={this.handleClick}>add one</button>
      </div>
    )
  }
}

export default ComponentClass;

```

## 函数组件使用 store 方法

```
import React from 'react'
import {inject, observer} from 'mobx-react';

const ComponentFun = ({store}) => {
  return (
    <div>
      <p>you clicked {store.count} times</p>
      <button onClick={() => store.handleCount()}>Add</button>
    </div>
  );
}

export default inject('store')(observer(ComponentFun));
```
