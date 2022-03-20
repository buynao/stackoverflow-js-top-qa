## 问题：如何取消正在执行的 Promise ？

有没有一种方法可以清除 `Promise` 实例的 `.thens` ？

我试着用 `Promise.race([result, at.promise])`，它好像没有起作用。


## 答案

暂时没有方法可以取消，至少在 `ECMAScript 6` 中没有。

`Promise`（以及它们的处理程序）默认是不可取消的，在 [es-discuss](https://esdiscuss.org/topic/cancellation-architectural-observations) 上有一些关于如何取消的讨论;

目前的观点是：你可以通过 `子类化（subclassing）` 自己实现可取消的 `Promise` ：

```js
const makeCancelable = (promise) => {
  let hasCanceled_ = false;

  const wrappedPromise = new Promise((resolve, reject) => {
    promise.then((val) =>
      hasCanceled_ ? reject({isCanceled: true}) : resolve(val)
    );
    promise.catch((error) =>
      hasCanceled_ ? reject({isCanceled: true}) : reject(error)
    );
  });

  return {
    promise: wrappedPromise,
    cancel() {
      hasCanceled_ = true;
    },
  };
};

const cancelablePromise = makeCancelable(
  new Promise(r => component.setState({...}}))
);

cancelablePromise
  .promise
  .then(() => console.log('resolved'))
  .catch((reason) => console.log('isCanceled', reason.isCanceled));

cancelablePromise.cancel(); // Cancel the promise
```

相关标准讨论草案在这里 [https://github.com/bergus/promise-cancellation](https://github.com/bergus/promise-cancellation)。

> 译者注：Promise 默认不可取消，即使如上面的自定义方法，也只是取消了 Promise 后续的默认标准行为，而并没有实际取消他的实际执行结果（该发的 http 请求还是会发出去~）。

> 问题来源：[https://stackoverflow.com/questions/29478751/cancel-a-vanilla-ecmascript-6-promise-chain](https://stackoverflow.com/questions/29478751/cancel-a-vanilla-ecmascript-6-promise-chain)