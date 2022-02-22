## 问题：什么时候应该使用encodeURI，而不是encodeURIComponent？

当需要对查询的参数进行编码时，应该使用 `encodeURI` 还是 `encodeURIComponent` ?

## 答案：

### 1. encodeURI()

当你需要一个完整有效的URL时，可以使用 `encodeURI` ：

```js

encodeURI("http://www.example.org/a file with spaces.html")

// http://www.example.org/a%20file%20with%20spaces.html

```

如果直接使用 `encodeURIComponent`，它将破坏 url 的完整性：

```js
encodeURIComponent("http://www.example.org/a file with spaces.html")

// 'http%3A%2F%2Fwww.example.org%2Fa%20file%20with%20spaces.html'

```

### 2. encodeURIComponent()

如果需要对一个 URL 参数的值进行编码时，可以使用 `encodeURIComponent`。
```js

// 查询参数
var p1 = encodeURIComponent("http://example.org/?a=12&b=55");

// 通过查询参数p1，构建你的url
var url = "http://example.net/?param1=" + p1 + "&param2=99";

// url => http://example.net/?param1=http%3A%2F%2Fexample.org%2F%Ffa%3D12%26b%3D55&param2=99


```

注意，`encodeURIComponent` 并不转义 `'` 字符。

有种常见的错误用法是用它来创建 `html` 的属性，如 `href='MyUrl'`，这可能会出现注入错误。

在构建 `html` 时，最好使用双引号 `"` 而不是单引号 `'` 作为属性的引号，或者增加一层额外的编码（ `'` 可以被编码为 `%27` ）。

关于这种类型的编码的更多信息，你可以查看：[http://en.wikipedia.org/wiki/Percent-encoding](http://en.wikipedia.org/wiki/Percent-encoding)

## 其他答案

`encodeURI` 会替换所有的字符，但不包括以下字符，即使它们具有适当的 `UTF-8` 转义序列：

|  类型   | 包含  |
|  ----  | ----  |
| 保留字符	  | ; , / ? : @ & = + $ |
| 非转义的字符	  | 字母 数字 - _ . ! ~ * ' ( ) |
| 数字符号		  | # |

如果试图编码一个非高-低位完整的代理字符，`encodeURI` 和 `encodeURIComponent` 都将会抛出一个 `URIError` 错误，例如：

```js
// 编码高-低位完整字符 ok
console.log(encodeURI('\uD800\uDFFF'));

// 编码单独的高位字符抛出 "Uncaught URIError: URI malformed"
console.log(encodeURI('\uD800'));

// 编码单独的低位字符抛出 "Uncaught URIError: URI malformed"
console.log(encodeURI('\uDFFF'));
```

并且需要注意，如果URL需要遵循较新的 [RFC3986](https://datatracker.ietf.org/doc/html/rfc3986) 标准，那么方括号是被保留的(给 `IPv6` )，因此对于那些没有被编码的 URL 部分(例如主机)，可以使用下面的代码：

```js
function fixedEncodeURI (str) {
    return encodeURI(str).replace(/%5B/g, '[').replace(/%5D/g, ']');
}
```

`encodeURIComponent` 转义除了如下所示外的所有字符：

不转义的字符： `A-Z a-z 0-9 - _ . ! ~ * ' ( )`

通过以下方法测试，`encodeURIComponent()` 和 `encodeURI` 在编码字符时，有 11 个不同点：

```js
var arr = [];
for(var i=0;i<256;i++) {
  var char=String.fromCharCode(i);
  if(encodeURI(char)!==encodeURIComponent(char)) {
    arr.push({
      character:char,
      encodeURI:encodeURI(char),
      encodeURIComponent:encodeURIComponent(char)
    });
  }
}
console.table(arr);
```

输出

![result](https://raw.githubusercontent.com/buynao/stackoverflow-js-top-qa/main/contents/advanced/assets/encode.png)

为了避免服务器收到不可预知的请求，对任何用户输入的作为URI部分的内容你都需要用 `encodeURIComponent` 进行转义。

比如，一个用户可能会输入`"Thyme &time=again"` 作为 `comment` 变量的一部分。如果不使用 `encodeURIComponent` 对此内容进行转义，服务器得到的将是`comment=Thyme%20&time=again`。请注意，`"&"` 符号和 `"="` 符号产生了一个新的键值对，所以服务器得到两个键值对（一个键值对是 `comment=Thyme`，另一个则是 `time=again`），而不是一个键值对。

对于` application/x-www-form-urlencoded (POST)` 这种数据方式，空格需要被替换成 `'+'`，所以通常使用 `encodeURIComponent` 的时候还会把 `"%20"` 替换为 `"+"`。

为了更严格的遵循 [RFC3986](https://datatracker.ietf.org/doc/html/rfc3986) 标准（它保留 !, ', (, ), 和 *），即使这些字符并没有正式划定 URI 的用途，下面这种方式是比较安全的：

```js
function fixedEncodeURIComponent (str) {
  return encodeURIComponent(str).replace(/[!'()*]/g, function(c) {
    return '%' + c.charCodeAt(0).toString(16).toUpperCase();
  });
}

```