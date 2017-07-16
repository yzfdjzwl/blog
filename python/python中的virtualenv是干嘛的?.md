众所周知，python的各种库跨度比较大，比如如果你开发web的话，一个项目使用的Django是1.8， 而另一个项目使用的Django版本是1.7， 这就给开发人员带来了很大的困扰。

因此，python提供了`virtualenv`这个工具，你可以将它想象成是虚拟机，但是它并不是虚拟机。它可以与你本机的全局python的环境隔离开, 下面介绍一下它的基本使用。

```bash
// 安装
sudo pip install virtualenv

// 使用
mkdir envTest && cd envTest

// 创建一个虚拟环境
virtualenv ENV

// 如果你遇到这样的报错: virtualenv is not compatible with this system or executable
// 那么请在英文目录下进行操作

// 你所有安装的包都会在 ENV/lib/pythonx.x下面

cd ENV

// 启动虚拟环境
source bin/activate

// 这个时候你的控制台前面会显示(ENV)表示已经进入虚拟环境了

// 退出环境
deactivate

// 安装包
pip install -r requirements.txt
```