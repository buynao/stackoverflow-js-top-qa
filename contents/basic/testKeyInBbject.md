## 问题：检查 JavaScript 对象中是否存在某个键？

如何检查 `JavaScript` 对象或数组中是否存在特定 `键`？

如果`键`不存在，而我尝试访问它，它会返回 `false` 吗？还是抛出错误？ 

## 答案

直接判断 `key === undefined`， 并不能准确的判断这个 `键` 是否存在。

比如， `键` 确实存在，但 `值` 实际上是 `undefined` 呢？

```js
var obj = { key: undefined };
console.log(obj["key"] !== undefined); // false, 但键是存在的
```

改用 `in` 运算符：

```js
var obj = { key: undefined };
console.log("key" in obj); // true, 与实际值无关
```

请注意，如果需要直接判断 `键` 是否存在，记得使用括号把判断条件包裹起来：

```js

var obj = { not_key: undefined };
console.log(!("key" in obj)); // 如果对象中不存在“key”，则为 true
console.log(!"key" in obj);   // 不要这样做！会被当做这种判断: false in obj

```

如果你想测试对象实例的属性（而不是继承的属性），请使用 `hasOwnProperty` ：

```js
var obj = { key: undefined };
console.log(obj.hasOwnProperty("key")); // true
```

关于 `in` , `hasOwnProperty` 和 `key === undefined` 三种方法之间的性能比较，请参阅此 [文档](https://jsben.ch/WqlIl) 。

![result](https://raw.githubusercontent.com/buynao/stackoverflow-js-top-qa/main/contents/basic/assets/result.png)
