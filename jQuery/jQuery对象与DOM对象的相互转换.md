## jQuery对象转DOM对象

### 第一种方法

```
var $test = $(".test");
var test = $test[0];
```

### 第二种方法

```
var $test = $(".test");
var test = $test.get(0);
```

## DOM对象转jQuery对象

```
var test = document.getElementById("test");
var $test = $test;
```