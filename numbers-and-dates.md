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

月份值为0表示一月； 1表示2月； 2表示3月； 3表示四月； 4表示五月； 5表示6月； 6表示7月； 7表示8月； 8表示9月； 9表示十月； 10指定11月；和11指定12月。请注意，MonthFromTime（0）= 0，对应于1970年1月1日，星期四。

#### 20.3.1.5 日期数 <div id="sec-date-number"></div>

A date number is identified by an integer in the range 1 through 31, inclusive. The mapping DateFromTime(t) from a time value t to a date number is defined by:

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

The weekday for a particular time value t is defined as

​	WeekDay(t) = (Day(t) + 4) modulo 7

A weekday value of 0 specifies Sunday; 1 specifies Monday; 2 specifies Tuesday; 3 specifies Wednesday; 4 specifies Thursday; 5 specifies Friday; and 6 specifies Saturday. Note that WeekDay(0) = 4, corresponding to Thursday, 01 January, 1970.

#### 20.3.1.7 LocalTZA ( t, isUTC ) <div id="sec-local-time-zone-adjustment"></div>

LocalTZA( t, isUTC ) is an implementation-defined algorithm that must 返回 a number representing milliseconds suitable for adding to a Time Value. The local political rules for standard time and daylight saving time in effect at t should be used to determine the result in the way specified in the following three paragraphs.

When isUTC is true, LocalTZA( t, true ) should 返回 the offset of the local time zone from UTC measured in milliseconds at time represented by time value t (UTC). When 结果为 added to t (UTC), it should yield the local time.

When isUTC is false, LocalTZA( t, false ) should 返回 the offset of the local time zone from UTC measured in milliseconds at local time represented by time value t local = t. When 结果为 subtracted from the local time t local, it should yield the corresponding UTC.

When t local represents local time repeating multiple times at a negative time zone transition (e.g. when the daylight saving time ends or the time zone adjustment is decreased due to a time zone rule change) or skipped local time at a positive time zone transitions (e.g. when the daylight saving time starts or the time zone adjustment is increased due to a time zone rule change), t local must be interpreted with the time zone adjustment before the transition.

若 an implementation does not support a conversion described above or if political rules for time t are not available within the implementation，the result must be 0.

>NOTE It is recommended that implementations use the time zone information of the IANA Time Zone Database https://www.iana.org/time-zones/. 
>
>1:30 AM on November 5, 2017 in America/New_York is repeated twice (fall backward), but it must be interpreted as 1:30 AM UTC-04 instead of 1:30 AM UTC-05. LocalTZA(TimeClip(MakeDate(MakeDay(2017, 10, 5), MakeTime(1, 30, 0, 0))), false) is -4 × msPerHour. 
>
>2:30 AM on March 12, 2017 in America/New_York does not exist, but it must be interpreted as 2:30 AM UTC-05 (equivalent to 3:30 AM UTC-04). LocalTZA(TimeClip(MakeDate(MakeDay(2017, 2, 12), MakeTime(2, 30, 0, 0))), false) is -5 × msPerHour.

#### 20.3.1.8 LocalTime ( t ) <div id="sec-localtime"></div>

The abstract operation LocalTime with argument t converts t from UTC to local time by performing the following steps:

1. 返回 t + LocalTZA(t, true).

> NOTE Two different time values (t (UTC)) are converted to the same local time t local at a negative time zone transition when there are repeated times (e.g. the daylight saving time ends or the time zone adjustment is decreased.).

#### 20.3.1.9 UTC ( t ) <div id="sec-utc-t"></div>

The abstract operation UTC with argument t converts t from local time to UTC. It performs the following steps:

1. 返回 t - LocalTZA(t, false).

> NOTE UTC(LocalTime(t)) is not necessarily always equal to t. LocalTime(UTC(t local)) is not necessarily always equal to t local, either.

#### 20.3.1.10 小时，分钟，秒和毫秒 <div id="sec-hours-minutes-second-and-milliseconds"></div>

The following abstract operations are useful in decomposing time values:

​	HourFromTime(t) = floor(t / msPerHour) modulo HoursPerDay
​	MinFromTime(t) = floor(t / msPerMinute) modulo MinutesPerHour
​	SecFromTime(t) = floor(t / msPerSecond) modulo SecondsPerMinute
​	msFromTime(t) = t modulo msPerSecond

where

​	HoursPerDay = 24MinutesPerHour = 60
​	SecondsPerMinute = 60
​	msPerSecond = 1000
​	msPerMinute = 60000 = msPerSecond × SecondsPerMinute
​	msPerHour = 3600000 = msPerMinute × MinutesPerHour

#### 20.3.1.11 MakeTime ( hour, min, sec, ms ) <div id="sec-maketime"></div>

The abstract operation MakeTime calculates a number of milliseconds from its four arguments, which must be ECMAScript Number values. This operator functions as follows:

1. 若 hour 不是 finite or min is not finite or sec is not finite or ms is not finite，返回 NaN.
2. 令 h 为 ! ToInteger(hour).
3. 令 m 为 ! ToInteger(min).
4. 令 s 为 ! ToInteger(sec).
5. 令 milli 为 ! ToInteger(ms).
6. 令 t 为 h * msPerHour + m * msPerMinute + s * msPerSecond + milli, performing the arithmetic according to IEEE 754-2008 rules (that is, as if using the ECMAScript operators * and +).
7. 返回 t.

#### 20.3.1.12 MakeDay ( year, month, date ) <div id="sec-makeday"></div>

The abstract operation MakeDay calculates a number of days from its three arguments, which must be ECMAScript Number values. This operator functions as follows:

1. 若 year 不是 finite or month is not finite or date is not finite，返回 NaN.
2. 令 y 为 ! ToInteger(year).
3. 令 m 为 ! ToInteger(month).
4. 令 dt 为 ! ToInteger(date).
5. 令 ym 为 y + floor(m / 12).
6. 令 mn 为 m modulo 12.
7. Find a value t such that YearFromTime(t) is ym and MonthFromTime(t) is mn and DateFromTime(t) is 1; but 若 this 不是 possible (because some argument is out of range)，返回 NaN.
8. 返回 Day(t) + dt - 1.

#### 20.3.1.13 MakeDate ( day, time ) <div id="sec-makedate"></div>

The abstract operation MakeDate calculates a number of milliseconds from its two arguments, which must be ECMAScript Number values. This operator functions as follows:

1. 若 day 不是 finite or time is not finite，返回 NaN.
2. 返回 day × msPerDay + time.

#### 20.3.1.14 TimeClip ( time ) <div id="sec-timeclip"></div>

The abstract operation TimeClip calculates a number of milliseconds from its argument, which must be an ECMAScript Number value. This operator functions as follows:

1. 若 time 不是 finite，返回 NaN.
2. 若 abs(time) > 8.64 × 1015，返回 NaN.
3. 令 clippedTime 为 ! ToInteger(time).
4. 若 clippedTime 是 -0，设置 clippedTime 为 +0.
5. 返回 clippedTime.

> NOTE The point of step 4 is that an implementation is permitted a choice of internal representations of time values, for example as a 64-bit signed integer or as a 64-bit floating-point value. Depending on the implementation, this internal representation may or may not distinguish -0 and +0.

#### 20.3.1.15 日期时间字符串格式 <div id="sec-date-time-string-format"></div>

ECMAScript defines a string interchange format for date-times based upon a simplification of the ISO 8601 calendar date extended format. The format is as follows: YYYY-MM-DDTHH:mm:ss.sssZ

Where the fields are as follows:

| `YYYY` | is the decimal digits of the year 0000 to 9999 in the Gregorian calendar. |
| ------ | ------------------------------------------------------------ |
| `-`    | `"-"` (hyphen) appears literally twice in the string.        |
| `MM`   | is the month of the year from 01 (January) to 12 (December). |
| `DD`   | is the day of the month from 01 to 31.                       |
| `T`    | `"T"` appears literally in the string, to indicate the beginning of the time element. |
| `HH`   | is the number of complete hours that have passed since midnight as two decimal digits from 00 to 24. |
| `:`    | `":"` (colon) appears literally twice in the string.         |
| `mm`   | is the number of complete minutes since the start of the hour as two decimal digits from 00 to 59. |
| `ss`   | is the number of complete seconds since the start of the minute as two decimal digits from 00 to 59. |
| `.`    | `"."` (dot) appears literally in the string.                 |
| `sss`  | is the number of complete milliseconds since the start of the second as three decimal digits. |
| `Z`    | is the time zone offset specified as `"Z"` (for UTC) or either `"+"` or `"-"` followed by a time expression `HH:mm` |

This format includes date-only forms:

```
YYYY
YYYY-MM
YYYY-MM-DD
```

It also includes “date-time” forms that consist of one of the above date-only forms immediately followed by one of the following time forms with an optional time zone offset appended:

```
THH:mm
THH:mm:ss
THH:mm:ss.sss
```

All numbers must be base 10. 若 the MM or DD fields are absent "01" is used as the value. 若 the HH, mm，or ss fields are absent "00" is used as the value and the value of an absent sss field is "000". When the time zone offset 是 absent，date-only forms are interpreted as a UTC time and date-time forms are interpreted as a local time.

A string containing out-of-bounds or nonconforming fields is not a valid instance of this format.

> NOTE 1 As every day both starts and ends with midnight, the two notations 00:00 and 24:00 are available to distinguish the two midnights that can be associated with one date. This means that the following two notations refer to exactly the same point in time: 1995-02-04T24:00 and 1995-02-05T00:00. This interpretation of the latter form as "end of a calendar day" is consistent with ISO 8601, even though that specification reserves it for describing time intervals and does not permit it within representations of single points in time. 

> NOTE 2 There exists no international standard that specifies abbreviations for civil time zones like CET, EST, etc. and sometimes the same abbreviation is even used for two very different time zones. For this reason, both ISO 8601 and this format specify numeric representations of time zone offsets.

##### 20.3.1.15.1 年扩展 <div id="sec-expanded-years"></div>

Covering the full time value range of approximately 273,790 years forward or backward from 01 January, 1970 (20.3.1.1) requires representing years before 0 or after 9999. ISO 8601 permits expansion of the year representation, but only by mutual agreement of the partners in information interchange. In the simplified ECMAScript format, such an expanded year representation shall have 6 digits and is always prefixed with a + or - sign. The year 0 is considered positive and hence prefixed with a + sign. Strings matching the Date Time String Format with expanded years representing instants in time outside the range of a time value are treated as unrecognizable by Date.parse and cause that function to 返回 NaN without falling back to implementation-specific behavior or heuristics

>NOTE Examples of date-time values with expanded years:
>
>-271821-04-20T00:00:00Z 271822 B.C.
>-000001-01-01T00:00:00Z 2 B.C.
>+000000-01-01T00:00:00Z 1 B.C.
>+000001-01-01T00:00:00Z 1 A.D.
>+001970-01-01T00:00:00Z 1970 A.D.
>+002009-12-15T00:00:00Z 2009 A.D.
>+275760-09-13T00:00:00Z 275760 A.D.

### 20.3.2 Date 构造器 <div id="sec-date-constructor"></div>

The Date constructor:

- is the intrinsic object %Date%.
- is the initial value of the Date property of the global object.
- creates and initializes a new Date object when called as a constructor.
- returns a String representing the current time (UTC) when called as a function rather than as a constructor.
- is a single function whose behaviour is overloaded based upon the number and types of its arguments.
- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. Subclass constructors that intend to inherit the specified Date behaviour must include a super call to the Date constructor to create and initialize the subclass instance with a [[DateValue]] internal slot.
- has a "length" property whose value is 7.

#### 20.3.2.1 Date ( year, month [ , date [ , hours [ , minutes [ , seconds [ , ms ] ] ] ] ] ) <div id="sec-date-year-month-date-hours-minutes-seconds-ms"></div>

This description applies only if the Date constructor is called with at least two arguments.

When the Date function is called, the following steps are taken:

1. 令 numberOfArgs 为 the number of arguments passed to this function call.
2. 断言：numberOfArgs ≥ 2.
3. 若 NewTarget 是 undefined，那么
    1. 令 now 为 the Number that is the time value (UTC) identifying the current time.
    2. 返回 ToDateString(now).
4. 否则，
    1. 令 y 为 ? ToNumber(year).
    2. 令 m 为 ? ToNumber(month).
    3. 若 date 是 present，令 dt 为 ? ToNumber(date); else 令 dt 为 1.
    4. 若 hours 是 present，令 h 为 ? ToNumber(hours); else 令 h 为 0.
    5. 若 minutes 是 present，令 min 为 ? ToNumber(minutes); else 令 min 为 0.
    6. 若 seconds 是 present，令 s 为 ? ToNumber(seconds); else 令 s 为 0.
    7. 若 ms 是 present，令 milli 为 ? ToNumber(ms); else 令 milli 为 0.
    8. 若 y 是 NaN，令 yr 为 NaN.
    9. 否则，
        1. 令 yi 为 ! ToInteger(y).
        2. 若 0 ≤ yi ≤ 99, 令 yr 为 1900 + yi; otherwise，令 yr 为 y.
    10. 令 finalDate 为 MakeDate(MakeDay(yr, m, dt), MakeTime(h, min, s, milli)).
    11. 令 O 为 ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    12. 设置 O.[[DateValue]] 为 TimeClip(UTC(finalDate)).
    13. 返回 O.

#### 20.3.2.2 Date ( value ) <div id="sec-date-value"></div>

This description applies only if the Date constructor is called with exactly one argument

When the Date function is called, the following steps are taken:

1. 令 numberOfArgs 为 the number of arguments passed to this function call.
2. 断言：numberOfArgs = 1.
3. 若 NewTarget 是 undefined，那么
1. 令 now 为 the Number that is the time value (UTC) identifying the current time.
2. 返回 ToDateString(now).
4. 否则，
    1. 若 Type(value) 是 Object and value has a [[DateValue]] internal slot，那么
        1. 令 tv 为 thisTimeValue(value).
    2. 否则，
        1. 令 v 为 ? ToPrimitive(value).
        2. 若 Type(v) 是 String，那么
            1. 断言：The next step never returns an abrupt completion because Type(v) is String.
            2. 令 tv 为 the result of parsing v as a date, in exactly the same manner as for the parse method (20.3.3.2).
        3. 否则，
            1. 令 tv 为 ? ToNumber(v).
    3. 令 O 为 ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    4. 设置 O.[[DateValue]] 为 TimeClip(tv).
    5. 返回 O.

#### 20.3.2.3 Date ( ) <div id="sec-date-constructor-date"></div>

This description applies only if the Date constructor is called with no arguments.

When the Date function is called, the following steps are taken:

1. 令 numberOfArgs 为 the number of arguments passed to this function call.
2. 断言：numberOfArgs = 0.
3. 若 NewTarget 是 undefined，那么
    1. 令 now 为 the Number that is the time value (UTC) identifying the current time.
    2. 返回 ToDateString(now).
4. 否则，
    1. 令 O 为 ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    2. 设置 O.[[DateValue]] 为 the time value (UTC) identifying the current time.
    3. 返回 O.

### 20.3.3 Date 构造器属性 <div id="sec-properties-of-the-date-constructor"></div>

The Date constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 20.3.3.1 Date.now ( ) <div id="sec-date.now"></div>

The now function returns a Number value that is the time value designating the UTC date and time of the occurrence of the call to now.

#### 20.3.3.2 Date.parse ( string ) <div id="sec-date.parse"></div>

The parse function applies the ToString operator to its argument. 若 ToString results in an abrupt completion the Completion Record 是 immediately 返回ed. 否则，parse interprets the resulting String as a date and time; it returns a Number, the UTC time value corresponding to the date and time. The String may be interpreted as a local time, a UTC time，或 a time in some other time zone, depending on the contents of the String. The function first attempts to parse the String according to the format described in Date Time String Format (20.3.1.15)，including expanded years. If the String does not conform to that format the function may fall back to any implementation-specific heuristics or implementation-specific date formats. Strings that are unrecognizable or contain out-of-bounds format field values shall cause Date.parse to 返回 NaN.

若 x 是 any Date object whose milliseconds amount is zero within a particular implementation of ECMAScript, then all of the following expressions should produce the same numeric value in that implementation，if all the properties referenced have their initial values:

```
x.valueOf()
Date.parse(x.toString())
Date.parse(x.toUTCString())
Date.parse(x.toISOString())
```

However, the expression

```
Date.parse(x.toLocaleString())
```

is not required to produce the same Number value as the preceding three expressions and, in general, the value produced by Date.parse is implementation-dependent when given any String value that does not conform to the Date Time String Format (20.3.1.15) and that could not be produced in that implementation by the toString or toUTCString method

#### 20.3.3.3 Date.prototype <div id="sec-date.prototype"></div>

The initial value of Date.prototype is the intrinsic object %DatePrototype%.

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.3.3.4 Date.UTC ( year [ , month [ , date [ , hours [ , minutes [ , seconds [ , ms ] ] ] ] ] ] ) <div id="sec-date.utc"></div>

When the UTC function is called, the following steps are taken:

1. 令 y 为 ? ToNumber(year).
2. 若 month 是 present，令 m 为 ? ToNumber(month); else 令 m 为 0.
3. 若 date 是 present，令 dt 为 ? ToNumber(date); else 令 dt 为 1.
4. 若 hours 是 present，令 h 为 ? ToNumber(hours); else 令 h 为 0.
5. 若 minutes 是 present，令 min 为 ? ToNumber(minutes); else 令 min 为 0.
6. 若 seconds 是 present，令 s 为 ? ToNumber(seconds); else 令 s 为 0.
7. 若 ms 是 present，令 milli 为 ? ToNumber(ms); else 令 milli 为 0.
8. 若 y 是 NaN，令 yr 为 NaN.
9. 否则，
1. 令 yi 为 ! ToInteger(y).
2. 若 0 ≤ yi ≤ 99, 令 yr 为 1900 + yi; otherwise，令 yr 为 y.
10. 返回 TimeClip(MakeDate(MakeDay(yr, m, dt), MakeTime(h, min, s, milli))).

The "length" property of the UTC function is 7.

> NOTE The UTC function differs from the Date constructor in two ways: it returns a time value as a Number, rather than creating a Date object, and it interprets the arguments in UTC rather than as local time.

### 20.3.4 Date 原型对象属性 <div id="sec-properties-of-the-date-prototype-object"></div>

The Date prototype object:

- is the intrinsic object %DatePrototype%.
- is itself an ordinary object.
- is not a Date instance and does not have a [[DateValue]] internal slot.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

Unless explicitly defined 否则，the methods of the Date prototype object defined below are not generic and the this value passed to them must be an object that has a [[DateValue]] internal slot that has been initialized to a time value.

The abstract operation thisTimeValue(value) performs the following steps:

1. 若 Type(value) 是 Object and value has a [[DateValue]] internal slot，那么
1. 返回 value.[[DateValue]].
2. 抛出 TypeError 异常

In following descriptions of functions that are properties of the Date prototype object, the phrase “this Date object” refers to the object that is the this value for the invocation of the function. 若 the Type of the this value 不是 Object，a TypeError exception is thrown. The phrase “this time value” within the specification of a method refers to the result 返回ed by calling the abstract operation thisTimeValue with the this value of the method invocation passed as the argument.

#### 20.3.4.1 Date.prototype.constructor <div id="sec-date.prototype.constructor"></div>

The initial value of Date.prototype.constructor is the intrinsic object %Date%

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
5. 设置 the [[DateValue]] internal slot of this Date object 为 u.
6. 返回 u.

#### 20.3.4.21 Date.prototype.setFullYear ( year [ , month [ , date ] ] ) <div id="sec-date.prototype.setfullyear"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN, 设置 t to +0; otherwise，set t 为 LocalTime(t).
3. 令 y 为 ? ToNumber(year).
4. 若 month 不存在, 令 m 为 MonthFromTime(t); otherwise，令 m 为 ? ToNumber(month).
5. 若 date 不存在, 令 dt 为 DateFromTime(t); otherwise，令 dt 为 ? ToNumber(date).
6. 令 newDate 为 MakeDate(MakeDay(y, m, dt), TimeWithinDay(t)).
7. 令 u 为 TimeClip(UTC(newDate)).
8. 设置 the [[DateValue]] internal slot of this Date object 为 u.
9. 返回 u.

The "length" property of the setFullYear method is 3.

> NOTE 若 month 不存在, this method behaves as if month was present with the value getMonth(). If date is not present，it behaves as if date was present with the value getDate().

#### 20.3.4.22 Date.prototype.setHours ( hour [ , min [ , sec [ , ms ] ] ] ) <div id="sec-date.prototype.sethours"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 h 为 ? ToNumber(hour).
3. 若 min 不存在, 令 m 为 MinFromTime(t); otherwise，令 m 为 ? ToNumber(min).
4. 若 sec 不存在, 令 s 为 SecFromTime(t); otherwise，令 s 为 ? ToNumber(sec).
5. 若 ms 不存在, 令 milli 为 msFromTime(t); otherwise，令 milli 为 ? ToNumber(ms).
6. 令 date 为 MakeDate(Day(t), MakeTime(h, m, s, milli)).
7. 令 u 为 TimeClip(UTC(date)).
8. 设置 the [[DateValue]] internal slot of this Date object 为 u.
9. 返回 u.

The "length" property of the setHours method is 4.

> NOTE 若 min 不存在, this method behaves as 若 min was present with the value getMinutes(). If sec 不存在，it behaves as if sec was present with the value getSeconds(). If ms is not present，it behaves as if ms was present with the value getMilliseconds().

#### 20.3.4.23 Date.prototype.setMilliseconds ( ms ) <div id="sec-date.prototype.setmilliseconds"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 设置 ms 为 ? ToNumber(ms).
3. 令 time 为 MakeTime(HourFromTime(t), MinFromTime(t), SecFromTime(t), ms).
4. 令 u 为 TimeClip(UTC(MakeDate(Day(t), time))).
5. 设置 the [[DateValue]] internal slot of this Date object 为 u.
6. 返回 u

#### 20.3.4.24 Date.prototype.setMinutes ( min [ , sec [ , ms ] ] ) <div id="sec-date.prototype.setminutes"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 m 为 ? ToNumber(min).
3. 若 sec 不存在, 令 s 为 SecFromTime(t); otherwise，令 s 为 ? ToNumber(sec).
4. 若 ms 不存在, 令 milli 为 msFromTime(t); otherwise，令 milli 为 ? ToNumber(ms).
5. 令 date 为 MakeDate(Day(t), MakeTime(HourFromTime(t), m, s, milli)).
6. 令 u 为 TimeClip(UTC(date)).
7. 设置 the [[DateValue]] internal slot of this Date object 为 u.
8. 返回 u

The "length" property of the setMinutes method is 3.

>NOTE 若 sec 不存在, this method behaves as if sec was present with the value getSeconds(). If ms is not present，this behaves as if ms was present with the value getMilliseconds().

#### 20.3.4.25 Date.prototype.setMonth ( month [ , date ] ) <div id="sec-date.prototype.setmonth"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 m 为 ? ToNumber(month).
3. 若 date 不存在, 令 dt 为 DateFromTime(t); otherwise，令 dt 为 ? ToNumber(date).
4. 令 newDate 为 MakeDate(MakeDay(YearFromTime(t), m, dt), TimeWithinDay(t)).
5. 令 u 为 TimeClip(UTC(newDate)).
6. 设置 the [[DateValue]] internal slot of this Date object 为 u.
7. 返回 u.

The "length" property of the setMonth method is 2.

> NOTE 若 date 不存在，this method behaves as if date was present with the value getDate().

#### 20.3.4.26 Date.prototype.setSeconds ( sec [ , ms ] ) <div id="sec-date.prototype.setseconds"></div>

执行以下步骤：

1. 令 t 为 LocalTime(? thisTimeValue(this value)).
2. 令 s 为 ? ToNumber(sec).
3. 若 ms 不存在, 令 milli 为 msFromTime(t); otherwise，令 milli 为 ? ToNumber(ms).
4. 令 date 为 MakeDate(Day(t), MakeTime(HourFromTime(t), MinFromTime(t), s, milli)).
5. 令 u 为 TimeClip(UTC(date)).
6. 设置 the [[DateValue]] internal slot of this Date object 为 u.
7. 返回 u.

The "length" property of the setSeconds method is 2.

> NOTE 若 ms 不存在，this method behaves as if ms was present with the value getMilliseconds().

#### 20.3.4.27 Date.prototype.setTime ( time ) <div id="sec-date.prototype.settime"></div>

执行以下步骤：

1. Perform ? thisTimeValue(this value).
2. 令 t 为 ? ToNumber(time).
3. 令 v 为 TimeClip(t).
4. 设置 the [[DateValue]] internal slot of this Date object 为 v.
5. 返回 v.

#### 20.3.4.28 Date.prototype.setUTCDate ( date ) <div id="sec-date.prototype.setutcdate"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 dt 为 ? ToNumber(date).
3. 令 newDate 为 MakeDate(MakeDay(YearFromTime(t), MonthFromTime(t), dt), TimeWithinDay(t)).
4. 令 v 为 TimeClip(newDate).
5. 设置 the [[DateValue]] internal slot of this Date object 为 v.
6. 返回 v

#### 20.3.4.29 Date.prototype.setUTCFullYear ( year [ , month [ , date ] ] ) <div id="sec-date.prototype.setutcfullyear"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 若 t 是 NaN，设置 t 为 +0.
3. 令 y 为 ? ToNumber(year).
4. 若 month 不存在, 令 m 为 MonthFromTime(t); otherwise，令 m 为 ? ToNumber(month).
5. 若 date 不存在, 令 dt 为 DateFromTime(t); otherwise，令 dt 为 ? ToNumber(date).
6. 令 newDate 为 MakeDate(MakeDay(y, m, dt), TimeWithinDay(t)).
7. 令 v 为 TimeClip(newDate).
8. 设置 the [[DateValue]] internal slot of this Date object 为 v.
9. 返回 v

The "length" property of the setUTCFullYear method is 3

> NOTE 若 month 不存在, this method behaves as if month was present with the value getUTCMonth(). If date is not present，it behaves as if date was present with the value getUTCDate().

#### 20.3.4.30 Date.prototype.setUTCHours ( hour [ , min [ , sec [ , ms ] ] ] ) <div id="sec-date.prototype.setutchours"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 h 为 ? ToNumber(hour).
3. 若 min 不存在, 令 m 为 MinFromTime(t); otherwise，令 m 为 ? ToNumber(min).
4. 若 sec 不存在, 令 s 为 SecFromTime(t); otherwise，令 s 为 ? ToNumber(sec).
5. 若 ms 不存在, 令 milli 为 msFromTime(t); otherwise，令 milli 为 ? ToNumber(ms).
6. 令 newDate 为 MakeDate(Day(t), MakeTime(h, m, s, milli)).
7. 令 v 为 TimeClip(newDate).
8. 设置 the [[DateValue]] internal slot of this Date object 为 v.
9. 返回 v

The "length" property of the setUTCHours method is 4.

> NOTE 若 min 不存在, this method behaves as 若 min was present with the value getUTCMinutes(). If sec 不存在，it behaves as if sec was present with the value getUTCSeconds(). If ms is not present，it behaves as if ms was present with the value getUTCMilliseconds().

#### 20.3.4.31 Date.prototype.setUTCMilliseconds ( ms ) <div id="sec-date.prototype.setutcmilliseconds"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 milli 为 ? ToNumber(ms).
3. 令 time 为 MakeTime(HourFromTime(t), MinFromTime(t), SecFromTime(t), milli).
4. 令 v 为 TimeClip(MakeDate(Day(t), time)).
5. 设置 the [[DateValue]] internal slot of this Date object 为 v.
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
9. 设置 the [[DateValue]] internal slot of this Date object 为 v.
10. 返回 v.

The "length" property of the setUTCMinutes method is 3

> NOTE 若 sec 不存在, this method behaves as if sec was present with the value getUTCSeconds(). If ms is not present，it function behaves as if ms was present with the value 返回 by getUTCMilliseconds().

#### 20.3.4.33 Date.prototype.setUTCMonth ( month [ , date ] ) <div id="sec-date.prototype.setutcmonth"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 m 为 ? ToNumber(month).
3. 若 date 不存在，令 dt 为 DateFromTime(t).
4. 否则，
    1. 令 dt 为 ? ToNumber(date).
5. 令 newDate 为 MakeDate(MakeDay(YearFromTime(t), m, dt), TimeWithinDay(t)).
6. 令 v 为 TimeClip(newDate).
7. 设置 the [[DateValue]] internal slot of this Date object 为 v.
8. 返回 v

The "length" property of the setUTCMonth method is 2.

> NOTE 若 date 不存在，this method behaves as if date was present with the value getUTCDate().

#### 20.3.4.34 Date.prototype.setUTCSeconds ( sec [ , ms ] ) <div id="sec-date.prototype.setutcseconds"></div>

执行以下步骤：

1. 令 t 为 ? thisTimeValue(this value).
2. 令 s 为 ? ToNumber(sec).
3. 若 ms 不存在，令 milli 为 msFromTime(t).
4. 否则，
    1. 令 milli 为 ? ToNumber(ms).
5. 令 date 为 MakeDate(Day(t), MakeTime(HourFromTime(t), MinFromTime(t), s, milli)).
6. 令 v 为 TimeClip(date).
7. 设置 the [[DateValue]] internal slot of this Date object 为 v.
8. 返回 v.

The "length" property of the setUTCSeconds method is 2

> NOTE 若 ms 不存在，this method behaves as if ms was present with the value getUTCMilliseconds().

#### 20.3.4.35 Date.prototype.toDateString ( ) <div id="sec-date.prototype.todatestring"></div>

执行以下步骤：

1. 令 O 为 this Date object.
2. 令 tv 为 ? thisTimeValue(O).
3. 若 tv 是 NaN，返回 "Invalid Date".
4. 令 t 为 LocalTime(tv).
5. 返回 DateString(t).

#### 20.3.4.36 Date.prototype.toISOString ( ) <div id="sec-date.prototype.toisostring"></div>

This function returns a String value representing the instance in time corresponding to this time value. The format of the String is the Date Time string format defined in 20.3.1.15. All fields are present in the String. The time zone is always UTC, denoted by the suffix Z. 若 this time value is not a finite Number or if the year 不是 a value that can be represented in that format (if necessary using expanded year format)，a RangeError exception is thrown.

#### 20.3.4.37 Date.prototype.toJSON ( key ) <div id="sec-date.prototype.tojson"></div>

This function provides a String representation of a Date object for use by JSON.stringify (24.5.2).

When the toJSON method is called with argument key, the following steps are taken:

1. 令 O 为 ? ToObject(this value).
2. 令 tv 为 ? ToPrimitive(O, hint Number).
3. 若 Type(tv) is Number and tv 不是 finite，返回 null.
4. 返回 ? Invoke(O, "toISOString")

> NOTE 1 The argument is ignored.

> NOTE 2 The toJSON function is intentionally generic; it does not require that its this value be a Date object. Therefore, it can be transferred to other kinds of objects for use as a method. However, it does require that any such object have a toISOString method.

#### 20.3.4.38 Date.prototype.toLocaleDateString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-date.prototype.tolocaledatestring"></div>

An ECMAScript implementation that includes the ECMA-402 Internationalization API must implement the Date.prototype.toLocaleDateString method as specified in the ECMA-402 specification. If an ECMAScript implementation does not include the ECMA-402 API the following specification of the toLocaleDateString method is used

This function returns a String value. The contents of the String are implementation-dependent, but are intended to represent the “date” portion of the Date in the current time zone in a convenient, human-readable form that corresponds to the conventions of the host environment's current locale.

The meaning of the optional parameters to this method are defined in the ECMA-402 specification; implementations that do not include ECMA-402 support must not use those parameter positions for anything else.

#### 20.3.4.39 Date.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-date.prototype.tolocalestring"></div>

An ECMAScript implementation that includes the ECMA-402 Internationalization API must implement the Date.prototype.toLocaleString method as specified in the ECMA-402 specification. If an ECMAScript implementation does not include the ECMA-402 API the following specification of the toLocaleString method is used.

This function returns a String value. The contents of the String are implementation-dependent, but are intended to represent the Date in the current time zone in a convenient, human-readable form that corresponds to the conventions of the host environment's current locale.

The meaning of the optional parameters to this method are defined in the ECMA-402 specification; implementations that do not include ECMA-402 support must not use those parameter positions for anything else.

#### 20.3.4.40 Date.prototype.toLocaleTimeString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-date.prototype.tolocaletimestring"></div>

An ECMAScript implementation that includes the ECMA-402 Internationalization API must implement the Date.prototype.toLocaleTimeString method as specified in the ECMA-402 specification. If an ECMAScript implementation does not include the ECMA-402 API the following specification of the toLocaleTimeString method is used.

This function returns a String value. The contents of the String are implementation-dependent, but are intended to represent the “time” portion of the Date in the current time zone in a convenient, human-readable form that corresponds to the conventions of the host environment's current locale.

The meaning of the optional parameters to this method are defined in the ECMA-402 specification; implementations that do not include ECMA-402 support must not use those parameter positions for anything else.

#### 20.3.4.41 Date.prototype.toString ( ) <div id="sec-date.prototype.tostring"></div>

执行以下步骤：

1. 令 tv 为 ? thisTimeValue(this value).
2. 返回 ToDateString(tv).

> NOTE 1 For any Date object d whose milliseconds amount is zero, the result of Date.parse(d.toString()) is equal to d.valueOf(). See 20.3.3.2.

> NOTE 2 The toString function is not generic; it throws a TypeError exception 若 its this value 不是 a Date object. Therefore，it cannot be transferred to other kinds of objects for use as a method.

##### 20.3.4.41.1 RS: TimeString ( tv ) <div id="sec-timestring"></div>

执行以下步骤：

1. 断言：Type(tv) is Number.
2. 断言：tv is not NaN.
3. 令 hour 为 the String representation of HourFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
4. 令 minute 为 the String representation of MinFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
5. 令 second 为 the String representation of SecFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
6. 返回 the string-concatenation of hour, ":", minute, ":", second, the code unit 0x0020 (SPACE), and "GMT".

##### 20.3.4.41.2 RS: DateString ( tv ) <div id="sec-datestring"></div>

执行以下步骤：

1. 断言：Type(tv) is Number.

2. 断言：tv is not NaN.

3. 令 weekday 为 the Name of the entry in Table 49 with the Number WeekDay(tv).

4. 令 month 为 the Name of the entry in Table 50 with the Number MonthFromTime(tv).

5. 令 day 为 the String representation of DateFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.

6. 令 year 为 the String representation of YearFromTime(tv), formatted as a decimal number of at least four digits, padded to the left with zeroes if necessary.

7. 返回 the string-concatenation of weekday, the code unit 0x0020 (SPACE), month, the code unit 0x0020 (SPACE), day, the code unit 0x0020 (SPACE), and year.

Table 49: Names of days of the week

| Number | Name  |
| ------ | ----- |
| 0      | "Sun" |
| 1      | "Mon" |
| 2      | "Tue" |
| 3      | "Wed" |
| 4      | "Thu" |
| 5      | "Fri" |
| 6      | "Sat" |

Table 50: Names of months of the year

| Number | Name  |
| ------ | ----- |
| 0      | "Jan" |
| 1      | "Feb" |
| 2      | "Mar" |
| 3      | "Apr" |
| 4      | "May" |
| 5      | "Jun" |
| 6      | "Jul" |
| 7      | "Aug" |
| 8      | "Sep" |
| 9      | "Oct" |
| 10     | "Nov" |
| 11     | "Dec" |

##### 20.3.4.41.3 RS: TimeZoneString ( tv ) <div id="sec-timezoneestring"></div>

执行以下步骤：

1. 断言：Type(tv) is Number.
2. 断言：tv is not NaN.
3. 令 offset 为 LocalTZA(tv, true).
4. 若 offset ≥ 0, 令 offsetSign 为 "+"; otherwise，令 offsetSign 为 "-".
5. 令 offsetMin 为 the String representation of MinFromTime(abs(offset)), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
6. 令 offsetHour 为 the String representation of HourFromTime(abs(offset)), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
7. 令 tzName 为 an implementation-defined string that is either the empty string or the string concatenation of the code unit 0x0020 (SPACE), the code unit 0x0028 (LEFT PARENTHESIS), an implementation-dependent timezone name, and the code unit 0x0029 (RIGHT PARENTHESIS).
8. 返回 the string-concatenation of offsetSign, offsetHour, offsetMin, and tzName.

##### 20.3.4.41.4 RS: ToDateString ( tv ) <div id="sec-todatestring"></div>

执行以下步骤：

1. 断言：Type(tv) is Number.
2. 若 tv 是 NaN，返回 "Invalid Date".
3. 令 t 为 LocalTime(tv).
4. 返回 the string-concatenation of DateString(t), the code unit 0x0020 (SPACE), TimeString(t), and TimeZoneString(tv).

#### 20.3.4.42 Date.prototype.toTimeString ( ) <div id="sec-date.prototype.totimestring"></div>

执行以下步骤：

1. 令 O 为 this Date object.
2. 令 tv 为 ? thisTimeValue(O).
3. 若 tv 是 NaN，返回 "Invalid Date".
4. 令 t 为 LocalTime(tv).
5. 返回 the string-concatenation of TimeString(t) and TimeZoneString(tv)

#### 20.3.4.43 Date.prototype.toUTCString ( ) <div id="sec-date.prototype.toutcstring"></div>

执行以下步骤：

1. 令 O 为 this Date object.
2. 令 tv 为 ? thisTimeValue(O).
3. 若 tv 是 NaN，返回 "Invalid Date".
4. 令 weekday 为 the Name of the entry in Table 49 with the Number WeekDay(tv).
5. 令 month 为 the Name of the entry in Table 50 with the Number MonthFromTime(tv).
6. 令 day 为 the String representation of DateFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
7. 令 year 为 the String representation of YearFromTime(tv), formatted as a decimal number of at least four digits, padded to the left with zeroes if necessary.
8. 返回 the string-concatenation of weekday, ",", the code unit 0x0020 (SPACE), day, the code unit 0x0020 (SPACE), month, the code unit 0x0020 (SPACE), year, the code unit 0x0020 (SPACE), and TimeString(tv).

#### 20.3.4.44 Date.prototype.valueOf ( ) <div id="sec-date.prototype.valueof"></div>

执行以下步骤：

1. 返回 ? thisTimeValue(this value)

#### 20.3.4.45 Date.prototype [ @@toPrimitive ] ( hint ) <div id="sec-date.prototype-@@toprimitive"></div>

This function is called by ECMAScript language operators to convert a Date object to a primitive value. The allowed values for hint are "default", "number", and "string". Date objects, are unique among built-in ECMAScript object in that they treat "default" as being equivalent to "string", All other built-in ECMAScript objects treat "default" as being equivalent to "number".

When the @@toPrimitive method is called with argument hint, the following steps are taken:

1. 令 O 为 the this value.
2. 若 Type(O) 不是 Object，抛出 TypeError 异常
3. 若 hint 是 the String value "string" or the String value "default"，那么
    1. 令 tryFirst 为 "string".
4. Else 若 hint 是 the String value "number"，那么
    1. 令 tryFirst 为 "number".
5. 否则，抛出 TypeError 异常
6. 返回 ? OrdinaryToPrimitive(O, tryFirst).

The value of the name property of this function is "[Symbol.toPrimitive]".

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

### 20.3.5 Date 实例属性 <div id="sec-properties-of-date-instances"></div>

Date instances are ordinary objects that inherit properties from the Date prototype object. Date instances also have a [[DateValue]] internal slot. The [[DateValue]] internal slot is the time value represented by this Date object.