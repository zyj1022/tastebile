# vue2.0 组件之间传值方式

前提是已经搭建好vue-cli开发环境对情况下，针对组件间传值

> [demo例子查看](https://github.com/zyj1022/vue-start) https://github.com/zyj1022/vue-start

## 父组件给子组件传值

**使用prop传递数据**

> props 可以是数组或对象，用于接收来自父组件的数据。
> props 可以是简单的数组，或者使用对象作为替代，对象允许配置高级选项，如类型检测、自定义校验和设置默认值。

prop 是父组件用来传递数据的一个自定义属性。子组件需要显式地用 props 选项声明 “prop”：

```
Vue.component('child', {
  // 声明 props
  props: ['message'],
  // 就像 data 一样，prop 可以用在模板内
  // 同样也可以在 vm 实例中像 “this.message” 这样使用
  template: '<span>{{ message }}</span>'
})
```

然后向它传入一个普通字符串：

```
<child :message="hello!"></child>
```
以上，总结要想父组件给子组件传值

- 子组件在 `props` 中创建一个属性比如：`message`，用于接收父组件传过来对值
- 在父组调用子组件的标签中添加子组件 `props` 中创建的属性 `:message = data`
- 把需要传给子组件的值赋给该属性 `this.data = "hello child"`


## 子组件给父组件传值

子组件给父组件传值方式:

- 使用 $on(eventName) 监听事件
- 使用 $emit(eventName) 触发事

父组件可以在使用子组件的地方直接用 v-on 来监听子组件触发的事件

```
/**
 * event: 要触发的事件
 * args: 将要传给父组件的参数
 */

this.$emit(event,...args);
```

子组件：

```
<template>
	<button @click="myclick">按钮</button>
</template>
methods:{
    myclick(){
        let data = {
            test:'测试数据'
        };
        this.$emit('myEvent',data,'我也是数据！');
    }
}
```

父组件：

```
// 在使用子组件对地方用 v-on:myEvent 监听子组件触发的事件，简写为 @myEvent
<childNode @myEvent = "fevent"></childNode>
methods:{
    fevent(...data){
        console.log('子组件传过来对数据:', data );
		// data为包含传过来所有数据的数组，第一个元素是对象，第二个元素是字符串
    }
}
```
