## 1、删除数组里指定的某个元素

```
var arr = [3,2,3,3,5,3],
	val = 3;

var removeElement = function(arr, elm) {
	for (var i = 0; i < arr.length; i++) {
		if (arr[i] == elm) {
			arr.splice(i, 1);
			i--;
		}
	}
	return arr;
};

console.log(removeElement(arr, val)); // [2, 5]

```
**注意：这里for循环里的length是动态的，随着splice删除数组元素而变小，这里不能用forEach，因为forEach方法它一开始执行就自动缓存数组的长度，删除数组元素会出现意外。**

<!-- more -->

## 2、数组去重的方法


### 第一种，双重遍历去重法

1. 构建一个新的数组存放结果
2. for循环中每次从原数组中取出一个元素，用这个元素循环与结果数组对比
3. 若结果数组中没有该元素，则存到结果数组中

```
// 去除数组里的重复元素
function unique(arr) {
    var ret = [];
    var len = arr.length;
    var isRepeat;
    for(var i=0; i<len; i++) {
        isRepeat = false;
        for(var j=i+1; j<len; j++) {
            if(arr[i] === arr[j]){
                isRepeat = true;
                break;
            }
        }
        if(!isRepeat){
            ret.push(arr[i]);
        }
    }
    return ret;
}
var arr = [3, 3, 2, 3];
console.log(unique(arr)); // [2, 3]
```
### 第二种，对象键值对法

1. 创建一个新的数组存放结果
2. 创建一个空对象
3. for循环时，每次取出一个元素与对象进行对比，如果这个元素不重复，则把它存放到结果数组中，同时把这个元素的内容作为对象的一个属性，并赋值为1，存入到第2步建立的对象中。

**说明：至于如何对比，就是每次从原数组中取出一个元素，然后到对象中去访问这个属性，如果能访问到值，则说明重复。**

```
function unique(arr) {
	var res = [];
	var json = {};
	for (var i = 0; i < arr.length; i++) {
		if (!json[arr[i]]) {
			res.push(arr[i]);
			json[arr[i]] = 1;
		}
	}
	return res;
}

var arr = [3, 3, 2, "3", "a", "a"];
console.log(unique(arr)); // [3, 2, "a"]
```

### 第三种，ES5方法

利用 ES5 里的方法, indexOf、filter

arr.indexOf(要查找的元素)，返回被找到的元素在数组中的索引位置，如果没有则返回-1。

arr.filter(元素的值，元素的索引，被遍历的数组)，返回一个通过测试的数组。


```
function unique(arr) {
  var res = arr.filter(function(item, index, array) {
    return array.indexOf(item) === index;
  });
  return res;
}

var arr = [1, 1, '1', '2', 1];
var ans = unique(arr);
console.log(ans); // => [1, "1", "2"]
```

### 第四种，ES6方法

ES6 部署了 Set 以及 Array.from 方法，如果浏览器支持，完全可以这样：

ES2015还引入了一种叫作Set的数据类型，它不允许重复元素出现，如果你重复添加同一个元素的话，Set中只会存在一个。

```
function unique(arr) {
  return Array.from(new Set(arr));
}

var arr = [1, 2, "a", "1", 2 ,"a"];
console.log(unique(arr)) // [1, 2, "a", "1"]

```



----

> 关于数组去重更多详细方法参看
>
> [JavaScript 数组去重](https://github.com/hanzichi/underscore-analysis/issues/9)
>
> [数组去重 – JS Tips – A JS tip per day!](http://www.jstips.co/zh_cn/deduplicate-an-array/)
>
> [Remove Duplicates from JavaScript Array](http://stackoverflow.com/questions/9229645/remove-duplicates-from-javascript-array)
>
> [从 JavaScript 数组去重谈性能优化](https://github.com/lifesinger/blog/issues/113)
>
> [也谈 JavaScript 数组去重](https://www.toobug.net/article/array_unique_in_javascript.html)
>

## 3、取数组中最大值最小值

### 遍历比较方法

1. 设一个变量存放最大值，将数组中第一个值赋值给该变量
2. 遍历数组与最大值变量比较，如果大于最大值，则将该值赋值最大值变量
3. 遍历结束后，变量里储存的就是数组里的最大值

```
function arrayMax(arr) {
   var max = arr[0];
   arr.forEach(function(v){
   	  if(v > max) {
	     max = v
      }
   })
   return max;
}

var arr = [1, 3, 45, 24, 8, 20];
console.log(arrayMax(arr)); // 45
```

### 利用 Math.max() 与 Math.min() 方法

将数组转换成参数传进Math.max()或Math.min()。

```
function max(arr) {
  return Math.max.apply({},arr);
}

var arr = [1, 3, 45, 24, 8, 20];
console.log(max(arr)); // 45
```

### ES5方法

1. 利用数组实例的 reduce(function(prev,curv,index,arr))  方法
2. 依次比较回调函数中参数 prev 与 curv 的大小，返回大的那个

**reduce(function(上一次调用回调返回的值, 数组中正在处理的元素, 当前元素索引, 调用reduce的数组))**

```
function max(arr) {
  return arr.reduce(function(prev,curv){
      return prev > curv ? prev : curv
  })
}

var arr = [1, 3, 45, 24, 8, 20];
console.log(max(arr)); // 45

```

## 4、多维数组扁平化

将多维数组"拍平"

将下列数组转为单一数组

````
var myArray = [[1, 2],[3, 4, 5], [6, 7, 8, 9]];
````

### 1、双层遍历法

```
var myArray = [[1, 2],[3, 4, 5], [6, 7, 8, 9]];
var newArray = [];
for (var i = 0; i < myArray.length; ++i) {
  for (var j = 0; j < myArray[i].length; ++j)
    newArray.push(myArray[i][j]);
}
console.log(newArray);
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 2、使用concat()和apply()

```
var myArray = [[1, 2],[3, 4, 5], [6, 7, 8, 9]];
var newArray = [].concat.apply([], myArray);

console.log(newArray);
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 3、使用reduce()

```
var myArray = [[1, 2],[3, 4, 5], [6, 7, 8, 9]];
var newArray = myArray.reduce(function(prev, curr) {
  return prev.concat(curr);
});
console.log(newArray);
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 4、使用 ES6 的展开运算符

```
var myArray = [[1, 2],[3, 4, 5], [6, 7, 8, 9]];
var newArray = [].concat(...myArray);
console.log(newArray);
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

----

> 引用参考列表
>
> [MDN 的 JavaScript 标准库 Array 部分](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)
>
> [Js Tips](http://www.jstips.co/zh_CN/)
>
> [撸js基础之数组](http://www.ferecord.com/lujs-array.html#_13)
