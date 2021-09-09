## Object.assign()
用来将所有可枚举的属性从一个或多个源对象赋值给目标对象，返回目标对象。

``` js
const target = { a: 1, b: 2 }
const source = { b: 4, c: 5 }

const returnedTarget = Object.assign(target, source)
console.log(          
  target,                                 // {a: 1, b: 4, c: 5} 
  target === returnedTarget               // true
)
```

### 相同属性值会覆盖
如果目标对象中的属性具有相同的键，则属性将被源对象中的属性覆盖。后面的源对象的属性将类似地覆盖前面的源对象的属性。

``` js
const o1 = { a: 1, b: 1, c: 1 }
const o2 = { b: 2, c: 2 }
const o3 = { c: 3 }

const obj = Object.assign({}, o1, o2, o3) // { a: 1, b: 2, c: 3 }
```

### Object.assign() 是浅拷贝
如果源对象的值是一个对象的引用，它仅仅会复制它的应用。

``` js
const o1 = { a: 1, b: { c: 2 } }
const o2 = Object.assign({}, o1)          // { a: 1, b: { c: 2 } }

o2.b.c = 1
console.log(o1.b.c)                       // 1

o1.b.c = 0
console.log(o2.b.c)                       // 0
```

### Object.assign 对目标对象的处理
如果目标对象是 null 或 undefined，则报错。因为 null 和 undefined 本身无法转成对象：

``` js
Object.assign(null, { a: 1} )             // Uncaught TypeError: Cannot convert undefined or null to object
```

如果目标对象是非对象类型的基本类型的值，则会被转成对象：

``` js
Object.assign(2)                          // Number {2}
Object.assign('')                         // String {''}
Object.assign(Symbol(1))                  // Symbol {Symbol(1)}
```

### Object.assign 对源对象的处理
Object.assign 仅拷贝可枚举的属性。

``` js
const obj = { a: 1 }
Object.defineProperty(obj, 'b', {
  value: 2,
  enumerable: false,
})
Object.assign({}, obj)                    // { a: 1 }
```

源对象这些参数都会转成对象。如果无法转成对象，将被跳过。除了字符串以数组的形式，拷贝到目标对象，其他值将不会产生效果。

``` js
Object.assign({}, undefined, null, 10, true, () => {}, 'str') // {0: 's', 1: 't', 2: 'r'}
```

上述例子：
- undefined/null 不能转成对象会被忽略但不报错。
- 10 转成对象 { [[PrimitiveValue]]: 10 }
- true 转成对象 {[[PrimitiveValue]]: true}
- 函数没可枚举属性
- str 转成对象 { "0": "s", "1": "t", "2": "r" }
仅 str 会产生可枚举的属性，这些属性则会被拷贝。

Object.assign 只能拷贝源对象内本身的属性，不能拷贝源对象的继承属性（即其原型对象上的属性）：

``` js
const o = { c: 2 }
const obj = Object.create(o)             
obj.a = 'a'
obj.b = 'b'
Object.assign({}, obj)                    // { a: 'a', b: 'b' }
```

### Object.assign 和扩展运算符的异同
相同点：
- 都能够实现将源对象浅拷贝到目标对象
- 将 null/undefined 作为源对象不会报错，但会被忽略
- 两者都属于 es6 语法，IE 均不支持。

``` js
Object.assign({}, undefined, null, 10, true, () => {}, 'str')
var a = {}, b = undefined, c = null, d = 10, e = () => {}, f = 'str'

// {0: 's', 1: 't', 2: 'r'} {0: 's', 1: 't', 2: 'r'}
console.log(
  Object.assign(a, b, c, d, e, f),
  {...a, ...b, ...c, ...d, ...d, ...e, ...f}
)
```

不同点：
- 扩展运算符更加简洁、性能更高。

### refs
- [MDN Object.assign](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
- [ruanyifeng Object.assign()](https://github.com/ruanyf/es6tutorial/blob/14a4724b859ed0f119b58e91f939ffe6dc040c0d/docs/object-methods.md#objectassign)
- [Object.assign() & object Spread 运算符](https://segmentfault.com/a/1190000023965313)
