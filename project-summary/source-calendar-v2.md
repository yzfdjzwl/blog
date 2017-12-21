## 目录

<!-- vim-markdown-toc GFM -->
* [ES6相关](#es6相关)
      * [对象引用问题](#对象引用问题)
      * [一组array里面存放object的，匹配projectId](#一组array里面存放object的匹配projectid)
      * [在上面的描述里，加上一个需求](#在上面的描述里加上一个需求)
      * [数据格式化](#数据格式化)
      * [includes](#includes)
* [react相关](#react相关)
      * [react的state](#react的state)
* [antd相关](#antd相关)
      * [我也不是特别明白的问题](#我也不是特别明白的问题)
      * [同时获取多个Input的值](#同时获取多个input的值)
      * [AutoComplete渲染报错](#autocomplete渲染报错)
      * [AutoComplete搜索显示](#autocomplete搜索显示)
      * [modal框不显示](#modal框不显示)
      * [table渲染(大坑)](#table渲染大坑)
      * [table的渲染(rowkey)](#table的渲染rowkey)
* [mongoose相关](#mongoose相关)
      * [关于查询](#关于查询)
* [eslint相关](#eslint相关)
* [记自己傻逼](#记自己傻逼)
      * [InputNumber组件在modal里的table里没有显示出来](#inputnumber组件在modal里的table里没有显示出来)
      * [打包的数据出错](#打包的数据出错)

<!-- vim-markdown-toc -->

## ES6相关

### 对象引用问题

描述: 在做一个操作的时候，发现数据总是出问题，后来才发现，在涉及到对象的时候，自己就直接把对象赋值过去了。所以几个变量实际上操作的是一个对象。(有两次都是这个原因，而且花费了很多时间找bug。)

解决: 使用`Object.assign()`对对象进行复制，还有，遇到对象进行操作的时候一定要谨慎。

### 一组array里面存放object的，匹配projectId

描述: 通常有这么一个需求，有一个数组(a)里，存放了很多对象，然后需要将每个对象里的`projectName`拿出来进行渲染到`AutoComplete`组件里，然后有另一个数组(b)里，同样，它也存放了很多对象，然后也需要将每个对象里的`username`拿出来渲染到`AutoComplete`组件里。最后当用户在2个`AutoComplete`里面选择或者输入了值后，然后再将它们和2个数组里的`projectName`和`username`进行匹配，如果匹配成功，就将`projectId`和`uid`拿出来, 然后进行相应的数据处理。

解决办法:

```
function getSelectProject(projectName) {
  const projects = this.props.project.data;
  return projects.filter(item => item.projectName === projectName);
}

const inputProjectName = values.projectName; 
const selectProject = getSelectProject(inputProjectName);

if (!selectProject) {
  return;
}
const { projectId } = selectProject;
```

### 在上面的描述里，加上一个需求

描述: 在上面的需求里，终究只有2个`AutoComplete`里面需要进行匹配。那么如果我们将上面的数据当作一行, 而有很多这样的行在`table`里面，那么又该如何去实现呢？

解决办法: 

* 先判断是否所有的`projectName`和`username`都是对的。
* 如果都是对的，然后再得到相应的`projectId`和`uid`。
* [javascript foreach break](https://stackoverflow.com/questions/6260756/how-to-stop-javascript-foreach)
* 为什么不使用foreach看上面。

```
const projectMatch = inputProjectNames.every(item => dbProjectNames.includes(item));
if (!projectMatch) {
  return false;
}

inputProjectNames.every((item1) => {
  dbProjects.every(item2) => {
    if (item1 === item2.projectName) {
      projectIds.push(item2.projectId);
      return false;
    }
    return true;
  }
  return true;
});
```

### 数据格式化

描述: 需要将下面的这种数据格式转换为另外一种:

```
const array = [
  {
    date: '2017-01-02', 
    uid: 'yuzf',
    hour: 8,
  },
  {
    date: '2017-01-02', 
    uid: 'yuzf',
    hour: 5,
  },
  {
    date: '2017-01-03', 
    uid: 'hwy',
    hour: 1,
  },
  {
    date: '2017-01-03', 
    uid: 'hwy',
    hour: 2,
  },
];

const newArray = [
  {
    date: '2017-01-02', 
    uid: 'yuzf',
    hour: 13,
  },
  {
    date: '2017-01-03', 
    uid: 'hwy',
    hour: 3,
  },
];
```

解决办法: 第一种是使用双重循环，第二种是不使用双重循环，使用一个循环就可以了。看代码:

```
// method1
for (let i = 0; i < array.length; i++) {
  let isOk = false;
  for (let j = 0;j < newArray.length; j++) {
    if (array[i].uid === newArray[j].uid && 
      array[i].date === newArray[j].date) {
      newArray[j].hour += array[i].hour;
      isOk = true;
    }
  }
  if (!isOk) {
    newArray.push(array[i]);
  }
}

// method2
let obj = {};
let arr2 = [];
array.forEach((item, index) => {
  if (obj[item.uid+item.date]) {
    obj[item.uid+item.date].hour += item.hour;
  } else {
    obj[item.uid+item.date] = item;
  }
});

for (let i in obj) {
  arr2.push(obj[i]);
}
console.log(arr2);
```

### includes

描述: `includes`方法用于判断数组里是否包含某一个值，如果包含则发挥true，否则false。注意，当测试它用来判断对象的时候，特别要注意，因为`Object`是引用类型，如果判断是否包含的话，这里是判断的是否为同一个对象，如果是同一个对象，则返回true，否则是false。

## react相关

### react的state

描述: `state`里会有一个元素是数组，经常要对这个数组进行增加新元素。一般方法如下:
```
const { isActive } = this.state;
this.setState({
  isActive: [...isActive, date], 
});
```

## antd相关

### 我也不是特别明白的问题

```
// Error
<Button onClick={this.onCopy(index)} />

// Right
<Button onClick={() => this.onCopy(index)} />

// Error
getDate={this.getDate}

// Right
getDate={() => this.getDate}
```

### 同时获取多个Input的值

描述: 几个`Input`, 使用antd提供的API, 比如`onChange onSelect`等方法，它们的参数是当前`change`或者`select`的值。但是我每次onChange或者onSelect的时候我要获取同时判断2个`Input`的值怎么办呢? 如果使用`state`或者说是通过antd的`form`提供的API获取的多个Input的值，并不是实时的，它们都类似于`state`的异步一样的，也就是说，比如输入`a`, onChange的时候获取的是空的，再输入b，onChange的时候获取的是`a`, 再输入c，onChange的时候获取的是`ab`， 也就是说是异步的。

解决办法: 最后的解决办法是将2个Input的数据存到`state`里, 然后onChange的时候，获取当前的参数值，是实时的，而另一个值是放到`state`里面的，因此去`state`里去取就可以了。

### AutoComplete渲染报错

描述: 使用AutoComplete的是否，要自定义数据，因此需要使用`map()`去重新写子组件，但是报错了(key 重复)。

解决办法: 因为是key使用的是人名，人名有重复的，因此要么使用index, 要么使用其他办法。

### AutoComplete搜索显示

描述: `AutoComplete`默认搜索时，只有当全部匹配的时候才会显示，如果想要部分匹配的话请看下面。

解决办法: 使用`filterOption`参数, 它的值是一个`function`, 比如下面这样:

```
export function filterOption = (v, option) => {
  const source = option.props.children.toUpperCase();  
  return source.indexOf(v.toUpperCase() !== -1);
};
```

### modal框不显示

描述: 使用antd的modal框，但是modal框不显示。

原因: 必须为modal组件设置`visible`, 它才会显示。

### table渲染(大坑)

描述: `modal`框里是一个`table`, table里每一行都有`AutoComplete`以及`Input`可以对table进行编辑，然而`table`里的数据是提供的API:`dataSource`, 我每次对table里的`AutoComplete`或者`Input`进行直接编辑的时候，必须得修改`dataSource`的值，但是它的值一旦修改了，整个`table`都要重新渲染，一旦重新渲染，输入框就失去了焦点。也就是说，每输入一下就失去了焦点。

解决办法: 最终，我的解决办法是优雅降级，将`AutoComplete`的事件触发改为`onSelect`, 将`Input`修改为官方的那种需要点击编辑图标才能进行编辑，这样`table`重新渲染的时候，即使失去了焦点也不会影响使用。

我在网,上找到别人和我遇到的一样问题的地址: [点击这里](https://segmentfault.com/q/1010000006048160) 

**推荐:** 然后另一个降级的办法是: 将Input写为非受控组件，`onBlur`失去焦点的时候再触发视图刷新, 这样就可以了。代码如下:

```
// This is not work, but I don't know why
<AutoComplete
  onBlur={console.log('ok')}
/>

// This is good.
constructor() {
  this.handleBlur = this.handleBlur.bind(this);
}

handleBlur() {
  console.log('blur');
}

<AutoComplete
  onBlur={this.handleBlur}
/>

<Input
  onBlur={this.handleBlur}
/>
```

第三种方法: 这是后面改的时候发现的，但是这是一个坑，只能用一次。为什么这么说呢？因为违背了react里的state的设计原则。解决办法如下:

```JS
constructor() {
  const { tableData } = this.props.resourceState;
  this.state = {  tableData };
}

// 这里是table行里的组件的操作
onProjectChange(index, value) {
  const { tableData } = this.state;
  const copyTableData = tableData.slice();
  copyTableData[index].projectName = value;
}

render() {
  const { tableData } = this.state;
  <Table
    dataSource={tableData}
  />
}
```

**再次强调，不到万不得已，是不可以这么做的，这违背了react的设计原则**

### table的渲染(rowkey)

描述: 使用antd的`table`时，控制台报了错，原来是要求`table`的每一行数据都需要指定唯一的一个值，有点儿类似于`react`要求使用`map`的时候，子组件都要指定key。但是我对table进行了增加、删除、修改操作，因此我不能为`rowkey`指定为index。

解决办法: 最后我在找了一个npm包，是关于`uuid`, 设置`<Table rowkey="uuid" />`, 这样就保证每一行都有唯一的key了，同时也要注意，每一行也需要设置`key`。

## mongoose相关

### 关于查询

描述: 当我还没有为表增加任何一条数据的时候，表此时还没有生成。因此在做查询操作的时候，打印出来的`error`是null, 说明没有error, 而为什么没有打印出`doc`是因为本身就没有查询出来东西。因此，要必须先插入至少一条数据才可以看到有表生成，以及查询操作才会出来结果。

## eslint相关

描述: `Expected parentheses around arrow function argument having a body with curly braces arrow-parens`, 意思是说箭头函数的参数需要一个括号`()`。 

## 记自己傻逼

### InputNumber组件在modal里的table里没有显示出来

原因: 自己将`render`写成了`rener`, 控制台没有报错，真的很傻逼。

### 打包的数据出错

描述: 自己还以为是后台进行数据操作的时候会存在异步，后来才发现数据打包的时候就出错了。一步一步的走到上层的组件，结果发现在最外层组件，数据就出错了。