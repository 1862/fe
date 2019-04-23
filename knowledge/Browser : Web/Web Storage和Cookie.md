# Web Storage和Cookie

## Cookie

- 生命周期

  - 一般由服务器生成, 可设置失效时间(自定义时间)
    - 如果在浏览器端生成Cookie, 默认是关闭浏览器后失效

- 大小

  - 每个大小限制**4KB左右**
    - ie 4095字节
    - opera 4096字节
    - firefox safari 4097字节

- 通信

  - 每次都会携带在HTTP头中, 如果使用cookie保存过多数据会带来性能问题 

- 字段说明

  - 名称
    - 不区分大小写, 经过URL编码
  - 路径
    - 指定要把cookie发往的服务器路径

  - 域
    - cookie对于哪个域有效
  - 失效时间 
    - 表示cookie何时应该被删除的时间戳
  - 安全标志
    - 指定后, cookie只有在使用SSL连接的时候才发送到服务器 

## Web Storage

###  localStorage

- 生命周期
  - 除非被清除, 否则永久保存
- 大小
  - 一般为5MB 
- 通信
  - 仅在客户端（即浏览器）中保存, 不参与和服务器的通信 

### sessionStorage

- 生命周期
  - 仅在当前会话下有效
    - 清除的情况
      1. 当前**标签**关闭
      2. 进入非同源页面
- 其他与localStorage一致

### Web Storage

- API

  - getItem(数据项的键 ) 查
  - setItem(数据项的键) 更
  - removeItem(数据项的键, 对应的值) 删
  - clear() 清空域名对应的整个存储对象

- storage事件

  > **当前域名不同页面的localStorage对象**发生改变时该事件监听器会触发 

  ```
  window.addEventListener('storage', function(e) {  
    document.querySelector('.my-key').textContent = e.key;
    document.querySelector('.my-old').textContent = e.oldValue;
    document.querySelector('.my-new').textContent = e.newValue;
    document.querySelector('.my-url').textContent = e.url;
    document.querySelector('.my-storage').textContent = e.storageArea;
  });
  ```

  - key: 发生改变项的key 
  - oldValue: key的原值 
  - newValue: key的新值 
  - url: key改变发生的URL
  - storageArea: 表示存储类型（Session或Local）

- 缺点

  - 数据以简单字符串存储
  - 可被用户或者系统管理员禁用 

- 优点

  - 在在线或离线情况下使用 
  - 能够对浏览器使用事件钩子，例如offline，online，storage change 
  - 比cookies更便于管理，没有额外的的请求头部数据 
  - 提供更大的空间 