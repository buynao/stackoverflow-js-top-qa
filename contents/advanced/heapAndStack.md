## Javascript 在哪里为函数调用的结果分配内存？栈还是堆？

我浏览了整个互联网，似乎找不到这个问题的答案。

- 我读到过 `栈（Stack）` 用于存储原始数据类型(number, string...)，`堆（Heap）` 用于存储引用数据类型(object, array...)。
- 我还读到过 `栈（Stack）` 的大小是固定的，一旦分配它就不会改变，那么它为什么还能用于原始数据类型的存储？因为 `JS` 在运行前并不一定知道每个变量的大小。
- 基础数据类型是不可变的，所以如果我们尝试重新分配一个变量，它不会改变其内存地址的值，而是会在内存中分配新空间，存储新值并将其地址分配给变量。

如果以上是正确的，那么当我这样做时会发生什么？

```js

let string = "Hello World"; // Memory allocated for "Hello World"
string = string.ToUpperCase(); // New memory allocated for "HELLO WORLD"

```

`HELLO WORLD` 的新内存分配在哪里？如果是在 `栈（Stack）` 中，是因为它是基础数据类型吗？还是在 `堆（Heap）` 中，因为 `JS` 在运行代码之前还不知道它的值。

如果它确实是在 `栈（Stack）` 中，那么旧的 `Hello World` 会发生什么变化？

只有 `堆（Heap）` 里的数据会被垃圾回收收集释放，这是否意味着旧的、未使用的字符串将一直保留着，直到程序运行结束？

如果我这样做会发生什么？

```js

string = string.repeat(10000000000)

```

如果 `栈（Stack）` 的内存空间是固定的，是否意味着，我如果让变量变得巨大，从而让 `栈（Stack）` 溢出？

## 答案

先说结论，答案因 `JS引擎` 而异，`EScript 标准规范` 中并没有对 `JS内存` 分配做出具体要求。

你没必要担心 `栈（Stack）` 用完的情况，这对你的代码来说真的不重要。

`EScript 标准规范` 并不要求实现的每一步都很具体。它描述的是行为，而不是该行为的实现。

但从实际情况来看。一个 `JavaScript引擎` 似乎不太可能尝试把一个巨大的字符串放在 `栈（Stack）` 里。更有可能的情况，它将分配在 `堆（Heap）` 里。但这并不意味着它不能把其他字符串放在 `栈（Stack）` 中。

也许它是根据大小来决定的。

也许它是根据寿命来决定的。

也许它会把一个只在函数中局部使用的小字符串放在 `栈（Stack）` 里，而把一个巨大的字符串，或者一个全局保留的字符串放在 `堆（Heap）` 里。

重要的是，现代的 `JavaScript引擎` 很复杂，而且每个阶段都会进行高度优化。

他们不可能采取:

> `栈（Stack）` 用于存储原始数据类型(number, string...)，`堆（Heap）` 用于存储引用数据类型(object, array...)

这样简单的观点。

引用数据类型也可以在 `栈（Stack）` 中分配，而且至少在历史上的不同时期，一些 `JavaScript引擎` 已经这样做过了。

如果一个对象不能在函数调用结束后继续存在，并且不是大规模的，那么把它放在 `栈（Stack）` 中就可以在函数返回时很容易地回收它。

    译者注：

    同意上述观点，V8引擎的内存分配，是根据不同情况来分配的，并不是简单的 [原始数据存在堆栈上，引用数据类型存在堆上]

    举两个小例子：

    V8引擎在分配 numer 类型时，如果是整数，number 类型就是Smi，如果是浮点数或者bigInt，number 类型就是heap number，从类型名可以推断，内部分配方式两者并不一样。
    
    V8引擎在分配 object 类型时，如果当前属性数量小于 10 个，会将数据直接分配在该对象上；超过 10 个，视不同属性名的类型，会在进行划分：快属性和慢属性，慢属性通过 map 隐藏类映射数据。

    以上，V8引擎在内存分配中，做了大量性能优化工作，绝不仅仅只是 [原始数据存在堆栈上，引用数据类型存在堆上].

参考资料：
[v8 快属性](https://v8.js.cn/blog/fast-properties/)
[v8 性能陷阱](https://siyuan.pub/2021/04/26/javascript-performance-pitfalls-v8/)

> 问题来源：[https://stackoverflow.com/questions/67356107/where-does-javascript-allocate-memory-for-the-result-of-a-function-call-stack-o](https://stackoverflow.com/questions/67356107/where-does-javascript-allocate-memory-for-the-result-of-a-function-call-stack-o)