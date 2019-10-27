---
title: "Java中BigDecimal的用法"
date: 2019-10-25 17:38:58
comments: true
tags:
	- Java
---

# 用法

 `BigDecimal`可以表示一个任意大小且精度完全准确的浮点数 

```java
BigDecimal bd = new BigDecimal("123.4567");
System.out.priyonntln(bd.multiply(bd)); // 15241.55677489
```

 `BigDecimal`用`scale()`表示小数位数，例如： 

```java
BigDecimal d1 = new BigDecimal("123.45");
BigDecimal d2 = new BigDecimal("123.4500");
BigDecimal d3 = new BigDecimal("1234500");
System.out.println(d1.scale()); // 2,两位小数
System.out.println(d2.scale()); // 4
System.out.println(d3.scale()); // 0
```

 <!-- more -->

通过`BigDecimal`的`stripTrailingZeros()`方法，可以将一个`BigDecimal`格式化为一个相等的，但去掉了末尾0的`BigDecimal`： 

```java
BigDecimal d1 = new BigDecimal("123.4500");
BigDecimal d2 = d1.stripTrailingZeros();
System.out.println(d1.scale()); // 4
System.out.println(d2.scale()); // 2,因为去掉了00

BigDecimal d3 = new BigDecimal("1234500");
BigDecimal d4 = d3.stripTrailingZeros();
System.out.println(d3.scale()); // 0
System.out.println(d4.scale()); // -2
```

 如果一个`BigDecimal`的`scale()`返回负数，例如，`-2`，表示这个数是个整数，并且末尾有2个0。 

 可以对一个`BigDecimal`设置它的`scale`，如果精度比原始值低，那么按照指定的方法进行四舍五入或者直接截断： 

```java
BigDecimal d1 = new BigDecimal("123.456789");
BigDecimal d2 = d1.setScale(4, RoundingMode.HALF_UP); // 四舍五入，123.4568
BigDecimal d3 = d1.setScale(4, RoundingMode.DOWN); // 直接截断，123.4567
```

## 运算

1.**加法 add()** 			2.**减法subtract()**  			3.**乘法multiply()**  

4.**除法divide()**  		5.**绝对值abs()**

```java
//加法
BigDecimal result1 = num1.add(num2);
BigDecimal result12 = num12.add(num22);
 
//减法
BigDecimal result2 = num1.subtract(num2);
BigDecimal result22 = num12.subtract(num22);
 
//乘法
BigDecimal result3 = num1.multiply(num2);
BigDecimal result32 = num12.multiply(num22);
 
//绝对值
BigDecimal result4 = num3.abs();
BigDecimal result42 = num32.abs();
 
//除法
BigDecimal result5 = num2.divide(num1,20,BigDecimal.ROUND_HALF_UP);
BigDecimal result52 = num22.divide(num12,20,BigDecimal.ROUND_HALF_UP);
```

 对`BigDecimal`做加、减、乘时，精度不会丢失，但是做除法时，存在无法除尽的情况，这时，就必须指定精度以及如何进行截断： 

```java
BigDecimal d1 = new BigDecimal("123.456");
BigDecimal d2 = new BigDecimal("23.456789");
BigDecimal d3 = d1.divide(d2, 10, RoundingMode.HALF_UP); // 保留10位小数并四舍五入
```

 ## 比较

在比较两个`BigDecimal`的值是否相等时，要特别注意，使用`equals()`方法不但要求两个`BigDecimal`的值相等，还要求它们的`scale()`相等： 

```java
BigDecimal d1 = new BigDecimal("123.456");
BigDecimal d2 = new BigDecimal("123.45600");
System.out.println(d1.equals(d2)); // false,因为scale不同
System.out.println(d1.equals(d2.stripTrailingZeros())); // true,因为d2去除尾部0后scale变为2
System.out.println(d1.compareTo(d2)); // 0
```

 必须使用`compareTo()`方法来比较，它根据两个值的大小分别返回负数、正数和`0`，分别表示小于、大于和等于。 

 <p style="color:red;font-weight:bold;"> 总是使用compareTo()比较两个BigDecimal的值，不要使用equals()！ </p>
 如果查看`BigDecimal`的源码，可以发现，实际上一个`BigDecimal`是通过一个`BigInteger`和一个`scale`来表示的，即`BigInteger`表示一个完整的整数，而`scale`表示小数位数： 

```java
public class BigDecimal extends Number implements Comparable<BigDecimal> {
    private final BigInteger intVal;
    private final int scale;
}
```

 `BigDecimal`也是从`Number`继承的，也是不可变对象。 

## 判段

### 判断一个`BigDecimal`是否为负数

```java
BigDecimal bigDecimal = bprocurementOrderPojo.getPayableFee();
int num = bigDecimal.signum();
```

 num是 -1, 0, 1，分别表示 负数、零、正数 

### 和0比较

```java
paymentLogVo.getRebateAmount().equals(BigDecimal.ZERO)//是否等于0
```

```java
int r=big_decimal.compareTo(BigDecimal.ZERO); //和0，ZERO比较
if(r==0) //等于
if(r==1) //大于
if(r==-1) //小于
```

# 小结

1.  `BigDecimal`用于表示精确的小数，常用于财务计算； 
2.  比较`BigDecimal`的值是否相等，必须使用`compareTo()`而不能使用`equals()`。 