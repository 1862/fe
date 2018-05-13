# DOM&BOM

## DOM

- 事件流

  - 默认冒泡 false
  - e.stopPropagation()
  - e.preventDefault()

- 事件代理

- 宽高和定位

  - clientWidth/clientHeight内容的容器内可见宽高

  - offsetWidth/offsetHeight内容的盒子(不包括margin)宽高

  - scrollWidth/scrollHeight内容真正的宽高

    > 参考JQuery

- 滚动条

  - e.target.scrollTop

- UI组件设计

  - 结构设计
  - API 设计
  - 控制流设计

## BOM

- 常用API
  - alert / confirm
  - Location
    - **window.location** 只读属性, 返回一个 [`Location`](https://developer.mozilla.org/zh-CN/docs/Web/API/Location)  对象, 其中包含有关文档当前位置的信息 
  - navigator.userAgent
    - 识别浏览器的方法之一
    - 有弱规范, 很多厂商会隐藏或伪装自己的浏览器
    - http 请求时会发送, 服务器端也能够获得 (可用来统计)
    - 在某些特定情况下可以用来判断浏览器规避特定 bug
    - 在通常情况下不作识别浏览器差异化使用
      - 推荐采用特性检测
  - Cookie
    - 网站跟踪用户行为, 服务器用以标记 Session
    - 少量数据, 跟随 http 请求 (header) 发送
    - 可设置过期时间
    - 网站 Cookie 数量有限制
    - 支持 http-only
  - Storage
    - 存储的数据量比较大, 但仍有限制
    - 数据格式为字符串, 可以用 JSON 解析
    - 不会随着 http 请求发给服务器
  - XHR(Ajax)
  - WebSockets
  - Canvas