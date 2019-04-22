# React 父组件调用子组件的方法

**父组件**

父组件给子组件传入一个 `onRef` 方法过去。接受子组件反馈的参数e, 把接受到的值赋予给，`event` 这时就可以调用子组件的一个方法叫 `sonClick()` 方法。

```
import React, { Component } from 'react';
import ChildClick from './ChildClick';

let event = '';

class ParentClick extends Component{

  onRef = (e) => {
    event = e;
  }

  handleClick = () => {
    // 调用子组件方法
    event.sonClick();
  }

  render() {
    return (
      <div>
        <ChildClick onRef={this.onRef} click={this.handleClick} />
      </div>
    )
  }
}

export default ParentClick;
```

**子组件**

子组件接受到父组件传过来的方法，把this反馈了出去。此时的this就是子组件的实例。

```
import React, { Component } from 'react';

class ChildClick extends Component {
  constructor(props){
    super(props)
    this.props.onRef(this);
  }

  sonClick = () =>{
    console.log('我被parent调用了！');
  }
  render() {
    return (
      <div>
         <h2>我是子组件<h2>
      </div>
    )
  }
}

export default ChildClick;
```

父组件给子组件传入一个方法，子组件把自己的实例反馈出来，就是自身的this。

父组件接受到子组件反馈的信息，保存在了 `event` 里面。

此时父组件的另外一个方法 `handleClick` 就可以触发子组件的 `sonClick()` 方法。
