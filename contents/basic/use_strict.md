## 问题：“use strict”在 JavaScript 中做了什么，背后的原因是什么？

最近，我运行一些 JavaScript 代码。 发现了以下错误:

```js

Problem at line 1 character 1: Missing "use strict" statement.

```

检查一番后，我发现是有人在之前的代码里引入了 `use strict`。而且当我添加一些声明语句时，错误就不再出现。

不幸的是，我搜索一些相关资料之后，并没有加深我对这个字符串语句的了解。当然，它肯定与浏览器如何解释 `JavaScript` 有关系，但我不知道具体会产生什么影响？

所以， `use strict` 到底什么 ? 它意味着什么，它现在是否仍然起着什么其他作用？

现代的浏览器会对这个字符串 `use strict` 有所反应，还是将来才会使用？

## 答案

### ES6 更新

在原生 ECMAScript 模块（带有 import 和 export 语句）内部 和 `ES6 class `  环境中运行代码时，严格模式始终处于启用状态且无法禁用。

### 原始回答

您可能会对这篇关于 `Javascript 严格模式` 的文章感兴趣：[John Resig - ECMAScript 5 Strict Mode, JSON, and More](https://johnresig.com/blog/ecmascript-5-strict-mode-json-and-more/)

引用一些有趣的部分：

```
严格模式是 ECMAScript 5 中的一项新功能，它允许您将程序或函数置于 `严格模式` 上下文中。这种严格的上下文环境会阻止一些引发异常的操作。
```

还有：

```

严格模式有几个方面的帮助：

- 捕捉一些常见的编码错误，抛出异常。
- 当采取相对 "不安全 "的操作时（如获得对全局对象的访问），它可以禁止或抛出错误。
- 禁用那些令人困惑或考虑不周的功能。

```

另外，你可以将 `"strict mode"` 应用于整个文件中...

或者仅将其用于特定的函数中。

```js

// 非严格模式

(function(){
  "use strict";

  // 严格模式下编写你的代码
})();

// 非严格模式

```

如果你必须混合旧代码和新代码，上面这种写法会有所帮助;-)

**另外：现在所有主流浏览器都支持严格模式。**

## 其他答案：

这是ECMAScript 5的一个新特性。[John Resig](https://johnresig.com/blog/ecmascript-5-strict-mode-json-and-more/) 对它做了一个很好的 [总结](https://johnresig.com/blog/ecmascript-5-strict-mode-json-and-more/)。

它只是一个字符串，你把它放在你的JavaScript文件中（在文件的顶部或在一个函数中），看起来像这样。

```js

"use strict";

```

现在把它放在你的代码中应该不会对当前的浏览器造成任何问题，因为它只是一个字符串。如果你的代码违反了 `pragma`，它可能会在将来给你的代码带来一些问题。

例如，如果你现在有 `foo = "bar "`, 而没有先定义 `foo`，你的代码就会开始报错......在我看来这其实是一件好事。

## 其他答案：

声明 `"use strict"; `；指示浏览器使用 `严格模式`，是一种更安全的 `JavaScript` 特性。

#### 一些特性列表（非详尽）

- 不允许使用全局变量。(捕捉丢失的 `var` 声明和变量名称中的错别字）。
- 在 `严格模式` 下，失败的赋值将抛出错误（`NaN = 5;`)
- 试图删除不可删除的属性将抛出错位（`delete Object.prototype`）
- 对象字面的所有属性名称必须是唯一的 (`var x = {x1: "1", x1: "2"}`)
- 函数参数名称必须是唯一的(`function sum (x, x) {...}`)
- 禁止使用八进制语法（`var x = 023；`有些开发者错误地认为前面的零不会改变数字。）
- 禁止使用 `with` 关键字
- `eval` 中不能引入新的变量
- 禁止删除普通名称（`delete x;`）
- 禁止以任何形式绑定或分配名称 `eval` 和 `arguments`
- 不以形式参数来别名参数对象的属性。(例如，在函数 `sum (a,b) { return arguments[0] + b;}` 中。这样做是因为 `arguments[0]` 被绑定到 `a`，以此类推。)
- `arguments.callee` 不再得到支持

---
> 问题来源：[https://stackoverflow.com/questions/1335851/what-does-use-strict-do-in-javascript-and-what-is-the-reasoning-behind-it](https://stackoverflow.com/questions/1335851/what-does-use-strict-do-in-javascript-and-what-is-the-reasoning-behind-it)
