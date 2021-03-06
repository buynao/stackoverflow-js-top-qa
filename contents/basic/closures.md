## 问题: JavaScript闭包是如何工作的？

你会如何向已经了解了一些基本语法概念（例如函数、变量等），但不了解 `闭包`  的人解释 `JavaScript 闭包`？

## 答案

`闭包` 产生于一种组合：一个函数和对该函数外部范围 `词法环境` 的引用。

`词法环境` 是每个 `执行上下文` 的一部分，是 `标识符（既变量名称）` 和 `值` 之间的映射集合，可以把它理解成一个对象。它的 `属性` 就是当前环境中的 `标识符（变量名称）`，`值` 就是变量的 `值`。

`JavaScript` 中的每个函数都保持对其外部 `词法环境` 的引用。当函数被调用时，这个引用能够找到其当时创建的 `执行环境`。

不管当前的函数在任意时刻被调用，这个引用都可以使得该函数内部的代码能够 "看到" 函数外部声明的变量，从而进行引用。

如果一个 `函数A` 被另一个 `函数B` 包含，而这个 `函数B` 又被另一个 `函数C` 包含，那么从最底层的 `函数A` 内部就会产生一个指向外部一直到 `函数C` 的 `词法环境` 的 `引用链`，这就是我们经常说的 `作用域链`。

在下面的代码中，`inner` 与 `foo` 被调用时创建的 `执行上下文` 的 `词法环境` 形成一个 `闭包`，将变量 `secret` 进行了 [隐藏]。

```js

function foo() {
  const secret = Math.trunc(Math.random() * 100)
  return function inner() {
    console.log(`The secret number is ${secret}.`)
  }
}
const f = foo() // `secret' 不能从 `foo` 外部直接访问 
f() // 取到 `secret` 的唯一方法是调用 `f()`


```

换句话说：

在 `JavaScript` 中，函数带有一个私有 `状态容器（box of state）` 的引用，只有在当前 `词法环境` 中，或者在其中声明的 `其他函数` 才能对这个 `状态容器`  里的数据进行访问。

`状态容器` 对函数的调用者来说是不可见的，这也为数据隐藏和封装提供了一个很好的机制。

请记住：

`JavaScript` 中的函数是一等公民，他们也像变量一样可以随意传递。这也就意味着函数的功能和状态，可以在你的代码中随意传递。

如果 `JavaScript` 没有 `闭包`，则必须在函数之间 `显式传递` 这些状态，从而使参数列表更长且代码更嘈杂。

所以，如果您想让函数始终可以访问到其他可能的 `私有状态`，则可以使用 `闭包`。

在 `C` 和大多数其他常见语言中，在函数返回后，所有 `局部变量` 都不再可访问，因为 `堆栈帧` 被破坏了。

在 `JavaScript` 中，如果你在另一个 `函数A` 中声明一个 `函数B`，那么外部 `函数A` 的 `局部变量` 在从它返回后仍然可以在 `函数B` 中被访问到。

所以，在上面的代码中，`secret` 在从 `foo` 返回后，仍然可以被函数对象 `inner` 使用。

### 闭包的使用

每当您需要 `函数` 包含一些私有状态时，`闭包` 都是有用的。

请记住：`JavaScript` 直到 2015 年才有一个 `class`，而且它仍然是个没有私有属性的语法。而 `闭包` 可以满足这一需求。

---
> 译者注: ES2022规范中，已经支持了，`class` 的私有属性。为了加深大家对闭包的理解，以下内容继续翻译，不做删除。更加现代化的方案可以参考一下文档：

> [MDN 类私有域](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes/Private_class_fields)

> [ES2022 class-definitions](https://tc39.es/ecma262/multipage/ecmascript-language-functions-and-classes.html#sec-class-definitions)

---
### 私有实例变量

在下面的代码中，函数 `toString()` 隐藏了汽车的详细信息。

```js

function Car(manufacturer, model, year, color) {
  return {
    toString() {
      return `hello : ${manufacturer} ${model} (${year}, ${color})`
    }
  }
}

const car = new Car('Aston Martin', 'V8 Vantage', '2012', 'Quantum Silver')
console.log(car.toString()) // hello : Aston Martin V8 Vantage (2012, Quantum Silver)

```

### 函数式编程

在下面的代码中，函数 `inner` [隐藏] 了 `fn` 和 `args` 。

```js

function curry(fn) {
  const args = []
  return function inner(arg) {
    if(args.length === fn.length) return fn(...args)
    args.push(arg)
    return inner
  }
}

function add(a, b) {
  return a + b
}

const curriedAdd = curry(add)
console.log(curriedAdd(2)(3)()) // 5

```

### 事件编程

在下面的代码中，函数 `onClick` [隐藏] 了变量 `BACKGROUND_COLOR`

```js

const $ = document.querySelector.bind(document)
const BACKGROUND_COLOR = 'rgba(200, 200, 242, 1)'

function onClick() {
  $('body').style.background = BACKGROUND_COLOR
}

$('button').addEventListener('click', onClick)
<button>Set background color</button>

```


### 模块化

在下面的例子中，所有的执行细节都隐藏在一个 `立即执行` 的 `函数表达式` 里面。

函数 `tick` 和 `toString` 隐藏了它们完成工作所需的 `私有状态 numbers ` 和 `函数 format` 。`闭包` 使我们能够对代码进行模块化和封装。

```js

let namespace = {};

(function foo(n) {
  let numbers = []

  function format(n) {
    return Math.trunc(n)
  }

  function tick() {
    numbers.push(Math.random() * 100)
  }

  function toString() {
    return numbers.map(format)
  }

  n.counter = {
    tick,
    toString
  }
}(namespace))

const counter = namespace.counter
counter.tick()
counter.tick()
console.log(counter.toString())

```

## Examples

### Example 1

这个例子表明，局部变量在 `闭包` 中没有被复制：`闭包` 保持着对原始变量本身的引用。

这就好比 `堆栈(call stack)` 在内存中一直保持着活力，甚至在外层函数退出之后。

```js

function foo() {
  let x = 42
  let inner = () => console.log(x)
  x = x + 1
  return inner
}

foo()() // logs 43

```


### Example 2

在下面的代码中，三个方法 `log`、`increment` 和 `update` 都在同一个`词法环境`中关闭。

每当 `createObject` 被调用时，一个新的执行环境`（堆栈call stack）`被创建，一个全新的变量 `x` 和一组新的函数`（log等）`被创建，它们在这个`新变量 P`中隐藏着。

```js

function createObject() {
  let x = 42;
  return {
    log() { console.log(x) },
    increment() { x++ },
    update(value) { x = value }
  }
}

const o = createObject()
o.increment()
o.log() // 43
o.update(5)
o.log() // 5
const p = createObject()
p.log() // 42

```

### Example 3

如果你在使用 `var` 声明变量，你要明白这个变量的一些副作用： `var` 声明的变量是会 `声明提升` 的。

由于引入了 `let` 和 `const` ，这个问题在现代的 `JavaScript` 中要少得多。

在下面的代码中，每次循环都会创建一个新的函数 `inner`，它隐藏了 `i`。

但由于 `var i` 被 `声明提升` 在循环之外了，所以这些内部函数都在同一个变量中闭合了，这意味着 `i` 的最终值 `3` 被打印了三次。

```js

function foo() {
  var result = []
  for (var i = 0; i < 3; i++) {
    result.push(function inner() { console.log(i) } )
  }

  return result
}

const result = foo()
// 下面将打印 "3"，三次...。
for (var i = 0; i < 3; i++) {
  result[i]() 
}

```

## 最后几点

- 每当在 `JavaScript` 中声明函数时，都会产生 `闭包`。
- `function` 从另一个函数内部返回 `a` 是 `闭包` 的经典案例，因为外部函数内部的状态对于返回的内部函数是隐式可用的，即使在外部函数完成执行之后也是如此。
- 每当 `eval()` 在函数内部使用时，都会使用 `闭包`。文本 `eval` 可以引用函数的局部变量，在非严格模式下，甚至可以使用 `eval('var foo = …')`.
- 当 `new Function(…)` 在函数内使用 `( Function 构造函数)` 时，它不会关闭其 `词法环境`：而是关闭全局上下文。新函数不能引用外部函数的局部变量。
- `JavaScript` 中的 `闭包` 就像在函数声明时保留了对作用域的引用（而不是副本），而后者又保留对其外部作用域的引用，依此类推，一直到顶部的全局对象作用域链。
声明函数时会创建一个`闭包`；这个 `闭包` 用于在调用函数时配置执行上下文。
- 每次调用函数时都会创建一组新的局部变量。

## 一些引用

- Douglas Crockford's simulated private attributes and [private methods for an object](http://www.crockford.com/javascript/private.html), using closures.
- A great explanation of how closures can cause [memory leaks in IE](https://www.codeproject.com/Articles/12231/Memory-Leakage-in-Internet-Explorer-revisited) if you are not careful.
- MDN documentation on [JavaScript Closures.](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)

> 问题来源：[https://stackoverflow.com/questions/111102/how-do-javascript-closures-work](https://stackoverflow.com/questions/111102/how-do-javascript-closures-work)