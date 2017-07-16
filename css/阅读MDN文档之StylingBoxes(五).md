# 目录

* [BoxModelRecap](#boxmodelrecap)
    * [Box properties](#box-properties)
        * [Overflow](#overflow)
        * [Background clip](#background-clip)
        * [Background origin](#background-origin)
        * [Outline](#outline)
    * [Advanced box properties](#advanced-box-properties)
        * [Setting width and height constraints(约束)](#setting-width-and-height-constraints约束)
        * [Changing the box model completely](#changing-the-box-model-completely)
    * [Box display types](#box-display-types)
        * [Common display types](#common-display-types)
        * [Uncommon display types](#uncommon-display-types)
* [Backgrounds](#backgrounds)
    * [What exactly is a background?](#what-exactly-is-a-background)
    * [The basis: color, image, position, repeat](#the-basis-color-image-position-repeat)
        * [Background color](#background-color)
        * [Background image](#background-image)
        * [Background repeat](#background-repeat)
        * [Background position](#background-position)
    * [Background image: gradients](#background-image-gradients)
    * [Background attachment](#background-attachment)
    * [Background shorthand](#background-shorthand)
    * [Multiple backgrounds](#multiple-backgrounds)
    * [Background size](#background-size)
* [Borders](#borders)
    * [Border radius](#border-radius)
    * [Border images](#border-images)
* [StylingTables](#stylingtables)
    * [A typical HTML table](#a-typical-html-table)
    * [Active learning: Styling our table](#active-learning-styling-our-table)
        * [Spacing and layout](#spacing-and-layout)
        * [Some simple typography(排版)](#some-simple-typography排版)
        * [Graphics and colors](#graphics-and-colors)
            * [Zebra striping(隔行变色)](#zebra-striping隔行变色)
        * [Styling the caption](#styling-the-caption)
* [AdvancedBoxEffects](#advancedboxeffects)
    * [Box shadow](#box-shadow)
        * [A simple box shadow](#a-simple-box-shadow)
        * [Multiple box shadow](#multiple-box-shadow)
        * [Other box shadow features](#other-box-shadow-features)
    * [Filters(滤镜)](#filters滤镜)
    * [Blend modes(混合模式)](#blend-modes混合模式)

# BoxModelRecap

## Box properties

Some points worth remembering:

* 盒模型不会遵守百分比的高度；盒子高度自适应内容或者设置一个绝对的高度。
* 边框忽略宽度百分比设置。
* Margins会存在[margin collapse](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)

### Overflow

当设置盒子绝对的高度或者宽度时，内容可能会超出。为了解决这个问题，有了overflow属性，下面介绍最常见的3个值。

* auto
* hidden
* visible

### Background clip

背景由背景图和背景颜色构成。默认下，背景扩展到border的边缘。这很好，但是有时候有其他的情况怎么办？[look at this](https://developer.mozilla.org/en-US/docs/Web/CSS/background-clip)。默认下，是用的border-box。**其实这里只是简单的裁剪。**

* border-box: 背景延伸到边框的外边缘。(但z-index排序在边框下)
* padding-box: 在边框下方没有绘制背景。(背景延伸到填充的外边缘)
* content-box: 背景画在内容框(content)内。

### Background origin

background-origin这个属性定义了，背景图是相对于什么哪个位置来定位的。它有三个值：

* content-box: 背景图像相对于内容来定位
* border-box: 背景图像相对于边框来定位
* padding-box: 背景图像相对于内边距来定位

### Outline

和border差不多，但是它不是box model的一部分。

## Advanced box properties

### Setting width and height constraints(约束)

一个明显的用途是，如果你想允许一个布局的宽度是灵活的，通过设置它的外部容器的宽度为一个百分比，但你也不希望它变得太宽或太窄，因为布局会开始看起来不好。 这里的一个选择是使用响应式网页设计技术（我们将在后面介绍），但更简单的方法可能是给布局一个最大和最小宽度约束：

```
body {
	width: 70%;
	max-width: 1280px;
	min-width: 480px;
	margin: 0 auto;
}
```

0 causes the top and bottom margins to be 0, while auto (in this case) shares the available space between the left and right margins of the container, centering it. The end result is that when the container is within the min and max width constraints, it will fill the entire viewport width. When 1280px width is exceeded, the layout will stay at 1280px wide, and start to be centered inside the available space. When the width goes below 480px, the viewport will become smaller than the container, and you'll have to scroll to see it all.

0使顶部和底部边距为0，而auto（在这种情况下）共享容器的左右边距之间的可用空间，以此为中心。 最终结果是，当容器在最小和最大宽度约束内时，它将填充整个视口宽度。 当超过1280像素宽度时，布局将保持在1280像素宽，并开始在可用空间内居中。 当宽度低于480像素时，视口将变得小于容器，您必须滚动才能看到它。

我理解的，准确来说，当超过1280px时候，就保持1280px，在480到1280px之间，就会保持宽度70%，如果小于480px时，视口会变得小于容器。必须滚动才能看到它。


Another good use of max-width is to keep media(e.g. images and video) constrained inside a container. For example:

```
img {
	display: block;
	margin: 0 auto;
	width: 100%;
}
```

### Changing the box model completely 

box-sizing属性也有三个值

* content-box(default): 设置width为500px，border和padding依然会增加盒子的宽度。(padding和border计入width内。)
* border-box: 设置width为500px，border和padding不会增加盒子的宽度。(padding和border不计入width内。)
* padding-box: 设置width为500px，padding计入width内，但是border不会计入width。这个值只有firefox实现了，但是firefox 50就删除了它。


## Box display types

### Common display types

display有很多个值，但是最常见的3个还是: block、inline、inline-block

* block: 单独为一行，可以设置高度和宽度。
* inline：它于与文档的文本一起流动(即它和周围文本以及其他inline元素在一行，**其内容与文本流断开(试一试padding，它并不会影响下一行的文本。)**)。 不能设置高度和宽度。任何inline元素上的margin、border、padding都会更新周围的(左右的，并不是上下的)文本位置，但是不会影响周围block元素的位置。
* inline-block:它和周围的文本会在一行，并且也可以设置高度和宽度。它不会在段落行之间被破坏(也就是说inline-block的文本不会跨越两行。)

For example:

```
HTML:

<p>
   Lorem ipsum dolor sit amet, consectetur adipiscing elit.
   <span class="inline">Mauris tempus turpis id ante mollis dignissim.</span>
   Nam sed dolor non tortor lacinia lobortis id dapibus nunc.
</p>

<p>
  Lorem ipsum dolor sit amet, consectetur adipiscing elit.
  <span class="block">Mauris tempus turpis id ante mollis dignissim.</span>
  Nam sed dolor non tortor lacinia lobortis id dapibus nunc.
</p>

<p>
  Lorem ipsum dolor sit amet, consectetur adipiscing elit.
  <span class="inline-block">Mauris tempus turpis id ante mollis dignissim.</span>
  Nam sed dolor non tortor lacinia lobortis id dapibus nunc.
</p>


CSS:

p {
  padding : 1em;
  border  : 1px solid black;
}

span {
  padding : 0.5em;
  border  : 1px solid green;

  /* That makes the box visible, regardless of its type */
  background-color: yellow;
}

.inline       { display: inline;       }
.block        { display: block;        }
.inline-block { display: inline-block; }
```

### Uncommon display types

* table
* flex: [look at this](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox)
* grid: [look at this](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Grids)

# Backgrounds

## What exactly is a background?

There are many other different properties you can use to manipulate the element's background, some of which we have already seen a number of times in our course already:

* background-color
* background-image
* background-position
* background-repeat
* background-attachment: Specifies the behavior of an element's background when its content scrolls, e.g. does it scroll with the content, or is it fixed?
* background: Shorthand for specifying 	the above five properties on one line.
* background-size: Allows background image to be resized dynamically.

## The basis: color, image, position, repeat

### Background color

几乎所有地方斗支持背景颜色，而且背景渐变只有新浏览器才支持，因此设置一个背景颜色是非常有必要的有时候。

### Background image

我觉得没有什么多说的。

```
p {
	background-image: url(/example.png);
}
```

### Background repeat

常见的几个值：

* no-repeat
* repeat-x
* repeat-y

### Background position

Generally the property will take two values, seperated by a space, which specify the horizontal(x) and vertical(y) coordinates of the image --- the top left corner of the image, to be exact. Think of the background as a graph, with the x coordinate going across from left to right, and the y coordinate going from top to bottom.

背景位置允许我们将背景图像放置在我们想要的背景内。 通常，属性将采用两个值，这两个值由空格分隔，这两个值指定图像的水平（x）和垂直（y）坐标，即**图像的左上角**。 将背景视为图形，x坐标从左到右穿过，y坐标从上到下。

这个属性可以接受很多值，你会用到的大多数值有：

* 绝对单位，比如px。background-position: 200px 25px.
* 相对单位，比如rems。background-position: 20rem 2.5rem.
* 百分比，background-position: 90% 25%.
* 关键字，比如background: right center。这两个值是直视的，并且可以分别取左、中、右和上、中、下的值。

你应该注意，可以混合和匹配这些值，比如background-position: 99% center。还要注意，如果只指定一个值，那么该值将被假定为水平值，垂直值将默认为center.

## Background image: gradients

一个linear gradient通过linear-gradient()创建，它作为background-image属性的值。至少，函数需要3个值，它们由逗号分开。分别是：渐变应该跨越背景的方向，开始处的颜色，结束处的颜色。举例来说：

```
background-image: linear-gradient(to bottom, origin, yellow); 
```

可以使用关键字来表示方向，比如(to bottom, to right, to bottom right, .etc)或者用角度来表示(0度表示to top, 90度表示to right, 360度表示to top.)

还可以指定开始渐变的位置，比如说：

```
background-image: linear-gradient(to bottom, origin 40%, yellow);
```

它将从高度为40%的地方才开始渐变。而高度40%之前是origin颜色。

还可以指定多个值(除了是百分比之外，还可以是px阿等等其他的), 比如说

```
background-image: linear-gradient(to bottom, yellow, origin 40%, yellow);
```

当然，我们还可以使用repeating-linear-gradient()还设置重复线性渐变。下面的例子，每40px(必须指定停止点)后就会重复渐变。

```
background-image: repeating-linear-gradient(top bottom, yellow, origin 40px); 
```

## Background attachment

> [Look at this](http://www.cnblogs.com/starof/p/4511367.html)

这个属性是用于指定当内容滚动时它们如何滚动，它有以下的值：

* scroll(也是默认属性): 表示会随着页面滚动而滚动(可以想象成它被附加到元素的边框上), 如果你滚动的是元素（上面有背景图片）,那么背景不会滚动。
* fixed：它表示不会随着页面的滚动而滚动，无论滚动的是页面还是拥有背景图片的元素。
* local：它只支持IE9+ 以及其他主流浏览器。它将背景固定到它所设置的元素，因此当滚动元素时，background会随着滚动。

## Background shorthand

For example:

```
background: yellow linear-gradient(to bottom, yellow, origin) no-repeat left center scroll;
```

如果有的属性没有被设置值，那么[默认的值](https://developer.mozilla.org/en-US/docs/Web/CSS/background)会被设置。

## Multiple backgrounds

IE9后，可以给一个元素添加多个背景图片。用逗号分割开。它们的最终效果是这样的：最前面的值在最上面，然后依次在下面，这里需要注意。

```
p {
	background-color: orange; // 请务必要写，因为有时候可能会加载不出来图片或者低版本不支持background-image。
    background: url(https://mdn.mozillademos.org/files/13026/fire-ball-icon.png) no-repeat 99% center,
              linear-gradient(to bottom, yellow, #dddd00 50%, orange);
}
```

## Background size

IE9以下不支持哟。每个background-size，需要两个background-size的值，一个是水平方向上的，另一个是垂直方向上的. 同样，px,em,rem,percentages,keywords等都支持。如果只设置了一个值，那么第二个值会被设置为auto。

[background-size](https://developer.mozilla.org/en-US/docs/Web/CSS/background-size)

简单总结一下：

* percentages: 以父元素的百分比来设置图像的高度和宽度。
* length
* keywords
	* contain: 根据一定的比例将图片放大(图片的高宽比不边)，但是会有留白区域。
	* cover: 根据一定的比例将图片放大，会铺满容器的高宽，而图片多出来的部分会被裁掉。

# Borders

## Border radius

For example:

```
// 第一个值代表左上角和右下角；第二个值代表右上角和左下角
border-radius: 20px 10px;

// 第一个值代表左上角，第二个值代表右上角和左下角，第三个值代表右下角。
border-radius: 20px 10px 50px;

// 第一个值：左上角，第二个值：右上角，第三个值：右下角，第四个值：左下角
border-radius: 20px 30px 40px 50px;
```

当然，也可以创建椭圆形角(其中x半径不同于y半径), 两个不同的半径由'/'分割指定。

```
// 第一个值是x半径，第二个值是y半径。
border-radius: 100px / 50px;

// 前两个值表示x半径(其中第一个值表示左上角和右下角的，第二个值表示右上角和左下角的), 后两个表示y半径。
border-radius: 100px 50px / 50px 25px;
```

## Border images

属性太多内容，[MDN-border-image](https://developer.mozilla.org/en-US/docs/Web/CSS/border-image)

IE11+ 以及其他现代浏览器支持。

是将一张图，分成9部分，除去中间的一部分，来将其他8部分作为盒子的边框。因此就会用到background-clip: padding-box;来将border部分的背景(主要是背景颜色)给裁掉。

```
border: 20px solid black; // 低版本浏览器
border-clip: padding-box; // 上面介绍了主要是为了裁掉背景颜色
border-image-source: url(https://mdn.mozillademos.org/files/13060/border-image.png); // 设置边框背景图片
border-image-slice: 40; // 以40(可能是像素，可能是坐标，根据是否是矢量图来判断的)。指定切片的大小是40.如果是一个值，则应用四个角
border-image-repeat: round; // 设置可以重复
```

**border-image-slice** 可以有多个值，它和padding与margin差不多：

* Two values: top and bottom, left and right;
* Three values: top, left and right, bottom;
* Four values: top, right, bottom, left;

# StylingTables

## A typical HTML table

```
<table summary="This is a test table">
	<caption></caption>
	<thead>
		<tr>
			<th scope="col"></th>
			<th scope="col"></th>
			<th scope="col"></th>
			<th scope="col"></th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td scope="row"></td>
			<td></td>
			<td></td>
			<td></td>
		</tr>
	</tbody>
	<tfoot>
		<tr>
			<td scope="row" colspan="2"></td>
			<td></td>
		</tr>
	</tfoot>
</table>
```

简单的说明：

* summary属性规定表格内容的摘要，不会对浏览器产生视觉效果。但是有利于屏幕阅读器
* scope属性定义将表头单元和数据单元相关联，有利于屏幕阅读器。 
	* col: 规定单元格是列的表头
	* row：规定单元格是行的表头
	* colgroup：规定单元格是列组的表头
	* rowgroup:规定单元格是行组的表头

## Active learning: Styling our table

### Spacing and layout

The first thing we need to is sort out the spacing/layout --- default table styling is so cramped(约束的，狭窄的)!To do this, add the following CSS to your style.css file:

```
/* spacing */

table {
	table-layout: fixed;
	width: 100%;
	border-collpase: collapse;
	border: 3px solid red;
}

thead th:nth-child(1) {
    width: 30%;
}

thead th:nth-child(2) {
    width: 20%;
}

thead th:nth-child(3) {
    width: 15%;
}

thead th:nth-child(4) {
    width: 35%;
}

th, td {
    padding: 20px;
}
```

最重要的部分注意如下：

* 设置table-layout的值为fixed通常对于你的table是很不错的选择。因为它比默认的table更可预见。通常，表列的大小设置它由它们包含多大的内容而决定，这可能会导致奇怪的结果。**设置table-layout:fixed, 你可以根据它们的heading来设置列的宽度**, 然后适当的处理它们的内容。这就是为什么我们选择了四个不同的heading的th，并且给它们设置了一定的宽度(百分比)。整个列的宽度都会根据heading的宽度来设置，这是一个设置表格列的很好的一种方法。[Fixed Table Layouts](https://css-tricks.com/fixing-tables-long-strings/)
* 我们设置了它的宽度为100%, 意味着可以很好的响应式设计(虽然还需要一些工作来让它在窄屏幕上看起来更好。)
* 设置border-collapse的值为collapse是任何表格样式工作的标准最佳做法。默认情况下，当你在表格元素上设置边框时，它们会有默认的间距，这看起来不太好看。而设置border-collapse的值为collapse，这样看起来就会好很多。
* 为table设置了边框的。
* 为th、td设置了padding。

### Some simple typography(排版)

```
/* typography */
html {
    font-family: 'helvetica neue', helvetica, arial, sans-serif;
}

thead th, tfoot th {
    font-family: 'Rock Salt', cursive;
}

th {
    letter-spacing: 2px;
}

td {
    letter-spacing: 1px;
}

tbody td {
    text-align: center;
}

tfoot th {
    text-align: right;
}
```

### Graphics and colors

```
thead, tfoot {
    background: url(leopardskin.jpg);
    color: white;
    text-shadow: 1px 1px 1px black;
}

thead th, tfoot th, tfoot td {
    background: linear-gradient(to bottom, rgba(0,0,0,0.1), rgba(0,0,0,0.5));
    border: 3px solid purple;
}
```

#### Zebra striping(隔行变色)

```
tbody tr:nth-child(even) {
	background: red;
}

tbody tr:nth-child(odd) {
	background: green;
}

tbody {
	background: red; /*优雅降级*/
}
```

### Styling the caption

```
caption {
    font-family: 'Rock Salt', cursive;
    padding: 20px;
    font-style: italic;
    caption-side: bottom; /*设置位置在下面*/
    color: #666;
    text-align: right;
    letter-spacing: 1px;
}
```

# AdvancedBoxEffects

> This article acts as a box of tricks, providing an introduction to some of the advanced features available for styling boxes that don't fit into the other categories above — like box shadows, blend modes(混合模式) and filters.

## Box shadow

### A simple box shadow

```
/* HTML */
<article class="simple">
  <p><strong>Warning</strong>: The thermostat on the cosmic transcender has reached a critical level.</p>
</article>


/* CSS */
p {
  margin: 0;
}

article {
  max-width: 500px;
  padding: 10px;
  background-color: red;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0), rgba(0,0,0,0.25));
}  

.simple {
  box-shadow: 5px 5px 5px rgba(0,0,0,0.7);
}
```

box-shadow接受四个值，分别是水平偏移量，垂直偏移量，模糊半径，阴影颜色。

### Multiple box shadow

可以设置多个box shadow，用逗号隔开。

```
p {
  margin: 0;
}

article {
  max-width: 500px;
  padding: 10px;
  background-color: red;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0), rgba(0,0,0,0.25));
}  

.multiple {
  box-shadow: 1px 1px 1px black,
              2px 2px 1px black,
              3px 3px 1px red,
              4px 4px 1px red,
              5px 5px 1px black,
              6px 6px 1px black;
}
```

### Other box shadow features

不像text-shadow，box-shadow有一个inset关键字可以用。把这个属性放在开头，就可以让阴影成为内阴影,而不是外阴影。

```
button {
  width: 150px;
  font-size: 1.1rem;
  line-height: 2;
  border-radius: 10px;
  border: none;
  background-image: linear-gradient(to bottom right, #777, #ddd);
  box-shadow: 1px 1px 1px black,
              inset 2px 3px 5px rgba(0,0,0,0.3),
              inset -2px -3px 5px rgba(255,255,255,0.5);
}

button:focus, button:hover {
  background-image: linear-gradient(to bottom right, #888, #eee);
}

button:active {
  box-shadow: inset 2px 2px 1px black,
              inset 2px 3px 5px rgba(0,0,0,0.3),
              inset -2px -3px 5px rgba(255,255,255,0.5);
}
```

## Filters(滤镜)

CSS过滤器提供了将滤镜应用到元素的方式。有很多不同的选项可以使用，可以在[这里](https://developer.mozilla.org/en-US/docs/Web/CSS/filter)查看filter的用法。这篇文章，主要是为了介绍语法。

滤镜可以应用任何元素，一些滤镜选项可以和其他CSS功能非常类似的事情，比如drop-shadow(),box-shadow和text-shadow实现出来的效果差不多。其实filter的真正好处在于，they work on the exact shapes of the content inside the box(**它们处理框内的内容的确切形状。**), not just the box itself as one big chunk, which can look nicer, although it may not always be want you want.下面我们来举个例子:

```
/* HTML */
<p class="filter">filter</p>
<p class="box-shadow">box-shadow</p>


/* CSS */
p {
	margin: 1em auto;
	padding: 20px;
	width: 100px;
	border: 5px dashed red;
}


.filter {
	-webkit-filter: drop-shadow(5px 5px 5px rgba(0, 0, 0, 0.7));
	filter: drop-shadow(5px 5px 5px rgba(0, 0, 0, 0.7));
}

.box-shadow {
	box-shadow: 5px 5px 5px rgba(0, 0, 0, 0.7);
}

```

你会发现，滤镜drop-shadow阴影会让文本和边框都有阴影。box-shadow只是让边框有阴影。

一些注意事项:

* filter支持edge和很多现代浏览器，但是不包括所有IE浏览器。
* 你会看到一个带有-webkit-前缀的过滤器属性版本，有时由浏览器在最终确定其新功能的实现之前使用。Chrome/Safari/Opera支持-webkit-版本, 而EDGE和Firefox使用最终的非前缀版本。

## Blend modes(混合模式)

CSS混合模式允许我们在两个元素重叠时为 混合效果添加混合模式。每个像素显示的最终颜色将是原始像素颜色和下面图层中像素的组合。

这有两个属性在CSS中使用混合模式：background-blend-mode, 它将在一个元素上设置的多个背景图像和颜色混在一起，mix-blend-mode将元素与元素混合在一起，是重叠背景和内容的。

注意：混合模式是新特性，很多浏览器都不支持。