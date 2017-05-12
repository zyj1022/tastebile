SessionStorage, LocalStorage, Cookie这三者都可以被用来在浏览器端存储数据，而且都是**字符串类型的键值对**！。 区别在于前两者属于WebStorage，创建它们的目的便是存储客户端数据。 而Cookie早在网景公司的浏览器中就开始支持，最初目的是为了保持HTTP的状态。

## 概念介绍

### Cookie

 - **Cookie** 是存储在客户端的小型文本文件，可以包含若干键值对，每个键值对可以设置过期时间（默认过期时间为关闭浏览器时）。 
 - Cookie 会在每次发送HTTP请求时附加到`Cookie`头字段，服务器以此得知用户所处的状态。 
 - 在HTTP标准中，规定 Cookie 至少要有4K，至少支持300项Cookie，每个域名至少支持20项。

### LocalStorage

- LocalStorage 本地存储，不会发送到服务器上，一般5～10M
- 每个域名会有不同的LocalStorage，而且可以在多个标签页中互相访问
- LocalStorage没有过期时间，除非手动删除它会一直存在。

### SessionStorage

- SessionStorage 也是本地存储，不会被发送到服务器上，一般5～10M
- 每个域名会有不同的SessionStorage
- SessionStorage在浏览器会话结束时（关闭标签页，不包括刷新和跳转）清空


## 使用方法

### Cookie的使用

服务器可以在HTTP响应中设置 `set-cookie` 字段来设置Cookie。 客户端也可以操作Cookie，比如有的电商网站会使用Cookie来存储购物车信息。 客户端JavaScript也可以通过浏览器提供的 `document.cookie` 来操作Cookie，但document.cookie整个是一个字符串， 所以操作较为复杂，Web开发中一般倾向于封装Cookie的读写操作:

```
function setCookie(key,value){
    var dateStr = (new Date()).toGMTString();
    document.cookie = key + "=" + escape(value) + ";expires=" + dateStr;
}
function getCookie(key){
    var idx = document.cookie.indexOf(key + "=")
    if(idx == -1) return;
    
    var start = idx + key.length + 1,
        end = document.cookie.indexOf(";", idx);
    if(end==-1) end = document.cookie.length;
    
    return unescape(document.cookie.substr(start,end));
}
```

通过jQuery-Cookie插件可以更方便地操作Cookie：

```
$.cookie('name', 'value');  
$.cookie('name', 'value', { expires: 7 });  
$.cookie('name', 'value', { expires: 7, path: '/' });   // 全站有效

$.cookie('name');   // "value"
$.cookie();         // { "name": "value" }
```


### Storage的使用

LocalStorage/SessionStorage提供的存储也是基于字符串的键值对。可以通过setItem，getItem来访问其中的存储项：

```
localStorage.clear();
localStorage.setItem('key', 'value');
localStorage.getItem('key');     // => 'value'
localStorage.removeItem('key');
```

因为它只能存储字符串，要存JSON只能序列化为字符串：

```
var testObject = { 'one': 1, 'two': 2, 'three': 3 };

// Put the object into storage
localStorage.setItem('testObject', JSON.stringify(testObject));

// Retrieve the object from storage
var retrievedObject = localStorage.getItem('testObject');

console.log('retrievedObject: ', JSON.parse(retrievedObject));
```

> 这段代码来自StackOverflow： http://stackoverflow.com/questions/2010892/storing-objects-in-html5-localstorage