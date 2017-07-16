## 目录

* [tmux](#tmux)
    * [背景](#背景)
    * [安装](#安装)
    * [使用](#使用)
        * [启动一个tmux session](#启动一个tmux-session)
        * [暂时离开当前session](#暂时离开当前session)
        * [回到之前的session](#回到之前的session)
        * [重命名session](#重命名session)
        * [创建window](#创建window)
        * [创建pane](#创建pane)
* [ps](#ps)
* [scp](#scp)
* [参考](#参考)

## tmux

### 背景

当我用ssh连接到服务器后，起了一个服务后，如果我想要退出ssh, 但是又要保证服务是开启的，tmux就是这么的一个神器。

当然，它主要是用于终端复用, Tmux的Session可以用来保存一大堆的终端, 每个Session又可以有多个Window, 每个Window又可以分为多个Pane。


### 安装

Centos7下实用yum 安装:
```
yum install tmux
```

### 使用

#### 启动一个tmux session

```
tmux 

// 指定session的name
tmux new -s name
```

#### 暂时离开当前session

```
// 暂时离开当前会话
<prefxi>d

// 注意不是ctrl+d, ctrl+d会退出了tmux这个进程了

// 查看有哪些session
tmux ls
```

#### 回到之前的session

```
// 回到之前的session
tmux attach

// 回到某个name的session
tmux attach -t name
tmux a -t name
tmux at -t name
```

#### 重命名session

```
// prefix为session的前缀键
// 实用tmux的时候都要按prefix
// 默认的prefix是 ctrl+b

<prefix>$
```

#### 创建window

```
<prefix>c
```

#### 创建pane

```
// 垂直分割
<prefix>"

// 水平分割
<prefix>%
```

## ps

ps(Process Status)列出当前运行的哪些进程, 我简单描述几个常用的参数规则。

```
// 列出用户ss的进程
ps -u ss

// 列出与python相关的进程
ps -ef | grep python
```

## scp

通常我们使用scp命令来上传文件到服务器上去, 以前我是这么笨拙的去操作。

```
tar -cvf demo.rar 1.md 2.md 3.md
scp demo.rar root@xx.xx.xx.xx:/opt/
ssh root@xx.xx.xx.xx
cd /opt
tar -xvf demo.rar
```

后来发现，这真是一个愚蠢的行为, 其实不需要打包上传，参数r可以递归上传。

```
scp -r demo root@xx.xx.xx.xx:/opt
```

当然，还有一个`rsync`命令，也可以用来上传文件。

## 参考

[优雅地使用命令行：Tmux 终端复用](http://harttle.com/2015/11/06/tmux-startup.html)
[Tmux - Linux从业者必备利器](http://cenalulu.github.io/linux/tmux/)
[Tmux简介与使用](http://kuanghy.github.io/2016/09/29/tmux)
[每天一个linux命令（41）：ps命令](http://www.cnblogs.com/peida/archive/2012/12/19/2824418.html)
[10个重要的Linux ps命令实战](https://www.zybuluo.com/kuailezhishang/note/67011)