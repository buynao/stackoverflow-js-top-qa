## 问题：在 JavaScript 中循环枚举对象

我有一个 JavaScript 对象，如下所示：

```js
var p = {
    "p1": "value1",
    "p2": "value2",
    "p3": "value3"
};
```

现在我想遍历 `p` 的所有属性 `p1、p2、p3...`，并获取它们的 `键` 和 `值`。

我该怎么做？

## 答案

**1. ECMAScript 6 之前可以使用 `for-in` 循环**

但是，您必须得确保您获得的 `key` 是对象的实例属性，而不是来自原型。

**代码片段：**

```js
var p = {
    "p1": "value1",
    "p2": "value2",
    "p3": "value3"
};

for (var key in p) {
    // 确保 key 来自实例
    if (p.hasOwnProperty(key)) {
        console.log(key + " -> " + p[key]);
    }
}
// p1 -> value1
// p2 -> value2
// p3 -> value3
```

**2. ECMAScript 6 可以使用 `for-of` 和 `Object.keys()`**

```js
var p = {
    0: "value1",
    "b": "value2",
    key: "value3"
};

for (var key of Object.keys(p)) {
    console.log(key + " -> " + p[key])
}
// 输出同上
```

请注意上面使用的是 `for-of`，而不是 `for-in`。如果用错了，属性 `值` 返回的将是 `undefined`。 

`Object.keys()` 仅使用对象自己的属性而不会是整个原型链上的属性。

**3. ECMAScript 8 可以使用`Object.entries()`**

```js
  const p = {
      "p1": "value1",
      "p2": "value2",
      "p3": "value3"
  };

  for (let [key, value] of Object.entries(p)) {
    console.log(`${key}: ${value}`);
}
```

## 其他

`Object.keys()` 和 `Object.entries()` 都以与 `for...in` 循环相同的顺序迭代属性，但前两者会忽略原型链上的属性。只有对象自己的可枚举属性被迭代。
