<!-- vim-markdown-toc GFM -->
* [Introducing positioning](#introducing-positioning)
    * [Static positioning](#static-positioning)
    * [Relative positioning](#relative-positioning)
    * [Introducing top, bottom, left and right](#introducing-top-bottom-left-and-right)
    * [Absolute positioning](#absolute-positioning)
    * [Positioning contexts](#positioning-contexts)
    * [Introducing z-index](#introducing-z-index)
    * [Fixed positioning](#fixed-positioning)
* [浮动](#浮动)
    * [简单例子](#简单例子)
    * [清除浮动](#清除浮动)
        * [浮动的特点](#浮动的特点)
        * [浮动造成的影响](#浮动造成的影响)
        * [浮动问题](#浮动问题)
    * [参考](#参考)
* [BFC](#bfc)
    * [哪些元素会生成BFC?](#哪些元素会生成bfc)
    * [BFC的布局规则](#bfc的布局规则)
    * [BFC的作用与原理](#bfc的作用与原理)
* [替换元素与非替换元素](#替换元素与非替换元素)
    * [margin-top/margin-bottom与padding-top/padding-bottom](#margin-topmargin-bottom与padding-toppadding-bottom)

<!-- vim-markdown-toc -->

## Introducing positioning

> Positioning可以元素脱离normal document layout flow.

默认下，块级盒子的宽度是其父元素的100%，并且和它自己的内容一样高。行级盒子的宽度和高度都和它自己的内容一样大。

The normal layout flow是元素放置在浏览器视口内的系统。默认下，block元素垂直列出，每个出现在一个新行，并且在上一行的下面，它们由margin隔开。

inline元素不一样，它和其他的inline元素或者text内容在同一行，只要父级block盒子有空间就这么做，如果没有空间，那么超出的内容或元素会移动到新的一行里。

特性请看**替换元素与非替换元素**。

### Static positioning

Static positioning 	is the default that every element gets -- it just means "put the element into it's normal position in the document layout flow  --- nothing special to see here".

### Relative positioning 

相对定位和static定位很相似，除了一旦定位的元素在正常流中占据了位置，则可以修改其最终位置，包括使其与页面上的其他元素重叠。

简单概括，设置了relative后，脱离了文档流，原来的位置依然占据。通过left，right设置的值会与其他元素重叠。

```
position: relative;
```

### Introducing top, bottom, left and right

使用top、bottom、left和right可以指定元素往哪个方向移动。举例来说：

```
div {
	position: relative;
	top: 30px; 
	/*表示举例原来的top30px*/
}
```

### Absolute positioning

使用绝对定位，元素脱离文档流，原来的位置将不再保留。当设置了绝对定位后，它的位置也会改变。而使用top、right、bottom、left也将不一样了，它表示距离最近有定位属性的祖先的top或者right或者bottom或者left。

###	Positioning contexts

哪个元素是绝对定位的"包含元素"? 默认情况下，它是html元素--- 定位元素嵌套在body里，但在最终的布局中，它离页面边缘的顶端和左边30px，这是html元素。这更准确地称为元素的positioning context.

我们可以改变positioning content --- 绝对定位的元素相对于其定位的元素。这是通过在元素的其他祖先元素之一设置定位实现的。

```
div {
	position: relative;
}

div p {
	position: absolute;
}
```

### Introducing z-index

定位元素会在未定位元素上面，但是当有多个定位元素的时候，我们如何考虑它们的层叠顺序呢?

如果有多个定位元素的话，后定位的元素会在之前定位的元素的上面，但是如何改变它们的顺序呢？

我们可以使用"z-index", 默认下，定位的元素z-index的值是auto，实际上是0。我们可以通过改变它的值来改变定位元素的层叠顺序。但是不可以改变未定位元素的层叠顺序。

### Fixed positioning 

Fixed position和绝对定位类似，但是fixed position 它相对的是浏览器的视口。不管浏览器是如何滚动的或者如何放大缩小的，它都相对的是浏览器的视口。

## 浮动

**浮动过后的任何元素都成了block level element**

### 简单例子

```
/* html */
<p>
	This is my very impoort paragraph.
 I am a distinguished gentleman of such renown that my paragraph
	  needs to be styled in a manner befitting my majesty. Bow before
	  my splendour, dear students, and go forth and learn CSS!
</p>

/* CSS */
p {
	width: 400px;
	margin: 0 auto;
}

p::first-line {
	text-transform: uppercase;
}

p::first-letter {
	font-size: 3em;
	border: 1px solid black;
	background: red;
	float: left;
	padding: 2px;
	margin-right: 4px;
}
```

### 清除浮动

所有内容在浮动，如果不处理，那么就会很可怕。幸运的是有一个属性叫做clear，当将它应用到一个元素时，它基本上说"停止浮动到这里"--- 这个元素和它之后的元素都不会浮动，除非你应用一个新的float到另一个元素。

clear有三个值:

* left: 停止左侧浮动
* right: 停止右侧浮动
* both: 停止左右浮动

#### 浮动的特点

* 浮动元素会脱离正常的文档流，按照其margin指定的位置相对于它的上一个块级元素(或父元素)显示。
* 浮动元素后面的块级元素的内容会围绕此浮动元素, 但是边框和背景却忽略浮动元素而向上一个任意非浮动元素靠其。
* 浮动元素后面的内联元素会向此浮动元素的外边距靠齐。

#### 浮动造成的影响

1. 对父元素的影响： 对于其父元素来说，元素浮动之后，它将脱离正常的文档流，所以它也无法撑开其父元素, 造成其父元素的塌陷。
2. 对其兄弟元素(非浮动)的影响:
	* 如果兄弟元素是块级元素: 该元素会忽略浮动元素的位置而占据它的位置，并且元素会处在浮动元素的下层，但它的内部文字和其他行内元素都会围绕浮动元素。
	* 如果兄弟元素是内联元素：则元素会环绕浮动元素排列。
3. 对其兄弟元素(浮动)的影响:
	* 同一个方向的浮动元素：当一个浮动元素在浮动过程中碰到同一个方向的浮动元素时，它会紧跟在它们后面。
	* 相反方向的浮动元素：互不影响，在同一条水平线上，当空间不够时会被挤下去。

#### 浮动问题

当对div用的百分比时，然后浮动刚刚好，但是如果要为div设置border、padding等等，那么可能就会超过100%或者父级盒子的大小，因此可以为div设置box-sizing: border-box。

另外一个问题, 当你有一下布局时：

```
<div>
	<div class="fl">111</div>
	<div class="fl">222</div>
</div>
<footer>333</footer>

.fl {float: left}
footer {margin-top: 50px;clear: left;}
```

因为使用了clear: left后，所以footer不在围绕浮动元素。但是你会发现，margin-top: 50px并不起作用。为了解决这个问题, 在HTML中添加新的div元素，并且设置它的class为clearfix。此时，可以取消了footer的clear:both;如下：

```
<div>
	<div class="fl">111</div>
	<div class="fl">222</div>
	<div class="clearfix"></div>
</div>
<footer>333</footer>

.fl {float: left}
footer {margin-top: 50px;}
clearfix {clear: both;}
```

但是这只是简单的解决了这个问题而已。

因此概括一下清除浮动(我觉得应该叫闭合浮动更好一点儿，清除浮动只是清除了浮动，但是不能解决高度塌陷的问题)的几种方法：

* 第一个，像上面那样，但是没有语意化，并且增加了许多毫无意义的标签。
* 第二种，The Overflow Method, 设置overflow: auto或者hidden，就可以了，但是它不是专门用来闭合浮动的，而且要小心overflow后可能会隐藏或者出现滚动条。
* 第三种，推荐使用。

```
.clearfix:after {
	content: ".";
	display: block;
	clear: both;
}
```

### 参考

[All about floats](https://css-tricks.com/all-about-floats/)
[那些年我们清除过的浮动](http://www.iyunlu.com/view/css-xhtml/55.html)
[BFC神奇背后的原理](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)

## BFC

BFC的中文意思是块级格式化上下文。它是一块独立的渲染区域，只有Block-level box参与，它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相关。

### 哪些元素会生成BFC?

* 根元素
* float属性不为none
* position为absolute或fixed
* display为inline-block, table-cell, table-caption, flex, inline-flex(关于这一点我是真的没有测试出来)
* overflow不为visible

### BFC的布局规则

* 内部的box会在垂直方向，一个接一个地放置。
* Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
* 每个元素的margin box的左边，与包含块的border box的左边相接触，即使存在浮动也是如此。
* BFC的区域不会和float box重叠。
* BFC就是页面上的一个隔离的独立容器，容器里的子元素不会影响到外面的元素。反之也如此。
* 计算BFC的高度时，浮动元素也参与运算。

### BFC的作用与原理

* 自适应两栏布局
* 清除内部浮动
* 防止margin重叠

参考：

[BFC背后神奇的原理](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)

## 替换元素与非替换元素

替换元素(replaced element)：所谓替换元素就是浏览器根据元素的标签和属性，来决定元素具体显示什么内容。比如说：img标签的src属性的值用来读取图片信息并且显示出来。又比如说，input标签的type属性决定是显示输入框还是单选按钮。html中的img、input、textarea、object、video、embed都是替换元素。

非替换元素(non-replaced element): 是指内容包含在文档里的元素。比如p、span等等。

### margin-top/margin-bottom与padding-top/padding-bottom

在考虑inline的时候，我们就需要考虑inline元素到底是行内可替换元素还是行内不可替换元素。

**讨论margin-top和margin-bottom对行内非替换元素是否其作用:**

1. 规范允许margin可以设置到行内元素。
2. 由于向一个行内非替换元素应用margin，对行高没有影响。
3. 并且margin是透明的，所以声明margin-bottom, margin-top没有任何视觉效果。
4. 而对于行内非替换元素应用左右的margin，是有影响的。

**讨论margin-top和margin-bottom对行内替换元素是否其作用:**

1. 为行内替换元素设置margin会影响行高。
2. 所以margin-top和margin-bottom是有视觉效果的。
3. 对行内替换元素应用左右的margin，也是有影响的。

**讨论padding-top和padding-bottom对行内非替换元素是否其作用:**

1. 明确是有作用的，可以设置背景颜色看出来。
2. 不会影响block布局。

**讨论padding-top和padding-bottom对行内替换元素是否其作用:**

1. 明确是有作用的，可以设置背景颜色看出来。
2. 也会影响block布局的。

**参考:**

[是不是inline元素就不能设置宽高margin，padding？](https://segmentfault.com/q/1010000000694623)
[CSS中的一些概念](http://www.cnblogs.com/shenfangfang/p/5667848.html)