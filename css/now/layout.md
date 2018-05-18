# CSS基本布局原理

## 布局元素

### 1. 内联(行内)元素

* 宽度、高度由子元素决定，不能手动修改高度和宽度，和其他内联元素在同一行。
* 内联元素会有`3像素问题`。

行内元素，分为替换元素以及非替换元素。这两个概念还是蛮奇怪的，因为这涉及到HTML、浏览器的发展历史。
* 非替换元素(non-replaced element): 指的是内容包含在文档里的元素，大部分的`inline box`都是非替换元素，比如`a`、`span`、`strong`、`b`等。
* 替换元素(replaced element): 所谓替换元素就是浏览器根据元素的标签和属性，来决定显示什么内容。比如`img`标签的`src`属性的值用来读取图片的信息并展示出来。又比如说，`input`标签的`type`属性来决定显示输入框还是按钮之类的。总结一下： `it is best to read "replaced" as "embedding"`

#### 1.1 非替换元素

非替换元素不能改变宽高，导致很多属性是不能使用的，比如`width`系列，`height`系列，`margin-top`, `margin-bottom`, `padding-top`, `padding-bottom`, `line-height`。
* 设置`width`, `height`没有作用。
* 设置`margin-top`, `margin-bottom`没有任何视觉效果。
* 设置`padding-top`, `padding-bottom`，设置背景颜色，可以看到有效果，但是对布局没有任何影响。

#### 1.2 替换元素

而替换元素的表现和块级元素差不多。
* 设置`width`, `height`有作用。
* 设置`margin-top`, `margin-bottom`有影响, 有作用。
* 设置`padding-top`, `padding-bottom`有影响, 有作用。

### 2. 块级元素

* 默认高度等于内容高度、宽度充满父级宽度、块级元素之间纵向排列。
* 如果块级元素不设置`float`或者`position`的话，那么块级元素将永远纵向排列, 不管宽度是多少。
* 在块级元素保持文档流的情况下，它的`width`, `height`默认是`auto`, 也就是说，宽度会永远充满容器宽度。 ***如果有东西挡住，那么文本流会默认往后移动，但是实际区域并没有边，只是被遮住了, 可以根据这个特性做图文排版。***
* 在块级元素保持文档流的情况下，它的`margin`会自动填充整个容器的宽度。


### 3. 内联块元素

* 宽度、高度由子元素决定，可以设置高度和宽度，和其他内联(块)元素在同一行。
* 同样存在`3像素`问题。

### 4. 浮动元素

* 浮动会让元素脱离文档流，其他元素将无视浮动元素，把浮动元素的位置给占了。
* 脱离文档流的元素层级比较高，会叠在上面。
* 浮动元素没有完全脱离文档里，因此可以通过设置浮动元素的父级元素的`class="clearfix"`，这样就可以将浮动元素拉回文档流里。

### 5. 定位元素

* `position: static`
* `position: absolute`
* `position: relative`
* `position: fixed`

## 参考

* https://segmentfault.com/a/1190000009139500#articleHeader2
* https://www.w3.org/TR/2011/REC-CSS2-20110607/conform.html#replaced-element
