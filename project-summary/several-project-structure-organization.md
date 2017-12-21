## 目录

<!-- vim-markdown-toc GFM -->
* [写在前面](#写在前面)
	* [传统web项目开发](#传统web项目开发)
	* [传统的前后端分离项目](#传统的前后端分离项目)
	* [react项目](#react项目)

<!-- vim-markdown-toc -->

## 写在前面

> 由于自己是一个只做一些简单的需求的前端小白，还没有自己独立完整的完成过一个项目，因此对前端的一些开发充满了好奇心。在这里，简单记录一下我遇到过的项目结构组织方式。

### 传统web项目开发

首先介绍的是：传统web项目开发。

* 这是一个后端是Java的工程，前端代码放置在webapp下面。
* 公用的页面模块，比如`header.ftl`, `footer.ftl`放置在`decorators`里面，可以通过`#include`来引用。
* 页面模板，因为是后端渲染，使用的是`freemarker`这样类似于`jsp`的模板语言, 放置在`webapp/WEB-INFO/views`下面。
* 所有的css，js，img以及静态的html放置在`static`目录下面。
* 页面如何引用的CSS? 在decorators下面会有公共的`header.ftl`，所有的页面都会使用到这个`header.ftl`, 而在`header.ftl`里面的`head`标签里，可以通过配置(模板变量配置路径)引用到相应页面的css。
	```html
	<head>
		<link rel="stylesheet" href="${staticPath}/css/page${page.properties['page.css']}.css" />
		<script>
			var MT = {};
			MT.BOOTSTAMP = new Date().getTime();
			MT.STATIC_URL = "${staticPath}";
			MT.ENV = "${__environment__}";
		</script>
	</head>
	```
* 页面如何引用的JS? 和引用CSS的方法一致，不过不一样的是，是所有的页面都用到了`footer.ftl`, 在`footer.ftl`里面通过配置引用到相应页面的JS。
* 上面的引用JS的方式，并不是简单的引用，而是通过[require.js](http://requirejs.org/)来引用的, 看看代码：
	```javascript
	<script type="text/javascript" data-main="${staticPath}/js/page${page.properties['page.javascript']}" src="https://cdn.bootcss.com/require.js/2.3.5/require.js"></script>
	```
* 那么每个页面的js都是使用AMD规范，可以看到大概这样的代码：
	```javascript
	require.config({
		baseUrl: MT.STATIC_URL + '/js'
	});
	require(['lib/jquery'], function($) {
		var aClick = function() {};
		var bClick = function() {};
		var cClick = function() {};
		var eventBind = function() {
			document.querySelector('#a').onclick = aClick;
			document.querySelector('#b').onclick = bClick;
			document.querySelector('#c').onclick = cClick;
		};
		eventBind();
	});
	```
* 上面的js文件中，就可以很方便且容易的使用面向对象的javascript。
* 既然用到了`require.js`，那么就会涉及到一些相关的知识，比如AMD模块规范、路径解析、lazyLoad等等。


* 项目目录如下：
	```text
	├── webapp
		├── WEB-INF
		│   ├── conf
		│   ├── decorators
		│   └── views
		│       ├── view1
		│      		└── view1.ftl
		│      		└── view1_config.ftl
		│       ├── view2
		│      		└── view2.ftl
		│     		└── view2_config.ftl
		└── static
			├── css
			│   ├── basic
			│   │   └── old
			│   ├── lib
			│   │   └── jquery
			│   ├── module
			│   │   └── some-module
			│   ├── page
			│   │   └── some-page
			│   └── widget
			│       └── dialog
			├── img
			│   └── some-page-img
			├── html
			│   ├── some-static-html
			└── js
				├── lib
				├── module
				│   └── some-module
				├── page
				│   └── some-page-js
				├── util
	```

### 传统的前后端分离项目

为什么说是传统的前后端分离项目, 因为他不是用`react vue angular`完成的，而是用jquery完成的。并且，还是一个前后端分离的项目，也就是说，并没有用到后端渲染， 并且前端和后端两个项目也是分开的。

* 此类项目是由[cortex.js](https://github.com/cortexjs/cortex)(一个类似于npm的包管理工具)构建的。
* 既然是前后端分离的，那么是前端控制路由的，前端是如何控制路由的？
* 分析一下启动过程，根据cortex的文档我们可以猜测，启动后首先运行的是`src/index.js`这个文件。
	* `var routeconfig = {}`, 这里面配置了路由的路径。
	* `var app = App.create({ routeconfig }); app.start();` 启动项目。
	* 在App.js里面:
		```javascript
		this.start = function() {
			this.action(hashState.getHash()); // 获取windows.location.hash
			// hashChange事件
			hashState.on('hashChange', function(e) {
				self.action(e.newHash);
			});
		};
		this.action = function() {
			// 大概的伪代码的意思就是去调用那个页面对应的js文件: init.js里面的某个函数(show/reshow)。
		};
		```
* 而对于每个页面，会有一个`init.js`以及`template.html`，init.js页面的show()方法，用于初始化页面，即进行前端页面的渲染, 使用的是前端的模板引擎。
	```javascript
	var tpl = require('tpl');
	// template.html.js是template.html变为字符串，通过require.js导出的。
	var template = require('./template.html.js');
	var self = {
		show: function() {
			var templateFn = tpl.compile(template); // 将前端模板编译成一个函数
			var result = templateFn(someData); // 将数据传入后，渲染出DOM
			$('.page').appendTo(result); // 将DOM结构添加到页面上
		};
	};
	```
* 这样，每次hash值变化后，就会重新去调用show方法，重新渲染出新的页面来。
* 目录结构:
    ```text
	├── src
		├── common
		├── components
		├── lib
		├── pages
		└── service
		└── index.js
    ```

### react项目
