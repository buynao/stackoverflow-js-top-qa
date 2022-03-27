## 问题

在不同的浏览器中，URL 的最大长度是多少？不同的浏览器之间是否有差异？

URL 的长度限制是 `HTTP` 规范的一部分吗？

## 答案

### 大概有2000个字符的长度限制

如果你将 `URLs` 保持在 `2000` 个字符以下，它们可以在任何浏览器中正常工作。

如果你的目标是特定的浏览器，请继续浏览下面关于具体限制的更多细节：

### HTTP 标准...

[RFC 2616](http://www.faqs.org/rfcs/rfc2616.html)（超文本传输协议HTTP/1.1）第3.2.1节说

    HTTP 协议对 URI 的长度不做限制。服务器必须能够处理它们所提供的任何资源的 URI。如果一个 URI 超过了服务器可以处理的长度，服务器应该返回 414（Request-URI Too Long）状态（见10.4.15节）。

该RFC已经被 [RFC7230](https://www.rfc-editor.org/rfc/rfc7230#section-3.1.1) 所取代，它是对`HTTP/1.1` 规范的更新。它与之前协议相同，也提出了这样的建议：

    目前在具体实现上，各浏览器对请求行长度有各种特别的限制。
    我们建议所有的 HTTP 发送方和接收方至少支持 8000 个字节的请求行长度。

### 现实中...

然而现实中，在 [boutell.com](boutell.com) 上层级有一篇文章，讨论了各个浏览器的实现将支持什么功能，其中有一段摘要：

    极长的 URLs 是一个错误。超过 2000 个字符的 URL 将无法在最流行的网络浏览器中运行。
    如果你打算让你的网站对大多数互联网用户起作用，就不要使用它们。

### 搜索引擎对小于 2048 个字符的URL更友好...

请注意 [站点地图协议（sitemaps protocol）](https://www.sitemaps.org/protocol.html)，它允许网站通知搜索引擎抓取自身页面，前提是该网站的 URL 有 2048 个字符的限制。

如果你打算使用 [站点地图协议（sitemaps protocol）](https://www.sitemaps.org/protocol.html)，那么它将为你做此限制。

### CDN 也有限制

CDN 也对 URI 长度进行了限制，例如，当达到这些限制时，将返回 `414 Too long request`

### 其他浏览器的最新限制（截止2021年）

    Browser     Address bar   document.location
                              or anchor tag
    ------------------------------------------
    Chrome          32779           >64k
    Android          8192           >64k
    Firefox          >64k           >64k
    Safari           >64k           >64k
    IE11             2047           5120
    Edge 16          2047          10240

> 问题来源：[https://stackoverflow.com/questions/417142/what-is-the-maximum-length-of-a-url-in-different-browsers](https://stackoverflow.com/questions/417142/what-is-the-maximum-length-of-a-url-in-different-browsers)