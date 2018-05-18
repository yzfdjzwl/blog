# 执行环境与执行环境栈

* [翻译来自](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/#)
* 每个执行环境都与一个变量对象相对应。
* 每个函数都有自己的执行环境。
* 当代码在一个环境中执行时，会创建变量对象的一个作用域链。

## 详解

每次调用一个函数时，一个新的执行执行环境会被创建。但是，在JavaScript解释器中，对执行上下文的每次调用都有2个阶段。

**创建阶段**

> 创建阶段也就是：当函数被调用时，但在它执行所有代码之前。

* 创建作用域链。
* 创建变量，函数和参数。
* 确定值"this"。

**激活/代码执行阶段**

* 分配值，引用函数和解释/执行代码。
* 可以在概念上将每个执行环境表示为具有3个属性的对象
```javascript
executionContextObj = {
	'scopeChain': {/* variableObject + all parent execution context's variableObject */},
	'variableObject': {/* function arguments / parameters, inner variable and function declarations */},
	'this': {}
}
```

**这里是解释器如何评估代码的伪描述**

1. 找到一些代码来调用函数。
2. 在执行function代码之前，创建execution context。
3. 进入创建阶段。
	* 初始化Scope chain
	* 创建variable object:
		* 创建arguments object, 检查参数的上下文，初始化名称和值来创建引用副本。
		* 扫描函数声明的上下文:
			* 对于找到的每个函数，在变量对象中创建一个属性，它具有一个引用指针，指向内存中的函数。
			* 如果函数名已经存在，则引用指针被覆盖。
		* 扫描变量声明的上下文:
			* 对于找到的每个变量声明，在变量对象中创建一个属性，即变量名，并将值初始化为undefined。
			* 如果变量名已经存在当前变量对象中，则不执行操作并继续扫描。
	* 确定"this"上下文内部的值。
4. 激活/执行代码阶段：
	* 在上下文中运行/解释函数代码，并在代码逐行执行时分配变量值。

### 一个例子

```javascript
function foo(i) {
	var a = 'hello';
	var b = function privateB() {
	};
	function c() {
	}
}

foo(22);
```

在调用foo(22)时，创建执行环境阶段如下：
```javascript
fooExecutionContext = {
	scopeChain: {/* */};
	variableObject: {
		arguments: {
			0: 22,
			length: 1
		},
		i: 22,
		c: pointer to function c()
		a: undefined
		c: undefined
	},
	this: {/* */}
};
```

激活代码阶段在函数完成执行时看起来像这样：

```javascript
foExecutionContext = {
	scopeChain: {/* */},
	variableObject: {
		arguments: {
			0: 22,
			length: 1
		}
		i: 22,
		c: pointer to funciton c()
		a: 'hello'
		b: pointer to function privateB()
	},
	this: {/* */}
};
```

### 变量提升
```javascript
(function() {

	console.log(typeof foo); // function
	console.log(typeof bar); // undefined

	var foo = 'hello';
	var bar = function() {
		return 'world';
	};

	function foo() {
		return 'hello';
	}
})();
```

* 为什么我们要在声明foo之前访问foo?
	* 因为我们遵循'创建阶段', 我们知道变量在代码执行前就已经创建了。所以随着函数流开始执行，foo已经定义在了活动对象中。
* foo被声明了2次，为什么是foo显示function，而不是显示undefined或string?
	* 即使foo被声明了2次，我们知道，在创建执行环境阶段，函数是在变量前创建的，并且，如果属性名称已经存在于活动对象中，我们只是绕过它。
	* 因此，引用function foo()是在活动对象里首先创建的，当解析器得到var foo 时，我们已经看到数姓名foo存在，因此代码不执行任何操作并继续。
* 为什么bar是undefind?
	* bar实际上是一个具有函数赋值的变量，我们知道这些变量是创建的，创建执行环境阶段会用undeinfed来初始化它们。

**下面再看一个例子：**

```javascript
var foo = 1;
function bar() {
	foo = 10;
	return;
	function fun() {
	}
}
bar();
console.log(foo);  // 1
```

* 为什么输出foo是1而不是10呢？
	* 我相信大部分人不了解执行环境的人和我一样，第一眼会认为答案是10，其实不然。当执行到bar()时，会先创建执行环境，而创建执行环境有一个阶段是创建变量对象，在创建变量对象这个阶段里，会对函数声明和变量声明，依次进行扫描。对于扫描到的函数声明，会在变量对象里添加一个属性，属性为函数名，属性值是指针，指向内存中的函数。对于扫描到的变量声明，如果在变量对象，已经发现有同名的属性了，那么则跳过。所以 这里， 进行函数声明扫描的时候，fun已经被加入到变量对象里了。所以这个fun是局部变量，在全局作用域里打印的foo是1。


这里附上一些题，以检验自己是否真的理解到了执行环境。

```javascript
// 第一题
function bar(a) {
	console.log(a);
	var a = 10;
	function a() {
	}
}
bar(2);
```

* 第一题的答案是function a() {}
* 这是因为 "函数声明在预编译期就执行，函数表达式在执行的时候初始化", 其实这句话的意思就是创建执行环境的过程。而对于传入的参数a，它相当于是内部作用域的局部对象，也就是说等同于"var a", 它会在变量声明扫描的阶段添加进变量对象，但是由于函数声明扫描的时候变量对象里已经有了a属性，那么变量声明扫描的阶段a就会跳过扫描。


```javascript
// 第二题
console.log(a);
console.log(fn);
var a = 1;
function fn() {
	console.log(a);
}
```

第二题的答案是：undefined, function fn() {}


```javascript
// 第三题
console.log(a);
a = 2;
```

第三题的答案是：报错。


```javascript
// 第四题
console.log(a); // function a() {console.log(4)}

var a = 1;

console.log(a); // 1

function a() {
	console.log(2);
}

console.log(a); // 1

var a = 3;

console.log(a); // 3

function a() {
	console.log(4);
}

console.log(a); // 3

a(); // 报错，因为a这时候不是函数了，是一个数字。
```

第四题的答案是上面已经给了。


```javascript
// 第五题
var a = 1;
function fn() {
	console.log(a);
	var a = 2;
}
fn(); // undefined
console.log(a); // 1
```

第五题的答案是undefine和1


```javascript
// 第六题
var a = 1;
function fn() {
	console.log(a);
	a = 2;
}
fn(); // 1
console.log(); // 2
```

第六题的答案是1和2

```javascript
// 第七题
var a = 1;
function fn(a) {
	console.log(a);
	a = 2;
}
fn(a); // undefined
console.log(a); // 1
```

第七题的答案是undefined和1

## 综合题

这道题主要涉及的知识点有：

* this
* 闭包
* 执行环境

```javascript
var num = 1;
var obj = {
	num: 2,
	fn: (function() {
		this.num += 10;
		num += 10;
		var num = 3;
		return function() {
			this.num += 10;
			num++;
			console.log(num);
		}
	})()
};
var fn = obj.fn;
fn();
obj.fn();
console.log(num);
console.log(obj.num);
```

首先我们要说明2点:

* 第一个就是变量提升(创建执行环境阶段)
* 第二个就是关于this

	所有的函数在被当作函数执行的时候，this都指向window(global), ES6除外。

[更多关于this的介绍](this.md)

**好的，下面就开始来解析这道题**

第一步，将预解析(创建执行环境)的阶段，给表示出来。
```javascript
var num = 1;
var obj = {
	num: 2,
	fn: (function() {
		var num;
		this.num += 10;
		num += 10;
		num = 3;
		return function() {
			this.num += 10;
			num++;
			console.log(num);
		};
	})();
}

// 代码从上往下执行
// this.num += 10， window.num的值是11
// 进入fn自执行环境中
// num = 3, 这个3被闭包引用，因此永远也不会被清除掉，除非闭包释放引用。
// 然后返回fn返回一个函数, 里面的num的值是3。
```

* 执行代码，var fn = obj.fn
* 调用函数fn(), 根据第二点，this对象指向的是window。

```javascript
// this.num += 10 等价于 window.num = 11 + 10 = 21
// num++, num是闭包引用外部作用域里的num，之前的值是3, 加了过后就是4了。由于这是闭包，所以num的值是被一直引用在内存里的，并不会消亡。
// console.log(num); 打印出4，函数作用域里的num
```

* obj.fn(), 此时this的值是obj的变量对象。

```javascript
// this.num += 10 等价于 obj.num += 10, 值为12
// num++, 此时的num是闭包里的num，之前的值一直在内存里，值仍然是4，所以这里num++后值就为5了。
// console.log(num); 值为5
```

* console.log(num)，此时的num是window.num, 值已经是21了。
* console.log(obj.num)， 此时的obj.num值是12.

因此最后的打印结果是： 4  5 21 12

这里还有一道题：

```javascript
var getName;

function getName() {
	console.log(1);
}

getName = function() {
	console.log(2);
}
getName(); // 2
```
