## 问题：Promise 和 Observables 之间的区别

在 `Angular/RxJs` 中， `Promise` 和 `Observable` 的区别是什么？

## 答案

### Promise

`Promise` 擅长处理单一的事件，比如等待一个异步操作，监听它的完成或失败。

注意：目前有一些支持取消的 `Promise` 库，但到目前为止还没有官方的取消规范。

### Observable

`Observable` 就像 `Stream`，允许传递零或多个事件，每个事件的回调都会进行调用。

它也比 `Promise` 更灵活一些，因为它提供了比 `Promise` 更多的功能。

有了 `Observable` ，无论你想多少个事件都不重要，你可以使用相同的 `API` 处理不同的情况。

与 `Promise` 相比，`Observable` 还具有可取消的优势。

`Observable` 的订阅是允许取消的，当你不再需要服务器的 `HTTP` 请求或其他昂贵的异步操作的响应结果时，随时可以取消。

而 `Promise` 一旦执行，始终会调用成功或失败，即使你不再需要它提供的结果。

`Promise` 会立即启动，而 `Observable` 只有在你订阅它时才会启动，因为 `Observable` 是惰性的。

`Observable` 提供了类似数组的一些操作符：像 map、forEach、reduce..., 方便你对异步的结果进行处理。当然，也有一些更强大的 [RxJS操作符](https://rxjs-dev.firebaseapp.com/guide/operators)

因其惰性执行的特性，可以在观察器被订阅执行之前建立一连串的操作符，以进行更加声明式的编程方式。

> 问题来源：[https://stackoverflow.com/questions/37364973/what-is-the-difference-between-promises-and-observables](https://stackoverflow.com/questions/37364973/what-is-the-difference-between-promises-and-observables)