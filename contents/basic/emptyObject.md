## 问题：如何测试一个空的 JavaScript 对象？

在 AJAX 请求之后，有时我的程序可能会返回一个空对象，例如：

```js
var a = {};
```

我该如何检查这种情况？

## 答案

### ECMA 5之后:

```js
// 因为 Object.keys(new Date()).length === 0;
// 所以还得做一些额外的检查
obj // 👈 null and undefined check
&& Object.keys(obj).length === 0
&& Object.getPrototypeOf(obj) === Object.prototype
```

但是请注意，这会创建一个不必要的数组（`key` 的返回值）

### ECMA 5以前:

```js
function isEmpty(obj) {
  for(var prop in obj) {
    if(Object.prototype.hasOwnProperty.call(obj, prop)) {
      return false;
    }
  }

  return JSON.stringify(obj) === JSON.stringify({});
}
```

### 其他

```js

// lodash:
_.isEmpty({}); // true

// Underscore:
_.isEmpty({}); // true

// jquery
jQuery.isEmptyObject({}); // true
```
