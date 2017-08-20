title: python时间操作总结
date: 2016-03-30 10:16:23
tags:
- python
- 时间操作
categories:
- python
---

# 获取时间戳
python时间戳是float类型的,个位是秒。
```
time.time()
```
<!-- more -->
# 获取日期
```
datetime.datetime.now()
datetime.datetime.today()
```
now默认参数None时，与today是相同的，可以指定时区。

# 时间戳转日期
```
timeStamp = time.time()
dateArray = datetime.datetime.utcfromtimestamp(timeStamp)
print(dateArray) #datetime.datetime(2016, 3, 30, 2, 37, 14, 702067)
```
# 时间戳转日期字符串
```
timeStamp = time.time()
timeArray = time.localtime(timeStamp)
timeStr = time.strftime("%Y-%m-%d %H:%M:%S", timeArray) # "2013-10-10 23:40:00"
```
或者
```
timeStamp = time.time()
dateArray = datetime.datetime.utcfromtimestamp(timeStamp)
timeStr = dateArray.strftime("%Y-%m-%d %H:%M:%S")
```
# 日期转字符串
```
dateArray = datetime.datetime.now()
timeStr = dateArray.strftime("%Y-%m-%d %H:%M:%S")
```

# 日期转时间戳
```
dateArray = datetime.datetime.now()
timeStamp = time.mktime(dateArray.timetuple())
```
# 字符串转日期
```
timeStr = "2016-06-1 19:20:00"
dateArray = datetime.datetime.strptime(timeStr,"%Y-%m-%d %H:%M:%S")
```
# 字符串转时间戳
```
timeStr = "2016-06-1 19:20:00"
timeStruct = time.strptime(timeStr,'%Y-%m-%d %H:%M:%S')
tiemStamp = time.mktime(timeStruct)
```
# 日期加减
天，小时，分，秒
```
now = datetime.datetime.now()
last_day = now - datetime.timedelta(1) ＃一天前,或datetime.timedelta(days=1)
last_hour = now - datetime.timedelta(hours = 1) #一小时前
last_minute = now - datetime.timedelta(minutes=1) # 一分钟前
last_second = now - datetime.timedelta(seconds=1) # 一秒钟前
```
年，月，日
```
now = datetime.datetime.now()
year = now.month == 1 ? now.year - 1: now.year
month = now.month == 1 ? 12 : now.month - 1
last_month = datetime.datetime(year,month,now.day)
last_year = datetime.datetime(now.year-1,now.month,now.day)
```
