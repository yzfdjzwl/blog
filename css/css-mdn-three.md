# 阅读MDN文档之基本盒模型(3)

<!-- vim-markdown-toc GFM -->
* [Box properties](#box-properties)
  * [Margin collapsing](#margin-collapsing)
    * [Adjacent siblings(相邻兄弟)](#adjacent-siblings相邻兄弟)
    * [Parent and first/last child](#parent-and-firstlast-child)
    * [Empty blocks](#empty-blocks)
* [Active learning: playing with boxes](#active-learning-playing-with-boxes)
* [Advanced box manipulation (先进的盒子操作)](#advanced-box-manipulation-先进的盒子操作)
  * [Overflow](#overflow)
  * [Background clip](#background-clip)
  * [Outline](#outline)
* [Types of CSS boxes](#types-of-css-boxes)

<!-- vim-markdown-toc -->

## Box properties

1. content

2. padding

3. border

4. margin

> Note: Margins have a specific behavior called margin collapsing: When two boxes touch against one other, the distance between is the value of the value of the largest of the two touching margins, and not their sum.

### Margin collapsing 

Margin collapsing occurs in three basic cases.

#### Adjacent siblings(相邻兄弟)
	
The margins of adjacent(相邻的) siblings(兄弟) are collapsed(except when the later sibling needs to be cleared past floats).For example:

```
<p>The bottom margin of this paragraph is collapsed...</p>
<p>...with the top margin of this paragraph.</p>
```

#### Parent and first/last child

If there is no border, padding, inline content, block_formatting_context(BFC) created or dearance(清除) to seperate the margin-top of a block from the margin-top of its first child block, or no border, padding, inline content, height, min-height, or max-height to seperate the margin-bottom of a block from the margin-bottom of its last child, then those margins collapse. The collapsed margin ends up outside the parent.

如果这里没有border, padding, 内联内容, BFC的创建或清除, 来将块的margin-top与其第一个子块的margin-top分开。或者没有border, padding, inline content, height, min-height, or max-height, 来将块的margin-bottom和最后一个子块的margin-bottom分开，那么这些margins会塌陷。塌陷的margin结束于父容器的外面。

#### Empty blocks

If there is no border, padding, inline content, height or min-height to sperate a block's margin-top from its margin-bottom, then its top and bottom margins collapse.

## Active learning: playing with boxes 

1. 如果设置padding或者margin的宽度是百分比的话, 是相对于父容器的宽度(只是cotent，不包括padding和margin)。

2. Borders ignore percentages width settings too, but you can use padding to simulate(模仿，假装).

3. You should have noticed that the total width of a box is the sum of its width, padding-left, padding-right, boder-left, border-right properties. In some cases it annoying  (for example, what if you want to have a box with a total width of 50% with border and padding expressed in pixels?) To avoid such problems, it's possible to tweak(用力拉) the box model with property box-sizing. With the value border-box, it changes the box model to this new one (width = content + border + padding; height = content + border + padding).

## Advanced box manipulation (先进的盒子操作)

### Overflow

When you set the size of a box with absolute value(e.g. a fixed pixel width/height), the content may not fit within the allowed size, in which case the content overflows the box. To control what happens in such cases, we can use the overflow property. It takes several possible values, but the most common are:

1. auto: If there is too much content, the overflowing content is hidden and scroll bars are shown to let the user scroll to see all the content.

2. hidden: If there is too much content, the overflowing content is hidden.

3. visible: If there is too much content, the overflowing content is shown outside of the box(this is usually the default behavior.)

### Background clip

Box backgrounds are made up of colors and images, stacked on top of each other(background-color, background-image.) They are applied to a box and drawn under that box.By default, backgrounds extend to the outer of the border.This is often fine, but in some cases it can be annoying (what if you have a tiled(平铺的) background image that you want to only extend to the edge of the content?) This behavior can be adjusted by setting background-clip property on the box.

1. border-box(this is default)

2. padding-box

3. content-box

### Outline

使用起来和border差不多，但是它不是盒模型的一部分。

## Types of CSS boxes

Everything we're said  so far applies to boxes that represented block level elements(块级元素). However, CSS has other types of boxes that behave differently. The type of box applied to an element is specified by the display property. There are many different values available for display, but in this article we will focus on the three most common ones ones;block, inline and inline-block.

* A block box is defined as a box that's stacked upon other boxes(i.e. content before and after the box appears on a seperate line), and can have width and height set on it. The whole box model as described above applies to block boxes.(块级元素是这样的一个盒子，它堆放在其他盒子上面(补充说明：内容前后都有一个空行，也就是说，block独占一行), 可以设置宽度和高度。上面说的整个盒模型都适用于块级元素。)
* An inline box is the opposite of a block box: it flows with the document's text(i.e. it will appear on the same line as surrounding text and other inline elements, and its content will break with the flow of the text, like lines of text in a paragraph.) Width and height settings have no effect on inline boxes;any padding, margin, border set on inline boxes will update the position of surrounding text, but will not affect the position of surrounding block boxes.(内联框与块框相反：它与文档的文本一起流动（即，它将与周围文本和其他内联元素显示在同一行上，其内容将与文本流断开，如文本行 在一个段落中。）宽度和高度设置对内联框没有影响; 在内嵌框中设置的任何填充，边距和边框将更新周围文本的位置，但不会影响周围块框的位置。
)
* An inline-block box is something in between the first two: it flows with surrounding text without creating line breaks before and after it like an inline box, but it can be sized using width and height and maintains its block integrity(完整) like a block box --- it won't be broken across paragraph lines.

> 更多的BFC、Visual formatting model
