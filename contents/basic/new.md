## 问题：怎么理解 JavaScript 中的 new

通常大家认为 `JavaScript` 不是一种面向对象的编程语言，所以，在遇到 `new` 这个关键字时，会有一些疑惑

- 它是用来干什么的？
- 它解决了哪些问题？
- 什么时候用合适，什么时候不合适？

## 答案

它主要做了 5 件事：

1. 创建了一个新 `object`，这个 `object` 就是一个比较纯粹的 `{} object`。
2. 将这个 `object` 内部、不可访问的 `[[prototype]]`（即 `__proto__` ）属性赋值为 `构造函数`的 `prototype`（每个函数对象都自动具有 `prototype` 属性）。
3. 使 `构造函数` 内部的 `this` 指向新创建的 `object` 。
4. 执行 `构造函数` 中的代码，只要执行到 `this` 相关，就将该属性赋值给新 `object` 。
5. 如果 `构造函数` 的执行结果返回的也是一个 `object`，则直接返回；否则，返回刚创建的 `object`。

注意：`构造函数` 是指 `new` 关键字后面的函数：

```js
// 构造函数 = ConstructorFunction
new ConstructorFunction(arg1, arg2)
```

得到新 `object` 后，如果查询它的未知属性，将改为查询它的 `[[prototype]]` 对象上的属性。这就是在 `JavaScript` 中获得类似于传统类继承的方法。

其中最难理解的部分是第 2 点。每个对象（包括函数）都有一个内部属性，叫做 `[[prototype]]`。

它通常只在对象创建时被设置，要么用 `new`，要么用 `Object.create`，或者基于字面意思（函数默认为 `Function.prototype` ，数字为 `Number.prototype`，等等）。它只能通过 `Object.getPrototypeOf(someObject)` 读取。没有其他方法来设置或读取这个值。

函数除了隐藏的 `[[prototype]]` 属性外，还有一个叫做 `prototype` 的属性，你可以对其随时访问和修改，以便为你创建的对象提供继承的属性和方法。

### 示例

```js

ObjMaker = function() { this.a = 'first'; };
// ObjMaker 只是一个函数，并没有什么特别的地方让它成为一个构造函数。 
// 它是一个构造器。

ObjMaker.prototype.b = 'second';
// 像所有的函数一样，ObjMaker 有一个可访问的原型 prototype 属性。
// 我们可以改变它。我只是给它添加了一个叫做'b'的属性。
// 像所有的对象一样，ObjMaker 也有一个不可访问的 [[prototype]] 属性
// 我们不能对它做任何事情

obj1 = new ObjMaker();
// 刚刚发生了 3 件事。
// 1. 一个新的、空的对象被创建，叫做 obj1。 起初 obj1 和 {} 是一样的，是一个空对象。
// 2. 然后 obj1 的 [[prototype]] 属性被设置为当前的 ObjMaker.prototype 的对象值
//（ 如果 ObjMaker.prototype 后来被分配一个新的对象值，obj1 的 [[prototype]] 将不会变，
// 但你可以改变 ObjMaker.prototype 的属性，将其添加到 prototype 和 [[prototype]] ）
// 3. ObjMaker 函数被执行，其中 obj1 代替了 this ... 所以 obj1.a 被设置为 'first'。

obj1.a;
// returns 'first'

obj1.b;
// obj1 上没有一个叫 'b' 的属性，所以 JavaScript 查询了它的 [[prototype]]。
// 它的 [[prototype]] 与 ObjMaker.prototype 相同。
// ObjMaker.prototype 上有一个名为 'b' 的属性，值为 'second'。
// 所以，obj1.b 返回'second'。

```

这就像类继承，因为现在，您使用 `new ObjMaker()` 创建的任何对象，都继承了 `b` 属性。

如果你想要一个子类之类的东西，那么你可以这样做：

```js
SubObjMaker = function () {};
SubObjMaker.prototype = new ObjMaker(); 
// 注意：这种模式已经被废弃了!
// 因为我们使用了'new'，所以 SubObjMaker.prototype 的 [[prototype]] 属性
// 现在被设置为 ObjMaker.prototype 的对象值
// 现代的方法是使用 Object.create()，这是在 ECMAScript 5 中加入的。
// SubObjMaker.prototype = Object.create(ObjMaker.prototype);

SubObjMaker.prototype.c = 'third';  
obj2 = new SubObjMaker();
// bj2 的 [[prototype]] 属性现在被设置为 SubObjMaker.prototype。
// 记住 SubObjMaker.prototype 的 [[prototype]] 属性是 ObjMaker.prototype。
// 所以现在obj2有一个原型链! obj2 --> SubObjMaker.prototype --> ObjMaker.prototype

obj2.c;
// returns 'third', 来自 SubObjMaker.prototype

obj2.b;
// returns 'second', 来自 ObjMaker.prototype

obj2.a;
// returns 'first', 来自 SubObjMaker.prototype, 因为 SubObjMaker.prototype 
// 是用 ObjMaker 函数创建的，它为我们分配了 a 属性
```

我在这个问题上读了很多浪费时间的文章，最后才找到[这篇](https://zeekat.nl/articles/constructors-considered-mildly-confusing.html)，这里有很多漂亮的图表可以更好的解释这个问题。

> 问题来源：[https://stackoverflow.com/questions/1646698/what-is-the-new-keyword-in-javascript](https://stackoverflow.com/questions/1646698/what-is-the-new-keyword-in-javascript)

