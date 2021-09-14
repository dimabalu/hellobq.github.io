## deboundce 防抖
事件被触发后，过 n 秒再执回调函数。如果在 n 秒内再次触发该事件，就会被认为抖动，将会重新计时。最终，该回调函数**只会被执行一次**。

### 原理
利用了 “闭包”，将 `timer` “缓存” 起来，防止污染全局环境。并结合 `setTimeout`、`clearTimeout` 及时取消回调函数地执行。

### 案例
处理 input 输入：

``` js
var input = document.querySelector('input')
input.addEventListener('input', handleInput)

function ajaxReq(val) {
  console.log(val)
}

// 性能损耗：每次 input 内容修改，都发起请求
function handleInput(e) {
  console.log(e.target.value)
  input.value = e.target.value
  ajaxReq(e.target.value)       
}

// 高阶：当最后一次 input 内容修改，且过了 delay 秒后，才发起请求
let debounceFn = debounce(ajaxReq)
function advancedHandleInput(e) {
  ...

  // 执行经 “防抖” 处理过的函数
  debounceFn(e.target.value)    
}

function debounce(fn, delay = 1.5) {
  let timer = null
  return (args) => {
    timer && clearTimeout(timer)
    timer = setTimeout(() => fn(args), delay * 1000)
  }
}
```

需要注意的是，不能在 `advancedHandleInput` 内直接调用 `debounce`，因为这会导致每次 `input` 事件触发，都会执行 `debounce`，`timer` 都会一直被创建，一直初始化为 `null`。

``` js
function advancedHandleInput(e) {
  ...

  // 错！每次都生成值是 null 的 timer，clearTimeout 不会被调用，setTimeout 总不会被取消
  debounce(ajaxReq)(e.target.value)    
}
```

### refs
- [js 函数防抖为什么要用闭包？](https://blog.csdn.net/KangTongShun/article/details/105316227)
- [浅谈 JS 防抖和节流](https://segmentfault.com/a/1190000018428170)
