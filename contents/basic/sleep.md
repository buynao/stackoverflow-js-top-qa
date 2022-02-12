## What is the JavaScript version of sleep()?

有没有比 `pausecomp` 更好的方法， 在 `JavaScript` 中设计 `sleep` ？

```js

function pausecomp(millis)
{
    var date = new Date();
    var curDate = null;
    do { curDate = new Date(); }
    while(curDate-date < millis);
}

```

## 最佳答案

### 2017 — 2021 更新

自 2009 年提出这个问题以来，`JavaScript` 发生了重大变化。所有其他答案现在都已过时或过于复杂。

这是当前的最佳实践：

```js

function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}
//  或作为单行

await new Promise(r => setTimeout(r, 2000));

// 或

const sleep = ms => new Promise(r => setTimeout(r, ms));

await sleep(<duration>);

```

### 使用

```js

function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

async function demo() {
    for (let i = 0; i < 5; i++) {
        console.log(`Waiting ${i} seconds...`);
        await sleep(i * 1000);
    }
    console.log('Done');
}

demo();

```

**请注意。**

`await` 只能在以 `async` 关键字为前缀的函数中执行。`await` 只暂停当前的异步函数。

这意味着它不会阻止脚本其他部分的执行，这是你在绝大多数情况下想要的。

如果你确实想要一个`阻塞结构`，可以试下 `Atomics.wait`，它应该在 Node.js 9.3 或更高版本中工作。

```js
let ms = 10000;
Atomics.wait(new Int32Array(new SharedArrayBuffer(4)), 0, 0, ms);
```

跑了几个 10 秒计时器。

使用 `setTimeout`，我会收到高达 7000 微秒（7 毫秒）的错误。

使用 `Atomics`，我的错误似乎保持在 600 微秒（0.6 毫秒）以下

**总结：**

```js
  function sleep(millis){ // Need help of a server-side page
    let netMillis = Math.max(millis-5, 0); // Assuming 5 ms overhead
    let xhr = new XMLHttpRequest();
    xhr.open('GET', '/sleep.jsp?millis=' + netMillis + '&rand=' + Math.random(), false);
    try{
      xhr.send();
    }catch(e){
    }
  }

  function sleepAsync(millis){ // Use only in async function
    let netMillis = Math.max(millis-1, 0); // Assuming 1 ms overhead
    return new Promise((resolve) => {
      setTimeout(resolve, netMillis);
    });
  }
  function sleepSync(millis){ // Use only in worker thread, currently Chrome-only
    Atomics.wait(new Int32Array(new SharedArrayBuffer(4)), 0, 0, millis);
  }

  function sleepTest(){
    console.time('sleep');
    sleep(1000);
    console.timeEnd('sleep');
  }

  async function sleepAsyncTest(){
    console.time('sleepAsync');
    await sleepAsync(1000);
    console.timeEnd('sleepAsync');
  }

  function sleepSyncTest(){
    let source = `${sleepSync.toString()}
      console.time('sleepSync');
      sleepSync(1000);
      console.timeEnd('sleepSync');`;
    let src = 'data:text/javascript,' + encodeURIComponent(source);
    console.log(src);
    var worker = new Worker(src);
  }
```

> [https://stackoverflow.com/questions/951021/what-is-the-javascript-version-of-sleep](https://stackoverflow.com/questions/951021/what-is-the-javascript-version-of-sleep)