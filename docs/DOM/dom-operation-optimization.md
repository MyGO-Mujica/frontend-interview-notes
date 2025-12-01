

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



### 5.如何封装一个支持过期时间的 localStorage

**核心知识点**

通过封装原生 `localStorage` 方法，实现对存储数据**设置时间戳**和在**读取时进行校验**的核心逻辑。

- **数据结构设计：** 存储的值不应该是原始值，而是一个包含原始数据和过期时间戳（`expires`）的**对象**。
  - `value`: 实际需要存储的数据。
  - `expires`: 数据的过期时间点，通常是当前时间加上有效期（毫秒数）。
- **存储（`setItem` 封装）：** 存储时，将原始值和计算出的过期时间一起封装成一个对象，然后用 `JSON.stringify()` 存入 `localStorage`。
- **读取（`getItem` 封装）：**
  1. 从 `localStorage` 取出存储的字符串，并用 `JSON.parse()` 解析回对象。
  2. **核心判断：** 检查当前时间是否大于存储的过期时间戳 (`expires`)。
  3. 如果**已过期**，调用 `removeItem` 清除该项，并返回 `null`。
  4. 如果**未过期**，返回对象中的实际数据 (`value`)。

```javascript
(function () {

    localStorage.setItem = function (key, value, time = Infinity) {
        
        // 检查传入的 time 是否是有限数字（即是否设置了明确的过期时间）。
        const payload = Number.isFinite(time)
            ? {
                  // 如果设置了过期时间，将数据和过期时间封装成一个对象。
                  __data: value, // 存储实际数据
                  // 计算数据在未来的过期时间点（当前时间 + 有效期毫秒数）
                  __expiresTime: Date.now() + time, 
              }
            : value; // 如果 time 是 Infinity，则 payload 就是原始值，不添加过期标记。

        // 调用原生 Storage.prototype.setItem 方法来执行实际的存储操作。
        // 使用 .call(localStorage, ...) 确保方法在 localStorage 实例的上下文上执行。
        // 注意：无论是封装的对象还是原始值，都必须先用 JSON.stringify() 转换为字符串才能存储。
        Storage.prototype.setItem.call(localStorage, key, JSON.stringify(payload));
    };

    /**
     * 重写 localStorage.getItem 方法，使其在读取时能进行过期校验。
     * @param {string} key 键名
     * @returns {string | undefined} 未过期则返回 JSON 字符串形式的实际数据，否则返回 undefined。
     */
    localStorage.getItem = function (key) {
        
        // 调用原生 Storage.prototype.getItem 方法获取存储的字符串。
        const value = Storage.prototype.getItem.call(localStorage, key);
        
        // 如果未取到值（null），直接返回，不做后续处理。
        if (value === null) {
            return null;
        }

        try {
            // 尝试解析取出的字符串，看它是否是我们封装的 JSON 对象。
            const jsonVal = JSON.parse(value);
            
            // 检查解析后的对象中是否存在 __expiresTime 属性。
            if (jsonVal && jsonVal.__expiresTime) {
                // 如果存在过期时间标记，则进行过期校验：
                
                // 校验：过期时间点 >= 当前时间，即数据未过期。
                return jsonVal.__expiresTime >= Date.now()
                    // 未过期：返回实际存储的数据 __data (JSON.stringify 是因为原来的 value 是 stringified)
                    ? JSON.stringify(jsonVal.__data)
                    // 已过期：返回 undefined (void 0 是一个返回 undefined 的简洁写法)
                    : void 0;
            }
            
            // 如果解析成功，但没有 __expiresTime 标记，说明它是一个永不过期的原始存储项，直接返回。
            return value; 

        } catch (error) {
            // 如果 JSON.parse 失败，说明存储的是一个原始字符串（非 JSON），
            // 或者是一个无效的 JSON 格式，直接返回原始值。
            return value;
        }
    };
})();
```

 **推荐回答结构**：

> 要设置支持过期时间的 `localStorage`，由于原生 `localStorage` 没有这个功能，我们需要通过**封装**它的 `setItem` 和 `getItem` 方法来实现。
>
> 核心思路是：
>
> 1. **存储（`setItem`）：** 不只存储数据，而是将数据和一个计算好的**过期时间戳**（过期时间戳是计算得到的，通常是 `当前时间` + `有效期（毫秒数）`。）封装成一个对象，然后用 `JSON.stringify()` 存进去。
> 2. **读取（`getItem`）：** 取出数据时，先检查当前时间 (`Date.now()`) 是否大于存储的那个过期时间戳。
> 3. 如果**已过期**，就调用 `removeItem` 清除该数据，并返回 `null`。
> 4. 如果**未过期**，就返回实际存储的数据。



### 6. cookie

**核心知识**

用于 **存储少量文本数据**（如登录状态、用户偏好），主要用于 **会话管理**。

------

#### 1.  浏览器中 cookie 有哪些字段

在浏览器中，一个 Cookie 主要由以下字段组成：

1. **name / value** Cookie 的键和值，是最基本的字段。

2. **expires** 指定 Cookie 的过期时间（绝对时间）。到了时间后浏览器会自动删除。

3. **max-age** 指定 Cookie 的存活时长（相对时间，单位秒）。`max-age` 和 `expires` 二选一，`max-age` 优先级更高。

4. **domain** 指定 Cookie 允许发送的域名范围。 例如：`domain=.example.com` 可在所有子域共享 Cookie。

5. **path** 指定 Cookie 生效的路径。 如 `path=/admin` 则只有访问 `/admin` 下的接口才会带上 Cookie。

6. **secure** 表示 Cookie 仅在 HTTPS 连接中传输。

7. **httpOnly** 表示 Cookie 无法通过 JavaScript 的 `document.cookie` 访问，提高安全性。

8. sameSite

    

   用于限制跨站请求时 Cookie 是否能发送，防止 CSRF。常见取值：

   - `Strict`：完全禁止跨站发送
   - `Lax`：部分跨站允许（如 Get 导航）
   - `None`：允许跨站，但必须搭配 `Secure`

**一句话可用**

**Cookie 的字段包括：name、value、expires、max-age、domain、path、secure、httpOnly、sameSite。其中 secure、httpOnly、sameSite 是安全相关字段。**



#### 2. 当 cookie 没有设置 maxage 时，cookie 会存在多久

> 如果 Cookie 没有设置 `expires` 或 `max-age`，它会变成会话 Cookie，只在当前浏览器**会话中有效**，关闭浏览器后就会被清除。
