# vuex 状态管理系统

> Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
>
> 官方文档 https://vuex.vuejs.org/zh-cn/intro.html
>
> [demo例子查看](https://github.com/zyj1022/vue-start) https://github.com/zyj1022/vue-start


## vuex里面是什么

- 应用级的状态集中放在store中；
- 改变状态的方式是提交mutations，这是个同步的事物；
- 异步逻辑应该封装在action中。
