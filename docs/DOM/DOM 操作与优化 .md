

# DOM 操作与优化 

## 📌 核心概念

DOM（Document Object Model，文档对象模型）是 HTML 和 XML 文档的编程接口。它将文档表示为节点树，允许 JavaScript 访问和操作页面内容、结构和样式。

**核心要点：**

- DOM 是浏览器提供的 API，用于操作网页元素
- DOM 操作会触发浏览器的重排（Reflow）和重绘（Repaint）
- 频繁的 DOM 操作会影响性能，需要优化
- 现代前端框架（Vue/React）都是为了优化 DOM 操作而生



## **常见面试问题**

### 1.  统计当前页面出现次数最多的标签

***问题***：如何统计当前页面中出现次数最多的 HTML 标签？ 



**推荐回答思路：**

\- 使用 `document.querySelectorAll('*')` 获取所有元素

\- 遍历元素统计每个标签名出现的次数

\- 找出出现次数最多的标签



```javascript
function getMostFrequentTag() {
  const tags = document.querySelectorAll('*');
  const tagCount = {};
  
  // 统计每个标签出现次数
  tags.forEach(tag => {
    const tagName = tag.tagName.toLowerCase();
    tagCount[tagName] = (tagCount[tagName] || 0) + 1;
  });
  
  // 找出出现次数最多的标签
  let maxTag = '';
  let maxCount = 0;
  
  for (let tag in tagCount) {
    if (tagCount[tag] > maxCount) {
      maxCount = tagCount[tag];
      maxTag = tag;
    }
  }
  
  return { tag: maxTag, count: maxCount };
}
```



### 2. 什么是跨域，如何解决什么是跨域？

**核心概念**

跨域是由于浏览器的***\*同源策略\****（Same-Origin Policy）导致的安全限制。当协议（protocol）、域名（domain）或端口（port）任一不同时，就会产生跨域问题。

1. **方案1：CORS（跨域资源共享）** ⭐ 最常用

   **原理**：服务器设置响应头允许跨域

2. **方案2：JSONP**

​	  **原理**：利用 `<script>` 标签不受同源策略限制的特性

​	  **缺点**：只支持 GET 请求，存在安全风险 **适用场景**：老项目或只需要 GET 请求的场景

3. **方案3：代理服务器**

​    \- **开发环境：**webpack devServer 或 Vite 代理

​    \- **生产环境：**Nginx 反向代理



**推荐回答结构**：

> "跨域是由于浏览器的同源策略导致的，当协议、域名或端口任一不同时就会产生跨域问题。
>
> **最常用的解决方案是 CORS**，由服务器设置 `Access-Control-Allow-Origin` 等响应头来允许跨域。
>
> **开发环境中**，我们通常使用 webpack 的 devServer 代理或 Nginx 反向代理来解决。
>
> **其他方案**还包括 JSONP（只支持 GET）、WebSocket 等，具体选择哪种方案要根据实际业务场景决定。"



### 3. 网站开发中，如何实现图片的懒加载

**1. 核心原理：** 它的核心是 **“延迟加载”**。页面加载时，先不加载视口（Viewport）之外的图片，只加载可视区域内的。当用户滚动页面，图片即将进入视口时，才去触发加载。

**2. 为什么用：** 主要目的是**提升首屏加载速度**，减少首次加载时不必要的HTTP请求，同时**节省用户流量**。

**3. 如何实现（两种主流方式）：**

- **方式一：`IntersectionObserver` (交叉观察器) - （目前推荐）** 这是目前**性能最好、最主流**的方式。

```javascript
const observer = new IntersectionObserver((changes) => {
  changes.forEach((change) => {
    // intersectionRatio
    if (change.isIntersecting) {
      const img = change.target
      img.src = img.dataset.src
      observer.unobserve(img)
    }
  })
})
 
observer.observe(img)
```

​      1. 先把图片真实地址存在`data-src`属性上。

​      2. 我们用`IntersectionObserver`去‘观察’这个图片。

​      3. 当API监听到图片进入视口时，我们再把`data-src`的值赋给`src`属性，图片就加载了。

​	  4. 它比以前监听`scroll`事件性能好得多，不会引发卡顿。

- **方式二：`loading="lazy"` (浏览器原生) - （最简单）** 如果不需要兼容老旧浏览器，这是**最简单**的方式。



### 4. sessionStorage与localStorage有何区别

 **推荐回答结构**：

> localStorage生命周期是永久除非自主清除 sessionStorage生命周期为当前窗口或标签页，关闭窗口或标签页则会清除数据
>
> 他们均只能存储字符串类型的对象
>
> 不同浏览器无法共享localStorage或sessionStorage中的信息。相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享sessionStorage的信息。