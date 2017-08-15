> google: eslint+rules

### es6: impot

```
When you import the module's default, don't use brace {}
```

意思是，当你使用默认的模块时，不要加`{}`。如果不是默认模块的话，请把{}加上。

### 定义后不使用

### 对象的最后一个属性后面要加逗号

### 不得重复定义

### 缩进

### constructor必须有super

### constructor必须有意义，就是有其他代码

###  if 后要有括号

### no-trailing-spaces

最后面不能有额外的空格

### 关闭的标签必须对齐

```
The closing bracket must be aligned with the line containing the opening tag.

// like this
<Icon 
/>
```

### 不得在refs里使用字符串

```
using string literals in ref attribute is deprecated.

// so should use like this:
<input 
  type="text"
  ref={(c) => {
    this.input = c;
  }}
/>

handleClick(e) {
  const node = this.input;
  const value = node.value;
}

unexpected parentheses around single function argument having a body with no 
```

### 单个函数参数周围的意外括号具有一个没有花括号的主体的箭头

```
unexpected parentheses around single function argument having a body with no curly braces arrow-parens
```

### 箭头函数不应该返回赋值

```
arrow function should not return assignment.
```

### 箭头函数参数周围的预期括号具有带大括号的主体

```
expected parenteses around arrow function argument having a body with curly braces arrow-parens
```

### jsx中等号前面没有空格

```
there should ne no space before '='
```

### 没有交互的元素不允许有事件

```
visible, non-interactive elements should not have moue or keyboard listeners jsx-ally/no-static-element-interations

common interactive roles include:

1. button
2. link
3. checkbox
4. menuitem
5. menuitemcheckbox
6. menuitemradio
7. option
8. radio
9. searchbox
10. switch
11. textbox
```