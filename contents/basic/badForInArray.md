## 为什么不能使用“for...in”进行数组迭代？

为什么大家建议不要在 `JavaScript` 中使用 `for...in` 遍历数组。

什么原因？

## 答案

有一种情况，是来自下边这种构造数组的方式

```js
var a = []; // 创建一个空数组。
a[5] = 5;   // 在 JavaScript 中，可以直接调整数组大小

for (var i = 0; i < a.length; i++) {
    // 正如你所期望的那样，迭代从 0 到 5 的数字索引。
    console.log(a[i]);
}

/* 输出:
   undefined
   undefined
   undefined
   undefined
   undefined
   5
*/
```

有时也可能完全不同：

```js
var a = [];
a[5] = 5;
for (var x in a) {
    // 仅显示直接设置的索引 5，忽略了 0-4
    console.log(x);
}

/* 输出:
   5
*/
```

还有一种情况，要考虑一些 `JavaScript` 库可能会做原型覆盖这样的事情，这将影响您创建的任何数组：

```js
// 在你的 JavaScript 库深处的某个地方......
Array.prototype.foo = 1;

// 现在你不知道下面的代码会做什么。
var a = [1, 2, 3, 4, 5];
for (var x in a){
    // 现在 foo 是每个数组的一部分，
    // 并且将在此处显示为 x 的值。
    console.log(x);
}

/* 输出:
   0
   1
   2
   3
   4
   foo
*/
```

## 其他答案

`for-in` 语句的目的是枚举对象属性。

该语句将在原型链中上升，还会枚举继承的属性，通常情况下，这不是我们所期望的。

此外，规范表面了它不能保证迭代的顺序，这意味着如果你想 `迭代` 一个数组对象，使用这个语句你不能确定属性（数组索引）一定将按数字顺序访问。

例如，在之前的 `IE` 浏览器中：
```js
var array = [];
array[2] = 'c';
array[1] = 'b';
array[0] = 'a';

for (var p in array) {
  console.log([])
  //... p will be "2", "1" and "0" on IE
}
```

> 问题来源：[https://stackoverflow.com/questions/500504/why-is-using-for-in-for-array-iteration-a-bad-idea](https://stackoverflow.com/questions/500504/why-is-using-for-in-for-array-iteration-a-bad-idea)