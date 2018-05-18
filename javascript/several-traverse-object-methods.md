## `Object.entries()`
`Object.entries()`方法返回给定对象自己的枚举属性`[key, value]`对的数组。其顺序和与`for...in`循环提供的顺序相通，区别在于`for...in`循环还枚举原型链中的属性。

我们具体看一个例子:
```javascript
const Person = {
  name: 'yuzf',
  age: 18,
  gender: 'man',
};

console.log(Object.entries(Person));
// [['name', 'yuzf'], ['age', 18], ['gender', 'man']]
```

## `Object.keys()`
`Object.keys()`会返回一个数组，数组的元素是由对象的`key`(枚举属性)组成的(不包括原型链上的), 数组的顺序和`for in`循环的一致。区别是`for in`会遍历原型链上的属性。

看一个简单的例子:
```javascript
// Array
const arr = ['apple', 'mac', 'green'];
console.log(Object.keys(arr)); // [0, 1, 2]

// Object
const person = {
  name: 'yuzf',
  age: 18,
  gender: 'man',
};
console.log(Object.keys(person)); // ['name', 'age', 'gender']
```

## `for of`与`for in`

`for...in`语句以任意顺序遍历一个对象的可枚举属性。对于每个不同的属性，语句都会被执行。

`for...of`语句在可迭代对象(Array, Map, Set, String, TypedArray, arguments对象等)上创建一个迭代循环，调用自定义迭代钩子，并为每个不同的属性的值执行语句。

如果`for...in`与`for...of`来遍历数组的话, `for...in`遍历的是键值，即数组的索引，`for...of`遍历的是数组的元素值。

## `Object.getOwnPropertyNames()`

`Object.getOwnPropertyNames()`方法返回一个直接发现给定对象的所有属性(可枚举和不可枚举)的数组。

下面看代码例子：
```javascript
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


## 遍历对象的多种方法

### `Object.entries()`配合数组解构以及`for of`

```javascript
const Person = {
  name: 'yuzf',
  age: 18,
  gender: 'man',
};

const result = {};

for (const [key ,value] of Object.entries(Person)) {
  // good
  result[key] = value;

  // bad
  // result.key = value;
}
```

### `Object.keys()`配合`for of`

```javascript
const Person = {
  name: 'yuzf',
  age: 18,
  gender: 'man',
};

for (const key of Object.keys(Person)) {
  console.log(key);
}
```

### 使用`for in`

```javascript
const Person = {
  name: 'yuzf',
  age: 18,
  gender: 'man',
};

for (const key in Person) {
  console.log(key);
}
```

### `Object.getOwnPropertyNames`配合`hasOwnProperty`

```javascript
var arr = ['a', 'b', 'c'];

for (let i in Object.getOwnPropertyNames(arr)) {
  if (arr.hasOwnProperty(i)) {
    console.log(i);
  }
}
```
