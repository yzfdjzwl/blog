[本文为阅读MDN文档笔记](https://developer.mozilla.org/zh-CN/)

# 目录

<!-- vim-markdown-toc GFM -->
* [Different types of Selectors](#different-types-of-selectors)
	* [Attribute Selectors](#attribute-selectors)
		* [Presence and value attribute selectors](#presence-and-value-attribute-selectors)
		* [Substring and value attribute selectors](#substring-and-value-attribute-selectors)
	* [Pseudo-selectors](#pseudo-selectors)
		* [Pseudo-classes](#pseudo-classes)
		* [Pseudo-elements](#pseudo-elements)
	* [Combinators](#combinators)

<!-- vim-markdown-toc -->

# Different types of Selectors

1. Simple selectors: Match one or more elements based on element type, class or id(还有通用选择器).

2. Attribute selectors: Match one or more elements based on their attributes/attribute values.

3. Pseudo-classes(伪类): Match one or more elements that exist in a certain state, such as an element that is being hovered over by the mouse pointer, or a checkbox that is currently disabled or checkd, or an element that is the first child of its parent in the DOM tree.

4. Preudo-elements(伪元素): Match one or more parts of content that are in a certain position in relation to an element, for example the first word of each paragraph, or generated content appearing just before an element.(for example: :first-letter, :first-line, :before, :after)

5. Combinators(关系选择器): These are not exactly selectors themselves, but ways of combining two or more selectors in useful ways for very specific selections.So for example, you could select only paragraphs that are direct descendants of divs, or paragraphs that come directly after headings.

6. Multiple selectors(多重选择器): Again, these are not separate selectors;the idea is that you can put multiple selectors on the same CSS rule, separated by commas(逗号), to apply a single set of declarations to all the elements selectd by those seletors.


## Attribute Selectors

Attribute selectors can ben divided into two categories depending on the way they match attribute values: Presence value attribute selectors and Substring value attribute selectors.

### Presence and value attribute selectors

These attribute selectors try to match an exact attribute value:

	1. [attr]: This selector will select all elements with the attribute attr, whatever its value.

	2. [attr=val]: This selector will select all elements with the attribute attr, but only if its value is val.

	3. [attr~=val]: This selector will select all elements with the attribute attr, but only if the value val is one of a space-separated list of values contained in attr's value, for example a single class in a space-separated list of classes.

### Substring and value attribute selectors

Attribute selectors in this class are also known as "RegExp-like selectors", because they offer flexible matching in a similar fashion to regular expression(but to be clear, these selectors are not true regular expressions):

	1. [attr|=val]: This selector will select all elements with the attribute attr for which the value is exactly val or starts with val-.

	2. [attr^=val]: This selector will select all elements with the attribute attr for which the value starts with val.

	3. [attr$=val]: This selector will select all elements with the attribute attr for which the value ends with val.

	4. [attr*=val]: This selector will select all elements with the attribute attr for which the value contains the string val(unlike [attr~=val]), this selector doesn't treat spaces as value separators but as part of the attribute value.)

## Pseudo-selectors

Pseudo-slelectors, they don't select actual elements, but rather certain parts of elements, or elements only in certain contexts.They come in two main types --- pseudo-classes and pseudo-elements.

### Pseudo-classes

```
:active
:any
:checked
:default
:dir()
:disabled
:empty
:first
:first-child
:first-of-type
:fullscreen
:focus
:hover
:indeterminate
:in-range
:invalid
:lang()
:last-child
:last-of-type
:left
:link
:not()
:nth-child()
:nth-last-child()
:nth-last-of-type()
:nth-of-type()
:only-child
:only-of-type
:optional
:out-of-range
:read-only
:read-write
:required
:right
:root
:scope
:target
:valid
:visited
```


### Pseudo-elements

```
::after
::before
::first-letter
::first-line
::selection
::backdrop
```

## Combinators

We'll explore combinators and multiple selectors --- two ways of combining multiple selectors together for futher useful selection capabilities.

1. AB: Any element matching both A and B at the same time.

2. A B: Any element matching B is a descendant(子孙) of an element matching A(that is: a child, or a child of a child, etc.)

3. A > B: Any element matching B that is a direct child of an element matching A.

4. A + B: Any element matching B that is the next sibling of an element matching A(that is: the next child of the same parent.)

5. A ~ B: Any element matching B that is among the next sibling of an element matching A(that is: one of the next children of the same parent.)