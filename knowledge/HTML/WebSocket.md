# WebSocket

## HTTP部分

- HTTP协议与TCP/IP协议的关系
  - HTTP属于应用层协议
  - 在传输层使用TCP协议
    - TCP协议主要解决如何在IP层之上**可靠**的传递数据包
      - A收到B发出的所有包, 且顺序与发出顺序一致
    - TCP有可靠, 面向连接的特点
  - 在网络层使用IP协议
    - IP协议主要解决网络路由和寻址问题


- 短连接

  - 浏览器和服务器每进行一次HTTP操作, 就建立一次连接

  - 步骤

    ```
    建立连接——数据传输——关闭连接...建立连接——数据传输——关闭连接
    ```

  - HTTP 1.0 默认连接

  - 优点

    - 不需要额外的控制手段
    - 不一直占用服务器的资源

  - 缺点

    - TCP的建立和关闭操作上浪费时间和带宽

- 长连接 (Comet的实现方式)

  - 客户端或服务器端认为会话结束, 才断开连接

  - 步骤

    ```
    建立连接——数据传输...（保持连接）...数据传输——关闭连接
    ```

  - HTTP 1.0 头部增加字段 `Connection:keep-alive`

  - HTTP 1.1 默认连接

  - 优点

    - 减少了每次建立连接和断开连接的开销 (复用TCP连接)
    - 减少后续请求响应时间 (因为此时不需要建立TCP, 也不需要TCP握手等过程)
    - 减少的CPU和内存的使用(由于同时打开的连接的减少)

  - 缺点

    - 保持了不必要的连接 (即某一方不能确定何时会话结束)
    - 影响服务器性能 (耗资源, 影响高并发)

- 长连接 / 短连接

  - 本质上是TCP长连接和短连接
  - 差别在于采取的关闭策略

			WebSocket部分

### 背景

在WS之前, 服务器主动向客户端推送消息方案

- 短轮询
  - 在特定的的时间间隔，由浏览器对服务器发出HTTP请求, 然后由服务器返回最新的数据给客户端的浏览器
  - 优点
    - 编写简单
  - 缺点
    - 实时性不够 (需要快速的处理速度)
    - 频繁的请求会给服务器带来极大的压力 (且产生很多无用请求)
- Comet
  - 长轮询 (long-poll, 基于AJAX)
    - 在打开一条连接以后保持长连接, 等待服务器推送来数据再关闭
    - 服务器端会阻塞请求直到有数据传递或超时才返回 (阻塞模型)
    - 客户端 JavaScript 响应处理函数会在处理完服务器返回的信息后，再次发出请求，重新建立连接
    - 假如一直没更新, 返回数据超时
    - 优点
      - 客户端很容易实现良好的错误处理系统和超时管理
    - 缺点
      - 以多线程模式运行的服务器会让大部分线程大部分时间都处于挂起状态, 浪费服务器资源
      - 必须定期发一些ping数据表示连接“正常工作”
  - iframe流
    - 在页面中插入一个隐藏的iframe, 利用其src属性在服务器和客户端之间创建一条长连接, 服务器向iframe传输数据
    - 优点
      - 容易实现, 在所有支持 iframe 的浏览器上都可用
    - 缺点
      - 没有方法可用来实现可靠的错误处理或是跟踪连接的状态
      - 无法跨域
- Comet相较短轮询的优点
  - 处理快
- Comet相较短轮询的缺点
  - 消耗服务器资源, 影响高并发

### 概念

- **WebSocket**是一种在单个[TCP](https://zh.wikipedia.org/wiki/TCP)连接上进行[全双工](https://zh.wikipedia.org/wiki/%E5%85%A8%E9%9B%99%E5%B7%A5)通讯的协议
- 独立的, 建立在TCP上的协议, 与HTTP协议有良好兼容性
- 没有同源限制, 客户端可以与任意服务器通信
- 协议标识符是`ws `, 如果加密则为`wss`([TLS](https://zh.wikipedia.org/wiki/TLS))
- ![示例](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017051503.jpg)

### 优点

- 较少的控制开销 
  - 相对于HTTP请求每次都要携带完整的头部此项开销显著减少


- 更强的实时性 
  - 全双工


- 保持连接状态
  - 先创建连接, 这就使得其成为一种有状态的协议, 之后通信时可以省略部分状态信息
- 更好的二进制支持
  - 定义了二进制帧, 相对HTTP, 可以更轻松地处理二进制内容
- 可以支持扩展
- 更好的压缩效果

### 握手协议

为了创建WebSocket连接, 需要通过浏览器发出请求, 之后服务器进行回应, 这个过程通常称为“握手” (handshaking)

- 通过 [HTTP](https://zh.wikipedia.org/wiki/HTTP) 1.1 的101[状态码](https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)进行握手

- 例子

  客户端请求

  ```
  GET / HTTP/1.1
  Upgrade: websocket
  Connection: Upgrade
  Host: example.com
  Origin: http://example.com
  Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
  Sec-WebSocket-Protocol: chat, superchat
  Sec-WebSocket-Version: 13
  ```

  服务器响应

  ```
  HTTP/1.1 101 Switching Protocols
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
  Sec-WebSocket-Location: ws://example.com/
  ```

  - Connection必须设置Upgrade, 表示客户端希望连接升级
  - Upgrade字段必须设置websocket, 表示希望升级到WebSocket协议
  - Sec-WebSocket-Key是浏览器随机生成的字符串, 验证协议
  - Sec_WebSocket-Protocol是用户定义的字符串, 用来区分同URL下, 不同的服务所需要的协议
  - Sec-WebSocket-Version是表示支持的Websocket版本
    - RFC6455要求使用的版本是13, 之前草案的版本均应当弃用
  - 其他一些定义在HTTP协议中的字段, 如[Cookie](https://zh.wikipedia.org/wiki/Cookie)等, 也可以在WebSocket中使用
  - Sec-WebSocket-Accept是经过服务器确认, 并且加密过后的 Sec-WebSocket-Key (证清白)

### 客户端示例

```
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) { 
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};   
```

(点击[这里](http://jsbin.com/muqamiqimu/edit?js,console)查看运行结果)

### 客户端API

- 构造函数

  - WebSocket构造器方法接受一个必须的参数和一个可选的参数
  - url
    - 表示要连接的URL, 这个URL应该为响应WebSocket的地址
  - protocols (可选)
    - 可以是一个单个的协议名字字符串或者包含多个协议名字字符串的数组 (默认为空字符串)

- 常量

  - readyState

    - 描述 WebSocket 连接的状态

      - CONNECTING: 值为0, 表示正在连接
      - OPEN: 值为1, 表示连接成功, 可以通信了
      - CLOSING: 值为2, 表示连接正在关闭
      - CLOSED: 值为3, 表示连接已经关闭, 或者打开连接失败

    - 示例

      ```
      switch (ws.readyState) {
        case WebSocket.CONNECTING:
          // do something
          break;
        case WebSocket.OPEN:
          // do something
          break;
        case WebSocket.CLOSING:
          // do something
          break;
        case WebSocket.CLOSED:
          // do something
          break;
        default:
          // this never happens
          break;
      }
      ```

- 属性

  - 实例对象常见属性
    - `onopen`属性, 用于指定连接成功后的回调函数
    - `onclose`属性, 用于指定连接关闭后的回调函数
    - `onmessage`属性, 用于指定收到服务器数据后的回调函数
    - `bufferedAmount`属性, 表示还有多少字节的二进制数据没有发送出去, 它可以用来判断发送是否结束
    - `onerror`属性, 用于指定报错时的回调函数
  - 全部属性
    - 参考[MDN WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)

- 方法

  - close
    - 关闭WebSocket连接或停止正在进行的连接请求
    - 如果连接的状态已经是`closed`, 这个方法不会有任何效果
    - 可能抛出的异常
      - INVALID_ACCESS_ERR 选定了无效的code
      - SYNTAX_ERR `reason` 字符串太长或者含有unpaired surrogates
  - send
    - 通过WebSocket连接向服务器发送数据
    - 可能抛出的异常
      - INVALID_STATE_ERR 当前连接的状态不是`OPEN`
      - SYNTAX_ERR 数据是一个包含unpaired surrogates的字符串

### 服务端实现

WebSocket 服务器的实现, 可以查看维基百科的[列表](https://en.wikipedia.org/wiki/Comparison_of_WebSocket_implementations)

常用的 Node 实现有以下三种

- [µWebSockets](https://github.com/uWebSockets/uWebSockets)
- [Socket.IO](http://socket.io/)
- [WebSocket-Node](https://github.com/theturtle32/WebSocket-Node)