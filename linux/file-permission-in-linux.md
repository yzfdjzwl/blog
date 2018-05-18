# linux文件权限基础

## 查看文件权限

```bash
// 列出所有文件
ls -al

// 最前面的一串10个字母的字符串
// 可能像
drwxrwxr-x

// 第一位，表示文件类型，如d表示directory

// 第234位，表示所有者(user)的权限
r 表示可读, 对应数字4
w 表示可写, 对应数字2
x 表示可执行(如果可以被执行的话), 对应数字1
- 表示还没有被授权

// 第567位，表示组群(group)的权限

// 第8910位，表示其他人(other)的权限
```

## 开始实验

```bash
mkdir test && cd test

vim xx.md

ls -al

# 表示所有者可读可写，其他任何人只拥有读取的权限
-rw-r--r-- xx.md

# 设置所有者拥有3个权限， 群组拥有可读可写的权限， 其他人拥有可读可写的权限
chmod 766 xx.md

# 设置3种身份的人有没有写的权限
chmod -w xx.md

# 只给拥有者加上可写权限
chmod u+w xx.md

# 只给群组加上可写权限
chmod g+w xx.md

# 只给其他人加上可写权限
chmod o+w xx.md

# 去掉其他人的可写权限
chmod o-w xx.md

# 给所有人去掉可写权限
chmod a-w xx.md

# 设置群组的特定权限
chmod g=rwx xx.md

# 给整个目录所有的文件添加权限(即递归)
chmod -R 777 xx.md
```
