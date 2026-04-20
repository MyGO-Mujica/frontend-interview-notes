# React

### 常见面试问题

#### 1. diff算法
核心知识点总结：
React 的 Diff 算法用来对比新旧虚拟 DOM，找出最小化的 DOM 操作。key 是帮助 React 识别列表元素身份的唯一标识，让 Diff 算法能准确复用节点，避免不必要的销毁和重建。

React 的 Diff 算法是一套基于假设条件的对比策略，通过三层递进式对比（Tree Diff → Component Diff → Element Diff），将传统 O(n³) 的树对比降低到 O(n) 复杂度。核心是 Reconciliation（协调）过程和 Key 机制。

key 的本质：

👉 唯一标识元素，用于提高 diff 精度

> React Diff 算法基于三大假设、三层递进对比：
>
> 三大假设（性能基础）：
> 1. 不同类型的节点产生不同的树，直接替换无需深入对比
> 2. 同层级节点变化最频繁，跨层级变化极少
> 3. 开发者通过 key 标识哪些子节点在不同渲染中保持稳定
>
> 三层对比：
> 1. Tree Diff：只对比同层，跨层直接销毁重建
> 2. Component Diff：同类型组件对比 props，不同类型直接替换
> 3. Element Diff：同层子节点通过 key 建立映射，精准复用移动

第一层：Tree Diff
原则：只对比同层，跨层直接替换
```javascript
// 旧树
<div>
  <A />
  <B />
</div>

// 新树
<div>
  <B />
  <C />
</div>

// React 的同层对比流程：
// 位置0: <A /> vs <B /> → 类型不同 → 删除A，创建B
// 位置1: <B /> vs <C /> → 类型不同 → 删除B，创建C
```
第二层：Component Diff
原则：同类型对比 props，不同类型直接替换
```javascript
// ✅ 同类型组件 → 复用实例，只更新 props
// 旧
<UserCard name="张三" age={20} />
// 新
<UserCard name="李四" age={21} />
// Diff 结果：复用 UserCard 实例，把 props 更新为 {name:'李四', age:21}

// ❌ 不同类型组件 → 销毁重建（丢失所有状态）
// 旧
<UserCard />
// 新
<AdminCard />
// Diff 结果：
// 1. UserCard 卸载，触发 componentWillUnmount
// 2. AdminCard 挂载，触发 componentDidMount
// 3. 原来的状态全部丢失！
```
第三层：Element Diff（最重要）
这是 key 发挥作用的核心场景
有 key：建立身份映射，精准复用
```javascript
// 旧列表
<ul>
  <li key="a">A</li>  // 位置 0
  <li key="b">B</li>  // 位置 1
  <li key="c">C</li>  // 位置 2
</ul>

// 新列表
<ul>
  <li key="c">C</li>  // 位置 0
  <li key="a">A</li>  // 位置 1
  <li key="b">B</li>  // 位置 2
</ul>

// Diff 过程：
// 1. 建立旧节点 key 映射表：
//    { "a": fiber0, "b": fiber1, "c": fiber2 }

// 2. 遍历新列表查找对应节点：
//    key="c" → 找到 fiber2 ✅ 复用，标记需要移动
//    key="a" → 找到 fiber0 ✅ 复用，标记需要移动
//    key="b" → 找到 fiber1 ✅ 复用，无需移动（位置最优）

// 结果：0次内容更新，只需少量 DOM 移动操作 ✅
```

总结：
> React 的 diff 算法是一种基于假设条件的启发式算法：不同类型节点直接替换、只做同层比较，并结合 key 优化列表 diff，从而将复杂度从 O(n³) 降到 O(n)，在保证性能的同时实现高效更新。

#### 2. React Hook 的核心机制
React Hook 本质上是一套基于**链表结构**的状态管理机制。每个组件在底层维护着一个 Hook 链表，每次渲染时 React 会按照顺序依次读取这个链表中的节点。让函数组件也能使用 state、生命周期、上下文 等功能，避免类组件的复杂性和逻辑分散问题。
1. 只在组件顶层调用，不能在循环、条件或嵌套函数中调用。
2. 只在 React 函数组件或自定义 Hook 中调用。

常用 Hook 及其原理

- useState：底层存储一个 `state` 值和对应的 dispatch 函数，调用 setter 时会触发重新渲染，并将新值更新到对应的链表节点上。
- useEffect：在每次渲染后异步执行副作用，React 会比较依赖数组的前后值（浅比较），如果变化了才会重新执行。返回的清理函数会在下次执行前或组件卸载时调用。
- useRef：返回一个持久化的 `{ current: ... }` 对象，修改 current 不会触发重新渲染，常用于访问 DOM 或保存不需要引起渲染的变量。
- useMemo / useCallback：都是做缓存优化的，useMemo 缓存计算结果，useCallback 缓存函数引用，避免子组件不必要的重渲染。

Hooks 让我们可以用自定义 Hook 将逻辑抽离复用，代码更简洁、更易维护。