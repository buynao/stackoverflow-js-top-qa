## 在 JavaScript 中如何将十进制转换为十六进制

## 答案

将数字转换为十六进制字符串：

```js
  hexString = yourNumber.toString(16);
```

并通过以下方式反转该过程：

```js
 yourNumber = parseInt(hexString, 16);
```

## 其他答案

如果还需要处理 `位字段 (Bit field)`或 32 位颜色等内容，则需要考虑一些带有符号的数字类型。
直接使用 `toString(16)` 会返回一个负的十六进制数字，这通常不是我们想要的。

这个函数做了一些额外处理，使其成为一个正数，在进行转换...

```js

function decimalToHexString(number)
{
  if (number < 0)
  {
    number = 0xFFFFFFFF + number + 1;
  }

  return number.toString(16).toUpperCase();
}

console.log(decimalToHexString(27)); // 1B
console.log(decimalToHexString(48.6)); // 30.99999999999A

```

译者注：

关于 `位字段` 的相关知识，[这篇文章](https://emergent.systems/posts/bit-fields/) 介绍的比较详细，有兴趣的同学可以看下。

> 问题来源：[https://stackoverflow.com/questions/57803/how-to-convert-decimal-to-hexadecimal-in-javascript](https://stackoverflow.com/questions/57803/how-to-convert-decimal-to-hexadecimal-in-javascript)

