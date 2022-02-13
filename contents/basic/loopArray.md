## 问题：在 JavaScript 中循环遍历数组

在 Java 中，可以使用 for 循环遍历数组中的对象，如下所示：

```java

String[] myStringArray = {"Hello", "World"};
for (String s : myStringArray)
{
    // Do something
}

```

在 JavaScript 中应该怎么做?

## 答案

### 三个主要选项：

- `for (var i = 0; i < xs.length; i++) { console.log(xs[i]); }`
- `xs.forEach((x, i) => console.log(x));`
- `for (const x of xs) { console.log(x); }
`

详细示例如下：

**1.顺序 for 循环：**


```js
var myStringArray = ["Hello","World"];
var arrayLength = myStringArray.length;
for (var i = 0; i < arrayLength; i++) {
    console.log(myStringArray[i]);
    //Do something
}
```

优点：

- 适用于各种环境
- 可以使用 `break` 和 `continue` 流控制语句

缺点：

- 代码冗长
- 容易出现一个 [错误（有时也称为栅栏柱错误）](https://en.wikipedia.org/wiki/Off-by-one_error#Looping_over_arrays)

**2. Array.prototype.forEach:**

`ES5` 规范引入了很多有益的数组方法。

`Array.prototype.forEach` 就是其中之一，它为我们提供了一种迭代数组的简洁方法：

```js
const array = ["one", "two", "three"]
array.forEach(function (item, index) {
  console.log(item, index);
});

// 使用 ES6 箭头函数语法，会让它更加简洁：

array.forEach(item => console.log(item));

```

优点：

- 非常简短和简洁
- 声明式

缺点：

- 对于迭代串行异步不太友好
- 不能使用控制流语句，不能中断/继续

然而，您可以通过在迭代数组之前，先过滤它们，从而代替中断命令式循环的需要，例如：

```js
array.filter(item => item.condition < 10)
     .forEach(item => console.log(item))
```

请记住，如果您正在迭代一个数组以从中构建另一个数组，您应该使用 `map`。我已经多次看到这种反模式。

**反模式：**

```js
const numbers = [1,2,3,4,5], doubled = [];

numbers.forEach((n, i) => { doubled[i] = n * 2 });

```

**正确用例：**

```js
const numbers = [1,2,3,4,5];
const doubled = numbers.map(n => n * 2);
```

如果您尝试将数组归约为一个 `值` ，例如，你想对一个数字数组求和，则应使用 `reduce` 方法。


**反模式：**

```js
const numbers = [1,2,3,4,5];
const sum = 0;
numbers.forEach(num => { sum += num });
```

**正确用例：**

```js
const numbers = [1,2,3,4,5];
const sum = numbers.reduce((total, n) => total + n, 0);
```

**3. ES6 for-of ：**

`ES6` 标准引入了可迭代对象的概念，并定义了一种用于遍历数据的新结构，即 `for...of` 语句。

该语句适用于任何类型的可迭代对象，也适用于生成器（任何具有 `Symbol.iterator` 属性的对象）。

`数组` 是 `ES6` 中定义的内置 `可迭代对象`，因此可以直接使用以下语句：

```js
let colors = ['red', 'green', 'blue'];
for (const color of colors){
    console.log(color);
}
```

**优点:**

- 它可以迭代各种各样的对象
- 可以使用正常的流控制语句 `break/continue`
- 对于迭代串行异步很有用，可以使用 `async await 语句`

**缺点：**

- 无法获取当前索引

### 不要使用 `for...in`

对于迭代数组 `for-in` 应该避免，这个语句主要是用来 **枚举** 对象属性。

它不应该用于类似数组的对象，因为：

- 它不能保证迭代的顺序；数组索引可能不会按数字顺序访问。
- 还会枚举继承的属性。

尤其是第二点，它会带来很多问题。例如，如果你的代码库里扩展了 `Array.prototype` 对象，在其中添加了其他方法，那么这些添加的属性也会被枚举出来。

```js
Array.prototype.foo = "foo!";
var array = ['a', 'b', 'c'];

for (var i in array) {
    console.log(array[i]);
}
```

上面的代码将控制台记录`“a”、“b”、“c”和“foo！”`。

如果您使用一些严重依赖原生原型增强的库（例如 `MooTools`），这可能会变得很复杂。

正如我之前所说，`for-in` 语句用于枚举对象属性，例如：

```js
var obj = {
    "a": 1,
    "b": 2,
    "c": 3
};

for (var prop in obj) {
    if (obj.hasOwnProperty(prop)) {
        //  为了安全起见，也可以
        //  if (Object.prototype.hasOwnProperty.call(obj,prop))
        console.log("prop: " + prop + " value: " + obj[prop])
    }
}
```

在上面的示例中， `hasOwnProperty` 方法允许你仅枚举自己的属性：只检索对象当前具有的属性，绕过继承的属性（原型上的属性）。

最后，我建议您阅读以下文章：

[Enumeration VS Iteration
](http://web.archive.org/web/20101213150231/http://dhtmlkitchen.com/?category=/JavaScript/&date=2007/10/21/&entry=Iteration-Enumeration-Primitives-and-Objects)

> 问题来源：[https://stackoverflow.com/questions/3010840/loop-through-an-array-in-javascript](https://stackoverflow.com/questions/3010840/loop-through-an-array-in-javascript)