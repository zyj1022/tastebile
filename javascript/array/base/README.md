# JS基础之数组


## 简介

数组是编程语言中应用最广泛的存储结构，在 ECMAScript 中数组是非常常用的引用类型。很有必要将数组的内容捋一遍，加深印象。

## 数组属性

- constructor 表示引用数组对象的构造函数
- length 表示数组的长度，即其中元素的个数。注意在js中length属性是可变的，当设置一个数组的length值变大时，数组内容不会改变，仅仅是length更改，但当length设置小于实际数组的时候，则原数组中索引大于或等于length的元素的值全部被丢失。
- prototype属性是object共有的，可以通过增加属性和方法扩展数组定义。


## 基本操作

### 一、创建方法

#### 创建一个空数组 

```
var arr = [];
var arr = new Array(); // 创建一个空数组
````

#### 创建一个指定长度的数组

```
var arr = new Array(size) // size 表示数组的长度
```
#### 创建一个指定元素的数组

```
var arr = new Array(1,2,3,4,5) //创建数组并赋值 [1,2,3,4,5]
```

### 二、检测方法



#### 1、利用 instanceof 操作符

instanceof操作符是检测对象的原型链是否指向构造函数的prototype对象,

```
var arr =[1,2,3];
console.log(arr instanceof Array) // true
```

#### 2、通过对象自身的 constructor 属性


```
var arr =[1,2,3];
console.log(arr.constructor === Array) // true
```

**跨frame实例化对象带来的问题**

`constructor` 和 `instanceof` 貌似很好的两个检测数组的方法，但实际上还是有些漏洞的，当你在多个frame中回来跳的时候，这两种方法就惨了。

由于每一个frame都有自己的一套执行环境，跨frame实例化的对象彼此并不共享原型链，通过 `instanceof` 操作符和 `constructor` 属性检测的方法自然会失败。

那么第三种方法就比较好了，如下

#### 3、对象原生toString检测

`Object.prototype.toString` 的行为：首先，取得对象的一个内部属性 [[Class]]，然后依据这个属性，返回一个类似于"[object Array]"的字符串作为结果(看过ECMA标准的应该都知道，[[]]用来表示语言内部用到的、外部不可直接访问的属性，称为“内部属性”)。利用这 个方法，再配合call，我们可以取得任何对象的内部属性[[Class]]，然后把类型检测转化为字符串比较，以达到我们的目的。 

```
var arr =[1,2,3];
console.log(Object.prototype.toString.call(arr) === '[object Array]'); //true

```

可以将判断方法封装一个函数

```
function isArray(obj) {
   return Object.prototype.toString.call(obj) === '[object Array]';
}
var arr =[1,2,3];
console.log(isArray(arr)); // true

```

#### 4、ECMAScript 5的isArray函数

为了让数组检测更方便，ECMAScript5新增了Array.isArray()方法。该方法的目的是最终确定某个值到底是不是数组，而不管它在哪个全局环境中创建的。

**注：此方法在IE8之前的版本是不支持的**

```
var arr =[1,2,3];
console.log(Array.isArray(arr)); // true

```


## ECM3方法

### 1、Array.prototype.join()

`join()` 方法将数组（或一个类数组对象）的所有元素连接到一个字符串中。此方法不会改变原数组。也就是说所有的数组元素被转换成字符串，再用一个分隔符将这些字符串连接起来。如果元素是undefined 或者null， 则会转化成空字符串。

`arr.join(separator)` 参数 `separator`

- 指定一个字符串来分隔数组的每个元素
- 如果省略()，数组元素用逗号分隔。默认为 ","
- 如果separator是空字符串("")，则所有元素之间都没有任何字符

```
var arr = ['a1', 'b2', 'c3'];
var myArr1 = arr.join();
var myArr2 = arr.join(', ');
var myArr3 = arr.join(' + ');
var myArr4 = arr.join('');

console.log(myArr1);  // a1,b2,c3
console.log(myArr2);  // a1, b2, c3
console.log(myArr3);  // a1 + b2 + c3
console.log(myArr4);  // a1b2c3
```

### 2、Array.prototype.push()

push() 方法将一个或多个元素添加到数组的末尾，并返回数组的新长度。

**添加元素：**可以添加新的元素到数组

```
// 添加元素
var letter = ["a", "b"];
var total = letter.push("c","d");
console.log(total); // 4
console.log(letter); // ["a", "b", "c", "d"]
```
**合并数组**：可以使用 apply() 添加第二个数组的所有元素

```
// 合并数组
var arr1 = [1, 2];
var arr2 = ["a", "b"];

// 将第二个数组融合进第一个数组
// 相当于 arr1.push('a', 'b');
Array.prototype.push.apply(arr1, arr2);

console.log(arr1); // [1, 2, "a", "b"]

```

### 3、Array.prototype.pop()

pop()方法从数组中删除最后一个元素，并返回该元素的值。此方法更改数组的长度。

如果你在一个空数组上调用 pop()，它返回  undefined。


### 4、Array.prototype.unshift()

unshift() 方法将一个或多个元素添加到数组的开头，并返回新数组的长度。

```
var arr = [1, 2];

arr.unshift(0);  
//arr is [0, 1, 2]

arr.unshift(-2, -1); // = 5
//arr is [-2, -1, 0, 1, 2]

arr.unshift( [-3] );
//arr is [[-3], -2, -1, 0, 1, 2]
```


### 5、Array.prototype.shift()

shift() 方法从数组中删除第一个元素，并返回该元素的值。此方法更改数组的长度。

```
let myFish = ['angel', 'clown', 'mandarin', 'surgeon'];

console.log('调用 shift 之前: ' + myFish);
// "调用 shift 之前: angel,clown,mandarin,surgeon"

var shifted = myFish.shift(); 

console.log('调用 shift 之后: ' + myFish); 
// "调用 shift 之后: clown,mandarin,surgeon" 

console.log('被删除的元素: ' + shifted); 
// "被删除的元素: angel"
```

### 6、Array.prototype.sort()

sort(compareFunction) 方法在适当的位置对数组的元素进行排序，并返回数组。

sort 排序不一定是稳定的。默认排序顺序是根据字符串Unicode码点。

一般我们给sort带入个比较函数来替代原来的默认的比较方法，比较方法接受两个参数:

- 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；
- 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。
- 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。
- compareFunction(a, b) 必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的。

```
var numbers = [4, 2, 5, 1, 3];
numbers.sort(function(a, b) {
  return a - b;
});
console.log(numbers); // [1, 2, 3, 4, 5]
```

### 7、Array.prototype.reverse()

reverse() 方法颠倒数组中元素的位置。第一个元素会成为最后一个，最后一个会成为第一个。

下例将会创建一个数组 myArray，其包含三个元素，然后颠倒该数组。

```
var myArray = ['a', 'b', 'c'];
myArray.reverse(); 
console.log(myArray);  // ['c', 'b', 'a']
```

### 8、Array.prototype.concat()

concat() 方法用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组。

```
var arr1 = ["a", "b", "c"];
var arr2 = ["d", "e", "f"];

var arr3 = arr1.concat(arr2);

console.log(arr3);
// 返回结果是一个新数组
// [ "a", "b", "c", "d", "e", "f" ]

// 原数组没有改变
console.log(arr1); // ["a", "b", "c"]
console.log(arr2); // ["d", "e", "f"]
```

### 9、Array.prototype.slice()

`slice(start, end)` 方法将数组的一部分浅拷贝, 返回到从开始到结束（不包括结束）选择的新数组对象。原始数组不会被修改。

- slice()
- slice(start)
- slice(start,end)

```
var arr = ['one','two','three','four'];

//如果不传参数，表示从数组0开始到到end（包含end）
var newArr1 = arr.slice();
console.log(newArr1)  // ["one", "two", "three", "four"]

//如果省略 end，则表示从start开始到end（包含end）
var newArr2 = arr.slice(1);
console.log(newArr2) // ["two", "three", "four"]

//如果传人star、end，则表示从start到end不包含end
var newArr3 = arr.slice(1, 3);
console.log(newArr3) // ["two", "three"]

console.log(arr)    // ["one", "two", "three", "four"]

```

### 10、Array.prototype.splice()

splice() 方法通过删除现有元素和/或添加新元素来更改数组的内容。

**注意：splice 方法直接更改原数组内容**

- array.splice(start)
- array.splice(start, deleteCount) 
- array.splice(start, deleteCount, item1, item2, ...)

```
var myFish = ["angel", "clown", "mandarin", "surgeon"];

//从第 2 位开始删除 0 个元素，插入 "drum"
var removed = myFish.splice(2, 0, "drum");
console.log(myFish);
//运算后的 myFish:["angel", "clown", "drum", "mandarin", "surgeon"]
//被删除元素数组：[]，没有元素被删除

//从第 3 位开始删除 1 个元素
removed = myFish.splice(3, 1);
//运算后的myFish：["angel", "clown", "drum", "surgeon"]
//被删除元素数组：["mandarin"]

//从第 2 位开始删除 1 个元素，然后插入 "trumpet"
removed = myFish.splice(2, 1, "trumpet");
//运算后的myFish: ["angel", "clown", "trumpet", "surgeon"]
//被删除元素数组：["drum"]

//从第 0 位开始删除 2 个元素，然后插入 "parrot", "anemone" 和 "blue"
removed = myFish.splice(0, 2, "parrot", "anemone", "blue");
//运算后的myFish：["parrot", "anemone", "blue", "trumpet", "surgeon"]
//被删除元素的数组：["angel", "clown"]

//从第 3 位开始删除 2 个元素
removed = myFish.splice(3, Number.MAX_VALUE);
//运算后的myFish: ["parrot", "anemone", "blue"]
//被删除元素的数组：["trumpet", "surgeon"]
```

--------

## ECM5方法

### 1、Array.prototype.indexOf()

indexOf()方法返回在数组中可以找到给定元素的第一个索引，如果不存在，则返回-1。

```
var a = [2, 6, 9, 6]; 

a.indexOf(2); // 0 
a.indexOf(7); // -1 不存在
a.indexOf(6); // 1 返回指定元素的第一个索引值

```

**找出指定元素出现的所有位置**

```
// 找出 a 在 array 里的所有位置
var str = 'a';
var array = ['a', 'b', 'a', 'c', 'a', 'd'];


var indices = [];
var idx = array.indexOf(str);

while (idx != -1) {
  indices.push(idx);
  idx = array.indexOf(str, idx + 1);
}

console.log(indices);
// [0, 2, 4]
```


### 2、Array.prototype.lastIndexOf()

lastIndexOf() 方法返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 fromIndex 处开始。

```
arr.lastIndexOf(searchElement[, fromIndex = arr.length - 1])
```
**定位数组中的值：**

```
var array = [2, 5, 9, 2];
var index = array.lastIndexOf(2);
// index is 3
index = array.lastIndexOf(7);
// index is -1
index = array.lastIndexOf(2, 3);
// index is 3
index = array.lastIndexOf(2, 2);
// index is 0
index = array.lastIndexOf(2, -2);
// index is 0
index = array.lastIndexOf(2, -1);
// index is 3
```

**查找所有元素**

下例使用 lastIndexOf 查找到一个元素在数组中所有的索引（下标），并使用 push 将所有添加到另一个数组中。

```

var element = 'a';
var array = ['a', 'b', 'a', 'c', 'a', 'd'];

var indices = [];
var idx = array.lastIndexOf(element);

while (idx != -1) {
  indices.push(idx);
  idx = (idx > 0 ? array.lastIndexOf(element, idx - 1) : -1);
}

console.log(indices);
// [4, 2, 0];
```


### 3、Array.prototype.every()

every() 方法测试数组的所有元素是否都通过了指定函数的测试。

every 方法为数组中的每个元素执行一次 callback 函数，直到它找到一个使 callback 返回 false（表示可转换为布尔值 false 的值）的元素。如果发现了一个这样的元素，every 方法将会立即返回 false。否则，callback 为每一个元素返回 true，every 就会返回 true。callback 只会为那些已经被赋值的索引调用。不会为那些被删除或从来没被赋值的索引调用。

callback 被调用时传入三个参数：元素值，元素的索引，原数组。

如果为 every 提供一个 thisArg 参数，在该参数为调用 callback 时的 this 值。如果省略该参数，则 callback 被调用时的 this 值，在非严格模式下为全局对象，在严格模式下传入 undefined。

every 不会改变原数组。

every 遍历的元素范围在第一次调用 callback 之前就已确定了。在调用 every 之后添加到数组中的元素不会被 callback 访问到。如果数组中存在的元素被更改，则他们传入 callback 的值是 every 访问到他们那一刻的值。那些被删除的元素或从来未被赋值的元素将不会被访问到。


**检测所有数组元素的大小**

```
function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [12, 5, 8, 130, 44].every(isBigEnough);
// passed is false
passed = [12, 54, 18, 130, 44].every(isBigEnough);
// passed is true
```


### 4、Array.prototype.some()

some() 方法测试数组中的某些元素是否通过了指定函数的测试。

some 为数组中的每一个元素执行一次 callback 函数，直到找到一个使得 callback 返回一个“真值”（即可转换为布尔值 true 的值）。如果找到了这样一个值，some 将会立即返回 true。否则，some 返回 false。callback 只会在那些”有值“的索引上被调用，不会在那些被删除或从来未被赋值的索引上调用。

callback 被调用时传入三个参数：元素的值，元素的索引，被遍历的数组。

```
arr.some(callback[, thisArg])
```

如果为 some 提供了一个 thisArg 参数，将会把它传给被调用的 callback，作为 this 值。否则，在非严格模式下将会是全局对象，严格模式下是 undefined。

some 被调用时不会改变数组。


**测试数组元素的值**

下面的例子检测在数组中是否有元素大于 10。

```
function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [2, 5, 8, 1, 4].some(isBigEnough);
// passed is false
passed = [12, 5, 8, 1, 4].some(isBigEnough);
// passed is true
```

### 5、Array.prototype.filter()

filter() 方法使用指定的函数测试所有元素，并创建一个包含所有通过测试的元素的新数组。

对数组中的每一项运行给定函数，返回该函数会返回 true 的项组成的数组。

```
var new_array = arr.filter(callback[, thisArg])
```

**筛选排除掉所有的小值**

下例使用 filter 创建了一个新数组，该数组的元素由原数组中值大于 10 的元素组成。

```
function isBigEnough(value) {
  return value >= 10;
}

var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// filtered is [12, 130, 44]
```


### 6、Array.prototype.map()

map() 方法创建一个新数组，其结果是该数组中的每个元素调用一个提供的函数，**返回这个新数组**。

**求数组中每个元素的平方根**

下面的代码创建了一个新数组，值为原数组中对应数字的平方根。

```
var numbers = [1, 4, 9];
var roots = numbers.map(Math.sqrt);
/* roots的值为[1, 2, 3], numbers的值仍为[1, 4, 9] */
```

**问答题**

问题：`["1","2","3"].map(parseInt)` 答案是多少？

答案是`[1,NaN,NaN]`

### 7、Array.prototype.forEach()

forEach() 方法对数组的每个元素执行一次提供的函数。

对数组中的每一项运行给定函数，这个方法没有返回值。本质上与使用 for 循环迭代数组一样。

```
array.forEach(callback(currentValue, index, array){
    //do something
}, this)
```
- currentValue(当前值) 数组中正在处理的当前元素。
- index(索引) 数组中正在处理的当前元素的索引。
- array forEach()方法正在操作的数组。

```
var arr=[2, 4, 5];
arr.forEach(function(elm,index, array) {
   console.log(index, elm);
});

// 0 2
// 1 4
// 2 5

```

### 8、Array.prototype.reduce()  

reduce() 方法对累加器和数组的每个值 (从左到右)应用一个函数，以将其减少为单个值。


**将数组所有项相加**

```
var sum = [0, 1, 2, 3].reduce(function(a, b) {
    return a + b;
}, 0);
// sum is 6
```

**数组扁平化**

```
var flattened = [[0, 1], [2, 3], [4, 5]].reduce(function(a, b) {
    return a.concat(b);
}, []);
// flattened is [0, 1, 2, 3, 4, 5]
```

### 9、Array.prototype.reduceRight()

reduceRight() 方法接受一个函数作为累加器（accumulator），让每个值（从右到左，亦即从尾到头）缩减为一个值。（与 reduce() 的执行方向相反）

```
var flattened = [[0, 1], [2, 3], [4, 5]].reduceRight(function(a, b) {
    return a.concat(b);
}, []);

// flattened is [4, 5, 2, 3, 0, 1]
```


