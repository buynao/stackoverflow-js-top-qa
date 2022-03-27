## 问题

我问这个问题是因为我和我的同事在编码风格上有一些争议，因为他更喜欢箭头函数，而我更喜欢传统的函数声明。

```js
const sum = (a, b) => a + b;
// or
function sum(a, b) {
    return a + b;
}
```

我的观点是，传统的函数声明更具可读性，可以更清楚地分辨出函数和变量的声明。

他的观点是，箭头函数的代码运行得会更快。

那么在 `v8` 中，当使用传统的函数声明而不是箭头函数时，性能有损失吗？这种影响是否真的存在？

## 答案

我是 `V8` 的开发者。箭头函数（大部分）只是传统函数声明的 "语法糖"。

在性能上没有任何区别。

> 问题来源：[https://stackoverflow.com/questions/44030645/are-arrow-functions-faster-more-performant-lighter-than-ordinary-standalone-f](https://stackoverflow.com/questions/44030645/are-arrow-functions-faster-more-performant-lighter-than-ordinary-standalone-f)