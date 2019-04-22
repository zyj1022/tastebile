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

### 创建router文件

在 `src` 有 router目录，在初始化开发环境的时候，已经有 `index.js` 文件就是我们需要的路由文件。

在此将需要路由跳转的界面导入，并做好 router

```
import Vue from 'vue'
import Router from 'vue-router'

import Hello from '@/pages/Hello'
import Home from '@/pages/home'
import About from '@/pages/about'
import Case from '@/pages/case'
import Contact from '@/pages/contact'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Hello',
      component: Hello
    },
    {
      path: '/home',
      name: 'Home',
      component: Home
    },

    {
      path: '/about',
      name: 'About',
      component: About
    },
    {
      path: '/case',
      name: 'Case',
      component: Case
    },
    {
      path: '/contact',
      name: 'Contact',
      component: Contact
    },
    {
      path: '*',
      redirect: '/'
    }
  ]
})

```

在 App.vue 文件里 加入

```
<router-view></router-view>
```

### vue-router的超链接标签“router-link”

在 menu.vue 组件里 加入

```
<div class="menu">
     <ul class="mlist">
       <li><router-link :to="{name: 'Home'}">home</router-link></li>
       <li><router-link :to="{name: 'About'}">about</router-link></li>
       <li><router-link :to="{name: 'Case'}">case</router-link></li>
       <li><router-link :to="{name: 'Contact'}">contact</router-link></li>
     </ul>
</div>
```

### :to属性的对象的可选结构

```
:to="{
	name: 'Home', 
	params: {  // 可选字段 隐性的传递参数(多用来页面传参)
		userId: 365  // 传递的参数
	}, 
	query: { // 可选字段查询参数，就是url里‘?’之后的部分
		name: 'zhi' // 查询参数
	}
}"
```



 

