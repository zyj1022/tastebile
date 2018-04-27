# 字符串特殊符号转义

为了保证页面输出安全，我们经常需要对一些特殊的字符进行转义，用一个函数escapeHtml，将<, >, &, “ 等进行转义

```
function escapeHTML(str) {
	str = "" + str;
	return str.replace(/&/g, "&amp;")
			.replace(/</g, "&lt;")
			.replace(/>/g, "&gt;")
			.replace(/"/g, "&quot;")
			.replace(/'/g, "&apos;");;
}

function unescapeHTML(str) {
	str = "" + str;
	return str.replace(/&lt;/g, "<")
			.replace(/&gt;/g, ">")
			.replace(/&amp;/g, "&")
			.replace(/&quot;/g, '"')
			.replace(/&apos;/g, "'");
}

console.log(escapeHTML("<div>")); // &lt;div&gt;
```
