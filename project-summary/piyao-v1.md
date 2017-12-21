# 目录

<!-- vim-markdown-toc GFM -->
* [当接手新项目时](#当接手新项目时)
* [关于`CSS`](#关于css)
    * [`translate3d`](#translate3d)
    * [`box-shadow`](#box-shadow)
* [关于`JavaScript`](#关于javascript)
* [关于`Django`](#关于django)
* [关于Linux命令](#关于linux命令)
* [关于微信分享踩坑](#关于微信分享踩坑)
* [很傻逼的坑](#很傻逼的坑)

<!-- vim-markdown-toc -->

## 当接手新项目时

1. 看项目的文档。
2. 看文档里写的如何将项目给跑通。
3. 有的时候写的如何跑通是针对了他个人习惯，因此一定要知道每条命令是什么意思，不然有的时候自己多加了个参数导致跑不起来之类的。
4. 看配置文件，如`.env`，`__setting__`或者还有其他的，带有这种字眼的，文件里面提示了前端的地址是多少，后端的地址是多少。以及端口号或者其他的一些参数配置，反正看配置文件是一个很重要的环节。

## 关于`CSS`

### `translate3d`

```css
transform: translate3d(x, y, z);
transform: translate3d(0, 1px, -2px); // 这样就有立体展示的效果了
```

### `box-shadow`

```css
box-shadow: <inset> x-offset y-offset <blur-radius> <spread-radius> <color>; // <>表示可选的, 模糊半径，伸展半径
// 将x-offset 和 y-offset的值设为0就是四周都有阴影
```

## 关于`JavaScript`

`console.log()`打印的是对象的指针，下面举个例子:

```js
const a = {};
console.log(a); // {b: 'test'}
a.b = 'test';
```

有这么一个需求，当获取到后台数据的时候，才往下执行。一般来说，这个就是JavaScript的异步编程, 但是自己在这里却卡住了。之前的代码如下: 

```js
// utils/RestClient.js
class RestClient {
  constructor() {
  }

  fire() {
  }
}

// actions
export function getSignaturePack(url) {
  return (dispatch) => {
    query: {
      url, 
    },
    onSuccess({ ret }) {
      dispatch({
        at.SET_SIGNATUREPACK,
        ret,
      });
    },
    onError(e) {
      console.log(e);
    }
  };
}

// NewsDetail.js
const { appId, timestamp, nonceStr, signature } = this.props.state.signPackage;
const url = encodeComponentURI(location.href);
```

我的想法是当获取到后台的数据后，就将数据存到`reducer`里面，然后在前端处理业务逻辑的时候，直接判断如果数据已经存在了，就往下执行，但是这样的话，如果数据不存在的话，我想要等待前端获取到数据后再执行，这明显就是异步编程嘛。可是我就是在这里卡住了。解决办法如下:

```js
// utils/RestClient.js
class RestClient {
  constructor() {
  }

  fire() {
  }
}

// actions
export function getSignaturePack(url, callback) {
  return (dispatch) => {
    query: {
      url, 
    },
    onSuccess({ ret }) {
      callback(ret);
    },
    onError(e) {
      console.log(e);
    }
  };
}

// NewsDetail.js
this.props.actions.getSignaturePack(url, (obj) => {
  const { appId, timestamp, nonceStr, signature } = this.props.state.signPackage;
  const url = encodeComponentURI(location.href);
  
  // 继续往下执行
});
```

这样，这个问题就解决了，因为在JavaScript中，函数是第一等公民，可以作为参数传递。

## 关于`Django`

1. 开启服务: `python manage.by runserver 8001`, 后面可以直接跟端口号或者IP地址。
2. 安装依赖: `pip install -r requirements.txt -i https://pypi.douban.com/simple`。
3. `python manage.py makemigrations`, 它的用法是，当你改变了或者新增了`modal`后，数据库需要进行变更，就要执行这条命令。
4. `python manage.py migrate`, 它的用法是，是你第一次新建表。
5. `Django`跨域解决，在`settings.py`里设置白名单。[参考这里](http://www.th7.cn/Program/Python/201606/872434.shtml)
6. 一般的`json`格式如下: `{'code': 1, 'ret': {'message': 'request error'}}`或者是`{'code': 0, 'ret': {'list': list}}`
7. `Django`生成用户: `python manage.by createsuperuser`
8. 别将`ascii`写错了，这是一个坑。。 

## 关于Linux命令

1. 设置所有权限: `chmod -R 777`。
2. 查看ip: `ip addr`

## 关于微信分享踩坑

因为用的是`react`, 在需要引入微信的sdk(`jWeixin`), 然后就想着`react`是组件写法，没办法直接引入，因此就只有在`componentWillMount`里面通过创建`script`的方式，然后使用`document.body.appendChild`来将微信的sdk引入，但是这样导致这么一个问题，就是使用到`jWeixin`对象的时候，`jWeixin`对象还没有完全引入。因此就需要这么写到:`window.onload = () => {}`, 总的来说，这不是一个好的办法。

后来发现，每次请求的都是一个新的静态页面(`index.html`)，在静态页面(`index.html`)里直接引入`<script src=""></script>`就可以了，当然需要将它放在`body`的顶部，因为第一避免`jWeixin`对象还没有完成引入，第二由于`script`是阻塞式的。

后台需要获取到当前页面的url, 我的办法是将前端的url获取后，将url作为参数传到后端去。值得注意的是，[微信公众平台](https://mp.weixin.qq.com/wiki)文档里的`微信网页开发 -> 微信JS-SDK说明文档 -> 附录5-常见错误及解决办法`, 有一段话是关于url的说明的(`确认url是页面完整的url(请在当前页面alert(location.href.split('#')[0])确认)，包括'http(s)://'部分，以及'？'后面的GET参数部分,但不包括'#'hash后面的部分。`), 这段话是有一定的问题的。

如果你的项目是采用的restful风格并且使用的是`react-router`, 你的url可能像这样`http://9ab13bb5.ngrok.io/#/news/index/4153?_k=6yx6me`, 你要尤其的注意，你的url就是整个url，而不是像文档里所说的那样。如果你报了这么一个错误`invalid signature`, 一定要多检查检查你的url是否是对的, 对测试测试。

另外: 当使用`location.href`获取到url后，记得一定对url进行编码(`encodeComponentURI(location.url)`)。

## 很傻逼的坑

[微信公众平台](https://mp.weixin.com/wiki)里面提到了通过`ready`接口处理成功验证是以下面的方式来调用的:

```js
wx.ready(function() {
});
```

但是由于`es-lint`不允许有匿名函数，所以我当时写成了下面这样:

```js
wx.ready = () => {
};
```

这真的是一个很傻逼的行为啊。。 因为`ready`的参数里是一个回调函数，我却把`ready`给重写了，所以说应该这么写:

```
wx.ready(() => {
});
```