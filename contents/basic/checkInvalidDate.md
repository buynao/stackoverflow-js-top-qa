## 问题：在 JavaScript 中如何检测 "invalid date" 日期?

在 `JavaScript` 中，我想知道如何区分有效日期和无效日期

```js

var d = new Date("foo");
console.log(d.toString()); // 'Invalid Date'
console.log(typeof d); // 'object'
console.log(d instanceof Date); // 'true'

```

## 答案

我会这样做：

```js

if (Object.prototype.toString.call(d) === "[object Date]") {
  // 是日期类型
  if (isNaN(d)) { // 也可以 isNaN(d.getTime()) or isNaN(d.valueOf()) 
    // 无效日期
  } else {
    // 有效日期
  }
} else {
  // 不是日期类型
}

```

**Update [2018-05-31]:**

如果您不关心来自其他 `JavaScript` 上下文（外部窗口、框架或 iframe）的 `Date` 对象，可以使用这种更简单的方式：

```js
function isValidDate(d) {
  return d instanceof Date && !isNaN(d);
}
```

请注意，`invalid dates` -> `2013-13-32` 和 `invalid date objects` -> `new Date('foo')` 之间存在本质的区别。 本答案不验证日期输入是否正常，仅判断 `Date` 实例是否是有效日期。


> 问题来源：[https://stackoverflow.com/questions/1353684/detecting-an-invalid-date-date-instance-in-javascript](https://stackoverflow.com/questions/1353684/detecting-an-invalid-date-date-instance-in-javascript)