# Javascript 面向对象编程详解


Javascript是一种基于对象的语言，秉承一切皆对象的理念。

但是，它又不是一种真正的面向对象编程（OOP）语言，因为它的语法中没有class（类）。我们知道面向对象编程的三大特征就是封装、继承、多态

那么如何用 Javascript 来实现面向对象编程呢


# 1、利用Prototype模式实现封装

在Javascript里每一个构造函数都有一个`prototype`属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。

这样，我们把那些不变的属性和方法，直接定义在`prototype`对象上

```
function Person(name,age) {
    this.name = name;
    this.age = age;
}

Person.prototype.legs = 2
Person.prototype.speak = function() {
	console.log("我的名字是：" + this.name + "，年龄：" + this.age);
}

```
然后，生成实例

```
var jack = new Person("jack", 26);
var lily = new Person("lily", 25);

jack.legs; // 2
lily.speak() // 我的名字是：lily，年龄：25

```

<!-- more -->


# 2、实现构造函数继承的五种方法

举例，有一个水果的构造函数。

```
function Fruit() {
  this.type = "水果";
}
```

还有个“苹果”的构造函数

```
function Apple(name, color) {
	this.name = name;
	this.color = color;
}
```
怎么使得 `Apple` 继承 `Fruit` 呢？

## 第一种，借用构造函数（经典继承）

**使用call/apply，构造函数绑定**，如题，使用`call`或`apply`方法，将父对象的构造函数绑定在子对象上，即在子对象构造函数中加一行

> 基本思想:即在子类型构造函数的内部调用超类型构造函数.

```
function Fruit() {
  this.type = "水果";
}

function Apple(name,color) {
    // 继承了Fruit，且向父类传递参数
	Fruit.apply(this, arguments); // 这里用call也可以
	this.name = name;
	this.color = color;
}

var a = new Apple("苹果", "红色");
console.log(a.type) // 水果
a.type = "南瓜" // 这里修改父类的属性值
console.log(a.type) // 南瓜

var b = new Apple();
console.log(b.type); // 水果 可见引用类型值是独立的

```

在这里，借用构造函数解决了原型链的两个问题：

- 保证了原型链中引用类型值的独立,不再被所有实例共享;
- 子类型创建时也能够向父类型传递参数.

随之而来的是, 如果仅仅借用构造函数,那么将无法避免构造函数模式存在的问题--方法都在构造函数中定义, 因此函数复用也就不可用了.而且超类型(如Fruit)中定义的方法,对子类型而言也是不可见的. 考虑此,借用构造函数的技术也很少单独使用.

## 第二种，原型prototype继承


### 1、使用prototype属性继承

第二种方法比较常见，使用prototype属性。

如果水果的prototype对象，指向一个Fruit的实例，那么所有"水果"的实例，就能继承Fruit了。

```
function Fruit() {
  this.type = "水果";
}

function Apple(name, color) {
	this.name = name;
	this.color = color;
}

/*
* 将 Apple 的 prototype 对象指向 Fruit实例，
* 它相当于完全删除了prototype 对象原先的值，然后赋予一个新值
*／
Apple.prototype = new Fruit();

// 将 Apple 的 prototype 对象的构造函数指回原来的构造函数
Apple.prototype.constructor = Apple;

var a2 = new Apple("香蕉苹果", "黄色");
console.log(a2.type) // 水果

```

### 2、直接继承prototype

这里第2种方法是对第1种方法的改进。由于Fruit对象中，不变的属性都可以直接写入Fruit.prototype。
所以，我们也可以让Apple()跳过 Fruit()，直接继承Fruit.prototype。

```
function Fruit() {}
Fruit.prototype.type = "水果";

function Apple(name, color) {
	this.name = name;
    this.color = color;
}

Apple.prototype = Fruit.prototype;
Apple.prototype.constructor = Apple; // 实际上把Fruit.prototype对象的constructor属性也改掉了
var a2 = new Apple("香蕉苹果", "黄色");
console.log(a2.type) // 水果

```
与前一种方法相比，这样做的优点是效率比较高（不用执行和建立Fruit的实例了），比较省内存。缺点是 `Apple.prototype`和`Fruit.prototype`现在指向了同一个对象，那么任何对`Apple.prototype`的修改，都会反映到`Fruit.prototype`。

所以，上面这一段代码其实是有问题的。请看这行

```
Apple.prototype.constructor = Apple;
```

这一句实际上把 `Fruit.prototype` 对象的 `constructor` 属性也改掉了！

```
alert(Fruit.prototype.constructor); // Apple
```

## 第三种 组合继承

组合继承, 有时候也叫做伪经典继承,指的是将原型链和借用构造函数的技术组合到一块,从而发挥两者之长的一种继承模式.

> 基本思路: 使用原型链实现对原型属性和方法的继承,通过借用构造函数来实现对实例属性的继承.

这样,既通过在原型上定义方法实现了函数复用,又能保证每个实例都有它自己的属性. 如下.


```
function Fruit() {
  this.type = '水果';
}
Fruit.prototype.getType = function() {
    console.log(this.type);
}

function Apple(name,color) {
	Fruit.apply(this, arguments); // 继承实例属性，第一次调用Fruit，
    this.name = name;
	this.color = color;
}

Apple.prototype = new Fruit(name); // 继承父类方法，第二次调用Fruit
Apple.prototype.getColor = function() {
    console.log(this.color)
}

var ap1 = new Apple("苹果", "红色");
ap1.type = '红富士'
ap1.getType(); // '红富士'
ap1.getColor(); // '红色'

var ap2 = new Apple('香蕉苹果',"黄色")
console.log(ap2.type); // 水果
ap2.getType(); // '水果'
ap2.getColor(); // '红色'

```

组合继承避免了原型链和借用构造函数的缺陷,融合了它们的优点,成为 JavaScript 中**最常用的继承模式**. 而且, `instanceof` 和 `isPrototypeOf()` 也能用于识别基于组合继承创建的对象.

同时我们还注意到组合继承其实调用了两次父类构造函数, 造成了不必要的消耗, 那么怎样才能避免这种不必要的消耗呢?

## 第四种，利用空对象作为中介(寄生组合式继承)

由于前面的 "直接继承prototype" 存在上述的缺点，所以就有第四种方法，利用一个空对象作为中介

```
function Fruit() {}
Fruit.prototype.type = "水果";

function Apple(name, color) {
	this.name = name;
    this.color = color;
}

var F = function(){}; // F是空对象，所以几乎不占内存
F.prototype = Fruit.prototype;
Apple.prototype = new F(); // 修改Apple的prototype对象，就不会影响到Fruit的prototype对象
Apple.prototype.constructor = Apple;

var a2 = new Apple("香蕉苹果", "黄色");
console.log(a2.type) // 水果

```

将上面方法，封装一个函数

```
function extend(Child, Parent) {
　　var F = function(){};
　　F.prototype = Parent.prototype;
　　Child.prototype = new F();
　　Child.prototype.constructor = Child;
　　Child.uber = Parent.prototype;
}
```

使用的时候，方法如下:


```
extend(Apple,Fruit)
var a2 = new Apple("香蕉苹果", "黄色");
console.log(a2.type) // 水果
```

这个extend函数，就是YUI库如何实现继承的方法。
另外，说明一点，函数体最后一行

```
Child.uber = Parent.prototype;
```
意思是为子对象设一个uber属性，这个属性直接指向父对象的prototype属性。（uber是一个德语词，意思是"向上"、"上一层"。）这等于在子对象上打开一条通道，可以直接调用父对象的方法。这一行放在这里，只是为了实现继承的完备性，纯属备用性质。

## 第五种， 拷贝继承

上面是采用prototype对象，实现继承。我们也可以换一种思路，纯粹采用"拷贝"方法实现继承。简单说，如果把父对象的所有属性和方法，拷贝进子对象，不也能够实现继承吗？这样我们就有了第五种方法。

首先，还是把Fruit的所有不变属性，都放到它的prototype对象上。

```
function Fruit(){}
Fruit.prototype.type = "水果";
```

然后，写一个函数，实现属性拷贝的目的

```
function extend2(Child, Parent) {
　　var p = Parent.prototype;
　　var c = Child.prototype;
　  for (var i in p) {
　　　　	c[i] = p[i];
　　}
　　c.uber = p;
}
```

这个函数的作用，就是将父对象的 prototype 对象中的属性，一一拷贝给 Child 对象的 prototype 对象。

使用的时候，这样写：

```
extend2(Apple, Fruit);
var a2 = new Apple("香蕉苹果", "黄色");
console.log(a2.type) // 水果
```


# 3、不使用构造函数实现"继承"的方法

**什么是"非构造函数"的继承？**

比如，现在有一个对象，叫做"中国人"。

```
var Chinese = {
　　nation:'中国'
};
```

还有一个对象，叫做"医生"。

```
var Doctor ={
　　career:'医生'
}
```

请问怎样才能让"医生"去继承"中国人"，也就是说，我怎样才能生成一个"中国医生"的对象？

这里要注意，这两个对象都是普通对象，不是构造函数，无法使用构造函数方法实现"继承"。

### 第一种 object()方法

json格式的发明人Douglas Crockford，提出了一个object()函数，可以做到这一点。

```
function object(o) {
　　function F() {}
　　F.prototype = o;
　　return new F();
}
```

这个object()函数，其实只做一件事，就是把子对象的prototype属性，指向父对象，从而使得子对象与父对象连在一起。

使用的时候，第一步先在父对象的基础上，生成子对象：

```
var Doctor = object(Chinese);
```

然后，再加上子对象本身的属性：

```
Doctor.career = '医生';
```

这时，子对象已经继承了父对象的属性了。

```
var Chinese = {
　　　nation:'中国'
};

var Doctor ={
　　　career:'医生'
}

function object(o) {
　　function F() {}
　　F.prototype = o;
　　return new F();
}

var Doctor = object(Chinese);

Doctor.career = '医生';

alert(Doctor.nation); //中国

```

在 ECMAScript5 中,通过新增 object.create() 方法规范化了上面的原型式继承.

object.create() 接收两个参数:

- 一个用作新对象原型的对象
- (可选的)一个为新对象定义额外属性的对象

```
var Chinese = {
　　　nation:'中国'
};

var Doctor ={
　　　career:'医生'
}

var Doctor = Object.create(Chinese);

Doctor.career = '医生';

alert(Doctor.nation); //中国
```
`object.create()` 只有一个参数时功能与上述`object`方法相同，目前支持 `Object.create()` 的浏览器有 IE9+, Firefox 4+, Safari 5+, Opera 12+ 和 Chrome.


### 第二种 浅拷贝

除了使用"prototype链"以外，还有另一种思路：把父对象的属性，全部拷贝给子对象，也能实现继承。

下面这个函数，就是在做拷贝：

```
function extendCopy(p) {
　　var c = {};
　　for (var i in p) {
　　　　c[i] = p[i];
　　}
　　c.uber = p;
　　return c;
}
```

但是，这样的拷贝有一个问题。那就是，如果父对象的属性等于数组或另一个对象，那么实际上，子对象获得的只是一个内存地址，而不是真正拷贝，因此存在父对象被篡改的可能。

请看，现在给Chinese添加一个"出生地"属性，它的值是一个数组。

```
Chinese.birthPlaces = ['北京','上海','香港'];
```
通过extendCopy()函数，Doctor继承了Chinese。

```
var Doctor = extendCopy(Chinese);
```

然后，我们为Doctor的"出生地"添加一个城市

```
Doctor.birthPlaces.push('厦门');
```
发生了什么事？Chinese的"出生地"也被改掉了！

```
alert(Doctor.birthPlaces); //北京, 上海, 香港, 厦门
alert(Chinese.birthPlaces); //北京, 上海, 香港, 厦门
```
所以，extendCopy()只是拷贝基本类型的数据，我们把这种拷贝叫做"浅拷贝"。这是早期jQuery实现继承的方式。

### 第三种 深拷贝

所谓"深拷贝"，就是能够实现真正意义上的数组和对象的拷贝。它的实现并不难，只要递归调用"浅拷贝"就行了。

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
var Doctor = deepCopy(Chinese);
```
现在，给父对象加一个属性，值为数组。然后，在子对象上修改这个属性：

```
Chinese.birthPlaces = ['北京','上海','香港'];
Doctor.birthPlaces.push('厦门');
```
这时，父对象就不会受到影响了。

```
alert(Doctor.birthPlaces); //北京, 上海, 香港, 厦门
alert(Chinese.birthPlaces); //北京, 上海, 香港
```

目前，jQuery库使用的就是这种继承方法。


-----

> 以上文章内容，转载修改自[阮一峰的网络日志](http://www.ruanyifeng.com/blog/javascript/)，仅作学习总结，原文链接如下：
>
> [Javascript面向对象编程（三）：非构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance_continued.html)
>
> [Javascript面向对象编程（二）：构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)
>
> [Javascript 面向对象编程（一）：封装](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html)


本文地址: http://zyj1022.github.io/posts/frontend/2017/js-oop.html

转载时必须以链接形式注明原始出处及本声明
