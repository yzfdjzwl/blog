<!-- vim-markdown-toc GFM -->
* [当我遇到了一个很坑的跨域问题](#当我遇到了一个很坑的跨域问题)
* [使用margin: 0 auto的前提](#使用margin-0-auto的前提)
* [如何给react组件添加多个class](#如何给react组件添加多个class)
* [如何重写antd的样式](#如何重写antd的样式)
* [关于CSS3不熟悉的用法](#关于css3不熟悉的用法)
* [如何使用babel来测试ES6](#如何使用babel来测试es6)
* [ES6相关](#es6相关)
* [react相关](#react相关)
* [reducer相关](#reducer相关)
* [后端](#后端)
* [ESLINT规则集](#eslint规则集)
    * [ESLINT 数组部分实践](#eslint-数组部分实践)
* [数组常用方法实践](#数组常用方法实践)

<!-- vim-markdown-toc -->

## 当我遇到了一个很坑的跨域问题

问题描述: 不知道为什么，就是跨域，我检查了端口号以及域名，都没有问题，但是还是跨域。

原因: `BACKEND_URL='localhost:3000'`, 这里指定了地址，但是没有指定协议，所以跨域。应该是`http://localhost:3000`。

总结: 属于同一个域，必须包括 协议、域名、端口号都一致，否则跨域。

## 使用margin: 0 auto的前提

如果想要使用`margin: 0 auto;`，它有以下几个前提:

1. 必须是块级元素, `inline-block`元素也不行。
2. 必须指定宽度。
3. 不能使用定位属性以及浮动。

## 如何给react组件添加多个class

这里介绍几种方法:

1. 简单拼接字符串: `<div className={class1 + ' ' + class2}></div>`
2. 使用es6字符串模板拼接字符串: `<div className={`${class1} ${class2}`}></div>`
3. 使用`classnames`, 先导入模块`import classnames from 'classnames'`, 然后使用`<div className={classnames(class1, class2, class3)}></div>`, 推荐使用这种。

## 如何重写antd的样式

这里介绍两种方法:

1. 直接修改组件的类型，如:`<Icon className="test-class"/>`
2. 使用global修改，这种情况，一般需要在组件外面包一层，但是不推荐这么用，因为版本升级可能导致意想不到的结果，比如:

```
:global(.footer .ant-input-group-adoon) {
  color: red;
}
```

## 关于CSS3不熟悉的用法

1. `box-shadow`合成写法以及所表达的意思: `none | [inset? && [<offset-x> <offset-y> <blur-radius>?<spread-radius>?<color>? ]]#`, 默认阴影在边框外, 使用`inset`后，阴影就会在边框内。`blur-radius`值只能为正，值越大，模糊面积越大，阴影就越大越淡。`spread-radius`值可正可负，正值，阴影扩大，负值，阴影缩小。

2. `border-radius`分开写法顺序: `border-top-left-radius: 4px`。

## 如何使用babel来测试ES6

1. 安装`babel-preset-es2015`, 使用命令`cnpm install --save-dev babel-preset-es2015`
2. 配置`.babelrc`文件
```
{
  "presets": ["es2015"]
}
```
3. 安装`babel-cli`，使用命令`cnpm install babel-cli -g`
4. 使用`babel-node`, 它是`babel-cli`自带的工具，使用命令`babel-node main.js`来测试main.js文件

## ES6相关

1. 当导入默认模块时，直接`import test from './test'`
2. 当导入非默认模块时， 需要加上括号，如: `import { test } from './main'`
3. `...array`, 如果`const array = [1, 2, 3];`,这个扩展运算符的计算结果是`1 2 3`, 它们之间没有逗号。
4. `array.indexOf('a')`这是一个坑，如果要判断是否有的话，应该用是否大于-1来判断，而非直接`if (array.indexOf('a') {})`, 应该用`if (array.indexOf('a') > -1)`来判断。
5. `splice()`方法，返回的是一个新数组，举例来说，如果使用的是删除，那么返回的就是被删除的元素组成的数组。而被删除元素的数组自然也就少了元素。
6. ES6实现数组去重的方法: `projectName = [...new Set(projectName)]`

## react相关

1. `antd`的日历组件，如何实现点击日历，改变点击的那天的颜色，这个时候要转变操作DOM的思想，要向react上转，是通过数据来的，因此我们要考虑到使用`state`或者说是`reducer`之类的。

2. `antd`的`form`组件，如果要验证的话，两种方法，要么写在`validator`里面, 要么最后提交表单的时候统一验证。如果在统一表单的时候统一验证，`this.porps.form.validateFields(err, value){}`, 这个函数里，如果有错，一定要return。`if (err) {return;}`, 不然，最后界面上不会提示出错。

3. 报错: `Warning: React.createElement: type should not be null, undefined, boolean, or number. It should be a string (for DOM elements) or a ReactClass (for composite components). Check the render method of ProjectCalendar.`, 这个报错我遇到了多次，原因是自己引入模块不小心写错了，比如写了`import { Input } from 'react'`, 实际上应该是: `import { Input } from 'antd'`

4. 像报这样的错: `Object is undefined`, 一定要好好检查是不是导入模块错了，或者是忘了导入模块。

5. 在使用antd的`AutoComplete`组件时，想要实时获取输入框里的值, 可以直接使用`onSelect(value)`方法，value就是实时的值，但是我的需求是实时获取2个`AutoComplete`组件里的值，我用到了`form`表单里的`getFieldValue()`方法，但是在使用的过程中，发现这样并不可以，这个方法获取的并不是实时的，而是上一次`onChange`或者`onSelect`后的值。最终的解决办法是，使用`state`, 调用`onSelect或onChange`方法后，把当前的值存入state里，并在`setState()`的方法的回调函数里，进行判断2个值，因为2个值都存到state里面的，所以就可以获取2个`AutoComplete`里的实时的值了。

6. 使用antd的`mention`组件，报错`toContentState is not a function`, 原因，版本问题。

## reducer相关

1. 最外层`reducers`将所有`container`的`reducer`组合起来了。

## 后端

1. 表里有个字段设置了`unique`, 但是在自己插入的时候，那个字段又不是唯一的，所以mongodb报错:`11000`。就算在代码里将`unique`去掉，但是它仍然没有反映到数据库里，所以需要将表删除了，重新创建。

2. 查询请求的时候报错，大概意思是不知道请求的方法是`GET`还是`HEAD`, 原因是`schema`里面的那个`validator()`, 里面不需要返回任何数据。

3. `Missing or undefind handler`, 这是由于方法没有添加到`prototype`里去。

4. 当有可能是更新操作，有可能是创建操作的时候，是后端将`create`以及`update`的API都提供了，然后前端进行判断，来判断是更新操作还是插入操作。而不是前端一个函数，后端来判断!!!

## ESLINT规则集

* `no-trailing-spaces`, 最后面不能跟空格。
* `the closing bracket must be aligned with the line containing the open tag.`, 闭合括符必须和开标签对齐。
* `using string literals in ref attribute is deprecated`, 在ref中使用字符串文字已经被弃用, 这个时候应该使用这种:

```
constructor() {
  super();
  this.handleClick = this.handleClick.bind(this);
}

handleClick() {
  const node = this.btnRef;
}

return (
  <div>
    <div onClick={this.handleClick}><span ref={(el) => {this.btnRef = el;}}></span></div>
  </div>
);
```

* `unexpected parentheses around single function argument having a body with no curly braces arrow-parens`, 大概意思是如果箭头函数的参数只有一个，那么不需要用`()`包括起来，其他任何情况都需要用`()`包括起来。 
* `arrow function should not return assignment`, 箭头函数禁止返回赋值，也就是在return的时候，禁止赋值语句。
* `visible, non-interactive elements should not have moue or keyboard listeners jsx-ally/no-static-element-interactions`, 非可见，非交互元素，不应该就事件监听。

```
common interactive roles include:

1. button
2. link
3. checkbox
4. menuitem
5. menuitemcheckbox
6. menuitemradio
7. option
8. radio
9. searchbox
10. switch
11. textbox
```

### ESLINT 数组部分实践

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