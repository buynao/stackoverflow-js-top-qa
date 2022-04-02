## 问题

我有一些副作用要在我的 `React` 组件中进行调用，想知道如何更好的组织它们：

- 作为一个单一的 `useEffect`
- 或几个 `useEffects`

从性能和架构上看，哪个更好？

## 答案

你应该按照实际的业务场景，来决定遵循哪种方式。

### 第一种场景：

你可能会遇到这样的情况：

- 你需要在初始挂载时添加事件监听器，并在卸载时将其清理掉
- 另一种情况是在 `props` 改变时，需要清理并重新添加特定的监听器

```js
useEffect(() => {
   // adding event listeners on mount here
   return () => {
       // cleaning up the listeners here
   }
}, []);

useEffect(() => {
   // adding listeners everytime props.x changes
   return () => {
       // removing the listener when props.x changes
   }
}, [props.x])
```

### 第二中场景

你的组件 `state` 或 `props` 发生变化时，需要触发 `API` 调用或其他一些副作用。

在这种情况下，带有相关依赖关系的单一 `useEffect` 来监控会更好

```js
useEffect(() => {
    // side effect here on change of any of props.x or stateY
}, [props.x, stateY])
```

### 第三种场景

对不同的变化有单独的副作用，你就需要把相关的副作用分离到不同的 `useEffects` 中。

```js
useEffect(() => {
   // some side-effect on change of props.x
}, [props.x])

useEffect(() => {
   // another side-effect on change of stateX or stateY 
}, [stateX, stateY])
```

> 问题来源：[https://stackoverflow.com/questions/54002792/in-general-is-it-better-to-use-one-or-many-useeffect-hooks-in-a-single-component](https://stackoverflow.com/questions/54002792/in-general-is-it-better-to-use-one-or-many-useeffect-hooks-in-a-single-component)