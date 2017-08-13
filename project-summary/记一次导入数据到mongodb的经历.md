## 背景

公司的一个crm系统，需要导一些数据到线上的去，需求来的时候，感觉好像也没有什么难的，但是自己做的还是遇到了很多很多问题，才发现自己的不足之处。

涉及到的技术: 数据库是`mongodb`, 脚本语言为`node.js`。

### 商量数据格式

客户过来要数据格式的时候，也没有太在意，之前系统里有数据导入的功能，让客户直接下载数据导入功能提供的模板，但是当客户填了部分数据在向我们询问有些数据的时候，一下才反应过来，自己的一句先填着留下了好多坑。。。


1. 首先，先看表里有哪些字段，一般来说，表里提供的字段基本上都是必须的，因此在给客户说我们需要哪些数据的时候一定要说清楚，不然到后面真的是踩坑，这个是后话了。

2. 研究每张表里对插入的数据做了哪些限制，比如客户表里限制: 客户名称 + 经办人必须唯一。联系人表里又做了限制: 客户名称 + 联系人名称 + 经办人必须唯一。类似于这种地方一定要和客户沟通好。

3. 由于是一个crm系统，所以数据的关联性特别强，这点儿最重要了。
    * 写代码之前要考虑清楚插入数据的时候，需要关联到的另一条数据是什么，怎么保证关联到的另一条数据是唯一的？
    * 这特别重要，比如之前插入联系人的时候只考虑客户唯一就是了，但是实际的数据，客户并不是唯一的，有可能同一个客户，比如"京东", 但是经办人是两个，如果只考虑客户名称一样，插入的数据就会是乱的，因此还需要将经办人考虑到。
    * 好在给出的客户的经办人和联系人的经办人是同一个，如果不是这个样的话，就会出大问题。
    * 像一些字段里的值，是常量字符串，前端代码里也是用到了这些常量字符串的，如果插入的常量字符串有问题，那么就会导致前端报错，因此一定要注意插入的常量字符串是否和商量的一致。
    * 因为是excel表格， 之前只考虑到每项数据前后会出现空格，但是没有考虑到还会有回车以及换行的这两种情况。可能还有更多的情况。
    * 有的时候只考虑了if的时候，因为根据数据上来看，只有if这种情况，但是数据量很大，根本看不出来，所以一定要做好异常处理，不然到后面出问题就是给自己埋坑。

### 开始写代码

以前老是听到回调地狱，回调地狱这四个字，没有写过的时候，根本不知道这四个字给代码的可读性带来多么差的体验，自己可能都会把自己搞懵, 下面给看之前写的部分垃圾代码, 除了能用之外，毫无可读性而言。

自己主要遇到的问题有:

#### for-loop-callback

一个简单的流程如下:

1. 解析CSV文件。
2. 对解析出来的结果数组进行遍历，插入到数据库里。
3. 结束后，调用回调函数。

不熟悉JavaScript语言特性的同学乍一看，觉得没什么，比如我。但是写着写着才想起JavaScript单线程这一特性，附[阮老师: JavaScript 运行机制详解](http://www.ruanyifeng.com/blog/2014/10/event-loop.html), [Philip Roberts的演讲](《Help, I'm stuck in an event-loop》)。

我从网上找到的各种解决办法:

##### 1 setTimeout 递归(与我的没有多大关系)

来自[delay a for loop with javascript](https://stackoverflow.com/questions/27443185/delay-a-for-loop-with-javascript)。

```
function jsHello(i) {
    if (i < 0) return;
    setTimeout(function() {
        alert("Hello" + i);
        jsHello(--i);
    });
}
```

##### 2 setTimeout function(与我的没有多大关系)

```
for (var i = 0; i < 10; i++) {
    doSomething(i);
}

function doSomething(j) {
    setTimeout(function() {
        console.log(j);
    }, j * 1000);
}
```

##### 3 async库

[地址](https://github.com/caolan/async#each)

#### 4 很诡异的解决办法

[来自](https://stackoverflow.com/questions/16290375/node-js-callback-function-at-the-end-of-each-iteration-in-a-for-loop)

我感觉这种解决办法很猥琐，因为多了一个变量。但是他确实又是一个挺好的解决办法。

```
// 如果你只是调用
var numCompletedCalls = 0;
for (var i = 0; i < data.length; i++) {
    callDatabase(function(results) {
        numCompletedCalls++;
        if (numCompletedCalls === data.length) {
            // 循环执行完之后，调用回调函数
            callback();
        }
    });
}
```

##### 5 promise all

这个办法应该是可以的，但是自己还是没有测试过。

#### 回调地狱解决办法

##### 1 Promise

[mdn: Promise](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)

##### 2 ES7: Async, Await


#### 自己不熟的JavaScript的一些API

### mongodb的一些基本操作

### 踩的坑


