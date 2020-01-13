# 21 文字处理
## 21.1 String 对象 <div id="sec-string-objects"></div>
### 21.1.1 String 构造器 <div id="sec-string-constructor"></div>

String构造器：

- 是内部对象％String％。
- 是全局对象的String属性的初始值。
- 在作为构造函数调用时创建并初始化一个新的String对象。
- 当作为函数而不是构造函数调用时执行类型转换。
- 设计为可归类的。它可以用作类定义的extends子句的值。打算继承指定的String行为的子类构造函数必须包括对String构造函数的超级调用，以使用[[StringData]]内部插槽创建和初始化子类实例。

#### 21.1.1.1 String ( value ) <div id="sec-string-constructor-string-value"></div>

当使用参数值调用String时，将执行以下步骤：

1. 如果没有参数传递给该函数调用，则将其设为“”。
2.  否则，
   1. 若 NewTarget 是 undefined，并且 Type(value) 是 Symbol，返回 SymbolDescriptiveString(value).
   2. 令 s 为 ? ToString(value).
3. 若 NewTarget 是 undefined，返回 s.
4. 返回 ! StringCreate(s, ? GetPrototypeFromConstructor(NewTarget, "%StringPrototype%")).

### 21.1.2 String 构造器属性 <div id="sec-properties-of-the-string-constructor"></div>

String构造器：

- 有一个[[Prototype]]内部插槽，其值是内部对象％FunctionPrototype％。
- 具有以下特性:

#### 21.1.2.1 String.fromCharCode ( ...codeUnits ) <div id="sec-string.fromcharcode"></div>

可以使用组成rest参数codeUnits的任意数量的参数调用String.fromCharCode函数。采取以下步骤:

1. 令 codeUnits 为包含传递给此函数的参数的列表。
2. 令 length 为codeUnits中的元素数量。
3. 令 elements 为一个新的空列表。
4. 令 nextIndex 为 0.
5. 重复，当 nextIndex < length
   1. 令 next 为 codeUnits[nextIndex].
   2. 令 nextCU 为 ? ToUint16(next).
   3. 添加 nextCU 到元素的结尾。
   4. nextIndex 增加 1
6. 返回代码单元按顺序为列表元素中的元素的字符串值。如果长度为0，则返回空字符串。

fromCharCode函数的“length”属性是1。

#### 21.1.2.2 String.fromCodePoint ( ...codePoints ) <div id="sec-string.fromcodepoint"></div>

可以使用组成rest参数codePoints的任意数量的参数调用String.fromCodePoint函数。采取以下步骤:

1. 令 codePoints 为包含传递给此函数的参数的列表。
2. 令 length 为代码点中的元素数量。
3. 令 elements 为一个新的空列表。
4. 令 nextIndex 为 0.
5. 重复，当 nextIndex < length
1. 令 next 为 codePoints[nextIndex].
2. 令 nextCP 为 ? ToNumber(next).
3. 若 SameValue(nextCP, ! ToInteger(nextCP)) 是 false, 抛出 RangeError 异常.
4. 若 nextCP < 0 or nextCP > 0x10FFFF, 抛出 RangeError 异常.
5. 添加 nextCP 的 utf16 编码的元素到元素的结尾。
6. nextIndex 增加 1
6. 返回代码单元按顺序为列表元素中的元素的字符串值。如果长度为0，则返回空字符串。

fromCharCode函数的“length”属性是1。

#### 21.1.2.3 String.prototype <div id="sec-string.prototype"></div>

字符串的初始值。prototype是内部对象%StringPrototype%。

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 21.1.2.4 String.raw ( template, ...substitutions ) <div id="sec-string.raw"></div>

String.raw 函数可以用可变数量的参数来调用。第一个参数是template，其余的参数构成列表替换。采取以下步骤:

1. 令 substitutions 为由传递给这个函数的所有参数组成的列表，从第二个参数开始。如果传递的参数少于两个，则列表为空。
2. 令 numberOfSubstitutions 为替换中元素的数量。
3. 令 cooked 为 ? ToObject(template).
4. 令 raw 为 ? ToObject(? Get(cooked, "raw")).
5. 令 literalSegments 为 ? ToLength(? Get(raw, "length")).
6. 若 literalSegments ≤ 0，返回空字符串.
7. 令 stringElements 为一个新的空列表。
8. 令 nextIndex 为 0.
9. 重复，
   1. 令 nextKey 为 ! ToString(nextIndex).
   2. 令 nextSeg 为 ? ToString(? Get(raw, nextKey)).
   3. 添加 nextSeg 的代码单元元素的顺序 到 stringElements 的结尾。
   4. 若 nextIndex + 1 = literalSegments，那么 
   5. 返回代码单元按顺序为stringElements列表中的元素的字符串值。如果stringElements没有元素，则返回空字符串。
   6. 若 nextIndex < numberOfSubstitutions, 令 next 为 substitutions[nextIndex].
   7. 否则，令 next 为空字符串
   8. 令 nextSub 为 ? ToString(next).
   9. 添加 nextSeg 的代码单元元素的顺序 到 stringElements 的结尾。
   10. nextIndex 增加1

> 注：String.raw 是用于标记模板的标记函数(12.3.7)。当这样调用时，第一个参数将是格式良好的模板对象，而rest参数将包含替换值。

### 21.1.3 String 原型对象属性 <div id="sec-properties-of-the-string-prototype-object"></div>

字符串原型对象:

- 是内部对象%StringPrototype%。
- 是一个字符串异类对象，并具有为此类对象指定的内部方法。
- 有一个[[StringData]]内部插槽，其值为空String。
- 具有 "length" 属性，其初始值为0，其属性为 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.
- 有一个[[Prototype]]内部插槽，其值是内部对象％ObjectPrototype％。

除非另有明确说明，否则以下定义的String原型对象的方法不是通用的，并且传递给它们的this值必须是String值或具有[[StringData]]内部插槽且已初始化为String的对象值

抽象操作thisStringValue（value）执行以下步骤：

1. 若 Type(value) 是 String，返回 value.
2. 若 Type(value) 是对象和值具有一个[[StringData]]内部插槽，那么 
   1. 令 s 为 value.[[StringData]].
   2. 断言：Type(s) 是 String.
   3. 返回 s.
3. 抛出 TypeError 异常.

#### 21.1.3.1 String.prototype.charAt ( pos ) <div id="sec-string.prototype.charat"></div>

> 注1：返回单个元素String，该字符串包含在String值内的索引pos处的代码单元，该值由将该对象转换为String产生。如果该索引处没有元素，则结果为空String。结果是一个String值，而不是String对象。
>
> 如果pos是Number类型的值为整数的值，则x.charAt（pos）的结果等于x.substring（pos，pos + 1）的结果

当使用一个参数pos调用charAt方法时，将执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 position 为 ? ToInteger(pos).
4. 令 size 为S的长度.
5. 若 position < 0 or position ≥ size，返回空字符串.
6. 返回长度为1的字符串值，包含一个来自S的代码单元，即索引位置的代码单元。

> 注2：charAt函数是有意通用的。它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.2 String.prototype.charCodeAt ( pos ) <div id="sec-string.prototype.charcodeat"></div>

> 注1：返回一个数字（小于216的非负整数），该数字是在String内的索引pos处代码单元的数字值，该值是将此对象转换为String所得到的。如果该索引处没有元素，则结果为NaN。

当使用一个参数pos调用charCodeAt方法时，将执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 position 为 ? ToInteger(pos).
4. 令 size 为S的长度.
5. 若 position < 0 or position ≥ size，返回 NaN.
6. 返回一个Number类型的值，其值是字符串S中位于索引位置的代码单元的数值。

> 注2：charCodeAt函数是有意通用的。它不需要其this值是String对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 21.1.3.3 String.prototype.codePointAt ( pos ) <div id="sec-string.prototype.codepointat"></div>

> 注1：返回一个小于0x110000的非负整数，该整数是UTF-16编码的代码点（6.1.4）的代码点值，从该对象转换为String所得的String索引处的字符串pos开始。如果该索引处没有元素，则结果不确定。如果有效的UTF-16代理对不是从pos开始，则结果是在pos的代码单元

当使用一个参数pos调用codePointAt方法时，将执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 position 为 ? ToInteger(pos).
4. 令 size 为S的长度。
5. 若 position < 0 or position ≥ size，返回 undefined.
6. 令 first 为字符串S中索引位置的代码单元的数值。
7. 若 first < 0xD800 或 first > 0xDBFF 或 position + 1 = size，返回 first.
8. 令 second 为字符串S中位于索引位置+1处的代码单元的数值。
9. 若 second < 0xDC00 or second > 0xDFFF，返回 first.
10. 返回 UTF16Decode(first, second).

> 注2：codePointAt函数是有意通用的。它不需要其this值是String对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 21.1.3.4 String.prototype.concat ( ...args ) <div id="sec-string.prototype.concat"></div>

> 注1：调用concat方法时，它返回由该对象的代码单元（转换为字符串）组成的字符串值，然后是转换为字符串的每个参数的代码单元。结果是一个String值，而不是String对象。

当使用零个或多个参数调用concat方法时，将执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 args 为一个列表，其元素是传递给此函数的参数。
4. 令 R 为 S.
5. 重复，而args不为空
   1. 从args中删除第一个元素，然后让next为该元素的值。
   2. 令 nextString 为 ? ToString(next).
   3. 将R设置为R和nextString的上一个值的字符串串联。
6. 返回 R.

concat方法的“ length”属性为1。

> 注2：concat函数是有意通用的。它不需要其this值是String对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 21.1.3.5 String.prototype.constructor <div id="sec-string.prototype.constructor"></div>

String.prototype.constructor的初始值为内部对象％String％。

#### 21.1.3.6 String.prototype.endsWith ( searchString [ , endPosition ] ) <div id="sec-string.prototype.endswith"></div>

采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 isRegExp 为 ? IsRegExp(searchString).
4. 若 isRegExp 是 true, 抛出 TypeError 异常.
5. 令 searchStr 为 ? ToString(searchString).
6. 令 len 为S的长度.
7. 若 endPosition 是 undefined, 令 pos 为 len，否则令 pos 为 ? ToInteger(endPosition).
8. 令 end 为 min(max(pos, 0), len).
9. 令 searchLength 为searchStr的长度。
10. 令 start 为 end - searchLength.
11. 若 start 小于 0，返回 false.
12. 若从长度searchLength开始的S的代码单元序列与searchStr的完整代码单元序列相同，返回 true.
13. 除此之外，返回 false.

>注 1
>
>如果searchString转换为String的代码单元序列与此对象（转换为String）从endPosition-length（this）开始的相应代码单元序列相同，则返回true。否则返回false。
>
>注 2
>
>如果第一个参数是RegExp，则抛出异常，以允许将来的版本定义允许此类参数值的扩展。
>
>注 3
>
>endsWith函数是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.7 String.prototype.includes ( searchString [ , position ] ) <div id="sec-string.prototype.includes"></div>

include方法采用两个参数searchString和position，并执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 isRegExp 为 ? IsRegExp(searchString).
4. 若 isRegExp 是 true, 抛出 TypeError 异常.
5. 令 searchStr 为 ? ToString(searchString).
6. 令 pos 为 ? ToInteger(position).
7. 断言：若 position 是 undefined，那么  pos 是 0.
8. 令 len 为S的长度。
9. 令 start 为 min(max(pos, 0), len).
10. 令 searchLen 为searchStr的长度。

11. 如果存在不小于起始值的整数k使得k + searchLen不大于len，并且对于所有小于searchLen的非负整数j，则S中位于索引k + j的代码单元与位于索引处的代码单元相同在searchStr中的j，返回true；但是，如果没有这样的整数k，则返回false。

>注 1
>
>如果searchString作为将该对象转换为String的结果的子字符串出现，则在大于或等于position的一个或多个索引处，返回true；否则，返回true。否则，返回false。如果未定义位置，则假定为0，以便搜索所有String。
>
>注 2
>
>如果第一个参数是RegExp，则抛出异常，以允许将来的版本定义允许此类参数值的扩展。
>
>注 3
>
>包含函数是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.8 String.prototype.indexOf ( searchString [ , position ] ) <div id="sec-string.prototype.indexof"></div>

> 注 1：如果searchString作为将此对象转换为String的结果的子字符串出现，则在大于或等于position的一个或多个索引处，将返回最小的索引；否则，返回-1。如果未定义位置，则假定为0，以便搜索所有String。

indexOf方法采用两个参数searchString和position，并执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 searchStr 为 ? ToString(searchString).
4. 令 pos 为 ? ToInteger(position).
5. 断言：若 position 是  undefined，那么  pos 是 0.
6. 令 len 为S的长度。
7. 令 start 为 min(max(pos, 0), len).
8. 令 searchLen 为searchStr的长度。
9. 返回不小于开始的最小可能整数k，使得k + searchLen不大于len，并且对于所有小于searchLen的非负整数j，S中位于索引k + j的代码单元与位于索引j的代码单元相同在searchStr内；但是如果没有这样的整数k，则返回值-1。

> 注 2：indexOf函数是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.9 String.prototype.lastIndexOf ( searchString [ , position ] ) <div id="sec-string.prototype.lastindexof"></div>

> 注 1：如果searchString作为将该对象转换为String的结果的子字符串出现在一个或多个小于或等于position的索引处，则返回最大的索引；否则，返回-1。如果position未定义，则采用String值的长度，以便搜索所有String。

lastIndexOf方法采用两个参数searchString和position，并执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 searchStr 为 ? ToString(searchString).
4. 令 numPos 为 ? ToNumber(position).
5. 断言：若 position 是 undefined，那么  numPos 是 NaN.
6. 若 numPos 是 NaN, 令 pos 为 +∞; 除此之外，令 pos 为 ! ToInteger(numPos).
7. 令 len 为S的长度.
8. 令 start 为 min(max(pos, 0), len).
9. 令 searchLen 为searchStr的长度。
10. 返回不大于start的最大可能的非负整数k，以使k + searchLen不大于len，并且对于所有小于searchLen的非负整数j，S中位于索引k + j的代码单元与位于索引处的代码单元相同j在searchStr内；但是如果没有这样的整数k，则返回值-1。

> 注 2 lastIndexOf函数是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.10 String.prototype.localeCompare ( that [ , reserved1 [ , reserved2 ] ] ) <div id="sec-string.prototype.localecompare"></div>

包含ECMA-402国际化API的ECMAScript实现必须实现ECMA-402规范中指定的localeCompare方法。如果ECMAScript实现不包含ECMA-402 API，则使用以下localeCompare方法的规范。

当使用带有作为参数的参数调用localeCompare方法时，它返回的数字不是NaN，它表示此值（转换为String）与该值（转换为String）的区域设置敏感的String比较结果。这两个字符串是S和That。这两个字符串以实现定义的方式进行比较。该结果旨在按主机默认语言环境指定的排序顺序对String值进行排序，并将为负，零或正，具体取决于S排在前面，还是S排在后面； S是排在后面还是相等。分别以排序顺序表示。

在执行比较之前，请执行以下步骤来准备字符串：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 That 为 ? ToString(that).

ECMA-402规范中定义了此方法的可选第二和第三参数的含义；不包含ECMA-402支持的实现不得将任何其他解释分配给这些参数位置。

如果localeCompare方法被视为this和that两个参数的函数，则它是所有String集合上的一致比较函数（如22.1.3.27中所定义）。

实际的返回值是由实现定义的，以允许实现者在值中编码其他信息，但是需要该函数来定义所有String的总顺序。此函数必须将根据Unicode标准规范等效的字符串视为相同，并且在比较被认为等效的字符串时必须返回0。

> 注 1
>
> localeCompare方法本身并不直接适合作为Array.prototype.sort的参数，因为后者需要两个参数的函数。
>
> 注 2
>
> 此功能旨在依赖宿主环境可用于ECMAScript环境的任何语言敏感比较功能，并根据宿主环境当前语言环境的规则进行比较。但是，无论主机提供了什么比较功能，此函数都必须将根据Unicode标准规范等效的字符串视为相同。建议此函数不要遵循Unicode兼容性等效或分解。有关规范等效性的定义和讨论，请参见Unicode标准第2章和第3章，以及Unicode标准附件＃15，Unicode规范化形式（https://unicode.org/reports/tr15/）和Unicode技术说明＃5 ，应用中的规范对等（https://www.unicode.org/notes/tn5/）。另请参阅Unicode技术标准＃10，Unicode排序算法（https://unicode.org/reports/tr10/）。
>
> 注 3
>
> localeCompare函数是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.11 String.prototype.match ( regexp ) <div id="sec-string.prototype.match"></div>

当使用参数regexp调用match方法时，将执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 若 regexp 不是 undefined 或 null，那么 
1. 令 matcher 为 ? GetMethod(regexp, @@match).
2. 若 matcher 不是 undefined，那么 
   1. 返回 ? Call(matcher, regexp, « O »).
3. 令 S 为 ? ToString(O).
4. 令 rx 为 ? RegExpCreate(regexp, undefined).
5. 返回 ? Invoke(rx, @@match, « S »).

> 注：匹配功能是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.12 String.prototype.normalize ( [ form ] ) <div id="sec-string.prototype.normalize"></div>

当使用一个参数形式调用normalize方法时，将执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 若 form 不存在，或 form 是 undefined, 设置 form 为 "NFC".
4. 令 f 为 ? ToString(form).
5. 若 f 不是 "NFC", "NFD", "NFKC", 或 "NFKD"其中之一, 抛出 RangeError 异常.
6. 令 ns 为字符串值，它是将S标准化为https://unicode.org/reports/tr15/中指定的以f命名的标准化形式的结果。
7. 返回 ns.

> 注：规范化功能是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 21.1.3.13 String.prototype.padEnd ( maxLength [ , fillString ] ) <div id="sec-string.prototype.padend"></div>

调用padEnd方法时，将执行以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 intMaxLength 为 ? ToLength(maxLength).
4. 令 stringLength 为S的长度.
5. 若 intMaxLength 不大于 stringLength，返回 S.
6. 若 fillString 是 undefined, 令 filler 为仅由代码单元0x0020（空格）组成的字符串值。
7.  否则，令 filler 为 ? ToString(fillString).
8. 若 filler 是空字符串，返回 S.
9. 令 fillLen 为 intMaxLength - stringLength.
10. 令 truncatedStringFiller 为String值，该值由填充的重复串联组成，这些填充被截断为长度fillLen。
11. 返回 S 和 truncatedStringFiller 的字符串连接。

> 注 1 
>
> 将限制第一个参数maxLength，使其不小于该值的长度。
>
> 注 2
>
> 第二个可选参数fillString默认为“”(由代码单元0x0020空格组成的字符串值)。

#### 21.1.3.14 String.prototype.padStart ( maxLength [ , fillString ] ) <div id="sec-string.prototype.padstart"></div>

当调用padStart方法时，采取以下步骤:

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 intMaxLength 为 ? ToLength(maxLength).
4. 令 stringLength 为S的长度.
5. 若 intMaxLength 不大于 stringLength，返回 S.
6. 若 fillString 是 undefined, 令 filler 为仅由代码单元0x0020(空格)组成的字符串值。
7.  否则， 令 filler 为 ? ToString(fillString).
8. 若 filler 是空字符串，返回 S.
9. 令 fillLen 为 intMaxLength - stringLength.
10. 令 truncatedStringFiller 为由重复连接的填充符组成的字符串值，将其截断为长度fillLen。
11. 返回truncatedStringFiller和S的字符串连接。

> 注 1
>
> 将限制第一个参数maxLength，使其不小于该值的长度。
>
> 注 2 
>
> 第二个可选参数fillString默认为“”(由代码单元0x0020空格组成的字符串值)。

#### 21.1.3.15 String.prototype.repeat ( count ) <div id="sec-string.prototype.repeat"></div>

将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 n 为 ? ToInteger(count).
4. 若 n < 0, 抛出 RangeError 异常.
5. 若 n 是 +∞, 抛出 RangeError 异常.
6. 若 n 是 0，返回空字符串.
7. 返回由S的n个副本组成的String值

> 注 1
>
> 此方法创建String值，该值由该对象的代码单元（转换为String）重复的计数时间组成。
>
> 注 2
> 重复功能是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.16 String.prototype.replace ( searchValue, replaceValue ) <div id="sec-string.prototype.replace"></div>

当用参数searchValue和replaceValue调用replace方法时，将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 若 searchValue 既不是undefined，也不是null，那么 
   1. 令 replacer 为 ? GetMethod(searchValue, @@replace).
   2. 若 replacer 不是 undefined，那么 
      1. 返回 ? Call(replacer, searchValue, « O, replaceValue »).
3. 令 string 为 ? ToString(O).
4. 令 searchString 为 ? ToString(searchValue).
5. 令 functionalReplace 为 IsCallable(replaceValue).
6. 若 functionalReplace 是 false，那么 
   1. 设置 replaceValue 为 ? ToString(replaceValue).
7. 搜索字符串以查找searchString的第一个匹配项，并令pos为匹配子字符串的第一个代码单元的字符串内的索引，而让match为searchString，返回 string.
8. 若 functionalReplace 是 true，那么 
     1. 令 replValue 为 ? Call(replaceValue, undefined, « matched, pos, string »).
     2. 令 replStr 为 ? ToString(replValue).
9. 否则，
     1. 令 captures 为一个新的空列表。
     2. 令 replStr 为 GetSubstitution(matched, string, pos, captures, undefined, replaceValue).
10. 令 tailPos 为 pos + 匹配的代码单元数。
11. 令 newString 为 string 的第一个pos代码单元，replStr，从索引tailPos开始的字符串的尾部子字符串的字符串级联。如果pos为0，则串联的第一个元素将为空String。
12. 返回 newString.

> 注
>
> 替换功能是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

##### 21.1.3.16.1 RS: GetSubstitution ( matched, str, position, captures, namedCaptures, replacement )222<div id="sec-getsubstitution"></div>

抽象操作GetSubstitution执行以下步骤：

1. 断言：Type(matched) 是 String.
2. 令 matchLength 为匹配的代码单元数。
3. 断言：Type(str) 是 String.
4. 令 stringLength 为 str 中的代码单位数。
5. 断言：position是一个非负整数。
6. 断言：position ≤ stringLength.
7. 断言：captures是一个可能为空的字符串列表。
8. 断言：Type(replacement) 是 String.
9. 令 tailPos 为 position + matchLength.
10. 令 m 为捕获中元素的数量。
11. 若 namedCaptures 不是 undefined，那么 
1. 设置 namedCaptures 为 ? ToObject(namedCaptures).
12. 令 result 为替换产生的字符串值，方法是按照表51的指定在执行替换时将代码单元元素从替换复制到结果。这些$替换从左到右完成，并且一旦执行了这样的替换，就不会生成新的替换文本有待进一步更换。
13. 返回 result.

表51：替换文本符号替换


| 代码单元                              | Unicode Characters                                           | Replacement text                                             |
| ------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 0x0024, 0x0024                        | `$$`                                                         | `$`                                                          |
| 0x0024, 0x0026                        | `$&`                                                         | matched                                                      |
| 0x0024, 0x0060                        | `$``                                                         | 如果position为0，则替换为空String。否则，替换将是str的子字符串，该子字符串从索引0开始并且其最后一个代码单元位于索引position -1。 |
| 0x0024, 0x0027                        | `$'`                                                         | 如果 tailPos ≥ stringLength ，则替换为空String。否则，替换将是str的子字符串，该子字符串从索引tailPos开始并一直持续到str的结尾。 |
| 0x0024, N 此处 0x0031 ≤ N ≤ 0x0039    | `$n`，`n`是 1 2 3 4 5 6 7 8 9 其中之一，并且 `$n` 后没有十进制数字 | 捕获的第n个元素，其中n是1到9范围内的一位数字。如果n≤m并且捕获的第n个元素undefined，请改用空字符串。如果n> m，则不进行替换。 |
| 0x0024, N, N 此处 0x0030 ≤ N ≤ 0x0039 | `$nn`，`n` 是`0 1 2 3 4 5 6 7 8 9` 其中之一                  | 捕获的第n个元素，其中n是1到9范围内的一个数字。如果n≤m并且捕获的第n个元素未定义，请改用空字符串。如果n> m，则不进行替换。 |
| 0x0024, 0x003C                        | $<                                                           | 1. 若 namedCaptures 是 undefined，替换文本为字符串"$<".<br/>2.  否则，<br/>    1. 扫描到下一个 > U+003E (大于符号).<br/>    2. 如果未找到，则替换文本为字符串“ $ <”。<br/>    3. 否则，<br/>        1. 令groupName作为所包含的子字符串。<br/>        2. 令 capture 为 ? Get(namedCaptures, groupName).<br/>        3. 若 capture 是 undefined, 通过 > 将文本替换为空字符串。<br/>        4. 否则, 通过>将文本替换为 ? ToString(capture). |
| 0x0024                                | `$` 在与以上任何条件都不匹配的任何上下文中。                 | `$`                                                          |



#### 21.1.3.17 String.prototype.search ( regexp ) <div id="sec-string.prototype.search"></div>

当使用参数regexp调用搜索方法时，将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 若 regexp 既不是undefined，也不是null，那么 
1. 令 searcher 为 ? GetMethod(regexp, @@search).
2. 若 searcher 不是 undefined，那么 
  1. 返回 ? Call(searcher, regexp, « O »).
3. 令 string 为 ? ToString(O).
4. 令 rx 为 ? RegExpCreate(regexp, undefined).
5. 返回 ? Invoke(rx, @@search, « string »)

> 注：搜索功能是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 21.1.3.18 String.prototype.slice ( start, end ) <div id="sec-string.prototype.slice"></div>

slice方法有两个参数，即start和end，并返回将该对象转换为String的结果的子字符串，该子字符串从索引start开始并运行至但不包括索引end（或end，如果不包括，则通过String的结尾）未定义）。如果start为负，则将其视为sourceLength + start，其中sourceLength是字符串的长度。如果end为负，则将其视为sourceLength + end，其中sourceLength是字符串的长度。结果是一个String值，而不是String对象。将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 len 为S的长度.
4. 令 intStart 为 ? ToInteger(start).
5. 若 end 是 undefined, 令 intEnd 为 len; else 令 intEnd 为 ? ToInteger(end).
6. 若 intStart < 0, 令 from 为 max(len + intStart, 0); otherwise 令 from 为 min(intStart, len).
7. 若 intEnd < 0, 令 to 为 max(len + intEnd, 0); otherwise 令 to 为 min(intEnd, len).
8. 令 span 为 max(to - from, 0).
9. 返回包含从S开始的跨度连续代码单元的String值，该代码单元从索引from处的代码单元开始。

> 注：slice函数是有意通用的；它不需要其this值是String对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 21.1.3.19 String.prototype.split ( separator, limit ) <div id="sec-string.prototype.split"></div>

返回一个Array对象，此对象转换为String的结果的子字符串已存储在其中。通过从左到右搜索是否出现分隔符来确定子字符串。这些出现不属于返回数组中任何子字符串的一部分，而是用于划分String值。分隔符的值可以是任意长度的字符串，也可以是具有@@split方法的对象，例如RegExp。

调用split方法时，将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 若 separator 既不是undefined，也不是null，那么 
   1. 令 splitter 为 ? GetMethod(separator, @@split).
   2. 若 splitter 不是 undefined，那么 
        1. 返回 ? Call(splitter, separator, « O, limit »).
3. 令 S 为 ? ToString(O).
4. 令 A 为 ! ArrayCreate(0).
5. 令 lengthA 为 0.
6. 若 limit 是 undefined, 令 lim 为 232 - 1; 否则，令 lim 为 ? ToUint32(limit).
7. 令 s 为S的长度.
8. 令 p 为 0.
9. 令 R 为 ? ToString(separator).
10. 若 lim = 0，返回 A.
11. 若 separator 是 undefined，那么 
    1. 执行 ! CreateDataProperty(A, "0", S).
    2. 返回 A.
12. 若 s = 0，那么 
    1. 令 z 为 SplitMatch(S, 0, R).
    2. 若 z 不是 false，返回 A.
    3. 执行 ! CreateDataProperty(A, "0", S).
    4. 返回 A.
13. 令 q 为 p.
14. 重复, 直到 q ≠ s
    1. 令 e 为 SplitMatch(S, q, R).
    2. 若 e 是 false, q 增加1.
    3. 否则 e 是整数索引 ≤ s,
       1. 若 e = p, q 增加1.
       2. 否则 e ≠ p,
          1. 令 T 为字符串值等于S的子字符串，由下标p(包括)到q(不包括)的代码单元组成。
          2. 执行 ! CreateDataProperty(A, ! ToString(lengthA), T).
          3. lengthA 增加 1.
          4. 若 lengthA = lim，返回 A.
          5. 设置 p 为 e.
          6. 设置 q 为 p. 
    
15. 令 T 为字符串值等于S的子字符串，由下标p(包括)到下标S(不包括)的代码单元组成。

1. 执行 ! CreateDataProperty(A, ! ToString(lengthA), T).
2. 返回 A.

> 注 1 
>
> 分隔符的值可以是空字符串。在本例中，separator不匹配输入字符串开头或结尾的空子字符串，也不匹配前面的分隔符匹配结尾的空子字符串。如果分隔符是空字符串,字符串分割成单个代码单元元素；结果数组的长度等于字符串的长度，每个子字符串包含一个代码单元。
>
> 如果这个对象(或皈依)空字符串,结果取决于分离器可以匹配空字符串。如果可以，则结果数组不包含任何元素。否则,结果数组包含一个元素,它是空字符串。
>
> 如果分隔符未定义，则结果数组只包含一个字符串，即this值(转换为字符串)。如果没有定义限制,那么输出数组截断,包含不超过限制元素。
>
> 注 2 
>
> 分裂函数是故意通用的;它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

##### 21.1.3.19.1 RS: SplitMatch ( S, q, R ) <div id="sec-splitmatch"></div>

SplitMatch抽象操作接受三个参数，一个字符串S、一个整数q和一个字符串R，执行以下步骤以返回匹配的false或end索引:

1. 断言：Type(R) 是 String.
2. 令 r 为R中代码单位的数量。
3. 令 s 为S中代码单位的数量。
4. 若 q + r > s，返回 false.
5. 若在0(含)和r(不含)之间存在一个整数i，使得S中q + i下标处的码元与r中i下标处的码元不同，返回 false.
6. 返回 q + r.

#### 21.1.3.20 String.prototype.startsWith ( searchString [ , position ] ) <div id="sec-string.prototype.startswith"></div>

将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 isRegExp 为 ? IsRegExp(searchString).
4. 若 isRegExp 是 true, 抛出 TypeError 异常.
5. 令 searchStr 为 ? ToString(searchString).
6. 令 pos 为 ? ToInteger(position).
7. 断言：若 position 是 undefined，那么  pos 是 0.
8. 令 len 为S的长度.
9. 令 start 为 min(max(pos, 0), len).
10. 令 searchLength 为searchStr的长度。
11. 若 searchLength + start is大于 len，返回 false.
12. 若S从长度searchLength开始的码元序列与searchStr的全码元序列相同，返回 true.
13. 除此之外，返回 false.

> 注 1
>
> 如果转换为字符串的searchString的代码单元序列与此对象(转换为字符串)从索引位置开始的相应代码单元相同，则此方法返回true。否则返回false。
>
> 注 2 
>
> 如果指定的第一个参数是RegExp，则抛出异常，以便允许将来的版本定义允许此类参数值的扩展。
>
> 注 3 
>
> 带有函数的startsWith是故意通用的；它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用

#### 21.1.3.21 String.prototype.substring ( start, end ) <div id="sec-string.prototype.substring"></div>

substring方法接受两个参数start和end，并返回将该对象转换为字符串的结果的子字符串，从索引开始并运行到(但不包括)字符串的索引结束(如果end未定义，则通过字符串结束)。结果是一个字符串值，而不是一个字符串对象。

如果参数是NaN或负数，则用0代替；如果其中一个参数大于字符串的长度，则用字符串的长度替换它。

如果开始大于结束，则交换它们。

将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 令 len 为S的长度.
4. 令 intStart 为 ? ToInteger(start).
5. 若 end 是 undefined, 令 intEnd 为 len; 否则，令 intEnd 为 ? ToInteger(end).
6. 令 finalStart 为 min(max(intStart, 0), len).
7. 令 finalEnd 为 min(max(intEnd, 0), len).
8. 令 from 为 min(finalStart, finalEnd).
9. 令 to 为 max(finalStart, finalEnd).
10. 返回长度为to - from的字符串值，包含来自S的代码单位，即从下到- 1的代码单位，按升序排列。

> 注
>
> 子字符串函数是通用的；它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

#### 21.1.3.22 String.prototype.toLocaleLowerCase ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-string.prototype.tolocalelowercase"></div>

包含ECMA-402国际化API的ECMAScript实现必须实现ECMA-402规范中指定的toLocaleLowerCase方法。
如果ECMAScript实现不包含ECMA-402 API，则使用以下toLocaleLowerCase方法规范。

该函数将字符串值解释为UTF-16编码的编码点序列，如6.1.4所述。

这个函数的工作方式与toLowerCase完全相同，不同之处在于它的结果是为主机环境的当前语言环境生成正确的结果，而不是独立于语言环境的结果。只有在少数情况下(比如土耳其语)，这种语言的规则与常规的Unicode大小写映射冲突时才会有所不同。

该方法的可选参数的含义在ECMA-402规范中定义；不包括ECMA-402支持的实现不能将这些参数位置用于其他任何地方。

> 注：toLocaleLowerCase函数是故意通用的；它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

#### 21.1.3.23 String.prototype.toLocaleUpperCase ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-string.prototype.tolocaleuppercase"></div>

包含ECMA-402国际化API的ECMAScript实现必须实现ECMA-402规范中指定的toLocaleUpperCase方法。
如果ECMAScript实现不包含ECMA-402 API，则使用以下toLocaleUpperCase方法规范。

该函数将字符串值解释为UTF-16编码的编码点序列，如6.1.4所述。

这个函数的工作方式与toUpperCase完全相同，只是它的结果是为主机环境的当前语言环境生成正确的结果，而不是独立于语言环境的结果。只有在少数情况下(比如土耳其语)，这种语言的规则与常规的Unicode大小写映射冲突时才会有所不同。

该方法的可选参数的含义在ECMA-402规范中定义；不包括ECMA-402支持的实现不能将这些参数位置用于其他任何地方。

> 注：toLocaleUpperCase函数是故意通用的；它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用

#### 21.1.3.24 String.prototype.toLowerCase ( ) <div id="sec-string.prototype.tolowercase"></div>

该函数将字符串值解释为UTF-16编码的编码点序列，如6.1.4所述。将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value)
2. 令 S 为 ? ToString(O).
3. 令 cpList 为按顺序包含S的6.1.4中定义的代码点的列表，从S的第一个元素开始。
4. 令 cuList 为根据Unicode默认大小写转换算法，其中元素是toLowercase(cpList)的结果。
5. 令 L 为代码单元为cuList代码点的utf16编码的字符串值。
6. 返回 L.

结果必须根据Unicode字符数据库中的不区分区域设置的大小写映射(这不仅显式地包括UnicodeData.txt文件，还包括它所附带的SpecialCasings.txt文件中的所有不区分区域设置的映射)。

> 注 1
>
> 某些代码点的案例映射可能产生多个代码点。在这种情况下，结果字符串可能与源字符串的长度不同。因为toUpperCase和toLowerCase都具有上下文敏感的行为，所以函数不是对称的。换句话说，s.toUpperCase(). tolowercase()不一定等于s.toLowerCase()。
>
> 注 2
>
> toLowerCase函数是故意通用的；它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

#### 21.1.3.25 String.prototype.toString ( ) <div id="sec-string.prototype.tostring"></div>

当toString方法被调用时， 将采取以下步骤：

1. 返回 ? thisStringValue(this value).

> 注：对于String对象，toString方法恰好返回与valueOf方法相同的内容。

#### 21.1.3.26 String.prototype.toUpperCase ( ) <div id="sec-string.prototype.touppercase"></div>

该函数将字符串值解释为UTF-16编码的编码点序列，如6.1.4所述。

此函数的行为与String.prototype完全相同。除了字符串是使用Unicode默认大小写转换的toUppercase算法映射的。

> 注：toUpperCase函数是故意通用的；它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

#### 21.1.3.27 String.prototype.trim ( ) <div id="sec-string.prototype.trim"></div>

该函数将字符串值解释为UTF-16编码的编码点序列，如6.1.4所述。

将采取以下步骤：

1. 令 S 为 this 值.
2. 返回 ? TrimString(S, "start+end")

> 注：修剪函数是故意通用的；它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

##### 21.1.3.27.1 RS: TrimString ( string, where ) <div id="sec-trimstring"></div>

调用带有参数字符串和where的抽象操作TrimString，并将字符串值字符串解释为UTF-16编码的编码点序列，如6.1.4中所述。它执行以下步骤:

1. 令 str 为 ? RequireObjectCoercible(string).
2. 令 S 为 ? ToString(str).
3. 若 where 是 "start", 令 T删除前导空白的S的副本的字符串值。
4. 否则若 where 是 "end", 令 T 为删除尾随空格的S的副本的字符串值。
5. 否则，
   1. 断言：where 是 "start+end".
   2. 令 T 为字符串值，它是S的副本，去掉了开头和结尾的空白。
6. 返回 T.

空白的定义是空白和行结束符的结合。在确定Unicode编码点是否属于Unicode通用类别“Space_Separator”(“Zs”)时，代码单元序列被解释为6.1.4中指定的UTF-16编码的编码点序列。

#### 21.1.3.28 String.prototype.trimEnd ( ) <div id="sec-string.prototype.trimend"></div>

该函数将字符串值解释为UTF-16编码的编码点序列，如6.1.4所述。

将采取以下步骤：

1. 令 S 为 this value.
2. 返回 ? TrimString(S, "end").

> 注：trimEnd函数是故意通用的;它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

#### 21.1.3.29 String.prototype.trimStart ( ) <div id="sec-string.prototype.trimstart"></div>

该函数将字符串值解释为UTF-16编码的编码点序列，如6.1.4所述。

将采取以下步骤：

1. 令 S 为 this value.
2. 返回 ? TrimString(S, "start").

> 注：trimStart函数是故意通用的；它不要求它的这个值是一个字符串对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

#### 21.1.3.30 String.prototype.valueOf ( ) <div id="sec-string.prototype.valueof"></div>

当valueOf方法被调用时，将采取以下步骤：

1. 返回 ? thisStringValue(this value).

#### 21.1.3.31 String.prototype [ @@iterator ] ( ) <div id="sec-string.prototype-@@iterator"></div>

当@@iterator方法被调用时，它返回一个迭代器对象(25.1.1.2)，迭代一个字符串值的代码点，将每个代码点作为一个字符串值返回。将采取以下步骤：

1. 令 O 为 ? RequireObjectCoercible(this value).
2. 令 S 为 ? ToString(O).
3. 返回 CreateStringIterator(S).

这个函数的name属性的值是“[Symbol.iterator]”。

### 21.1.4 String 实例属性 <div id="sec-properties-of-string-instances"></div>

字符串实例是字符串外来对象，并具有为此类对象指定的内部方法。字符串实例从字符串原型对象继承属性。字符串实例也有一个[[StringData]]内部槽。

字符串实例有一个“length”属性和一组具有整数索引名称的可枚举属性。

#### 21.1.4.1 length <div id="sec-properties-of-string-instances-length"></div>

此字符串对象表示的字符串值中的元素数。

初始化字符串对象后，此属性将保持不变。它有属性{ [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 21.1.5 String 迭代器对象 <div id="sec-string-iterator-objects"></div>

字符串迭代器是一个对象，它表示对某个特定字符串实例对象的特定迭代。字符串迭代器对象没有指定的构造函数。相反，String迭代器对象是通过调用String实例对象的某些方法来创建的

#### 21.1.5.1 CreateStringIterator ( string ) <div id="sec-createstringiterator"></div>

字符串对象的几个方法返回迭代器对象。带有参数字符串的抽象操作CreateStringIterator用于创建这样的迭代器对象。它执行以下步骤:

1. 断言：Type(string) 是 String.
2. 令 iterator 为 ObjectCreate(%StringIteratorPrototype%, « [[IteratedString]], [[StringIteratorNextIndex]] »).
3. 设置 iterator.[[IteratedString]] 为 string.
4. 设置 iterator.[[StringIteratorNextIndex]] 为 0.
5. 返回 iterator.

#### 21.1.5.2 The %StringIteratorPrototype% Object <div id="sec-%stringiteratorprototype%-object"></div>

% StringIteratorPrototype %对象:

- 具有所有字符串迭代器对象继承的属性。
- 是一个普通的对象。
- 有一个[[Prototype]]内部插槽，其值是内部对象％IteratorPrototype％。
- 具有以下属性：

##### 21.1.5.2.1 %StringIteratorPrototype%.next ( ) <div id="sec-%stringiteratorprototype%.next"></div>

1. 令 O 为 this 值。
2. 若 Type(O) 不是 Object, 抛出 TypeError 异常.
3. 若 O 没有字符串迭代器实例（21.1.5.3）的所有内部插槽，抛出 TypeError 异常.
4. 令 s 为 O.[[IteratedString]].
5. 若 s 是 undefined，返回 CreateIterResultObject(undefined, true).
6. 令 position 为 O.[[StringIteratorNextIndex]].
7. 令 len 为S的长度.
8. 若 position ≥ len，那么 
   1. 设置 O.[[IteratedString]] 为 undefined.
   2. 返回 CreateIterResultObject(undefined, true).
9. 令 first 为s中位于索引位置的代码单元的数值。
10. 若 first < 0xD800 或 first > 0xDBFF 或 position + 1 = len, 令 resultString 为首先由单个代码单元组成的String值。
11.  否则，
    1. 令 second 为String内索引位置+1处的代码单元的数值。
    2. 若 second < 0xDC00 或 second > 0xDFFF, 令 resultString 为首先由单个代码单元组成的String值。
    3.  否则， 令 resultString 为 首先是代码单元的字符串连接，然后是代码单元的字符串连接。
12. 令 resultSize 为 resultString 中的代码单元数。
13. 设置 O.[[StringIteratorNextIndex]] 为 position + resultSize.
14. 返回 CreateIterResultObject(resultString, false).

##### 21.1.5.2.2 %StringIteratorPrototype% [ @@toStringTag ] <div id="sec-%stringiteratorprototype%-@@tostringtag"></div>

@@toStringTag属性的初始值为String值“ String Iterator”。

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 21.1.5.3 String 迭代器实例的属性 <div id="sec-properties-of-string-iterator-instances"></div>

字符串迭代器实例是从％StringIteratorPrototype％内部对象继承属性的普通对象。最初使用表52中列出的内部插槽创建String Iterator实例。

表52：字符串迭代器实例的内部插槽

| 内部插槽                    | 描述                                       |
| --------------------------- | ------------------------------------------ |
| [[IteratedString]]          | 要迭代其代码单元的String值。               |
| [[StringIteratorNextIndex]] | 此迭代要检查的下一个字符串索引的整数索引。 |

## 21.2 RegExp（正则表达式）对象 <div id="sec-regexp-regular-expression-objects"></div>

RegExp对象包含一个正则表达式和关联的标志。

> 注：正则表达式的形式和功能是根据Perl 5编程语言中的正则表达式工具建模的。

### 21.2.1 模式 <div id="sec-patterns"></div>

RegExp构造函数将以下语法应用于输入模式字符串。如果语法不能将字符串解释为模式的展开，则会发生错误。

**语法**

```
Pattern[U, N] ::
	Disjunction[?U, ?N]
Disjunction[U, N] ::
    Alternative[?U, ?N]
    Alternative[?U, ?N] | Disjunction[?U, ?N]
Alternative[U, N] ::
    [empty]
    Alternative[?U, ?N] Term[?U, ?N]
Term[U, N] ::
    Assertion[?U, ?N]
    Atom[?U, ?N]
    Atom[?U, ?N] Quantifier
Assertion[U, N] ::
    ^
    $
    \ b
    \ B
    ( ? = Disjunction[?U, ?N] )
    ( ? ! Disjunction[?U, ?N] )
    ( ? <= Disjunction[?U, ?N] )
    ( ? <! Disjunction[?U, ?N] )
Quantifier ::
    QuantifierPrefix
    QuantifierPrefix ?
QuantifierPrefix ::
    *
    +
    ?
    { DecimalDigits }
    { DecimalDigits , }
    { DecimalDigits , DecimalDigits }
Atom[U, N] ::
    PatternCharacter
    .
    \ AtomEscape[?U, ?N]
    CharacterClass[?U]
    ( GroupSpecifier[?U] Disjunction[?U, ?N] )
    ( ? : Disjunction[?U, ?N] )
SyntaxCharacter :: one of
	^ $ \ . * + ? ( ) [ ] { } |
PatternCharacter ::
	SourceCharacter but not SyntaxCharacter
AtomEscape[U, N] ::
    DecimalEscape
    CharacterClassEscape[?U]
    CharacterEscape[?U]
    [+N] k GroupName[?U]
CharacterEscape[U] ::
    ControlEscape
    c ControlLetter
    0 [lookahead ∉ DecimalDigit]
    HexEscapeSequence
    RegExpUnicodeEscapeSequence[?U]
    IdentityEscape[?U]
ControlEscape :: one of
	f n r t v
ControlLetter :: one of
    a b c d e f g h i j k l m n o p q r s t u v w x y z A B C D E F G H I J K L M N O
    P Q R S T U V W X Y Z
GroupSpecifier[U] ::
    [empty]
    ? GroupName[?U]
GroupName[U] ::
	< RegExpIdentifierName[?U] >
RegExpIdentifierName[U] ::
    RegExpIdentifierStart[?U]
    RegExpIdentifierName[?U] RegExpIdentifierPart[?U]
RegExpIdentifierStart[U] ::
    UnicodeIDStart
    $
    _
    \ RegExpUnicodeEscapeSequence[?U]
RegExpIdentifierPart[U] ::
    UnicodeIDContinue
    $
    \ RegExpUnicodeEscapeSequence[?U]
    <ZWNJ>
    <ZWJ>
RegExpUnicodeEscapeSequence[U] ::
    [+U] u LeadSurrogate \u TrailSurrogate
    [+U] u LeadSurrogate
    [+U] u TrailSurrogate
    [+U] u NonSurrogate
    [~U] u Hex4Digits
    [+U] u{ CodePoint }
```

每个与其关联的 u LeadSurrogate的选择不明确的 \u TrailSurrogate都应与最接近的可能的 u LeadSurrogate关联，否则，它们将没有对应的 \u TrailSurrogate。

```
LeadSurrogate ::
	Hex4Digits but only if the SV of Hex4Digits is in the inclusive range 0xD800 to 0xDBFF
TrailSurrogate ::
	Hex4Digits but only if the SV of Hex4Digits is in the inclusive range 0xDC00 to 0xDFFF
NonSurrogate ::
	Hex4Digits but only if the SV of Hex4Digits is not in the inclusive range 0xD800 to 0xDFFF
IdentityEscape[U] ::
    [+U] SyntaxCharacter
    [+U] /
    [~U] SourceCharacter but not UnicodeIDContinue
DecimalEscape ::
	NonZeroDigit DecimalDigitsopt [lookahead ∉ DecimalDigit]
CharacterClassEscape[U] ::
    d
    D
    s
    S
    w
    W
    [+U] p{ UnicodePropertyValueExpression }
    [+U] P{ UnicodePropertyValueExpression }
UnicodePropertyValueExpression ::
	UnicodePropertyName = UnicodePropertyValue
	LoneUnicodePropertyNameOrValue
UnicodePropertyName ::
	UnicodePropertyNameCharacters
UnicodePropertyNameCharacters ::
	UnicodePropertyNameCharacter UnicodePropertyNameCharactersopt
UnicodePropertyValue ::
	UnicodePropertyValueCharacters
LoneUnicodePropertyNameOrValue ::
	UnicodePropertyValueCharacters
UnicodePropertyValueCharacters ::
	UnicodePropertyValueCharacter UnicodePropertyValueCharactersopt
UnicodePropertyValueCharacter ::
    UnicodePropertyNameCharacter
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
UnicodePropertyNameCharacter ::
    ControlLetter
    _
CharacterClass[U] ::
    [ [lookahead ∉ { ^ }] ClassRanges[?U] ]
    [ ^ ClassRanges[?U] ]
ClassRanges[U] ::
    [empty]
    NonemptyClassRanges[?U]
NonemptyClassRanges[U] ::
    ClassAtom[?U]
    ClassAtom[?U] NonemptyClassRangesNoDash[?U]
    ClassAtom[?U] - ClassAtom[?U] ClassRanges[?U]
NonemptyClassRangesNoDash[U] ::
    ClassAtom[?U]
    ClassAtomNoDash[?U] NonemptyClassRangesNoDash[?U]
    ClassAtomNoDash[?U] - ClassAtom[?U] ClassRanges[?U]
ClassAtom[U] ::
    -
    ClassAtomNoDash[?U]
ClassAtomNoDash[U] ::
	SourceCharacter but not one of \ or ] or -
	\ ClassEscape[?U]
ClassEscape[U] ::
    b
    [+U] -
    CharacterClassEscape[?U]
    CharacterEscape[?U]
```

#### 21.2.1.1 SS: Early Errors <div id="sec-patterns-static-semantics-early-errors"></div>

```
Pattern :: Disjunction
```

- 如果 NcapturingParens≥2^32-1，则为语法错误。
- 如果Pattern包含多个GroupSpecifier，且其封闭的RegExpIdentifierNames具有相同的StringValue，则t为语法错误。

```
QuantifierPrefix :: { DecimalDigits , DecimalDigits }
```

- 如果第一个DecimalDigits的MV大于第二个DecimalDigits的MV，则是语法错误。

```
AtomEscape :: k GroupName
```

- 如果封闭的模式不包含带有封闭的RegExpIdentifierName的GroupSpecifier，则其语法值为String等于此生产的GroupName的RegExpIdentifierName的StringValue，这是语法错误。

```
AtomEscape :: DecimalEscape
```

- It is a Syntax Error if the CapturingGroupNumber of DecimalEscape is larger than NcapturingParens (21.2.2.1).

```
NonemptyClassRanges :: ClassAtom - ClassAtom ClassRanges
```

- 如果第一个ClassAtom的IsCharacterClass为true或第二个ClassAtom的IsCharacterClass为true，则为语法错误。
- 如果第一个ClassAtom的IsCharacterClass为false，第二个ClassAtom的IsCharacterClass为false，并且第一个ClassAtom的CharacterValue大于第二个ClassAtom的CharacterValue，则是语法错误。

```
NonemptyClassRangesNoDash :: ClassAtomNoDash - ClassAtom ClassRanges
```

- 如果ClassAtomNoDash的IsCharacterClass为true或ClassAtom的IsCharacterClass为true，则为语法错误。
- 如果ClassAtomNoDash的IsCharacterClass为false，ClassAtom的IsCharacterClass为false，并且ClassAtomNoDash的CharacterValue大于ClassAtom的CharacterValue，则是语法错误。

```
RegExpIdentifierStart :: \ RegExpUnicodeEscapeSequence
```

- 如果SV（RegExpUnicodeEscapeSequence）不为“ $”或“ _”或与UnicodeIDStart词汇语法生成匹配的代码点的UTF16Encoding，则为语法错误。

```
RegExpIdentifierPart :: \ RegExpUnicodeEscapeSequence
```

- 如果SV（RegExpUnicodeEscapeSequence）都不是“ $”或“ _”，或者不是或的UTF16Encoding，或者不是UnicodeIDContinue词汇语法产生的Unicode代码点的UTF16Encoding，则是语法错误。

```
UnicodePropertyValueExpression :: UnicodePropertyName = UnicodePropertyValue
```

- 如果UnicodePropertyName的SourceText Unicode代码点列表与表54的“属性名称和别名”列中列出的Unicode属性名或属性别名的Unicode代码点列表不同，则这是语法错误。
- 如果UnicodePropertyValue的Unicode代码点列表与UnicodePropertyValue的值或值别名或UnicodePropertyName的SourceText给出的Unicode属性值或值别名的Unicode代码点列表不同，则会出现语法错误。表56或表57对应表的“属性值和别名”列。

```
UnicodePropertyValueExpression :: LoneUnicodePropertyNameOrValue
```

- 如果作为LoneUnicodePropertyNameOrValue的SourceText的Unicode代码点列表与作为表56的“属性值和别名”列中列出的Unicode常规类别或常规类别别名的Unicode代码点列表不同，则会出现语法错误。 ，也不会在表55的“属性名称和别名”列中列出二进制属性或二进制属性别名。

#### 21.2.1.2 SS: CapturingGroupNumber <div id="sec-patterns-static-semantics-capturing-group-number"></div>

```
DecimalEscape :: NonZeroDigit
```

1. 返回NonZeroDigit的MV。

```
DecimalEscape :: NonZeroDigit DecimalDigits
```

1. 令 n 为DecimalDigits中的代码点数。
2. 返回 (NonZeroDigit的MV × 10n)加上DecimalDigits的MV。

11.8.3中定义了“ NonZeroDigit的MV”和“ DecimalDigits的MV”。

#### 21.2.1.3 SS: IsCharacterClass <div id="sec-patterns-static-semantics-is-character-class"></div>

```
ClassAtom :: -
ClassAtomNoDash :: SourceCharacter but not one of \ or ] or -
ClassEscape :: b
ClassEscape :: -
ClassEscape :: CharacterEscape
```

1. 返回 false.

```
ClassEscape :: CharacterClassEscape
```

1. 返回 true.

#### 21.2.1.4 SS: CharacterValue <div id="sec-patterns-static-semantics-character-value"></div>

```
ClassAtom :: -
```

1. 返回U + 002D的代码点值（减号）。

```
ClassAtomNoDash :: SourceCharacter but not one of \ or ] or -
```

1. 令 ch 为SourceCharacter匹配的代码点。
2. 返回ch的代码点值。

```
ClassEscape :: b
```

1. 返回U + 0008（退格）的代码点值。

```
ClassEscape :: -
```

1. 返回U + 002D的代码点值(减号).

```
CharacterEscape :: ControlEscape
```

1. 返回根据表53的代码点值。

表53：ControlEscape代码点值

| ControlEscape | 代码点值 | 代码点   | Unicode名称          | 符号  |
| ------------- | -------- | -------- | -------------------- | ----- |
| `t`           | 9        | `U+0009` | CHARACTER TABULATION | \<HT> |
| `n`           | 10       | `U+000A` | LINE FEED (LF)       | \<LF> |
| `v`           | 11       | `U+000B` | LINE TABULATION      | \<VT> |
| `f`           | 12       | `U+000C` | FORM FEED (FF)       | \<FF> |
| `r`           | 13       | `U+000D` | CARRIAGE RETURN (CR) | \<CR> |

```
CharacterEscape :: c ControlLetter
```

1. 令 ch 为ControlLetter匹配的代码点。
2. 令 i 为ch的代码点值。
3. 返回将i除以32的余数。

```
CharacterEscape :: 0 [lookahead ∉ DecimalDigit]
```

1. 返回U + 0000（NULL）的代码点值。

> 注：\0代表字符，不能跟十进制数字。

```
CharacterEscape :: HexEscapeSequence
```

1. 返回十六进制转义序列SV的代码单元的数值。

```
RegExpUnicodeEscapeSequence :: u LeadSurrogate \u TrailSurrogate
```

1. 令 lead 为LeadSurrogate的CharacterValue。
2. 令 trail 为TrailSurrogate的CharacterValue。
3. 令 cp 为 UTF16Decode(lead, trail).
4. 返回cp的代码点值。

```
RegExpUnicodeEscapeSequence :: u LeadSurrogate
```

1. 返回LeadSurrogate的CharacterValue。

```
RegExpUnicodeEscapeSequence :: u TrailSurrogate
```

1. 返回TrailSurrogate的CharacterValue。

```
RegExpUnicodeEscapeSequence :: u NonSurrogate
```

1. 返回TrailSurrogate的CharacterValue。

```
RegExpUnicodeEscapeSequence :: u Hex4Digits
```

1. 返回Hex4Digits的MV

```
RegExpUnicodeEscapeSequence :: u{ CodePoint }
```

1. 返回CodePoint的MV

```
LeadSurrogate :: Hex4Digits
TrailSurrogate :: Hex4Digits
NonSurrogate :: Hex4Digits
```

1. 返回HexDigits的MV。

```
CharacterEscape :: IdentityEscape
```

1. 令 ch 为与IdentityEscape匹配的代码点。
2. 返回ch的代码点值。

#### 21.2.1.5 SS: SourceText <div id="sec-static-semantics-sourcetext"></div>

```
UnicodePropertyNameCharacters :: UnicodePropertyNameCharacter UnicodePropertyNameCharacters
UnicodePropertyValueCharacters :: UnicodePropertyValueCharacter UnicodePropertyValueCharacters
```

1. 返回按源文本顺序列出与此生产匹配的源文本中的Unicode代码点的列表。

#### 21.2.1.6 SS: StringValue <div id="sec-regexp-identifier-names-static-semantics-stringvalue"></div>

```
RegExpIdentifierName[U] ::
RegExpIdentifierStart[?U]
RegExpIdentifierName[?U] RegExpIdentifierPart[?U]
```

1. 返回由对应于RegExpIdentifierName的代码单元序列组成的String值。在确定序列时，首先用RegExpUnicodeEscapeSequence表示的代码点替换\ RegExpUnicodeEscapeSequence的出现，然后通过UTF16Encoding每个代码点将整个RegExpIdentifierName的代码点转换为代码单元。

### 21.2.2 模式语义 <div id="sec-pattern-semantics"></div>

使用下面描述的过程将正则表达式模式转换为内部过程。鼓励实现使用比下面列出的算法更有效的算法，只要结果相同即可。内部过程用作RegExp对象的[[RegExpMatcher]]内部插槽的值。

模式是BMP模式还是Unicode模式，具体取决于其关联标志是否包含“ u”。 BMP模式与解释为由一系列16位值组成的字符串匹配，这些值是基本多语言平面范围内的Unicode代码点。 Unicode模式与解释为由使用UTF-16编码的Unicode代码点组成的字符串匹配。在描述BMP模式的行为的上下文中，“字符”表示单个16位Unicode BMP代码点。在描述Unicode模式的行为的上下文中，“字符”表示UTF-16编码的代码点（6.1.4）。在任一上下文中，“字符值”是指相应的未编码代码点的数值。

定义Pattern的语法和语义，就好像Pattern的源代码是SourceCharacter值的列表，其中每个SourceCharacter对应于一个Unicode代码点。如果BMP模式包含非BMP SourceCharacter，则整个模式使用UTF-16进行编码，并且该编码的各个代码单元用作列表的元素。

> 注
>
> 例如，将源文本中表示的模式视为单个非BMP字符U + 1D11E（音乐符号G CLEF）。解释为Unicode模式，它将是由单个代码点0x1D11E组成的单个元素（字符）列表。但是，将其解释为BMP模式后，首先对其进行UTF-16编码，以生成一个由两个单元组成的List，该List由代码单元0xD834和0xDD1E组成。
>
> 模式作为ECMAScript字符串值传递给RegExp构造函数，其中非BMP字符采用UTF-16编码。例如，以字符串值表示的单字符MUSICAL SYMBOL G CLEF模式是长度为2的字符串，其元素是代码单元0xD834和0xDD1E。因此，无需进一步转换字符串即可将其处理为由两个模式字符组成的BMP模式。但是，要将其处理为Unicode模式，必须在生成由单个模式字符组成的List（代码点U + 1D11E）时使用UTF16Decode。
>
> 一个实现可能实际上并没有执行往返于UTF-16的转换，但是此规范的语义要求模式匹配的结果就像执行了此类转换一样。

#### 21.2.2.1 表示法 <div id="sec-notation"></div>

以下说明使用以下变量：

- 输入是一个列表，该列表按顺序包含由正则表达式模式匹配的所有所有字符。每个字符都可以是一个代码单元或一个代码点，具体取决于所涉及的图案类型。输入法Input [n]表示Input的第n个字符，其中n的范围可以在0（含）和InputLength（不含）之间。
- InputLength是输入中的字符数。
- NcapturingParens是模式中左捕捉括号的总数（即Atom ::（GroupSpecifier Disjunction）解析节点的总数）。左捕获括号是与Atom ::（GroupSpecifier Disjunction）产生的（终端匹配的任何（模式字符）。
- 如果RegExp对象的[[OriginalFlags]]内部插槽包含“ s”，则DotAll为true，否则为false。
- 如果RegExp对象的[[OriginalFlags]]内部插槽包含“ i”，则IgnoreCase为true，否则为false。
- 如果RegExp对象的[[OriginalFlags]]内部插槽包含“ m”，则Multiline为true，否则为false。
- 如果RegExp对象的[[OriginalFlags]]内部插槽包含“ u”，则Unicode为true，否则为false。

此外，以下描述使用以下内部数据结构：

- CharSet是数学上的字符集，取决于Unicode标志的状态，可以是代码单位或代码点。 “所有字符”是指所有代码单位值或所有代码点值，也取决于Unicode的状态。
- 一个State是一个有序对（endIndex，捕获），其中endIndex是一个整数，捕获是NcapturingParens值的列表。状态用于表示正则表达式匹配算法中的部分匹配状态。 endIndex是一个值，加上该模式到目前为止匹配的最后一个输入字符的索引，而captures保存捕获括号的结果。捕获的第n个元素是一个List，代表通过第n组捕获括号获得的值，或者如果尚未达到第n组捕获括号，则为undefined。由于回溯，许多状态可能在匹配过程的任何时候被使用。
- MatchResult要么是状态，要么是表示匹配失败的特殊令牌失败。
- Continuation过程是一个内部闭包（即一个内部过程，其中一些参数已绑定到值），该闭包采用一个State参数并返回MatchResult结果。如果内部闭包引用了在创建闭包的函数中绑定的变量，则闭包将使用这些变量在创建闭包时具有的值。 Continuation尝试将模式的其余部分（由闭包的已绑定参数指定）与Input匹配，从其State参数给出的中间状态开始。如果比赛成功，Continuation会返回到达的最终状态；如果匹配失败，则Continuation返回失败。
- Matcher过程是一个内部闭包，它使用两个参数（一个State和一个Continuation）并返回MatchResult结果。 Matcher尝试从其State参数给定的中间状态开始，将模式的中间子模式（由闭包的已绑定参数指定）与Input进行匹配。 Continuation参数应该是与模式的其余部分匹配的闭包。在匹配模式的子模式以获得新的状态之后，匹配器随后在该新状态上调用Continuation以测试模式的其余部分是否也可以匹配。如果可以，则匹配器返回由Continuation返回的状态。如果不是，则匹配器可能会在其选择点尝试不同的选择，重复调用Continuation，直到成功或所有可能性用尽。
- AssertionTester过程是一个内部闭包，它接受State参数并返回布尔结果。断言测试器针对Input中的当前位置测试特定条件（由闭包的已绑定参数指定），如果条件匹配则返回true，否则返回false。

#### 21.2.2.2 模式 <div id="sec-pattern"></div>

Pattern :: Disjunction 的评估如下：

1. 用+1作为方向参数计算析取，得到一个匹配器m。
2. 返回一个内部闭包，它接受两个参数，一个String str和一个整数索引，并执行以下步骤：
     1. 断言：index ≤ str的长度。
     2. 若 Unicode 是 true, 令序列组成的输入是一个列表的代码点str解读为utf - 16编码(6.1.4)Unicode字符串。否则,令输入是一个列表的代码单元组成的序列str。输入的元素将为整个算法用于21.2.2。输入的每个元素都被认为是一个字符。
     3. 令InputLength输入中包含的字符数。这个变量将为整个算法用于21.2.2。
     4. 令 listIndex 为从str的元素索引中得到的字符的输入的索引。
     5. 令 c 为一个延续，它总是以成功的MatchResult返回它的状态参数。
     6. 令 cap 为NcapturingParens未定义值的列表，通过NcapturingParens索引1。
     7. 令 x 为 the State (listIndex, cap).
     8. 调用 m(x, c) 并且返回它的结果。

> 注：模式计算(“编译”)一个内部过程值。然后，RegExpBuiltinExec可以将此过程应用于字符串和字符串中的偏移量，以确定模式是否将从字符串中的偏移量开始匹配，如果匹配，则捕获括号的值将是什么。21.2.2中的算法是这样设计的:编译一个模式可能会抛出一个SyntaxError异常；另一方面，一旦成功地编译了模式，应用产生的内部过程来查找字符串中的匹配就不能抛出异常(除了主机定义的任何异常，如内存不足)

#### 21.2.2.3 析取 <div id="sec-disjunction"></div>

与参数的方向。

产生式 Disjunction :: Alternative 如下解释执行：

1. 用参数方向对备选方案进行解释执行，以获得一个匹配器m。
2. 返回 m.

产生式 Disjunction :: Alternative | Disjunction 如下解释执行：

1. Evaluate Alternative with argument direction to obtain a Matcher m1.
2. Evaluate Disjunction with argument direction to obtain a Matcher m2.
3. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
1. 调用 m1(x, c) and 令 r 为 its result.
2. 若 r 不是 failure，返回 r.
3. 调用 m2(x, c) and return its result.

> 注
>
> The | regular expression operator separates two alternatives. The pattern first tries to match the left Alternative (followed by the sequel of the regular expression); if it fails, it tries to match the right Disjunction (followed by the sequel of the regular expression). If the left Alternative, the right Disjunction, and the sequel all have choice points, all choices in the sequel are tried before moving on to the next choice in the left Alternative. If choices in the left Alternative are exhausted, the right Disjunction is tried instead of the left Alternative. Any capturing parentheses inside a portion of the pattern skipped by | produce undefined values instead of Strings. Thus, for example,
>
> /a|ab/.exec("abc")
>
> returns the result "a" and not "ab". Moreover,
>
> /((a)|(ab))((c)|(bc))/.exec("abc")
>
> returns the array
>
> ["abc", "a", "a", undefined, "bc", undefined, "bc"]
>
> and not
>
> ["abc", "ab", undefined, "ab", "c", "c", undefined]
>
> The order in which the two alternatives are tried is independent of the value of direction.

#### 21.2.2.4 选择项 <div id="sec-alternative"></div>

With parameter direction.

产生式 Alternative :: [empty] 如下解释执行：

1. 返回 a Matcher that takes two arguments, a State x and a Continuation c, and returns the result of calling c(x).

产生式 Alternative :: Alternative Term 如下解释执行：

1. Evaluate Alternative with argument direction to obtain a Matcher m1.
2. Evaluate Term with argument direction to obtain a Matcher m2.
3. 若 direction 是 equal to +1，那么 
1. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
  1. 令 d 为 a Continuation that takes a State argument y and returns the result of calling m2(y, c).
  2. 调用 m1(x, d) and return its result.
4.  否则，
  1. 断言：direction is equal to -1.
  2. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
        1. 令 d 为 a Continuation that takes a State argument y and returns the result of calling m1(y, c).
            2. 调用 m2(x, d) and return its result.

> 注 Consecutive Terms try to simultaneously match consecutive portions of Input. When direction is equal to +1, if the left Alternative, the right Term, and the sequel of the regular expression all have choice points, all choices in the sequel are tried before moving on to the next choice in the right Term, and all choices in the right Term are tried before moving on to the next choice in the left Alternative. When direction is equal to -1, the evaluation order of Alternative and Term are reversed.

#### 21.2.2.5 匹配项 <div id="sec-term"></div>

With parameter direction.

产生式 Term :: Assertion 如下解释执行：

1. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
  1. Evaluate Assertion to obtain an AssertionTester t.
  2. 调用 t(x) and 令 r 为 the resulting Boolean value.
  3. 若 r 是 false，返回 failure.
  4. 调用 c(x) and return its result

> 注 The AssertionTester is independent of direction.

产生式 Term :: Atom 如下解释执行：

1. 返回 the Matcher that is the result of evaluating Atom with argument direction.

产生式 Term :: Atom Quantifier 如下解释执行：

1. Evaluate Atom with argument direction to obtain a Matcher m.
2. Evaluate Quantifier to obtain the three results: an integer min, an integer (or ∞) max, and Boolean greedy.
3. 断言：If max is finite，那么  max is not less than min.
4. 令 parenIndex 为 the number of left-capturing parentheses in the entire regular expression that occur to the left of this Term. This is the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes prior to or enclosing this Term.
5. 令 parenCount 为 the number of left-capturing parentheses in Atom. This is the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes enclosed by Atom.
6. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
1. 调用 RepeatMatcher(m, min, max, greedy, x, c, parenIndex, parenCount) and return its result.

##### 21.2.2.5.1 RS: RepeatMatcher ( m, min, max, greedy, x, c, parenIndex, parenCount ) <div id="sec-runtime-semantics-repeatmatcher-abstract-operation"></div>

The abstract operation RepeatMatcher takes eight parameters, a Matcher m, an integer min, an integer (or ∞) max, a Boolean greedy, a State x, a Continuation c, an integer parenIndex, and an integer parenCount, and performs the following steps:

1. 若 max 是 zero，返回 c(x).
2. 令 d 为 an internal Continuation closure that takes one State argument y and performs the following steps when evaluated:
1. 若 min is zero and y's endIndex 是 equal to x's endIndex，返回 failure.
2. 若 min 是 zero, 令 min2 为 zero; otherwise 令 min2 为 min - 1.
3. 若 max 是 ∞, 令 max2 为 ∞; otherwise 令 max2 为 max - 1.
4. 调用 RepeatMatcher(m, min2, max2, greedy, y, c, parenIndex, parenCount) and return its result.
3. 令 cap 为 a copy of x's captures List.
4. For each integer k that satisfies parenIndex < k and k ≤ parenIndex + parenCount, set cap[k] to undefined.
5. 令 e 为 x's endIndex.
6. 令 xr 为 the State (e, cap).
7. 若 min 不是 zero，返回 m(xr, d).
8. 若 greedy 是 false，那么 
  1. 调用 c(x) and 令 z 为 its result.
  2. 若 z 不是 failure，返回 z.
  3. 调用 m(xr, d) and return its result.
9. 调用 m(xr, d) and 令 z 为 its result.
10. 若 z is not failure，返回 z.
11. 调用 c(x) and return its result.

> 注 1 
>
> An Atom followed by a Quantifier is repeated the number of times specified by the Quantifier. A Quantifier can be nongreedy, in which case the Atom pattern is repeated as few times as possible while still matching the sequel, or it can be greedy, in which case the Atom pattern is repeated as many times as possible while still matching the sequel. The Atom pattern is repeated rather than the input character sequence that it matches, so different repetitions of the Atom can match different input substrings.
>
> 注 2
>
> If the Atom and the sequel of the regular expression all have choice points, the Atom is first matched as many (or as few, if non-greedy) times as possible. All choices in the sequel are tried before moving on to the next choice in the last repetition of Atom. All choices in the last (nth) repetition of Atom are tried before moving on to the next choice in the next-to-last (n - 1)st repetition of Atom; at which point it may turn out that more or fewer repetitions of Atom are now possible; these are exhausted (again, starting with either as few or as many as possible) before moving on to the next choice in the (n - 1)st repetition of Atom and so on.
>
> Compare
>
> /a[a-z]{2,4}/.exec("abcdefghi")
>
> which returns "abcde" with
>
> /a[a-z]{2,4}?/.exec("abcdefghi")
>
> which returns "abc".
>
> Consider also
>
> /(aa|aabaac|ba|b|c)*/.exec("aabaac")
>
> which, by the choice point ordering above, returns the array
>
> ["aaba", "ba"]
>
> and not any of:
>
> ["aabaac", "aabaac"]
> ["aabaac", "c"]
>
> The above ordering of choice points can be used to write a regular expression that calculates the greatest common divisor of two numbers (represented in unary notation). The following example calculates the gcd of 10 and 15: 
>
> "aaaaaaaaaa,aaaaaaaaaaaaaaa".replace(/^(a+)\1*,\1+$/, "$1")
>
> which returns the gcd in unary notation "aaaaa".
>
> 注 3
>
> Step 4 of the RepeatMatcher clears Atom's captures each time Atom is repeated. We can see its behaviour in the regular expression
>
> /(z)((a+)?(b+)?(c))*/.exec("zaacbbbcac")
>
> which returns the array
>
> ["zaacbbbcac", "z", "ac", "a", undefined, "c"]
>
> and not
>
> ["zaacbbbcac", "z", "ac", "a", "bbb", "c"]
>
> because each iteration of the outermost * clears all captured Strings contained in the quantified Atom, which in this case includes capture Strings numbered 2, 3, 4, and 5.
>
> 注 4
>
> Step 1 of the RepeatMatcher's d closure states that, once the minimum number of repetitions has been satisfied, any more expansions of Atom that match the empty character sequence are not considered for further repetitions. This prevents the regular expression engine from falling into an infinite loop on patterns such as:
>
> /(a*)*/.exec("b")
>
> or the slightly more complicated:
>
> /(a*)b\1+/.exec("baaaac")
>
> which returns the array
>
> ["b", ""]

#### 21.2.2.6 断言 <div id="sec-assertion"></div>

产生式 Assertion :: ^ 如下解释执行：

1. 返回 an internal AssertionTester closure that takes a State argument x and performs the following steps when evaluated:
1. 令 e 为 x's endIndex.
2. 若 e 是 zero，返回 true.
3. 若 Multiline 是 false，返回 false.
4. 若 the character Input[e - 1] 是 one of LineTerminator，返回 true.
5. 返回 false.

> 注 Even when the y flag is used with a pattern, ^ always matches only at the beginning of Input, or (if Multiline is true) at the beginning of a line.

产生式 Assertion :: $ 如下解释执行：

1. 返回 an internal AssertionTester closure that takes a State argument x and performs the following steps when evaluated:
1. 令 e 为 x's endIndex.
2. 若 e 是 equal to InputLength，返回 true.
3. 若 Multiline 是 false，返回 false.
4. 若 the character Input[e] 是 one of LineTerminator，返回 true.
5. 返回 false

产生式 Assertion :: \ b 如下解释执行：

1. 返回 an internal AssertionTester closure that takes a State argument x and performs the following steps when evaluated:
  1. 令 e 为 x's endIndex.
  2. 调用 IsWordChar(e - 1) and 令 a 为 the Boolean result.
  3. 调用 IsWordChar(e) and 令 b 为 the Boolean result.
  4. 若 a is true and b 是 false，返回 true.
  5. 若 a is false and b 是 true，返回 true.
  6. 返回 false.

产生式 Assertion :: \ B 如下解释执行：

1. 返回 an internal AssertionTester closure that takes a State argument x and performs the following steps when evaluated:
1. 令 e 为 x's endIndex.
2. 调用 IsWordChar(e - 1) and 令 a 为 the Boolean result.
3. 调用 IsWordChar(e) and 令 b 为 the Boolean result.
4. 若 a is true and b 是 false，返回 false.
5. 若 a is false and b 是 true，返回 false.
6. 返回 true.

产生式 Assertion :: ( ? = Disjunction ) 如下解释执行：

1. Evaluate Disjunction with +1 as its direction argument to obtain a Matcher m.
2. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
1. 令 d 为 a Continuation that always returns its State argument as a successful MatchResult.
2. 调用 m(x, d) and 令 r 为 its result.
3. 若 r 是 failure，返回 failure.
4. 令 y 为 r's State.
5. 令 cap 为 y's captures List.
6. 令 xe 为 x's endIndex.
7. 令 z 为 the State (xe, cap).
8. 调用 c(z) and return its result.

产生式 Assertion :: ( ? ! Disjunction ) 如下解释执行：

1. Evaluate Disjunction with +1 as its direction argument to obtain a Matcher m.
2. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
1. 令 d 为 a Continuation that always returns its State argument as a successful MatchResult.
2. 调用 m(x, d) and 令 r 为 its result.
3. 若 r 不是 failure，返回 failure.
4. 调用 c(x) and return its result.

产生式 Assertion :: ( ? <= Disjunction ) 如下解释执行：

1. Evaluate Disjunction with -1 as its direction argument to obtain a Matcher m.
2. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
  1. 令 d 为 a Continuation that always returns its State argument as a successful MatchResult.
  2. 调用 m(x, d) and 令 r 为 its result.
  3. 若 r 是 failure，返回 failure.
  4. 令 y 为 r's State.
  5. 令 cap 为 y's captures List.
  6. 令 xe 为 x's endIndex.
  7. 令 z 为 the State (xe, cap).
  8. 调用 c(z) and return its result

产生式 Assertion :: ( ? <! Disjunction ) 如下解释执行：

1. Evaluate Disjunction with -1 as its direction argument to obtain a Matcher m.
2. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
1. 令 d 为 a Continuation that always returns its State argument as a successful MatchResult.
2. 调用 m(x, d) and 令 r 为 its result.
3. 若 r 不是 failure，返回 failure.
4. 调用 c(x) and return its result.

##### 21.2.2.6.1 RS: WordCharacters ( ) <div id="sec-runtime-semantics-wordcharacters-abstract-operation"></div>

The abstract operation WordCharacters performs the following steps:

1. 令 A 为 a set of characters containing the sixty-three characters:

   ```
   a b c d e f g h i j k l m n o p q r s t u v w x y z
   A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
   0 1 2 3 4 5 6 7 8 9 _
   ```

2. 令 U 为 an empty set.
3. For each character c not in set A where Canonicalize(c) is in A, add c to U.
4. 断言：Unless Unicode and IgnoreCase are both true, U is empty.
5. Add the characters in set U to set A.
6. 返回 A.

##### 21.2.2.6.2 RS: IsWordChar ( e ) <div id="sec-runtime-semantics-iswordchar-abstract-operation"></div>

The abstract operation IsWordChar takes an integer parameter e and performs the following steps:

1. 若 e is -1 or e 是 InputLength，返回 false.
2. 令 c 为 the character Input[e].
3. 令 wordChars 为 the result of ! WordCharacters().
4. 若 c 是 in wordChars，返回 true.
5. 返回 false.

#### 21.2.2.7 量词 <div id="sec-quantifier"></div>

产生式 Quantifier :: QuantifierPrefix 如下解释执行：

1. Evaluate QuantifierPrefix to obtain the two results: an integer min and an integer (or ∞) max.
2. 返回 the three results min, max, and true.

产生式 Quantifier :: QuantifierPrefix ? 如下解释执行：

1. Evaluate QuantifierPrefix to obtain the two results: an integer min and an integer (or ∞) max.
2. 返回 the three results min, max, and false.

产生式 QuantifierPrefix :: * 如下解释执行：

1. 返回 the two results 0 and ∞.

产生式 QuantifierPrefix :: + 如下解释执行：

1. 返回 the two results 1 and ∞.

产生式 QuantifierPrefix :: ? 如下解释执行：

1. 返回 the two results 0 and 1.

产生式 QuantifierPrefix :: { DecimalDigits } 如下解释执行：

1. 令 i 为 the MV of DecimalDigits (see 11.8.3).
2. 返回 the two results i and i.

产生式 QuantifierPrefix :: { DecimalDigits , } 如下解释执行：

1. 令 i 为 the MV of DecimalDigits.
2. 返回 the two results i and ∞.

产生式 QuantifierPrefix :: { DecimalDigits , DecimalDigits } 如下解释执行：

1. 令 i 为 the MV of the first DecimalDigits.
2. 令 j 为 the MV of the second DecimalDigits.
3. 返回 the two results i and j.

#### 21.2.2.8 原子 <div id="sec-atom"></div>

With parameter direction.

产生式 Atom :: PatternCharacter 如下解释执行：

1. 令 ch 为 the character matched by PatternCharacter.
2. 令 A 为 a one-element CharSet containing the character ch.
3. 调用 CharacterSetMatcher(A, false, direction) and return its Matcher result.

产生式 Atom :: . 如下解释执行：

1. 若 DotAll 是 true，那么 
1. 令 A 为 the set of all characters.
2. 除此之外, 令 A 为 the set of all characters except LineTerminator.
3. 调用 CharacterSetMatcher(A, false, direction) and return its Matcher result.

产生式 Atom :: \ AtomEscape 如下解释执行：

1. Evaluate CharacterClass to obtain a CharSet A and a Boolean invert.
2. 调用 CharacterSetMatcher(A, invert, direction) and return its Matcher result.

产生式 Atom :: ( GroupSpecifier Disjunction ) 如下解释执行：

1. Evaluate Disjunction with argument direction to obtain a Matcher m.
2. 令 parenIndex 为 the number of left-capturing parentheses in the entire regular expression that occur to the left of this Atom. This is the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes prior to or enclosing this Atom.
3. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
  1. 令 d 为 an internal Continuation closure that takes one State argument y and performs the following steps:
        1. 令 cap 为 a copy of y's captures List.
            2. 令 xe 为 x's endIndex.
                3. 令 ye 为 y's endIndex.
                    4. 若 direction 是 equal to +1，那么 
      1. 断言：xe ≤ ye.
      2. 令 s 为 a new List whose elements are the characters of Input at indices xe (inclusive) through ye (exclusive).
        5.  否则，
      1. 断言：direction is equal to -1.
      2. 断言：ye ≤ xe.
      3. 令 s 为 a new List whose elements are the characters of Input at indices ye (inclusive) through xe
          (exclusive).
        6. 设置 cap[parenIndex + 1] 为 s.
        7. 令 z 为 the State (ye, cap).
        8. 调用 c(z) and return its result.
  2. 调用 m(x, d) and return its result.

产生式 Atom :: ( ? : Disjunction ) 如下解释执行：

1. 返回 the Matcher that is the result of evaluating Disjunction with argument direction.

##### 21.2.2.8.1 RS: CharacterSetMatcher ( A, invert, direction ) <div id="sec-runtime-semantics-charactersetmatcher-abstract-operation"></div>

The abstract operation CharacterSetMatcher takes three arguments, a CharSet A, a Boolean flag invert, and an integer direction, and performs the following steps:

1. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
  1. 令 e 为 x's endIndex.
  2. 令 f 为 e + direction.
  3. 若 f < 0 or f > InputLength，返回 failure.
  4. 令 index 为 min(e, f).
  5. 令 ch 为 the character Input[index].
  6. 令 cc 为 Canonicalize(ch).
  7. 若 invert 是 false，那么 
        1. 若 there does not exist a member a of set A such that Canonicalize(a) 是 cc，返回 failure.
  8.  否则，
        1. 断言：invert is true.
            2. 若 there exists a member a of set A such that Canonicalize(a) 是 cc，返回 failure.
  9. 令 cap 为 x's captures List.
  10. 令 y 为 the State (f, cap).
  11. 调用 c(y) and return its result.

##### 21.2.2.8.2 RS: Canonicalize ( ch ) <div id="sec-runtime-semantics-canonicalize-ch"></div>

The abstract operation Canonicalize takes a character parameter ch and performs the following steps:

1. 若 IgnoreCase 是 false，返回 ch.
2. 若 Unicode 是 true，那么 
  1. 若 the file CaseFolding.txt of the Unicode Character Database provides a simple or common case folding

    mapping for ch，返回 the result of applying that mapping to ch.
  2. 返回 ch.
3.  否则，
  1. 断言：ch is a UTF-16 code unit.
  2. 令 s 为 the String value consisting of the single code unit ch.
  3. 令 u 为 the same result produced as if by performing the algorithm for String.prototype.toUpperCase using s as the this value.
  4. 断言：Type(u) is String.
  5. 若 u does not consist of a single code unit，返回 ch.
  6. 令 cu 为 u's single code unit element.
  7. 若 the numeric value of ch ≥ 128 and the numeric value of cu < 128，返回 ch.
  8. 返回 cu.

> 注 1
>
> Parentheses of the form ( Disjunction ) serve both to group the components of the Disjunction pattern together and to save the result of the match. The result can be used either in a backreference (\ followed by a nonzero decimal number), referenced in a replace String, or returned as part of an array from the regular expression matching internal procedure. To inhibit the capturing behaviour of parentheses, use the form (?: Disjunction ) instead.
>
> 注 2
>
> The form (?= Disjunction ) specifies a zero-width positive lookahead. In order for it to succeed, the pattern inside Disjunction must match at the current position, but the current position is not advanced before matching the sequel. If Disjunction can match at the current position in several ways, only the first one is tried. Unlike other regular expression operators, there is no backtracking into a (?= form (this unusual behaviour is inherited from Perl). This only matters when the Disjunction contains capturing parentheses and the sequel of the pattern contains backreferences to those captures.
>
> For example,
>
> /(?=(a+))/.exec("baaabac")
>
> matches空字符串 immediately after the first b and therefore returns the array:
>
> ["", "aaa"]
>
> To illustrate the lack of backtracking into the lookahead, consider:
>
> /(?=(a+))a*b\1/.exec("baaabac")
>
> This expression returns
>
> ["aba", "a"]
>
> and not:
>
> ["aaaba", "a"].
>
> 注 3
>
> The form (?! Disjunction ) specifies a zero-width negative lookahead. In order for it to succeed, the pattern inside Disjunction must fail to match at the current position. The current position is not advanced before matching the sequel. Disjunction can contain capturing parentheses, but backreferences to them only make sense from within Disjunction itself. Backreferences to these capturing parentheses from elsewhere in the pattern always return undefined because the negative lookahead must fail for the pattern to succeed. For example,
>
> /(.*?)a(?!(a+)b\2c)\2(.*)/.exec("baaabaac")
>
> looks for an a not immediately followed by some positive number n of a's, a b, another n a's (specified by the first \2) and a c. The second \2 is outside the negative lookahead, so it matches against undefined and therefore always succeeds. The whole expression returns the array:
>
> ["baaabaac", "ba", undefined, "abaac"]
>
> 注 4
>
> In case-insignificant matches when Unicode is true, all characters are implicitly case-folded using the simple mapping provided by the Unicode standard immediately before they are compared. The simple mapping always maps to a single code point, so it does not map, for example, "ß" (U+00DF) to "SS". It may however map a code point outside the Basic Latin range to a character within, for example, "ſ" (U+017F) to "s". Such characters are not mapped if Unicode is false. This prevents Unicode code points such as U+017F and U+212A from matching regular expressions such as /[a-z]/i, but they will match /[a-z]/ui.

##### 21.2.2.8.3 RS: UnicodeMatchProperty ( p ) <div id="sec-runtime-semantics-unicodematchproperty-p"></div>

The abstract operation UnicodeMatchProperty takes a parameter p that is a List of Unicode code points and performs the following steps:

1. 断言：p is a List of Unicode code points that is identical to a List of Unicode code points that is a Unicode property name or property alias listed in the “Property name and aliases” column of Table 54 or Table 55.
2. 令 c 为 the canonical property name of p as given in the “Canonical property name” column of the corresponding row.
3. 返回 the List of Unicode code points of c.

Implementations must support the Unicode property names and aliases listed in Table 54 and Table 55. To ensure interoperability, implementations must not support any other property names or aliases.

> 注 1 
>
> For example, Script_Extensions (property name) and scx (property alias) are valid, but script_extensions or Scx aren't.
>
> 注 2 
>
> The listed properties form a superset of what UTS18 RL1.2 requires

Table 54: Non-binary Unicode property aliases and their canonical property names

| [Property name](http://www.ecma-international.org/ecma-262/10.0/index.html#property-name) and aliases | Canonical [property name](http://www.ecma-international.org/ecma-262/10.0/index.html#property-name) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `General_Category``gc`                                       | [`General_Category`](https://unicode.org/reports/tr18/#General_Category_Property) |
| `Script``sc`                                                 | [`Script`](https://unicode.org/reports/tr24/#Script)         |
| `Script_Extensions``scx`                                     | [`Script_Extensions`](https://unicode.org/reports/tr24/#Script_Extensions) |

Table 55: Binary Unicode property aliases and their canonical property names

| [Property name](http://www.ecma-international.org/ecma-262/10.0/index.html#property-name) and aliases | Canonical [property name](http://www.ecma-international.org/ecma-262/10.0/index.html#property-name) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `ASCII`                                                      | [`ASCII`](https://unicode.org/reports/tr18/#General_Category_Property) |
| `ASCII_Hex_Digit``AHex`                                      | [`ASCII_Hex_Digit`](https://unicode.org/reports/tr44/#ASCII_Hex_Digit) |
| `Alphabetic``Alpha`                                          | [`Alphabetic`](https://unicode.org/reports/tr44/#Alphabetic) |
| `Any`                                                        | [`Any`](https://unicode.org/reports/tr18/#General_Category_Property) |
| `Assigned`                                                   | [`Assigned`](https://unicode.org/reports/tr18/#General_Category_Property) |
| `Bidi_Control``Bidi_C`                                       | [`Bidi_Control`](https://unicode.org/reports/tr44/#Bidi_Control) |
| `Bidi_Mirrored``Bidi_M`                                      | [`Bidi_Mirrored`](https://unicode.org/reports/tr44/#Bidi_Mirrored) |
| `Case_Ignorable``CI`                                         | [`Case_Ignorable`](https://unicode.org/reports/tr44/#Case_Ignorable) |
| `Cased`                                                      | [`Cased`](https://unicode.org/reports/tr44/#Cased)           |
| `Changes_When_Casefolded``CWCF`                              | [`Changes_When_Casefolded`](https://unicode.org/reports/tr44/#CWCF) |
| `Changes_When_Casemapped``CWCM`                              | [`Changes_When_Casemapped`](https://unicode.org/reports/tr44/#CWCM) |
| `Changes_When_Lowercased``CWL`                               | [`Changes_When_Lowercased`](https://unicode.org/reports/tr44/#CWL) |
| `Changes_When_NFKC_Casefolded``CWKCF`                        | [`Changes_When_NFKC_Casefolded`](https://unicode.org/reports/tr44/#CWKCF) |
| `Changes_When_Titlecased``CWT`                               | [`Changes_When_Titlecased`](https://unicode.org/reports/tr44/#CWT) |
| `Changes_When_Uppercased``CWU`                               | [`Changes_When_Uppercased`](https://unicode.org/reports/tr44/#CWU) |
| `Dash`                                                       | [`Dash`](https://unicode.org/reports/tr44/#Dash)             |
| `Default_Ignorable_Code_Point``DI`                           | [`Default_Ignorable_Code_Point`](https://unicode.org/reports/tr44/#Default_Ignorable_Code_Point) |
| `Deprecated``Dep`                                            | [`Deprecated`](https://unicode.org/reports/tr44/#Deprecated) |
| `Diacritic``Dia`                                             | [`Diacritic`](https://unicode.org/reports/tr44/#Diacritic)   |
| `Emoji`                                                      | [`Emoji`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Emoji_Component`                                            | [`Emoji_Component`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Emoji_Modifier`                                             | [`Emoji_Modifier`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Emoji_Modifier_Base`                                        | [`Emoji_Modifier_Base`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Emoji_Presentation`                                         | [`Emoji_Presentation`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Extended_Pictographic`                                      | [`Extended_Pictographic`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Extender``Ext`                                              | [`Extender`](https://unicode.org/reports/tr44/#Extender)     |
| `Grapheme_Base``Gr_Base`                                     | [`Grapheme_Base`](https://unicode.org/reports/tr44/#Grapheme_Base) |
| `Grapheme_Extend``Gr_Ext`                                    | [`Grapheme_Extend`](https://unicode.org/reports/tr44/#Grapheme_Extend) |
| `Hex_Digit``Hex`                                             | [`Hex_Digit`](https://unicode.org/reports/tr44/#Hex_Digit)   |
| `IDS_Binary_Operator``IDSB`                                  | [`IDS_Binary_Operator`](https://unicode.org/reports/tr44/#IDS_Binary_Operator) |
| `IDS_Trinary_Operator``IDST`                                 | [`IDS_Trinary_Operator`](https://unicode.org/reports/tr44/#IDS_Trinary_Operator) |
| `ID_Continue``IDC`                                           | [`ID_Continue`](https://unicode.org/reports/tr44/#ID_Continue) |
| `ID_Start``IDS`                                              | [`ID_Start`](https://unicode.org/reports/tr44/#ID_Start)     |
| `Ideographic``Ideo`                                          | [`Ideographic`](https://unicode.org/reports/tr44/#Ideographic) |
| `Join_Control``Join_C`                                       | [`Join_Control`](https://unicode.org/reports/tr44/#Join_Control) |
| `Logical_Order_Exception``LOE`                               | [`Logical_Order_Exception`](https://unicode.org/reports/tr44/#Logical_Order_Exception) |
| `Lowercase``Lower`                                           | [`Lowercase`](https://unicode.org/reports/tr44/#Lowercase)   |
| `Math`                                                       | [`Math`](https://unicode.org/reports/tr44/#Math)             |
| `Noncharacter_Code_Point``NChar`                             | [`Noncharacter_Code_Point`](https://unicode.org/reports/tr44/#Noncharacter_Code_Point) |
| `Pattern_Syntax``Pat_Syn`                                    | [`Pattern_Syntax`](https://unicode.org/reports/tr44/#Pattern_Syntax) |
| `Pattern_White_Space``Pat_WS`                                | [`Pattern_White_Space`](https://unicode.org/reports/tr44/#Pattern_White_Space) |
| `Quotation_Mark``QMark`                                      | [`Quotation_Mark`](https://unicode.org/reports/tr44/#Quotation_Mark) |
| `Radical`                                                    | [`Radical`](https://unicode.org/reports/tr44/#Radical)       |
| `Regional_Indicator``RI`                                     | [`Regional_Indicator`](https://unicode.org/reports/tr44/#Regional_Indicator) |
| `Sentence_Terminal``STerm`                                   | [`Sentence_Terminal`](https://unicode.org/reports/tr44/#STerm) |
| `Soft_Dotted``SD`                                            | [`Soft_Dotted`](https://unicode.org/reports/tr44/#Soft_Dotted) |
| `Terminal_Punctuation``Term`                                 | [`Terminal_Punctuation`](https://unicode.org/reports/tr44/#Terminal_Punctuation) |
| `Unified_Ideograph``UIdeo`                                   | [`Unified_Ideograph`](https://unicode.org/reports/tr44/#Unified_Ideograph) |
| `Uppercase``Upper`                                           | [`Uppercase`](https://unicode.org/reports/tr44/#Uppercase)   |
| `Variation_Selector``VS`                                     | [`Variation_Selector`](https://unicode.org/reports/tr44/#Variation_Selector) |
| `White_Space``space`                                         | [`White_Space`](https://unicode.org/reports/tr44/#White_Space) |
| `XID_Continue``XIDC`                                         | [`XID_Continue`](https://unicode.org/reports/tr44/#XID_Continue) |
| `XID_Start``XIDS`                                            | [`XID_Start`](https://unicode.org/reports/tr44/#XID_Start)   |

##### 21.2.2.8.4 RS: UnicodeMatchPropertyValue ( p, v ) <div id="sec-runtime-semantics-unicodematchpropertyvalue-p-v"></div>

The abstract operation UnicodeMatchPropertyValue takes two parameters p and v, each of which is a [List](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-list-and-record-specification-type) of Unicode code points, and performs the following steps:

1. 断言：p is a List of Unicode code points that is identical to a List of Unicode code points that is a canonical, unaliased Unicode property name listed in the “Canonical property name” column of Table 54.
2. 断言：v is a List of Unicode code points that is identical to a List of Unicode code points that is a property value or property value alias for Unicode property p listed in the “Property value and aliases” column of Table 56 or Table 57.
3. 令 value 为 the canonical property value of v as given in the “Canonical property value” column of the corresponding row.
4. 返回 the List of Unicode code points of value.

Implementations must support the Unicode property value names and aliases listed in Table 56 and Table 57. To ensure interoperability, implementations must not support any other property value names or aliases.

>注 1
>For example, Xpeo and Old_Persian are valid Script_Extensions values, but xpeo and Old Persian aren't.
>注 2
>This algorithm differs from the matching rules for symbolic values listed in UAX44: case, white space, U+002D (HYPHEN-MINUS), and U+005F (LOW LINE) are not ignored, and the Is prefix is not supported.

Table 56: Value aliases and canonical values for the Unicode property [`General_Category`](https://unicode.org/reports/tr18/#General_Category_Property)

| Property value and aliases  | Canonical property value |
| --------------------------- | ------------------------ |
| `Cased_Letter``LC`          | `Cased_Letter`           |
| `Close_Punctuation``Pe`     | `Close_Punctuation`      |
| `Connector_Punctuation``Pc` | `Connector_Punctuation`  |
| `Control``Cc``cntrl`        | `Control`                |
| `Currency_Symbol``Sc`       | `Currency_Symbol`        |
| `Dash_Punctuation``Pd`      | `Dash_Punctuation`       |
| `Decimal_Number``Nd``digit` | `Decimal_Number`         |
| `Enclosing_Mark``Me`        | `Enclosing_Mark`         |
| `Final_Punctuation``Pf`     | `Final_Punctuation`      |
| `Format``Cf`                | `Format`                 |
| `Initial_Punctuation``Pi`   | `Initial_Punctuation`    |
| `Letter``L`                 | `Letter`                 |
| `Letter_Number``Nl`         | `Letter_Number`          |
| `Line_Separator``Zl`        | `Line_Separator`         |
| `Lowercase_Letter``Ll`      | `Lowercase_Letter`       |
| `Mark``M``Combining_Mark`   | `Mark`                   |
| `Math_Symbol``Sm`           | `Math_Symbol`            |
| `Modifier_Letter``Lm`       | `Modifier_Letter`        |
| `Modifier_Symbol``Sk`       | `Modifier_Symbol`        |
| `Nonspacing_Mark``Mn`       | `Nonspacing_Mark`        |
| `Number``N`                 | `Number`                 |
| `Open_Punctuation``Ps`      | `Open_Punctuation`       |
| `Other``C`                  | `Other`                  |
| `Other_Letter``Lo`          | `Other_Letter`           |
| `Other_Number``No`          | `Other_Number`           |
| `Other_Punctuation``Po`     | `Other_Punctuation`      |
| `Other_Symbol``So`          | `Other_Symbol`           |
| `Paragraph_Separator``Zp`   | `Paragraph_Separator`    |
| `Private_Use``Co`           | `Private_Use`            |
| `Punctuation``P``punct`     | `Punctuation`            |
| `Separator``Z`              | `Separator`              |
| `Space_Separator``Zs`       | `Space_Separator`        |
| `Spacing_Mark``Mc`          | `Spacing_Mark`           |
| `Surrogate``Cs`             | `Surrogate`              |
| `Symbol``S`                 | `Symbol`                 |
| `Titlecase_Letter``Lt`      | `Titlecase_Letter`       |
| `Unassigned``Cn`            | `Unassigned`             |
| `Uppercase_Letter``Lu`      | `Uppercase_Letter`       |

Table 57: Value aliases and canonical values for the Unicode properties [`Script`](https://unicode.org/reports/tr24/#Script) and [`Script_Extensions`](https://unicode.org/reports/tr24/#Script_Extensions)

| Property value and aliases     | Canonical property value |
| ------------------------------ | ------------------------ |
| `Adlam``Adlm`                  | `Adlam`                  |
| `Ahom``Ahom`                   | `Ahom`                   |
| `Anatolian_Hieroglyphs``Hluw`  | `Anatolian_Hieroglyphs`  |
| `Arabic``Arab`                 | `Arabic`                 |
| `Armenian``Armn`               | `Armenian`               |
| `Avestan``Avst`                | `Avestan`                |
| `Balinese``Bali`               | `Balinese`               |
| `Bamum``Bamu`                  | `Bamum`                  |
| `Bassa_Vah``Bass`              | `Bassa_Vah`              |
| `Batak``Batk`                  | `Batak`                  |
| `Bengali``Beng`                | `Bengali`                |
| `Bhaiksuki``Bhks`              | `Bhaiksuki`              |
| `Bopomofo``Bopo`               | `Bopomofo`               |
| `Brahmi``Brah`                 | `Brahmi`                 |
| `Braille``Brai`                | `Braille`                |
| `Buginese``Bugi`               | `Buginese`               |
| `Buhid``Buhd`                  | `Buhid`                  |
| `Canadian_Aboriginal``Cans`    | `Canadian_Aboriginal`    |
| `Carian``Cari`                 | `Carian`                 |
| `Caucasian_Albanian``Aghb`     | `Caucasian_Albanian`     |
| `Chakma``Cakm`                 | `Chakma`                 |
| `Cham``Cham`                   | `Cham`                   |
| `Cherokee``Cher`               | `Cherokee`               |
| `Common``Zyyy`                 | `Common`                 |
| `Coptic``Copt``Qaac`           | `Coptic`                 |
| `Cuneiform``Xsux`              | `Cuneiform`              |
| `Cypriot``Cprt`                | `Cypriot`                |
| `Cyrillic``Cyrl`               | `Cyrillic`               |
| `Deseret``Dsrt`                | `Deseret`                |
| `Devanagari``Deva`             | `Devanagari`             |
| `Dogra``Dogr`                  | `Dogra`                  |
| `Duployan``Dupl`               | `Duployan`               |
| `Egyptian_Hieroglyphs``Egyp`   | `Egyptian_Hieroglyphs`   |
| `Elbasan``Elba`                | `Elbasan`                |
| `Ethiopic``Ethi`               | `Ethiopic`               |
| `Georgian``Geor`               | `Georgian`               |
| `Glagolitic``Glag`             | `Glagolitic`             |
| `Gothic``Goth`                 | `Gothic`                 |
| `Grantha``Gran`                | `Grantha`                |
| `Greek``Grek`                  | `Greek`                  |
| `Gujarati``Gujr`               | `Gujarati`               |
| `Gunjala_Gondi``Gong`          | `Gunjala_Gondi`          |
| `Gurmukhi``Guru`               | `Gurmukhi`               |
| `Han``Hani`                    | `Han`                    |
| `Hangul``Hang`                 | `Hangul`                 |
| `Hanifi_Rohingya``Rohg`        | `Hanifi_Rohingya`        |
| `Hanunoo``Hano`                | `Hanunoo`                |
| `Hatran``Hatr`                 | `Hatran`                 |
| `Hebrew``Hebr`                 | `Hebrew`                 |
| `Hiragana``Hira`               | `Hiragana`               |
| `Imperial_Aramaic``Armi`       | `Imperial_Aramaic`       |
| `Inherited``Zinh``Qaai`        | `Inherited`              |
| `Inscriptional_Pahlavi``Phli`  | `Inscriptional_Pahlavi`  |
| `Inscriptional_Parthian``Prti` | `Inscriptional_Parthian` |
| `Javanese``Java`               | `Javanese`               |
| `Kaithi``Kthi`                 | `Kaithi`                 |
| `Kannada``Knda`                | `Kannada`                |
| `Katakana``Kana`               | `Katakana`               |
| `Kayah_Li``Kali`               | `Kayah_Li`               |
| `Kharoshthi``Khar`             | `Kharoshthi`             |
| `Khmer``Khmr`                  | `Khmer`                  |
| `Khojki``Khoj`                 | `Khojki`                 |
| `Khudawadi``Sind`              | `Khudawadi`              |
| `Lao``Laoo`                    | `Lao`                    |
| `Latin``Latn`                  | `Latin`                  |
| `Lepcha``Lepc`                 | `Lepcha`                 |
| `Limbu``Limb`                  | `Limbu`                  |
| `Linear_A``Lina`               | `Linear_A`               |
| `Linear_B``Linb`               | `Linear_B`               |
| `Lisu``Lisu`                   | `Lisu`                   |
| `Lycian``Lyci`                 | `Lycian`                 |
| `Lydian``Lydi`                 | `Lydian`                 |
| `Mahajani``Mahj`               | `Mahajani`               |
| `Makasar``Maka`                | `Makasar`                |
| `Malayalam``Mlym`              | `Malayalam`              |
| `Mandaic``Mand`                | `Mandaic`                |
| `Manichaean``Mani`             | `Manichaean`             |
| `Marchen``Marc`                | `Marchen`                |
| `Medefaidrin``Medf`            | `Medefaidrin`            |
| `Masaram_Gondi``Gonm`          | `Masaram_Gondi`          |
| `Meetei_Mayek``Mtei`           | `Meetei_Mayek`           |
| `Mende_Kikakui``Mend`          | `Mende_Kikakui`          |
| `Meroitic_Cursive``Merc`       | `Meroitic_Cursive`       |
| `Meroitic_Hieroglyphs``Mero`   | `Meroitic_Hieroglyphs`   |
| `Miao``Plrd`                   | `Miao`                   |
| `Modi``Modi`                   | `Modi`                   |
| `Mongolian``Mong`              | `Mongolian`              |
| `Mro``Mroo`                    | `Mro`                    |
| `Multani``Mult`                | `Multani`                |
| `Myanmar``Mymr`                | `Myanmar`                |
| `Nabataean``Nbat`              | `Nabataean`              |
| `New_Tai_Lue``Talu`            | `New_Tai_Lue`            |
| `Newa``Newa`                   | `Newa`                   |
| `Nko``Nkoo`                    | `Nko`                    |
| `Nushu``Nshu`                  | `Nushu`                  |
| `Ogham``Ogam`                  | `Ogham`                  |
| `Ol_Chiki``Olck`               | `Ol_Chiki`               |
| `Old_Hungarian``Hung`          | `Old_Hungarian`          |
| `Old_Italic``Ital`             | `Old_Italic`             |
| `Old_North_Arabian``Narb`      | `Old_North_Arabian`      |
| `Old_Permic``Perm`             | `Old_Permic`             |
| `Old_Persian``Xpeo`            | `Old_Persian`            |
| `Old_Sogdian``Sogo`            | `Old_Sogdian`            |
| `Old_South_Arabian``Sarb`      | `Old_South_Arabian`      |
| `Old_Turkic``Orkh`             | `Old_Turkic`             |
| `Oriya``Orya`                  | `Oriya`                  |
| `Osage``Osge`                  | `Osage`                  |
| `Osmanya``Osma`                | `Osmanya`                |
| `Pahawh_Hmong``Hmng`           | `Pahawh_Hmong`           |
| `Palmyrene``Palm`              | `Palmyrene`              |
| `Pau_Cin_Hau``Pauc`            | `Pau_Cin_Hau`            |
| `Phags_Pa``Phag`               | `Phags_Pa`               |
| `Phoenician``Phnx`             | `Phoenician`             |
| `Psalter_Pahlavi``Phlp`        | `Psalter_Pahlavi`        |
| `Rejang``Rjng`                 | `Rejang`                 |
| `Runic``Runr`                  | `Runic`                  |
| `Samaritan``Samr`              | `Samaritan`              |
| `Saurashtra``Saur`             | `Saurashtra`             |
| `Sharada``Shrd`                | `Sharada`                |
| `Shavian``Shaw`                | `Shavian`                |
| `Siddham``Sidd`                | `Siddham`                |
| `SignWriting``Sgnw`            | `SignWriting`            |
| `Sinhala``Sinh`                | `Sinhala`                |
| `Sogdian``Sogd`                | `Sogdian`                |
| `Sora_Sompeng``Sora`           | `Sora_Sompeng`           |
| `Soyombo``Soyo`                | `Soyombo`                |
| `Sundanese``Sund`              | `Sundanese`              |
| `Syloti_Nagri``Sylo`           | `Syloti_Nagri`           |
| `Syriac``Syrc`                 | `Syriac`                 |
| `Tagalog``Tglg`                | `Tagalog`                |
| `Tagbanwa``Tagb`               | `Tagbanwa`               |
| `Tai_Le``Tale`                 | `Tai_Le`                 |
| `Tai_Tham``Lana`               | `Tai_Tham`               |
| `Tai_Viet``Tavt`               | `Tai_Viet`               |
| `Takri``Takr`                  | `Takri`                  |
| `Tamil``Taml`                  | `Tamil`                  |
| `Tangut``Tang`                 | `Tangut`                 |
| `Telugu``Telu`                 | `Telugu`                 |
| `Thaana``Thaa`                 | `Thaana`                 |
| `Thai``Thai`                   | `Thai`                   |
| `Tibetan``Tibt`                | `Tibetan`                |
| `Tifinagh``Tfng`               | `Tifinagh`               |
| `Tirhuta``Tirh`                | `Tirhuta`                |
| `Ugaritic``Ugar`               | `Ugaritic`               |
| `Vai``Vaii`                    | `Vai`                    |
| `Warang_Citi``Wara`            | `Warang_Citi`            |
| `Yi``Yiii`                     | `Yi`                     |
| `Zanabazar_Square``Zanb`       | `Zanabazar_Square`       |

#### 21.2.2.9 转义原子 <div id="sec-atomescape"></div>

With parameter direction.

产生式 AtomEscape :: DecimalEscape 如下解释执行：

1. Evaluate DecimalEscape to obtain an integer n.
2. 断言：n ≤ NcapturingParens.
3. 调用 BackreferenceMatcher(n, direction) and return its Matcher result.

产生式 AtomEscape :: CharacterEscape 如下解释执行：

1. Evaluate CharacterEscape to obtain a character ch.
2. 令 A 为 a one-element CharSet containing the character ch.
3. 调用 CharacterSetMatcher(A, false, direction) and return its Matcher result.

产生式 AtomEscape :: CharacterClassEscape 如下解释执行：

1. Evaluate CharacterClassEscape to obtain a CharSet A.
2. 调用 CharacterSetMatcher(A, false, direction) and return its Matcher result.

> 注 An escape sequence of the form \ followed by a nonzero decimal number n matches the result of the nth set of capturing parentheses (21.2.2.1). It is an error if the regular expression has fewer than n capturing parentheses. If the regular expression has n or more capturing parentheses but the nth one is undefined because it has not captured anything，那么  the backreference always succeeds.

产生式 AtomEscape :: k GroupName 如下解释执行：

1. Search the enclosing Pattern for an instance of a GroupSpecifier for a RegExpIdentifierName which has a StringValue equal to the StringValue of the RegExpIdentifierName contained in GroupName.
2. 断言：A unique such GroupSpecifier is found.
3. 令 parenIndex 为 the number of left-capturing parentheses in the entire regular expression that occur to the left of the located GroupSpecifier. This is the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes prior to or enclosing the located GroupSpecifier.
4. 调用 BackreferenceMatcher(parenIndex, direction) and return its Matcher result.

##### 21.2.2.9.1 RS: BackreferenceMatcher ( n, direction ) <div id="sec-backreference-matcher"></div>

The abstract operation BackreferenceMatcher takes two arguments, an integer n and an integer direction, and performs the following steps:

1. 返回 an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
   1. 令 cap 为 x's captures [List](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-list-and-record-specification-type).
   2. 令 s 为 cap[n].
   3. 若 s 是 undefined，返回 c(x).
   4. 令 e 为 x's endIndex.
   5. 令 len 为 the number of elements in s.
   6. 令 f 为 e + direction × len.
   7. 若 f < 0 or f > InputLength，返回 failure.
   8. 令 g 为 [min](http://www.ecma-international.org/ecma-262/10.0/index.html#eqn-min)(e, f).
   9. 若 there exists an integer i between 0 (inclusive) and len (exclusive) such that [Canonicalize](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-runtime-semantics-canonicalize-ch)(s[i]) 不是 the same character value as [Canonicalize](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-runtime-semantics-canonicalize-ch)(Input[g + i])，返回 failure.
   10. 令 y 为 the State (f, cap).
   11. 调用 c(y) and return its result.

#### 21.2.2.10 转义字符 <div id="sec-characterescape"></div>

The CharacterEscape productions evaluate as follows:

```
CharacterEscape ::
    ControlEscape
    c ControlLetter
    0 [lookahead ∉ DecimalDigit]
    HexEscapeSequence
    RegExpUnicodeEscapeSequence
    IdentityEscape
```

1. 令 cv 为 the CharacterValue of this CharacterEscape.
2. 返回 the character whose character value is cv.

#### 21.2.2.11 转义十进制 <div id="sec-decimalescape"></div>

The DecimalEscape productions evaluate as follows:

```
DecimalEscape :: NonZeroDigit DecimalDigits
```

1. 返回 the CapturingGroupNumber of this DecimalEscape.

>注 If \ is followed by a decimal number n whose first digit is not 0，那么  the escape sequence is considered to be a backreference. It is an error if n is大于 the total number of left-capturing parentheses in the entire regular expression.

#### 21.2.2.12 转义字符类 <div id="sec-characterclassescape"></div>

产生式 CharacterClassEscape :: d 如下解释执行：

1. 返回 the ten-element set of characters containing the characters 0 through 9 inclusive.

产生式 CharacterClassEscape :: D 如下解释执行：

1. 返回 the set of all characters not included in the set returned by CharacterClassEscape :: d .

产生式 CharacterClassEscape :: s 如下解释执行：

1. 返回 the set of characters containing the characters that are on the right-hand side of the WhiteSpace or LineTerminator productions.

产生式 CharacterClassEscape :: S 如下解释执行：

1. 返回 the set of all characters not included in the set returned by CharacterClassEscape :: s .

产生式 CharacterClassEscape :: w 如下解释执行：

1. 返回 the set of all characters returned by WordCharacters().

产生式 CharacterClassEscape :: W 如下解释执行：

1. 返回 the set of all characters not included in the set returned by CharacterClassEscape :: w .

The production CharacterClassEscape :: p{ UnicodePropertyValueExpression } evaluates by returning the CharSet containing all Unicode code points included in the CharSet returned by UnicodePropertyValueExpression.

The production CharacterClassEscape :: P{ UnicodePropertyValueExpression } evaluates by returning the CharSet containing all Unicode code points not included in the CharSet returned by UnicodePropertyValueExpression.

产生式 UnicodePropertyValueExpression :: UnicodePropertyName = UnicodePropertyValue 如下解释执行：

1. 令 ps 为 SourceText of UnicodePropertyName.
2. 令 p 为 ! UnicodeMatchProperty(ps).
3. 断言：p is a Unicode property name or property alias listed in the “Property name and aliases” column of Table 54.
4. 令 vs 为 SourceText of UnicodePropertyValue.
5. 令 v 为 ! UnicodeMatchPropertyValue(p, vs).
6. 返回 the CharSet containing all Unicode code points whose character database definition includes the property p with value v.

产生式 UnicodePropertyValueExpression :: LoneUnicodePropertyNameOrValue 如下解释执行：

1. 令 s 为 SourceText of LoneUnicodePropertyNameOrValue.
2. 若 ! UnicodeMatchPropertyValue("General_Category", s) is identical to a List of Unicode code points that 是 the name of a Unicode general category or general category alias listed in the “Property value and aliases” column of Table 56，那么 
  1. 返回 the CharSet containing all Unicode code points whose character database definition includes the property “General_Category” with value s.
3. 令 p 为 ! UnicodeMatchProperty(s).
4. 断言：p is a binary Unicode property or binary property alias listed in the “Property name and aliases” column of Table 55.
5. 返回 the CharSet containing all Unicode code points whose character database definition includes the property p with value “True”.

#### 21.2.2.13 字符类 <div id="sec-characterclass"></div>

产生式 CharacterClass :: [ ClassRanges ] 如下解释执行：

1. Evaluate ClassRanges to obtain a CharSet A.
2. 返回 the two results A and false.

产生式 CharacterClass :: [ ^ ClassRanges ] 如下解释执行：

1. Evaluate ClassRanges to obtain a CharSet A.
2. 返回 the two results A and true.

#### 21.2.2.14 字符范围集 <div id="sec-classranges"></div>

产生式 ClassRanges :: [empty] 如下解释执行：

1. 返回 the empty CharSet.

产生式 ClassRanges :: NonemptyClassRanges 如下解释执行：

1. 返回 the CharSet that is the result of evaluating NonemptyClassRanges.

#### 21.2.2.15 非空字符范围集 <div id="sec-nonemptyclassranges"></div>

产生式 NonemptyClassRanges :: ClassAtom 如下解释执行：

1. 返回 the CharSet that is the result of evaluating ClassAtom.

产生式 NonemptyClassRanges :: ClassAtom NonemptyClassRangesNoDash 如下解释执行：

1. Evaluate ClassAtom to obtain a CharSet A.

2. Evaluate NonemptyClassRangesNoDash to obtain a CharSet B.
3. 返回 the union of CharSets A and B.

产生式 NonemptyClassRanges :: ClassAtom - ClassAtom ClassRanges 如下解释执行：

1. Evaluate the first ClassAtom to obtain a CharSet A.
2. Evaluate the second ClassAtom to obtain a CharSet B.
3. Evaluate ClassRanges to obtain a CharSet C.
4. 调用 CharacterRange(A, B) and 令 D 为 the resulting CharSet.
5. 返回 the union of CharSets D and C.

##### 21.2.2.15.1 RS: CharacterRange ( A, B ) <div id="sec-runtime-semantics-characterrange-abstract-operation"></div>

The abstract operation CharacterRange takes two CharSet parameters A and B and performs the following steps:

1. 断言：A and B each contain exactly one character.
2. 令 a 为 the one character in CharSet A.
3. 令 b 为 the one character in CharSet B.
4. 令 i 为 the character value of character a.
5. 令 j 为 the character value of character b.
6. 断言：i ≤ j.
7. 返回 the set containing all characters numbered i through j, inclusive.

#### 21.2.2.16 无连接符非空字符范围集 <div id="sec-nonemptyclassrangesnodash"></div>

产生式 NonemptyClassRangesNoDash :: ClassAtom 如下解释执行：

1. 返回 the CharSet that is the result of evaluating ClassAtom.

产生式 NonemptyClassRangesNoDash :: ClassAtomNoDash NonemptyClassRangesNoDash 如下解释执行：

1. Evaluate ClassAtomNoDash to obtain a CharSet A.
2. Evaluate NonemptyClassRangesNoDash to obtain a CharSet B.
3. 返回 the union of CharSets A and B

产生式 NonemptyClassRangesNoDash :: ClassAtomNoDash - ClassAtom ClassRanges 如下解释执行：

1. Evaluate ClassAtomNoDash to obtain a CharSet A.
2. Evaluate ClassAtom to obtain a CharSet B.
3. Evaluate ClassRanges to obtain a CharSet C.
4. 调用 CharacterRange(A, B) and 令 D 为 the resulting CharSet.
5. 返回 the union of CharSets D and C.

> 注 1 
>
> ClassRanges can expand into a single ClassAtom and/or ranges of two ClassAtom separated by dashes. In the latter case the ClassRanges includes all characters between the first ClassAtom and the second ClassAtom, inclusive; an error occurs if either ClassAtom does not represent a single character (for example, if one is \w) or if the first ClassAtom's character value is大于 the second ClassAtom's character value. 
>
> 注 2 
>
> Even if the pattern ignores case, the case of the two ends of a range is significant in determining which characters belong to the range. Thus, for example, the pattern /[E-F]/i matches only the letters E, F, e, and f, while the pattern /[E-f]/i matches all upper and lower-case letters in the Unicode Basic Latin block as well as the symbols [, \, ], ^, _, and `.
>
> 注 3
>
> A - character can be treated literally or it can denote a range. It is treated literally if it is the first or last character of ClassRanges, the beginning or end limit of a range specification, or immediately follows a range specification.

#### 21.2.2.17 字符类原子 <div id="sec-classatom"></div>

产生式 ClassAtom :: - 如下解释执行：

1. 返回 the CharSet containing the single character - U+002D (HYPHEN-MINUS).

产生式 ClassAtom :: ClassAtomNoDash 如下解释执行：

1. 返回 the CharSet that is the result of evaluating ClassAtomNoDash.

#### 21.2.2.18 非连接符字符类原子 <div id="sec-classatomnodash"></div>

产生式 ClassAtomNoDash :: SourceCharacter but not one of \ or ] or - 如下解释执行：

1. 返回 the CharSet containing the character matched by SourceCharacter.

产生式 ClassAtomNoDash :: \ ClassEscape 如下解释执行：

1. 返回 the CharSet that is the result of evaluating ClassEscape.

#### 21.2.2.19 字符可用转义 <div id="sec-classescape"></div>

The ClassEscape productions evaluate as follows:

```
ClassEscape :: b
ClassEscape :: -
ClassEscape :: CharacterEscape
```

1. 令 cv 为 the CharacterValue of this ClassEscape.
2. 令 c 为 the character whose character value is cv.
3. 返回 the CharSet containing the single character c.

```
ClassEscape :: CharacterClassEscape
```

1. 返回 the CharSet that is the result of evaluating CharacterClassEscape.

> 注
>
> A ClassAtom can use any of the escape sequences that are allowed in the rest of the regular expression except for \b, \B, and backreferences. Inside a CharacterClass, \b means the backspace character, while \B and backreferences raise errors. Using a backreference inside a ClassAtom causes an error.

### 21.2.3 RegExp 构造器 <div id="sec-regexp-constructor"></div>

The RegExp constructor:

- is the intrinsic object %RegExp%.
- is the initial value of the RegExp property of the global object.
- creates and initializes a new RegExp object when called as a function rather than as a constructor. Thus the function call RegExp(…) is equivalent to the object creation expression new RegExp(…) with the same arguments.
- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. Subclass constructors that intend to inherit the specified RegExp behaviour must include a super call to the RegExp constructor to create and initialize subclass instances with the necessary internal slots.

#### 21.2.3.1 RegExp ( pattern, flags ) <div id="sec-regexp-pattern-flags"></div>

将采取以下步骤：

1. 令 patternIsRegExp 为 ? IsRegExp(pattern).
2. 若 NewTarget 是 undefined，那么 
  1. 令 newTarget 为 the active function object.
  2. 若 patternIsRegExp is true and flags 是 undefined，那么 
        1. 令 patternConstructor 为 ? Get(pattern, "constructor").
            2. 若 SameValue(newTarget, patternConstructor) 是 true，返回 pattern.
3.  否则， 令 newTarget 为 NewTarget.
4. 若 Type(pattern) 是 Object and pattern has a [[RegExpMatcher]] internal slot，那么 
  1. 令 P 为 pattern.[[OriginalSource]].
  2. 若 flags 是 undefined, 令 F 为 pattern.[[OriginalFlags]].
  3.  否则， 令 F 为 flags.
5. Else if patternIsRegExp is true，那么 
  1. 令 P 为 ? Get(pattern, "source").
  2. 若 flags 是 undefined，那么 
  3. 令 F 为 ? Get(pattern, "flags").
  4.  否则， 令 F 为 flags.
6.  否则，
  1. 令 P 为 pattern.
  2. 令 F 为 flags.
7. 令 O 为 ? RegExpAlloc(newTarget).
8. 返回 ? RegExpInitialize(O, P, F).

> 注
>
> If pattern is supplied using a StringLiteral, the usual escape sequence substitutions are performed before the String is processed by RegExp. If pattern must contain an escape sequence to be recognized by RegExp, any U+005C (REVERSE SOLIDUS) code points must be escaped within the StringLiteral to prevent them being removed when the contents of the StringLiteral are formed

#### 21.2.3.2 RegExp构造器的抽象操作 <div id="sec-abstract-operations-for-the-regexp-constructor"></div>

##### 21.2.3.2.1 RS: RegExpAlloc ( newTarget ) <div id="sec-regexpalloc"></div>

When the abstract operation RegExpAlloc with argument newTarget is called, 将采取以下步骤：

1. 令 obj 为 ? OrdinaryCreateFromConstructor(newTarget, "%RegExpPrototype%", « [[RegExpMatcher]],[[OriginalSource]], [[OriginalFlags]] »). 
2. 执行 ! DefinePropertyOrThrow(obj, "lastIndex", PropertyDescriptor { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }). 
3. 返回 obj.

##### 21.2.3.2.2 RS: RegExpInitialize ( obj, pattern, flags ) <div id="sec-regexpinitialize"></div>

When the abstract operation RegExpInitialize with arguments obj, pattern, and flags is called, 将采取以下步骤：

1. 若 pattern 是 undefined, 令 P 为空字符串.
2.  否则， 令 P 为 ? ToString(pattern).
3. 若 flags 是 undefined, 令 F 为空字符串.
4.  否则， 令 F 为 ? ToString(flags).
5. 若 F contains any code unit other than "g", "i", "m", "s", "u", or "y" or if it contains the same code unit more than once, 抛出 SyntaxError 异常.
6. 若 F contains "u", 令 BMP 为 false; else 令 BMP 为 true.
7. 若 BMP 是 true，那么 
   1. Parse P using the grammars in 21.2.1 and interpreting each of its 16-bit elements as a Unicode BMP code point. UTF-16 decoding is not applied to the elements. The goal symbol for the parse is Pattern[~U, ~N]. If the result of parsing contains a GroupName, reparse with the goal symbol Pattern[~U, +N] and use this result instead. 抛出 SyntaxError 异常 if P did not conform to the grammar, if any elements of P were not matched by the parse, or if any Early Error conditions exist.
   2. 令 patternCharacters 为 a List whose elements are the code unit elements of P.
8.  否则，
   1. Parse P using the grammars in 21.2.1 and interpreting P as UTF-16 encoded Unicode code points (6.1.4). The goal symbol for the parse is Pattern[+U, +N]. 抛出 SyntaxError 异常 if P did not conform to the grammar, if any elements of P were not matched by the parse, or if any Early Error conditions exist.
   2. 令 patternCharacters 为 a List whose elements are the code points resulting from applying UTF-16 decoding to P's sequence of elements.
9. 设置 obj.[[OriginalSource]] 为 P.
10. 设置 obj.[[OriginalFlags]] 为 F.
11. 设置 obj.[[RegExpMatcher]] 为 the internal procedure that evaluates the above parse of P by applying the semantics provided in 21.2.2 using patternCharacters as the pattern's List of SourceCharacter values and F as the flag parameters.
12. 执行 ? Set(obj, "lastIndex", 0, true).
13. 返回 obj.

##### 21.2.3.2.3 RS: RegExpCreate ( P, F ) <div id="sec-regexpcreate"></div>

When the abstract operation RegExpCreate with arguments P and F is called, 将采取以下步骤：

1. 令 obj 为 ? RegExpAlloc(%RegExp%).
2. 返回 ? RegExpInitialize(obj, P, F).

##### 21.2.3.2.4 RS: EscapeRegExpPattern ( P, F ) <div id="sec-escaperegexppattern"></div>

When the abstract operation EscapeRegExpPattern with arguments P and F is called, the following occurs:

1. Let S be a String in the form of a Pattern[~U] (Pattern[+U] if F contains "u") equivalent to P interpreted as UTF-16 encoded Unicode code points (6.1.4), in which certain code points are escaped as described below. S may or may not be identical to P; however, the internal procedure that would result from evaluating S as a Pattern[~U] (Pattern[+U] if F contains "u") must behave identically to the internal procedure given by the constructed object's [[RegExpMatcher]] internal slot. Multiple calls to this abstract operation using the same values for P and F must produce identical results.
2. The code points / or any LineTerminator occurring in the pattern shall be escaped in S as necessary to ensure that the string-concatenation of "/", S, "/", and F can be parsed (in an appropriate lexical context) as a RegularExpressionLiteral that behaves identically to the constructed regular expression. For example, if P is "/"，那么  S could be "\/" or "\u002F", among other possibilities, but not "/", because /// followed by F would be parsed as a SingleLineComment rather than a RegularExpressionLiteral. If P is空字符串, this specification can be met by letting S be "(?:)".
3. 返回 S.

### 21.2.4 RegExp 构造器属性 <div id="sec-properties-of-the-regexp-constructor"></div>

The RegExp constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 21.2.4.1 RegExp.prototype <div id="sec-regexp.prototype"></div>

The initial value of RegExp.prototype is the intrinsic object %RegExpPrototype%.

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 21.2.4.2 get RegExp [ @@species ] <div id="sec-get-regexp-@@species"></div>

RegExp[@@species] is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 返回 the this value.

The value of the name property of this function is "get [Symbol.species]".

> 注 RegExp prototype methods normally use their this object's constructor to create a derived object. However, a subclass constructor may over-ride that default behaviour by redefining its @@species property.

### 21.2.5 RegExp 原型对象属性 <div id="sec-properties-of-the-regexp-prototype-object"></div>

The RegExp prototype object:

- is the intrinsic object %RegExpPrototype%.
- is an ordinary object.
- is not a RegExp instance and does not have a [[RegExpMatcher]] internal slot or any of the other internal slots of RegExp instance objects.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

> 注 The RegExp prototype object does not have a valueOf property of its own; however, it inherits the valueOf property from the Object prototype object.

#### 21.2.5.1 RegExp.prototype.constructor <div id="sec-regexp.prototype.constructor"></div>

The initial value of RegExp.prototype.constructor is the intrinsic object %RegExp%.

#### 21.2.5.2 RegExp.prototype.exec ( string ) <div id="sec-regexp.prototype.exec"></div>

Performs a regular expression match of string against the regular expression and returns an Array object containing the results of the match, or null if string did not match.

The String ToString(string) is searched for an occurrence of the regular expression pattern as follows:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R does not have a [[RegExpMatcher]] internal slot, 抛出 TypeError 异常.
4. 令 S 为 ? ToString(string).
5. 返回 ? RegExpBuiltinExec(R, S).

##### 21.2.5.2.1 RS: RegExpExec ( R, S ) <div id="sec-regexpexec"></div>

The abstract operation RegExpExec with arguments R and S performs the following steps:

1. 断言：Type(R) is Object.
2. 断言：Type(S) is String.
3. 令 exec 为 ? Get(R, "exec").
4. 若 IsCallable(exec) 是 true，那么 
1. 令 result 为 ? Call(exec, R, « S »).
2. 若 Type(result) 是 neither Object or Null, 抛出 TypeError 异常.
3. 返回 result.
5. 若 R does not have a [[RegExpMatcher]] internal slot, 抛出 TypeError 异常.
6. 返回 ? RegExpBuiltinExec(R, S).

> 注 If a callable exec property is not found this algorithm falls back to attempting to use the built-in RegExp matching algorithm. This provides compatible behaviour for code written for prior editions where most built-in algorithms that use regular expressions did not perform a dynamic property lookup of exec.

##### 21.2.5.2.2 RS: RegExpBuiltinExec ( R, S ) <div id="sec-regexpbuiltinexec"></div>

The abstract operation RegExpBuiltinExec with arguments R and S performs the following steps:

1. 断言：R is an initialized RegExp instance.
2. 断言：Type(S) is String.
3. 令 length 为 the number of code units in S.
4. 令 lastIndex 为 ? ToLength(? Get(R, "lastIndex")).
5. 令 flags 为 R.[[OriginalFlags]].
6. 若 flags contains "g", 令 global 为 true, else 令 global 为 false.
7. 若 flags contains "y", 令 sticky 为 true, else 令 sticky 为 false.
8. 若 global is false and sticky 是 false, set lastIndex to 0.
9. 令 matcher 为 R.[[RegExpMatcher]].
10. 若 flags contains "u", 令 fullUnicode 为 true, else 令 fullUnicode 为 false.
11. 令 matchSucceeded 为 false.
12. Repeat, while matchSucceeded is false
    1. 若 lastIndex > length，那么 
       1. 若 global is true or sticky 是 true，那么 
          1. 执行 ? Set(R, "lastIndex", 0, true).
       2. 返回 null.
    2. 令 r 为 matcher(S, lastIndex).
    3. 若 r 是 failure，那么 
       1. 若 sticky 是 true，那么 
          1. 执行 ? Set(R, "lastIndex", 0, true).
          2. 返回 null.
       2. 设置 lastIndex 为 AdvanceStringIndex(S, lastIndex, fullUnicode).
    4.  否则，
       1. 断言：r is a State.
       2. 设置 matchSucceeded 为 true.
13. 令 e 为 r's endIndex value.
14. 若 fullUnicode is true，那么 
    1. e is an index into the Input character list, derived from S, matched by matcher. Let eUTF be the smallest index into S that corresponds to the character at element e of Input. If e is大于 or equal to the number of elements in Input，那么  eUTF is the number of code units in S.
    2. 设置 e 为 eUTF.
15. 若 global is true or sticky is true，那么 
    1. 执行 ? Set(R, "lastIndex", e, true).
16. 令 n 为 the number of elements in r's captures List. (This is the same value as 21.2.2.1's NcapturingParens.)
17. Assert: n < 232 - 1.
18. 令 A 为 ! ArrayCreate(n + 1).
19. Assert: The value of A's "length" property is n + 1.
20. 执行 ! CreateDataProperty(A, "index", lastIndex).
21. 执行 ! CreateDataProperty(A, "input", S).
22. Let matchedSubstr be the matched substring (i.e. the portion of S between offset lastIndex inclusive and offset e exclusive).
23. 执行 ! CreateDataProperty(A, "0", matchedSubstr).
24. 若 R contains any GroupName，那么 
    1. 令 groups 为 ObjectCreate(null).
25.  否则，
    1. 令 groups 为 undefined.
26. 执行 ! CreateDataProperty(A, "groups", groups).
27. For each integer i such that i > 0 and i ≤ n, do
    1. 令 captureI 为 ith element of r's captures List.
    2. 若 captureI 是 undefined, 令 capturedValue 为 undefined.
    3. Else if fullUnicode is true，那么 
       1. 断言：captureI is a List of code points.
       2. 令 capturedValue 为 the String value whose code units are the UTF16Encoding of the code points of captureI.
    4. Else fullUnicode is false,
       1. 断言：captureI is a List of code units.
       2. 令 capturedValue 为 the String value consisting of the code units of captureI.
    5. 执行 ! CreateDataProperty(A, ! ToString(i), capturedValue).
    6. 若 the ith capture of R was defined with a GroupName，那么 
       1. 令 s 为 the StringValue of the corresponding RegExpIdentifierName.
       2. 执行 ! CreateDataProperty(groups, s, capturedValue).
28. 返回 A.

##### 21.2.5.2.3 AdvanceStringIndex ( S, index, unicode ) <div id="sec-advancestringindex"></div>

The abstract operation AdvanceStringIndex with arguments S, index, and unicode performs the following steps:

1. 断言：Type(S) is String.
2. 断言：index is an integer such that 0 ≤ index ≤ 253 - 1.
3. 断言：Type(unicode) is Boolean.
4. 若 unicode 是 false，返回 index + 1.
5. 令 length 为 the number of code units in S.
6. 若 index + 1 ≥ length，返回 index + 1.
7. 令 first 为 the numeric value of the code unit at index index within S.
8. 若 first < 0xD800 or first > 0xDBFF，返回 index + 1.
9. 令 second 为 the numeric value of the code unit at index index + 1 within S.
10. 若 second < 0xDC00 or second > 0xDFFF，返回 index + 1.
11. 返回 index + 2.

#### 21.2.5.3 获取 RegExp.prototype.dotAll <div id="sec-get-regexp.prototype.dotAll"></div>

RegExp.prototype.dotAll is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R does not have an [[OriginalFlags]] internal slot，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags contains the code unit 0x0073 (LATIN SMALL LETTER S)，返回 true.
6. 返回 false

#### 21.2.5.4 获取 RegExp.prototype.flags <div id="sec-get-regexp.prototype.flags"></div>

RegExp.prototype.flags is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 令 result 为空字符串.
4. 令 global 为 ToBoolean(? Get(R, "global")).
5. 若 global 是 true, append the code unit 0x0067 (LATIN SMALL LETTER G) as the last code unit of result.
6. 令 ignoreCase 为 ToBoolean(? Get(R, "ignoreCase")).
7. 若 ignoreCase 是 true, append the code unit 0x0069 (LATIN SMALL LETTER I) as the last code unit of result.
8. 令 multiline 为 ToBoolean(? Get(R, "multiline")).
9. 若 multiline 是 true, append the code unit 0x006D (LATIN SMALL LETTER M) as the last code unit of result.
10. 令 dotAll 为 ToBoolean(? Get(R, "dotAll")).
11. 若 dotAll is true, append the code unit 0x0073 (LATIN SMALL LETTER S) as the last code unit of result.
12. 令 unicode 为 ToBoolean(? Get(R, "unicode")).
13. 若 unicode is true, append the code unit 0x0075 (LATIN SMALL LETTER U) as the last code unit of result.
14. 令 sticky 为 ToBoolean(? Get(R, "sticky")).
15. 若 sticky is true, append the code unit 0x0079 (LATIN SMALL LETTER Y) as the last code unit of result.
16. 返回 result.

#### 21.2.5.5 获取 RegExp.prototype.global <div id="sec-get-regexp.prototype.global"></div>

RegExp.prototype.global is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R does not have an [[OriginalFlags]] internal slot，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags contains the code unit 0x0067 (LATIN SMALL LETTER G)，返回 true.
6. 返回 false.

#### 21.2.5.6 获取 RegExp.prototype.ignoreCase <div id="sec-get-regexp.prototype.ignorecase"></div>

RegExp.prototype.ignoreCase is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R does not have an [[OriginalFlags]] internal slot，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags contains the code unit 0x0069 (LATIN SMALL LETTER I)，返回 true.
6. 返回 false.

#### 21.2.5.7 RegExp.prototype [ @@match ] ( string ) <div id="sec-regexp.prototype-@@match"></div>

When the @@match method is called with argument string, 将采取以下步骤：

1. 令 rx 为 the this value.
2. 若 Type(rx) 不是 Object, 抛出 TypeError 异常.
3. 令 S 为 ? ToString(string).
4. 令 global 为 ToBoolean(? Get(rx, "global")).
5. 若 global 是 false，那么 
   1. 返回 ? RegExpExec(rx, S).
6. Else global is true,
   1. 令 fullUnicode 为 ToBoolean(? Get(rx, "unicode")).
   2. 执行 ? Set(rx, "lastIndex", 0, true).
   3. 令 A 为 ! ArrayCreate(0).
   4. 令 n 为 0.
   5. 重复，
      1. 令 result 为 ? RegExpExec(rx, S).
      2. 若 result 是 null，那么 
         1. 若 n = 0，返回 null.
         2. 返回 A.
      3. Else result is not null,
         1. 令 matchStr 为 ? ToString(? Get(result, "0")).
         2. 令 status 为 CreateDataProperty(A, ! ToString(n), matchStr).
         3. 断言：status is true.
         4. 若 matchStr 是空字符串，那么 
            1. 令 thisIndex 为 ? ToLength(? Get(rx, "lastIndex")).
            2. 令 nextIndex 为 AdvanceStringIndex(S, thisIndex, fullUnicode).
            3. 执行 ? Set(rx, "lastIndex", nextIndex, true).
         5. Increment n.

The value of the name property of this function is "[Symbol.match]".

> 注 The @@match property is used by the IsRegExp abstract operation to identify objects that have the basic behaviour of regular expressions. The absence of a @@match property or the existence of such a property whose value does not Boolean coerce to true indicates that the object is not intended to be used as a regular expression object.

#### 21.2.5.8 获取 RegExp.prototype.multiline <div id="sec-get-regexp.prototype.multiline"></div>

RegExp.prototype.multiline is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R does not have an [[OriginalFlags]] internal slot，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags contains the code unit 0x006D (LATIN SMALL LETTER M)，返回 true.
6. 返回 false.

#### 21.2.5.9 RegExp.prototype [ @@replace ] ( string, replaceValue ) <div id="sec-regexp.prototype-@@replace"></div>

When the @@replace method is called with arguments string and replaceValue, 将采取以下步骤：

1. 令 rx 为 the this value.
2. 若 Type(rx) 不是 Object, 抛出 TypeError 异常.
3. 令 S 为 ? ToString(string).
4. 令 lengthS 为 the number of code unit elements in S.
5. 令 functionalReplace 为 IsCallable(replaceValue).
6. 若 functionalReplace 是 false，那么 
   1. 设置 replaceValue 为 ? ToString(replaceValue).
7. 令 global 为 ToBoolean(? Get(rx, "global")).
8. 若 global 是 true，那么 
   1. 令 fullUnicode 为 ToBoolean(? Get(rx, "unicode")).
   2. 执行 ? Set(rx, "lastIndex", 0, true).
9. 令 results 为一个新的空列表。
10. 令 done 为 false.
11. Repeat, while done is false
    1. 令 result 为 ? RegExpExec(rx, S).
    2. 若 result 是 null, set done to true.
    3. Else result is not null,
       1. 添加 result 到 the end of results.
       2. 若 global 是 false, set done to true.
       3.  否则，
          1. 令 matchStr 为 ? ToString(? Get(result, "0")).
          2. 若 matchStr 是空字符串，那么 
             1. 令 thisIndex 为 ? ToLength(? Get(rx, "lastIndex")).
             2. 令 nextIndex 为 AdvanceStringIndex(S, thisIndex, fullUnicode).
             3. 执行 ? Set(rx, "lastIndex", nextIndex, true).
12. 令 accumulatedResult 为空字符串 value.
13. 令 nextSourcePosition 为 0.
14. For each result in results, do
    1. 令 nCaptures 为 ? ToLength(? Get(result, "length")).
    2. 设置 nCaptures 为 max(nCaptures - 1, 0).
    3. 令 matched 为 ? ToString(? Get(result, "0")).
    4. 令 matchLength 为 the number of code units in matched.
    5. 令 position 为 ? ToInteger(? Get(result, "index")).
    6. 设置 position 为 max(min(position, lengthS), 0).
    7. 令 n 为 1.
    8. 令 captures 为一个新的空列表。
    9. 重复， while n ≤ nCaptures
       1. 令 capN 为 ? Get(result, ! ToString(n)).
       2. 若 capN 不是 undefined，那么 
          1. 设置 capN 为 ? ToString(capN).
       3. Append capN as the last element of captures.
       4. n 增加 1
    10. 令 namedCaptures 为 ? Get(result, "groups").
    11. 若 functionalReplace is true，那么 
        1. 令 replacerArgs 为 « matched ».
        2. 添加 in list order the elements of captures 到 the end of the List replacerArgs.
        3. 添加 position and S 到 replacerArgs.
        4. 若 namedCaptures 不是 undefined，那么 
           1. Append namedCaptures as the last element of replacerArgs.
        5. 令 replValue 为 ? Call(replaceValue, undefined, replacerArgs).
        6. 令 replacement 为 ? ToString(replValue).
    12.  否则，
        1. 令 replacement 为 GetSubstitution(matched, S, position, captures, namedCaptures, replaceValue).
    13. 若 position ≥ nextSourcePosition，那么 
        1. 注: position should not normally move backwards. If it does, it is an indication of an ill-behaving RegExp subclass or use of an access triggered side-effect to change the global flag or other characteristics of rx. In such cases, the corresponding substitution is ignored.
        2. 设置 accumulatedResult to the string-concatenation of the current value of accumulatedResult, the substring of S consisting of the code units from nextSourcePosition (inclusive) up 为 position (exclusive), and replacement.
        3. 设置 nextSourcePosition 为 position + matchLength.
15. 若 nextSourcePosition ≥ lengthS，返回 accumulatedResult.
16. 返回 the string-concatenation of accumulatedResult and the substring of S consisting of the code units from nextSourcePosition (inclusive) up through the final code unit of S (inclusive).

The value of the name property of this function is "[Symbol.replace]".

#### 21.2.5.10 RegExp.prototype [ @@search ] ( string ) <div id="sec-regexp.prototype-@@search"></div>

When the @@search method is called with argument string, 将采取以下步骤：

1. 令 rx 为 the this value.
2. 若 Type(rx) 不是 Object, 抛出 TypeError 异常.
3. 令 S 为 ? ToString(string).
4. 令 previousLastIndex 为 ? Get(rx, "lastIndex").
5. 若 SameValue(previousLastIndex, 0) 是 false，那么 
1. 执行 ? Set(rx, "lastIndex", 0, true).
6. 令 result 为 ? RegExpExec(rx, S).
7. 令 currentLastIndex 为 ? Get(rx, "lastIndex").
8. 若 SameValue(currentLastIndex, previousLastIndex) 是 false，那么 
  1. 执行 ? Set(rx, "lastIndex", previousLastIndex, true).
9. 若 result 是 null，返回 -1.
10. 返回 ? Get(result, "index").

The value of the name property of this function is "[Symbol.search]".

> 注 The lastIndex and global properties of this RegExp object are ignored when performing the search. The lastIndex property is left unchanged.

#### 21.2.5.11 获取 RegExp.prototype.source <div id="sec-get-regexp.prototype.source"></div>

RegExp.prototype.source is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R does not have an [[OriginalSource]] internal slot，那么 
   1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 "(?:)".
   2. 除此之外, 抛出 TypeError 异常.
4. 断言：R has an [[OriginalFlags]] internal slot.
5. 令 src 为 R.[[OriginalSource]].
6. 令 flags 为 R.[[OriginalFlags]].
7. 返回 EscapeRegExpPattern(src, flags).

#### 21.2.5.12 RegExp.prototype [ @@split ] ( string, limit ) <div id="sec-regexp.prototype-@@split"></div>

> 注 1
>
> Returns an Array object into which substrings of the result of converting string to a String have been stored. The substrings are determined by searching from left to right for matches of the this value regular expression; these occurrences are not part of any substring in the returned array, but serve to divide up the String value.
>
> The this value may be an empty regular expression or a regular expression that can match an empty String. In this case, the regular expression does not match the empty substring at the beginning or end of the input String, nor does it match the empty substring at the end of the previous separator match. (For example, if the regular expression matches空字符串, the String is split up into individual code unit elements; the length of the result array equals the length of the String, and each substring contains one code unit.) Only the first match at a given index of the String is considered, even if backtracking could yield a non-empty-substring match at that index. (For example, /a\*?/\[Symbol.split]("ab") evaluates to the array ["a", "b"], while /a\*/\[Symbol.split]("ab") evaluates to the array ["","b"].)
>
> If the string is (or converts to)空字符串, the result depends on whether the regular expression can match空字符串. If it can, the result array contains no elements. Otherwise, the result array contains one element, which is空字符串
>
> If the regular expression contains capturing parentheses，那么  each time separator is matched the results (including any undefined results) of the capturing parentheses are spliced into the output array. For example,
>
> /<(\/)?(\[^<>]+)>/\[Symbol.split]("A\<B>bold\</B>and\<CODE>coded\</CODE>")
>
> evaluates to the array
>
> ["A", undefined, "B", "bold", "/", "B", "and", undefined, "CODE", "coded", "/", "CODE"
>
> If limit is not undefined，那么  the output array is truncated so that it contains no more than limit elements.

When the @@split method is called, 将采取以下步骤：

1. 令 rx 为 the this value.
2. 若 Type(rx) 不是 Object, 抛出 TypeError 异常.
3. 令 S 为 ? ToString(string).
4. 令 C 为 ? SpeciesConstructor(rx, %RegExp%).
5. 令 flags 为 ? ToString(? Get(rx, "flags")).
6. 若 flags contains "u", 令 unicodeMatching 为 true.
7.  否则， 令 unicodeMatching 为 false.
8. 若 flags contains "y", 令 newFlags 为 flags.
9.  否则， 令 newFlags 为 the string-concatenation of flags and "y".
10. 令 splitter 为 ? Construct(C, « rx, newFlags »).
11. 令 A 为 ! ArrayCreate(0).
12. 令 lengthA 为 0.
13. 若 limit is undefined, 令 lim 为 232 - 1; else 令 lim 为 ? ToUint32(limit).
14. 令 size 为S的长度.
15. 令 p 为 0.
16. 若 lim = 0，返回 A.
17. 若 size = 0，那么 
    1. 令 z 为 ? RegExpExec(splitter, S).
    2. 若 z 不是 null，返回 A.
    3. 执行 ! CreateDataProperty(A, "0", S).
    4. 返回 A.
18. 令 q 为 p.
19. Repeat, while q < size
    1. 执行 ? Set(splitter, "lastIndex", q, true).
    2. 令 z 为 ? RegExpExec(splitter, S).
    3. 若 z 是 null, set q to AdvanceStringIndex(S, q, unicodeMatching).
    4. Else z is not null,
    5. 令 e 为 ? ToLength(? Get(splitter, "lastIndex")).
    6. 设置 e 为 min(e, size).
    7. 若 e = p, set q to AdvanceStringIndex(S, q, unicodeMatching).
    8. Else e ≠ p,
       1. 令 T 为 the String value equal to the substring of S consisting of the code units at indices p (inclusive) through q (exclusive).
       2. 执行 ! CreateDataProperty(A, ! ToString(lengthA), T).
       3. lengthA 增加 1
       4. 若 lengthA = lim，返回 A.
       5. 设置 p 为 e.
       6. 令 numberOfCaptures 为 ? ToLength(? Get(z, "length")).
       7. 设置 numberOfCaptures 为 max(numberOfCaptures - 1, 0).
       8. 令 i 为 1.
       9. 重复， while i ≤ numberOfCaptures,
          1. 令 nextCapture 为 ? Get(z, ! ToString(i)).
          2. 执行 ! CreateDataProperty(A, ! ToString(lengthA), nextCapture).
          3. i 增加 1
          4. lengthA 增加 1
          5. 若 lengthA = lim，返回 A.
       10. 设置 q 为 p.
20. 令 T 为 the String value equal to the substring of S consisting of the code units at indices p (inclusive) through size (exclusive).
21. 执行 ! CreateDataProperty(A, ! ToString(lengthA), T).
22. 返回 A.

The value of the name property of this function is "[Symbol.split]".

> 注 2 The @@split method ignores the value of the global and sticky properties of this RegExp object.

#### 21.2.5.13 获取 RegExp.prototype.sticky <div id="sec-get-regexp.prototype.sticky"></div>

RegExp.prototype.sticky is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R does not have an [[OriginalFlags]] internal slot，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags contains the code unit 0x0079 (LATIN SMALL LETTER Y)，返回 true.
6. 返回 false.

#### 21.2.5.14 RegExp.prototype.test ( S ) <div id="sec-regexp.prototype.test"></div>

将采取以下步骤：

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 令 string 为 ? ToString(S).
4. 令 match 为 ? RegExpExec(R, string).
5. 若 match 不是 null，返回 true; else return false

#### 21.2.5.15 RegExp.prototype.toString ( ) <div id="sec-regexp.prototype.tostring"></div>

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 令 pattern 为 ? ToString(? Get(R, "source")).
4. 令 flags 为 ? ToString(? Get(R, "flags")).
5. 令 result 为 the string-concatenation of "/", pattern, "/", and flags.
6. 返回 result.

> 注 The returned String has the form of a RegularExpressionLiteral that evaluates to another RegExp object with the same behaviour as this object.

#### 21.2.5.16 获取 RegExp.prototype.unicode <div id="sec-get-regexp.prototype.unicode"></div>

RegExp.prototype.unicode is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 R 为 the this value.
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R does not have an [[OriginalFlags]] internal slot，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags contains the code unit 0x0075 (LATIN SMALL LETTER U)，返回 true.
6. 返回 false

### 21.2.6 RegExp 实例属性 <div id="sec-properties-of-regexp-instances"></div>

RegExp instances are ordinary objects that inherit properties from the RegExp prototype object. RegExp instances have internal slots [[RegExpMatcher]], [[OriginalSource]], and [[OriginalFlags]]. The value of the [[RegExpMatcher]] internal slot is an implementation-dependent representation of the Pattern of the RegExp object.

> 注 Prior to ECMAScript 2015, RegExp instances were specified as having the own data properties source, global, ignoreCase, and multiline. Those properties are now specified as accessor properties of RegExp.prototype.

RegExp instances also have the following property:

#### 21.2.6.1 lastIndex <div id="sec-lastindex"></div>

The value of the lastIndex property specifies the String index at which to start the next match. It is coerced to an integer when used (see 21.2.5.2.2). This property shall have the attributes { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.