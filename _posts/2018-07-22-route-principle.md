---
layout: post
title: "前端路由实现原理"
subtitle: "刚学web开发的时候，只知道有后端路由的概念，并不知道前端也可以来主导路由。直到学习到了Vue和Vue-Router，才知道了前端路由这个东西，这次就来研究一下前端路由"
author: "Volare"
header-style: text
tags:
  - JavaScript
---

> 前端路由的方式有两种
>
> history路由：history.pushState()+popState事件
>
> hash路由：location.hash+hashchange事件

### history路由
---

history API是HTML5新增的API，跟路由有关的两个API是 `history.pushState()`和`history.repalceState()`

这两个api都可以操作浏览器的历史记录，而且不会引起刷新。不过前者会向浏览器新增一条历史记录，而后者是直接替换当前的历史记录。

这两个api都接受三个参数

- **状态对象（state object）** — 一个JavaScript对象，与用pushState()方法创建的新历史记录条目关联。无论何时用户导航到新创建的状态，popstate事件都会被触发，并且事件对象的state属性都包含历史记录条目的状态对象的拷贝。
- **标题（title）** — FireFox浏览器目前会忽略该参数，虽然以后可能会用上。考虑到未来可能会对该方法进行修改，传一个空字符串会比较安全。或者，你也可以传入一个简短的标题，标明将要进入的状态。
- **地址（URL）** — 新的历史记录条目的地址。浏览器不会在调用pushState()方法后加载该地址，但之后，可能会试图加载，例如用户重启浏览器。新的URL不一定是绝对路径；如果是相对路径，它将以当前URL为基准；传入的URL与当前URL应该是同源的，否则，pushState()会抛出异常。该参数是可选的；不指定的话则为文档当前URL。

除此之外，官方文档提供了 popstate 事件，当我们在历史记录中切换时就会产生 popstate 事件。对于触发 popstate 事件的方式，各浏览器实现也有差异，我们可以根据不同浏览器做兼容处理。 

### hash路由
---

我们经常在url中看到 # 符号，代表的就是哈希路由，使用Vue-Router的时候默认就是使用hash路由，url会以 http(s)://***/#/\*\*\*的形式出现。

我们可以通过监听hashchange事件，来达到前端路由的效果

index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Route</title>
  <style>
    .main {
      display: block;
    }
    .A {
      display: none;
    }
    .B {
      display: none;
    }
  </style>
</head>
<body>
  <div class="main">
    <a href="#/A">去到A路由</a>
    <a href="#/B">去到B路由</a>
  </div>
  <div class="A">A路由页面</div>
  <div class="B">B路由页面</div>
  <script src="Route.js"></script>
</body>
</html>
```

Route.js

```javascript
function Router() {
  // 路由表
  this.routes = {},
  // 当前hash路由值
  this.currentUrl = ''
}

Router.prototype.route = function (path, callback) {
  this.routes[path] = callback || function() {}
}

Router.prototype.refresh = function () {
  this.currentUrl = location.hash.slice(1) || '/index'
  if (this.currentUrl) {
    this.routes[this.currentUrl]()
  }
}

Router.prototype.init = function () {
  window.addEventListener('load', this.refresh.bind(this), false)
  window.addEventListener('hashchange', this.refresh.bind(this), false)
}

window.Router = new Router();

Router.route('/index',function(){
  document.getElementsByClassName('main')[0].style.display = 'block'
  document.getElementsByClassName('A')[0].style.display = 'none'
  document.getElementsByClassName('B')[0].style.display = 'none'
})

Router.route('/A',function(){
  document.getElementsByClassName('main')[0].style.display = 'none'
  document.getElementsByClassName('A')[0].style.display = 'block'
})

Router.route('/B',function(){
  document.getElementsByClassName('main')[0].style.display = 'none'
  document.getElementsByClassName('B')[0].style.display = 'block'
})
Router.init();
```

这样就实现一个简单的hash路由了。