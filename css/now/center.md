# 居中布局

## 1. 水平居中

### 1.1 inline or inline-*

* 可以在`block-level`的元素里水平居中`inline`的元素。
* 使用`text-align: center;`, 适用于`inline, inline-block, inline-table, inline-flex`等。

```html
<style>
.box {
  text-align: center;
  background: #E2E2E2;
}
</style>

<div class="box">
  <span>我是span1</span>
  <span>我是span3</span>
</div>
```


### 1.2 block元素

#### 1.2.1 元素宽度固定

* 保证block元素宽度是固定的。
* 使用`margin: 0 auto`来设置居中。

```html
<style>
.outer {
  background: #E2E2E2;
  padding: 10px;
}
.box {
  margin: 0 auto;
  width: 150px;
}
</style>

<div class="outer">
  <div class="box">我是一行特殊的文字</div>
</div>
```

#### 1.2.2 元素宽度不固定


## 垂直居中

## 水平垂直居中

## 参考

* https://css-tricks.com/centering-css-complete-guide/
