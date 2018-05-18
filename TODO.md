## CSS

* https://segmentfault.com/a/1190000009139500#articleHeader2
* https://segmentfault.com/a/1190000011358507
* https://zhuanlan.zhihu.com/p/25565751
* http://zh.learnlayout.com/
* https://leohxj.gitbooks.io/front-end-database/html-and-css-basic/css-layout.html
* https://www.w3.org/TR/CSS2/visuren.html#dis-pos-flo
* https://www.jianshu.com/p/1e25d113464f
* https://stackoverflow.com/questions/28584078/css-display-not-inheriting-when-using-float
* https://blog.csdn.net/baoxiaofeicsdn/article/details/58603959

* 删除无用、低级的博文。
* 整理需要总结的TODO。

# linux

## 服务器默认端口

21, FTP端口
22, SSH端口
80, 网站默认端口
443, SSL
8080

## other cli

* top
* ps
* netstat
* history
* less more


这里讲解下最常用的用法: `ps aux | grep vim`, 查看所有的vim进程。`ps aux | grep vim | grep -v grep`, 查看所有的vim进程，去处掉当前的grep进程。一般查询出来的结果就是进程的id。


# CSS

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


## 使用margin: 0 auto的前提

如果想要使用`margin: 0 auto;`，它有以下几个前提:

1. 必须是块级元素, `inline-block`元素也不行。
2. 必须指定宽度。
3. 不能使用定位属性以及浮动。



## 数组常用方法实践

如果要过滤掉`['abc', 'abx', 'abm', 'll', 'am']`数组里只含有`ab`的元素如何操作?

```
const arr = ['abc', 'abx', 'abm', 'll', 'am'];
let newArr = arr.filter(item => (item.indexOf('ab') > -1));

// 可以进阶为数组元素为对象
// 同理，还有其他需要过滤的，不需要专门写判断，直接return就可以了。
// 同理，map操作也是，不需要专门写判断, 格式也是和上面一样。
```

如果想要匹配一个数组里的部分和元素和另一个数组的部分元素, 该如何操作？

```
const mentionArr = ['yuzf', 'mkmk', 'zhouh', 'yangh', 'klk'];
const users = [
  {
    name: 'yuzf',
    uid: '1',
  },
  {
    name: 'zhouh',
    uid: '2',
  },
  {
    name: 'yangh',
    uid: '3',
  },
  {
    name: 'yaocl',
    uid: '4',
  },
  {
    name: 'zhaozm',
    uid: '5',
  },
  {
    name: 'hwy',
    uid: '6'
  },
];

// 现在需求是这样，mentionArr是用户输入的一堆数据，users是数据库里的数据
// 我想将输入的一堆数据去和users进行比对
// 如果users里存在那么mentionArr里的数据，就将users里面的uid给拿到
let uids = [];

// 1. 先将匹配的users给拿出来
// 2. 再将uid拿出来
users.filter(item => (mentionArr.includes(item.name))).forEach(item => (uids.push(item.uid)));
console.log(uids); // [1, 2, 3]
```

