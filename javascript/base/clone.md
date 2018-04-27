# 关于JS中的深度复制和浅复制

js中的数据类型分为两大类：原始类型和对象类型。

- 原始类型包括：数值、字符串、布尔值、null、undefined
- 对象类型包括：对象即是属性的集合，当然这里又两个特殊的对象----函数（js中的一等对象）、数组（键值的有序集合）。

好了既然对象分为这两类，这两种类型在复制克隆的时候是有很大区别的。原始类型存储的是对象的实际数据，而对象类型存储的是对象的引用地址（对象的实际内容单独存放，为了减少数据开销通常存放在内存中）。

ps：说到这里，大家要知道，对象的原型也是引用对象，它把原型的方法和属性放在内存当中，通过原型链的方式来指向这个内存地址。

## 什么是深度复制、浅复制？

**浅度复制：原始类型为值传递，对象类型仍为引用传递。**

**深度复制：所有元素或属性均完全复制，与原对象完全脱离，也就是说所有对于新对象的修改都不会反映到原对象中。**

### 浅复制的表现

```
// 数值复制的表现
var a=1;
var b=a;
b = 2
console.log(a);// "1"
console.log(b);// "2"

//字符串克隆的表现
var c="1";
var d=c;
d="2";
console.log(c);// "1"
console.log(d);// "2"

//字符串克隆的表现
var x=true;
var y=x;
y=false;
console.log(x); // true
console.log(y); // false
```

从上面的代码大家可以看出，原始类型即使我们采用普通的复制方式仍能得到正确的结果，原因就是原始类型存储的是对象的实际数据。

### 对象类型的复制

```
var Chinese = {
　　nation:'中国'
}
var Doctor = {
　　career:'医生'
}　　
// 把父对像的属性，全部拷贝给子对象
function extendCopy(p) {
　　　　var c = {};
　　　　for (var i in p) {
　　　　　　c[i] = p[i];
　　　　}
　　　　c.uber = p;
　　　　return c;
}

```
使用的时候，这样写：

```
var Doctor = extendCopy(Chinese);
Doctor.career = '医生';
alert(Doctor.nation); // 中国
```

但是，这样的拷贝有一个问题。那就是，如果父对象的属性等于数组或另一个对象，那么实际上，子对象获得的只是一个内存地址，而不是真正拷贝，因此存在父对象被篡改的可能。

请看，现在给Chinese添加一个"出生地"属性，它的值是一个数组。

```
Chinese.birthPlaces = ['北京','上海','香港'];
// 通过extendCopy()函数，Doctor继承了Chinese。
var Doctor = extendCopy(Chinese);
// 然后，我们为Doctor的"出生地"添加一个城市：
Doctor.birthPlaces.push('厦门');
// 发现 Chinese 的"出生地"也被改掉了！
alert(Doctor.birthPlaces); //北京, 上海, 香港, 厦门
alert(Chinese.birthPlaces); //北京, 上海, 香港, 厦门
```
所以，extendCopy()只是拷贝基本类型的数据，我们把这种拷贝叫做"浅拷贝"。这是早期jQuery实现继承的方式。

## 深克复制的实现

### 最简单的方法

```
var newObject=JSON.parse(JSON.stringify(oldObject))
```

上面这种方法好处是非常简单易用，但是坏处也显而易见，这会抛弃对象的constructor，也就是深复制之后，无论这个对象原本的构造函数是什么，在深复制之后都会变成 Object。


### 方法一

```
function clone(Obj) {
    var buf;
    if (Obj instanceof Array) {
        buf = [];  // 创建一个空的数组
        var i = Obj.length;
        while (i--) {
            buf[i] = clone(Obj[i]); // 递归调用
        }
        return buf;
    } else if (Obj instanceof Object){
        buf = {};  // 创建一个空对象
        for (var k in Obj) {  // 为这个对象添加新的属性
            buf[k] = clone(Obj[k]);
        }
        return buf;
    }else{
        return Obj;
    }
}
```

以上方法比较粗糙，因为数组其实也是对象，那么就会有下面方法

### 方法二

```
function deep_clone(obj) {
    var newObj = obj.constructor === Array ? [] : {};
    for (var key in obj) {
        newObj[key] = (typeof(obj[key]) === 'object') ? deep_clone(obj[key]) : obj[key];
    }
    return newObj;
}
```

### 方法三

> 所谓"深拷贝"，就是能够实现真正意义上的数组和对象的拷贝。它的实现并不难，只要递归调用"浅拷贝"就行了。

```
function deepCopy(p, c) {
　　　　var c = c || {};
　　　　for (var i in p) {
　　　　　　if (typeof p[i] === 'object') {
　　　　　　　　c[i] = (p[i].constructor === Array) ? [] : {};
　　　　　　　　 deepCopy(p[i], c[i]);
　　　　　　} else {
　　　　　　　　　c[i] = p[i];
　　　　　　}
　　　　}
　　　　return c;
　　}
```

使用的时候这样写：

```
var newObj = deepCopy(oldObj);
```

> 参考文档
>
> [深入剖析 JavaScript 的深复制](http://jerryzou.com/posts/dive-into-deep-clone-in-javascript/)
>
> [avascript面向对象编程（三）：非构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance_continued.html)
