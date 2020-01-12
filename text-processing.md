# 21 文字处理
## 21.1 String 对象 <div id="sec-string-objects"></div>
### 21.1.1 String 构造器 <div id="sec-string-constructor"></div>

The String constructor:

- is the intrinsic object %String%.
- is the initial value of the String property of the global object.
- creates and initializes a new String object when called as a constructor.
- performs a type conversion when called as a function rather than as a constructor.
- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. Subclass constructors that intend to inherit the specified String behaviour must include a super call to the String constructor to create and initialize the subclass instance with a [[StringData]] internal slot.

#### 21.1.1.1 String ( value ) <div id="sec-string-constructor-string-value"></div>

When String is called with argument value, the following steps are taken:

1. If no arguments were passed to this function invocation, let s be "".
2. Else,
1. If NewTarget is undefined and Type(value) is Symbol, return SymbolDescriptiveString(value).
2. Let s be ? ToString(value).
3. If NewTarget is undefined, return s.
4. Return ! StringCreate(s, ? GetPrototypeFromConstructor(NewTarget, "%StringPrototype%")).

### 21.1.2 String 构造器属性 <div id="sec-properties-of-the-string-constructor"></div>

The String constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 21.1.2.1 String.fromCharCode ( ...codeUnits ) <div id="sec-string.fromcharcode"></div>

The String.fromCharCode function may be called with any number of arguments which form the rest parameter codeUnits. The following steps are taken:

1. Let codeUnits be a List containing the arguments passed to this function.
2. Let length be the number of elements in codeUnits.
3. Let elements be a new empty List.
4. Let nextIndex be 0.
5. Repeat, while nextIndex < length
1. Let next be codeUnits[nextIndex].
2. Let nextCU be ? ToUint16(next).
3. Append nextCU to the end of elements.
4. Increase nextIndex by 1.
6. Return the String value whose code units are, in order, the elements in the List elements. If length is 0, the empty string is returned.

The "length" property of the fromCharCode function is 1.

#### 21.1.2.2 String.fromCodePoint ( ...codePoints ) <div id="sec-string.fromcodepoint"></div>

The String.fromCodePoint function may be called with any number of arguments which form the rest parameter codePoints. The following steps are taken:

1. Let codePoints be a List containing the arguments passed to this function.
2. Let length be the number of elements in codePoints.
3. Let elements be a new empty List.
4. Let nextIndex be 0.
5. Repeat, while nextIndex < length
1. Let next be codePoints[nextIndex].
2. Let nextCP be ? ToNumber(next).
3. If SameValue(nextCP, ! ToInteger(nextCP)) is false, throw a RangeError exception.
4. If nextCP < 0 or nextCP > 0x10FFFF, throw a RangeError exception.
5. Append the elements of the UTF16Encoding of nextCP to the end of elements.
6. Increase nextIndex by 1.
6. Return the String value whose code units are, in order, the elements in the List elements. If length is 0, the empty string is returned.

The "length" property of the fromCodePoint function is 1

#### 21.1.2.3 String.prototype <div id="sec-string.prototype"></div>

The initial value of String.prototype is the intrinsic object %StringPrototype%.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 21.1.2.4 String.raw ( template, ...substitutions ) <div id="sec-string.raw"></div>

The String.raw function may be called with a variable number of arguments. The first argument is template and the remainder of the arguments form the List substitutions. The following steps are taken:

1. Let substitutions be a List consisting of all of the arguments passed to this function, starting with the second argument. If fewer than two arguments were passed, the List is empty.
2. Let numberOfSubstitutions be the number of elements in substitutions.
3. Let cooked be ? ToObject(template).
4. Let raw be ? ToObject(? Get(cooked, "raw")).
5. Let literalSegments be ? ToLength(? Get(raw, "length")).
6. If literalSegments ≤ 0, return the empty string.
7. Let stringElements be a new empty List.
8. Let nextIndex be 0.
9. Repeat,
1. Let nextKey be ! ToString(nextIndex).
2. Let nextSeg be ? ToString(? Get(raw, nextKey)).
3. Append in order the code unit elements of nextSeg to the end of stringElements.
4. If nextIndex + 1 = literalSegments, then
5. Return the String value whose code units are, in order, the elements in the List stringElements. If stringElements has no elements, the empty string is returned.
6. If nextIndex < numberOfSubstitutions, let next be substitutions[nextIndex].
7. Else, let next be the empty String.
8. Let nextSub be ? ToString(next).
9. Append in order the code unit elements of nextSub to the end of stringElements.
10. Increase nextIndex by 1

> NOTE String.raw is intended for use as a tag function of a Tagged Template (12.3.7). When called as such, the first argument will be a well formed template object and the rest parameter will contain the substitution values.

### 21.1.3 String 原型对象属性 <div id="sec-properties-of-the-string-prototype-object"></div>

The String prototype object:

- is the intrinsic object %StringPrototype%.
- is a String exotic object and has the internal methods specified for such objects.
- has a [[StringData]] internal slot whose value is the empty String.
- has a "length" property whose initial value is 0 and whose attributes are { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

Unless explicitly stated otherwise, the methods of the String prototype object defined below are not generic and the this value passed to them must be either a String value or an object that has a [[StringData]] internal slot that has been initialized to a String value

The abstract operation thisStringValue(value) performs the following steps:

1. If Type(value) is String, return value.
2. If Type(value) is Object and value has a [[StringData]] internal slot, then
   1. Let s be value.[[StringData]].
   2. Assert: Type(s) is String.
   3. Return s.
3. Throw a TypeError exception.

#### 21.1.3.1 String.prototype.charAt ( pos ) <div id="sec-string.prototype.charat"></div>

> NOTE 1 Returns a single element String containing the code unit at index pos within the String value resulting from converting this object to a String. If there is no element at that index, the result is the empty String. The result is a String value, not a String object.
>
> If pos is a value of Number type that is an integer, then the result of x.charAt(pos) is equal to the result of x.substring(pos, pos + 1)

When the charAt method is called with one argument pos, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let position be ? ToInteger(pos).
4. Let size be the length of S.
5. If position < 0 or position ≥ size, return the empty String.
6. Return the String value of length 1, containing one code unit from S, namely the code unit at index position.

> NOTE 2 The charAt function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.2 String.prototype.charCodeAt ( pos ) <div id="sec-string.prototype.charcodeat"></div>

> NOTE 1 Returns a Number (a nonnegative integer less than 216) that is the numeric value of the code unit at index pos within the String resulting from converting this object to a String. If there is no element at that index, the result is NaN.

When the charCodeAt method is called with one argument pos, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let position be ? ToInteger(pos).
4. Let size be the length of S.
5. If position < 0 or position ≥ size, return NaN.
6. Return a value of Number type, whose value is the numeric value of the code unit at index position within the String S.

> NOTE 2 The charCodeAt function is intentionally generic; it does not require that its this value be a String object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.3 String.prototype.codePointAt ( pos ) <div id="sec-string.prototype.codepointat"></div>

> NOTE 1 Returns a nonnegative integer Number less than 0x110000 that is the code point value of the UTF-16 encoded code point (6.1.4) starting at the string element at index pos within the String resulting from converting this object to a String. If there is no element at that index, the result is undefined. If a valid UTF-16 surrogate pair does not begin at pos, the result is the code unit at pos

When the codePointAt method is called with one argument pos, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let position be ? ToInteger(pos).
4. Let size be the length of S.
5. If position < 0 or position ≥ size, return undefined.
6. Let first be the numeric value of the code unit at index position within the String S.
7. If first < 0xD800 or first > 0xDBFF or position + 1 = size, return first.
8. Let second be the numeric value of the code unit at index position + 1 within the String S.
9. If second < 0xDC00 or second > 0xDFFF, return first.
10. Return UTF16Decode(first, second).

> NOTE 2 The codePointAt function is intentionally generic; it does not require that its this value be a String object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.4 String.prototype.concat ( ...args ) <div id="sec-string.prototype.concat"></div>

> NOTE 1 When the concat method is called it returns the String value consisting of the code units of the this object (converted to a String) followed by the code units of each of the arguments converted to a String. The result is a String value, not a String object.

When the concat method is called with zero or more arguments, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let args be a List whose elements are the arguments passed to this function.
4. Let R be S.
5. Repeat, while args is not empty
1. Remove the first element from args and let next be the value of that element.
2. Let nextString be ? ToString(next).
3. Set R to the string-concatenation of the previous value of R and nextString.
6. Return R.

The "length" property of the concat method is 1.

> NOTE 2 The concat function is intentionally generic; it does not require that its this value be a String object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.5 String.prototype.constructor <div id="sec-string.prototype.constructor"></div>

The initial value of String.prototype.constructor is the intrinsic object %String%.

#### 21.1.3.6 String.prototype.endsWith ( searchString [ , endPosition ] ) <div id="sec-string.prototype.endswith"></div>

The following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let isRegExp be ? IsRegExp(searchString).
4. If isRegExp is true, throw a TypeError exception.
5. Let searchStr be ? ToString(searchString).
6. Let len be the length of S.
7. If endPosition is undefined, let pos be len, else let pos be ? ToInteger(endPosition).
8. Let end be min(max(pos, 0), len).
9. Let searchLength be the length of searchStr.
10. Let start be end - searchLength.
11. If start is less than 0, return false.
12. If the sequence of code units of S starting at start of length searchLength is the same as the full code unit sequence of searchStr, return true.
13. Otherwise, return false.

>NOTE 1
>Returns true if the sequence of code units of searchString converted to a String is the same as the corresponding code units of this object (converted to a String) starting at endPosition - length(this). Otherwise returns false.
>NOTE 2
>Throwing an exception if the first argument is a RegExp is specified in order to allow future editions to define extensions that allow such argument values.
>NOTE 3
>The endsWith function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.7 String.prototype.includes ( searchString [ , position ] ) <div id="sec-string.prototype.includes"></div>

The includes method takes two arguments, searchString and position, and performs the following steps:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let isRegExp be ? IsRegExp(searchString).
4. If isRegExp is true, throw a TypeError exception.
5. Let searchStr be ? ToString(searchString).
6. Let pos be ? ToInteger(position).
7. Assert: If position is undefined, then pos is 0.
8. Let len be the length of S.
9. Let start be min(max(pos, 0), len).
10. Let searchLen be the length of searchStr.

11. If there exists any integer k not smaller than start such that k + searchLen is not greater than len, and for all nonnegative integers j less than searchLen, the code unit at index k + j within S is the same as the code unit at index j within searchStr, return true; but if there is no such integer k, return false.

>NOTE 1
>If searchString appears as a substring of the result of converting this object to a String, at one or more indices that are greater than or equal to position, return true; otherwise, returns false. If position is undefined, 0 is assumed, so as to search all of the String.
>NOTE 2
>Throwing an exception if the first argument is a RegExp is specified in order to allow future editions to define extensions that allow such argument values.
>NOTE 3
>The includes function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.8 String.prototype.indexOf ( searchString [ , position ] ) <div id="sec-string.prototype.indexof"></div>

> NOTE 1 If searchString appears as a substring of the result of converting this object to a String, at one or more indices that are greater than or equal to position, then the smallest such index is returned; otherwise, -1 is returned. If position is undefined, 0 is assumed, so as to search all of the String.

The indexOf method takes two arguments, searchString and position, and performs the following steps:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let searchStr be ? ToString(searchString).
4. Let pos be ? ToInteger(position).
5. Assert: If position is undefined, then pos is 0.
6. Let len be the length of S.
7. Let start be min(max(pos, 0), len).
8. Let searchLen be the length of searchStr.
9. Return the smallest possible integer k not smaller than start such that k + searchLen is not greater than len, and for all nonnegative integers j less than searchLen, the code unit at index k + j within S is the same as the code unit at index j within searchStr; but if there is no such integer k, return the value -1.

> NOTE 2 The indexOf function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.9 String.prototype.lastIndexOf ( searchString [ , position ] ) <div id="sec-string.prototype.lastindexof"></div>

> NOTE 1 If searchString appears as a substring of the result of converting this object to a String at one or more indices that are smaller than or equal to position, then the greatest such index is returned; otherwise, -1 is returned. If position is undefined, the length of the String value is assumed, so as to search all of the String.

The lastIndexOf method takes two arguments, searchString and position, and performs the following steps:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let searchStr be ? ToString(searchString).
4. Let numPos be ? ToNumber(position).
5. Assert: If position is undefined, then numPos is NaN.
6. If numPos is NaN, let pos be +∞; otherwise, let pos be ! ToInteger(numPos).
7. Let len be the length of S.
8. Let start be min(max(pos, 0), len).
9. Let searchLen be the length of searchStr.
10. Return the largest possible nonnegative integer k not larger than start such that k + searchLen is not greater than len, and for all nonnegative integers j less than searchLen, the code unit at index k + j within S is the same as the code unit at index j within searchStr; but if there is no such integer k, return the value -1.

> NOTE 2 The lastIndexOf function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.10 String.prototype.localeCompare ( that [ , reserved1 [ , reserved2 ] ] ) <div id="sec-string.prototype.localecompare"></div>

An ECMAScript implementation that includes the ECMA-402 Internationalization API must implement the localeCompare method as specified in the ECMA-402 specification. If an ECMAScript implementation does not include the ECMA-402 API the following specification of the localeCompare method is used.

When the localeCompare method is called with argument that, it returns a Number other than NaN that represents the result of a locale-sensitive String comparison of the this value (converted to a String) with that (converted to a String). The two Strings are S and That. The two Strings are compared in an implementation-defined fashion. The result is intended to order String values in the sort order specified by a host default locale, and will be negative, zero, or positive, depending on whether S comes before That in the sort order, the Strings are equal, or S comes after That in the sort order, respectively.

Before performing the comparisons, the following steps are performed to prepare the Strings:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let That be ? ToString(that).

The meaning of the optional second and third parameters to this method are defined in the ECMA-402 specification; implementations that do not include ECMA-402 support must not assign any other interpretation to those parameter positions.

The localeCompare method, if considered as a function of two arguments this and that, is a consistent comparison function (as defined in 22.1.3.27) on the set of all Strings.

The actual return values are implementation-defined to permit implementers to encode additional information in the value, but the function is required to define a total ordering on all Strings. This function must treat Strings that are canonically equivalent according to the Unicode standard as identical and must return 0 when comparing Strings that are considered canonically equivalent.

> NOTE 1
>
> The localeCompare method itself is not directly suitable as an argument to Array.prototype.sort because the latter requires a function of two arguments. 
>
> NOTE 2 This function is intended to rely on whatever language-sensitive comparison functionality is available to the ECMAScript environment from the host environment, and to compare according to the rules of the host environment's current locale. However, regardless of the host provided comparison capabilities, this function must treat Strings that are canonically equivalent according to the Unicode standard as identical. It is recommended that this function should not honour Unicode compatibility equivalences or decompositions. For a definition and discussion of canonical equivalence see the Unicode Standard, chapters 2 and 3, as well as Unicode Standard Annex #15, Unicode Normalization Forms (https://unicode.org/reports/tr15/) and Unicode Technical Note #5, Canonical Equivalence in Applications (https://www.unicode.org/notes/tn5/). Also see Unicode Technical Standard #10, Unicode Collation Algorithm (https://unicode.org/reports/tr10/). 
>
> NOTE 3 The localeCompare function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.11 String.prototype.match ( regexp ) <div id="sec-string.prototype.match"></div>

When the match method is called with argument regexp, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. If regexp is neither undefined nor null, then
1. Let matcher be ? GetMethod(regexp, @@match).
2. If matcher is not undefined, then
   1. Return ? Call(matcher, regexp, « O »).
3. Let S be ? ToString(O).
4. Let rx be ? RegExpCreate(regexp, undefined).
5. Return ? Invoke(rx, @@match, « S »).

> NOTE The match function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.12 String.prototype.normalize ( [ form ] ) <div id="sec-string.prototype.normalize"></div>

When the normalize method is called with one argument form, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. If form is not present or form is undefined, set form to "NFC".
4. Let f be ? ToString(form).
5. If f is not one of "NFC", "NFD", "NFKC", or "NFKD", throw a RangeError exception.
6. Let ns be the String value that is the result of normalizing S into the normalization form named by f as specified in https://unicode.org/reports/tr15/.
7. Return ns.

> NOTE The normalize function is intentionally generic; it does not require that its this value be a String object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.13 String.prototype.padEnd ( maxLength [ , fillString ] ) <div id="sec-string.prototype.padend"></div>

When the padEnd method is called, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let intMaxLength be ? ToLength(maxLength).
4. Let stringLength be the length of S.
5. If intMaxLength is not greater than stringLength, return S.
6. If fillString is undefined, let filler be the String value consisting solely of the code unit 0x0020 (SPACE).
7. Else, let filler be ? ToString(fillString).
8. If filler is the empty String, return S.
9. Let fillLen be intMaxLength - stringLength.
10. Let truncatedStringFiller be the String value consisting of repeated concatenations of filler truncated to length fillLen.
11. Return the string-concatenation of S and truncatedStringFiller.

> NOTE 1 
>
> The first argument maxLength will be clamped such that it can be no smaller than the length of the this value. 
>
> NOTE 2 The optional second argument fillString defaults to " " (the String value consisting of the code unit 0x0020 SPACE).

#### 21.1.3.14 String.prototype.padStart ( maxLength [ , fillString ] ) <div id="sec-string.prototype.padstart"></div>

When the padStart method is called, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let intMaxLength be ? ToLength(maxLength).
4. Let stringLength be the length of S.
5. If intMaxLength is not greater than stringLength, return S.
6. If fillString is undefined, let filler be the String value consisting solely of the code unit 0x0020 (SPACE).
7. Else, let filler be ? ToString(fillString).
8. If filler is the empty String, return S.
9. Let fillLen be intMaxLength - stringLength.
10. Let truncatedStringFiller be the String value consisting of repeated concatenations of filler truncated to length fillLen.
11. Return the string-concatenation of truncatedStringFiller and S.

> NOTE 1
>
> The first argument maxLength will be clamped such that it can be no smaller than the length of the this value.
>
> NOTE 2 
>
> The optional second argument fillString defaults to " " (the String value consisting of the code unit 0x0020 SPACE).

#### 21.1.3.15 String.prototype.repeat ( count ) <div id="sec-string.prototype.repeat"></div>

The following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let n be ? ToInteger(count).
4. If n < 0, throw a RangeError exception.
5. If n is +∞, throw a RangeError exception.
6. If n is 0, return the empty String.
7. Return the String value that is made from n copies of S appended together

> NOTE 1
>
> This method creates the String value consisting of the code units of the this object (converted to String) repeated count times. 
>
> NOTE 2
> The repeat function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.16 String.prototype.replace ( searchValue, replaceValue ) <div id="sec-string.prototype.replace"></div>

When the replace method is called with arguments searchValue and replaceValue, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. If searchValue is neither undefined nor null, then
1. Let replacer be ? GetMethod(searchValue, @@replace).
2. If replacer is not undefined, then
   1. Return ? Call(replacer, searchValue, « O, replaceValue »).
3. Let string be ? ToString(O).
4. Let searchString be ? ToString(searchValue).
5. Let functionalReplace be IsCallable(replaceValue).
6. If functionalReplace is false, then
1. Set replaceValue to ? ToString(replaceValue).
7. Search string for the first occurrence of searchString and let pos be the index within string of the first code unit of the matched substring and let matched be searchString. If no occurrences of searchString were found, return string.
8. If functionalReplace is true, then
  1. Let replValue be ? Call(replaceValue, undefined, « matched, pos, string »).
  2. Let replStr be ? ToString(replValue).
9. Else,
  1. Let captures be a new empty List.
  2. Let replStr be GetSubstitution(matched, string, pos, captures, undefined, replaceValue).
10. Let tailPos be pos + the number of code units in matched.
11. Let newString be the string-concatenation of the first pos code units of string, replStr, and the trailing substring of string starting at index tailPos. If pos is 0, the first element of the concatenation will be the empty String.
12. Return newString.

> NOTE
>
> The replace function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

##### 21.1.3.16.1 RS: GetSubstitution ( matched, str, position, captures, namedCaptures, replacement ) <div id="sec-getsubstitution"></div>

The abstract operation GetSubstitution performs the following steps:

1. Assert: Type(matched) is String.
2. Let matchLength be the number of code units in matched.
3. Assert: Type(str) is String.
4. Let stringLength be the number of code units in str.
5. Assert: position is a nonnegative integer.
6. Assert: position ≤ stringLength.
7. Assert: captures is a possibly empty List of Strings.
8. Assert: Type(replacement) is String.
9. Let tailPos be position + matchLength.
10. Let m be the number of elements in captures.
11. If namedCaptures is not undefined, then
1. Set namedCaptures to ? ToObject(namedCaptures).
12. Let result be the String value derived from replacement by copying code unit elements from replacement to result while performing replacements as specified in Table 51. These $ replacements are done left-to-right, and, once such a replacement is performed, the new replacement text is not subject to further replacements.
13. Return result.

Table 51: Replacement Text Symbol Substitutions


| Code units                             | Unicode Characters                                           | Replacement text                                             |
| -------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 0x0024, 0x0024                         | `$$`                                                         | `$`                                                          |
| 0x0024, 0x0026                         | `$&`                                                         | matched                                                      |
| 0x0024, 0x0060                         | `$``                                                         | If position is 0, the replacement is the empty String. Otherwise the replacement is the substring of str that starts at index 0 and whose last code unit is at index position - 1. |
| 0x0024, 0x0027                         | `$'`                                                         | If tailPos ≥ stringLength, the replacement is the empty String. Otherwise the replacement is the substring of str that starts at index tailPos and continues to the end of str. |
| 0x0024, N Where 0x0031 ≤ N ≤ 0x0039    | `$n` where `n` is one of `1 2 3 4 5 6 7 8 9` and `$n` is not followed by a decimal digit | The nth element of captures, where n is a single digit in the range 1 to 9. If n≤m and the nth element of captures is undefined, use the empty String instead. If n>m, the result is implementation-defined. |
| 0x0024, N, N Where 0x0030 ≤ N ≤ 0x0039 | `$nn` where `n` is one of `0 1 2 3 4 5 6 7 8 9`              | The nnth element of captures, where nn is a two-digit decimal number in the range 01 to 99. If nn≤m and the nnth element of captures is undefined, use the empty String instead. If nn is 00 or nn>m, the result is implementation-defined. |
| 0x0024, 0x003C                         | $<                                                           | 1. If namedCaptures is undefined, the replacement text is the String<br/>"$<".<br/>2. Else,<br/>a. Scan until the next > U+003E (GREATER-THAN SIGN).<br/>b. If none is found, the replacement text is the String "$<".<br/>c. Else,<br/>i. Let groupName be the enclosed substring.<br/>ii. Let capture be ? Get(namedCaptures, groupName).<br/>iii. If capture is undefined, replace the text through > with the empty string.<br/>iv. Otherwise, replace the text through > with ? ToString(capture). |
| 0x0024                                 | `$` in any context that does not match any of the above.     | `$`                                                          |



#### 21.1.3.17 String.prototype.search ( regexp ) <div id="sec-string.prototype.search"></div>

When the search method is called with argument regexp, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. If regexp is neither undefined nor null, then
1. Let searcher be ? GetMethod(regexp, @@search).
2. If searcher is not undefined, then
  1. Return ? Call(searcher, regexp, « O »).
3. Let string be ? ToString(O).
4. Let rx be ? RegExpCreate(regexp, undefined).
5. Return ? Invoke(rx, @@search, « string »)

> NOTE The search function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.18 String.prototype.slice ( start, end ) <div id="sec-string.prototype.slice"></div>

The slice method takes two arguments, start and end, and returns a substring of the result of converting this object to a String, starting from index start and running to, but not including, index end (or through the end of the String if end is undefined). If start is negative, it is treated as sourceLength + start where sourceLength is the length of the String. If end is negative, it is treated as sourceLength + end where sourceLength is the length of the String. The result is a String value, not a String object. The following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let len be the length of S.
4. Let intStart be ? ToInteger(start).
5. If end is undefined, let intEnd be len; else let intEnd be ? ToInteger(end).
6. If intStart < 0, let from be max(len + intStart, 0); otherwise let from be min(intStart, len).
7. If intEnd < 0, let to be max(len + intEnd, 0); otherwise let to be min(intEnd, len).
8. Let span be max(to - from, 0).
9. Return the String value containing span consecutive code units from S beginning with the code unit at index from.

> NOTE The slice function is intentionally generic; it does not require that its this value be a String object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.19 String.prototype.split ( separator, limit ) <div id="sec-string.prototype.split"></div>

Returns an Array object into which substrings of the result of converting this object to a String have been stored. The substrings are determined by searching from left to right for occurrences of separator; these occurrences are not part of any substring in the returned array, but serve to divide up the String value. The value of separator may be a String of any length or it may be an object, such as a RegExp, that has a @@split method.

When the split method is called, the following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. If separator is neither undefined nor null, then
   1. Let splitter be ? GetMethod(separator, @@split).
   2. If splitter is not undefined, then
        1. Return ? Call(splitter, separator, « O, limit »).
3. Let S be ? ToString(O).
4. Let A be ! ArrayCreate(0).
5. Let lengthA be 0.
6. If limit is undefined, let lim be 232 - 1; else let lim be ? ToUint32(limit).
7. Let s be the length of S.
8. Let p be 0.
9. Let R be ? ToString(separator).
10. If lim = 0, return A.
11. If separator is undefined, then
    1. Perform ! CreateDataProperty(A, "0", S).
    2. Return A.
12. If s = 0, then
    1. Let z be SplitMatch(S, 0, R).
    2. If z is not false, return A.
    3. Perform ! CreateDataProperty(A, "0", S).
    4. Return A.
13. Let q be p.
14. Repeat, while q ≠ s
    1. Let e be SplitMatch(S, q, R).
    2. If e is false, increase q by 1.
    3. Else e is an integer index ≤ s,
       1. If e = p, increase q by 1.
       2. Else e ≠ p,
          1. Let T be the String value equal to the substring of S consisting of the code units at indices p (inclusive) through q (exclusive).
          2. Perform ! CreateDataProperty(A, ! ToString(lengthA), T).
          3. Increment lengthA by 1.
          4. If lengthA = lim, return A.
          5. Set p to e.
          6. Set q to p. 
    
15. Let T be the String value equal to the substring of S consisting of the code units at indices p (inclusive) through s (exclusive).

1. Perform ! CreateDataProperty(A, ! ToString(lengthA), T).
2. Return A.

> NOTE 1 
>
> The value of separator may be an empty String. In this case, separator does not match the empty substring at the beginning or end of the input String, nor does it match the empty substring at the end of the previous separator match. If separator is the empty String, the String is split up into individual code unit elements; the length of the result array equals the length of the String, and each substring contains one code unit. 
>
> If the this object is (or converts to) the empty String, the result depends on whether separator can match the empty String. If it can, the result array contains no elements. Otherwise, the result array contains one element, which is the empty String. 
>
> If separator is undefined, then the result array contains just one String, which is the this value (converted to a String). If limit is not undefined, then the output array is truncated so that it contains no more than limit elements. 
>
> NOTE 2 
>
> The split function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

##### 21.1.3.19.1 RS: SplitMatch ( S, q, R ) <div id="sec-splitmatch"></div>

The abstract operation SplitMatch takes three parameters, a String S, an integer q, and a String R, and performs the following steps in order to return either false or the end index of a match:

1. Assert: Type(R) is String.
2. Let r be the number of code units in R.
3. Let s be the number of code units in S.
4. If q + r > s, return false.
5. If there exists an integer i between 0 (inclusive) and r (exclusive) such that the code unit at index q + i within S is different from the code unit at index i within R, return false.
6. Return q + r.

#### 21.1.3.20 String.prototype.startsWith ( searchString [ , position ] ) <div id="sec-string.prototype.startswith"></div>

The following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let isRegExp be ? IsRegExp(searchString).
4. If isRegExp is true, throw a TypeError exception.
5. Let searchStr be ? ToString(searchString).
6. Let pos be ? ToInteger(position).
7. Assert: If position is undefined, then pos is 0.
8. Let len be the length of S.
9. Let start be min(max(pos, 0), len).
10. Let searchLength be the length of searchStr.
11. If searchLength + start is greater than len, return false.
12. If the sequence of code units of S starting at start of length searchLength is the same as the full code unit sequence of searchStr, return true.
13. Otherwise, return false.

> NOTE 1
>
> This method returns true if the sequence of code units of searchString converted to a String is the same as the corresponding code units of this object (converted to a String) starting at index position. Otherwise returns false.
>
> NOTE 2 
>
> Throwing an exception if the first argument is a RegExp is specified in order to allow future editions to define extensions that allow such argument values.
>
> NOTE 3 
>
> The startsWith function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method

#### 21.1.3.21 String.prototype.substring ( start, end ) <div id="sec-string.prototype.substring"></div>

The substring method takes two arguments, start and end, and returns a substring of the result of converting this object to a String, starting from index start and running to, but not including, index end of the String (or through the end of the String if end is undefined). The result is a String value, not a String object.

If either argument is NaN or negative, it is replaced with zero; if either argument is larger than the length of the String, it is replaced with the length of the String.

If start is larger than end, they are swapped.

The following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Let len be the length of S.
4. Let intStart be ? ToInteger(start).
5. If end is undefined, let intEnd be len; else let intEnd be ? ToInteger(end).
6. Let finalStart be min(max(intStart, 0), len).
7. Let finalEnd be min(max(intEnd, 0), len).
8. Let from be min(finalStart, finalEnd).
9. Let to be max(finalStart, finalEnd).
10. Return the String value whose length is to - from, containing code units from S, namely the code units with indices from through to - 1, in ascending order.

> NOTE
>
> The substring function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.22 String.prototype.toLocaleLowerCase ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-string.prototype.tolocalelowercase"></div>

An ECMAScript implementation that includes the ECMA-402 Internationalization API must implement the toLocaleLowerCase method as specified in the ECMA-402 specification. If an ECMAScript implementation does not include the ECMA-402 API the following specification of the toLocaleLowerCase method is used.

This function interprets a String value as a sequence of UTF-16 encoded code points, as described in 6.1.4.

This function works exactly the same as toLowerCase except that its result is intended to yield the correct result for the host environment's current locale, rather than a locale-independent result. There will only be a difference in the few cases (such as Turkish) where the rules for that language conflict with the regular Unicode case mappings.

The meaning of the optional parameters to this method are defined in the ECMA-402 specification; implementations that do not include ECMA-402 support must not use those parameter positions for anything else.

> NOTE The toLocaleLowerCase function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.23 String.prototype.toLocaleUpperCase ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-string.prototype.tolocaleuppercase"></div>

An ECMAScript implementation that includes the ECMA-402 Internationalization API must implement the toLocaleUpperCase method as specified in the ECMA-402 specification. If an ECMAScript implementation does not include the ECMA-402 API the following specification of the toLocaleUpperCase method is used.

This function interprets a String value as a sequence of UTF-16 encoded code points, as described in 6.1.4.

This function works exactly the same as toUpperCase except that its result is intended to yield the correct result for the host environment's current locale, rather than a locale-independent result. There will only be a difference in the few cases (such as Turkish) where the rules for that language conflict with the regular Unicode case mappings.

The meaning of the optional parameters to this method are defined in the ECMA-402 specification; implementations that do not include ECMA-402 support must not use those parameter positions for anything else.

> NOTE The toLocaleUpperCase function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method

#### 21.1.3.24 String.prototype.toLowerCase ( ) <div id="sec-string.prototype.tolowercase"></div>

This function interprets a String value as a sequence of UTF-16 encoded code points, as described in 6.1.4. The following steps are taken:

1. Let O be ? RequireObjectCoercible(this value)
2. Let S be ? ToString(O).
3. Let cpList be a List containing in order the code points as defined in 6.1.4 of S, starting at the first element of S.
4. Let cuList be a List where the elements are the result of toLowercase(cpList), according to the Unicode Default Case Conversion algorithm.
5. Let L be the String value whose code units are the UTF16Encoding of the code points of cuList.
6. Return L.

The result must be derived according to the locale-insensitive case mappings in the Unicode Character Database (this explicitly includes not only the UnicodeData.txt file, but also all locale-insensitive mappings in the SpecialCasings.txt file that accompanies it).

> NOTE 1
>
> The case mapping of some code points may produce multiple code points. In this case the result String may not be the same length as the source String. Because both toUpperCase and toLowerCase have context-sensitive behaviour, the functions are not symmetrical. In other words, s.toUpperCase().toLowerCase() is not necessarily equal to s.toLowerCase().
>
> NOTE 2
>
> The toLowerCase function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.25 String.prototype.toString ( ) <div id="sec-string.prototype.tostring"></div>

When the toString method is called, the following steps are taken:

1. Return ? thisStringValue(this value).

> NOTE For a String object, the toString method happens to return the same thing as the valueOf method.

#### 21.1.3.26 String.prototype.toUpperCase ( ) <div id="sec-string.prototype.touppercase"></div>

This function interprets a String value as a sequence of UTF-16 encoded code points, as described in 6.1.4.

This function behaves in exactly the same way as String.prototype.toLowerCase, except that the String is mapped using the toUppercase algorithm of the Unicode Default Case Conversion.

> NOTE The toUpperCase function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.27 String.prototype.trim ( ) <div id="sec-string.prototype.trim"></div>

This function interprets a String value as a sequence of UTF-16 encoded code points, as described in 6.1.4.

The following steps are taken:

1. Let S be this value.
2. Return ? TrimString(S, "start+end"

> NOTE The trim function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

##### 21.1.3.27.1 RS: TrimString ( string, where ) <div id="sec-trimstring"></div>

The abstract operation TrimString is called with arguments string and where, and interprets the String value string as a sequence of UTF-16 encoded code points, as described in 6.1.4. It performs the following steps:

1. Let str be ? RequireObjectCoercible(string).
2. Let S be ? ToString(str).
3. If where is "start", let T be the String value that is a copy of S with leading white space removed.
4. Else if where is "end", let T be the String value that is a copy of S with trailing white space removed.
5. Else,
1. Assert: where is "start+end".
2. Let T be the String value that is a copy of S with both leading and trailing white space removed.
6. Return T.

The definition of white space is the union of WhiteSpace and LineTerminator. When determining whether a Unicode code point is in Unicode general category “Space_Separator” (“Zs”), code unit sequences are interpreted as UTF-16 encoded code point sequences as specified in 6.1.4.

#### 21.1.3.28 String.prototype.trimEnd ( ) <div id="sec-string.prototype.trimend"></div>

This function interprets a String value as a sequence of UTF-16 encoded code points, as described in 6.1.4.

The following steps are taken:

1. Let S be this value.
2. Return ? TrimString(S, "end").

> NOTE The trimEnd function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.29 String.prototype.trimStart ( ) <div id="sec-string.prototype.trimstart"></div>

This function interprets a String value as a sequence of UTF-16 encoded code points, as described in 6.1.4.

The following steps are taken:

1. Let S be this value.
2. Return ? TrimString(S, "start").

> NOTE The trimStart function is intentionally generic; it does not require that its this value be a String object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 21.1.3.30 String.prototype.valueOf ( ) <div id="sec-string.prototype.valueof"></div>

When the valueOf method is called, the following steps are taken:

1. Return ? thisStringValue(this value).

#### 21.1.3.31 String.prototype [ @@iterator ] ( ) <div id="sec-string.prototype-@@iterator"></div>

When the @@iterator method is called it returns an Iterator object (25.1.1.2) that iterates over the code points of a String value, returning each code point as a String value. The following steps are taken:

1. Let O be ? RequireObjectCoercible(this value).
2. Let S be ? ToString(O).
3. Return CreateStringIterator(S).

The value of the name property of this function is "[Symbol.iterator]".

### 21.1.4 String 实例属性 <div id="sec-properties-of-string-instances"></div>

String instances are String exotic objects and have the internal methods specified for such objects. String instances inherit properties from the String prototype object. String instances also have a [[StringData]] internal slot.

String instances have a "length" property, and a set of enumerable properties with integer-indexed names.

#### 21.1.4.1 length <div id="sec-properties-of-string-instances-length"></div>

The number of elements in the String value represented by this String object.

Once a String object is initialized, this property is unchanging. It has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 21.1.5 String 迭代器对象 <div id="sec-string-iterator-objects"></div>

A String Iterator is an object, that represents a specific iteration over some specific String instance object. There is not a named constructor for String Iterator objects. Instead, String iterator objects are created by calling certain methods of String instance objects

#### 21.1.5.1 CreateStringIterator ( string ) <div id="sec-createstringiterator"></div>

Several methods of String objects return Iterator objects. The abstract operation CreateStringIterator with argument string is used to create such iterator objects. It performs the following steps:

1. Assert: Type(string) is String.
2. Let iterator be ObjectCreate(%StringIteratorPrototype%, « [[IteratedString]], [[StringIteratorNextIndex]] »).
3. Set iterator.[[IteratedString]] to string.
4. Set iterator.[[StringIteratorNextIndex]] to 0.
5. Return iterator.

#### 21.1.5.2 The %StringIteratorPrototype% Object <div id="sec-%stringiteratorprototype%-object"></div>

The %StringIteratorPrototype% object:

- has properties that are inherited by all String Iterator Objects.
- is an ordinary object.
- has a [[Prototype]] internal slot whose value is the intrinsic object %IteratorPrototype%.
- has the following properties:

##### 21.1.5.2.1 %StringIteratorPrototype%.next ( ) <div id="sec-%stringiteratorprototype%.next"></div>

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have all of the internal slots of a String Iterator Instance (21.1.5.3), throw a TypeError exception.
4. Let s be O.[[IteratedString]].
5. If s is undefined, return CreateIterResultObject(undefined, true).
6. Let position be O.[[StringIteratorNextIndex]].
7. Let len be the length of s.
8. If position ≥ len, then
   1. Set O.[[IteratedString]] to undefined.
   2. Return CreateIterResultObject(undefined, true).
9. Let first be the numeric value of the code unit at index position within s.
10. If first < 0xD800 or first > 0xDBFF or position + 1 = len, let resultString be the String value consisting of the
    single code unit first.
11. Else,
    1. Let second be the numeric value of the code unit at index position + 1 within the String s.
    2. If second < 0xDC00 or second > 0xDFFF, let resultString be the String value consisting of the single code unit first.
    3. Else, let resultString be the string-concatenation of the code unit first and the code unit second.
12. Let resultSize be the number of code units in resultString.
13. Set O.[[StringIteratorNextIndex]] to position + resultSize.
14. Return CreateIterResultObject(resultString, false).

##### 21.1.5.2.2 %StringIteratorPrototype% [ @@toStringTag ] <div id="sec-%stringiteratorprototype%-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value "String Iterator".

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 21.1.5.3 String 迭代器实例的属性 <div id="sec-properties-of-string-iterator-instances"></div>

String Iterator instances are ordinary objects that inherit properties from the %StringIteratorPrototype% intrinsic object. String Iterator instances are initially created with the internal slots listed in Table 52.

Table 52: Internal Slots of String Iterator Instances

| Internal Slot               | Description                                                  |
| --------------------------- | ------------------------------------------------------------ |
| [[IteratedString]]          | The String value whose code units are being iterated.        |
| [[StringIteratorNextIndex]] | The integer index of the next string index to be examined by this iteration. |

## 21.2 RegExp（正则表达式）对象 <div id="sec-regexp-regular-expression-objects"></div>

A RegExp object contains a regular expression and the associated flags.

> NOTE The form and functionality of regular expressions is modelled after the regular expression facility in the Perl 5 programming language.

### 21.2.1 模式 <div id="sec-patterns"></div>

The RegExp constructor applies the following grammar to the input pattern String. An error occurs if the grammar cannot interpret the String as an expansion of Pattern.

Syntax

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

Each \u TrailSurrogate for which the choice of associated u LeadSurrogate is ambiguous shall be associated with the nearest possible u LeadSurrogate that would otherwise have no corresponding \u TrailSurrogate.

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

- It is a Syntax Error if NcapturingParens ≥ 232 - 1.
- It is a Syntax Error if Pattern contains multiple GroupSpecifiers whose enclosed RegExpIdentifierNames have the same StringValue.

```
QuantifierPrefix :: { DecimalDigits , DecimalDigits }
```

- It is a Syntax Error if the MV of the first DecimalDigits is larger than the MV of the second DecimalDigits.

```
AtomEscape :: k GroupName
```

- It is a Syntax Error if the enclosing Pattern does not contain a GroupSpecifier with an enclosed RegExpIdentifierName whose StringValue equals the StringValue of the RegExpIdentifierName of this production's GroupName.

```
AtomEscape :: DecimalEscape
```

- It is a Syntax Error if the CapturingGroupNumber of DecimalEscape is larger than NcapturingParens (21.2.2.1).

```
NonemptyClassRanges :: ClassAtom - ClassAtom ClassRanges
```

- It is a Syntax Error if IsCharacterClass of the first ClassAtom is true or IsCharacterClass of the second ClassAtom is true.
- It is a Syntax Error if IsCharacterClass of the first ClassAtom is false and IsCharacterClass of the second ClassAtom is false and the CharacterValue of the first ClassAtom is larger than the CharacterValue of the second ClassAtom.

```
NonemptyClassRangesNoDash :: ClassAtomNoDash - ClassAtom ClassRanges
```

- It is a Syntax Error if IsCharacterClass of ClassAtomNoDash is true or IsCharacterClass of ClassAtom is true. 
- It is a Syntax Error if IsCharacterClass of ClassAtomNoDash is false and IsCharacterClass of ClassAtom is false and the CharacterValue of ClassAtomNoDash is larger than the CharacterValue of ClassAtom.

```
RegExpIdentifierStart :: \ RegExpUnicodeEscapeSequence
```

- It is a Syntax Error if SV(RegExpUnicodeEscapeSequence) is none of "$", or "_", or the UTF16Encoding of a code point matched by the UnicodeIDStart lexical grammar production.

```
RegExpIdentifierPart :: \ RegExpUnicodeEscapeSequence
```

- It is a Syntax Error if SV(RegExpUnicodeEscapeSequence) is none of "$", or "_", or the UTF16Encoding of either  or , or the UTF16Encoding of a Unicode code point that would be matched by the UnicodeIDContinue lexical grammar production.

```
UnicodePropertyValueExpression :: UnicodePropertyName = UnicodePropertyValue
```

- It is a Syntax Error if the List of Unicode code points that is SourceText of UnicodePropertyName is not identical to a List of Unicode code points that is a Unicode property name or property alias listed in the “Property name and aliases” column of Table 54. 
- It is a Syntax Error if the List of Unicode code points that is SourceText of UnicodePropertyValue is not identical to a List of Unicode code points that is a value or value alias for the Unicode property or property alias given by SourceText of UnicodePropertyName listed in the “Property value and aliases” column of the corresponding tables Table 56 or Table 57.

```
UnicodePropertyValueExpression :: LoneUnicodePropertyNameOrValue
```

- It is a Syntax Error if the List of Unicode code points that is SourceText of LoneUnicodePropertyNameOrValue is not identical to a List of Unicode code points that is a Unicode general category or general category alias listed in the “Property value and aliases” column of Table 56, nor a binary property or binary property alias listed in the “Property name and aliases” column of Table 55.

#### 21.2.1.2 SS: CapturingGroupNumber <div id="sec-patterns-static-semantics-capturing-group-number"></div>

```
DecimalEscape :: NonZeroDigit
```

1. Return the MV of NonZeroDigit.

```
DecimalEscape :: NonZeroDigit DecimalDigits
```

1. Let n be the number of code points in DecimalDigits.
2. Return (the MV of NonZeroDigit × 10n) plus the MV of DecimalDigits.

The definitions of “the MV of NonZeroDigit” and “the MV of DecimalDigits” are in 11.8.3.

#### 21.2.1.3 SS: IsCharacterClass <div id="sec-patterns-static-semantics-is-character-class"></div>

```
ClassAtom :: -
ClassAtomNoDash :: SourceCharacter but not one of \ or ] or -
ClassEscape :: b
ClassEscape :: -
ClassEscape :: CharacterEscape
```

1. Return false.

```
ClassEscape :: CharacterClassEscape
```

1. Return true.

#### 21.2.1.4 SS: CharacterValue <div id="sec-patterns-static-semantics-character-value"></div>

```
ClassAtom :: -
```

1. Return the code point value of U+002D (HYPHEN-MINUS).

```
ClassAtomNoDash :: SourceCharacter but not one of \ or ] or -
```

1. Let ch be the code point matched by SourceCharacter.
2. Return the code point value of ch.

```
ClassEscape :: b
```

1. Return the code point value of U+0008 (BACKSPACE).

```
ClassEscape :: -
```

1. Return the code point value of U+002D (HYPHEN-MINUS).

```
CharacterEscape :: ControlEscape
```

1. Return the code point value according to Table 53.

Table 53: ControlEscape Code Point Values

| ControlEscape | Code Point Value | Code Point | Unicode Name         | Symbol |
| ------------- | ---------------- | ---------- | -------------------- | ------ |
| `t`           | 9                | `U+0009`   | CHARACTER TABULATION | <HT>   |
| `n`           | 10               | `U+000A`   | LINE FEED (LF)       | <LF>   |
| `v`           | 11               | `U+000B`   | LINE TABULATION      | <VT>   |
| `f`           | 12               | `U+000C`   | FORM FEED (FF)       | <FF>   |
| `r`           | 13               | `U+000D`   | CARRIAGE RETURN (CR) | <CR>   |

```
CharacterEscape :: c ControlLetter
```

1. Let ch be the code point matched by ControlLetter.
2. Let i be ch's code point value.
3. Return the remainder of dividing i by 32.

```
CharacterEscape :: 0 [lookahead ∉ DecimalDigit]
```

1. Return the code point value of U+0000 (NULL).

> NOTE \0 represents the  character and cannot be followed by a decimal digit.

```
CharacterEscape :: HexEscapeSequence
```

1. Return the numeric value of the code unit that is the SV of HexEscapeSequence.

```
RegExpUnicodeEscapeSequence :: u LeadSurrogate \u TrailSurrogate
```

1. Let lead be the CharacterValue of LeadSurrogate.
2. Let trail be the CharacterValue of TrailSurrogate.
3. Let cp be UTF16Decode(lead, trail).
4. Return the code point value of cp.

```
RegExpUnicodeEscapeSequence :: u LeadSurrogate
```

1. Return the CharacterValue of LeadSurrogate.

```
RegExpUnicodeEscapeSequence :: u TrailSurrogate
```

1. Return the CharacterValue of TrailSurrogate.

```
RegExpUnicodeEscapeSequence :: u NonSurrogate
```

1. Return the CharacterValue of NonSurrogate

```
RegExpUnicodeEscapeSequence :: u Hex4Digits
```

1. Return the MV of Hex4Digits

```
RegExpUnicodeEscapeSequence :: u{ CodePoint }
```

1. Return the MV of CodePoint

```
LeadSurrogate :: Hex4Digits
TrailSurrogate :: Hex4Digits
NonSurrogate :: Hex4Digits
```

1. Return the MV of HexDigits.

```
CharacterEscape :: IdentityEscape
```

1. Let ch be the code point matched by IdentityEscape.
2. Return the code point value of ch.

#### 21.2.1.5 SS: SourceText <div id="sec-static-semantics-sourcetext"></div>

```
UnicodePropertyNameCharacters :: UnicodePropertyNameCharacter UnicodePropertyNameCharacters
UnicodePropertyValueCharacters :: UnicodePropertyValueCharacter UnicodePropertyValueCharacters
```

1. Return the List, in source text order, of Unicode code points in the source text matched by this production.

#### 21.2.1.6 SS: StringValue <div id="sec-regexp-identifier-names-static-semantics-stringvalue"></div>

```
RegExpIdentifierName[U] ::
RegExpIdentifierStart[?U]
RegExpIdentifierName[?U] RegExpIdentifierPart[?U]
```

1. Return the String value consisting of the sequence of code units corresponding to RegExpIdentifierName. In determining the sequence any occurrences of \ RegExpUnicodeEscapeSequence are first replaced with the code point represented by the RegExpUnicodeEscapeSequence and then the code points of the entire RegExpIdentifierName are converted to code units by UTF16Encoding each code point.

### 21.2.2 模式语义 <div id="sec-pattern-semantics"></div>

A regular expression pattern is converted into an internal procedure using the process described below. An implementation is encouraged to use more efficient algorithms than the ones listed below, as long as the results are the same. The internal procedure is used as the value of a RegExp object's [[RegExpMatcher]] internal slot.

A Pattern is either a BMP pattern or a Unicode pattern depending upon whether or not its associated flags contain a "u". A BMP pattern matches against a String interpreted as consisting of a sequence of 16-bit values that are Unicode code points in the range of the Basic Multilingual Plane. A Unicode pattern matches against a String interpreted as consisting of Unicode code points encoded using UTF-16. In the context of describing the behaviour of a BMP pattern “character” means a single 16-bit Unicode BMP code point. In the context of describing the behaviour of a Unicode pattern “character” means a UTF-16 encoded code point (6.1.4). In either context, “character value” means the numeric value of the corresponding non-encoded code point.

The syntax and semantics of Pattern is defined as if the source code for the Pattern was a List of SourceCharacter values where each SourceCharacter corresponds to a Unicode code point. If a BMP pattern contains a non-BMP SourceCharacter the entire pattern is encoded using UTF-16 and the individual code units of that encoding are used as the elements of the List.

> NOTE
>
> For example, consider a pattern expressed in source text as the single non-BMP character U+1D11E (MUSICAL SYMBOL G CLEF). Interpreted as a Unicode pattern, it would be a single element (character) List consisting of the single code point 0x1D11E. However, interpreted as a BMP pattern, it is first UTF-16 encoded to produce a two element List consisting of the code units 0xD834 and 0xDD1E.
>
> Patterns are passed to the RegExp constructor as ECMAScript String values in which non-BMP characters are UTF-16 encoded. For example, the single character MUSICAL SYMBOL G CLEF pattern, expressed as a String value, is a String of length 2 whose elements were the code units 0xD834 and 0xDD1E. So no further translation of the string would be necessary to process it as a BMP pattern consisting of two pattern characters. However, to process it as a Unicode pattern UTF16Decode must be used in producing a List consisting of a single pattern character, the code point U+1D11E.
>
> An implementation may not actually perform such translations to or from UTF-16, but the semantics of this specification requires that the result of pattern matching be as if such translations were performed.

#### 21.2.2.1 表示法 <div id="sec-notation"></div>

The descriptions below use the following variables:

- Input is a List consisting of all of the characters, in order, of the String being matched by the regular expression pattern. Each character is either a code unit or a code point, depending upon the kind of pattern involved. The notation Input[n] means the n th character of Input, where n can range between 0 (inclusive) and InputLength (exclusive).
- InputLength is the number of characters in Input.
- NcapturingParens is the total number of left-capturing parentheses (i.e. the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes) in the pattern. A left-capturing parenthesis is any ( pattern character that is matched by the ( terminal of the Atom :: ( GroupSpecifier Disjunction ) production.
- DotAll is true if the RegExp object's [[OriginalFlags]] internal slot contains "s" and otherwise is false.
- IgnoreCase is true if the RegExp object's [[OriginalFlags]] internal slot contains "i" and otherwise is false.
- Multiline is true if the RegExp object's [[OriginalFlags]] internal slot contains "m" and otherwise is false.
- Unicode is true if the RegExp object's [[OriginalFlags]] internal slot contains "u" and otherwise is false.

Furthermore, the descriptions below use the following internal data structures:

- A CharSet is a mathematical set of characters, either code units or code points depending up the state of the Unicode flag. “All characters” means either all code unit values or all code point values also depending upon the state of Unicode.
- A State is an ordered pair (endIndex, captures) where endIndex is an integer and captures is a List of NcapturingParens values. States are used to represent partial match states in the regular expression matching algorithms. The endIndex is one plus the index of the last input character matched so far by the pattern, while captures holds the results of capturing parentheses. The n th element of captures is either a List that represents the value obtained by the n th set of capturing parentheses or undefined if the n th set of capturing parentheses hasn't been reached yet. Due to backtracking, many States may be in use at any time during the matching process
- A MatchResult is either a State or the special token failure that indicates that the match failed.
- A Continuation procedure is an internal closure (i.e. an internal procedure with some arguments already bound to values) that takes one State argument and returns a MatchResult result. If an internal closure references variables which are bound in the function that creates the closure, the closure uses the values that these variables had at the time the closure was created. The Continuation attempts to match the remaining portion (specified by the closure's already-bound arguments) of the pattern against Input, starting at the intermediate state given by its State argument. If the match succeeds, the Continuation returns the final State that it reached; if the match fails, the Continuation returns failure.
- A Matcher procedure is an internal closure that takes two arguments — a State and a Continuation — and returns a MatchResult result. A Matcher attempts to match a middle subpattern (specified by the closure's already-bound arguments) of the pattern against Input, starting at the intermediate state given by its State argument. The Continuation argument should be a closure that matches the rest of the pattern. After matching the subpattern of a pattern to obtain a new State, the Matcher then calls Continuation on that new State to test if the rest of the pattern can match as well. If it can, the Matcher returns the State returned by Continuation; if not, the Matcher may try different choices at its choice points, repeatedly calling Continuation until it either succeeds or all possibilities have been exhausted.
- An AssertionTester procedure is an internal closure that takes a State argument and returns a Boolean result. The assertion tester tests a specific condition (specified by the closure's already-bound arguments) against the current place in Input and returns true if the condition matched or false if not.

#### 21.2.2.2 模式 <div id="sec-pattern"></div>

The production Pattern :: Disjunction evaluates as follows:

1. Evaluate Disjunction with +1 as its direction argument to obtain a Matcher m.
2. Return an internal closure that takes two arguments, a String str and an integer index, and performs the following steps:
     1. Assert: index ≤ the length of str.
     2. If Unicode is true, let Input be a List consisting of the sequence of code points of str interpreted as a UTF-16 encoded (6.1.4) Unicode string. Otherwise, let Input be a List consisting of the sequence of code units that are the elements of str. Input will be used throughout the algorithms in 21.2.2. Each element of Input is considered to be a character.
     3. Let InputLength be the number of characters contained in Input. This variable will be used throughout the algorithms in 21.2.2.
     4. Let listIndex be the index into Input of the character that was obtained from element index of str.
     5. Let c be a Continuation that always returns its State argument as a successful MatchResult.
     6. Let cap be a List of NcapturingParens undefined values, indexed 1 through NcapturingParens.
     7. Let x be the State (listIndex, cap).
     8. Call m(x, c) and return its result.

> NOTE A Pattern evaluates (“compiles”) to an internal procedure value. RegExpBuiltinExec can then apply this procedure to a String and an offset within the String to determine whether the pattern would match starting at exactly that offset within the String, and, if it does match, what the values of the capturing parentheses would be. The algorithms in 21.2.2 are designed so that compiling a pattern may throw a SyntaxError exception; on the other hand, once the pattern is successfully compiled, applying the resulting internal procedure to find a match in a String cannot throw an exception (except for any host-defined exceptions that can occur anywhere such as out-of-memory)

#### 21.2.2.3 析取 <div id="sec-disjunction"></div>

With parameter direction.

The production Disjunction :: Alternative evaluates as follows:

1. Evaluate Alternative with argument direction to obtain a Matcher m.
2. Return m.

The production Disjunction :: Alternative | Disjunction evaluates as follows:

1. Evaluate Alternative with argument direction to obtain a Matcher m1.
2. Evaluate Disjunction with argument direction to obtain a Matcher m2.
3. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
1. Call m1(x, c) and let r be its result.
2. If r is not failure, return r.
3. Call m2(x, c) and return its result.

> NOTE
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

The production Alternative :: [empty] evaluates as follows:

1. Return a Matcher that takes two arguments, a State x and a Continuation c, and returns the result of calling c(x).

The production Alternative :: Alternative Term evaluates as follows:

1. Evaluate Alternative with argument direction to obtain a Matcher m1.
2. Evaluate Term with argument direction to obtain a Matcher m2.
3. If direction is equal to +1, then
1. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
  1. Let d be a Continuation that takes a State argument y and returns the result of calling m2(y, c).
  2. Call m1(x, d) and return its result.
4. Else,
  1. Assert: direction is equal to -1.
  2. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
        1. Let d be a Continuation that takes a State argument y and returns the result of calling m1(y, c).
            2. Call m2(x, d) and return its result.

> NOTE Consecutive Terms try to simultaneously match consecutive portions of Input. When direction is equal to +1, if the left Alternative, the right Term, and the sequel of the regular expression all have choice points, all choices in the sequel are tried before moving on to the next choice in the right Term, and all choices in the right Term are tried before moving on to the next choice in the left Alternative. When direction is equal to -1, the evaluation order of Alternative and Term are reversed.

#### 21.2.2.5 匹配项 <div id="sec-term"></div>

With parameter direction.

The production Term :: Assertion evaluates as follows:

1. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
  1. Evaluate Assertion to obtain an AssertionTester t.
  2. Call t(x) and let r be the resulting Boolean value.
  3. If r is false, return failure.
  4. Call c(x) and return its result

> NOTE The AssertionTester is independent of direction.

The production Term :: Atom evaluates as follows:

1. Return the Matcher that is the result of evaluating Atom with argument direction.

The production Term :: Atom Quantifier evaluates as follows:

1. Evaluate Atom with argument direction to obtain a Matcher m.
2. Evaluate Quantifier to obtain the three results: an integer min, an integer (or ∞) max, and Boolean greedy.
3. Assert: If max is finite, then max is not less than min.
4. Let parenIndex be the number of left-capturing parentheses in the entire regular expression that occur to the left of this Term. This is the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes prior to or enclosing this Term.
5. Let parenCount be the number of left-capturing parentheses in Atom. This is the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes enclosed by Atom.
6. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
1. Call RepeatMatcher(m, min, max, greedy, x, c, parenIndex, parenCount) and return its result.

##### 21.2.2.5.1 RS: RepeatMatcher ( m, min, max, greedy, x, c, parenIndex, parenCount ) <div id="sec-runtime-semantics-repeatmatcher-abstract-operation"></div>

The abstract operation RepeatMatcher takes eight parameters, a Matcher m, an integer min, an integer (or ∞) max, a Boolean greedy, a State x, a Continuation c, an integer parenIndex, and an integer parenCount, and performs the following steps:

1. If max is zero, return c(x).
2. Let d be an internal Continuation closure that takes one State argument y and performs the following steps when evaluated:
1. If min is zero and y's endIndex is equal to x's endIndex, return failure.
2. If min is zero, let min2 be zero; otherwise let min2 be min - 1.
3. If max is ∞, let max2 be ∞; otherwise let max2 be max - 1.
4. Call RepeatMatcher(m, min2, max2, greedy, y, c, parenIndex, parenCount) and return its result.
3. Let cap be a copy of x's captures List.
4. For each integer k that satisfies parenIndex < k and k ≤ parenIndex + parenCount, set cap[k] to undefined.
5. Let e be x's endIndex.
6. Let xr be the State (e, cap).
7. If min is not zero, return m(xr, d).
8. If greedy is false, then
  1. Call c(x) and let z be its result.
  2. If z is not failure, return z.
  3. Call m(xr, d) and return its result.
9. Call m(xr, d) and let z be its result.
10. If z is not failure, return z.
11. Call c(x) and return its result.

> NOTE 1 
>
> An Atom followed by a Quantifier is repeated the number of times specified by the Quantifier. A Quantifier can be nongreedy, in which case the Atom pattern is repeated as few times as possible while still matching the sequel, or it can be greedy, in which case the Atom pattern is repeated as many times as possible while still matching the sequel. The Atom pattern is repeated rather than the input character sequence that it matches, so different repetitions of the Atom can match different input substrings.
>
> NOTE 2
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
> NOTE 3
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
> NOTE 4
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

The production Assertion :: ^ evaluates as follows:

1. Return an internal AssertionTester closure that takes a State argument x and performs the following steps when evaluated:
1. Let e be x's endIndex.
2. If e is zero, return true.
3. If Multiline is false, return false.
4. If the character Input[e - 1] is one of LineTerminator, return true.
5. Return false.

> NOTE Even when the y flag is used with a pattern, ^ always matches only at the beginning of Input, or (if Multiline is true) at the beginning of a line.

The production Assertion :: $ evaluates as follows:

1. Return an internal AssertionTester closure that takes a State argument x and performs the following steps when evaluated:
1. Let e be x's endIndex.
2. If e is equal to InputLength, return true.
3. If Multiline is false, return false.
4. If the character Input[e] is one of LineTerminator, return true.
5. Return false

The production Assertion :: \ b evaluates as follows:

1. Return an internal AssertionTester closure that takes a State argument x and performs the following steps when evaluated:
  1. Let e be x's endIndex.
  2. Call IsWordChar(e - 1) and let a be the Boolean result.
  3. Call IsWordChar(e) and let b be the Boolean result.
  4. If a is true and b is false, return true.
  5. If a is false and b is true, return true.
  6. Return false.

The production Assertion :: \ B evaluates as follows:

1. Return an internal AssertionTester closure that takes a State argument x and performs the following steps when evaluated:
1. Let e be x's endIndex.
2. Call IsWordChar(e - 1) and let a be the Boolean result.
3. Call IsWordChar(e) and let b be the Boolean result.
4. If a is true and b is false, return false.
5. If a is false and b is true, return false.
6. Return true.

The production Assertion :: ( ? = Disjunction ) evaluates as follows:

1. Evaluate Disjunction with +1 as its direction argument to obtain a Matcher m.
2. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
1. Let d be a Continuation that always returns its State argument as a successful MatchResult.
2. Call m(x, d) and let r be its result.
3. If r is failure, return failure.
4. Let y be r's State.
5. Let cap be y's captures List.
6. Let xe be x's endIndex.
7. Let z be the State (xe, cap).
8. Call c(z) and return its result.

The production Assertion :: ( ? ! Disjunction ) evaluates as follows:

1. Evaluate Disjunction with +1 as its direction argument to obtain a Matcher m.
2. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
1. Let d be a Continuation that always returns its State argument as a successful MatchResult.
2. Call m(x, d) and let r be its result.
3. If r is not failure, return failure.
4. Call c(x) and return its result.

The production Assertion :: ( ? <= Disjunction ) evaluates as follows:

1. Evaluate Disjunction with -1 as its direction argument to obtain a Matcher m.
2. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
  1. Let d be a Continuation that always returns its State argument as a successful MatchResult.
  2. Call m(x, d) and let r be its result.
  3. If r is failure, return failure.
  4. Let y be r's State.
  5. Let cap be y's captures List.
  6. Let xe be x's endIndex.
  7. Let z be the State (xe, cap).
  8. Call c(z) and return its result

The production Assertion :: ( ? <! Disjunction ) evaluates as follows:

1. Evaluate Disjunction with -1 as its direction argument to obtain a Matcher m.
2. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
1. Let d be a Continuation that always returns its State argument as a successful MatchResult.
2. Call m(x, d) and let r be its result.
3. If r is not failure, return failure.
4. Call c(x) and return its result.

##### 21.2.2.6.1 RS: WordCharacters ( ) <div id="sec-runtime-semantics-wordcharacters-abstract-operation"></div>

The abstract operation WordCharacters performs the following steps:

1. Let A be a set of characters containing the sixty-three characters:

   ```
   a b c d e f g h i j k l m n o p q r s t u v w x y z
   A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
   0 1 2 3 4 5 6 7 8 9 _
   ```

2. Let U be an empty set.
3. For each character c not in set A where Canonicalize(c) is in A, add c to U.
4. Assert: Unless Unicode and IgnoreCase are both true, U is empty.
5. Add the characters in set U to set A.
6. Return A.

##### 21.2.2.6.2 RS: IsWordChar ( e ) <div id="sec-runtime-semantics-iswordchar-abstract-operation"></div>

The abstract operation IsWordChar takes an integer parameter e and performs the following steps:

1. If e is -1 or e is InputLength, return false.
2. Let c be the character Input[e].
3. Let wordChars be the result of ! WordCharacters().
4. If c is in wordChars, return true.
5. Return false.

#### 21.2.2.7 量词 <div id="sec-quantifier"></div>

The production Quantifier :: QuantifierPrefix evaluates as follows:

1. Evaluate QuantifierPrefix to obtain the two results: an integer min and an integer (or ∞) max.
2. Return the three results min, max, and true.

The production Quantifier :: QuantifierPrefix ? evaluates as follows:

1. Evaluate QuantifierPrefix to obtain the two results: an integer min and an integer (or ∞) max.
2. Return the three results min, max, and false.

The production QuantifierPrefix :: * evaluates as follows:

1. Return the two results 0 and ∞.

The production QuantifierPrefix :: + evaluates as follows:

1. Return the two results 1 and ∞.

The production QuantifierPrefix :: ? evaluates as follows:

1. Return the two results 0 and 1.

The production QuantifierPrefix :: { DecimalDigits } evaluates as follows:

1. Let i be the MV of DecimalDigits (see 11.8.3).
2. Return the two results i and i.

The production QuantifierPrefix :: { DecimalDigits , } evaluates as follows:

1. Let i be the MV of DecimalDigits.
2. Return the two results i and ∞.

The production QuantifierPrefix :: { DecimalDigits , DecimalDigits } evaluates as follows:

1. Let i be the MV of the first DecimalDigits.
2. Let j be the MV of the second DecimalDigits.
3. Return the two results i and j.

#### 21.2.2.8 原子 <div id="sec-atom"></div>

With parameter direction.

The production Atom :: PatternCharacter evaluates as follows:

1. Let ch be the character matched by PatternCharacter.
2. Let A be a one-element CharSet containing the character ch.
3. Call CharacterSetMatcher(A, false, direction) and return its Matcher result.

The production Atom :: . evaluates as follows:

1. If DotAll is true, then
1. Let A be the set of all characters.
2. Otherwise, let A be the set of all characters except LineTerminator.
3. Call CharacterSetMatcher(A, false, direction) and return its Matcher result.

The production Atom :: \ AtomEscape evaluates as follows:

1. Evaluate CharacterClass to obtain a CharSet A and a Boolean invert.
2. Call CharacterSetMatcher(A, invert, direction) and return its Matcher result.

The production Atom :: ( GroupSpecifier Disjunction ) evaluates as follows:

1. Evaluate Disjunction with argument direction to obtain a Matcher m.
2. Let parenIndex be the number of left-capturing parentheses in the entire regular expression that occur to the left of this Atom. This is the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes prior to or enclosing this Atom.
3. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
  1. Let d be an internal Continuation closure that takes one State argument y and performs the following steps:
        1. Let cap be a copy of y's captures List.
            2. Let xe be x's endIndex.
                3. Let ye be y's endIndex.
                    4. If direction is equal to +1, then
      1. Assert: xe ≤ ye.
      2. Let s be a new List whose elements are the characters of Input at indices xe (inclusive) through ye (exclusive).
        5. Else,
      1. Assert: direction is equal to -1.
      2. Assert: ye ≤ xe.
      3. Let s be a new List whose elements are the characters of Input at indices ye (inclusive) through xe
          (exclusive).
        6. Set cap[parenIndex + 1] to s.
        7. Let z be the State (ye, cap).
        8. Call c(z) and return its result.
  2. Call m(x, d) and return its result.

The production Atom :: ( ? : Disjunction ) evaluates as follows:

1. Return the Matcher that is the result of evaluating Disjunction with argument direction.

##### 21.2.2.8.1 RS: CharacterSetMatcher ( A, invert, direction ) <div id="sec-runtime-semantics-charactersetmatcher-abstract-operation"></div>

The abstract operation CharacterSetMatcher takes three arguments, a CharSet A, a Boolean flag invert, and an integer direction, and performs the following steps:

1. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps when evaluated:
  1. Let e be x's endIndex.
  2. Let f be e + direction.
  3. If f < 0 or f > InputLength, return failure.
  4. Let index be min(e, f).
  5. Let ch be the character Input[index].
  6. Let cc be Canonicalize(ch).
  7. If invert is false, then
        1. If there does not exist a member a of set A such that Canonicalize(a) is cc, return failure.
  8. Else,
        1. Assert: invert is true.
            2. If there exists a member a of set A such that Canonicalize(a) is cc, return failure.
  9. Let cap be x's captures List.
  10. Let y be the State (f, cap).
  11. Call c(y) and return its result.

##### 21.2.2.8.2 RS: Canonicalize ( ch ) <div id="sec-runtime-semantics-canonicalize-ch"></div>

The abstract operation Canonicalize takes a character parameter ch and performs the following steps:

1. If IgnoreCase is false, return ch.
2. If Unicode is true, then
  1. If the file CaseFolding.txt of the Unicode Character Database provides a simple or common case folding

    mapping for ch, return the result of applying that mapping to ch.
  2. Return ch.
3. Else,
  1. Assert: ch is a UTF-16 code unit.
  2. Let s be the String value consisting of the single code unit ch.
  3. Let u be the same result produced as if by performing the algorithm for String.prototype.toUpperCase using s as the this value.
  4. Assert: Type(u) is String.
  5. If u does not consist of a single code unit, return ch.
  6. Let cu be u's single code unit element.
  7. If the numeric value of ch ≥ 128 and the numeric value of cu < 128, return ch.
  8. Return cu.

> NOTE 1
>
> Parentheses of the form ( Disjunction ) serve both to group the components of the Disjunction pattern together and to save the result of the match. The result can be used either in a backreference (\ followed by a nonzero decimal number), referenced in a replace String, or returned as part of an array from the regular expression matching internal procedure. To inhibit the capturing behaviour of parentheses, use the form (?: Disjunction ) instead.
>
> NOTE 2
>
> The form (?= Disjunction ) specifies a zero-width positive lookahead. In order for it to succeed, the pattern inside Disjunction must match at the current position, but the current position is not advanced before matching the sequel. If Disjunction can match at the current position in several ways, only the first one is tried. Unlike other regular expression operators, there is no backtracking into a (?= form (this unusual behaviour is inherited from Perl). This only matters when the Disjunction contains capturing parentheses and the sequel of the pattern contains backreferences to those captures.
>
> For example,
>
> /(?=(a+))/.exec("baaabac")
>
> matches the empty String immediately after the first b and therefore returns the array:
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
> NOTE 3
>
> The form (?! Disjunction ) specifies a zero-width negative lookahead. In order for it to succeed, the pattern inside Disjunction must fail to match at the current position. The current position is not advanced before matching the sequel. Disjunction can contain capturing parentheses, but backreferences to them only make sense from within Disjunction itself. Backreferences to these capturing parentheses from elsewhere in the pattern always return undefined because the negative lookahead must fail for the pattern to succeed. For example,
>
> /(.*?)a(?!(a+)b\2c)\2(.*)/.exec("baaabaac")
>
> looks for an a not immediately followed by some positive number n of a's, a b, another n a's (specified by the first \2) and a c. The second \2 is outside the negative lookahead, so it matches against undefined and therefore always succeeds. The whole expression returns the array:
>
> ["baaabaac", "ba", undefined, "abaac"]
>
> NOTE 4
>
> In case-insignificant matches when Unicode is true, all characters are implicitly case-folded using the simple mapping provided by the Unicode standard immediately before they are compared. The simple mapping always maps to a single code point, so it does not map, for example, "ß" (U+00DF) to "SS". It may however map a code point outside the Basic Latin range to a character within, for example, "ſ" (U+017F) to "s". Such characters are not mapped if Unicode is false. This prevents Unicode code points such as U+017F and U+212A from matching regular expressions such as /[a-z]/i, but they will match /[a-z]/ui.

##### 21.2.2.8.3 RS: UnicodeMatchProperty ( p ) <div id="sec-runtime-semantics-unicodematchproperty-p"></div>

The abstract operation UnicodeMatchProperty takes a parameter p that is a List of Unicode code points and performs the following steps:

1. Assert: p is a List of Unicode code points that is identical to a List of Unicode code points that is a Unicode property name or property alias listed in the “Property name and aliases” column of Table 54 or Table 55.
2. Let c be the canonical property name of p as given in the “Canonical property name” column of the corresponding row.
3. Return the List of Unicode code points of c.

Implementations must support the Unicode property names and aliases listed in Table 54 and Table 55. To ensure interoperability, implementations must not support any other property names or aliases.

> NOTE 1 
>
> For example, Script_Extensions (property name) and scx (property alias) are valid, but script_extensions or Scx aren't.
>
> NOTE 2 
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

1. Assert: p is a List of Unicode code points that is identical to a List of Unicode code points that is a canonical, unaliased Unicode property name listed in the “Canonical property name” column of Table 54.
2. Assert: v is a List of Unicode code points that is identical to a List of Unicode code points that is a property value or property value alias for Unicode property p listed in the “Property value and aliases” column of Table 56 or Table 57.
3. Let value be the canonical property value of v as given in the “Canonical property value” column of the corresponding row.
4. Return the List of Unicode code points of value.

Implementations must support the Unicode property value names and aliases listed in Table 56 and Table 57. To ensure interoperability, implementations must not support any other property value names or aliases.

>NOTE 1
>For example, Xpeo and Old_Persian are valid Script_Extensions values, but xpeo and Old Persian aren't.
>NOTE 2
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

The production AtomEscape :: DecimalEscape evaluates as follows:

1. Evaluate DecimalEscape to obtain an integer n.
2. Assert: n ≤ NcapturingParens.
3. Call BackreferenceMatcher(n, direction) and return its Matcher result.

The production AtomEscape :: CharacterEscape evaluates as follows:

1. Evaluate CharacterEscape to obtain a character ch.
2. Let A be a one-element CharSet containing the character ch.
3. Call CharacterSetMatcher(A, false, direction) and return its Matcher result.

The production AtomEscape :: CharacterClassEscape evaluates as follows:

1. Evaluate CharacterClassEscape to obtain a CharSet A.
2. Call CharacterSetMatcher(A, false, direction) and return its Matcher result.

> NOTE An escape sequence of the form \ followed by a nonzero decimal number n matches the result of the nth set of capturing parentheses (21.2.2.1). It is an error if the regular expression has fewer than n capturing parentheses. If the regular expression has n or more capturing parentheses but the nth one is undefined because it has not captured anything, then the backreference always succeeds.

The production AtomEscape :: k GroupName evaluates as follows:

1. Search the enclosing Pattern for an instance of a GroupSpecifier for a RegExpIdentifierName which has a StringValue equal to the StringValue of the RegExpIdentifierName contained in GroupName.
2. Assert: A unique such GroupSpecifier is found.
3. Let parenIndex be the number of left-capturing parentheses in the entire regular expression that occur to the left of the located GroupSpecifier. This is the total number of Atom :: ( GroupSpecifier Disjunction ) Parse Nodes prior to or enclosing the located GroupSpecifier.
4. Call BackreferenceMatcher(parenIndex, direction) and return its Matcher result.

##### 21.2.2.9.1 RS: BackreferenceMatcher ( n, direction ) <div id="sec-backreference-matcher"></div>

The abstract operation BackreferenceMatcher takes two arguments, an integer n and an integer direction, and performs the following steps:

1. Return an internal Matcher closure that takes two arguments, a State x and a Continuation c, and performs the following steps:
   1. Let cap be x's captures [List](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-list-and-record-specification-type).
   2. Let s be cap[n].
   3. If s is undefined, return c(x).
   4. Let e be x's endIndex.
   5. Let len be the number of elements in s.
   6. Let f be e + direction × len.
   7. If f < 0 or f > InputLength, return failure.
   8. Let g be [min](http://www.ecma-international.org/ecma-262/10.0/index.html#eqn-min)(e, f).
   9. If there exists an integer i between 0 (inclusive) and len (exclusive) such that [Canonicalize](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-runtime-semantics-canonicalize-ch)(s[i]) is not the same character value as [Canonicalize](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-runtime-semantics-canonicalize-ch)(Input[g + i]), return failure.
   10. Let y be the State (f, cap).
   11. Call c(y) and return its result.

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

1. Let cv be the CharacterValue of this CharacterEscape.
2. Return the character whose character value is cv.

#### 21.2.2.11 转义十进制 <div id="sec-decimalescape"></div>

The DecimalEscape productions evaluate as follows:

```
DecimalEscape :: NonZeroDigit DecimalDigits
```

1. Return the CapturingGroupNumber of this DecimalEscape.

>NOTE If \ is followed by a decimal number n whose first digit is not 0, then the escape sequence is considered to be a backreference. It is an error if n is greater than the total number of left-capturing parentheses in the entire regular expression.

#### 21.2.2.12 转义字符类 <div id="sec-characterclassescape"></div>

The production CharacterClassEscape :: d evaluates as follows:

1. Return the ten-element set of characters containing the characters 0 through 9 inclusive.

The production CharacterClassEscape :: D evaluates as follows:

1. Return the set of all characters not included in the set returned by CharacterClassEscape :: d .

The production CharacterClassEscape :: s evaluates as follows:

1. Return the set of characters containing the characters that are on the right-hand side of the WhiteSpace or LineTerminator productions.

The production CharacterClassEscape :: S evaluates as follows:

1. Return the set of all characters not included in the set returned by CharacterClassEscape :: s .

The production CharacterClassEscape :: w evaluates as follows:

1. Return the set of all characters returned by WordCharacters().

The production CharacterClassEscape :: W evaluates as follows:

1. Return the set of all characters not included in the set returned by CharacterClassEscape :: w .

The production CharacterClassEscape :: p{ UnicodePropertyValueExpression } evaluates by returning the CharSet containing all Unicode code points included in the CharSet returned by UnicodePropertyValueExpression.

The production CharacterClassEscape :: P{ UnicodePropertyValueExpression } evaluates by returning the CharSet containing all Unicode code points not included in the CharSet returned by UnicodePropertyValueExpression.

The production UnicodePropertyValueExpression :: UnicodePropertyName = UnicodePropertyValue evaluates as follows:

1. Let ps be SourceText of UnicodePropertyName.
2. Let p be ! UnicodeMatchProperty(ps).
3. Assert: p is a Unicode property name or property alias listed in the “Property name and aliases” column of Table 54.
4. Let vs be SourceText of UnicodePropertyValue.
5. Let v be ! UnicodeMatchPropertyValue(p, vs).
6. Return the CharSet containing all Unicode code points whose character database definition includes the property p with value v.

The production UnicodePropertyValueExpression :: LoneUnicodePropertyNameOrValue evaluates as follows:

1. Let s be SourceText of LoneUnicodePropertyNameOrValue.
2. If ! UnicodeMatchPropertyValue("General_Category", s) is identical to a List of Unicode code points that is the name of a Unicode general category or general category alias listed in the “Property value and aliases” column of Table 56, then
  1. Return the CharSet containing all Unicode code points whose character database definition includes the property “General_Category” with value s.
3. Let p be ! UnicodeMatchProperty(s).
4. Assert: p is a binary Unicode property or binary property alias listed in the “Property name and aliases” column of Table 55.
5. Return the CharSet containing all Unicode code points whose character database definition includes the property p with value “True”.

#### 21.2.2.13 字符类 <div id="sec-characterclass"></div>

The production CharacterClass :: [ ClassRanges ] evaluates as follows:

1. Evaluate ClassRanges to obtain a CharSet A.
2. Return the two results A and false.

The production CharacterClass :: [ ^ ClassRanges ] evaluates as follows:

1. Evaluate ClassRanges to obtain a CharSet A.
2. Return the two results A and true.

#### 21.2.2.14 字符范围集 <div id="sec-classranges"></div>

The production ClassRanges :: [empty] evaluates as follows:

1. Return the empty CharSet.

The production ClassRanges :: NonemptyClassRanges evaluates as follows:

1. Return the CharSet that is the result of evaluating NonemptyClassRanges.

#### 21.2.2.15 非空字符范围集 <div id="sec-nonemptyclassranges"></div>

The production NonemptyClassRanges :: ClassAtom evaluates as follows:

1. Return the CharSet that is the result of evaluating ClassAtom.

The production NonemptyClassRanges :: ClassAtom NonemptyClassRangesNoDash evaluates as follows:

1. Evaluate ClassAtom to obtain a CharSet A.

2. Evaluate NonemptyClassRangesNoDash to obtain a CharSet B.
3. Return the union of CharSets A and B.

The production NonemptyClassRanges :: ClassAtom - ClassAtom ClassRanges evaluates as follows:

1. Evaluate the first ClassAtom to obtain a CharSet A.
2. Evaluate the second ClassAtom to obtain a CharSet B.
3. Evaluate ClassRanges to obtain a CharSet C.
4. Call CharacterRange(A, B) and let D be the resulting CharSet.
5. Return the union of CharSets D and C.

##### 21.2.2.15.1 RS: CharacterRange ( A, B ) <div id="sec-runtime-semantics-characterrange-abstract-operation"></div>

The abstract operation CharacterRange takes two CharSet parameters A and B and performs the following steps:

1. Assert: A and B each contain exactly one character.
2. Let a be the one character in CharSet A.
3. Let b be the one character in CharSet B.
4. Let i be the character value of character a.
5. Let j be the character value of character b.
6. Assert: i ≤ j.
7. Return the set containing all characters numbered i through j, inclusive.

#### 21.2.2.16 无连接符非空字符范围集 <div id="sec-nonemptyclassrangesnodash"></div>

The production NonemptyClassRangesNoDash :: ClassAtom evaluates as follows:

1. Return the CharSet that is the result of evaluating ClassAtom.

The production NonemptyClassRangesNoDash :: ClassAtomNoDash NonemptyClassRangesNoDash evaluates as follows:

1. Evaluate ClassAtomNoDash to obtain a CharSet A.
2. Evaluate NonemptyClassRangesNoDash to obtain a CharSet B.
3. Return the union of CharSets A and B

The production NonemptyClassRangesNoDash :: ClassAtomNoDash - ClassAtom ClassRanges evaluates as follows:

1. Evaluate ClassAtomNoDash to obtain a CharSet A.
2. Evaluate ClassAtom to obtain a CharSet B.
3. Evaluate ClassRanges to obtain a CharSet C.
4. Call CharacterRange(A, B) and let D be the resulting CharSet.
5. Return the union of CharSets D and C.

> NOTE 1 
>
> ClassRanges can expand into a single ClassAtom and/or ranges of two ClassAtom separated by dashes. In the latter case the ClassRanges includes all characters between the first ClassAtom and the second ClassAtom, inclusive; an error occurs if either ClassAtom does not represent a single character (for example, if one is \w) or if the first ClassAtom's character value is greater than the second ClassAtom's character value. 
>
> NOTE 2 
>
> Even if the pattern ignores case, the case of the two ends of a range is significant in determining which characters belong to the range. Thus, for example, the pattern /[E-F]/i matches only the letters E, F, e, and f, while the pattern /[E-f]/i matches all upper and lower-case letters in the Unicode Basic Latin block as well as the symbols [, \, ], ^, _, and `.
>
> NOTE 3
>
> A - character can be treated literally or it can denote a range. It is treated literally if it is the first or last character of ClassRanges, the beginning or end limit of a range specification, or immediately follows a range specification.

#### 21.2.2.17 字符类原子 <div id="sec-classatom"></div>

The production ClassAtom :: - evaluates as follows:

1. Return the CharSet containing the single character - U+002D (HYPHEN-MINUS).

The production ClassAtom :: ClassAtomNoDash evaluates as follows:

1. Return the CharSet that is the result of evaluating ClassAtomNoDash.

#### 21.2.2.18 非连接符字符类原子 <div id="sec-classatomnodash"></div>

The production ClassAtomNoDash :: SourceCharacter but not one of \ or ] or - evaluates as follows:

1. Return the CharSet containing the character matched by SourceCharacter.

The production ClassAtomNoDash :: \ ClassEscape evaluates as follows:

1. Return the CharSet that is the result of evaluating ClassEscape.

#### 21.2.2.19 字符可用转义 <div id="sec-classescape"></div>

The ClassEscape productions evaluate as follows:

```
ClassEscape :: b
ClassEscape :: -
ClassEscape :: CharacterEscape
```

1. Let cv be the CharacterValue of this ClassEscape.
2. Let c be the character whose character value is cv.
3. Return the CharSet containing the single character c.

```
ClassEscape :: CharacterClassEscape
```

1. Return the CharSet that is the result of evaluating CharacterClassEscape.

> NOTE
>
> A ClassAtom can use any of the escape sequences that are allowed in the rest of the regular expression except for \b, \B, and backreferences. Inside a CharacterClass, \b means the backspace character, while \B and backreferences raise errors. Using a backreference inside a ClassAtom causes an error.

### 21.2.3 RegExp 构造器 <div id="sec-regexp-constructor"></div>

The RegExp constructor:

- is the intrinsic object %RegExp%.
- is the initial value of the RegExp property of the global object.
- creates and initializes a new RegExp object when called as a function rather than as a constructor. Thus the function call RegExp(…) is equivalent to the object creation expression new RegExp(…) with the same arguments.
- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. Subclass constructors that intend to inherit the specified RegExp behaviour must include a super call to the RegExp constructor to create and initialize subclass instances with the necessary internal slots.

#### 21.2.3.1 RegExp ( pattern, flags ) <div id="sec-regexp-pattern-flags"></div>

The following steps are taken:

1. Let patternIsRegExp be ? IsRegExp(pattern).
2. If NewTarget is undefined, then
  1. Let newTarget be the active function object.
  2. If patternIsRegExp is true and flags is undefined, then
    1. Let patternConstructor be ? Get(pattern, "constructor").
    2. If SameValue(newTarget, patternConstructor) is true, return pattern.
3. Else, let newTarget be NewTarget.
4. If Type(pattern) is Object and pattern has a [[RegExpMatcher]] internal slot, then
  1. Let P be pattern.[[OriginalSource]].
  2. If flags is undefined, let F be pattern.[[OriginalFlags]].
  3. Else, let F be flags.
5. Else if patternIsRegExp is true, then
  1. Let P be ? Get(pattern, "source").
  2. If flags is undefined, then
  3. Let F be ? Get(pattern, "flags").
  4. Else, let F be flags.
6. Else,
  1. Let P be pattern.
  2. Let F be flags.
7. Let O be ? RegExpAlloc(newTarget).
8. Return ? RegExpInitialize(O, P, F).

> NOTE
>
> If pattern is supplied using a StringLiteral, the usual escape sequence substitutions are performed before the String is processed by RegExp. If pattern must contain an escape sequence to be recognized by RegExp, any U+005C (REVERSE SOLIDUS) code points must be escaped within the StringLiteral to prevent them being removed when the contents of the StringLiteral are formed

#### 21.2.3.2 RegExp构造器的抽象操作 <div id="sec-abstract-operations-for-the-regexp-constructor"></div>

##### 21.2.3.2.1 RS: RegExpAlloc ( newTarget ) <div id="sec-regexpalloc"></div>

When the abstract operation RegExpAlloc with argument newTarget is called, the following steps are taken:

1. Let obj be ? OrdinaryCreateFromConstructor(newTarget, "%RegExpPrototype%", « [[RegExpMatcher]],[[OriginalSource]], [[OriginalFlags]] »). 
2. Perform ! DefinePropertyOrThrow(obj, "lastIndex", PropertyDescriptor { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }). 
3. Return obj.

##### 21.2.3.2.2 RS: RegExpInitialize ( obj, pattern, flags ) <div id="sec-regexpinitialize"></div>

When the abstract operation RegExpInitialize with arguments obj, pattern, and flags is called, the following steps are taken:

1. If pattern is undefined, let P be the empty String.
2. Else, let P be ? ToString(pattern).
3. If flags is undefined, let F be the empty String.
4. Else, let F be ? ToString(flags).
5. If F contains any code unit other than "g", "i", "m", "s", "u", or "y" or if it contains the same code unit more than once, throw a SyntaxError exception.
6. If F contains "u", let BMP be false; else let BMP be true.
7. If BMP is true, then
   1. Parse P using the grammars in 21.2.1 and interpreting each of its 16-bit elements as a Unicode BMP code point. UTF-16 decoding is not applied to the elements. The goal symbol for the parse is Pattern[~U, ~N]. If the result of parsing contains a GroupName, reparse with the goal symbol Pattern[~U, +N] and use this result instead. Throw a SyntaxError exception if P did not conform to the grammar, if any elements of P were not matched by the parse, or if any Early Error conditions exist.
   2. Let patternCharacters be a List whose elements are the code unit elements of P.
8. Else,
   1. Parse P using the grammars in 21.2.1 and interpreting P as UTF-16 encoded Unicode code points (6.1.4). The goal symbol for the parse is Pattern[+U, +N]. Throw a SyntaxError exception if P did not conform to the grammar, if any elements of P were not matched by the parse, or if any Early Error conditions exist.
   2. Let patternCharacters be a List whose elements are the code points resulting from applying UTF-16 decoding to P's sequence of elements.
9. Set obj.[[OriginalSource]] to P.
10. Set obj.[[OriginalFlags]] to F.
11. Set obj.[[RegExpMatcher]] to the internal procedure that evaluates the above parse of P by applying the semantics provided in 21.2.2 using patternCharacters as the pattern's List of SourceCharacter values and F as the flag parameters.
12. Perform ? Set(obj, "lastIndex", 0, true).
13. Return obj.

##### 21.2.3.2.3 RS: RegExpCreate ( P, F ) <div id="sec-regexpcreate"></div>

When the abstract operation RegExpCreate with arguments P and F is called, the following steps are taken:

1. Let obj be ? RegExpAlloc(%RegExp%).
2. Return ? RegExpInitialize(obj, P, F).

##### 21.2.3.2.4 RS: EscapeRegExpPattern ( P, F ) <div id="sec-escaperegexppattern"></div>

When the abstract operation EscapeRegExpPattern with arguments P and F is called, the following occurs:

1. Let S be a String in the form of a Pattern[~U] (Pattern[+U] if F contains "u") equivalent to P interpreted as UTF-16 encoded Unicode code points (6.1.4), in which certain code points are escaped as described below. S may or may not be identical to P; however, the internal procedure that would result from evaluating S as a Pattern[~U] (Pattern[+U] if F contains "u") must behave identically to the internal procedure given by the constructed object's [[RegExpMatcher]] internal slot. Multiple calls to this abstract operation using the same values for P and F must produce identical results.
2. The code points / or any LineTerminator occurring in the pattern shall be escaped in S as necessary to ensure that the string-concatenation of "/", S, "/", and F can be parsed (in an appropriate lexical context) as a RegularExpressionLiteral that behaves identically to the constructed regular expression. For example, if P is "/", then S could be "\/" or "\u002F", among other possibilities, but not "/", because /// followed by F would be parsed as a SingleLineComment rather than a RegularExpressionLiteral. If P is the empty String, this specification can be met by letting S be "(?:)".
3. Return S.

### 21.2.4 RegExp 构造器属性 <div id="sec-properties-of-the-regexp-constructor"></div>

The RegExp constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 21.2.4.1 RegExp.prototype <div id="sec-regexp.prototype"></div>

The initial value of RegExp.prototype is the intrinsic object %RegExpPrototype%.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 21.2.4.2 get RegExp [ @@species ] <div id="sec-get-regexp-@@species"></div>

RegExp[@@species] is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Return the this value.

The value of the name property of this function is "get [Symbol.species]".

> NOTE RegExp prototype methods normally use their this object's constructor to create a derived object. However, a subclass constructor may over-ride that default behaviour by redefining its @@species property.

### 21.2.5 RegExp 原型对象属性 <div id="sec-properties-of-the-regexp-prototype-object"></div>

The RegExp prototype object:

- is the intrinsic object %RegExpPrototype%.
- is an ordinary object.
- is not a RegExp instance and does not have a [[RegExpMatcher]] internal slot or any of the other internal slots of RegExp instance objects.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

> NOTE The RegExp prototype object does not have a valueOf property of its own; however, it inherits the valueOf property from the Object prototype object.

#### 21.2.5.1 RegExp.prototype.constructor <div id="sec-regexp.prototype.constructor"></div>

The initial value of RegExp.prototype.constructor is the intrinsic object %RegExp%.

#### 21.2.5.2 RegExp.prototype.exec ( string ) <div id="sec-regexp.prototype.exec"></div>

Performs a regular expression match of string against the regular expression and returns an Array object containing the results of the match, or null if string did not match.

The String ToString(string) is searched for an occurrence of the regular expression pattern as follows:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. If R does not have a [[RegExpMatcher]] internal slot, throw a TypeError exception.
4. Let S be ? ToString(string).
5. Return ? RegExpBuiltinExec(R, S).

##### 21.2.5.2.1 RS: RegExpExec ( R, S ) <div id="sec-regexpexec"></div>

The abstract operation RegExpExec with arguments R and S performs the following steps:

1. Assert: Type(R) is Object.
2. Assert: Type(S) is String.
3. Let exec be ? Get(R, "exec").
4. If IsCallable(exec) is true, then
1. Let result be ? Call(exec, R, « S »).
2. If Type(result) is neither Object or Null, throw a TypeError exception.
3. Return result.
5. If R does not have a [[RegExpMatcher]] internal slot, throw a TypeError exception.
6. Return ? RegExpBuiltinExec(R, S).

> NOTE If a callable exec property is not found this algorithm falls back to attempting to use the built-in RegExp matching algorithm. This provides compatible behaviour for code written for prior editions where most built-in algorithms that use regular expressions did not perform a dynamic property lookup of exec.

##### 21.2.5.2.2 RS: RegExpBuiltinExec ( R, S ) <div id="sec-regexpbuiltinexec"></div>

The abstract operation RegExpBuiltinExec with arguments R and S performs the following steps:

1. Assert: R is an initialized RegExp instance.
2. Assert: Type(S) is String.
3. Let length be the number of code units in S.
4. Let lastIndex be ? ToLength(? Get(R, "lastIndex")).
5. Let flags be R.[[OriginalFlags]].
6. If flags contains "g", let global be true, else let global be false.
7. If flags contains "y", let sticky be true, else let sticky be false.
8. If global is false and sticky is false, set lastIndex to 0.
9. Let matcher be R.[[RegExpMatcher]].
10. If flags contains "u", let fullUnicode be true, else let fullUnicode be false.
11. Let matchSucceeded be false.
12. Repeat, while matchSucceeded is false
    1. If lastIndex > length, then
       1. If global is true or sticky is true, then
          1. Perform ? Set(R, "lastIndex", 0, true).
       2. Return null.
    2. Let r be matcher(S, lastIndex).
    3. If r is failure, then
       1. If sticky is true, then
          1. Perform ? Set(R, "lastIndex", 0, true).
          2. Return null.
       2. Set lastIndex to AdvanceStringIndex(S, lastIndex, fullUnicode).
    4. Else,
       1. Assert: r is a State.
       2. Set matchSucceeded to true.
13. Let e be r's endIndex value.
14. If fullUnicode is true, then
    1. e is an index into the Input character list, derived from S, matched by matcher. Let eUTF be the smallest index into S that corresponds to the character at element e of Input. If e is greater than or equal to the number of elements in Input, then eUTF is the number of code units in S.
    2. Set e to eUTF.
15. If global is true or sticky is true, then
    1. Perform ? Set(R, "lastIndex", e, true).
16. Let n be the number of elements in r's captures List. (This is the same value as 21.2.2.1's NcapturingParens.)
17. Assert: n < 232 - 1.
18. Let A be ! ArrayCreate(n + 1).
19. Assert: The value of A's "length" property is n + 1.
20. Perform ! CreateDataProperty(A, "index", lastIndex).
21. Perform ! CreateDataProperty(A, "input", S).
22. Let matchedSubstr be the matched substring (i.e. the portion of S between offset lastIndex inclusive and offset e exclusive).
23. Perform ! CreateDataProperty(A, "0", matchedSubstr).
24. If R contains any GroupName, then
    1. Let groups be ObjectCreate(null).
25. Else,
    1. Let groups be undefined.
26. Perform ! CreateDataProperty(A, "groups", groups).
27. For each integer i such that i > 0 and i ≤ n, do
    1. Let captureI be ith element of r's captures List.
    2. If captureI is undefined, let capturedValue be undefined.
    3. Else if fullUnicode is true, then
       1. Assert: captureI is a List of code points.
       2. Let capturedValue be the String value whose code units are the UTF16Encoding of the code points of captureI.
    4. Else fullUnicode is false,
       1. Assert: captureI is a List of code units.
       2. Let capturedValue be the String value consisting of the code units of captureI.
    5. Perform ! CreateDataProperty(A, ! ToString(i), capturedValue).
    6. If the ith capture of R was defined with a GroupName, then
       1. Let s be the StringValue of the corresponding RegExpIdentifierName.
       2. Perform ! CreateDataProperty(groups, s, capturedValue).
28. Return A.

##### 21.2.5.2.3 AdvanceStringIndex ( S, index, unicode ) <div id="sec-advancestringindex"></div>

The abstract operation AdvanceStringIndex with arguments S, index, and unicode performs the following steps:

1. Assert: Type(S) is String.
2. Assert: index is an integer such that 0 ≤ index ≤ 253 - 1.
3. Assert: Type(unicode) is Boolean.
4. If unicode is false, return index + 1.
5. Let length be the number of code units in S.
6. If index + 1 ≥ length, return index + 1.
7. Let first be the numeric value of the code unit at index index within S.
8. If first < 0xD800 or first > 0xDBFF, return index + 1.
9. Let second be the numeric value of the code unit at index index + 1 within S.
10. If second < 0xDC00 or second > 0xDFFF, return index + 1.
11. Return index + 2.

#### 21.2.5.3 获取 RegExp.prototype.dotAll <div id="sec-get-regexp.prototype.dotAll"></div>

RegExp.prototype.dotAll is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. If R does not have an [[OriginalFlags]] internal slot, then
1. If SameValue(R, %RegExpPrototype%) is true, return undefined.
2. Otherwise, throw a TypeError exception.
4. Let flags be R.[[OriginalFlags]].
5. If flags contains the code unit 0x0073 (LATIN SMALL LETTER S), return true.
6. Return false

#### 21.2.5.4 获取 RegExp.prototype.flags <div id="sec-get-regexp.prototype.flags"></div>

RegExp.prototype.flags is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. Let result be the empty String.
4. Let global be ToBoolean(? Get(R, "global")).
5. If global is true, append the code unit 0x0067 (LATIN SMALL LETTER G) as the last code unit of result.
6. Let ignoreCase be ToBoolean(? Get(R, "ignoreCase")).
7. If ignoreCase is true, append the code unit 0x0069 (LATIN SMALL LETTER I) as the last code unit of result.
8. Let multiline be ToBoolean(? Get(R, "multiline")).
9. If multiline is true, append the code unit 0x006D (LATIN SMALL LETTER M) as the last code unit of result.
10. Let dotAll be ToBoolean(? Get(R, "dotAll")).
11. If dotAll is true, append the code unit 0x0073 (LATIN SMALL LETTER S) as the last code unit of result.
12. Let unicode be ToBoolean(? Get(R, "unicode")).
13. If unicode is true, append the code unit 0x0075 (LATIN SMALL LETTER U) as the last code unit of result.
14. Let sticky be ToBoolean(? Get(R, "sticky")).
15. If sticky is true, append the code unit 0x0079 (LATIN SMALL LETTER Y) as the last code unit of result.
16. Return result.

#### 21.2.5.5 获取 RegExp.prototype.global <div id="sec-get-regexp.prototype.global"></div>

RegExp.prototype.global is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. If R does not have an [[OriginalFlags]] internal slot, then
1. If SameValue(R, %RegExpPrototype%) is true, return undefined.
2. Otherwise, throw a TypeError exception.
4. Let flags be R.[[OriginalFlags]].
5. If flags contains the code unit 0x0067 (LATIN SMALL LETTER G), return true.
6. Return false.

#### 21.2.5.6 获取 RegExp.prototype.ignoreCase <div id="sec-get-regexp.prototype.ignorecase"></div>

RegExp.prototype.ignoreCase is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. If R does not have an [[OriginalFlags]] internal slot, then
1. If SameValue(R, %RegExpPrototype%) is true, return undefined.
2. Otherwise, throw a TypeError exception.
4. Let flags be R.[[OriginalFlags]].
5. If flags contains the code unit 0x0069 (LATIN SMALL LETTER I), return true.
6. Return false.

#### 21.2.5.7 RegExp.prototype [ @@match ] ( string ) <div id="sec-regexp.prototype-@@match"></div>

When the @@match method is called with argument string, the following steps are taken:

1. Let rx be the this value.
2. If Type(rx) is not Object, throw a TypeError exception.
3. Let S be ? ToString(string).
4. Let global be ToBoolean(? Get(rx, "global")).
5. If global is false, then
   1. Return ? RegExpExec(rx, S).
6. Else global is true,
   1. Let fullUnicode be ToBoolean(? Get(rx, "unicode")).
   2. Perform ? Set(rx, "lastIndex", 0, true).
   3. Let A be ! ArrayCreate(0).
   4. Let n be 0.
   5. Repeat,
      1. Let result be ? RegExpExec(rx, S).
      2. If result is null, then
         1. If n = 0, return null.
         2. Return A.
      3. Else result is not null,
         1. Let matchStr be ? ToString(? Get(result, "0")).
         2. Let status be CreateDataProperty(A, ! ToString(n), matchStr).
         3. Assert: status is true.
         4. If matchStr is the empty String, then
            1. Let thisIndex be ? ToLength(? Get(rx, "lastIndex")).
            2. Let nextIndex be AdvanceStringIndex(S, thisIndex, fullUnicode).
            3. Perform ? Set(rx, "lastIndex", nextIndex, true).
         5. Increment n.

The value of the name property of this function is "[Symbol.match]".

> NOTE The @@match property is used by the IsRegExp abstract operation to identify objects that have the basic behaviour of regular expressions. The absence of a @@match property or the existence of such a property whose value does not Boolean coerce to true indicates that the object is not intended to be used as a regular expression object.

#### 21.2.5.8 获取 RegExp.prototype.multiline <div id="sec-get-regexp.prototype.multiline"></div>

RegExp.prototype.multiline is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. If R does not have an [[OriginalFlags]] internal slot, then
1. If SameValue(R, %RegExpPrototype%) is true, return undefined.
2. Otherwise, throw a TypeError exception.
4. Let flags be R.[[OriginalFlags]].
5. If flags contains the code unit 0x006D (LATIN SMALL LETTER M), return true.
6. Return false.

#### 21.2.5.9 RegExp.prototype [ @@replace ] ( string, replaceValue ) <div id="sec-regexp.prototype-@@replace"></div>

When the @@replace method is called with arguments string and replaceValue, the following steps are taken:

1. Let rx be the this value.
2. If Type(rx) is not Object, throw a TypeError exception.
3. Let S be ? ToString(string).
4. Let lengthS be the number of code unit elements in S.
5. Let functionalReplace be IsCallable(replaceValue).
6. If functionalReplace is false, then
   1. Set replaceValue to ? ToString(replaceValue).
7. Let global be ToBoolean(? Get(rx, "global")).
8. If global is true, then
   1. Let fullUnicode be ToBoolean(? Get(rx, "unicode")).
   2. Perform ? Set(rx, "lastIndex", 0, true).
9. Let results be a new empty List.
10. Let done be false.
11. Repeat, while done is false
    1. Let result be ? RegExpExec(rx, S).
    2. If result is null, set done to true.
    3. Else result is not null,
       1. Append result to the end of results.
       2. If global is false, set done to true.
       3. Else,
          1. Let matchStr be ? ToString(? Get(result, "0")).
          2. If matchStr is the empty String, then
             1. Let thisIndex be ? ToLength(? Get(rx, "lastIndex")).
             2. Let nextIndex be AdvanceStringIndex(S, thisIndex, fullUnicode).
             3. Perform ? Set(rx, "lastIndex", nextIndex, true).
12. Let accumulatedResult be the empty String value.
13. Let nextSourcePosition be 0.
14. For each result in results, do
    1. Let nCaptures be ? ToLength(? Get(result, "length")).
    2. Set nCaptures to max(nCaptures - 1, 0).
    3. Let matched be ? ToString(? Get(result, "0")).
    4. Let matchLength be the number of code units in matched.
    5. Let position be ? ToInteger(? Get(result, "index")).
    6. Set position to max(min(position, lengthS), 0).
    7. Let n be 1.
    8. Let captures be a new empty List.
    9. Repeat, while n ≤ nCaptures
       1. Let capN be ? Get(result, ! ToString(n)).
       2. If capN is not undefined, then
          1. Set capN to ? ToString(capN).
       3. Append capN as the last element of captures.
       4. Increase n by 1.
    10. Let namedCaptures be ? Get(result, "groups").
    11. If functionalReplace is true, then
        1. Let replacerArgs be « matched ».
        2. Append in list order the elements of captures to the end of the List replacerArgs.
        3. Append position and S to replacerArgs.
        4. If namedCaptures is not undefined, then
           1. Append namedCaptures as the last element of replacerArgs.
        5. Let replValue be ? Call(replaceValue, undefined, replacerArgs).
        6. Let replacement be ? ToString(replValue).
    12. Else,
        1. Let replacement be GetSubstitution(matched, S, position, captures, namedCaptures, replaceValue).
    13. If position ≥ nextSourcePosition, then
        1. NOTE: position should not normally move backwards. If it does, it is an indication of an ill-behaving RegExp subclass or use of an access triggered side-effect to change the global flag or other characteristics of rx. In such cases, the corresponding substitution is ignored.
        2. Set accumulatedResult to the string-concatenation of the current value of accumulatedResult, the substring of S consisting of the code units from nextSourcePosition (inclusive) up to position (exclusive), and replacement.
        3. Set nextSourcePosition to position + matchLength.
15. If nextSourcePosition ≥ lengthS, return accumulatedResult.
16. Return the string-concatenation of accumulatedResult and the substring of S consisting of the code units from nextSourcePosition (inclusive) up through the final code unit of S (inclusive).

The value of the name property of this function is "[Symbol.replace]".

#### 21.2.5.10 RegExp.prototype [ @@search ] ( string ) <div id="sec-regexp.prototype-@@search"></div>

When the @@search method is called with argument string, the following steps are taken:

1. Let rx be the this value.
2. If Type(rx) is not Object, throw a TypeError exception.
3. Let S be ? ToString(string).
4. Let previousLastIndex be ? Get(rx, "lastIndex").
5. If SameValue(previousLastIndex, 0) is false, then
1. Perform ? Set(rx, "lastIndex", 0, true).
6. Let result be ? RegExpExec(rx, S).
7. Let currentLastIndex be ? Get(rx, "lastIndex").
8. If SameValue(currentLastIndex, previousLastIndex) is false, then
  1. Perform ? Set(rx, "lastIndex", previousLastIndex, true).
9. If result is null, return -1.
10. Return ? Get(result, "index").

The value of the name property of this function is "[Symbol.search]".

> NOTE The lastIndex and global properties of this RegExp object are ignored when performing the search. The lastIndex property is left unchanged.

#### 21.2.5.11 获取 RegExp.prototype.source <div id="sec-get-regexp.prototype.source"></div>

RegExp.prototype.source is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. If R does not have an [[OriginalSource]] internal slot, then
   1. If SameValue(R, %RegExpPrototype%) is true, return "(?:)".
   2. Otherwise, throw a TypeError exception.
4. Assert: R has an [[OriginalFlags]] internal slot.
5. Let src be R.[[OriginalSource]].
6. Let flags be R.[[OriginalFlags]].
7. Return EscapeRegExpPattern(src, flags).

#### 21.2.5.12 RegExp.prototype [ @@split ] ( string, limit ) <div id="sec-regexp.prototype-@@split"></div>

> NOTE 1
>
> Returns an Array object into which substrings of the result of converting string to a String have been stored. The substrings are determined by searching from left to right for matches of the this value regular expression; these occurrences are not part of any substring in the returned array, but serve to divide up the String value.
>
> The this value may be an empty regular expression or a regular expression that can match an empty String. In this case, the regular expression does not match the empty substring at the beginning or end of the input String, nor does it match the empty substring at the end of the previous separator match. (For example, if the regular expression matches the empty String, the String is split up into individual code unit elements; the length of the result array equals the length of the String, and each substring contains one code unit.) Only the first match at a given index of the String is considered, even if backtracking could yield a non-empty-substring match at that index. (For example, /a\*?/\[Symbol.split]("ab") evaluates to the array ["a", "b"], while /a\*/\[Symbol.split]("ab") evaluates to the array ["","b"].)
>
> If the string is (or converts to) the empty String, the result depends on whether the regular expression can match the empty String. If it can, the result array contains no elements. Otherwise, the result array contains one element, which is the empty String
>
> If the regular expression contains capturing parentheses, then each time separator is matched the results (including any undefined results) of the capturing parentheses are spliced into the output array. For example,
>
> /<(\/)?(\[^<>]+)>/\[Symbol.split]("A\<B>bold\</B>and\<CODE>coded\</CODE>")
>
> evaluates to the array
>
> ["A", undefined, "B", "bold", "/", "B", "and", undefined, "CODE", "coded", "/", "CODE"
>
> If limit is not undefined, then the output array is truncated so that it contains no more than limit elements.

When the @@split method is called, the following steps are taken:

1. Let rx be the this value.
2. If Type(rx) is not Object, throw a TypeError exception.
3. Let S be ? ToString(string).
4. Let C be ? SpeciesConstructor(rx, %RegExp%).
5. Let flags be ? ToString(? Get(rx, "flags")).
6. If flags contains "u", let unicodeMatching be true.
7. Else, let unicodeMatching be false.
8. If flags contains "y", let newFlags be flags.
9. Else, let newFlags be the string-concatenation of flags and "y".
10. Let splitter be ? Construct(C, « rx, newFlags »).
11. Let A be ! ArrayCreate(0).
12. Let lengthA be 0.
13. If limit is undefined, let lim be 232 - 1; else let lim be ? ToUint32(limit).
14. Let size be the length of S.
15. Let p be 0.
16. If lim = 0, return A.
17. If size = 0, then
    1. Let z be ? RegExpExec(splitter, S).
    2. If z is not null, return A.
    3. Perform ! CreateDataProperty(A, "0", S).
    4. Return A.
18. Let q be p.
19. Repeat, while q < size
    1. Perform ? Set(splitter, "lastIndex", q, true).
    2. Let z be ? RegExpExec(splitter, S).
    3. If z is null, set q to AdvanceStringIndex(S, q, unicodeMatching).
    4. Else z is not null,
    5. Let e be ? ToLength(? Get(splitter, "lastIndex")).
    6. Set e to min(e, size).
    7. If e = p, set q to AdvanceStringIndex(S, q, unicodeMatching).
    8. Else e ≠ p,
       1. Let T be the String value equal to the substring of S consisting of the code units at indices p (inclusive) through q (exclusive).
       2. Perform ! CreateDataProperty(A, ! ToString(lengthA), T).
       3. Increase lengthA by 1.
       4. If lengthA = lim, return A.
       5. Set p to e.
       6. Let numberOfCaptures be ? ToLength(? Get(z, "length")).
       7. Set numberOfCaptures to max(numberOfCaptures - 1, 0).
       8. Let i be 1.
       9. Repeat, while i ≤ numberOfCaptures,
          1. Let nextCapture be ? Get(z, ! ToString(i)).
          2. Perform ! CreateDataProperty(A, ! ToString(lengthA), nextCapture).
          3. Increase i by 1.
          4. Increase lengthA by 1.
          5. If lengthA = lim, return A.
       10. Set q to p.
20. Let T be the String value equal to the substring of S consisting of the code units at indices p (inclusive) through size (exclusive).
21. Perform ! CreateDataProperty(A, ! ToString(lengthA), T).
22. Return A.

The value of the name property of this function is "[Symbol.split]".

> NOTE 2 The @@split method ignores the value of the global and sticky properties of this RegExp object.

#### 21.2.5.13 获取 RegExp.prototype.sticky <div id="sec-get-regexp.prototype.sticky"></div>

RegExp.prototype.sticky is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. If R does not have an [[OriginalFlags]] internal slot, then
1. If SameValue(R, %RegExpPrototype%) is true, return undefined.
2. Otherwise, throw a TypeError exception.
4. Let flags be R.[[OriginalFlags]].
5. If flags contains the code unit 0x0079 (LATIN SMALL LETTER Y), return true.
6. Return false.

#### 21.2.5.14 RegExp.prototype.test ( S ) <div id="sec-regexp.prototype.test"></div>

The following steps are taken:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. Let string be ? ToString(S).
4. Let match be ? RegExpExec(R, string).
5. If match is not null, return true; else return false

#### 21.2.5.15 RegExp.prototype.toString ( ) <div id="sec-regexp.prototype.tostring"></div>

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. Let pattern be ? ToString(? Get(R, "source")).
4. Let flags be ? ToString(? Get(R, "flags")).
5. Let result be the string-concatenation of "/", pattern, "/", and flags.
6. Return result.

> NOTE The returned String has the form of a RegularExpressionLiteral that evaluates to another RegExp object with the same behaviour as this object.

#### 21.2.5.16 获取 RegExp.prototype.unicode <div id="sec-get-regexp.prototype.unicode"></div>

RegExp.prototype.unicode is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let R be the this value.
2. If Type(R) is not Object, throw a TypeError exception.
3. If R does not have an [[OriginalFlags]] internal slot, then
1. If SameValue(R, %RegExpPrototype%) is true, return undefined.
2. Otherwise, throw a TypeError exception.
4. Let flags be R.[[OriginalFlags]].
5. If flags contains the code unit 0x0075 (LATIN SMALL LETTER U), return true.
6. Return false

### 21.2.6 RegExp 实例属性 <div id="sec-properties-of-regexp-instances"></div>

RegExp instances are ordinary objects that inherit properties from the RegExp prototype object. RegExp instances have internal slots [[RegExpMatcher]], [[OriginalSource]], and [[OriginalFlags]]. The value of the [[RegExpMatcher]] internal slot is an implementation-dependent representation of the Pattern of the RegExp object.

> NOTE Prior to ECMAScript 2015, RegExp instances were specified as having the own data properties source, global, ignoreCase, and multiline. Those properties are now specified as accessor properties of RegExp.prototype.

RegExp instances also have the following property:

#### 21.2.6.1 lastIndex <div id="sec-lastindex"></div>

The value of the lastIndex property specifies the String index at which to start the next match. It is coerced to an integer when used (see 21.2.5.2.2). This property shall have the attributes { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.