# JavaScript

### 常见面试问题

### API

#### 1.JavaScript中基本数据类型

- number
- bigint: **这个常常会忽略，最新加入 在 JavaScript 中，BigInt 是一种数字数据类型，它可以表示的整数**。
- string
- undefined
- null
- symbol
- bool

#### 2.数组

##### 1. 在 js 中如何把类数组转化为数组

类数组（array-like）指的是：

- 有 `length` 属性
- 可以通过下标访问（如 `obj[0]`）
- **但没有数组的方法**（如 `map / filter / push`）

常见例子：

- `arguments`
- `NodeList`（如 `document.querySelectorAll`）
- `HTMLCollection`

```javascript
Array.from(arrayLike);

const arr = [...arrayLike];

Array.prototype.concat.apply([], arrayLike);

// 底层
const arr = [];
for (let i = 0; i < arrayLike.length; i++) {
  arr.push(arrayLike[i]);
}
```

##### 2. 如何判断某一个值是数组

```
// 1 最常用
Array.isArray(value) //true
// 2
Object.prototype.toString.call(value) === '[object Array]'
// 3 instanceof（有坑）
[1,2,3] instanceof Array  // true
//❌ 问题：iframe（跨作用域）会失效
```

##### 3. 如何创建一个数组大小为100，每个值都为0的数组

```
// 方法一:
Array(100).fill(0);

// 方法二:
// 注: 如果直接使用 map，会出现稀疏数组
Array.from(Array(100), (x) => 0);

// 方法二变体:
Array.from({ length: 100 }, (x) => 0);

// 方法三：
const arr = new Array(100); arr.fill(0)
```

#### 3.Number、String、Array、Object、Promise API

##### 1.**Number（数字）**

1️⃣ 静态方法（直接用 Number.xxx）

- `Number.isNaN(value)` → 判断是不是 NaN（比 isNaN 更严格）
- `Number.isFinite(value)` → 是否是有限数
- `Number.parseInt()` / `Number.parseFloat()` →函数将其第一个参数转换为一个字符串，对该字符串进行解析，然后返回一个整数/浮点数或 `NaN`

2️⃣ 实例方法（num.xxx）

- `toFixed(n)` → 保留 n 位小数
- `toPrecision(n)` → 指定精度
- `toString()` → 转字符串

###### 1. Number 中最大数、最大安全整数、EPSILON 都是多少，原理是什么

| 属性                      | 值          | 含义                              | 原理                                                         |
| ------------------------- | ----------- | --------------------------------- | ------------------------------------------------------------ |
| `Number.MAX_VALUE`        | ≈ 1.79e+308 | JS 能表示的 **最大有限数**        | JS 的 Number 是 **64 位浮点数（IEEE 754）**                  |
| `Number.MAX_SAFE_INTEGER` | 2^53 - 1    | 可以被“精确表示”的最大整数        | JS 的 Number 只有 53 位有效精度（52 位尾数 + 1 位隐含位），所以最多能精确表示到 2⁵³−1 |
| `Number.EPSILON`          | 2^-52       | 1 和比 1 大的最小浮点数之间的差值 | 尾数只有 52 位(小数点后最多 52 位)       

###### 2. 为何 0.1+0.2 不等于 0.3，应如何做相等比较
0.1，0.2 表示为二进制会有精度的损失，比较时可引入一个很小的数值 `Number.EPSILON` 容忍误差，其值为 2^-52。
```javascript
function equal(a, b) {
  return Math.abs(a - b) < Number.EPSILON;
}

``` 


##### 2.**String（字符串）**

**高频 API：**

- 截取类：
  - `slice(start, end)`
  - `substring(start, end)`
- 查找类：
  - `includes()`
  - `indexOf()`
  - `startsWith()` / `endsWith()`
- 处理类：
  - `trim()` 去空格
  - `toUpperCase()` / `toLowerCase()`
- 转换类：
  - `split()` 字符串 → 数组
  - `replace()` 替换

##### 3.**Array（数组，重点！）**

1️⃣ 增删改查

- `push()` / `pop()`
- `shift()`   - 删除数组第一个元素
- `unshift()`- 向数组开头添加一个或多个元素
- `splice()` -万能增删改，指定位置删除 / 插入元素

2️⃣ 遍历类（非常重要）

- `map()` → 映射
- `filter()` → 过滤
- `reduce()` → 聚合（面试重点！）
- `forEach()`

3️⃣ 判断类

- `includes()`
- `find()` / `findIndex()`
- `some()` / `every()`

4️⃣ 其他高频

- `sort()`
- `reverse()`
- `flat()`
- `join()` 数组 → 字符串
- `slice` -不修改原数组

总结
1. 修改原数组：push/pop/unshift/shift/splice/sort/reverse
2. 不修改原数组：slice/concat/join/map/filter/flat
3. 高频必用：map、filter、reduce、forEach、find、includes
4. 判断数组：Array.isArray()

###### ① map 和 forEach 区别

✅ 面试标准回答

> `map` 会返回一个新数组，适合做“数据转换”；
> `forEach` 没有返回值，适合做“副作用操作”，比如：遍历渲染节点

###### ② slice和splice的区别

- slice：不修改原数组，纯截取，返回新数组
- splice：直接修改原数组，可增 / 删 / 改，返回被删除的元素

###### ③ reduce 能做什么？

✅ 面试标准回答

> `reduce` 本质是“把数组压缩成一个值”，可以用来做累加、计数、去重、扁平化等。

##### **4.Object（对象）**

1️⃣ 静态方法

- `Object.keys(obj)` → 返回 key 数组
- `Object.getOwnPropertyNames(obj)`
- `Object.values(obj)`
- `Object.entries(obj)`
- `Object.assign(target, source)` → 合并对象

2️⃣ 对象控制

- `Object.freeze()` → 冻结（不可修改）
- `Object.seal()` → 可改值不可增删
- `Object.defineProperty`

3️⃣ 原型相关

- `hasOwnProperty()`→ 方法返回一个布尔值，表示对象自有属性（而不是继承来的属性）中是否具有指定的属性
- `Object.create()`

###### 1. 简述 Object.defineProperty

Object.defineProperty() 静态方法会直接在一个对象上定义一个新属性，或修改其现有属性，可以定义属性的值、是否可修改、是否可枚举，以及设置 getter / setter。并返回此对象。
🧱 基本语法

```
Object.defineProperty(obj, prop, descriptor)
```

第三个参数 descriptor（属性描述符）是核心

🔑 两种描述符（面试重点）
1️⃣ 数据描述符

```
Object.defineProperty(obj, 'name', {
  value: 'anno',       // 该属性值的值
  writable: false,     // 是否可修改
  enumerable: true,    // 是否可枚举（遍历），不可枚举属性无法通过Object.keys获取到
  configurable: false  // 是否可删除 / 再配置
})
```

2️⃣ 存取描述符（getter / setter）

```
let value = ''

Object.defineProperty(obj, 'name', {
  get() {
    return value
  },
  set(newVal) {
    value = newVal
  }
})
```

> Object.defineProperty 会直接在一个对象上定义一个新属性，或修改其现有属性，可以定义属性的值、是否可修改、是否可枚举，以及设置 getter/setter 实现响应式监听，是 Vue2 响应式的核心实现

###### 2. Object.keys 与 Object.getOwnPropertyNames() 有何区别

- `Object.keys`：列出可枚举的属性值
- `Object.getOwnPropertyNames`:列出所有属性值(包括可枚举与不可枚举)

> _Object.defineProperty_ 中的选项 enumerable 可定义属性是否可枚举

##### 5.Promise（异步核心）

1️⃣ 实例方法

- `then()` ✨ 成功回调（支持链式调用，返回新的 Promise）

- `catch()` ❗ 失败回调（等价于 then(null, fn)，可捕获链式错误）

- `finally()` 🧩 最终执行（无论成功失败都会执行，不影响结果）

2️⃣ 静态方法

- `Promise.resolve()` ✅ 返回一个成功的 Promise（传 Promise 会原样返回）

- `Promise.reject()` ❌ 返回一个失败的 Promise

- `Promise.all()` 📦 全部成功才成功（有一个失败就直接失败，常用于并发请求）

- `Promise.race()` ⏱️ 谁先完成用谁，只看**谁先结束**（谁先 resolve / reject 就返回谁，常用于超时控制 / 抢结果场景）

- `Promise.allSettled()` 📊 全部执行完才返回（不管成功失败，返回状态数组）

- `Promise.any()` 🎯 任意一个成功即可（全部失败才报错）

###### 1. Promise.allSettled()

```
const promises = [
  Promise.resolve(1),
  Promise.reject('error'),
  Promise.resolve(3)
];

Promise.allSettled(promises).then(results => {
  console.log(results);
});
```

输出结果：

```
[
  { status: 'fulfilled', value: 1 },
  { status: 'rejected', reason: 'error' },
  { status: 'fulfilled', value: 3 }
]
```

> Promise.allSettled() 用于并发执行多个异步任务，并在所有任务都结束（无论成功或失败）后返回每个任务的结果状态，不会因为某个失败而中断。 它返回的是一个包含每个 Promise 执行结果的数组，每一项都有 status（fulfilled 或 rejected）以及对应的 value 或 reason，适合需要拿到全部结果的场景，比如批量请求。

#### 4. js 中什么是可选链操作符，如何访问数组

**可选链运算符（?.）**
用于访问对象的属性或调用函数。如果使用此运算符访问的对象或调用的函数是 undefined 或 null，则表达式会短路并计算为 undefined，而不是抛出错误。用于**安全访问对象属性**，避免出现 undefined / null 报错。

```javascript
const user = {};
user.profile.name; // ❌ 报错
user.profile?.name; // ✅ undefined（不会报错）
```

可选链同样可以用于数组：

```javascript
const arr = [1, 2, 3];

arr?.[0]; // 1
arr?.[10]; // undefined
```

#### 5. 前端中遇到过处理二进制的场景吗？

> 有的，比如文件上传下载、图片处理等场景，本质都是在处理二进制数据，前端通常基于 Blob、ArrayBuffer 和 FileReader 来操作，比如上传通过 FormData 发送 Blob，下载通过 Blob 配合 URL.createObjectURL 实现文件保存

##### 1.TypedArray

> TypedArray 是基于 ArrayBuffer 的类型化视图，用固定数据类型（如 Uint8、Float32）来高效读写和解析二进制数据。

##### 2. ArrayBuffer / TypedArray / Blob 三者关系

> ArrayBuffer 是底层二进制内存，TypedArray 是对其进行类型化读写的视图，而 Blob 是对二进制数据的封装，用于文件处理和网络传输。

#### 6. 什么是 Iterable 对象，与 Array 有什么区别？

> Iterable 是实现了 `Symbol.iterator` 方法的对象、可以被 `for...of` 遍历的对象；Array 是其中一种具体的数据结构，属于 Iterable 的子集。

#### 7. 解构赋值以下对象，他们的值是多少

##### 1.`const {a: aa, b } = {a: 3, b: 4} `

```
console.log(a);  // ❌ 报错：a is not defined
console.log(aa); // ✅ 3
console.log(b);  // ✅ 4
```

> a: aa 表示把属性 a 的值赋给变量 aa，不会创建变量 a，所以 a 是未定义，aa 是 3，b 是 4。

等价理解

```
const obj = { a: 3, b: 4 };

const aa = obj.a;
const b = obj.b;
```

##### 2. move

```
//这种写法x,y有默认值
function move({x = 0, y = 0} = {}) {
  return [x, y];
}
```

逻辑拆解
参数默认值：= {}（只有在参数是 undefined 时才用）
解构默认值：x = 0, y = 0（属性不存在才用）

```
move({ x: 3, y: 8 }); // [3, 8]
move({ x: 3 });       // [3, 0]
move({});             // [0, 0]
move();               // [0, 0]
```

```
//这种写法x,y没有默认值
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
```

逻辑拆解
参数默认值：= { x:0, y:0 }
❌ 没有解构默认值

```
move({ x: 3, y: 8 }); // [3, 8]
move({ x: 3 });       // [3, undefined]
move({});             // [undefined, undefined]
move();               // [0, 0]
```

> 参数默认值只在“没传参数”时生效，而解构默认值是在“属性为 undefined”时生效。
> 参数默认值解决“有没有传”，
> 解构默认值解决“有没有这个属性”

#### 8. Map 与 WeakMap 有何区别？

**核心区别总览（面试重点**

| 对比点      | Map                       | WeakMap                                                      |
| ----------- | ------------------------- | ------------------------------------------------------------ |
| key 类型    | 任意类型（对象 / 原始值） | **只能是对象**                                               |
| 引用类型    | 强引用                    | **弱引用**</br>WeakMap 的 key 是弱引用，不参与垃圾回收的可达性分析，因此当key没有其他强引用时，会被自动回收(GC)，从而避免内存泄漏 |
| 是否阻止 GC | ✅ 会                      | ❌ 不会                                                       |
| 是否可遍历  | ✅ 可以                    | ❌ 不可以                                                     |
| size 属性   | ✅ 有                      | ❌ 没有                                                       |
| 使用场景    | 通用键值存储              | **对象私有数据 / 避免内存泄漏**                              |

> - `Map`: 可使用任何数据类型作为 key，但因其在内部实现原理中需要维护两个数组，存储 key/value，因此垃圾回收机制无法回收
> - `WeakMap`: 只能使用引用数据类型作为 key。弱引用，不在内部维护两个数组，可被垃圾回收，但因此无法被遍历！即没有与枚举相关的 API，如 `keys`、`values`、`entries` 等

#### 9. 循环引用

##### 1. JS 如何检测到对象中有循环引用

**循环引用**是指对象的某个属性，经过一系列引用，最终又指回它自己。

```
const obj = {}
obj.self = obj

obj → obj
```

📌 为什么这是问题？

比如：

```
JSON.stringify(obj)
```

会直接报错：

```
TypeError: Converting circular structure to JSON
```

因为：

程序在递归遍历
永远走不出来（死循环）

**检测对象中有循环引用思路**：可以通过DFS遍历对象，用 WeakSet 记录访问过的对象，如果再次访问同一个引用，就说明存在循环引用

_手写代码_

```
function hasCycle(obj) {
  const visited = new WeakSet()

  function dfs(target) {
    if (typeof target !== 'object' || target === null) return false
    if (visited.has(target)) return true

    visited.add(target)

    for (let key in target) {
      if (dfs(target[key])) return true
    }

    return false
  }

  return dfs(obj)
}
```

##### 2. JS 深克隆时如何处理循环引用 (深拷贝的实现)

深克隆（deep clone）指的是 **复制一个对象及其所有子对象，使得原对象和新对象互不影响**。

为什么需要深拷贝？
深拷贝不会**污染原始数据**

如果对象内部存在循环引用,直接递归拷贝就会无限调用而导致栈溢出。

解决思路：

> 深拷贝时，如果对象存在循环引用，递归会无限执行。解决方法是使用 WeakMap 记录已经拷贝过的对象，当再次遇到同一个对象时直接返回之前的拷贝结果，从而避免死循环。

```
function deepClone(obj, map = new WeakMap()) {
  // 基本类型直接返回
  if (typeof obj !== 'object' || obj === null) return obj

  // 已经拷贝过 → 返回之前的 clone
  if (map.has(obj)) return map.get(obj)

  // 创建拷贝对象
  const clone = Array.isArray(obj) ? [] : {}

  // 记录映射关系
  map.set(obj, clone)

  // 递归拷贝属性
  for (const key of Object.keys(obj)) {
      clone[key] = deepClone1(obj[key], map);
}

  return clone
}
```

### 基础

#### 1. 什么是防抖和节流，他们的应用场景有哪些

防抖（debounce）和节流（throttle）都是用来**控制函数触发频率**的优化手段，主要用于解决高频事件导致的性能问题。

- **防抖**：防抖是指：在事件被触发后，延迟一段时间执行函数，如果这段时间内再次触发，则重新计时，避免事件被误伤触发多次。代码实现重在清零 clearTimeout。防抖可以比作等电梯，只要有一个人进来，就需要再等一会儿。业务场景有避免登录按钮多次点击的重复提交。
  应用场景
  - 🔍 搜索框输入（最经典）
  - 📱 输入验证（避免频繁请求）
  - 🪟 resize / scroll 结束后计算
  - 💬 自动保存（用户停止输入后触发

> 适用于“用户操作结束后才需要结果”的场景

- **节流**：控制流量，规定在一个时间间隔内，函数最多只执行一次，与服务器端的限流 (Rate Limit) 类似。代码实现重在开锁关锁 timer=timeout; timer=null。节流可以比作过红绿灯，每等一个红灯时间就可以过一批。
  应用场景
  - 🖱️ scroll 事件（页面滚动）
  - 📏 resize 持续触发
  - 🎮 鼠标移动（mousemove）
  - 🔘 按钮防止重复点击

> 适用于“持续触发但需要降低频率”的场景

**防抖 vs 节流**

| 对比点   | 防抖     | 节流     |
| -------- | -------- | -------- |
| 执行时机 | 最后一次 | 固定间隔 |
| 触发频率 | 低       | 稳定     |
| 适用场景 | 输入类   | 滚动类   |
| 用户体验 | 更“懒”   | 更“实时” |

它们的核心区别是：防抖关注“最后一次”，节流关注“执行频率”

#### 2. this
this 的核心思想

this 不是在函数定义时决定的，而是在函数调用时决定的。

> 简单记忆：谁调用，this 就指向谁。

##### 1. bind 与 call/apply 的区别是什么

`call / apply / bind` 都是用来改变函数执行时的 this 指向

- call / apply → 立即执行函数
- bind → 返回一个新函数，不会立即执行

call / apply 本质就是：

> 临时把函数挂到某个对象上执行一下
> 区别：call 是逐个传参，apply 是数组传参




##### 2. 关于 this 与包装对象，以下输出多少
```
function foo() {
  console.log(this);
}
 
foo.call(3);
//"use strict" 严格模式 → 3 任意值
// 非严格模式           → Number {3}  对象
```

#### 3. JavaScript 原型链

> 原型链的存在意义在于实现对象之间的属性共享和继承机制。
> JavaScript 中每个对象都有一个内部属性 `[[Prototype]]`，可以通过 ` __proto__ ` 访问。
> 当访问对象属性时，如果对象本身没有，就会沿着原型链向上查找，直到 Object.prototype 或 null 为止。
> 这种通过原型逐级查找属性的机制，就叫原型链。

| 维度           | prototype            | **`__proto__` (`[[Prototype]]`)** |
| -------------- | -------------------- | --------------------------------- |
| 本质           | 函数的属性           | 对象的属性                        |
| 作用           | 定义共享属性         | 指向原型对象                      |
| 谁拥有         | 只有函数有           | 所有对象都有                      |
| 是否参与原型链 | ❌ 不直接参与         | ✅ 直接参与                        |
| 指向关系       | 指向“未来实例的原型” | 指向“当前对象的原型”              |

> prototype 是一个“会被未来所有实例共享引用”的对象，
> 而 proto 是对象内部指向其原型的引用。
> 在创建实例时，实例的 proto 会指向构造函数的 prototype，从而形成原型链。

##### 1. typeof 与 instanceof 的区别

- `typeof` 用以判断基础数据类型 (null 除外)
  ✅ 优点

  - 能快速判断 基本数据类型
  - 不会报错（安全）

  ❌ 缺点

  - 无法区分对象类型

  ```
  typeof [] === typeof {}  // true
  ```

  - null 是 bug

  ```
  typeof null === "object"
  ```

- `instanceOf` 借助原型链判断复杂数据类型
  ✅ 优点

  - 能区分 具体对象类型
  - 适合判断：Array / Date / 自定义类

  

  ❌ 缺点

  - 不能判断基本类型 （基本类型没有原型链）

  ```
   123 instanceof Number   // false
  ```

  - 跨 iframe 会失效 (因为原型链不同)

    

用法：

```
   obj instanceof Constructor
```

本质是判断：

Constructor.prototype 是否在 obj 的**原型链**上

##### 2. js 中在 new 的时候发生了什么？ （四步）

`new` 操作符主要做了 4 件事：

> 1.  创建一个新的空对象
> 2.  将这个对象的 `[[Prototype]]` 指向构造函数的 `prototype`
> 3.  执行构造函数，并将 `this` 绑定到这个新对象
> 4.  根据返回值决定最终返回对象（对象则返回该对象，否则返回新对象）

手写 new

```
function myNew(fn, ...args) {
  // 第1步：创建对象
  const obj = {}

  // 第2步：连接原型
  obj.__proto__ = fn.prototype

  // 第3步：执行构造函数
  const result = fn.apply(obj, args)

  // 第4步：返回值处理
  return result instanceof Object ? result : obj
}
```

> new 的核心是构建一个基于原型链的实例对象。
> 它通过将新对象的 `[[Prototype]]` 指向构造函数的 `prototype`，
> 从而实现属性和方法的继承，同时通过 call/apply 绑定 this 执行初始化逻辑，
> 最终返回构造函数返回的对象或默认实例。

###### 1. 关于 new，判断以下代码输出

```javascript
function F () {
  this.a = 3;
  return 
    {a: 4}
}
 
const f = new F();
console.log(f.a);
```

`new F()` 背后做了 4 件事：

1. 创建一个新对象 `obj`
2. `obj.__proto__ = F.prototype`
3. 执行 `F.call(obj)`
4. **根据返回值决定最终结果**

```javaScript
this.a = 3;       // 给默认的 this 赋值（但可能被丢弃）
return { a: 4 };  // 返回的是一个对象！
```



##### 3. js 如何实现继承？

本质：
    让一个对象能访问另一个对象的属性和方法（通过**原型链**）

- 原型链继承（理解本质）
  ✅ 写法

  ```
  function Parent() {
    this.name = "parent"
  }
  
  Parent.prototype.say = function () {
    console.log("hello")
  }
  
  function Child() {}
  
  Child.prototype = new Parent()
  
  const c = new Child()
  c.say() // hello
  ```

  🔥 原理

  ```
  c
  ↓
  Child.prototype（= new Parent()）
  ↓
  Parent.prototype
  ↓
  Object.prototype
  ```

  ❌ 问题

  - 1️⃣ 所有实例共享引用类型属性（会互相污染）
  - 2️⃣ 不能给 Parent 传参

- 二、借用构造函数（解决参数问题）
  ✅ 写法

  ```
  function Parent(name) {
    this.name = name
  }
  
  Parent.prototype.say = function () {
    console.log("hello")
  }
  
  function Child(name) {
    Parent.call(this, name)
  }
  
  const c = new Child("Tom")
  console.log(c.name) // Tom
  ```

  🔥 原理

  - 把 Parent 的 this 绑定到 Child 实例上

  

  ❌ 问题

  - 方法不能复用（每个实例一份）

  

  ❗画出真实原型链

  ```
  c
  ↓
  Child.prototype
  ↓
  Object.prototype
  ↓
  null
  
  ```

 **Parent.prototype 完全不在链上** !



- 组合继承
  ✅ 写法

  ```
  function Parent(name) {
    this.name = name
  }
  
  Parent.prototype.say = function () {
    console.log(this.name)
  }
  
  function Child(name) {
    Parent.call(this, name) // 第一次调用
  }
  
  Child.prototype = new Parent() // 第二次调用 ❗
  Child.prototype.constructor = Child
  ```

  ✅ 优点

  - ✔ 可以传参
  - ✔ 方法复用


  ❌ 缺点（重点）

  - Parent 被调用了两次 ❌

- 寄生组合继承

  ```
  function Parent(name) {
    this.name = name
  }
  
  Parent.prototype.say = function () {
    console.log(this.name)
  }
  
  function Child(name) {
    Parent.call(this, name) // 继承属性
  }
  
  // ⭐ 核心：只继承原型
  Child.prototype = Object.create(Parent.prototype)
  
  // ⭐ 修复 constructor
  Child.prototype.constructor = Child
  ```

   为什么更好？

  -  ✔ 只调用一次 Parent
  -  ✔ 原型链干净
  -  ✔ 性能更好

- ES6 class 继承（现代写法）

  ```
  class Parent {
  constructor(name) {
    this.name = name
  }
  
  say() {
    console.log(this.name)
    }
  }
  
  class Child extends Parent {
    constructor(name) {
    super(name)
    }
  } 
  ```

   本质

   - extends = 建立原型链
   - super = 调用父类构造函数
   - ES6 的 class 本质上是对 ES5 原型链继承的语法封装，尤其是 extends 实现的就是寄生组合继承。


总结：

>JavaScript 中继承的本质是通过原型链实现的,目的为让一个对象能访问另一个对象的属性和方法
>常见方式有原型链继承、构造函数继承、组合继承以及**寄生组合继承**。
>其中寄生组合继承是最推荐的实现方式，通过 Object.create 建立原型链，避免了多次调用父类构造函数的问题。
>在 ES6 中可以使用 class 和 extends 语法糖实现继承，但底层仍然是基于原型链。

#### 4. 事件循环（Event Loop）与 宏任务 / 微任务


##### 1. 请简述一下 （事件循环）event loop

**Event Loop（事件循环）是 JavaScript 处理异步任务的机制。**

因为 JavaScript 是**单线程**的，为了避免阻塞，它把任务分为：

- **同步任务（主线程执行）**
- **异步任务（交给浏览器/Node 处理）**

异步任务完成后会进入不同的队列，最终由 Event Loop 调度执行。

执行流程是：

1. 执行当前调用栈中的同步代码（Call Stack）
2. 清空后，优先执行 **微任务队列（Microtask Queue）**
3. 再执行一个 **宏任务（Macrotask）**
4. 循环往复（这就是 Event Loop）

![image](https://user-images.githubusercontent.com/19162008/109372242-850c0980-78e3-11eb-8fe6-ecb15fa5e480.png)

**核心结构**

你可以稍微展开一点，说出这几个关键词：

1️⃣ 调用栈（Call Stack）

- 执行同步代码
- 先进后出（函数调用栈）

------

2️⃣ 微任务（Microtask）⭐重点

常见：

- `Promise.then`
- `catch / finally`
- `MutationObserver`
- `queueMicrotask`

👉 特点：**优先级高于宏任务**

------

3️⃣ 宏任务（Macrotask）

常见：

- `setTimeout`
- `setInterval`
- `setImmediate`（Node）
- `I/O`







**执行顺序**：
⭐

> 同步 → 微任务 → 宏任务（循环执行）



**总结：**

>代码执行时，先进入调用栈执行同步代码；
>遇到异步任务，会交给 Web API 处理；
>当异步任务完成后，会被放入任务队列；
>Event Loop 会在调用栈为空时：
>
>- 先清空所有微任务
>- 再取一个宏任务执行
>  然后不断循环。



##### 2. 以下输出顺序多少?（事件循环的应用）

###### 1.
```javascript
setTimeout(() => console.log(0));
new Promise((resolve) => {
  console.log(1);
  resolve(2);
  console.log(3);
}).then((o) => console.log(o));
 
new Promise((resolve) => {
  console.log(4);
  resolve(5);
})
  .then((o) => console.log(o))
  .then(() => console.log(6));
```

```
1 3 4 2 5 6 0
```



```
┌───────────────────────────┐
│  执行同步代码             │
└───────────┬───────────────┘
            ↓
┌───────────────────────────┐
│  微任务队列全部清空       │ ← Promise.then、queueMicrotask
└───────────┬───────────────┘
            ↓
┌───────────────────────────┐
│  取一个宏任务执行         │ ← setTimeout、setInterval
└───────────┬───────────────┘
            ↓
┌───────────────────────────┐
│  微任务队列全部清空       │ ← 新产生的微任务
└───────────┬───────────────┘
            ↓
          循环...
```


###### 2. 
```javascript
console.log('start')

setTimeout(() => {
  console.log('timeout1')

  Promise.resolve().then(() => {
    console.log('then1')
  })
}, 0)

Promise.resolve().then(() => {
  console.log('then2')

  setTimeout(() => {
    console.log('timeout2')
  }, 0)
})

console.log('end')
```

```
start
end
then2
timeout1
then1
timeout2
```

###### 3. 
```
setTimeout(() => console.log("A"));
 
Promise.resolve().then(() => console.log("B"));
 
console.log("C");
```

```
C
B
A
```

###### 4.
```
setTimeout(() => {
  console.log("A");
  Promise.resolve().then(() => {
    console.log("B");
  });
}, 1000);
 
Promise.resolve().then(() => {
  console.log("C");
});
 
new Promise((resolve) => {
  console.log("D");
  resolve("");
}).then(() => {
  console.log("E");
});
 
async function sum(a, b) {
  console.log("F");
}
 
async function asyncSum(a, b) {
  await Promise.resolve();
  console.log("G");
  return Promise.resolve(a + b);
}
 
sum(3, 4);
asyncSum(3, 4);
console.log("H");
```
```
D
F
H
C
E
G
A
B
```
1️⃣ Promise 构造函数是同步执行
```
(resolve) => {
  console.log("D");
  resolve("");
}
立即执行 → 直接输出：D
```

2️⃣ async function sum

🔹 本质
- async 函数 ≈ 返回 Promise 的函数
内部没有 await → 全部同步执行

3️⃣ 🚨重点：asyncSum
```
async function asyncSum(a, b) {
  await Promise.resolve();
  console.log("G");
  return Promise.resolve(a + b);
}
```

🔥 结论（必须记住）

> await = Promise.then 的语法糖
```
await Promise.resolve();
console.log("G");

//等价于

Promise.resolve().then(() => {
  console.log("G");
});
```

###### 5.
```
Promise.resolve()
  .then(() => {
    console.log(0);
    return Promise.resolve(4);
  })
  .then((res) => {
    console.log(res);
  });
 
Promise.resolve()
  .then(() => {
    console.log(1);
  })
  .then(() => {
    console.log(2);
  })
  .then(() => {
    console.log(3);
  })
  .then(() => {
    console.log(5);
  })
  .then(() => {
    console.log(6);
  });
```



```
微任务队列变化：

初始：        [A1, B1]
执行A1(输出0)：[B1, PRTJ]          ← 产生第1个额外微任务
执行B1(输出1)：[PRTJ, B2]
执行PRTJ：     [B2, resolve4]      ← 产生第2个额外微任务
执行B2(输出2)：[resolve4, B3]
执行resolve4： [B3, A2]            ← A2 终于入队！
执行B3(输出3)：[A2, B4]
执行A2(输出4)：[B4]
执行B4(输出5)：[B5]
执行B5(输出6)：[]

最终输出：0 → 1 → 2 → 3 → 4 → 5 → 6
```
**核心原理**：当 .then() 回调返回一个 Promise 时，根据 ECMA 规范会产生 2 个额外的微任务：
1. 第 1 个额外微任务（PromiseResolveThenableJob）：调用返回的 Promise 的 .then(resolve, reject)
2. 第 2 个额外微任务：执行 resolve(4)，用拿到的值去 resolve 外层 Promise

类似题目：
```
new Promise((resolve) => {
  let resolvedPromise = Promise.resolve();
  resolve(resolvedPromise);
}).then(() => {
  console.log("resolvePromise resolved");
});
 
Promise.resolve()
  .then(() => {
    console.log("promise1");
  })
  .then(() => {
    console.log("promise2");
  })
  .then(() => {
    console.log("promise3");
  });
```
输出结果：
```
promise1
promise2
resolvePromise resolved
promise3
```

> 面试的时候只需要简单记住：如果resolve()的括号内的结果是一个promise 或 .then() 的回调中 return 了一个 Promise 实例，会多执行两个micro task



**总结**

>在 Event Loop 中，同步代码先执行，然后清空微任务队列；执行一个宏任务后，又会立即清空微任务队列。在执行过程中，无论是宏任务还是微任务，都可以继续产生新的微任务或宏任务，其中微任务会在当前轮立即执行，而宏任务会进入下一轮循环







#### 5. node/v8 相关机制

##### 1. 简述 node/v8 中的垃圾回收机制

核心思想是：

 **基于分代（Generational）+ 标记清除（Mark-Sweep）为主的垃圾回收机制**



**一、内存分代模型（重点）**

V8 把内存分成两大块：

1️⃣ 新生代（New Space）

特点：

- 存放生命周期短的对象
- 回收频率高
- 空间小（默认几 MB）

使用算法：

👉 **Scavenge（复制算法）**



机制：

- 分成两个空间：From / To
- 活对象复制到 To
- 清空 From
- 交换角色



✅ 优点：

- 快（只处理存活对象）
- 无内存碎片

❗缺点：

- 空间利用率只有一半



2️⃣ 老生代（Old Space）

特点：

- 生命周期长的对象
- 空间大
- 回收频率低但耗时长



使用算法：

**（1）标记清除（Mark-Sweep）**

流程：

1. 标记所有可达对象
2. 清除未标记对象

❗问题：

- 会产生**内存碎片**



💡 优化：**懒惰清除（Lazy Sweeping）**

标记阶段完成后，**不立即清除所有垃圾**，而是按需清除：

-  当某个内存页需要分配新对象时，才对该页执行清除
- 将清除工作分散到后续的程序执行过程中



✅ 优点：

- 避免一次性清除带来的长时间停顿
- 与增量标记配合，进一步减少 Stop-the-World 时间



❗注意： 

- 仍然无法解决内存碎片问题（这是标记压缩的职责）



**（2）标记压缩（Mark-Compact）**

优化版：

👉 在清除后 **把存活对象往一端移动**

✅ 优点：

- 解决碎片问题
- 提高内存利用率

❗缺点：

- 需要移动对象 → 更慢



**二、关键机制 **

**1️⃣ 晋升机制（Promotion）**

对象从新生代进入老生代的条件：

- 经历多次 GC 仍然存活
- 或新生代空间不足



**2️⃣ Stop-The-World**

👉 GC 时会暂停 JS 执行

但 V8 做了优化：

- 增量标记（Incremental Marking）增量标记是为了减少垃圾回收对应用程序的暂停时间（Stop-the-World）而引入的。V8会将标记过程拆分为多个小步骤，插入到程序执行中，这样就不会一次性占用大量时间，从而提高应用的响应性。
- 并发标记（Concurrent Marking）

👉 **减少卡顿**



**3️⃣ 可达性分析（核心原理）**

GC 判断对象是否需要回收：

👉 **从 Root（全局对象、栈等）出发，能访问到的就不回收**



三 、Node 场景补充

在 Node.js 中需要注意：

 内存泄漏常见原因

- 全局变量未释放
- 闭包引用
- 定时器未清除
- 缓存无限增长



总结：

> Node.js 的垃圾回收由 V8 引擎负责，核心是**分代回收机制**，把内存分成新生代和老生代分别处理。
>
> 新生代存放生命周期短的对象，用的是 **Scavenge 复制算法**——把内存分成 From 和 To 两块，每次 GC 只把存活对象复制到 To 空间，然后直接清空 From。这样回收很快、也没有内存碎片，代价是内存利用率只有一半。
>
> 老生代存放生命周期长的对象，主要用**标记清除**和**标记压缩**。标记清除是从 GC Root 出发做可达性分析，标记所有存活对象，再清掉没被标记的，问题是会产生内存碎片。标记压缩是在清除之后把存活对象往一端移动，解决碎片问题，但代价是更慢。在清除阶段，V8 还引入了 **Lazy Sweeping 懒惰清除**。标记完成后不会一次性清理所有垃圾，而是等到需要分配内存时才按需清理，把清除工作分散开，避免一次性阻塞太久。
>
> GC 期间 JS 会暂停执行，也就是 Stop-The-World。V8 为了减少卡顿做了两个优化：一是**增量标记**，把标记过程拆成小步骤插入到程序执行中间；二是**并发标记**，把标记放到后台线程去做，进一步降低主线程的停顿。
>
> 新生代的对象如果经历多次 GC 仍然存活，或者空间不够了，就会被**晋升到老生代**。
>
> 在 Node.js 场景下，因为 JS 是单线程的，GC 停顿会直接影响事件循环，所以需要注意内存泄漏问题，比如全局变量、闭包意外持有引用、或者忘记清理的定时器，这些都会导致对象无法被回收，最终把老生代撑大。

##### 2. v8 是如何执行一段 JS 代码的
🎯 核心知识点总结
V8 执行 JS 代码经历：**源码 → 词法/语法分析 → AST → 解释器生成字节码 → 执行（同时监控热点代码）→ 编译器优化生成机器码**

展开流程图：
```
JS源码
 ↓
Parser（解析）
 ↓
AST（抽象语法树）
 ↓
Ignition（解释器 → 生成字节码）
 ↓
执行字节码
 ↓
TurboFan（热点代码优化为机器码）
 ↓
执行优化后的机器码
 ↓
GC（垃圾回收）
```
>V8 执行 JS 代码分为四个阶段：
>首先是解析阶段，V8 会对源码进行词法分析和语法分析，生成 AST。
>
>然后由 Ignition 解释器将 AST 编译成字节码并执行。
>
>在执行过程中，如果某段代码被频繁调用，V8 会将其标记为热点代码，并通过 TurboFan 将字节码优化为机器码，以提升执行效率。
>
>如果运行过程中类型发生变化，还可能触发反优化，回退到字节码执行。
>
>同时，V8 使用分代垃圾回收机制管理内存，新生代采用 Scavenge 算法，老生代采用标记清除、标记整理，并结合 Lazy Sweeping 来减少停顿时间。
>

#### 6. 作用域
##### 1. 块级作用域
🎯 核心知识点总结

作用域是变量的可访问范围。块级作用域是 ES6 新增的概念，使用 let/const 声明的变量只在 **{} 块内**有效。而 var **没有块级作用域**，只有函数作用域或全局作用域，会变量提升且可重复声明。

- var 是函数作用域，存在变量提升并初始化为 undefined，可以重复声明，会挂载到全局对象；
- let 和 const 是块级作用域，存在暂时性死区，不允许重复声明；
- const 还要求必须初始化且不允许重新赋值，但对于引用类型只保证引用不变。


>块级作用域指的是变量只在 {} 代码块内部生效。
在 ES6 之前，JavaScript 只有 var，它是函数作用域，会导致变量污染和循环变量泄漏的问题。
>
>ES6 引入了 let 和 const 来支持块级作用域，使变量只在当前代码块内有效。
>
>相比 var，let 的主要区别有：
>
>- 支持块级作用域
>- 不存在变量提升（有暂时性死区）
>- 不允许重复声明
>
>特别是在 for 循环中，let 每次循环都会创建一个新的变量实例，而 var 是共享同一个变量。


##### 2. 暂时性死区
核心知识点总结：
let / const 声明的变量会在**块作用域顶部被提升**，但不会**被初始化**，从块顶部到声明语句之间的区域，就叫**暂时性死区**，在此期间访问变量会直接报错。
```
{
  // ← 死区开始（b 已存在作用域，但未初始化）
  console.log(b); // ❌ ReferenceError
  
  let b = 2; // ← 死区结束
  
  console.log(b); // ✅ 2
}
```
###### 1. 关于暂时性死域，判断以下代码输出
第一段代码如下
```
var a = 3;
let a;
```
第二段代码如下
```
var x = 3;
 
function foo (x=x) {
  //         ↑   ↑
  //    参数x  默认值x
  //    
  // 函数参数有自己独立的作用域
  // 默认值右边的 x，找的是「参数作用域」里的 x
  // 但此时参数 x 还没初始化完成 → 触发暂时性死区 → 报错！
}
 
foo()
```

**两段代码核心对比**

|                  | 第一段               | 第二段                           |
| ---------------- | -------------------- | -------------------------------- |
| 错误类型         | `SyntaxError`        | `ReferenceError`                 |
| 报错时机         | 编译阶段（还没执行） | 运行时（调用函数时）             |
| 触发原因         | `let` 不允许重复声明 | 参数默认值引用自身触发暂时性死区 |
| 外层变量有用吗？ | -                    | ❌ 被参数作用域遮蔽，无法访问     |

###### 2. 关于词法作用域，判断以下代码输出

```javascript
var scope = "global scope";
function checkScope() {
  var scope = "local scope";
  function f() {
    return scope;
  }
  return f;
}
 
checkScope()();
// local scope
```

> 由于 js 为词法作用域(Lexical Scope)，访问某个变量时，先在当前作用域中查找，如果查找不到则在嵌套作用域中查找，直到找到。如果找不到，则报 ReferenceError。

##### 3. 变量提升
核心知识点总结:
JavaScript 在代码执行之前，会先扫描整个作用域，把变量声明和函数声明提到作用域顶部处理，这个过程叫变量提升

> 变量提升是指 JS 引擎在执行代码前，会先进行"编译"阶段，将 var 声明的变量和 function 声明的函数，提升到当前作用域的顶部。var 只提升声明，不提升赋值；函数声明整体提升；let/const 虽然也提升但不初始化，进入暂时性死区。

##### 4. 关于块级作用域，以下代码输出多少，在何时间输出?
```
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 1000);
}
```

> 这段代码会在 1 秒后几乎同时输出 5 个 5。
>
> 原因是 var 声明的变量没有块级作用域，会被提升到全局，整个循环中只有一个 i。循环会立即执行完，i 的最终值变成 5。而 5 个 setTimeout 的回调函数都是在 1 秒后才执行，此时它们引用的是同一个全局 i，所以全部输出 5。

解决方法：
用 let 替代 var，因为 let 有**块级作用域**，每次循环都会创建一个新的 i，每个 setTimeout 捕获的是各自的 i。

#### 7. 什么是闭包？闭包的应用有哪些地方
 🔑 核心知识点总结
**闭包**= 函数 + 函数能访问的外部作用域变量的组合
```
function outerFunction() {
  let outerVariable = '我是一个外部变量'; // ← 这个变量被"封存"了
 
  function innerFunction() {
    console.log(outerVariable); // ← 内部函数持有对它的引用
  }
 
  return innerFunction; // 将内部函数返回
}
 
const myClosure = outerFunction(); // 执行outerFunction，它返回innerFunction
myClosure(); // 输出: "我是一个外部变量"
// outerFunction 已经执行完了，但 outerVariable 没有被销毁！
```
>outerFunction 执行完后，按理说 outerVariable 应该被垃圾回收，但因为 innerFunction 还持有对它的引用，所以它被保留在内存中 —— 这就是闭包的核心。
**本质**：函数执行完后，其作用域本应被销毁，但如果内部函数**持有对外部变量的引用**，该作用域就会被保留在内存中。

1. **数据私有化 / 封装**
模拟私有变量，外部无法直接访问内部状态：
```
function createCounter() {
  let count = 0; // 私有变量，外部无法直接访问

  return {
    increment() { count++; },
    decrement() { count--; },
    getCount()  { return count; }
  };
}

const counter = createCounter();
counter.increment();
counter.increment();
console.log(counter.getCount()); // 2
console.log(counter.count);      // undefined ← 无法直接访问！
```
2. **防抖 / 节流（Debounce / Throttle）**
两者都通过闭包保存**定时器引用**或**时间戳**来控制函数执行频率：
防抖 —— 最后一次触发后才执行
```
function debounce(fn, delay) {
  let timer = null; // 定时器被闭包保存，多次调用共享同一个 timer ✅

  return function(...args) {
    clearTimeout(timer);          // 每次触发先清掉上一个定时器
    timer = setTimeout(() => {
      fn.apply(this, args);       // delay 毫秒内没有新触发，才真正执行
    }, delay);
  };
}

// 搜索框输入：用户停止输入 500ms 后才发请求
const searchInput = document.getElementById('search');
searchInput.addEventListener('input', debounce((e) => {
  console.log('发起搜索:', e.target.value);
}, 500));
```

节流 —— 固定时间间隔内只执行一次
```
function throttle(fn, interval) {
  let lastTime = 0; // 上次执行时间被闭包保存 ✅

  return function(...args) {
    const now = Date.now();

    if (now - lastTime >= interval) {
      lastTime = now;             // 更新时间戳
      fn.apply(this, args);       // 距离上次执行已超过间隔，允许执行
    }
  };
}

// 滚动事件：每 200ms 最多执行一次
window.addEventListener('scroll', throttle(() => {
  console.log('滚动位置:', window.scrollY);
}, 200));
```

3. 函数柯里化
函数柯里化（Currying）是一种将多个参数的函数转换为一系列接受单个参数的函数的过程。举个简单的例子，我们有一个原始函数add(a, b, c)，我们可以将它柯里化为addCurried(a)(b)(c)的形式
```
//柯里化前
function add(a, b, c) {
  return a + b + c;
}
console.log(add(1, 2, 3)); //6
//柯里化后
function addCurried1(a) {
  return function (b) {
    return function (c) {
      return a + b + c;
    };
  };
}
//箭头函数简写
const addCurried2 = (a) => (b) => (c) => a + b + c;
console.log(addCurried1(1)(2)(3)); //6
console.log(addCurried2(1)(2)(3)); //6
```

4. 函数工厂（批量生成函数）
根据参数生成定制化函数：
```
function createMultiplier(multiplier) {
  return (num) => num * multiplier; // multiplier 被闭包保存
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```


5.  缓存 / 记忆化（Memoization）
将计算结果缓存，避免重复计算：
```
function createMemoize(fn) {
  const cache = {}; // 缓存对象被闭包保存

  return function(n) {
    if (cache[n] !== undefined) {
      console.log('从缓存获取');
      return cache[n];
    }
    console.log('计算中...');
    cache[n] = fn(n);
    return cache[n];
  };
}

// 模拟一个"慢计算"：计算平方
const memoSquare = createMemoize((n) => n * n);

memoSquare(4);  // 计算中...     → 16
memoSquare(4);  // 从缓存获取   → 16 ✅
memoSquare(5);  // 计算中...     → 25
memoSquare(5);  // 从缓存获取   → 25 ✅

第一次调用 memoSquare(4)  →  cache 里没有  →  计算并存入 cache
第二次调用 memoSquare(4)  →  cache 里有了  →  直接返回，跳过计算
```

总结：

>闭包概念：闭包是**函数 + 它能访问的外部变量**的组合。
>
>原理：正常情况下，一个函数执行完毕，内部变量就会被销毁。但如果内部有一个函数引用了这些变量，并且被返回到外部，那这些变量就不会被垃圾回收，会一直保留在内存中，这就形成了闭包。
>
>闭包在实际开发中有很多应用场景：
>第一个是数据私有化。我们可以用闭包模拟私有变量。
>第二个是函数工厂。利用闭包保存参数，批量生成定制化函数。
>第三个是缓存记忆化。在函数外层用闭包保存一个 cache 对象，每次执行前先查缓存，有就直接返回，没有才计算并存入缓存。这样相同的入参只计算一次，后续全部走缓存，性能提升很明显。
>第四个是柯里化。把一个多参数函数拆成多个单参数函数链式调用，每一层都用闭包把已传入的参数保存下来，等参数凑齐了才真正执行。实际业务中常用来固定某些通用参数，比如固定请求的 baseURL，或者固定权限校验的角色。
>第五个是防抖和节流，这是我觉得最典型的场景。防抖是用闭包保存一个 timer，每次触发先清掉上一个定时器再重新计时，只有用户停止操作后才真正执行，适合搜索框输入。节流是用闭包保存 lastTime，每次触发对比当前时间和上次执行时间的差值，超过间隔才允许执行，适合滚动监听。两者本质都是利用闭包跨调用共享同一份状态。

#### 8. 类型转换

##### 1.  == 和 === 的区别
-----
核心区别一句话
- `===`  严格相等：类型 + 值 必须都相同，绝不转换
- `==`   宽松相等：类型不同时，会先做隐式类型转换，再比较

```
1 === '1'  // false ← 类型不同，直接 false
1 == '1'   // true  ← '1' 转成数字 1，再比较 ✅
```

`==` 的隐式转换规则（重点）
① null == undefined   → true（特殊规定，记住就好）
  null == 其他任何值 → false
② NaN == 任何值       → false
③ 布尔值 == 其他      → 布尔值先转数字，再继续比较
④ 字符串 == 数字      → 字符串转数字，再比较
⑤ 对象 == 原始值      → 对象先转原始值（ToPrimitive），再比较
⑦ {} 转原始值是 '[object Object]' 再转 NaN
常见题型
```
 [] == false; // true
![] == false; // true
 []  == ![]   // true
 {} == false  // false
 {} == []     // false
 ⬆️
 // 两边都是对象，根据 == 规则：
对象和对象比较，直接比较引用地址，不做类型转换
({}) 和 [] 是两个不同的对象，地址不同
```

##### 2. 关于类型转化，判断以下代码输出
```
Boolean(new Boolean(false)); // true
Boolean(document.all);       // false
 
[] == "";                    // true
[3] == 3;                    // true
[] == false;                 // true
42 == true;                  // false
```
```
// 原始值
false                    // 原始布尔值，false
Boolean(false)           // false

// 对象
new Boolean(false)       // 布尔包装对象，是个对象！
Boolean(new Boolean(false)) // true ← 对象转布尔永远 true
typeof new Boolean(false)   // 'object' ← 证明它是对象不是原始值

>💡 口诀：new 一下就变成对象，对象转布尔值永远是 true，内容是什么无所谓。
```

#### 9. 虚拟列表
>虚拟列表是一种长列表性能优化技术。核心问题是，如果直接渲染上万条数据，会创建大量 DOM 节点，导致首次渲染卡顿、内存占用高、滚动掉帧。
>
>核心思想是"可视区域渲染"——用户一次只能看到一屏内容，所以只渲染视口内的几十个节点，滚动时动态替换内容。
>
>实现原理主要是三步：
>
>第一，用一个空的占位元素撑起完整高度，保证滚动条正常，总高度等于数据总数乘以每项高度。
>
>第二，监听滚动事件，计算出当前应该显示的起始索引和结束索引，然后从数据里 slice 出可见部分。
>
>第三，把真实渲染的节点用 transform: translateY(offset) 定位到正确位置。用 transform 而不是 top 是因为 transform 走 GPU 合成层，不触发重排。
>
>可使用 `react-window` 或 `@tanstack/react-virtual` 这样的成熟库