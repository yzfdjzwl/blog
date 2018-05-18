# 使用es5面向对象实现简单的todolist

<!-- vim-markdown-toc GFM -->
* [前言](#前言)
* [遵守](#遵守)
* [开始](#开始)
  * [布局](#布局)
  * [设计对象](#设计对象)
  * [对象的属性](#对象的属性)
  * [事件绑定](#事件绑定)
  * [业务逻辑单元的操作](#业务逻辑单元的操作)
  * [实例化对象](#实例化对象)
* [参考](#参考)

<!-- vim-markdown-toc -->


## 前言

最近阅读了JavaScript设计模式的面向对象篇，但是又苦于实践，便想到了写一个简单的todo-list来巩固自己JavaScript面向对象设计的思想。希望对和我一样的小白有帮助。本文代码使用的是ES5，并非ES6r, 要求：了解原型链。

## 遵守

怕文中有人不明白为什么要这么写，所以这里先说一下一些地方为什么要这么写。

> 来自《编写可维护的JavaScript》

* 将一些常量和不变的数据，放在配置文件里。
* CSS和JavaScript分离。
* 事件处理和业务逻辑单元分离。

```javascript
// 如:
var config = {
	classShow: 'show',
	classHide: 'hide'
};
```

## 开始

### 布局
首先，先将HTML框架搭好，这里，就没有特别的做美化了。
```html
<div>
	<input type="text" data-input="work">
	<ul data-list="work">
	</ul>
</div>
```

### 设计对象

这个地方，倒是花了我一部分事件，不知道到底该如何设计，因为自己之前写过Java，Java里封装类的思想是尽可能单一功能，和组件的思想很像。

而最开始，我就陷入了深深的思考中，因为我想将这个简单的todo-list封装成一个对象，如果按单一功能的思想的话，就得将input封装为一个对象，li封装为一个对象，ul封装为一个对象，显然，这是不可能的。

于是，我转变思想，将它们整个封装一个对象。

### 对象的属性

对象的属性有：input, ul, list。为了实例化多个对象，将name传入函数里。因此, 代码如下：

```javascript
var TodoList = function(name) {
	this.list = document.querySelector('[data-list="' + name + '"]');
	this.childs = this.list.querySelectorAll('li');
	this.input = document.querySelector('[data-input="' + name + '"]');
}
```

### 事件绑定

仔细思考，其实就是一个增加、删除、查找的操作, 并且为了将事件处理和逻辑处理单元分隔开，所以事件绑定仅仅处理的是绑定事件。因此，代码如下：

```javascript
var TodoList = function(name) {
	this.list = document.querySelector('[data-list="' + name + '"]');
	this.childs = this.list.querySelectorAll('li');
	this.input = document.querySelector('[data-input="' + name + '"]');

	// 绑定事件
	this.init = function() {
		
		// input按下enter键
		this.input.addEventListener('keyup', function(event) {
			this.handleKeyup(event);
		}.bind(this));

		// 事件委托，点击list
		this.list.addEventListener('click', function(event) {
			this.handleListClick(event);
		}.bind(this));
	
		// input的值改变时
		this.input.addEventListener('input', function(event) {
			this.handleInputChange(event);
		}.bind(this));
	};
};


/**
 *
 * 事件与应用单元分离
 * handleKeyup
 * 
 * {param} object event
 *
 */
TodoList.prototype.handleKeyup = function(event) {
	event.preventDefault();
	event.stopPropagation();
	this.addItem(event.keyCode, event.target);
}

/**
 * 事件和应用单元分离
 * 点击list，事件冒泡阶段处理, 委托事件
 * 
 * {param} object event
 */
TodoList.prototype.handleListClick = function(event) {
	event.preventDefault();
	event.stopPropagation();
	this.removeItem(event.target);
}


/**
 * 事件和应用单元分离
 * input内容改变，则模糊查找, 调用findItem()函数
 * 
 * {param} object event
 */
TodoList.prototype.handleInputChange = function(event) {
	var value = event.target.value;
	event.preventDefault();
	event.stopPropagation();
	this.findItem(value);
}
```

在上面，你可能不知道为什么要绑定this，你需要了解一下bind函数的用处, 而调用事件对象的两个API是为了阻止事件的默认行为 以及 防止事件对象传播。

### 业务逻辑单元的操作

事件绑定成功后，就将进行逻辑单元的代码编写, 看下面的代码：

```javascript
/**
 *
 * 增加一个todo-item
 * 注意，这里要绑定this
 *
 * {param} number event.keyCode
 * {param} object event.target
 *
 */
TodoList.prototype.addItem = function(keyCode, target) {

	if(event.keyCode == 13 && event.target.value.trim().length > 0) {
		this.append();
		this.clearInput();			
		this.childs = this.list.querySelectorAll('li');
	}
}


/**
 * 删除一个todo-item
 *
 * {param} object event.target
 */
TodoList.prototype.removeItem = function(target) {
		
	// 初始化执行的时候，不存在target
	if(target) {
		if(target.nodeName == 'BUTTON') {
			target.parentNode.parentNode.removeChild(target.parentNode);
			this.childs = this.list.querySelectorAll('li');
		}
	}
};

/**
 *
 * 查找输入字符串是否存在
 * 如果不存在，就显示所有的list
 * 如果存在，就显示存在的
 *
 */
TodoList.prototype.findItem = function(value) {

	var find = false;

	// 隐藏所有
	for(var i = 0;i < this.childs.length;i++) {
		this.childs[i].className = config.classHide;
	}

	// 如果存在就显示
	for(var i = 0;i < this.childs.length;i++) {
		var nowEl = this.childs[i];

		if(nowEl.firstChild.innerText.indexOf(value) != -1) {
			nowEl.className = config.classShow;
			find  = true;
		}
	}

	// 如果都没有就全部显示
	if(!find) {
		for(var i = 0;i < this.childs.length;i++) {
			this.childs[i].className = config.classShow;
		}
	}
}


/**
 *
 * 清空输入框
 *
 */
TodoList.prototype.clearInput = function() {
	this.input.value = '';
}


/**
 *
 * 为totolist append一个li标签
 *
 */
TodoList.prototype.append = function() {
	var oLi = document.createElement('li');
	var oBtn = document.createElement('button');
	var oSpan = document.createElement('span');
	oSpan.innerHTML = this.input.value.trim();
	oBtn.innerHTML = 'delete';
	oLi.appendChild(oSpan);
	oLi.appendChild(oBtn);
	this.list.appendChild(oLi);
};
```

### 实例化对象

```javascript
var todo = new TodoList('work');
todo.init();
```

如果你想要查看这个简单的todo-list所有的代码，请点击[这里](https://github.com/yzfdjzwl/JavaScript/blob/master/%E8%BF%9B%E9%98%B6/object-oriented-todo-list.html)

## 参考

* 《编写可维护的JavaScript》
* 《JavaScript设计模式》
