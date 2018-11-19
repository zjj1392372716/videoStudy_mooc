# SPA 单页面

> spa出现的意义

```
1. 前后端分离
2、减轻服务端的压力
3、增强用户的体验
   我们平时的每一个页面都需要进行dns加载，每次切换都需要对整个页面的接口调用。
   但是spa单页面并不会，用户的切换并不会对页面进行切换加载、仅仅是接口的调用。
4、单页面应用的主要内容都依赖于JS的执行，当其首页面下载下来的时候，其实不是完整的页面，而是HTML + JS文件，浏览器提供执行环境于是页面被渲染了出来。用户在访问的时候体验会很好，但是对于搜索引擎的爬虫就不太友善了，因为他们不能执行JS，这时候Prerender就派上用场了，它可以帮忙把页面渲染完成之后再返回给爬虫工具，我们的页面也就能被解析到了。
```

spa最主要的设计之处在于路由的控制。

我们学了vue之后。肯定了解过vue-router，这个插件。他有两种mode（模式）：`history`、`hash`

**hash**

在 H5 还没有流行开来时，一般 SPA 都采用 url 的 `hash(#)` 作为锚点，获取到 `#` 之后的值，并监听其改变，再进行渲染对应的子页面。

`http://localhost:8888/#/abc` 那么利用 `location.hash` 输出的内容就为 `#/abc`。


location对象：

```

```

属性 | 描述
-- | --
hash | 设置或返回从 # 开始的 URL （锚）
host | 设置或返回主机名和当前 URL 的端口号
hostname | 设置或返回当前 URL 的主机名
href| 设置或返回完整的 URL
pathname | 设置或返回当前 URL 的路径部分
port | 设置或返回当前 URL 的端口号
protocol|设置或返回当前 URL 的协议
search | 设置或返回从 ? 开始的 URL 部分

window又一个事件可以监听`hash`的变化： `onhashchange`, 可见此时我们已经完全监听到了 URL 的变化，页面上的内容也对应改变了

页面的引入大致可以分为：

import、ajax、节点插入


```js
<body>
  <h1 id="id"></h1>
  <a href="#/id1">id1</a>
  <a href="#/id2">id2</a>
  <a href="#/id3">id3</a>
</body>

<script>

  window.addEventListener('hashchange', e => {
    e.preventDefault()
    document.querySelector('#id').innerHTML = location.hash
  })
</script>
```

较为完整的一个例子：
```html
<body>
  <h1 id="id">我是空白页</h1>
  <a href="#/id1">id1</a>
  <a href="#/id2">id2</a>
  <a href="#/id3">id3</a>
</body>
<script type="module">
  import demo1 from './demo1.js'
  // 创建一个 newRouter 类
  class newRouter {
    // 初始化路由信息
    constructor() {
      this.routes = {};
      this.currentUrl = '';
    }
    // 传入 URL 以及 根据 URL 对应的回调函数
    route(path, callback = () => {}) {
      this.routes[path] = callback;
    }
    // 切割 hash，渲染页面
    refresh() {
      this.currentUrl = location.hash.slice(1) || '/';
      this.routes[this.currentUrl] && this.routes[this.currentUrl]();
    }
    // 初始化
    init() {
      window.addEventListener('load', this.refresh.bind(this), false);
      window.addEventListener('hashchange', this.refresh.bind(this), false);
    }
  }
  // new 一个 Router 实例
  window.Router = new newRouter();
  // 路由实例初始化
  window.Router.init();

  // 获取关键节点
  var content = document.querySelector('#id');

  Router.route('/id1', () => {
    content.innerHTML = 'id1'
  });
  Router.route('/id2', () => {
    content.innerHTML = demo1
  });
  Router.route('/id3', () => {
    $.ajax({
      url: './demo2.html',
      success: (res) => {
        content.innerHTML = res
      }
    })
  });
</script>
```

![](https://user-gold-cdn.xitu.io/2018/9/21/165faf7b7084a1e6?imageslim)

**history**


h5时代来临，为history提供了更丰富的功能

方法| 含义 
--- | ---
back | 返回上一个页面,与浏览器后退键类似
forward | 页面前进，前进到回退之前的 URL （与浏览器点击向前按钮相同）
go(n) | n 接收一个整数，移动到该整数指定的页面，比如go(1)相当于forward()，go(-1) 相当于 back()，go(0)相当于刷新当前页面


* 往历史栈中添加记录 pushState（state, title, url）

```js
// 现在是 localhost/1.html
const stateObj = { foo: 'bar' };
history.pushState(stateObj, 'page 2', '2.html');

// 浏览器地址栏将立即变成 localhost/2.html
// 但！！！
// 不会跳转到 2.html
// 不会检查 2.html 是否存在
// 不会在 popstate 事件中获取
// 不会触发页面刷新

// 这个方法仅仅是添加了一条最新记录
```

* demo

```html
<body>
  <h1 id="id">我是空白页</h1>
  <a class="route" href="/id1">id1</a>
  <a class="route" href="/id2">id2</a>
  <a class="route" href="/id3">id3</a>
</body>
```


```js
import demo1 from './demo1.js'
  // 创建一个 newRouter 类
  class newRouter {
    // 初始化路由信息
    constructor() {
      this.routes = {};
      this.currentUrl = '';
    }

    // 注册路由，并网记录中添加
    route(path, callback) {

      // 每一个数组成员都是一个函数
      this.routes[path] = (type) => {
        if (type === 1) history.pushState( { path }, path, path );
        if (type === 2) history.replaceState( { path }, path, path );
        callback()
      };
    }

    // 处理路径并调用其方法
    refresh(path, type) {
      this.routes[this.currentUrl] && this.routes[this.currentUrl](type);
    }
    // 初始化
    init() {
      window.addEventListener('load', () => {
        // 获取当前 URL 路径
        this.currentUrl = location.href.slice(location.href.indexOf('/', 8))
        this.refresh(this.currentUrl, 2)
      }, false);

      // 每当同一个文档的浏览历史（即 history 对象）出现变化时，就会触发 popstate 事件。（比如我们点击浏览器的回退和前进按钮）
      window.addEventListener('popstate', () => {
        this.currentUrl = history.state.path
        this.refresh(this.currentUrl, 2)
      }, false);
      const links = document.querySelectorAll('.route')
      links.forEach((item) => {
        // 覆盖 a 标签的 click 事件，防止默认跳转行为
        item.onclick = (e) => {
          e.preventDefault()
          // 获取修改之后的 URL
          this.currentUrl = e.target.getAttribute('href')
          // 渲染
          this.refresh(this.currentUrl, 2)
        }
      })
    }
  }
  // new 一个 Router 实例
  window.Router = new newRouter();
  // 实例初始化
  window.Router.init();

  // 获取关键节点
  var content = document.querySelector('#id');

  Router.route('/id1', () => {
    content.innerHTML = 'id1'
  });
  Router.route('/id2', () => {
    content.innerHTML = demo1
  });
  Router.route('/id3', () => {
    $.ajax({
      url: './demo2.html',
      success: (res) => {
        content.innerHTML = res
      }
    })
  });


```

![](https://user-gold-cdn.xitu.io/2018/9/21/165faf7b6dea7e72?imageslim)

