## 什么是跨域?

> [前端解决跨域的8种方案](http://blog.csdn.net/joyhen/article/details/21631833), 请看原文，不要往下看了, 辣眼睛。

## 同源策略

要求同一协议，同一主机，同一端口号。

## 解决跨域的方法

### 1 document.domain + iframe(只有当主域相同的情况下才可以使用)

```
// www.a.com/a.html
document.domain = 'a.com';
var ifr = document.createElement('script');
ifr.src = 'http://www.script.a.com/b.html';
ifr.display = 'block';
document.appendChild(ifr);

ifr.onload = function() {
    var doc = if.contentDocument || ifr.contentWindow.document;
    // 在这里操作doc，也就是b.html
    ifr.onload = null;
}
```

### 2 动态创建script

因为script标签不受同源策略。

### 3 location.hash + iframe

1. 请求域(a)里通过iframe的hash将参数传递到另一个域里(b)去。
2. 在另一个域(b)里如果检测到自己的hash变化，然后通过修改parent.location.hash将参数传递过去。
3. 由于不在同一个域，ie和chrome无法修改parent.location.hash, 所以需要设置一个中间代理层。
4. 于是，利用b域下的代理iframe, 设置iframe的src为a域(新的html页面c)，然后将参数带到hash上去。
5. 同时，在a域里，添加一个定时器，检测自己的hash值有否有变化，如果有，就获取hash值。
6. 在c的html里，由于parent.parent和自身属于同一个域，所以可以改变它的hash值, `parent.parent.location.hash = self.location.hash.substring(1)`

### 4 window.name + iframe

[参考](http://web.jobbole.com/82646/)

原理: window.name值可以在不同的页面(甚至是不同域名)加载后依然存在，并且可以支持非常长的name值(2MB)。

1. index.html里去请求服务器的数据。
2. 在index.html下面新建一个iframe，利用iframe的跨域能力，设置iframe的src为远程服务器的页面。
3. 然后在index.html里面读取该iframe的window.name值。
4. 但是，这里会报跨域错误，因为index.html和该页面的iframe框架的src不同源，也无法操作框架里的任何东西。
5. 因此，既然要同源，就换个src去指向，在index.html相同目录下新建一个proxy.html。
6. 在proxy.html里面，先设置它的src为远程服务器页面的地址, 一旦加载完成后，马上设置它的src为index.html。这样，就同源了。
7. 但是，这里还有个小问题要注意，因为马上设置src就表示又window.onload了，每次就重置src了，因此这里需要做一个简单的判断就可以了。

### 5 postMessage(html5 API)

```
// a.com/index.html

<iframe id="ifr" src="b.com/index.html"></iframe>
<script>
    window.onload = function() {
        var ifr = document.getElementById('ifr');
        var targetOrigin = 'b.com'; // 'b.com/index.html'效果一样
                                    // 'c.com'就不会执行postMessage了
        ifr.contentWindow.postMessage('I am here', targetOrigin);
    }
</script>

// b.com/index.html
<script>
    window.addEventListener('message', function(event) {
        if (event.origin === 'http://a.com') {
            console.log(event.data);
            console.log(event.source);
        }
    });
</script>

```

### 6 CORS(跨域资源共享)

[mdn: CORS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)

跨域资源共享标准新增了一组HTTP首部字段，允许服务器声明哪些哪些源站有权限访问哪些资源。

另外，规范要求，对那些可能对服务器产生副作用的HTTP请求方法(特别是GET以外的HTTP请求, 或者搭配某些MIME类型的POST请求)，请求必须首先使用OPTIONs方法发起一个预检请求(preflight request), 从而获知服务端是否允许该跨域请求。服务端允许后，才能发起实际的HTTP请求。在预检请求的返回中，服务器端也可以通知客户端，是否需要携带身份凭证(包括Cookie和HTTP认证相关数据)。

响应首部里: `Access-Control-Allow-Origin: *`,

### 7 JSONP

[参考](https://segmentfault.com/a/1190000007232373)

JSONP是JSON with Padding的简称。

1. 页面调用js文件不受跨域文件的影响，而且只要拥有src属性的标签，它们都有跨域 能力，比如script，img，iframe。
2. 可以在远程服务器设法将数据装入js格式的文件里, 以供客户端调用，实现跨域。
3. 一般格式为JSON格式。
4. 客户端调用JSON文件成功后，对其进行处理。
5. 为了客户端方便使用数据，逐渐形成了一种非正式传输协议，人们把它称为JSONP，该协议的一个要点就是允许用户传递一个callback参数给服务端，然后服务端返回数据时会将这个callback参数作为函数名来包裹住JSON数据，这样客户端就可以随意定制自己的函数来自动处理返回的数据了。

```
//
<script>
    var localHandler = function(data) {
        console.log('跨域的remote.js可以调用本函数，带来的数据是' + data.result);
    }
</script>

// remote.js
localHandler({
    "result": "我是远程js带来的数据"
});
```

如何让服务端知道客户端函数的名字呢？只要服务端提供的js脚本是动态生成的就可以了，调用者可以传递一个参数过去告诉服务端本地函数的名字，于是服务端就可以按照客户的需求来生成js脚本了。

```
var flightHandler = function(data) {
    console.log('票价:' + data.price + "元" + data.tickets + '张");
}
var url = 'http://example.com/jsonp/flight.aspx?code=CA1998&&callback=flightHandler';
var script = document.createElement('script');
script.setAttribute('src', url);
document.getElementsByTagName('head')[0].appendChild(script);
```

服务器端的flight.aspx生成了以下代码提供给页面：
```
flightHandler({
    "code": "CA1998",
    "price": 2000,
    "tickets": 5,
});
```

这里用伪代码简单说明一下服务端的实现：

```
string callback = request.get("callback");
string json = "...";

response.setHeader('contentType', 'application/json');
response.write(callback + "(" + json + ")");
```

### 8 websockets

同源策略对websockets不适用。

websockets是一种浏览器的api，它的目标是一个单独的持久连接上提供全双工、双向通信。

原理: 在js创建了websocket后，会有一个http请求发送到服务器端。取得服务器响应后，建立的连接会使用http升级从http协议交换为web socket协议。
