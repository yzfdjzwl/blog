```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title></title>
	<style>
		body {
			color: green;
		}

		.red {
			color: red;
			font-size: 50px;
		}
	</style>
</head>
<body>
	<div class="">
		<a href="#">这是a标签，有href</a>
		<a>这是a标签，没有href</a>
		<span>这是文字</span>

		<div class="red">
			<h2>我是标题</h2>
		</div>
	</div>

	<p>下面，我理一理有哪些情况</p>
	<ul>
		<li>1. 设置body的颜色为red, a标签颜色不改变, 原因是因为user agent的a自带有 ':any-link'.权重为10，覆盖了a标签继承a标签的样式。为什么是user agent的样式高于了开发者定义的样式呢? 其实这里，a继承来的颜色和user agent的a的颜色不是同一级别的。因为它的颜色是继承来的(也就是说，并不是user agent的样式高于了开发者定义的样式, 如果要比较，是指定了a的color而不是从其他地方继承过来的color)。所以不能单纯的比较样式的优先级。如果要比较user agent 和 user 以及 author的优先级，是建立在同一元素以及同一级别。</li>

		<li>2. 如果直接设置a的颜色，a标签颜色要改变, 原因是author优先级比user agent优先级高.</li>

		<li>3. 如果没有href, 那么a标签颜色要改变。原因是没有user-agent里的':any-link'</li>
	</ul>
</body>
</html>
```