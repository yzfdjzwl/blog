## 原生JavaScript如何获取select选中的value

```
// 1. 拿到select对象
const selectObject = document.getElementById('test');

// 2. 拿到下标
const index = selectObject.selectedIndex;

// 3. 拿到value
const value = selectObject.options[index].value;
```

## 如何比较日期的大小

我自己想到的方法:

```
// 前提格式: 2017-02-02
let startTime = '2017-02-02'.replace(/(\d{4})-(\d{2})-(\d{2})/, '$1$2$3')
let endtTime = '2017-02-03'.replace(/(\d{4})-(\d{2})-(\d{2})/, '$1$2$3')

console.log(parseInt(startTime) - parseInt(endTime));
```