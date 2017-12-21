## eslint

`Unexpected block statement surrounding arrow body`, 意思说是在箭头函数题周围有不被期望的块语句。下面看实例代码:

```
// Bad!
const Bxx = () => {
    return (
        <div>Hello World!</div>;
    );
};

// Good!
const Axx = () =>
    <div>Hello World!</div>;
```

像这种写法，十分适合抽象简单的独立组件。

## css

当想要实现文字两端对齐的时候，`text-align: justify`就很容易就可以搞定，但是在实践的过程中发现，`text-align: justify`它不能解决当文字只有一行的情况，因此当文字只有一行的时候，需要这样去解决: `text-align-last: justify`。

### 基线以及vertical-align详情

关于同行对齐，这一直是我个人的缺点，对此，我认为自己需要做下面两件事情:

1. 看相关的文章和博客。
2. 打开淘宝或其他电商网站，看其布局实现。

### 国际化时遇到的问题

本来，使用`text-align: justify`, 保证两端对齐是没有问题的， 但是发现当国际化成英文的时候问题就来了，单词内部字母间的距离大，这是由于一行单词太少的原因，解决办法有两个:

```
/* 1 */
word-break: break-all;

/* 2 */
word-spacing: -2px;
```

### 点击图片时不选中文字

当用CSS画一个图标时，由于图标内有问题，发现当自己点击的时候，会选中文字。显然，这并不是我所想要的，所以查了一下, 解决办法: `onselectstart="return false"`。
