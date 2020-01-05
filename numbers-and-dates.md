# 20 数字和日期
## 20.1 Number 对象 <div id="sec-number-objects"></div>
### 20.1.1 Number 构造器 <div id="sec-number-constructor"></div>

The Number constructor:

- is the intrinsic object %Number%.
- is the initial value of the Number property of the global object.
- creates and initializes a new Number object when called as a constructor.
- performs a type conversion when called as a function rather than as a constructor.
- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. Subclass constructors that intend to inherit the specified Number behaviour must include a super call to the Number constructor to create and initialize the subclass instance with a [[NumberData]] internal slot.

#### 20.1.1.1 Number ( value ) <div id="sec-number-constructor-number-value"></div>

When Number is called with argument value, the following steps are taken:

1. If no arguments were passed to this function invocation, let n be +0.
2. Else, let n be ? ToNumber(value).
3. If NewTarget is undefined, return n.
4. Let O be ? OrdinaryCreateFromConstructor(NewTarget, "%NumberPrototype%", « [[NumberData]] »).
5. Set O.[[NumberData]] to n.
6. Return O.

### 20.1.2 Number 构造器属性 <div id="sec-properties-of-the-number-constructor"></div>

The Number constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 20.1.2.1 Number.EPSILON <div id="sec-number.epsilon"></div>

The value of Number.EPSILON is the difference between 1 and the smallest value greater than 1 that is representable as a Number value, which is approximately 2.2204460492503130808472633361816 x 10 - 16 .

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.2 Number.isFinite ( number ) <div id="sec-number.isfinite"></div>

When Number.isFinite is called with one argument number, the following steps are taken:

1. If Type(number) is not Number, return false.
2. If number is NaN, +∞, or -∞, return false.
3. Otherwise, return true

#### 20.1.2.3 Number.isInteger ( number ) <div id="sec-number.isinteger"></div>

When Number.isInteger is called with one argument number, the following steps are taken:

1. If Type(number) is not Number, return false.
2. If number is NaN, +∞, or -∞, return false.
3. Let integer be ! ToInteger(number).
4. If integer is not equal to number, return false.
5. Otherwise, return true.

#### 20.1.2.4 Number.isNaN ( number ) <div id="sec-number.isnan"></div>

When Number.isNaN is called with one argument number, the following steps are taken:

1. If Type(number) is not Number, return false.
2. If number is NaN, return true.
3. Otherwise, return false.

> NOTE This function differs from the global isNaN function (18.2.3) in that it does not convert its argument to a Number before determining whether it is NaN.

#### 20.1.2.5 Number.isSafeInteger ( number ) <div id="sec-number.issafeinteger"></div>

When Number.isSafeInteger is called with one argument number, the following steps are taken:

1. If Type(number) is not Number, return false.
2. If number is NaN, +∞, or -∞, return false.
3. Let integer be ! ToInteger(number).
4. If integer is not equal to number, return false.
5. If abs(integer) ≤ 253 - 1, return true.
6. Otherwise, return false.

#### 20.1.2.6 Number.MAX_SAFE_INTEGER <div id="sec-number.max_safe_integer"></div>

> NOTE The value of Number.MAX_SAFE_INTEGER is the largest integer n such that n and n + 1 are both exactly representable as a Number value.

The value of Number.MAX_SAFE_INTEGER is 9007199254740991 (2^53 - 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.7 Number.MAX_VALUE <div id="sec-number.max_value"></div>

The value of Number.MAX_VALUE is the largest positive finite value of the Number type, which is approximately 1.7976931348623157 × 10^308

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.8 Number.MIN_SAFE_INTEGER <div id="sec-number.min_safe_integer"></div>

> NOTE The value of Number.MIN_SAFE_INTEGER is the smallest integer n such that n and n - 1 are both exactly representable as a Number value.

The value of Number.MIN_SAFE_INTEGER is -9007199254740991 (-(2^53 - 1)).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.9 Number.MIN_VALUE <div id="sec-number.min_value"></div>

The value of Number.MIN_VALUE is the smallest positive value of the Number type, which is approximately 5 × 10^-324

In the IEEE 754-2008 double precision binary representation, the smallest possible value is a denormalized number. If an implementation does not support denormalized values, the value of Number.MIN_VALUE must be the smallest nonzero positive value that can actually be represented by the implementation.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.10 Number.NaN <div id="sec-number.nan"></div>

The value of Number.NaN is NaN

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.11 Number.NEGATIVE_INFINITY <div id="sec-number.negative_infinity"></div>

The value of Number.NEGATIVE_INFINITY is -∞.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.1.2.12 Number.parseFloat ( string ) <div id="sec-number.parsefloat"></div>

The value of the Number.parseFloat data property is the same built-in function object that is the value of the parseFloat property of the global object defined in 18.2.4.

#### 20.1.2.13 Number.parseInt ( string, radix ) <div id="sec-number.parseint"></div>

The value of the Number.parseInt data property is the same built-in function object that is the value of the parseInt property of the global object defined in 18.2.5.

#### 20.1.2.14 Number.POSITIVE_INFINITY <div id="sec-number.positive_infinity"></div>

The value of Number.POSITIVE_INFINITY is +∞.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

#### 20.1.2.15 Number.prototype <div id="sec-number.prototype"></div>

The initial value of Number.prototype is the intrinsic object %NumberPrototype%.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 20.1.3 Number 原型对象属性 <div id="sec-properties-of-the-number-prototype-object"></div>

The Number prototype object:

- is the intrinsic object %NumberPrototype%.
- is an ordinary object.
- is itself a Number object; it has a [[NumberData]] internal slot with the value +0.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

Unless explicitly stated otherwise, the methods of the Number prototype object defined below are not generic and the this value passed to them must be either a Number value or an object that has a [[NumberData]] internal slot that has been initialized to a Number value.

The abstract operation thisNumberValue(value) performs the following steps:

1. If Type(value) is Number, return value.
2. If Type(value) is Object and value has a [[NumberData]] internal slot, then
  1. Let n be value.[[NumberData]].
  2. Assert: Type(n) is Number.
  3. Return n.
3. Throw a TypeError exception.

The phrase “this Number value” within the specification of a method refers to the result returned by calling the abstract operation thisNumberValue with the this value of the method invocation passed as the argument.

#### 20.1.3.1 Number.prototype.constructor <div id="sec-number.prototype.constructor"></div>

The initial value of Number.prototype.constructor is the intrinsic object %Number%

#### 20.1.3.2 Number.prototype.toExponential ( fractionDigits ) <div id="sec-number.prototype.toexponential"></div>

Return a String containing this Number value represented in decimal exponential notation with one digit before the significand's decimal point and fractionDigits digits after the significand's decimal point. If fractionDigits is undefined, include as many significand digits as necessary to uniquely specify the Number (just like in ToString except that in this case the Number is always output in exponential notation). Specifically, perform the following steps:

1. Let x be ? thisNumberValue(this value).
2. Let f be ? ToInteger(fractionDigits).
3. Assert: If fractionDigits is undefined, then f is 0.
4. If x is NaN, return the String "NaN".
5. Let s be the empty String.
6. If x < 0, then
   1. Set s to "-".
   2. Set x to -x.
7. If x = +∞, then
     1. Return the string-concatenation of s and "Infinity".
8. If f < 0 or f > 100, throw a RangeError exception.
9. If x = 0, then
     1. Let m be the String value consisting of f + 1 occurrences of the code unit 0x0030 (DIGIT ZERO).
     2. Let e be 0.
10. Else x ≠ 0,
    1. If fractionDigits is not undefined, then
       1. Let e and n be integers such that 10^f ≤ n < 10^(f+1) and for which the exact mathematical value of n × 10^(e-f) - x is as close to zero as possible. If there are two such sets of e and n, pick the e and n for which n × 10^(e-f) is larger.
    2. Else fractionDigits is undefined,
        1. Let e, n, and f be integers such that f ≥ 0, 10^f ≤ n < 10^(f+1), the Number value for n × 10e - f is x, and f is as small as possible. Note that the decimal representation of n has f + 1 digits, n is not divisible by 10, and the least significant digit of n is not necessarily uniquely determined by these criteria.
    3. Let m be the String value consisting of the digits of the decimal representation of n (in order, with no leading zeroes).
11. If f ≠ 0, then
      1. Let a be the first code unit of m, and let b be the remaining f code units of m.
      2. Set m to the string-concatenation of a, ".", and b.
12. If e = 0, then
    1. Let c be "+".
    2. Let d be "0".
13. Else,
    1. If e > 0, let c be "+".
    2. Else e ≤ 0,
       1. Let c be "-".
       2. Set e to -e.
    3. Let d be the String value consisting of the digits of the decimal representation of e (in order, with no leading zeroes).
14. Set m to the string-concatenation of m, "e", c, and d.
15. Return the string-concatenation of s and m.

> NOTE For implementations that provide more accurate conversions than required by the rules above, it is recommended that the following alternative version of step 10.b.i be used as a guideline:
>
> 1. Let e, n, and f be integers such that 10^f ≤ n < 10^(f+1), the Number value for n × 10(e - f)
>     is x, and f is as small as possible. If there are multiple possibilities for n, choose the value of n for which n × 10(e - f) is closest in value to x. If there are two such possible values of n, choose the one that is even.

#### 20.1.3.3 Number.prototype.toFixed ( fractionDigits ) <div id="sec-number.prototype.tofixed"></div>

> NOTE 1 toFixed returns a String containing this Number value represented in decimal fixed-point notation with fractionDigits digits after the decimal point. If fractionDigits is undefined, 0 is assumed.

The following steps are performed:

1. Let x be ? thisNumberValue(this value).
2. Let f be ? ToInteger(fractionDigits).
3. Assert: If fractionDigits is undefined, then f is 0.
4. If f < 0 or f > 100, throw a RangeError exception.
5. If x is NaN, return the String "NaN".
6. Let s be the empty String.
7. If x < 0, then
1. Set s to "-".
2. Set x to -x.
8. If x ≥ 1021, then
  1. Let m be ! ToString(x).
9. Else x < 10^21,
  1. Let n be an integer for which the exact mathematical value of n ÷ 10^(f - x) is as close to zero as possible. If there are two such n, pick the larger n.
  2. If n = 0, let m be the String "0". Otherwise, let m be the String value consisting of the digits of the decimal representation of n (in order, with no leading zeroes).
  3. If f ≠ 0, then
        1. Let k be the length of m.
            2. If k ≤ f, then
      1. Let z be the String value consisting of f + 1 - k occurrences of the code unit 0x0030 (DIGIT
          ZERO).
      2. Set m to the string-concatenation of z and m.
      3. Set k to f + 1.
        3. Let a be the first k - f code units of m, and let b be the remaining f code units of m.
        4. Set m to the string-concatenation of a, ".", and b.
10. Return the string-concatenation of s and m.

> NOTE 2 The output of toFixed may be more precise than toString for some values because toString only prints enough significant digits to distinguish the number from adjacent number values. For example,
>
> (1000000000000000128).toString() returns "1000000000000000100", while (1000000000000000128).toFixed(0) returns "1000000000000000128".

#### 20.1.3.4 Number.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-number.prototype.tolocalestring"></div>

An ECMAScript implementation that includes the ECMA-402 Internationalization API must implement the Number.prototype.toLocaleString method as specified in the ECMA-402 specification. If an ECMAScript implementation does not include the ECMA-402 API the following specification of the toLocaleString method is used.

Produces a String value that represents this Number value formatted according to the conventions of the host environment's current locale. This function is implementation-dependent, and it is permissible, but not encouraged, for it to return the same thing as toString.

The meanings of the optional parameters to this method are defined in the ECMA-402 specification; implementations that do not include ECMA-402 support must not use those parameter positions for anything else.

#### 20.1.3.5 Number.prototype.toPrecision ( precision ) <div id="sec-number.prototype.toprecision"></div>

Return a String containing this Number value represented either in decimal exponential notation with one digit before the significand's decimal point and precision - 1 digits after the significand's decimal point or in decimal fixed notation with precision significant digits. If precision is undefined, call ToString instead. Specifically, perform the following steps:

1. Let x be ? thisNumberValue(this value).
2. If precision is undefined, return ! ToString(x).
3. Let p be ? ToInteger(precision).
4. If x is NaN, return the String "NaN".
5. Let s be the empty String.
6. If x < 0, then
    1. Set s to the code unit 0x002D (HYPHEN-MINUS).
    2. Set x to -x.
7. If x = +∞, then.
    1. Return the string-concatenation of s and "Infinity".
8. If p < 1 or p > 100, throw a RangeError exception.
9. If x = 0, then
    1. Let m be the String value consisting of p occurrences of the code unit 0x0030 (DIGIT ZERO).
    2. Let e be 0.
10. Else x ≠ 0,
    1. Let e and n be integers such that 10^(p - 1) ≤ n < 10^p and for which the exact mathematical value of n × 10^(e - p + 1 - x) is as close to zero as possible. If there are two such sets of e and n, pick the e and n for which n × 10^(e - p + 1) is larger.
    2. Let m be the String value consisting of the digits of the decimal representation of n (in order, with no leadingzeroes).
    3. If e < -6 or e ≥ p, then
        1. Assert: e ≠ 0.
        2. If p ≠ 1, then
            1. Let a be the first code unit of m, and let b be the remaining p - 1 code units of m.
            2. Set m to the string-concatenation of a, ".", and b.
        3. If e > 0, then
            1. Let c be the code unit 0x002B (PLUS SIGN).
        4. Else e < 0,
            1. Let c be the code unit 0x002D (HYPHEN-MINUS).
            2. Set e to -e.
        5. Let d be the String value consisting of the digits of the decimal representation of e (in order, with no leading zeroes).
        6. Return the string-concatenation of s, m, the code unit 0x0065 (LATIN SMALL LETTER E), c, and d.
11. If e = p - 1, return the string-concatenation of s and m.
12. If e ≥ 0, then
    1. Set m to the string-concatenation of the first e + 1 code units of m, the code unit 0x002E (FULL STOP), and the remaining p - (e + 1) code units of m.
13. Else e < 0,
    1. Set m to the string-concatenation of the code unit 0x0030 (DIGIT ZERO), the code unit 0x002E (FULL STOP), -(e + 1) occurrences of the code unit 0x0030 (DIGIT ZERO), and the String m.
14. Return the string-concatenation of s and m.

#### 20.1.3.6 Number.prototype.toString ( [ radix ] ) <div id="sec-number.prototype.tostring"></div>

> NOTE The optional radix should be an integer value in the inclusive range 2 to 36. If radix is not present or is undefined the Number 10 is used as the value of radix.

The following steps are performed:

1. Let x be ? thisNumberValue(this value).
2. If radix is not present, let radixNumber be 10.
3. Else if radix is undefined, let radixNumber be 10.
4. Else, let radixNumber be ? ToInteger(radix).
5. If radixNumber < 2 or radixNumber > 36, throw a RangeError exception.
6. If radixNumber = 10, return ! ToString(x).
7. Return the String representation of this Number value using the radix specified by radixNumber. Letters a-z are used for digits with values 10 through 35. The precise algorithm is implementation-dependent, however the algorithm should be a generalization of that specified in 7.1.12.1.

The toString function is not generic; it throws a TypeError exception if its this value is not a Number or a Number object. Therefore, it cannot be transferred to other kinds of objects for use as a method.

The "length" property of the toString method is 1

#### 20.1.3.7 Number.prototype.valueOf ( ) <div id="sec-number.prototype.valueof"></div>

1. Return ? thisNumberValue(this value).

### 20.1.4 Number 实例属性 <div id="sec-properties-of-number-instances"></div>

Number instances are ordinary objects that inherit properties from the Number prototype object. Number instances also have a [[NumberData]] internal slot. The [[NumberData]] internal slot is the Number value represented by this Number object.

## 20.2 Math 对象 <div id="sec-math-object"></div>

The Math object:

- is the intrinsic object %Math%.
- is the initial value of the Math property of the global object.
- is an ordinary object.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.
- is not a function object.
- does not have a [[Construct]] internal method; it cannot be used as a constructor with the new operator.
- does not have a [[Call]] internal method; it cannot be invoked as a function.

> NOTE In this specification, the phrase “the Number value for x” has a technical meaning defined in 6.1.6.

### 20.2.1 Math 对象值属性 <div id="sec-value-properties-of-the-math-object"></div>

#### 20.2.1.1 Math.E <div id="sec-math.e"></div>

The Number value for e, the base of the natural logarithms, which is approximately 2.7182818284590452354.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.2.1.2 Math.LN10 <div id="sec-math.ln10"></div>

The Number value for the natural logarithm of 10, which is approximately 2.302585092994046

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.2.1.3 Math.LN2 <div id="sec-math.ln2"></div>

The Number value for the natural logarithm of 2, which is approximately 0.6931471805599453.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

#### 20.2.1.4 Math.LOG10E <div id="sec-math.log10e"></div>

The Number value for the base-10 logarithm of e, the base of the natural logarithms; this value is approximately 0.4342944819032518.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

> NOTE The value of Math.LOG10E is approximately the reciprocal of the value of Math.LN10.

#### 20.2.1.5 Math.LOG2E <div id="sec-math.log2e"></div>

The Number value for the base-2 logarithm of e, the base of the natural logarithms; this value is approximately 1.4426950408889634.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

> NOTE The value of Math.LOG2E is approximately the reciprocal of the value of Math.LN2.

#### 20.2.1.6 Math.PI <div id="sec-math.pi"></div>

The Number value for π, the ratio of the circumference of a circle to its diameter, which is approximately 3.1415926535897932.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.2.1.7 Math.SQRT1_2 <div id="sec-math.sqrt1_2"></div>

The Number value for the square root of ½, which is approximately 0.7071067811865476.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

> NOTE The value of Math.SQRT1_2 is approximately the reciprocal of the value of Math.SQRT2.

#### 20.2.1.8 Math.SQRT2 <div id="sec-math.sqrt2"></div>

The Number value for the square root of 2, which is approximately 1.4142135623730951.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.2.1.9 Math [ @@toStringTag ] <div id="sec-math-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value "Math".

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }

### 20.2.2 Math 对象函数属性 <div id="sec-function-properties-of-the-math-object"></div>

Each of the following Math object functions applies the ToNumber abstract operation to each of its arguments (in leftto-right order if there is more than one). If ToNumber returns an abrupt completion, that Completion Record is immediately returned. Otherwise, the function performs a computation on the resulting Number value(s). The value returned by each function is a Number.

In the function descriptions below, the symbols NaN, -0, +0, -∞ and +∞ refer to the Number values described in 6.1.6.

> NOTE The behaviour of the functions acos, acosh, asin, asinh, atan, atanh, atan2, cbrt, cos, cosh, exp, expm1, hypot, log,log1p, log2, log10, pow, random, sin, sinh, sqrt, tan, and tanh is not precisely specified here except to require specific results for certain argument values that represent boundary cases of interest. For other argument values, these functions are intended to compute approximations to the results of familiar mathematical functions, but some latitude is allowed in the choice of approximation algorithms. The general intent is that an implementer should be able to use the same mathematical library for ECMAScript on a given hardware platform that is available to C programmers on that platform.
>
> Although the choice of algorithms is left to the implementation, it is recommended (but not specified by this standard) that implementations use the approximation algorithms for IEEE 754-2008 arithmetic contained in fdlibm, the freely distributable mathematical library from Sun Microsystems (http://www.netlib.org/fdlibm).

#### 20.2.2.1 Math.abs ( x ) <div id="sec-math.abs"></div>

Returns the absolute value of x; the result has the same magnitude as x but has positive sign.

- If x is NaN, the result is NaN.
- If x is -0, the result is +0.
- If x is -∞, the result is +∞.

#### 20.2.2.2 Math.acos ( x ) <div id="sec-math.acos"></div>

Returns an implementation-dependent approximation to the arc cosine of x. The result is expressed in radians and ranges from +0 to +π.

- If x is NaN, the result is NaN.
- If x is greater than 1, the result is NaN.
- If x is less than -1, the result is NaN.
- If x is exactly 1, the result is +0.

#### 20.2.2.3 Math.acosh ( x ) <div id="sec-math.acosh"></div>

Returns an implementation-dependent approximation to the inverse hyperbolic cosine of x.

- If x is NaN, the result is NaN.
- If x is less than 1, the result is NaN.
- If x is 1, the result is +0.
- If x is +∞, the result is +∞.

#### 20.2.2.4 Math.asin ( x ) <div id="sec-math.asin"></div>

Returns an implementation-dependent approximation to the arc sine of x. The result is expressed in radians and ranges from -π / 2 to +π / 2.

- If x is NaN, the result is NaN.
- If x is greater than 1, the result is NaN.
- If x is less than -1, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.

#### 20.2.2.5 Math.asinh ( x ) <div id="sec-math.asinh"></div>

Returns an implementation-dependent approximation to the inverse hyperbolic sine of x.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.
- If x is -∞, the result is -∞.

#### 20.2.2.6 Math.atan ( x ) <div id="sec-math.atan"></div>

Returns an implementation-dependent approximation to the arc tangent of x. The result is expressed in radians and ranges from -π / 2 to +π / 2.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is an implementation-dependent approximation to +π / 2.
- If x is -∞, the result is an implementation-dependent approximation to -π / 2.

#### 20.2.2.7 Math.atanh ( x ) <div id="sec-math.atanh"></div>

Returns an implementation-dependent approximation to the inverse hyperbolic tangent of x

- If x is NaN, the result is NaN.
- If x is less than -1, the result is NaN.
- If x is greater than 1, the result is NaN.
- If x is -1, the result is -∞.
- If x is +1, the result is +∞.
- If x is +0, the result is +0.
- If x is -0, the result is -0.

#### 20.2.2.8 Math.atan2 ( y, x ) <div id="sec-math.atan2"></div>

Returns an implementation-dependent approximation to the arc tangent of the quotient y / x of the arguments y and x, where the signs of y and x are used to determine the quadrant of the result. Note that it is intentional and traditional for the two-argument arc tangent function that the argument named y be first and the argument named x be second. The result is expressed in radians and ranges from -π to +π.

- If either x or y is NaN, the result is NaN.
- If y > 0 and x is +0, the result is an implementation-dependent approximation to +π / 2.
- If y > 0 and x is -0, the result is an implementation-dependent approximation to +π / 2.
- If y is +0 and x > 0, the result is +0.
- If y is +0 and x is +0, the result is +0.
- If y is +0 and x is -0, the result is an implementation-dependent approximation to +π.
- If y is +0 and x < 0, the result is an implementation-dependent approximation to +π.
- If y is -0 and x > 0, the result is -0.
- If y is -0 and x is +0, the result is -0.
- If y is -0 and x is -0, the result is an implementation-dependent approximation to -π.
- If y is -0 and x < 0, the result is an implementation-dependent approximation to -π.
- If y < 0 and x is +0, the result is an implementation-dependent approximation to -π / 2.
- If y < 0 and x is -0, the result is an implementation-dependent approximation to -π / 2.
- If y > 0 and y is finite and x is +∞, the result is +0.
- If y > 0 and y is finite and x is -∞, the result is an implementation-dependent approximation to +π.
- If y < 0 and y is finite and x is +∞, the result is -0.
- If y < 0 and y is finite and x is -∞, the result is an implementation-dependent approximation to -π.
- If y is +∞ and x is finite, the result is an implementation-dependent approximation to +π / 2.
- If y is -∞ and x is finite, the result is an implementation-dependent approximation to -π / 2.
- If y is +∞ and x is +∞, the result is an implementation-dependent approximation to +π / 4.
- If y is +∞ and x is -∞, the result is an implementation-dependent approximation to +3π / 4.
- If y is -∞ and x is +∞, the result is an implementation-dependent approximation to -π / 4.
- If y is -∞ and x is -∞, the result is an implementation-dependent approximation to -3π / 4.

#### 20.2.2.9 Math.cbrt ( x ) <div id="sec-math.cbrt"></div>

Returns an implementation-dependent approximation to the cube root of x.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.
- If x is -∞, the result is -∞.

#### 20.2.2.10 Math.ceil ( x ) <div id="sec-math.ceil"></div>

Returns the smallest (closest to -∞) Number value that is not less than x and is equal to a mathematical integer. If x is already an integer, the result is x.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.
- If x is -∞, the result is -∞.
- If x is less than 0 but greater than -1, the result is -0.

The value of Math.ceil(x) is the same as the value of -Math.floor(-x).

#### 20.2.2.11 Math.clz32 ( x ) <div id="sec-math.clz32"></div>

When Math.clz32 is called with one argument x, the following steps are taken:

1. Let n be ? ToUint32(x).
2. Let p be the number of leading zero bits in the 32-bit binary representation of n.
3. Return p.

> NOTE If n is 0, p will be 32. If the most significant bit of the 32-bit binary encoding of n is 1, p will be 0.

#### 20.2.2.12 Math.cos ( x ) <div id="sec-math.cos"></div>

Returns an implementation-dependent approximation to the cosine of x. The argument is expressed in radians

- If x is NaN, the result is NaN.
- If x is +0, the result is 1.
- If x is -0, the result is 1.
- If x is +∞, the result is NaN.
- If x is -∞, the result is NaN

#### 20.2.2.13 Math.cosh ( x ) <div id="sec-math.cosh"></div>

Returns an implementation-dependent approximation to the hyperbolic cosine of x.

- If x is NaN, the result is NaN.
- If x is +0, the result is 1.
- If x is -0, the result is 1.
- If x is +∞, the result is +∞.
- If x is -∞, the result is +∞.

> NOTE The value of cosh(x) is the same as (exp(x) + exp(-x)) / 2.

#### 20.2.2.14 Math.exp ( x ) <div id="sec-math.exp"></div>

Returns an implementation-dependent approximation to the exponential function of x (e raised to the power of x, where e is the base of the natural logarithms).

- If x is NaN, the result is NaN.
- If x is +0, the result is 1.
- If x is -0, the result is 1.
- If x is +∞, the result is +∞.
- If x is -∞, the result is +0.

#### 20.2.2.15 Math.expm1 ( x ) <div id="sec-math.expm1"></div>

Returns an implementation-dependent approximation to subtracting 1 from the exponential function of x (e raised to the power of x, where e is the base of the natural logarithms). The result is computed in a way that is accurate even when the value of x is close 0.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.
- If x is -∞, the result is -1.

#### 20.2.2.16 Math.floor ( x ) <div id="sec-math.floor"></div>

Returns the greatest (closest to +∞) Number value that is not greater than x and is equal to a mathematical integer. If x is already an integer, the result is x.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.
- If x is -∞, the result is -∞.
- If x is greater than 0 but less than 1, the result is +0.

> NOTE The value of Math.floor(x) is the same as the value of -Math.ceil(-x).

#### 20.2.2.17 Math.fround ( x ) <div id="sec-math.fround"></div>

When Math.fround is called with argument x, the following steps are taken:

1. If x is NaN, return NaN.
2. If x is one of +0, -0, +∞, -∞, return x.
3. Let x32 be the result of converting x to a value in IEEE 754-2008 binary32 format using roundTiesToEven.
4. Let x64 be the result of converting x32 to a value in IEEE 754-2008 binary64 format.
5. Return the ECMAScript Number value corresponding to x64.

#### 20.2.2.18 Math.hypot ( value1, value2, ...values ) <div id="sec-math.hypot"></div>

Math.hypot returns an implementation-dependent approximation of the square root of the sum of squares of its arguments.

- If no arguments are passed, the result is +0.
- If any argument is +∞, the result is +∞.
- If any argument is -∞, the result is +∞.
- If no argument is +∞ or -∞, and any argument is NaN, the result is NaN.
- If all arguments are either +0 or -0, the result is +0.

> NOTE Implementations should take care to avoid the loss of precision from overflows and underflows that are prone to occur in naive implementations when this function is called with two or more arguments.

#### 20.2.2.19 Math.imul ( x, y ) <div id="sec-math.imul"></div>

When Math.imul is called with arguments x and y, the following steps are taken:

1. Let a be ? ToUint32(x).
2. Let b be ? ToUint32(y).
3. Let product be (a × b) modulo 2^32.
4. If product ≥ 2^31, return product - 2^32; otherwise return product.

#### 20.2.2.20 Math.log ( x ) <div id="sec-math.log"></div>

Returns an implementation-dependent approximation to the natural logarithm of x.

- If x is NaN, the result is NaN.
- If x is less than 0, the result is NaN.
- If x is +0 or -0, the result is -∞.
- If x is 1, the result is +0.
- If x is +∞, the result is +∞.

#### 20.2.2.21 Math.log1p ( x ) <div id="sec-math.log1p"></div>

Returns an implementation-dependent approximation to the natural logarithm of 1 + x. The result is computed in a way that is accurate even when the value of x is close to zero.

- If x is NaN, the result is NaN.
- If x is less than -1, the result is NaN.
- If x is -1, the result is -∞.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.

#### 20.2.2.22 Math.log10 ( x ) <div id="sec-math.log10"></div>

Returns an implementation-dependent approximation to the base 10 logarithm of x.

- If x is NaN, the result is NaN.
- If x is less than 0, the result is NaN.
- If x is +0, the result is -∞.
- If x is -0, the result is -∞.
- If x is 1, the result is +0.
- If x is +∞, the result is +∞.

#### 20.2.2.23 Math.log2 ( x ) <div id="sec-math.log2"></div>

Returns an implementation-dependent approximation to the base 2 logarithm of x.

- If x is NaN, the result is NaN.
- If x is less than 0, the result is NaN.
- If x is +0, the result is -∞.
- If x is -0, the result is -∞.
- If x is 1, the result is +0.
- If x is +∞, the result is +∞.

#### 20.2.2.24 Math.max ( value1, value2, ...values ) <div id="sec-math.max"></div>

Given zero or more arguments, calls ToNumber on each of the arguments and returns the largest of the resulting values

- If no arguments are given, the result is -∞.
- If any value is NaN, the result is NaN.
- The comparison of values to determine the largest value is done using the Abstract Relational Comparison algorithm except that +0 is considered to be larger than -0.

#### 20.2.2.25 Math.min ( value1, value2, ...values ) <div id="sec-math.min"></div>

Given zero or more arguments, calls ToNumber on each of the arguments and returns the smallest of the resulting values.

- If no arguments are given, the result is +∞.
- If any value is NaN, the result is NaN.
- The comparison of values to determine the smallest value is done using the Abstract Relational Comparison algorithm except that +0 is considered to be larger than -0.

#### 20.2.2.26 Math.pow ( base, exponent ) <div id="sec-math.pow"></div>

1. Return the result of Applying the ** operator with base and exponent as specified in 12.6.4.

#### 20.2.2.27 Math.random ( ) <div id="sec-math.random"></div>

Returns a Number value with positive sign, greater than or equal to 0 but less than 1, chosen randomly or pseudo randomly with approximately uniform distribution over that range, using an implementation-dependent algorithm or strategy. This function takes no arguments.

Each Math.random function created for distinct realms must produce a distinct sequence of values from successive calls.

#### 20.2.2.28 Math.round ( x ) <div id="sec-math.round"></div>

Returns the Number value that is closest to x and is equal to a mathematical integer. If two integer Number values are equally close to x, then the result is the Number value that is closer to +∞. If x is already an integer, the result is x.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.
- If x is -∞, the result is -∞.
- If x is greater than 0 but less than 0.5, the result is +0.
- If x is less than 0 but greater than or equal to -0.5, the result is -0.

>NOTE 1
>Math.round(3.5) returns 4, but Math.round(-3.5) returns -3.

>NOTE 2
>The value of Math.round(x) is not always the same as the value of Math.floor(x + 0.5). When x is -0 or is less than 0 but greater than or equal to -0.5, Math.round(x) returns -0, but Math.floor(x + 0.5) returns +0. Math.round(x) may also differ from the value of Math.floor(x + 0.5)because of internal rounding when computing x + 0.5.

#### 20.2.2.29 Math.sign ( x ) <div id="sec-math.sign"></div>

Returns the sign of x, indicating whether x is positive, negative, or zero

- If x is NaN, the result is NaN.
- If x is -0, the result is -0.
- If x is +0, the result is +0.
- If x is negative and not -0, the result is -1.
- If x is positive and not +0, the result is +1.

#### 20.2.2.30 Math.sin ( x ) <div id="sec-math.sin"></div>

Returns an implementation-dependent approximation to the sine of x. The argument is expressed in radians.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞ or -∞, the result is NaN.

#### 20.2.2.31 Math.sinh ( x ) <div id="sec-math.sinh"></div>

Returns an implementation-dependent approximation to the hyperbolic sine of x

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.
- If x is -∞, the result is -∞.

> NOTE The value of sinh(x) is the same as (exp(x) - exp(-x)) / 2.

#### 20.2.2.32 Math.sqrt ( x ) <div id="sec-math.sqrt"></div>

Returns an implementation-dependent approximation to the square root of x.

- If x is NaN, the result is NaN.
- If x is less than 0, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +∞.

#### 20.2.2.33 Math.tan ( x ) <div id="sec-math.tan"></div>

Returns an implementation-dependent approximation to the tangent of x. The argument is expressed in radians.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞ or -∞, the result is NaN.

#### 20.2.2.34 Math.tanh ( x ) <div id="sec-math.tanh"></div>

Returns an implementation-dependent approximation to the hyperbolic tangent of x.

- If x is NaN, the result is NaN.
- If x is +0, the result is +0.
- If x is -0, the result is -0.
- If x is +∞, the result is +1.
- If x is -∞, the result is -1.

> NOTE The value of tanh(x) is the same as (exp(x) - exp(-x))/(exp(x) + exp(-x)).

#### 20.2.2.35 Math.trunc ( x ) <div id="sec-math.trunc"></div>

Returns the integral part of the number x, removing any fractional digits. If x is already an integer, the result is x.

- If x is NaN, the result is NaN.
- If x is -0, the result is -0.If x is +0, the result is +0.
- If x is +∞, the result is +∞.
- If x is -∞, the result is -∞.
- If x is greater than 0 but less than 1, the result is +0.
- If x is less than 0 but greater than -1, the result is -0.

## 20.3 Date 对象 <div id="sec-date-objects"></div>
### 20.3.1 Date 对象的概述和抽象操作定义 <div id="sec-overview-of-date-objects-and-definitions-of-abstract-operations"></div>

The following functions are abstract operations that operate on time values (defined in 20.3.1.1). Note that, in every case, if any argument to one of these functions is NaN, the result will be NaN.

#### 20.3.1.1 时间值和时间范围 <div id="sec-time-values-and-time-range"></div>

A Date object contains a Number representing an instant in time with millisecond precision. Such a Number is called a time value. A time value may also be NaN, indicating that the Date object does not represent a specific instant in time.

Time is measured in ECMAScript as milliseconds since midnight at the beginning of 01 January, 1970 UTC. Time in ECMAScript does not observe leap seconds; they are ignored. Time calculations assume each and every day contains exactly 60 × 60 × 24 × 1000 = 86,400,000 milliseconds, to align with the POSIX specification of each and every day containing exactly 86,400 seconds.

A Number can exactly represent all integers from -9,007,199,254,740,992 to 9,007,199,254,740,992 (20.1.2.8 and 20.1.2.6). A time value supports a slightly smaller range of exactly -100,000,000 days to 100,000,000 days measured relative to midnight at the beginning of 01 January, 1970 UTC. This yields an exact supported time value range of - 8,640,000,000,000,000 to 8,640,000,000,000,000 milliseconds relative to midnight at the beginning of 01 January, 1970 UTC.

The exact moment of midnight at the beginning of 01 January, 1970 UTC is represented by the time value +0

> NOTE 
>
> The 400 year cycle of the Gregorian calendar contains 97 leap years. This yields an average of 365.2425 days per year, or an average of 31,556,952,000 milliseconds per year under the Gregorian calendar. ECMAScript applies a proleptic Gregorian calendar for all time computations. 
>
> As specified by this section, the maximum year range a Number can represent exactly with millisecond precision is approximately -285,426 to 285,426 years relative to midnight at the beginning of 01 January, 1970 UTC. 
>
> As specified by this section, the maximum year range a time value can represent is approximately -273,790 to 273,790 years relative to midnight at the beginning of 01 January, 1970 UTC.

#### 20.3.1.2 天数和一天的时间 <div id="sec-day-number-and-time-within-day"></div>

A given time value t belongs to day number

​	Day(t) = floor(t / msPerDay)

where the number of milliseconds per day is

​	msPerDay = 86400000

The remainder is called the time within the day:

​	TimeWithinDay(t) = t modulo msPerDay

#### 20.3.1.3 年数 <div id="sec-year-number"></div>

ECMAScript uses a proleptic Gregorian calendar to map a day number to a year number and to determine the month and date within that year. In this calendar, leap years are precisely those which are (divisible by 4) and ((not divisible by 100) or (divisible by 400)). The number of days in year number y is therefore defined by

​	DaysInYear(y)
​		= 365 if (y modulo 4) ≠ 0
​		= 366 if (y modulo 4) = 0 and (y modulo 100) ≠ 0
​		= 365 if (y modulo 100) = 0 and (y modulo 400) ≠ 0
​		= 366 if (y modulo 400) = 0

All non-leap years have 365 days with the usual number of days per month and leap years have an extra day in February. The day number of the first day of year y is given by:

​	DayFromYear(y) = 365 × (y - 1970) + floor((y - 1969) / 4) - floor((y - 1901) / 100) + floor((y - 1601) / 400)

The time value of the start of a year is:

​	TimeFromYear(y) = msPerDay × DayFromYear(y)

A time value determines a year by:

​	YearFromTime(t) = the largest integer y (closest to positive infinity) such that TimeFromYear(y) ≤ t

The leap-year function is 1 for a time within a leap year and otherwise is zero:

​	InLeapYear(t)
​		= 0 if DaysInYear(YearFromTime(t)) = 365
​		= 1 if DaysInYear(YearFromTime(t)) = 366

#### 20.3.1.4 月数 <div id="sec-month-number"></div>

Months are identified by an integer in the range 0 to 11, inclusive. The mapping MonthFromTime(t) from a time value t to a month number is defined by:

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

where

​	DayWithinYear(t) = Day(t) - DayFromYear(YearFromTime(t))

A month value of 0 specifies January; 1 specifies February; 2 specifies March; 3 specifies April; 4 specifies May; 5 specifies June; 6 specifies July; 7 specifies August; 8 specifies September; 9 specifies October; 10 specifies November; and 11 specifies December. Note that MonthFromTime(0) = 0, corresponding to Thursday, 01 January, 1970.

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

LocalTZA( t, isUTC ) is an implementation-defined algorithm that must return a number representing milliseconds suitable for adding to a Time Value. The local political rules for standard time and daylight saving time in effect at t should be used to determine the result in the way specified in the following three paragraphs.

When isUTC is true, LocalTZA( t, true ) should return the offset of the local time zone from UTC measured in milliseconds at time represented by time value t (UTC). When the result is added to t (UTC), it should yield the local time.

When isUTC is false, LocalTZA( t, false ) should return the offset of the local time zone from UTC measured in milliseconds at local time represented by time value t local = t. When the result is subtracted from the local time t local, it should yield the corresponding UTC.

When t local represents local time repeating multiple times at a negative time zone transition (e.g. when the daylight saving time ends or the time zone adjustment is decreased due to a time zone rule change) or skipped local time at a positive time zone transitions (e.g. when the daylight saving time starts or the time zone adjustment is increased due to a time zone rule change), t local must be interpreted with the time zone adjustment before the transition.

If an implementation does not support a conversion described above or if political rules for time t are not available within the implementation, the result must be 0.

>NOTE It is recommended that implementations use the time zone information of the IANA Time Zone Database https://www.iana.org/time-zones/. 
>
>1:30 AM on November 5, 2017 in America/New_York is repeated twice (fall backward), but it must be interpreted as 1:30 AM UTC-04 instead of 1:30 AM UTC-05. LocalTZA(TimeClip(MakeDate(MakeDay(2017, 10, 5), MakeTime(1, 30, 0, 0))), false) is -4 × msPerHour. 
>
>2:30 AM on March 12, 2017 in America/New_York does not exist, but it must be interpreted as 2:30 AM UTC-05 (equivalent to 3:30 AM UTC-04). LocalTZA(TimeClip(MakeDate(MakeDay(2017, 2, 12), MakeTime(2, 30, 0, 0))), false) is -5 × msPerHour.

#### 20.3.1.8 LocalTime ( t ) <div id="sec-localtime"></div>

The abstract operation LocalTime with argument t converts t from UTC to local time by performing the following steps:

1. Return t + LocalTZA(t, true).

> NOTE Two different time values (t (UTC)) are converted to the same local time t local at a negative time zone transition when there are repeated times (e.g. the daylight saving time ends or the time zone adjustment is decreased.).

#### 20.3.1.9 UTC ( t ) <div id="sec-utc-t"></div>

The abstract operation UTC with argument t converts t from local time to UTC. It performs the following steps:

1. Return t - LocalTZA(t, false).

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

1. If hour is not finite or min is not finite or sec is not finite or ms is not finite, return NaN.
2. Let h be ! ToInteger(hour).
3. Let m be ! ToInteger(min).
4. Let s be ! ToInteger(sec).
5. Let milli be ! ToInteger(ms).
6. Let t be h * msPerHour + m * msPerMinute + s * msPerSecond + milli, performing the arithmetic according to IEEE 754-2008 rules (that is, as if using the ECMAScript operators * and +).
7. Return t.

#### 20.3.1.12 MakeDay ( year, month, date ) <div id="sec-makeday"></div>

The abstract operation MakeDay calculates a number of days from its three arguments, which must be ECMAScript Number values. This operator functions as follows:

1. If year is not finite or month is not finite or date is not finite, return NaN.
2. Let y be ! ToInteger(year).
3. Let m be ! ToInteger(month).
4. Let dt be ! ToInteger(date).
5. Let ym be y + floor(m / 12).
6. Let mn be m modulo 12.
7. Find a value t such that YearFromTime(t) is ym and MonthFromTime(t) is mn and DateFromTime(t) is 1; but if this is not possible (because some argument is out of range), return NaN.
8. Return Day(t) + dt - 1.

#### 20.3.1.13 MakeDate ( day, time ) <div id="sec-makedate"></div>

The abstract operation MakeDate calculates a number of milliseconds from its two arguments, which must be ECMAScript Number values. This operator functions as follows:

1. If day is not finite or time is not finite, return NaN.
2. Return day × msPerDay + time.

#### 20.3.1.14 TimeClip ( time ) <div id="sec-timeclip"></div>

The abstract operation TimeClip calculates a number of milliseconds from its argument, which must be an ECMAScript Number value. This operator functions as follows:

1. If time is not finite, return NaN.
2. If abs(time) > 8.64 × 1015, return NaN.
3. Let clippedTime be ! ToInteger(time).
4. If clippedTime is -0, set clippedTime to +0.
5. Return clippedTime.

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

All numbers must be base 10. If the MM or DD fields are absent "01" is used as the value. If the HH, mm, or ss fields are absent "00" is used as the value and the value of an absent sss field is "000". When the time zone offset is absent, date-only forms are interpreted as a UTC time and date-time forms are interpreted as a local time.

A string containing out-of-bounds or nonconforming fields is not a valid instance of this format.

> NOTE 1 As every day both starts and ends with midnight, the two notations 00:00 and 24:00 are available to distinguish the two midnights that can be associated with one date. This means that the following two notations refer to exactly the same point in time: 1995-02-04T24:00 and 1995-02-05T00:00. This interpretation of the latter form as "end of a calendar day" is consistent with ISO 8601, even though that specification reserves it for describing time intervals and does not permit it within representations of single points in time. 

> NOTE 2 There exists no international standard that specifies abbreviations for civil time zones like CET, EST, etc. and sometimes the same abbreviation is even used for two very different time zones. For this reason, both ISO 8601 and this format specify numeric representations of time zone offsets.

##### 20.3.1.15.1 年扩展 <div id="sec-expanded-years"></div>

Covering the full time value range of approximately 273,790 years forward or backward from 01 January, 1970 (20.3.1.1) requires representing years before 0 or after 9999. ISO 8601 permits expansion of the year representation, but only by mutual agreement of the partners in information interchange. In the simplified ECMAScript format, such an expanded year representation shall have 6 digits and is always prefixed with a + or - sign. The year 0 is considered positive and hence prefixed with a + sign. Strings matching the Date Time String Format with expanded years representing instants in time outside the range of a time value are treated as unrecognizable by Date.parse and cause that function to return NaN without falling back to implementation-specific behavior or heuristics

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

1. Let numberOfArgs be the number of arguments passed to this function call.
2. Assert: numberOfArgs ≥ 2.
3. If NewTarget is undefined, then
    1. Let now be the Number that is the time value (UTC) identifying the current time.
    2. Return ToDateString(now).
4. Else,
    1. Let y be ? ToNumber(year).
    2. Let m be ? ToNumber(month).
    3. If date is present, let dt be ? ToNumber(date); else let dt be 1.
    4. If hours is present, let h be ? ToNumber(hours); else let h be 0.
    5. If minutes is present, let min be ? ToNumber(minutes); else let min be 0.
    6. If seconds is present, let s be ? ToNumber(seconds); else let s be 0.
    7. If ms is present, let milli be ? ToNumber(ms); else let milli be 0.
    8. If y is NaN, let yr be NaN.
    9. Else,
        1. Let yi be ! ToInteger(y).
        2. If 0 ≤ yi ≤ 99, let yr be 1900 + yi; otherwise, let yr be y.
    10. Let finalDate be MakeDate(MakeDay(yr, m, dt), MakeTime(h, min, s, milli)).
    11. Let O be ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    12. Set O.[[DateValue]] to TimeClip(UTC(finalDate)).
    13. Return O.

#### 20.3.2.2 Date ( value ) <div id="sec-date-value"></div>

This description applies only if the Date constructor is called with exactly one argument

When the Date function is called, the following steps are taken:

1. Let numberOfArgs be the number of arguments passed to this function call.
2. Assert: numberOfArgs = 1.
3. If NewTarget is undefined, then
1. Let now be the Number that is the time value (UTC) identifying the current time.
2. Return ToDateString(now).
4. Else,
    1. If Type(value) is Object and value has a [[DateValue]] internal slot, then
        1. Let tv be thisTimeValue(value).
    2. Else,
        1. Let v be ? ToPrimitive(value).
        2. If Type(v) is String, then
            1. Assert: The next step never returns an abrupt completion because Type(v) is String.
            2. Let tv be the result of parsing v as a date, in exactly the same manner as for the parse method (20.3.3.2).
        3. Else,
            1. Let tv be ? ToNumber(v).
    3. Let O be ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    4. Set O.[[DateValue]] to TimeClip(tv).
    5. Return O.

#### 20.3.2.3 Date ( ) <div id="sec-date-constructor-date"></div>

This description applies only if the Date constructor is called with no arguments.

When the Date function is called, the following steps are taken:

1. Let numberOfArgs be the number of arguments passed to this function call.
2. Assert: numberOfArgs = 0.
3. If NewTarget is undefined, then
    1. Let now be the Number that is the time value (UTC) identifying the current time.
    2. Return ToDateString(now).
4. Else,
    1. Let O be ? OrdinaryCreateFromConstructor(NewTarget, "%DatePrototype%", « [[DateValue]] »).
    2. Set O.[[DateValue]] to the time value (UTC) identifying the current time.
    3. Return O.

### 20.3.3 Date 构造器属性 <div id="sec-properties-of-the-date-constructor"></div>

The Date constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 20.3.3.1 Date.now ( ) <div id="sec-date.now"></div>

The now function returns a Number value that is the time value designating the UTC date and time of the occurrence of the call to now.

#### 20.3.3.2 Date.parse ( string ) <div id="sec-date.parse"></div>

The parse function applies the ToString operator to its argument. If ToString results in an abrupt completion the Completion Record is immediately returned. Otherwise, parse interprets the resulting String as a date and time; it returns a Number, the UTC time value corresponding to the date and time. The String may be interpreted as a local time, a UTC time, or a time in some other time zone, depending on the contents of the String. The function first attempts to parse the String according to the format described in Date Time String Format (20.3.1.15), including expanded years. If the String does not conform to that format the function may fall back to any implementation-specific heuristics or implementation-specific date formats. Strings that are unrecognizable or contain out-of-bounds format field values shall cause Date.parse to return NaN.

If x is any Date object whose milliseconds amount is zero within a particular implementation of ECMAScript, then all of the following expressions should produce the same numeric value in that implementation, if all the properties referenced have their initial values:

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

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 20.3.3.4 Date.UTC ( year [ , month [ , date [ , hours [ , minutes [ , seconds [ , ms ] ] ] ] ] ] ) <div id="sec-date.utc"></div>

When the UTC function is called, the following steps are taken:

1. Let y be ? ToNumber(year).
2. If month is present, let m be ? ToNumber(month); else let m be 0.
3. If date is present, let dt be ? ToNumber(date); else let dt be 1.
4. If hours is present, let h be ? ToNumber(hours); else let h be 0.
5. If minutes is present, let min be ? ToNumber(minutes); else let min be 0.
6. If seconds is present, let s be ? ToNumber(seconds); else let s be 0.
7. If ms is present, let milli be ? ToNumber(ms); else let milli be 0.
8. If y is NaN, let yr be NaN.
9. Else,
1. Let yi be ! ToInteger(y).
2. If 0 ≤ yi ≤ 99, let yr be 1900 + yi; otherwise, let yr be y.
10. Return TimeClip(MakeDate(MakeDay(yr, m, dt), MakeTime(h, min, s, milli))).

The "length" property of the UTC function is 7.

> NOTE The UTC function differs from the Date constructor in two ways: it returns a time value as a Number, rather than creating a Date object, and it interprets the arguments in UTC rather than as local time.

### 20.3.4 Date 原型对象属性 <div id="sec-properties-of-the-date-prototype-object"></div>

The Date prototype object:

- is the intrinsic object %DatePrototype%.
- is itself an ordinary object.
- is not a Date instance and does not have a [[DateValue]] internal slot.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

Unless explicitly defined otherwise, the methods of the Date prototype object defined below are not generic and the this value passed to them must be an object that has a [[DateValue]] internal slot that has been initialized to a time value.

The abstract operation thisTimeValue(value) performs the following steps:

1. If Type(value) is Object and value has a [[DateValue]] internal slot, then
1. Return value.[[DateValue]].
2. Throw a TypeError exception.

In following descriptions of functions that are properties of the Date prototype object, the phrase “this Date object” refers to the object that is the this value for the invocation of the function. If the Type of the this value is not Object, a TypeError exception is thrown. The phrase “this time value” within the specification of a method refers to the result returned by calling the abstract operation thisTimeValue with the this value of the method invocation passed as the argument.

#### 20.3.4.1 Date.prototype.constructor <div id="sec-date.prototype.constructor"></div>

The initial value of Date.prototype.constructor is the intrinsic object %Date%

#### 20.3.4.2 Date.prototype.getDate ( ) <div id="sec-date.prototype.getdate"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return DateFromTime(LocalTime(t)).

#### 20.3.4.3 Date.prototype.getDay ( ) <div id="sec-date.prototype.getday"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return WeekDay(LocalTime(t)).

#### 20.3.4.4 Date.prototype.getFullYear ( ) <div id="sec-date.prototype.getfullyear"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return YearFromTime(LocalTime(t)).

#### 20.3.4.5 Date.prototype.getHours ( ) <div id="sec-date.prototype.gethours"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return HourFromTime(LocalTime(t))

#### 20.3.4.6 Date.prototype.getMilliseconds ( ) <div id="sec-date.prototype.getmilliseconds"></div>

The following steps are performed

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return msFromTime(LocalTime(t))

#### 20.3.4.7 Date.prototype.getMinutes ( ) <div id="sec-date.prototype.getminutes"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return MinFromTime(LocalTime(t)).

#### 20.3.4.8 Date.prototype.getMonth ( ) <div id="sec-date.prototype.getmonth"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return MonthFromTime(LocalTime(t)).

#### 20.3.4.9 Date.prototype.getSeconds ( ) <div id="sec-date.prototype.getseconds"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return SecFromTime(LocalTime(t)).

#### 20.3.4.10 Date.prototype.getTime ( ) <div id="sec-date.prototype.gettime"></div>

The following steps are performed:

1. Return ? thisTimeValue(this value).

#### 20.3.4.11 Date.prototype.getTimezoneOffset ( ) <div id="sec-date.prototype.gettimezoneoffset"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return (t - LocalTime(t)) / msPerMinute

#### 20.3.4.12 Date.prototype.getUTCDate ( ) <div id="sec-date.prototype.getutcdate"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return DateFromTime(t).

#### 20.3.4.13 Date.prototype.getUTCDay ( ) <div id="sec-date.prototype.getutcday"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return WeekDay(t).

#### 20.3.4.14 Date.prototype.getUTCFullYear ( ) <div id="sec-date.prototype.getutcfullyear"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return YearFromTime(t)

#### 20.3.4.15 Date.prototype.getUTCHours ( ) <div id="sec-date.prototype.getutchours"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return HourFromTime(t).

#### 20.3.4.16 Date.prototype.getUTCMilliseconds ( ) <div id="sec-date.prototype.getutcmilliseconds"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return msFromTime(t).

#### 20.3.4.17 Date.prototype.getUTCMinutes ( ) <div id="sec-date.prototype.getutcminutes"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return MinFromTime(t).

#### 20.3.4.18 Date.prototype.getUTCMonth ( ) <div id="sec-date.prototype.getutcmonth"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return MonthFromTime(t).

#### 20.3.4.19 Date.prototype.getUTCSeconds ( ) <div id="sec-date.prototype.getutcseconds"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, return NaN.
3. Return SecFromTime(t).

#### 20.3.4.20 Date.prototype.setDate ( date ) <div id="sec-date.prototype.setdate"></div>

The following steps are performed:

1. Let t be LocalTime(? thisTimeValue(this value)).
2. Let dt be ? ToNumber(date).
3. Let newDate be MakeDate(MakeDay(YearFromTime(t), MonthFromTime(t), dt), TimeWithinDay(t)).
4. Let u be TimeClip(UTC(newDate)).
5. Set the [[DateValue]] internal slot of this Date object to u.
6. Return u.

#### 20.3.4.21 Date.prototype.setFullYear ( year [ , month [ , date ] ] ) <div id="sec-date.prototype.setfullyear"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, set t to +0; otherwise, set t to LocalTime(t).
3. Let y be ? ToNumber(year).
4. If month is not present, let m be MonthFromTime(t); otherwise, let m be ? ToNumber(month).
5. If date is not present, let dt be DateFromTime(t); otherwise, let dt be ? ToNumber(date).
6. Let newDate be MakeDate(MakeDay(y, m, dt), TimeWithinDay(t)).
7. Let u be TimeClip(UTC(newDate)).
8. Set the [[DateValue]] internal slot of this Date object to u.
9. Return u.

The "length" property of the setFullYear method is 3.

> NOTE If month is not present, this method behaves as if month was present with the value getMonth(). If date is not present, it behaves as if date was present with the value getDate().

#### 20.3.4.22 Date.prototype.setHours ( hour [ , min [ , sec [ , ms ] ] ] ) <div id="sec-date.prototype.sethours"></div>

The following steps are performed:

1. Let t be LocalTime(? thisTimeValue(this value)).
2. Let h be ? ToNumber(hour).
3. If min is not present, let m be MinFromTime(t); otherwise, let m be ? ToNumber(min).
4. If sec is not present, let s be SecFromTime(t); otherwise, let s be ? ToNumber(sec).
5. If ms is not present, let milli be msFromTime(t); otherwise, let milli be ? ToNumber(ms).
6. Let date be MakeDate(Day(t), MakeTime(h, m, s, milli)).
7. Let u be TimeClip(UTC(date)).
8. Set the [[DateValue]] internal slot of this Date object to u.
9. Return u.

The "length" property of the setHours method is 4.

> NOTE If min is not present, this method behaves as if min was present with the value getMinutes(). If sec is not present, it behaves as if sec was present with the value getSeconds(). If ms is not present, it behaves as if ms was present with the value getMilliseconds().

#### 20.3.4.23 Date.prototype.setMilliseconds ( ms ) <div id="sec-date.prototype.setmilliseconds"></div>

The following steps are performed:

1. Let t be LocalTime(? thisTimeValue(this value)).
2. Set ms to ? ToNumber(ms).
3. Let time be MakeTime(HourFromTime(t), MinFromTime(t), SecFromTime(t), ms).
4. Let u be TimeClip(UTC(MakeDate(Day(t), time))).
5. Set the [[DateValue]] internal slot of this Date object to u.
6. Return u

#### 20.3.4.24 Date.prototype.setMinutes ( min [ , sec [ , ms ] ] ) <div id="sec-date.prototype.setminutes"></div>

The following steps are performed:

1. Let t be LocalTime(? thisTimeValue(this value)).
2. Let m be ? ToNumber(min).
3. If sec is not present, let s be SecFromTime(t); otherwise, let s be ? ToNumber(sec).
4. If ms is not present, let milli be msFromTime(t); otherwise, let milli be ? ToNumber(ms).
5. Let date be MakeDate(Day(t), MakeTime(HourFromTime(t), m, s, milli)).
6. Let u be TimeClip(UTC(date)).
7. Set the [[DateValue]] internal slot of this Date object to u.
8. Return u

The "length" property of the setMinutes method is 3.

>NOTE If sec is not present, this method behaves as if sec was present with the value getSeconds(). If ms is not present, this behaves as if ms was present with the value getMilliseconds().

#### 20.3.4.25 Date.prototype.setMonth ( month [ , date ] ) <div id="sec-date.prototype.setmonth"></div>

The following steps are performed:

1. Let t be LocalTime(? thisTimeValue(this value)).
2. Let m be ? ToNumber(month).
3. If date is not present, let dt be DateFromTime(t); otherwise, let dt be ? ToNumber(date).
4. Let newDate be MakeDate(MakeDay(YearFromTime(t), m, dt), TimeWithinDay(t)).
5. Let u be TimeClip(UTC(newDate)).
6. Set the [[DateValue]] internal slot of this Date object to u.
7. Return u.

The "length" property of the setMonth method is 2.

> NOTE If date is not present, this method behaves as if date was present with the value getDate().

#### 20.3.4.26 Date.prototype.setSeconds ( sec [ , ms ] ) <div id="sec-date.prototype.setseconds"></div>

The following steps are performed:

1. Let t be LocalTime(? thisTimeValue(this value)).
2. Let s be ? ToNumber(sec).
3. If ms is not present, let milli be msFromTime(t); otherwise, let milli be ? ToNumber(ms).
4. Let date be MakeDate(Day(t), MakeTime(HourFromTime(t), MinFromTime(t), s, milli)).
5. Let u be TimeClip(UTC(date)).
6. Set the [[DateValue]] internal slot of this Date object to u.
7. Return u.

The "length" property of the setSeconds method is 2.

> NOTE If ms is not present, this method behaves as if ms was present with the value getMilliseconds().

#### 20.3.4.27 Date.prototype.setTime ( time ) <div id="sec-date.prototype.settime"></div>

The following steps are performed:

1. Perform ? thisTimeValue(this value).
2. Let t be ? ToNumber(time).
3. Let v be TimeClip(t).
4. Set the [[DateValue]] internal slot of this Date object to v.
5. Return v.

#### 20.3.4.28 Date.prototype.setUTCDate ( date ) <div id="sec-date.prototype.setutcdate"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. Let dt be ? ToNumber(date).
3. Let newDate be MakeDate(MakeDay(YearFromTime(t), MonthFromTime(t), dt), TimeWithinDay(t)).
4. Let v be TimeClip(newDate).
5. Set the [[DateValue]] internal slot of this Date object to v.
6. Return v

#### 20.3.4.29 Date.prototype.setUTCFullYear ( year [ , month [ , date ] ] ) <div id="sec-date.prototype.setutcfullyear"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. If t is NaN, set t to +0.
3. Let y be ? ToNumber(year).
4. If month is not present, let m be MonthFromTime(t); otherwise, let m be ? ToNumber(month).
5. If date is not present, let dt be DateFromTime(t); otherwise, let dt be ? ToNumber(date).
6. Let newDate be MakeDate(MakeDay(y, m, dt), TimeWithinDay(t)).
7. Let v be TimeClip(newDate).
8. Set the [[DateValue]] internal slot of this Date object to v.
9. Return v

The "length" property of the setUTCFullYear method is 3

> NOTE If month is not present, this method behaves as if month was present with the value getUTCMonth(). If date is not present, it behaves as if date was present with the value getUTCDate().

#### 20.3.4.30 Date.prototype.setUTCHours ( hour [ , min [ , sec [ , ms ] ] ] ) <div id="sec-date.prototype.setutchours"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. Let h be ? ToNumber(hour).
3. If min is not present, let m be MinFromTime(t); otherwise, let m be ? ToNumber(min).
4. If sec is not present, let s be SecFromTime(t); otherwise, let s be ? ToNumber(sec).
5. If ms is not present, let milli be msFromTime(t); otherwise, let milli be ? ToNumber(ms).
6. Let newDate be MakeDate(Day(t), MakeTime(h, m, s, milli)).
7. Let v be TimeClip(newDate).
8. Set the [[DateValue]] internal slot of this Date object to v.
9. Return v

The "length" property of the setUTCHours method is 4.

> NOTE If min is not present, this method behaves as if min was present with the value getUTCMinutes(). If sec is not present, it behaves as if sec was present with the value getUTCSeconds(). If ms is not present, it behaves as if ms was present with the value getUTCMilliseconds().

#### 20.3.4.31 Date.prototype.setUTCMilliseconds ( ms ) <div id="sec-date.prototype.setutcmilliseconds"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. Let milli be ? ToNumber(ms).
3. Let time be MakeTime(HourFromTime(t), MinFromTime(t), SecFromTime(t), milli).
4. Let v be TimeClip(MakeDate(Day(t), time)).
5. Set the [[DateValue]] internal slot of this Date object to v.
6. Return v.

#### 20.3.4.32 Date.prototype.setUTCMinutes ( min [ , sec [ , ms ] ] ) <div id="sec-date.prototype.setutcminutes"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. Let m be ? ToNumber(min).
3. If sec is not present, let s be SecFromTime(t).
4. Else,
    1. Let s be ? ToNumber(sec).
5. If ms is not present, let milli be msFromTime(t).
6. Else,
    1. Let milli be ? ToNumber(ms).
7. Let date be MakeDate(Day(t), MakeTime(HourFromTime(t), m, s, milli)).
8. Let v be TimeClip(date).
9. Set the [[DateValue]] internal slot of this Date object to v.
10. Return v.

The "length" property of the setUTCMinutes method is 3

> NOTE If sec is not present, this method behaves as if sec was present with the value getUTCSeconds(). If ms is not present, it function behaves as if ms was present with the value return by getUTCMilliseconds().

#### 20.3.4.33 Date.prototype.setUTCMonth ( month [ , date ] ) <div id="sec-date.prototype.setutcmonth"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. Let m be ? ToNumber(month).
3. If date is not present, let dt be DateFromTime(t).
4. Else,
    1. Let dt be ? ToNumber(date).
5. Let newDate be MakeDate(MakeDay(YearFromTime(t), m, dt), TimeWithinDay(t)).
6. Let v be TimeClip(newDate).
7. Set the [[DateValue]] internal slot of this Date object to v.
8. Return v

The "length" property of the setUTCMonth method is 2.

> NOTE If date is not present, this method behaves as if date was present with the value getUTCDate().

#### 20.3.4.34 Date.prototype.setUTCSeconds ( sec [ , ms ] ) <div id="sec-date.prototype.setutcseconds"></div>

The following steps are performed:

1. Let t be ? thisTimeValue(this value).
2. Let s be ? ToNumber(sec).
3. If ms is not present, let milli be msFromTime(t).
4. Else,
    1. Let milli be ? ToNumber(ms).
5. Let date be MakeDate(Day(t), MakeTime(HourFromTime(t), MinFromTime(t), s, milli)).
6. Let v be TimeClip(date).
7. Set the [[DateValue]] internal slot of this Date object to v.
8. Return v.

The "length" property of the setUTCSeconds method is 2

> NOTE If ms is not present, this method behaves as if ms was present with the value getUTCMilliseconds().

#### 20.3.4.35 Date.prototype.toDateString ( ) <div id="sec-date.prototype.todatestring"></div>

The following steps are performed:

1. Let O be this Date object.
2. Let tv be ? thisTimeValue(O).
3. If tv is NaN, return "Invalid Date".
4. Let t be LocalTime(tv).
5. Return DateString(t).

#### 20.3.4.36 Date.prototype.toISOString ( ) <div id="sec-date.prototype.toisostring"></div>

This function returns a String value representing the instance in time corresponding to this time value. The format of the String is the Date Time string format defined in 20.3.1.15. All fields are present in the String. The time zone is always UTC, denoted by the suffix Z. If this time value is not a finite Number or if the year is not a value that can be represented in that format (if necessary using expanded year format), a RangeError exception is thrown.

#### 20.3.4.37 Date.prototype.toJSON ( key ) <div id="sec-date.prototype.tojson"></div>

This function provides a String representation of a Date object for use by JSON.stringify (24.5.2).

When the toJSON method is called with argument key, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let tv be ? ToPrimitive(O, hint Number).
3. If Type(tv) is Number and tv is not finite, return null.
4. Return ? Invoke(O, "toISOString")

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

The following steps are performed:

1. Let tv be ? thisTimeValue(this value).
2. Return ToDateString(tv).

> NOTE 1 For any Date object d whose milliseconds amount is zero, the result of Date.parse(d.toString()) is equal to d.valueOf(). See 20.3.3.2.

> NOTE 2 The toString function is not generic; it throws a TypeError exception if its this value is not a Date object. Therefore, it cannot be transferred to other kinds of objects for use as a method.

##### 20.3.4.41.1 RS: TimeString ( tv ) <div id="sec-timestring"></div>

The following steps are performed:

1. Assert: Type(tv) is Number.
2. Assert: tv is not NaN.
3. Let hour be the String representation of HourFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
4. Let minute be the String representation of MinFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
5. Let second be the String representation of SecFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
6. Return the string-concatenation of hour, ":", minute, ":", second, the code unit 0x0020 (SPACE), and "GMT".

##### 20.3.4.41.2 RS: DateString ( tv ) <div id="sec-datestring"></div>

The following steps are performed:

1. Assert: Type(tv) is Number.

2. Assert: tv is not NaN.

3. Let weekday be the Name of the entry in Table 49 with the Number WeekDay(tv).

4. Let month be the Name of the entry in Table 50 with the Number MonthFromTime(tv).

5. Let day be the String representation of DateFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.

6. Let year be the String representation of YearFromTime(tv), formatted as a decimal number of at least four digits, padded to the left with zeroes if necessary.

7. Return the string-concatenation of weekday, the code unit 0x0020 (SPACE), month, the code unit 0x0020 (SPACE), day, the code unit 0x0020 (SPACE), and year.

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

The following steps are performed:

1. Assert: Type(tv) is Number.
2. Assert: tv is not NaN.
3. Let offset be LocalTZA(tv, true).
4. If offset ≥ 0, let offsetSign be "+"; otherwise, let offsetSign be "-".
5. Let offsetMin be the String representation of MinFromTime(abs(offset)), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
6. Let offsetHour be the String representation of HourFromTime(abs(offset)), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
7. Let tzName be an implementation-defined string that is either the empty string or the string concatenation of the code unit 0x0020 (SPACE), the code unit 0x0028 (LEFT PARENTHESIS), an implementation-dependent timezone name, and the code unit 0x0029 (RIGHT PARENTHESIS).
8. Return the string-concatenation of offsetSign, offsetHour, offsetMin, and tzName.

##### 20.3.4.41.4 RS: ToDateString ( tv ) <div id="sec-todatestring"></div>

The following steps are performed:

1. Assert: Type(tv) is Number.
2. If tv is NaN, return "Invalid Date".
3. Let t be LocalTime(tv).
4. Return the string-concatenation of DateString(t), the code unit 0x0020 (SPACE), TimeString(t), and TimeZoneString(tv).

#### 20.3.4.42 Date.prototype.toTimeString ( ) <div id="sec-date.prototype.totimestring"></div>

The following steps are performed:

1. Let O be this Date object.
2. Let tv be ? thisTimeValue(O).
3. If tv is NaN, return "Invalid Date".
4. Let t be LocalTime(tv).
5. Return the string-concatenation of TimeString(t) and TimeZoneString(tv)

#### 20.3.4.43 Date.prototype.toUTCString ( ) <div id="sec-date.prototype.toutcstring"></div>

The following steps are performed:

1. Let O be this Date object.
2. Let tv be ? thisTimeValue(O).
3. If tv is NaN, return "Invalid Date".
4. Let weekday be the Name of the entry in Table 49 with the Number WeekDay(tv).
5. Let month be the Name of the entry in Table 50 with the Number MonthFromTime(tv).
6. Let day be the String representation of DateFromTime(tv), formatted as a two-digit decimal number, padded to the left with a zero if necessary.
7. Let year be the String representation of YearFromTime(tv), formatted as a decimal number of at least four digits, padded to the left with zeroes if necessary.
8. Return the string-concatenation of weekday, ",", the code unit 0x0020 (SPACE), day, the code unit 0x0020 (SPACE), month, the code unit 0x0020 (SPACE), year, the code unit 0x0020 (SPACE), and TimeString(tv).

#### 20.3.4.44 Date.prototype.valueOf ( ) <div id="sec-date.prototype.valueof"></div>

The following steps are performed:

1. Return ? thisTimeValue(this value)

#### 20.3.4.45 Date.prototype [ @@toPrimitive ] ( hint ) <div id="sec-date.prototype-@@toprimitive"></div>

This function is called by ECMAScript language operators to convert a Date object to a primitive value. The allowed values for hint are "default", "number", and "string". Date objects, are unique among built-in ECMAScript object in that they treat "default" as being equivalent to "string", All other built-in ECMAScript objects treat "default" as being equivalent to "number".

When the @@toPrimitive method is called with argument hint, the following steps are taken:

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If hint is the String value "string" or the String value "default", then
    1. Let tryFirst be "string".
4. Else if hint is the String value "number", then
    1. Let tryFirst be "number".
5. Else, throw a TypeError exception.
6. Return ? OrdinaryToPrimitive(O, tryFirst).

The value of the name property of this function is "[Symbol.toPrimitive]".

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

### 20.3.5 Date 实例属性 <div id="sec-properties-of-date-instances"></div>

Date instances are ordinary objects that inherit properties from the Date prototype object. Date instances also have a [[DateValue]] internal slot. The [[DateValue]] internal slot is the time value represented by this Date object.