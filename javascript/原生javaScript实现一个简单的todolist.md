直接上代码：

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title></title>
	<style>

	</style>
</head>
<body>
	<h1>todolist</h1>
	<input type="text">
	<ul>

	<ul>
	<script>
		var oInput = document.getElementsByTagName("input")[0];

		oInput.onkeydown = function(event) {

			var event = event || window.event;
			if(event.keyCode == 13) {
				// 添加数据
				var data = this.value;
				addData(data);
				// 清空数据
				clearData();
			}
		}

		// 模糊查询
		oInput.oninput = function() {
			var arrLi = document.getElementsByTagName("li");
			var arr = [];
			
			for(var i = 0;i < arrLi.length;i++) {
				if(arrLi[i].firstChild.innerHTML.trim().indexOf(this.value.trim()) != -1) {
					arr.push(i);
				}
			}
			// show
			show(arr);
		};


		function show(arr) {
			var arrLi = document.getElementsByTagName("li");
			// 隐藏所有
			for(var i = 0;i < arrLi.length;i++) {
				arrLi[i].style.display = "none";
			}

			// 显示
			if(arr.length > 0) {
				for(var i = 0;i < arr.length;i++) {
					arrLi[arr[i]].style.display = "block";
				}
			} else {
				for(var i = 0;i < arrLi.length;i++) {
					arrLi[i].style.display = "block";
				}
			}

		}

		function addData(data)  {
			var tempLi = document.createElement("li");
			var tempBtn = document.createElement("button");
			var tempSpan = document.createElement("span");
			var oUl = document.getElementsByTagName("ul")[0];

			tempBtn.innerHTML = "delete";
			tempBtn.name = "delete";
			tempSpan.innerHTML = data;
			tempLi.appendChild(tempSpan);
			tempLi.appendChild(tempBtn);
			oUl.appendChild(tempLi);
		}

		function clearData() {
			var oInput = document.getElementsByTagName("input")[0];

			// 不能用innerHTML
			oInput.value = "";
		}

		// 点击删除
		var oUl = document.getElementsByTagName("ul")[0];
		oUl.onclick = function(event) {
			var event = event || window.event;
			var target = event.target;

			if(target.name == "delete") {
				target.parentNode.parentNode.removeChild(target.parentNode);
			}
		};

	</script>
</body>
</html>
```