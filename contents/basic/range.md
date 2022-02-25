## 问题：JavaScript是否有类似 range() 的方法，可以在边界内的生成一个范围？

在 PHP 语言中，有种 range 方法，通过传递上下界限值生成一个数字或字符的范围。

在 JavaScript 中是否有内置的方法可以实现这个功能？如果没有，我将如何实现它
？

```php
range(1, 3); // Array(1, 2, 3)
range("A", "C"); // Array("A", "B", "C")
```

## 答案

### 生成数字

```js
[...Array(5).keys()]; // => [0, 1, 2, 3, 4]
```

### 生成字符

```js
String.fromCharCode(...[...Array('D'.charCodeAt(0) - 'A'.charCodeAt(0) + 1).keys()].map(i => i + 'A'.charCodeAt(0)));
// => 'ABCD'
```

### 迭代

```js
for (const x of Array(5).keys()) {
  console.log(x, String.fromCharCode('A'.charCodeAt(0) + x));
}
 => 0,"A" 1,"B" 2,"C" 3,"D" 4,"E"
```

### 函数

```js
function range(size, startAt = 0) {
    return [...Array(size).keys()].map(i => i + startAt);
}

function characterRange(startChar, endChar) {
    return String.fromCharCode(...range(endChar.charCodeAt(0) -
            startChar.charCodeAt(0), startChar.charCodeAt(0)))
}
```

### typescript 函数

```ts
function range(size:number, startAt:number = 0):ReadonlyArray<number> {
    return [...Array(size).keys()].map(i => i + startAt);
}

function characterRange(startChar:string, endChar:string):ReadonlyArray<string> {
    return String.fromCharCode(...range(endChar.charCodeAt(0) -
            startChar.charCodeAt(0), startChar.charCodeAt(0)))
}
```

### lodash.js _.range()函数

```js
_.range(10);
 => [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
_.range(1, 11);
 => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
_.range(0, 30, 5);
 => [0, 5, 10, 15, 20, 25]
_.range(0, -10, -1);
 => [0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
String.fromCharCode(..._.range('A'.charCodeAt(0), 'D'.charCodeAt(0) + 1));
 => "ABCD"
```

### es6之前

```js
Array.apply(null, Array(5)).map(function (_, i) {return i;});
 => [0, 1, 2, 3, 4]
```


> 问题来源：[https://stackoverflow.com/questions/3895478/does-javascript-have-a-method-like-range-to-generate-a-range-within-the-supp](https://stackoverflow.com/questions/3895478/does-javascript-have-a-method-like-range-to-generate-a-range-within-the-supp)