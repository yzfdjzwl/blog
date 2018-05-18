# 浮动后会发生哪些事?

<!-- vim-markdown-toc GFM -->
* [清除浮动](#清除浮动)
  * [浮动的特点](#浮动的特点)
  * [浮动造成的影响](#浮动造成的影响)
* [浮动问题](#浮动问题)
* [参考](#参考)

<!-- vim-markdown-toc -->

**浮动过后的任何元素都成了block level element**

## 清除浮动

所有内容在浮动，如果不处理，那么就会很可怕。幸运的是有一个属性叫做clear，当将它应用到一个元素时，它基本上说"停止浮动到这里"--- 这个元素和它之后的元素都不会浮动，除非你应用一个新的float到另一个元素。

clear有三个值:

* left: 停止左侧浮动
* right: 停止右侧浮动
* both: 停止左右浮动

### 浮动的特点

* 浮动元素会脱离正常的文档流，按照其margin指定的位置相对于它的上一个块级元素(或父元素)显示。
* 浮动元素后面的块级元素的内容会围绕此浮动元素, 但是边框和背景却忽略浮动元素而向上一个任意非浮动元素靠其。
* 浮动元素后面的内联元素会向此浮动元素的外边距靠齐。

### 浮动造成的影响

1. 对父元素的影响
  * 对于其父元素来说，元素浮动之后，它将脱离正常的文档流，所以它也无法撑开其父元素, 造成其父元素的塌陷。
2. 对其兄弟元素(非浮动)的影响
	* 如果兄弟元素是块级元素: 该元素会忽略浮动元素的位置而占据它的位置，并且元素会处在浮动元素的下层，但它的内部文字和其他行内元素都会围绕浮动元素。
	* 如果兄弟元素是内联元素：则元素会环绕浮动元素排列。
3. 对其兄弟元素(浮动)的影响
	* 同一个方向的浮动元素：当一个浮动元素在浮动过程中碰到同一个方向的浮动元素时，它会紧跟在它们后面。
	* 相反方向的浮动元素：互不影响，在同一条水平线上，当空间不够时会被挤下去。

## 浮动问题

当对div用的百分比时，然后浮动刚刚好，但是如果要为div设置border、padding等等，那么可能就会超过100%或者父级盒子的大小，因此可以为div设置box-sizing: border-box。

另外一个问题, 当你有一下布局时：

```html
<div>
	<div class="fl">111</div>
	<div class="fl">222</div>
</div>
<footer>333</footer>

.fl {float: left}
footer {margin-top: 50px;clear: left;}
```

因为使用了clear: left后，所以footer不在围绕浮动元素。但是你会发现，margin-top: 50px并不起作用。为了解决这个问题, 在HTML中添加新的div元素，并且设置它的class为clearfix。此时，可以取消了footer的clear:both;如下：

```html
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

```css
.clearfix:after {
	content: " ";
	display: block;
	clear: both;
}
```

## 参考

* [All about floats](https://css-tricks.com/all-about-floats/)
* [那些年我们清除过的浮动](http://www.iyunlu.com/view/css-xhtml/55.html)
* [BFC神奇背后的原理](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)
* [mdn](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Floats#清除浮动会变复杂)
