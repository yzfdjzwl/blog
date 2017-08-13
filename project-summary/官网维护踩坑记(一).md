## 目录

* [linux](#linux)
    * [supervisisor](#supervisisor)
* [react](#react)
    * [dangerouse html](#dangerouse-html)
    * [内联样式](#内联样式)
* [CSS](#css)
    * [padding简写说明](#padding简写说明)
* [git](#git)
    * [git reset](#git-reset)
    * [git pull](#git-pull)
    * [撤销最后一次提交](#撤销最后一次提交)
* [JavaScript](#javascript)
    * [遍历对象](#遍历对象)
        * [使用for in 方法](#使用for-in-方法)
        * [使用for of 方法](#使用for-of-方法)
* [eslint](#eslint)
* [代码优化](#代码优化)
* [参考](#参考)

## linux 

### supervisisor

个人认为这篇文章适合新人理解: [supervisisor简介](http://www.tuicool.com/articles/Ejm2u2)

> 简单的说，supervisior是一个管理进程的工具。用途就是有一个进程需要每时每刻不断的跑，但是这个进程又有可能由于各种原因有可能中断。当进程中断的时候我希望能自动重新启动它，此时，我就需要使用到了Supervisor。

## react

### dangerouse html

由于react并不会自动解析帮你解析代码，这是由于可能存在XSS攻击，因此，React提供了这样一种机制(`dangerouslySetInnerHTML`)。它的用法如下: 

```
constructor) {
    this.state = {
        html: '<a>Hello</a></br><a>World</a>',
    };
}

render() {
    <div dangerouslySetInnerHTML={{__html: this.state.html}}></div>
}
```

### 内联样式

如何在react里写内联样式呢？基本写法如下:

```
<div style={{ height: 0, overflow: 'hidden'}}>我是测试</div>
<div style={{ width: '100px', marginRight: '30px'}}>我是测试</div>
```

## CSS

### padding简写说明

之前感觉对padding，margin它们俩的简写很熟，但是发现自己用的时候，还是有些不大清楚，因此特意总结一下。

```
padding: 1px 2px 3px;

// 等价于:
padding-top: 1px;
padding-right: 2px;
padding-bottom: 3px;
padding-left: 2px;  // 省略的左值，等于右值。

padding: 1px 2px;

// 等价于:
padding-top: 1px;
padding-right: 2px;
padding-bottom: 1px; 
padding-left: 2px;
```

## git

### git reset

`git reset --hard commit_id`这个命令可以让当前HEAD回到`commit_id`这个快照，由于使用的是hard, 因此回到`commit_id`这个快照后，会将`commit_id`前面的快照重置，像是没有提交过快照一样。

### git pull

`git pull --rebase 远程仓库名 远程分支`, 一般我们为了保持和线上的代码一致，我们可以这样使用`git pull`。

### 撤销最后一次提交

`git commit --amend`, 此命令会重新提交当前的快照, 通常用于当我们发现有几个文件写错了或者说是忘记添加了的时候使用。一般使用了这个命令后，我们push到远程仓库的时候会这样`git push origin test:dev --force`使用git push。

## JavaScript

推荐阅读[阮一峰](http://es6.ruanyifeng.com/#docs/iterator)的这篇内容。

### 遍历对象

一般来说，有的时候我们经常需要遍历对象。下面介绍几种遍历对象的方法。

#### 使用for in 方法

```
const person = {
    name: 'yuzf',
    age: '18',
    sex: 'male',
};

for (const key in person) {
    console.log(key);
}

// result
name
age
sex
```

描述: `for...in`语句以任意顺序遍历一个对象的可枚举属性。

#### 使用for of 方法

```
const person = {
    name: 'yuzf',
    age: '18',
    sex: 'male',
};

for (const key in Object.keys(person)) {
    console.log(key);
}

// Object.keys()
// 它会返回一个数组，其元素是对应于直接找到的枚举属性的字符串object。属性的属性和通过手动循环对象的属性给出的顺序相同。

// result
name
age
sex
```

描述: `for...of`语句在可迭对象(Array, Map, Set, String, Arguments对象等等)上创建一个迭代循环，对每个不同属性的属性值，调用一个自定义的有执行语句的迭代挂钩。

## eslint

经常写代码的时候会操作数组和对象，一般会遇到下面这种情况:

```
let obj = {};

if (/* condition */) {
    obj.xx = xx;
}
```

但是写完之后，每次eslint都会提示`prefer-const`, 我很好奇，为什么我要对obj进行修改，而它还是会报错, 年轻的我还以为是eslint出了问题。后来实在是忍不住了，就去问了一下我们的架构师，他告诉我，因为对象和数组是引用，只要引用没变，就要用const。原来是因为是引用类型。。。。我还以为是eslint有问题。

## 代码优化

```
let a = '';
if (isShow) {
    a = 'ok';
}

// 完全可以改写成这样
const a = isShow ? 'ok' : '';
```

## 参考

[MDN: for...in](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in)
[MDN: for...of](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of)
[MDN: Object.keys](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)
[阮一峰ES6](http://es6.ruanyifeng.com/#docs/iterator)