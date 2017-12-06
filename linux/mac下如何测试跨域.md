## 本地服务器

1. mac下默认装了apache2
2. cd /etc/apache
3. vim httpd.conf
4. 在httpd.conf里可以配置端口号(line 52)和项目路径
5. httpd -k start(启动apache)
6. httpd -k stop(停止apache)
7. 修改了httpd.conf后记得`httpd -k restart`
8. 路径: `/Library/WebServer/Documents`


## 本地hosts映射

1. vim /etc/hosts
2. `127.0.0.1 www.example.com`
3. `127.0.0.1 www.test.example.com`

## 开始测试
