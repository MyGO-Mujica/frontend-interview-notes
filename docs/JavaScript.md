# JavaScript

### 常见面试问题

### API

#### 1.JavaScript中基本数据类型

- number
- bigint: **这个常常会忽略，最新加入  在 JavaScript 中，BigInt 是一种数字数据类型，它可以表示的整数**。 
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

```javascript
Array.from(arrayLike);

const arr = [...arrayLike];

Array.prototype.concat.apply([], arrayLike);



const arr = [];
for (let i = 0; i < arrayLike.length; i++) {
	arr.push(arrayLike[i]);
}
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

- `Object.keys(obj)`  → 返回 key 数组
- `Object.values(obj)`
- `Object.entries(obj)`
- `Object.assign(target, source)`  → 合并对象

2️⃣ 对象控制

- `Object.freeze()`  → 冻结（不可修改）
- `Object.seal()`  → 可改值不可增删

3️⃣ 原型相关

- `hasOwnProperty()`
- `Object.create()`



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

###### Promise.allSettled() 
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
>  Promise.allSettled() 用于并发执行多个异步任务，并在所有任务都结束（无论成功或失败）后返回每个任务的结果状态，不会因为某个失败而中断。 它返回的是一个包含每个 Promise 执行结果的数组，每一项都有 status（fulfilled 或 rejected）以及对应的 value 或 reason，适合需要拿到全部结果的场景，比如批量请求。 

#### 4. js 中什么是可选链操作符，如何访问数组

**可选链运算符（?.）**
  用于访问对象的属性或调用函数。如果使用此运算符访问的对象或调用的函数是 undefined 或 null，则表达式会短路并计算为 undefined，而不是抛出错误。用于**安全访问对象属性**，避免出现 undefined / null 报错。 

```javascript
const user = {}
user.profile.name // ❌ 报错
user.profile?.name // ✅ undefined（不会报错）
```



可选链同样可以用于数组：

```javascript
const arr = [1, 2, 3]

arr?.[0]  // 1
arr?.[10] // undefined
```

####  5. 前端中遇到过处理二进制的场景吗？
 >  有的，比如文件上传下载、图片处理等场景，本质都是在处理二进制数据，前端通常基于 Blob、ArrayBuffer 和 FileReader 来操作，比如上传通过 FormData 发送 Blob，下载通过 Blob 配合 URL.createObjectURL 实现文件保存

##### 1.TypedArray
 >  TypedArray 是基于 ArrayBuffer 的类型化视图，用固定数据类型（如 Uint8、Float32）来高效读写和解析二进制数据。

##### 2. ArrayBuffer / TypedArray / Blob 三者关系
>  ArrayBuffer 是底层二进制内存，TypedArray 是对其进行类型化读写的视图，而 Blob 是对二进制数据的封装，用于文件处理和网络传输。

#### 6. 什么是 Iterable 对象，与 Array 有什么区别？

>Iterable 是实现了 `Symbol.iterator` 方法的对象、可以被 `for...of` 遍历的对象；Array 是其中一种具体的数据结构，属于 Iterable 的子集。

#### 7. 解构赋值以下对象，他们的值是多少
##### 1.```const {a: aa, b } = {a: 3, b: 4} ```
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
>参数默认值只在“没传参数”时生效，而解构默认值是在“属性为 undefined”时生效。
参数默认值解决“有没有传”，
解构默认值解决“有没有这个属性”

#### 8. Map 与 WeakMap 有何区别？
 ** 核心区别总览（面试重点）**

| 对比点      | Map                       | WeakMap                         |
| ----------- | ------------------------- | ------------------------------- |
| key 类型    | 任意类型（对象 / 原始值） | **只能是对象**                  |
| 引用类型    | 强引用                    | **弱引用**</br>如果一个对象只被 WeakMap 引用，那么它会被垃圾回收（GC）自动释放。                      |
| 是否阻止 GC | ✅ 会                      | ❌ 不会                          |
| 是否可遍历  | ✅ 可以                    | ❌ 不可以                        |
| size 属性   | ✅ 有                      | ❌ 没有                          |
| 使用场景    | 通用键值存储              | **对象私有数据 / 避免内存泄漏** |

>- `Map`: 可使用任何数据类型作为 key，但因其在内部实现原理中需要维护两个数组，存储 key/value，因此垃圾回收机制无法回收
>- `WeakMap`: 只能使用引用数据类型作为 key。弱引用，不在内部维护两个数组，可被垃圾回收，但因此无法被遍历！即没有与枚举相关的 API，如 `keys`、`values`、`entries` 等