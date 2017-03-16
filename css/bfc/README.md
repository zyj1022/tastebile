# CSS之BFC


## 什么是 BFC：Block Formatting Context
BFC就是一种布局方式，在这种布局方式下，盒子们自所在的 containing block 顶部起一个接一个垂直排列，
水平方向上撑满整个宽度 (除非内部的盒子自己内部建立了新的 BFC)



一个 block formatting context 是web页面可视化CSS渲染的一个部分，是一块 block boxes 排布以及 float 元素相互作用的区域。

可以理解为一个作用范围，在一个BFC里的布局与其之外的布局不相关或者说不相互影响。有一个形象的例子可作参考：

把一个页面想象成大的集装箱，这个集装箱里装的货物就是HTML元素。在现实生活中为了避免不同人的货物相互混淆，
都是把 货物打好包装再装入集装箱，这样的话无论你包装里面的货物怎么摆放，都不会影响到其他人的货物。
那么这个包装就可以被想象成Block Formatting Context。


## 如何创建BFC 

当一个HTML元素满足下面条件的任何一点，都可以产生 Block Formatting Context：

- float的值不为”none”
- overflow的值不为”visible”
- display的值为 “table-cell”, “table-caption”, or “inline-block” 中的任何一个
- position的值不为 “static” 或 “relative” 中的任何一个

## 利用BFC来布局页面