> 想要实现的效果是，一列宽度固定，一列宽度自适应。想到的有三种方法。

1. 绝对定位

2. 浮动

3. 两列都绝对定位

下面请看代码：

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title></title>
	<style>
		.container1, .container2, .container3 {
			width: 100%;
			margin-bottom: 20px;
			position: relative;
			height: 200px;
		}

		.left {
			width: 100px;
			height: 200px;
			background: #999;
		}

		.right {
			height: 200px;
			background: red;
		}

		/*绝对定位*/
		.container1 .left {
			position: absolute;
			left: 0;
			top: 0;
		}

		.container1 .right {
			margin-left: 120px;
		}

		/*浮动*/
		.container2 .left {
			float: left;
		}

		.container2 .right {
			margin-left: 120px;
		}

		/*都绝对定位*/
		.container3 .left {
			position: absolute;
			left: 0;
			top: 0;
		}

		.container3 .right {
			position: absolute;
			left: 120px;
			top: 0;
		}



	</style>
</head>
<body>
	<div class="container1">
		<div class="left">绝对定位</div>
		<div class="right">margin-left</div>
	</div>

	<div class="container2">
		<div class="left">浮动</div>
		<div class="right">margin-left</div>
	</div>

	<div class="container3">
		<div class="left">绝对定位</div>
		<div class="right">绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位绝对定位</div>
	</div>
</body>
</html>
```