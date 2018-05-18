# 从输入URL到页面渲染出来的浏览过程是怎么样的?

1. 输入URL
	浏览器从URL中解析出服务器的域名
2. DNS服务器解析URL，查找IP。
	* 如果有端口号的话，将端口号也解析出来
	* 先从浏览器缓存中查找
	* 再从系统系统中查找
	* 再从路由器缓存中查找
	* 递归查找...
3. 构建HTTP请求包(HTTP头，HTTP体)
4. 传输层发送TCP连接
5. 3次握手建立TCP连接成功后，发送TCP包
	* TCP包发送过程中的几个重要过程
	* 窗口滑动
	* 流量控制
	* 拥塞处理
6. TCP包在网络层被封装成IP包
7. IP包在数据链路层被封装成数据帧
8. 数据帧在物理层被转为比特流
9. 发送方这边就结束了, 接受方那变和这边差不多
10. 操作系统将阻塞的进程(HTTP服务器进程)唤醒，并且建立自己的子进程，子进程开始处理请求，而自己调用block原语阻塞自己。
11. 处理结束后，将找到的资源封装成HTTP响应包
12. 发送给客户端, 发送成功后，关闭连接
13. 客户端进行页面渲染
	* HTML解析器解析HTML，转换为DOM Tree
	* CSS解析器解析CSS，提取CSS Rules
	* 将HTML和CSS混合为一个attachment
	* attachment通过布局(layout)被转换为一个Render Tree
	* Render Tree通过绘制就显示页面了

## 参考链接

* [浏览器的工作原理](https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/)
