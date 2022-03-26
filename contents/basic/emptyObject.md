## 问题：如何测试一个空的 JavaScript 对象？

在 `AJAX` 请求之后，有时我的程序可能会返回一个空对象，我该如何对空对象进行检查？

```js
var a = {};
```


## 答案

### ECMA 5之后:

```js
// 因为 Object.keys(new Date()).length === 0;
// 所以还得做一些额外的检查
obj // 👈 null and undefined check
&& Reflect.ownKeys(obj).length === 0 // 译者补充：增加非枚举属性判断
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
> 问题来源：[https://stackoverflow.com/questions/679915/how-do-i-test-for-an-empty-javascript-object](https://stackoverflow.com/questions/679915/how-do-i-test-for-an-empty-javascript-object)
