## Linux

### tmux

如何查看

tmux如何进行滚动呢？
1. `prefix + [`, prefix为tmux的前置动作，默认是`ctrl + b`。
2. 使用方向键或者pageUp来进行翻页。
3. `q`可以退出滚动模式。
4. 支持vi模式，需要在`.tmux.conf`里面配置: `set -g mode-keys vi`

### 进程号, 端口

ps命令查找与进程相关的PID号，可以通过man来查询ps的一些参数。

这里讲解下最常用的用法: `ps aux | grep vim`, 查看所有的vim进程。`ps aux | grep vim | grep -v grep`, 查看所有的vim进程，去处掉当前的grep进程。一般查询出来的结果就是进程的id。

```
1. linux
// 先查看进程的pid
ps aux | grep vim

// 根据进程的pid，使用netstat查看该进程所占用的端口
netstat -nap | grep pid

2. mac
// 直接查询与listen相关的进程所有的情况(端口号和进程号都可以看见)
lsof -i -P | grep -i "listen"
```

#### 参考

* [linux 根据进程名查看其占用的端口](http://blog.csdn.net/bhq2010/article/details/7370354)
* [命令行查看端口号被哪个进程占用（转）](http://www.cnblogs.com/chenwenbiao/archive/2012/06/24/2559954.html)
* [Linux如何查看进程、杀死进程、启动进程等常用命令](http://blog.csdn.net/wojiaopanpan/article/details/7286430/)


## ES6

### Object.values

`Object.values`场景: `Object.values(attr.value).some(x => !x)`, 表示只要有一个为空，则返回true。

但是`Object.values`浏览器支持的不好，但是`Object.keys`却支持的非常好，因此可以这样来搞:`const values = Object.keys(obj).map(x => obj[x]);`。

### Symbol

`Symbol`是ES6新引入的一个变量类型，它表示独一无二，可以这样来定义一个独一无二的值，它和任何其他变量都不相等: `const a = Symbol('key')`。

## CSS

三列布局，常见的那种等宽三列布局，就是每列的左右距离都是一样的。下面介绍常用的几种方法:

```
// flex
.parent {
    display: flex;
    justify-content: space-between;
}

// text-align, 它用于inline-block和inline元素
.parent {
    display: block;
    text-align: center;
}

.parent .left {
    float: left;
    /*absolute*/
}

.parent .right {
    float: right;
    /*absolute*/
}

.parent .midde {
    display: inline-block;
}
```

## 坑

### localStorage

有一个需求是，在Table展示页面的时候，点击单条数据跳到新页面，然后新页面点击跳到第三个页面，需要将某个数据在第三个页面里进行展示。由于数据结构比较复杂，如果采用`reducer`的话，太过于麻烦了, 会花费很多时间。

因此，想了一个比较猥琐的解决办法，就是存到`localStorage`里面去, 每次点击Table里一条数据的时候，就将该数据存到`localStorage`里面去。但是后来经过实际测试，测试出来了有bug。

因为，如果一直点击数据，会弹出新的tab页，而每次点击后`localStorage`里面的值就会被更新掉，所以导致所有要跳转的第三个页面的填充值都是一样的。

### can't resolve a module

ES6Module, 自己经常遇到这种情况，就直接慌了，因为感觉自己查不到错，其实很简单的。总结自己的两步:

1. 先检查路径是否正确。
2. 检查路径的单词是否拼错。
3. 检查被引用的JavaScript文件，发现文件名不是index.js, 而是list.js, 因为要改为index.js。这里和脚手架的配置有关系。

### mongodb shell

这是真的把自己蠢哭了，因为代码跑了几次，但是数据一点儿变化都没有。后来才发现，自己在shell里面操作的时候，查询出来的数据只是部分的，需要`it`查看更多的数据，才发现数据是变化了的。

### Why is the MongoDB Node Driver generating instance pool destroyed errors?

自己当时不太会写异步，结果数据库操作还没有处理完，但是for循环早已经结束了，因此回调就`db.close`了，所以导致这样的报错。

## react

### Map

由于自己写一个简单的map都会报错，要么是eslint，要么是语法规则，总是不能一次性写对。
```
// 注意map后没有分号
// 要写key
// jsx要有大括号
<ul>
    {
        data.map((x, index) => {
            <li key={index}>{x}</li>
        })
    }
</ul>
```

### react不能识别odd, even

由于在react里面不能使用`odd even`这样的伪元素。因此需要自己来单独处理。

可以看这个: [一个简单的解决办法](https://stackoverflow.com/questions/32254504/adding-classnames-to-odd-and-even-elements-in-reactjs)。

## node

### classnames的用法

由于写react的时候，经常会遇到多个类的情况，我这里就不展开总结了，参考这里: [classnames](http://blog.csdn.net/starwmx520/article/details/50595588)

### 写脚本操作mongodb时提示`Error: cyclic dependency detectd_id`

说说我遇到的一种场景: 由于要用到某条数据进行操作的时候，没有注意到它是一个对象，然后就循环引用了。

### 如何插入带有Object_id的数据

[解决方案](https://stackoverflow.com/questions/35025090/how-to-insert-a-custum-objectid-with-collection-insert-in-mongoose)


## 代码习惯

1. 表单提交时，可以将表单检测分为2个过程，`static error check`以及`post error check`
