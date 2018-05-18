# 浏览器缓存机制

<!-- vim-markdown-toc GFM -->
* [说明](#说明)
* [很古老的缓存](#很古老的缓存)
  * [pragma](#pragma)
  * [expires](#expires)
* [cache-control](#cache-control)
  * [作为请求首部时](#作为请求首部时)
  * [作为响应首部时](#作为响应首部时)
* [缓存校验字段](#缓存校验字段)
  * [Last-Modified](#last-modified)
  * [ETag](#etag)
    * [If-None-Match: ETag-value](#if-none-match-etag-value)
    * [If-Match: ETag-value](#if-match-etag-value)

<!-- vim-markdown-toc -->
## 说明

本文的内容为他人的博客摘抄，谢谢各位大佬。为什么要抄别人的博客呢？为了加深自己的印象。如果你不小心点到这篇文章，请直接跳过本文，直接点击[浅谈浏览器http的缓存机制](http://www.cnblogs.com/vajoy/p/5341664.html)。

1. [浅谈浏览器http的缓存机制](http://www.cnblogs.com/vajoy/p/5341664.html)
2. [Http协议头信息](http://www.studyofnet.com/news/1352.html)

## 很古老的缓存

在HTTP1.0时代，使用"Pragma"和"Expires"来规范，虽然它们早就被抛弃了，但是为了做兼容，很多网站上还是会带上这两个字段。

### pragma

当该字段的值为'no-cache'的时候，会通知客户端不要对该资源读缓存，即每次都要向服务器发送请求。

1. 只有ie才识别这个字段，其他浏览器仅能识别"Cache-control: no-store"的meta标签。
2. ie识别后，并不一定会在请求字段上加上'pragma'， 但是会每次都发新请求。

### expires

Expires是用来启用缓存和定义缓存时间。

1. Expires有一个值是格林尼治时间，来告诉浏览器缓存过期时间。
2. 响应报文定义的缓存时间是相对于服务器的，因此如果客户端的时间和服务器的时间不一样，那缓存的时间就没有多大意义了。
3. Pragma的优先级高于Expires

## cache-control

由于Expires时间是相对服务器出现，因此http1.1新增了cache-control来定义缓存过期时间，如果同时出现pragma, expires, cache-control, 那么会以cache-control为准。

cache是一个通用首部，因此可以在请求体用，也可以在响应体用。

### 作为请求首部时

1. no-cache, 告知服务器不直接使用缓存，要求向原服务器发送请求。
2. no-store, 所有内容都不会被保存到缓存或internet临时文件。
3. max-age=delta-seconds, 告知服务器，客户端希望接收一个存在时间(Age)不大于delta-seconds秒的资源。即缓存的内容将在delta-seconds秒后失效，这个选项只在HTTP1.1可用，并如果与Last-Modified一起使用时，优先级较高。
4. max-stale=s, 在s秒内，缓存可以过期。即客户端可以接收过期响应消息，如果指定max-stale的值，则客户端可以接受过期但在指定时间内的值。
5. no-transform, 客户端希望实体数据没有被转换(比如压缩)过的资源。
6. only-if-cached, 告知服务器浏览器希望获取缓存中的内容（若有）, 则不用向原服务器请求。

### 作为响应首部时

1. public, 表示任何情况都要缓存该资源。
2. Private[="field-name"], 表示返回报文中的全部数据或部分数据（field-name）仅开放给某些用户做缓存使用，其他用户则不能缓存这部分数据。
3. no-cache
4. no-store
5. no-transform
6. only-if-cached
7. must-revalidate, 当前资源一定是向原服务器发去验证请求的，若请求失败，则返回504(而非代理服务器上的缓存)。
8. max-age=delta-seconds

举例:

```
// 前者防止ie中点击back按钮从缓存中加载，后者防止其他浏览器点击back按钮从缓存中加载
Cache-control: no-cache, no-store
```

## 缓存校验字段

上面的字段均能让客户端决定是否向服务器发送请求，比如设置的缓存时间没有过期，那么自然从本地缓存中读取数据数据即可(在chrome中表现为200 from cache), 若缓存时间过期了或者资源不走缓存，则会发送请求到服务器上去。

那么， 如果客户端发送了请求，那么是否意味着一定要读取回该资源的整个实体内容呢？当然不一定，因为可能服务器的资源也根本没有更新过。http1.1新增了几个首部字段用于: 实现客户端与服务端之间缓存文件是否更新的验证，提升缓存的利用率。

### Last-Modified

服务器将资源传递给客户端时，会将资源最后更改的时间以"Last-Modified: GMT"的形式加在实体首部上一起返回给客户端。

客户端会为资源标记上该信息，下次请求时，会把该信息附带在请求报文中一并带给服务器去做检查，若传递的时间值与服务器上该资源最终修改时间是一致的，则说明该资源没有被修改过，直接返回304状态码即可。

至于传递标记起来的最终修改时间的氢气报文首都字段一共有2个:

1. If-Modified-Since: Last-Modified-value

该请求首部告诉服务器如果客户端传过来的最后修改时间与服务器一致，则直接返回304和响应报头即可。

当前各浏览器均是使用的该请求首部来向服务器传送保存的Last-Modified值。

2. If-Unmodified-Since: Last-Modified-value

告诉服务器，若Last-Modified没有匹配上，则应当返回412状态码给客户端。

Last-Modified也不是特别好，因为如果在服务器上，一个资源被修改了，单其实际内容根本没有发生过改变，会因为Last-Modified时间匹配不上而返回了整个实体给客户端(即使客户端缓存里有一个一模一样的资源)。

### ETag

为了解决上诉Last-Modified可能存在的不准确的问题，Http1.1还推出了ETag实体首部字段。

服务器会通过某种算法， 给资源计算出一个唯一标志符，比如md5标志，在把资源响应给客户端的时候，会在实体首部加上"ETag: 唯一标志符"一起返回给客户端。

同样，客户端会保存该ETag字段，并在下一次请求时候将其一并带过去给服务器。服务器只需要比较客户端传过来的ETag和自己服务器上该资源的ETag是否一致，那么就能很好地判断资源相对客户端而言是否被修改过了。

如果服务器发现ETag匹配不上，那么直接以常规GET 200回包形式将新的资源(当然也包括新的ETag)发送给客户端；如果ETag是一致的，则直接返回304通知客户端直接使用本地缓存。

那么客户端是如何把标记在资源上的ETag传去给服务器的呢？请求报文中有两个首部字段可以带上ETag值:

#### If-None-Match: ETag-value

告诉服务器端如果ETag没有匹配上需求重新发资源数据，否则直接回送304和响应报头即可。当前各浏览器均是使用的该请求首部来向服务器传递保存的ETag值。

#### If-Match: ETag-value

告诉服务器如果没有匹配到ETag，或者收到了"*"值而当前并没有该资源实体，则应当返回412状态码给客户端。否则服务器直接忽略该字段。

If-Match的一个应用场景是，客户端走PUT方法向服务器请求上传／更替资源，这时候可以通过If-Match传递资源的ETag。

如果Last-Modified和ETag同时被使用，则要求它们的验证都必须通过才会返回304，若其中某个验证没有通过，则服务器会按常规返回资源实体及200状态码。
