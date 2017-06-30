# JavaScript进阶之路——认识和使用Promise

近几年随着JavaScript开发模式的逐渐成熟，CommonJS规范顺势而生，其中就包括提出了Promise规范，Promise完全改变了js异步编程的写法，让异步编程变得十分的易于理解。今天我们就来了解一下Promise~~

# 初始promise？

## 1.什么是promise？

　　Promise可能大家都不陌生，因为Promise规范已经出来好一段时间了，同时Promise也已经纳入了ES6，而且高版本的chrome、firefox浏览器都已经原生实现了Promise，只不过和现如今流行的类Promise类库相比少些API。

　　所谓Promise，字面上可以理解为“承诺”，就是说A调用B，B返回一个“承诺”给A，然后A就可以在写计划的时候这么写：当B返回结果给我的时候，A执行方案S1，反之如果B因为什么原因没有给到A想要的结果，那么A执行应急方案S2，这样一来，所有的潜在风险都在A的可控范围之内了。

Promise规范如下：

**Promise的状态**

我们已经大概了解了Promise的处理流程，接下来让我们来稍微整理一下Promise的状态。

用new Promise 实例化的promise对象有以下三个状态。

- "has-resolution" - Fulfilled
  resolve(成功)时。此时会调用 onFulfilled

- "has-rejection" - Rejected
reject(失败)时。此时会调用 onRejected

- "unresolved" - Pending
既不是resolve也不是reject的状态。也就是promise对象刚被创建后的初始化状态等

一个promise的状态只可能从“等待”转到“完成”态或者“拒绝”态，不能逆向转换，同时“完成”态和“拒绝”态不能相互转换promise必须实现then方法（可以说，then就是promise的核心），而且then必须返回一个promise，同一个promise的then可以调用多次，并且回调的执行顺序跟它们被定义时的顺序一致
then方法接受两个参数，第一个参数是成功时的回调，在promise由“等待”态转换到“完成”态时调用，另一个是失败时的回调，在promise由“等待”态转换到“拒绝”态时调用。同时，then可以接受另一个promise传入，也接受一个“类then”的对象或方法，即thenable对象。

## 2.promise原理分析

　　可以看到promise的规范并不是很多，下面我们一边分析promise一边自己写一个promise的实现。Promise实现的大致思路如下：

　　构造函数Promise接受一个函数resolver，可以理解为传入一个异步任务，resolver接受两个参数，一个是成功时的回调，一个是失败时的回调，这两参数和通过then传入的参数是对等的。

其次是then的实现，由于Promise要求then必须返回一个promise，所以在then调用的时候会新生成一个promise，挂在当前promise的_next上，同一个promise多次调用都只会返回之前生成的_next。

由于then方法接受的两个参数都是可选的，而且类型也没限制，可以是函数，也可以是一个具体的值，还可以是另一个promise。下面是then的具体实现：

```
Promise.prototype.then = function(resolve, reject) {  
    var next = this._next || (this._next = Promise());  
    var status = this.status;  
    var x;  
  
    if('pending' === status) {  
        isFn(resolve) && this._resolves.push(resolve);  
        isFn(reject) && this._rejects.push(reject);  
        return next;  
    }  
  
    if('resolved' === status) {  
        if(!isFn(resolve)) {  
            next.resolve(resolve);  
        } else {  
            try {  
                x = resolve(this.value);  
                resolveX(next, x);  
            } catch(e) {  
                this.reject(e);  
            }  
        }  
        return next;  
    }  
  
    if('rejected' === status) {  
        if(!isFn(reject)) {  
            next.reject(reject);  
        } else {  
            try {  
                x = reject(this.reason);  
                resolveX(next, x);  
            } catch(e) {  
                this.reject(e);  
            }  
        }  
        return next;  
    }  
}; 
```

这里，then做了简化，其他promise类库的实现比这个要复杂得多，同时功能也更多，比如还有第三个参数——notify，表示promise当前的进度，这在设计文件上传等时很有用。对then的各种参数的处理是最复杂的部分，有兴趣的同学可以参看其他类Promise库的实现。

在then的基础上，应该还需要至少两个方法，分别是完成promise的状态从pending到resolved或rejected的转换，同时执行相应的回调队列，即resolve()和reject()方法。

到此，一个简单的promise就设计完成了，下面简单实现下两个promise化的函数：

```
function sleep(ms) {  
    return function(v) {  
        var p = Promise();  
  
        setTimeout(function() {  
            p.resolve(v);  
        });  
  
        return p;  
    };  
};  
  
function getImg(url) {  
    var p = Promise();  
    var img = new Image();  
  
    img.onload = function() {  
        p.resolve(this);  
    };  
  
    img.onerror = function(err) {  
        p.reject(err);  
    };  
  
    img.url = url;  
  
    return p;  
};  
```

由于Promise构造函数接受一个异步任务作为参数，所以getImg还可以这样调用：

```
function getImg(url) {  
    return Promise(function(resolve, reject) {  
        var img = new Image();  
  
        img.onload = function() {  
            resolve(this);  
        };  
  
        img.onerror = function(err) {  
            reject(err);  
        };  
  
        img.url = url;  
    });  
};  
```

接下来（见证奇迹的时刻），假设有一个BT的需求要这么实现：异步获取一个json配置，解析json数据拿到里边的图片，然后按顺序队列加载图片，每张图片加载时给个loading效果，

```
function addImg(img) {  
    $('#list').find('> li:last-child').html('').append(img);  
};  
  
function prepend() {  
    $('<li>')  
        .html('loading...')  
        .appendTo($('#list'));  
};  
  
function run() {  
    $('#done').hide();  
    getData('map.json')  
        .then(function(data) {  
            $('h4').html(data.name);  
  
            return data.list.reduce(function(promise, item) {  
                return promise  
                    .then(prepend)  
                    .then(sleep(1000))  
                    .then(function() {  
                        return getImg(item.url);  
                    })  
                    .then(addImg);  
            }, Promise.resolve());  
        })  
        .then(sleep(300))  
        .then(function() {  
            $('#done').show();  
        });  
};  
  
$('#run').on('click', run)
```

这里的sleep只是为了看效果加的，[可猛击查看demo！](http://www.dmyang.me/demos/promise/browser.html)

在这里，Promise.resolve(v)静态方法只是简单返回一个以v为肯定结果的promise，v可不传入，也可以是一个函数或者是一个包含then方法的对象或函数（即thenable）。

类似的静态方法还有Promise.cast(promise)，生成一个以promise为肯定结果的promise；Promise.reject(reason)，生成一个以reason为否定结果的promise。

我们实际的使用场景可能很复杂，往往需要多个异步的任务穿插执行，并行或者串行同在。这时候，可以对Promise进行各种扩展，比如实现Promise.all()，接受promises队列并等待他们完成再继续，再比如Promise.any()，promises队列中有任何一个处于完成态时即触发下一步操作。

> 参考引用资料
> 
> [JavaScript Promise迷你书（中文版）](http://liubin.org/promises-book/#how-to-write-promise)
> 
> [JavaScript进阶之路——认识和使用Promise，重构你的Js代码
](http://www.cnblogs.com/yunfeifei/p/4453690.html)
> 