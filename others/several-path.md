# 几种路径的对比

<!-- vim-markdown-toc GFM -->
* [requirejs的路径解析机制](#requirejs的路径解析机制)
  * [例子测试](#例子测试)
    * [结论一](#结论一)
      * [demo01](#demo01)
      * [demo02](#demo02)
* [nodejs的路径解析机制](#nodejs的路径解析机制)
* [es6模块路径解析机制](#es6模块路径解析机制)
* [webpack模块路径解析机制](#webpack模块路径解析机制)
* [参考](#参考)

<!-- vim-markdown-toc -->

## requirejs的路径解析机制

在requirejs中，设置`baseUrl`的方式有下面三种：
* 通过`requirejs.config({ baseUrl: '' })`来设置。
* 若调用requirejs的那个html页面里，script标签指定了入口点(data-main)文件，那么baseUrl为入口点所在目录。
* 如果不存在上述的两种情况，则baseUrl为运行requirejs的那个HTML文件所在的目录。
    ```html
    <!-- 引用方式 -->
    <script src="js/require.js" data-main="js/app.js"></script>
    ```

> 引入"主目录"的概念: 即指调用requirejs的那个html所在的目录。
### 例子测试

首先我们有这样的目录:
```javascript
└── www
	├── html
	│   └── html-index.html
	├── index.html
	└── js
		├── app.js
		├── lib
		│   └── hello.js
		└── require.js
```

#### 结论一

##### demo01

> `baseUrl`是相对于"主目录"而言的, 即如果主目录为`www/`, baseUrl为`js/lib`, 则最后的baseUrl目录实际是`www/js/lib`。

`www/index.html`里面的代码是这样的:
	```html
	<script src="js/require.js" data-main="js/app.js"></script>
	```
`js/app.js`里面的代码是这样的
	```javascript
	// baseUrl: www/js/lib
	requirejs.config({
		baseUrl: 'js/lib'
	});
	require(['hello'], function(hello) {
		hello.hello('requirejs');
	});
	```

##### demo02

`www/html/html-index.html`里面的代码是这样的:
	```html
	<script src="../js/require.js" data-main="../js/app.js"></script>
	```
`js/app.js`里面的代码是这样的
	```javascript
	// baseUrl: www/js/lib
	requirejs.config({
		baseUrl: 'js/lib'
	});
	require(['hello'], function(hello) {
		hello.hello('requirejs');
	});
	```




## nodejs的路径解析机制

## es6模块路径解析机制

## webpack模块路径解析机制

## 参考

* [RequireJS路径详解(深入理解)](http://www.jianshu.com/p/99321f292776)
