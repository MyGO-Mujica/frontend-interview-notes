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