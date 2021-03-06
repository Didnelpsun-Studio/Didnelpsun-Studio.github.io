---
layout: post
title:  "数字处理类"
date:   2019-09-26 19:18:15 +0800
categories: notes java base
tags: Java 基础 DecimalFormat BigInteger BigDeciaml Random
excerpt: "数字格式化、数学与大数"
---

Java中为了处理数学、随机、金融等数字问题，封装了一些类。

## DecimalFormat类（格式化数字）

Java主要对浮点型数据进行数字格式化，包括`double`和`float`型。Java未格式化数据遵循以下原则：  

绝对值在e-3到e7之间的数值以常规小数形式表示。在此之外以科学计数法表示。  

除此之外可以使用`DecimalFormat`类：`java.text.DecimalFormat`。  

该类是`NumberFormat`类的子类，用于格式化十进制数字，可以转换为整数，浮点数，百分数等。可以向其传入数字格式，格式化数字可以利用该类中的一些特殊字符造成一个格式化模板，使数字按照一定模式进行匹配。

特殊符号：  
0 一个数字，如果该位不存在数字就显示0。  
\# 一个数字，不包括 0。  
. 小数的分隔符的占位符。  
, 分组分隔符的占位符。  
; 分隔格式。  
\- 缺省负数前缀。  
% 乘以 100 和作为百分比显示。  
\u2030 乘以 1000 和作为千进制显示。  
\u00A4 本符号放置在数字的前缀或者后缀，作为货币记号。  
X 前缀或后缀中使用的任何其它字符，用来引用前缀或后缀中的特殊字符。  
E 分隔科学记数法中的尾数和指数。  
' 转义字符，当出现特殊符号，放在前面以转义。  

类的使用方法：

### &emsp;1. format()方法

<span style="color:aqua">格式：</span>

`DecimalFormat 格式模板变量名 = new DecimalFormat(格式模板字符串);`  

`String 保存格式化变量名 = 格式模板变量名.format(目标数据);`

如：

```java
DecimalFormat df1 = new DecimalFormat("0.0");
　　DecimalFormat df2 = new DecimalFormat("#.#");
　　DecimalFormat df3 = new DecimalFormat("000.000");
　　DecimalFormat df4 = new DecimalFormat("###.###");
　　System.out.println(df1.format(12.34));
　　System.out.println(df2.format(12.34));
　　System.out.println(df3.format(12.34));
　　System.out.println(df4.format(12.34));
>>>>>
12.3
12.3
012.340
12.34
```

<span style="color:aqua">格式：</span>`String 保存格式化变量名 = new DecimalFormat(格式模板字符串).format(目标数据)`
  
如；

```java
importjava.text.DecimalFormat;
publicclassTestNumberFormat{
　　publicstaticvoidmain(String[]args){  
　　　　double pi=3.1415927;　//圆周率  
　　　　//取一位整数  
　　　　System.out.println(new DecimalFormat("0").format(pi));　　　//3  
　　　　//取一位整数和两位小数  
　　　　System.out.println(new DecimalFormat("0.00").format(pi));　//3.14  
　　　　//取两位整数和三位小数，整数不足部分以0填补。  
　　　　System.out.println(new DecimalFormat("00.000").format(pi));// 03.142  
　　　　//取所有整数部分  
　　　　System.out.println(newDecimalFormat("#").format(pi));　　　//3  
　　　　//以百分比方式计数，并取两位小数  
　　　　System.out.println(new DecimalFormat("#.##%").format(pi));　//314.16%  
　　　　long c=299792458;　　//光速  
　　　　//显示为科学计数法，并取五位小数  
　　　　System.out.println(newDecimalFormat("#.#####E0").format(c));　//2.99792E8  
　　　　//显示为两位整数的科学计数法，并取四位小数  
　　　　System.out.println(newDecimalFormat("00.####E0").format(c));　//29.9792E7  
　　　　//每三位以逗号进行分隔。  
　　　　System.out.println(newDecimalFormat(",###").format(c));　　　//299,792,458  
　　　　//将格式嵌入文本  
　　　　System.out.println(newDecimalFormat("每秒###\米。").format(c));  
　　}  
}
```

### &emsp;2. setGroupingSize()方法

```java
package test;
import java.text.DecimalFormat;
public class DecimalMethod {
    public static void main(String[] args) {
        DecimalFormat myFormat = new DecimalFormat();
        myFormat.setGroupingSize(2);//将数字以每两个数字分组(整数部分)
        String output = myFormat.format(123456.789);
        System.out.println("将数字以每两个数字分组："+output);
        myFormat.setGroupingUsed(false);//设置不允许数字进行分组
        String output2 = myFormat.format(123456.789);
        System.out.println("不允许数字分组"+output2);
    }
}
>>>>>
将数字以每两个数字分组：12,34,56.789
不允许数组分组：123456.789
```

&emsp;

## Math类（数学运算）

`Math`类提供了许多数学函数方法，被定义为`static`形式，可以通过`Math.`数学方法的形式调用。

### &emsp;1. 数学常量

Math.PI：圆周率；  

Math.E：自然对数；

### &emsp;2. 常用数学方法

#### &emsp;&emsp;2.1 三角函数

1. `sin(角度/弧度)`：正弦函数；  
2. `cos(角度/弧度)`：余弦函数；  
3. `tan(角度/弧度)`：正切函数；  
4. `asin(角度/弧度)`：反正弦函数；  
5. `acos(角度/弧度)`：反余弦函数；  
6. `atan(角度/弧度)`：反正切函数；  
7. `toRadians(角度)`：将角度转换为弧度函数；  
8. `toDegrees(弧度)`：将弧度转换为角度函数；  

三角函数参数和返回值都是`double`类型的。  

#### &emsp;&emsp;2.2 指数函数

1. `exp(值)`：取e的参数值次方，即取e^值；  
2. `log(值)`：取自然对数，即取ln值；  
3. `log10(值)`：取底数为10的对数；  
4. `sqrt(值)`：取值的平方根，值不能为负数；  
5. `cbrt(值)`：取值的立方根；  
6. `pow(值a,值b)`：取a的b次方；  

指数函数的参数和返回值也都是`double`类型的。  

#### &emsp;&emsp;2.3 取整函数

1. `ceil(值)`：返回大于等于参数的最小整数；  
2. `floor(值)`：返回小于等于参数的最大整数；  
3. `rint(值)`：返回与参数最接近的整数，如果两个同为整数且同样接近，取偶数。  

这三个方法的参数和返回值都是double类型。  

`round(值)`：将float型参数加上0.5后返回与参数最接近的int型整数；将double型参数加上0.5后返回与参数最接近的long型整数。  

#### &emsp;&emsp;2.4 最值绝对值函数

1. `max(值a,值b)`：取ab之间的最大值，a，b和返回值都是`double`型。  
2. `min(值a,值b)`：取ab之间的最小值，a，b是什么类型返回值就是什么类型，可以为`int`、`long`、`float`和`double`型数据。  
3. `abs(值)`：取值的绝对值，参数是什么类型返回值就是什么类型。  

### &emsp;3. 随机数

用于产生随机数字，默认生成大于等于0.0且小于1.0的`double`型随机数。  

1. `(int)(Math.random()*n)`>>>>返回大于等于0且小于n的随机数；  
2. `m+(int)(Math.random()*n)`>>>>返回大于等于m且小于m+n的随机数；  
3. `(char)('a'+Math.random()*('z'-'a'+1))`>>>>随机生成a到z的字符；  
4. `(char)('A'+Math.random()*('R'-'A'+1))`>>>>随机生成A到R的字符；

### &emsp;4. 大整数运算（BigInteger类）

<span style="color:aqua">格式：</span>`BigInteger 变量名 = new BigInteger(字符串);`  

使用 变量名.方法名()的格式对大整数进行运算。  

1. `add(值)`：加法运算；  
2. `subtract(值)`：减法运算；  
3. `multiply(值)`：乘法运算；  
4. `divide(值)`：除法运算；  
5. `remainder(值)`：取余运算；  
6. `divideAndRemainder(值)`：用`BigInteger`数组返回余数和商，结果数组中第一个值为商，第二个值为余数；  

其参数值和返回值都是`BigInteger`类。  

1. `pow(整数值)`：对参数进行整数值次方操作；  
2. `negate()`：取反操作；  
3. `shiftLeft(n)`：将数字左移n位，如果n是负数则右移；  
4. `shiftRight(n)`：将数字右移n位，如果n为负数则左移；  
5. `and(值)`：做与操作；  
6. `or(值)`：做或操作；  
7. `max(值)`：返回较大值；  
8. `min(值)`：返回较小值；  

其参数值和返回值都是`BigInteger`类。  

1. `compareTo(值)`：做数字比较操作，返回值int类型；  
2. `equals(值)`：当参数值为BigInteger类型的数字且数值相等时，才返回`true`，否则返回`false`。

### &emsp;5. 大小数运算（BigDeciaml类）

<span style="color:aqua">格式：</span>`BigDeciaml 变量名 = new BigDeciaml(字符串);` / `BigDeciaml 变量名 = new BigDeciaml(双精度浮点数);`

使用 变量名.方法名() 的格式对大小数进行运算。

1. `add(值)`：加法运算；  
2. `subtract(值)`：减法运算；  
3. `multiply(值)`：乘法运算；  
4. `divide(除数,商小数点后的位数,近似处理模式)`：除法运算；  

`divide()`的处理模式：  
`BigDecimal.ROUND_UP`：商的最后一位如果大于0，就向前进位，无论负数正数都是如此；  
`BigDecimal.ROUND_DOWN`：商的最后一位无论什么数字都省略；  
`BigDecimal.ROUND_CEILING`：商如果是正数就按照ROUND_UP模式处理，如果是负数就是按照`ROUND_DOWN`模式处理，这种处理模式是商的近似值使用大于或者等于实际值。  
`BigDecimal.ROUND_FLOOR`：商如果是正数就按照`ROUND_DOWN`模式处理，如果是负数就是按照`ROUND_UP`模式处理，这种处理模式是商的近似值使用小于于或者等于实际值。  
`BigDecimal.ROUND_HALF_DOWN`：对商进行四舍五入操作，如果商小于等于5就舍弃，如果大于5就进位； 如：7.5>>>>>7  
`BigDecimal.ROUND_HALF_UP`：对商进行四舍五入操作，如果商小于5就舍弃，如果大于等于5就进位； 如：7.5>>>>>8  
`BigDecimal.ROUND_HALF_EVEN`：如果商的倒数第二位是奇数就按照`ROUND_HALF_UP`处理，如果是偶数，就按照`HALF_DOWN`处理；  

其参数值和返回值都是`BigInteger`类。

## Random类

除了使用Math类中的random()方法外，还可以使用java.util.Random类。实例化一个Random对象创建一个随机数生成器。  

<span style="color:aqua">格式：</span>`Random 随机数生成器名 = new Random();`  

这种创建方式下Java编译器将以系统当前时间作为随机数生成器的种子，来产生随机数，如果运行速度过快也可能出现同样的随机数。  
也可以传入随机数生成器的种子作为函数参数。  

还有一些返回值格式方法，以 `随机数生成器名.方法名();` 的方式调用：  
`nextInt()`：返回一个随机整数；  
`nextInt(整数n)`：返回一个大于等于0小于n的随机整数；  
`nextLong()`：返回一个随机长整型数；  
`nextBoolean()`：返回一个随机布尔值；  
`nextFloat()`：返回一个随机单精度浮点型值；  
`nextDouble()`：返回一个随机双精度浮点型；  
`nextGaussian()`：返回一个概率密度为高斯分布的双精度值。 
