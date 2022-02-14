## 问题：如何创建一个 [1，2，3，4，N...] 的数组？

我想找到能够替代下面这种的方法，来创建一个包含 1 到 N 的 JavaScript 数组，其中 N 是已知的。

```js
var foo = [];

for (var i = 1; i <= N; i++) {
   foo.push(i);
}
```

我觉得应该有一种方法，可以在 `没有循环 `的情况下做到这一点。

## 答案：

**在 `ES6` 中使用 ` Array from()` 和 `keys()` 方法。**


```js
Array.from(Array(10).keys())
//=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```


**使用 `扩展运算符` 的简单版本。**

```js
[...Array(10).keys()]
//=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

**从 1 开始，将具有 `length` 属性的对象和 `map` 函数传递给 `Array from()` ：**

```js
Array.from({length: 10}, (_, i) => i + 1)
//=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

> 问题来源：[https://stackoverflow.com/questions/3746725/how-to-create-an-array-containing-1-n](https://stackoverflow.com/questions/3746725/how-to-create-an-array-containing-1-n)