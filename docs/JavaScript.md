# JavaScript

### 常见面试问题

### API

#### 1.JavaScript中基本数据类型

- number
- bigint: **这个常常会忽略，最新加入的**
- string
- undefined
- null
- symbol
- bool

#### 2.在 js 中如何把类数组转化为数组

类数组（array-like）指的是：

- 有 `length` 属性
- 可以通过下标访问（如 `obj[0]`）
- **但没有数组的方法**（如 `map / filter / push`）

常见例子：

- `arguments`
- `NodeList`（如 `document.querySelectorAll`）
- `HTMLCollection`

>Array.from(arrayLike);
>
>const arr = [...arrayLike];
>
>Array.prototype.concat.apply([], arrayLike);
>
>
>
>const arr = [];
>for (let i = 0; i < arrayLike.length; i++) {
>  	arr.push(arrayLike[i]);
>}

#### 3.Number、String、Array、Object、Promise API

##### 1.**Number（数字）**

1️⃣ 静态方法（直接用 Number.xxx）

- `Number.isNaN(value)` → 判断是不是 NaN（比 isNaN 更严格）
- `Number.isFinite(value)` → 是否是有限数
- `Number.parseInt()` / `Number.parseFloat()` →字符串转数字

2️⃣ 实例方法（num.xxx）

- `toFixed(n)` → 保留 n 位小数
- `toPrecision(n)` → 指定精度
- `toString()` → 转字符串



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
  - `trim()`  去空格
  - `toUpperCase()` / `toLowerCase()`
- 转换类：
  - `split()`  字符串 → 数组
  - `replace()`  替换



##### 3.**Array（数组，重点！）**

1️⃣ 增删改查

- `push()` / `pop()`
- `shift()` / `unshift()`
- `splice()`

2️⃣ 遍历类（非常重要）

- `map()` 👉 映射
- `filter()` 👉 过滤
- `reduce()` 👉 聚合（面试重点！）
- `forEach()`
- join

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
>  `forEach` 没有返回值，适合做“副作用操作”。

###### ② reduce 能做什么？

✅ 面试标准回答

> `reduce` 本质是“把数组压缩成一个值”，可以用来做累加、计数、去重、扁平化等。



##### **4.Object（对象）**

1️⃣ 静态方法

- `Object.keys(obj)` 👉 返回 key 数组
- `Object.values(obj)`
- `Object.entries(obj)`
- `Object.assign(target, source)` 👉 合并对象

2️⃣ 对象控制

- `Object.freeze()` 👉 冻结（不可修改）
- `Object.seal()` 👉 可改值不可增删

3️⃣ 原型相关

- `hasOwnProperty()`
- `Object.create()`



##### **5.Promise（异步核心**）

1️⃣ 实例方法

- `then()` 👉 成功回调
- `catch()` 👉 失败回调
- `finally()` 👉 最终执行

2️⃣ 静态方法

- `Promise.resolve()`
- `Promise.reject()`
- `Promise.all()` 👉 全部成功才成功
- `Promise.race()` 👉 谁先完成用谁
- `Promise.allSettled()` 👉 全部执行完（不管成功失败）
- `Promise.any()` 👉 任意一个成功
