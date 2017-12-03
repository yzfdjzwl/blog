简单的画一个三角形，代码如下: 

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title></title>
	<style>

		/* 正方形8*8 */
		.a {
			width: 0;
			height: 0;
			border: 4px solid;
		}
			
		/* 正方形80*80 (4个三角形) */
		.b {
			width: 0;
			height: 0;
			border-top: 40px solid #000;
			border-right: 40px solid #ff0000;
			border-left: 40px solid #ff0000;
			border-bottom: 40px solid #000;
		}

		/* 三角形: 左右透明，下面没有边框。 */
		.c {
			width: 0;
			height: 0;
			font-size: 0;
			border-width: 40px;
			border-color: #333 transparent transparent transparent;
			border-style: solid;
			border-bottom: none;
		}

	</style>
</head>
<body>
	<div class="a"></div>
	<div class="b"></div>
	<div class="c"></div>
</body>
</html>
```