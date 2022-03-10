## 问题：在JavaScript中把字符转换成ASCII编码？

如何用 JavaScript 将一个 `string` 转换成 `ASCII` 字符？

例如：

```js
get 10 from "\n".
```

## 答案：

[String.prototype.charCodeAt()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt) 可以将 string 字符转换为 `ASCII` 字符。

相反，使用 [String.fromCharCode()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/fromCharCode) 可以将数字转换为相等的 `ASCII` 字符。这个函数可以接受多个数字并连接所有的字符然后返回字符串。

例子:

```js
"\n".charCodeAt(0); // 10

String.fromCharCode(10); // \n

```

这里有一个快速的 `ASCII` 字符参考。

![result](https://raw.githubusercontent.com/buynao/stackoverflow-js-top-qa/main/contents/basic/assets/ascii.png)

## 其他

`charCodeAt()` 方法返回 0 到 65535 之间的整数，表示给定索引处的 UTF-16 代码单元

请注意，答案中建议的 `String.prototype.charCodeAt()` 方法会返回 `UTF-16` 编码单元（由于历史原因，其并不是完整的正确的 `UTF-16` 编码）。只有前 128 个 `Unicode` 码位是直接匹配 `ASCII` 字符编码的。

`UTF-16` 编码单元匹配能用一个 `UTF-16` 编码单元表示的 `Unicode` 码位。如果 `Unicode` 码位不能用一个 `UTF-16` 编码单元表示（因为它的值大于0xFFFF ），则所返回的编码单元会是这个码位代理对的第一个编码单元) 。如果你想要整个码位的值，使用 `codePointAt()` 。

> 问题来源：[https://stackoverflow.com/questions/94037/convert-character-to-ascii-code-in-javascript](https://stackoverflow.com/questions/94037/convert-character-to-ascii-code-in-javascript)