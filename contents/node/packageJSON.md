## 问题：

在我升级到最新的稳定版 `node` 和 `npm` 之后，我尝试了 `npm install moment --save`。

它在 `package.json` 中保存了记录，前缀是 `^` 。之前，是 `~` 前缀。

为什么 `npm` 会做这些改变？`~` 和 `^` 之间的区别是什么？各自有什么优势？

## 答案

参见 [NPM](https://docs.npmjs.com/files/package.json) 文档和 [semver](https://docs.npmjs.com/cli/v6/using-npm/semver#tilde-ranges-123-12-1) 文档。

- `~` version，**约等于版本（Approximately equivalent to version）**，会将你更新到最新的补丁版本，而不会更新到小版本。`~1.2.3` 将使用从 `1.2.3` 到 `<1.3.0` 之间的版本。
- `^` versiong，**与版本兼容（Compatible with version）**，会将你更新到最新的小版本/补丁版本，而不会更新到大版本。`^2.3.4` 将使用从 `2.3.4` 到 `<3.0.0` 之间的版本。


> 问题来源：[https://stackoverflow.com/questions/22343224/whats-the-difference-between-tilde-and-caret-in-package-json](https://stackoverflow.com/questions/22343224/whats-the-difference-between-tilde-and-caret-in-package-json)