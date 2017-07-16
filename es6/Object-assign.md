> Object.assign()用于将所有可枚举的值从一个或多个源对象复制到目标对象。它将返回目标对象。

### 语法

```
Object.assign(target, ...source);

var obj = {a: 1};
var copy = Object.assign({}, obj);
console.log(copy); // {a: 1}
```

### 深度拷贝问题

针对深度拷贝，需要使用其他方法，因为Object.assign()拷贝的是属性值。假如源对象的属性值也是一个指向对象的引用，它也只拷贝那个引用值。

### 合并objects

说明：如果目标对象中的属性具有相同的键，则属性将被源中的属性覆盖。后来的源的属性将类似地覆盖早先的属性。

```
var o1 = {o1: 1};
var o2 = {o2: 2};
var o3 = {o3: 3};
var copy = Object.assign(o1, o2, o3);
console.log(copy); // {o1: 1, o2: 2, o3: 3}
console.log(o1); // {o1: 1, o2: 2, o3: 3}, 注意o1的值也会发生改变
```

[参考MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)