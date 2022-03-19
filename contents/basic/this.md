## 问题： this 的运行原理

我看到过很多关于 `this` 的一些非常奇怪的行为，但却不明白为什么会发生这种情况。

`this` 的运行原理是怎样的，应该怎么使用它？

## 答案

`this` 是 JavaScript 中 `执行环境` 里的一个属性。主要用途通常是在函数和构造函数中。

### ECMA规范中对 this 的技术描述

[ECMAScript标准](https://tc39.es/ecma262/) 通过 [ResolveThisBinding](https://tc39.es/ecma262/#sec-resolvethisbinding) AO（可以理解成是一种抽象方法，缩写为AO） 来定义 [this](https://tc39.es/ecma262/#sec-this-keyword) ：

```text
ResolveThisBinding 不需要任何参数。它使用运行中的执行环境的 [词法环境 LexicalEnvironment] 来完成 this 的绑定。在被调用时执行以下步骤。

1. Let envRec be GetThisEnvironment().
2. Return ? envRec.GetThisBinding().
```

[全局环境记录 Global Environment Records](https://tc39.es/ecma262/#sec-global-environment-records)、[模块环境记录 Module Environment Records](https://tc39.es/ecma262/#sec-module-environment-records) 和 [函数环境记录 Function Environment Records](https://tc39.es/ecma262/#sec-function-environment-records) 都有自己的 `GetThisBinding` 方法。

通过 `GetThisEnvironment` AO 找到当前执行环境中的 `词法环境 LexicalEnvironment`，并找到最接近的具有此绑定（即 `HasThisBinding` 返回 true ）的环境记录（通过迭代访问其 `[[OuterEnv]]` 属性）。然后会产生上述 `三种环境类型` 中的一种环境记录。

`this` 往往也取决于代码环境是否处于 `严格模式（strict mode）` 中。

`GetThisBinding` 的返回值就是当前执行上下文的 `this`，所以每当建立一个新的执行上下文时，`this` 就会解析为一个不同的值。当当前执行上下文被修改时也会发生这种情况。

下面的例子列出了可能发生这种情况的五种场景：

### 1. script 中的执行上下文

例如，这是在最顶层执行的代码，直接在 `<script>` 内。

```html
<script>
// Global context
console.log(this); // Logs global object.

setTimeout(function(){
  console.log("Not global context");
});
</script>
```

当脚本代码在全局环境中执行时， `GetThisBinding` 的步骤如下：

```text
The GetThisBinding concrete method of a global Environment Record envRec […] [does this]:

Return envRec.[[GlobalThisValue]].
```

`全局环境记录` 的 `[[GlobalThisValue]]` 属性始终被设置为当前环境的全局对象，可以直接通过 `globalThis`（Web 上的 `window`，Node.js 上的 `global`；[MDN上的文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/globalThis) ）进行访问，也就是指向全局变量。

你可以访问 [InitializeHostDefinedRealm](https://tc39.es/ecma262/#sec-initializehostdefinedrealm) 进一步了解 `[[GlobalThisValue]]` 的属性是如何形成的。

### 2. module 中的执行上下文

`module` 在 `ECMAScript 2015` 中被引入。

例子：直接在 `<script type="module">` 里面执行的代码。

当 `module` 在全局环境中执行代码时，`GetThisBinding` 的步骤如下：

```text
The GetThisBinding concrete method of a module Environment Record […] [does this]:

Return undefined.
```

因此在 `module` 中，`this` 在全局范围内始终是 `undefined`。`module` 默认是开启严格模式的。

### 3.  eval 中的执行上下文

从 `ECMAScript 第五版` 的时候，`eval` 就有两种调用方式：[直接调用](https://tc39.es/ecma262/#sec-function-calls-runtime-semantics-evaluation) 和 [间接调用](https://tc39.es/ecma262/#sec-eval-x)。

- 直接调用的 `eval` 通常看起来像 `eval(...);` 或者 `(eval)(...);`，只有在调用表达符合狭义模式的情况下才是 `直接` 的。
- 间接调用的 `eval` 通常是以调用函数引用 `eval` 来完成的，比如：`eval?.(…)` , `(…, eval)(…)` , `window.eval(…)` , `eval.call(…,…)` 等...

通过 [performeval](https://tc39.es/ecma262/#sec-performeval) AO 执行 `eval` 代码时，会创建一个新的 [声明性环境记录](https://tc39.es/ecma262/#sec-declarative-environment-records) 作为其 `词法环境 LexicalEnvironment`，前边说过，这是通过 `GetThisEnvironment` AO 获取 `this` 值的地方。

如果 `this` 出现在 `eval` 代码中，先通过 `GetThisEnvironment` AO 找到其环境记录，在直接调用 `GetThisBinding` AO 并返回其值。

刚刚所说的 `声明性环境记录` 来源哪里，是取决于 `eval` 的调用方式是 `直接` 还是 `间接`。

- 在 `直接` 调用中，它将基于当前运行的执行环境中的 `词法环境 LexicalEnvironment`。
- 在 `间接` 调用中，它将基于执行当前环境记录中的 `[[GlobalEnv]]` 属性（一个全局环境记录）。

翻译一下，也就是说：

- 在 `直接` 调用中，`this` 值不会改变；它就来自当前执行的词法环境中。
- 在 `间接` 调用中，`this` 值就是全局对象。

那么 `new Function` 呢？- `new Function` 与 `eval` 类似，但它并不立即调用代码，而是创建一个构造函数。在构造函数中的任何地方都不适用 `this` 的绑定，但是当这个函数被调用时，`this` 才能够发挥作用，下一小节会继续进行相关解释

### 4.  function 中的执行上下文

在调用一个函数时，我们将进入 function 执行上下文，目前有四种语法可以调用一个函数。

- [EvaluateCall](https://tc39.es/ecma262/#sec-evaluatecall) AO 是针对这三种情况进行的：
  - 正常的函数调用（[Normal function calls](https://tc39.es/ecma262/#sec-function-calls-runtime-semantics-evaluation)）
  - 可选的链式调用（[Optional chaining calls](https://tc39.es/ecma262/#sec-optional-chaining-evaluation)）
  - 标签模板（[Tagged templates](https://tc39.es/ecma262/#sec-tagged-templates-runtime-semantics-evaluation)）
- [EvaluateNew](https://tc39.es/ecma262/#sec-evaluatenew) AO 只为一种情况进行：
  - 构造函数的调用（[Constructor invocations](https://tc39.es/ecma262/#sec-new-operator-runtime-semantics-evaluation)）

实际的函数调用发生在 [Call](https://tc39.es/ecma262/#sec-call) AO 中，它由当前执行上下文中已经确定了的 `thisValue` 来进行调用；函数参数会在一长串与调用相关的调用中被传递。

当 [调用](https://tc39.es/ecma262/#sec-call) 函数的内部槽 `[[Call]]` ，就会调用 [PrepareForOrdinaryCall](https://tc39.es/ecma262/#sec-prepareforordinarycall)，最后会创建一个新的 [函数环境记录](https://tc39.es/ecma262/#sec-function-environment-records)。

```text
`函数环境记录` 是一个声明性的环境记录，用来表示一个函数的顶层作用域，如果该函数不是 ArrowFunction 函数，则提供 this 绑定。

如果它的内部引用了 super，那么它的 `函数环境记录` 也包含了用于从函数中执行 super 方法后的环境状态。
```

此外，在一个 `函数环境记录` 中包含 `[[ThisValue]]` 字段。

```text
这是在调用函数时使用的 this 值。
```

`NewFunctionEnvironment` 的调用也设置了函数环境的 `[[ThisBindingStatus]]` 属性。

`[[Call]]` 调用 `OrdinaryCallBindThis`，其中 `thisArgument` 是根据以下情况来确定的：

- 原始调用/引用
- 函数的种类
- 代码是否处于严格模式。

一旦确定，开始调用函数环境记录里的 `BindThisValue` 方法，将 `[[ThisValue]]` 字段设置为 `thisArgument`。最终，通过 `函数环境记录` 的 `GetThisBinding AO` 可以直接获得 `this` 值：

```text
The GetThisBinding concrete method of a function Environment Record envRec […] [does this]:

[…]
1. Return envRec.[[ThisValue]].
```

`this` 究竟是如何确定的，取决于许多因素，上述只是一个总体概述。

有了上边的技术背景介绍，方便我们接下来研究更多具体例子：

#### Arrow functions

当一个 [箭头函数](https://tc39.es/ecma262/#sec-runtime-semantics-instantiatearrowfunctionexpression) 被执行时，函数对象的 `[[ThisMode]]` 内部槽在 [OrdinaryFunctionCreate](https://tc39.es/ecma262/#sec-ordinaryfunctioncreate) 中被设置为 `词法`。

在 [OrdinaryFunctionCreate](https://tc39.es/ecma262/#sec-ordinaryfunctioncreate)，它需要一个函数 F 来作为参数。

```text
1. Let thisMode be F.[[ThisMode]].
2. If thisMode is lexical, return NormalCompletion(undefined). […]
```

这意味着箭头函数不绑定自己的 `this` 值，其内部跳过了绑定 `this` 的算法部分。

那么，在一个箭头函数里面，`this` 又是从哪里来的呢？回头看看 [ResolveThisBinding](https://tc39.es/ecma262/#sec-resolvethisbinding) 和 [GetThisEnvironment](https://tc39.es/ecma262/#sec-getthisenvironment), [HasThisBinding 方法明确地返回 false](https://tc39.es/ecma262/#sec-function-environment-records-hasthisbinding)。

```text
The HasThisBinding concrete method of a function Environment Record envRec […] [does this]:

If envRec.[[ThisBindingStatus]] is lexical, return false; otherwise, return true.
```

所以，外部环境会被迭代进行查询。该过程将在三个绑定的环境中，直到找到一个有 this值绑定的环境而结束。

这只是意味着，**在箭头函数体中**，`this` 来自箭头函数的 `词法范围`。
[更多可参考这个问题](https://stackoverflow.com/questions/34361379/are-arrow-functions-and-functions-equivalent-interchangeable/34361380#34361380)

```text
箭头函数没有自己的 this [...] 绑定。相反，[this 标识符] 像其他变量一样在词法范围内被解析。这意味着在一个箭头函数中，this [指]的是箭头函数所定义的环境中的 [this的值]（即箭头函数的 "外部"）。
```

#### Function properties

在正常的函数（函数、方法）中，`this` 是由函数的调用方式决定的。`this` 就是
这些 "语法变体" 派上用场的地方。

这里有一个对象包裹着一个函数，函数是它的一个属性

```js
const refObj = {
  func: function(){
    console.log(this);
  }
};

//or 

const refObj = {
  func(){
    console.log(this);
  }
};
```

在以下任何一个函数调用中，`func` 里面的 `this` 值都将为 `refObj`

```js
refObj.func()
refObj["func"]()
refObj?.func()
refObj.func?.()
refObj.func``
```

如果被调用的函数是一个对象的属性，那么这个对象将是调用函数的 "引用"，在通常情况下，它就是 `this` 的值。

这可以通过上面链接的评估步骤来解释；例如，在 `refObj.func()` （或 `refObj["func"]()` ）中，[CallMemberExpression](https://tc39.es/ecma262/#prod-CallMemberExpression) 是整个表达式 `refObj.func()` ，它由 [MemberExpression](https://tc39.es/ecma262/#prod-MemberExpression) `refObj.func` 和 [Arguments（）](https://tc39.es/ecma262/#prod-Arguments) 组成。

但是，`refObj.func` 和 `refObj` 也分别还有三个角色。

- 它们是表达式
- 它们都有一个引用
- 它们也都是一个值

`refObj.func` 作为一个值是可调用的函数对象；相应的引用被用来确定 `this` 绑定。

可选链和标记模板的例子工作起来非常相似：基本上，引用就是在 `?()、`` 或 ()` 之前的一切。

[EvaluateCall](https://tc39.es/ecma262/#sec-evaluatecall) 使用该引用的 [IsPropertyReference](https://tc39.es/ecma262/#sec-ispropertyreference) 来确定它是否是一个对象的属性，在语法上。它试图获得该引用的 `[[Base]]` 属性（例如，当应用于 `refObj.func` 时；或者 `foo.bar`，当应用于 `foo.bar.baz` 时，他们的 [[Base]] 属性就是 `refObj` ）。如果它被写成一个属性，那么 [GetThisValue](https://tc39.es/ecma262/#sec-getthisvalue) 将获得这个 `[[Base]]` 属性并将其作为 `this` 值。

注意：关于 `this`，[Getters / Setters](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/get) 的工作方式与方法相同。简单的属性不影响执行环境，例如，在这里，this 是在全局范围。

```js
const o = {
    a: 1,
    b: this.a, // Is `globalThis.a`.
    [this.a]: 2 // Refers to `globalThis.a`.
  };
```

#### 无基础引用的调用，严格模式，以及 with

没有基础引用的调用通常是指没有作为属性调用的函数。比如说。

```js
func(); // As opposed to `refObj.func();`.
```

当 [传递或分配方法](https://stackoverflow.com/q/30486345)，或使用[逗号运算符](https://stackoverflow.com/q/35522560) 时，也会发生这种情况。这就是引用调用和值调用的区别所在。

注意函数 `j` ：按照规范，你会注意到 `j` 只能返回函数对象（Value）本身，而不是引用记录。引用的对象 `refObj` 丢失了。

```js
const g = (f) => f(); // No base ref.
const h = refObj.func;
const j = () => refObj.func;

g(refObj.func);
h(); // No base ref.
j()(); // No base ref.
```

[EvaluateCall](https://tc39.es/ecma262/#sec-evaluatecall) 调用 [Call](https://tc39.es/ecma262/#sec-call)，这里的 [thisValue]() 为 **undefined**。这在 [OrdinaryCallBindThis](https://tc39.es/ecma262/#sec-ordinarycallbindthis) 是有区别的（F：函数对象；thisArgument：传递给 [Call](https://tc39.es/ecma262/#sec-call) 的 thisValue）


1. Let thisMode be F.[[ThisMode]].

[…]

5. If thisMode is strict, let thisValue be thisArgument.
   
6. Else,
   1. If thisArgument is undefined or null, then
      1. Let globalEnv be calleeRealm.[[GlobalEnv]].
      2. […]
      3. Let thisValue be globalEnv.[[GlobalThisValue]].
     1. Else,
         1. Let thisValue be ! ToObject(thisArgument).
         2. NOTE: ToObject produces wrapper objects […].
[…]


注意：步骤 5 将 `this` 的实际值设置为严格模式下提供的 `thisArgument` -- **undefined**。在 "宽松模式 "下，一个 undefined 或 null 的`thisArgument` 会导致 `this` 成为全局的 `this` 值。

如果 [IsPropertyReference](https://tc39.es/ecma262/#sec-ispropertyreference) 返回 `false`，那么 [EvaluateCall](https://tc39.es/ecma262/#sec-evaluatecall) 会采取以下步骤：

1. Let refEnv be ref.[[Base]].
2. Assert: refEnv is an Environment Record.
3. Let thisValue be refEnv.WithBaseObject().

这就是 undefined 的 `thisValue` 可能来自的地方：除了在 with 语句中,[WithBaseObject()](https://tc39.es/ecma262/#sec-object-environment-records-withbaseobject) 总是 **undefined**。在这种情况下，`thisValue` 将是绑定对象。

总结一下，到目前为止。

```js
function f1(){
  console.log(this);
}

function f2(){
  console.log(this);
}

function f3(){
  console.log(this);
}

const o = {
    f1,
    f2,
    [Symbol.unscopables]: {
      f2: true
    }
  };

f1(); // Logs `globalThis`.

with(o){
  f1(); // Logs `o`.
  f2(); // `f2` is unscopable, so this logs `globalThis`.
  f3(); // `f3` is not on `o`, so this logs `globalThis`.
}
```

和

```js
"use strict";

function f(){
  console.log(this);
}

f(); // Logs `undefined`.

// `with` statements are not allowed in strict-mode code.
```

注意，在检查 `this` 时，函数是在哪里定义的并不重要。

#### .call, .apply, .bind, thisArg, and primitives

[OrdinaryCallBindThis](https://tc39.es/ecma262/#sec-ordinarycallbindthis) 的第 5 步与第 6.2 步，结合起来可以推断，只有在 "宽松模式" 下，才会将一个原始的 `this` 值强制分配给一个对象。

为了研究这个问题，让我们介绍一下 `this` 值的另一个来源：

重写 `this` 绑定的三个方法：

- Function.prototype.apply(thisArg, argArray)
- Function.prototype. {call, bind} (thisArg, ...args)

`.bind` 创建一个绑定 `this` 的函数，其 `this` 绑定被设置为 `thisArg`，并且不能再次改变。

`.call` 和 `.apply` 立即调用该函数，并将 `this` 绑定设置为 `thisArg`。

`.call` 和 `.apply` 直接映射到 [Call](https://tc39.es/ecma262/#sec-call)，使用指定的 `thisArg`。

`.bind` 用 `BoundFunctionCreate` 创建一个绑定函数。

以上执行的函数都有自己的 `[[Call]]` 方法，可以查找函数对象的 `[[BoundThis]]` 内部槽。

设置自定义 `this` 值的例子：

```js
function f(){
  console.log(this);
}

const myObj = {},
  g = f.bind(myObj),
  h = (m) => m();

// All of these log `myObj`.
g();
f.bind(myObj)();
f.call(myObj);
h(g);
```

对于对象，在严格和非严格模式下是一样的。

现在，试着提供一个原始值 (primitives)。

```js
function f(){
  console.log(this);
}

const myString = "s",

  g = f.bind(myString);

g();              // Logs `String { "s" }`.
f.call(myString); // Logs `String { "s" }`.
```

在非严格模式下，原始值 (primitives)被强制为其对象包裹的形式。

这与你调用 `Object("s")` 或 `new String("s")` 时得到的对象种类相同。

在严格模式下，你可以使用原始值 (primitives)。

```js
"use strict";

function f(){
  console.log(this);
}

const myString = "s",
  g = f.bind(myString);

g();              // Logs `"s"`.
f.call(myString); // Logs `"s"`.
```

#### Constructors, classes, and new

当使用 `new` 操作符将一个函数作为构造函数调用时，[EvaluateNew](https://tc39.es/ecma262/#sec-evaluatenew) 会调用[Construct](https://tc39.es/ecma262/#sec-construct)，它会调用内部 `[[Construct]]` 方法。

如果该函数是一个基础构造函数（即不是一个类的扩展...{...}），它将 `thisArgument` 设置为一个由构造函数的原型创建的新对象。

在构造函数中设置的属性将最终出现在生成的实例对象上。

除非你明确地返回你自己的非原始值，否则 `this` 将隐式返回。

`class` 是一种创建构造函数的新方式，在 `ECMAScript 2015` 中引入。

```js
function Old(a){
  this.p = a;
}

const o = new Old(1);

console.log(o);  // Logs `Old { p: 1 }`.

class New{
  constructor(a){
    this.p = a;
  }
}

const n = new New(1);

console.log(n); // Logs `New { p: 1 }`.
```

严格模式下的 `class` 定义

```js
class A{
  m1(){
    return this;
  }
  m2(){
    const m1 = this.m1;
    
    console.log(m1());
  }
}

new A().m2(); // Logs `undefined`.
```

#### super

如上所述，`new` 行为的例外是 `class extends...{...}`。派生类在调用时不会立即设置它们的 `this `值；它们只有在通过一系列 `super` 调用到达基类时才会这样做。在调用 `super` 之前使用 `this` 是不允许的。

```js
class DerivedNew extends New{
  constructor(a, a2){
    // Using `this` before `super` results in a ReferenceError.
    super(a);
    this.p2 = a2;
  }
}

const n2 = new DerivedNew(1, 2);

console.log(n2); // Logs `DerivedNew { p: 1, p2: 2 }`.
```

### 5. 进入 class 执行上下文

实例字段和静态字段是在 `ECMAScript 2022` 中引入的。

当一个 class 将要执行时，[ClassDefinitionEvaluation](https://tc39.es/ecma262/#sec-runtime-semantics-classdefinitionevaluation) 被执行，修改[运行的执行环境](https://tc39.es/ecma262/#running-execution-context)。

对于每个[ClassElement](https://tc39.es/ecma262/#prod-ClassElement)：

- 如果一个字段是静态的，那么 this 指的是 class 本身。
- 如果一个字段不是静态的，那么 this 指的是实例。

私有字段（例如#x）和方法被添加到 [PrivateEnvironment]() 中。

[静态块](https://github.com/tc39/proposal-class-static-block) 目前是[TC39的第三阶段提案](https://tc39.es/process-document/) 。[静态块](https://github.com/tc39/proposal-class-static-block) 的工作原理与静态字段和方法相同：它们内部的 this 指的是类本身。

请注意，在方法和 `getters/setters` 中，this 与普通函数属性的工作原理相同。

```js
class Demo{
  a = this;
  b(){
    return this;
  }
  static c = this;
  static d(){
    return this;
  }
  // Getters, setters, private modifiers are also possible.
}

const demo = new Demo;

console.log(demo.a, demo.b()); // Both log `demo`.
console.log(Demo.c, Demo.d()); // Both log `Demo`.
```

> 译者注：本文含有大量 ECMA规范中的专业词汇，由于对ECMA规范没有仔细研读过，且规范文章相对比较抽象；本文翻译较为拙劣，建议有能力的同学，直接查看原文答案。

> 问题来源：[https://stackoverflow.com/questions/3127429/how-does-the-this-keyword-work](https://stackoverflow.com/questions/3127429/how-does-the-this-keyword-work)