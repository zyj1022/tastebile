## 设计模式之职责链模式(Chain of responsibility)

## 定义

**职责链模式的定义**：使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间 5 的耦合关系，将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。

职责链模式的名字非常形象，一系列可能会处理请求的对象被连接成一条链，请求在这些对 象之间依次传递，直到遇到一个可以处理它的对象，我们把这些对象称为链中的节点。

<!-- more -->


## 使用实例


假设我们负责一个售卖手机的电商网站，经过分别交纳 500 元定金和 200 元定金的两轮预定后(订单已在此时生成)，现在已经到了正式购买的阶段。公司针对支付过定金的用户有一定的优惠政策。在正式购买后，已经支付过 500 元定金的用 户会收到 100 元的商城优惠券，200 元定金的用户可以收到 50 元的优惠券，而之前没有支付定金 的用户只能进入普通购买模式，也就是没有优惠券，且在库存有限的情况下不一定保证能买到。


在页面加载之初，后端会传递给页面几个字段。
- **orderType**：表示订单类型(定金用户或者普通购买用户)，code 的值为 1 的时候是 500 元 定金用户，为 2 的时候是 200 元定金用户，为 3 的时候是普通购买用户。
- **pay**：表示用户是否已经支付定金，值为 true 或者 false, 虽然用户已经下过 500 元定金的 订单，但如果他一直没有支付定金，现在只能降级进入普通购买模式。- **stock**：表示当前用于普通购买的手机库存数量，已经支付过 500 元或者 200 元定金的用 户不受此限制。

看到上面的需求，简单点可以想到有用 `if` `else` 分之来实现，但肯定会比较难以维护及扩展。

那么就将此需求用职责链模式重构代码：

首先需要 3 种购买模式的节点函数，我们约定，如果某个节点不能处理请求，则返回一个特定的字符串 'nextSuccessor'来表示该请求需要继续往后面传递:

```
// 500 元订单
var order500 = function(orderType, pay, stock) {
     if (orderType === 1 && pay === true ) {
         console.log('500 元定金预购, 得到 100 优惠券');
    } else {
        return 'nextSuccessor'; // 我不知道下一个节点是谁，反正把请求往后面传递
    }
}
// 200 元订单
var order200 = function(orderType, pay, stock) {
    if (orderType === 2 && pay === true ) {
        console.log('200 元定金预购, 得到 50 优惠券');
   } else {
       return 'nextSuccessor'; // 我不知道下一个节点是谁，反正把请求往后面传递
   }
}
// 普通购买订单
var orderNormal = function(orderType, pay, stock) {
    if (stock > 0) {
        console.log('普通购买, 无优惠券');
   } else {
       console.log( '手机库存不足' );
   }
}
```

接下来需要把函数包装进职责链节点，我们定义一个构造函数 Chain，在 new Chain 的时候传 递的参数即为需要被包装的函数， 同时它还拥有一个实例属性 this.successor，表示在链中的下 一个节点。

此外 Chain 的 prototype 中还有两个函数，它们的作用如下所示:

```
// Chain.prototype.setNextSuccessor 指定在链中的下一个节点
// Chain.prototype.passRequest 传递请求给某个节点

var Chain = function(fn){
    this.fn = fn;
    this.successor = null;
};
Chain.prototype.setNextSuccessor = function(successor) {
    return this.successor = successor;
};
Chain.prototype.passRequest = function() {
    var ret = this.fn.apply(this, arguments);
    if (ret === 'nextSuccessor') {
        return this.successor && this.successor.passRequest.apply( this.successor, arguments );
    }
    return ret;
}
```

现在我们把 3 个订单函数分别包装成职责链的节点:

```
var chainOrder500 = new Chain(order500);
var chainOrder200 = new Chain(order200);
var chainOrderNormal = new Chain(orderNormal);
```
然后指定节点在职责链中的顺序:

```
chainOrder500.setNextSuccessor(chainOrder200);
chainOrder200.setNextSuccessor(chainOrderNormal);
```

最后把请求传递给第一个节点:

```
chainOrder500.passRequest(1, true, 500); // 输出:500 元定金预购，得到 100 优惠券
chainOrder500.passRequest(2, true, 500); // 输出:200 元定金预购，得到 50 优惠券
chainOrder500.passRequest(3, true, 500); // 输出:普通购买，无优惠券
chainOrder500.passRequest(1, false, 0);  // 输出:手机库存不足
```


通过改进，我们可以自由灵活地增加、移除和修改链中的节点顺序，假如某天网站运营人员 又想出了支持 300 元定金购买，那我们就在该链中增加一个节点即可:


```
var order300 = function(){
	// 具体实现略
};
chainOrder300= new Chain( order300 );
chainOrder500.setNextSuccessor( chainOrder300);
chainOrder300.setNextSuccessor( chainOrder200);
```
在这里完全不用理会原来的订单函数代码，我们要做的只是增 加一个节点，然后重新设置链中相关节点的顺序。


## 总结

职责链模式可以很好地帮助我们管理代码，降低发起请求的对象和处理请求的对象之间的耦合性。职责链中的节点数量和顺序是可以自由变化的，我们可以在运行时决定链中包含哪些节点。
无论是作用域链、原型链，还是 DOM 节点中的事件冒泡，我们都能从中找到职责链模式的影子。职责链模式还可以和组合模式结合在一起，用来连接部件和父部件，或是提高组合对象的效率。

-----

> 参考引用资料
>
> 《JavaScript设计模式与开发实践》
>
> [汤姆大叔的博客——深入理解JavaScript系列](http://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html)
>
