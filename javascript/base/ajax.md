# Ajax 详解

## 概述

AJAX（Asynchronous Javascript And XML）翻译成中文就是“异步Javascript和XML”。即使用Javascript语言与服务器进行异步交互，传输的数据为XML（当然，传输的数据不只是XML）。

AJAX还有一个最大的特点就是，当服务器响应时，不用刷新整个浏览器页面，而是可以局部刷新。这一特点给用户的感受是在不知不觉中完成请求和响应过程。它包括：

- 与服务器的异步交互
- 浏览器的局部刷新

## 原生 Ajax

原生的 Ajax 主要就是使用 XMLHttpRequest 对象来完成请求的操作，该对象在主流浏览器中均存在（除早期的IE），Ajax首次出现 IE5.5 中存在（ActiveX控件）。

###  XMLHttpRequest 对象的属性和方法

**属性：**

- `readyState`：HTTP 请求的状态 从 0 到 4 发生变化
	- 0: 请求未初始化
	- 1: 服务器连接已建立
	- 2: 请求已接收
	- 3: 请求处理中
	- 4: 请求已完成，且响应已就绪
- `responseText`：响应体（不包括头部）
- `responseXML`：对请求的响应，解析为 XML 并作为 Document 对象返回。
- `status`：由服务器返回的 HTTP 状态代码，如 200 表示成功
- `statusText`：这个属性用名称而不是数字指定了请求的 HTTP 的状态代码。也就是说，当状态为 200 的时候它是 "OK"，当状态为 404 的时候它是 "Not Found"。

当 `readyState` 等于 4 且 `status` 为 200 时，表示响应已就绪

**方法：**

- `abort()`：取消当前响应，关闭连接并且结束任何未决的网络活动。
- `getAllResponseHeaders()`：把 HTTP 响应头部作为未解析的字符串返回。
- `getResponseHeader()`：返回指定的 HTTP 响应头部的值。
- `open()`：初始化 HTTP 请求参数，例如 URL 和 HTTP 方法，但是并不发送请求。
- `send()`：发送 HTTP 请求，使用传递给 open() 方法的参数，以及传递给该方法的可选请求体。
- `setRequestHeader()`：向一个打开但未发送的请求设置或添加一个 HTTP 请求。

### 跨浏览器的 XHR 对象

IE7之前的浏览器都是不支持原生的XMLHttpRquest对象的，IE5,6中是通过MSXML库中的一个ActiveX对象实现的。

```
var xhr = null;
function createXHR(){
    if (window.XMLHttpRequest){
         // 新浏览器
         xhr = new XMLHttpRequest();
     }else if (window.ActiveXObject){
         // IE5,IE6
         xhr = new ActiveXObject("Microsoft.XMLHTTP");
     }
}
```

## 实现 Ajax

### get请求

```
var xhr = function(){
    if (window.XMLHttpRequest) {
        return new XMLHttpRequest();
    }else{
        return new ActiveObject('Micrsorf.XMLHttp');
    }
}();

xhr.onreadystatechange = function(){
    switch(xhr.readyState){
        case 0 :
            console.log(0,'未初始化....');
            break;
        case 1 :
            console.log(1,'请求参数已准备，尚未发送请求...');
            break;
        case 2 :
            console.log(2,'已经发送请求,尚未接收响应');
            break;
        case 3 :
            console.log(3,'正在接受部分响应.....');
            data.innerHTML = xhr.responseText;
            break;
        case 4 :
            console.log(4,'响应全部接受完毕');
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                document.write(xhr.responseText);
            }else{
                document.write('error:' + xhr.status);
            }
            break;
    }
}
xhr.open('get','https://www.easy-mock.com/mock/5900413f739ac1685205dc45/we/ajax/get');
xhr.send(null);
```

### post请求

举个最简单的例子要用手机号和密码登录账号。

```
var btn = document.getElementById('add');
btn.onclick = function(){
    var tel = document.getElementById('tel').value.toString(),
        pwd = document.getElementById('pwd').value.toString();
	var data =encodeFormData({
        tel : tel,
        pwd : pwd
    });
    xhr.onreadystatechange = function(){
        switch(xhr.readyState){
            case 0 :
                console.log(0,'未初始化....');
                break;
            case 1 :
                console.log(1,'请求参数已准备，尚未发送请求...');
                break;
            case 2 :
                console.log(2,'正在添加....');
                break;
            case 3 :
                console.log(3,'已接收数据长度：'+xhr.responseText.length );
                break;
            case 4 :
                console.log(4,'响应全部接受完毕');
                if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                    databox.innerHTML = xhr.responseText;
                }else{
                    databox.innerHTML = 'error:' + xhr.status;
                }
                break;
        }
    }
    xhr.open('post','/member/login');
    xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded')
	// xhr.send('tel='+tel+'&pwd='+pwd); 拼接提交数据
	xhr.send(data);

};

function encodeFormData(data){
    if(!data) return '';
    var pairs = [];
    for(var name in data){
        if(!data.hasOwnProperty(name)) continue;
        if(typeof data[name] === 'function') continue;
        var value = data[name].toString();
        name = encodeURIComponent(name.replace('%20','+'));
        value = encodeURIComponent(value.replace('%20','+'));
        pairs.push(name+'='+value);
    }
    return pairs.join('&');
}
```

## Ajax 封装

[查看DEMO](./javascript/base/ajax.html)

```
function ajax(ops) {
    ops = ops || {}; // 传入方式默认为对象
    ops.type = (ops.type || "GET").toUpperCase(); // 默认为GET请求
    ops.dataType = ops.dataType || 'json'; // 返回值类型默认为json
    ops.async = ops.async || true; // 默认为异步请求
    var params = getParams(ops.data); // 对需要传入的参数的处理
    var xhr;
    if (window.XMLHttpRequest) {
        xhr = new XMLHttpRequest(); // 创建一个 ajax请求
    }else{
        xhr = new ActiveXObject('Microsoft.XMLHTTP')
    }
    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4){
            var status = xhr.status;
            if (status >= 200 && status < 300 ){
                ops.success && ops.success(xhr.responseText, xhr.responseXML);
            }else{
                ops.fail && ops.fail(status);
            }
        }
    };
    if (ops.type === 'GET'){
        xhr.open("GET",ops.url + '?' + params ,ops.async);
        xhr.send(null)
    }else if (ops.type === 'POST'){
        xhr.open('POST',ops.url,ops.async); // 打开请求
        xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded'); // POST请求设置请求头
        xhr.send(params); // 发送请求参数
    }
}

/**
 * 对象参数的处理
 * @param data
 * @returns {string}
 */
function getParams(data) {
    var arr = [];
    for (var param in data){
        arr.push(encodeURIComponent(param) + '=' +encodeURIComponent(data[param]));
    }
    arr.push(('randomNumber=' + Math.random()).replace('.'));
    // console.log('getParams',arr.join('&'));
    return arr.join('&');
}
```

**对封装好的ajax请求进行调用**

```
ajax({
    url:"https://www.easy-mock.com/mock/5900413f739ac1685205dc45/we/ajax/get", // 请求地址
    type:'GET',   // 请求方式 
    data:{ name:'oldman',age :'33'}, // 请求参数
    dataType:"json",     // 返回值类型的设定
    async:false,   // 是否异步
    success:function (res,xml) {
        console.log(res);   // 此处执行请求成功后的代码
    },
    fail:function (status) {
        console.log('状态码为'+status);   // 此处为执行失败后的代码
    }
});
```
