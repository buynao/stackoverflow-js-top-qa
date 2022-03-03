## 问题：React Context 和 React Redux 的区别是什么？应该怎么使用？

[React 16.3.0](https://reactjs.org/blog/2018/03/29/react-v-16-3.html) 正式发布了 `Context API` 功能。

那么现在，我应该什么时候使用 `React Context` 而不是 `React Redux` ？

## 答案

`Context API` 的主要设计目的，是可以很方便的将数据传递给深度嵌套的组件。

正如 `Mark Erikson` 在他的 [博客](https://blog.isquaredsoftware.com/2018/03/redux-not-dead-yet/) 中写的：

    如果你只是用 Redux 来避免传递 props，那么 context 可以取代 Redux -- 但是，你也许可能一开始就不需要 Redux。

    Context 没有给你任何像 Redux DevTools 那样的东西，没有追踪状态更新的能力，没有可以集中处理应用逻辑的中间件，以及一些其他 Redux 的强大特性。

`Redux` 的功能显然更强大，提供了大量 `Context API` 所不能提供的功能，也正如 @danAbramov 提到的那样：

    React Redux 也在内部使用了 context ，但它并没有在公共 API 中将它暴露出来。因此，通过 React Redux 使用 context 实际上要比你自己直接使用要安全得多，因为如果上下文发生了什么变化，更新代码的负担将由 React Redux 来承担，而不是你。

Redux 更新起内部实现，以遵守最新的Context API。

如果你只是你简单地使用 `Redux` 在组件之间传递数据，那么最新的 `Context API` 可以用于进行替代。然而，如果需要集中数据或者使用 `redux-thunk` 或 `redux-saga` 的 `Action creators` 处理 API 请求的应用程序，可能仍然需要 `Redux`。

除此之外，`Redux` 还有其他相关的库，比如 `redux-persist`，它允许你在 `localStorage` 中保存/读取数据，并在刷新时重新填充你的 `store`，这也是 `Context API` 所不支持的。

@dan_abramov 在他的文章中 [你可能不需要Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367) 中提到 `Redux` 还有很多其他有用的应用，比如

- [开箱即用，从启动后将状态持久化到本地存储](https://egghead.io/lessons/javascript-redux-persisting-the-state-to-the-local-storage?course=building-react-applications-with-idiomatic-redux)。
- [开箱即用的ssr，在服务器上预先填充状态，以HTML的形式发送到客户端](https://redux.js.org/usage/server-rendering)
- [将用户操作序列化，并将其与状态快照一起附加到自动错误报告中，以便开发人员可以重放它们，方便重现bug。](https://github.com/dtschust/redux-bug-reporter)
- [通过网络传递行动对象来实现协作环境，不需要对代码的编写方式进行巨大的改变。](https://github.com/philholden/redux-swarmlog)
- [维护撤销历史或实现乐观的变化，不需要对代码的编写方式进行巨大的改变。](https://redux.js.org/usage/implementing-undo-history)
- [在开发中的状态历史之间穿梭，当代码改变时，从行动历史中重新评估当前状态，就像TDD一样。](https://github.com/reduxjs/redux-devtools)
- [在重用大部分业务逻辑的同时，提供替代的UI。](https://www.youtube.com/watch?v=gvVpSezT5_M&t=711s)

`redux` 有这么多的扩展应用，认为 `Redux` 将被新的 `Context API` 取代还为时过早。

## 其他答案

如果你使用 `Redux` 只是为了避免向深度嵌套的组件传递 `props`，那么你可以用 `Context API` 代替 `Redux`。它正是为这种使用情况而设计的。

另一方面，如果你正在使用 `Redux` 做其他事情（拥有一个可预测的状态容器，在组件之外处理你的应用程序的逻辑，集中你的应用程序的状态，使用 `Redux DevTools` 来跟踪你的应用程序的状态何时、何地、为何以及如何改变，或者使用 `Redux Form`、`Redux Saga`、`Redux Undo`、`Redux Persist`、`Redux Logger` 等插件...），那你绝对没有理由放弃 `Redux`。`Context API` 目前并没有提供这些。

而我个人认为，`Redux DevTools` 扩展是一个神奇的、被低估的调试工具，它的存在本身就证明了继续使用 `Redux` 的理由。

---

`Redux` 是一个状态管理库。它所做的远不止是像 `Contexts API` 那样用一个 `set/get` 接口来存储状态。在它的内部实现，`Redux` 实际上使用 `React Context` 来存储其状态。

它强迫你通过 `action` 来改变状态。如果你的状态变化很复杂（例如，一个动作应该改变你的状态的多个部分），使用 `redux` 是有意义的。在一个复杂的应用程序中，`Redux` 可以防止错误和不一致的状态。

根据个人经验，只要你的状态变化是简单的，你就应该使用 `React Context` 。如果你遇到的问题是很难让你的状态的多个地方保持同步，使用 `Redux` 可能是有意义的。

> 问题来源：[https://stackoverflow.com/questions/49568073/react-context-vs-react-redux-when-should-i-use-each-one](https://stackoverflow.com/questions/49568073/react-context-vs-react-redux-when-should-i-use-each-one)