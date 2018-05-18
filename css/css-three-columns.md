# 常见的三列布局

在这里，介绍几种我所了解到的几种三列布局。实现的方法分别是：

1. 两边绝对定位，中间设置margin-left和margin-right。
2. 两边分别左右浮动，中间设置margin-left和margin-right。
3. 圣杯布局。
4. 双飞翼布局。
5. BFC布局，左边左浮动，设置margin-right：120px， 右边右浮动，设置margin-left：120px，设置中间部分：overflow: hidden, 形成BFC。
6. flex，这里就没有单独介绍了。

看代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title></title>
	<style>

		.clearfix:after {
			content: '';
			display: block;
			clear: both;
		}
		.container {
			width: 100%;
			margin-bottom: 20px;
		}
		.main {
			height: 200px;
			background: red;
		}
		.left, .right {
			width: 100px;
			height: 200px;
			background: #999;
		}

		/*绝对定位*/
		.absolute {
			position: relative;
		}
		.absolute .left {
			position: absolute;
			left: 0;
			top: 0;
		}
		.absolute .right {
			position: absolute;
			right: 0;
			top: 0;
		}
		.absolute .main {
			margin-left: 120px;
			margin-right: 120px;
		}

		/*浮动定位*/
		.float .left {
			float: left;
		}

		.float .right {
			float: right;
		}
		
		.float .main {
			margin-left: 120px;
			margin-right: 120px;
		}

		/*圣杯布局*/
		.shengbei .main, .shengbei .left, .shengbei .right {
			float: left;
		}

		.shengbei .left {
			margin-left: -100%;
			position: relative;
			left: -120px;
		}

		.shengbei .right {
			margin-left: -100px;
			position: relative;
			right: -120px;
		}

		.shengbei .main {
			width: 100%;
		}
		
		/*这里用了box-sizing， 兼容性不是很好*/
		.shengbei.clearfix {
			box-sizing: border-box;
			padding-left: 120px;
			padding-right: 120px;
		}

		..clearfix::after {
			content: '';
			display: block;
			clear: both;
		}


		/*双飞翼布局*/
		.fly .main {
			width: 100%;
			background: #fff !important;
		}
		.fly .main, .fly .left, .fly .right {
			float: left;
		}	
		.fly .left {
			margin-left: -100%;
		}
		.fly .right {
			margin-left: -100px;
		}
		.fly .main .inner {
			margin-left: 120px;
			margin-right: 120px;
			background: red;
			height: 200px;
		}

		/*bfc布局*/
		.bfc .left {
			float: left;
			margin-right: 20px;
		}

		.bfc .right {
			float: right;
			margin-left: 20px;
		}
		
		.bfc .main {
			overflow: hidden;
		}

	</style>
</head>
<body>
	<div class="container absolute">
		<div class="left"></div>
		<div class="main">absolute</div>
		<div class="right"></div>
	</div>

	<div class="container float">
		<div class="left"></div>
		<div class="right"></div>
		<div class="main">float: 因为浮动脱离了文档流，所以将main放在最后。</div>
	</div>
	
	<div class="container shengbei clearfix">
		<div class="main">圣杯布局，main放在最前面，最开始渲染</div>
		<div class="left">left</div>
		<div class="right">right</div>
	</div>

	<div class="container fly clearfix">
		<div class="main">
			<div class="inner">
				双飞翼布局: 注意取消main的背景颜色，设置inner的颜色和高度。
			</div>
		</div>
		<div class="left"></div>
		<div class="right"></div>
	</div>

	<div class="container bfc">
		<div class="left">margin-right: 20px;</div>
		<div class="right">margin-left: 20px;</div>
		<div class="main">bfc布局: overflow: hidden;</div>
	</div>

</body>
</html>
```
