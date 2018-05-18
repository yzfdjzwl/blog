# 阅读MDN文档之层叠与继承(2)

<!-- vim-markdown-toc GFM -->
* [The cascade](#the-cascade)
  * [Importance](#importance)
  * [Specificity](#specificity)
  * [Source order](#source-order)
  * [A note on rule mixing](#a-note-on-rule-mixing)
* [Inheritance](#inheritance)
  * [Controlling inheritance](#controlling-inheritance)

<!-- vim-markdown-toc -->

[CSS样式覆盖规则](http://blog.csdn.net/wl110231/article/details/7642652)

[CSS三大特性](http://bbs.520it.com/forum.php?mod=viewthread&tid=3106&extra=Array)

## The cascade

What selectors win out in the cascade depends on three factors(these are listed in order of weight---earlier ones will overrule later ones):

1. Importance

2. Specificity

3. Source order(资源顺序)

### Importance

In CSS, there is a special piece of syntax you can use to make sure that a certain rule will win over all others: <code>!important</code>. Adding this to the end of a property value will give it superowers.

Let's look at example: 

```html
<p class="better">This is a paragraph.</p>
<p class="better" id="winning">One selector to rule them all!</p>
```

```css
#winning {
	background-color: red;
	border: 1px solid black;
}

.better {
	background-color: gray;
	border: none !important;
}

p {
	background-color: blue;
	color: white;
	padding: 5px;
}
```

This produces the following:

This is a paragraph.(backgound: gray;color: white;padding: 5px;border: none;)
One selector to rule them all!(background: red;color: white;padding:5px;border: none;)


Let's walk through this to see what's happening.

1. You'll see that the third rule's color and padding values have been applied, but the background-color hasn't. why? Really all three should surely apply, because rules later in the source order generally override earlier rules.(因为资源顺序后面的规则通常会继承前面的规则。)

2. However, The rules above it win, because ID/class selectors have higher specificity than element selectors(You will learn way more about this in the next section.)(ID/class选择器比元素选择器有更高的特异性.)

3. Both elements have a class of better, but the second one has an id of winning too.Since IDs have an even higher specificity than classes(you can only have one ID on a page, but many classes --- ID selectors are very specific in what they target), the red background color and 1 pixel black border should both be applied to the second element, with first element getting the gray background color, and no border, as specified by the class.(ID选择器比class选择器有更高的特异性。)

4. The second element does get the red background color, but no border.Why? Because of the <code>!important</code> declaration in the second rule --- including this after border;none means that this declaration will win over the border value in the previous rule, even though the ID has high specifity.(即使ID有更高的特异性，但是因为!important的存在。!important赢了。)

> Note: The only way to override this !important declaration would be to include another !important declaration of the same specificity, later in the source order.

**We would advertise you to never use !important unless you absolutely have to.** 

Conflicting declarations will be applied in the following order, with later ones overriding earlier ones:

1. Declarations in user agent style sheets(e.g. the browser's default styles, used when no other styling is set.)(用户代理样式表声明, 比如浏览器默认样式)

2. Normal declarations in user style sheets(custom styles set by a user.) (用户样式表普通声明, 用户定制的样式)

3. Normal declarations in author style sheets(these are the styles set by us, the web developers!)(开发者的普通声明)

4. Important declarations in author style sheets(开发者的important声明)

5. Important declarations in user style sheets(用户的important声明)


### Specificity

The amount of specificity a selector has is based on something called the **selector weight**. This has four different values, which can be thought of as thousands, hundreds, tens and ones --- four single digits in four columns.The selector weight is calculated based on the selector types of its sub-parts:

1. Thousands: One if the matching selector is inside a <code>< style> </code> element or the declaration is inside a <code>style</code> attribute(such declarations do not have selectors, so their specificity is always simple 1000.) Otherwise 0.

2. Hundreds: One for each ID selector contained inside the overall(全部的) selector. 

3. Tens: One for each class selector, attribute selector or pseudo-class contained inside the overall selector.

4. Ones: one for each element selector and pseudo-element contained inside the overall selector.

> Note: Universal selector( * ), combinators(+, >, ~, '') and nagation pseudo-class(:not) have no effect on specificity.


### Source order

As mentioned above, if multiple competing selectors have the same importance and specificity, the third factor that comes into play to help decide which rule wins is source order ---late rules will win over earlier rules.

### A note on rule mixing

One thing you should bear in mind when considering all this cascade theroy, and what styles get applied over other styles, is that all this happens at the property level --- properties override other properties, but you don't get  entire rules overriding other rules. When several CSS rules match the same element, they are all applied to that element. Only after that are any conflicting properties evaluated to see which individual styles will win over others.(当几个CSS规则匹配同样的元素时，它们都将被用于元素。只有当它们是相互冲突的属性时，它们才会被判断出哪一个单独的样式会将其他的所有样式打败。)

## Inheritance

The idea is that some property values applied to an element will be inherited by that element's children, and some won't.

1. For example, it makes sense for font-family and color to be inherited.

2. As other example, it makes sense for margin, padding, border and background-image to NOT be inherited.


### Controlling inheritance

CSS provides three special values to handle inheritance.

1. inherite: This value sets the property value applied to a selected element to be the same as that of its parent element. 

2. initial: This value sets the property value applied to a selected element to be the same as the value set for that element in the browser's default style sheet. If no value is set by the browser's default style sheet and the property is naturally, then the property value is set to inherit instead.(属性被设置为浏览器的默认样式，如果浏览器没有默认样式，并且该属性是自然继承的，那么属性值将被设置为inherit)

3. unset: This value resets the property to its natural value, which means that if the property is natually inherited it acts like inherit, otherwise it acts like initial.(要么是inherite, 要么是initial)

> 引申：a元素为什么不继承父元素的字体颜色。
