# js数组相关题目

### 删除数组里指定的某个元素

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