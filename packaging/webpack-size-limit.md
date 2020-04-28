# WebPack 警告WARNING in asset size limit: The following asset(s) exceed the recommended size limit (244 KiB).解决

> WARNING in asset size limit: The following asset(s) exceed the recommended size limit (244 KiB).
> This can impact web performance.

## 解决方法：

WEBPACK中添加如下配置:

```
performance: {
  hints: 'warning', // 枚举
  maxAssetSize: 30000000, // 整数类型（以字节为单位）
  maxEntrypointSize: 50000000, // 整数类型（以字节为单位）
  assetFilter: function(assetFilename) {
    // 提供资源文件名的断言函数
    return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
  }
},
```