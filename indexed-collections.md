# 22 索引集合
## 22.1 数组对象 <div id="sec-array-objects"></div>

Array objects are exotic objects that give special treatment to a certain class of property names. See [9.4.2](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array-exotic-objects) for a definition of this special treatment.

### 22.1.1 Array 构造器 <div id="sec-array-constructor"></div>

The Array constructor:

- is the intrinsic object %Array%.
- is the initial value of the `Array` property of the global object.
- creates and initializes a new Array exotic object when called as a constructor.
- also creates and initializes a new Array object when called as a function rather than as a constructor. Thus the function call `Array(…)` is equivalent to the object creation expression `new Array(…)` with the same arguments.
- is a single function whose behaviour is overloaded based upon the number and types of its arguments.
- is designed to be subclassable. It may be used as the value of an `extends` clause of a class definition. Subclass constructors that intend to inherit the exotic `Array` behaviour must include a `super` call to the `Array` constructor to initialize subclass instances that are Array exotic objects. However, most of the `Array.prototype` methods are generic methods that are not dependent upon their `this` value being an Array exotic object.
- has a `"length"` property whose value is 1.

#### 22.1.1.1 Array ( ) <div id="sec-array-constructor-array"></div>

This description applies if and only if the Array constructor is called with no arguments.

1. Let numberOfArgs be the number of arguments passed to this function call.
2. Assert: numberOfArgs = 0.
3. If NewTarget is undefined, let newTarget be the active function object, else let newTarget be NewTarget.
4. Let proto be ? GetPrototypeFromConstructor(newTarget, `"%ArrayPrototype%"`).
5. Return ! ArrayCreate(0, proto).

#### 22.1.1.2 Array ( len ) <div id="sec-array-len"></div>

This description applies if and only if the Array constructor is called with exactly one argument.

1. Let numberOfArgs be the number of arguments passed to this function call.
2. Assert: numberOfArgs = 1.
3. If NewTarget is undefined, let newTarget be the active function object, else let newTarget be NewTarget.
4. Let proto be ? GetPrototypeFromConstructor(newTarget, `"%ArrayPrototype%"`).
5. Let array be ! ArrayCreate(0, proto).
6. If Type (len) is not Number, then
   1. Let defineStatus be CreateDataProperty(array, `"0"`, len).
   2. Assert: defineStatus is true.
   3. Let intLen be 1.
7. Else,
   1. Let intLen be ToUint32(len).
   2. If intLen ≠ len, throw a RangeError exception.
8. Perform ! Set(array, `"length"`, intLen, true).
9. Return array.

#### 22.1.1.3 Array ( ...items ) <div id="sec-array-items"></div>

This description applies if and only if the Array constructor is called with at least two arguments.

When the `Array` function is called, the following steps are taken:

1. Let numberOfArgs be the number of arguments passed to this function call.
2. Assert: numberOfArgs ≥ 2.
3. If NewTarget is undefined, let newTarget be the active function object, else let newTarget be NewTarget.
4. Let proto be ? GetPrototypeFromConstructor(newTarget, `"%ArrayPrototype%"`).
5. Let array be ? ArrayCreate(numberOfArgs, proto).
6. Let k be 0.
7. Let items be a zero-origined List containing the argument items in order.
8. Repeat, while k < numberOfArgs
   1. Let Pk be ! ToString(k).
   2. Let itemK be items[k].
   3. Let defineStatus be CreateDataProperty(array, Pk, itemK).
   4. Assert: defineStatus is true.
   5. Increase k by 1.
9. Assert: The value of array's `"length"` property is numberOfArgs.
10. Return array.

### 22.1.2 Array 构造器属性 <div id="sec-properties-of-the-array-constructor"></div>
The Array constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 22.1.2.1 Array.from ( items [ , mapfn [ , thisArg ] ] ) <div id="sec-array.from"></div>

When the `from` method is called with argument items and optional arguments mapfn and thisArg, the following steps are taken:

1. Let C be the this value.
2. If mapfn is undefined, let mapping be false.
3. Else,
   1. If IsCallable(mapfn) is false, throw a TypeError exception.
   2. If thisArg is present, let T be thisArg; else let T be undefined.
   3. Let mapping be true.
4. Let usingIterator be ? GetMethod(items, @@iterator).
5. If usingIterator is not undefined, then
   1. If IsConstructor(C) is true, then
      1. Let A be ? Construct(C).
   2. Else,
      1. Let A be ! ArrayCreate(0).
   3. Let iteratorRecord be ? GetIterator(items, sync, usingIterator).
   4. Let k be 0.
   5. Repeat,
      1. If k ≥ 253 - 1, then
         1. Let error be ThrowCompletion(a newly created TypeError object).
         2. Return ? IteratorClose(iteratorRecord, error).
      2. Let Pk be ! ToString(k).
      3. Let next be ? IteratorStep(iteratorRecord).
      4. If next is false, then
         1. Perform ? Set(A, "length", k, true).
         2. Return A.
      5. Let nextValue be ? IteratorValue(next).
      6. If mapping is true, then
         1. Let mappedValue be Call(mapfn, T, « nextValue, k »).
         2. If mappedValue is an abrupt completion, return ? IteratorClose(iteratorRecord, mappedValue).
         3. Set mappedValue to mappedValue.[[Value]].
      7. Else, let mappedValue be nextValue.
      8. Let defineStatus be CreateDataPropertyOrThrow(A, Pk, mappedValue).
      9. If defineStatus is an abrupt completion, return ? IteratorClose(iteratorRecord, defineStatus).
      10. Increase k by 1.
6. NOTE: items is not an Iterable so assume it is an array-like object.
7. Let arrayLike be ! ToObject(items).
8. Let len be ? ToLength(? Get(arrayLike, "length")).
9. If IsConstructor(C) is true, then
   1. Let A be ? Construct(C, « len »).
10. Else,
    1. Let A be ? ArrayCreate(len).
11. Let k be 0.
12. Repeat, while k < len
    1. Let Pk be ! ToString(k).
    2. Let kValue be ? Get(arrayLike, Pk).
    3. If mapping is true, then
       1. Let mappedValue be ? Call(mapfn, T, « kValue, k »).
    4. Else, let mappedValue be kValue.
    5. Perform ? CreateDataPropertyOrThrow(A, Pk, mappedValue).
    6. Increase k by 1.
13. Perform ? Set(A, "length", len, true).
14. Return A.

#### 22.1.2.2 Array.isArray ( arg ) <div id="sec-array.isarray"></div>

The `isArray` function takes one argument arg, and performs the following steps:

1. Return ? IsArray(arg).

#### 22.1.2.3 Array.of ( ...items ) <div id="sec-array.of"></div>

When the `of` method is called with any number of arguments, the following steps are taken:

1. Let len be the actual number of arguments passed to this function.
2. Let items be the List of arguments passed to this function.
3. Let C be the this value.
4. If IsConstructor(C) is true, then
   1. Let A be ? Construct(C, « len »).
5. Else,
   1. Let A be ? ArrayCreate(len).
6. Let k be 0.
7. Repeat, while k < len
   1. Let kValue be items[k].
   2. Let Pk be ! ToString(k).
   3. Perform ? CreateDataPropertyOrThrow(A, Pk, kValue).
   4. Increase k by 1.
8. Perform ? Set(A, "length", len, true).
9. Return A.

> NOTE 1
>
> The items argument is assumed to be a well-formed rest argument value.

> NOTE 2
>
> The `of` function is an intentionally generic factory method; it does not require that its this value be the Array constructor. Therefore it can be transferred to or inherited by other constructors that may be called with a single numeric argument.

#### 22.1.2.4 Array.prototype <div id="sec-array.prototype"></div>

The value of `Array.prototype` is [%ArrayPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-array-prototype-object), the intrinsic Array prototype object.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 22.1.2.5 获取 Array [ @@species ] <div id="sec-get-array-@@species"></div>

`Array[@@species]` is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Return the this value.

The value of the `name` property of this function is `"get [Symbol.species]"`.

>NOTE
>
>Array prototype methods normally use their `this` object's constructor to create a derived object. However, a subclass constructor may over-ride that default behaviour by redefining its @@species property.

### 22.1.3 Array 原型对象属性 <div id="sec-properties-of-the-array-prototype-object"></div>

The Array prototype object:

- is the intrinsic object %ArrayPrototype%.
- is an Array exotic object and has the internal methods specified for such objects.
- has a "length" property whose initial value is 0 and whose attributes are { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

> NOTE
>
> The Array prototype object is specified to be an Array exotic object to ensure compatibility with ECMAScript code that was created prior to the ECMAScript 2015 specification.

#### 22.1.3.1 Array.prototype.concat ( ...arguments ) <div id="sec-array.prototype.concat"></div>

When the `concat` method is called with zero or more arguments, it returns an array containing the array elements of the object followed by the array elements of each argument in order.

The following steps are taken:

1. Let O be ? ToObject(this value).
2. Let A be ? ArraySpeciesCreate(O, 0).
3. Let n be 0.
4. Let items be a List whose first element is O and whose subsequent elements are, in left to right order, the arguments that were passed to this function invocation.
5. Repeat, while items is not empty
   1. Remove the first element from items and let E be the value of the element.
   2. Let spreadable be ? IsConcatSpreadable(E).
   3. If spreadable is true, then
      1. Let k be 0.
      2. Let len be ? ToLength(? Get(E, "length")).
      3. If n + len > 253 - 1, throw a TypeError exception.
      4. Repeat, while k < len
         1. Let P be ! ToString(k).
         2. Let exists be ? HasProperty(E, P).
         3. If exists is true, then
            1. Let subElement be ? Get(E, P).
            2. Perform ? CreateDataPropertyOrThrow(A, ! ToString(n), subElement).
         4. Increase n by 1.
         5. Increase k by 1.
   4. Else E is added as a single item rather than spread,
      1. If n ≥ 253 - 1, throw a TypeError exception.
      2. Perform ? CreateDataPropertyOrThrow(A, ! ToString(n), E).
      3. Increase n by 1.
6. Perform ? Set(A, "length", n, true).
7. Return A.

The `"length"` property of the `concat` method is 1.

> NOTE 1
>
> The explicit setting of the `"length"` property in step 6 is necessary to ensure that its value is correct in situations where the trailing elements of the result Array are not present.

> NOTE 2
>
> The `concat` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

##### 22.1.3.1.1 RS: IsConcatSpreadable ( O )<div id="sec-isconcatspreadable"></div>

The abstract operation IsConcatSpreadable with argument O performs the following steps:

1. If Type(O) is not Object, return false.
2. Let spreadable be ? Get(O, @@isConcatSpreadable).
3. If spreadable is not undefined, return ToBoolean(spreadable).
4. Return ? IsArray(O).

#### 22.1.3.2 Array.prototype.constructor <div id="sec-array.prototype.constructor"></div>

The initial value of `Array.prototype.constructor` is the intrinsic object [%Array%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array-constructor).

#### 22.1.3.3 Array.prototype.copyWithin ( target, start [ , end ] ) <div id="sec-array.prototype.copywithin"></div>

The `copyWithin` method takes up to three arguments target, start and end.

> NOTE 1
>
> The end argument is optional with the length of the this object as its default value. If target is negative, it is treated as length + target where length is the length of the array. If start is negative, it is treated as length + start. If end is negative, it is treated as length + end.

The following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. Let relativeTarget be ? ToInteger(target).
4. If relativeTarget < 0, let to be max((len + relativeTarget), 0); else let to be min(relativeTarget, len).
5. Let relativeStart be ? ToInteger(start).
6. If relativeStart < 0, let from be max((len + relativeStart), 0); else let from be min(relativeStart, len).
7. If end is undefined, let relativeEnd be len; else let relativeEnd be ? ToInteger(end).
8. If relativeEnd < 0, let final be max((len + relativeEnd), 0); else let final be min(relativeEnd, len).
9. Let count be min(final - from, len - to).
10. If from < to and to < from + count, then
    1. Let direction be -1.
    2. Set from to from + count - 1.
    3. Set to to to + count - 1.
11. Else,
    1. Let direction be 1.
12. Repeat, while count > 0
    1. Let fromKey be ! ToString(from).
    2. Let toKey be ! ToString(to).
    3. Let fromPresent be ? HasProperty(O, fromKey).
    4. If fromPresent is true, then
       1. Let fromVal be ? Get(O, fromKey).
       2. Perform ? Set(O, toKey, fromVal, true).
    5. Else fromPresent is false,
       1. Perform ? DeletePropertyOrThrow(O, toKey).
    6. Set from to from + direction.
    7. Set to to to + direction.
    8. Decrease count by 1.
13. Return O.

> NOTE 2
>
> The `copyWithin` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.4 Array.prototype.entries ( ) <div id="sec-array.prototype.entries"></div>

The following steps are taken:

1. Let O be ? ToObject(this value).
2. Return CreateArrayIterator(O, `"key+value"`).

This function is the %ArrayProto_entries% intrinsic object.

#### 22.1.3.5 Array.prototype.every ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.every"></div>

> NOTE 1
>
> callbackfn should be a function that accepts three arguments and returns a value that is coercible to the Boolean value true or false. `every` calls callbackfn once for each element present in the array, in ascending order, until it finds one where callbackfn returns false. If such an element is found, `every` immediately returns false. Otherwise, if callbackfn returned true for all elements, `every` will return true. callbackfn is called only for elements of the array which actually exist; it is not called for missing elements of the array.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of callbackfn. If it is not provided, undefined is used instead.
>
> callbackfn is called with three arguments: the value of the element, the index of the element, and the object being traversed.
>
> `every` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn.
>
> The range of elements processed by `every` is set before the first call to callbackfn. Elements which are appended to the array after the call to `every` begins will not be visited by callbackfn. If existing elements of the array are changed, their value as passed to callbackfn will be the value at the time `every` visits them; elements that are deleted after the call to `every` begins and before being visited are not visited. `every` acts like the "for all" quantifier in mathematics. In particular, for an empty array, it returns true.

When the `every` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If IsCallable(callbackfn) is false, throw a TypeError exception.
4. If thisArg is present, let T be thisArg; else let T be undefined.
5. Let k be 0.
6. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kPresent be ? HasProperty(O, Pk).
   3. If kPresent is true, then
      1. Let kValue be ? Get(O, Pk).
      2. Let testResult be ToBoolean(? Call(callbackfn, T, « kValue, k, O »)).
      3. If testResult is false, return false.
   4. Increase k by 1.
7. Return true.

> NOTE 2
>
> The `every` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.6 Array.prototype.fill ( value [ , start [ , end ] ] ) <div id="sec-array.prototype.fill"></div>

The `fill` method takes up to three arguments value, start and end.

> NOTE 1
>
> The start and end arguments are optional with default values of 0 and the length of the this object. If start is negative, it is treated as length + start where length is the length of the array. If end is negative, it is treated as length + end.

The following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. Let relativeStart be ? ToInteger(start).
4. If relativeStart < 0, let k be max((len + relativeStart), 0); else let k be min(relativeStart, len).
5. If end is undefined, let relativeEnd be len; else let relativeEnd be ? ToInteger(end).
6. If relativeEnd < 0, let final be max((len + relativeEnd), 0); else let final be min(relativeEnd, len).
7. Repeat, while k < final
   1. Let Pk be ! ToString(k).
   2. Perform ? Set(O, Pk, value, true).
   3. Increase k by 1.
8. Return O.

> NOTE 2
>
> The `fill` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.7 Array.prototype.filter ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.filter"></div>

> NOTE 1
>
> 
> callbackfn should be a function that accepts three arguments and returns a value that is coercible to the Boolean value true or false. `filter` calls callbackfn once for each element in the array, in ascending order, and constructs a new array of all the values for which callbackfn returns true. callbackfn is called only for elements of the array which actually exist; it is not called for missing elements of the array.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of callbackfn. If it is not provided, undefined is used instead.
>
> callbackfn is called with three arguments: the value of the element, the index of the element, and the object being traversed.
>
> `filter` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn.
>
> The range of elements processed by `filter` is set before the first call to callbackfn. Elements which are appended to the array after the call to `filter` begins will not be visited by callbackfn. If existing elements of the array are changed their value as passed to callbackfn will be the value at the time `filter` visits them; elements that are deleted after the call to `filter` begins and before being visited are not visited.

When the `filter` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If IsCallable(callbackfn) is false, throw a TypeError exception.
4. If thisArg is present, let T be thisArg; else let T be undefined.
5. Let A be ? ArraySpeciesCreate(O, 0).
6. Let k be 0.
7. Let to be 0.
8. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kPresent be ? HasProperty(O, Pk).
   3. If kPresent is true, then
      1. Let kValue be ? Get(O, Pk).
      2. Let selected be ToBoolean(? Call(callbackfn, T, « kValue, k, O »)).
      3. If selected is true, then
      4. Perform ? CreateDataPropertyOrThrow(A, ! ToString(to), kValue).
      5. Increase to by 1.
   4. Increase k by 1.
9. Return A.

#### 22.1.3.8 Array.prototype.find ( predicate [ , thisArg ] ) <div id="sec-array.prototype.find"></div>

The `find` method is called with one or two arguments, predicate and thisArg.

> NOTE 1
>
> 
> predicate should be a function that accepts three arguments and returns a value that is coercible to a Boolean value. `find` calls predicate once for each element of the array, in ascending order, until it finds one where predicate returns true. If such an element is found, `find` immediately returns that element value. Otherwise, `find` returns undefined.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of predicate. If it is not provided, undefined is used instead.
>
> predicate is called with three arguments: the value of the element, the index of the element, and the object being traversed.
>
> `find` does not directly mutate the object on which it is called but the object may be mutated by the calls to predicate.
>
> The range of elements processed by `find` is set before the first call to predicate. Elements that are appended to the array after the call to `find` begins will not be visited by predicate. If existing elements of the array are changed, their value as passed to predicate will be the value at the time that `find` visits them.

When the `find` method is called, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If IsCallable(predicate) is false, throw a TypeError exception.
4. If thisArg is present, let T be thisArg; else let T be undefined.
5. Let k be 0.
6. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kValue be ? Get(O, Pk).
   3. Let testResult be ToBoolean(? Call(predicate, T, « kValue, k, O »)).
   4. If testResult is true, return kValue.
   5. Increase k by 1.
7. Return undefined.

> NOTE 2
>
> The `find` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.9 Array.prototype.findIndex ( predicate [ , thisArg ] ) <div id="sec-array.prototype.findindex"></div>

> NOTE 1
>
> 
> predicate should be a function that accepts three arguments and returns a value that is coercible to the Boolean value true or false. `findIndex` calls predicate once for each element of the array, in ascending order, until it finds one where predicate returns true. If such an element is found, `findIndex` immediately returns the index of that element value. Otherwise, `findIndex` returns -1.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of predicate. If it is not provided, undefined is used instead.
>
> predicate is called with three arguments: the value of the element, the index of the element, and the object being traversed.
>
> `findIndex` does not directly mutate the object on which it is called but the object may be mutated by the calls to predicate.
>
> The range of elements processed by `findIndex` is set before the first call to predicate. Elements that are appended to the array after the call to `findIndex` begins will not be visited by predicate. If existing elements of the array are changed, their value as passed to predicate will be the value at the time that `findIndex` visits them.

When the `findIndex` method is called with one or two arguments, the following steps are taken:

#### 22.1.3.10 Array.prototype.flat( [ depth ] ) <div id="sec-array.prototype.flat"></div>

When the `flat` method is called with zero or one arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let sourceLen be ? ToLength(? Get(O, "length")).
3. Let depthNum be 1.
4. If depth is not undefined, then
   1. Set depthNum to ? ToInteger(depth).
5. Let A be ? ArraySpeciesCreate(O, 0).
6. Perform ? FlattenIntoArray(A, O, sourceLen, 0, depthNum).
7. Return A.

##### 22.1.3.10.1 FlattenIntoArray(target, source, sourceLen, start, depth [ , mapperFunction, thisArg ]) <div id="sec-flattenintoarray"></div>

1. Let targetIndex be start.
2. Let sourceIndex be 0.
3. Repeat, while sourceIndex < sourceLen
   1. Let P be ! ToString(sourceIndex).
   2. Let exists be ? HasProperty(source, P).
   3. If exists is true, then
      1. Let element be ? Get(source, P).
      2. If mapperFunction is present, then
         1. Assert: thisArg is present.
         2. Set element to ? Call(mapperFunction, thisArg , « element, sourceIndex, source »).
      3. Let shouldFlatten be false.
      4. If depth > 0, then
      5. Set shouldFlatten to ? IsArray(element).
      6. If shouldFlatten is true, then
      7. Let elementLen be ? ToLength(? Get(element, "length")).
      8. Set targetIndex to ? FlattenIntoArray(target, element, elementLen, targetIndex, depth - 1).
      9. Else,
      10. If targetIndex ≥ 253-1, throw a TypeError exception.
      11. Perform ? CreateDataPropertyOrThrow(target, ! ToString(targetIndex), element).
      12. Increase targetIndex by 1.
   4. Increase sourceIndex by 1.
4. Return targetIndex.

#### 22.1.3.11 Array.prototype.flatMap ( mapperFunction [ , thisArg ] ) <div id="sec-array.prototype.flatmap"></div>

When the `flatMap` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let sourceLen be ? ToLength(? Get(O, `"length"`)).
3. If IsCallable(mapperFunction) is false, throw a TypeError exception.
4. If thisArg is present, let T be thisArg; else let T be undefined.
5. Let A be ? ArraySpeciesCreate(O, 0).
6. Perform ? FlattenIntoArray(A, O, sourceLen, 0, 1, mapperFunction, T).
7. Return A.

#### 22.1.3.12 Array.prototype.forEach ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.foreach"></div>

> NOTE 1
>
> callbackfn should be a function that accepts three arguments. `forEach` calls callbackfn once for each element present in the array, in ascending order. callbackfn is called only for elements of the array which actually exist; it is not called for missing elements of the array.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of callbackfn. If it is not provided, undefined is used instead.
>
> callbackfn is called with three arguments: the value of the element, the index of the element, and the object being traversed.
>
> `forEach` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn.

When the forEach method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If IsCallable(callbackfn) is false, throw a TypeError exception.
4. If thisArg is present, let T be thisArg; else let T be undefined.
5. Let k be 0.
6. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kPresent be ? HasProperty(O, Pk).
   3. If kPresent is true, then
      1. Let kValue be ? Get(O, Pk).
      2. Perform ? Call(callbackfn, T, « kValue, k, O »).
   4. Increase k by 1.
7. Return undefined.

This function is the %ArrayProto_forEach% intrinsic object.

> NOTE 2
>
> The `forEach` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.13 Array.prototype.includes ( searchElement [ , fromIndex ] ) <div id="sec-array.prototype.includes"></div>

> NOTE 1
>
> `includes` compares searchElement to the elements of the array, in ascending order, using the SameValueZero algorithm, and if found at any position, returns true; otherwise, false is returned.
>
> The optional second argument fromIndex defaults to 0 (i.e. the whole array is searched). If it is greater than or equal to the length of the array, false is returned, i.e. the array will not be searched. If it is negative, it is used as the offset from the end of the array to compute fromIndex. If the computed index is less than 0, the whole array will be searched.

When the `includes` method is called, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If len is 0, return false.
4. Let n be ? ToInteger(fromIndex).
5. Assert: If fromIndex is undefined, then n is 0.
6. If n ≥ 0, then
   1. Let k be n.
7. Else n < 0,
   1. Let k be len + n.
   2. If k < 0, set k to 0.
8. Repeat, while k < len
   1. Let elementK be the result of ? Get(O, ! ToString(k)).
   2. If SameValueZero(searchElement, elementK) is true, return true.
   3. Increase k by 1.
9. Return false.

> NOTE2
>
> The `includes` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

> NOTE 3
>
> The `includes` method intentionally differs from the similar `indexOf` method in two ways. First, it uses the SameValueZero algorithm, instead of Strict Equality Comparison, allowing it to detect NaN array elements. Second, it does not skip missing array elements, instead treating them as undefined.

#### 22.1.3.14 Array.prototype.indexOf ( searchElement [ , fromIndex ] ) <div id="sec-array.prototype.indexof"></div>

> NOTE 1
>
> `indexOf` compares searchElement to the elements of the array, in ascending order, using the Strict Equality Comparison algorithm, and if found at one or more indices, returns the smallest such index; otherwise, -1 is returned.
>
> The optional second argument fromIndex defaults to 0 (i.e. the whole array is searched). If it is greater than or equal to the length of the array, -1 is returned, i.e. the array will not be searched. If it is negative, it is used as the offset from the end of the array to compute fromIndex. If the computed index is less than 0, the whole array will be searched.

When the `indexOf` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If len is 0, return -1.
4. Let n be ? ToInteger(fromIndex).
5. Assert: If fromIndex is undefined, then n is 0.
6. If n ≥ len, return -1.
7. If n ≥ 0, then
   1. If n is -0, let k be +0; else let k be n.
8. Else n < 0,
   1. Let k be len + n.
   2. If k < 0, set k to 0.
9. Repeat, while k < len
   1. Let kPresent be ? HasProperty(O, ! ToString(k)).
   2. If kPresent is true, then
      1. Let elementK be ? Get(O, ! ToString(k)).
      2. Let same be the result of performing Strict Equality Comparison searchElement === elementK.
      3. If same is true, return k.
   3. Increase k by 1.
10. Return -1.

> NOTE 2
>
> The `indexOf` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.15 Array.prototype.join ( separator ) <div id="sec-array.prototype.join"></div>

> NOTE 1
>
> The elements of the array are converted to Strings, and these Strings are then concatenated, separated by occurrences of the separator. If no separator is provided, a single comma is used as the separator.

The `join` method takes one argument, separator, and performs the following steps:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If separator is undefined, let sep be the single-element String ",".
4. Else, let sep be ? ToString(separator).
5. Let R be the empty String.
6. Let k be 0.
7. Repeat, while k < len
   1. If k > 0, set R to the string-concatenation of R and sep.
   2. Let element be ? Get(O, ! ToString(k)).
   3. If element is undefined or null, let next be the empty String; otherwise, let next be ? ToString(element).
   4. Set R to the string-concatenation of R and next.
   5. Increase k by 1.
8. Return R.

> NOTE 2
>
> The `join` function is intentionally generic; it does not require that its this value be an Array object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.16 Array.prototype.keys ( ) <div id="sec-array.prototype.keys"></div>

The following steps are taken:

1. Let O be ? ToObject(this value).
2. Return CreateArrayIterator(O, "key").

This function is the %ArrayProto_keys% intrinsic object.

#### 22.1.3.17 Array.prototype.lastIndexOf ( searchElement [ , fromIndex ] ) <div id="sec-array.prototype.lastindexof"></div>

> NOTE 1
>
> `lastIndexOf` compares searchElement to the elements of the array in descending order using the Strict Equality Comparison algorithm, and if found at one or more indices, returns the largest such index; otherwise, -1 is returned.
>
> The optional second argument fromIndex defaults to the array's length minus one (i.e. the whole array is searched). If it is greater than or equal to the length of the array, the whole array will be searched. If it is negative, it is used as the offset from the end of the array to compute fromIndex. If the computed index is less than 0, -1 is returned.

When the `lastIndexOf` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If len is 0, return -1.
4. If fromIndex is present, let n be ? ToInteger(fromIndex); else let n be len - 1.
5. If n ≥ 0, then
   1. If n is -0, let k be +0; else let k be min(n, len - 1).
6. Else n < 0,
   1. Let k be len + n.
7. Repeat, while k ≥ 0
   1. Let kPresent be ? HasProperty(O, ! ToString(k)).
   2. If kPresent is true, then
      1. Let elementK be ? Get(O, ! ToString(k)).
      2. Let same be the result of performing Strict Equality Comparison searchElement === elementK.
      3. If same is true, return k.
   3. Decrease k by 1.
8. Return -1.

> NOTE 2
>
> The `lastIndexOf` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.18 Array.prototype.map ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.map"></div>

> NOTE 1
>
> callbackfn should be a function that accepts three arguments. `map` calls callbackfn once for each element in the array, in ascending order, and constructs a new Array from the results. callbackfn is called only for elements of the array which actually exist; it is not called for missing elements of the array.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of callbackfn. If it is not provided, undefined is used instead.
>
> callbackfn is called with three arguments: the value of the element, the index of the element, and the object being traversed.
>
> `map` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn.
>
> The range of elements processed by `map` is set before the first call to callbackfn. Elements which are appended to the array after the call to `map` begins will not be visited by callbackfn. If existing elements of the array are changed, their value as passed to callbackfn will be the value at the time `map` visits them; elements that are deleted after the call to `map` begins and before being visited are not visited.

When the `map` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If IsCallable(callbackfn) is false, throw a TypeError exception.
4. If thisArg is present, let T be thisArg; else let T be undefined.
5. Let A be ? ArraySpeciesCreate(O, len).
6. Let k be 0.
7. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kPresent be ? HasProperty(O, Pk).
   3. If kPresent is true, then
      1. Let kValue be ? Get(O, Pk).
      2. Let mappedValue be ? Call(callbackfn, T, « kValue, k, O »).
      3. Perform ? CreateDataPropertyOrThrow(A, Pk, mappedValue).
   4. Increase k by 1.
8. Return A.

> NOTE 2
>
> The `map` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.19 Array.prototype.pop ( ) <div id="sec-array.prototype.pop"></div>

> NOTE 1
>
> The last element of the array is removed from the array and returned.

When the `pop` method is called, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If len is zero, then
   1. Perform ? Set(O, "length", 0, true).
   2. Return undefined.
4. Else len > 0,
   1. Let newLen be len - 1.
   2. Let index be ! ToString(newLen).
   3. Let element be ? Get(O, index).
   4. Perform ? DeletePropertyOrThrow(O, index).
   5. Perform ? Set(O, "length", newLen, true).
   6. Return element.

> NOTE 2
>
> The `pop` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.20 Array.prototype.push ( ...items ) <div id="sec-array.prototype.push"></div>

> NOTE 1
>
> The arguments are appended to the end of the array, in the order in which they appear. The new length of the array is returned as the result of the call.

When the `push` method is called with zero or more arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. Let items be a List whose elements are, in left to right order, the arguments that were passed to this function invocation.
4. Let argCount be the number of elements in items.
5. If len + argCount > 253 - 1, throw a TypeError exception.
6. Repeat, while items is not empty
   1. Remove the first element from items and let E be the value of the element.
   2. Perform ? Set(O, ! ToString(len), E, true).
   3. Increase len by 1.
7. Perform ? Set(O, "length", len, true).
8. Return len.

The `"length"` property of the `push` method is 1.

> NOTE 2
>
> The `push` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.21 Array.prototype.reduce ( callbackfn [ , initialValue ] ) <div id="sec-array.prototype.reduce"></div>

> NOTE 1
>
> 
> callbackfn should be a function that takes four arguments. `reduce` calls the callback, as a function, once for each element after the first element present in the array, in ascending order.
>
> callbackfn is called with four arguments: the previousValue (value from the previous call to callbackfn), the currentValue (value of the current element), the currentIndex, and the object being traversed. The first time that callback is called, the previousValue and currentValue can be one of two values. If an initialValue was supplied in the call to `reduce`, then previousValue will be equal to initialValue and currentValue will be equal to the first value in the array. If no initialValue was supplied, then previousValue will be equal to the first value in the array and currentValue will be equal to the second. It is a TypeError if the array contains no elements and initialValue is not provided.
>
> `reduce` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn.
>
> The range of elements processed by `reduce` is set before the first call to callbackfn. Elements that are appended to the array after the call to `reduce` begins will not be visited by callbackfn. If existing elements of the array are changed, their value as passed to callbackfn will be the value at the time `reduce` visits them; elements that are deleted after the call to `reduce` begins and before being visited are not visited.

When the `reduce` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If IsCallable(callbackfn) is false, throw a TypeError exception.
4. If len is 0 and initialValue is not present, throw a TypeError exception.
5. Let k be 0.
6. Let accumulator be undefined.
7. If initialValue is present, then
   1. Set accumulator to initialValue.
8. Else initialValue is not present,
   1. Let kPresent be false.
   2. Repeat, while kPresent is false and k < len
      1. Let Pk be ! ToString(k).
      2. Set kPresent to ? HasProperty(O, Pk).
      3. If kPresent is true, then
         1. Set accumulator to ? Get(O, Pk).
      4. Increase k by 1.
   3. If kPresent is false, throw a TypeError exception.
9. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kPresent be ? HasProperty(O, Pk).
   3. If kPresent is true, then
      1. Let kValue be ? Get(O, Pk).
      2. Set accumulator to ? Call(callbackfn, undefined, « accumulator, kValue, k, O »).
   4. Increase k by 1.
10. Return accumulator.

> NOTE 2
>
> The `reduce` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.22 Array.prototype.reduceRight ( callbackfn [ , initialValue ] ) <div id="sec-array.prototype.reduceright"></div>

> NOTE 1
>
> callbackfn should be a function that takes four arguments. `reduceRight` calls the callback, as a function, once for each element after the first element present in the array, in descending order.
>
> callbackfn is called with four arguments: the previousValue (value from the previous call to callbackfn), the currentValue (value of the current element), the currentIndex, and the object being traversed. The first time the function is called, the previousValue and currentValue can be one of two values. If an initialValue was supplied in the call to `reduceRight`, then previousValue will be equal to initialValue and currentValue will be equal to the last value in the array. If no initialValue was supplied, then previousValue will be equal to the last value in the array and currentValue will be equal to the second-to-last value. It is a TypeError if the array contains no elements and initialValue is not provided.
>
> `reduceRight` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn.
>
> The range of elements processed by `reduceRight` is set before the first call to callbackfn. Elements that are appended to the array after the call to `reduceRight` begins will not be visited by callbackfn. If existing elements of the array are changed by callbackfn, their value as passed to callbackfn will be the value at the time `reduceRight` visits them; elements that are deleted after the call to `reduceRight` begins and before being visited are not visited.

When the `reduceRight` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If IsCallable(callbackfn) is false, throw a TypeError exception.
4. If len is 0 and initialValue is not present, throw a TypeError exception.
5. Let k be len - 1.
6. Let accumulator be undefined.
7. If initialValue is present, then
   1. Set accumulator to initialValue.
8. Else initialValue is not present,
   1. Let kPresent be false.
   2. Repeat, while kPresent is false and k ≥ 0
      1. Let Pk be ! ToString(k).
      2. Set kPresent to ? HasProperty(O, Pk).
      3. If kPresent is true, then
         1. Set accumulator to ? Get(O, Pk).
      4. Decrease k by 1.
   3. If kPresent is false, throw a TypeError exception.
9. Repeat, while k ≥ 0
   1. Let Pk be ! ToString(k).
   2. Let kPresent be ? HasProperty(O, Pk).
   3. If kPresent is true, then
      1. Let kValue be ? Get(O, Pk).
      2. Set accumulator to ? Call(callbackfn, undefined, « accumulator, kValue, k, O »).
   4. Decrease k by 1.
10. Return accumulator.

> NOTE 2
>
> The `reduceRight` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.23 Array.prototype.reverse ( ) <div id="sec-array.prototype.reverse"></div>

> NOTE 1
>
> The elements of the array are rearranged so as to reverse their order. The object is returned as the result of the call.

When the `reverse` method is called, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. Let middle be floor(len / 2).
4. Let lower be 0.
5. Repeat, while lower ≠ middle
   1. Let upper be len - lower - 1.
   2. Let upperP be ! ToString(upper).
   3. Let lowerP be ! ToString(lower).
   4. Let lowerExists be ? HasProperty(O, lowerP).
   5. If lowerExists is true, then
      1. Let lowerValue be ? Get(O, lowerP).
   6. Let upperExists be ? HasProperty(O, upperP).
   7. If upperExists is true, then
      1. Let upperValue be ? Get(O, upperP).
   8. If lowerExists is true and upperExists is true, then
      1. Perform ? Set(O, lowerP, upperValue, true).
      2. Perform ? Set(O, upperP, lowerValue, true).
   9. Else if lowerExists is false and upperExists is true, then
      1. Perform ? Set(O, lowerP, upperValue, true).
      2. Perform ? DeletePropertyOrThrow(O, upperP).
   10. Else if lowerExists is true and upperExists is false, then
       1. Perform ? DeletePropertyOrThrow(O, lowerP).
       2. Perform ? Set(O, upperP, lowerValue, true).
   11. Else both lowerExists and upperExists are false,
       1. No action is required.
   12. Increase lower by 1.
6. Return O.

> NOTE 2
>
> The `reverse` function is intentionally generic; it does not require that its this value be an Array object. Therefore, it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.24 Array.prototype.shift ( ) <div id="sec-array.prototype.shift"></div>

> NOTE 1
>
> The first element of the array is removed from the array and returned.

When the `shift` method is called, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If len is zero, then
   1. Perform ? Set(O, "length", 0, true).
   2. Return undefined.
4. Let first be ? Get(O, "0").
5. Let k be 1.
6. Repeat, while k < len
   1. Let from be ! ToString(k).
   2. Let to be ! ToString(k - 1).
   3. Let fromPresent be ? HasProperty(O, from).
   4. If fromPresent is true, then
      1. Let fromVal be ? Get(O, from).
      2. Perform ? Set(O, to, fromVal, true).
   5. Else fromPresent is false,
      1. Perform ? DeletePropertyOrThrow(O, to).
   6. Increase k by 1.
7. Perform ? DeletePropertyOrThrow(O, ! ToString(len - 1)).
8. Perform ? Set(O, "length", len - 1, true).
9. Return first.

> NOTE 2
>
> The `shift` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.25 Array.prototype.slice ( start, end ) <div id="sec-array.prototype.slice"></div>

> NOTE 1
>
> The `slice` method takes two arguments, start and end, and returns an array containing the elements of the array from element start up to, but not including, element end (or through the end of the array if end is undefined). If start is negative, it is treated as length + start where length is the length of the array. If end is negative, it is treated as length + end where length is the length of the array.

The following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. Let relativeStart be ? ToInteger(start).
4. If relativeStart < 0, let k be max((len + relativeStart), 0); else let k be min(relativeStart, len).
5. If end is undefined, let relativeEnd be len; else let relativeEnd be ? ToInteger(end).
6. If relativeEnd < 0, let final be max((len + relativeEnd), 0); else let final be min(relativeEnd, len).
7. Let count be max(final - k, 0).
8. Let A be ? ArraySpeciesCreate(O, count).
9. Let n be 0.
10. Repeat, while k < final
    1. Let Pk be ! ToString(k).
    2. Let kPresent be ? HasProperty(O, Pk).
    3. If kPresent is true, then
       1. Let kValue be ? Get(O, Pk).
       2. Perform ? CreateDataPropertyOrThrow(A, ! ToString(n), kValue).
    4. Increase k by 1.
    5. Increase n by 1.
11. Perform ? Set(A, "length", n, true).
12. Return A.

> NOTE 2
>
> The explicit setting of the `"length"` property of the result Array in step 11 was necessary in previous editions of ECMAScript to ensure that its length was correct in situations where the trailing elements of the result Array were not present. Setting `"length"` became unnecessary starting in ES2015 when the result Array was initialized to its proper length rather than an empty Array but is carried forward to preserve backward compatibility.

> NOTE 3
>
> The `slice` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.26 Array.prototype.some ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.some"></div>

> NOTE 1
>
> callbackfn should be a function that accepts three arguments and returns a value that is coercible to the Boolean value true or false. `some` calls callbackfn once for each element present in the array, in ascending order, until it finds one where callbackfn returns true. If such an element is found, `some` immediately returns true. Otherwise, `some` returns false. callbackfn is called only for elements of the array which actually exist; it is not called for missing elements of the array.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of callbackfn. If it is not provided, undefined is used instead.
>
> callbackfn is called with three arguments: the value of the element, the index of the element, and the object being traversed.
>
> `some` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn.
>
> The range of elements processed by `some` is set before the first call to callbackfn. Elements that are appended to the array after the call to `some` begins will not be visited by callbackfn. If existing elements of the array are changed, their value as passed to callbackfn will be the value at the time that `some` visits them; elements that are deleted after the call to `some` begins and before being visited are not visited. `some` acts like the "exists" quantifier in mathematics. In particular, for an empty array, it returns false.

When the `some` method is called with one or two arguments, the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. If IsCallable(callbackfn) is false, throw a TypeError exception.
4. If thisArg is present, let T be thisArg; else let T be undefined.
5. Let k be 0.
6. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kPresent be ? HasProperty(O, Pk).
   3. If kPresent is true, then
      1. Let kValue be ? Get(O, Pk).
      2. Let testResult be ToBoolean(? Call(callbackfn, T, « kValue, k, O »)).
      3. If testResult is true, return true.
   4. Increase k by 1.
7. Return false.

> NOTE 2
>
> The `some` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.27 Array.prototype.sort ( comparefn ) <div id="sec-array.prototype.sort"></div>

The elements of this array are sorted. The sort must be stable (that is, elements that compare equal must remain in their original order). If comparefn is not undefined, it should be a function that accepts two arguments x and y and returns a negative value if x < y, zero if x = y, or a positive value if x > y.

Upon entry, the following steps are performed to initialize evaluation of the `sort` function:

1. If comparefn is not undefined and IsCallable(comparefn) is false, throw a TypeError exception.
2. Let obj be ? ToObject(this value).
3. Let len be ? ToLength(? Get(obj, "length")).

Within this specification of the `sort` method, an object, obj, is said to be *sparse* if the following algorithm returns true:

1. For each integer i in the range 0 ≤ i < len, do
   1. Let elem be obj.\[\[GetOwnProperty]](! ToString(i)).
   2. If elem is undefined, return true.
2. Return false.

The *sort order* is the ordering, after completion of this function, of the [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) property values of obj whose integer indexes are less than len. The result of the `sort` function is then determined as follows:

If comparefn is not undefined and is not a consistent comparison function for the elements of this array (see below), the sort order is implementation-defined. The sort order is also implementation-defined if comparefn is undefined and SortCompare does not act as a consistent comparison function.

Let proto be obj.\[\[GetPrototypeOf]](). If proto is not null and there exists an integer j such that all of the conditions below are satisfied then the sort order is implementation-defined:

- obj is sparse
- 0 ≤ j < len
- HasProperty(proto, ToString(j)) is true

The sort order is also implementation-defined if obj is sparse and any of the following conditions are true:

- IsExtensible(obj) is false.
- Any integer index property of obj whose name is a nonnegative integer less than len is a data property whose [[Configurable]] attribute is false.

The sort order is also implementation-defined if any of the following conditions are true:

- If obj is an exotic object (including Proxy exotic objects) whose behaviour for [[Get]], [[Set]], [[Delete]], and [[GetOwnProperty]] is not the ordinary object implementation of these internal methods.
- If any index property of obj whose name is a nonnegative integer less than len is an accessor property or is a data property whose [[Writable]] attribute is false.
- If comparefn is undefined and the application of ToString to any value passed as an argument to SortCompare modifies obj or any object on obj's prototype chain.
- If comparefn is undefined and all applications of ToString, to any specific value passed as an argument to SortCompare, do not produce the same result.

The following steps are taken:

1. Perform an implementation-dependent sequence of calls to the [[Get]] and [[Set]] internal methods of obj, to the DeletePropertyOrThrow and HasOwnProperty abstract operation with obj as the first argument, and to SortCompare (described below), such that:
   - The property key argument for each call to [[Get]], [[Set]], HasOwnProperty, or DeletePropertyOrThrow is the string representation of a nonnegative integer less than len.
   - The arguments for calls to SortCompare are values returned by a previous call to the [[Get]] internal method, unless the properties accessed by those previous calls did not exist according to HasOwnProperty. If both prospective arguments to SortCompare correspond to non-existent properties, use +0 instead of calling SortCompare. If only the first prospective argument is non-existent use +1. If only the second prospective argument is non-existent use -1.
   - If obj is not sparse then DeletePropertyOrThrow must not be called.
   - If any [[Set]] call returns false a TypeError exception is thrown.
   - If an abrupt completion is returned from any of these operations, it is immediately returned as the value of this function.
2. Return obj.

Unless the sort order is specified above to be implementation-defined, the returned object must have the following two characteristics:

- There must be some mathematical permutation π of the nonnegative integers less than len, such that for every nonnegative integer j less than len, if property old[j] existed, then new[π(j)] is exactly the same value as old[j]. But if property old[j] did not exist, then new[π(j)] does not exist.
- Then for all nonnegative integers j and k, each less than len, if SortCompare(old[j], old[k]) < 0 (see SortCompare below), then new[π(j)] < new[π(k)].

Here the notation old[j] is used to refer to the hypothetical result of calling obj.[[Get]](j) before this function is executed, and the notation new[j] to refer to the hypothetical result of calling obj.[[Get]](j) after this function has been executed.

A function comparefn is a consistent comparison function for a set of values S if all of the requirements below are met for all values a, b, and c (possibly the same value) in the set S: The notation a <CF b means comparefn(a, b) < 0; a =CF b means comparefn(a, b) = 0 (of either sign); and a >CF b means comparefn(a, b) > 0.

- Calling comparefn(a, b) always returns the same value v when given a specific pair of values a and b as its two arguments. Furthermore, Type(v) is Number, and v is not NaN. Note that this implies that exactly one of a <CF b, a =CF b, and a >CF b will be true for a given pair of a and b.
- Calling comparefn(a, b) does not modify obj or any object on obj's prototype chain.
- a =CF a (reflexivity)
- If a =CF b, then b =CF a (symmetry)
- If a =CF b and b =CF c, then a =CF c (transitivity of =CF)
- If a <CF b and b <CF c, then a <CF c (transitivity of <CF)
- If a >CF b and b >CF c, then a >CF c (transitivity of >CF)

> NOTE 1
>
> The above conditions are necessary and sufficient to ensure that comparefn divides the set S into equivalence classes and that these equivalence classes are totally ordered.

> NOTE 2
>
> The `sort` function is intentionally generic; it does not require that its this value be an Array object. Therefore, it can be transferred to other kinds of objects for use as a method.

##### 22.1.3.27.1 RS: SortCompare ( x, y ) <div id="sec-sortcompare"></div>

The SortCompare abstract operation is called with two arguments x and y. It also has access to the comparefn argument passed to the current invocation of the `sort` method. The following steps are taken:

1. If x and y are both undefined, return +0.
2. If x is undefined, return 1.
3. If y is undefined, return -1.
4. If comparefn is not undefined, then
   1. Let v be ? ToNumber(? Call(comparefn, undefined, « x, y »)).
   2. If v is NaN, return +0.
   3. Return v.
5. Let xString be ? ToString(x).
6. Let yString be ? ToString(y).
7. Let xSmaller be the result of performing Abstract Relational Comparison xString < yString.
8. If xSmaller is true, return -1.
9. Let ySmaller be the result of performing Abstract Relational Comparison yString < xString.
10. If ySmaller is true, return 1.
11. Return +0.

> NOTE 1
>
> Because non-existent property values always compare greater than undefined property values, and undefined always compares greater than any other value, undefined property values always sort to the end of the result, followed by non-existent property values.

> NOTE 2
>
> Method calls performed by the ToString abstract operations in steps 5 and 7 have the potential to cause SortCompare to not behave as a consistent comparison function.

#### 22.1.3.28 Array.prototype.splice ( start, deleteCount, ...items ) <div id="sec-array.prototype.splice"></div>

> NOTE 1
>
> When the `splice` method is called with two or more arguments start, deleteCount and zero or more items, the deleteCount elements of the array starting at integer index start are replaced by the arguments items. An Array object containing the deleted elements (if any) is returned.

The following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. Let relativeStart be ? ToInteger(start).
4. If relativeStart < 0, let actualStart be max((len + relativeStart), 0); else let actualStart be min(relativeStart, len).
5. If the number of actual arguments is 0, then
   1. Let insertCount be 0.
   2. Let actualDeleteCount be 0.
6. Else if the number of actual arguments is 1, then
   1. Let insertCount be 0.
   2. Let actualDeleteCount be len - actualStart.
7. Else,
   1. Let insertCount be the number of actual arguments minus 2.
   2. Let dc be ? ToInteger(deleteCount).
   3. Let actualDeleteCount be min(max(dc, 0), len - actualStart).
8. If len + insertCount - actualDeleteCount > 253 - 1, throw a TypeError exception.
9. Let A be ? ArraySpeciesCreate(O, actualDeleteCount).
10. Let k be 0.
11. Repeat, while k < actualDeleteCount
    1. Let from be ! ToString(actualStart + k).
    2. Let fromPresent be ? HasProperty(O, from).
    3. If fromPresent is true, then
       1. Let fromValue be ? Get(O, from).
       2. Perform ? CreateDataPropertyOrThrow(A, ! ToString(k), fromValue).
    4. Increment k by 1.
12. Perform ? Set(A, "length", actualDeleteCount, true).
13. Let items be a List whose elements are, in left to right order, the portion of the actual argument list starting with the third argument. The list is empty if fewer than three arguments were passed.
14. Let itemCount be the number of elements in items.
15. If itemCount < actualDeleteCount, then
    1. Set k to actualStart.
    2. Repeat, while k < (len - actualDeleteCount)
       1. Let from be ! ToString(k + actualDeleteCount).
       2. Let to be ! ToString(k + itemCount).
       3. Let fromPresent be ? HasProperty(O, from).
       4. If fromPresent is true, then
          1. Let fromValue be ? Get(O, from).
          2. Perform ? Set(O, to, fromValue, true).
       5. Else fromPresent is false,
          1. Perform ? DeletePropertyOrThrow(O, to).
       6. Increase k by 1.
    3. Set k to len.
    4. Repeat, while k > (len - actualDeleteCount + itemCount)
       1. Perform ? DeletePropertyOrThrow(O, ! ToString(k - 1)).
       2. Decrease k by 1.
16. Else if itemCount > actualDeleteCount, then
    1. Set k to (len - actualDeleteCount).
    2. Repeat, while k > actualStart
       1. Let from be ! ToString(k + actualDeleteCount - 1).
       2. Let to be ! ToString(k + itemCount - 1).
       3. Let fromPresent be ? HasProperty(O, from).
       4. If fromPresent is true, then
          1. Let fromValue be ? Get(O, from).
          2. Perform ? Set(O, to, fromValue, true).
       5. Else fromPresent is false,
          1. Perform ? DeletePropertyOrThrow(O, to).
       6. Decrease k by 1.
17. Set k to actualStart.
18. Repeat, while items is not empty
    1. Remove the first element from items and let E be the value of that element.
    2. Perform ? Set(O, ! ToString(k), E, true).
    3. Increase k by 1.
19. Perform ? Set(O, "length", len - actualDeleteCount + itemCount, true).
20. Return A.

> NOTE 2
>
> The explicit setting of the `"length"` property of the result Array in step 19 was necessary in previous editions of ECMAScript to ensure that its length was correct in situations where the trailing elements of the result Array were not present. Setting `"length"` became unnecessary starting in ES2015 when the result Array was initialized to its proper length rather than an empty Array but is carried forward to preserve backward compatibility.

> NOTE 3
>
> The `splice` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.29 Array.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-array.prototype.tolocalestring"></div>

An ECMAScript implementation that includes the ECMA-402 Internationalization API must implement the `Array.prototype.toLocaleString` method as specified in the ECMA-402 specification. If an ECMAScript implementation does not include the ECMA-402 API the following specification of the `toLocaleString` method is used.

> NOTE 1
>
> The first edition of ECMA-402 did not include a replacement specification for the `Array.prototype.toLocaleString` method.

The meanings of the optional parameters to this method are defined in the ECMA-402 specification; implementations that do not include ECMA-402 support must not use those parameter positions for anything else.

The following steps are taken:

1. Let array be ? ToObject(this value).
2. Let len be ? ToLength(? Get(array, "length")).
3. Let separator be the String value for the list-separator String appropriate for the host environment's current locale (this is derived in an implementation-defined way).
4. Let R be the empty String.
5. Let k be 0.
6. Repeat, while k < len
   1. If k > 0, then
      1. Set R to the string-concatenation of R and separator.
   2. Let nextElement be ? Get(array, ! ToString(k)).
   3. If nextElement is not undefined or null, then
      1. Let S be ? ToString(? Invoke(nextElement, "toLocaleString")).
      2. Set R to the string-concatenation of R and S.
   4. Increase k by 1.
7. Return R.

> NOTE 2
>
> The elements of the array are converted to Strings using their `toLocaleString` methods, and these Strings are then concatenated, separated by occurrences of a separator String that has been derived in an implementation-defined locale-specific way. The result of calling this function is intended to be analogous to the result of `toString`, except that the result of this function is intended to be locale-specific.

> NOTE 3
>
> The `toLocaleString` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.30 Array.prototype.toString ( ) <div id="sec-array.prototype.tostring"></div>

When the `toString` method is called, the following steps are taken:

1. Let array be ? ToObject(this value).
2. Let func be ? Get(array, "join").
3. If IsCallable(func) is false, set func to the intrinsic function %ObjProto_toString%.
4. Return ? Call(func, array).

> NOTE
>
> The `toString` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.31 Array.prototype.unshift ( ...items ) <div id="sec-array.prototype.unshift"></div>

> NOTE 1
>
> The arguments are prepended to the start of the array, such that their order within the array is the same as the order in which they appear in the argument list.

When the `unshift` method is called with zero or more arguments item1, item2, etc., the following steps are taken:

1. Let O be ? ToObject(this value).
2. Let len be ? ToLength(? Get(O, "length")).
3. Let argCount be the number of actual arguments.
4. If argCount > 0, then
   1. If len + argCount > 253 - 1, throw a TypeError exception.
   2. Let k be len.
   3. Repeat, while k > 0,
      1. Let from be ! ToString(k - 1).
      2. Let to be ! ToString(k + argCount - 1).
      3. Let fromPresent be ? HasProperty(O, from).
      4. If fromPresent is true, then
         1. Let fromValue be ? Get(O, from).
         2. Perform ? Set(O, to, fromValue, true).
      5. Else fromPresent is false,
         1. Perform ? DeletePropertyOrThrow(O, to).
      6. Decrease k by 1.
   4. Let j be 0.
   5. Let items be a List whose elements are, in left to right order, the arguments that were passed to this 
   6. function invocation.
   7. Repeat, while items is not empty
      1. Remove the first element from items and let E be the value of that element.
      2. Perform ? Set(O, ! ToString(j), E, true).
      3. Increase j by 1.
5. Perform ? Set(O, "length", len + argCount, true).
6. Return len + argCount.

The `"length"` property of the `unshift` method is 1.

> NOTE 2
>
> The `unshift` function is intentionally generic; it does not require that its this value be an Array object. Therefore it can be transferred to other kinds of objects for use as a method.

#### 22.1.3.32 Array.prototype.values ( ) <div id="sec-array.prototype.values"></div>

The following steps are taken:

1. Let O be ? ToObject(this value).
2. Return CreateArrayIterator(O, `"value"`).

This function is the %ArrayProto_values% intrinsic object.

#### 22.1.3.33 Array.prototype [ @@iterator ] ( ) <div id="sec-array.prototype-@@iterator"></div>

The initial value of the @@iterator property is the same function object as the initial value of the `Array.prototype.values` property.

#### 22.1.3.34 Array.prototype [ @@unscopables ] <div id="sec-array.prototype-@@unscopables"></div>

The initial value of the @@unscopables data property is an object created by the following steps:

1. Let unscopableList be ObjectCreate(null).
2. Perform CreateDataProperty(unscopableList, "copyWithin", true).
3. Perform CreateDataProperty(unscopableList, "entries", true).
4. Perform CreateDataProperty(unscopableList, "fill", true).
5. Perform CreateDataProperty(unscopableList, "find", true).
6. Perform CreateDataProperty(unscopableList, "findIndex", true).
7. Perform CreateDataProperty(unscopableList, "flat", true).
8. Perform CreateDataProperty(unscopableList, "flatMap", true).
9. Perform CreateDataProperty(unscopableList, "includes", true).
10. Perform CreateDataProperty(unscopableList, "keys", true).
11. Perform CreateDataProperty(unscopableList, "values", true).
12. Assert: Each of the above calls returns true.
13. Return unscopableList.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

> NOTE
>
> The own property names of this object are property names that were not included as standard properties of `Array.prototype` prior to the ECMAScript 2015 specification. These names are ignored for `with` statement binding purposes in order to preserve the behaviour of existing code that might use one of these names as a binding in an outer scope that is shadowed by a `with` statement whose binding object is an Array object.

### 22.1.4 Array 实例属性 <div id="sec-properties-of-array-instances"></div>

Array instances are Array exotic objects and have the internal methods specified for such objects. Array instances inherit properties from the Array prototype object.

Array instances have a `"length"` property, and a set of enumerable properties with array index names.

#### 22.1.4.1 length <div id="sec-properties-of-array-instances-length"></div>

The `"length"` property of an Array instance is a data property whose value is always numerically greater than the name of every configurable own property whose name is an array index.

The `"length"` property initially has the attributes { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.

> NOTE
>
> Reducing the value of the `"length"` property has the side-effect of deleting own array elements whose array index is between the old and new length values. However, non-configurable properties can not be deleted. Attempting to set the `"length"` property of an Array object to a value that is numerically less than or equal to the largest numeric own property name of an existing non-configurable [array-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#array-index) property of the array will result in the length being set to a numeric value that is one greater than that non-configurable numeric own property name. See [9.4.2.1](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array-exotic-objects-defineownproperty-p-desc).

### 22.1.5 Array 迭代器对象 <div id="sec-array-iterator-objects"></div>

An Array Iterator is an object, that represents a specific iteration over some specific Array instance object. There is not a named constructor for Array Iterator objects. Instead, Array iterator objects are created by calling certain methods of Array instance objects.

#### 22.1.5.1 CreateArrayIterator ( array, kind ) <div id="sec-createarrayiterator"></div>

Several methods of Array objects return Iterator objects. The abstract operation CreateArrayIterator with arguments array and kind is used to create such iterator objects. It performs the following steps:

1. Assert: Type(array) is Object.
2. Let iterator be ObjectCreate([%ArrayIteratorPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%arrayiteratorprototype%-object), « [[IteratedObject]], [[ArrayIteratorNextIndex]], [[ArrayIterationKind]] »).
3. Set iterator.[[IteratedObject]] to array.
4. Set iterator.[[ArrayIteratorNextIndex]] to 0.
5. Set iterator.[[ArrayIterationKind]] to kind.
6. Return iterator.

#### 22.1.5.2 The %ArrayIteratorPrototype% Object <div id="sec-%arrayiteratorprototype%-object"></div>

The %ArrayIteratorPrototype% object:

- has properties that are inherited by all Array Iterator Objects.
- is an ordinary object.
- has a [[Prototype]] internal slot whose value is the intrinsic object [%IteratorPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%iteratorprototype%-object).
- has the following properties:

##### 22.1.5.2.1 %ArrayIteratorPrototype%.next ( ) <div id="sec-%arrayiteratorprototype%.next"></div>

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have all of the internal slots of an Array Iterator Instance (22.1.5.3), throw a TypeError exception.
4. Let a be O.[[IteratedObject]].
5. If a is undefined, return CreateIterResultObject(undefined, true).
6. Let index be O.[[ArrayIteratorNextIndex]].
7. Let itemKind be O.[[ArrayIterationKind]].
8. If a has a [[TypedArrayName]] internal slot, then
   1. If IsDetachedBuffer(a.[[ViewedArrayBuffer]]) is true, throw a TypeError exception.
   2. Let len be a.[[ArrayLength]].
9. Else,
   1. Let len be ? ToLength(? Get(a, "length")).
10. If index ≥ len, then
    1. Set O.[[IteratedObject]] to undefined.
    2. Return CreateIterResultObject(undefined, true).
11. Set O.[[ArrayIteratorNextIndex]] to index + 1.
12. If itemKind is "key", return CreateIterResultObject(index, false).
13. Let elementKey be ! ToString(index).
14. Let elementValue be ? Get(a, elementKey).
15. If itemKind is "value", let result be elementValue.
16. Else,
    1. Assert: itemKind is "key+value".
    2. Let result be CreateArrayFromList(« index, elementValue »).
17. Return CreateIterResultObject(result, false).

##### 22.1.5.2.2 %ArrayIteratorPrototype% [ @@toStringTag ] <div id="sec-%arrayiteratorprototype%-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value `"Array Iterator"`.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 22.1.5.3 Properties of Array Iterator Instances <div id="sec-properties-of-array-iterator-instances"></div>

Array Iterator instances are ordinary objects that inherit properties from the [%ArrayIteratorPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%arrayiteratorprototype%-object) intrinsic object. Array Iterator instances are initially created with the internal slots listed in Table 58.

Table 58: Internal Slots of Array Iterator Instances

| Internal Slot              | Description                                                  |
| -------------------------- | ------------------------------------------------------------ |
| [[IteratedObject]]         | The object whose array elements are being iterated.          |
| [[ArrayIteratorNextIndex]] | The integer index of the next integer index to be examined by this iteration. |
| [[ArrayIterationKind]]     | A String value that identifies what is returned for each element of the iteration. The possible values are: `"key"`, `"value"`, `"key+value"`. |

## 22.2 类数组对象 <div id="sec-typedarray-objects"></div>

TypedArray objects present an array-like view of an underlying binary data buffer ([24.1](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-arraybuffer-objects)). Each element of a TypedArray instance has the same underlying binary scalar data type. There is a distinct TypedArray constructor, listed in Table 59, for each of the nine supported element types. Each constructor in Table 59 has a corresponding distinct prototype object.

Table 59: The TypedArray Constructors

| Constructor Name and Intrinsic | Element Type | Element Size | Conversion Operation                                         | Description                                 | Equivalent C Type |
| ------------------------------------------------------------ | ------------ | ------------ | ------------------------------------------------------------ | ------------------------------------------- | ----------------- |
| Int8Array %Int8Array%                                        | Int8         | 1            | ToInt8 | 8-bit 2's complement signed integer         | signed char       |
| Uint8Array %Uint8Array%                                      | Uint8        | 1            | ToUint8 | 8-bit unsigned integer                      | unsigned char     |
| Uint8ClampedArray %Uint8ClampedArray%                        | Uint8C       | 1            | ToUint8Clamp | 8-bit unsigned integer (clamped conversion) | unsigned char     |
| Int16Array %Int16Array%                                      | Int16        | 2            | ToInt16 | 16-bit 2's complement signed integer        | short             |
| Uint16Array %Uint16Array%                                    | Uint16       | 2            | ToUint16 | 16-bit unsigned integer                     | unsigned short    |
| Int32Array %Int32Array%                                      | Int32        | 4            | ToInt32 | 32-bit 2's complement signed integer        | int               |
| Uint32Array %Uint32Array%                                    | Uint32       | 4            | ToUint32 | 32-bit unsigned integer                     | unsigned int      |
| Float32Array %Float32Array%                                  | Float32      | 4            |                                                              | 32-bit IEEE floating point                  | float             |
| Float64Array %Float64Array%                                  | Float64      | 8            |                                                              | 64-bit IEEE floating point                  | double            |

In the definitions below, references to TypedArray should be replaced with the appropriate constructor name from the above table. The phrase “the element size in bytes” refers to the value in the Element Size column of the table in the row corresponding to the constructor. The phrase “element Type” refers to the value in the Element Type column for that row.

### 22.2.1 %TypedArray% 内部对象 <div id="sec-%typedarray%-intrinsic-object"></div>

The %TypedArray% intrinsic object:

- is a constructor function object that all of the TypedArray constructor objects inherit from.
- along with its corresponding prototype object, provides common properties that are inherited by all TypedArray constructors and their instances.
- does not have a global name or appear as a property of the global object.
- acts as the abstract superclass of the various TypedArray constructors.
- will throw an error when invoked, because it is an abstract class constructor. The TypedArray constructors do not perform a `super` call to it.

#### 22.2.1.1 %TypedArray% ( ) <div id="sec-%typedarray%"></div>

The [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object) constructor performs the following steps:

1. Throw a TypeError exception.

The `"length"` property of the [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object) constructor function is 0.

### 22.2.2 %TypedArray% 内部对象属性 <div id="sec-properties-of-the-%typedarray%-intrinsic-object"></div>

The [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object) intrinsic object:

- has a [[Prototype]] internal slot whose value is the intrinsic object [%FunctionPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-function-prototype-object).
- has a `name` property whose value is `"TypedArray"`.
- has the following properties:

#### 22.2.2.1 %TypedArray%.from ( source [ , mapfn [ , thisArg ] ] ) <div id="sec-%typedarray%.from"></div>

When the from method is called with argument source, and optional arguments mapfn and thisArg, the following steps are taken:

1. Let C be the this value.
2. If IsConstructor(C) is false, throw a TypeError exception.
3. If mapfn is present and mapfn is not undefined, then
   1. If IsCallable(mapfn) is false, throw a TypeError exception.
   2. Let mapping be true.
4. Else, let mapping be false.
5. If thisArg is present, let T be thisArg; else let T be undefined.
6. Let usingIterator be ? GetMethod(source, @@iterator).
7. If usingIterator is not undefined, then
   1. Let values be ? IterableToList(source, usingIterator).
   2. Let len be the number of elements in values.
   3. Let targetObj be ? TypedArrayCreate(C, « len »).
   4. Let k be 0.
   5. Repeat, while k < len
      1. Let Pk be ! ToString(k).
      2. Let kValue be the first element of values and remove that element from values.
      3. If mapping is true, then
         1. Let mappedValue be ? Call(mapfn, T, « kValue, k »).
      4. Else, let mappedValue be kValue.
      5. Perform ? Set(targetObj, Pk, mappedValue, true).
      6. Increase k by 1.
   6. Assert: values is now an empty List.
   7. Return targetObj.
8. NOTE: source is not an Iterable so assume it is already an array-like object.
9. Let arrayLike be ! ToObject(source).
10. Let len be ? ToLength(? Get(arrayLike, "length")).
11. Let targetObj be ? TypedArrayCreate(C, « len »).
12. Let k be 0.
13. Repeat, while k < len
    1. Let Pk be ! ToString(k).
    2. Let kValue be ? Get(arrayLike, Pk).
    3. If mapping is true, then
       1. Let mappedValue be ? Call(mapfn, T, « kValue, k »).
    4. Else, let mappedValue be kValue.
    5. Perform ? Set(targetObj, Pk, mappedValue, true).
    6. Increase k by 1.
14. Return targetObj.

##### 22.2.2.1.1Runtime Semantics: IterableToList ( items, method )<div id="sec-iterabletolist"></div>

The abstract operation IterableToList performs the following steps:

1. Let iteratorRecord be ? GetIterator(items, sync, method).
2. Let values be a new empty List.
3. Let next be true.
4. Repeat, while next is not false
   1. Set next to ? IteratorStep(iteratorRecord).
   2. If next is not false, then
      1. Let nextValue be ? IteratorValue(next).
      2. Append nextValue to the end of the List values.
5. Return values.

#### 22.2.2.2 %TypedArray%.of ( ...items ) <div id="sec-%typedarray%.of"></div>

When the `of` method is called with any number of arguments, the following steps are taken:

1. Let len be the actual number of arguments passed to this function.
2. Let items be the List of arguments passed to this function.
3. Let C be the this value.
4. If IsConstructor(C) is false, throw a TypeError exception.
5. Let newObj be ? TypedArrayCreate(C, « len »).
6. Let k be 0.
7. Repeat, while k < len
   1. Let kValue be items[k].
   2. Let Pk be ! ToString(k).
   3. Perform ? Set(newObj, Pk, kValue, true).
   4. Increase k by 1.
8. Return newObj.

> NOTE
>
> The items argument is assumed to be a well-formed rest argument value.

#### 22.2.2.3 %TypedArray%.prototype <div id="sec-%typedarray%.prototype"></div>

The initial value of [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype` is the [%TypedArrayPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-%typedarrayprototype%-object) intrinsic object.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 22.2.2.4 get %TypedArray% [ @@species ] <div id="sec-get-%typedarray%-@@species"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`[@@species]` is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Return the this value.

The value of the `name` property of this function is `"get [Symbol.species]"`.

>NOTE
>
>[%TypedArrayPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-%typedarrayprototype%-object) methods normally use their `this` object's constructor to create a derived object. However, a subclass constructor may over-ride that default behaviour by redefining its @@species property.

### 22.2.3 %TypedArrayPrototype% 对象属性 <div id="sec-properties-of-the-%typedarrayprototype%-object"></div>

The %TypedArrayPrototype% object:

- has a [[Prototype]] internal slot whose value is the intrinsic object [%ObjectPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-object-prototype-object).
- is an ordinary object.
- does not have a [[ViewedArrayBuffer]] or any other of the internal slots that are specific to TypedArray instance objects.

#### 22.2.3.1 获取 %TypedArray%.prototype.buffer <div id="sec-get-%typedarray%.prototype.buffer"></div>

%TypedArray%.prototype.buffer is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have a [[TypedArrayName]] internal slot, throw a TypeError exception.
4. Assert: O has a [[ViewedArrayBuffer]] internal slot.
5. Let buffer be O.[[ViewedArrayBuffer]].
6. Return buffer.

#### 22.2.3.2 获取 %TypedArray%.prototype.byteLength <div id="sec-get-%typedarray%.prototype.bytelength"></div>

%TypedArray%.prototype.byteLength is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have a [[TypedArrayName]] internal slot, throw a TypeError exception.
4. Assert: O has a [[ViewedArrayBuffer]] internal slot.
5. Let buffer be O.[[ViewedArrayBuffer]].
6. If IsDetachedBuffer(buffer) is true, return 0.
7. Let size be O.[[ByteLength]].
8. Return size.

#### 22.2.3.3 获取 %TypedArray%.prototype.byteOffset <div id="sec-get-%typedarray%.prototype.byteoffset"></div>

%TypedArray%.prototype.byteOffset is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have a [[TypedArrayName]] internal slot, throw a TypeError exception.
4. Assert: O has a [[ViewedArrayBuffer]] internal slot.
5. Let buffer be O.[[ViewedArrayBuffer]].
6. If IsDetachedBuffer(buffer) is true, return 0.
7. Let offset be O.[[ByteOffset]].
8. Return offset.

#### 22.2.3.4 %TypedArray%.prototype.constructor <div id="sec-%typedarray%.prototype.constructor"></div>

The initial value of [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.constructor` is the [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object) intrinsic object.

#### 22.2.3.5 %TypedArray%.prototype.copyWithin ( target, start [ , end ] ) <div id="sec-%typedarray%.prototype.copywithin"></div>

The interpretation and use of the arguments of [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.copyWithin` are the same as for `Array.prototype.copyWithin` as defined in [22.1.3.3](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.copywithin).

The following steps are taken:

1. Let O be this value.
2. Perform ? ValidateTypedArray(O).
3. Let len be O.[[ArrayLength]].
4. Let relativeTarget be ? ToInteger(target).
5. If relativeTarget < 0, let to be max((len + relativeTarget), 0); else let to be min(relativeTarget, len).
6. Let relativeStart be ? ToInteger(start).
7. If relativeStart < 0, let from be max((len + relativeStart), 0); else let from be min(relativeStart, len).
8. If end is undefined, let relativeEnd be len; else let relativeEnd be ? ToInteger(end).
9. If relativeEnd < 0, let final be max((len + relativeEnd), 0); else let final be min(relativeEnd, len).
10. Let count be min(final - from, len - to).
11. If count > 0, then
    1. NOTE: The copying must be performed in a manner that preserves the bit-level encoding of the source data.
    2. Let buffer be O.[[ViewedArrayBuffer]].
    3. If IsDetachedBuffer(buffer) is true, throw a TypeError exception.
    4. Let typedArrayName be the String value of O.[[TypedArrayName]].
    5. Let elementSize be the Number value of the Element Size value specified in Table 59 for typedArrayName.
    6. Let byteOffset be O.[[ByteOffset]].
    7. Let toByteIndex be to × elementSize + byteOffset.
    8. Let fromByteIndex be from × elementSize + byteOffset.
    9. Let countBytes be count × elementSize.
    10. If fromByteIndex < toByteIndex and toByteIndex < fromByteIndex + countBytes, then
        1. Let direction be -1.
        2. Set fromByteIndex to fromByteIndex + countBytes - 1.
        3. Set toByteIndex to toByteIndex + countBytes - 1.
    11. Else,
        1. Let direction be 1.
    12. Repeat, while countBytes > 0
        1. Let value be GetValueFromBuffer(buffer, fromByteIndex, "Uint8", true, "Unordered").
        2. Perform SetValueInBuffer(buffer, toByteIndex, "Uint8", value, true, "Unordered").
        3. Set fromByteIndex to fromByteIndex + direction.
        4. Set toByteIndex to toByteIndex + direction.
        5. Decrease countBytes by 1.
12. Return O.

##### 22.2.3.5.1 RS: ValidateTypedArray ( O ) <div id="sec-validatetypedarray"></div>

When called with argument O, the following steps are taken:

1. If Type(O) is not Object, throw a TypeError exception.
2. If O does not have a [[TypedArrayName]] internal slot, throw a TypeError exception.
3. Assert: O has a [[ViewedArrayBuffer]] internal slot.
4. Let buffer be O.[[ViewedArrayBuffer]].
5. If IsDetachedBuffer(buffer) is true, throw a TypeError exception.
6. Return buffer.

#### 22.2.3.6 %TypedArray%.prototype.entries ( ) <div id="sec-%typedarray%.prototype.entries"></div>

The following steps are taken:

1. Let O be the this value.
2. Perform ? ValidateTypedArray(O).
3. Return CreateArrayIterator(O, "key+value").

#### 22.2.3.7 %TypedArray%.prototype.every ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.every"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.every` is a distinct function that implements the same algorithm as `Array.prototype.every` as defined in [22.1.3.5](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.every) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.8 %TypedArray%.prototype.fill ( value [ , start [ , end ] ] ) <div id="sec-%typedarray%.prototype.fill"></div>

The interpretation and use of the arguments of %TypedArray%.prototype.fill are the same as for Array.prototype.fill as defined in 22.1.3.6.

The following steps are taken:

1. Let O be the this value.
2. Perform ? ValidateTypedArray(O).
3. Let len be O.[[ArrayLength]].
4. Set value to ? ToNumber(value).
5. Let relativeStart be ? ToInteger(start).
6. If relativeStart < 0, let k be max((len + relativeStart), 0); else let k be min(relativeStart, len).
7. If end is undefined, let relativeEnd be len; else let relativeEnd be ? ToInteger(end).
8. If relativeEnd < 0, let final be max((len + relativeEnd), 0); else let final be min(relativeEnd, len).
9. If IsDetachedBuffer(O.[[ViewedArrayBuffer]]) is true, throw a TypeError exception.
10. Repeat, while k < final
    1. Let Pk be ! ToString(k).
    2. Perform ! Set(O, Pk, value, true).
    3. Increase k by 1.
11. Return O.

#### 22.2.3.9 %TypedArray%.prototype.filter ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.filter"></div>

The interpretation and use of the arguments of %TypedArray%.prototype.filter are the same as for Array.prototype.filter as defined in 22.1.3.7.

When the filter method is called with one or two arguments, the following steps are taken:

1. Let O be the this value.
2. Perform ? ValidateTypedArray(O).
3. Let len be O.[[ArrayLength]].
4. If IsCallable(callbackfn) is false, throw a TypeError exception.
5. If thisArg is present, let T be thisArg; else let T be undefined.
6. Let kept be a new empty List.
7. Let k be 0.
8. Let captured be 0.
9. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kValue be ? Get(O, Pk).
   3. Let selected be ToBoolean(? Call(callbackfn, T, « kValue, k, O »)).
   4. If selected is true, then
      1. Append kValue to the end of kept.
      2. Increase captured by 1.
   5. Increase k by 1.
10. Let A be ? TypedArraySpeciesCreate(O, « captured »).
11. Let n be 0.
12. For each element e of kept, do
    1. Perform ! Set(A, ! ToString(n), e, true).
    2. Increment n by 1.
13. Return A.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.10 %TypedArray%.prototype.find ( predicate [ , thisArg ] ) <div id="sec-%typedarray%.prototype.find"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.find` is a distinct function that implements the same algorithm as `Array.prototype.find` as defined in [22.1.3.8](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.find) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to predicate may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.11 %TypedArray%.prototype.findIndex ( predicate [ , thisArg ] ) <div id="sec-%typedarray%.prototype.findindex"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.findIndex` is a distinct function that implements the same algorithm as `Array.prototype.findIndex` as defined in [22.1.3.9](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.findindex) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to predicate may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.12 %TypedArray%.prototype.forEach ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.foreach"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.forEach` is a distinct function that implements the same algorithm as `Array.prototype.forEach` as defined in [22.1.3.12](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.foreach) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.13 %TypedArray%.prototype.includes ( searchElement [ , fromIndex ] ) <div id="sec-%typedarray%.prototype.includes"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.includes` is a distinct function that implements the same algorithm as `Array.prototype.includes` as defined in [22.1.3.13](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.includes) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.14 %TypedArray%.prototype.indexOf ( searchElement [ , fromIndex ] ) <div id="sec-%typedarray%.prototype.indexof"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.indexOf` is a distinct function that implements the same algorithm as `Array.prototype.indexOf` as defined in [22.1.3.14](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.indexof) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.15 %TypedArray%.prototype.join ( separator ) <div id="sec-%typedarray%.prototype.join"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.join` is a distinct function that implements the same algorithm as `Array.prototype.join` as defined in [22.1.3.15](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.join) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.16 %TypedArray%.prototype.keys ( ) <div id="sec-%typedarray%.prototype.keys"></div>

The following steps are taken:

1. Let O be the this value.
2. Perform ? ValidateTypedArray(O).
3. Return CreateArrayIterator(O, "key").

#### 22.2.3.17 %TypedArray%.prototype.lastIndexOf ( searchElement [ , fromIndex ] ) <div id="sec-%typedarray%.prototype.lastindexof"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.lastIndexOf` is a distinct function that implements the same algorithm as `Array.prototype.lastIndexOf` as defined in [22.1.3.17](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.lastindexof) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.18 获取 %TypedArray%.prototype.length <div id="sec-get-%typedarray%.prototype.length"></div>

%TypedArray%.prototype.length is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have a [[TypedArrayName]] internal slot, throw a TypeError exception.
4. Assert: O has [[ViewedArrayBuffer]] and [[ArrayLength]] internal slots.
5. Let buffer be O.[[ViewedArrayBuffer]].
6. If IsDetachedBuffer(buffer) is true, return 0.
7. Let length be O.[[ArrayLength]].
8. Return length.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.19 %TypedArray%.prototype.map ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.map"></div>

The interpretation and use of the arguments of %TypedArray%.prototype.map are the same as for Array.prototype.map as defined in 22.1.3.18.

When the map method is called with one or two arguments, the following steps are taken:

1. Let O be the this value.
2. Perform ? ValidateTypedArray(O).
3. Let len be O.[[ArrayLength]].
4. If IsCallable(callbackfn) is false, throw a TypeError exception.
5. If thisArg is present, let T be thisArg; else let T be undefined.
6. Let A be ? TypedArraySpeciesCreate(O, « len »).
7. Let k be 0.
8. Repeat, while k < len
   1. Let Pk be ! ToString(k).
   2. Let kValue be ? Get(O, Pk).
   3. Let mappedValue be ? Call(callbackfn, T, « kValue, k, O »).
   4. Perform ? Set(A, Pk, mappedValue, true).
   5. Increase k by 1.
9. Return A.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.20 %TypedArray%.prototype.reduce ( callbackfn [ , initialValue ] ) <div id="sec-%typedarray%.prototype.reduce"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.reduce` is a distinct function that implements the same algorithm as `Array.prototype.reduce` as defined in [22.1.3.21](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.reduce) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.21 %TypedArray%.prototype.reduceRight ( callbackfn [ , initialValue ] ) <div id="sec-%typedarray%.prototype.reduceright"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.reduceRight` is a distinct function that implements the same algorithm as `Array.prototype.reduceRight` as defined in [22.1.3.22](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.reduceright) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.22 %TypedArray%.prototype.reverse ( ) <div id="sec-%typedarray%.prototype.reverse"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.reverse` is a distinct function that implements the same algorithm as `Array.prototype.reverse` as defined in [22.1.3.23](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.reverse) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.23 %TypedArray%.prototype.set ( overloaded [ , offset ] ) <div id="sec-%typedarray%.prototype.set-overloaded-offset"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.set` is a single function whose behaviour is overloaded based upon the type of its first argument.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

##### 22.2.3.23.1 %TypedArray%.prototype.set ( array [ , offset ] ) <div id="sec-%typedarray%.prototype.set-array-offset"></div>

Sets multiple values in this TypedArray, reading the values from the object array. The optional offset value indicates the first element index in this TypedArray where values are written. If omitted, it is assumed to be 0.

1. Assert: array is any ECMAScript language value other than an Object with a [[TypedArrayName]] internal slot. If it is such an Object, the definition in 22.2.3.23.2 applies.
2. Let target be the this value.
3. If Type(target) is not Object, throw a TypeError exception.
4. If target does not have a [[TypedArrayName]] internal slot, throw a TypeError exception.
5. Assert: target has a [[ViewedArrayBuffer]] internal slot.
6. Let targetOffset be ? ToInteger(offset).
7. If targetOffset < 0, throw a RangeError exception.
8. Let targetBuffer be target.[[ViewedArrayBuffer]].
9. If IsDetachedBuffer(targetBuffer) is true, throw a TypeError exception.
10. Let targetLength be target.[[ArrayLength]].
11. Let targetName be the String value of target.[[TypedArrayName]].
12. Let targetElementSize be the Number value of the Element Size value specified in Table 59 for targetName.
13. Let targetType be the String value of the Element Type value in Table 59 for targetName.
14. Let targetByteOffset be target.[[ByteOffset]].
15. Let src be ? ToObject(array).
16. Let srcLength be ? ToLength(? Get(src, "length")).
17. If srcLength + targetOffset > targetLength, throw a RangeError exception.
18. Let targetByteIndex be targetOffset × targetElementSize + targetByteOffset.
19. Let k be 0.
20. Let limit be targetByteIndex + targetElementSize × srcLength.
21. Repeat, while targetByteIndex < limit
    1. Let Pk be ! ToString(k).
    2. Let kNumber be ? ToNumber(? Get(src, Pk)).
    3. If IsDetachedBuffer(targetBuffer) is true, throw a TypeError exception.
    4. Perform SetValueInBuffer(targetBuffer, targetByteIndex, targetType, kNumber, true, "Unordered").
    5. Increase k by 1.
    6. Set targetByteIndex to targetByteIndex + targetElementSize.
22. Return undefined.

##### 22.2.3.23.2 %TypedArray%.prototype.set ( typedArray [ , offset ] ) <div id="sec-%typedarray%.prototype.set-typedarray-offset"></div>

Sets multiple values in this TypedArray, reading the values from the typedArray argument object. The optional offset value indicates the first element index in this TypedArray where values are written. If omitted, it is assumed to be 0.

1. Assert: typedArray has a [[TypedArrayName]] internal slot. If it does not, the definition in 22.2.3.23.1 applies.
2. Let target be the this value.
3. If Type(target) is not Object, throw a TypeError exception.
4. If target does not have a [[TypedArrayName]] internal slot, throw a TypeError exception.
5. Assert: target has a [[ViewedArrayBuffer]] internal slot.
6. Let targetOffset be ? ToInteger(offset).
7. If targetOffset < 0, throw a RangeError exception.
8. Let targetBuffer be target.[[ViewedArrayBuffer]].
9. If IsDetachedBuffer(targetBuffer) is true, throw a TypeError exception.
10. Let targetLength be target.[[ArrayLength]].
11. Let srcBuffer be typedArray.[[ViewedArrayBuffer]].
12. If IsDetachedBuffer(srcBuffer) is true, throw a TypeError exception.
13. Let targetName be the String value of target.[[TypedArrayName]].
14. Let targetType be the String value of the Element Type value in Table 59 for targetName.
15. Let targetElementSize be the Number value of the Element Size value specified in Table 59 for targetName.
16. Let targetByteOffset be target.[[ByteOffset]].
17. Let srcName be the String value of typedArray.[[TypedArrayName]].
18. Let srcType be the String value of the Element Type value in Table 59 for srcName.
19. Let srcElementSize be the Number value of the Element Size value specified in Table 59 for srcName.
20. Let srcLength be typedArray.[[ArrayLength]].
21. Let srcByteOffset be typedArray.[[ByteOffset]].
22. If srcLength + targetOffset > targetLength, throw a RangeError exception.
23. If both IsSharedArrayBuffer(srcBuffer) and IsSharedArrayBuffer(targetBuffer) are true, then
    1. If srcBuffer.[[ArrayBufferData]] and targetBuffer.[[ArrayBufferData]] are the same Shared Data Block values, let same be true; else let same be false.
24. Else, let same be SameValue(srcBuffer, targetBuffer).
25. If same is true, then
    1. Let srcByteLength be typedArray.[[ByteLength]].
    2. Set srcBuffer to ? CloneArrayBuffer(srcBuffer, srcByteOffset, srcByteLength, %ArrayBuffer%).
    3. NOTE: %ArrayBuffer% is used to clone srcBuffer because is it known to not have any observable side-effects.
    4. Let srcByteIndex be 0.
26. Else, let srcByteIndex be srcByteOffset.
27. Let targetByteIndex be targetOffset × targetElementSize + targetByteOffset.
28. Let limit be targetByteIndex + targetElementSize × srcLength.
29. If SameValue(srcType, targetType) is true, then
    1. NOTE: If srcType and targetType are the same, the transfer must be performed in a manner that preserves the bit-level encoding of the source data.
    2. Repeat, while targetByteIndex < limit
       1. Let value be GetValueFromBuffer(srcBuffer, srcByteIndex, "Uint8", true, "Unordered").
       2. Perform SetValueInBuffer(targetBuffer, targetByteIndex, "Uint8", value, true, "Unordered").
       3. Increase srcByteIndex by 1.
       4. Increase targetByteIndex by 1.
30. Else,
    1. Repeat, while targetByteIndex < limit
       1. Let value be GetValueFromBuffer(srcBuffer, srcByteIndex, srcType, true, "Unordered").
       2. Perform SetValueInBuffer(targetBuffer, targetByteIndex, targetType, value, true, "Unordered").
       3. Set srcByteIndex to srcByteIndex + srcElementSize.
       4. Set targetByteIndex to targetByteIndex + targetElementSize.
31. Return undefined.

#### 22.2.3.24 %TypedArray%.prototype.slice ( start, end ) <div id="sec-%typedarray%.prototype.slice"></div>

The interpretation and use of the arguments of %TypedArray%.prototype.slice are the same as for Array.prototype.slice as defined in 22.1.3.25. The following steps are taken:

1. Let O be the this value.
2. Perform ? ValidateTypedArray(O).
3. Let len be O.[[ArrayLength]].
4. Let relativeStart be ? ToInteger(start).
5. If relativeStart < 0, let k be max((len + relativeStart), 0); else let k be min(relativeStart, len).
6. If end is undefined, let relativeEnd be len; else let relativeEnd be ? ToInteger(end).
7. If relativeEnd < 0, let final be max((len + relativeEnd), 0); else let final be min(relativeEnd, len).
8. Let count be max(final - k, 0).
9. Let A be ? TypedArraySpeciesCreate(O, « count »).
10. Let srcName be the String value of O.[[TypedArrayName]].
11. Let srcType be the String value of the Element Type value in Table 59 for srcName.
12. Let targetName be the String value of A.[[TypedArrayName]].
13. Let targetType be the String value of the Element Type value in Table 59 for targetName.
14. If SameValue(srcType, targetType) is false, then
    1. Let n be 0.
    2. Repeat, while k < final
       1. Let Pk be ! ToString(k).
       2. Let kValue be ? Get(O, Pk).
       3. Perform ! Set(A, ! ToString(n), kValue, true).
       4. Increase k by 1.
       5. Increase n by 1.
15. Else if count > 0, then
    1. Let srcBuffer be O.[[ViewedArrayBuffer]].
    2. If IsDetachedBuffer(srcBuffer) is true, throw a TypeError exception.
    3. Let targetBuffer be A.[[ViewedArrayBuffer]].
    4. Let elementSize be the Number value of the Element Size value specified in Table 59 for srcType.
    5. NOTE: If srcType and targetType are the same, the transfer must be performed in a manner that preserves the bit-level encoding of the source data.
    6. Let srcByteOffet be O.[[ByteOffset]].
    7. Let targetByteIndex be A.[[ByteOffset]].
    8. Let srcByteIndex be (k × elementSize) + srcByteOffet.
    9. Let limit be targetByteIndex + count × elementSize.
    10. Repeat, while targetByteIndex < limit
        1. Let value be GetValueFromBuffer(srcBuffer, srcByteIndex, "Uint8", true, "Unordered").
        2. Perform SetValueInBuffer(targetBuffer, targetByteIndex, "Uint8", value, true, "Unordered").
        3. Increase srcByteIndex by 1.
        4. Increase targetByteIndex by 1.
16. Return A.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.25 %TypedArray%.prototype.some ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.some"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.some` is a distinct function that implements the same algorithm as `Array.prototype.some` as defined in [22.1.3.26](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.some) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.26 %TypedArray%.prototype.sort ( comparefn ) <div id="sec-%typedarray%.prototype.sort"></div>

%TypedArray%.prototype.sort is a distinct function that, except as described below, implements the same requirements as those of Array.prototype.sort as defined in 22.1.3.27. The implementation of the %TypedArray%.prototype.sort specification may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. The only internal methods of the this object that the algorithm may call are [[Get]] and [[Set]].

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

Upon entry, the following steps are performed to initialize evaluation of the sort function. These steps are used instead of the entry steps in 22.1.3.27:

1. If comparefn is not undefined and IsCallable(comparefn) is false, throw a TypeError exception.
2. Let obj be the this value.
3. Let buffer be ? ValidateTypedArray(obj).
4. Let len be obj.[[ArrayLength]].

The implementation-defined sort order condition for exotic objects is not applied by %TypedArray%.prototype.sort.

The following version of SortCompare is used by %TypedArray%.prototype.sort. It performs a numeric comparison rather than the string comparison used in 22.1.3.27. SortCompare has access to the comparefn and buffer values of the current invocation of the sort method.

When the TypedArray SortCompare abstract operation is called with two arguments x and y, the following steps are taken:

1. Assert: Both Type(x) and Type(y) is Number.
2. If comparefn is not undefined, then
   1. Let v be ? ToNumber(? Call(comparefn, undefined, « x, y »)).
   2. If IsDetachedBuffer(buffer) is true, throw a TypeError exception.
   3. If v is NaN, return +0.
   4. Return v.
3. If x and y are both NaN, return +0.
4. If x is NaN, return 1.
5. If y is NaN, return -1.
6. If x < y, return -1.
7. If x > y, return 1.
8. If x is -0 and y is +0, return -1.
9. If x is +0 and y is -0, return 1.
10. Return +0.

> NOTE
>
> Because NaN always compares greater than any other value, NaN property values always sort to the end of the result when comparefn is not provided.

#### 22.2.3.27 %TypedArray%.prototype.subarray ( begin, end ) <div id="sec-%typedarray%.prototype.subarray"></div>

Returns a new TypedArray object whose element type is the same as this TypedArray and whose ArrayBuffer is the same as the ArrayBuffer of this TypedArray, referencing the elements at begin, inclusive, up to end, exclusive. If either begin or end is negative, it refers to an index from the end of the array, as opposed to from the beginning.

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have a [[TypedArrayName]] internal slot, throw a TypeError exception.
4. Assert: O has a [[ViewedArrayBuffer]] internal slot.
5. Let buffer be O.[[ViewedArrayBuffer]].
6. Let srcLength be O.[[ArrayLength]].
7. Let relativeBegin be ? ToInteger(begin).
8. If relativeBegin < 0, let beginIndex be max((srcLength + relativeBegin), 0); else let beginIndex be min(relativeBegin, srcLength).
9. If end is undefined, let relativeEnd be srcLength; else, let relativeEnd be ? ToInteger(end).
10. If relativeEnd < 0, let endIndex be max((srcLength + relativeEnd), 0); else let endIndex be min(relativeEnd, srcLength).
11. Let newLength be max(endIndex - beginIndex, 0).
12. Let constructorName be the String value of O.[[TypedArrayName]].
13. Let elementSize be the Number value of the Element Size value specified in Table 59 for constructorName.
14. Let srcByteOffset be O.[[ByteOffset]].
15. Let beginByteOffset be srcByteOffset + beginIndex × elementSize.
16. Let argumentsList be « buffer, beginByteOffset, newLength ».
17. Return ? TypedArraySpeciesCreate(O, argumentsList).

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.28 %TypedArray%.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-%typedarray%.prototype.tolocalestring"></div>

[%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.toLocaleString` is a distinct function that implements the same algorithm as `Array.prototype.toLocaleString` as defined in [22.1.3.29](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.tolocalestring) except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose [integer-indexed](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

> NOTE
>
> If the ECMAScript implementation includes the ECMA-402 Internationalization API this function is based upon the algorithm for `Array.prototype.toLocaleString` that is in the ECMA-402 specification.

#### 22.2.3.29 %TypedArray%.prototype.toString ( ) <div id="sec-%typedarray%.prototype.tostring"></div>

The initial value of the [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.toString` data property is the same built-in function object as the `Array.prototype.toString` method defined in [22.1.3.30](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-array.prototype.tostring).

#### 22.2.3.30 %TypedArray%.prototype.values ( ) <div id="sec-%typedarray%.prototype.values"></div>

The following steps are taken:

1. Let O be the this value.
2. Perform ? ValidateTypedArray(O).
3. Return CreateArrayIterator(O, "value").

#### 22.2.3.31 %TypedArray%.prototype [ @@iterator ] ( ) <div id="sec-%typedarray%.prototype-@@iterator"></div>

The initial value of the @@iterator property is the same function object as the initial value of the [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype.values` property.

#### 22.2.3.32 获取 %TypedArray%.prototype [ @@toStringTag ] <div id="sec-get-%typedarray%.prototype-@@tostringtag"></div>

%TypedArray%.prototype[@@toStringTag] is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let O be the this value.
2. If Type(O) is not Object, return undefined.
3. If O does not have a [[TypedArrayName]] internal slot, return undefined.
4. Let name be O.[[TypedArrayName]].
5. Assert: Type(name) is String.
6.  Return name.

This property has the attributes { [[Enumerable]]: false, [[Configurable]]: true }.

The initial value of the name property of this function is "get [Symbol.toStringTag]".

### 22.2.4 TypedArray 构造器 <div id="sec-typedarray-constructors"></div>

Each TypedArray constructor:

- is an intrinsic object that has the structure described below, differing only in the name used as the constructor name instead of TypedArray, in Table 59.
- is a single function whose behaviour is overloaded based upon the number and types of its arguments. The actual behaviour of a call of TypedArray depends upon the number and kind of arguments that are passed to it.
- is not intended to be called as a function and will throw an exception when called in that manner.
- is designed to be subclassable. It may be used as the value of an `extends` clause of a class definition. Subclass constructors that intend to inherit the specified TypedArray behaviour must include a `super` call to the TypedArray constructor to create and initialize the subclass instance with the internal state necessary to support the [%TypedArray%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%typedarray%-intrinsic-object)`.prototype` built-in methods.
- has a `"length"` property whose value is 3.

#### 22.2.4.1 TypedArray ( ) <div id="sec-typedarray"></div>

This description applies only if the TypedArray function is called with no arguments.

1. If NewTarget is undefined, throw a TypeError exception.
2. Let constructorName be the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
3. Return ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%", 0).

#### 22.2.4.2 TypedArray ( length ) <div id="sec-typedarray-length"></div>

This description applies only if the TypedArray function is called with at least one argument and the Type of the first argument is not Object.

TypedArray called with argument length performs the following steps:

1. Assert: Type(length) is not Object.
2. If NewTarget is undefined, throw a TypeError exception.
3. Let elementLength be ? ToIndex(length).
4. Let constructorName be the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
5. Return ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%", elementLength).

##### 22.2.4.2.1 RS: AllocateTypedArray ( constructorName, newTarget, defaultProto [ , length ] ) <div id="sec-allocatetypedarray"></div>

The abstract operation AllocateTypedArray with arguments constructorName, newTarget, defaultProto and optional argument length is used to validate and create an instance of a TypedArray constructor. constructorName is required to be the name of a TypedArray constructor in Table 59. If the length argument is passed, an ArrayBuffer of that length is also allocated and associated with the new TypedArray instance. AllocateTypedArray provides common semantics that is used by all of the TypedArray overloads. AllocateTypedArray performs the following steps:

1. Let proto be ? GetPrototypeFromConstructor(newTarget, defaultProto).
2. Let obj be IntegerIndexedObjectCreate(proto, « [[ViewedArrayBuffer]], [[TypedArrayName]], [[ByteLength]], [[ByteOffset]], [[ArrayLength]] »).
3. Assert: obj.[[ViewedArrayBuffer]] is undefined.
4. Set obj.[[TypedArrayName]] to constructorName.
5. If length is not present, then
   1. Set obj.[[ByteLength]] to 0.
   2. Set obj.[[ByteOffset]] to 0.
   3. Set obj.[[ArrayLength]] to 0.
6. Else,
   1. Perform ? AllocateTypedArrayBuffer(obj, length).
7. Return obj.

##### 22.2.4.2.2 RS: AllocateTypedArrayBuffer ( O, length ) <div id="sec-allocatetypedarraybuffer"></div>

The abstract operation AllocateTypedArrayBuffer with arguments O and length allocates and associates an ArrayBuffer with the TypedArray instance O. It performs the following steps:

1. Assert: O is an Object that has a [[ViewedArrayBuffer]] internal slot.
2. Assert: O.[[ViewedArrayBuffer]] is undefined.
3. Assert: length ≥ 0.
4. Let constructorName be the String value of O.[[TypedArrayName]].
5. Let elementSize be the Element Size value in Table 59 for constructorName.
6. Let byteLength be elementSize × length.
7. Let data be ? AllocateArrayBuffer(%ArrayBuffer%, byteLength).
8. Set O.[[ViewedArrayBuffer]] to data.
9. Set O.[[ByteLength]] to byteLength.
10. Set O.[[ByteOffset]] to 0.
11. Set O.[[ArrayLength]] to length.
12. Return O.

#### 22.2.4.3 TypedArray ( typedArray ) <div id="sec-typedarray-typedarray"></div>

This description applies only if the TypedArray function is called with at least one argument and the Type of the first argument is Object and that object has a [[TypedArrayName]] internal slot.

TypedArray called with argument typedArray performs the following steps:

1. Assert: Type(typedArray) is Object and typedArray has a [[TypedArrayName]] internal slot.
2. If NewTarget is undefined, throw a TypeError exception.
3. Let constructorName be the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
4. Let O be ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%").
5. Let srcArray be typedArray.
6. Let srcData be srcArray.[[ViewedArrayBuffer]].
7. If IsDetachedBuffer(srcData) is true, throw a TypeError exception.
8. Let elementType be the String value of the Element Type value in Table 59 for constructorName.
9. Let elementLength be srcArray.[[ArrayLength]].
10. Let srcName be the String value of srcArray.[[TypedArrayName]].
11. Let srcType be the String value of the Element Type value in Table 59 for srcName.
12. Let srcElementSize be the Element Size value in Table 59 for srcName.
13. Let srcByteOffset be srcArray.[[ByteOffset]].
14. Let elementSize be the Element Size value in Table 59 for constructorName.
15. Let byteLength be elementSize × elementLength.
16. If IsSharedArrayBuffer(srcData) is false, then
    1. Let bufferConstructor be ? SpeciesConstructor(srcData, %ArrayBuffer%).
17. Else,
    1. Let bufferConstructor be %ArrayBuffer%.
18. If SameValue(elementType, srcType) is true, then
    1. Let data be ? CloneArrayBuffer(srcData, srcByteOffset, byteLength, bufferConstructor).
19. Else,
    1. Let data be ? AllocateArrayBuffer(bufferConstructor, byteLength).
    2. If IsDetachedBuffer(srcData) is true, throw a TypeError exception.
    3. Let srcByteIndex be srcByteOffset.
    4. Let targetByteIndex be 0.
    5. Let count be elementLength.
    6. Repeat, while count > 0
       1. Let value be GetValueFromBuffer(srcData, srcByteIndex, srcType, true, "Unordered").
       2. Perform SetValueInBuffer(data, targetByteIndex, elementType, value, true, "Unordered").
       3. Set srcByteIndex to srcByteIndex + srcElementSize.
       4. Set targetByteIndex to targetByteIndex + elementSize.
    7. Decrement count by 1.
20. Set O.[[ViewedArrayBuffer]] to data.
21. Set O.[[ByteLength]] to byteLength.
22. Set O.[[ByteOffset]] to 0.
23. Set O.[[ArrayLength]] to elementLength.
24. Return O.

#### 22.2.4.4 TypedArray ( object ) <div id="sec-typedarray-object"></div>

This description applies only if the TypedArray function is called with at least one argument and the Type of the first argument is Object and that object does not have either a [[TypedArrayName]] or an [[ArrayBufferData]] internal slot.

TypedArray called with argument object performs the following steps:

1. Assert: Type(object) is Object and object does not have either a [[TypedArrayName]] or an [[ArrayBufferData]] internal slot.
2. If NewTarget is undefined, throw a TypeError exception.
3. Let constructorName be the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
4. Let O be ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%").
5. Let usingIterator be ? GetMethod(object, @@iterator).
6. If usingIterator is not undefined, then
   1. Let values be ? IterableToList(object, usingIterator).
   2. Let len be the number of elements in values.
   3. Perform ? AllocateTypedArrayBuffer(O, len).
   4. Let k be 0.
   5. Repeat, while k < len
      1. Let Pk be ! ToString(k).
      2. Let kValue be the first element of values and remove that element from values.
      3. Perform ? Set(O, Pk, kValue, true).
      4. Increase k by 1.
   6. Assert: values is now an empty List.
   7. Return O.
7. NOTE: object is not an Iterable so assume it is already an array-like object.
8. Let arrayLike be object.
9. Let len be ? ToLength(? Get(arrayLike, "length")).
10. Perform ? AllocateTypedArrayBuffer(O, len).
11. Let k be 0.
12. Repeat, while k < len
    1. Let Pk be ! ToString(k).
    2. Let kValue be ? Get(arrayLike, Pk).
    3. Perform ? Set(O, Pk, kValue, true).
    4. Increase k by 1.
13. Return O.

#### 22.2.4.5 TypedArray ( buffer [ , byteOffset [ , length ] ] ) <div id="sec-typedarray-buffer-byteoffset-length"></div>

This description applies only if the TypedArray function is called with at least one argument and the Type of the first argument is Object and that object has an [[ArrayBufferData]] internal slot.

TypedArray called with at least one argument buffer performs the following steps:

1. Assert: Type(buffer) is Object and buffer has an [[ArrayBufferData]] internal slot.
2. If NewTarget is undefined, throw a TypeError exception.
3. Let constructorName be the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
4. Let O be ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%").
5. Let elementSize be the Number value of the Element Size value in Table 59 for constructorName.
6. Let offset be ? ToIndex(byteOffset).
7. If offset modulo elementSize ≠ 0, throw a RangeError exception.
8. If length is present and length is not undefined, then
   1. Let newLength be ? ToIndex(length).
9. If IsDetachedBuffer(buffer) is true, throw a TypeError exception.
10. Let bufferByteLength be buffer.[[ArrayBufferByteLength]].
11. If length is either not present or undefined, then
    1. If bufferByteLength modulo elementSize ≠ 0, throw a RangeError exception.
    2. Let newByteLength be bufferByteLength - offset.
    3. If newByteLength < 0, throw a RangeError exception.
12. Else,
    1. Let newByteLength be newLength × elementSize.
    2. If offset + newByteLength > bufferByteLength, throw a RangeError exception.
13. Set O.[[ViewedArrayBuffer]] to buffer.
14. Set O.[[ByteLength]] to newByteLength.
15. Set O.[[ByteOffset]] to offset.
16. Set O.[[ArrayLength]] to newByteLength / elementSize.
17. Return O.

#### 22.2.4.6 TypedArrayCreate ( constructor, argumentList ) <div id="typedarray-create"></div>

The abstract operation TypedArrayCreate with arguments constructor and argumentList is used to specify the creation of a new TypedArray object using a constructor function. It performs the following steps:

1. Let newTypedArray be ? Construct(constructor, argumentList).
2. Perform ? ValidateTypedArray(newTypedArray).
3. If argumentList is a List of a single Number, then
   1. If newTypedArray.[[ArrayLength]] < argumentList[0], throw a TypeError exception.
4. Return newTypedArray.

#### 22.2.4.7 TypedArraySpeciesCreate ( exemplar, argumentList ) <div id="typedarray-species-create"></div>

The abstract operation TypedArraySpeciesCreate with arguments exemplar and argumentList is used to specify the creation of a new TypedArray object using a constructor function that is derived from exemplar. It performs the following steps:

1. Assert: exemplar is an Object that has a [[TypedArrayName]] internal slot.
2. Let defaultConstructor be the intrinsic object listed in column one of Table 59 for exemplar.[[TypedArrayName]].
3. Let constructor be ? SpeciesConstructor(exemplar, defaultConstructor).
4. Return ? TypedArrayCreate(constructor, argumentList).

### 22.2.5 TypedArray 构造器属性 <div id="sec-properties-of-the-typedarray-constructors"></div>

Each TypedArray constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %TypedArray%.
- has a name property whose value is the String value of the constructor name specified for it in Table 59.
- has the following properties:

#### 22.2.5.1 TypedArray.BYTES_PER_ELEMENT <div id="sec-typedarray.bytes_per_element"></div>

The value of TypedArray.BYTES_PER_ELEMENT is the Number value of the Element Size value specified in Table 59 for TypedArray.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 22.2.5.2 TypedArray.prototype <div id="sec-typedarray.prototype"></div>

The initial value of TypedArray`.prototype` is the corresponding TypedArray prototype intrinsic object ([22.2.6](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-typedarray-prototype-objects)).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 22.2.6 TypedArray 原型对象属性 <div id="sec-properties-of-typedarray-prototype-objects"></div>

Each TypedArray prototype object:

- has a [[Prototype]] internal slot whose value is the intrinsic object %TypedArrayPrototype%.
- is an ordinary object.
- does not have a [[ViewedArrayBuffer]] or any other of the internal slots that are specific to TypedArray instance objects.

#### 22.2.6.1 TypedArray.prototype.BYTES_PER_ELEMENT <div id="sec-typedarray.prototype.bytes_per_element"></div>

The value of TypedArray.prototype.BYTES_PER_ELEMENT is the Number value of the Element Size value specified in Table 59 for TypedArray.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 22.2.6.2 TypedArray.prototype.constructor <div id="sec-typedarray.prototype.constructor"></div>

The initial value of a TypedArray.prototype.constructor is the corresponding %TypedArray% intrinsic object.

### 22.2.7 TypedArray 实例属性 <div id="sec-properties-of-typedarray-instances"></div>

TypedArray instances are [Integer-Indexed exotic objects](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-indexed-exotic-object). Each TypedArray instance inherits properties from the corresponding TypedArray prototype object. Each TypedArray instance has the following internal slots: [[TypedArrayName]], [[ViewedArrayBuffer]], [[ByteLength]], [[ByteOffset]], and [[ArrayLength]].