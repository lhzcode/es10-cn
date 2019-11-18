# 7 抽象操作

这些抽象操作不是语言本身的一部分；它们被定义在这里是为了协助语言的语义规范。 在本规范中定义了其他更专门的抽象操作。

## 7.1 类型转换 <div id="sec-type-conversion"></div>

ECMAScript运行环境会在需要时执行自动类型转换。定义一套关于转换的抽象操作有助于阐明某些结构的语义。这些关于转换的抽象操作是多态的，它们可以接受任何ECMAScript语言类型，但不能接受规范类型。

### 7.1.1 ToPrimitive ( input [ , PreferredType ] ) <div id="sec-toprimitive"></div>

**ToPrimitive** 抽象操作接受一个值，和一个可选的期望类型作参数。**ToPrimitive** 运算符把其值参数转换为非对象类型。如果对象有能力被转换为不止一种原语类型，可以使用可选的期望类型来暗示那个类型。根据下面的算法完成转换：

1. 断言：input是一个ECMAScript语言值。
2. 如果 Type(input)  是对象，那么
   1. 如果当前没有 PreferredType ，令 hint 为" default "。
   2. 否则如果 PreferredType 是提示字符串，令 hint 为 "string"。
   3. 否则 PreferredType 是提示数字，令 hint 为 "number"。
   4. 令 exoticToPrim 为 ? GetMethod(input, @@toPrimitive) 。
   5. 如果 exoticToPrim 不是 **undefined**，那么
      1. 令 result 为  ? Call(exoticToPrim, input, « hint ») 。
      2. 如果  Type(result)  不是对象，返回 result。
      3. 抛出 TypeError 异常。

3. 返回 input

> 注意
> 当不带提示地调用ToPrimitive时，通常它的行为就像提示是Number。但是，对象可以通过定义@@ toPrimitive方法来替代此行为。在本规范中定义的对象中，只有日期对象（请参见20.3.4.45）和符号对象（请参见19.4.3.5）会覆盖默认的ToPrimitive行为。日期对象不将提示视为字符串。

#### 7.1.1.1 OrdinaryToPrimitive ( O, hint ) <div id="sec-ordinarytoprimitive"></div>

当使用参数O和hint调用抽象操作OrdinaryToPrimitive时，将执行以下步骤：

1. 断言：Type(O) 是对象。
2. 断言：Type(hint) 是字符串，同时它的值既不是"string"也不是"number"。
3. 如果 hint 是 "string"，那么
   1. 令 methodNames 为  « "toString", "valueOf" »。

4. 否则，
   1. 令 methodNames 为 « "valueOf", "toString" »。

5. 对于methodNames中按List顺序的每个名称，请执行
   1. 令 method 为 ? Get(O, name)。
   2. 如果 IsCallable(method)  是 true，那么
      1. 令 result 为 ? Call(method, O)。
      2. 如果 Type(result)  不是 Object，返回 reuslt。

6. 抛出一个 TypeError 异常

### 7.1.2 ToBoolean ( argument ) <div id="sec-toboolean"></div>

根据表9，抽象操作ToBoolean将参数转换为Boolean类型的值：

| 输入类型 | 结果                                                         |
| -------- | ------------------------------------------------------------ |
| 未定义   | **false**                                                    |
| 空值     | **false**                                                    |
| 布尔值   | 结果等于输入的参数（不转换）。                               |
| 数值     | 如果参数是 **+0**, **-0**, 或 **NaN**，结果为 **false** ；否则结果为 **true**。 |
| 字符串   | 如果参数是空字符串（其长度为零），结果为 **false**，否则结果为 **true**。 |
| Symbol   | **true**                                                     |
| 对象     | **true**                                                     |

### 7.1.3 ToNumber ( argument ) <div id="sec-tonumber"></div>

根据表10，抽象操作ToNumber将参数转换为Number类型的值：

| 输入类型 | 结果                                                         |
| -------- | ------------------------------------------------------------ |
| 未定义   | **NaN**                                                      |
| 空值     | **+0**                                                       |
| 布尔值   | 如果参数是 **true**，结果为 **1**。如果参数是 **false**，此结果为 **+0**。 |
| 数字     | 结果等于输入的参数（不转换）。                               |
| 字符串   | 参见下文的文法和注释。                                       |
| Symbol   | 抛出TypeError异常                                            |
| 对象     | 应用下列步骤：<br />1. 令primValue为 ToPrimitive(输入参数, 暗示数值类型)。<br />2. 返回 ? **ToNumber**(primValue)。 |

#### 7.1.3.1 对字符串类型应用 ToNumber<div id="sec-tonumber-applied-to-the-string-type"></div>

对字符串应用 ToNumber 时，对输入字符串应用如下文法。输入的String解释为一系列UTF-16编码的代码点。如果此文法无法将字符串解释为 StringNumericLiteral 的扩展，那么 ToNumber 的结果为 NaN。

> 注意 1
>
> 该语法的结尾符号全部由Unicode基本多语言平面（BMP）中的字符组成。因此，如果字符串包含任何成对或不成对的前导或尾随替代代码单元，则ToNumber的结果将为NaN。

```
StringNumericLiteral :::
   StrWhiteSpaceopt
   StrWhiteSpaceopt StringNumericLiteralopt StrWhiteSpaceopt
   
 StrWhiteSpace :::
   StrWhiteSpaceChar StrWhiteSpaceopt
   
 StrWhiteSpaceChar :::
   WhiteSpace
   LineTerminator
   
 StrNumericLiteral :::
   StrDecimalLiteral
   BinaryIntegerLiteral
   OctalIntegerLiteral
   HexIntegerLiteral
   
 StrDecimalLiteral :::
   StrUnsignedDecimalLiteral
   + StrUnsignedDecimalLiteral
   - StrUnsignedDecimalLiteral
   
 StrUnsignedDecimalLiteral :::
   Infinity
   DecimalDigits . DecimalDigitsopt ExponentPartopt
   . DecimalDigits ExponentPartopt
   DecimalDigits ExponentPartopt
```

上面未明确定义的所有语法符号都具有在词法语法中用于数字词法的定义

> 注意 2
>
> 需要注意到 StringNumericLiteral 和 NumericLiteral 语法上的不同：
>
> StringNumericLiteral 的前后可以有若干的空白字符或行终止符。
> 十进制的 StringNumericLiteral 可有任意位数的 0 在前头。
> 十进制的 StringNumericLiteral 可有指示其符号的 + 或 - 前缀。
> 空的，或只包含空白的 StringNumericLiteral 會被转换为 +0。
> Infinity 和 -Infinity 会被识别为 StringNumericLiteral 而不是 NumericLiteral.  

##### 7.1.3.1.1 运行时语义: MV <div id="sec-rs-mv"></div>

字符串到数字值的转换，大体上类似于判定 NumericLiteral 的数字值（参见 11.8.3），不过有些细节上的不同，所以，这里给出了把 StringNumericLiteral 转换为数值类型的值的全部过程。这个值分两步来判定：首先，从 StringNumericLiteral 中导出数学值；第二步，以下面所描述的方式对该数学值进行舍入。下文未提供的任何语法符号上的MV是11.8.3.1中定义的该符号的MV 

- StringNumericLiteral ::: [empty] 的数学值是 0。
- StringNumericLiteral ::: StrWhiteSpace 的数学值是 0。
- StringNumericLiteral ::: StrWhiteSpaceopt StringNumericLiteral StrWhiteSpaceopt 的数学值是里面的 StringNumericLiteral 部分，无论前后是否存在空白。
- StrNumericLiteral ::: StrDecimalLiteral 的数学值是 StrDecimalLiteral 的数学值。
- StrNumericLiteral ::: BinaryIntegerLiteral 的数学值是 BinaryIntegerLiteral 的数学值。
- StrNumericLiteral ::: OctalIntegerLiteral 的数学值是 OctalIntegerLiteral 的数学值。
- StrNumericLiteral ::: HexIntegerLiteral 的数学值是 HexIntegerLiteral 的数学值。
- StrDecimalLiteral ::: StrUnsignedDecimalLiteral 的数学值是 StrUnsignedDecimalLiteral 的数学值。
- StrDecimalLiteral ::: + StrUnsignedDecimalLiteral 的数学值是 StrUnsignedDecimalLiteral 的数学值。
StrDecimalLiteral ::: - StrUnsignedDecimalLiteral 的数学值是 StrUnsignedDecimalLiteral 的数学值的负数。 （需要注意的是，如果 StrUnsignedDecimalLiteral 的数学值是 0, 其负数也是 0。下面中描述的舍入规则会合适地处理小于数学零到浮点数 +0 或 -0 的变换。）
- StrUnsignedDecimalLiteral ::: Infinity 的数学值是 10^10000（一个大到会舍入为 +∞ 的值）。
- StrUnsignedDecimalLiteral ::: DecimalDigits . 的数学值是 DecimalDigits 的数学值。
- StrUnsignedDecimalLiteral ::: DecimalDigits . DecimalDigits 的数学值是第一个 DecimalDigits 的数学值加（第二个 DecimalDigits 的数学值乘以 10^-n），这里的 n 是第二个 DecimalDigits 的字符数量。
- StrUnsignedDecimalLiteral ::: DecimalDigits . ExponentPart 的数学值是 DecimalDigits 的数学值乘以 10^e, 这里的 e 是 ExponentPart 的数学值。
- StrUnsignedDecimalLiteral ::: DecimalDigits . DecimalDigits ExponentPart 的数学值是（第一个 DecimalDigits 的数学值加（第二个 DecimalDigits 的数学值乘以 10^-n））乘以 10^e，这里的 n 是 第二个 DecimalDigits 中的字符个数，e 是 ExponentPart 的数学值。
- StrUnsignedDecimalLiteral ::: . DecimalDigits 的数学值是 DecimalDigits 的数学值乘以 10^-n，这里的 n 是 DecimalDigits 中的字符个数。
- StrUnsignedDecimalLiteral ::: . DecimalDigits ExponentPart 的数学值是 DecimalDigits 的数学值乘以 10^e-n，这里的 n 是 DecimalDigits 中的字符个数，e 是 ExponentPart 的数学值。
- StrUnsignedDecimalLiteral ::: DecimalDigits 的数学值是 DecimalDigits 的数学值。
- StrUnsignedDecimalLiteral ::: DecimalDigits ExponentPart 的数学值是 DecimalDigits 的数学值乘以 10^e，这里的 e 是 ExponentPart 的数学值。

一旦字符串数值常量的数学值被精确地确定，接下来就会被舍入为数值类型的一个值。如果数学值是 0，那么舍入值为 +0，否则如果字符串数值常量中第一个非空白字符是 ‘-’，舍入值为 -0。对于其它情况，舍入后的值必须是其数学值的数字值（6.1.6定义）。如果该字面量包含一个 StrUnsignedDecimalLiteral 且该字面量大于 20 位有效数字，则此数字的值是下面两种之一：一是将其 20 位之后的每个数位用 0 替换，产生此字符串解析出的数学值的数字值；二是将其 20 位之后的每个数位用 0 替换，并在第 20 位有效数字加一，产生此字符串解析出的数学值的数字值。判断一个数位是否为有效数位，首先它不能是 ExponentPart 的一部分，且

- 它不是 **0**；或
- 它的左边是一个非零值，右边是一个不在 *ExponentPart* 中的非零值。

### 7.1.4 ToInteger ( argument ) <div id="sec-tointeger"></div>

**ToInteger** 抽象操作将其参数转换为整数值，此运算符功能如下所示：

1. 令 number 为 ? ToNumber(argument)。
2. 如果 number 是 NaN 则返回 +0。
3. 如果 number 是 +0、 -0、 +∞ 或 -∞ 则返回 number。
4. 令 int 符号与 number 相同，大小为 floor(abs(number))。

### 7.1.5 ToInt32 ( argument ) <div id="sec-toint32"></div>

**ToInt32** 抽象操作将其参数转换为 在 **-2^31** 到 **2^31-1** 闭区间内的 **2^32** 个整数中的任意一个。 此运算符功能如下所示：

1. 令 number 为 ? ToNumber(argument)。
2. 如果 number 是 NaN、 +0、 -0、 +∞ 或 -∞ 则返回 +0。
3. 令 int 符号与 number 相同，大小为 floor(abs(number))。
4. 令 int32bit 为 int modulo 2^32。
5. 如果 int32bit  ≥  2^31 则返回 int32bit-2^32 否则返回 int32bit。

> 注意：上面对 **ToInt32**
>
> - **ToInt32** 抽象操作是幂等的：对同一个值调用一次和调用两次这个操作会得到同样的结果。
> - 对于任意的 **x** 值，**ToInt32**(**ToUint32**(**x**)) 总是等价于 **ToInt32**(**x**)。（它延续了后者将 **+∞** 和 **-∞** 映射到 **+0** 的性质）
> - **ToInt32** 会将 **-0** 映射到 **+0**。

### 7.1.6 ToUint32 ( argument ) <div id="sec-touint32"></div>

**ToUint32** 抽象操作将其参数转换为 在 **0** 到 **2^32-1** 闭区间内的 **2^32** 个整数中的任意一个。 

此运算符功能如下所示：

1. 令 number 为 ? ToNumber(argument)。
2. 如果 number 是 NaN、 +0、 -0、 +∞ 或 -∞ 则返回 +0。
3. 令 int 符号与 number 相同，大小为 floor(abs(number))。
4. 令 int32bit 为 int modulo 2^32。
5. 返回 int32bit。

> 注：上面对 **ToUint32**
>
> - **ToUint32** 与 **ToInt32** 之间唯有 **第5步** 不同。
> - **ToUint32** 抽象操作是幂等的：对同一个值调用一次和调用两次这个操作会得到同样的结果。
> - 对于任意的 **x** 值，**ToUint32**(**ToInt32**(**x**)) 总是等价于 **ToUint32**(**x**)。（它延续了后者将 **+∞** 和 **-∞** 映射到 **+0** 的性质）
> - **ToUint32** 会将 **-0** 映射到 **+0**。



### 7.1.7 ToInt16 ( argument ) <div id="sec-toint16"></div>

**ToInt16** 抽象操作将其参数转换为 在 **-32768** 到 **32767** 闭区间内的 **2^16** 个整数中的任意一个。 

此运算符功能如下所示：

1. 令 number 为 ? ToNumber(argument)。
2. 如果 number 是 NaN、 +0、 -0、 +∞ 或 -∞ 则返回 +0。
3. 令 int 符号与 number 相同，大小为 floor(abs(number))。
4. 令 int16bit 为 int modulo 2^16；
5. 如果 int16bit  ≥  2^16 则返回 int16bit-2^16 否则返回 int16bit。

### 7.1.8 ToUint16 ( argument ) <div id="sec-tounit16"></div>

**ToUint16** 抽象操作将其参数转换为 在 **0** 到 **2^16-1** 闭区间内的 **2^16** 个整数中的任意一个。

此运算符功能如下所示：

1. 令 number 为 ? ToNumber(argument)。
2. 如果 number 是 NaN、 +0、 -0、 +∞ 或 -∞ 则返回 +0。
3. 令 int 符号与 number 相同，大小为 floor(abs(number))。
4. 令 int16bit 为 int modulo 2^16。
5. 返回 int16bit。

> 注意：上面对于ToUint16
>
> ToUnit32与ToUinit16唯有第4步用2^16代替2^32不同
>
> ToUinit16会将 -0 映射到 +0

### 7.1.9 ToInt8 ( argument ) <div id="sec-toint8"></div>

**ToInt8** 抽象操作将其参数转换为 在 **-128** 到 **127** 闭区间内的 **2^8** 个整数中的任意一个。

此运算符功能如下所示：

1. 令 number 为 ? ToNumber(argument)。
2. 如果 number 是 NaN、 +0、 -0、 +∞ 或 -∞ 则返回 +0。
3. 令 int 符号与 number 相同，大小为 floor(abs(number))。
4. 令 int8bit 为 int modulo 2^8；
5. 如果 int8bit  ≥  2^7 则返回 int8bit-2^8 否则返回 int8bit。

### 7.1.10 ToUint8 ( argument ) <div id="sec-touint8"></div>

**ToUint16** 抽象操作将其参数转换为 在 **0** 到 **255** 闭区间内的 **2^8 个整数中的任意一个。

此运算符功能如下所示：

1. 令 number 为 ? ToNumber(argument)。
2. 如果 number 是 NaN、 +0、 -0、 +∞ 或 -∞ 则返回 +0。
3. 令 int 符号与 number 相同，大小为 floor(abs(number))。
4. 令 int8bit 为 int modulo 2^8。
5. 返回 int8bit。

### 7.1.11 ToUint8Clamp ( argument ) <div id="sec-touint8clamp"></div>

**ToUint16** 抽象操作将其参数转换为 在 **0** 到 **255** 闭区间内的 **2^8 个整数中的任意一个。

此运算符功能如下所示：

1. 令 number 为 ? ToNumber(argument)。
2. 如果 number 是 NaN、 +0、 -0、 +∞ 或 -∞ 则返回 +0。
3. 如果 number ≤ 0，返回 +0。
4. 如果 number ≥ 255，返回 255
5. 令 f 为 floor(number)
6. 如果 f + 0.5 < number, 返回 f + 1。
7. 如果 number < f + 0.5, 返回 f。
8. 如果 f 是 奇数, 返回 f + 1。
9. 返回 f

### 7.1.12 ToString ( argument ) <div id="sec-tostring"></div>

ToString 运算符根据下表将其参数转换为字符串类型的值：

| 输入类型 | 结果                                                         |
| -------- | ------------------------------------------------------------ |
| 未定义   | **"undefined"**                                              |
| 空值     | **"null"**                                                   |
| 布尔值   | 如果参数是 **true**，那么结果为 **"true"**。如果参数是 **false**，那么结果为 **"false"**。 |
| 数值     | 返回 NumberToString(argument)                                |
| 字符串   | 返回输入的参数（不转换）。                                   |
| A        | 抛出一个TypeError异常                                        |
| 对象     | 应用下列步骤：<br />1. 令 primValue 为 ? ToPrimitive(argument, hint String)。<br />2. 返回 ? **ToString**(primValue)。 |

#### 7.1.12.1 NumberToString ( m ) <div id="sec-numbertostring"></div>

ToString 抽象操作将数字 m 转换为字符串格式的给出如下所示：

1. 果 m 是 **NaN**，返回字符串 **"NaN"**。

2. 如果 m 是 **+0** 或 **-0**，返回字符串 **"0"**。

3. 如果 m 小于零，返回连接 **"-"** 和  ! NumberToString(-m) 。

4. 如果 m 无限大，返回字符串 **"Infinity"**。

5. 否则，令 n、k 和 s 皆为整数，且满足 k ≥ **1** 且 **10^(k-1)** ≤ s < **10^k** 、 s×**10(n-k)** 的数字值为 m 且 k 足够小。注意 k 是 s 的十进制位数。s 不能被 **10** 整除，且 s 的至少要求的有效数字位数不一定要被这些标准唯一确定。

6. 如果 k ≤ n ≤ **21**，返回以下字符串连接：

   - 由 k 个 s 在十进制表示中的数字组成的字符串（有序的，开头没有零）

   - n-k 个 码元为0x0030（数字"0"）字符。

7. 如果 **0** < n ≤ **21**，返回以下字符串连接：

   - 由 s 在十进制表示中的、最多 n 个有效数字组成的字符串
   - 一个码元为0x002E（小数点 **"."**）
   - 余下的 k-n 个 s 在十进制表示中的数字。

8. 如果 **-6** < n ≤ **0**，返回以下字符串连接：

   - 一个码元为0x0030（数字"0"），
   - 一个码元为0x002E（小数点 **"."**），
   - -n 个码元为0x0030（数字"0"）字符，
   - k 个 s 在十进制表示中的数字。

9. 否则，如果 k = **1**，返回以下字符串连接：

   - 由单个数字 s 组成的码元，
   - 码元为0x002E(小写字母 **"e"**)，
   - 根据 n-**1** 是正或负，码元为0x002B（加号 **"+"** ）或码元为0x002D（减号 **"-"** ），
   - 整数 abs(n-**1**) 的十进制表示（没有前置的零）。

10. 返回以下字符串连接：

    - 由 s 在十进制表示中的、最多的有效数字组成的字符串，
    - 一个码元为0x002E（小数点 **"."**），
    - 余下的是 k-**1** 个 s 在十进制表示中的数字，
    - 码元为0x002E(小写字母 **"e"**)，
    - 根据 n-**1** 是正或负，码元为0x002B（加号 **"+"** ）或码元为0x002D（减号 **"-"** ），
    - 整数 abs(n-**1**) 的十进制表示（没有前置的零）。

> 注1：下面的评语可能对指导实现有用，但不是本标准的常规要求。
>
> - 如果 **x** 是除 **-0** 以外的任一数字值，那么 **ToNumber**(**ToString**(**x**)) 与 **x** 是完全相同的数字值。
> - **s** 至少要求的有效数字位数并非总是由 **第5步** 中所列的要求唯一确定。
>
> 注：对于那些提供了比上面的规则所要求的更精确的转换的实现，我们推荐下面这个步骤 5 的可选版本，作为指导：
>
> 5. 否则，令 **n**, **k**, 和 **s** 是整数，使得 **k** ≥ 1, 10^(**k**-1) ≤ **s** < **10^k**，**s**×10^(**n**-**k**) 的数字值是 **m**，且 **k** 足够小。如果有数倍于 **s** 的可能性，选择 **s** × 10^(**n**-**k**) 最接近于 **m** 的值作为 **s** 的值。如果 **s** 有两个这样可能的值，选择是偶数的那个。要注意的是，**k** 是 **s** 在十进制表示中的数字的个数，且 **s** 不被 **10** 整除。
>
> 注2：ECMAScript 的实现者们可能会发现，David M 所写的关于浮点数进行二进制到十进制转换方面的文章和代码很有用：
>
> Gay, David M. Correctly Rounded Binary-Decimal and Decimal-Binary
> Conversions. Numerical Analysis Manuscript 90-10. AT&T Bell
> Laboratories (Murray Hill, New Jersey). November 30, 1990. 在这里取得
> http://ampl.com/REFS/abstracts.html#rounding 。有关的代码在这里
> http://netlib.sandia.gov/fp/dtoa.c 还有
> http://netlib.sandia.gov/fp/g_fmt.c 。这些都可在众多的 netlib 镜像站点上找到。 

### 7.1.13 ToObject ( argument ) <div id="sec-toobject"></div>

**ToObject** 抽象操作根据下表将其参数转换为对象类型的值：

| 输入类型 | 结果                                                         |
| -------- | ------------------------------------------------------------ |
| 未定义   | 抛出 **TypeError** 异常。                                    |
| 空值     | 抛出 **TypeError** 异常。                                    |
| 布尔值   | 创建一个新的 Boolean 对象，其 [[BooleanData]] 内部插槽被设为该参数的值。 |
| 数值     | 创建一个新的 Number 对象，其 [[NumberData]] 内部插槽被设为该参数的值。 |
| 字符串   | 创建一个新的 String 对象，其 [[StringData]] 内部插槽被设为该参数的值。 |
| Symbol   | 创建一个新的 Symbol对象，其 [[SymbolData]] 内部插槽被设为该参数的值 |
| 对象     | 结果是输入的参数（不转换）。                                 |

### 7.1.14 ToPropertyKey ( argument ) <div id="sec-topropertykey"></div>

抽象操作ToPropertyKey将参数转换为可用作属性键的值。执行以下步骤：

1. 令 key 成为 ? ToPrimitive(argument, hint String)。
2. 如果 Type(key) 是 Symbol，那么
   1. 返回 key。

3. 返回 ! ToString(key) 

### 7.1.15 ToLength ( argument ) <div id="sec-tolength"></div>

抽象操作ToLength将参数转换为适合用作数组对象长度的整数。执行以下步骤：

1. 令 len 成为 ? ToInterger(argument)。
2. 如果 len ≤ +0, 返回 +0.  

3. 返回 ! min(len, 2^53 - 1) 

### 7.1.16 CanonicalNumericIndexString ( argument ) <div id="sec-canonicalnumericindexstring"></div>

如果它是由 ToString 生成或字符串`"-0"`，抽象操作CanonicalNumericIndexString将argument转换为数值。 否则，它返回it returns undefined。 该抽象操作的功能如下: 

1. 断言：Type(argument)是字符串。
2. 如果 argument 是 "-0"， 返回 -0。
3. 令 n 为 ! ToNumber(argument) 。
4. 如果 SameValue( ! ToString(n), argument) 是 false, 返回 undefined。
5. 返回 n。

规范数字字符串是CanonicalNumericIndexString抽象操作不会返回undefined的任何String值。

### 7.1.17 ToIndex ( value ) <div id="sec-toindex"></div>

如果ToIndex是有效的整数索引值，则抽象操作ToIndex返回转换为数字值的value参数。此抽象操作的功能如下：

1. 如果 value 是 undfined ，那么
   1. 令 index 为 0。

2. 否则
   1. 令 integerIndex 为 ? ToInteger(value) 。
   2. 如果 integerIndex < 0，抛出RangeError异常。
   3. 令 index 为 ! ToLength(integerIndex) 。
   4. 如果 SameValueZero(integerIndex, index) 是 false, 抛出 RangeError 异常。

3. 返回 index。

## 7.2 测试和比较操作 <div id="sec-testing-and-comparison"></div>

### 7.2.1 RequireObjectCoercible ( argument ) <div id="sec-requireobjectcoerible"></div>

如果参数是无法使用ToObject转换为Object的值，则抽象操作RequireObjectCoercible会引发错误。由表13定义：

| 输入类型 | 结果                        |
| -------- | --------------------------- |
| 未定义   | 抛出一个 **TypeError** 异常 |
| 空值     | 抛出一个 **TypeError** 异常 |
| 布尔值   | 返回 argument               |
| 数值     | 返回 argument               |
| 字符串   | 返回 argument               |
| 对象     | 返回 argument               |

### 7.2.2 IsArray ( argument ) <div id="sec-isarray"></div>

抽象操作IsArray采用一个实参作为参数，并执行以下步骤：

1. 如果 Type(argument)不是对象，返回 false。
2. 如果 argument 是一个数组怪异对象，返回 true。
3. 如果 argument 是一个代理怪异对象，那么
   1. 如果 argument.[[ProxyHandler]]  是 null，抛出TypeError异常。
   2. 令 target 为  argument.[[ProxyTarget]]  
   3. 返回 ? IsArray(target). 

4， 返回 false。

### 7.2.3 IsCallable ( argument ) <div id="sec-iscallable"></div>

抽象操作IsCallable，用于确定ECMAScript语言值的参数是否是具有[[Call]]内部方法的可调用函数。

1. 如果 Type(argument) 不是Object，返回 false。
2. 如果 argument 有 [[Call]] 内部方法，返回 true。
3. 返回 false。

### 7.2.4 IsConstructor ( argument ) <div id="sec-isconstructor"></div>

抽象操作IsConstructor，用于确定ECMAScript语言值的参数是否是具有[[Construct]]内部方法的函数对象。

1. 如果 Type(argument) 不是对象，返回 false。
2. 如果 argument 有 [[Construct]] 内部方法，返回 true。
3. 返回 false

### 7.2.5 IsExtensible ( O ) <div id="sec-isextensible"></div>

抽象操作IsExtensible，用于确定是否可以将其他属性添加到O对象。返回一个布尔值。此抽象操作执行以下步骤：

1. 断言：Type(O)是对象。
2. 返回 ? O.[[IsExtensible\]\]() 

### 7.2.6 IsInteger ( argument ) <div id="sec-isinteger"></div>

抽象操作IsInteger，用于确定参数是否为有限整数。

1. 如果 Type(argument) ，返回 false。
2. 如果 argument 是 NaN, +∞, or -∞, 返回 false。
3. 如果 floor(abs(argument)) ≠ abs(argument)，返回 false

### 7.2.7 IsPropertyKey ( argument ) <div id="sec-ispropertykey"></div>

抽象操作IsPropertyKey，用于确定必须为ECMAScript语言值的参数是否可以用作属性键的值。

1. 如果 Type(argument) 是字符串，返回 true。
2. 如果 Type(argument) 是Symbol，返回 true。
3. 返回 false。

### 7.2.8 IsRegExp ( argument ) <div id="sec-isregexp"></div>

带参数arguments的抽象操作IsRegExp执行以下步骤：

1. 如果 Type(argument) 不是对象，返回 false。
2. 令 matcher 成为 ? Get(argument, @@match)。
3. 如果 matcher 不是 undefined，返回 ToBoolean(matcher)。
4. 如果 argument 有 [[RegExpMatcher]] 内部插槽，返回 true。
5. 返回 false

### 7.2.9 IsStringPrefix ( p, q ) <div id="sec-isstringprefix"></div>

抽象操作IsStringPrefix，确定字符串 p 是否为字符串 q 的前缀。

1. 断言：Type(p)是字符串。
2. 断言：Type(q)是字符串。
3. 如果q可以是p和其他一些字符串r的字符串串联，则返回true。否则，返回false。
4. 注意：任何字符串都是其自身的前缀，因为r可能是空字符串。

### 7.2.10 SameValue ( x, y ) <div id="sec-samevalue"></div>

内部比较抽象操作 SameValue ( x , y )（其中x和y是ECMAScript语言值）产生true或false。这样的比较执行如下：

1. 如果 Type(x) 不同于 Type(y)，返回 false。
2. 如果 Type(x) 是数字，那么
   1. 如果 x 是 NaN，并且 y 是 NaN，返回 true。
   2. 如果 x 是 +0，并且 y 是 -0，返回 false。
   3. 如果 x 是 -0，并且 y 是 +0，返回 false。
   4. 如果 x 与 y 相同的 Number 值，返回 true。
   5. 返回 false

3. 返回  SameValueNonNumber(x, y)。

> 注：该算法与严格平等比较算法的区别在于对有符号零和NaN的处理。

### 7.2.11 SameValueZero ( x, y ) <div id="sec-samevaluezero"></div>

内部比较抽象操作SameValueZero（x，y）（其中x和y是ECMAScript语言值）产生true或false。这样的比较执行如下：

1. 如果 Type(x) 不同于 Type(y)，返回 false。
2. 如果 Type(x) 是数字，那么
   1. 如果 x 是 NaN，并且 y 是 NaN，返回 true。
   2. 如果 x 是 +0，并且 y 是 -0，返回 true。
   3. 如果 x 是 -0，并且 y 是 +0，返回 true。
   4. 如果 x 与 y 相同的 Number 值，返回 true。
   5. 返回 false

3. 返回  SameValueNonNumber(x, y)。

> 注： SameValueZero 与 SameValue 的区别在于 +0 and -0 的处理。

### 7.2.12 SameValueNonNumber ( x, y ) <div id="sec-samevaluenonnumber"></div>

内部比较抽象操作SameValueNonNumber（x，y）（其中x和y都不是Number值）产生true或false。这样的比较执行如下：

1. 断言：Type(x)不是 Number。
2. 断言：Type(x)和Type(y)相同。
3. 如果 Type(x) 是 Undefined，返回 true。
4. 如果 Type(x) 是 Null，返回 true。
5. 如果 Type(x) 是 String，那么
   1. 如果x和y是完全相同的码元序列（在相同的索引处具有相同的长度和相同的码元），则返回true；否则，返回false

6. 如果 Type(x) 是 Boolean，那么
   1. 如果 x 和 y 都是 true，或者都是 false，返回 true；否则，返回 false。

7. 如果 Type(x) 是Symbol，那么
   1. 如果 x 和 y 是一样的Symbol值，返回 true；否则，返回 false。

8. 如果 x 和 y 是一样的 Object 值，返回 true；否则，返回 false。

### 7.2.13 抽象关系比较 <div id="sec-abstract-relation-comparison"></div>

比较 x < y（其中 x 和 y 是值）会产生 true，false 或 undefined（这表明至少一个操作数是NaN）。除x和y外，该算法还使用名为LeftFirst的布尔标志作为参数。该标志用于控制对x和y执行具有潜在可见副作用的操作的顺序。这是必要的，因为ECMAScript指定了表达式的从左到右执行。LeftFirst的默认值为true，表示x参数对应于y参数对应表达式左侧出现的表达式。如果LeftFirst为false，则情况相反，必须在x之前的y上执行操作。这样的比较执行如下：

1. 如果 LeftFirst 标志为 true，那么
   1. 令 px 为 ? ToPrimitive(x, hint Number) 。
   2. 令 py 为 ? ToPrimitive(y, hint Number) 。

2. 否则，左到右的执行顺序需要反转，
   1. 令 py 为 ? ToPrimitive(y, hint Number) 。
   2. 令 px 为 ? ToPrimitive(x, hint Number) 。

3. 如果 Type(px) 是 String，同时 Type(py) 是 String，那么
   1. 如果 IsStringPrefix(py, px) 是 true，返回 false。
   2. 如果 IsStringPrefix(px, py) 是 true，返回 true。
   3. 令 k 为最小的非负整数，使得 px 内索引k处的码元与 py 内索引k处的码元不同。（必须有一个k，因为 String 都不是另一个的前缀。）
   4. 令 m 为整数，它是 px 中的索引 k 处的码元的数值。
   5. 令 n 为整数，它是 py 中索引 k 处的代码单元的数值。 
   6. 如果 m < n ，返回 true。否则，返回 false。

4. 否则，
   1. 注：因为px和py是原始值，所以执行顺序并不重要。
   2. 令 nx 为 ? ToNumber(px)。
   3. 令 ny 为 ? ToNumber(py)。
   4. 若 nx 是 NaN, 返回 undefined.
   5. 若 ny 是 NaN, 返回 undefined.
   6. 若 nx 和 ny 是同一个 Number 值, 返回 false.
   7. 若 nx 是 +0 并且 ny 也是 -0, 返回 false.
   8. 若 nx 是 -0 并且 ny 也是 +0, 返回 false.
   9. 若 nx 是 +∞, 返回 false.
   10. 若 ny 是 +∞, 返回 true.
   11. 若 ny 是 -∞, 返回 false.
   12. 若 nx 是 -∞, 返回 true.
   13. 如果 nx 的数学值小于 ny 的数学值（请注意，这些数学值都是有限的且不都是零），则返回 true。否则，返回 false

> 注 1：第3步与第7步的区别在于，加法运算符+（12.8.3）的算法使用逻辑和运算而不是逻辑或运算。
>
> 注 2：字符串使用简单的码元值序列字典顺序来做比较。规范没有使用更复杂的，面向语义的字符或字符串相等性定义以及Unicode规范中定义的整理顺序。因此，根据Unicode标准规范相等的字符串值可以验证为不相等。实际上，该算法假定两个字符串都已经处于规范化形式。此外，请注意，对于包含补充字符的字符串，UTF-16码元序列的字典顺序与码点序列的字典顺序不同。

### 7.2.14 抽象相等比较 <div id="sec-abstract-equality-comparison"></div>

比较x == y（其中x和y是值）产生true或false。这样的比较执行如下：

1. 如果 Type(x) 与 Type(y) 相等，那么
   1. 返回执行严格相等比较x === y的结果。
2. 如果 x 是 null，同时 y 是 undefined，返回 true。
3. 如果 x 是 undefined，同时 y 是 null，返回 true。
4. 如果 Type(x) 是 Number 同时 Type(y) 是 String, 返回 x == ! ToNumber(y) 的比较结果。
5. 如果 Type(x) 是 String 同时 Type(y) 是 Number, 返回 ! ToNumber(x) == y 的比较结果。
6. 如果 Type(x) 是 Boolean，返回 ! ToNumber(x) == y 的比较结果。
7. 如果 Type(x) 是 Boolean，返回 x == ! ToNumber(y) 的比较结果。
8. 如果 Type(x) 是 String，Number，或者Symbol，并且 Type(y) 是Object，返回 x == ToPrimitive(y) 的比较结果。
9. 如果 Type(x) 是 Object，同时Type(y) 是 String，Number，或者Symbol，返回 ToPrimitive(x) == y 的比较结果
10. 返回 false。

### 7.2.15 严格相等比较 <div id="sec-strict-equality-comparison"></div>

比较x === y（其中x和y是值）会产生true或false。这样的比较执行如下：

1. 如果 Type(x) 与 Type(y) 不同，返回 false。
2. 如果 Type(x) 是 Number，那么
   1. 如果 x 是 NaN，返回 false。
   2. 如果 y 是 NaN，返回 false。
   3. 如果 x 与 y Number值相同，返回 true。
   4. 如果 x 是 +0 同时 y 是 -0，返回 true。
   5. 如果 x 是 -0 同时 y 是 +0，返回 true。
   6. 返回 false。

3. 返回 SameValueNonNumber(x, y) 

> 注：该算法与Same Value算法的区别在于对有符号零和NaN的处理。

## 7.3 对象的操作 <div id="sec-operations-on-objects"></div>

### 7.3.1 Get ( O, P ) <div id="sec-get"></div>

抽象操作Get用于检索对象的特定属性的值。使用参数O和P调用该操作，其中O是对象，P是属性键。此抽象操作执行以下步骤：

1. 断言：Type(O) 是 Object。
2. 断言：IsPropertyKey(P) 是 true。
3. 返回 O.\[\[Get]](P, O)。

### 7.3.2 GetV ( V, P ) <div id="sec-getv"></div>

抽象操作GetV用于检索ECMAScript语言值的特定属性的值。如果该值不是对象，则使用适合该值类型的包装对象执行属性查找。使用参数V和P调用该操作，其中V是值，P是属性键。此抽象操作执行以下步骤：

1. 断言：IsPropertyKey(P) 是 true。
2. 令 O 为 ? ToObject(V) 。
3. 返回 O.\[\[Get]](P, V)。

### 7.3.3 Set ( O, P, V, Throw ) <div id="sec-set"></div>

抽象操作Set用于设置对象的特定属性的值。使用参数O，P，V和Throw调用该操作，其中O是对象，P是属性键，V是该属性的新值，并且Throw是布尔值标志。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: IsPropertyKey(P) 是 true.
3. 断言: Type(Throw) 是 Boolean.
4. 令 success 为 ? O.\[\[Set]](P, V, O).
5. 如果 success 是 false 同时 Throw 是 true, 抛出 TypeError 异常.
6. 返回 success.

### 7.3.4 CreateDataProperty ( O, P, V ) <div id="sec-createdataproperty"></div>

抽象操作CreateDataProperty用于创建对象的新自身属性。使用参数O，P和V调用该操作，其中O是对象，P是属性键，V是属性的值。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: IsPropertyKey(P) 是 true.
3. 令 newDesc 为 the PropertyDescriptor{[[Value]]: V, [[Writable]]: true, [[Enumerable]]: true, [[Configurable]]: true}.
4. 返回 ? O.\[\[DefineOwnProperty]](P, newDesc).

> 注：此抽象操作将创建一个属性，该属性的属性设置为与ECMAScript语言赋值运算符创建的属性所使用的默认值相同的默认值。通常，该属性将不存在。如果它确实存在并且不可配置，或者O不可扩展，则[[DefineOwnProperty]]将返回false。

### 7.3.5 CreateMethodProperty ( O, P, V ) <div id="sec-createmethodproperty"></div>

抽象操作CreateMethodProperty用于创建对象的新自身属性。使用参数O，P和V调用该操作，其中O是对象，P是属性键，V是属性的值。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: IsPropertyKey(P) 是 true.
3. 令 newDesc 为 PropertyDescriptor{[[Value]]: V, [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: true}.
4. 返回 ? O.\[\[DefineOwnProperty]](P, newDesc).

> 注：此抽象操作创建一个属性，该属性的属性设置为与内置方法和使用类声明语法定义的方法相同的默认值。通常，该属性将不存在。如果它确实存在并且不可配置，或者O不可扩展，则[[DefineOwnProperty]]将返回false。

### 7.3.6 CreateDataPropertyOrThrow ( O, P, V ) <div id="sec-createdatapropertyorthrow"></div>

抽象操作CreateDataPropertyOrThrow用于创建对象的新自身属性。如果无法执行请求的属性更新，则会引发TypeError异常。使用参数O，P和V调用该操作，其中O是对象，P是属性键，V是属性的值。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: IsPropertyKey(P) 是 true.
3. 令 success 为 ? CreateDataProperty(O, P, V).
4. 如果 success 是 false, 抛出 TypeError 异常.
5. 返回 success.

> 注：此抽象操作将创建一个属性，该属性的属性设置为与ECMAScript语言赋值运算符创建的属性所使用的默认值相同的默认值。通常，该属性将不存在。如果确实存在并且不可配置，或者O不可扩展，则[[DefineOwnProperty]]将返回false，导致此操作引发TypeError异常。

### 7.3.7 DefinePropertyOrThrow ( O, P, desc ) <div id="sec-definepropertyorthrow"></div>

抽象操作DefinePropertyOrThrow用于以某种方式调用对象的[[DefineOwnProperty]]内部方法，如果无法执行请求的属性更新，该方式将引发TypeError异常。使用参数O，P和desc调用该操作，其中O是对象，P是属性键，而desc是该属性的属性描述符。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: IsPropertyKey(P) 是 true.
3. 令 success 为 ? O.\[\[DefineOwnProperty]](P, desc).
4. 如果 success 是 false, 抛出 TypeError 异常.
5. 返回 success.

### 7.3.8 DeletePropertyOrThrow ( O, P ) <div id="sec-deletepropertyorthrow"></div>

抽象操作DeletePropertyOrThrow用于删除对象的特定自身属性。如果该属性不可配置，它将引发异常。使用参数O和P调用该操作，其中O是对象，P是属性键。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: IsPropertyKey(P) 是 true.
3. 令 success 为 ? O.\[\[Delete]](P).
4. 如果 success 是 false, 抛出 TypeError 异常.
5. 返回 success.

### 7.3.9 GetMethod ( V, P ) <div id="sec-getmethod"></div>

当期望该属性的值是一个函数时，抽象操作GetMethod用于获取ECMAScript语言值的特定属性的值。使用参数V和P调用该操作，其中V是ECMAScript语言值，P是属性键。此抽象操作执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 func 为 ? GetV(V, P).
3. 如果 func 是 undefined 或 null, 返回 undefined.
4. 如果 IsCallable(func) 是 false, 抛出 TypeError 异常.
5. 返回 func.

### 7.3.10 HasProperty ( O, P ) <div id="sec-hasproperty"></div>

抽象操作HasProperty用于确定对象是否具有具有指定属性键的属性。该属性可以是自己的或继承的。返回一个布尔值。使用参数O和P调用该操作，其中O是对象，P是属性键。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: IsPropertyKey(P) 是 true.
3. 返回 ? O.\[\[HasProperty]](P).

### 7.3.11 HasOwnProperty ( O, P ) <div id="sec-hasownproperty"></div>

抽象操作HasOwnProperty用于确定对象是否具有带有指定属性键的自己的属性。返回一个布尔值。使用参数O和P调用该操作，其中O是对象，P是属性键。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: IsPropertyKey(P) 是 true.
3. 令 desc 为 ? O.\[\[GetOwnProperty]](P).
4. 如果 desc 是 undefined, 返回 false.
5. 返回 true.

### 7.3.12 Call ( F, V [ , argumentsList ] ) <div id="sec-call"></div>

抽象操作Call用于调用函数对象的[[Call]]内部方法，使用参数F，V和可选的argumentsList调用该操作，其中F是函数对象，V是ECMAScript语言值，它是[[Call]]的this值，argumentsList是传递给对应的参数的值内部方法。如果argumentsList不存在，则将新的空List用作其值。此抽象操作执行以下步骤：

1. 如果 argumentsList 不存在, 令 argumentsList 为 一个新的空数组.
2. 如果 IsCallable(F) 是 false, 抛出 TypeError 异常.
3. 返回 ? F.\[\[Call]](V, argumentsList).

### 7.3.13 Construct ( F [ , argumentsList [ , newTarget ] ] ) <div id="sec-construct"></div>

抽象操作Construct用于调用函数对象的[[Construct]]内部方法。该操作使用参数F以及可选的argumentsList和newTarget调用，其中F是函数对象。argumentsList和newTarget是要作为内部方法的相应参数传递的值。如果argumentsList不存在，则将新的空List用作其值。如果newTarget不存在，则F用作其值。此抽象操作执行以下步骤：

1. 如果 newTarget was 不存在, 令 newTarget 为 F.
2. 如果 argumentsList 不存在, 令 argumentsList 为 一个新的空数组.
3. 断言: IsConstructor(F) 是 true.
4. 断言: IsConstructor(newTarget) 是 true.
5. 返回 ? F.\[\[Construct]](argumentsList, newTarget).

> 注：如果不存在newTarget，则此操作等效于：new F（... argumentsList）

### 7.3.14 SetIntegrityLevel ( O, level ) <div id="sec-setintegritylevel"></div>

抽象操作SetIntegrityLevel用于修复对象自身属性的集合。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: level 是 "sealed" 或者 "frozen".
3. 令 status 为 ? O.\[\[PreventExtensions]]().
4. 如果 status 是 false, 返回 false.
5. 令 keys 为 ? O.\[\[OwnPropertyKeys]]().
6. 如果 level 是 "sealed", 那么
   1. 对于每一个keys的k
      1. 执行 ? DefinePropertyOrThrow(O, k, PropertyDescriptor{[[Configurable]]: false}).
7. 否则，level 是 "frozen",
   1. 对于所有keys的k
      1. 令 currentDesc 为 ? O.\[\[GetOwnProperty]](k).
      2. 如果 currentDesc 不是 undefined, 那么
         1. 如果 IsAccessorDescriptor(currentDesc) 是 true, 那么
            1. 令 desc 为 PropertyDescriptor{[[Configurable]]: false}.
         2. 否则,
            1. 令 desc 为 the PropertyDescriptor { [[Configurable]]: false, [[Writable]]: false }.
         3. 执行 ? DefinePropertyOrThrow(O, k, desc) 
8. 返回 true.

### 7.3.15 TestIntegrityLevel ( O, level ) <div id="sec-testintegritylevel"></div>

抽象操作TestIntegrityLevel用于确定对象自身属性的集合是否固定。此抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 断言: level 是 "sealed" 或者 "frozen".
3. 令 status 为 ? IsExtensible(O).
4. 如果 status 是 true, 返回 false.
5. 注意：如果对象是可扩展的，则不会检查其任何属性。
6. 令 keys 为 ? O.\[\[OwnPropertyKeys]](). 
7. 对于每一个keys的k
   1. 令 currentDesc 为 ? O.\[\[GetOwnProperty]](k) .
   2. 如果 currentDesc 不是 undefined, 那么
      1. 如果 currentDesc.[[Configurable]] 是 true, 返回 false
      2.  如果 level 是 "frozen" 并且 IsDataDescriptor(currentDesc) 是 true, 那么
         1. 如果 currentDesc.[[Writable]] 是 true, 返回 false.
8. 返回 true.

### 7.3.16 CreateArrayFromList ( elements ) <div id="sec-createarrayfromlist"></div>

抽象操作CreateArrayFromList用于创建一个Array对象，其元素由List提供。此抽象操作执行以下步骤：

1. 断言: elements是一个List，其元素均为ECMAScript语言值。
2. 令 array 为 ! ArrayCreate(0).
3. 令 n 为 0.
4. 对于elements 的每一个 e 
   1. 令 status 为 CreateDataProperty(array, ! ToString(n), e).
   2. 断言: status 是 true.
   3. n 增加 1.
5. 返回 array.

### 7.3.17 CreateListFromArrayLike ( obj [ , elementTypes ] ) <div id="sec-createlistfromarraylick"></div>

抽象操作CreateListFromArrayLike用于创建一个List值，其值由类似数组的对象 obj 的索引属性提供。可选参数elementTypes 是一个 List，其中包含所创建 List 的元素值所允许的ECMAScript语言类型的名称。此抽象操作执行以下步骤：

1. 如果 elementTypes 不存在, 令 elementTypes 为 « Undefined, Null, Boolean, String, Symbol, Number, Object ».
2. 如果 Type(obj) 不是 Object, 抛出 TypeError 异常.
3. 令 len 为 ? ToLength(? Get(obj, "length")).
4. 令 list 为 一个空的List.
5. 令 index 为 0.
6. 重复，直到 index < len
   1. 令 indexName 为 ! ToString(index).
   2. 令 next 为 ? Get(obj, indexName).
   3. 如果 Type(next) 是 不是 elementTypes 元素, 抛出 TypeError 异常.
   4. 将next附加到列表的最后一个元素。
   5. index增加1
7. 返回 list.

### 7.3.18 Invoke ( V, P [ , argumentsList ] ) <div id="sec-invoke"></div>

抽象操作Invoke用于调用ECMAScript语言值的method属性。使用参数V，P和可选的argumentsList调用该操作，其中V既用作属性的查找点，又用作调用的this值，P是属性键，而argumentsList是传递给方法的参数值的列表。如果argumentsList不存在，则将新的空List用作其值。此抽象操作执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 如果 argumentsList 不存在，把 argumentsList 设为 新的空.
3. 令 func 为 ? GetV(V, P).
4. 返回 ? Call(func, V, argumentsList).

### 7.3.19 OrdinaryHasInstance ( C, O ) <div id="sec-ordinaryhasinstance"></div>

抽象操作OrdinaryHasInstance实现用于确定对象O是否从构造函数C提供的实例对象继承路径继承的默认算法。该抽象操作执行以下步骤：

1. 如果 IsCallable(C) 是 false, 返回 false.
2. 如果 C 有 [[BoundTargetFunction]] 内置插槽, 那么
   1. 令 BC 为 C 的 [[BoundTargetFunction]] 内置插槽值。
   2. 返回 ? InstanceofOperator(O, BC).
3. 如果 Type(O) 不是 Object, 返回 false.
4. 令 P 为 ? Get(C, "prototype").
5. 如果 Type(P) 不是 Object, 抛出 TypeError 异常.
6. 重复
   1. 令 O 为 ? O.\[\[GetPrototypeOf]]().
   2. 如果 O 是 null, 返回 false.
   3. 如果 SameValue(P, O) 是 true, 返回 true.

### 7.3.20 SpeciesConstructor ( O, defaultConstructor ) <div id="sec-speciesconstructor"></div>

抽象操作SpeciesConstructor用于检索应用于创建从参数对象O派生的新对象的构造函数。defaultConstructor参数是在从O开头找不到构造函数@@ species属性时要使用的构造函数。抽象操作执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 令 C 为 ? Get(O, "constructor").
3. 如果 C 是 undefined, 返回 defaultConstructor.
4. 如果 Type(C) 不是 Object, 抛出 TypeError 异常.
5. 令 S 为 ? Get(C, @@species).
6. 如果 S 是 undefined 或 null, 返回 defaultConstructor.
7. 如果 IsConstructor(S) 是 true, 返回 S.
8. 抛出 TypeError 异常.

### 7.3.21 EnumerableOwnPropertyNames ( O, kind ) <div id="sec-enumerableownpropertynames"></div>

当使用对象O和字符串类型调用抽象操作EnumerableOwnPropertyNames时，将执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 令 ownKeys 为 ? O.\[\[OwnPropertyKeys]]().
3. 令 properties 为一个新的空数组.
4. 对于List顺序中ownKeys的每个key元素
  1. 如果 Type(key) 是 String, 那么
     1. 令 desc 为 ? O.\[\[GetOwnProperty]](key).
     2. 如果 desc 不是 undefined 同时 desc.[[Enumerable]] 是 true , 那么
        1. 如果 kind 是 "key"，添加 key 到 properties。
        2. 否则，
           1. 令 value 为 ? Get(O, key)。
           2. 如果 kind 是 "key"，添加 value 到 properties。
           3. 否则，
              1. 断言：kind 是 "key+value"。
              2. 令 entry 为  CreateArrayFromList(« key, value »). 
              3. 添加 entry 到 properties。
5. 对属性的元素进行排序，以使它们与Iterator产生的相对顺序相同，如果使用O调用EnumerateObjectProperties内部方法，则返回的迭代器相同。
6. 返回  properties .

### 7.3.22 GetFunctionRealm ( obj ) <div id="sec-getfunctionrealm"></div>

参数为obj的抽象操作GetFunctionRealm执行以下步骤：

1. 断言: obj 是一个可被调用的对象.
2. 如果 obj 有 [[Realm]] 内部插槽, 那么
   1. 返回 obj 的 [[Realm]] 内部插槽.
3. 如果 obj 是绑定函数的怪异对象, 那么
    1. 令 target 为 obj.[[BoundTargetFunction]].
  2. 返回 ? GetFunctionRealm(target).
4. 如果 obj 是一个代理怪异对象, 那么
     1. 如果 obj.[[ProxyHandler]]是 null, 抛出 TypeError 异常.
     2. 令 proxyTarget 为 obj.[[ProxyTarget]].
     3. 返回 ? GetFunctionRealm(proxyTarget).
5. 返回当前的Realm记录。

> 注：仅当obj是不具有[[Realm]]内部插槽的非标准函数怪异对象时，才可以执行步骤5。

### 7.3.23 CopyDataProperties ( target, source, excludedItems ) <div id="sec-copydataproperties"></div>

当使用目标，源和排除项目参数调用抽象操作CopyDataProperties时，将执行以下步骤：

1. 断言: Type(target) 是 Object.
2. 断言: excludedItems 是属性键列表.
3. 如果 source 是 undefined 或 null, 返回 target.
4. 令 from 为 ! ToObject(source).
5. 令 keys 为 ? from.\[\[OwnPropertyKeys]]().
6. 对于按List顺序的keys的每个元素nextKey，执行
   1. 令 excluded 为 false.
   2. 对于按List顺序的excludedItems的每个元素e，执行
      1. 如果 SameValue(e, nextKey) 是 true, 那么
         1. 设置 excluded 为 true.
   3. 如果 excluded 是 false, 那么
      1. 令 desc 为 ? from.\[\[GetOwnProperty]](nextKey).
      2. 如果 desc 不是 undefined 同时 desc.[[Enumerable]] 是 true, 那么
         1. 令 propValue 为 ? Get(from, nextKey).
         2. 执行 ! CreateDataProperty(target, nextKey, propValue).
7. 返回 target。

> 注：传入的目标始终是新创建的对象，如果抛出错误，则不能直接访问该对象

## 7.4 迭代器对象的操作 <div id="sec-operations-on-iterator-objects"></div>

请参见通用迭代接口（25.1）。

### 7.4.1 GetIterator ( obj [ , hint [ , method ] ] ) <div id="sec-getiterator"></div>

具有参数obj和可选参数hint和method的抽象操作GetIterator执行以下步骤：

1. 如果 hint 不存在, 设置 hint 为 sync.
2. 断言: hint 是 sync 或 async.
3. 如果 method 不存在, 那么
   1. 如果 hint 是 async, 那么
      1. 设置 method 为 ? GetMethod(obj, @@asyncIterator).
      2. 如果 method 是 undefined, 那么
         1. 令 syncMethod 为 ? GetMethod(obj, @@iterator).
         2. 令 syncIteratorRecord 为 ? GetIterator(obj, sync, syncMethod).
         3. 返回 ? CreateAsyncFromSyncIterator(syncIteratorRecord).
   2. 否则，设置 method 为 ? GetMethod(obj, @@iterator).
4. 令 iterator 为 ? Call(method, obj).
5. 如果 Type(iterator) 不是 Object, 抛出 TypeError 异常.
6. 令 nextMethod 为 ? GetV(iterator, "next").
7. 令 iteratorRecord 为 Record { [[Iterator]]: iterator, [[NextMethod]]: nextMethod, [[Done]]: false }.
8. 返回 iteratorRecord.

### 7.4.2 IteratorNext ( iteratorRecord [ , value ] ) <div id="sec-iteratornext"></div>

具有参数iteratorRecord和可选参数值的抽象操作IteratorNext执行以下步骤：

1. 如果 value 不存在, 那么
   2. 令 result 为 ? Invoke(iterator, "next", « »).
2. 否则,
   1. 令 result 为 ? Invoke(iterator, "next", « value »).
3. 如果 Type(result) 不是 Object, 抛出 TypeError 异常.
4. 返回 result.

### 7.4.3 IteratorComplete ( iterResult ) <div id="sec-iteratorcomplete"></div>

参数为iterResult的抽象操作IteratorComplete执行以下步骤：

1. 断言: Type(iterResult) 是 Object.
2. 返回 ToBoolean(? Get(iterResult, "done")).

### 7.4.4  IteratorValue ( iterResult ) <div id="sec-iteratorvalue"></div>

具有参数iterResult的抽象操作IteratorValue执行以下步骤：

1. 断言: Type(iterResult) 是 Object.
2. 返回 ? Get(iterResult, "value").

### 7.4.5 IteratorStep ( iteratorRecord ) <div id="sec-iteratorstep"></div>

参数为iteratorRecord的抽象操作IteratorStep通过调用iteratorRecord.[[NextMethod]]从iteratorRecord.[[Iterator]]请求下一个值。返回false指示迭代器已到达末尾，或者返回IteratorResult对象（如果有下一个值）。IteratorStep执行以下步骤：

1. 令 result 为 ? IteratorNext(iterator).
2. 令 done 为 ? IteratorComplete(result).
3. 如果 done 是 true, 返回 false.
4. 返回 result.

### 7.4.6 IteratorClose ( iteratorRecord, completion ) <div id="sec-iteratorclose"></div>

带有参数iteratorRecord和complete的抽象操作IteratorClose用于通知迭代器它应该执行在达到其完成状态时通常应执行的任何操作：

1. 断言: Type(iterator) 是 Object.
2. 断言: completion 是一个完成状态记录.
3. 令 iterator 为 iteratorRecord.[[Iterator]]. 
4. 令 return 为 ? GetMethod(iterator, "return").
5. 如果 return 是 undefined, 返回 Completion(completion).
6. 令 innerResult 为 Call(返回, iterator, « »).
7. 如果 completion.[[Type]] 是 throw, 返回 Completion(completion).
8. 如果 innerResult.[[Type]] 是 throw, 返回 Completion(innerResult).
9. 如果 Type(innerResult.[[Value]]) 不是 Object, 抛出 TypeError 异常.
10. 返回 Completion(completion).

### 7.4.7 AsyncIteratorClose ( iteratorRecord, completion ) <div id="sec-asynclteratorclose"></div>

带有参数iteratorRecord和complete的抽象操作AsyncIteratorClose用来通知异步迭代器它应该执行在达到其完成状态时通常应执行的任何操作：

1. 断言: Type(iteratorRecord.[[Iterator]]) 是 Object.
2. 断言: completion 是一个完成记录.
3. 令 iterator 为 iteratorRecord.[[Iterator]].
4. 令 return 为 ? GetMethod(iterator, "return").
5. 如果 return 是 undefined, 返回 Completion(completion).
6. 令 innerResult 为 Call(返回, iterator, « »).
7. 如果 innerResult.[[Type]] 是 normal, 设置 innerResult 为 Await(innerResult.[[Value]]).
8. 如果 completion.[[Type]] 是 throw, 返回 Completion(completion).
9. 如果 innerResult.[[Type]] 是 throw, 返回 Completion(innerResult).
10. 如果 Type(innerResult.[[Value]]) 不是 Object, 抛出 TypeError 异常.
11. 返回 Completion(completion).

### 7.4.8 CreateIterResultObject ( value, done ) <div id="sec-createiterresultobject"></div>

通过执行以下步骤，将抽象操作CreateIterResultObject与参数value和done一起创建一个支持IteratorResult接口的对象：

1. 断言: Type(done) 为 Boolean.
2. 令 obj 为 ObjectCreate(%ObjectPrototype%).
3. 执行 CreateDataProperty(obj, "value", value).
4. 执行 CreateDataProperty(obj, "done", done).
5. 返回 obj.

### 7.4.9 CreateListIteratorRecord ( list ) <div id="sec-createlistiteratorrecord"></div>

具有参数列表的抽象操作CreateListIteratorRecord创建一个Iterator（25.1.1.2）对象记录，其下一个方法返回list的连续元素。它执行以下步骤：

1. 让 iterator 为 ObjectCreate（％IteratorPrototype％，«[[IteratedList]]，[[ListIteratorNextIndex]]»）。
2. 设置 iterator.[[IteratedList]] 为 list.
3. 设置 iterator.[[ListIteratorNextIndex]] 为 0.
4. 令 steps 为 定义在 迭代器 next  (7.4.9.1) 的算法步骤.
5. 令 next 为 CreateBuiltinFunction(steps, « »).
6. 返回 Record { [[Iterator]]: iterator, [[NextMethod]]: next, [[Done]]: false }

> 注：迭代器列表对象永远不能直接由ECMAScript代码访问。

#### 7.4.9.1 ListIterator next ( ) <div id="sec-listiterator-next"></div>

ListIterator next方法是一个标准的内置函数对象（第17节），该对象执行以下步骤：

1. 令 O 为 this 值。
2. 断言：Type(O) 是 Object。
3. 断言：O 有一个 [[IteratorNext]] 内置插槽。
4. 令 list 为 O.\[\[IteratedList]]
5. 令 index 为 O.\[\[ListIteratorNextIndex]].
6. 令 len 为 list的元素个数.
7. 如果 index ≥ len, 那么
   1. 返回 CreateIterResultObject(undefined, true).
8. 设置 O.[[ListIteratorNextIndex]] 为 index+1.
9. 返回 CreateIterResultObject(list[index], false).

