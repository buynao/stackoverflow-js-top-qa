## 问题：ES6 中 WeakMap 的实际用途是什么？

`ECMAScript 6` 中引入了一种新的数据结构 `WeakMap`，它的实际用途是什么？

由于它的 `键 key` 是一个弱引用，而其 `值 value` 是一个强引用。因此只要它的 `键 key` 还在内存中"存在"，其 `值 value` 就会在。所以不能安全的将其用于 `备忘录 memo tables` , `缓存 caches` 或者其他需要用到弱引用的场景中。

我认为这两种写法并没有什么大的区别，

```js
weakmap.set(key, value);

//or

key.value = value;
```

有没有更有用的场景？

## 答案

`WeakMaps` 提供了一种从外部扩展对象而不影响垃圾回收的方法。

当你想扩展一个对象，但由于它是来自内部，或来自外部的以及其他情况，而不能扩展时，就可以应用 `WeakMap`。

`WeakMap` 是个 `map 字典`，它的 `键 key` 是 `弱应用` 的。

也就是说，如果对 `键 key` 的所有引用都丢失了，并且也没有对 `值 value` 的引用 -- 那么其 `值 value` 可以被垃圾回收。

让我们先通过例子来了解一下

比方说，我正在使用一个 API，它给了我一个特定的对象。

```js
var obj = getObjectFromLibrary();
```

现在，我有一个使用该对象的方法。

```js
function useObj(obj){
   doSomethingWith(obj);
}
```

我想跟踪某个对象调用该方法的次数，如果超过 N 次就上报。

大家通常会认为应该使用 `Map` 来作记录。

```js
var map = new Map(); // maps can have object keys
function useObj(obj){
    doSomethingWith(obj);
    var called = map.get(obj) || 0;
    called++; // called one more time
    if(called > 10) report(); // Report called more than 10 times
    map.set(obj, called);
}
```

这确实有效，但它有 `内存泄漏`：现在要跟踪传递给函数的每一个 `obj`，这使得该对象永远不会被垃圾回收。

这种情况下，我们可以使用 `WeakMap` 来解决内存泄露的问题。

```js
var map = new WeakMap(); // create a weak map
function useObj(obj){
    doSomethingWith(obj);
    var called = map.get(obj) || 0;
    called++; // called one more time
    if(called > 10) report(); // Report called more than 10 times
    map.set(obj, called);
}
```

### 使用案例 Use cases

应该使用 `WeakMaps`，不然会导致内存泄漏的一些用例：

- 对特定对象的私有数据进行保存，并且只允许对该 Map 有权限的人可以访问。
- 保留关于其他库中对象的数据，不会改变它们或者有其他开销
- 保留关于浏览器中特有对象的数据，如 DOM 节点。
- 从外部为一个对象添加其他特性

### 让我们来看看一个真实的用途

`WeakMap` 可以从外部扩展一个对象。让我们从 `Node.js` 中举一个实际的例子。

假如你是 `Node.js`，你的 `Promise` 对象 -- 现在你想跟踪所有当前被 `reject` 的 `Promise` 。

由于显而易见的原因，你不想直接给本地对象添加属性。

如果你保留对 `Promise` 的引用，就会造成内存泄漏，因为不能发生垃圾回收。

如果你不保留引用，那么你就不能保存关于 `Promise` 的额外信息。

任何涉及保存 `Promise` 的 ID 的方案都意味着你需要一个对它的引用。

### 进入WeakMaps

`WeakMaps` 的 所有 `键 key` 都是 `弱 weak` 引用。

没有办法枚举 `弱 weak` 引用，也没有办法枚举它的所有 `值 value`。

在 `弱引用 (weak map)` 中，你可以根据 `键 key` 来存储数据，当 `键 key` 被垃圾回收时，`值 value` 也会被垃圾回收。

这意味着，在需要特定处理的 `promise` 中，你可以创建一个 `obj 对象`，存储关于它的 `state 状态` -- 而且这个 `对象` 可以正常被垃圾回收，释放内存。

总而言之，可以设想将 `promise` 和其 `state状态` 等信息保存在 `WeakMaps` 中，有了这个映射关系，在添加一些扫描逻辑，就可以知道何时处理一个被 `reject` 的 `promise` 了。

    针对该问题，此处没有具体代码，答者仅仅是给了一个思路

    关于该问题，业界已经有所讨论，可以参考以下链接，去了解更多相关讨论：

    https://github.com/iojs/io.js/issues/256

    https://iojs.org/api/process.html#process_event_unhandledrejection

> 问题来源：[https://stackoverflow.com/questions/29413222/what-are-the-actual-uses-of-es6-weakmap](https://stackoverflow.com/questions/29413222/what-are-the-actual-uses-of-es6-weakmap)