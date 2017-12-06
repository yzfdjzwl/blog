## Python简介

### 数字

```
4 ** 2 // 16

17 // 3.0 // 5.0

width = 20
height = 30
width * height
width + _ // 620
```

### 字符串

```
print 'C:\some\name' // C:\some 
                        ame

print r'C:\some\name' // C:\some\name

print '''You know you are a boy // 可以跨多行
could you please
tell me that'''

3 * 'i' + 'mm' // iiimm

'Py' 'thon' // Python 

word = 'Python'

word[0] // 'P'

word[-1] // 'n'

word[0:5] // 'Pytho', 这叫切片

word[3:] // 'hon'

len(word) // 6

// 字符串是不可变的
```

### unicode字符串 

```
u'hello world' // 用于存储文本数据的类型

u'你好吗'.encode('utf-8') // 将unicode转为utf-8

unicode('\xc3\xa4\xc4', 'utf-8') // 生成一个unicode的字符串
```

### 列表

```
a = [1, 4, 9, 16]

a[1:4] // 也有切片操作, [1, 4, 9]

a[:] // 浅拷贝, [1, 4, 9, 16]

a + [25, 36] // [1, 4, 9, 16, 25, 36]

b = [1]

b.append(2 ** 3) // [1, 8]

b[1:3] = [2, 4, 5] // [1, 2, 4, 5]

len(b) // 4
```

## 控制流

### if

```
a = 4

if a < 1:

elif a < 3:

else:
```

### for

```
``
