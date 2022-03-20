## 问题：为什么 ◎ܫ◎ 和 ☺ 不是有效的 JavaScript 变量名？

我注意到，在 `Internet Explorer` 中（在我测试的其他浏览器中没有），你可以使用一些 `Unicode` 变量名。

这让我大开眼界，我很高兴，因为我可以写出像这样充满 `Unicode` 的有趣代码。

```js
var ктоείναι草泥马 = "You dirty horse.",
    happy☺n☺mat☺p☺eia = ":)Yay!",
    ಠ_ಠ = "emoticon";

alert(ктоείναι草泥马 + happy☺n☺mat☺p☺eia + ಠ_ಠ);
```

由于某些原因，◎ܫ◎、♨_♨ 和 ☺ 都不是有效的变量名。

为什么 `ಠ_ಠ` 和 `草泥马` 可以用，而 ◎ܫ◎、♨_♨ 和 ☺ 却不行？

## 答案

`ಠ_ಠ` 和 `草泥马` 其实只是实际的字母中使用的 `字符(letters)`；

也就是说，`ಠ` 是卡纳达字母表中的一个符号，而 `草泥马` 也是由汉字组成。

而，◎ 和 ☺ 是纯粹的符号；它们与任何字母都没有联系。

ECMAScript标准第7.6章（除Internet Explorer外，所有的浏览器都遵循这个标准）规定，标识符必须以下列之一开头。

- Unicode 字符
- `$` or `_`
- `\` 后面是一个 unicode 转义序列

识别符的以下字符必须是以下之一。

- 任何允许在开头的字符
- Unicode 组合标记
- Unicode 数字
- Unicode 连接的标点符号
- 零宽度的非连接符
- 零宽度的连接符

`IE` 超越了标准，允许使用一些符号，如 ☺ 。

有一个 [工具](https://mothereff.in/js-variables) 可以告诉你，你输入的字符串是否是一个有效的 `JavaScript` 变量名。


> 问题来源：[https://stackoverflow.com/questions/7451524/why-arent-%dc%ab-and-valid-javascript-variable-names](https://stackoverflow.com/questions/7451524/why-arent-%dc%ab-and-valid-javascript-variable-names)