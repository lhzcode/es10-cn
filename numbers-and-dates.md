# 20 数字和日期
## 20.1 Number 对象 <div id="sec-number-objects"></div>
### 20.1.1 Number 构造器 <div id="sec-number-constructor"></div>

Number构造函数：

- 是内部对象％Number％。
- 是全局对象的Number属性的初始值。
- 在作为构造函数调用时创建并初始化一个新的Number对象。
- 当作为函数而不是构造函数调用时执行类型转换。
- 设计为可归类的。它可以用作类定义的extends子句的值。打算继承指定Number行为的子类构造函数必须包括对Number构造函数的超级调用，以使用[[NumberData]]内部插槽创建和初始化子类实例。

#### 20.1.1.1 Number ( value ) <div id="sec-number-constructor-number-value"></div>

当使用参数值调用Number时，将执行以下步骤：

1. 若没有参数传递给此函数调用，令 n 为 +0.
2. 否则，令 n 为 ? ToNumber(value).
3. 若 NewTarget 是 undefined，返回 n.
4. 令 O 为 ? OrdinaryCreateFromConstructor(NewTarget, "%NumberPrototype%", « [[NumberData]] »).
5. 设置 O.[[NumberData]] 为 n.
6. 返回 O.

### 20.1.2 Number 构造器属性 <div id="sec-properties-of-the-number-constructor"></div>

Number构造函数：

- 有一个[[Prototype]]内部插槽，其值是内部对象％FunctionPrototype％。
- 具有以下属性：

#### 20.1.2.1 Number.EPSILON <div id="sec-number.epsilon"></div>

Number.EPSILON的值是1与可以表示为Number值的大于1的最小值之间的差，大约是2.2204460492503130808472633361816 x 10^-16。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.2 Number.isFinite ( number ) <div id="sec-number.isfinite"></div>

当使用一个参数number调用Number.isFinite时，将执行以下步骤：

1. 若 Type(number) 不是 Number，返回 false.
2. 若 number 是 NaN, +∞，或 -∞，返回 false.
3. 否则，返回 true

#### 20.1.2.3 Number.isInteger ( number ) <div id="sec-number.isinteger"></div>

当使用一个参数number调用Number.isInteger时，将执行以下步骤：

1. 若 Type(number) 不是 Number，返回 false.
2. 若 number 是 NaN, +∞，或 -∞，返回 false.
3. 令 integer 为 ! ToInteger(number).
4. 若 integer 不等于 number，返回 false.
5. 否则，返回 true.

#### 20.1.2.4 Number.isNaN ( number ) <div id="sec-number.isnan"></div>

当使用一个参数number调用Number.isNaN时，将执行以下步骤：

1. 若 Type(number) 不是 Number，返回 false.
2. 若 number 是 NaN，返回 true.
3. 否则，返回 false.

> 注：此函数与全局isNaN函数（18.2.3）的不同之处在于，在确定它是否为NaN之前，不会将其参数转换为Number。

#### 20.1.2.5 Number.isSafeInteger ( number ) <div id="sec-number.issafeinteger"></div>

当使用一个参数number调用Number.isSafeInteger时，将执行以下步骤：

1. 若 Type(number) 不是 Number，返回 false.
2. 若 number 是 NaN, +∞，或 -∞，返回 false.
3. 令 integer 为 ! ToInteger(number).
4. 若 integer 不等于 number，返回 false.
5. 若 abs(integer) ≤ 253 - 1，返回 true.
6. 否则，返回 false.

#### 20.1.2.6 Number.MAX_SAFE_INTEGER <div id="sec-number.max_safe_integer"></div>

> 注：Number.MAX_SAFE_INTEGER的值是最大的整数n，因此n和n +1都可以精确表示为Number值。

Number.MAX_SAFE_INTEGER的值为9007199254740991(2 ^ 53-1)。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.7 Number.MAX_VALUE <div id="sec-number.max_value"></div>

Number.MAX_VALUE的值是Number类型的最大正有限值，大约为1.7976931348623157×10 ^ 308

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.8 Number.MIN_SAFE_INTEGER <div id="sec-number.min_safe_integer"></div>

> 注：Number.MIN_SAFE_INTEGER的值是最小的整数n，因此n和n-1都可以精确表示为Number值。

Number.MIN_SAFE_INTEGER的值为-9007199254740991(-(2 ^ 53-1))。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.9 Number.MIN_VALUE <div id="sec-number.min_value"></div>

Number.MIN_VALUE的值是Number类型的最小正值，大约为5×10 ^ -324

在IEEE 754-2008双精度二进制表示中，最小的可能值是非规范化的数字。如果实现不支持非规范化的值，则Number.MIN_VALUE的值必须是该实现实际可以表示的最小非零正值。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.10 Number.NaN <div id="sec-number.nan"></div>

Number.NaN的值为NaN

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.11 Number.NEGATIVE_INFINITY <div id="sec-number.negative_infinity"></div>

Number.NEGATIVE_INFINITY的值为-∞。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.12 Number.parseFloat ( string ) <div id="sec-number.parsefloat"></div>

Number.parseFloat数据属性的值是与18.2.4中定义的全局对象的parseFloat属性的值相同的内置函数对象。

#### 20.1.2.13 Number.parseInt ( string, radix ) <div id="sec-number.parseint"></div>

Number.parseInt数据属性的值是相同的内置函数对象，与18.2.5中定义的全局对象的parseInt属性的值相同。

#### 20.1.2.14 Number.POSITIVE_INFINITY <div id="sec-number.positive_infinity"></div>

Number.POSITIVE_INFINITY的值为+∞。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

#### 20.1.2.15 Number.prototype <div id="sec-number.prototype"></div>

Number.prototype的初始值为内部对象％NumberPrototype％。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 20.1.3 Number 原型对象属性 <div id="sec-properties-of-the-number-prototype-object"></div>

Number原型对象：

- 是内部对象％NumberPrototype％。
- 是一个普通的对象。
- 本身是一个Number对象；它具有一个[[NumberData]]内部插槽，其值为+0。
- 有一个[[Prototype]]内部插槽，其值是内部对象％ObjectPrototype％。

除非另有明确说明，否则下面定义的Number原型对象的方法不是通用的，传递给它们的this值必须是Number值或具有[[NumberData]]内部插槽已初始化为Number的对象值。

抽象操作thisNumberValue(value)执行以下步骤：

1. 若 Type(value) 是 Number，返回 value.
2. 若 Type(value) 是对象和值具有一个[[NumberData]]内部插槽，那么
  1. 令 n 为 value.[[NumberData]].
  2. 断言：Type(n) 是 Number.
  3. 返回 n.
3. 抛出 TypeError 异常

方法规范中的短语“ this Number value”是指通过调用抽象操作thisNumberValue并将方法调用的this值作为参数传递来返回的结果。

#### 20.1.3.1 Number.prototype.constructor <div id="sec-number.prototype.constructor"></div>

Number.prototype.constructor的初始值为内部对象％Number％

#### 20.1.3.2 Number.prototype.toExponential ( fractionDigits ) <div id="sec-number.prototype.toexponential"></div>

返回一个包含此Number值的字符串，该值以十进制指数表示，在有效位数的小数点前一位，在有效位数的小数点后一位分数。如果fractionDigits是undefined，则包含必要的有效数字以唯一地指定Number（就像在ToString中一样，只是在这种情况下，Number始终以指数表示法输出）。具体来说，请执行以下步骤：

1. 令 x 为 ? thisNumberValue(this value).
2. 令 f 为 ? ToInteger(fractionDigits).
3. 断言：若 fractionDigits 是 undefined，那么 f is 0.
4. 若 x 是 NaN，返回 the String "NaN".
5. 令 s 为空字符串。
6. 若 x < 0，那么
   1. 设置 s 为 "-".
   2. 设置 x 为 -x.
7. 若 x = +∞，那么
     1. returns和“ Infinity”的字符串连接。
8. 若 f < 0 or f > 100，抛出 RangeError 异常
9. 若 x = 0，那么
     1. 令 m 为由f +1次出现的String值组成的代码单元0x0030(0)。
     2. 令 e 为 0.
10. Else x ≠ 0,
    1. 若 fractionDigits 不是 undefined，那么
       1. 令e和n为整数，使10 ^ f≤n <10 ^(f + 1)，并且n×10 ^(e-f)-x的精确数学值尽可能接近零。如果有两个这样的e和n集，请选择n×10 ^(e-f)较大的e和n。
    2. 否则 fractionDigits 是 undefined,
        1. 令e，n和f为整数，使得f≥0，10 ^ f≤n <10 ^(f + 1)，n×10e-f的Number值为x，并且f尽可能小。请注意，n的十进制表示形式具有f +1个数字，n不能被10整除，并且n的最低有效位不一定由这些条件唯一确定。
    3. 令 m 为由n的十进制表示形式的数字组成的String值（按顺序，没有前导零）。
11. 若 f ≠ 0，那么
      1. 令 a 为m的第一个代码单元，令b为m的其余f个代码单元。
      2. 设置 m 为a，“.”和b的字符串连接。
12. 若 e = 0，那么
    1. 令 c 为 "+"。
    2. 令 d 为 "0"。
13. 否则，
    1. 若 e > 0，令 c 为 "+".
    2. 否则 e ≤ 0,
       1. 令 c 为 "-".
       2. 设置 e 为 -e.
    3. 令 d 为由e的十进制表示形式的数字组成的String值（按顺序，没有前导零）。
14. 设置 m 为 m，“ e”，c和d的字符串连接。
15. returns和m的字符串连接。

> 注：对于提供比以上规则要求的转换更为准确的实现，建议将以下步骤10.b.i的替代版本用作指导：
>
> 1. 令e，n和f为整数，使得10 ^ f≤n <10 ^(f + 1)，n×10(e-f)的数值
>     是x，并且f尽可能小。如果n有多种可能性，则选择n的值，其中n×10(e-f)的值最接近x。如果存在两个这样的n可能值，请选择一个偶数。

#### 20.1.3.3 Number.prototype.toFixed ( fractionDigits ) <div id="sec-number.prototype.tofixed"></div>

> 注 1：toFixed返回一个字符串，其中包含此数字值，该数字值以十进制定点表示法表示，小数点后有小数位数。如果fractionDigits是undefined，则假定为0。

执行以下步骤：

1. 令 x 为 ? thisNumberValue(this value).
2. 令 f 为 ? ToInteger(fractionDigits).
3. 断言：若 fractionDigits 是 undefined，那么 f is 0.
4. 若 f < 0 or f > 100，抛出 RangeError 异常
5. 若 x 是 NaN，返回 the String "NaN".
6. 令 s 为空字符串。
7. 若 x < 0，那么
      1. 设置 s 为 "-".
      2. 设置 x 为 -x.
8. 若 x ≥ 1021，那么
        1. 令 m 为 ! ToString(x).
9. 否则 x < 10^21,
    1. 令 n 为一个整数，其精确数学值n÷10 ^(f-x)尽可能接近零。如果有两个这样的n，则选择较大的n。
    2. 若 n = 0, 令 m 为 字符串 "0". 否则，令 m 为由 n 的十进制表示形式的数字组成的String值（按顺序，无前导零）。
    3. 若 f ≠ 0，那么
        1. 令 k 为 m 的长度
        2. 若 k ≤ f，那么
            1. 令 z 为由f +1-k个出现的字符串值组成的代码单元0x0030(0)。
            2. 设置 m 为 z 和 m 的字符串连接。
            3. 设置 k 为 f + 1.
        3. 令 a 为 m 的前k-f个代码单元，令b为m的其余f个代码单元。
        4. 设置 m 为a，“.”和b的字符串连接。
10. returns和m的字符串连接。

> 注 2：对于某些值，toFixed的输出可能比toString更精确，因为toString仅打印足够的有效数字以将数字与相邻数字值区分开。例如，
>
> (1000000000000000128).toString() 返回 "1000000000000000100", 当(1000000000000000128).toFixed(0) 返回 "1000000000000000128".

#### 20.1.3.4 Number.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-number.prototype.tolocalestring"></div>

包含ECMA-402国际化API的ECMAScript实现必须实现ECMA-402规范中指定的Number.prototype.toLocaleString方法。如果ECMAScript实现不包含ECMA-402 API，则使用以下toLocaleString方法的规范。

产生一个String值，该值表示此Number值，该值根据主机环境当前语言环境的约定进行格式化。此函数与实现有关，并且允许但不鼓励使用，因为它返回与toString相同的东西。

此方法的可选参数的含义在ECMA-402规范中定义；不包含ECMA-402支持的实现不得将这些参数位置用于其他任何用途。

#### 20.1.3.5 Number.prototype.toPrecision ( precision ) <div id="sec-number.prototype.toprecision"></div>

返回一个包含此Number值的字符串，该字符串以十进制指数符号表示，并在有效位数的小数点前加一位数字，而精度-在有效位数的小数点之后为1位数，或者以十进制固定表示法（具有精度有效位数）来表示。如果不确定精度，则调用ToString。具体来说，请执行以下步骤：

1. 令 x 为 ? thisNumberValue(this value).
2. 若 precision 是 undefined，返回 ! ToString(x).
3. 令 p 为 ? ToInteger(precision).
4. 若 x 是 NaN，返回字符串 "NaN".
5. 令 s 为空字符串。
6. 若 x < 0，那么
    1. 设置 s 为代码单元0x002D(-)。
    2. 设置 x 为 -x.
7. 若 x = +∞，那么.
    1. returns和“ Infinity”的字符串连接。
8. 若 p < 1 or p > 100，抛出 RangeError 异常
9. 若 x = 0，那么
    1. 令 m 为字符串值，由p个出现的代码单元0x0030(0)组成。
    2. 令 e 为 0.
10. 否则 x ≠ 0,
    1. 令e和n为整数，使10 ^(p-1)≤n <10 ^ p，并且n×10 ^(e-p + 1-x)的精确数学值尽可能接近零。如果有两个这样的e和n集，请选择n×10 ^(e-p +1)较大的e和n。
    2. 令 m 为由n的十进制表示形式的数字组成的String值（按顺序，没有前导零）。
    3. 若 e < -6 or e ≥ p，那么
        1. 断言：e ≠ 0.
        2. 若 p ≠ 1，那么
            1. 令 a 为 m 的第一个代码单元，令b为m的其余p-1个代码单元。
            2. 设置 m 为a，“.”和b的字符串连接。
        3. 若 e > 0，那么
            1. 令 c 为代码单元0x002B(+)。
        4. 否则 e < 0,
            1. 令 c 为代码单元0x002D(-)。
            2. 设置 e 为 -e.
        5. 令 d 为由 e 的十进制表示形式的数字组成的String值（按顺序，没有前导零）。
        6. returns，m，代码单元0x0065（拉丁文小写字母E），c和d的字符串连接。
11. 若 e = p - 1，返回 s 和 m 的字符串连接。
12. 若 e ≥ 0，那么
    1. 设置 m 为 m 的前e + 1个代码单元，代码单元0x002E(FULL STOP)和m的其余p-(e +1)个代码单元的字符串连接。
13. Else e < 0,
    1. 设置 m 为代码单元0x0030(0)的字符串连接，代码单元0x002E(.)，-(e +1)代码单元0x0030(0)的出现以及字符串m。
14. 返回 s 和 m 的字符串连接。

#### 20.1.3.6 Number.prototype.toString ( [ radix ] ) <div id="sec-number.prototype.tostring"></div>

> 注：可选的基数应该是2到36之间的整数值。如果基数不存在或为undefined，则使用数字10作为基数值。

执行以下步骤：

1. 令 x 为 ? thisNumberValue(this value).
2. 若 radix 不存在，令 radixNumber 为 10.
3. Else 若 radix 是 undefined，令 radixNumber 为 10.
4. 否则，令 radixNumber 为 ? ToInteger(radix).
5. 若 radixNumber < 2 or radixNumber > 36，抛出 RangeError 异常
6. 若 radixNumber = 10，返回 ! ToString(x).
7. 返回使用radixNumber指定的基数表示此数字值的字符串。字母a-z用于值为10到35的数字。精确的算法依赖于实现，但是算法应该是7.1.12.1中规定的泛化。

toString函数不是通用的；如果该值不是数字或数字对象，则抛出一个TypeError异常。因此，它不能作为一种方法转移到其他类型的对象上。

toString方法的“length”属性是1

#### 20.1.3.7 Number.prototype.valueOf ( ) <div id="sec-number.prototype.valueof"></div>

1. 返回 ? thisNumberValue(this value).

### 20.1.4 Number 实例属性 <div id="sec-properties-of-number-instances"></div>

Number实例是从Number prototype对象继承属性的普通对象。Number实例也有一个[[NumberData]]内部槽。内部槽是这个Number对象表示的数值。

## 20.2 Math 对象 <div id="sec-math-object"></div>

Math对象：

- 是内部对象％Math％。
- 是全局对象的Math属性的初始值。
- 是一个普通的对象。
- 有一个[[Prototype]]内部插槽，其值是固有对象％ObjectPrototype％。
- 不是函数对象。
- 没有[[Construct]]内部方法；它不能与new运算符一起用作构造函数。
- 没有[[Call]]内部方法；它不能作为函数调用。

> 注：在本规范中，短语“ x的数字值”具有6.1.6中定义的技术含义。

### 20.2.1 Math 对象值属性 <div id="sec-value-properties-of-the-math-object"></div>

#### 20.2.1.1 Math.E <div id="sec-math.e"></div>

e的数字值，自然对数的底数，大约为2.7182818284590452354。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.2.1.2 Math.LN10 <div id="sec-math.ln10"></div>

自然对数10的Number值，大约为2.302585092994046

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.2.1.3 Math.LN2 <div id="sec-math.ln2"></div>

自然对数2的Number值，大约为0.6931471805599453。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

#### 20.2.1.4 Math.LOG10E <div id="sec-math.log10e"></div>

e的以10为底的对数的Number值，自然对数的底；该值约为0.4342944819032518。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

> 注：Math.LOG10E的值大约是Math.LN10的值的倒数。

#### 20.2.1.5 Math.LOG2E <div id="sec-math.log2e"></div>

e的以2为底的对数的Number值，自然对数的底；该值约为1.4426950408889634。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

> 注：Math.LOG2E的值大约是Math.LN2的值的倒数。

#### 20.2.1.6 Math.PI <div id="sec-math.pi"></div>

为π的数值，一个圆其直径，这大约是3.1415926535897932的周长之比。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.2.1.7 Math.SQRT1_2 <div id="sec-math.sqrt1_2"></div>

平方根的Number的数值，大约为0.7071067811865476。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

> 注：Math.SQRT1_2的值大约是Math.SQRT2的值的倒数。

#### 20.2.1.8 Math.SQRT2 <div id="sec-math.sqrt2"></div>

2的平方根的Number值，大约为1.4142135623730951。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.2.1.9 Math [ @@toStringTag ] <div id="sec-math-@@tostringtag"></div>

@@toStringTag属性的初始值为字符串值“ Math”。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }

### 20.2.2 Math 对象函数属性 <div id="sec-function-properties-of-the-math-object"></div>

以下每个Math对象函数将ToNumber抽象操作应用于其每个参数（如果有多个，则以从左到右的顺序）。如果ToNumber返回一个突然的完成，该完成记录将立即返回。否则，该函数将对所得的Number值进行计算。每个函数返回的值是一个数字。

在下面的功能描述中，符号NaN，-0，+ 0，-∞和+∞表示6.1.6中描述的Number值。

> 注：函数acos，acosh，asin，asinh，atan，atanh，atan2，cbrt，cos，cosh，exp，expm1，hypot，log，log1p，log2，log10，pow，pow，sin，sinh，sqrt， tan和tanh在此处未明确指定，只是要求某些表示感兴趣边界情况的参数值具有特定结果。对于其他自变量值，这些函数旨在计算对熟悉的数学函数的结果的近似值，但是在选择近似算法时允许有一定的自由度。总体意图是，实现者应该能够在给定的硬件平台上为ECMAScript使用相同的数学库，该平台可供C程序员使用。
>
> 尽管算法的选择留给实现，但建议（但未由该标准指定）实现使用fdlibm中包含的IEEE 754-2008算法的近似算法，fdlibm是Sun Microsystems(http：/ /www.netlib.org/fdlibm)。

#### 20.2.2.1 Math.abs ( x ) <div id="sec-math.abs"></div>

返回x的绝对值；结果的大小与x相同，但具有正号。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 -0，结果为 +0.
- 若 x 是 -∞，结果为 +∞.

#### 20.2.2.2 Math.acos ( x ) <div id="sec-math.acos"></div>

返回x的反余弦的与实现相关的近似值。结果以弧度表示，范围为+0到+π。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 greater than 1，结果为 NaN.
- 若 x 是 less than -1，结果为 NaN.
- 若 x 是 exactly 1，结果为 +0.

#### 20.2.2.3 Math.acosh ( x ) <div id="sec-math.acosh"></div>

返回x的反双曲余弦的依赖于实现的近似值。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 less than 1，结果为 NaN.
- 若 x 是 1，结果为 +0.
- 若 x 是 +∞，结果为 +∞.

#### 20.2.2.4 Math.asin ( x ) <div id="sec-math.asin"></div>

返回x的反正弦值的与实现相关的近似值。结果以弧度表示，范围为-π/ 2到+π/ 2。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 greater than 1，结果为 NaN.
- 若 x 是 less than -1，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.

#### 20.2.2.5 Math.asinh ( x ) <div id="sec-math.asinh"></div>

返回x的反双曲正弦的与实现相关的近似值。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 -∞.

#### 20.2.2.6 Math.atan ( x ) <div id="sec-math.atan"></div>

返回x的反正切的与实现有关的近似值。结果以弧度表示，范围为 -π/ 2到 +π/ 2。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为与实现相关的近似值 +π/2。
- 若 x 是 -∞，结果为与实现有关的近似值 -π/ 2。

#### 20.2.2.7 Math.atanh ( x ) <div id="sec-math.atanh"></div>

返回x的反双曲正切的依赖于实现的近似

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 less than -1，结果为 NaN.
- 若 x 是 greater than 1，结果为 NaN.
- 若 x 是 -1，结果为 -∞.
- 若 x 是 +1，结果为 +∞.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.

#### 20.2.2.8 Math.atan2 ( y, x ) <div id="sec-math.atan2"></div>

返回参数y和x的商y / x的反正切值的与实现有关的近似值，其中y和x的符号用于确定结果的象限。请注意，对于两个参数的反正切函数而言，有意和传统的做法是将名为y的参数设为第一个，将名为x的参数设为第二个。结果以弧度表示，范围为-π到+π。

- 若 either x or y 是 NaN，结果为 NaN.
- 若 y > 0 and x 是 +0，结果为与实现相关的近似值 +π/2。
- 若 y > 0 and x 是 -0，结果为与实现相关的近似值 +π / 2.
- 若 y 是 +0 and x > 0，结果为 +0.
- 若 y 是 +0 and x is +0，结果为 +0.
- 若 y 是 +0 and x is -0，结果为与实现相关的近似值 +π.
- 若 y 是 +0 and x < 0，结果为与实现相关的近似值 +π.
- 若 y 是 -0 and x > 0，结果为 -0.
- 若 y 是 -0 and x is +0，结果为 -0.
- 若 y 是 -0 and x is -0，结果为与实现相关的近似值-π.
- 若 y 是 -0 and x < 0，结果为与实现相关的近似值-π.
- 若 y < 0 and x 是 +0，结果为与实现相关的近似值 -π / 2.
- 若 y < 0 and x 是 -0，结果为与实现相关的近似值 -π / 2.
- 若 y > 0 and y is finite and x 是 +∞，结果为 +0.
- 若 y > 0 and y is finite and x 是 -∞，结果为与实现相关的近似值+π.
- 若 y < 0 and y is finite and x 是 +∞，结果为 -0.
- 若 y < 0 and y is finite and x 是 -∞，结果为与实现相关的近似值-π.
- 若 y 是 +∞ and x is finite，结果为与实现相关的近似值 +π / 2.
- 若 y 是 -∞ and x is finite，结果为与实现相关的近似值 -π / 2.
- 若 y 是 +∞ and x is +∞，结果为与实现相关的近似值 +π / 4.
- 若 y 是 +∞ and x is -∞，结果为与实现相关的近似值 +3π / 4.
- 若 y 是 -∞ and x is +∞，结果为与实现相关的近似值 -π / 4.
- 若 y 是 -∞ and x is -∞，结果为与实现相关的近似值 -3π / 4.

#### 20.2.2.9 Math.cbrt ( x ) <div id="sec-math.cbrt"></div>

returns an implementation-dependent approximation to the cube root of x.

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 -∞.

#### 20.2.2.10 Math.ceil ( x ) <div id="sec-math.ceil"></div>

返回不小于x且等于数学整数的最小（最接近-∞）数值。如果x已经是整数，则结果为x。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 -∞.
- 若 x 是 小于 0 大于 -1，结果为 -0.

Math.ceil(x)的值与-Math.floor(-x)的值相同。

#### 20.2.2.11 Math.clz32 ( x ) <div id="sec-math.clz32"></div>

当使用一个参数x调用Math.clz32时，将执行以下步骤：

1. 令 n 为 ? ToUint32(x).
2. 令 p 为 n 的32位二进制表示形式中前导零位的数量。
3. 返回 p.

> 注：如果 n 为0，则 p 将为32。如果32位二进制编码n的最高有效位为1，则 p 将为0。

#### 20.2.2.12 Math.cos ( x ) <div id="sec-math.cos"></div>

返回x的余弦的与实现相关的近似值。该参数以弧度表示

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 1.
- 若 x 是 -0，结果为 1.
- 若 x 是 +∞，结果为 NaN.
- 若 x 是 -∞，结果为 NaN

#### 20.2.2.13 Math.cosh ( x ) <div id="sec-math.cosh"></div>

返回x的双曲余弦的与实现相关的近似值。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 1.
- 若 x 是 -0，结果为 1.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 +∞.

> 注：cosh(x)的值等于(exp(x)+ exp(-x))/ 2。

#### 20.2.2.14 Math.exp ( x ) <div id="sec-math.exp"></div>

返回x的指数函数（将e提升为x的幂，其中e是自然对数的底）的实现依赖近似。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 1.
- 若 x 是 -0，结果为 1.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 +0.

#### 20.2.2.15 Math.expm1 ( x ) <div id="sec-math.expm1"></div>

返回依赖于实现的近似值，以从x的指数函数中减去1（e升至x的幂，其中e是自然对数的底）。即使x的值接近于0，也以准确的方式计算结果。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 -1.

#### 20.2.2.16 Math.floor ( x ) <div id="sec-math.floor"></div>

返回不大于x并且等于数学整数的最大（最接近+∞）数值。如果x已经是整数，则结果为x。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 -∞.
- 若 x 是 greater than 0 but less than 1，结果为 +0.

> 注：Math.floor(x)的值与-Math.ceil(-x)的值相同。

#### 20.2.2.17 Math.fround ( x ) <div id="sec-math.fround"></div>

当使用参数x调用Math.fround时，将执行以下步骤：

1. 若 x 是 NaN，返回 NaN.
2. 若 x 是 one of +0, -0, +∞, -∞，返回 x.
3. 令 x32 为使用roundTiesToEven将x转换为IEEE 754-2008 binary32格式的值的结果。
4. 令 x64 为使用roundTiesToEven将x转换为IEEE 754-2008 binary32格式的值的结果。
5. 返回与x64对应的ECMAScript Number值。

#### 20.2.2.18 Math.hypot ( value1, value2, ...values ) <div id="sec-math.hypot"></div>

Math.hypot返回其参数的平方和的平方根的依赖于实现的近似值。

- 若没有传递参数，结果为 +0.
- 若任何参数是 +∞，结果为 +∞.
- 若任何参数是 -∞，结果为 +∞.
- 若没有参数为+∞或-∞，并且任何参数为NaN，则结果为NaN。
- 若所有参数均为+0或-0，结果为 +0.

> 注：实现时应注意避免因使用两个或多个参数调用此函数，而在简单的实现中容易发生的上溢和下溢导致精度损失。

#### 20.2.2.19 Math.imul ( x, y ) <div id="sec-math.imul"></div>

当使用参数 x 和 y 调用Math.imul时，将执行以下步骤：

1. 令 a 为 ? ToUint32(x).
2. 令 b 为 ? ToUint32(y).
3. 令 product 为 (a × b) modulo 2^32.
4. 若 product ≥ 2^31，返回 product - 2^32; 否则，返回 product.

#### 20.2.2.20 Math.log ( x ) <div id="sec-math.log"></div>

返回 x 的自然对数的依赖于实现的近似值。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 less than 0，结果为 NaN.
- 若 x 是 +0 or -0，结果为 -∞.
- 若 x 是 1，结果为 +0.
- 若 x 是 +∞，结果为 +∞.

#### 20.2.2.21 Math.log1p ( x ) <div id="sec-math.log1p"></div>

返回与实现有关的近似值1 + x的自然对数。即使x的值接近于零，也以准确的方式计算结果。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 less than -1，结果为 NaN.
- 若 x 是 -1，结果为 -∞.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.

#### 20.2.2.22 Math.log10 ( x ) <div id="sec-math.log10"></div>

返回x的以10为底的对数的依赖于实现的近似值。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 less than 0，结果为 NaN.
- 若 x 是 +0，结果为 -∞.
- 若 x 是 -0，结果为 -∞.
- 若 x 是 1，结果为 +0.
- 若 x 是 +∞，结果为 +∞.

#### 20.2.2.23 Math.log2 ( x ) <div id="sec-math.log2"></div>

返回 x 的 2 为底对数的依赖于实现的近似值。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 less than 0，结果为 NaN.
- 若 x 是 +0，结果为 -∞.
- 若 x 是 -0，结果为 -∞.
- 若 x 是 1，结果为 +0.
- 若 x 是 +∞，结果为 +∞.

#### 20.2.2.24 Math.max ( value1, value2, ...values ) <div id="sec-math.max"></div>

给定零个或多个参数，在每个参数上调用 ToNumber 并返回最大结果值

- 若没有给出参数，结果为 -∞.
- 若任何值是 NaN，结果为 NaN.
- 使用抽象关系比较算法对值进行比较以确定最大值，只不过认为 +0 大于 -0。

#### 20.2.2.25 Math.min ( value1, value2, ...values ) <div id="sec-math.min"></div>

给定零个或多个参数，在每个参数上调用 ToNumber 并返回结果值的最小值。

- 若没有给出参数，结果为 +∞.
- 若任何值是 NaN，结果为 NaN.
- 使用抽象关系比较算法对确定最小值的值进行比较，只不过认为 +0 大于 -0。

#### 20.2.2.26 Math.pow ( base, exponent ) <div id="sec-math.pow"></div>

1. 返回应用定义在12.6.4的 base 和 exponent 的 ** 运算结果。

#### 20.2.2.27 Math.random ( ) <div id="sec-math.random"></div>

返回一个带正号的 Number 值，大于或等于 0，但小于 1，使用与实现相关的算法或策略随机或伪随机选择，该范围内分布大致一致。此函数不采用任何参数。

为不同领域创建的每个 Math.random 函数都必须生成与连续调用不同的值序列。

#### 20.2.2.28 Math.round ( x ) <div id="sec-math.round"></div>

返回最接近 x 且等于数学整数的数字值。如果两个整数值同样接近 x，则结果为更接近 + 的数字值。如果 x 已经是整数，则结果为 x。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 -∞.
- 若 x 是大于 0 但小于 0.5，结果为 +0.
- 若 x 是小于 0 但大于或等于 -0.5，结果为 -0.

>NOTE 1
>Math.round(3.5) 返回 4, 但是 Math.round(-3.5) 返回 -3.

>NOTE 2
>Math.round (x) 的值并不总是与 Math.floor(x + 0.5) 的值相同。当 x 为 -0 或小于 0 但大于或等于 -0.5 时，Math.round(x) 返回 -0，但 Math.floor(x + 0.5) 返回 +0。Math.round(x) 也可能不同于 Math.floor(x + 0.5) 的值，因为计算 x + 0.5 时存在内部舍入。

#### 20.2.2.29 Math.sign ( x ) <div id="sec-math.sign"></div>

返回 x 的符号，指示 x 是正数、负数还是零。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +0，结果为 +0.
- 若 x 是负数且不为 -0，结果为 -1.
- 若 x 是正数且不为 +0，结果为 +1.

#### 20.2.2.30 Math.sin ( x ) <div id="sec-math.sin"></div>

返回x的正弦依赖的近似值。该参数以弧度表示。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞ or -∞，结果为 NaN.

#### 20.2.2.31 Math.sinh ( x ) <div id="sec-math.sinh"></div>

返回 x 的双曲正弦的与实现有关的近似。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 -∞.

> 注：sinh(x)的值与(exp(x)-exp(-x))/ 2相同。

#### 20.2.2.32 Math.sqrt ( x ) <div id="sec-math.sqrt"></div>

返回 x 的平方根的依赖于实现的近似值。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 less than 0，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +∞.

#### 20.2.2.33 Math.tan ( x ) <div id="sec-math.tan"></div>

返回 x 的切线的与实现相关的近似值。该参数以弧度表示。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞ or -∞，结果为 NaN.

#### 20.2.2.34 Math.tanh ( x ) <div id="sec-math.tanh"></div>

返回 x 的双曲正切的与实现有关的近似值。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 +0，结果为 +0.
- 若 x 是 -0，结果为 -0.
- 若 x 是 +∞，结果为 +1.
- 若 x 是 -∞，结果为 -1.

> 注：tanh(x) 的值与(exp(x) - exp(-x))/(exp(x) + exp(-x))相同.

#### 20.2.2.35 Math.trunc ( x ) <div id="sec-math.trunc"></div>

返回数字x的整数部分，除去所有小数位数。如果x已经是整数，则结果为x。

- 若 x 是 NaN，结果为 NaN.
- 若 x 是 -0, 结果为 -0.If x is +0，结果为 +0.
- 若 x 是 +∞，结果为 +∞.
- 若 x 是 -∞，结果为 -∞.
- 若 x 是大于 0 但小于 1，结果为 +0.
- 若 x 是小于 0 但大于 -1，结果为 -0.

## 20.3 Date 对象 <div id="sec-date-objects"></div>
### 20.3.1 Date 对象的概述和抽象操作定义 <div id="sec-overview-of-date-objects-and-definitions-of-abstract-operations"></div>

以下函数是对时间值（在20.3.1.1中定义）进行操作的抽象操作。请注意，在每种情况下，如果这些函数之一的任何参数为NaN，则结果将为NaN。

#### 20.3.1.1 时间值和时间范围 <div id="sec-time-values-and-time-range"></div>

Date对象包含一个Number，代表毫秒级的时间瞬间。这样的数字称为时间值。时间值也可以是NaN，表示Date对象不代表特定的时间点。

从1970年1月1日UTC午夜起以毫秒为单位，以ECMAScript度量时间。 ECMAScript中的时间不遵守leap秒；他们被忽略了。时间计算假设每天精确地包含60×60×24×1000 = 86,400,000毫秒，以与每天精确地包含86,400秒的POSIX规范保持一致。

一个数字可以精确表示从-9,007,199,254,740,992到9,007,199,254,740,992（20.1.2.8和20.1.2.6）的所有整数。与1970年1月1日UTC开始的午夜相比，时间值支持的范围较小，范围从-100,000,000天到100,000,000天。相对于UTC 1970年1月1日开始的午夜，这将产生确切的支持时间值范围-8,640,000,000,000,000至8,640,000,000,000,000毫秒。

UTC 1970年1月1日开始的午夜的确切时刻由时间值+0表示

> 注：公历的400年周期包含97个闰年。根据公历，这每年平均产生365.2425天，或每年平均31,556,952,000毫秒。 ECMAScript对所有时间计算均采用通用的公历。
>
> 如本节所述，相对于1970年1月1日UTC午夜开始，Number可以精确表示的最大年份范围为-285,426到285,426年。
>
> 如本节所述，相对于UTC 1970年1月1日开始的午夜，时间值可以表示的最大年份范围约为-273,790至273,790年。

#### 20.3.1.2 天数和一天的时间 <div id="sec-day-number-and-time-within-day"></div>

给定的时间值 t 属于日期

​	Day(t) = floor(t / msPerDay)

每天的毫秒数是

​	msPerDay = 86400000

其余时间称为一天中的时间：

​	TimeWithinDay(t) = t modulo msPerDay

#### 20.3.1.3 年数 <div id="sec-year-number"></div>

ECMAScript使用冗长的格里高利历将日期编号映射为年份编号，并确定该年份中的月份和日期。在此日历中，leap年恰好是（可被4整除）和（（不可被100整除）或（可被400整除））的年份。因此，第y年的天数定义为

​	DaysInYear(y)
​		= 365 if (y modulo 4) ≠ 0
​		= 366 if (y modulo 4) = 0 and (y modulo 100) ≠ 0
​		= 365 if (y modulo 100) = 0 and (y modulo 400) ≠ 0
​		= 366 if (y modulo 400) = 0

所有非闰年都有365天，每个月都有天数，闰年在2月多出一天。y年第一天的日期由:

​	DayFromYear(y) = 365 × (y - 1970) + floor((y - 1969) / 4) - floor((y - 1901) / 100) + floor((y - 1601) / 400)

年初的时间值为:

​	TimeFromYear(y) = msPerDay × DayFromYear(y)

时间值通过以下方式确定一年:

​	YearFromTime(t) = the largest integer y (closest to positive infinity) such that TimeFromYear(y) ≤ t

闰年的函数为1，否则为0:

​	InLeapYear(t)
​		= 0 if DaysInYear(YearFromTime(t)) = 365
​		= 1 if DaysInYear(YearFromTime(t)) = 366

#### 20.3.1.4 月数 <div id="sec-month-number"></div>

月份由0到11(包括11)范围内的整数标识。将一个时间值t映射为一个月号的MonthFromTime(t)定义为:

​	MonthFromTime(t)
​		= 0 if 0 ≤ DayWithinYear(t) < 31
​		= 1 if 31 ≤ DayWithinYear(t) < 59 + InLeapYear(t)
​		= 2 if 59 + InLeapYear(t) ≤ DayWithinYear(t) < 90 + InLeapYear(t)
​		= 3 if 90 + InLeapYear(t) ≤ DayWithinYear(t) < 120 + InLeapYear(t)
​		= 4 if 120 + InLeapYear(t) ≤ DayWithinYear(t) < 151 + InLeapYear(t)
​		= 5 if 151 + InLeapYear(t) ≤ DayWithinYear(t) < 181 + InLeapYear(t)
​		= 6 if 181 + InLeapYear(t) ≤ DayWithinYear(t) < 212 + InLeapYear(t)
​		= 7 if 212 + InLeapYear(t) ≤ DayWithinYear(t) < 243 + InLeapYear(t)
​		= 8 if 243 + InLeapYear(t) ≤ DayWithinYear(t) < 273 + InLeapYear(t)
​		= 9 if 273 + InLeapYear(t) ≤ DayWithinYear(t) < 304 + InLeapYear(t)
​		= 10 if 304 + InLeapYear(t) ≤ DayWithinYear(t) < 334 + InLeapYear(t)
​		= 11 if 334 + InLeapYear(t) ≤ DayWithinYear(t) < 365 + InLeapYear(t)

在

​	DayWithinYear(t) = Day(t) - DayFromYear(YearFromTime(t))

月份值为0表示一月； 1表示2月； 2表示3月； 3表示四月； 4表示五月； 5表示6月； 6表示7月； 7表示8月； 8表示9月； 9表示十月； 10表示11月；和11表示12月。请注意，MonthFromTime（0）= 0，对应于1970年1月1日，星期四。

#### 20.3.1.5 日期数 <div id="sec-date-number"></div>

日期编号由范围 1 到 31 中的整数标识。从时间值 t 到日期编号的映射 DateFromTime(t) 由以下定义：

​	DateFromTime(t)
​		= DayWithinYear(t) + 1 if MonthFromTime(t) = 0
​		= DayWithinYear(t) - 30 if MonthFromTime(t) = 1
​		= DayWithinYear(t) - 58 - InLeapYear(t) if MonthFromTime(t) = 2
​		= DayWithinYear(t) - 89 - InLeapYear(t) if MonthFromTime(t) = 3
​		= DayWithinYear(t) - 119 - InLeapYear(t) if MonthFromTime(t) = 4
​		= DayWithinYear(t) - 150 - InLeapYear(t) if MonthFromTime(t) = 5
​		= DayWithinYear(t) - 180 - InLeapYear(t) if MonthFromTime(t) = 6
​		= DayWithinYear(t) - 211 - InLeapYear(t) if MonthFromTime(t) = 7
​		= DayWithinYear(t) - 242 - InLeapYear(t) if MonthFromTime(t) = 8
​		= DayWithinYear(t) - 272 - InLeapYear(t) if MonthFromTime(t) = 9
​		= DayWithinYear(t) - 303 - InLeapYear(t) if MonthFromTime(t) = 10
​		= DayWithinYear(t) - 333 - InLeapYear(t) if MonthFromTime(t) = 11

#### 20.3.1.6 周数 <div id="sec-week-day"></div>

特定时间值 t 的个工作日定义为

​	WeekDay(t) = (Day(t) + 4) modulo 7

工作日的值 0 表示星期日；1 表示星期一；2 表示星期二；3 表示星期三；4 表示星期四；5 表示星期五；6 表示星期六。请注意，WeekDay（0）= 4，对应于 1970 年 1 月 1 日星期四。

#### 20.3.1.7 LocalTZA ( t, isUTC ) <div id="sec-local-time-zone-adjustment"></div>

LocalTZA（t，isUTC）是一种实现定义的算法，必须返回一个表示毫秒的数字，该数字适合加到时间值上。应该使用以下三段中指定的方式使用当地的有关标准时间和夏令时的政治规则来确定结果。

当 isUTC 为 true 时，LocalTZA（t，true） 应返回本地时区的偏移量，以时间值 t （UTC） 表示的时间以毫秒为单位。当结果添加到 t （UTC） 时，应生成本地时间。当 isUTC 为 true 时，LocalTZA（t，true） 应返回本地时区的偏移量，以时间值 t （UTC） 表示的时间以毫秒为单位。当结果添加到 t （UTC） 时，应生成本地时间。

当 isUTC 为 false 时，LocalTZA（t，false）应返回本地时区的偏移量，以本地时间以毫秒为单位，以时间值 t 本地 = t 表示。当从本地时间 t 本地减去结果时，应生成相应的 UTC。

当t local表示本地时间在负时区转换时重复多次（例如，夏时制结束或由于时区规则更改而导致时区调整减少）或在正时区转换时跳过本地时间（例如，当夏令时开始或由于时区规则更改而增加时区调整时），必须在转换前用时区调整来解释本地时间。

如果实现不支持上述转换，或者在实现内没有时间t的政治规则，则结果必须为0。

>注：建议实现使用IANA时区数据库 https://www.iana.org/time-zones/ 的时区信息。 
>
>2017年11月5日在美国/纽约，上午1:30重复了两次（倒退），但必须将其解释为UTC-04 AM 1:30，而不是UTC-05 AM 30。 LocalTZA（TimeClip（MakeDate（MakeDay（2017，10，5），MakeTime（1，30，0，0））），false）为-4×msPerHour。
>
>2017年3月12日上午2:30在美国/纽约州不存在，但必须将其解释为UTC-05 2:30 AM（相当于UTC-04 AM 3:30）。 LocalTZA（TimeClip（MakeDate（MakeDay（2017，2，12），MakeTime（2，30，0，0））），false）是-5×msPerHour。

#### 20.3.1.8 LocalTime ( t ) <div id="sec-localtime"></div>

参数为t的抽象操作LocalTime通过执行以下步骤将t从UTC转换为本地时间：

1. 返回 t + LocalTZA(t, true).

> 注：当重复次数较多时（例如，夏令时结束或时区调整减少），两个不同的时间值（t（UTC））在负时区转换时转换为相同的本地时间t local。

#### 20.3.1.9 UTC ( t ) <div id="sec-utc-t"></div>

参数为t的抽象运算UTC将t从本地时间转换为UTC。它执行以下步骤：

1. 返回 t - LocalTZA(t, false).

> 注：UTC（LocalTime（t））不一定总是等于t。 LocalTime（UTC（t local））也不一定总是等于t local。

#### 20.3.1.10 小时，分钟，秒和毫秒 <div id="sec-hours-minutes-second-and-milliseconds"></div>

以下抽象操作在分解时间值时很有用：

​	HourFromTime(t) = floor(t / msPerHour) modulo HoursPerDay
​	MinFromTime(t) = floor(t / msPerMinute) modulo MinutesPerHour
​	SecFromTime(t) = floor(t / msPerSecond) modulo SecondsPerMinute
​	msFromTime(t) = t modulo msPerSecond

此处

​	HoursPerDay = 24
​	MinutesPerHour = 60
​	SecondsPerMinute = 60
​	msPerSecond = 1000
​	msPerMinute = 60000 = msPerSecond × SecondsPerMinute
​	msPerHour = 3600000 = msPerMinute × MinutesPerHour

#### 20.3.1.11 MakeTime ( hour, min, sec, ms ) <div id="sec-maketime"></div>

抽象操作MakeTime根据其四个参数计算毫秒数，这些参数必须是ECMAScript Number值。该运算符的功能如下：

1. 若 hour 不是有限或 min 不是有限的，或者 sec 不是有限的，或者 ms 不是有限的，返回 NaN.
2. 令 h 为 ! ToInteger(hour).
3. 令 m 为 ! ToInteger(min).
4. 令 s 为 ! ToInteger(sec).
5. 令 milli 为 ! ToInteger(ms).
6. 令 t 为 h * msPerHour + m * msPerMinute + s * msPerSecond + milli, 根据IEEE 754-2008规则执行算术(也就是说，就像使用ECMAScript运算符*和+).
7. 返回 t.

#### 20.3.1.12 MakeDay ( year, month, date ) <div id="sec-makeday"></div>

抽象操作MakeDay通过其三个参数（必须为ECMAScript Number值）来计算天数。该运算符的功能如下：

1. 若 year 不是有限或 month 不是有限的或 date 不是有限的，返回 NaN.
2. 令 y 为 ! ToInteger(year).
3. 令 m 为 ! ToInteger(month).
4. 令 dt 为 ! ToInteger(date).
5. 令 ym 为 y + floor(m / 12).
6. 令 mn 为 m modulo 12.
7. 找到一个值t使得 YearFromTime(t) 是 ym，MonthFromTime(t) 是 mn，DateFromTime(t) 是 1; 但是如果这不可能（因为某些参数超出范围），返回 NaN.
8. 返回 Day(t) + dt - 1.

#### 20.3.1.13 MakeDate ( day, time ) <div id="sec-makedate"></div>

抽象操作MakeDate根据其两个参数计算毫秒数，该参数必须为ECMAScript Number值。该运算符的功能如下：

1. 若 day 不是有限的，或 time 不是有限的，返回 NaN.
2. 返回 day × msPerDay + time.

#### 20.3.1.14 TimeClip ( time ) <div id="sec-timeclip"></div>

抽象操作TimeClip根据其参数计算毫秒数，该参数必须为ECMAScript Number值。该运算符的功能如下：

1. 若 time 不是无限，返回 NaN.
2. 若 abs(time) > 8.64 × 1015，返回 NaN.
3. 令 clippedTime 为 ! ToInteger(time).
4. 若 clippedTime 是 -0，设置 clippedTime 为 +0.
5. 返回 clippedTime.

> 注：步骤4的要点是允许实现选择时间值的内部表示形式，例如，以64位有符号整数或64位浮点值的形式表示。根据实现的不同，此内部表示形式可能会也可能不会区分-0和+0。

#### 20.3.1.15 日期时间字符串格式 <div id="sec-date-time-string-format"></div>

ECMAScript基于简化的ISO 8601日历日期扩展格式，为日期时间定义了字符串交换格式。格式如下：YYYY-MM-DDTHH:mm:ss.sssZ

字段如下：

| | |
| ---- | ---- |
| `YYYY` | 是公历中从0000到9999年的十进制数字。                         |
| `-`    | `"-"`（连字符）在字符串中实际出现两次。                      |
| `MM`   | 是一年中从01（一月）到12（十二月）的月份。                   |
| `DD`   | 是从01到31的月份。                                           |
| `T`    | `"T"` 实际出现在字符串中，以指示时间元素的开始。             |
| `HH`   | 是自午夜以来经过的完整小时数，从00到24的两位十进制数字。 |
| `:`    | `":"`（冒号）在字符串中实际出现两次。 |
| `mm`   | 是从小时开始到现在的完整分钟数，从00到59的两位十进制数字。 |
| `ss`   | 是从分钟开始算起的完整秒数，从00到59的两位十进制数字。 |
| `.`    | `"."`（点）字面上出现在字符串中。   |
| `sss`  | 是从秒的开始算起的完整毫秒数，为三个十进制数字。 |
| `Z`    | 是指定为` "Z"`（对于UTC）或`"+"`或`"-"`后跟时间表达式`HH:mm`的时区偏移量 |

此格式包括仅日期形式：

```
YYYY
YYYY-MM
YYYY-MM-DD
```

它还包括“日期时间”格式，该格式由上述仅日期格式之一构成，后跟以下时间格式之一，并附加了可选的时区偏移量：

```
THH:mm
THH:mm:ss
THH:mm:ss.sss
```

所有数字都必须以10为底。如果缺少MM或DD字段，则将“ 01”用作值。如果HH，mm或ss字段不存在，则将“ 00”用作值，而将sss字段不存在的值设为“ 000”。如果没有时区偏移，则仅日期形式将被解释为UTC时间，而日期时间形式将被解释为本地时间。

包含超出范围或不符合要求的字段的字符串不是此格式的有效实例。

> 注1：由于每天都是从午夜开始和结束，所以可以使用两种表示法 00:00 和 24:00 来区分可以与一个日期关联的两个午夜。这意味着以下两个符号指的是完全相同的时间点：1995-02-04T24:00和1995-02-05T00:00。后一种形式的这种解释为“日历日结束”与ISO 8601一致，即使该规范保留了它来描述时间间隔，也不允许在单个时间点表示中使用它。

> 注2：尚无国际标准规定民用时区（如CET，EST等）的缩写，有时甚至在两个截然不同的时区使用相同的缩写。因此，ISO 8601和此格式均指定时区偏移量的数字表示。

##### 20.3.1.15.1 年扩展 <div id="sec-expanded-years"></div>

从1970年1月1日（20.3.1.1）开始，向前或向后覆盖大约273,790年的全部时间值范围，则需要表示0之前或9999之后的年份。ISO8601允许扩展年份表示，但前提是必须与合作伙伴达成共识信息交流。在简化的ECMAScript格式中，这种扩展的年份表示形式应为6位数字，并始终以+或-开头。 0年被认为是正数，因此以+号开头。与Date Time字符串格式匹配的字符串，如果扩展的年份表示超出时间值范围的时间，则Date.parse将其视为无法识别，并导致该函数返回NaN而不退回到实现特定的行为或启发式方法

>注：带有扩展年份的日期时间值示例：
>
>-271821-04-20T00:00:00Z 271822 B.C.
>-000001-01-01T00:00:00Z 2 B.C.
>+000000-01-01T00:00:00Z 1 B.C.
>+000001-01-01T00:00:00Z 1 A.D.
>+001970-01-01T00:00:00Z 1970 A.D.
>+002009-12-15T00:00:00Z 2009 A.D.
>+275760-09-13T00:00:00Z 275760 A.D.

### 20.3.2 Date 构造器 <div id="sec-date-constructor"></div>

Date构造函数：

- 是内部对象％Date％。
- 是全局对象的Date属性的初始值。
- 在作为构造函数调用时创建并初始化一个新的Date对象。
- 当作为函数而不是构造函数调用时，返回表示当前时间（UTC）的字符串。
- 是一个函数，其行为根据其参数的数量和类型而重载。
- 设计为可归类的。它可以用作类定义的extends子句的值。打算继承指定的Date行为的子类构造函数必须包括对Date构造函数的超级调用，以使用[[DateValue]]内部插槽创建和初始化子类实例。
- 具有 "length" 属性，其值为7。

#### 20.3.2.1 Date ( year, month [ , date [ , hours [ , minutes [ , seconds [ , ms ] ] ] ] ] ) <div id="sec-date-year-month-date-hours-minutes-seconds-ms"></div>

仅当使用至少两个参数调用Date构造函数时，此描述才适用。

调用Date函数时，将执行以下步骤：

1. 令 numberOfArgs 为传递给此函数调用的参数数量。
2. 断言：numberOfArgs ≥ 2.
3. 若 NewTarget 是 undefined，那么
    1. 令 now 为Number，它是标识当前时间的时间值（UTC）。
    2. 返回 ToDateString(now).
4. 否则，
    1. 令 y 为 ? ToNumber(year).
    2. 令 m 为 ? ToNumber(month).
    3. 若 date 存在，令 dt 为 ? ToNumber(date); 否则，令 dt 为 1.
    4. 若 hours 存在，令 h 为 ? ToNumber(hours); 否则，令 h 为 0.
    5. 若 minutes 存在，令 min 为 ? ToNumber(minutes); 否则，令 min 为 0.
    6. 若 seconds 存在，令 s 为 ? ToNumber(seconds); 否则，令 s 为 0.
    7. 若 ms 存在，令 milli 为 ? ToNumber(ms); 否则，令 milli 为 0.
    8. 若 y 是 NaN，令 yr 为 NaN.
    9. 否则，
        1. 令 yi 为 ! ToInteger(y).
        2. 若 0 ≤ yi ≤ 99, 令 yr 为 1900 + yi; 除此之外，令 yr 为 y.
    10. 令 finalDate 为 MakeDate(MakeDay(yr, m, dt), MakeTime(h, min, s, milli)).
    11. 令 O 为 ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    12. 设置 O.[[DateValue]] 为 TimeClip(UTC(finalDate)).
    13. 返回 O.

#### 20.3.2.2 Date ( value ) <div id="sec-date-value"></div>

仅当使用仅一个参数调用Date构造函数时，此描述才适用

调用Date函数时，将执行以下步骤：

1. 令 numberOfArgs 为传递给此函数调用的参数数量。
2. 断言：numberOfArgs = 1.
3. 若 NewTarget 是 undefined，那么
    1. 令 now 为Number，它是标识当前时间的时间值（UTC）。
    2. 返回 ToDateString(now).
4. 否则，
    1. 若 Type(value) 是对象，并且value具有一个[[DateValue]]内部插槽，那么
        1. 令 tv 为 thisTimeValue(value).
    2. 否则，
        1. 令 v 为 ? ToPrimitive(value).
        2. 若 Type(v) 是 String，那么
            1. 断言：下一步永远不会返回突然完成，因为Type（v）是String。
            2. 令 tv 为 将v解析为日期的结果，与解析方法（20.3.3.2）完全相同。
        3. 否则，
            1. 令 tv 为 ? ToNumber(v).
    3. 令 O 为 ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    4. 设置 O.[[DateValue]] 为 TimeClip(tv).
    5. 返回 O.

#### 20.3.2.3 Date ( ) <div id="sec-date-constructor-date"></div>

仅当不带参数调用Date构造函数时，此描述才适用。

调用Date函数时，将执行以下步骤：

1. 令 numberOfArgs 为传递给此函数调用的参数数量。
2. 断言：numberOfArgs = 0.
3. 若 NewTarget 是 undefined，那么
    1. 令 now 为Number，它是标识当前时间的时间值（UTC）。
    2. 返回 ToDateString(now).
4. 否则，
    1. 令 O 为 ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    2. 设置 O.[[DateValue]] 为标识当前时间的时间值（UTC）。
    3. 返回 O.

### 20.3.3 Date 构造器属性 <div id="sec-properties-of-the-date-constructor"></div>

Date构造函数：

- 有一个[[Prototype]]内部插槽，其值是固有对象％FunctionPrototype％。
- 具有以下属性：

#### 20.3.3.1 Date.now ( ) <div id="sec-date.now"></div>

now函数返回一个Number值，该值是指定对now进行回调的UTC日期和时间的时间值。

#### 20.3.3.2 Date.parse ( string ) <div id="sec-date.parse"></div>

解析函数将ToString运算符应用于其参数。如果ToString导致突然完成，则立即返回完成记录。否则parse将结果String解释为日期和时间。它返回一个数字，即与日期和时间相对应的UTC时间值。根据字符串的内容，字符串可以解释为本地时间，UTC时间或其他时区中的时间。该函数首先尝试根据日期时间字符串格式（20.3.1.15）中描述的格式（包括扩展年份）解析字符串。如果字符串不符合该格式，则该函数可能会退回到任何特定于实现的启发式或特定于实现的日期格式。无法识别的字符串或包含超出范围的格式字段值的字符串应使Date.parse返回NaN。

如果x是在特定ECMAScript实现中毫秒数为零的任何Date对象，则如果引用的所有属性都有其初始值，则以下所有表达式在该实现中应产生相同的数值：

```
x.valueOf()
Date.parse(x.toString())
Date.parse(x.toUTCString())
Date.parse(x.toISOString())
```

但是，表达式

```
Date.parse(x.toLocaleString())
```

不需要产生与前面三个表达式相同的Number值，并且通常，如果给定任何不符合Date Time字符串格式（20.3.1.15）的String值，则Date.parse产生的值取决于实现。而toString或toUTCString方法无法在该实现中产生

#### 20.3.3.3 Date.prototype <div id="sec-date.prototype"></div>

Date.prototype的初始值为内部对象％DatePrototype％。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.3.3.4 Date.UTC ( year [ , month [ , date [ , hours [ , minutes [ , seconds [ , ms ] ] ] ] ] ] ) <div id="sec-date.utc"></div>

调用UTC函数时，将执行以下步骤：

1. 令 y 为 ? ToNumber(year).
2. 若 month 存在，令 m 为 ? ToNumber(month); 否则，令 m 为 0.
3. 若 date 存在，令 dt 为 ? ToNumber(date); 否则，令 dt 为 1.
4. 若 hours 存在，令 h 为 ? ToNumber(hours); 否则，令 h 为 0.
5. 若 minutes 存在，令 min 为 ? ToNumber(minutes); 否则，令 min 为 0.
6. 若 seconds 存在，令 s 为 ? ToNumber(seconds); 否则，令 s 为 0.
7. 若 ms 存在，令 milli 为 ? ToNumber(ms); 否则，令 milli 为 0.
8. 若 y 是 NaN，令 yr 为 NaN.
9. 否则，
1. 令 yi 为 ! ToInteger(y).
2. 若 0 ≤ yi ≤ 99, 令 yr 为 1900 + yi; 除此之外，令 yr 为 y.
10. 返回 TimeClip(MakeDate(MakeDay(yr, m, dt), MakeTime(h, min, s, milli))).

UTC函数的 "length" 属性为7。

> 注：UTC函数与Date构造函数有两个不同之处：它以数字形式返回时间值，而不是创建Date对象，并且将UTC中的参数解释为本地时间。

### 20.3.4 Date 原型对象属性 <div id="sec-properties-of-the-date-prototype-object"></div>

Date原型对象：

- 是内部对象％DatePrototype％。
- 本身就是一个普通的对象。
- 不是Date实例，并且没有[[DateValue]]内部插槽。
- 有一个[[Prototype]]内部插槽，其值是内部对象％ObjectPrototype％。

除非明确定义，否则以下定义的Date原型对象的方法不是通用的，并且传递给它们的this值必须是具有[[DateValue]]内部插槽且已初始化为时间值的对象。

抽象操作 thisTimeValue（value）执行以下步骤：

1. 若 Type(value) 是对象，并且value具有一个[[DateValue]]内部插槽，那么
1. 返回 value.[[DateValue]].
2. 抛出 TypeError 异常

在对作为Date原型对象的属性的函数的以下描述中，短语“ this Date object”是指作为函数调用的this值的对象。如果此值的类型不是Object，则抛出TypeError异常。方法规范中的短语“此时间值”是指通过调用抽象操作thisTimeValue并将方法调用的this值作为参数传递而返回的结果。

#### 20.3.4.1 Date.prototype.constructor <div id="sec-date.prototype.constructor"></div>

Date.prototype.constructor的初始值为内部对象％Date％

#### 20.3.4.2 Date.prototype.getDate ( ) <div id="sec-date.prototype.getdate"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 DateFromTime(LocalTime(t)).

#### 20.3.4.3 Date.prototype.getDay ( ) <div id="sec-date.prototype.getday"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 WeekDay(LocalTime(t)).

#### 20.3.4.4 Date.prototype.getFullYear ( ) <div id="sec-date.prototype.getfullyear"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 YearFromTime(LocalTime(t)).

#### 20.3.4.5 Date.prototype.getHours ( ) <div id="sec-date.prototype.gethours"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 HourFromTime(LocalTime(t))

#### 20.3.4.6 Date.prototype.getMilliseconds ( ) <div id="sec-date.prototype.getmilliseconds"></div>

The following steps are performed

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 msFromTime(LocalTime(t))

#### 20.3.4.7 Date.prototype.getMinutes ( ) <div id="sec-date.prototype.getminutes"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 MinFromTime(LocalTime(t)).

#### 20.3.4.8 Date.prototype.getMonth ( ) <div id="sec-date.prototype.getmonth"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 MonthFromTime(LocalTime(t)).

#### 20.3.4.9 Date.prototype.getSeconds ( ) <div id="sec-date.prototype.getseconds"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 SecFromTime(LocalTime(t)).

#### 20.3.4.10 Date.prototype.getTime ( ) <div id="sec-date.prototype.gettime"></div>

执行以下步骤：

1. 返回 ? thisTimeValue(this value).

#### 20.3.4.11 Date.prototype.getTimezoneOffset ( ) <div id="sec-date.prototype.gettimezoneoffset"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 (t - LocalTime(t)) / msPerMinute

#### 20.3.4.12 Date.prototype.getUTCDate ( ) <div id="sec-date.prototype.getutcdate"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 DateFromTime(t).

#### 20.3.4.13 Date.prototype.getUTCDay ( ) <div id="sec-date.prototype.getutcday"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 WeekDay(t).

#### 20.3.4.14 Date.prototype.getUTCFullYear ( ) <div id="sec-date.prototype.getutcfullyear"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 YearFromTime(t)

#### 20.3.4.15 Date.prototype.getUTCHours ( ) <div id="sec-date.prototype.getutchours"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 HourFromTime(t).

#### 20.3.4.16 Date.prototype.getUTCMilliseconds ( ) <div id="sec-date.prototype.getutcmilliseconds"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 msFromTime(t).

#### 20.3.4.17 Date.prototype.getUTCMinutes ( ) <div id="sec-date.prototype.getutcminutes"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 MinFromTime(t).

#### 20.3.4.18 Date.prototype.getUTCMonth ( ) <div id="sec-date.prototype.getutcmonth"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 MonthFromTime(t).

#### 20.3.4.19 Date.prototype.getUTCSeconds ( ) <div id="sec-date.prototype.getutcseconds"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，返回 NaN.
3. 返回 SecFromTime(t).

#### 20.3.4.20 Date.prototype.setDate ( date ) <div id="sec-date.prototype.setdate"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 dt 为 ? ToNumber(date).
3. 令 newDate 为 MakeDate(MakeDay(YearFromTime(t), MonthFromTime(t), dt), TimeWithinDay(t)).
4. 令 u 为 TimeClip(UTC(newDate)).
5. 设置 the [[DateValue]]此Date对象的内部插槽为 u.
6. 返回 u.

#### 20.3.4.21 Date.prototype.setFullYear ( year [ , month [ , date ] ] ) <div id="sec-date.prototype.setfullyear"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN, 设置 t to +0; 除此之外，set t 为 LocalTime(t).
3. 令 y 为 ? ToNumber(year).
4. 若 month 不存在, 令 m 为 MonthFromTime(t); 除此之外，令 m 为 ? ToNumber(month).
5. 若 date 不存在, 令 dt 为 DateFromTime(t); 除此之外，令 dt 为 ? ToNumber(date).
6. 令 newDate 为 MakeDate(MakeDay(y, m, dt), TimeWithinDay(t)).
7. 令 u 为 TimeClip(UTC(newDate)).
8. 设置 the [[DateValue]]此Date对象的内部插槽为 u.
9. 返回 u.

setFullYear方法的 "length" 属性为3。

> 注意：如果不存在month，则此方法的行为就好像存在一个具有值getMonth（）的month一样。如果date不存在，则其行为就好像存在值getDate（）的date一样。

#### 20.3.4.22 Date.prototype.setHours ( hour [ , min [ , sec [ , ms ] ] ] ) <div id="sec-date.prototype.sethours"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 h 为 ? ToNumber(hour).
3. 若 min 不存在, 令 m 为 MinFromTime(t); 除此之外，令 m 为 ? ToNumber(min).
4. 若 sec 不存在, 令 s 为 SecFromTime(t); 除此之外，令 s 为 ? ToNumber(sec).
5. 若 ms 不存在, 令 milli 为 msFromTime(t); 除此之外，令 milli 为 ? ToNumber(ms).
6. 令 date 为 MakeDate(Day(t), MakeTime(h, m, s, milli)).
7. 令 u 为 TimeClip(UTC(date)).
8. 设置 the [[DateValue]]此Date对象的内部插槽为 u.
9. 返回 u.

setHours方法的 "length" 属性为4。

> 注：如果不存在min，则此方法的行为就像使用值getMinutes（）存在min一样。如果sec不存在，则其行为就好像sec存在，其值是getSeconds（）。如果不存在ms，则其行为就好像存在ms且其值为getMilliseconds（）。

#### 20.3.4.23 Date.prototype.setMilliseconds ( ms ) <div id="sec-date.prototype.setmilliseconds"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 设置 ms 为 ? ToNumber(ms).
3. 令 time 为 MakeTime(HourFromTime(t), MinFromTime(t), SecFromTime(t), ms).
4. 令 u 为 TimeClip(UTC(MakeDate(Day(t), time))).
5. 设置 the [[DateValue]]此Date对象的内部插槽为 u.
6. 返回 u

#### 20.3.4.24 Date.prototype.setMinutes ( min [ , sec [ , ms ] ] ) <div id="sec-date.prototype.setminutes"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 m 为 ? ToNumber(min).
3. 若 sec 不存在, 令 s 为 SecFromTime(t); 除此之外，令 s 为 ? ToNumber(sec).
4. 若 ms 不存在, 令 milli 为 msFromTime(t); 除此之外，令 milli 为 ? ToNumber(ms).
5. 令 date 为 MakeDate(Day(t), MakeTime(HourFromTime(t), m, s, milli)).
6. 令 u 为 TimeClip(UTC(date)).
7. 设置 the [[DateValue]]此Date对象的内部插槽为 u.
8. 返回 u

setMinutes 方法的 "length" 属性为3。

>注：如果不存在sec，则此方法的行为就像sec存在，其值是getSeconds（）。如果不存在ms，则其行为就好像ms的值是getMilliseconds（）。

#### 20.3.4.25 Date.prototype.setMonth ( month [ , date ] ) <div id="sec-date.prototype.setmonth"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 m 为 ? ToNumber(month).
3. 若 date 不存在, 令 dt 为 DateFromTime(t); 除此之外，令 dt 为 ? ToNumber(date).
4. 令 newDate 为 MakeDate(MakeDay(YearFromTime(t), m, dt), TimeWithinDay(t)).
5. 令 u 为 TimeClip(UTC(newDate)).
6. 设置 the [[DateValue]]此Date对象的内部插槽为 u.
7. 返回 u.

setMonth 方法的 "length" 属性为2。

> 注：如果不存在date，则此方法的行为就像使用值getDate（）存在date一样。

#### 20.3.4.26 Date.prototype.setSeconds ( sec [ , ms ] ) <div id="sec-date.prototype.setseconds"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 s 为 ? ToNumber(sec).
3. 若 ms 不存在, 令 milli 为 msFromTime(t); 除此之外，令 milli 为 ? ToNumber(ms).
4. 令 date 为 MakeDate(Day(t), MakeTime(HourFromTime(t), MinFromTime(t), s, milli)).
5. 令 u 为 TimeClip(UTC(date)).
6. 设置 the [[DateValue]]此Date对象的内部插槽为 u.
7. 返回 u.

setSeconds 方法的 "length" 属性为2。

> 注：如果不存在ms，则此方法的行为就像ms存在，其值是getMilliseconds（）。

#### 20.3.4.27 Date.prototype.setTime ( time ) <div id="sec-date.prototype.settime"></div>

执行以下步骤：

1. Perform ? thisTimeValue(this value).
2. 令 t 为 ? ToNumber(time).
3. 令 v 为 TimeClip(t).
4. 设置 the [[DateValue]]此Date对象的内部插槽为 v.
5. 返回 v.

#### 20.3.4.28 Date.prototype.setUTCDate ( date ) <div id="sec-date.prototype.setutcdate"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 dt 为 ? ToNumber(date).
3. 令 newDate 为 MakeDate(MakeDay(YearFromTime(t), MonthFromTime(t), dt), TimeWithinDay(t)).
4. 令 v 为 TimeClip(newDate).
5. 设置 the [[DateValue]]此Date对象的内部插槽为 v.
6. 返回 v

#### 20.3.4.29 Date.prototype.setUTCFullYear ( year [ , month [ , date ] ] ) <div id="sec-date.prototype.setutcfullyear"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，设置 t 为 +0.
3. 令 y 为 ? ToNumber(year).
4. 若 month 不存在, 令 m 为 MonthFromTime(t); 除此之外，令 m 为 ? ToNumber(month).
5. 若 date 不存在, 令 dt 为 DateFromTime(t); 除此之外，令 dt 为 ? ToNumber(date).
6. 令 newDate 为 MakeDate(MakeDay(y, m, dt), TimeWithinDay(t)).
7. 令 v 为 TimeClip(newDate).
8. 设置 the [[DateValue]]此Date对象的内部插槽为 v.
9. 返回 v

setUTCFullYear 方法的 "length" 属性为3。

> 注：如果不存在month，则此方法的行为就像使用值getUTCMonth（）一样存在month。如果date不存在，则其行为就好像存在值getUTCDate（）的date一样。

#### 20.3.4.30 Date.prototype.setUTCHours ( hour [ , min [ , sec [ , ms ] ] ] ) <div id="sec-date.prototype.setutchours"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 h 为 ? ToNumber(hour).
3. 若 min 不存在, 令 m 为 MinFromTime(t); 除此之外，令 m 为 ? ToNumber(min).
4. 若 sec 不存在, 令 s 为 SecFromTime(t); 除此之外，令 s 为 ? ToNumber(sec).
5. 若 ms 不存在, 令 milli 为 msFromTime(t); 除此之外，令 milli 为 ? ToNumber(ms).
6. 令 newDate 为 MakeDate(Day(t), MakeTime(h, m, s, milli)).
7. 令 v 为 TimeClip(newDate).
8. 设置 the [[DateValue]]此Date对象的内部插槽为 v.
9. 返回 v

setUTCHours 方法的 "length" 属性为4。

> 注：如果不存在min，则此方法的行为就好像存在值getUTCMinutes（）的min一样。如果秒不是
> 如果存在，则其行为就像sec存在，其值是getUTCSeconds（）。如果不存在ms，则其行为就好像ms的值是getUTCMilliseconds（）。

#### 20.3.4.31 Date.prototype.setUTCMilliseconds ( ms ) <div id="sec-date.prototype.setutcmilliseconds"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 milli 为 ? ToNumber(ms).
3. 令 time 为 MakeTime(HourFromTime(t), MinFromTime(t), SecFromTime(t), milli).
4. 令 v 为 TimeClip(MakeDate(Day(t), time)).
5. 设置 the [[DateValue]]此Date对象的内部插槽为 v.
6. 返回 v.

#### 20.3.4.32 Date.prototype.setUTCMinutes ( min [ , sec [ , ms ] ] ) <div id="sec-date.prototype.setutcminutes"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 m 为 ? ToNumber(min).
3. 若 sec 不存在，令 s 为 SecFromTime(t).
4. 否则，
    1. 令 s 为 ? ToNumber(sec).
5. 若 ms 不存在，令 milli 为 msFromTime(t).
6. 否则，
    1. 令 milli 为 ? ToNumber(ms).
7. 令 date 为 MakeDate(Day(t), MakeTime(HourFromTime(t), m, s, milli)).
8. 令 v 为 TimeClip(date).
9. 设置 the [[DateValue]]此Date对象的内部插槽为 v.
10. 返回 v.

setUTCMinutes 方法的 "length" 属性为3。

> 注：如果不存在sec，则此方法的行为就像sec存在，其值是getUTCSeconds（）。如果不存在ms，则该函数的行为就好像存在ms，且其值由getUTCMilliseconds（）返回。

#### 20.3.4.33 Date.prototype.setUTCMonth ( month [ , date ] ) <div id="sec-date.prototype.setutcmonth"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 m 为 ? ToNumber(month).
3. 若 date 不存在，令 dt 为 DateFromTime(t).
4. 否则，
    1. 令 dt 为 ? ToNumber(date).
5. 令 newDate 为 MakeDate(MakeDay(YearFromTime(t), m, dt), TimeWithinDay(t)).
6. 令 v 为 TimeClip(newDate).
7. 设置 the [[DateValue]]此Date对象的内部插槽为 v.
8. 返回 v

setUTCMonth 方法的 "length" 属性为2。

> 注：如果不存在date，则此方法的行为就像使用值getUTCDate（）存在date一样。

#### 20.3.4.34 Date.prototype.setUTCSeconds ( sec [ , ms ] ) <div id="sec-date.prototype.setutcseconds"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 s 为 ? ToNumber(sec).
3. 若 ms 不存在，令 milli 为 msFromTime(t).
4. 否则，
    1. 令 milli 为 ? ToNumber(ms).
5. 令 date 为 MakeDate(Day(t), MakeTime(HourFromTime(t), MinFromTime(t), s, milli)).
6. 令 v 为 TimeClip(date).
7. 设置 the [[DateValue]]此Date对象的内部插槽为 v.
8. 返回 v.

setUTCSeconds 方法的 "length" 属性为2。

> 注：如果不存在ms，则此方法的行为就像ms存在，其值是getUTCMilliseconds（）。

#### 20.3.4.35 Date.prototype.toDateString ( ) <div id="sec-date.prototype.todatestring"></div>

执行以下步骤：

1. 令 O 为 this Date object.
2. 令 tv 为 ? thisTimeValue(O).
3. 若 tv 是 NaN，返回 "Invalid Date".
4. 令 t 为 LocalTime(tv).
5. 返回 DateString(t).

#### 20.3.4.36 Date.prototype.toISOString ( ) <div id="sec-date.prototype.toisostring"></div>

此函数返回一个String值，该值表示与该时间值相对应的时间中的实例。字符串的格式是20.3.1.15中定义的日期时间字符串格式。所有字段都存在于字符串中。如果此时间值不是有限数字，或者年份不是可以用该格式表示的值（如果需要，则使用扩展年份格式），将抛出RangeError异常。

#### 20.3.4.37 Date.prototype.toJSON ( key ) <div id="sec-date.prototype.tojson"></div>

此函数提供JSON.stringify（24.5.2）使用的Date对象的String表示形式。

使用参数键调用toJSON方法时，将执行以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 tv 为 ? ToPrimitive(O, hint Number).
3. 若 Type(tv) is Number and tv 不是 finite，返回 null.
4. 返回 ? Invoke(O, "toISOString")

> 注1：该参数被忽略。

> 注2：toJSON函数是有意通用的。它不需要其此值是Date对象。因此，可以将其转移到其他种类的对象中用作方法。但是，它确实要求任何此类对象都具有toISOString方法。

#### 20.3.4.38 Date.prototype.toLocaleDateString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-date.prototype.tolocaledatestring"></div>

包含ECMA-402国际化API的ECMAScript实现必须实现ECMA-402规范中指定的Date.prototype.toLocaleDateString方法。如果ECMAScript实现不包含ECMA-402 API，则使用以下toLocaleDateString方法的规范

该函数返回一个字符串值。 String的内容与实现有关，但是旨在以一种方便的，易于理解的形式表示当前时区中Date的“ date”部分，该形式对应于宿主环境的当前语言环境的约定。

ECMA-402规范中定义了此方法的可选参数的含义；不包含ECMA-402支持的实现不得将这些参数位置用于其他任何用途。

#### 20.3.4.39 Date.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-date.prototype.tolocalestring"></div>

包含ECMA-402国际化API的ECMAScript实现必须实现ECMA-402规范中指定的Date.prototype.toLocaleString方法。如果ECMAScript实现不包含ECMA-402 API，则使用以下toLocaleString方法的规范。

该函数返回一个字符串值。 String的内容取决于实现，但是旨在以一种方便且易于阅读的形式表示当前时区中的Date，该形式对应于主机环境当前语言环境的约定。

ECMA-402规范中定义了此方法的可选参数的含义；不包含ECMA-402支持的实现不得将这些参数位置用于其他任何用途。

#### 20.3.4.40 Date.prototype.toLocaleTimeString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-date.prototype.tolocaletimestring"></div>

包含ECMA-402国际化API的ECMAScript实现必须实现ECMA-402规范中指定的Date.prototype.toLocaleTimeString方法。如果ECMAScript实现不包含ECMA-402 API，则使用以下toLocaleTimeString方法的规范。

该函数返回一个字符串值。 String的内容与实现有关，但是旨在以一种方便的，易于理解的形式表示当前时区中Date的“时间”部分，该形式与宿主环境的当前语言环境的约定相对应。

ECMA-402规范中定义了此方法的可选参数的含义；不包含ECMA-402支持的实现不得将这些参数位置用于其他任何用途。

#### 20.3.4.41 Date.prototype.toString ( ) <div id="sec-date.prototype.tostring"></div>

执行以下步骤：

1. 令 tv 为 ? thisTimeValue(this value).
2. 返回 ToDateString(tv).

> 注1：对于任何毫秒数为零的Date对象d，Date.parse（d.toString（））的结果等于d.valueOf（）。见20.3.3.2。

> 注2：toString函数不是通用的；如果此值不是Date对象，则抛出TypeError异常。因此，不能将其作为方法传递给其他类型的对象。

##### 20.3.4.41.1 RS: TimeString ( tv ) <div id="sec-timestring"></div>

执行以下步骤：

1. 断言：Type(tv) is Number.
2. 断言：tv is not NaN.
3. 令 hour 为 HourFromTime（tv）的字符串表示形式，格式为两位十进制数字，如有必要，在左侧填充零。
4. 令 minute 为 MinFromTime（tv）的字符串表示形式，格式为两位十进制数字，如有必要，在左侧填充零。
5. 令 second 为 SecFromTime（tv）的字符串表示形式，格式为两位十进制数字，如有必要，在左侧填充零。
6. 返回小时，“:”，分钟，“:”，秒，代码单元0x0020（SPACE）和“ GMT”的字符串连接。

##### 20.3.4.41.2 RS: DateString ( tv ) <div id="sec-datestring"></div>

执行以下步骤：

1. 断言：Type(tv) 是 Number.

2. 断言：tv 不是 NaN.

3. 令 weekday 为表 49 中带有数字WeekDay（tv）的条目的名称。

4. 令 month 为表 50 中条目的名称，其编号为MonthFromTime（tv）。

5. 令 day 为 DateFromTime（tv）的字符串表示形式，格式为两位十进制数字，如有必要，在左侧填充零。

6. 令 year 为 YearFromTime（tv）的字符串表示形式，格式为至少四位数的十进制数字，如有必要，在左侧用零填充。

7. 返回 星期几，代码单位0x0020（空格），月份，代码单位0x0020（空格），日期，代码单位0x0020（空格）和年份的字符串连接。ar.

表49：星期几的名称

| 数字 | 名称  |
| ---- | ----- |
| 0    | "Sun" |
| 1    | "Mon" |
| 2    | "Tue" |
| 3    | "Wed" |
| 4    | "Thu" |
| 5    | "Fri" |
| 6    | "Sat" |

表50：一年中的月份名称

| 数字 | 名称  |
| ---- | ----- |
| 0    | "Jan" |
| 1    | "Feb" |
| 2    | "Mar" |
| 3    | "Apr" |
| 4    | "May" |
| 5    | "Jun" |
| 6    | "Jul" |
| 7    | "Aug" |
| 8    | "Sep" |
| 9    | "Oct" |
| 10   | "Nov" |
| 11   | "Dec" |

##### 20.3.4.41.3 RS: TimeZoneString ( tv ) <div id="sec-timezoneestring"></div>

执行以下步骤：

1. 断言：Type(tv) is Number.
2. 断言：tv is not NaN.
3. 令 offset 为 LocalTZA(tv, true).
4. 若 offset ≥ 0, 令 offsetSign 为 "+"; 除此之外，令 offsetSign 为 "-".
5. 令 offsetMin 为 MinFromTime（abs（offset））的字符串表示形式，格式为两位十进制数，如有必要，在左侧填充零。
6. 令 offsetHour 为 HourFromTime（abs（offset））的字符串表示形式，格式为两位十进制数字，如有必要，在左侧填充零。
7. 令 tzName 为实现定义的字符串，可以是代码单元0x0020（空格），代码单元0x0028（左括号），实现相关的时区名称和代码单元0x0029（右括号）的空字符串或字符串串联。
8. 返回offsetSign，offsetHour，offsetMin和tzName的字符串串联。

##### 20.3.4.41.4 RS: ToDateString ( tv ) <div id="sec-todatestring"></div>

执行以下步骤：

1. 断言：Type(tv) is Number.
2. 若 tv 是 NaN，返回 "Invalid Date".
3. 令 t 为 LocalTime(tv).
4. 返回DateString（t），代码单元0x0020（空格），TimeString（t）和TimeZoneString（tv）的字符串连接。

#### 20.3.4.42 Date.prototype.toTimeString ( ) <div id="sec-date.prototype.totimestring"></div>

执行以下步骤：

1. 令 O 为此Date对象。
2. 令 tv 为 ? thisTimeValue(O).
3. 若 tv 是 NaN，返回 "Invalid Date".
4. 令 t 为 LocalTime(tv).
5. 返回TimeString（t）和TimeZoneString（tv）的字符串连接

#### 20.3.4.43 Date.prototype.toUTCString ( ) <div id="sec-date.prototype.toutcstring"></div>

执行以下步骤：

1. 令 O 为此Date对象。
2. 令 tv 为 ? thisTimeValue(O).
3. 若 tv 是 NaN，返回 "Invalid Date".
4. 令 weekday 为表49中带有数字WeekDay（tv）的条目的名称。
5. 令 month 为表50中条目的名称，其编号为MonthFromTime（tv）。
6. 令 day 为DateFromTime（tv）的字符串表示形式，格式为两位十进制数字，如有必要，在左侧填充零。
7. 令 year 为YearFromTime（tv）的字符串表示形式，格式为至少四位数的十进制数字，如有必要，在左侧用零填充。
8. 返回 星期几，“,”，代码单元0x0020（SPACE），日期，代码单元0x0020（SPACE），月份，代码单元0x0020（SPACE），年份，代码单元0x0020（SPACE）和TimeString（tv）的字符串连接。

#### 20.3.4.44 Date.prototype.valueOf ( ) <div id="sec-date.prototype.valueof"></div>

执行以下步骤：

1. 返回 ? thisTimeValue(this value)

#### 20.3.4.45 Date.prototype [ @@toPrimitive ] ( hint ) <div id="sec-date.prototype-@@toprimitive"></div>

ECMAScript语言操作员调用此函数以将Date对象转换为原始值。提示的允许值为“default”，“number”和“string”。日期对象在内置ECMAScript对象之间是唯一的，因为它们将“default”视为等同于“string”，所有其他内置ECMAScript对象将“default”视为等同于“number”。

当使用参数提示调用@@toPrimitive方法时，将执行以下步骤：

1. 令 O 为 this 值。
2. 若 Type(O) 不是 Object，抛出 TypeError 异常。
3. 若 hint 是字符串值 "string" 或字符串值 "default"，那么
    1. 令 tryFirst 为 "string".
4. 否则，若 hint 是字符串值 "number"，那么
    1. 令 tryFirst 为 "number".
5. 否则，抛出 TypeError 异常
6. 返回 ? OrdinaryToPrimitive(O, tryFirst).

该函数的名称属性的值为“ [Symbol.toPrimitive]”。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

### 20.3.5 Date 实例属性 <div id="sec-properties-of-date-instances"></div>

Date实例是从Date原型对象继承属性的普通对象。日期实例还具有一个[[DateValue]]内部插槽。内部[[DateValue]]插槽是此Date对象表示的时间值。