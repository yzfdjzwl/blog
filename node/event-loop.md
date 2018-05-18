# 浅析Node.js的Event Loop

* [浅析Node.js的Event Loop](#浅析nodejs的event-loop)
	* [引出问题](#引出问题)
	* [Node.js的基本架构](#nodejs的基本架构)
	* [Libuv](#libuv)
	* [Event Loop](#event-loop)
		* [Event Loop Phases Overview](#event-loop-phases-overview)
			* [Poll Phase](#poll-phase)
		* [The Heart Of Event Loop](#the-heart-of-event-loop)
	* [MacroTask VS MicroTask](#macrotask-vs-microtask)
		* [它是如何工作的？](#它是如何工作的)
	* [推荐阅读](#推荐阅读)
	* [参考](#参考)

## 引出问题

首先看两段代码，下面两段代码的执行结果是什么？为什么？

```javascript
// event-loop-1.js
setTimeout(() => {
    console.log('setTimeout');
}, 0);
setImmediate(() => {
    console.log('setImmediate');
});


// event-loop-2.js
const fs = require('fs');
fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('setTimeout');
    }, 0);
    setImmediate(() => {
        console.log('setImmediate');
    });
});
```

也许你心里已经有了答案，但是就是不太确定，其实这里面涉及到的知识点就是今天要说的`Event Loop`。

## Node.js的基本架构

在讲`Event Loop`之前，首先介绍一下Node.js的基本架构。提到Node.js的时候，我们耳熟能详的是: Node.js是一个基于ChromeV8引擎的JavaScript运行时。Node.js 使用高效、轻量级的事件驱动、非阻塞 I/O 模型。那么这句话真正想要表达的是什么呢？请看下图：

![](http://p0.meituan.net/codeman/e1324e3f1d7a6dbca59abfb7abeefc1b69981.jpg)

* Node standard library: Node的标准库，也就是我们平时所用的`fs, path, http, net, stream`等模块。
* Node bindlings: 是C++与JavaScript沟通的桥梁, 封装了V8和Libuv的细节，向上层提供API。
* 最后一层是支撑Node的关键。

使用`tree -L 1`可以看到Node.js源码的目录如下:

```
➜  node git:(master) tree -L 1
.
├── AUTHORS
├── BSDmakefile
├── BUILDING.md
├── CHANGELOG.md
├── CODE_OF_CONDUCT.md
├── COLLABORATOR_GUIDE.md
├── CONTRIBUTING.md
├── CPP_STYLE_GUIDE.md
├── GOVERNANCE.md
├── LICENSE
├── Makefile
├── README.md
├── android-configure
├── benchmark
├── common.gypi
├── configure
├── deps
├── doc
├── lib
├── node.gyp
├── node.gypi
├── src
├── test
├── tools
└── vcbuild.bat
```

而比较关键的几个目录是：

* deps: 一些Node.js的依赖库，比如Libuv, V8等。
* src: 包含C++的源码，即Node bindings。
* lib: 包含JavaScript的源码，存放的是Node.js的核心模块，即`fs, path, http, net, stream`等模块。

## Libuv

我们知道Node.js是一个Runtime, 它拥有异步，非阻塞的模型，那么它是如何实现非阻塞的呢？答案是：Libuv。

![](http://p0.meituan.net/codeman/4b006aaf3e6f0d6f2143924680c046a541942.png)

什么是Libuv？Libuv是一个高性能的，事件驱动的I/O库，并且提供了 ***跨平台***（如windows, *nix）的API。简单的来说，Node.js的异步、非阻塞I/O，底层实际上是Libuv实现的。

具体更多关于[Libuv](https://github.com/libuv/libuv)的知识这里不再阐述，感兴趣的同学下来可以去多了解一下。

## Event Loop

可以参考Node.js官方文档上的这样一篇文档: [The Node.js Event Loop, Timers, and process.nextTick()](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/), 我们可以知道:

 >When Node.js starts, it initializes the event loop, processes the provided input script (or drops into the REPL, which is not covered in this document) which may make async API calls, schedule timers, or call process.nextTick(), then begins processing the event loop.

即在Node.js启动的时候，它会初始化`Event Loop`, 处理提供的输入脚本, 这可能会使异步API调用，调用timers，或者调用`process.nextTick`, 然后开始处理事件循环。
下图简单展示了事件循环的操作顺序:

```
┌───────────────────────┐
┌─>│        timers         │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     I/O callbacks     │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     idle, prepare     │
│  └──────────┬────────────┘      ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │
│  │         poll          │<─────┤  connections, │
│  └──────────┬────────────┘      │   data, etc.  │
│  ┌──────────┴────────────┐      └───────────────┘
│  │        check          │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
└──┤    close callbacks    │
└───────────────────────┘
```

注意：每个盒子被当作Event Loop的一个阶段。

每个阶段都有一个执行回调的FIFO队列(官网这么描述的，实际上不是的，比如timers的数据结构实际上是堆), 简单概述，当Event Loop进入到某个阶段的时候，就会将该阶段的队列里的回调拿出来执行，直到队列为空(实际上要复杂一点儿)。

### Event Loop Phases Overview

简单的介绍一下这几个阶段所做的事情：

* timers: 这个阶段执行由`setTimeout()`和`setInterval()`调度的回调。
* I/O callbacks: 执行几乎所有的回调，除了`close callbacks`以及timers调度的回调和`setImmediate()`调度的回调。
* idle, prepare: 只在内部使用。
* poll: 检索新的I/O事件，node将在适当的时候阻塞。(retrieve new I/O events; node will block here when appropriate)
* check: `setImmediate()`的回调将会在这个阶段执行。
* close callbacks: 比如`socket.on('close', ...)`。

上面的阶段还是笔记容易理解的，就是poll阶段的解释有点儿让人迷惑，这是什么意思呢？官方文档给出了poll阶段的作用。

#### Poll Phase

poll阶段有两个功能:

* 当timers到达指定的时间后，执行指定的timer的回调(Executing scripts for timers whose threshold has elapsed, then)。
* 处理poll队列的事件(Processing events in the poll queue)。

当进入到poll阶段，并且没有timers被调用的时候，会发生下面的情况:

* 如果poll队列不为空，Event Loop 将同步的执行poll queue里的callback，直到queue为空或者执行的callback到达上线。
* 如果poll队列为空，则会发生下面的情况:
    * 如果脚本调用了`setImmediate()`, Event Loop将会结束poll阶段并且进入到`check`阶段执行`setImmediate()`的回调。
    * 如果脚本没有被`setImmediate()`调用，Event Loop将会等待回调被添加到队列中，然后立即执行它们。
当进入到poll阶段，并且调用了timers的话，会发生下面的情况:
* 一旦poll queue是空的话，Event Loop会检查是否timers, 如果有1个或多个timers时间已经到达，Event Loop将会回到timer阶段并执行那些timer的callback(即进入到下一次tick)。


看了上面的介绍，比较I/O callbacks阶段与poll阶段，可能会感到迷惑？为什么在I/O callbacks是执行几乎所有的回调，而在poll阶段也是执行回调？我找到了[Libuv的官方文档](http://docs.libuv.org/en/v1.x/design.html#the-i-o-loop):
> Pending callbacks are called. All I/O callbacks are called right after polling for I/O, for the most part. There are cases, however, in which calling such a callback is deferred for the next loop iteration. If the previous iteration deferred any I/O callback it will be run at this point.

结合[Libuv官方文档](http://docs.libuv.org/en/v1.x/design.html#the-i-o-loop)给出的流程图

![](http://p1.meituan.net/codeman/624c9768d8888b109a4649298c0cb09180528.png)

来看, 可以翻译为：Pending callbacks(即I/O callbacks)被调用。大多数情况下，所有的I/O callbacks都是在`poll for I/O`(即poll phase)后理解调用的。然而，有些情况，会在下一次tick调用，以前被推迟的`I/O callback`会在下一次tick的I/O阶段调用。

那么一般什么样的`callback`会在I/O callbacks阶段被调用呢?[Node.js官方](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)有提到：
> This phase executes callbacks for some system operations such as types of TCP errors. For example if a TCP socket receives ECONNREFUSED when attempting to connect, some *nix systems want to wait to report the error. This will be queued to execute in the I/O callbacks phase.

即：这个阶段对某些系统操作(比如TCP类型错误)执行回调。举个例子，如果尝试连接时,一个TCP套接字收到了`ECONNREFUSED`，则某些`*nix`系统会等待报错。这将排队在`I/O callbacks`阶段执行。

对于文档上的说法去一探究竟，在Node.js源码里全局搜索: `ECONNREFUSED`, 在[node/deps/uv/src/unix/tcp.c](https://github.com/nodejs/node/blob/master/deps/uv/src/unix/tcp.c
)目录下，第206行，`uv__tcp_connect`函数，代码如下：
```c
int uv__tcp_connect(uv_connect_t* req,
                    uv_tcp_t* handle,
                    const struct sockaddr* addr,
                    unsigned int addrlen,
                    uv_connect_cb cb) {
  int err;
  int r;

  assert(handle->type == UV_TCP);

  if (handle->connect_req != NULL)
    return -EALREADY;  /* FIXME(bnoordhuis) -EINVAL or maybe -EBUSY. */

  err = maybe_new_socket(handle,
                         addr->sa_family,
                         UV_STREAM_READABLE | UV_STREAM_WRITABLE);
  if (err)
    return err;

  handle->delayed_error = 0;

  do {
    errno = 0;
    r = connect(uv__stream_fd(handle), addr, addrlen);
  } while (r == -1 && errno == EINTR);

  /* We not only check the return value, but also check the errno != 0.
   * Because in rare cases connect() will return -1 but the errno
   * is 0 (for example, on Android 4.3, OnePlus phone A0001_12_150227)
   * and actually the tcp three-way handshake is completed.
   */
  if (r == -1 && errno != 0) {
    if (errno == EINPROGRESS)
      ; /* not an error */
    else if (errno == ECONNREFUSED)
    /* If we get a ECONNREFUSED wait until the next tick to report the
     * error. Solaris wants to report immediately--other unixes want to
     * wait.
     */
      handle->delayed_error = -errno;
    else
      return -errno;
  }

  uv__req_init(handle->loop, req, UV_CONNECT);
  req->cb = cb;
  req->handle = (uv_stream_t*) handle;
  QUEUE_INIT(&req->queue);
  handle->connect_req = req;

  uv__io_start(handle->loop, &handle->io_watcher, POLLOUT);

  if (handle->delayed_error)
    uv__io_feed(handle->loop, &handle->io_watcher);

  return 0;
}
```
从上面的代码我们可以知道，当`errno === ECONNREFUSED`时，会去调用`uv__io_feed(handle->loop, &handle->io_watcher)`方法，看一下[uv__io_feed](https://github.com/nodejs/node/blob/master/deps/uv/src/unix/core.c)的的实现:

```c
void uv__io_feed(uv_loop_t* loop, uv__io_t* w) {
  if (QUEUE_EMPTY(&w->pending_queue))
    QUEUE_INSERT_TAIL(&loop->pending_queue, &w->pending_queue);
}
```
从函数名字可以看出来，这里是在向`pendingQueue`插入发生错误时的回调。也就是说，`I/O callbacks`一般是对一些系统操作执行回调。

那么我们可以得出结论：
* 大部分的回调在poll阶段执行的。
* `I/O callbacks`阶段一般执行的是系统操作的回调。

### The Heart Of Event Loop

有了上面的知识后，我们依然不能解决文章开头的问题。来看一下，[Event Loop核心的代码](https://github.com/nodejs/node/blob/master/deps/uv/src/unix/core.c)：
```c
int uv_run(uv_loop_t* loop, uv_run_mode mode) {
  int timeout;
  int r;
  int ran_pending;

  r = uv__loop_alive(loop);
  if (!r)
    uv__update_time(loop);

  while (r != 0 && loop->stop_flag == 0) {
    uv__update_time(loop);
    uv__run_timers(loop);
    ran_pending = uv__run_pending(loop);
    uv__run_idle(loop);
    uv__run_prepare(loop);

    timeout = 0;
    if ((mode == UV_RUN_ONCE && !ran_pending) || mode == UV_RUN_DEFAULT)
      timeout = uv_backend_timeout(loop);

    uv__io_poll(loop, timeout);
    uv__run_check(loop);
    uv__run_closing_handles(loop);

    if (mode == UV_RUN_ONCE) {
      /* UV_RUN_ONCE implies forward progress: at least one callback must have
       * been invoked when it returns. uv__io_poll() can return without doing
       * I/O (meaning: no callbacks) when its timeout expires - which means we
       * have pending timers that satisfy the forward progress constraint.
       *
       * UV_RUN_NOWAIT makes no guarantees about progress so it's omitted from
       * the check.
       */
      uv__update_time(loop);
      uv__run_timers(loop);
    }

    r = uv__loop_alive(loop);
    if (mode == UV_RUN_ONCE || mode == UV_RUN_NOWAIT)
      break;
  }

  /* The if statement lets gcc compile it to a conditional store. Avoids
   * dirtying a cache line.
   */
  if (loop->stop_flag != 0)
    loop->stop_flag = 0;

  return r;
}
```

上面代码可以简化为下面的伪代码：

```c
while(true) {
    uv__update_time(loop); // 使用Linux下的高精度Timer hrtime更新loop->time,即event loop的时间戳
    uv__run_timers(loop);
    uv__run_pending(loop);
    uv__run_idle(loop);
    uv__run_prepare(loop);
    uv__io__poll(loop, timeout);
    uv__run_check(loop);
    uv__run_closing_handlers(loop);

    // Node默认的mode是`UV_RUN_ONCE`
    if (mode == UV_RUN_ONCE) {
        uv__run_timers();
        uv__update_time(loop); // 更新loop->time
    }
}
```
实际上，在一次tick的时候，首先会去调用一次`uv__run_timers`去处理timers, 然后在最后`if`语句里，还会去调用`uv__run_timers`。

我在[timers](https://github.com/nodejs/node/blob/master/lib/internal/timers.js)的实现里, 找到下面的代码：
```javascript
function Timeout(callback, after, args, isRepeat) {
  after *= 1; // coalesce to number or NaN
  if (!(after >= 1 && after <= TIMEOUT_MAX)) {
    if (after > TIMEOUT_MAX) {
      process.emitWarning(`${after} does not fit into` +
                          ' a 32-bit signed integer.' +
                          '\nTimeout duration was set to 1.',
                          'TimeoutOverflowWarning');
    }
    after = 1; // schedule on next tick, follows browser behavior
  }

  this._called = false;
  this._idleTimeout = after;
  this._idlePrev = this;
  this._idleNext = this;
  this._idleStart = null;
  // this must be set to null first to avoid function tracking
  // on the hidden class, revisit in V8 versions after 6.2
  this._onTimeout = null;
  this._onTimeout = callback;
  this._timerArgs = args;
  this._repeat = isRepeat ? after : null;
  this._destroyed = false;

  this[async_id_symbol] = ++async_id_fields[kAsyncIdCounter];
  this[trigger_async_id_symbol] = getDefaultTriggerAsyncId();
  if (async_hook_fields[kInit] > 0) {
    emitInit(this[async_id_symbol],
             'Timeout',
             this[trigger_async_id_symbol],
             this);
  }
}
```
也就是说，实际上`setTimeout(fn, 0);`最后会变为`setTimeout(fn, 1);`在一次tick的时候，大概的流程是这样的：

* 首先更新loop->time([uv__update_time](https://github.com/nodejs/node/blob/master/deps/uv/src/unix/internal.h))
    ```c
    UV_UNUSED(static void uv__update_time(uv_loop_t* loop)) {
      /* Use a fast time source if available.  We only need millisecond precision.
       */
      loop->time = uv__hrtime(UV_CLOCK_FAST) / 1000000;
    }
    ```
* 上面的`uv__hrtime(UV_CLOCK_FAST)`的值是精确到纳秒的，因此`loop->time`最后的结果可能是大于1的，也有可能是小于1的。
* 然后[uv__run_timers(loop)](https://github.com/nodejs/node/blob/master/deps/uv/src/unix/timer.c)被调用:
    ```c
    void uv__run_timers(uv_loop_t* loop) {
      struct heap_node* heap_node;
      uv_timer_t* handle;

      for (;;) {
        heap_node = heap_min((struct heap*) &loop->timer_heap);
        if (heap_node == NULL)
          break;

        handle = container_of(heap_node, uv_timer_t, heap_node);
        if (handle->timeout > loop->time)
          break;

        uv_timer_stop(handle);
        uv_timer_again(handle);
        handle->timer_cb(handle);
      }
    }
    ```

有了上面的理解后，就可以得到文章最开始的答案了，对于`event-loop-1.js`:
```javascript
/*
 * 如果第一次loop准备前的耗时超过1ms, 即loop->time > 1, 则会先执行setTimeout, 再执行setImmediate
 * 如果第一次loop准备前的耗时小于1ms，即loop->time < 1, 则会先执行setImediate，然后在执行setTimeout
 */
setTimeout(function() {
    console.log('setTimeout');
}, 0);

setImmediate(function() {
    console.log('setImmediate');
});
```

而对于`event-loop-2.js`:

```javascript
/*
 * 由于是在回调里面调用的setTimeout, setImmediate两个函数
 * 首先在poll阶段，执行回调函数
 * 然后进入到check阶段，会执行setImmediate()的回调函数
 * 最后进入在执行setTimeout()的回调函数
 *
 */
const fs = require('fs');
fs.readFile(__filename, () => {
    setTimeout(function() {
        console.log('setTimeout');
    }, 0);
    setImmediate(function() {
        console.log('setImmediate');
    });
});
```

## MacroTask VS MicroTask

在[Node.js官网文档](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)的描述中，提到了`process.nextTick()`, 它不属于`Libuv`的部分，实际上，它是属于Node.js的一部分。

实际上，除了Libuv里面要处理的回调，在Node.js里还有另外两个queue，分别是`Next Tick Queue`以及`MicroTask Queue`。

* `Next Tick Queue`: 使用`process.nextTick()`添加的回调。
* `MicroTask Queue`: 包含一些microtasks比如`resolved promise callbacks`。

那MacroTask是什么呢？Macrotask实际上就是上面我们遇到的那些异步任务，也被称为Task, 也就是说，有的人会将`MacroTask Queue`称为`Task Queue`。

### 它是如何工作的？

我们结合一张图来看看它在Event Loop是如何工作的：

![](http://p1.meituan.net/codeman/441db12a0388ee230bf4d238e19ebe3518591.png)

***在Event Loop完成一个阶段，然后到另一个阶段之前，Event Loop将会执行这`Next Tick Queue`以及`MicroTask Queue`里面的回调, 直到这两个队列为空。一旦它们空了后，Event Loop会进入到下一个阶段。***

***很多人会将这两个队列都当作是`MicroTask Queue`***, 因为它们是处于同一阶段执行的， 实际上，这两个队列执行依然是有一个先后顺序的: `Next Tick Queue`的优先级高于`MicroTask Queue`, ***注意：我们这里将两个队列称为`Immediate Queue`。***

> E.g, The event loop is currently processing the immediates queue which has 5 handlers to be processed. Meanwhile, two handlers are added to the next tick queue. Once the event loop completes 5 handlers in the immediates queue, event loop will detect that there are two items to be processed in the next tick queue before moving to the close handlers queue. It will then execute all the handlers in the next tick queue and then will move to process the close handlers queue.

上面的那段话引用来自[Event Loop and the Big Picture — NodeJS Event Loop Part 1](https://jsblog.insiderattack.net/event-loop-and-the-big-picture-nodejs-event-loop-part-1-1cb67a182810), 即`Event Loop`在处理拥有5个`handlers`的`Next Tick Queue`时，有2个`handlers`被添加到`Next Tick Queue`， 一旦5个`handlers`被处理完后，Event Loop会接着处理`Next Tick Queue`里面新增的两个`handlers`, 然后再处理`MicroTask Queue`里的回调，当`Immediate Queue`里面的回调都处理完成后，Event Loop将会进入到下一个阶段。举个例子：

```javascript
Promise.resolve().then(() => {
  console.log('resolve1');
});

process.nextTick(function() {
  console.log('tick1');
  process.nextTick(function() {
    console.log('tick2');
  });
  process.nextTick(function() {
    console.log('tick3');
  });
});

Promise.resolve().then(() => {
  console.log('resolve2');
});

process.nextTick(function() {
  console.log('tick4');
});


Promise.resolve().then(() => {
  console.log('resolve3');
});

process.nextTick(function() {
  console.log('tick5');
});
```

那么上面的执行顺序是：`tick1, tick4, tick5, tick2, tick3, resolve1, resolve2, resolve3`。不要递归调用`process.nextTick`, 因为这会导致`I/O starvation`。


## 推荐阅读

* [Philip Roberts: What the heck is the event loop anyway? | JSConf EU 2014(浏览器端的Node.js，需fq)](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
* [Event Loop and the Big Picture — NodeJS Event Loop Part 1(文中有该系列的其他文章)](https://jsblog.insiderattack.net/event-loop-and-the-big-picture-nodejs-event-loop-part-1-1cb67a182810)

## 参考

* [Node.js源码](https://github.com/nodejs/node)
* [The Node.js Event Loop, Timers, and process.nextTick()](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)
* [Node.js Event Loop 的理解 Timers，process.nextTick()](https://cnodejs.org/topic/57d68794cb6f605d360105bf)
* [深入理解js事件循环机制（Node.js篇）](深入理解js事件循环机制（Node.js篇）)
* [Event Loop and the Big Picture — NodeJS Event Loop Part 1(文中有该系列的其他文章)](https://jsblog.insiderattack.net/event-loop-and-the-big-picture-nodejs-event-loop-part-1-1cb67a182810)
* [Libuv官方文档](http://docs.libuv.org/en/v1.x/design.html#the-i-o-loop)
* [deep-into-node(非常不错的书，推荐阅读)](https://github.com/yjhjstz/deep-into-node)
* [深入浅出Node.js](https://item.jd.com/11355978.html)
