你可以选择字符串拼接，你也可以选择使用%s或者是format，下面简单介绍一下它们的使用方法：

```python
# 在字符串后面跟%，然后后面加上要被替换的值
print('I like %s' % 'apples')

# 如果是多个呢？
print('I %s %s' % ('hate', 'banana'))

# 如果使用format
print('I am a {}'.format('boy'))

# 如果是多个呢？
print('I am {} a {}'.format('not', 'boy'))
```