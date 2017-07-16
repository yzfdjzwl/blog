```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title></title>
	<style>
		* {margin: 0;padding:0;}
		body {background: #eee;}
		div {
			width: 300px;
			height: 300px;
			border: 1px solid #999;
			margin-left: 20px;
			margin-top: 20px;
		}

		/* 水平居中：text-align: center; */
		.c1 {
			text-align: center;
		}

		/* 水平居中：margin: 0 auto; */
		.c2 {
			width: 100px;
			height: 100px;
			margin: 0 auto;
		}

		/* 水平居中：padding-left */
		.c3 {padding-left: 100px;box-sizing: border-box;}
		.c3 div {
			width: 100px;
			height: 100px;
			margin: 0;
		}

		/* 水平居中：margin-left */
		.c4 {
			width: 100px;
			height: 100px;
			margin-left: 100px;
			margin-top: 0;
		}

		/* 水平居中：绝对定位 */
		.c5-pa {
			position: relative;
		}

		.c5 {
			width: 100px;
			height: 100px;
			position: absolute;
			left: 50%;
			margin: 0 0 0 -50px;
		}

		/* 多个块水平居中 */
		.c7 {
			width: 1000px;
			height: 300px;
			margin: 20px auto;
			text-align: center;
		}

		.c7 div {
			margin: 0;
			text-align: left;
			display: inline-block;
			width: 200px;
			height: auto;
		}
	
		/* 多个块水平居中: flex */
		.c8 {
			width: 1000px;
			height: auto;
			margin: 0 auto;
			display: flex;
			justify-content: center;
		}
		
		/* 单行垂直居中: 设置父级的padding值让它们居中，然后设置自己的padding，因为它们不会影响父级的布局。*/
		.v1 {
			width: auto;
			height: auto;
			background: #fff;
			margin: 20px 0;
			padding: 50px;
		}

		.v1 a {
			background: black;
			color: white;
			padding: 40px 30px; /*因为上下的padding是一样的*/
			text-decoration: none;
		}

		/* 单行垂直居中： 行高等于高度 */
		.v2 {
			line-height: 300px;
		}

		/*水平且垂直居中: 固定高度和宽度*/
		.v3-p {
			position: relative;
			height: 200px;
			width: 300px;
			margin: 0 auto;
		}

		.v3-s {
			position: absolute;
			top: 50%;
			left: 50%;
			width: 100px;
			height: 100px;
			margin-left: -50px;
			margin-top: -50px;
			/*说明，margin的百分比是相对于父元素的宽度的，padding的百分比是相对于自己的width的*/
		}

		/*水平垂直居中: 不固定高度和宽度*/
		.v4-p {
			position: relative;
			height: 200px;
			width: 300px;
			margin: 0 auto;
		}

		.v4-s {
			position: absolute;
			width: auto;
			height: auto;
			top: 50%;
			left: 50%;
			transform: translate(-50%, -50%);
			margin: 0;
		}

		/*水平垂直居中: flex*/
		.v5-p {
			height: 200px;
			width: 300px;
			margin: 0 auto;
			display: flex;
			justify-content: center;
			align-items: center;
		}
		.v5-s {
			height: auto;
			width: auto;
		}
	</style>
</head>
<body>
	<h1>水平居中</h1>
	参考https://css-tricks.com/centering-css-complete-guide/
	<div class="c1">
		<span>我是span元素，想要居中, 父级设置了text-align: center;</span>
	</div>

	<div>
		<div class="c2">我是div，已经有固定宽度，想要居中,设置我的margin: 0 auto;</div>
	</div>

	<div class="c3">
		<div class="">我是div，已经有固定宽度，想要居中,设置我的父级padding</div>
	</div>

	<div>
		<div class="c4">我是div，已经有固定宽度，想要居中,设置我的margin-left;</div>
	</div>

	<div class="c5-pa">
		<div class="c5">我是div，已经有固定宽度，想要居中,设置我绝对定位</div>
	</div>

	<div class="c7">
		<div>我是第一个div，我想要和我的兄弟们水平居中</div>
		<div>这里使用的居中方法是：父级text-align:center; 而我和我的兄弟们设置display: inline-block;text-align: left;</div>
		<div>我我是第三个div，我也想要和我的兄弟们水平居中我是第三个div，我也想要和我的兄弟们水平居中是第三个div，我也想要和我的兄弟们水平居中</div>
	</div>

	<div class="c8">
		<div>我是第一个div，我想要和我的兄弟们水平居中</div>
		<div>这里的居中方法是：设置父级display: flex; justify-content: center;</div>
		<div>我我是第三个div，我也想要和我的兄弟们水平居中我是第三个div，我也想要和我的兄弟们水平居中是第三个div，我也想要和我的兄弟们水平居中</div>
	</div>


	<h1>垂直居中</h1>
	<div class="v1">
		<a href="#">first</a>
		<a href="#">second</a>
		<a href="#">third</a>
		<a href="#">forth</a>
	</div>

	<div class="v2">
		<a href="#">first</a>
		<a href="#">second</a>
		<a href="#">third</a>
		<a href="#">forth</a>
	</div>

	<h1>水平垂直居中</h1>
	<div class="v3-p">
		<div class="v3-s">
		</div>
	</div>

	<div class="v4-p">
		<div class="v4-s">
			我想要居中自己！
		</div>
	</div>

	<div class="v5-p">
		<div class="v5-s">
			我想要居中自己！
		</div>
	</div>
</body>
</html>
```