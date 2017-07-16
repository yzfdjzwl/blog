## Node.js的安装

[菜鸟教程](http://www.runoob.com/nodejs/nodejs-install-setup.html)
`npm -v`查看npm的版本。

## npm更新

[npm官网](https://docs.npmjs.com/getting-started/installing-node)

## npm权限问题

由于npm经常会因为权限问题，不能全局安装模块，所以解决办法如下：

[npm官网](https://docs.npmjs.com/getting-started/fixing-npm-permissions)

## npm切换淘宝源

由于npm国内访问速度特别慢，所以要切换淘宝的镜像，解决办法如下：

[淘宝镜像](https://npm.taobao.org)

Linux用户，注意使用su

## yarn的安装方法

[yarn官网](https://yarnpkg.com/en/docs/install)

```
yarn
yarn install

// 它们是等价的，意思是安装项目的全部依赖。

yarn start 

// 启动项目

// 遇到跨域问题
BACKEND_URL = 'http://localhost:8000' 

// NOT THIS:
BACKEND_URL = 'localhost:8000' 
```

## mongodb简单说明

1. centos用户如何安装：

[来自linuxidc.com](http://www.linuxidc.com/Linux/2016-06/132675.htm)

2. mongodb配置数据源，并开启服务, 由于最开始mongodb没有存数据的文件，类似指定数据库的位置

```
mongod --dbpath /srv/mongodb/
```

[官方文档](https://docs.mongodb.com/manual/tutorial/manage-mongodb-processes/)

3. mongodb简单操作

```
show dbs
use seed_crm
show tables
./mongod --dbpath pathName
```