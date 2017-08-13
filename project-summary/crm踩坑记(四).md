## React

### 如何同步更新state

由于setState方法是异步的，而通常很多时候在一个生命周期里更新state后需要在另一个生命周期里使用这个state。

下面介绍几个方法

```
// 1
this.setState({}, () => {
    this.doSomething();
});

doSomething() {
    console.log(this.state);
}

// 2 使用setState

// 其他
this.setState((prevState, props) => {
    return { counter: prevState.counter + 1 };
});
```

## mongodb

### Object_id

由于Object_id是对象，因此在比较的时候一定不能直接比较两个ID是否相等，即不能`if (a == b)`, 通过查阅官方的文档，了解到可以这样用: `if (a.toString() === b.toString())`

### mongoose: virtual populate

在使用mongoose进行表关联查询的时候，我将查询出来的数据在后端打印出来，但是发现打印不出来任何东西。

我还以为是代码写错了，应该一番资料的查阅，了解到真实的情况是这样的: "通过virual查询出来的数据，打印出来是无法看见的，因为它的类型是***(我也不知道)的，需要将它转换为对象，才可以打印出来"。

同样，还有一个地方需要注意:

通过virtual查询出来的数据，如果不对结果进行操作，那么返回到前端的数据就是"_doc", 但是如果要对查询的结果进行操作，此时操作的数据不是"_doc"， 而是整个对象, 因此需要过滤一次(`const docs = results.map(x => x._doc)`)。

### mongodb shell导出到csv

```
mongoexport --host localhost --db seed_crm --collection customers --type=csv --out customer.csv --fields firstname,middlenamae,lastname
```

## node

### axios与hapi.js结合使用

```
// get
axios.get('/xx', {
    params: {
        a,
        b,
        c,
    },
});

// schema
function xx() {
    return {
        query: {
            a: Joi....
        }
    };
}

// post
axios.post('/xx', {
    a,
    b,
    c,
});

// schema
function xx() {
    return {
        payload: {
            a: Joi
        }
    };
}
```

## 代码技巧

### forEach+if+push ===> filter+concat

单从代码的整洁度来讲，这是一个很不错的方式。

```
// 只是简单的举例
let results = [];
const data = [1, 1, 2, 2, 3, 3];
data.forEach(x => {
    if (x === 2) {
        results.push(x);
    }
});

// to this
let results = [];
const data = [1, 1, 2, 2, 3, 3];
results = results.concat(data.filter(x => x === 2));
```

### Promise await

超过2个await建议使用Promise.all()

### ES6解构

```
const value1 = values[0];
const value2 = values[1];

// to this, 数组的解构
const [value1, value2] = values;
```

### 数组的几个方法

对于数组的几个常见的方法，要使用`return null 或者 return false`, 我知道这是什么意思, 就是不太好表达出来。

### 对象取值兼容

由于经常涉及到需要取对象的某某属性值，但是该对象可能不存在某某值，因此就会导致报错。故一般使用的办法有两个:

1. 使用loadsh库。
2. 做兼容。如下:
```
const defaultBy = (Person || {}).default;
// 或者这样的
const defaultBy = Person.default || '';
```

### find

多使用ES6里面的find方法，很好用的。

## 坑

### mongoose

在mongoose的model里面没有某个字段，因为需求，需要向数据库里插入某个字段。然后查询数据打印出来，可以看见这个数据，但是在使用的时候，它的值都是`undefined`, 后来才知道，这是mongoose的一个机制，需要在`schame`里面，将直接插入的那个字段给加上。

### Promise.resolve

resolve函数里面只能跟一个值，如果要resolve多个值，需要用对象包裹起来。

### Promise.all

Promise.all需要所有的Promise都resolve或者reject, 如果只有一部分完成了这两个操作，那么是会出问题的。

### React数据传递

`x={true} 与 x="true"`在子组件里，得到的数据结果是不一样的。
请看这里: [JSX Boolean](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-named-as-default.md)
