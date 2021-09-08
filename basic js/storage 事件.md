## storage 事件
同源下，跨页面、iframe 和父页面进行通讯的一种方式。

原理：当存储区域（`localStorage` 或 `sessionStorage`）被修改时，将触发 window 的 `storage` 事件。

### 示例
index.html 通过 iframe 引入 a.html，并且在新标签页内打开 b.html，它们的 html：

``` html
<!-- index.html -->
<body>
  <span>this is index page</span>
  <button onclick="create()">新增</button>
  <button onclick="modify()">修改</button>
  <button onclick="del()">删除</button>
  <iframe src="a.html" frameborder="0"></iframe>
  
  <script>
    window.onstorage = function (e) {
      console.log('index', e)
    }

    function random() {
      return Math.random() * 100 | 0
    }

    function create() {
      localStorage.setItem(random(), random())
    }
    function modify() {
      localStorage.setItem(
        Object.keys(localStorage).shift(), random()
      )
    }
    function del() {
      localStorage.removeItem(
        Object.keys(localStorage).shift()
      )
    }
  </script>
</body>

<!-- a.html -->
<script>
  window.onstorage = function (e) {
    console.log('a', e)
  }
</script>

<!-- b.html -->
<script>
  window.onstorage = function (e) {
    console.log('b', e)
  }
</script>
```

在 index.html 增加/修改/删除某个 key 时，在 a.html 和 b.html 都能触发对应的 storage 事件，但是在本页面不触发 storage 事件（IE 浏览器下，本页面也会触发）。

### storage 事件具柄一些重要参数
属性|类型|描述
--|--|--
url|string|修改 `localStorage` 或 `sessionStorage` 的页面
key|string|正在更改的健
oldValue|string|正在更改健的旧值
newValue|string|正在更改健的新值
storageArea|Storage|受影响的存储对象

### 如何判断是修改 localStorage 还是 sessionStorage，而触发的 storage 事件？
通过事件具柄参数来判断：

``` js
const storageFromType = (e) => {
  const { storageArea, key, newValue } = e
  return storageArea.length === localStorage.length && 
    localStorage.getItem(key) === newValue 
    ? 'localStorage' 
    : 'sessionStorage'
}
```

### 注意
- 标准浏览器，storage 事件在本页面不触发。在 IE 下，在本页面也触发。
- sessionStorage 触发 storage 不能跨标签页
  原理：不同 tab 页面对应的 sessionStorage 是不同的。当标签页被关闭时，其对应的 sessionStorage 存储的数据也会被清空。

  例如：在 index.html 内修改 sessionStorage 内的数据，位于 iframe 内的 a.html 可以监听到，但位于新标签页的 b.html 对此无感。

  所以 **跨标签页的通讯方式** 之一是：修改 localStorage 内的数据，在其他页面监听 window.onstorage 事件。
- 如果 localStorage/sessionStorage 本身 length 就是 0，即使使用 clear 方法，不会触发 storage 事件。
- removeItem 一个原来没有的 key，不会触发 storage 事件。

### refs
- [MDN storage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/storage_event)
- [Storage 事件](https://mp.weixin.qq.com/s/9_lTlHk0_QDPjeLrpAeovg)
- [sessionStorage changes aren't firing 'storage' event](https://stackoverflow.com/questions/46734395/sessionstorage-changes-arent-firing-storage-event)
