# vue-router 使用指南

> [demo例子查看](https://github.com/zyj1022/vue-start) https://github.com/zyj1022/vue-start

## 简介

对于大多数单页面应用，都推荐使用官方支持的vue-router库。vue的单页面应用是基于路由和组件的，路由用于设定访问路径，并将路径和组件映射起来。传统的页面应用，是用一些超链接来实现页面切换和跳转的。在vue-router单页面应用中，则是路径之间的切换，也就是组件的切换。更多细节可以看 [vue-router文档](https://router.vuejs.org/zh-cn)。

## 使用方法

首先用 vue-cli 快速搭建开发环境,如果需要使用vue-router

```
npm install vue-router
```
通常会在`main.js` 文件增加如下：

```
import VueRouter from 'vue-router'
Vue.use(VueRouter)
```
