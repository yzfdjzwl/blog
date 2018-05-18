# 替换元素与非替换元素的区别

替换元素(replaced element)：所谓替换元素就是浏览器根据元素的标签和属性，来决定元素具体显示什么内容。比如说：img标签的src属性的值用来读取图片信息并且显示出来。又比如说，input标签的type属性决定是显示输入框还是单选按钮。html中的img、input、textarea、object、video、embed都是替换元素。

非替换元素(non-replaced element): 是指内容包含在文档里的元素。比如p、span等等。

## margin-top/margin-bottom与padding-top/padding-bottom

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
