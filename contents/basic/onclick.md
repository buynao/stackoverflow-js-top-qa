## 问题： 在 JavaScript 中 addEventListener 和 onclick 之间有什么区别？

下面的代码都能正常的执行，那么 `addEventListener` 和 `onclick` 之间有什么区别？

```js

var h = document.getElementById("a");
h.onclick = dothing1;
h.addEventListener("click", dothing2);

```

## 答案

这两种方法都是正确的，但它们本身都不是 "最好的"，而且开发选择使用这两种方法可能有不太原因。

### 事件监听器 (addEventListener and IE's attachEvent)

早期版本的 `Internet Explorer` 实现 `javascript` 的方式与其他浏览器都不太相同同。`IE9` 以下的版本，你可以使用`attachEvent` ，像这样：

```js
element.attachEvent('onclick', function() { /* do stuff here*/ });

```

在大多数其他浏览器中（包括 `IE 9` 及以上版本），你使用`addEventListener`，像这样：

```js
element.addEventListener('click', function() { /* do stuff here*/ }, false);
```

使用（[DOM Level 2 events](http://www.w3.org/wiki/Handling_events_with_JavaScript#The_evolution_of_events)）里的方法，你可以为任意的单一元素添加无限量的事件。唯一的限制可能就是你的浏览器内存和一些性能问题。

上面举的例子，使用了一个匿名函数。你也可以使用一个具名函数或一个闭包来添加一个事件监听器。

```js
var myFunctionReference = function() { /* do stuff here*/ }
element.attachEvent('onclick', myFunctionReference);
element.addEventListener('click', myFunctionReference , false);
```

`addEventListener` 的另一个重要特性是它的最后一个参数，最后的参数控制了监听器对冒泡事件的反应。

上述例子中，最终参数传递的都是 `false`，这也是目前 DOM 标准事件流的触发方式 - 事件冒泡。

而 `attachEvent`，或者使用内联事件时，则没有相应的参数来进行对监听器事件的控制。

### 内联事件（HTML onclick="" 属性和 element.onclick）。

在所有支持 `javascript` 的浏览器中，你可以通过内联的方式添加事件监听，也就是直接放在 HTML 代码中。

```js
<a id="testing" href="#" onclick="alert('did stuff inline');">Click me</a>
```

大多数有经验的开发者都会回避这种方法，虽然它简单而直接，而且确实能完成工作。

但是你不能在这里使用闭包或匿名函数，而且你对范围的控制也是有限的。

你提到的另一种方法,

```js
element.onclick = function () { /*do stuff here */ };

```

...相当于内联的 `javascript` ，只是你对范围有了更多的控制（因为你写的是脚本而不是 HTML ），也可以使用匿名函数、函数引用和/或闭包。

内联事件的显著缺点是，与上述的事件监听器（event listeners）不同，你只能分配一次内联事件。

内联事件是作为元素的属性 (attribute/property) 存在的，这意味着它可以被重写。

```js
var element = document.getElementById('testing');
element.onclick = function () { alert('did stuff #1'); };
element.onclick = function () { alert('did stuff #2'); };
```

...当你点击该元素时，你只会看到 `"Did stuff #2"`

第二个值覆盖了 `onclick` 属性的第一次的分配，并且也覆盖了原始的内联 `HTML onclick` 属性。请看这里：[http://jsfiddle.net/jpgah/。](http://jsfiddle.net/jpgah/。)

通常来说，不要使用内联事件。可能你有特定的使用场景，但如果你不能 100% 确定这种特殊场景存在，那么你就不应该使用内联事件。

### 现代 Javascript（Angular 和 React 的框架）

自从这个答案最初发布以来，像 `React` 这样的 `javascript` 框架已经变得非常流行。你会在 `React` 模板中看到这样的代码。

```js
<button onClick="doSomething()">Do Something</button>
```

这种写法看起来像是在写内联事件，但它并不是。

这种类型的写法，在框架中有着更复杂的处理，本质还是在底层使用了事件监听器。

我在上述写的关于事件的所有内容仍然适用于现在，你应该了解这些细节，起码能够帮你拨开一层关于事件的迷雾。但是如果你在现代 JS 框架中，写过这种内联代码，不要认为你在使用内联事件。

## 哪个是最好的？

这个问题是浏览器兼容性和必要性的问题。

你需要为一个元素附加一个以上的事件吗？现在不会，将来呢？

所以，`attachEvent` 和 `addEventListener` 是有必要的，如果不是，一个内联事件可能看起来就能解决这个问题，但你最好为未来做准备，开始使用事件监听器。

> 问题来源: [https://stackoverflow.com/questions/6348494/addeventlistener-vs-onclick](https://stackoverflow.com/questions/6348494/addeventlistener-vs-onclick)