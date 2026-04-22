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
>CSS 的盒模型主要包括以下两种，可通过 box-sizing 属性进行配置：
>
>content-box（标准盒子模型）：默认属性。width 只包含 content
border-box  （怪异盒子模型）：width 包含 (content、padding、border)