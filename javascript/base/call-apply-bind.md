# call()、apply()、bind()的用法

在javascript中，函数不仅是一种语法，也是值，也就是说，可以将函数赋值给变量，存储在对象的属性或数组的元素中， 作为参数传入另外一个函数等。

## bind、call、apply

每一个函数都包含一个 prototype属性，这个属性是指向一个对象的引用，这个对象称作“原型对象”。 每一个函数都包含不同的原型对象。当将函数用作构造函数的时候，新创建的对象会从原型对象上继承属性。

## Function.prototype.call()

call() 方法在使用一个指定的this值和若干个指定的参数值的前提下调用某个函数或方法.


>注意：该方法的作用和 apply() 方法类似，只有一个区别，就是call()方法接受的是若干个参数的列表，而apply()方法接受的是一个包含多个参数的数组。

```
fun.call(thisArg[, arg1[, arg2[, ...]]])

fun.apply(thisArg[, argsArray])
```

apply 与 call() 非常相似，不同之处在于提供参数的方式。

apply 使用参数数组而不是一组参数列表，apply 可以使用数组字面量（array literal），

如：fun.apply(this, ['eat', 'bananas'])，或数组对象，

如：fun.apply(this, new Array('eat', 'bananas'))。




### 使用call方法调用函数并且指定上下文的'this'

```
function greet() {
  var arr = [this.person, 'is age', this.age].join(' ');
  console.log(arr);
}

var i = {
  person: "wang",
  age: 20
}

greet.call(i); // 这里的 this 会绑定到 i 对象上。

```

###  使用call方法调用匿名函数

在下例中的for循环体内，我们创建了一个匿名函数，然后通过调用该函数的call方法，将每个数组元素作为指定的this值执行了那个匿名函数。这个匿名函数的主要目的是给每个数组元素对象添加一个print方法，这个print方法可以打印出各元素在数组中的正确索引号。当然，这里不是必须得让数组元素作为this值传入那个匿名函数（普通参数就可以），目的是为了演示call的用法。

```
var animals = [
  {species: 'Lion', name: 'King'},
  {species: 'Whale', name: 'Fail'}
];

for (var i = 0; i < animals.length; i++) {
  (function (i) {
    this.print = function () {
      console.log('#' + i  + ' ' + this.species + ': ' + this.name);
    }
    this.print();
  }).call(animals[i], i);
}
```
### 使用apply和内置函数

聪明的apply用法允许你在某些本来需要写成遍历数组变量的任务中使用内建的函数。在接下里的例子中我们会使用Math.max/Math.min来找出一个数组中的最大/最小值。

```
var numbers = [5, 6, 2, 3, 7];

var max = Math.max.apply(null, numbers); /* This about equal to Math.max(numbers[0], ...) or Math.max(5, 6, ..) */
var min = Math.min.apply(null, numbers);

max = -Infinity, min = +Infinity;

for (var i = 0; i < numbers.length; i++) {
  if (numbers[i] > max)
    max = numbers[i];
  if (numbers[i] < min)
    min = numbers[i];
}
```


## Function.prototype.bind()

`bind()` 方法会创建一个新函数。当这个新函数被调用时，bind()的第一个参数将作为它运行时的 this, 之后的一序列参数将会在传递的实参前传入作为它的参数。

`bind()`是在ES5中新增的方法，从名字可以看出，这个方法的主要作用就是将函数绑定到某个对象。

### 创建绑定函数

bind() 最简单的用法是创建一个函数，使这个函数不论怎么调用都有同样的 this 值。

经常犯的一个错误是将一个方法从对象中拿出来，然后再调用，希望方法中的 this 是原来的对象。（比如在回调中传入这个方法。）如果不做特殊处理的话，一般会丢失原来的对象。从原来的函数和原来的对象创建一个绑定函数，则能很漂亮地解决这个问题：

```
var x = 9;
var obj = {
	x: 81,
    getX: function() {
		return this.x;
    }
}
obj.getX(); // 返回 81

var b = obj.getX;
b() // 返回 9, 在这种情况下，"this"指向全局作用域

// 创建一个新函数，将"this"绑定到obj对象
// 新手可能会被全局的x变量和obj里的属性x所迷惑
var bindX = b.bind(obj);
bindX(); // 返回 81
```

当在函数f()上调用bind()方法并后传入一个对象o作为参数，这个方法将返回一个新函数： （以函数调用的方式）调用新的函数将会把原始的函数f()作为o的方法来调用。例如：

```
function f(y) {
  return this.x + y;
}

var o = {
  x: 1
};

var g = f.bind(o);  // 通过调用 g(x) 来调用 o.f(x)
g(2); // 3
```

-----

> 参考引用资料
>
> [MDN JavaScript 参考文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
>
