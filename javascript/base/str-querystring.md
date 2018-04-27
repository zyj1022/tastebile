# JS获取URL中参数值（QueryString）的方法

今天碰到要在一个页面获取另外一个页面url传过来的参数，一开始很本能的想到了用 split("?")这样一步步的分解出需要的参数。
后来想了一下，肯定会有更加简单的方法的！所以在网上找到了几个很又简单实用的方法。

## 方法一：正则法

```
function getQueryString(name) {
    var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
    var r = window.location.search.substr(1).match(reg);
    if (r != null) {
        return unescape(r[2]);
    }
    return null;
}
// 这样调用：
alert(GetQueryString("参数名1"));
alert(GetQueryString("参数名2"));
alert(GetQueryString("参数名3"));
```
通过JS获取url参数，这个经常用到。比如说一个url：http://wwww.baidu.net/?q=js,我们想得到参数q的值，那可以通过以下函数调用即可。

```
function GetQueryString(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
    var r = window.location.search.substr(1).match(reg);  // 获取url中"?"符后的字符串并正则匹配
    var context = "";
    if (r != null)
         context = r[2];
    reg = null;
    r = null;
    return context == null || context == "" || context == "undefined" ? "" : context;
}
alert(GetQueryString("q"));
```


## 方法二：split拆分法

```
function GetRequest() {
    var url = location.search; //获取url中"?"符后的字串
    var theRequest = new Object();
    if (url.indexOf("?") != -1) {
        var str = url.substr(1);
        strs = str.split("&");
        for(var i = 0; i < strs.length; i ++) {
            theRequest[strs[i].split("=")[0]] = unescape(strs[i].split("=")[1]);
        }
    }
    return theRequest;
}
var Request = new Object();
Request = GetRequest();
// var 参数1,参数2,参数3,参数N;
// 参数1 = Request['参数1'];
// 参数2 = Request['参数2'];
// 参数3 = Request['参数3'];
// 参数N = Request['参数N'];

```

## 方法三：单个参数的获取方法

```
function GetRequest() {
   var url = location.search; // 获取url中"?"符后的字串
   if (url.indexOf("?") != -1) {    // 判断是否有参数
      var str = url.substr(1); // 从第一个字符开始 因为第0个是?号 获取所有除问号的所有符串
      strs = str.split("=");
	  // 用等号进行分隔 （因为知道只有一个参数 所以直接用等号进分隔 如果有多个参数 要用&号分隔 再用等号进行分隔）
      alert(strs[1]);          // 直接弹出第一个参数 （如果有多个参数 还要进行循环的）
   }
}
```
