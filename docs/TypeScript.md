# 前段工程化

### 常见面试题

#### 1. type和interface有什么区别，你如何选择

📚 核心知识点
相同点
两者都可以描述对象结构：

```ts
type User = {
  name: string
  age: number
}

interface User {
  name: string
  age: number
}
```

区别
1. interface 可以重复声明合并，type 不行
```ts
interface User {
  name: string
}
interface User {
  age: number
}
// 自动合并 → { name: string, age: number } ✅

type User = { name: string }
type User = { age: number } // ❌ 报错，不能重复定义
```

2. type 可以定义联合类型、交叉类型，interface 不行
```ts
// 联合类型
type ID = string | number  // ✅
type Status = 'success' | 'error' | 'loading'  // ✅

// interface 做不到
interface ID = string | number  // ❌
```
```
描述对象结构       → interface
联合类型、字面量类型 → type
第三方库扩展类型    → interface（利用声明合并）
```

> `type` 和 `interface` 大部分场景可以互换，主要区别有两点：`interface` 支持重复声明自动合并，适合给第三方库扩展类型；`type` 支持联合类型，`interface` 做不到。
选择上，描述对象结构两者都行；需要联合类型用 `type`；需要声明合并用 `interface`；日常开发团队统一一种就好，我习惯能用 interface 就用 interface，需要联合类型才用 type。

#### 2. 泛型
📚 核心知识点
泛型（Generic）
让类型变成参数，写一份代码支持多种类型：
```ts
// 没有泛型，要写多个函数
function returnNumber(val: number): number { return val }
function returnString(val: string): string { return val }

// 有泛型，一个函数搞定
function identity<T>(val: T): T {
  return val
}

identity<number>(1)    // T = number
identity<string>('hi') // T = string
identity(true)         // T 自动推断为 boolean
```
- 泛型：让类型变成参数，实现类型复用
- interface：描述对象结构，支持继承合并
- type：更灵活，支持联合、交叉、工具类型