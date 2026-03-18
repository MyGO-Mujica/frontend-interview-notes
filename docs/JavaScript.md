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





