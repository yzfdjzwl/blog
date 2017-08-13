# 目录

* [antd](#antd)
* [es6](#es6)
    * [Object.entries()](#objectentries)
    * [Object.keys()](#objectkeys)
    * [Object.getOwnPropertyNames()](#objectgetownpropertynames)
    * [很神奇的用法！！！](#很神奇的用法)
* [eslint](#eslint)
* [傻逼](#傻逼)
* [其他](#其他)
* [参考](#参考)

## antd

1. `antd2.0.1`里的`Cascorder`这个组件，它的`defaultValue`不起作用，是一个bug。当我发现这是一个bug的时候，应该想着如何去避免这个bug，比如使用`value`, 而不是告诉别人这是个bug。要学会自己解决问题!!!

## es6

### Object.entries()

`Object.entries()`方法返回给定对象自己的枚举属性`[key, value]`对的数组。其顺序和与`for...in`循环提供的顺序相通，区别在于`for...in`循环枚举原型中的属性。

下面看例子:

```
const Person = {
    name: 'yuzf',
    age: '18',
    gender: 'man',
};

// [['name': 'yuzf'], ['age': 18], ['gender', 'man']]
console.log(Object.entries(Person));
const result = {};

// 数组解构/for of 循环
for (const [key, value] of Object.entries(Person)) {
    // good
    result[key] = value; 

    // bad
    // result.key = value;
}
```

### Object.keys()

`Object.keys()`返回一个数组，其元素是对应于直接在对象上发现的可枚举属性的字符串(即不包含原型链上的)。属性的顺序与通过手动循环对象的属性给出的顺序相同。

下面直接看代码说明:

```
const arr1 = ['a', 'b', 'c'];
console.log(Object.keys(arr1)); // ['0', '1', '2']

const person = {
    name: 'yuzf',
    age: 18,
    gender: 'man',
};

for (const key of Object.keys(person)) {
    console.log(key); // name, age, gender
}
```

### Object.getOwnPropertyNames()

`Object.getOwnPropertyNames()`方法返回一个直接发现给定对象的所有属性(可枚举和不可枚举)的数组。

下面看代码例子：

```
const arr = ['a', 'b', 'c'];
console.log(Object.getOwnPropertyNames(arr).sort);
// logs: ['0', '1', '2', 'length']

const arr2 = {
    name: 'yuzf',
    age: 18,
    gender: 'man',
};
console.log(Object.getOwnPropertyNames(arr).sort);
// logs: ['name', 'age', 'gender']
```

### 很神奇的用法！！！

之前，我也遇到这样的需求, 将一个数组里包含的多个对象转成一个对象，结构像下面这个样子:

```
// origin
const Person = [
    { name: 'yuzf'},
    { age: 18 },
    { gender: 'man' },
];

// to => this
{
    name: 'yuzf',
    age: 18,
    gender: 'man',
}

// do
const obj = Person.reduce((x, y) => Object.assign({}, x, y), {}) 
```

## eslint

`Do not access Object.prototype method 'hasOwnProperty' from target object no-prototype-builtins`, 大概的意思是不要使用对象原型上的方法，因为原型上的方法可能被重写了。[This is why](https://stackoverflow.com/questions/12017693/why-use-object-prototype-hasownproperty-callmyobj-prop-instead-of-myobj-hasow)。

那么该如何避免这个问题呢？下面请看代码:

```
// bad
if (obj.hasOwnProperty('name')) {
}

// good
if (Object.prototype.hasOwnProperty.call(obj, 'name')) {
}
```

## 傻逼

可能是自己代码写着写着就将自己写懵了，`fields.concat(['xx'])`, 这行代码执行后, 怎么fields的值没有变化。

这真是一件很傻的事情。

## 其他

1. 一个简单的需求并不是看起来那么简单。
2. 自己写的代码，一定要多测试，一定一定要！！！
3. 这次向线上的数据库里插入了脏数据，以后遇到加了字段的需求，一定要多测试，多考量。

## 参考

[mdn: Object.entries()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)
[mdn: Object.keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)
[mdn: Object.getOwnPropertyNames()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames)
