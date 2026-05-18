# Vur

### 常见面试问题

#### 1. Vue 的 MVVM 模式是什么？
Vue 遵循 MVVM（Model-View-ViewModel）模式。
- Model（数据层）：Vue 中的数据对象（data、reactive、ref）
- View（视图层）：模板（template）生成的 DOM
- ViewModel（逻辑层）：Vue 实例本身，负责连接 Model 和 View

工作原理：
1. Vue 使用 响应式系统（Object.defineProperty / Proxy）对数据进行代理，追踪依赖。
2. 当数据变化时，会触发依赖收集的 Watcher 更新 DOM（视图自动更新）。
3. 视图操作（如 input 输入）会通过事件绑定更新数据，实现 数据→视图、视图→数据 的双向绑定。

#### 2.  Vue2 与 Vue3 的核心区别？

|维度	|Vue2	|Vue3|
|---|---|---|
响应式实现|	Object.defineProperty，无法监听新增/删除属性，性能稍低|	Proxy，全量监听，支持新增/删除属性，性能更好|
API 风格|	Options API（data、methods、computed、watch）|	Composition API（setup + reactive/ref/computed/watch）|
性能优化	|Diff 算法优化有限	|支持 Fragment、Tree-shaking、Lazy loading、Teleport、Suspense
体积 & 构建|	较大，Tree-shaking 不完全	|更轻量，支持模块化按需打包

> Vue2 和 Vue3 主要有这几个区别：
**响应式原理**，Vue2 用 Object.defineProperty，有缺陷，无法检测到对象新增属性和数组索引变化，需要用 `$set` 解决；Vue3 换成了 Proxy，直接代理整个对象，能检测到任何变化，没有这些限制。
**组合式 API**，Vue2 是选项式 API，逻辑按 data、methods、computed 分类写，相关逻辑分散；Vue3 引入 Composition API，用 setup 把同一个功能的逻辑聚合在一起，更好维护，也方便逻辑复用抽成自定义 Hook。
**性能**，Vue3 重写了虚拟 DOM，引入静态节点提升和 PatchFlags，只 Diff 真正动态的节点，更快；打包体积也更小，支持 Tree Shaking。
**TypeScript**，Vue3 源码用 TS 重写，类型支持更好。
**其他**，Vue3 支持多个根节点，Vue2 只能有一个根节点；Vue3 移除了 `$on/$off` 全局事件总线，用 mitt 代替

#### 3. Vue 的响应式原理

📚 核心知识点
Vue2 响应式原理
核心：Object.defineProperty