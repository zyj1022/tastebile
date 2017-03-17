# 圣杯布局和双飞翼布局

事实上，圣杯布局其实和双飞翼布局是一回事。它们实现的都是三栏布局，两边的盒子宽度固定，中间盒子自适应，也就是我们常说的 **固-比-固** 布局。它们实现的效果是一样的，差别在于其实现的思想。

那么下面我们就来分别解析两种布局,可以在这里看 [最终demo](https://github.com/zyj1022/tastebile/blob/master/html/the-holy-grail/the-holy-grail.html)

## 圣杯布局

圣杯布局的出现是来自于a list part上的一篇文章 [In Search of the Holy Grail](http://www.alistapart.com/articles/holygrail/)，在西方，圣杯是表达“渴求之物”的意思。那么先看看他怎么实现。

首先看 html 结构

```
<div class="container">
	<div class="main">
		<h2>圣杯布局主内容区</h2>
	</div>
	<div class="left">左边栏内容区</div>
	<div class="right">右边栏内容区</div>
</div>

```

既然是让主内容区 `main` 自适应宽度，那么 `width:100%`,假设我们左、右边栏区宽度都为`200px`,高度是可以自适应的，在这里我们为了直观，统一设置为 `300px`,并分别设置不同的背景色，好实际观测。

这个时候，初步 css 样式是这样

```
/* ------ 圣杯布局初始化 ------ */
.container {}
.container .left {
	width: 200px;
	height: 300px;
	background: #ff8d66;
}
.container .right {
	width: 200px;
	height: 300px;
	background: #7ffe8b;
}
.container .main {
	width: 100%;
	height: 300px;
	background-color:#fcf187;

}
```
这个时候我们看页面实际上是竖排列 `main` `left` `right` 这个三个区域。接下来就要给左右边栏区域腾地方啦
我们让外层div `container` 的左右内间距分别等同于左右边栏的宽度:`padding: 0 200px;`,这个时候，依然是竖着一列，别急，我们先让他们处于同一行位置，怎么做呢，利用 **浮动情况下，负边界值会导致div上移**,所以将这个三个区域都增加`float:left;`,left区域 `margin-left: -100%;`（这个100%实际是main的宽度） 会叠加到 main 区域上方，right 区域`margin-left: -200px`（这200px是右边区域的宽度）,也会叠加到 main 区上方。


此时应该是这个样子：

![圣杯布局](http://og8z552x2.bkt.clouddn.com/git-html-layout-holy.png "圣杯布局")

灰色区域是为left、和right 空出来归位的区域。

而我们的css应该是这样:

```
/* ------ 圣杯布局同行层叠状态 ------ */
.container {
	padding: 0 200px;
}
.container .left {
	width: 200px;
	height: 300px;
	background: #ff8d66;
	float: left;
	margin-left: -100%;
}
.container .right {
	width: 200px;
	height: 300px;
	background: #7ffe8b;
	float: left;
	margin-left: -200px;
}
.container .main {
	width: 100%;
	height: 300px;
	background-color:#fcf187;
	float: left;
}
```

### 圣杯布局——全部 relative 形态

到了这里，相信你已经知道怎么解决div层叠问题了，没错就是利用 `position` 属性了，给三栏都添加 `position: relative;`,然后左边 `left: -200px`，右边`left: 200px`,

这个时候就是最终版了, 别忘记了给外层 `container` 清除浮动。

![圣杯布局](http://og8z552x2.bkt.clouddn.com/git-html-layout-holy2.png "圣杯布局")

```
/* ------ 圣杯布局区 ------ */
.container {
	padding: 0 200px;
    overflow:hidden;
	zoom:1;
}

.container .left {
	width: 200px;
	height: 300px;
	background: #ff8d66;
	float: left;
	margin-left: -100%;
	position: relative;
	left: -200px;
}

.container .right {
	width: 200px;
	height: 300px;
	background: #7ffe8b;
	float: left;
	position: relative;
	left: 200px;
	margin-left: -200px;
}

.container .main {
	width: 100%;
	height: 300px;
	background-color:#fcf187;
	float: left;
	position: relative;
}
```

### 圣杯布局——边栏 absolute 形态

等等，到这里还没结束，在上面div层叠状态的时候，利用 `position: relative;` 属性，
那么你是否想过用 `position: absolute;` 属性呢？事实上这里也可以这么做：

```
/* ------ 圣杯布局区 - 形态二 ------ */
.container-2 {
	padding: 0 200px;
	overflow:hidden;
	zoom:1;
	position: relative; ／* 相对定位 *／
}

.container-2 .left {
	width: 200px;
	height: 300px;
	background: #ff8d66;
	position: absolute;  ／* 绝对定位 *／
	left:0;

}

.container-2 .right {
	width: 200px;
	height: 300px;
	background: #7ffe8b;
	position: absolute; ／* 绝对定位 *／
	right: 0;
}

.container-2 .main {
	width: 100%;
	height: 300px;
	background-color:#fcf187;
	position: relative;
	float:left;
}
```

html 结构完全一样，只是换了个`container-2`的样式名，当然具体样式也有点不同了，但实现效果也一样。


## 双飞翼布局

好，终于到比翼双飞的时刻了，最终实现的效果肯定和圣杯一样咯，那么来看html结构,

```
<div class="wrap">
	<div class="main">
		<div class="main-content">
			<h2>双飞翼布局主内容区</h2>
		</div>
	</div>
	<div class="left">左边栏内容区</div>
	<div class="right">右边栏内容区</div>
</div>
```
这里，外层div换个名字 `wrap`, 乍一看，和圣杯一样呗，主要是css实现方式不同。和圣杯一样，分别设置不同背景色，统一高度，方便观察。

```
/*---- 双飞翼布局初始化 ---*/
.wrap {}
.wrap .left {
	width: 200px;
	height: 300px;
	background: #ccc;
}
.wrap .right {
	width: 200px;
	height: 300px;
	background: #333;

}
.wrap .main {
	width: 100%;
	height: 300px;
	background-color: #666;
}
```

这个时候，和圣杯布局一样的预料，竖着排三行。接下来，其实和圣杯的想法一样，利用 **浮动情况下，负边界值会导致div上移**,所以将这个三个区域都增加`float:left;`,并给 `wrap` 清除浮动。

```
/*---- 双飞翼布局两行状态 ---*/
.wrap {
	zoom: 1;
	overflow: hidden;
}
.wrap .left {
	width: 200px;
	height: 300px;
	background: #ccc;
	float: left;
}
.wrap .right {
	width: 200px;
	height: 300px;
	background: #333;
	float: left;
}
.wrap .main {
	width: 100%;
	height: 300px;
	background-color: #666;
	float: left;
}
.wrap .main-content {
	background: #eee;
}
```

这个时候，让`left`和`right`移动到和`main`同行，left区域需要负100% `margin-left: -100%;`,这个100%其实就是中间main的宽度,right区域则只需要负他自己的宽度即可 `margin-left: -200px;`，现在，三个区域又处于同一行了

![双飞翼布局](http://og8z552x2.bkt.clouddn.com/git-html-layout-fly.png "双飞翼布局")

```
/*---- 双飞翼布局一行层叠状态 ---*/
.wrap {
	zoom: 1;
	overflow: hidden;
}

.wrap .left {
	width: 200px;
	height: 300px;
	background: #ccc;
	float: left;
	margin-left: -100%;
}

.wrap .right {
	width: 200px;
	height: 300px;
	background: #333;
	float: left;
	margin-left: -200px;
}

.wrap .main {
	width: 100%;
	height: 300px;
	background-color: #666;
	float: left;
}

.wrap .main-content {
	background: #eee;
}
```

不过，看到我们的展示图，中间区域的内容区（紫色），被左右区域给覆盖住了，那这个好办，给`main-content`这个区域来 `padding: 0 200px;`,就是左右区域的宽度，最终效果

![双飞翼布局最终状态](http://og8z552x2.bkt.clouddn.com/git-html-layout-fly2.png "双飞翼布局最终状态")

图示可以看到，浅灰色区域就是 `main-content` 区域的 padding

```
/*---- 双飞翼布局最终状态 ---*/
.wrap {
	zoom: 1;
	overflow: hidden;
}

.wrap .left {
	width: 200px;
	height: 300px;
	background: #ccc;
	float: left;
	margin-left: -100%;
}

.wrap .right {
	width: 200px;
	height: 300px;
	background: #333;
	float: left;
	margin-left: -200px;
}

.wrap .main {
	width: 100%;
	height: 300px;
	background-color: #666;
	float: left;
}
```

## 圣杯与双飞翼的区别

通过上面的实践，其实你应该看到了，布局实现的效果都是一样，区别：

- 圣杯的`padding`放在了父层,利用了`float`布局、`position`定位、`margin`负边距
- 双飞翼则将 `padding`放在了`main`内层嵌套的 div上，仅用了 `float`布局、`margin` 负边距


貌似，看起来双飞翼css更简单，但多嵌套了一层 html 标签，也不好说哪个更好了，根据实际需要，灵活应用吧。

那么这种自适应宽度三栏布局的优点有哪些呢，如下：

- 内容与布局分离，main部分自适应宽度，容易在定宽布局和流体布局中切换
- 在浏览器上的兼容性非常好，IE5.5以上都支持
- 三列布局位置切换起来非常方便，只要修改他们自身的margin属性就可以

好了，至此结束。



