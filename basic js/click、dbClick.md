## click、dbClick
在 chrome 测试中，触发元素双击事件（dbClick）的同时会触发两次单击（click）事件：

``` html
<button onclick="onbtnClick()" ondblclick="onbtndbclick()">按钮</button>

<script>
  function onbtnClick() {
    console.log('onbtnClick')
  }
  function onbtndbclick() {
    console.log('onbtndbclick')
  }
</script>
```

用户双击时，会打印：onbtnClick、onbtnClick、onbtndbclick。

### 区分 click、dbClick
使用 setTimeout、clearTimeout 来区分：
- 单击时，双击事件肯定不执行，300 毫秒后执行单击回调。
- 双击时，`onbtnClick` 会执行两遍。第二遍执行 `clearTimeout` 时会把第一次执行的 `setTimeout` 给去取消掉。`onbtndbclick` 会执行一遍，会把 `onbtnClick` 第二次执行的 `setTimeout` 给取消掉。

``` js
let timer = null
function onbtnClick() {
  timer && clearTimeout(timer)
  timer = setTimeout(() => console.log('onbtnClick'), 300)
}
function onbtndbclick() {
  timer && clearTimeout(timer)
  console.log('onbtndbclick')
}
```

使用**哨兵**：
定义一变量 type，标识当前单击类型：
- 单击时，type 设置为 click，双击时设置为 dbclick。
- 仅当 type 是 click 时，才触发单击回调。

``` js
let type = ''
function onbtnClick() {
  type = 'click'
  setTimeout(
    () => type === 'click' && console.log('onbtnClick'), 
    300
  )
}
function onbtndbclick() {
  type = 'dbclick'
  console.log('onbtndbclick')
}
```

### refs
- [js判断鼠标单击或者双击事件](https://blog.csdn.net/shenmill/article/details/56565086)
