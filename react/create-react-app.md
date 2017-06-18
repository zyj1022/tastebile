# React 项目快速开始

## 安装 create-react-app

```
npm install create-react-app -g
```
## 创建并启动项目

```
create-react-app react-demo

// 进入项目
cd react-demo

// 安装依赖包
npm install

// 启动项目
npm start
```

自动生成的项目是一个简单的react demo。这个时候项目中会有三个文件夹，我们来分别了解一下这三个文件夹的作用。

### node_modules

项目依赖包存放位置。当我们运行npm install安装package.json中的依赖包时，该文件夹会自动创建，所有的依赖包会安装到该文件夹里。

###  public

主要的作用是html入口文件的存放。当然我们也可以存放其他公用的静态资源，如图片，css等。其中的index.html就是我们项目的入口html文件。

###  src

组件的存放目录。在create-react-app创建的项目中，每一个单独的文件都可以被看成一个单独的模块，单独的image，单独的css，单独js等，而所有的组件都存放于src目录中，其中index.js则是js的入口文件。虽然我们并没有在index.html中使用script标签引入他，但是他的作用就和此一样。

至此，浏览器可以打开页面查看demo效果，并进行开发。
