## 问题

我想使用 `performance.memory` 来测量我的 `Web SPA` 的内存使用情况，主要是想检测是整个页面的生命周期内是否有内存泄漏。

出于这个原因，我需要在特定的时间间隔内调用这个 `API` -- 可能是每 3 秒，30 秒，或1分钟...

然后我的问题是--为了快速有效地检测可能会出现的问题，我想要将时间间隔尽可能地短一些，但我又担心是否会产生性能问题。

如果测量任务的开销比较昂贵，那么测量本身可能会影响到网络应用程序的性能。

有了以上的背景，我的问题如下：

- `performance.memory` 这种方法是否会影响到浏览器主线程的性能

- 有没有什么正确的方法或程序来确认 `Javascript` 的一些任务是否会影响设备的性能？

## 答案

（我是 V8 的开发者）

调用 `performance.memory` 是相当快的。

你可以在一个快速测试中很容易地验证这一点：比如在一个循环中将其调用 1000 次，然后测量需要多长时间。

然而，这种观察结果并不能解决你的问题。`performance.memory` 之所以快，也是频繁调用它毫无意义的原因：**它只是返回一个缓存的值**，实际上并没有做任何工作来测量内存消耗。(如果它真的做了，那么调用它就会超级慢。)

下面是一个快速测试来证明这两点。

```js
function f() {
  if (!performance.memory) {
    console.error("unsupported browser");
    return;
  }
  let objects = [];
  for (let i = 0; i < 100; i++) {
    // We'd expect heap usage to increase by ~1MB per iteration.
    objects.push(new Array(256000));
    let before = performance.now();
    let memory = performance.memory.usedJSHeapSize;
    let after = performance.now();
    console.log(`Took ${after - before} ms, result: ${memory}`);
  }
}
f();
// 输出
// ....
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// Took 0 ms, result: 58817896
// ...
```

你可以看到，浏览器出于安全考虑，对定时器的粒度进行了钳制：打印的执行时间要么是 0ms ，要么是 0.1ms ，从来没有介于两者之间，这并不是巧合。

然而，这并不是个问题，因为想要 "为了快速有效地检测可能会出现的问题，我想要将时间间隔尽可能地短" 的前提是被误导的：

在使用垃圾收集的语言中，内存使用量有一些上下波动是完全正常的。

这是因为寻找可以释放的对象是一项昂贵的工作，所以垃圾收集器被仔细地调整为一个很好的折衷方案：它们应该尽可能快地释放内存，而不把 CPU 周期浪费在无用的忙碌工作上。

检查你的应用程序内存消耗是一个好主意，你不是第一个这样做的人，而`performance.memory` 是目前最好的工具。

**你要知道，你需要要找的是一个长期的上升趋势，而不是短期的波动。**

因此，每 10 分钟左右进行一次测量，就完全足够了。此外，你仍然需要大量的数据才能看到有统计意义的结果。这是因为你的任何一次测量都可能发生在垃圾收集周期之前或之后，这将导致一些数据偏差。

例如，如果你观察你的用户在 10 秒后的内存消耗比 5 秒后的高，那么这可能只是垃圾收集器在按计划工作，没什么大不了。

而如果你注意到 10 分钟后，读数在 100-300MB 的范围内，20 分钟后在 200-400MB 的范围内，1 小时后是 500-1000MB ，那么现在是时候去寻找这个漏洞了。

> 问题来源：[https://stackoverflow.com/questions/71293653/would-calling-performance-api-frequently-be-causing-a-performance-issue/71295042#71295042](https://stackoverflow.com/questions/71293653/would-calling-performance-api-frequently-be-causing-a-performance-issue/71295042#71295042)