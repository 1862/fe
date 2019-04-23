# Event Loop机制

单线程的JS为解决"任务排队"问题所创机制

- 任务队列
  - 执行机制
      1. 所有同步任务都在主线程上执行, 形成一个[执行栈](http://www.ruanyifeng.com/blog/2013/11/stack.html)（execution context stack）
      2. 主线程之外, 还存在一个"任务队列"（task queue）只要异步任务有了运行结果, 就在"任务队列"之中放置一个事件
      3. 一旦"执行栈"中的所有同步任务执行完毕, 系统就会读取"任务队列", 看看里面有哪些事件, 那些对应的异步任务结束等待状态, 进入执行栈开始执行
      4. 主线程不断重复上面的第三步

  - 顺序

      - 先进先出

      - 定时器

          > 主线程首先要检查一下执行时间, 某些事件只有到了规定的时间, 才能返回主线程 

- 事件和回调函数

  - 用户产生的事件只要指定过回调函数, 事件发生时就会进入"任务队列", 等待主线程读取

- 事件循环

  ![事件循环](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100802.png)

  > 主线程运行的时候，产生堆（heap）和栈（stack），栈中的代码调用各种外部API，它们在"任务队列"中加入各种事件（click，load，done）。只要栈中的代码执行完毕，主线程就会去读取"任务队列"，依次执行那些事件所对应的回调函数 

  - 执行栈中的代码（同步任务）, 总是在读取"任务队列"（异步任务）之前执行 

- macro task与micro task

  - micro task 微任务
    - `new Promise()`
    - `new MutaionObserver()`
  - macro task 宏任务
    - `setInterval()`
    - `setTimeout()`
  - **同一次事件循环中, 微任务永远在宏任务之前执行** 

- 定时器setTimeout

  ```
  setTimeout(function(){console.log(1);}, 0);
  console.log(2);
  
  //2 1
  ```

  - setTimeout(fn,0)的含义
    - 指定某个任务在主线程最早可得的空闲时间执行, 它在"任务队列"的尾部添加一个事件, 因此要等到同步任务和"任务队列"现有的事件都处理完, 才会得到执行
  - setTimeout最短间隔 
    - 不得低于4毫秒, 如果低于这个值, 就会自动增加 
    - 对于那些DOM的变动（尤其是涉及页面重新渲染的部分）, 通常不会立即执行, 而是每16毫秒执行一次 
  - 注意
    - setTimeout()只是将事件插入了"任务队列", 必须等到当前代码（执行栈）执行完, 主线程才会去执行它指定的回调函数, 要是当前代码耗时很长，有可能要等很久, 所以并没有办法保证，回调函数一定会在setTimeout()指定的时间执行

- Node.js的事件循环

  - process.nextTick 方法
    - 在当前"执行栈"的尾部----下一次Event Loop（主线程读取"任务队列"）之前----触发回调函数 
  - setImmediate  方法 
    - 在当前"任务队列"的尾部添加事件 
  - 区别
    - 多个process.nextTick语句总是在当前"执行栈"一次执行完 
    - 多个setImmediate可能则需要多次loop才能执行完 (setImmediate总是将事件注册到下一轮Event Loop)

- 例子

  ```javascript
  console.log('1');
  
  setTimeout(function() {
      console.log('2');
      process.nextTick(function() {
          console.log('3');
      })
      new Promise(function(resolve) {
          console.log('4');
          resolve();
      }).then(function() {
          console.log('5')
      })
  })
  process.nextTick(function() {
      console.log('6');
  })
  new Promise(function(resolve) {
      console.log('7');
      resolve();
  }).then(function() {
      console.log('8')
  })
  
  setTimeout(function() {
      console.log('9');
      process.nextTick(function() {
          console.log('10');
      })
      new Promise(function(resolve) {
          console.log('11');
          resolve();
      }).then(function() {
          console.log('12')
      })
  })
  ```

  分析：

  1. 首先整段scripts作为宏任务进入第一次事件循环，执行`console.log('1')`。
  2. 遇到`setTimeout`，将其回调函数注册后分发到`Event Queue`（宏任务），等待下一次事件循环取用。
  3. 遇到微任务`process.nextTick`，将其放入第一次事件的微任务队列。
  4. 遇到`promise`，直接执行`console.log('7')`，`.then()`回调作为微任务被放入微任务队列。
  5. 又遇到`setTimeout`，不管，将其回调函数注册后分发到`Event Queue`（宏任务），等待取用，注意这时候事件队列`Event Queue`已有两个宏任务。
  6. 第一次事件循环的宏任务执行完毕，开始执行微任务列表，依次执行`console.log('6')`、`console.log('8')`，微任务执行完毕，第一次事件循环结束，所以第一次事件循环执行过程：`'1'->'7'->'6'->'8'`。
  7. 进程栈为空，开始从事件队列拉取任务，首先取出来第一个任务，作为宏任务进入第二次事件循环。
  8. 先遇到`console.log('2')`，直接执行。
  9. 接着遇到微任务`process.nextTick`，将其放入第二次事件的微任务队列。
  10. 遇到`promise`，直接执行`console.log('4')`，`.then()`回调作为微任务被放入微任务队列。
  11. 第二次循环宏任务执行完毕，开始执行微任务队列任务，依次执行`console.log('3')`、`console.log('5')`，微任务执行完毕，第二次事件循环结束，所以第二次事件循环执行过程：`'2'->'4'->'3'->'5'`。
  12. 进程栈为空，继续从事件队列拉取任务，取出下一个任务，作为宏任务进入第三次事件循环。
  13. 先遇到`console.log('9')`，直接执行。
  14. 接着遇到微任务`process.nextTick`，将其放入第三次事件的微任务队列。
  15. 遇到`promise`，直接执行`console.log('11')`，`.then()`回调作为微任务被放入微任务队列。
  16. 第三次循环宏任务执行完毕，开始执行微任务队列任务，依次执行`console.log('10')`、`console.log('12')`，微任务执行完毕，第二次事件循环结束，所以第二次事件循环执行过程：`'9'->'11'->'10'->'12'`。
  17. 整个事件循环结束。

## 参考

- [Li's BLOG](https://liyang0207.github.io/2018/07/08/JavaScript%E4%B8%AD%E7%9A%84%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF/)