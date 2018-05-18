# 正则表达式总结 

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
		 * 元字符
		 */

		// 字符串里的\本身是特殊字符，如果要用，需要转义
		var str1 = "He is a boy, is he?";
		var regexp = new RegExp('\\bis\\b', 'g');

		console.log(str1.replace(/\bis\b/, 'IS'));
		console.log(str1.replace(/\bis\b/g, 'IS'));
		console.log(str1.replace(/\bis\b/i, 'IS'));
		console.log(str1.replace(regexp, "IS"));

		var str2 = "@123\n@456\n@789";
		console.log(str2.replace(/^@./gm, 'A'));


		/*
		 * 构建类[abc], one of abc
		 */
		var str3 = "abcdefgABCDEFG123456";
		console.log(str3.replace(/[abc]/g, 'B'));

		/*
		 * 反向类[^abc], none of abc
		 */
		console.log(str3.replace(/[^abc]/g, 'C'));

		/*
		 * 范围类[a-z] [A-Z] [0-9]
		 */
		console.log(str3.replace(/[a-z]/g, 'D'));
		console.log(str3.replace(/[a-zA-Z]/g, 'E'));
		console.log(str3.replace(/[a-zA-Z0-9]/g, 'F'));

		/*
		 * 预定义类
		 * .等价与 [^\r\n]
		 * \d等价于[0-9]
		 * \D等价于[^0-9]
		 * \s等价于[^\t\n\x0B\f\r] ，即空白符
		 * \S等价于[\t\n\x0B\f\r] ，即非空白符
		 * \w等价于[a-zA-Z_0-9], 即字母，数字，下划线
		 * \W等价于[^a-zA-Z_0-9], 即非字母，非数字，非下划线
		 */
		var str3 = "abcdefgABCDEFG123456";
		var str4 = "      xxx    ";
		var str5 = "123abcDEF_$%^&*(";
		console.log(str3.replace(/./g, 'G'));
		console.log(str3.replace(/\d/g, 'H'));
		console.log(str3.replace(/\D/g, 'I'));
		console.log(str4.replace(/\s/g, 'J'));
		console.log(str4.replace(/\S/g, 'K'));
		console.log(str5.replace(/\w/g, 'L'));
		console.log(str5.replace(/\W/g, 'M'));

		/*
		 * 边界字符
		 * ^以什么开始
		 * $以什么结束
		 * \b单词边界
		 * \B非单词边界
		 */
		var str6 = "@123@456@";
		var str7 = "This is a boy";
		var str8 = "ab123456hhh";
		console.log(str6.replace(/^@/, 'N')); // 以@开头的转为'L'
		console.log(str6.replace(/^@./, 'O')); // 以@开头且紧跟后的任意字符 ，将它们转为'L'
		console.log(str6.replace(/^@[0-9]/, 'P')); // 以@开头且紧跟后的数字，将它们转为'L'
		console.log(str6.replace(/@$/, 'Q'));  // 将最后的一个@转为L
		console.log(str7.replace(/\bis\b/, 'IS'));  
		console.log(str7.replace(/\Bis\b/, 'IS'));  

		// ab+数字+任意字符 
		console.log(str8.replace(/ab[0-9][^\r\n]/g, 'R'));
		console.log(str8.replace(/ab\d./g, 'S'));

		/*
		 * 量词， 表示数量的词
		 * ? 表示最多一次
		 * + 表示至少一次
		 * * 表示任意次
		 * {n} 表示n次
		 * {n,m} 表示n到m次, 注意没有空格
		 * {n, } 表示至少n次
		 */
		var str9 = "123456789";
		console.log(str9.replace(/\d?/, 'T'));
		console.log(str9.replace(/\d+/, 'U'))
		console.log(str9.replace(/\d*/, 'V'));
		console.log(str9.replace(/\d{3}/, 'W'));
		console.log(str9.replace(/\d{3,5}/, 'X')); //注意没有空格
		console.log(str9.replace(/\d{2,}/, 'Y')); //注意没有空格

		/*
		 * 正则表达式有贪婪模式
		 * 就像上面量词匹配那样
		 * 它会尽可能匹配多的，比如{3,}表示至少3次，如果超过3次，那么它会多的去匹配
		 * 所以上面最后一个是将str9所有的数字换成了Y
		 *
		 */


		/*
		 * 正则表达式非贪婪模式
		 * 那么如何让正则表达式到非贪婪模式呢？
		 * 在量词后面加上一个问号即可
		 *
		 */
		console.log(str9.replace(/\d{2,}?/, 'Z')); //注意没有空格, 匹配出来只有2次

		/*
		 * 分组
		 * 比如匹配iloveyou出现多次的场景怎么办？
		 * 引入分组，用()包起来
		 * 那么量词将会作用于分组
		 * 
		 * 或
		 * 使用|
		 *
		 */
		var str10 = "ByronByronByron";
		var str12 = "iloveyouihateyou";
		console.log(str10.replace(/(Byron){2}/, 'A'));
		console.log(str12.replace(/i(love|hate)you/g, 'memeda'));


		/*
		 * 反向引用
		 * 如果要将2015-02-15变为15/02/2015该怎么办呢？
		 * 那么就会用到反向引用了。
		 * 每个分组即()都会捕获中间的数据
		 * 用$1 $2 $3来取得捕获的数据
		 *
		 * 那么如何忽略分组呢？
		 * 即不希望捕获分组, 只要在分组内部加上?: 
		 *
		 */
		var str11 = "2016-04-21";
		console.log(str11.replace(/(\d{4})-(\d{2})-(\d{2})/, '$3/$2/$1'));
		console.log(str11.replace(/(?:\d{4})-(\d{2})-(\d{2})/, '$1-$2'));

		/*
		 * 前瞻后顾
		 * 前瞻也就是所谓的断言
		 * 后顾的话，JavaScript不支持
		 * 语法：
		 * 正向前瞻：exp(?=assert)
		 * 负向前瞻：exp(?!assert)
		 * 正向后瞻：exp(?<=assert)
		 * 负向后瞻：exp(?<!assert)
		 *
		 */
		var str12 = "a2*3";
		var str13 = "a2*34v8";
		console.log(str12.replace(/\w(?=\d)/g, 'A')); // 注意，只是匹配\w,后面的那个只是规则，并不会被替换
		console.log(str13.replace(/\w(?=\d)/g, 'B'));  
		console.log(str13.replace(/\w(?!\d)/g, 'C'));

	</script>
</body>
</html>
```
