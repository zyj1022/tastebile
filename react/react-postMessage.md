# React中使用postMessage解决跨域传值及样式更改

## 简介
在平时开发中，除了与服务端客户端传值之外，我们前端传值也会面临一些传值的问题

- 多窗口之间传值
- 页面与嵌套的iframe消息传递

## 跨域解决之postMessage方式

发消息方调用 `postMessage` 方法发送消息 `postMessage` 是html5引入的API可以更方便、有效、安全的解决这些问题。postMessage() 方法允许来自不同源的脚本采用异步方式进行有限的通信，可以实现跨文本档、多窗口、跨域消息传递。

收消息方用 `MessageEvent` 监听消息。

**postMessage(data,origin) 方法接受两个参数**

- data:要传递的数据， html5 规范中提到该参数可以是JavaScript的任意基本类型或可复制的对象，然而并不是所有浏览器都做到了这点儿，部分浏览器只能处理字符串参数，所以我们在传递参数的时候需要使用JSON.stringify() 方法对对象参数序列化，在低版本IE中引用json2.js可以实现类似效果。
- origin：字符串参数，指明目标窗口的源， 协议+主机+端口号[+URL]，URL会被忽略，所以可以不写，这个参数是为了安全考虑，someWindow.postMessage() 方法只会在someWindow所在的源(url的protocol, host, port)和指定源一致时才会成功触发message
- event，当然如果愿意也可以将参数设置为"*"，someWindow可以在任意源，如果要指定和当前窗口同源的话设置为"/"。

**MessageEvent的属性**
- data：顾名思义，是传递来的message
- source：发送消息的窗口对象
- origin：发送消息窗口的源（协议+主机+端口号）

## 同域/跨域父子页面间通讯

父页面

```
import React, { Component } from 'react';
import { autobind } from 'core-decorators';

export default class FatherPage extends Component {
  componentDidMount() {
    // 监听子页面发回的数据
		window.addEventListener('message', this.receiveMessage);
    // 父页面向子页面发送数据
    this.refs.iframe.postMessage({style: '#000'}, '*');
	}

	@autobind
	receiveMessage(e) {
		if (e.data) {
			console.log('from-son', e.data);
		}
	}

  render() {
    return (
      <h1>Hello FatherPage!</h1>
      <iframe
        ref="iframe"
        name="iframe"
        id="iframe"
        className="iframe-box"
        style={{ height: 500 }}
        src='/son-path'
      />
    )
  }
}
```

子页面

可以通过父页面传 `style` 来更改子页面样式

```
import React, { Component } from 'react';
import { observable } from 'mobx';
import { autobind } from 'core-decorators';
import { Button } from 'antd';

export default class SonPage extends Component {
  @observable bg = '#fff';

  componentDidMount() {
    // 监听父页面发送的数据
		window.addEventListener('message', this.receiveMessage);
	}

	@autobind
	receiveMessage(e) {
		if (e.data) {
			console.log('from-father', e.data);
      if(e.data.style) {
        this.bg = e.data.style
      }
		}
	}

  @autobind
	onTest() {
    // 触发父页面的 message 事件 * 代表任何页面都可接收
		window.top.postMessage('hi', '*');
	}

  render() {
    return (
      <h1 style={{backgroundColor: `${this.bg}`}}>Hello SonPage!</h1>
      <Button onClick={this.onTest} type="primary">测试</Button>
    )
  }
}
```


