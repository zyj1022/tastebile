# SetInterval与setTimeout使用方法详解

## 基本介绍
setTimeout和setInterval的语法相同。
它们都有两个参数，一个是将要执行的代码字符串，还有一个是以毫秒为单位的时间间隔，当过了那个时间段之后就将执行那段代码。
不过这两个函数还是有区别的，setInterval在执行完一次代码之后，经过了那个固定的时间间隔，它还会自动重复执行代码，
而setTimeout只执行一次那段代码。

**区别：**
- window.setTimeout("function",time)；// 设置一个超时对象，只执行一次,无周期
- window.setInterval("function",time)；// 设置一个超时对象，周期＝'交互时间'

**停止定时：**

- window.clearTimeout(对象) 清除已设置的setTimeout对象
- window.clearInterval(对象) 清除已设置的setInterval对象

## “this”问题

由setTimeout()调用的代码运行在与所在函数完全分离的执行环境上. 这会导致,这些代码中包含的 this 关键字会指向 window (全局对象)对象,这和所期望的this的值是不一样的。setInterval的情况类似。

```
function shape(name) {
	this.name = name;
	this.timer = function() {
		alert('my shape is ' + this.name)
	};
	setTimeout(this.timer, 50);
}
new shape('rectangle');

// 输出结果为 my shape is
```
没有被传进去，分别用chrome，firefox和IE9实验了下，都是这个结果。

**解决方法：**

```
function shape(name) {
	this.name = name;
	this.timer = function() {
		alert('my shape is ' + this.name)
	};
	var _this = this;
	setTimeout(function() {
		_this.timer.call(_this)
	}, 50);
}
new shape('rectangle');
```

设置一个局部变量_this，然后放到setTimeout的函数变量中，timer执行call或apply，设置this值。
function能够调用局部变量_this，多亏了Javascript的闭包。


## setTimeout与setInterval之间的一个区别

javascript都是以单线程的方式运行于浏览器的javascript引擎中的, setTimeout和setInterval的作用只是把你要执行的代码在你设定的一个时间点插入js引擎维护的一个代码队列中, 插入代码队列并不意味着你的代码就会立马执行的,理解这一点很重要. 而且setTimeout和setInterval还有点不一样.

```
setTimeout(function() {
	 // console.log("hello setTimeout!");
	setTimeout(arguments.callee, 1000);
}, 10);

setInterval(function() {
	// console.log("hello setInterval!");
}, 1000);
```

看上去，两个功能是差不多的，但是里面其实是不一样的。
setTimeout回调函数的执行和上一次执行之间的间隔至少有100ms（可能会更多，但不会少于100ms）
setInterval的回调函数将尝试每隔100ms执行一次，不论上次是否执行完毕，时间间隔理论上是会<=delay的。

情况大致是这样的：由于count函数的执行时间远大于setInterval的定时间隔，那么定时触发线程就会源源不断的产生异步定时事件，并放到任务队列尾而不管它们是否已被处理，但一旦一个定时事件任务处理完，这些排列中的剩余定时事件就依次不间断的被执行。

## requtestAnimationFrame

为了解决setTimeout不准确问题和提高动画的流畅度，大部分浏览器厂商都实现了requestAnimationFrame方法，它的主要目的就是来实现动画。requestAnimationFrame只接受一个参数，就是回调函数。它不需要设置时间间隔，因为它会在浏览器每次刷新之前执行回调函数的任务。这样我们动画的更新就能和浏览器的刷新频率保持一直，从而大大提高动画的流畅度，使用GPU去控制什么时候更新，也可以减小CPU的压力。

在游戏引擎中，如果使用setTiemout方法，那么它就是使用CPU去计算更新的。有一点需要注意的是：CPU计算更新的任务完成后，并不代表这些更新就能立刻显示在浏览器上，因为还必须等待浏览器的下一次刷新，才能显示更新后的动画。因此，如果CPU的的更新频率和浏览的刷新频率存在误差，那么势必会影响动画的流畅性。因此对于游戏引擎中，动画部分的更新，可以采用该方法。

> 参考文档
>
> [setTimeout和setInterval的区别你真的了解吗?](http://blog.csdn.net/cui_angel/article/details/7768241)
