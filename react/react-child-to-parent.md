# React 子组件调用父组件中的方法

**父组件**

```
import React, { Component } from 'react';
import ChildClick from './ChildClick';
class ParentClick extends Component{
  handleClick(){
    alert("子组件调我啦!");
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

```
import React, { Component } from 'react';

class ChildClick extends Component {
  sonClick = () => {
    // 调用父组件方法
    this.props.handleClick();
  }
  render() {
    return (
      <div>
         <h2>我是子组件<h2>
         <button onClick={this.sonClick}>调用父组件中的方法</button>
      </div>
    )
  }
}

export default ChildClick;
```
