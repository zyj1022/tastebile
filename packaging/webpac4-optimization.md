# [webpack4 optimization](https://www.webpackjs.com/plugins/split-chunks-plugin/) 使用介绍

从webpack4开始官方移除了 commonchunk 插件，改用了 `optimization` 属性进行更加灵活的配置，下面的代码即是optimize.splitChunks 中的一些配置参考介绍：

可以实现很多功能，最常见的功能是：

- three shaking 只支持ES Module模块的引入，删除没有引用的模块打包
- Code Splitting 拆分代码，提高执行效率和用户体验

```
/**
   * webpack中实现代码分割的两种方式：
   * 1.同步代码：只需要在webpack配置文件总做optimization的配置即可
   * 2.异步代码(import)：异步代码，无需做任何配置，会自动进行代码分割，放置到新的文件中
   */
  optimization: {
    splitChunks: {
      chunks: "all",            //async异步代码分割 initial同步代码分割 all同步异步分割都开启
      minSize: 30000,           //字节 引入的文件大于30kb才进行分割
      //maxSize: 50000,         //50kb，尝试将大于50kb的文件拆分成n个50kb的文件
      minChunks: 1,             //模块至少使用次数
      maxAsyncRequests: 5,      //同时加载的模块数量最多是5个，只分割出同时引入的前5个文件
      maxInitialRequests: 3,    //首页加载的时候引入的文件最多3个
      automaticNameDelimiter: '~', //缓存组和生成文件名称之间的连接符
      name: true,                  //缓存组里面的filename生效，覆盖默认命名
      cacheGroups: { //缓存组，将所有加载模块放在缓存里面一起分割打包
        vendors: {  //自定义打包模块
          test: /[\\/]node_modules[\\/]/,
          priority: -10, //优先级，先打包到哪个组里面，值越大，优先级越高
          filename: 'vendors.js',
        },
        default: { //默认打包模块
          priority: -20,
          reuseExistingChunk: true, //模块嵌套引入时，判断是否复用已经被打包的模块
          filename: 'common.js'
        }
      }
    }
  }

```

## splitChunks 基本配置

- 1.chunks：分割代码的模式（默认配置是async）

  - `async` 异步代码分割模式：只分割出异步引入的模块 （动态加载import()）
  - `initial` 同步代码分割模式：只分割同步引入的模块
  - `all` 同步异步都分割模式：在所有配置条件都满足的情况下，无论如何引入模块都会进行分割

- 2.`minSize` 和 `maxSize` （单位：字节）
  `minSize` 指的是引入的模块的最小值
  `maxSize` 指的是引入的模块的最大值，当引入的模块大小大于最大值时，webpack 会尝试将这个模块以最大值为准分割成多个模块，前提是这个模块可以分割，比如 lodash 的提交大于50KB，那么设置 `maxSize：5000` 时，依然打包出一个文件来，故此属性一般不用

- 3.`minChunks`：模块至少使用次数(**值必须设置为大于等于1的数**)
  当值为2时，代表只引用了一次的模块不做分割打包处理

- 4.`maxAsyncRequests`：同时加载的模块数量最大值(**值必须设置为大于等于1的数**)
  也就是用来限制异步模块内部的并行最大请求数的，说白了你可以理解为是每个import()它里面的最大并行请求数量

- 5.`maxInitialRequests`：首次加载引入模块可分割的最大值(**值必须设置为大于等于1的数**)，也就是用来限制入口的拆分数量的参数。
- 6.`automaticNameDelimiter`：缓存组名称和生成文件名称之间的连接字符串
- 7.`name`： 设置为 `true` 时，缓存组里面的 filename 生效，覆盖默认命名方式

## cacheGroups 缓存组

- `cacheGroup` 必须同时满足各个条件才能生效, 例如 `minSize` 或是 `minChunks` 等条件必须同时满足可以拆分

- `splitChunks` 的配置项都是作用于 `cacheGroup` 上的，如果将 `cacheGroup` 的默认两个分组 `vendor` 和 `default` 设置为 `false` ，则 `splitChunks` 就不会起作用

```
cacheGroups: { // 缓存组，将所有加载模块放在缓存里面一起分割打包
  vendors: {  // 自定义打包模块
    test: /[\\/]node_modules[\\/]/,
    priority: -10, // 优先级，先打包到哪个组里面，值越大，优先级越高
    filename: 'vendors.js',
  },
  default: { //默认打包模块
    priority: -20,
    reuseExistingChunk: true, // 模块嵌套引入时，判断是否复用已经被打包的模块
    filename: 'common.js'
  }
}
```

`vendors` 为自定义打包模块，当 `vendors.test` 设置为 `/[\/]node_modules[\/]/` ，webpack 将把使用 npm 命令安装的第三方库打包到 `vendors` 缓存组里面。
- 1.`test`：正则匹配打包分离的文件条件
- 2.`priority`：定义打包组的打包顺序优先级，值越大，优先级越高
- 3.`filename`：打包模块输出的文件名，默认为 缓存组名称（vendors） + 连接字符串（automaticNameDelimiter） + 模块入口文件（main.js） 例如：vendors~main.js

    - `default` 为默认模块打包的缓存组，一般情况下打包业务模块编码。
    - `reuseExistingChunk：` 模块嵌套引入时，判断是否复用已经被打包的模块

> 同类文章

> [理解webpack4.splitChunks](https://www.cnblogs.com/kwzm/p/10314438.html)

> [理解webpack4.splitChunks之cacheGroups](https://www.cnblogs.com/kwzm/p/10315080.html)