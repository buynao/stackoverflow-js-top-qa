## 问题：React中 state 和 props 的区别是什么？

我在学习关于 props 与 state 的内容时，看到文章里提到：

> props 和 state 的变化都会触发渲染更新。

文章的后面接着说：

> props（属性的缩写）是组件的配置项，你可以进行选择配置。它们是从组件上层直接传递下来的，并且是不可变的数据。

- props 会不会产生变化，为什么是它们是不可变( `immutable` ) 的数据？
- 什么时候应该使用 `props`，什么时候应该使用 `state` ?
- 如果 React 组件需要你来提供数据，应该通过 `props` 传递，还是通过`getInitialState` 在React组件中配置？

## 答案

`props` 和 `state` 是相关联的。一个组件的 `state` 往往会成为其子组件的 `props。`

`props` 在父组件的渲染方法中作为 `React.createElement()` 的第二个参数传递给子组件，如果你使用 `JSX`，则更简单：

```js
<MyChild name={this.state.childsName} />
```

父组件的 `state` 值 `childsName` 成为子组件的 `this.props.name`。

从子组件的角度来看，`props` 是不可变的。如果它需要被改变，也只会在父组件的内部改变它的 `state` 值并传递下去：

```js
this.setState({ childsName: 'New name' });
```

`React` 将为你把改变后的 `state` 传递给子组件。
一个后续问题是：如果子组件也需要改变它的 `props` 怎么办？通常我们是通过子组件产生的事件，加上父组件回调函数一起完成的。

子组件会暴露一个事件，比如说，`onNameChanged`。父组件将通过传递一个回调函数来订阅该事件。

```js
<MyChild name={this.state.childsName} onNameChanged={this.handleName} />
```

子组件将通过调用 `this.props.onNameChanged('New name')`，将其获取到的新名字作为参数传递给事件的回调函数，而父组件将在回调函数中使用该名字来更新其 `state` 。

```js
handleName: function(newName) {
   this.setState({ childsName: newName });
}
```

> props 会产生变化，但它们是否应该属于不可变( `immutable` ) 的数据？

这就是React的特性，所有的数据（几乎）都是往下流（传递）的。

`props` 是只有父组件才有的，所以只有在父组件中才能改变它。

理想情况下，子组件应该是无状态的。但是这在实践中是不太可能的（见React网站上的表单文档）。

## 其他答案

对于父组件和子组件的数据交流，只需传递 props。

使用 `state` 来存储你的组件在控制器 -> 视图中所需要的数据。

使用 `props` 将数据和事件处理程序向下传递给你的子组件。

### Props

> `props` 通过赋予组件从其父级组件接收 `props` 形式的数据的能力，使组件可重复使用。等同于函数的参数。


- 是不可变的( `immutable` ) -> React 可以根据此特性进行快速的引用检查
- 是用来从你的视图控制器向下传递数据的 -> 你是当前的顶层组件
- 有更好的性能 -> 用它来传递数据给子组件

### State

> `state` 是组件的本地状态，不能在组件之外被访问和修改。相当于一个函数中的局部变量。

- 应该在你的视图控制器中由自己来管理 -> 你是当前的顶层组件
- 是可变的
- 性能会差些
- 不应从子组件中访问 -> 应该使用 props 来传递

### 哪些组件应该使用 state ?

你的大多数组件应该只是从 `props` 获取数据并进行渲染。然而，有时你需要对用户的输入、服务器的请求或时间的流逝做出响应。这种情况下，你可以使用 `state` 。

尽量保持尽可能多的组件是 `无状态( stateless )` 的，从而把 state 隔离到最合适的地方，并尽量减少冗余，这会让你的应用程序更容易维护。

一个常见的模式是创建几个只渲染数据的 `无状态( stateless )组件`，并在其上层有一个 `有状态( stateful )组件`，通过 `props` 将其 `state` 传递给其子组件。`有状态( stateful )组件` 封装了所有的交互逻辑，而 `无状态( stateless )组件` 则以声明的方式处理数据的渲染。- [更多可参考此文章](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html#what-components-should-have-state)

### 哪些应该放到 state 中？

`state` 包含了： 组件在各种事件处理过中，将会改变的数据，以此触发UI更新。

在实际的应用程序中，这些数据往往是非常小的，并且是可以被 `JSON序列化(  JSON-serializable )` 的。

当构建一个 `有状态( stateful )组件` 时，要尽可能的减少 `state` 的数据量，并且只在 `this.state` 中存储这些属性。

在 `render()` 中，只需根据这个 `state` 计算你需要的任何其他信息。你会发现，以这种方式思考和编写应用程序往往会让你的应用更加稳定和已维护，因为向 `state` 添加冗余或计算值意味着你需要明确地保持它们的同步，而不是依靠 `React` 为你计算它们。- [更多可参考此文章](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html#what-should-go-in-state)

> 译者注：对于使用 props 可能会让应用的性能更友好，对此我表示疑惑，有兴趣的同学，可在 PR 或者 issues 进行解释


> 问题来源：[https://stackoverflow.com/questions/27991366/what-is-the-difference-between-state-and-props-in-react](https://stackoverflow.com/questions/27991366/what-is-the-difference-between-state-and-props-in-react)