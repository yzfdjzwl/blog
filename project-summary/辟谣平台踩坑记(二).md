## 目录

* [es7中的async, await](#es7中的async-await)
* [Django生成二维码并转为base64](#django生成二维码并转为base64)
* [Django配置404页面](#django配置404页面)
* [很傻逼的坑](#很傻逼的坑)
    * [`no module named pil`](#no-module-named-pil)
* [其他](#其他)

## es7中的async, await

其他相关: Promise, generator

[请看这篇文章](https://segmentfault.com/a/1190000002566697)

注意: 在请求数据的时候，存在数据依赖的时候，要么使用链式方法，要么使用`async await`的方式，或者也许有其他更好的方法，但是一定要注意JavaScript是异步的。

## Django生成二维码并转为base64

```python
import qrcode
import base64
import image

from cStringIO import StringIO
def qrcode(request):
    response = {'code': 1, 'ret': {'message': 'request error'}}
    if request.method =! 'GET'
        return JsonResponse(response)
    try:
        url = request.GET.get('url', '')
        img = qocode.make(url)
        
        buf = StringIO()
        img.save(buf)

        img_stream = buf.getValue()
        img_64 = base64.b64encode(image_stream)
        response = {'code': 0, 'ret': {'qrcode': img_64}}
    except Exception, e:
        response['ret'] = {'message': str(e)}
    
    return JsonResponse(response)
```

## Django配置404页面

1. 设置`settings.py`里的`DEBUG`的值为`False`。如果你在本地测试，请检查你是否有本地的配置文件，如:`local_settings.py`之类的，请设置`DEBUG`的值为`False`，一定要检查好这一步，很重要。

2. 配置`settings.py`里的`TEMPLATES`以及`ALLOWED_HOSTS`, 这一步请参考网上其他文章。

3. 配置`urls.py`, 代码如下:

```
// urls.py
from apps import views

handler404 = views.page_not_found()

// views.py
from django.shortcuts import render_to_response

def page_not_found(request):
    return render_to_response('404.html')
```

## 很傻逼的坑

###  `no module named pil`

安装`qrcode image`的时候，是单独装的`pip install qrcode, pip install image`, 但是在跑生成二维码的时候却报了这样的错误: `No module named pil`。

去百度上搜了搜，现在一般都用`Pillow`, 但是我`pil pillow`都安装了，依然报这个错，各种方法都是过了，就是不行，最后无果。试了试先卸载再安装的方法: `pip uninstall qrcode, pip uninstall image`， 然后接着再安装`pip install qrcode image`，最后居然跑起来了，依然不知道是为什么。

## 其他

写代码，一定要考虑到抛出异常，一定一定！并且，一定要处理好细节的地方，这是非常重要的。