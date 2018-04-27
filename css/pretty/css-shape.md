# CSS图形

尝试利用CSS3来绘制一些图形，权当练习属性，网上搜罗一番，已经有很多大神画的不错了
也一并搜罗归纳一起。不过不能光看不练，抽时间练习画点好玩的，图形离不开点线(各种曲线)面
剩下的也就是熟能生巧了。当然canvas能做到更多，在此先不并入。

## 弧形画法

也就是圆啦，利用 `border-radius` 属性

```
// border-radius: 左上 右上 右下 左下
border-radius: 10px 20px 30px 40px;
border-radius: 50%;  // 正圆
```
如上所述，利用不同数值，将矩形都四个角切圆。

## 2D转换

图形中不仅有直线，还有位置的倾斜，旋转，这个是时候就是 `transform` 出场了

### transform 转换方法

- translate(x,y) 元素从其当前位置移动
- rotate(angle) 元素顺时针旋转给定的角度
- scale(x,y) 元素的尺寸会增加或减少
- skew(angle) 元素翻转给定的角度
- matrix() 方法需要六个参数，包含数学函数，允许您：旋转、缩放、移动以及倾斜元素

## 动画制作

画好图形希望有点动作，走动，眨眼，摇摆等，基本上可以利用 CSS3 `@keyframes`制作。

### CSS3 动画属性


| 属性        |  描述  |
| --------   | :----: |
| @keyframes        |  规定动画。    |
| animation         |   所有动画属性的简写属性，除了 animation-play-state 属性。   |
| animation-name         |   规定 @keyframes 动画的名称。    |
|animation-duration	 |规定动画完成一个周期所花费的秒或毫秒。默认是 0。 |
|animation-timing-function	|规定动画的速度曲线。默认是 "ease"。 |
|animation-delay	|规定动画何时开始。默认是 0。 |
|animation-iteration-count	|规定动画被播放的次数。默认是 1。 |
|animation-direction	|规定动画是否在下一周期逆向地播放。默认是 "normal"。 |
|animation-play-state	|规定动画是否正在运行或暂停。默认是 "running"。 |
|animation-fill-mode	|规定对象动画时间之外的状态。|

### 动画举例

```
div {
    width:100px; height:100px; background:#000;
	animation: myfirst 5s linear 2s infinite alternate;
}
@keyframes myfirst {
0%   {background:red; left:0px; top:0px;}
25%  {background:yellow; left:200px; top:0px;}
50%  {background:blue; left:200px; top:200px;}
75%  {background:green; left:0px; top:200px;}
100% {background:red; left:0px; top:0px;}
}
// @-moz-keyframes
// @-webkit-keyframes
```
简单举例，上例简单用法,矩形绕圈运动。

> CSS3-图形实例
>
> [眨眼的螳螂](./css-grasshopper.html)
>
> [走动的小黄人](./css-minion.html)
