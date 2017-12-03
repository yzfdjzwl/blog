## input文本和label对齐

默认状态下，也就是下面这样, 文字和input是居中的。

```
<div>
	<label>我是中国人</label>
	<input type="text">
</div>
```

但是经常设计图中有label的行高，或者input的高度设计，默认状态下，依然是居中的。但是很多时候label和input要浮动，一旦浮动，后面的input标签就紧紧的贴住了label标签，也就造成了视觉上看到并没有居中。 

比如下面：

```
<div>
	<label>我是中国人</label>
	<input type="text">
</div>


label {
	font-size: 12px;
	float: left;
}

input {
	height: 20px;
}
```


渲染出来的效果是这样

![](http://images2015.cnblogs.com/blog/775835/201703/775835-20170312105525811-1977003843.png)

但是这并不是我想要的结果，所以通常可以控制label的padding-top，视觉上让它看起来是居中的。就像下面一样：

![](http://images2015.cnblogs.com/blog/775835/201703/775835-20170312105536217-48426761.png)

## input单选框和label对齐

默认状态下，label和radio是不对齐的，我们知道，input是内联元素，但是它依然是可以设置高度和宽度的，因为它是内联可替换元素。默认状态下，radio是有margin值的，但是这并不是它影响label和radio是不居中的。默认效果如下:

```
<div>
	<label>男x</label>
	<input type="radio">
</div>
```

![](http://images2015.cnblogs.com/blog/775835/201703/775835-20170312105556451-747816875.png)


因为label对齐的是文字的基线，所谓基线，就是x的底端。

如果我们浮动的话，会发生什么事情呢？

```
<div>
	<label>男x</label>
	<input type="radio">
</div>

label {
	font-size: 30px;
	float: left;
}
```

![](http://images2015.cnblogs.com/blog/775835/201703/775835-20170312105605248-966806547.png)
![](http://images2015.cnblogs.com/blog/775835/201703/775835-20170312105616342-1843019891.png)

结果发现，浮动之后，radio紧紧的贴着label，并且位于顶端。

那么到底如何让它们对齐呢?


```
<div>
	<label>男x</label>
	<input type="radio">
</div>

label {
	font-size: 50px;
	vertical-align: middle;
}

input {
	vertical-align: middle;
}
```

得出来的效果如下：
![](http://images2015.cnblogs.com/blog/775835/201703/775835-20170312105630248-502332812.png)



那么，我们再来测试一下，这种方法是否可以。设置label的高度。

```
<div>
	<label>男x</label>

	<input type="radio">
</div>

label {
	font-size: 50px;
	display: inline-block;
	height: 140px;
	vertical-align: middle;
}

input {
	vertical-align: middle;
}
```

结果显示如下：

![](http://images2015.cnblogs.com/blog/775835/201703/775835-20170312105653717-830635906.png)

说明，这种方法可行。还可以设置padding测试，这里就不测试了。

总结：

同时设置label和input的vertical-align: middle就可以了。

## 其他

1. margin-top百分比，是相对于父元素的宽度的百分比计算的, 也只是相对于宽度(width)，不包括父元素的padding和margin值。
2. 直接总结的CSS部分知识点。
![](http://images2015.cnblogs.com/blog/775835/201707/775835-20170716124516316-674093517.png)