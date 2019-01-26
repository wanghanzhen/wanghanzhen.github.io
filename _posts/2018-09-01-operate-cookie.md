---
layout: post
title: "JS操作cookie"
subtitle: "简单实现以下对cookie的操作"
author: "Volare"
header-style: text
tags:
  - JavaScript
  - cookie
---

> 由于之前面试被问到了设计cookie操作工具类的问题，答得不太好，也发觉自己对cookie的认识不够充分，因为在这里实现一下对cookie的基本操作。

这里简单介绍一下cookie的构成

- **名称**：一个唯一确定的cookie的名称。**不区分大小写，且是经过URL编码的**。
- **值**：储存在cookie中的字符值。**必须经过URL编码**。
- **域**：cookie对于哪个域是有效的。所有向该域发送的请求中都会包含这个cookie的信息。
- **路径**：对于指定域中的那个路径，应该向服务器发送cookie。
- **失效时间**：表示cookie何时应该被删除的时间戳。默认情况下，浏览器会话结束时cookie就会被删除。**若果设置的失效日期是以前的时间，cookie会被立刻删除**。
- **安全标志**：指定后，cookie只有在使用SSL连接的时候才发送到服务器。

**只有键值对才会被发送被服务器，其他参数均为服务器给浏览器的指示。**

使用`document.cookie`可获取到当前页面可用的所有的cookie的字符串，一系列由分号隔开的键值对。

JavaScript对cookie的操作有三种：读书、写入、删除。

```javascript
let Cookie = {
  get(key) {
  },
  set(key, value, config = {}) {
  },
  remove(key, config = {}) {
  }
}
```

要注意的是，给`document.cookie`赋值的时候，这个值会被解释并添加到现有的cookie集合中，并不会覆盖cookie，除非设置的值的cookie名称已经存在，除此之外，键值对均需要进行URL编码。

下面来实现三种操作：

`get(key)`通过正则表达来匹配传入的名称，从而获取cookie。

```javascript
get(key) {
  let cookie = document.cookie;
  let cookieName = encodeURIComponent(key);
  let reg = new RegExp(cookieName + '=([^;]+);');
  let arr = cookie.match(reg);
  return decodeURIComponent(arr[1]);
},
```


`set(key, value, config = {})` config可以用于配置cookie的属性，例如expires，path等。

```javascript
set(key, value, config = {}) {
  let cookieText = encodeURIComponent(key) + '=' + encodeURIComponent(value);
  if (config.expires && config.expires instanceof Date) {
    cookieText += '; expires=' + config.expires.toGMTString();
  }
  if (config.path) {
    cookieText += '; path=' + config.path;
  }
  if (config.domain) {
    cookieText += '; domain=' + config.domain;
  }
  if (config.secure) {
    cookieText += ';' + config.secure;
  }
  document.cookie = cookieText;
},
```
`remove(key, config = {})`直接调用set方法，将其expires属性设置为以前的时间即可，此处的new Data(0)为最初的时间。


```javascript
remove(key, config = {}) {
  this.set(key, '', {...config, expires: new Date(0)});
}
```



**以上就是实现一个简单的操作cookie工具类的实现。通过本次学习，也对cookie的理解加深了不少。** 