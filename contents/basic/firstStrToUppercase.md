## 问题：JavaScript 中如使字符串的第一个字母大写？

如何让一个字符串的第一个字母大写，但不改变其他字母的大小写？

示例：

```

- "this is a test" → "This is a test"
- "the Eiffel Tower" → "The Eiffel Tower"
- "/index.html" → "/index.html"

```

## 答案

基本解决方案是：

```js

function capitalizeFirstLetter(string) {
  return string.charAt(0).toUpperCase() + string.slice(1);
}

console.log(capitalizeFirstLetter('foo')); // Foo

```

其他一些答案修改了 `String.prototype`（这个答案以前也是如此），但我现在建议不要这样做，因为可维护性问题（很难发现函数被添加到原型中的位置，如果其他代码使用相同的名称/浏览器将来添加相同名称的本地函数，可能会导致冲突）。

如果你想用 `Unicode` 代码点而不是代码单位来工作（例如处理基本多语言平面以外的 `Unicode` 字符），你可以利用 [String#[@iterator]](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/@@iterator)，使用 `toLocaleUpperCase` 来获得当前正确的
大写字母。

```js

const capitalizeFirstLetter = ([ first, ...rest ], locale = navigator.language) =>
  first.toLocaleUpperCase(locale) + rest.join('')

console.log(
  capitalizeFirstLetter('foo'), // Foo
  capitalizeFirstLetter("𐐶𐐲𐑌𐐼𐐲𐑉"), // "𐐎𐐲𐑌𐐼𐐲𐑉" (correct!)
  capitalizeFirstLetter("italya", 'tr') // İtalya" (correct in Turkish Latin!)
)

```

更多国际化方案选择，请参阅下面的其他答案。

## 其他答案

`Uppercase` 在指定脚本的情况下，每种语言中得到的结果并不意味着都是相同的。

大多数建议的功能如下所示：

```js

function capitalizeFirstLetter(str) {
  return str[0].toUpperCase() + str.slice(1);
}

```

但是，一些大小写字符不在 `BMP`（基本多语言平面，代码点 `U+0` 到 `U+FFFF`）之外。例如，以这个 `Deseret` 文本为例：

```js

capitalizeFirstLetter("𐐶𐐲𐑌𐐼𐐲𐑉"); // "𐐶𐐲𐑌𐐼𐐲𐑉"

```

这里的第一个字符没有大写，因为字符串的数组索引属性不访问 `characters` 或 `代码点**`。他们访问 `UTF-16` 代码单元。切片时也是如此——索引值指向代码单元。

```js

'𐐶𐐲𐑌𐐼𐐲𐑉'[0] // '\uD801'

```

碰巧 `UTF-16` 代码单位就是 `1:1`，`USV` 代码单位在两个范围内：`U+0` 到 `U+D7FF` 和 `U+E000` 到 `U+FFFF` 。大多数大小写字符都在这两个范围内，但并不是全部的。

从 `ES2015` 开始，处理这个变得更容易了。` String.prototype[@@iterator]` 方法返回一个新的`Iterator` 对象，它遍历字符串的代码点，返回每一个代码点的字符串值。

```js

function capitalizeFirstLetter([ first, ...rest ]) {
  console.log(first)
  return [ first.toUpperCase(), ...rest ].join('');
}

capitalizeFirstLetter("𐐶𐐲𐑌𐐼𐐲𐑉") // 𐐶  and '𐐎𐐲𐑌𐐼𐐲𐑉'

```

对于较长的字符串，这可能不是非常有效的。因为我们并不真的需要对剩余部分进行迭代。我们可以使用`String.prototype.codePointAt` 来获得第一个（可能的）字母，但是我们仍然需要确定切片应该从哪里开始。避免迭代余数的一个方法是测试第一个代码点是否在 `BMP` 之外；如果不是，分片从 1 开始，如果是，分片从 2 开始。

```js

function capitalizeFirstLetter(str) {
  const firstCP = str.codePointAt(0);
  const index = firstCP > 0xFFFF ? 2 : 1;

  return String.fromCodePoint(firstCP).toUpperCase() + str.slice(index);
}

capitalizeFirstLetter("𐐶𐐲𐑌𐐼𐐲𐑉") // "𐐎𐐲𐑌𐐼𐐲𐑉"

```

在开始的时候，我提到了国际化的考虑。其中有些是很难解释的，因为它们不仅需要了解使用的是什么语言，而且还可能需要了解该语言中的单词的具体知识。

例如，爱尔兰的数字符号 `mb` 在一个词的开头大写为 `mB`。

另一个例子，德语的 `eszett`，据说从来不会放在单词的开头。小写的 eszett `ß`，大写为 `SS`，但 `SS` 可以小写为 `ß` 或 `ss`。

你需要有德语的语言知识才能知道哪个才是正确的。

这类问题最著名的例子可能是土耳其语。在土耳其拉丁语中，`i` 的大写形式是 `İ`，而 `I` 的小写形式是 `ı`。

它们是两个不同的字母。幸运的是，我们确实有办法解决这个问题：

```js
function capitalizeFirstLetter([ first, ...rest ], locale) {
  return [ first.toLocaleUpperCase(locale), ...rest ].join('');
}

capitalizeFirstLetter("italy", "en") // "Italy"
capitalizeFirstLetter("italya", "tr") // "İtalya"
```

在浏览器中，用户最常用的语言标签由 `navigator.language` 标明。

`navigator.languages` 中则也可以找到按优先顺序排列的语言标签列表。

```js
navigator.language // 'zh-CN'
navigator.languages // ['zh-CN', 'zh', 'en']
```

在 `ES2018` 中引入的 `RegExp` 中支持 `Unicode` 属性字符类的代理中，我们可以通过直接表达我们感兴趣的字符来进一步优化代码：

```js
function capitalizeFirstLetter(str, locale=navigator.language) {
  return str.replace(/^\p{CWU}/u, char => char.toLocaleUpperCase(locale));
}
```

优化过后，现在就能够比较精准地处理字符串中多个单词的大写。`CWU` 或 `Changes_When_Uppercased` 字符属性与所有代码点相匹配，这些代码点在大写时将会发生变化。

我们可以用诸如荷兰语 `ĳ` 这样的大写数字字符来试试。

```js
capitalizeFirstLetter('ĳsselmeer'); // "Ĳsselmeer"
```

## 最后

在大多数情况下，问这个问题的人都不会关心 `Deseret` 的大写字母或国际化问题。但是，能搞提前意识到这些问题是很好的，因为你不知道什么时候就会遇到这些问题。它们并不是 `边缘` 案例，或者说，它们不是定义上的边缘案例，毕竟有一整个国家的大多数人都说土耳其语。

将代码单元与代码点混淆是一个相当常见的错误来源（特别是在表情符号方面），字符串和语言都是相当复杂的。

> 问题来源：[https://stackoverflow.com/questions/1026069/how-do-i-make-the-first-letter-of-a-string-uppercase-in-javascript/53930826](https://stackoverflow.com/questions/1026069/how-do-i-make-the-first-letter-of-a-string-uppercase-in-javascript/53930826)