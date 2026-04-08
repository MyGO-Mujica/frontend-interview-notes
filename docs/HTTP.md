# HTTP

### 常见面试问题

#### 1. URL

##### 1. URL输入浏览器之后会发生什么？

整体流程：

> **解析 URL → DNS 解析 → 建立连接 → 发送 HTTP 请求 → 服务器处理 → 返回响应 → 浏览器解析渲染 → 页面显示**

详细拆解

1. URL 解析
   浏览器会先解析你输入的 URL，比如：

```
https://www.example.com:443/path?a=1#hash
```

解析出：

- 协议（HTTPS）
- 域名（www.example.com）
- 端口（443）
- 路径（/path）
- 查询参数（a=1）
- hash（#hash，不参与请求）

同时浏览器还会做：

- 检查是否是合法 URL
- 补全协议（你输入 google.com 时自动加 https）

2.  DNS 解析（域名 → IP）
    **查询顺序：**

          1. 浏览器缓存
          2. 操作系统缓存
          3. hosts 文件
          4. 本地 DNS 服务器
          5. 根 DNS → 顶级域 → 权威 DNS

    最终得到 IP，比如：www.example.com → 93.184.216.34

3.  建立连接（TCP / TLS）
    (1) TCP 三次握手
    客户端和服务器建立连接：
    - SYN（客户端）
    - SYN + ACK（服务器）
    - ACK（客户端）

目的是确保双方通信正常

（2）如果是 HTTPS，还要 TLS 握手
做的事情：验证服务器证书、协商加密算法、生成会话密钥
握手之后通信都是加密的

4. 发送 HTTP 请求
   浏览器构造请求报文

```
GET /path HTTP/1.1
Host: www.example.com
User-Agent: Chrome
Cookie: xxx
```

可能涉及：

- Cookie（登录态）
- 缓存（If-None-Match / If-Modified-Since

5. 服务器处理请求

6. 返回 HTTP 响应

7. 浏览器解析与渲染（重点）
   1. 解析 HTML → DOM 树
   2. 解析 CSS → CSSOM 树
   3. 合并 → DOM + CSSOM 合并 生成 Render Tree
   4. 布局（Layout / Reflow）
   5. 分层（Layer）
   6. 绘制（Paint）
   7. 合成（Composite）

8. JS 执行（阻塞点）
   注意：

- JS 会阻塞 HTML 解析
- CSS 会阻塞渲染（但不阻塞 DOM 解析）

所以才有：

- `defer`
- `async`

9.  页面加载完成

**总结：**

> 用户输入 URL 后，浏览器会先解析 URL，然后通过 DNS 解析获取服务器 IP，接着通过 TCP（三次握手）建立连接，如果是 HTTPS 还会进行 TLS 握手。连接建立后浏览器发送 HTTP 请求，服务器处理后返回响应。浏览器拿到 HTML 后会解析生成 DOM 树，同时解析 CSS 生成 CSSOM，再合成渲染树，经过布局和绘制最终展示页面。在这个过程中，JS 可能会阻塞解析，同时还涉及缓存、重定向和网络优化等机制。


##### 2. tcp为什么需要三次握手
![三次握手示例](https://images.openai.com/static-rsc-4/K6wAmvfPmJk4t6_AXbka8sRHSwZ-1QKE2guONxPXxAdkfs03SU6tCIjtKk1aiVwE0xuFxHlJgtrU9As9qtYiGnvXgdZsC9gQTZjY0uCa-vmehEtnDrRTHlxsskd_pzIRkyRNOpJYUCKA9gCFkCOFDcR7j9Ri3_LLs14rpUoY8Lg?purpose=inline)




**TCP 需要三次握手，是为了：**

1. 确认双方的收发能力正常
2. 同步双方的初始序列号（ISN）
3. 避免历史连接（旧报文）干扰新连接

