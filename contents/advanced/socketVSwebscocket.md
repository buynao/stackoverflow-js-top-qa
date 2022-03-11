## 问题

`node.js` 中的 `Socket.IO`
 和 `WebSocket` 之间有什么区别？ 它们都是关于服务器推送的技术吗？

我目前理解的区别是：

- `Socket.IO` 允许我通过指定一个事件名来 `发送/触发（send/emit）` 消息。
- 使用 `Socket.IO` ，来自服务器的消息会 `push` 到所有的客户端。
- 使用 `WebSocket` ，我必须使用一个包含所有 `连接（connections）` 的数组，通过循环这个数组向所有客户端发送消息。

另外，我想知道为什么很多 网络开发工具（如 Chrome/firebug/fiddler ）无法捕获来自服务器的这些消息（来自 socket.io/websocket）？

## 答案

### 误解

关于 `WebSocket` 和 `Socket.IO`，有一些常见的**误解**。

1. 使用 `Socket.IO` 比使用 `WebSocket` 更容易，
2. `WebSocket` 在浏览器中没有得到广泛支持。
3. `Socket.IO` 将连接降级作为老式浏览器的兼容方案。它将浏览器默认当做老式浏览器，并通过 `AJAX`与服务器进行连接，在检查浏览器支持 `WebSocket` 并交换了一些流量后，随后将连接进行升级。

### 我的实验

我写了一个 `npm` 模块来演示 `WebSocket` 和 `Socket.IO` 的区别。

- [https://www.npmjs.com/package/websocket-vs-socket.io](https://www.npmjs.com/package/websocket-vs-socket.io)
- [https://github.com/rsp/node-websocket-vs-socket.io](https://github.com/rsp/node-websocket-vs-socket.io)

这是个包含服务端和客户端代码的简单 demo -- 客户端使用 `WebSocket` 或 `Socket.IO` 连接到服务端，服务端以 `1s` 的间隔发送三条消息，这些消息被客户端添加到 `DOM` 中。

### 服务端写法上的比较

比较服务器端使用 `WebSocket` 和 `Socket.IO` 的例子，在 `Express.js` 应用程序中做同样的事情。

#### WebSocket Server

使用 `Express.js` 的 `WebSocket` 服务器实例。


```js
var path = require('path');
var app = require('express')();
var ws = require('express-ws')(app);

app.get('/', (req, res) => {
  console.error('express connection');
  res.sendFile(path.join(__dirname, 'ws.html'));
});

app.ws('/', (s, req) => {
  console.error('websocket connection');
  for (var t = 0; t < 3; t++)
    setTimeout(() => s.send('message from server', () => {}), 1000 * t);
});

app.listen(3001, () => console.error('listening on http://localhost:3001/'));
```

#### Socket.IO Server

使用 `Express.js` 的 `Socket.IO` 服务器实例。

```js
var path = require('path');
var app = require('express')();
var http = require('http').Server(app);
var io = require('socket.io')(http);

app.get('/', (req, res) => {
  console.error('express connection');
  res.sendFile(path.join(__dirname, 'si.html'));
});

io.on('connection', s => {
  console.error('socket.io connection');
  for (var t = 0; t < 3; t++)
    setTimeout(() => s.emit('message', 'message from server'), 1000*t);
});

http.listen(3002, () => console.error('listening on http://localhost:3002/'));
```

### 客户端写法上的比较

#### WebSocket Client

使用 `vanilla JavaScript` 的 `WebSocket` 客户端示例。

```js
var l = document.getElementById('l');

var log = function (m) {
    var i = document.createElement('li');
    i.innerText = new Date().toISOString()+' '+m;
    l.appendChild(i);
}
log('opening websocket connection');

var s = new WebSocket('ws://'+window.location.host+'/');

s.addEventListener('error', function (m) { log("error"); });
s.addEventListener('open', function (m) { log("websocket connection open"); });
s.addEventListener('message', function (m) { log(m.data); });
```

#### Socket.IO Client
  
使用 `vanilla JavaScript` 的 `Socket.IO` 客户端示例。

```js
var l = document.getElementById('l');

var log = function (m) {
    var i = document.createElement('li');
    i.innerText = new Date().toISOString()+' '+m;
    l.appendChild(i);
}
log('opening socket.io connection');

var s = io();

s.on('connect_error', function (m) { log("error"); });
s.on('connect', function (m) { log("socket.io connection open"); });
s.on('message', function (m) { log(m); });
```

### 网络流量 Network traffic

要看到网络流量的差异，你可以运行我的测试。以下是我得到的结果。

#### WebSocket 结果

**2 requests, 1.50 KB, 0.05 s**

来自这两个请求

1. HTML page request
2. connection upgrade to WebSocket

(连接升级的请求在开发者工具上可见，有个 101 交换协议的响应码)。

#### Socket.IO 结果

**6 requests, 181.56 KB, 0.25 s**

1. HTML page request
2. Socket.IO's JavaScript (180KB)
3. first long polling AJAX request
4. second long polling AJAX request
5. third long polling AJAX request
6. connection upgrade to WebSocket

### 浏览器兼容性 Browser compatibility

![result](https://raw.githubusercontent.com/buynao/stackoverflow-js-top-qa/main/contents/advanced/assets/WebSocket.png)

## 其他答案

`Socket.IO` 的优点是简化了你在第 2 条中所描述的 `WebSockets` 的使用场景，而且更重要的是，在浏览器或服务器不支持 `WebSocket` 的情况下，它提供了对其他协议的故障转移。

通常情况下，建议避免直接使用 `WebSocket`，除非你已经有了，非常明确使用它的场景。

### SocketIO

- 自动连接功能
- 有命名空间
- 有订阅服务
- 有一个预先设计好的通信协议
- 具有良好的兼容支持
- 可与 `redis` 等服务进行良好的集成
- 在不支持 `WebSocket` 的情况下有回退功能（好吧，虽然这种情况越来越少）。
- 它是一个 `库(library)` 。也就是说，它已经在内部封装好了它的功能，来帮助你更简单的完成你的工作。`Websocket` 只是一个`协议(protocol)`，而 `SocketIO` 使用的是整个集成封装好了的 `库(library)`。
- 整个架构是已经设计完成了的，你可以直接利用它去写你的业务代码，而不必在去花时间去设计和实现它的架构。
- 因为它是一个 `库(library)` ，所以它有一个正在发展的社区（你不可能为 `HTTP` 或 `Websocket` 拥有一个社区 -- 因为它们只是标准/协议）。

### Websocket

- 你对它有绝对的控制权，这可能是个好事，但也可能会带来一些麻烦
- 最轻量的（记住，它是一个 `协议`，不是一个 `库(library)`）。
- 你可以设计你想要的架构 & 协议
- 没有自动连接，如果你想要，你只能自己实现
- 没有订阅服务，同上，由你来设计
- 没有日志记录，由你来设计
- 没有回退兼容方案
- 没有命名空间
- 没有任何支持，你将是全部自己实现
- 你要先专注于它的技术部分，在设计出所以你想要的功能
- 接着要对你的设计进行调试，这将会花费你很长的时间

**很明显，你可以看到我对 `SocketIO` 有偏好。我很想这么说，但我真的真的不是。**

其实，我有在为**不使用** `SocketIO` 而努力，我喜欢设计自己的东西，自己解决自己的问题。

如果你有一个大的业务，而不仅仅是一个 1000 行的项目，选择 `Websocket` 之后，你将不得不自己实现每一件事。你必须调试一切。你必须做你自己的订阅服务。你自己的协议。你需要的一切。你必须知道大部分的功能实现都是相当复杂的。

在这一过程中，你会犯很多的错误。你会花大量的时间来设计和调试一切。我就是这样做的，现在也是如此。我正在使用 `websocket`，我在这里的原因是，对于一个试图为他的创业公司解决业务的人来说，他们可能无法忍受你为 `Websocket` 所做的设计。

如果你是一个人或是一个试图实现复杂功能的小团队，为一个大型应用选择 `websocket` 并不明智。

我在 `websocket` 中写的代码比我过去用 `SocketIO` 写的更多，更复杂。

我只想说...如果你想要一个成品和设计，请选择 `SocketIO`。(除非你要实现一个功能非常简单的业务)

> 问题来源：[https://stackoverflow.com/questions/10112178/differences-between-socket-io-and-websockets](https://stackoverflow.com/questions/10112178/differences-between-socket-io-and-websockets)