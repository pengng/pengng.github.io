---
layout: post
title:  "浏览器同源策略"
categories: web
---

### 同源策略 (Same-Origin Policy)

两个 **Web** 资源的 **URL** 如果具有相同的方案 **(Scheme)** 、 主机 **(Host)**  和端口 **(Port)**  ，则认为它们属于同一个源 **(Origin)**  。
例如，以下两个 **URL** 就属于同一个源。

```text
http://www.example.com/a.html
http://www.example.com/b.html
```

它们的方案是 **"http"** ，主机是 **"www.example.com"**，端口是 **"80"** (默认端口)。

当 **URL** 省略端口时，**HTTP** 协议的默认端口为 **80** ，**HTTPS** 协议的默认端口为 **443** 。
如下两个 **URL** 也属于同一个源。

```text
http://www.example.com/a.html
http://www.example.com:80/b.html
```

第一个 **URL** 省略端口，所以使用 **HTTP** 协议的默认端口 **80** 。第二个 **URL** 指定端口为 **80** 。所以它们仍然属于同一个源。

以下的 **URL** 都属于不同的源：

```text
http://www.example.com/a.html
http://www.example.com:81/a.html        端口同其他 URL 不同
http://example.com/a.html               主机同其他 URL 不同
https://www.example.com/a.html          方案同其他 URL 不同
```

上面几个 **URL** 有的是方案不同，有的是主机不同，有的则是端口不同。

其中一级域名 **example.com** 和二级域名 **www.example.com** 也认为不同。如果两个域名的 **DNS** 解析是同一 **IP** 地址，也认为不同。因为同源策略校验的并不是 **DNS** 解析后的结果。
