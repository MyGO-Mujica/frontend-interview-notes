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