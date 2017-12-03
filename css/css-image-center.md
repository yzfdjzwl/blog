## 未知图片高度，图片需要垂直居中
在我们写css的时候，经常会遇到图片在一个盒子里面要求垂直居中，但是又不知道该图片的高度和宽度。下面介绍几种方法

### 方法一

方法一通过添加一个额外的标签，使img和额外的标签(或者用一个伪元素实现`:before`)垂直对齐，这样就可以实现垂直居中了，看代码:
```html
<style>
    .helper {
        display: inline-block;
        height: 100%;
        vertical-align: middle;
    }
    img {
        vertical-align: middle;
    }
    /* 或者使用伪元素 */
    .helper:before {
        content: '';
        height: 100%;
        vertical-align: middle;
        display: inline-block;
    }
</style>
<div class="out">
    <span class="helper"></span><img src="" />
</div>
```

### 方法二
使用定位的方式来设置，直接看代码:
```html
<style>
    .out {
        position: relative;
        height: 100px;
        width: 100px;
    }
    img {
        position: absolute;
        top: 0;
        bottom: 0;
        margin: auto;
    }
</style>
<div class="out">
    <img src="" />
</div>
```

### 方法三
使用transform来实现，具体看代码:

```html
<style>
    img {
        position: relative;
        top: 50%;
        transform: translate(-50%);
    }
</style>
<div class="out">
    <img src="">
</div>
```

### 方法四
使用`line-height`, 这个方法的原理我不知道是为啥，但确实是一个挺优美的方法:

```html
<style>
    .out {
        line-height: 150px;
    }
    .out img {
        vertical-align: center;
    }
</style>
```

### 方法五
使用`flex`布局，看代码：

```html
<style>
    .out {
        display: flex;
        /* if you want to horizentoala */
        /* justify-content: center */
        align-items: center;
    }
</style>
```


## 参考

* [How to vertically align an image inside a div?](https://stackoverflow.com/questions/7273338/how-to-vertically-align-an-image-inside-a-div)
* [Vertical centering using CSS](http://www.student.oulu.fi/~laurirai/www/css/middle/)
