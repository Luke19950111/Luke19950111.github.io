---
title: 数值-JavaScript标准参考教程（阮一峰）读书笔记
date: 2018-03-23 18:14:57
tags: js数值
categories: JavaScript
---
<center>js数值的一些基础知识</center>
<!-- more -->

## 1.概述
### 1.1 整数和浮点数
JavaScript内部所有数字都以64位浮点数的形式存储。也就是说，在JacaScript语言底层没有整数，都是小数。
### 1.2 数值精度
JavaScript对15位的十进制数都可以精确处理。
### 1.3 数值范围
- JavaScript能表示的数值范围为2^1024^到2^-1023^(开区间）。
- 正向溢出，返回Infinity；负向溢出，返回0。
- Number对象的`MAX_VALUE`和`MIN_VALUE`两个属性返回可以表示的具体最大值和最小值。
***
## 2.数值表示方法
- 字面形式直接表示；
- 科学记数法：小数点前数字多余21位；小数点后的0多余5个。
***
## 3.数值的进制
- 字面量（literal）直接表示数值时：十进制（没有前导0）、十六进制（前缀0X）、八进制（前缀0O）、二进制（前缀0B）。
- 默认将八进制、十六进制、二进制转为十进制。
- 通常有前导0视为八进制，但前导0后有数字8和9，视为十进制。
***
## 4.特殊数值
### 4.1 正零和负零
+0和-0几乎在所有的场合都被当作正常的0；
唯一区别，+0和-0当作分母，返回值不同。（Infinity，-Infinity）
### 4.2 NaN
- Not a Number
- NaN不是独立的数据类型，而是特殊的数值，`typeof NaN //'number`'
- 0除以0也会得到NaN
**运算规则：**
- NaN不等于任何值，包括它本身；
- 数组的indexOf方法使用严格的相等运算符，对NaN不成立；
- NaN在布尔运算时被当作false；
- NaN与任何数（包括自己）的运算，得到的都是NaN。
### 4.3 Infinity
- 表示“无穷”，正数值太大或负数值太小，有正负之分；
- 数值正向溢出（overflow）、负向溢出（underflow）和被0除，返回Infinity；
- Infinity大于一切数值（除NaN），-Infinity小于一切数值（除NaN）；
- Infinity与NaN比较，返回false。
***
## 5.与数值相关的全局方法
## 5.1 parseInt()
- 用于将字符串转为整数；
- 返回值只有两种可能：十进制整数，NaN；
- parseInt方法可以接受第二个参数（2到36之间），表示被解析值的进制，返回该值对应的十进制数；默认第二个参数是10。parseInt方法可以用作进制转换。
## 5.2 parseFloat()
- parseFloat方法用于将字符串转为浮点数；
- 如果字符串符合科学记数法，则会进行相应的转换。（parseInt不识别科学记数法）
### 5.3 isNaN()
- 用来判断一个值是否为NaN；
- isNaN只对数值有效，如果传入其他值，会先被转成数值；
- 字符串、对象和数组都会被转成NaN，isNaN返回false；
- 但是对于空数组和只有一个数值成员的数组，isNaN返回false；（这些数组能被Number函数转成数值）
- 因此，使用isNaN之前最好判断一下数据类型。
### 5.4 isFinite()
- 返回一个布尔值，表示某个值是否为正常的数组；
- Infinity、-Infinity、NaN和undefined会返回false；
- isFinite对于其他数值都会返回true。