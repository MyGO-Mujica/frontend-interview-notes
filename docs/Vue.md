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
```js
function defineReactive(obj, key, val) {
  Object.defineProperty(obj, key, {
    get() {
      // 依赖收集：记录谁用了这个数据
      track()
      return val
    },
    set(newVal) {
      // 派发更新：通知所有用到这个数据的地方重新渲染
      val = newVal
      trigger()
    }
  })
}
```
缺陷：

- 无法检测对象新增/删除属性
- 无法检测数组索引赋值和 length 变化
- 初始化时需要递归遍历所有属性，性能差

```js
// 这些操作 Vue2 检测不到
obj.newKey = 'value'  // ❌ 新增属性
delete obj.key        // ❌ 删除属性
arr[0] = 'new'        // ❌ 索引赋值

// 需要用 $set 解决
this.$set(obj, 'newKey', 'value') // ✅
```

Vue3 响应式原理
核心：`Proxy`

```js
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key) {
      track(target, key) // 依赖收集
      return Reflect.get(target, key)
    },
    set(target, key, value) {
      Reflect.set(target, key, value)
      trigger(target, key) // 派发更新
      return true
    },
    deleteProperty(target, key) {
      Reflect.deleteProperty(target, key)
      trigger(target, key) // 删除也能触发
      return true
    }
  })
}
```

优势：

- 代理整个对象，新增、删除、数组变化全能检测
- 懒代理，访问到嵌套对象才代理，性能更好
- 代码更简洁

> Vue 响应式原理分两个版本：
Vue2 用 Object.defineProperty 对每个属性设置 getter 和 setter，读取数据时 getter 触发依赖收集，把用到这个数据的组件记录下来；数据变化时 setter 触发派发更新，通知所有依赖重新渲染。缺陷是无法检测对象新增删除属性和数组索引变化，因为这些操作不会触发 setter，需要用 $set 解决，另外初始化时要递归遍历所有属性，性能也较差。
Vue3 换成了 Proxy 直接代理整个对象，新增属性、删除属性、数组任何变化都能检测到，没有 Vue2 的缺陷。而且是懒代理，访问到嵌套对象才代理，初始化性能更好。
两者依赖收集和派发更新的思路是一样的，区别只在于拦截方式：Vue2 是属性级别的拦截，Vue3 是对象级别的代理。

#### 4. Vue 的 diff 算法.
📚 核心知识点

- Vue2 Diff —— 双端对比
同时从新旧两个列表的两端向中间对比，四个指针

- Vue3 Diff —— 最长递增子序列

Vue2 vs Vue3 Diff 对比
| |Vue2|Vue3|
|---|---|---|
算法|双端对比|最长递增子序列
静态节点|参与 Diff|跳过，不参与|
性能|一般|更好


> Vue 的 Diff 算法就是新旧虚拟 DOM 对比，找出差异只更新变化的部分。
Vue2 用双端对比，同时从新旧列表两端向中间对比，用四个指针互相匹配，命中就复用节点，都不中再用 key 查找，整体是 O(n) 复杂度。
Vue3 更进一步，先处理头尾相同的节点跳过，再处理新增和删除，中间乱序的部分用最长递增子序列算法，找出不需要移动的节点，只移动真正需要移动的，减少 DOM 操作次数。另外 Vue3 有 PatchFlags，静态节点直接跳过不参与 Diff，性能比 Vue2 更好。
key 的作用是帮助 Diff 算法精准识别节点身份，避免错误复用，所以要用唯一稳定的 id，不能用 index，因为增删时 index 会变。

#### 5. vue的虚拟dom
什么是虚拟 DOM
用 JS 对象描述真实 DOM，是真实 DOM 的轻量级表示：
```js
// 真实 DOM
<div class="box">
  <p>hello</p>
</div>

// Vue 的虚拟 DOM（VNode）
{
  type: 'div',
  props: { class: 'box' },
  children: [
    { type: 'p', props: {}, children: 'hello' }
  ]
}
```

>Vue 的虚拟 DOM 是用 JS 对象描述真实 DOM 结构，叫做 VNode。
渲染流程是：模板先被编译成 render 函数，执行 render 函数生成 VNode 树，再挂载到真实 DOM。数据变化时重新生成新的 VNode，和旧的做 Diff，只更新差异部分到真实 DOM，减少不必要的 DOM 操作。
Vue3 对虚拟 DOM 做了两个重要优化：静态节点提升，把不变的节点提到 render 函数外面，不参与 Diff 直接复用；PatchFlags 在编译时标记动态节点，运行时只检查有标记的部分，静态内容直接跳过，大幅减少 Diff 工作量。
虚拟 DOM 更大的价值是跨平台，同一套 VNode 换不同的渲染器，可以输出到 Web、SSR、甚至原生应用。

#### 6. Vue3 为什么性能比 Vue2更好？

> Vue3 比 Vue2 性能好主要体现在四个方面：
**响应式**，Vue2 初始化时递归遍历所有属性全部劫持；Vue3 用 Proxy 懒代理，访问到嵌套对象才代理，没用到的不处理，初始化更快。
**虚拟 DOM**，Vue2 每次更新对比整棵树，包括静态节点；Vue3 做了静态节点提升，静态内容只创建一次不参与 Diff，还用 PatchFlags 在编译时标记动态节点，运行时只检查有标记的节点，大幅减少 Diff 的工作量。
**编译优化**，Vue3 在编译阶段就分析出哪些是动态节点，生成更高效的渲染代码。
**Tree Shaking**，Vue3 的 API 全部按需引入，没用到的不会打包进去，bundle 体积更小。

#### 7. vue 中 key的作用以及底层原理？
key 是虚拟 DOM 节点的唯一身份标识，最常用于 v-for 列表渲染，核心作用有 3 个：
1. 大幅优化列表渲染性能：减少不必要的 DOM 创建 / 删除操作，提升 diff 算法效率；
2. 避免节点复用混乱：解决默认「就地复用」策略导致的状态错乱问题（比如表单输入值丢失、组件状态异常）；
3. 强制节点 / 组件重建：相同组件切换时，通过 key 可以强制触发组件销毁 + 重建，重置状态、重新执行生命周期。

底层：diff 算法的对比逻辑
`key` 的作用本质是优化虚拟 DOM 的 diff 对比策略

#### computed是什么？它的实现原理？
📚 核心知识点
什么是 `computed`
基于响应式数据派生出来的值，依赖不变就不重新计算，有缓存。

实现原理:
1. 懒执行

- 创建时不立即执行，第一次读取才计算

2. 缓存

- 内部有个 dirty 标志
- dirty = true：依赖变了，需要重新计算
- dirty = false：依赖没变，直接返回缓存值
```
第一次读取 computed
  → dirty = true → 执行计算 → 缓存结果 → dirty = false

再次读取
  → dirty = false → 直接返回缓存 ✅

依赖数据变化
  → dirty = true → 下次读取重新计算
```

> computed 是基于响应式数据派生的值，核心特点是有缓存，依赖没变就不重新计算，直接返回上次的结果。
原理是内部维护一个 dirty 标志，初始是 true，第一次读取时执行计算并缓存结果，把 dirty 置为 false；之后再读取直接返回缓存；依赖的数据变化时把 dirty 重新置为 true，下次读取才重新计算。
