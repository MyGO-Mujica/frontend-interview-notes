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

- 第一大策略：只对同层级DOM 节点进行对比，绝不跨层级对比
简单说，Diff 算法会把 DOM 树按层级拆分，只对比同一父节点下的子节点，完全不考虑跨父节点的节点移动。采用原因：前端实际开发里，DOM 节点跨层级移动的场景极少，几乎都是同级的增、删、改。如果做全树递归跨层级对比，计算量会爆炸式增长；放弃跨层级对比，是用极小的场景牺牲，换来了最核心的性能减负。
- 第二大策略：不同类型的节点，直接销毁旧树、新建新树，不做复用对比
比如 DOM 标签从`<div>`改成`<p>`，或组件从 Class 组件换成 Function 组件，Diff 会直接卸载旧节点、挂载新节点，不会再去对比内部子节点。采用原因：不同类型的节点 / 组件，渲染结构、样式、逻辑差异极大，继续深度对比复用的成本，远高于直接重建新节点。这种 “一刀切” 的方式，能避免无意义的对比开销，反而更高效。
- 第三大策略：列表节点通过唯一 key，实现精准节点匹配与复用
渲染列表时，给每个子节点绑定稳定、唯一的 key，Diff 算法会通过 key 匹配新旧列表的节点，而非默认按索引对比。采用原因：不加 key 时，列表增删会导致节点按索引错位复用，既会引发渲染 bug，又会大量重复创建 / 销毁 DOM；唯一 key 能让算法快速定位对应节点，直接复用 DOM 元素，大幅减少长列表的渲染损耗，保证列表更新的性能与正确性。

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

#### 3. 组件间通信方式

React

1. Props（父传子）
2. 状态提升（兄弟通信）

- 将共享 state 提升到最近公共父组件
- 通过 props 下发数据和回调



3. Context（跨层级）

- createContext + Provider + useContext
- 适合主题、语言、用户信息等全局数据
- 缺点：value 变化会导致所有消费组件重渲染



4. 状态管理库

- Zustand：轻量，hooks 风格，当下最流行



Vue 3

1. Props / Emits（父子）

- 父传子：defineProps
- 子传父：defineEmits → emit('event', data)



2. v-model（父子双向）

- 本质是 modelValue prop + update:modelValue emit 的语法糖
- Vue3 支持多个 v-model：v-model:title、v-model:content



3. provide / inject（跨层级）

- 先 provide('key', value)，后代 inject('key')
- 可配合 ref / reactive 保持响应式
- 


5. Pinia（状态管理）

- 替代 Vuex，更轻量，天然支持 Composition API
- 适合全局共享状态



6. defineExpose（子暴露给父）

- 配合 ref 获取子组件实例
- 子组件用 defineExpose({ method, data }) 显式暴露



 横向对比

| 场景       | Vue 3              | React                            |
| ---------- | ------------------ | -------------------------------- |
| 父传子     | props              | props                            |
| 子传父     | emits              | 回调函数 props                   |
| 双向绑定   | v-model            | 受控组件 + onChange              |
| 跨层级     | provide/inject     | Context                          |
| 兄弟通信   | mitt / Pinia       | 状态提升 / Zustand               |
| 父调子方法 | defineExpose + ref | forwardRef + useImperativeHandle |
| 全局状态   | Pinia              | Zustand / Redux                  |

>组件通信我分场景来说：
>
>**父子通信**，Vue3 用 `defineProps` 接收数据，`defineEmits` 向上触发事件；React >用 props 传数据，传回调函数实现子传父，思路一致。
>
>**双向绑定**，Vue3 的 `v-model` 是语法糖，本质是 `modelValue` + `update:modelValue`，还支持多个 v-model 绑不同字段；React 没有这个，靠受控组件加 >onChange 自己实现。
>
>**跨层级**，Vue3 用 `provide / inject`，配合 ref 可以保持响应式；React 用 `Context`，但要注意 value 变化会触发所有消费组件重渲染，可以用 `memo` 或拆分 Context 来优化。
>
>**兄弟或任意组件**，Vue3 推荐用 `mitt` 事件总线，因为 Vue3 把全局事件总线移除了；React 可以做状态提升，或者直接上状态管理库。
>
>**全局状态管理**，Vue3 用 Pinia，比 Vuex 更轻量，API 更友好；React 现在主流用 Zustand，够轻量，hooks 风格，大型项目才考虑 Redux Toolkit。
>
>**父调子方法**，Vue3 子组件用 `defineExpose` 显式暴露方法，父组件拿 ref 调用；React 用 `forwardRef` 配合 `useImperativeHandle` 实现同样效果。

#### 4. 虚拟DOM对性能的影响
>虚拟 DOM 对性能的影响是双面的，不能简单说它"提升性能"。
>
>**好的一面是**，它把多次状态变化合并成一次 DOM 更新，减少了浏览器的重排重绘次数。而且开发者只需要关心数据，框架自动处理 DOM 操作，降低了出错概率。
>
>**但它本身是有开销的**，首次渲染要构建完整的虚拟 DOM 树，比直接操作 DOM 慢；每次更新要创建新的 vnode 对象再做 Diff 计算，这些都是纯 JS 的运行成本。所以在极致性能场景下，手动操作 DOM 反而更快。
>
>**Diff 算法**是虚拟 DOM 的核心，主要有三个策略：只做同层对比把复杂度降到 O(n)；类型不同直接销毁重建；用 key 来准确识别节点身份，所以列表渲染一定要用唯一稳定的 id 做 key，不能用 index，因为增删元素时 index 会变，导致节点被错误复用。