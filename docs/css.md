# CSS

### 常见面试题

#### 1. 盒模型

CSS 盒模型（Box Model）描述的是浏览器如何把每个元素渲染成一个矩形盒子，以及这个盒子的尺寸是如何计算的。

一个盒子从内到外由四部分组成：

- content（内容区）
- padding（内边距）
- border（边框）
- margin（外边距）

两种盒模型

1. 标准盒模型

```css
box-sizing: content-box;
```

- width / height 只包含 content
- 实际占据空间：
  总宽度 = width + padding + border + margin

2. 怪异盒模型

```css
box-sizing: border-box;
```

- width / height 包含 content + padding + border
- content 会被“压缩”
  content = width - padding - border
  例子：

```css
width: 100px;
padding: 10px;
border: 5px;
```

实际渲染宽度：
总宽度 = 100px（已经包含 padding 和 border）

总的来说

> CSS 的盒模型主要包括以下两种，可通过 box-sizing 属性进行配置：
>
> content-box（标准盒子模型）：默认属性。width 只包含 content
> border-box （怪异盒子模型）：width 包含 (content、padding、border)

#### 2. 对于样式布局，你了解哪些方式呢？

1. Position 定位布局

- 5 个属性：static(默认) / relative(相对定位) / absolute(绝对定位) / fixed(固定定位) / sticky(粘性定位)；

2. 浮动布局（float）

- 缺陷：脱离文档流 → 父元素高度塌陷，必须手动清除浮动；
- 现状：仅用于文字环绕图片，新项目不用做主布局。

3. **Flex 弹性布局**
   核心思想：在一条轴上分配空间
   布局基于两条轴：主轴（Main Axis） 决定排列方向，交叉轴（Cross Axis） 与主轴垂直。

- `flex-direction` — 主轴方向
- `justify-content` — 主轴对齐
- `align-items` — 交叉轴对齐
- `flex-wrap` - 自动换行
- `flex: 1` - 平分剩余空间 `0` 最小化显示，不抢空间 `auto` 优先保留元素自身的宽度

万能居中：

```css
/* 万能居中 */
display: flex;
justify-content: center;
align-items: center;
```

`Flex` 布局中 `margin: auto` 会自动抢占所有剩余空间，把元素强行挤到正中间

![image-20260422213952352](C:\Users\11582\AppData\Roaming\Typora\typora-user-images\image-20260422213952352.png)

```css
.parent {
  width: 100px;
  height: 100px;
  background-color: #999999;
  border: 1px solid #000;

  /* 关键：开启 flex，竖向排列，纵向两端对齐 */
  display: flex;
  flex-direction: column;// 主轴方向
  justify-content:space-between; // 主轴对齐方式
  pading: 0 5px 0 5px;
  box-sizing: border-box

}

/* child1 不需要写，默认 align-self: flex-start（靠左） */
.child2 {
  align-self: center;   /* 居中 */
}

.child3 {
  align-self: flex-end; /* 靠右 */
}
```

4. **Grid 网状布局**

#### 3. 响应式布局

CSS 响应式布局的核心是：让网页在手机、平板、电脑等不同尺寸设备上自动适配，展示最优效果

1.  **媒体查询** → 响应式核心
    作用：设置断点根据屏幕宽度，给不同设备写专属 CSS 样式。
    常用断点

```css
/* 1. 手机：屏幕 ≤ 576px */
@media (max-width: 576px) {
}

/* 2. 平板：屏幕 ≤ 768px */
@media (max-width: 768px) {
}

/* 3. 笔记本：屏幕 ≤ 1200px */
@media (max-width: 1200px) {
}
```

2. Flex 弹性布局 → 最简单的自适应布局
   作用：元素自动换行、自动占满宽度，不用媒体查询也能响应式。

3. Grid 网格布局

4. 相对单位 → 替代固定像素 px

| 单位 | 含义                           | 适用场景         |
| ---- | ------------------------------ | ---------------- |
| %    | 相对于父元素宽度               | 宽度、布局       |
| vw   | 视口宽度（1vw = 屏幕 1% 宽度） | 字体、盒子宽度   |
| rem  | 相对于根元素字体大小           | 全局字体、间距   |
| fr   | Grid 弹性比例                  | 网格布局平分宽度 |

#### 4. scss

SCSS 本质上是 CSS 的预处理器，主要解决原生 CSS 在大型项目里可维护性差的问题。它支持嵌套、变量、复用和模块化，让样式从“堆规则”变成“可组织的代码”

> 我把 SCSS 当成工程化 CSS 来用，核心价值是统一和可维护。
> 第一点是全局变量统一主题和组件库风格。我们项目会先在全局层定义主题色、文字色、状态色这些设计令牌，然后页面和组件都复用这一套，不会每个人自己写一套颜色。这样做的好处是，风格一致、改版成本低，比如后面要从一种主题换到另一种主题，改变量就能全局生效，不需要逐页找样式。对组件库也一样，可以基于统一变量做二次定制，保证业务页面和组件库视觉是同一套语言。
>
> 第二点是用嵌套加 scoped 提升可读性并隔离样式。SCSS 的嵌套可以把父子结构、状态样式、伪类写在一起，读起来就是组件结构本身，维护时定位很快。再配合 scoped，样式作用域默认限制在当前组件，基本不会出现一个页面改样式把另一个页面误伤的问题。对于多人协作，这点很重要，能明显减少样式冲突和回归问题。

#### 5.CSS的模块化？怎么保证样式不冲突？
1. BEM 命名约定
   优点：零工具依赖，语义清晰，一眼看出层级关系。
   缺点：类名太长，全靠人工遵守规范，没有强制约束。

2. CSS Modules
   目前最常用的无侵入模块化方案，Webpack/Vite 原生支持

- 原理：**编译时自动生成唯一哈希类名**，将局部样式转为全局唯一类名，实现天然局部作用域；
- 用法：CSS文件命名为 `xxx.module.css`（固定后缀，标识为模块化样式文件）；

```jsx
import styles from "./Button.module.css";
// 编译后类名变为：Button_btn__abc123
<button className={styles.btn}>按钮</button>;
```

3. Tailwind CSS（原子化）

- 预设工具类：框架内置大量常用样式的工具类（如 `mt-4`=margin-top:1rem、`text-red-500`=color:#ef4444、`flex`=display:flex）；
- 无作用域冲突：工具类本身是全局的，但每个工具类功能单一，组合使用时不会出现冲突，且无需担心类名重复。

```HTML
// 无需写任何自定义CSS，直接组合工具类
<!-- 按钮：居中、背景色、文字色、内边距、圆角、hover效果 -->
<button class="flex items-center justify-center bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600">
  主按钮
</button>

<!-- 卡片：阴影、内边距、圆角、宽度 -->
<div class="w-64 p-4 rounded-lg shadow-md">
  卡片内容
</div>
```

4. CSS 预处理器 Sass / Less / Stylus （语法增强，不解决全局污染）

- 原理：扩展 CSS 语法，提供变量、嵌套、混合宏、模块化导入能力；

```scss
// 嵌套、变量、导入
$color: red;
.card {
  color: $color;
  &__title {
    font-size: 16px;
  } // 嵌套等价于 .card__title
}
```

5. Vue Scoped（框架内置）
   Vue 原生支持，加一个 scoped(范围) 属性搞定

```css
<template>
  <button class="button">Click me</button>
</template>

<style scoped>
/* 只作用于当前组件，不会影响外部 */
.button {
  color: white;
  background: #2563EB;
}
</style>
```
