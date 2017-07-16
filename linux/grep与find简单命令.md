在使用linux的时候，经常会用到查找文件或者查找文本，下面介绍两个命令。

### grep

使用方法:

```
// 在当前目录下递归查找class字符串
grep "string" -r .
```

### find

使用方法:

```
// 查找所有md文件
find . -name "*.md"
```