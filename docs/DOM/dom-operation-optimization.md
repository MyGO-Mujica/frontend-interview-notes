

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

​      1. 先把图片真实地址存在`data-src`属性上。`<img data-src="真实图片地址">`

​      2. 我们用`IntersectionObserver`去‘观察’这个图片。

​      3. 当API监听到图片进入视口时，我们再把`data-src`的值赋给`src`属性，图片就加载了。`img.src = img.dataset.src`

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



#### 3. SameSite Cookie 有哪些值，是如何预防 CSRF 攻击的

**核心知识**

**SameSite 属性用于限制第三方环境下 Cookie 的发送，主要有三个值：**

**1 `SameSite=Strict`**

- 最严格。
- **任何跨站请求都不会携带 Cookie**（包括点击外链）。
- 适用于高安全性场景，如银行、支付，几乎杜绝 CSRF。

**2 `SameSite=Lax`（浏览器默认值）**

- 大部分跨站请求不发送 Cookie。
- **允许“安全的 GET 跨站请求”携带 Cookie**，例如：
  - 点击链接跳转、打开新页面
- 不能用于跨站 POST、iframe、fetch。

**3 `SameSite=None; Secure`**

- **必须配合 `Secure` 一起使用**（否则被浏览器拒绝）。
- 允许 Cookie 在所有第三方场景中携带。
- 用于跨域登录、第三方 SDK、CDN 资源等。



**推荐回答**：

> **SameSite Cookie 主要有三个值：**
>
> 1. **Strict**：最严格，所有跨站请求都不会携带 Cookie，可以实现最强的 CSRF 防护。
> 2. **Lax**：浏览器默认值，只有导航到第三方网站的 Get 链接会发送 Cookie，跨域的图片、iframe、form表单都不会发送 Cookie，可以阻止大多数 CSRF 攻击。
> 3. **None**：必须与 Secure 配合使用，即在 https 下发送。任何情况下都会向第三方网站请求发送 Cookie，常用于跨域登录或第三方应用。
>
> **SameSite 能预防 CSRF 的原因是：**
>  CSRF 依赖浏览器在跨站请求中“自动带上 Cookie”。
>  而 SameSite 会限制 Cookie 在跨站环境下发送，从源头阻断了攻击者利用用户 Cookie 进行恶意请求的可能性。



#### 4. Cookie 增删改查

**题目：如何设置一个Cookie？**

核心知识

**1. 通过 `document.cookie` 设置（浏览器端最常见）**
 `document.cookie = "key=value; expires=...; max-age=...; path=/; domain=...; secure; samesite=..."`

**2.也可以使用新的API**

```javascript
await cookieStore.set({
  name: "token",
  value: "abc123",
  expires: Date.now() + 3600 * 1000,
  path: "/",
  sameSite: "Lax",
  secure: true
});
```

**推荐回答：**

> 在浏览器端设置 Cookie 主要有两种方式：传统方式是使用 `document.cookie` 写一段 `key=value` 的字符串，同时带上 `expires`、`max-age`、`path`、`secure`、`SameSite` 等属性。
>
> 在现代浏览器中，也可以使用 **Cookie Store API** 的 `cookieStore.set()`，它是异步的，参数是对象形式，更安全。



**题目： 如何删除一个Cookie？**

核心知识

前端 **无法真正删除 Cookie**，只能通过：
 **设置相同 key、相同 path、相同 domain，并把过期时间设为过去时间，从而让浏览器自动清除。**

**推荐回答：**

> 删除 Cookie 的本质是让浏览器把它过期掉：要么用 `max-age=-1，要么用 ` expires ` 设置为过去时间。如果用 Cookie Store API，则可以直接使用 `cookieStore.delete(name)`，但 path、domain 必须与原来一致。



> **顺便补充一下，Cookie Store API 比传统的 `document.cookie` 更现代、可读性更好，也支持异步操作，但目前 Safari 还没有完全支持，所以项目中一般是两者结合使用。**



### 7.  addEventListener()

**核心知识**

addEventListener() 用来给 DOM 绑定事件，它不会覆盖之前绑定的事件，并且事件会在捕获阶段或冒泡阶段触发。第三个参数决定事件触发阶段、是否只触发一次、是否阻止 passive 行为等。

```javascript
addEventListener(type, listener);
addEventListener(type, listener, options);
addEventListener(type, listener, useCapture);
```

`tpye`:表示监听事件类型的大小写敏感的字符串。

`	listener`:当所监听的事件类型触发时，会接收到一个事件通知（实现了Event接口的对象）对象。

 **推荐回答**

> addEventListener 的第三个参数可以是布尔值或一个配置对象。
>
> **当是布尔值时：**
>
> - `true` 表示在捕获阶段触发
> - `false`（默认）表示在冒泡阶段触发
>
> **当是 options 对象时，可以包含：**
>
> - `capture`: 是否在捕获阶段触发
> - `once`: 事件只触发一次，触发后自动移除监听
> - `passive`: 告诉浏览器监听器不会调用 `preventDefault()`，提升滚动性能
> - `signal`: 调用`AbortSignal` 的 `abort()` 方法移除监听器
>
> 实际开发中常用的是 `{ passive: true }` 和 `{ once: true }`。

 

### 8. 什么是事件冒泡和事件捕获

**核心知识点**

****

1. ✅ 一、核心知识点（面试必会）

   ##### **1. 事件流（Event Flow）**

   浏览器事件从触发开始到结束，会经历 **捕获阶段 → 目标阶段 → 冒泡阶段**。

   ------

   ##### **2. 事件捕获（Event Capturing）**

   - 由 **最外层（window/document）向目标元素逐层传递**。
   - 先父再子，本质是 **自上而下**。
   - addEventListener 的第三个参数设为 `true` 或 `{ capture: true }` 进入捕获阶段。

   ------

   ##### **3. 事件冒泡（Event Bubbling）**

   - 从 **目标元素向外不断向上冒泡到 document**。
   - 本质是 **自下而上**。
   - addEventListener 默认就是冒泡。

   ------

   ##### **4. 如何阻止事件传播**

   - `event.stopPropagation()`：停止冒泡/捕获继续传播。
   - `event.stopImmediatePropagation()`：同上 + 阻止同一元素上的其他监听器。

   ------

   ##### **5. 如何阻止默认行为**

   - `event.preventDefault()`：阻止浏览器默认行为（如 a 标签跳转、表单提交）。

**推荐回答：**

>在浏览器中，事件会按照固定的事件流触发，它分为三个阶段：
>
>1. **事件捕获阶段（capturing）**：事件从最外层的 window、document 开始，一层一层向下传递到目标元素，这叫“捕获”。如果在 addEventListener 的第三个参数设置为 `true`，事件会在捕获阶段触发。
>2. **目标阶段（target）**：事件到达实际触发的目标元素本身。
>3. **事件冒泡阶段（bubbling）**：从目标元素开始，事件又会向外一层一层冒泡到 document，这叫“冒泡”。addEventListener 默认就是在冒泡阶段执行。
>
>如果需要中断事件传播，可以使用 `event.stopPropagation()`；如果要阻止默认行为，如阻止表单提交，用 `event.preventDefault()`。



#### 1.关于事件捕获和冒泡，以下代码输出多少？



```html
<div class="container" id="container">
  <div class="item" id="item">
    <div class="btn" id="btn">Click me</div>
  </div>
</div>
```



```javascript
document.addEventListener(
  "click",
  (e) => {
    console.log("Document click");
  },
  {
    capture: true,
  },
);
 
container.addEventListener(
  "click",
  (e) => {
    console.log("Container click");
    // e.stopPropagation()
  },
  {
    capture: true,
  },
);
 
item.addEventListener("click", () => {
  console.log("Item click");
});
 
btn.addEventListener("click", () => {
  console.log("Btn click");
});
 
btn.addEventListener(
  "click",
  () => {
    console.log("Btn click When Capture");
  },
  {
    capture: true,
  },
);
```



> Document click
>
> Container click
>
> Btn click When Capture
>
> Btn click
>
> Item click

```html
<div id="s1">
  s1
  <div id="s2">s2</div>
</div>
```



```javascript
let s1 = document.getElementById("s1");
let s2 = document.getElementById("s2");
 
s2.onclick = function () {
  console.log("s2 click1");
};
s1.addEventListener(
  "click",
  function (e) {
    console.log("s1 冒泡事件");
  },
  false,
);
s2.addEventListener(
  "click",
  function (e) {
    console.log("s2 冒泡事件2");
  },
  false,
);
s2.addEventListener(
  "click",
  function (e) {
    console.log("s2 冒泡事件1");
  },
  false,
);
s1.addEventListener(
  "click",
  function (e) {
    console.log("s1 捕获事件");
  },
  true,
);
s2.addEventListener(
  "click",
  function (e) {
    console.log("s2 捕获事件");
  },
  true,
);
s2.onclick = function () {
  console.log("s2 click2");
};
```



> s1 捕获事件
>
> s2 捕获事件
>
> s2 click2
>
> s2 冒泡事件2
>
> s2 冒泡事件1
>
> s1 冒泡事件

**总结**

> 浏览器事件传播遵循捕获、目标和冒泡三个阶段。通过 addEventListener 的第三个参数可以控制监听发生在捕获还是冒泡阶段。需要注意的是，**onclick 属于 DOM0 事件**，在目标阶段会**优先**于通过 addEventListener 注册的冒泡监听执行。事件传播过程中可以通过 stopPropagation 阻止事件继续传播。



#### 2.DOM 中如何阻止事件默认行为，如何判断事件否可阻止？

**如何阻止事件默认行为？**

- `e.preventDefault()`: 取消事件

如果 `addEventListener` 第三个参数 `{ passive: true}`，`preventDefault` 将会会无效

**如何判断事件是否“可阻止”？**

#### `event.cancelable`

- `true`：事件**可以**被 `preventDefault()` 阻止
- `false`：调用 `preventDefault()` **不会生效**

**推荐回答**

>在 DOM 事件模型中，阻止事件默认行为的标准方式是调用 `event.preventDefault()`，它可以阻止浏览器在事件触发后执行的内置行为，例如表单提交、链接跳转等。
>
>并不是所有事件都支持阻止默认行为，可以通过事件对象的 `event.cancelable` 属性判断该事件是否可被阻止，只有当 `cancelable` 为 `true` 时，`preventDefault()` 才会生效。
>
>此外，在使用 `addEventListener` 时，如果第三个参数中设置了 `{ passive: true }`，表示该监听器不会调用 `preventDefault()`，此时即使事件本身是可取消的，调用 `preventDefault()` 也会失效]。
>
>需要注意的是，`preventDefault()` 只负责阻止默认行为，而不会影响事件传播，事件传播需要使用 `stopPropagation()` 来控制。



#### 3. 什么是事件委托(事件代理)，e.currentTarget 与 e.target 有何区别

**一、核心知识点总结**

1. 什么是事件委托（Event Delegation）

- **定义**：事件委托是指不直接给每个子元素绑定事件，而是将事件绑定到其**父元素或祖先元素**上，利用**事件冒泡机制**来统一处理子元素的事件。
- **原理**：DOM 事件会从触发元素开始向上冒泡，父元素可以在事件处理函数中通过 `event.target` 判断真正触发事件的子元素。

------

2. **e.target**

- 表示**真正触发事件的元素**
- 指向事件源（最内层被点击的元素）
- 在事件冒泡过程中 **不会改变**

------

3. **e.currentTarget**

- 表示**当前正在处理事件的元素**
- 即**事件监听器绑定在哪个元素上**
- 在事件冒泡的不同阶段可能会变化

------

4. **e.target 与 e.currentTarget 的关系**

- 当事件绑定在子元素自身时：
  `e.target === e.currentTarget`
- 当使用事件委托（事件绑定在父元素）时：
  `e.target` 指向子元素
  `e.currentTarget` 指向父元素

------

5. **事件委托的优点（面试高频）**

- 减少事件监听器数量，**节省内存**
- 支持**动态元素**
- 代码结构更清晰，易维护



**推荐回答：**

>**事件委托**是指将事件统一绑定到父元素上，而不是为每个子元素单独绑定事件。它利用的是 DOM 的**事件冒泡机制**，当子元素触发事件后，事件会冒泡到父元素，由父元素的事件处理函数统一处理。
>
>具体来说，`event.target` 指向的是实际触发事件的 DOM 元素，在整个事件传播过程中不会改变；而 `event.currentTarget` 指向的是当前正在执行事件处理函数的元素，也就是事件绑定的元素。
>
>当事件直接绑定在元素自身时，`event.target` 和 `event.currentTarget` 是相同的；但在事件委托场景下，`event.target` 通常是子元素，而 `event.currentTarget` 是父元素。
>
>使用事件委托可以减少事件绑定数量，降低内存消耗，同时还能很好地支持动态添加的子元素。另外，React 把所有事件委托在 Root Element，用以提升性能。



### 9. input

#### 1. input 中监听值的变化是在监听什么事件？

一、核心结论（直接回答面试官）

在 `<input>` 中监听值的变化，本质上是在监听**原生 DOM 的 `input` 事件**。

------

二、原生 DOM 层面的解释（必须说清）

在原生 JavaScript 中：

- `input` 事件
  - **当输入框的值发生变化时立即触发**
  - 包括键盘输入、粘贴、剪切、拖拽等
  - 是监听输入值变化的**首选事件**
- `change` 事件
  - 通常在**失去焦点或按回车后才触发**
  - 不是实时的

因此，如果要监听“值的实时变化”，监听的是 **`input` 事件，而不是 `change`**。

------

三、React / Vue 中的对应关系（加分点）

在主流前端框架中：

- React 的 `onChange`
  - 行为上等价于原生 `input` 事件
  - 底层基于 `input` 事件封装成合成事件
- Vue 的 `v-model`
  - 本质也是监听原生 `input` 事件
  - 并在 `input` 事件中同步数据

------

四、标准面试**推荐回答**（可直接使用）

> 在 `<input>` 中监听值的变化，本质上是监听原生 DOM 的 `input` 事件。
> `input` 事件在输入内容发生变化时会立即触发，适用于实时获取输入值；而 `change` 事件通常在失去焦点后才触发，不适合实时监听。
> 因此，无论是在原生 JavaScript 还是在 React、Vue 等框架中，输入值变化的监听核心都是 `input` 事件。



#### 2. React 中监听 input 的 onChange 事件的原生事件是什么？

一、核心结论（先给面试官的直接答案）

在 React 中，`onChange` 事件底层**主要基于原生 DOM 的 `input` 事件**，而不是 `change` 事件。
 React 对原生事件做了一层封装，提供了一个**跨浏览器一致的合成事件（SyntheticEvent）**。

------

二、为什么不是原生 `change` 事件

原生 DOM 中：

- `input` 事件：
  - **输入内容实时变化就触发**
  - 适用于 `<input />`、`<textarea />`
- `change` 事件：
  - **失去焦点或回车后才触发**
  - 不是实时的

而 React 的 `onChange`：

- **每次输入内容变化都会触发**
- 行为上等价于原生的 `input` 事件
- 与原生 `change` 的触发时机不同

因此，React 的 `onChange` 并不是简单地绑定了原生 `change`。

------



React 内部会：

- 监听原生的 `input`、`change`、`composition` 等事件
- 在不同浏览器下做兼容处理
- 最终统一派发为一个 **`onChange` 合成事件**

所以：

- 你拿到的不是原生事件对象
- 而是 `SyntheticEvent`
- 但它的行为更接近原生 `input` 事件

------

四、标准面试推荐回答（可直接背）

> 在 React 中，`input` 的 `onChange` 事件底层主要是基于原生 DOM 的 **`input` 事件**，而不是 `change` 事件。
> React 对原生事件进行了封装，通过合成事件机制统一不同浏览器的行为，使 `onChange` 在输入值发生变化时就会触发，表现为实时更新。
> 因此，React 的 `onChange` 在语义上更接近原生的 `input` 事件。



## 10. ClipBoard API

#### 1. 在浏览器中如何获取剪切板中内容

**一、核心知识点（面试必答）**

1. **获取剪切板内容的标准方式：Clipboard API**

浏览器通过 **Clipboard API** 提供对剪切板的访问能力，主要方法是：

- `navigator.clipboard.readText()`
  用于**读取剪切板中的文本内容**
- `navigator.clipboard.writeText(text)`
  用于**向剪切板写入内容**

------

2. **使用前提与限制（非常关键）**

面试官**重点关注这些点**：

1. **必须在安全上下文**

   - 仅支持 `https` 或 `localhost`

2. **必须由用户触发**

   - 通常需要在 `click`、`keydown` 等用户交互事件中调用

3. **返回 Promise**

   - 属于异步 API，需要使用 `then / catch` 或 `async / await`

4. **权限控制**

   - 浏览器可能弹出授权提示
   - 用户可拒绝访问剪切板

   ------

   **二、标准示例**

```javascript
async function getClipboardText() {
  try {
    const text = await navigator.clipboard.readText()
    console.log(text)
  } catch (err) {
    console.error('读取剪切板失败', err)
  }
}
```



---

**三、兼容性与旧方案**

在早期浏览器中，可以通过 `document.execCommand('paste')` 读取剪切板：

```javascript
document.execCommand('paste')
```

但该方式：

- 已被废弃（deprecated）
- 受限严重
- 现代浏览器基本不可用

因此，**不推荐使用**

---

**面试推荐回答**

>   在浏览器中获取剪切板内容，主要通过 HTML5 提供的 Clipboard API。
>   使用 `navigator.clipboard.readText()` 可以异步读取剪切板中的文本内容。
>   该 API 必须运行在 HTTPS 环境下，并且通常需要由用户交互事件触发，比如点击按钮。
>   同时它是基于 Promise 的，浏览器会进行权限控制，用户可能拒绝访问。
>   旧的 `document.execCommand('paste')` 方法已经被废弃，不建议使用。



#### 2.浏览器的剪切板中如何监听复制事件

>   在浏览器中可以通过监听 DOM 的 `copy` 事件来获取用户复制行为，通常使用 `addEventListener('copy', handler)`。在事件对象中可以通过 `event.clipboardData` 读取或设置剪切板内容，例如使用 `getData('text/plain')` 获取复制的文本。
>
>   需要注意的是，剪切板操作受到浏览器安全策略限制，必须由用户主动触发，不能在脚本中随意读取。另外，现代浏览器还提供了 `navigator.clipboard` API，用于在 HTTPS 环境下异步读写剪切板。



#### 3.如何实现页面文本不可复制

>  页面文本不可复制一般通过前端交互限制实现，比如使用 CSS 的 `user-select: none` 禁止文本选中，或者监听 `copy` 事件并调用 `preventDefault()` 阻止复制行为。
>
>  但需要说明的是，这种方式只能提高操作门槛，无法从根本上防止内容被获取。只要内容已经渲染到浏览器中，就一定可以通过开发者工具或网络请求拿到，真正的安全控制必须放在后端。



### 11.fetch 中 credentials 指什么意思



>`fetch` 中的 `credentials` 用于控制请求是否携带浏览器自动管理的凭证信息，比如 Cookie。它有三个取值：
>
>- `omit` 表示不携带任何凭证；
>- `same-origin` 是默认值，仅同源请求携带 Cookie；
>- `include` 表示同源和跨域请求都会携带 Cookie。



### 12.如何取消请求的发送

>在前端中，请求一旦发出就无法真正从客户端撤回，但可以通过浏览器提供的机制中断请求或忽略响应。
>
>对于 `fetch`，通常使用 `AbortController`，通过 `signal` 控制请求，在需要时调用 `abort()` 取消；
>
>对于 `axios`，在新版本中同样推荐使用 `AbortController`，早期的 `CancelToken` 已经废弃；
>
>对于`XMLHttpRequest`,通过 `xhr.abort()` 终止请求



### 13. 如何判断当前环境是移动端还是PC端

当然，不要重复造轮子，推荐一个库: https://github.com/kaimallea/isMobile

```javascript
import isMobile from 'ismobilejs'
 
const mobile = isMobile()
```



### 14. 简单介绍 requestIdleCallback 及使用场景

**一、requestIdleCallback 的核心知识点**

**1. 基本概念**
 `requestIdleCallback` 是浏览器提供的一个 API，用于在**主线程空闲时**执行低优先级任务，避免影响关键渲染和用户交互。

**2. 执行时机**

- 浏览器完成一次帧的渲染（Layout / Paint）
- 当前没有高优先级任务（如用户输入、动画）
- 剩余的空闲时间内执行回调

**3. 回调参数**
 回调函数会接收一个 `IdleDeadline` 对象：

- `deadline.timeRemaining()`：当前空闲时间剩余（毫秒）
- `deadline.didTimeout`：是否因超时被强制执行

**4. 可选配置**

```javascript
requestIdleCallback(callback, { timeout: 2000 })
```

- `timeout`：即使主线程一直忙，超过该时间也会强制执行

**5. 特点总结**

- 非实时执行
- 可能被延迟
- 适合低优先级、可拆分任务

------

**二、典型使用场景**

**1. 首屏渲染后的非关键任务**

- 数据预处理
- 本地缓存写入
- 日志上报

**2. 性能优化场景**

- 大数组的分片计算
- 复杂但不紧急的数据统计
- 长列表预计算

**3. 资源预加载**

- 预加载图片
- 预解析数据
- 非关键模块初始化

---

**三、推荐做法：requestIdleCallback 分片执行**

1️⃣ 将大任务拆成小任务

```javascript
const taskQueue = data.slice() // 复制一份任务队列

function performWork(deadline) {
  while (
    deadline.timeRemaining() > 0 &&
    taskQueue.length > 0
  ) {
    const item = taskQueue.shift()
    heavyFormat(item)
  }

  // 如果任务还没做完，继续等下一个空闲期
  if (taskQueue.length > 0) {
    requestIdleCallback(performWork)
  } else {
    localStorage.setItem('list', JSON.stringify(data))
  }
}

```

2️⃣ 启动空闲任务

```javascript
requestIdleCallback(performWork, { timeout: 2000 })
```

---



**四、面试推荐回答（可直接背）**

>   `requestIdleCallback` 是浏览器提供的一个在主线程空闲时执行任务的 API，主要用于处理低优先级、不影响用户体验的工作。
>  它会在浏览器完成渲染并且有空闲时间时调用回调，并通过 `timeRemaining()` 告诉开发者还能执行多久。
>  常见使用场景包括首屏渲染后的数据处理、日志上报、缓存写入以及大任务的分片执行，用于提升页面性能和响应速度。
>  它不适合用于动画或高实时性任务。
