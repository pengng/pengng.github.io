---
layout: post
title:  "Web 跨域"
categories: web
---

### 跨域 (Cross-Origin)

浏览器通常管理着多个 **web** 资源，包括 **HTML** 文档、**Javascript** 脚本、**CSS** 层叠样式表，以及图片、音频、视频、字体等多媒体资源，甚至是 **Local Storage** 、**Session Storage** 、**Cookies** 和 **IndexedDB** 这些数据存储，还有服务器的动态数据（通常以 **JSON** 或 **XML** 数据表示，也可以是普通文本或二进制数据）。

其中，**HTML** 文档通常作为入口资源，引用着其他的 **Web** 资源，这些被引用的 **Web** 资源可能来自不同的源。
一个 **Web** 资源访问来自不同的源的另一个 **Web** 资源就会产生跨域的情况，而浏览器的 [同源策略]({% post_url 2020-06-01-same-origin-policy %}) 则是限制这一种情况。

- **HTML** 文档可以通过 `<link>` 、 `<script>` 、 `<img>` 、 `<audio>` 、 `<video>` 、 `<embed>` 、 `<iframe>` 元素来引用其他 **Web** 资源，这些资源可以来自不同的源。
对于这些跨域情况，浏览器的 [同源策略]({% post_url 2020-06-01-same-origin-policy %}) 会放行。

- **CSS** 层叠样式表可以通过 `@import` 、`@font-face`、`url()` 引用其他 **Web** 资源，这些资源可以来自不同的源。
对于这些跨域情况，浏览器的 [同源策略]({% post_url 2020-06-01-same-origin-policy %}) 也会放行。

- **Javascript** 脚本涉及 **跨域网络访问**（使用 `AJAX` 方式进行跨域网络访问）、**跨域文档操作** (访问跨源 `<iframe>` 元素下的节点)、**跨域数据存储访问** (访问跨源 `<iframe>` 元素下的 **LocalStorage** 、 **SessionStorage** 和 **Cookies** 等)，这些跨域情况通常会受到 [同源策略]({% post_url 2020-06-01-same-origin-policy %}) 的限制。
  
