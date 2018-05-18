# 常用的正则表达式

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title></title>
</head>
<body>
	<script>
	/*
	 * 匹配名字输入，要求2～6个汉字
	 */
	function validateChineseName(value) {
		var rule = /^[\u4e00-\u9fa5]{2,6}$/;
		console.log(rule.test(value));
	}

	/*
	 * 验证10位数字(学号)
	 */
	function validateNumber(value) {
		var rule = /^2[0-9]{9}$/;
		console.log(rule.test(value));
	}

	/*
	 * 验证密码(强密码)
	 * 要求：必须包含数字、大写字母、小写字母， 并且不能有特殊字符，长度在8～10内
	 * 使用了断言(满足了那个规律，但是并不会向下匹配)
	 * 当单词后面是数字再往下匹配，当单词后面是小写字母再往下匹配
	 * 当单词后面是大写子吗再往后匹配
	 * 当断言都满足后，单词的大小长度是8～10个字符。
	 * 如果要求特殊字符也可以，那么就将\w改为.
	 */
	function validatePwd(value) {
		var rule = /^(?=\w*\d)(?=\w*[a-z])(?=\w*[A-Z])\w{8,10}$/;
		console.log(rule.test(value));
	}

	/*
	 * 验证电话
	 * 
	 * 以1开头，第二位是1~9，后面9位是数字
	 *
	 */
	function validatePhoneNumber(value) {
		var rule = /^1[1-9]\d{9}$/;
		console.log(rule.test(value));
	}

	/*
	 * 验证邮箱 
	 * 1-9开头表示电话或qq号码
	 *
	 */
	function validateEmail(value) {
		var rule = /^[a-z1-9]+([_-]?[a-z0-9]+)*@[a-z1-9]+([_-]?[a-z0-9]+)*(\.[a-z]{2,3})+$/;
		console.log(rule.test(value));
	}



	validateChineseName("我是一个人");
	validateNumber("2014110452");
	validatePwd("aa1Zs*8a_");
	validatePhoneNumber("18384259782");
	validateEmail("yzfdjzwl@gmail.com");
</script>
</body>
</html>
```
