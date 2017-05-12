# vue-cli 脚手架使用指南

## 简介

vue-cli 是vue.js的脚手架，用于自动生成vue.js模板工程的。

## 使用方法

```
npm install -g vue-cli  // 全局安装vue-cli

// 这里也可以使用 webpack-simple
vue init webpack-simple ProjectName // 生成项目明为 ProjectName 的模版。ProjectName自定义

vue init webpack ProjectName // 生成项目明为 ProjectName 的模版。ProjectName自定义

cd ProjectName

npm i // 初始化安装依赖

```
这样项目就安装完毕了，然后执行

```
npm run dev
```

在浏览器打开http://localhost:8080，则可以看到欢迎页了。
