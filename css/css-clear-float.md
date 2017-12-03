一直以来，被清除浮动给困惑了许久，终于，今天还是要搞懂它，只能算是自己简单的弄懂。
下面引用了别人文章里的一些知识才弄懂。

> http://www.cnblogs.com/dolphinX/p/3508869.html

问题来了，为什么要清除浮动?
当内层元素浮动后，父级元素会出现下面几种情况:

1. 背景不能显示
2. 边框不能撑开
3. margin和padding值不能正确显示

所以我们要清除浮动，清除浮动一般有两种方式，一种是利用clear属性清除浮动，另一种是让父容器形成BFC。 
那么问题来了，什么是BFC?


### Formatting context

Formatting context 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，
以及和其他元素的关系和相互作用。最常见的 Formatting context 有 Block fomatting context (简称BFC)和 Inline formatting context (简称IFC). 若要详细了解BFC，可以看下面这篇文章:

> http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html 

#### 对父级设置一定的高度

#### 对父级添加overflow:hidden

这样做的原理是让父级紧贴内容。

### 利用clear属性清除浮动
clear是什么呢？之前自己一直没搞懂大概是个怎么回事，今天终于弄懂个大概了。
clear属性规定元素的哪一侧不允许其它**之前**浮动元素

例子:

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Clear float</title>
    <style type="text/css">
        .container{
            margin: 30px auto;
            width:600px;
            height: 300px;
        }
        .p{
            border:solid 3px #a33;
        }
        .c{
            width: 100px;
            height: 100px;
            background: lightgreen;
            margin: 10px;
            float: left;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="p">
            <div class="c">1</div>
            <div class="c" style="clear: left;">2</div>
            <div class="c">3</div>
        </div>
    </div>
</body>
</html>
```

解释:
这里对第二个div设置了样式clear:left,它规定了，自己的左侧元素禁止浮动，所以第一个div就不是左浮动了，于是出现了下面的这种情况

![](http://oc1qt6u5g.bkt.clouddn.com/float1.png)

本身是3个div都是左浮动的，本应该是显示一行的，但是第二个div设置了样式clear:left,所以"第一个元素不会浮动了，而第二个第三个div还是左浮动，就会显示的如上图一样。"

上面引用一位大神的文章，文章里这样说道：
> clear:left属性只是消除了其左侧div浮动对它自己造成的影响，而不会改变左侧div甚至于父容器的表现，在父容器看来，三个div还都是浮动的，所以高度依然会塌陷。但是如果我们在最后添加一个非浮动的div，由于它有clear:left属性，所以它会按照左侧div不浮动来定位自己，也就是定位到下一行，而父容器看到有一个非浮动、普通流的子元素元素，会将其包围，这样造成了顺便也把3个浮动元素也包裹起来的效果。高度不再塌陷。


#### 在结尾处增加空div标签

这就是上面说的clear:both，但是这种方法并不推荐使用。

#### 父级定义伪类（强烈推荐）

```css
.clear {zoom:1;} (IE6,7)
.clear:after {
	content: "";
	display:table;
	clear:both;
}
// IE8和非IE使用的。
// :after是在元素内部插入，原理和在结尾处插入一个空div标签，display：table的作用是形成BFC
```

### 结尾

清除浮动有多种方式，这里只是简答的介绍几种而已。欢迎随意转载~~