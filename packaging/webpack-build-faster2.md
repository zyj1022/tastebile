# webpack 打包优化续篇

从更多的维度对webpack打包进行组合。

## 缩小文件搜索范围

Webpack从Entry出发递归解析导入语句寻找相应的依赖，在项目庞大的时候，文件量大增，递归解析速度明显的下降。

减少被loader匹配到的文件数,通过include缩小搜索范围。
```
include:path.resolve(_dirname,'src')        //只对src目录下的文件进行处理
```
`reolve.modules` 指定第三方模块的目录，默认情况下，webpack会先从`./node_modules`，如果未找到则在从`../node_modules`，依次沿上级目录寻找。当第三方模块固定在`./node_modules`时就不需要递归寻找。

```
resolve:{
    modules:[path.resolve(_dirname,'node_modules')],
}
```
`resolve.alias` 对于某些庞大的第三方库，替换其导入路径，直接使用打包完成最小的min文件，例如React
```
resolve:{
    alias:{
        'react':path.resolve(_dirname,'./node_modules/react/dist/react.min.js'),
    }
}
```
使用该方法的缺点是会影响Tree-Shaking，一般对于整体性较强的库来使用。` * resolve.extensions`配置 指定默认的匹配的后缀，减少不必要的尝试

```
resolve:{
    extensions:['js','css']
}
```
`module.noParse` 配置 让 webpack 忽略没有采用模块化标准的文件，不用去递归解析这一部分，如 JQuery

```
noParse:[/react\.min\.js%/],        //忽略react.min.js
```

## DllPlugin 动态链接库

**动态链接库的思想**

将网页依赖的基础模块抽离，打包到一个个单独的动态链接中。（一个链接可包括多个模块）
当导入的模块存在于动态链接库中时，模块不用再次被打包，直接从动态链接库中获取
页面依赖的动态链接库都需要被加载

**原理**

可以把DLL看做一个代码仓库，这个代码仓库里面拷贝了程序所需要的库函数，并且把这些函数存储在一个独立的文件里。项目编译时的时候并不会把其加入编译之后，而是当程序运行时需要调用到里面的函数时，动态的将函数地址传给调用程序

构建速度的对性能提升很大，因为动态链接库只用编译一次，之后的构建都不会再编译动态链接库中的模块了。

使用 DllPlugin 需要两个webpack的内部插件

**DllPlugin 插件**

用于打包出一个个单独的动态链接库文件

**DllReferencePlugin 插件**

引入 Dllplugin 打包好的动态链接库文件

步骤 * 新建一个webpack配置文件用于打包输出Dll文件

```
module.export = {
    entry :{
        react:['react','react-dom'],
    },
    output:{
        //输出的动态库的文件名称
        filename:'[name].dll.js',
        path:path.resolve(_dirname,'dist'),
        //存放动态库的全局变量名称
        library:'_dll_[name]'
    },
    plugins:[
        new DllPlugin({
            //动态库的全局变量名称，name和output.library一致，其实是manifest.json中的name值
            name:'_dll_[name]',
            path:path.join(_dirname,'dist','[name]_manifest.json'),
        }),
    ]
}
```

打包过后的会生成两个文件文件

**react_manifest.json**

```
{
    //文件暴露在全局变量中的名称
    "name": "_dll_react",
    "content": {
        //每个模块
        "./node_modules/_react@16.6.3@react/index.js": {
            "id": 0,
            "buildMeta": {
                "providedExports": true
            }
        },
        "./node_modules/_object-assign@4.1.1@object-assign/index.js": {
            "id": 1,
            "buildMeta": {
                "providedExports": true
            }
        },
        .........
    }
}
```
**react.dll.js**

其中 `react_manifest.json` 描述了对应的 `dll.js` 文件包含了哪些模块，每个模块的路径，id。当进行编译的时候，`main.js`入口文件遇到`dll.js`中的模块时，能够通过 `dll.js` 文件暴露的全局变量`_dll_react`直接获取。

使用动态链接库文件

```
module.exports = {
    entry: './src/index.js',
    output: {
        filename: '[name]_[hash:8].js',
        path: path.resolve(__dirname, 'dist')
    },
    plugins: [
        new DllReferencePlugin({
            //告诉webpack使用的动态链接库
            manifest:require('./dist/react_manifest.json'),
        })
    ]
}
```

## web-dev-server的优化

**自动刷新的原理**

向网页中注入代理客户端代码，代理客户端和webpack通过Websocket通信控制刷新整个网页
将要开发的网页放进iframe中，通过刷新iframe来刷新整个网页

**代理客户端刷新**

当项目输出多个chunk的时候，devserver 并不知道网页依赖于哪个chunk，于是简单的粗暴向所有的chunk都注入代理客户端，导致问题就是当输出的chunk变多的时候，编译速度明显的下降。
有两种方式可以优化其构建速度 

- 关闭不是很好用的inline模式，`--inline false`，这样`devser`会采用第二种方式刷新网页方式，将网页放入iframe中，通过访问`localhost.cn:8080/webpack-dev-server`就能够访问到原来的网页。

- 手动向网页注入代理客户端脚本，向index.html插入 `<script src="localhost.cn:8080/webpack-dev-server"></script>`

**模块热替换**

模块热替换与自动刷新原理相似，都是在网页中注入一个代理客户端来连接webpack和网页。

**原理**

模块热替换会对源码进行相应的处理 假设现在有两个js文件，一个`main.js`,一个 `tempComponent.js`，`main.js`是`tempComponent.js` 的父组件。当开启 `hmr` 的时候，`hotmodule` 会去改变 `main.js` 的源码如下

```
//加入了module.hot，accepet 第一个参数是代表要接收哪些子模块的替换，第二个参数是子模块发生变化的回调函数
if(module.hot){
    module.hot.accepet(['./tempComponent'],()=>{
        render(<tempComponent/>,window.getElementById("app"));
    });
    })
}
```
当修改了`tempComponent.js`的时候，`tempComponent` 的模块更新就会一层一层的往上传，直到某层文件接收当前文件的变化，例如上面的`main.js`接收`tempComponent.js`的更新之后，就会调用回调函数，将tempComponent重新替换掉，来实现不刷新使网页变化。但是如果修改的是最外层的`main.js`就会刷新整个网页。
当模块发生变化后，事件一层一层的上传，但是到最后也没有相应的文件来接收变化，就会刷新整个网页。