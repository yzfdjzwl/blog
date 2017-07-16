[阅读笔记来源](https://www.ibm.com/developerworks/cn/web/1308_caiys_jsload/index.html)

## 概括

* 无论当前JavaScript代码是内嵌还是在外链文件中，页面的下载和渲染都必须停下来等待脚本执行完成。
* 如果出现了下面的情况:

```
<p>
	<script type="text/javascript">
		document.write('test');
	</script>
</p>
```

当浏览器遇到< script>时，当前HTML页面无从获知JavaScript是否会向< p> 标签添加内容，或引入其他元素，或甚至移除该标签。因此此时，浏览器会停止处理页面，先执行JavaScript中的代码，然后再继续解析和渲染页面。同样，如果有src属性加载JavaScript时，浏览器必须先花时间下载外链里的代码，然后解析并执行它。整个过程中，页面渲染和用户交互被完全阻塞了。

### 脚本位置

HTML4规范指出script标签可以放在head或body里, 并允许出现多次。

以前，web开发者喜欢这么放:

```
<head>
	<script type="text/javascript" src="s1.js"></script>
	<script type="text/javascript" src="s2.js"></script>
	<script type="text/javascript" src="s3.js"></script>
	<link ref="stylesheet" type="css/text" href="style.css">
</head>
```

html页面，从上往下的执行过程是这样的：

1. 下载s1, 执行s1

2. 下载s2, 执行s2

3. 下载s3，执行s3

4. 下载style

因此，整个下载执行的过程中，用户看到的是一片空白。

从IE8、Firefox3.5、Safari4和chrome2开始允许 **并行** 下载JavaScript文件。遗憾的是，JavaScript的下载依然会阻碍其他资源的下载，比如样式和图片。虽然JavaScript可以并行下载，但是页面仍然必须等待所有JavaScript代码下载并执行完成后才能继续。

**所以推荐将Scirpt脚本放置与body的底部, 这是优化JavaScript的首要规则。**

### 组织脚本

减少script标签数量有助于改善情况。这是因为：

* 浏览器在解析HTML页面的过程中每遇到一个script标签，都会因执行脚本而导致一定的延时。 
* HTTP请求会带来额外的开销，所以下载单个100kb比下载5个20kb的文件要更快。
* 通常，一个大的网站通过打包工具或者CDN来实现。

需要特别注意的是，把内嵌script标签放在link后面会导致页面阻塞去等待样式表的下载。这么做是为了确保内嵌脚本在执行时能获得最精确的样式信息。因此，建议不要将内嵌的script标签紧跟在link标签后面。

## 无阻塞的脚本
 
减少JavaScript文件的大小并不总是可行，因为下载一个较大的JavaScript文件只产生一次HTTP请求，却会锁死浏览器的一大段时间。为了避免这种情况，需要通过一些特定的技术向页面中逐渐加载JavaScript文件，这样做在某种程度上不会阻塞浏览器。

### 延迟加载脚本

HTML4定义了defer属性，HTML5定义了async属性。它们的作用差不多，区别是浏览器支持不一样，如果浏览器支持async属性, 那么它会忽略defer属性。

```
<body>
	<script type="text/javascript" async>
		console.log('async');
	</script>

	<script type="text/javascript">
		console.log('script');
	</script>

	<script type="text/javascript" async>
		window.onload = function() {
			console.log('load');
		}
	</script>
</body>
```

如果是上面这样的话，最后输出的顺序是：async, script, load。

但是如果下面的代码：

```
<script type="text/javascript" src="s1.js" async></script>
<script type="text/javascript" src="s2.js"></script>
<script type="text/javascript" src="s3.js"></script>

// s1
console.log("async");

// s2
console.log("script");

// s3
window.onload = function() {
	console.log("load");
};
```

那么最后输出的顺序可能是async, script, load。也有可能是script, async, load。

因为带有async属性的外链script，它会立即被下载(和其他外链script一起下载)，但是不会马上执行，它一定会在onload被调用前执行。

### 动态脚本元素

也就是通过创建script标签来加载外链script, 此文件当元素添加到页面之后 **立即** 开始下载。此技术的重点是：无论在何处启动下载，文件的下载和运行都不会阻塞其他页面处理过程。


总结一下：

* 放在body之前
* 减少script标签
* 使用defer/async属性
* 使用动态脚本技术