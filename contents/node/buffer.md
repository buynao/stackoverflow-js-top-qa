## 问题：Node.js 中的 Buffer 最大容量限制是多少？

读取的文件太大，无法分配到 buffer，并得到了如下的报错

```js
FATAL ERROR: v8::Object::SetIndexedPropertiesToExternalArrayData() length exceeds max acceptable value
```

    RangeError: "size" argument must not be larger than 2147483647 at Function.Buffer.allocUnsafe (buffer.js:209:3)

如果我试图分配一个 1GB 的 `buffer`，会得到同样的错误。

```js
var oneGigInBytes = 1073741824;
var my1GBuffer = new Buffer(oneGigInBytes); //Crash   
```

那么 `Node.js` 的 `Buffer` 实例的最大容量限制是多少？

## 答案

在 `V8` 中，目前 `类型化数组` (typed array) 的最大容量被设置为 `kSmiMaxValue`，根据平台的不同，如下：

- 1Gb - 1byte on 32-bit
- 2Gb - 1byte on 64-bit

代码中的常数是 `v8::internal::JSTypedArray::kMaxLength`（[源代码](https://github.com/v8/v8/blob/d56ee2e3df56ad13e5d2f2d790faaffc3a493aee/src/objects/js-array-buffer.h#L181)）

```js

```

`V8` 团队正在努力在 `64位` 平台上进一步提高其容量限制，目前 `ArrayBuffer` 对象可以达到 `Number.MAX_SAFE_INTEGER大`（2**53-1）。[参见Bug 4153](https://bugs.chromium.org/p/v8/issues/detail?id=4153)。

> 问题来源：[https://stackoverflow.com/questions/8974375/whats-the-maximum-size-of-a-node-js-buffer](https://stackoverflow.com/questions/8974375/whats-the-maximum-size-of-a-node-js-buffer)