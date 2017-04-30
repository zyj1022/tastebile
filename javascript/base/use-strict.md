# 严格模式 "use strict"

## 作用

消除JS语法的一些不合理、不严谨、不安全的问题，减少怪异行为并保证代码运行安全
提高编译器解释器效率，增加运行速度

## 与标准模式的区别

- 隐式声明或定义变量：严格模式不能通过省略 var 关键字隐式声明变量。会报引用错误“ReferenceError：abc is not define”
- 对象重名的属性：严格模式下对象不允许有重名的属性。var obj = {a:1, b:2, a:3}会报语法错误“SyntaxError”。
- arguments.callee：通常我们使用这个语法来实现匿名函数的递归，但在严格模式下是不允许的。会报错TypeError。
- with语句：严格模式下with语句是被禁用的。会报语法错误 SyntaxError 。

2.注释/* */不可嵌套