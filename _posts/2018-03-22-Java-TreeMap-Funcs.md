### Java TreeMap 的方法，floorKey, celingKey, higherKey, lowerKey

```
TreeMap<Integer, String> tm = new TreeMap<>();
tm.put(1, "1");
tm.put(2, "2");
tm.put(5, "5");
tm.put(7, "7");

Integer floorKey1 = tm.floorKey(1);
System.out.println("floorKey1 = " + floorKey1);
Integer floorKey2 = tm.floorKey(2);
System.out.println("floorKey2 = " + floorKey2);
Integer floorKey3 = tm.floorKey(3);
System.out.println("floorKey3 = " + floorKey3);

Integer floorKey6 = tm.floorKey(6);
System.out.println("floorKey6 = " + floorKey6);

Integer floorKey8 = tm.floorKey(8);
System.out.println("floorKey8 = " + floorKey8);

Integer higherKey1 = tm.higherKey(1);
System.out.println("higherKey1 = " + higherKey1);
Integer higherKey3 = tm.higherKey(3);
System.out.println("higherKey3 = " + higherKey3);
Integer higherKey5 = tm.higherKey(5);
System.out.println("higherKey5 = " + higherKey5);
Integer higherKey6 = tm.higherKey(6);
System.out.println("higherKey6 = " + higherKey6);
Integer higherKey7 = tm.higherKey(7);
System.out.println("higherKey7 = " + higherKey7);
Integer higherKey8 = tm.higherKey(8);
System.out.println("higherKey8 = " + higherKey8);

Integer lowerKey1 = tm.lowerKey(1);
System.out.println("lowerKey1 = " + lowerKey1);
Integer lowerKey3 = tm.lowerKey(3);
System.out.println("lowerKey3 = " + lowerKey3);
Integer lowerKey5 = tm.lowerKey(5);
System.out.println("lowerKey5 = " + lowerKey5);
Integer lowerKey6 = tm.lowerKey(6);
System.out.println("lowerKey6 = " + lowerKey6);
Integer lowerKey7 = tm.lowerKey(7);
System.out.println("lowerKey7 = " + lowerKey7);
Integer lowerKey8 = tm.lowerKey(8);
System.out.println("lowerKey8 = " + lowerKey8);

Integer ceilingKey1 = tm.ceilingKey(1);
System.out.println("ceilingKey1 = " + ceilingKey1);
Integer ceilingKey2 = tm.ceilingKey(2);
System.out.println("ceilingKey2 = " + ceilingKey2);
Integer ceilingKey3 = tm.ceilingKey(3);
System.out.println("ceilingKey3 = " + ceilingKey3);
Integer ceilingKey5 = tm.ceilingKey(5);
System.out.println("ceilingKey5 = " + ceilingKey5);
Integer ceilingKey6 = tm.ceilingKey(6);
System.out.println("ceilingKey6 = " + ceilingKey6);
Integer ceilingKey7 = tm.ceilingKey(7);
System.out.println("ceilingKey7 = " + ceilingKey7);
Integer ceilingKey8 = tm.ceilingKey(8);
System.out.println("ceilingKey8 = " + ceilingKey8);
```

打印如下：

```
floorKey1 = 1
floorKey2 = 2
floorKey3 = 2
floorKey6 = 5
floorKey8 = 7
higherKey1 = 2
higherKey3 = 5
higherKey5 = 7
higherKey6 = 7
higherKey7 = null
higherKey8 = null
lowerKey1 = null
lowerKey3 = 2
lowerKey5 = 2
lowerKey6 = 5
lowerKey7 = 5
lowerKey8 = 7
ceilingKey1 = 1
ceilingKey2 = 2
ceilingKey3 = 5
ceilingKey5 = 5
ceilingKey6 = 7
ceilingKey7 = 7
ceilingKey8 = null
```

### 总结

`floorKey`：是小于等于传入参数的最大值

`celingKey`：大于等于传入参数的最小值

`higherKey`：大于传入参数的最小值

`lowerKey`：小于传入参数的最大值

### 其它

同理还有floorEntry, celingEntry, higherEntry, lowerEntry，取出来的是相对应的entry。