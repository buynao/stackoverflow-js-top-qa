## 问题：TypeScript是什么，为什么要用它来代替JavaScript？

我想知道 `TypeScript` 能做到哪些，而 `JavaScript` 或现有库所做不到？

## 答案

### TypeScript 和 JavaScript的关系

> TypeScript 是 JavaScript 的超集，它是一种类型化的语言，可以编译成普通的JavaScript - [typescriptlang.org](https://www.typescriptlang.org/)。

JavaScript 是一种编程语言，由 [ECMA's Technical Committee 39](https://tc39.es/) 所定义和维护，该委员会是由许多不同的利益相关者组成的团体。TC39 是一个由 [ECMA]((https://www.ecma-international.org/)) 主持的委员会：一个内部标准组织。JavaScript 有很多种实现，来自不同的运营商（如 Google, Microsoft, Oracle 等）。JavaScript 的目标是成为 Web 网络开发的通用语言。

`TypeScript` 是 JavaScript 的一个超集，也是一种开源的编译器，主要由一个供应商：微软 所开发。`TypeScript` 的目标是通过它的 `类型系统` 帮助开发者能够尽早发现错误，从而使 JavaScript 开发更有效率。

基本上，`TypeScript` 通过三种方式实现其目标：

1. **支持更多现代 JavaScript 特性** - JavaScript 语言的实现是通过 [ECMAScript](https://en.wikipedia.org/wiki/ECMAScript) 的规范来进行标准化实现的。然而并非所有的浏览器和 `JavaScript运行时` 都支持 [ECMAScript](https://en.wikipedia.org/wiki/ECMAScript) 规范下的所有功能（在此[查看esnext的浏览器兼容实现](https://kangax.github.io/compat-table/esnext/)）。`TypeScript` 可以使用很多最新的 `ECMAScript 特性`，并将它们编译成你所选择的旧的 `ECMAScript` 版本实现（参考 `--target` 选项，查看编译器选项的 [编译版本](https://www.typescriptlang.org/tsconfig/#target) ）。这意味着你可以安全地使用新的功能，如 module、lambda function、class、解构等...，同时保持向后兼容旧的浏览器和 `JavaScript运行时`。
2. **进阶的类型系统** - `ECMAScript规范` 并不包含类型支持，由于JavaScript 的语言性质，它是一门解释性语言，并不是编译性语言，所以类型支持可能永远不会成为标准的一部分。`TypeScript` 的类型系统非常丰富，包括：接口、枚举、混合类型、泛型、联合/交叉类型、访问修饰符等等。`TypeScript` 的[官方网站](https://www.typescriptlang.org/) 对这些功能进行了概述。`TypeScript` 的类型系统与其他类型语言差不多，在某些情况下甚至可以更加强大。
3. **开发者工具支持** - `TypeScript` 的编译器可以在后台进程中运行，以支持增量编译和 IDE 集成，这样就可以更容易地浏览、发现问题、检查可运行性以及快速重构。

### TypeScript与其他编译JavaScript目标版本语言的关系

`TypeScript` 有一个独特的理念，相比于其他编译 JavaScript 的语言：
`TypeScript` 是 JavaScript 的一个超集，所以，JavaScript 代码也是有效的 `TypeScript` 代码；

你完全可以将你的 `.js` 文件重命名为 `.ts` 文件，然后直接开始使用 `TypeScript`（见下面的 [与JavaScript的可互操作性](https://github.com/buynao/stackoverflow-js-top-qa/blob/main/contents/typescript/index.md)）进行开发。`TypeScript` 文件可以被编译成 可读的 JavaScript，所以迁移回来也是可以的，理解编译后的 `TypeScript` 一点也不难。`TypeScript` 建立在 JavaScript 的成功基础上，同时改进了它的弱点。

一方面，你有面向未来的工具，它能够采用现代化的 `ECMAScript 标准`，并将其编译为旧的 JavaScript 版本实现，`Babel` 是最受欢迎的工具。另一方面，你有一些可能与 JavaScript 完全不同的语言，它们也为旧的 JavaScript 版本实现为目标，比如CoffeeScript、Clojure、Dart、Elm、Haxe、Scala.js，以及很多其他语言（见这个[列表](https://github.com/jashkenas/coffeescript/wiki/List-of-languages-that-compile-to-JS)）。这些语言，尽管它们可能有比JavaScript 的更好的未来，但使用它们也面临着更大的风险，即没有足够的应用程序使用它们，来保证其未来的发展。你也很难找到有经验的开发人员，能够搞得定这些语言。与 JavaScript 的互操作性也会更复杂，因为它们与 JavaScript 有着本质的区别。

`TypeScript` 位于这两种极端情况之间，从而平衡了风险。从任何情况来看，`TypeScript` 都不是一个有风险的选择。如果你熟悉 JavaScript，它只需要花很少的精力就能适应它，因为它不是一种完全不同的语言，有很好的 JavaScript 互操作性支持，而且它目前已经在业界被大量的使用了。

### 可选的静态类型和类型推断

JavaScript 是一种动态类型的预言。在运行代码之前，JavaScript 可能并不知道一个变量最终会是什么类型。`TypeScript` 为 JavaScript 添加了类型支持，并在编译到 JavaScript 的过程中捕捉其类型错误。如果你想把牌打的漂亮些（你是否严格定义你的类型或者是否在代码中添加这些类型，都取决于你），那么那些因为错误地假设某些变量是某种类型，而造成的错误就可以完全被根除。

`TypeScript` 通过类型推断，可以使类型定义变得更简单。

例如：

在 `TypeScript` 中，`var x = "hello"` 与 `var x : string = "hello"` 是一个意思。类型可以从它的使用中直接推断出来。即使你没有明确的定义类型。类型依然存在着，并可以避免一些导致运行时的错误。

`TypeScript` 也可以默认选择类型。

例如，函数 `divideByTwo(x) { return x / 2 }` 在 `TypeScript` 中是一个有效的函数，可以用任何类型的参数进行调用，尽管用字符串调用它显然会导致运行时错误。

就像你在 JavaScript 中习惯的那样。当没有明确指定类型，并且不能推断出类型时，`TypeScript` 将隐式地指定类型为 `any` 。这意味着`divideByTwo` 函数的类型定义自动的变成 `divideByTwo(x : any) : any` 。

有一个编译器标志可以禁止这种行为：`--noImplicitAny` 。启用这个标志可以给你更高程度的安全，但也意味着你要做更多的检查，类型定义等工作。

类型定义有一些成本。首先，要花一定的时间学习，有一个学习曲线。其次，使用严格类型定义来编写代码也会花费更多的时间。根据我的经验，在你与他人共享的比较重要的代码库时，这些成本是完全值得的。Github 中的一项 [关于编程语言和代码质量的大规模研究表明](https://cs.uwaterloo.ca/~m2nagapp/courses/CS846/1171/papers/ray_fse14.pdf) ，"一般来说，静态类型的语言比动态类型的语言更不容易出现缺陷，同样的，强类型语言也比弱类型语言更加稳定"。

有趣的是，这篇论文还发现，`TypeScript` 的错误率比 JavaScript 要低：

> 对于那些有正系数的语言，我们可以预期，该语言与更多的缺陷修复有关。这些语言包括 C、C++、JavaScript、Objective-C、Php和Python。
> Clojure、Haskell、Ruby、Scala和TypeScript等语言的系数均为负数，意味着这些语言产生缺陷修复提交的可能性低于平均值。

### 更友好的IDE支持

使用 `TypeScript` 的开发体验相比 JavaScript，有很大的提高。

IDE 通过 `TypeScript 编译器` 实时了解其丰富的类型信息。这带来了一些主要的优势。例如，使用 `TypeScript`，你可以在整个代码库中安全地进行重构，如重命名。通过代码完成，你可以获得库中可能提供的任何功能的内联帮助。不再需要记住它们或在在线参考中查找它们。在你忙于编码的时候，编译错误会直接在IDE中以红色的斜线报告。总而言之，与使用 JavaScript 的开发相比，生产力有了显著的提高。我们可以花更多的时间编码，更少的时间进行调试。

### 严格模式的无效检查

`cannot read property 'x' of undefined` 或 `undefined is not a function`，这样的运行时错误通常是由错误的 JavaScript 代码引起的。

开箱即用的 `TypeScript` 降低了这些错误发生的概率，因为人们不能使用`TypeScript` 编译器不知道的变量（ `any` 的变量的属性除外）。但仍有可能误用一个被赋值成 `undefined` 的变量。然而，在 `TypeScript` 的2.0版本中，你可以通过使用 `non-nullable` 的类型来消除这些错误。其工作原理如下。

启用了严格的空值检查（`--strictNullChecks` 编译器标志），`TypeScript` 编译器不允许将 `undefined` 赋值给某个变量，除非你明确声明它是可空的类型。例如，`let x : number = undefined` 将导致一个编译错误。这完全符合类型理论，因为 `undefined` 不是一个 `number`。我们可以将 `x` 定义为 `number` 和 `undefined` 的联合类型来纠正这个问题：`let x : number | undefined = undefined`。

一旦一个类型被确定为 `nullable` ，意味着它的类型可以是 `null` 或`undefined` 的值。`TypeScript` 编译器可以通过基于控制流的类型分析来确定你的代码是否可以安全地使用一个变量。换句话说，当你通过 `if` 语句检查一个变量是 `undefined`，`TypeScript` 编译器将推断出你的代码控制流的那个分支中的类型不再是 `nullable`，因此可以安全使用。下面是一个简单的例子。

```js
let x: number | undefined;
if (x !== undefined) x += 1; // 这一行可以被编译成功，因为x的类型被确定了。

x += 1; // 这一行将无法编译，因为x可能是undefined。
```

...todo

### 编译

使用 `TypeScript`，你需要一个构建过程来将其编译成 `JavaScript`。构建过程一般只需要几秒钟，当然这取决于你项目的大小。`TypeScript` 编译器支持增量编译（`--watch` 编译器标志），这样所有后续的变化都可以以更快的速度编译。

`TypeScript` 编译器可以在生成的 `.js` 文件中内联 `Source map` 或创建单独的 `.map` 文件。`Source map` 可以被调试工具使用，比如 `Chrome DevTools` 和其他 IDE。

`Source map` 将 JavaScript 中的行与 TypeScript 中生成的行联系起来。

这使得你可以在运行期间直接在 `TypeScript` 代码上设置断点和检查变量。`Source map` 早在 `TypeScript` 之前就存在了，但调试 `TypeScript` 通常没有直接调试 JavaScript 时那么优化。

以 `this` 关键字为例。自 `ES2015` 以来，围绕闭包的 `this` 关键字的语义发生了变化，这可能在运行时实际存在一个名为 `_this` 的变量（见此[答案](https://stackoverflow.com/questions/30056593/visual-studio-shows-wrong-value-for-this-in-typescript/30070859#30070859)）。在调试过程中，这可能会让你你感到困惑，但如果你知道这了一点或去检查下 JavaScript 代码，就不会有什么问题了。需要注意的是，Babel 也有完全相同的问题。

`TypeScript` 编译器还能做一些其他的小技巧，比如基于 [装饰器 decorators](https://stackoverflow.com/questions/29775830/how-to-implement-a-typescript-decorator) 生成拦截代码，为不同的模块系统生成模块加载代码，以及解析 JSX。

然而，除了 `Typescript` 编译器之外，你可能还需要其他构建工具。例如，如果你想压缩你的代码，你将不得不在构建过程中添加其他工具来实现。

在 Webpack、Gulp、Grunt 和几乎所有其他的 JavaScript `构建工具中都有TypeScript` 编译插件。`TypeScript` 文档中有一个关于 [与构建工具集成](https://www.typescriptlang.org/docs/handbook/integrating-with-build-tools.html) 的部分，涵盖了所有这些工具。

如果你需要更多构建检查，也可以使用 [linter](https://www.npmjs.com/package/tslint)。还有大量的种子项目，可以让你开始使用 TypeScript 与其他技术的结合，如 Angular 2、React、Ember、SystemJS、Webpack、Gulp等。

### 与JavaScript的互操作性

由于 `TypeScript` 与 JavaScript 密切相关，它们之间有很好的互操作能力，但要在 `TypeScript` 中与 JavaScript 库一起混合工作，还需要一些额外工作。

`TypeScript` 的声明定义有时也是需要的，这样 `TypeScript` 编译器就能正常理解像 `_.groupBy` 或 `angular.copy` 或 `$.fadeOut` ，实际上这些并不是非法语句。 这样的函数定义可以放在 `.d.ts` 文件中。

定义类型的最简单形式是允许一个标识符以任何方式被使用。

例如，当使用 `Lodash` 时，一个单行定义文件声明 `var _ : any` 将允许你在`_` 上调用任何你想要的函数，但是，你也可能偶尔会犯错误。`_.foobar()` 是一个合法的 `TypeScript` 调用，但在运行时就是一个非法调用了。如果你想要正确的类型支持和代码完成，你的定义文件需要更加精确（见 [lodash定义](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/lodash) 的例子）。

如果加载的 `Npm Package` 有自己的声明文件，会被 `TypeScript` 编译器自动理解。对于一些没有声明文件定义的不太流行的 JavaScript 库，有人通过另一个 npm 模块提供了声明文件。这些模块的前缀是 `@types/` ，来自一个叫做[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped#how-do-i-get-them) 的 `Github` 资源库。

注意事项：声明文件必须符合你使用的库的版本保持一致。

如果不这样做，`TypeScript` 可能不允许你调用一个函数或解除引用一个存在的变量，或者允许你调用一个函数或解除引用一个不存在的变量...仅仅是因为类型在编译时与运行时的版本不匹配。所以要确保你为你所使用的库的正确版本加载正确的声明文件。

说实话，这有一点麻烦，这可能是你不选择 `TypeScript` 的原因之一，而是去选择像 `Babel` 这样的东西，因为它根本不需要定义类型。另一方面，如果你知道你在做什么，你可以很容易地克服由不正确或丢失定义文件引起的任何类型的问题。

## 最后

使用 `Typescript` 最大的障碍是有一个学习曲线。最好能够一开始就用一个小项目来练习。看看它是如何工作的，如何构建，使用哪些文件，如何配置，在你的IDE中如何运作，它的结构如何，使用哪些工具，等等。

只要确保你在切换 `TypeScript` 之前对它已经能够掌握，将大型 JavaScript 代码库转换为 `TypeScript` 是可以做到的。[阅读这个博客](https://www.lucidchart.com/techblog/2017/11/16/converting-600k-lines-to-typescript-in-72-hours/)，了解在 72 小时内将 60 万行转换为 TypeScript 的例子）。

## 其他参考

[译者在公司内部分享：应用TS的成本收益分析 PPT](https://github.com/buynao/notes/blob/master/typesript/xx%E5%BA%94%E7%94%A8TS%E7%9A%84%E6%88%90%E6%9C%AC%E6%94%B6%E7%9B%8A%E5%88%86%E6%9E%90.pdf)

[Why you should use TypeScript for your next project](https://blog.priceandcost.com/development/why-you-should-use-typescript-for-your-next-project/)

> 问题来源：[https://stackoverflow.com/questions/12694530/what-is-typescript-and-why-would-i-use-it-in-place-of-javascript/](https://stackoverflow.com/questions/12694530/what-is-typescript-and-why-would-i-use-it-in-place-of-javascript/)
