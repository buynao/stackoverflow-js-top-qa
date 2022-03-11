# stackoverflow-js-top-qa

## 背景

国内已经有很多技术社区，其中包含了海量的学习资源，但是像 stackoverflow 这样的，以技术问答形式做分享的却比较少（我知道有一个，但是...）。

在学习过程中，如果能够针对一个问题着手，一直深入下去，会让你对这个知识点掌握的更加深刻。

虽然经过了一番筛选后，整理的很多问题，现在可能看起来比较过时，内容也没有一些现成的前端面试答案看起来更爽更舒服。但是在整理翻译的过程中，还是会有一些让人觉得 “原来还能这样” 的深刻体会。

这些问题之所以之所以票数比较高，除了问题比较经典，时间久远积累的原因之外，更多的是因为很多答疑者们，在提供解决方案的同时，还能集思广益，引发更多角度的思考：

- 比如通过ECMA标准规范，对问题刨根剖底：[this 的运行原理？](./contents/basic/this.md)
- 比如针对 "老生常谈" 的问题，进行深度思考： [在 JavaScript 中如使字符串的第一个字母大写（含国际化方案讨论）？](./contents/advanced/firstStrToUppercase.md)
- 还有一些可能目前热度不高，但是却令人茅塞顿开：有些知识，传了百遍，千遍，就是正确的么？[Javascript 在哪里为函数调用的结果分配内存？堆栈还是堆？](./contents/advanced/heapAndStack.md)
- 比如会将解决方案的优缺点，进行一一对比：[在 JavaScript 中循环遍历数组](./contents/basic/loopArray.md)

总之言而，就是在解决问题的同时，还能够引导大家对问题的背后原因多一些思考~

    所以，我想对 stackoverflow 上 前端 相关（主要是JS，浏览器，框架，性能等）投票数较多且比较有意义的问题进行整理翻译！
    
    为了让 翻译 更有意义，给阅读者带来更有效的收获，会做一些额外加工：
    例如，删除过时问题、问题分类、聚合答案、删除冗余内容、加上自己的心得等等

    由于筛选机制原因，票数最多的问题，一般提问时间也比较久远，对于一些已经明显过时的问题，不在进行翻译
    
    只针对一些有意义的问题，进行翻译。

    有意义的标准，可能包括以下之一：
        1. 票数多
        2. 经典问题
        3. 常见问题，但是解答内容仍然令人醍醐灌顶
        4. 可能知道个大概，但是一下不知道如何具体表达的问题
        5. 一些被人误解的问题
        6. ...

    欢迎 star 激励~

## 在线阅读

[https://stackoverflow-js-top-qa.vercel.app](https://stackoverflow-js-top-qa.vercel.app)

## 目录

> 基础

1. [“use strict” 是如何工作的？](./contents/basic/use_strict.md)
2. [闭包是如何工作的？](./contents/basic/closures.md)
3. [new 是如何工作的？](./contents/basic/new.md)
4. [this 的运行原理？](./contents/basic/this.md)
5. [在 JavaScript 中循环遍历数组](./contents/basic/loopArray.md)
6. [在 JavaScript 中循环枚举对象](./contents/basic/enumerateObject.md)
7. [sleep() 的 JavaScript 版本是什么？](./contents/basic/sleep.md)
8. [如何检查一个空的 JavaScript 对象？](./contents/basic/emptyObject.md)
9. [检查 JavaScript 对象中是否存在某个键？](./contents/basic/testKeyInBbject.md)
10. [为什么不能使用 “for...in” 进行数组迭代？](./contents/basic/badForInArray.md)
11. [在 JavaScript 中如何检测 "invalid date" 日期？](./contents/basic/checkInvalidDate.md)
12. [在 JavaScript 中如何将十进制转换为十六进制？](./contents/basic/hexadecimal.md)
13. [如何创建一个 [1，2，3，4，N...] 的数组？](./contents/basic/arrayn.md)
14. [JavaScript 是否有类似 range() 的方法，可以在边界内的生成一个范围？](./contents/basic/range.md)
15. [在 JavaScript 中如何把字符转换成ASCII编码？](./contents/basic/ascii.md)
16. [在 JavaScript 中 addEventListener 和 onclick 之间有什么区别？](./contents/basic/onclick.md)
17. [在 JavaScript 中如何判断一个 number类型 是浮点数还是整数？](./contents/basic/integerVsFloat.md)
- todo ...

> 进阶

1. [在 JavaScript 中如使字符串的第一个字母大写（含国际化方案讨论）？](./contents/advanced/firstStrToUppercase.md)
2. [Javascript 在哪里为函数调用的结果分配内存？堆栈还是堆？](./contents/advanced/heapAndStack.md)
3. [什么时候应该使用encodeURI，而不是encodeURIComponent？](./contents/advanced/encode.md)
4. [WebSocket和Socket.io之间的区别](./contents/advanced/socketVSwebscocket.md)
- todo ...

> Typescript

1. [TypeScript是什么，为什么要用它来代替JavaScript？](./contents/typescript/index.md)
2. [TypeScript中 接口(interface) 与 类型(type) 的区别是什么？](./contents/typescript/interface.md)
- todo ...

> React

1. [React 中, state 和 props 的区别是什么？应该怎么使用？](./contents/react/stateVsProps.md)
2. [React Context 和 React Redux 的区别是什么？应该怎么使用？](./contents/react/context.md)
- todo ...

> Node.js

1. [package.json 中，版本前的符号 ~ 和 ^ 有什么区别？](./contents/node/packageJSON.md)
- todo ...
