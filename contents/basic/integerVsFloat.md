## 问题：如何判断一个 number 是浮点数还是整数？

如何判断一个 number 是浮点数还是整数？

```js
1.25 --> float 浮点数
1 --> integer  整数
0 --> integer  整数
0.25 --> float 浮点数
```

## 答案

### ES6 之后

> Number.isInteger

```js
function fits(x, y) {
  if (Number.isInteger(y / x)) {
    return 'Fits!';
  }
  return 'Does NOT fit!';
}

console.log(fits(5, 10));
// expected output: "Fits!"

console.log(fits(5, 11));
// expected output: "Does NOT fit!"

```

### ES6 之前

在除以 1 时检查是否还有余数。

```js
function isInt(n) {
   return n % 1 === 0;
}
```

如果你不知道参数是什么类型，你还需要在加个判断。

```js
function isInt(n){
    return Number(n) === n && n % 1 === 0;
}

function isFloat(n){
    return Number(n) === n && n % 1 !== 0;
}
```

> 问题来源：[https://stackoverflow.com/questions/3885817/how-do-i-check-that-a-number-is-float-or-integer](https://stackoverflow.com/questions/3885817/how-do-i-check-that-a-number-is-float-or-integer)