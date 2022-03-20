## 问题：为什么 Promise 在 resolve 或 reject 后仍在继续执行？

我惊讶地发现，Promise 在被 resolve 或 reject 调用后，后续的代码仍在继续执行。

我认为 `resolve` 或 `reject` 应该会友好的 `exit` 或 `return`，接着将停止所有即时函数的执行。

谁能解释一下为什么？

```js
  return new Promise((resolve, reject) => {
      resolve(true)
      console.log(`---------`); // 也会继续输出
  })
```

## 答案

`JavaScript` 有一个 ["Run-to-completion"](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#run-to-completion) 的概念。

除非报错，否则一个函数会一直执行到 `return` 语句或到达终点。

函数之外的代码不能干扰它（除非再次抛出一个错误）。

如果你想让 `resolve()` 退出你的初始化函数，你必须在它前面加上 `return`:

```js
  return new Promise((resolve, reject) => {
      return resolve(true)
      console.log(`---------`); // 不会继续输出
  })
```


> 问题来源：[https://stackoverflow.com/questions/28896280/why-does-javascript-es6-promises-continue-execution-after-a-resolve](https://stackoverflow.com/questions/28896280/why-does-javascript-es6-promises-continue-execution-after-a-resolve)