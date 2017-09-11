### 时间分段

#### 样例

```
input:
2017-03-05, 2017-06-22

output:
[
    {
        startTime: 2017-03-05,
        endTime: 2017-03-31,
    },
    {
        startTime: 2017-04-01,
        endTime: 2017-04-30,
    },
    {
        startTime: 2017-05-01,
        endTime: 2017-05-31,
    },
    {
        startTime: 2017-06-01,
        endTime: 2017-06-22,
    },
]
```

#### 原生javascript代码

```
function splitDateRange(startTime, endTime) {
  const startTimeDate = new Date(startTime);
  const endTimeDate = new Date(endTime);
  const startTimeSecond = startTimeDate.getTime();
  const endTimeSecond = endTimeDate.getTime();
  let currentYear = startTimeDate.getFullYear();
  let currentMonth = startTimeDate.getMonth();
  const oneMonth = 60 * 60 * 24 * 30 * 1000;
  let dateRange = [];
  let currentTimeSecond = startTimeSecond;
  const currentDay = endTimeDate.getDate();

  // 间隔没有超过一个月
  if (endTimeSecond - startTimeSecond < oneMonth) {
    dateRange = [{ startTime, endTime }];
    return dateRange;
  }
  while (currentTimeSecond < endTimeSecond) {
    if (currentTimeSecond === startTimeSecond) {
      endTime = moment(new Date(currentYear, currentMonth + 1, 0)).format('YYYY-MM-DD'); //开始月的最后一天
    } else {
      startTime = moment(new Date(currentYear, currentMonth, 1)).format('YYYY-MM-DD'); // 当前月第一天
      if (new Date(currentTimeSecond).getFullYear() === currentYear
        && new Date(currentTimeSecond).getMonth() + 1 === endTimeDate.getMonth()) { // 结束时间的最后一个月
        endTime = moment(new Date(currentYear, currentMonth, currentDay)).format('YYYY-MM-DD'); // 结束时间的当天
      } else {
        endTime = moment(new Date(currentYear, currentMonth + 1, 0)).format('YYYY-MM-DD'); // 当前月的最后一天
      }
    }
    currentTimeSecond = new Date(currentYear, currentMonth + 1, 0).getTime(); //当前月的最后一天
    currentMonth += 1;
    if (currentMonth === 13) { // 考虑跨年
      currentMonth = 1;
      currentYear += 1;
    }
    dateRange = [...dateRange, { startTime, endTime }];
  }
  return dateRange;
}
```

#### 基于moment库

```
function splitDateRange(startTime, endTime) {
  const startTimeDate = moment(startTime);
  const endTimeDate = moment(endTime);
  const startTimeSecond = startTimeDate.valueOf();
  const endTimeSecond = endTimeDate.valueOf();
  let currentYear = startTimeDate.year();
  let currentMonth = startTimeDate.month();
  const oneMonth = 60 * 60 * 24 * 30 * 1000;
  let dateRange = [];
  let currentTimeSecond = startTimeSecond;
  const currentDay = endTimeDate.date();

  if (endTimeSecond - startTimeSecond < oneMonth) {
    dateRange = [{ startTime, endTime }];
    return dateRange;
  }
  while (currentTimeSecond < endTimeSecond) {
    if (currentTimeSecond === startTimeSecond) {
      endTime = moment(new Date(currentYear, currentMonth + 1, 0)).format('YYYY-MM-DD');
    } else {
      startTime = moment(new Date(currentYear, currentMonth, 1)).format('YYYY-MM-DD');
      if (moment(currentTimeSecond).year() === currentYear
        && moment(currentTimeSecond).month() + 1 === endTimeDate.month()) {
        endTime = moment(new Date(currentYear, currentMonth, currentDay)).format('YYYY-MM-DD');
      } else {
        endTime = moment(new Date(currentYear, currentMonth + 1, 0)).format('YYYY-MM-DD');
      }
    }
    currentTimeSecond = new Date(currentYear, currentMonth + 1, 0).getTime();
    currentMonth += 1;
    if (currentMonth === 13) {
      currentMonth = 1;
      currentYear += 1;
    }
    dateRange = [...dateRange, { startTime, endTime }];
  }
  return dateRange;
}
```

### 对象值累加
