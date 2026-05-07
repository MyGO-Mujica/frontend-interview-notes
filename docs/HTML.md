# 手写代码
### 常见面试题
#### 1. HTML5 语义化

📚 核心知识点
什么是语义化
用**有意义的标签**来描述内容，而不是全用` <div> `和 `<span>`。

```js
<header>   <!-- 页头 -->
<nav>      <!-- 导航栏 -->
<main>     <!-- 主要内容 -->
<section>  <!-- 内容区块 -->
<article>  <!-- 独立文章 -->
<aside>    <!-- 侧边栏 -->
<footer>   <!-- 页脚 -->
```

好处：
1. 利于 SEO 搜索引擎能更好理解页面结构，知道哪里是标题哪里是正文，提升搜索排名。
  - > SEO 是搜索引擎优化，目的是让页面在搜索结果中排名更靠前。
2. 提升可访问性屏幕阅读器依赖语义标签来帮助视障用户理解页面内容。
3. 代码可读性更好一眼就知道每块是什么，比全是 `<div>`更好维护。


#### 2 meta标签

📚 核心知识点
什么是 meta 标签
写在 `<head>` 里，用来描述页面的元信息，浏览器和搜索引擎会读取，但用户看不到。

字符编码
```js
<meta charset="UTF-8">
```
告诉浏览器用 UTF-8 解析页面，防止中文乱码。

移动端适配
```js
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```
让页面宽度等于设备宽度，初始缩放比例为 1，移动端必写。

SEO 相关

```js
<meta name="description" content="页面描述">
<meta name="keywords" content="关键词1,关键词2">
```
搜索引擎读取 description 展示在搜索结果里，keywords 现在权重很低基本不用了。