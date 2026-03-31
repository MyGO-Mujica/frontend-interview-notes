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

| 属性                      | 值          | 含义                              | 原理                                                                                  |
| ------------------------- | ----------- | --------------------------------- | ------------------------------------------------------------------------------------- |
| `Number.MAX_VALUE`        | ≈ 1.79e+308 | JS 能表示的 **最大有限数**        | JS 的 Number 是 **64 位浮点数（IEEE 754）**                                           |
| `Number.MAX_SAFE_INTEGER` | 2^53 - 1    | 可以被“精确表示”的最大整数        | JS 的 Number 只有 53 位有效精度（52 位尾数 + 1 位隐含位），所以最多能精确表示到 2⁵³−1 |
| `Number.EPSILON`          | 2^-52       | 1 和比 1 大的最小浮点数之间的差值 | 尾数只有 52 位(小数点后最多 52 位)                                                    |

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
- `shift()` / `unshift()`
- `splice()`

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

###### ① map 和 forEach 区别

✅ 面试标准回答（先说结论）

> `map` 会返回一个新数组，适合做“数据转换”；
> `forEach` 没有返回值，适合做“副作用操作”。

###### ② reduce 能做什么？

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

##### **5.Promise（异步核心**）

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

** 核心区别总览（面试重点）**

| 对比点      | Map                       | WeakMap                                                                                                                           |
| ----------- | ------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| key 类型    | 任意类型（对象 / 原始值） | **只能是对象**                                                                                                                    |
| 引用类型    | 强引用                    | **弱引用**</br>WeakMap 的 key 是弱引用，不参与垃圾回收的可达性分析，因此当key没有其他强引用时，会被自动回收(GC)，从而避免内存泄漏 |
| 是否阻止 GC | ✅ 会                     | ❌ 不会                                                                                                                           |
| 是否可遍历  | ✅ 可以                   | ❌ 不可以                                                                                                                         |
| size 属性   | ✅ 有                     | ❌ 没有                                                                                                                           |
| 使用场景    | 通用键值存储              | **对象私有数据 / 避免内存泄漏**                                                                                                   |

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
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
        clone[key] = deepClone1(obj[key], map);
    }
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

#### 2. bind 与 call/apply 的区别是什么
`call / apply / bind` 都是用来改变函数执行时的 this 指向
- call / apply → 立即执行函数
- bind → 返回一个新函数，不会立即执行

call / apply 本质就是：

> 临时把函数挂到某个对象上执行一下
> 区别：call 是逐个传参，apply 是数组传参

#### 3. JavaScript 原型链

> 原型链的存在意义在于实现对象之间的属性共享和继承机制。
>JavaScript 中每个对象都有一个内部属性 `[[Prototype]]`，可以通过 ` __proto__ ` 访问。
> 当访问对象属性时，如果对象本身没有，就会沿着原型链向上查找，直到 Object.prototype 或 null 为止。
> 这种通过原型逐级查找属性的机制，就叫原型链。

| 维度           | prototype            | **`__proto__` (`[[Prototype]]`)**            |
| -------------- | -------------------- | -------------------- |
| 本质           | 函数的属性           | 对象的属性           |
| 作用           | 定义共享属性         | 指向原型对象         |
| 谁拥有         | 只有函数有           | 所有对象都有         |
| 是否参与原型链 | ❌ 不直接参与        | ✅ 直接参与          |
| 指向关系       | 指向“未来实例的原型” | 指向“当前对象的原型” |

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
  - ✔ 原型链干净
  - ✔ 性能更好

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
常见方式有原型链继承、组合继承以及**寄生组合继承**。
其中寄生组合继承是最推荐的实现方式，通过 Object.create 建立原型链，避免了多次调用父类构造函数的问题。
在 ES6 中可以使用 class 和 extends 语法糖实现继承，但底层仍然是基于原型链。

