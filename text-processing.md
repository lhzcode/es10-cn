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

1. 用参数direction对Alternative求值，得到一个Matcher m1。
2. 返回 m.

产生式 Disjunction :: Alternative | Disjunction 如下解释执行：

1. 用参数direction对Alternative求值，得到一个Matcher m1。
2. 用参数direction对Disjunction求值，得到一个Matcher m2。
3. 返回一个内部Matcher闭包，它接受两个参数，一个状态x，一个延续c，并在计算时执行以下步骤:
   1. 调用 m1(x, c) 并且，令 r 为它自己的结果
   2. 若 r 不是 failure，返回 r.
   3. 调用 m2(x, c) 并且返回它自己的结果

> 注
>
> |正则表达式运算符将两种选择分开。模式首先尝试匹配左侧的Alternative（随后是正则表达式的后继）；如果失败，它将尝试匹配正确的Disjunction（紧随其后的是正则表达式）。如果左侧的Alternative，右侧的Disjunction和续集都具有选择点，则尝试尝试续集中的所有选择，然后再转到左侧的Alternative中的下一个选项。如果左侧“选择”中的选择已用尽，则尝试使用右侧“分离”而不是左侧“选择”。 |跳过的模式的一部分内的任何捕获括号。产生未定义的值，而不是字符串。因此，例如
>
> /a|ab/.exec("abc")
>
> 返回结果“ a”而不是“ ab”。此外，
>
> /((a)|(ab))((c)|(bc))/.exec("abc")
>
> 返回数组
>
> ["abc", "a", "a", undefined, "bc", undefined, "bc"]
>
> 并不是
>
> ["abc", "ab", undefined, "ab", "c", "c", undefined]
>
> 尝试两种选择的顺序与方向的值无关。

#### 21.2.2.4 选择项 <div id="sec-alternative"></div>

带有参数方向。

产生式 Alternative :: [empty] 如下解释执行：

1. 返回一个Matcher，它接受两个参数，即State x和Continuation c，并返回调用c（x）的结果。

产生式 Alternative :: Alternative Term 如下解释执行：

1. 用参数direction对Alternative求值，得到一个Matcher m1。
2. 用参数direction对Term求值，得到一个Matcher m2。
3. 若 direction 等于 +1，那么 
      1. 返回一个内部Matcher闭包，该闭包采用两个参数（State x和Continuation c），并在解释执行时执行以下步骤：
           2. 令 d 为一个接受State参数y并返回调用m2（y，c）的结果的Continuation。
           2. 调用 m1(x, d)并且返回它的结果
4. 否则，
           1. 断言：direction 等于 -1.
        2. 返回一个内部Matcher闭包，该闭包接受两个参数（State x和Continuation c），并在评估时执行以下步骤：
              1. 令 d 为一个采用State参数y并返回调用m1（y，c）的结果的Continuation。
              2. 调用 m2(x, d)并且返回它的结果

> 注：连续字词尝试同时匹配Input的连续部分。当direction等于+1时，如果左侧的Alternative，右侧的Term和正则表达式的后代都具有选择点，则在继续选择右侧Term中的下一个选项之前，将尝试该续集中的所有选择，并且所有在继续进行左侧“替代”中的下一个选择之前，请先尝试正确的“术语”中的选择。当direction等于-1时，Alternate和Term的评估顺序颠倒。

#### 21.2.2.5 匹配项 <div id="sec-term"></div>

带有参数direction.

产生式 Term :: Assertion 如下解释执行：

1. 返回一个内部Matcher闭包，该闭包接受两个参数（状态x和连续性c），并在评估时执行以下步骤：
  1. 解释执行 Assertion 以获得 AssertionTester t。
  2. 调用 t(x) 并且令 r 为得到的布尔值。
  3. 若 r 是 false，返回 failure.
  4. 调用 c(x)并且返回它的结果

> 注：AssertionTester 是独立于 direction 的。

产生式 Term :: Atom 如下解释执行：

1. 返回 Matcher，即是用参数 direction 对Atom求值的结果。

产生式 Term :: Atom Quantifier 如下解释执行：

1. 用参数direction对Atom求值，得到一个Matcher m。
2. 解释执行量词以获取三个结果：最小整数，最大整数（或∞）和布尔值greedy。
3. 断言：若 max 是 finite，那么最大值不小于最小值。
4. 令 parenIndex 为整个正则表达式中出现在该术语左侧的左捕获括号的数量。这是此术语之前或附带的Atom ::（GroupSpecifier Disjunction）解析节点的总数。
5. 令 parenCount 为 Atom 中左捕捉括号的数量。这是Atom包围的Atom ::（GroupSpecifier Disjunction）解析节点的总数。
6. 返回一个内部Matcher闭包，该闭包接受两个参数（状态x和连续性c），并在评估时执行以下步骤：
   1. 调用 RepeatMatcher(m, min, max, greedy, x, c, parenIndex, parenCount)并且返回它的结果

##### 21.2.2.5.1 RS: RepeatMatcher ( m, min, max, greedy, x, c, parenIndex, parenCount ) <div id="sec-runtime-semantics-repeatmatcher-abstract-operation"></div>

抽象操作RepeatMatcher采用八个参数，一个Matcher m，一个整数min，一个整数（或∞），一个布尔值greedy，一个State x，一个Continuation c，一个整数parenIndex和一个整数parenCount，并执行以下步骤。 ：

1. 若 max 是 zero，返回 c(x).
2. 令 d 为内部Continuation闭包，它接受一个State参数y并在解释执行时执行以下步骤：
   1. 若 min为零，y的endIndex等于x的endIndex，返回 failure.
   2. 若 min 是零, 令 min2 为零; 否则 令 min2 为 min - 1.
   3. 若 max 是 ∞, 令 max2 为 ∞; 否则 令 max2 为 max - 1.
   4. 调用 RepeatMatcher(m, min2, max2, greedy, y, c, parenIndex, parenCount)并且返回它的结果
3. 令 cap 为 x的捕获列表的副本。
4. 对于满足parenIndex <k和k≤parenIndex + parenCount的每个整数k，将cap [k]设置为undefined。
5. 令 e 为x的endIndex。
6. 令 xr 为 the State (e, cap).
7. 若 min 不是 zero，返回 m(xr, d).
   8. 若 greedy 是 false，那么 
     2. 调用 c(x)，并且令 z 为其结果。
     3. 若 z 不是 failure，返回 z.
  3. 调用 m(xr, d)并且返回它的结果
9. 调用 m(xr, d) 并且令 z 为它的结果。
10. 若 z 不是 failure，返回 z.
11. 调用 c(x)并且返回它的结果

> 注 1 
>
> 原子后面跟着量词的次数重复由量词指定的次数。量词可以是非贪婪的，在这种情况下，Atom模式在仍与续集匹配的情况下被重复最少的次数，也可以是贪婪的情况，在这种情况下，Atom样式在仍与续集匹配的情况下，被重复尽可能多的次数。 Atom模式是重复的，而不是它匹配的输入字符序列，因此Atom的不同重复可以匹配不同的输入子字符串。
>
> 注 2
>
> 如果Atom和正则表达式的后续都有选择点，则首先匹配Atom尽可能多的次数(如果非贪婪，则匹配次数越少越好)。在Atom的最后一次重复中，在进行下一个选择之前，将尝试后续中的所有选择。在原子的次(n - 1)次重复中，在进行下一个选择之前，尝试原子的最后(n)次重复中的所有选择；在这一点上，原子的重复或多或少是可能的；在进行(n - 1)st原子重复的下一个选择之前(同样，从尽可能少的或尽可能多的开始)，这些都已耗尽。
>
> 比较
>
> /a[a-z]{2,4}/.exec("abcdefghi")
>
> 返回“ abcde”
>
> /a[a-z]{2,4}?/.exec("abcdefghi")
>
> 返回“ abc”。
>
> 还考虑
>
> /(aa|aabaac|ba|b|c)*/.exec("aabaac")
>
> 根据上面的选择点排序，它返回数组
>
> ["aaba", "ba"]
>
> 而不是以下任何一个：
>
> ["aabaac", "aabaac"]
> ["aabaac", "c"]
>
> 选择点的上述排序可用于编写一个正则表达式，该正则表达式计算两个数字的最大公约数（以一元表示法表示）。以下示例计算的gcd为10和15：
>
> "aaaaaaaaaa,aaaaaaaaaaaaaaa".replace(/^(a+)\1*,\1+$/, "$1")
>
> 它以一元符号“ aaaaa”返回gcd。
>
> 注 3
>
> 每次重复Atom时，RepeatMatcher的步骤4都会清除Atom的捕获。我们可以在正则表达式中看到它的行为
>
> /(z)((a+)?(b+)?(c))*/.exec("zaacbbbcac")
>
> 返回数组
>
> ["zaacbbbcac", "z", "ac", "a", undefined, "c"]
>
> 并不是
>
> ["zaacbbbcac", "z", "ac", "a", "bbb", "c"]
>
> 因为最外面的*的每次迭代都会清除量化的Atom中包含的所有捕获的字符串，在这种情况下，该原子包括编号为2、3、4和5的捕获字符串。
>
> 注 4
>
> RepeatMatcher d闭包的第1步指出，一旦满足了最小重复次数，则不再考虑与空字符序列匹配的Atom扩展。这样可以防止正则表达式引擎陷入以下模式的无限循环：
>
> /(a*)*/.exec("b")
>
> 或者稍微复杂一点：
>
> /(a*)b\1+/.exec("baaaac")
>
> 返回数组
>
> ["b", ""]

#### 21.2.2.6 断言 <div id="sec-assertion"></div>

产生式 Assertion :: ^ 如下解释执行：

1. 返回内部AssertionTester闭包，它带有State参数x并在评估时执行以下步骤：
   1. 令 e 为x的endIndex。
   2. 若 e 是 zero，返回 true.
   3. 若 Multiline 是 false，返回 false.
   4. 若字符输入[e - 1]是LineTerminator之一，返回 true.
   5. 返回 false.

> 注：即使当y标记与模式一起使用时，^也始终只匹配输入的开头，或者(如果Multiline为真)匹配一行的开头。

产生式 Assertion :: $ 如下解释执行：

1. 返回一个内部AssertionTester闭包，它接受一个状态参数x，并在计算时执行以下步骤:
1. 令 e 为x的endIndex。
2. 若 e 等于 InputLength，返回 true.
3. 若 Multiline 是 false，返回 false.
4. 若字符输入[e] 是LineTerminator之一，返回 true.
5. 返回 false

产生式 Assertion :: \ b 如下解释执行：

1. 返回内部AssertionTester闭包，它带有State参数x并在评估时执行以下步骤：
  1. 令 e 为x的endIndex。
  2. 调用 IsWordChar(e - 1)，并且令 a 为布尔结果。
  3. 调用 IsWordChar(e)，并且令 b 为布尔结果。
  4. 若 a 是 true，并且 b 是 false，返回 true.
  5. 若 a 是 false，并且 b 是 true，返回 true.
  6. 返回 false.

产生式 Assertion :: \ B 如下解释执行：

1. 返回内部AssertionTester闭包，它带有State参数x并在评估时执行以下步骤：
1. 令 e 为x的endIndex。
2. 调用 IsWordChar(e - 1)，并且令 a 为布尔结果。
3. 调用 IsWordChar(e)，并且令 b 为布尔结果。
4. 若 a 是 true，并且 b 是 false，返回 false.
5. 若 a 是 false，并且 b 是 true，返回 false.
6. 返回 true.

产生式 Assertion :: ( ? = Disjunction ) 如下解释执行：

1. 用+1作为其direction参数评估Disjunction，以获得Matcher m。
2. 返回一个内部Matcher闭包，它使用两个参数（状态x和连续性c），并执行以下步骤：
1. 令 d 为始终返回其State参数作为成功MatchResult的Continuation。
2. 调用 m(x, d)，并且令 r 为其结果。
3. 若 r 是 failure，返回 failure.
4. 令 y 为 r 的 State。
5. 令 cap 为 y 捕获列表。
6. 令 xe 为x的endIndex。
7. 令 z 为 State (xe, cap).
8. 调用 c(z)并且返回它的结果

产生式 Assertion :: ( ? ! Disjunction ) 如下解释执行：

1. 用+1作为其direction参数评估Disjunction，以获得Matcher m。
2. 返回一个内部Matcher闭包，它使用两个参数（状态x和连续性c），并执行以下步骤：
1. 令 d 为始终返回其State参数作为成功MatchResult的Continuation。
2. 调用 m(x, d)，并且令 r 为其结果.
3. 若 r 不是 failure，返回 failure.
4. 调用 c(x)并且返回它的结果

产生式 Assertion :: ( ? <= Disjunction ) 如下解释执行：

1. 以 -1 作为 direction 参数求 Disjunction，以获得Matcher m。
2. 返回一个内部Matcher闭包，它使用两个参数（状态x和连续性c），并执行以下步骤：
     1. 令 d 为始终返回其State参数作为成功MatchResult的Continuation。
     2. 调用 m(x, d)，并且令 r 为其结果。
     3. 若 r 是 failure，返回 failure.
     4. 令 y 为 r 的 State。
     5. 令 cap 为 y 的捕获列表。
     6. 令 xe 为 x 的endIndex。
     7. 令 z 为 State (xe, cap).
     8. 调用 c(z)并且返回它的结果

产生式 Assertion :: ( ? <! Disjunction ) 如下解释执行：

1. 以 -1 作为 direction 参数求 Disjunction，以获得Matcher m。
2. 返回一个内部Matcher闭包，它使用两个参数（状态x和连续性c），并执行以下步骤：
   1. 令 d 为始终返回其State参数作为成功MatchResult的Continuation。
   2. 调用 m(x, d)，并且令 r 为其结果。
   3. 若 r 不是 failure，返回 failure.
   4. 调用 c(x)并且返回它的结果

##### 21.2.2.6.1 RS: WordCharacters ( ) <div id="sec-runtime-semantics-wordcharacters-abstract-operation"></div>

抽象操作WordCharacters执行以下步骤：

1. 令 A 为包含六十三个字符的一组字符：

   ```
   a b c d e f g h i j k l m n o p q r s t u v w x y z
   A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
   0 1 2 3 4 5 6 7 8 9 _
   ```

2. 令 U 为一个空集。
3. 对于不在A中Canonicalize（c）在A中的每个字符c，请将c添加到U中。
4. 断言：除非Unicode和IgnoreCase都为true，否则U为空。
5. 将集合U中的字符添加到集合A中。
6. 返回 A.

##### 21.2.2.6.2 RS: IsWordChar ( e ) <div id="sec-runtime-semantics-iswordchar-abstract-operation"></div>

抽象运算IsWordChar使用整数参数e并执行以下步骤：

1. 若 e is -1 or e 是 InputLength，返回 false.
2. 令 c 为字符输入[e].
3. 令 wordChars 为 ! WordCharacters() 的结果。
4. 若 c 在 wordChars 中，返回 true.
5. 返回 false.

#### 21.2.2.7 量词 <div id="sec-quantifier"></div>

产生式 Quantifier :: QuantifierPrefix 如下解释执行：

1. 评估QuantifierPrefix可获得两个结果：最小整数和最大整数（或∞）。
2. 返回三个结果min，max和true。

产生式 Quantifier :: QuantifierPrefix ? 如下解释执行：

1. 评估QuantifierPrefix可获得两个结果：最小整数和最大整数（或∞）。
2. 返回三个结果min，max和false。

产生式 QuantifierPrefix :: * 如下解释执行：

1. 返回两个结果为0和∞。

产生式 QuantifierPrefix :: + 如下解释执行：

1. 返回两个结果1和∞。

产生式 QuantifierPrefix :: ? 如下解释执行：

1. 返回两个结果0和1。

产生式 QuantifierPrefix :: { DecimalDigits } 如下解释执行：

1. 令 i 为DecimalDigits的MV（请参见11.8.3）。
2. 返回 i 和 i 两个结果。

产生式 QuantifierPrefix :: { DecimalDigits , } 如下解释执行：

1. 令 i 为DecimalDigits的MV。
2. 返回两个结果i和∞。

产生式 QuantifierPrefix :: { DecimalDigits , DecimalDigits } 如下解释执行：

1. 令 i 为第一个DecimalDigits的MV。
2. 令 j 为第二个DecimalDigits的MV。
3. 返回两个结果i和j。

#### 21.2.2.8 原子 <div id="sec-atom"></div>

带有参数direction.

产生式 Atom :: PatternCharacter 如下解释执行：

1. 令 ch 为由PatternCharacter匹配的字符。
2. 令 A 为包含字符ch的单元素CharSet。
3. 调用 CharacterSetMatcher(A, false, direction)并返回其Matcher结果。

产生式 Atom :: . 如下解释执行：

1. 若 DotAll 是 true，那么 
1. 令 A 为所有字符的集合。
2. 除此之外, 令 A 为除LineTerminator外的所有字符的集合。
3. 调用 CharacterSetMatcher(A, false, direction)并返回其Matcher结果。

产生式 Atom :: \ AtomEscape 如下解释执行：

1. 评估CharacterClass以获得CharSet A和布尔值反转。
2. 调用 CharacterSetMatcher(A, invert, direction)并返回其Matcher结果。

产生式 Atom :: ( GroupSpecifier Disjunction ) 如下解释执行：

1. 用参数direction对Disjunction求值，得到一个Matcher m。
2. 令 parenIndex 为整个正则表达式中位于此Atom左侧的左捕获括号的数量。这是此Atom之前或包含Atom ::（GroupSpecifier Disjunction）解析节点的总数。
3. 返回一个内部Matcher闭包，它使用两个参数（状态x和连续性c），并执行以下步骤：
    1. 令 d 为内部Continuation闭包，它接受一个State参数y并执行以下步骤：
        1. 令 cap 为y的捕获列表的副本。
        2. 令 xe 为x的endIndex。
        3. 令 ye 为 y 的 endIndex.
        4. 若 direction 等于 +1，那么 
            1. 断言：xe ≤ ye.
            2. 令 s 为一个新的列表，其元素是从索引xe（含）到ye（不含）的Input字符。
        5. 否则，
            1. 断言：direction 等于 -1.
            2. 断言：ye ≤ xe.
            3. 令 s 为一个新的列表，其元素是从ye（含）到xe（不含）的Input字符。
        6. 设置 cap[parenIndex + 1] 为 s.
        7. 令 z 为 State (ye, cap).
        8. 调用 c(z)并且返回它的结果
    14. 调用 m(x, d)并且返回它的结果

产生式 Atom :: ( ? : Disjunction ) 如下解释执行：

1. 返回 Matcher，即是用 direction 参数解析执行 Disjunction 的结果。

##### 21.2.2.8.1 RS: CharacterSetMatcher ( A, invert, direction ) <div id="sec-runtime-semantics-charactersetmatcher-abstract-operation"></div>

抽象操作CharacterSetMatcher接受三个参数，一个字符集 a，一个Boolean标志 invert，一个整数 direction，并执行以下步骤：

1. 返回一个内部Matcher闭包，它接受两个参数，一个状态x，一个延续c，并在计算时执行以下步骤：
   1. 令 e 为 x 的endIndex。
   2. 令 f 为 e + direction.
   3. 若 f < 0 or f > InputLength，返回 failure.
   4. 令 index 为 min(e, f).
   5. 令 ch 为字符输入[index].
   6. 令 cc 为 Canonicalize(ch).
   7. 若 invert 是 false，那么 
      1. 若集合A中不存在这样一个 Canonicalize(a) 是 cc，返回 failure.
   8. 否则，
      1. 断言：invert 是 true.
      2. 若集合A中不存在这样一个 Canonicalize(a) 是 cc，返回 failure.
   9. 令 cap 为 x 的 captures 列表。
   10. 令 y 为 State (f, cap)。
   11. 调用 c(y)并且返回它的结果。

##### 21.2.2.8.2 RS: Canonicalize ( ch ) <div id="sec-runtime-semantics-canonicalize-ch"></div>

规范化的抽象操作采用字符参数ch，并执行以下步骤:

1. 若 IgnoreCase 是 false，返回 ch.
2. 若 Unicode 是 true，那么 
     1. 如果Unicode字符数据库的CaseFolding.txt文件为ch提供了一个简单或常见的大小写折叠映射，则返回将该映射应用到ch的结果。
     2. 返回 ch.
3. 否则，
     1. 断言：ch 是 UTF-16 代码单元
     2. 令 s 为由单个代码单元ch组成的字符串值。
     3. 令 u 为与对String.prototype执行算法得到的结果相同。使用s作为这个值的toUpperCase。
     4. 断言：Type(u) 是 String.
     5. 若 u 不包含单个代码单元，返回 ch.
     6. 令 cu 为u的单个代码单元元素。
     7. 若 ch ≥ 128 的数值和 cu < 128 的数值，返回 ch.
     8. 返回 cu.

> 注 1
>
> 形式（Disjunction）的括号既可以将Disjunction模式的各个组成部分组合在一起，又可以保存匹配结果。结果可以用在反向引用中（\后跟一个非零的十进制数字），可以在替换字符串中引用，也可以从匹配内部过程的正则表达式中作为数组的一部分返回。要禁止捕获括号，请改用（ ? : Disjunction）形式。
>
> 注 2
>
> 形式（ ? = Disjunction）指定零宽度的正向超前。为了使它成功，“分离”中的模式必须在当前位置匹配，但是在匹配后继序列之前，当前位置不会前进。如果“分离”可以几种方式在当前位置匹配，则仅尝试第一个。与其他正则表达式运算符不同，它没有回溯到（？= form（此异常行为是从Perl继承的）。仅当Disjunction包含捕获括号并且模式的后继包含对那些捕获的反向引用时，才有意义。
>
> 例如，
>
> /(?=(a+))/.exec("baaabac")
>
> 在第一个b之后立即匹配空String，因此返回数组：
>
> ["", "aaa"]
>
> 为了说明缺乏回溯到前瞻性，请考虑：
>
> /(?=(a+))a*b\1/.exec("baaabac")
>
> 该表达式返回
>
> ["aba", "a"]
>
> 并不是：
>
> ["aaaba", "a"].
>
> 注 3
>
> 形式（?!Disjunction）指定零宽度的负前瞻。为了使它成功，“分离”内的模式必须在当前位置不匹配。在匹配续集之前，当前位置不会前进。析取可以包含捕获的括号，但是对它们的反向引用仅在析取本身内部才有意义。对模式中其他位置的这些捕获括号的反向引用始终返回未定义的，因为负前瞻必须失败才能使模式成功。例如，
>
> /(.*?)a(?!(a+)b\2c)\2(.*)/.exec("baaabaac")
>
> 查找a，而不是紧随其后的是a的正数n，a b，另一个n a（由第一个\ 2指定）和a。第二个\ 2在负前瞻之外，因此它与undefined匹配，因此总是成功。整个表达式返回数组：
>
> ["baaabaac", "ba", undefined, "abaac"]
>
> 注 4
>
> 在Unicode为true的情况下，如果大小写无关紧要，则在比较所有字符之前，立即使用Unicode标准提供的简单映射对所有字符进行隐式大小写折叠。简单映射始终映射到单个代码点，因此不会映射例如“ß”（U + 00DF）到“ SS”。但是，它可能会将基本拉丁语范围之外的代码点映射到例如“ ſ”（U + 017F）到“ s”内的字符。如果Unicode为false，则不会映射此类字符。这样可以防止Unicode代码点（例如U + 017F和U + 212A）匹配正则表达式（例如/ [a-z] / i），但它们将匹配/ [a-z] / ui。

##### 21.2.2.8.3 RS: UnicodeMatchProperty ( p ) <div id="sec-runtime-semantics-unicodematchproperty-p"></div>

抽象操作UnicodeMatchProperty采用参数p（它是Unicode代码点列表），并执行以下步骤：

1. 断言：p是Unicode代码点列表，与Unicode代码点列表相同，后者是表54或表55的“属性名称和别名”列中列出的Unicode属性名称或属性别名。
2. 令 c 为 在相应行的“规范属性名称”列中给出的p的规范属性名称。
3. 返回 c 的Unicode代码点列表。

实现必须支持表54和表55中列出的Unicode属性名称和别名。为确保互操作性，实现不得支持任何其他属性名称或别名。

> 注 1 
>
> 例如，Script_Extensions（属性名称）和scx（属性别名）有效，但script_extensions或Scx无效。
>
> 注 2 
>
> 列出的属性构成UTS18 RL1.2要求的超集

表54：非二进制Unicode属性别名及其规范属性名称

| 属性名称和别名           | 规范属性名称                                                 |
| ------------------------ | ------------------------------------------------------------ |
| `General_Category``gc`   | [`General_Category`](https://unicode.org/reports/tr18/#General_Category_Property) |
| `Script``sc`             | [`Script`](https://unicode.org/reports/tr24/#Script)         |
| `Script_Extensions``scx` | [`Script_Extensions`](https://unicode.org/reports/tr24/#Script_Extensions) |

表55：二进制Unicode属性别名及其规范属性名称

| 属性名称和别名                        | **规范属性名称**                                             |
| ------------------------------------- | ------------------------------------------------------------ |
| `ASCII`                               | [`ASCII`](https://unicode.org/reports/tr18/#General_Category_Property) |
| `ASCII_Hex_Digit``AHex`               | [`ASCII_Hex_Digit`](https://unicode.org/reports/tr44/#ASCII_Hex_Digit) |
| `Alphabetic``Alpha`                   | [`Alphabetic`](https://unicode.org/reports/tr44/#Alphabetic) |
| `Any`                                 | [`Any`](https://unicode.org/reports/tr18/#General_Category_Property) |
| `Assigned`                            | [`Assigned`](https://unicode.org/reports/tr18/#General_Category_Property) |
| `Bidi_Control``Bidi_C`                | [`Bidi_Control`](https://unicode.org/reports/tr44/#Bidi_Control) |
| `Bidi_Mirrored``Bidi_M`               | [`Bidi_Mirrored`](https://unicode.org/reports/tr44/#Bidi_Mirrored) |
| `Case_Ignorable``CI`                  | [`Case_Ignorable`](https://unicode.org/reports/tr44/#Case_Ignorable) |
| `Cased`                               | [`Cased`](https://unicode.org/reports/tr44/#Cased)           |
| `Changes_When_Casefolded``CWCF`       | [`Changes_When_Casefolded`](https://unicode.org/reports/tr44/#CWCF) |
| `Changes_When_Casemapped``CWCM`       | [`Changes_When_Casemapped`](https://unicode.org/reports/tr44/#CWCM) |
| `Changes_When_Lowercased``CWL`        | [`Changes_When_Lowercased`](https://unicode.org/reports/tr44/#CWL) |
| `Changes_When_NFKC_Casefolded``CWKCF` | [`Changes_When_NFKC_Casefolded`](https://unicode.org/reports/tr44/#CWKCF) |
| `Changes_When_Titlecased``CWT`        | [`Changes_When_Titlecased`](https://unicode.org/reports/tr44/#CWT) |
| `Changes_When_Uppercased``CWU`        | [`Changes_When_Uppercased`](https://unicode.org/reports/tr44/#CWU) |
| `Dash`                                | [`Dash`](https://unicode.org/reports/tr44/#Dash)             |
| `Default_Ignorable_Code_Point``DI`    | [`Default_Ignorable_Code_Point`](https://unicode.org/reports/tr44/#Default_Ignorable_Code_Point) |
| `Deprecated``Dep`                     | [`Deprecated`](https://unicode.org/reports/tr44/#Deprecated) |
| `Diacritic``Dia`                      | [`Diacritic`](https://unicode.org/reports/tr44/#Diacritic)   |
| `Emoji`                               | [`Emoji`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Emoji_Component`                     | [`Emoji_Component`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Emoji_Modifier`                      | [`Emoji_Modifier`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Emoji_Modifier_Base`                 | [`Emoji_Modifier_Base`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Emoji_Presentation`                  | [`Emoji_Presentation`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Extended_Pictographic`               | [`Extended_Pictographic`](https://unicode.org/reports/tr51/#Emoji_Properties) |
| `Extender``Ext`                       | [`Extender`](https://unicode.org/reports/tr44/#Extender)     |
| `Grapheme_Base``Gr_Base`              | [`Grapheme_Base`](https://unicode.org/reports/tr44/#Grapheme_Base) |
| `Grapheme_Extend``Gr_Ext`             | [`Grapheme_Extend`](https://unicode.org/reports/tr44/#Grapheme_Extend) |
| `Hex_Digit``Hex`                      | [`Hex_Digit`](https://unicode.org/reports/tr44/#Hex_Digit)   |
| `IDS_Binary_Operator``IDSB`           | [`IDS_Binary_Operator`](https://unicode.org/reports/tr44/#IDS_Binary_Operator) |
| `IDS_Trinary_Operator``IDST`          | [`IDS_Trinary_Operator`](https://unicode.org/reports/tr44/#IDS_Trinary_Operator) |
| `ID_Continue``IDC`                    | [`ID_Continue`](https://unicode.org/reports/tr44/#ID_Continue) |
| `ID_Start``IDS`                       | [`ID_Start`](https://unicode.org/reports/tr44/#ID_Start)     |
| `Ideographic``Ideo`                   | [`Ideographic`](https://unicode.org/reports/tr44/#Ideographic) |
| `Join_Control``Join_C`                | [`Join_Control`](https://unicode.org/reports/tr44/#Join_Control) |
| `Logical_Order_Exception``LOE`        | [`Logical_Order_Exception`](https://unicode.org/reports/tr44/#Logical_Order_Exception) |
| `Lowercase``Lower`                    | [`Lowercase`](https://unicode.org/reports/tr44/#Lowercase)   |
| `Math`                                | [`Math`](https://unicode.org/reports/tr44/#Math)             |
| `Noncharacter_Code_Point``NChar`      | [`Noncharacter_Code_Point`](https://unicode.org/reports/tr44/#Noncharacter_Code_Point) |
| `Pattern_Syntax``Pat_Syn`             | [`Pattern_Syntax`](https://unicode.org/reports/tr44/#Pattern_Syntax) |
| `Pattern_White_Space``Pat_WS`         | [`Pattern_White_Space`](https://unicode.org/reports/tr44/#Pattern_White_Space) |
| `Quotation_Mark``QMark`               | [`Quotation_Mark`](https://unicode.org/reports/tr44/#Quotation_Mark) |
| `Radical`                             | [`Radical`](https://unicode.org/reports/tr44/#Radical)       |
| `Regional_Indicator``RI`              | [`Regional_Indicator`](https://unicode.org/reports/tr44/#Regional_Indicator) |
| `Sentence_Terminal``STerm`            | [`Sentence_Terminal`](https://unicode.org/reports/tr44/#STerm) |
| `Soft_Dotted``SD`                     | [`Soft_Dotted`](https://unicode.org/reports/tr44/#Soft_Dotted) |
| `Terminal_Punctuation``Term`          | [`Terminal_Punctuation`](https://unicode.org/reports/tr44/#Terminal_Punctuation) |
| `Unified_Ideograph``UIdeo`            | [`Unified_Ideograph`](https://unicode.org/reports/tr44/#Unified_Ideograph) |
| `Uppercase``Upper`                    | [`Uppercase`](https://unicode.org/reports/tr44/#Uppercase)   |
| `Variation_Selector``VS`              | [`Variation_Selector`](https://unicode.org/reports/tr44/#Variation_Selector) |
| `White_Space``space`                  | [`White_Space`](https://unicode.org/reports/tr44/#White_Space) |
| `XID_Continue``XIDC`                  | [`XID_Continue`](https://unicode.org/reports/tr44/#XID_Continue) |
| `XID_Start``XIDS`                     | [`XID_Start`](https://unicode.org/reports/tr44/#XID_Start)   |

##### 21.2.2.8.4 RS: UnicodeMatchPropertyValue ( p, v ) <div id="sec-runtime-semantics-unicodematchpropertyvalue-p-v"></div>

抽象操作UnicodeMatchPropertyValue具有两个参数p和v，每个参数都是Unicode代码点列表，并执行以下步骤：

1. 断言：p是Unicode代码点列表，与Unicode代码点列表相同，后者是表54的“规范属性名称”列中列出的规范的，未混叠的Unicode属性名称。
2. 断言：v是Unicode代码点列表，与Unicode代码点列表相同，后者是表56或表57的“属性值和别名”列中列出的Unicode属性p的属性值或属性值别名。
3. 令 value 为 v 的规范属性值（在相应行的“规范属性值”列中给出）。
4. 返回Unicode代码点列表。

实现必须支持表56和表57中列出的Unicode属性值名称和别名。为确保互操作性，实现不得支持任何其他属性值名称或别名。

>注 1
>例如，Xpeo和Old_Persian是有效的Script_Extensions值，但xpeo和Old Persian不是。
>注 2
>此算法不同于UAX44中列出的符号值的匹配规则：不区分大小写，空格，U + 002D（减号）和U + 005F（下划线），并且不支持Is前缀。

表56：Unicode属性 [`General_Category`](https://unicode.org/reports/tr18/#General_Category_Property)的值别名和规范值。

| 属性值和别名                | 规范属性值              |
| --------------------------- | ----------------------- |
| `Cased_Letter``LC`          | `Cased_Letter`          |
| `Close_Punctuation``Pe`     | `Close_Punctuation`     |
| `Connector_Punctuation``Pc` | `Connector_Punctuation` |
| `Control``Cc``cntrl`        | `Control`               |
| `Currency_Symbol``Sc`       | `Currency_Symbol`       |
| `Dash_Punctuation``Pd`      | `Dash_Punctuation`      |
| `Decimal_Number``Nd``digit` | `Decimal_Number`        |
| `Enclosing_Mark``Me`        | `Enclosing_Mark`        |
| `Final_Punctuation``Pf`     | `Final_Punctuation`     |
| `Format``Cf`                | `Format`                |
| `Initial_Punctuation``Pi`   | `Initial_Punctuation`   |
| `Letter``L`                 | `Letter`                |
| `Letter_Number``Nl`         | `Letter_Number`         |
| `Line_Separator``Zl`        | `Line_Separator`        |
| `Lowercase_Letter``Ll`      | `Lowercase_Letter`      |
| `Mark``M``Combining_Mark`   | `Mark`                  |
| `Math_Symbol``Sm`           | `Math_Symbol`           |
| `Modifier_Letter``Lm`       | `Modifier_Letter`       |
| `Modifier_Symbol``Sk`       | `Modifier_Symbol`       |
| `Nonspacing_Mark``Mn`       | `Nonspacing_Mark`       |
| `Number``N`                 | `Number`                |
| `Open_Punctuation``Ps`      | `Open_Punctuation`      |
| `Other``C`                  | `Other`                 |
| `Other_Letter``Lo`          | `Other_Letter`          |
| `Other_Number``No`          | `Other_Number`          |
| `Other_Punctuation``Po`     | `Other_Punctuation`     |
| `Other_Symbol``So`          | `Other_Symbol`          |
| `Paragraph_Separator``Zp`   | `Paragraph_Separator`   |
| `Private_Use``Co`           | `Private_Use`           |
| `Punctuation``P``punct`     | `Punctuation`           |
| `Separator``Z`              | `Separator`             |
| `Space_Separator``Zs`       | `Space_Separator`       |
| `Spacing_Mark``Mc`          | `Spacing_Mark`          |
| `Surrogate``Cs`             | `Surrogate`             |
| `Symbol``S`                 | `Symbol`                |
| `Titlecase_Letter``Lt`      | `Titlecase_Letter`      |
| `Unassigned``Cn`            | `Unassigned`            |
| `Uppercase_Letter``Lu`      | `Uppercase_Letter`      |

表57：Unicode属性[`Script`](https://unicode.org/reports/tr24/#Script) 和[`Script_Extensions`](https://unicode.org/reports/ tr24 /＃Script_Extensions)

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

带有参数direction.

产生式 AtomEscape :: DecimalEscape 如下解释执行：

1. 评估DecimalEscape以获取整数n。
2. 断言：n ≤ NcapturingParens.
3. 调用 BackreferenceMatcher(n, direction)，并且返回其Matcher结果

产生式 AtomEscape :: CharacterEscape 如下解释执行：

1. 评估CharacterEscape以获取字符ch。
2. 令 A 为包含字符ch的单元素CharSet。
3. 调用 CharacterSetMatcher(A, false, direction)，并且返回其Matcher结果

产生式 AtomEscape :: CharacterClassEscape 如下解释执行：

1. 评估CharacterClassEscape以获取CharSetA。
2. 调用 CharacterSetMatcher(A, false, direction)，并且返回其Matcher结果

> 注：形式为\的转义序列，后跟一个非零的十进制数字n，它与第n组捕获括号（21.2.2.1）的结果匹配。如果正则表达式的捕获括号少于n个，则会出现错误。如果正则表达式具有n个或多个捕获括号，但第n个因未捕获任何东西而未定义，则后向引用始终会成功。

产生式 AtomEscape :: k GroupName 如下解释执行：

1. 在封闭模式中搜索RegExpIdentifierName的GroupSpecifier实例，该实例的StringValue等于GroupName中包含的RegExpIdentifierName的StringValue。
2. 断言：找到唯一的此类GroupSpecifier。
3. 令 parenIndex 为整个正则表达式中位于定位的GroupSpecifier左侧的左捕获括号的数量。这是位于或封闭所定位GroupSpecifier之前的Atom ::（GroupSpecifier Disjunction）解析节点的总数。
4. 调用 BackreferenceMatcher(parenIndex, direction)，并且返回其Matcher结果

##### 21.2.2.9.1 RS: BackreferenceMatcher ( n, direction ) <div id="sec-backreference-matcher"></div>

抽象操作BackreferenceMatcher接受两个参数，整数n和整数direction，并执行以下步骤：

1. 返回一个内部Matcher闭包，它使用两个参数（状态x和连续性c），并执行以下步骤：
   1. 令 cap 为 x 的捕获列表。
   2. 令 s 为 cap[n].
   3. 若 s 是 undefined，返回 c(x).
   4. 令 e 为x的endIndex。
   5. 令 len 为 s 中的元素数。
   6. 令 f 为 e + direction × len.
   7. 若 f < 0 或 f > InputLength，返回 failure.
   8. 令 g 为 min (e, f).
   9. 若存在一个介于0（含）和len（不含）之间的整数i，以使 Canonicalize（s [i]）不是与Canonicalize（Input [g + i]）相同的字符值，返回 failure.
   10. 令 y 为 State (f, cap).
   11. 调用 c(y)并且返回它的结果

#### 21.2.2.10 转义字符 <div id="sec-characterescape"></div>

CharacterEscape产品评估如下：

```
CharacterEscape ::
    ControlEscape
    c ControlLetter
    0 [lookahead ∉ DecimalDigit]
    HexEscapeSequence
    RegExpUnicodeEscapeSequence
    IdentityEscape
```

1. 令 cv 为此CharacterEscape的CharacterValue。
2. 返回字符值为cv的字符。

#### 21.2.2.11 转义十进制 <div id="sec-decimalescape"></div>

DecimalEscape生产评估如下：

```
DecimalEscape :: NonZeroDigit DecimalDigits
```

1. 返回此DecimalEscape的CapturingGroupNumber。

>注：如果\后跟一个十进制数字n（其第一位数字不为0），则转义序列被视为反向引用。如果n大于整个正则表达式中左捕获括号的总数，则会出现错误。

#### 21.2.2.12 转义字符类 <div id="sec-characterclassescape"></div>

产生式 CharacterClassEscape :: d 如下解释执行：

1. 返回十个字符的字符集，包含字符0到9（含0和9）。

产生式 CharacterClassEscape :: D 如下解释执行：

1. 返回由CharacterClassEscape :: d返回的集合中未包括的所有字符的集合。

产生式 CharacterClassEscape :: s 如下解释执行：

1. 返回包含WhiteSpace或LineTerminator产品右侧的字符的字符集。

产生式 CharacterClassEscape :: S 如下解释执行：

1. 返回由CharacterClassEscape ::: s返回的所有字符集中的字符集。

产生式 CharacterClassEscape :: w 如下解释执行：

1. 返回WordCharacters（）返回的所有字符的集合。

产生式 CharacterClassEscape :: W 如下解释执行：

1. 返回CharacterClassEscape :: w返回的集合中未包括的所有字符的集合。

生产的CharacterClassEscape :: p {UnicodePropertyValueExpression}通过返回CharSet进行评估，该CharSet包含UnicodePropertyValueExpression返回的CharSet中包含的所有Unicode代码点。

生产的CharacterClassEscape :: P {UnicodePropertyValueExpression}通过返回包含所有未包含在UnicodePropertyValueExpression返回的CharSet中的所有Unicode代码点的CharSet进行评估。

产生式 UnicodePropertyValueExpression :: UnicodePropertyName = UnicodePropertyValue 如下解释执行：

1. 令 ps 为UnicodePropertyName的SourceText。
2. 令 p 为 ! UnicodeMatchProperty(ps).
3. 断言：p是表54的“属性名称和别名”列中列出的Unicode属性名称或属性别名。
4. 令 vs 为UnicodePropertyValue的SourceText。
5. 令 v 为 ! UnicodeMatchPropertyValue(p, vs).
6. 返回包含所有Unicode代码点的字符集的CharSet，这些字符集的字符数据库定义包含值为v的属性p。

产生式 UnicodePropertyValueExpression :: LoneUnicodePropertyNameOrValue 如下解释执行：

1. 令 s 为LoneUnicodePropertyNameOrValue的SourceText。
2. 若 ! UnicodeMatchPropertyValue("General_Category", s)与Unicode代码点列表相同，后者是表56的“属性值和别名”列中列出的Unicode常规类别或常规类别别名的名称，然后
  1. 返回 包含所有Unicode代码点的字符集的字符集，其字符数据库定义包括值为s的属性“ General_Category”。
3. 令 p 为 ! UnicodeMatchProperty(s).
4. 断言：p是表55的“属性名称和别名”列中列出的二进制Unicode属性或二进制属性别名。
5. 返回包含所有Unicode代码点的字符集的字符集，这些字符集的字符数据库定义包含值为“ True”的属性p。

#### 21.2.2.13 字符类 <div id="sec-characterclass"></div>

产生式 CharacterClass :: [ ClassRanges ] 如下解释执行：

1. 评估ClassRanges以获得CharSetA。
2. 返回两个结果A和False。

产生式 CharacterClass :: [ ^ ClassRanges ] 如下解释执行：

1. 评估ClassRanges以获得CharSetA。
2. 返回两个结果A和true。

#### 21.2.2.14 字符范围集 <div id="sec-classranges"></div>

产生式 ClassRanges :: [empty] 如下解释执行：

1. 返回空的CharSet。

产生式 ClassRanges :: NonemptyClassRanges 如下解释执行：

1. 返回CharSet是评估NonemptyClassRanges的结果。

#### 21.2.2.15 非空字符范围集 <div id="sec-nonemptyclassranges"></div>

产生式 NonemptyClassRanges :: ClassAtom 如下解释执行：

1. 返回CharSet是评估ClassAtom的结果。

产生式 NonemptyClassRanges :: ClassAtom NonemptyClassRangesNoDash 如下解释执行：

1. 评估ClassAtom以获得CharSetA。

2. 评估NonemptyClassRangesNoDash以获得CharSetB。
3. 返回字符集A和B的并集。

产生式 NonemptyClassRanges :: ClassAtom - ClassAtom ClassRanges 如下解释执行：

1. 评估第一个ClassAtom以获得CharSetA。
2. 评估第二个ClassAtom以获得CharSetB。
3. 评估ClassRanges以获得CharSetC。
4. 调用 CharacterRange(A, B)，并且令 D 为 得到的CharSet。
5. 返回字符集D和C的并集。

##### 21.2.2.15.1 RS: CharacterRange ( A, B ) <div id="sec-runtime-semantics-characterrange-abstract-operation"></div>

抽象操作CharacterRange采用两个CharSet参数A和B并执行以下步骤：

1. 断言：A和B都只包含一个字符。
2. 令 a 为CharSet A中的一个字符。
3. 令 b 为CharSet B中的一个字符。
4. 令 i 为字符a的字符值。
5. 令 j 为字符b的字符值。
6. 断言：i ≤ j.
7. 返回包含所有编号为i到j（包括i）的字符的集合。

#### 21.2.2.16 无连接符非空字符范围集 <div id="sec-nonemptyclassrangesnodash"></div>

产生式 NonemptyClassRangesNoDash :: ClassAtom 如下解释执行：

1. 返回 CharSet 是评估ClassAtom的结果。

产生式 NonemptyClassRangesNoDash :: ClassAtomNoDash NonemptyClassRangesNoDash 如下解释执行：

1. 评估ClassAtomNoDash以获取CharSetA。
2. 评估NonemptyClassRangesNoDash以获得CharSetB。
3. 返回字符集A和B的并集

产生式 NonemptyClassRangesNoDash :: ClassAtomNoDash - ClassAtom ClassRanges 如下解释执行：

1. 评估ClassAtomNoDash以获取CharSetA。
2. 评估ClassAtom以获得CharSetB。
3. 评估ClassRanges以获得CharSetC。
4. 调用 CharacterRange(A, B)，并且令 D 为得到的CharSet。
5. 返回字符集D和C的并集。

> 注 1 
>
> ClassRanges可以扩展为单个ClassAtom和/或两个以虚线分隔的ClassAtom范围。在后一种情况下，ClassRanges包括第一个ClassAtom和第二个ClassAtom之间的所有字符（包括首尾）。如果ClassAtom不代表单个字符（例如，如果是\ w），或者第一个ClassAtom的字符值大于第二个ClassAtom的字符值，则会发生错误。
>
> 注 2 
>
> 即使模式忽略大小写，在确定哪些字符属于该范围时，范围两端的大小写也很重要。因此，例如，模式/ [EF] / i仅匹配字母E，F，e和f，而模式/ [Ef] / i则匹配Unicode Basic Latin块中的所有大小写字母，例如以及符号[，\，]，^，_和`。
>
> 注 3
>
> -字符可以按字面意义对待，也可以表示范围。如果它是ClassRanges的第一个或最后一个字符，范围说明的开始或结束限制，或者紧随范围说明，则将按字面意义对待它。

#### 21.2.2.17 字符类原子 <div id="sec-classatom"></div>

产生式 ClassAtom :: - 如下解释执行：

1. 返回包含单个字符的字符集-U + 002D（减号）。

产生式 ClassAtom :: ClassAtomNoDash 如下解释执行：

1. 返回 CharClas s是评估ClassAtomNoDash的结果。

#### 21.2.2.18 非连接符字符类原子 <div id="sec-classatomnodash"></div>

产生式 ClassAtomNoDash :: SourceCharacter but not one of \ or ] or - 如下解释执行：

1. 返回包含与SourceCharacter匹配的字符的CharSet。

产生式 ClassAtomNoDash :: \ ClassEscape 如下解释执行：

1. 返回CharSet是评估ClassEscape的结果。

#### 21.2.2.19 字符可用转义 <div id="sec-classescape"></div>

ClassEscape产品评估如下：

```
ClassEscape :: b
ClassEscape :: -
ClassEscape :: CharacterEscape
```

1. 令 cv 为此ClassEscape的CharacterValue。
2. 令 c 为字符值为cv的字符。
3. 返回包含单个字符的字符集c。

```
ClassEscape :: CharacterClassEscape
```

1. 返回 CharSet 是评估CharacterClassEscape的结果。

> 注
>
> ClassAtom可以使用正则表达式其余部分中允许的任何转义序列，但\ b，\ B和反向引用除外。在CharacterClass内部，\ b表示退格字符，而\ B和反向引用会引发错误。在ClassAtom内部使用反向引用会导致错误。

### 21.2.3 RegExp 构造器 <div id="sec-regexp-constructor"></div>

RegExp构造函数：

- 是内部对象％RegExp％。
- 是全局对象的RegExp属性的初始值。
- 当作为函数而不是构造函数调用时，创建并初始化一个新的RegExp对象。因此，函数调用RegExp（…）等效于具有相同参数的对象创建表达式new RegExp（…）。
- 设计为可归类的。它可以用作类定义的extends子句的值。打算继承指定的RegExp行为的子类构造函数必须包括对RegExp构造函数的超级调用，以使用必需的内部插槽创建和初始化子类实例。

#### 21.2.3.1 RegExp ( pattern, flags ) <div id="sec-regexp-pattern-flags"></div>

将采取以下步骤：

1. 令 patternIsRegExp 为 ? IsRegExp(pattern).
2. 若 NewTarget 是 undefined，那么 
   1. 令 newTarget 为 the active function object.
   2. 若 patternIsRegExp is true and flags 是 undefined，那么 
        1. 令 patternConstructor 为 ? Get(pattern, "constructor").
        2. 若 SameValue(newTarget, patternConstructor) 是 true，返回 pattern.
8. 否则，令 newTarget 为 NewTarget.
4. 若 Type(pattern) 是 Object，并且 pattern 有 [[RegExpMatcher]] 内置插槽，那么 
    1. 令 P 为 pattern.[[OriginalSource]].
    2. 若 flags 是 undefined, 令 F 为 pattern.[[OriginalFlags]].
    3. 否则，令 F 为 flags.
5. 否则若 patternIsRegExp 是 true，那么 
   1. 令 P 为 ? Get(pattern, "source").
   2. 若 flags 是 undefined，那么 
   3. 令 F 为 ? Get(pattern, "flags").
   4. 否则，令 F 为 flags.
6. 否则，
   1. 令 P 为 pattern.
   2. 令 F 为 flags.
7. 令 O 为 ? RegExpAlloc(newTarget).
8. 返回 ? RegExpInitialize(O, P, F).

> 注
>
> 如果使用StringLiteral提供模式，则在RegExp处理字符串之前执行通常的转义序列替换。
> 如果模式必须包含一个要被RegExp识别的转义序列，则必须在StringLiteral中转义任何U+005C(反向SOLIDUS)代码点，以防止在形成StringLiteral内容时将它们删除

#### 21.2.3.2 RegExp构造器的抽象操作 <div id="sec-abstract-operations-for-the-regexp-constructor"></div>

##### 21.2.3.2.1 RS: RegExpAlloc ( newTarget ) <div id="sec-regexpalloc"></div>

当调用带有newTarget参数的抽象操作RegExpAlloc时，将采取以下步骤：

1. 令 obj 为 ? OrdinaryCreateFromConstructor(newTarget, "%RegExpPrototype%", « [[RegExpMatcher]],[[OriginalSource]], [[OriginalFlags]] »). 
2. 执行 ! DefinePropertyOrThrow(obj, "lastIndex", PropertyDescriptor { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }). 
3. 返回 obj.

##### 21.2.3.2.2 RS: RegExpInitialize ( obj, pattern, flags ) <div id="sec-regexpinitialize"></div>

当调用带有参数obj、模式和标志的抽象操作RegExpInitialize时，将采取以下步骤：

1. 若 pattern 是 undefined, 令 P 为空字符串.
2.  否则，令 P 为 ? ToString(pattern).
3. 若 flags 是 undefined, 令 F 为空字符串.
4.  否则， 令 F 为 ? ToString(flags).
5. 若 F 包含除"g", "i", "m", "s", "u", or "y"或者它包含相同的代码单元不止一次，抛出 SyntaxError 异常.
6. 若 F 包括 "u", 令 BMP 为 false；否则令 BMP 为 true.
7. 若 BMP 是 true，那么 
   1. 使用21.2.1中的语法解析P，并将其16位元素解释为Unicode BMP编码点。UTF-16解码不应用于元素。
      解析的目标符号是Pattern[~U， ~N]。如果解析的结果包含GroupName，则使用目标符号模式[~U， +N]重新解析，并使用此结果。抛出SyntaxError异常如果P不符合语法,如果任何元素的P被解析不匹配,或是否存在任何早期的错误条件。
   2. 令 patternCharacters 为一个列表，它的元素是P的代码单元元素。
8.  否则，
   1. 使用21.2.1中的语法解析P，并将P解释为UTF-16编码的Unicode编码点(6.1.4)。解析的目标符号是Pattern[+U， +N]。抛出SyntaxError异常如果P不符合语法,如果任何元素的P被解析不匹配,或是否存在任何早期的错误条件。
   2. 令 patternCharacters 为一个列表，它的元素是将UTF-16解码应用到P的元素序列后产生的代码点。
9. 设置 obj.[[OriginalSource]] 为 P.
10. 设置 obj.[[OriginalFlags]] 为 F.
11. 设置 obj.[[RegExpMatcher]] 为使用模式字符作为源字符值的模式列表，并使用F作为标志参数，通过应用21.2.2中提供的语义来评估P的上述解析的内部过程。
12. 执行 ? Set(obj, "lastIndex", 0, true).
13. 返回 obj.

##### 21.2.3.2.3 RS: RegExpCreate ( P, F ) <div id="sec-regexpcreate"></div>

当调用带有参数P和F的抽象操作RegExpCreate时，将采取以下步骤：

1. 令 obj 为 ? RegExpAlloc(%RegExp%).
2. 返回 ? RegExpInitialize(obj, P, F).

##### 21.2.3.2.4 RS: EscapeRegExpPattern ( P, F ) <div id="sec-escaperegexppattern"></div>

调用带有参数P和F的EscapeRegExpPattern抽象操作时，发生以下情况:

1. 假设S是一个模式\[~U]\(模式\[+U]，如果F包含"U")形式的字符串，它等价于P, P被解释为UTF-16编码的Unicode编码点(6.1.4)，其中某些编码点如下所述转义。S可能与P相同，也可能不相同；但是，将S求值为模式\[~U\]\(如果F包含"U"，则模式[+U])所产生的内部过程必须与构造对象的[[RegExpMatcher]]内部槽所给出的内部过程具有相同的行为。对这个抽象操作使用相同的P和F值的多次调用必须产生相同的结果。
2. 模式中出现的代码点/或任何LineTerminator必须在S中转义，以确保可以将“ /”，S，“ /”和F的字符串连接解析为（在适当的词法上下文中）为RegularExpressionLiteral，其行为与构造的正则表达式相同。例如，如果P为“ /”，则S可以为“ \ /”或“ \ u002F”，但不能为“ /”，因为///后跟F将被解析为SingleLineComment而不是A RegularExpressionLiteral。如果P是空字符串，则可以通过使S为“（？:)”来满足此规范。
3. 返回 S.

### 21.2.4 RegExp 构造器属性 <div id="sec-properties-of-the-regexp-constructor"></div>

RegExp构造函数：

- 有一个[[Prototype]]内部插槽，其值是固有对象％FunctionPrototype％。
- 具有以下属性：

#### 21.2.4.1 RegExp.prototype <div id="sec-regexp.prototype"></div>

RegExp.prototype的初始值为内部对象％RegExpPrototype％。

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 21.2.4.2 get RegExp [ @@species ] <div id="sec-get-regexp-@@species"></div>

RegExp [@@species]是一个访问器属性，其设置的访问器功能未定义。它的get访问器功能执行以下步骤：

1. 返回 this 值。

该函数的名称属性的值为“ get [Symbol.species]”。

> 注：RegExp原型方法通常使用其this对象的构造函数来创建派生对象。但是，子类构造函数可以通过重新定义其@@species属性来覆盖该默认行为。

### 21.2.5 RegExp 原型对象属性 <div id="sec-properties-of-the-regexp-prototype-object"></div>

RegExp原型对象：

- 是内部对象％RegExpPrototype％。
- 是一个普通的对象。
- 不是RegEx实例，并且没有[[RegExp Matcher]]内部插槽或RegExp实例对象的任何其他内部插槽。
- 有一个[[Prototype]]内部插槽，其值是固有对象％ObjectPrototype％。

> 注：RegExp原型对象没有自己的valueOf属性。但是，它从Object原型对象继承了valueOf属性。

#### 21.2.5.1 RegExp.prototype.constructor <div id="sec-regexp.prototype.constructor"></div>

RegExp.prototype.constructor的初始值为内部对象％RegExp％。

#### 21.2.5.2 RegExp.prototype.exec ( string ) <div id="sec-regexp.prototype.exec"></div>

与正则表达式执行字符串的正则表达式匹配，并返回包含匹配结果的Array对象；如果字符串不匹配，则返回null。

在字符串ToString（string）中搜索正则表达式模式，如下所示：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R 没有 [[RegExpMatcher]] 内部插槽, 抛出 TypeError 异常.
4. 令 S 为 ? ToString(string).
5. 返回 ? RegExpBuiltinExec(R, S).

##### 21.2.5.2.1 RS: RegExpExec ( R, S ) <div id="sec-regexpexec"></div>

具有参数R和S的抽象操作RegExpExec执行以下步骤：

1. 断言：Type(R) 是 Object。
2. 断言：Type(S) 是 String。
3. 令 exec 为 ? Get(R, "exec").
4. 若 IsCallable(exec) 是 true，那么 
   1. 令 result 为 ? Call(exec, R, « S »).
   2. 若 Type(result) 不是 Object 或 Null, 抛出 TypeError 异常.
   3. 返回 result.
5. 若 R 没有 [[RegExpMatcher]] 内部插槽, 抛出 TypeError 异常.
6. 返回 ? RegExpBuiltinExec(R, S).

> 注：如果找不到可调用的exec属性，则该算法将退回到尝试使用内置RegExp匹配算法的过程。这为为先前版本编写的代码提供了兼容的行为，在先前版本中，大多数使用正则表达式的内置算法未执行exec的动态属性查找。

##### 21.2.5.2.2 RS: RegExpBuiltinExec ( R, S ) <div id="sec-regexpbuiltinexec"></div>

具有参数R和S的抽象操作RegExpBuiltinExec执行以下步骤：

1. 断言：R是初始化的RegExp实例。
2. 断言：Type(S) 是 String。
3. 令 length 为S中的代码单位数。
4. 令 lastIndex 为 ? ToLength(? Get(R, "lastIndex")).
5. 令 flags 为 R.[[OriginalFlags]].
6. 若 flags 包含 "g", 令 global 为 true, else 令 global 为 false.
7. 若 flags 包含 "y", 令 sticky 为 true, else 令 sticky 为 false.
8. 若 global 是 false，并且 sticky 是 false, 设置 lastIndex 为 0.
9. 令 matcher 为 R.[[RegExpMatcher]].
10. 若 flags 包含 "u", 令 fullUnicode 为 true，否则 令 fullUnicode 为 false.
11. 令 matchSucceeded 为 false.
12. 重复，直到 matchSucceeded 是 false
    1. 若 lastIndex > length，那么 
       1. 若 global 是 true，或 sticky 是 true，那么 
          1. 执行 ? Set(R, "lastIndex", 0, true).
       2. 返回 null.
    2. 令 r 为 matcher(S, lastIndex).
    3. 若 r 是 failure，那么 
       1. 若 sticky 是 true，那么 
          1. 执行 ? Set(R, "lastIndex", 0, true).
          2. 返回 null.
       2. 设置 lastIndex 为 AdvanceStringIndex(S, lastIndex, fullUnicode).
    4.  否则，
       1. 断言：r 是 State.
       2. 设置 matchSucceeded 为 true.
13. 令 e 为 r 的 endIndex 值。
14. 若 fullUnicode 是 true，那么 
    1. e是输入字符列表的索引，由S派生，由匹配器匹配。假设eUTF是S中与Input元素e上的字符相对应的最小索引。如果e大于或等于Input中的元素数，则eUTF是S中的代码单元数。
    2. 设置 e 为 eUTF.
15. 若 global 是 true，或 sticky 是 true，那么 
    1. 执行 ? Set(R, "lastIndex", e, true).
16. 令 n 为r的捕获列表中元素的数量。 （该值与21.2.2.1的NcapturingParens相同。）
17. 断言: n < 232 - 1.
18. 令 A 为 ! ArrayCreate(n + 1).
19. 断言: A的“length”属性的值为n + 1。
20. 执行 ! CreateDataProperty(A, "index", lastIndex).
21. 执行 ! CreateDataProperty(A, "input", S).
22. 令matchSubstr为匹配的子字符串（即偏移量lastIndex包含在内和偏移量e排除之间的S部分）。
23. 执行 ! CreateDataProperty(A, "0", matchedSubstr).
24. 若R包含任何GroupName，那么 
    1. 令 groups 为 ObjectCreate(null).
25.  否则，
    1. 令 groups 为 undefined.
26. 执行 ! CreateDataProperty(A, "groups", groups).
27. 对于每个大于 i 且 i ≤ n 的整数 i，执行
    1. 令 captureI 为第 i 个 r 的捕获列表的元素。
    2. 若 captureI 是 undefined, 令 capturedValue 为 undefined.
    3. 否则若 fullUnicode 是 true，那么 
       1. 断言：captureI是代码点列表。
       2. 令 capturedValue 为 String 值，其代码单位是captureI的代码点的UTF16Encoding。
    4. 否则 fullUnicode 为 false，
       1. 断言：captureI是代码单元列表。
       2. 令 capturedValue 为由captureI的代码单元组成的String值。
    5. 执行 ! CreateDataProperty(A, ! ToString(i), capturedValue).
    6. 若使用GroupName定义R的第i个捕获，那么 
       1. 令 s 为相应的RegExpIdentifierName的StringValue。
       2. 执行 ! CreateDataProperty(groups, s, capturedValue).
28. 返回 A.

##### 21.2.5.2.3 AdvanceStringIndex ( S, index, unicode ) <div id="sec-advancestringindex"></div>

具有参数S，索引和unicode的抽象操作AdvanceStringIndex执行以下步骤：

1. 断言：Type(S) 是 String。
2. 断言：index是一个整数，使得 0 ≤ index ≤ 253 - 1.
3. 断言：Type(unicode) 是 Boolean。
4. 若 unicode 是 false，返回 index + 1.
5. 令 length 为S中的代码单位数。
6. 若 index + 1 ≥ length，返回 index + 1.
7. 令 first 为S中索引index处的代码单元的数值。
8. 若 first < 0xD800 or first > 0xDBFF，返回 index + 1.
9. 令 second 为S中索引index +1处的代码单元的数值。
10. 若 second < 0xDC00 或 second > 0xDFFF，返回 index + 1.
11. 返回 index + 2.

#### 21.2.5.3 获取 RegExp.prototype.dotAll <div id="sec-get-regexp.prototype.dotAll"></div>

RegExp.prototype.dotAll是一个访问器属性，其设置的访问器函数未定义。它的get访问器功能执行以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R 没有 [[OriginalFlags]] 内部插槽，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags 包含代码单元 0x0073 (拉丁文小写字母S)，返回 true.
6. 返回 false

#### 21.2.5.4 获取 RegExp.prototype.flags <div id="sec-get-regexp.prototype.flags"></div>

RegExp.prototype.flags是一个访问器属性，其设置的访问器功能未定义。它的get访问器功能执行以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 令 result 为空字符串.
4. 令 global 为 ToBoolean(? Get(R, "global")).
5. 若 global 是 true, 将代码单元0x0067（拉丁文小写字母G）附加为结果的最后一个代码单元。
6. 令 ignoreCase 为 ToBoolean(? Get(R, "ignoreCase")).
7. 若 ignoreCase 是 true, 将代码单元0x0069（拉丁文小写字母I）附加为结果的最后一个代码单元。
8. 令 multiline 为 ToBoolean(? Get(R, "multiline")).
9. 若 multiline 是 true, 将代码单元0x006D（拉丁文小写字母M）附加为结果的最后一个代码单元。
10. 令 dotAll 为 ToBoolean(? Get(R, "dotAll")).
11. 若 dotAll is true, 将代码单元0x0073（拉丁文小写字母S）附加为结果的最后一个代码单元。
12. 令 unicode 为 ToBoolean(? Get(R, "unicode")).
13. 若 unicode is true, 将代码单元0x0075（拉丁文小写字母U）附加为结果的最后一个代码单元。
14. 令 sticky 为 ToBoolean(? Get(R, "sticky")).
15. 若 sticky is true, 将代码单元0x0079（拉丁文小写字母Y）附加为结果的最后一个代码单元。
16. 返回 result.

#### 21.2.5.5 获取 RegExp.prototype.global <div id="sec-get-regexp.prototype.global"></div>

RegExp.prototype.global是一个访问器属性，其设置的访问器功能未定义。它的get访问器功能执行以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R 没有 [[OriginalFlags]] 内部插槽，那么 
   1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
   2. 除此之外, 抛出 TypeError 异常.
4. 除此之外, 抛出 TypeError 异常.
5. 令 flags 为 R.[[OriginalFlags]].
6. 若包含代码单元0x0067（拉丁文小写字母G），返回 true.
7. 返回 false.

#### 21.2.5.6 获取 RegExp.prototype.ignoreCase <div id="sec-get-regexp.prototype.ignorecase"></div>

RegExp.prototype.ignoreCase是一个访问器属性，其设置的访问器功能未定义。它的get访问器功能执行以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R 没有 [[OriginalFlags]] 内部插槽，那么 
   1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
   2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags 包含代码单元0x0069（拉丁文小写字母I），返回 true.
6. 返回 false.

#### 21.2.5.7 RegExp.prototype [ @@match ] ( string ) <div id="sec-regexp.prototype-@@match"></div>

用参数字符串调用@@match方法时，将采取以下步骤：

1. 令 rx 为 this 值。
2. 若 Type(rx) 不是 Object, 抛出 TypeError 异常.
3. 令 S 为 ? ToString(string).
4. 令 global 为 ToBoolean(? Get(rx, "global")).
5. 若 global 是 false，那么 
   1. 返回 ? RegExpExec(rx, S).
6. 否则 global 是 true,
   1. 令 fullUnicode 为 ToBoolean(? Get(rx, "unicode")).
   2. 执行 ? Set(rx, "lastIndex", 0, true).
   3. 令 A 为 ! ArrayCreate(0).
   4. 令 n 为 0.
   5. 重复，
      1. 令 result 为 ? RegExpExec(rx, S).
      2. 若 result 是 null，那么 
         1. 若 n = 0，返回 null.
         2. 返回 A.
      3. 否则 result 不是 null,
         1. 令 matchStr 为 ? ToString(? Get(result, "0")).
         2. 令 status 为 CreateDataProperty(A, ! ToString(n), matchStr).
         3. 断言：status 是 true.
         4. 若 matchStr 是空字符串，那么 
            1. 令 thisIndex 为 ? ToLength(? Get(rx, "lastIndex")).
            2. 令 nextIndex 为 AdvanceStringIndex(S, thisIndex, fullUnicode).
            3. 执行 ? Set(rx, "lastIndex", nextIndex, true).
         5. n增加

该函数的名称属性的值为“ [Symbol.match]”。

> 注：IsRegExp抽象操作使用@@match属性来标识具有正则表达式基本行为的对象。不存在@@match属性或该属性的值不将布尔值强制转换为true表示该对象不打算用作正则表达式对象。

#### 21.2.5.8 获取 RegExp.prototype.multiline <div id="sec-get-regexp.prototype.multiline"></div>

RegExp.prototype.multiline是一个访问器属性，其设置的访问器函数未定义。它的get访问器功能执行以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R 没有 [[OriginalFlags]] 内部插槽，那么 
   1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
   2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags 包含代码单元0x006D（拉丁文小写字母M），返回 true.
6. 返回 false.

#### 21.2.5.9 RegExp.prototype [ @@replace ] ( string, replaceValue ) <div id="sec-regexp.prototype-@@replace"></div>

当使用参数 string 和 replaceValue 调用 @@replace 方法时，将采取以下步骤：

1. 令 rx 为 this 值。
2. 若 Type(rx) 不是 Object, 抛出 TypeError 异常.
3. 令 S 为 ? ToString(string).
4. 令 lengthS 为S中的代码单元元素的数量。
5. 令 functionalReplace 为 IsCallable(replaceValue).
6. 若 functionalReplace 是 false，那么 
   1. 设置 replaceValue 为 ? ToString(replaceValue).
7. 令 global 为 ToBoolean(? Get(rx, "global")).
8. 若 global 是 true，那么 
   1. 令 fullUnicode 为 ToBoolean(? Get(rx, "unicode")).
   2. 执行 ? Set(rx, "lastIndex", 0, true).
9. 令 results 为一个新的空列表。
10. 令 done 为 false.
11. 重复, 直到 done 是 false
    1. 令 result 为 ? RegExpExec(rx, S).
    2. 若 result 是 null, 设置 done 为 true.
    3. 否则 result 不是 null,
       1. 添加 result 到 results 的结尾.
       2. 若 global 是 false, 设置 done 到 true.
       3.  否则，
          1. 令 matchStr 为 ? ToString(? Get(result, "0")).
          2. 若 matchStr 是空字符串，那么 
             1. 令 thisIndex 为 ? ToLength(? Get(rx, "lastIndex")).
             2. 令 nextIndex 为 AdvanceStringIndex(S, thisIndex, fullUnicode).
             3. 执行 ? Set(rx, "lastIndex", nextIndex, true).
12. 令 accumulatedResult 为空字符串 value.
13. 令 nextSourcePosition 为 0.
14. 对于results中的每个result，执行
    1. 令 nCaptures 为 ? ToLength(? Get(result, "length")).
    2. 设置 nCaptures 为 max(nCaptures - 1, 0).
    3. 令 matched 为 ? ToString(? Get(result, "0")).
    4. 令 matchLength 为匹配的代码单元数。
    5. 令 position 为 ? ToInteger(? Get(result, "index")).
    6. 设置 position 为 max(min(position, lengthS), 0).
    7. 令 n 为 1.
    8. 令 captures 为一个新的空列表。
    9. 重复， 当 n ≤ nCaptures
       1. 令 capN 为 ? Get(result, ! ToString(n)).
       2. 若 capN 不是 undefined，那么 
          1. 设置 capN 为 ? ToString(capN).
       3. 将capN追加为捕获的最后一个元素。
       4. n 增加 1
    10. 令 namedCaptures 为 ? Get(result, "groups").
    11. 若 functionalReplace 是 true，那么 
        1. 令 replacerArgs 为 « matched ».
        2. 添加按列表顺序 captures 的元素到列表 replacerArgs 的末尾。
        3. 添加 position 和 S 到 replacerArgs.
        4. 若 namedCaptures 不是 undefined，那么 
           1. 将namedCaptures追加为replacerArgs的最后一个元素。
        5. 令 replValue 为 ? Call(replaceValue, undefined, replacerArgs).
        6. 令 replacement 为 ? ToString(replValue).
    12.  否则，
        1. 令 replacement 为 GetSubstitution(matched, S, position, captures, namedCaptures, replaceValue).
    13. 若 position ≥ nextSourcePosition，那么 
        1. 注: 位置通常不应向后移动。如果是这样，则表明RegExp子类行为异常，或者使用访问触发的副作用来更改rx的全局标志或其他特征。在这种情况下，将忽略相应的替换。
        2. 设置 cumulativeResult 为 accumulatedResult 当前值的字符串连接，即 S 的子字符串，包括从nextSourcePosition（含）到位置（不含）的代码单元，和更换。
        3. 设置 nextSourcePosition 为 position + matchLength.
15. 若 nextSourcePosition ≥ lengthS，返回 accumulatedResult.
16. 返回 S 的子字符串（由nextSourcePosition（包括）开始，到S的最终代码单元（包括）），是SummaryResult的字符串连接。

该函数的名称属性的值为“ [Symbol.replace]”。

#### 21.2.5.10 RegExp.prototype [ @@search ] ( string ) <div id="sec-regexp.prototype-@@search"></div>

当使用参数字符串调用@@search方法时，将采取以下步骤：

1. 令 rx 为 this 值。
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

该函数的名称属性的值为“ [Symbol.search]”。

> 注：执行搜索时，将忽略此RegExp对象的lastIndex和全局属性。 lastIndex属性保持不变。

#### 21.2.5.11 获取 RegExp.prototype.source <div id="sec-get-regexp.prototype.source"></div>

RegExp.prototype.source是一个访问器属性，其设置的访问器功能未定义。它的get访问器功能执行以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R 没有 [[OriginalSource]] 内部插槽，那么 
   1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 "(?:)".
   2. 除此之外, 抛出 TypeError 异常.
4. 断言：R有一个[[OriginalFlags]]内部插槽。
5. 令 src 为 R.[[OriginalSource]].
6. 令 flags 为 R.[[OriginalFlags]].
7. 返回 EscapeRegExpPattern(src, flags).

#### 21.2.5.12 RegExp.prototype [ @@split ] ( string, limit ) <div id="sec-regexp.prototype-@@split"></div>

> 注 1
>
> 返回一个Array对象，其中已将字符串转换为String的结果的子字符串存储在其中。通过从左到右搜索此值正则表达式的匹配项来确定子字符串。这些出现不属于返回数组中任何子字符串的一部分，而是用于划分String值。
>
> 这个值可以是空的正则表达式，也可以是匹配空字符串的正则表达式。在本例中，正则表达式不匹配输入字符串开头或结尾的空子字符串，也不匹配前面的分隔符匹配结尾的空子字符串。(例如,如果正则表达式匹配空字符串,字符串分成独立的代码单元元素；结果数组的长度等于字符串的长度，每个子字符串包含一个代码单元。只考虑给定字符串索引处的第一个匹配，即使回溯可能在该索引处产生非空子字符串匹配。(例如,/ \ * ? / \ [Symbol.split] (ab)等于数组(“a”、“b”),而/ \ * / \ [Symbol.split] (ab)等于数组(“”,“b”))。
>
> 如果字符串是（或转换为）空字符串，则结果取决于正则表达式是否可以匹配空字符串。如果可以，则结果数组不包含任何元素。否则，结果数组包含一个元素，即空字符串
>
> 如果正则表达式包含捕获括号，则每次分隔符匹配时，捕获括号的结果（包括任何未定义的结果）都将被拼接到输出数组中。例如，
>
> /<(\/)?(\[^<>]+)>/\[Symbol.split]("A\<B>bold\</B>and\<CODE>coded\</CODE>")
>
> 解释执行到数组
>
> ["A", undefined, "B", "bold", "/", "B", "and", undefined, "CODE", "coded", "/", "CODE"
>
> 如果未定义limit，则输出数组将被截断，以使其包含的元素数不超过limit。

调用@@split方法时，将采取以下步骤：

1. 令 rx 为 this 值。
2. 若 Type(rx) 不是 Object, 抛出 TypeError 异常.
3. 令 S 为 ? ToString(string).
4. 令 C 为 ? SpeciesConstructor(rx, %RegExp%).
5. 令 flags 为 ? ToString(? Get(rx, "flags")).
6. 若 flags 包含 "u", 令 unicodeMatching 为 true.
7.  否则， 令 unicodeMatching 为 false.
8. 若 flags 包含 "y", 令 newFlags 为 flags.
9.  否则， 令 newFlags 为 flag 和“y”的字符串连接。
10. 令 splitter 为 ? Construct(C, « rx, newFlags »).
11. 令 A 为 ! ArrayCreate(0).
12. 令 lengthA 为 0.
13. 若 limit 是 undefined, 令 lim 为 232 - 1; 否则 令 lim 为 ? ToUint32(limit).
14. 令 size 为 S 的长度.
15. 令 p 为 0.
16. 若 lim = 0，返回 A.
17. 若 size = 0，那么 
    1. 令 z 为 ? RegExpExec(splitter, S).
    2. 若 z 不是 null，返回 A.
    3. 执行 ! CreateDataProperty(A, "0", S).
    4. 返回 A.
18. 令 q 为 p.
19. 重复, 直到 q < size
    1. 执行 ? Set(splitter, "lastIndex", q, true).
    2. 令 z 为 ? RegExpExec(splitter, S).
    3. 若 z 是 null, 设置 q 到 AdvanceStringIndex(S, q, unicodeMatching).
    4. 否则 z 不是 null,
       5. 令 e 为 ? ToLength(? Get(splitter, "lastIndex")).
       2. 设置 e 为 min(e, size).
       3. 若 e = p, set q to AdvanceStringIndex(S, q, unicodeMatching).
       4. 否则 e ≠ p,
          1. 令 T 为字符串值，它等于S的子字符串，该子字符串由索引p（含）至q（不含）处的代码单元组成。
          2. 执行 ! CreateDataProperty(A, ! ToString(lengthA), T).
          3. lengthA 增加 1
          4. 若 lengthA = lim，返回 A.
          5. 设置 p 为 e.
          6. 令 numberOfCaptures 为 ? ToLength(? Get(z, "length")).
          7. 设置 numberOfCaptures 为 max(numberOfCaptures - 1, 0).
          8. 令 i 为 1.
          9. 重复， 直到 i ≤ numberOfCaptures,
             1. 令 nextCapture 为 ? Get(z, ! ToString(i)).
             2. 执行 ! CreateDataProperty(A, ! ToString(lengthA), nextCapture).
             3. i 增加 1
             4. lengthA 增加 1
             5. 若 lengthA = lim，返回 A.
          10. 设置 q 为 p.
20. 令 T 为字符串值，它等于S的子字符串，该子字符串由索引p（含）到大小（不含）之间的代码单元组成。
21. 执行 ! CreateDataProperty(A, ! ToString(lengthA), T).
22. 返回 A.

该函数的名称属性的值为“ [Symbol.split]”。

> 注 2：@@split方法将忽略此RegExp对象的global和sticky属性的值。

#### 21.2.5.13 获取 RegExp.prototype.sticky <div id="sec-get-regexp.prototype.sticky"></div>

RegExp.prototype.sticky是一个访问器属性，其设置的访问器功能未定义。它的get访问器功能执行以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R 没有 [[OriginalFlags]] 内部插槽，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags 包含代码单元0x0079（拉丁文小写字母Y），返回 true.
6. 返回 false.

#### 21.2.5.14 RegExp.prototype.test ( S ) <div id="sec-regexp.prototype.test"></div>

将采取以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 令 string 为 ? ToString(S).
4. 令 match 为 ? RegExpExec(R, string).
5. 若 match 不是 null，返回 true; else return false

#### 21.2.5.15 RegExp.prototype.toString ( ) <div id="sec-regexp.prototype.tostring"></div>

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 令 pattern 为 ? ToString(? Get(R, "source")).
4. 令 flags 为 ? ToString(? Get(R, "flags")).
5. 令 result 为“ /”，模式，“ /”和标志的字符串连接。
6. 返回 result.

> 注：返回的String具有RegularExpressionLiteral的形式，该形式求值为另一个具有与此对象相同行为的RegExp对象。

#### 21.2.5.16 获取 RegExp.prototype.unicode <div id="sec-get-regexp.prototype.unicode"></div>

RegExp.prototype.unicode是一个访问器属性，其设置的访问器函数未定义。它的get访问器功能执行以下步骤：

1. 令 R 为 this 值。
2. 若 Type(R) 不是 Object, 抛出 TypeError 异常.
3. 若 R 没有 [[OriginalFlags]] 内部插槽，那么 
1. 若 SameValue(R, %RegExpPrototype%) 是 true，返回 undefined.
2. 除此之外, 抛出 TypeError 异常.
4. 令 flags 为 R.[[OriginalFlags]].
5. 若 flags 包含代码单元0x0075（拉丁文小写字母U），返回 true.
6. 返回 false

### 21.2.6 RegExp 实例属性 <div id="sec-properties-of-regexp-instances"></div>

RegExp实例是从RegExp原型对象继承属性的普通对象。 RegExp实例具有内部插槽[[RegExpMatcher]]，[[OriginalSource]]和[[OriginalFlags]]。 [[RegExpMatcher]]内部插槽的值是RegExp对象的Pattern的与实现有关的表示。

> 注：在ECMAScript 2015之前，RegExp实例被指定为具有自己的数据属性source，global，ignoreCase和multiline。这些属性现在指定为RegExp.prototype的访问器属性。

RegExp实例还具有以下属性：

#### 21.2.6.1 lastIndex <div id="sec-lastindex"></div>

lastIndex属性的值指定开始下一个匹配的String索引。使用时将其强制为整数（请参见21.2.5.2.2）。该属性应具有以下属性 { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.