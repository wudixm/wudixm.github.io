### 计算时间

```
SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
String string = format.format(new Date());
Date parse = format.parse(string);
Calendar instance = Calendar.getInstance();
instance.set(Calendar.HOUR_OF_DAY, 0); // 注意这里的Calendar.HOUR_OF_DAY 不能是Calendar.HOUR
instance.set(Calendar.MINUTE, 0);
instance.set(Calendar.SECOND, 0);
System.out.println("instance.getTime() = " + instance.getTime());
instance.roll(Calendar.DATE, -2);
System.out.println("instance.getTime() = " + instance.getTime());
System.out.println("parse = " + parse);

```

打印

```
instance.getTime() = Tue Sep 18 00:00:00 CST 2018
instance.getTime() = Sun Sep 16 00:00:00 CST 2018
parse = Tue Sep 18 00:00:00 CST 2018
```

### HOUR_OF_DAY 和HOUR

**Calendar.HOUR_OF_DAY**

> Calendar.HOUR_OF_DAY:是指获取24小时制的小时,取值范围:0-23;

**Calendar.HOUR**

> Calendar.HOUR:是指获取12小时制的小时,取值范围:0-12,凌晨和中午都是0,不是12;需要配合**Calendar.AM_PM**使用;

> 源码注释:12-hour clock (0 - 11). Noon and midnight are represented by 0, not by 12.

注意:凌晨和中午设置的时候参数都要传递为0,否则设置的凌晨12点就变成下午12点了,设置的下午12点就变成第二天的凌晨12点.

