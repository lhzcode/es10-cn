# 23 带键的集合
## 23.1 Map 对象 <div id="sec-map-objects"></div>

Map objects are collections of key/value pairs where both the keys and values may be arbitrary ECMAScript language values. A distinct key value may only occur in one key/value pair within the Map's collection. Distinct key values are discriminated using the SameValueZero comparison algorithm.

Map object must be implemented using either hash tables or other mechanisms that, on average, provide access times that are sublinear on the number of elements in the collection. The data structures used in this Map objects specification is only intended to describe the required observable semantics of Map objects. It is not intended to be a viable implementation model.

### 23.1.1 Map 构造器 <div id="sec-map-constructor"></div>

The Map constructor:

- is the intrinsic object %Map%.
- is the initial value of the `Map` property of the [global object](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-global-object).
- creates and initializes a new Map object when called as a [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor).
- is not intended to be called as a function and will throw an exception when called in that manner.
- is designed to be subclassable. It may be used as the value in an `extends` clause of a class definition. Subclass constructors that intend to inherit the specified `Map` behaviour must include a `super` call to the `Map` [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) to create and initialize the subclass instance with the internal state necessary to support the `Map.prototype` built-in methods.

#### 23.1.1.1 Map ( [ iterable ] ) <div id="sec-map-iterable"></div>

When the `Map` function is called with optional argument iterable, the following steps are taken:

1. If NewTarget is undefined, throw a TypeError exception.
2. Let map be ? [OrdinaryCreateFromConstructor](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-ordinarycreatefromconstructor)(NewTarget, `"%MapPrototype%"`, « [[MapData]] »).
3. Set map.[[MapData]] to a new empty [List](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-list-and-record-specification-type).
4. If iterable is not present, or is either undefined or null, return map.
5. Let adder be ? [Get](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-get-o-p)(map, `"set"`).
6. Return ? [AddEntriesFromIterable](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-add-entries-from-iterable)(map, iterable, adder).

> NOTE
>
> If the parameter iterable is present, it is expected to be an object that implements an @@iterator method that returns an iterator object that produces a two element array-like object whose first element is a value that will be used as a Map key and whose second element is the value to associate with that key.

#### 23.1.1.2 AddEntriesFromIterable ( target, iterable, adder ) <div id="sec-add-entries-from-iterable"></div>

The abstract operation AddEntriesFromIterable accepts a target object, an iterable of entries, and an adder function to be invoked, with target as the receiver.

1. If IsCallable(adder) is false, throw a TypeError exception.
2. Assert: iterable is present, and is neither undefined nor null.
3. Let iteratorRecord be ? GetIterator(iterable).
4. Repeat,
   1. Let next be ? IteratorStep(iteratorRecord).
   2. If next is false, return target.
   3. Let nextItem be ? IteratorValue(next).
   4. If Type(nextItem) is not Object, then
      1. Let error be ThrowCompletion(a newly created TypeError object).
      2. Return ? IteratorClose(iteratorRecord, error).
   5. Let k be Get(nextItem, "0").
   6. If k is an abrupt completion, return ? IteratorClose(iteratorRecord, k).
   7. Let v be Get(nextItem, "1").
   8. If v is an abrupt completion, return ? IteratorClose(iteratorRecord, v).
   9. Let status be Call(adder, target, « k.[[Value]], v.[[Value]] »).
   10. If status is an abrupt completion, return ? IteratorClose(iteratorRecord, status).

> NOTE
>
> The parameter iterable is expected to be an object that implements an @@iterator method that returns an iterator object that produces a two element array-like object whose first element is a value that will be used as a Map key and whose second element is the value to associate with that key.

### 23.1.2 Map 构造器属性 <div id="sec-properties-of-the-map-constructor"></div>

The Map [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor):

- has a [[Prototype]] internal slot whose value is the intrinsic object [%FunctionPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-function-prototype-object).
- has the following properties:

#### 23.1.2.1 Map.prototype <div id="sec-map.prototype"></div>

The initial value of `Map.prototype` is the intrinsic object [%MapPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-map-prototype-object).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 23.1.2.2 获取 Map [ @@species ] <div id="sec-get-map-@@species"></div>

`Map[@@species]` is an [accessor property](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-object-type) whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Return the this value.

The value of the `name` property of this function is `"get [Symbol.species]"`.

> NOTE
>
> Methods that create derived collection objects should call @@species to determine the [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) to use to create the derived objects. Subclass [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) may over-ride @@species to change the default [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) assignment.

### 23.1.3 Map 原型对象属性 <div id="sec-properties-of-the-map-prototype-object"></div>

The Map prototype object:

- is the intrinsic object %MapPrototype%.
- has a [[Prototype]] internal slot whose value is the intrinsic object [%ObjectPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-object-prototype-object).
- is an ordinary object.
- does not have a [[MapData]] internal slot.

#### 23.1.3.1 Map.prototype.clear ( ) <div id="sec-map.prototype.clear"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[MapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[MapData]].
5. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
a. Set p.[[Key]] to empty.
b. Set p.[[Value]] to empty.
6. Return undefined.

> NOTE
>
> The existing [[MapData]] List is preserved because there may be existing Map Iterator objects that are suspended midway through iterating over that List.

#### 23.1.3.2 Map.prototype.constructor <div id="sec-map.prototype.constructor"></div>

The initial value of `Map.prototype.constructor` is the intrinsic object [%Map%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-map-constructor).

#### 23.1.3.3 Map.prototype.delete ( key ) <div id="sec-map.prototype.delete"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[MapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[MapData]].
5. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty and SameValueZero(p.[[Key]], key) is true, then
      1. Set p.[[Key]] to empty.
      2. Set p.[[Value]] to empty.
      3. Return true.
6. Return false.

> NOTE
>
> The value empty is used as a specification device to indicate that an entry has been deleted. Actual implementations may take other actions such as physically removing the entry from internal data structures.

#### 23.1.3.4 Map.prototype.entries ( ) <div id="sec-map.prototype.entries"></div>

The following steps are taken:

1. Let M be the this value.
2. Return ? CreateMapIterator(M, "key+value").

#### 23.1.3.5 Map.prototype.forEach ( callbackfn [ , thisArg ] ) <div id="sec-map.prototype.foreach"></div>

When the `forEach` method is called with one or two arguments, the following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[MapData]] internal slot, throw a TypeError exception.
4. If IsCallable(callbackfn) is false, throw a TypeError exception.
5. If thisArg is present, let T be thisArg; else let T be undefined.
6. Let entries be the List that is M.[[MapData]].
7. For each Record { [[Key]], [[Value]] } e that is an element of entries, in original key insertion order, do
   1. If e.[[Key]] is not empty, then
      1. Perform ? Call(callbackfn, T, « e.[[Value]], e.[[Key]], M »).
8. Return undefined.

> NOTE
>
> callbackfn should be a function that accepts three arguments. `forEach` calls callbackfn once for each key/value pair present in the map object, in key insertion order. callbackfn is called only for keys of the map which actually exist; it is not called for keys that have been deleted from the map.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of callbackfn. If it is not provided, undefined is used instead.
>
> callbackfn is called with three arguments: the value of the item, the key of the item, and the Map object being traversed.
>
> `forEach` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn. Each entry of a map's [[MapData]] is only visited once. New keys added after the call to `forEach` begins are visited. A key will be revisited if it is deleted after it has been visited and then re-added before the `forEach` call completes. Keys that are deleted after the call to `forEach` begins and before being visited are not visited unless the key is added again before the `forEach` call completes.

#### 23.1.3.6 Map.prototype.get ( key ) <div id="sec-map.prototype.get"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[MapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[MapData]].
5. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty and SameValueZero(p.[[Key]], key) is true, return p.[[Value]].
6. Return undefined.

#### 23.1.3.7 Map.prototype.has ( key ) <div id="sec-map.prototype.has"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[MapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[MapData]].
5. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty and SameValueZero(p.[[Key]], key) is true, return true.
6. Return false

#### 23.1.3.8 Map.prototype.keys ( ) <div id="sec-map.prototype.keys"></div>

The following steps are taken:

1. Let M be the this value.
2. Return ? CreateMapIterator(M, "key").

#### 23.1.3.9 Map.prototype.set ( key, value ) <div id="sec-map.prototype.set"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[MapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[MapData]].
5. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty and SameValueZero(p.[[Key]], key) is true, then
      1. Set p.[[Value]] to value.
      2. Return M.
6. If key is -0, set key to +0.
7. Let p be the Record { [[Key]]: key, [[Value]]: value }.
8. Append p as the last element of entries.
9. Return M.

#### 23.1.3.10 获取 Map.prototype.size <div id="sec-get-map.prototype.size"></div>

Map.prototype.size is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[MapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[MapData]].
5. Let count be 0.
6. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty, increase count by 1.
7. Return count.

#### 23.1.3.11 Map.prototype.values ( ) <div id="sec-map.prototype.values"></div>

The following steps are taken:

1. Let M be the this value.
2. Return ? CreateMapIterator(M, "value").

#### 23.1.3.12 Map.prototype [ @@iterator ] ( ) <div id="sec-map.prototype-@@iterator"></div>

The initial value of the @@iterator property is the same [function object](http://www.ecma-international.org/ecma-262/10.0/index.html#function-object) as the initial value of the `entries` property.

#### 23.1.3.13 Map.prototype [ @@toStringTag ] <div id="sec-map.prototype-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value "Map".

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

### 23.1.4 Map 实例属性 <div id="sec-properties-of-map-instances"></div>

Map instances are ordinary objects that inherit properties from the Map prototype. Map instances also have a [[MapData]] internal slot.

### 23.1.5 Map 迭代器对象 <div id="sec-map-iterator-objects"></div>

A Map Iterator is an object, that represents a specific iteration over some specific Map instance object. There is not a named constructor for Map Iterator objects. Instead, map iterator objects are created by calling certain methods of Map instance objects.

#### 23.1.5.1 CreateMapIterator ( map, kind ) <div id="sec-createmapiterator"></div>

Several methods of Map objects return Iterator objects. The abstract operation CreateMapIterator with arguments map and kind is used to create such iterator objects. It performs the following steps:

1. If Type(map) is not Object, throw a TypeError exception.
2. If map does not have a [[MapData]] internal slot, throw a TypeError exception.
3. Let iterator be ObjectCreate(%MapIteratorPrototype%, « [[Map]], [[MapNextIndex]], [[MapIterationKind]] »).
4. Set iterator.[[Map]] to map.
5. Set iterator.[[MapNextIndex]] to 0.
6. Set iterator.[[MapIterationKind]] to kind.
7. Return iterator.

#### 23.1.5.2 The %MapIteratorPrototype% Object <div id="sec-%mapiteratorprototype%-object"></div>

The %MapIteratorPrototype% object:

- has properties that are inherited by all Map Iterator Objects.
- is an ordinary object.
- has a [[Prototype]] internal slot whose value is the intrinsic object %IteratorPrototype%.
- has the following properties:

##### 23.1.5.2.1 %MapIteratorPrototype%.next ( ) <div id="sec-%mapiteratorprototype%.next"></div>

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have all of the internal slots of a Map Iterator Instance (23.1.5.3), throw a TypeError exception.
4. Let m be O.[[Map]].
5. Let index be O.[[MapNextIndex]].
6. Let itemKind be O.[[MapIterationKind]].
7. If m is undefined, return CreateIterResultObject(undefined, true).
8. Assert: m has a [[MapData]] internal slot.
9. Let entries be the List that is m.[[MapData]].
10. Let numEntries be the number of elements of entries.
11. NOTE: numEntries must be redetermined each time this method is evaluated.
12. Repeat, while index is less than numEntries,
    1. Let e be the Record { [[Key]], [[Value]] } that is the value of entries[index].
    2. Increase index by 1.
    3. Set O.[[MapNextIndex]] to index.
    4. If e.[[Key]] is not empty, then
       1. If itemKind is "key", let result be e.[[Key]].
       2. Else if itemKind is "value", let result be e.[[Value]].
       3. Else,
          1. Assert: itemKind is "key+value".
          2. Let result be CreateArrayFromList(« e.[[Key]], e.[[Value]] »).
       4. Return CreateIterResultObject(result, false).
13. Set O.[[Map]] to undefined.
14. Return CreateIterResultObject(undefined, true).

##### 23.1.5.2.2 %MapIteratorPrototype% [ @@toStringTag ] <div id="sec-%mapiteratorprototype%-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value "Map Iterator".

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 23.1.5.3 Map 迭代器实例属性 <div id="sec-properties-of-map-iterator-instances"></div>

Map Iterator instances are ordinary objects that inherit properties from the %MapIteratorPrototype% intrinsic object. Map Iterator instances are initially created with the internal slots described in Table 60.

Table 60: Internal Slots of Map Iterator Instances

| Internal Slot        | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| [[Map]]              | The Map object that is being iterated.                       |
| [[MapNextIndex]]     | The [integer index](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) of the next Map data element to be examined by this iterator. |
| [[MapIterationKind]] | A String value that identifies what is to be returned for each element of the iteration. The possible values are: `"key"`, `"value"`, `"key+value"`. |

## 23.2 Set 对象 <div id="sec-set-objects"></div>

Set objects are collections of ECMAScript language values. A distinct value may only occur once as an element of a Set's collection. Distinct values are discriminated using the [SameValueZero](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-samevaluezero) comparison algorithm.

Set objects must be implemented using either hash tables or other mechanisms that, on average, provide access times that are sublinear on the number of elements in the collection. The data structures used in this Set objects specification is only intended to describe the required observable semantics of Set objects. It is not intended to be a viable implementation model.

### 23.2.1 Set 构造器 <div id="sec-set-constructor"></div>

The Set [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor):

- is the intrinsic object %Set%.
- is the initial value of the Set property of the global object.
- creates and initializes a new Set object when called as a constructor.
- is not intended to be called as a function and will throw an exception when called in that manner.
- is designed to be subclassable. It may be used as the value in an extends clause of a class definition. Subclass constructors that intend to inherit the specified Set behaviour must include a super call to the Set constructor to create and initialize the subclass instance with the internal state necessary to support the Set.prototype built-in methods.

#### 23.2.1.1 Set ( [ iterable ] ) <div id="sec-set-iterable"></div>

When the Set function is called with optional argument iterable, the following steps are taken:

1. If NewTarget is undefined, throw a TypeError exception.
2. Let set be ? OrdinaryCreateFromConstructor(NewTarget, "%SetPrototype%", « [[SetData]] »).
3. Set set.[[SetData]] to a new empty List.
4. If iterable is not present, set iterable to undefined.
5. If iterable is either undefined or null, return set.
6. Let adder be ? Get(set, "add").
7. If IsCallable(adder) is false, throw a TypeError exception.
8. Let iteratorRecord be ? GetIterator(iterable).
9. Repeat,
   1. Let next be ? IteratorStep(iteratorRecord).
   2. If next is false, return set.
   3. Let nextValue be ? IteratorValue(next).
   4. Let status be Call(adder, set, « nextValue »).
   5. If status is an abrupt completion, return ? IteratorClose(iteratorRecord, status).

### 23.2.2 Set 构造器属性 <div id="sec-properties-of-the-set-constructor"></div>

The Set constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 23.2.2.1 Set.prototype <div id="sec-set.prototype"></div>

The initial value of `Set.prototype` is the intrinsic [%SetPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-set-prototype-object) object.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 23.2.2.2 获取 Set [ @@species ] <div id="sec-get-set-@@species"></div>

Set[@@species] is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Return the this value.

The value of the `name` property of this function is `"get [Symbol.species]"`.

> NOTE
>
> Methods that create derived collection objects should call @@species to determine the [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) to use to create the derived objects. Subclass [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) may over-ride @@species to change the default [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) assignment.

### 23.2.3 Set 原型对象属性 <div id="sec-properties-of-the-set-prototype-object"></div>

The Set prototype object:

- is the intrinsic object %SetPrototype%.
- has a [[Prototype]] internal slot whose value is the intrinsic object [%ObjectPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-object-prototype-object).
- is an ordinary object.
- does not have a [[SetData]] internal slot.

#### 23.2.3.1 Set.prototype.add ( value ) <div id="sec-set.prototype.add"></div>

The following steps are taken:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[SetData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is S.[[SetData]].
5. For each e that is an element of entries, do
   1. If e is not empty and SameValueZero(e, value) is true, then
      1. Return S.
6. If value is -0, set value to +0.
7. Append value as the last element of entries.
8. Return S.

#### 23.2.3.2 Set.prototype.clear ( ) <div id="sec-set.prototype.clear"></div>

The following steps are taken:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[SetData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is S.[[SetData]].
5. For each e that is an element of entries, do
   1. Replace the element of entries whose value is e with an element whose value is empty.
6. Return undefined.

> NOTE
>
> The existing [[SetData]] [List](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-list-and-record-specification-type) is preserved because there may be existing Set Iterator objects that are suspended midway through iterating over that [List](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-list-and-record-specification-type).

#### 23.2.3.3 Set.prototype.constructor <div id="sec-set.prototype.constructor"></div>

The initial value of `Set.prototype.constructor` is the intrinsic object [%Set%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-set-constructor).

#### 23.2.3.4 Set.prototype.delete ( value ) <div id="sec-set.prototype.delete"></div>

The following steps are taken:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[SetData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is S.[[SetData]].
5. For each e that is an element of entries, do
   1. If e is not empty and SameValueZero(e, value) is true, then
      1. Replace the element of entries whose value is e with an element whose value is empty.
      2. Return true.
6. Return false.

> NOTE
>
> The value empty is used as a specification device to indicate that an entry has been deleted. Actual implementations may take other actions such as physically removing the entry from internal data structures.

#### 23.2.3.5 Set.prototype.entries ( ) <div id="sec-set.prototype.entries"></div>

The following steps are taken:

1. Let S be the this value.
2. Return ? CreateSetIterator(S, "key+value").

> NOTE
>
> For iteration purposes, a Set appears similar to a Map where each entry has the same value for its key and value.

#### 23.2.3.6 Set.prototype.forEach ( callbackfn [ , thisArg ] ) <div id="sec-set.prototype.foreach"></div>

When the `forEach` method is called with one or two arguments, the following steps are taken:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[SetData]] internal slot, throw a TypeError exception.
4. If IsCallable(callbackfn) is false, throw a TypeError exception.
5. If thisArg is present, let T be thisArg; else let T be undefined.
6. Let entries be the List that is S.[[SetData]].
7. For each e that is an element of entries, in original insertion order, do
   1. If e is not empty, then
      1. Perform ? Call(callbackfn, T, « e, e, S »).
8. Return undefined.

> NOTE
>
> callbackfn should be a function that accepts three arguments. `forEach` calls callbackfn once for each value present in the set object, in value insertion order. callbackfn is called only for values of the Set which actually exist; it is not called for keys that have been deleted from the set.
>
> If a thisArg parameter is provided, it will be used as the this value for each invocation of callbackfn. If it is not provided, undefined is used instead.
>
> callbackfn is called with three arguments: the first two arguments are a value contained in the Set. The same value is passed for both arguments. The Set object being traversed is passed as the third argument.
>
> The callbackfn is called with three arguments to be consistent with the call back functions used by `forEach` methods for Map and Array. For Sets, each item value is considered to be both the key and the value.
>
> `forEach` does not directly mutate the object on which it is called but the object may be mutated by the calls to callbackfn.
>
> Each value is normally visited only once. However, a value will be revisited if it is deleted after it has been visited and then re-added before the `forEach` call completes. Values that are deleted after the call to `forEach` begins and before being visited are not visited unless the value is added again before the `forEach` call completes. New values added after the call to `forEach` begins are visited.

#### 23.2.3.7 Set.prototype.has ( value ) <div id="sec-set.prototype.has"></div>

The following steps are taken:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[SetData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is S.[[SetData]].
5. For each e that is an element of entries, do
   1. If e is not empty and SameValueZero(e, value) is true, return true.
6. Return false.

#### 23.2.3.8 Set.prototype.keys ( ) <div id="sec-set.prototype.keys"></div>

The initial value of the `keys` property is the same [function object](http://www.ecma-international.org/ecma-262/10.0/index.html#function-object) as the initial value of the `values` property.

> NOTE
>
> For iteration purposes, a Set appears similar to a Map where each entry has the same value for its key and value.

#### 23.2.3.9 获取 Set.prototype.size <div id="sec-get-set.prototype.size"></div>

`Set.prototype.size` is an [accessor property](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-object-type) whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[SetData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is S.[[SetData]].
5. Let count be 0.
6. For each e that is an element of entries, do
   1. If e is not empty, increase count by 1.
7. Return count.

#### 23.2.3.10 Set.prototype.values ( ) <div id="sec-set.prototype.values"></div>

The following steps are taken:

1. Let S be the this value.
2. Return ? CreateSetIterator(S, "value").

#### 23.2.3.11 Set.prototype [ @@iterator ] ( ) <div id="sec-set.prototype-@@iterator"></div>

The initial value of the @@iterator property is the same [function object](http://www.ecma-international.org/ecma-262/10.0/index.html#function-object) as the initial value of the `values` property.

#### 23.2.3.12 Set.prototype [ @@toStringTag ] <div id="sec-set.prototype-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value `"Set"`.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

### 23.2.4 Set 实例属性 <div id="sec-properties-of-set-instances"></div>

Set instances are ordinary objects that inherit properties from the Set prototype. Set instances also have a [[SetData]] internal slot.

### 23.2.5 Set 实例对象 <div id="sec-set-iterator-objects"></div>

A Set Iterator is an ordinary object, with the structure defined below, that represents a specific iteration over some specific Set instance object. There is not a named [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) for Set Iterator objects. Instead, set iterator objects are created by calling certain methods of Set instance objects.

#### 23.2.5.1 CreateSetIterator ( set, kind ) <div id="sec-createsetiterator"></div>

Several methods of Set objects return Iterator objects. The abstract operation CreateSetIterator with arguments set and kind is used to create such iterator objects. It performs the following steps:

1. If Type(set) is not Object, throw a TypeError exception.
2. If set does not have a [[SetData]] internal slot, throw a TypeError exception.
3. Let iterator be ObjectCreate(%SetIteratorPrototype%, « [[IteratedSet]], [[SetNextIndex]], [[SetIterationKind]] »).
4. Set iterator.[[IteratedSet]] to set.
5. Set iterator.[[SetNextIndex]] to 0.
6. Set iterator.[[SetIterationKind]] to kind.
7. Return iterator.

#### 23.2.5.2 %SetIteratorPrototype% 对象 <div id="sec-%setiteratorprototype%-object"></div>

The %SetIteratorPrototype% object:

- has properties that are inherited by all Set Iterator Objects.
- is an ordinary object.
- has a [[Prototype]] internal slot whose value is the intrinsic object [%IteratorPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%iteratorprototype%-object).
- has the following properties:

##### 23.2.5.2.1 %SetIteratorPrototype%.next ( ) <div id="sec-%setiteratorprototype%.next"></div>

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. If O does not have all of the internal slots of a Set Iterator Instance (23.2.5.3), throw a TypeError exception.
4. Let s be O.[[IteratedSet]].
5. Let index be O.[[SetNextIndex]].
6. Let itemKind be O.[[SetIterationKind]].
7. If s is undefined, return CreateIterResultObject(undefined, true).
8. Assert: s has a [[SetData]] internal slot.
9. Let entries be the List that is s.[[SetData]].
10. Let numEntries be the number of elements of entries.
11. NOTE: numEntries must be redetermined each time this method is evaluated.
12. Repeat, while index is less than numEntries,
    1. Let e be entries[index].
    2. Increase index by 1.
    3. Set O.[[SetNextIndex]] to index.
    4. If e is not empty, then
       1. If itemKind is "key+value", then
          1. Return CreateIterResultObject(CreateArrayFromList(« e, e »), false).
       2. Return CreateIterResultObject(e, false).
13. Set O.[[IteratedSet]] to undefined.
14. Return CreateIterResultObject(undefined, true).

##### 23.2.5.2.2 %SetIteratorPrototype% [ @@toStringTag ] <div id="sec-%setiteratorprototype%-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value `"Set Iterator"`.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 23.2.5.3 Set 迭代器对象属性 <div id="sec-properties-of-set-iterator-instances"></div>

Set Iterator instances are ordinary objects that inherit properties from the [%SetIteratorPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-%setiteratorprototype%-object) intrinsic object. Set Iterator instances are initially created with the internal slots specified in [Table 61](http://www.ecma-international.org/ecma-262/10.0/index.html#table-51).

Table 61: Internal Slots of Set Iterator Instances

| Internal Slot        | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| [[IteratedSet]]      | The Set object that is being iterated.                       |
| [[SetNextIndex]]     | The [integer index](http://www.ecma-international.org/ecma-262/10.0/index.html#integer-index) of the next Set data element to be examined by this iterator |
| [[SetIterationKind]] | A String value that identifies what is to be returned for each element of the iteration. The possible values are: `"key"`, `"value"`, `"key+value"`. `"key"` and `"value"` have the same meaning. |

## 23.3 WeakMap 对象 <div id="sec-weakmap-objects"></div>

WeakMap objects are collections of key/value pairs where the keys are objects and values may be arbitrary ECMAScript language values. A WeakMap may be queried to see if it contains a key/value pair with a specific key, but no mechanism is provided for enumerating the objects it holds as keys. If an object that is being used as the key of a WeakMap key/value pair is only reachable by following a chain of references that start within that WeakMap, then that key/value pair is inaccessible and is automatically removed from the WeakMap. WeakMap implementations must detect and remove such key/value pairs and any associated resources.

An implementation may impose an arbitrarily determined latency between the time a key/value pair of a WeakMap becomes inaccessible and the time when the key/value pair is removed from the WeakMap. If this latency was observable to ECMAScript program, it would be a source of indeterminacy that could impact program execution. For that reason, an ECMAScript implementation must not provide any means to observe a key of a WeakMap that does not require the observer to present the observed key.

WeakMap objects must be implemented using either hash tables or other mechanisms that, on average, provide access times that are sublinear on the number of key/value pairs in the collection. The data structure used in this WeakMap objects specification are only intended to describe the required observable semantics of WeakMap objects. It is not intended to be a viable implementation model.

> NOTE
>
> 
> WeakMap and WeakSets are intended to provide mechanisms for dynamically associating state with an object in a manner that does not “leak” memory resources if, in the absence of the WeakMap or WeakSet, the object otherwise became inaccessible and subject to resource reclamation by the implementation's garbage collection mechanisms. This characteristic can be achieved by using an inverted per-object mapping of weak map instances to keys. Alternatively each weak map may internally store its key to value mappings but this approach requires coordination between the WeakMap or WeakSet implementation and the garbage collector. The following references describe mechanism that may be useful to implementations of WeakMap and WeakSets:
>
> Barry Hayes. 1997. Ephemerons: a new finalization mechanism. In *Proceedings of the 12th ACM SIGPLAN conference on Object-oriented programming, systems, languages, and applications (OOPSLA '97)*, A. Michael Berman (Ed.). ACM, New York, NY, USA, 176-183, http://doi.acm.org/10.1145/263698.263733.
>
> Alexandra Barros, Roberto Ierusalimschy, Eliminating Cycles in Weak Tables. Journal of Universal Computer Science - J.UCS, vol. 14, no. 21, pp. 3481-3497, 2008, http://www.jucs.org/jucs_14_21/eliminating_cycles_in_weak

### 23.3.1 WeakMap 构造器 <div id="sec-weakmap-constructor"></div>

The WeakMap [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor):

- is the intrinsic object %WeakMap%.
- is the initial value of the `WeakMap` property of the [global object](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-global-object).
- creates and initializes a new WeakMap object when called as a [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor).
- is not intended to be called as a function and will throw an exception when called in that manner.
- is designed to be subclassable. It may be used as the value in an `extends` clause of a class definition. Subclass constructors that intend to inherit the specified `WeakMap` behaviour must include a `super` call to the `WeakMap` [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) to create and initialize the subclass instance with the internal state necessary to support the `WeakMap.prototype` built-in methods.

#### 23.3.1.1 WeakMap ( [ iterable ] ) <div id="sec-weakmap-iterable"></div>

When the WeakMap function is called with optional argument iterable, the following steps are taken:

1. If NewTarget is undefined, throw a TypeError exception.
2. Let map be ? OrdinaryCreateFromConstructor(NewTarget, "%WeakMapPrototype%", « [[WeakMapData]] »).
3. Set map.[[WeakMapData]] to a new empty List.
4. If iterable is not present, or is either undefined or null, return map.
5. Let adder be ? Get(map, "set").
6. Return ? AddEntriesFromIterable(map, iterable, adder).

> NOTE
>
> If the parameter iterable is present, it is expected to be an object that implements an @@iterator method that returns an iterator object that produces a two element array-like object whose first element is a value that will be used as a WeakMap key and whose second element is the value to associate with that key.

### 23.3.2 WeakMap 构造器属性 <div id="sec-properties-of-the-weakmap-constructor"></div>

The WeakMap [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor):

- has a [[Prototype]] internal slot whose value is the intrinsic object [%FunctionPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-function-prototype-object).
- has the following properties:

#### 23.3.2.1 WeakMap.prototype <div id="sec-weakmap.prototype"></div>

The initial value of `WeakMap.prototype` is the intrinsic object [%WeakMapPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-weakmap-prototype-object).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 23.3.3 WeakMap 原型对象属性 <div id="sec-properties-of-the-weakmap-prototype-object"></div>

The WeakMap prototype object:

- is the intrinsic object %WeakMapPrototype%.
- has a [[Prototype]] internal slot whose value is the intrinsic object [%ObjectPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-object-prototype-object).
- is an ordinary object.
- does not have a [[WeakMapData]] internal slot.

#### 23.3.3.1 WeakMap.prototype.constructor <div id="sec-weakmap.prototype.constructor"></div>

The initial value of `WeakMap.prototype.constructor` is the intrinsic object [%WeakMap%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-weakmap-constructor).

#### 23.3.3.2 WeakMap.prototype.delete ( key ) <div id="sec-weakmap.prototype.delete"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[WeakMapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[WeakMapData]].
5. If Type(key) is not Object, return false.
6. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty and SameValue(p.[[Key]], key) is true, then
      1. Set p.[[Key]] to empty.
      2. Set p.[[Value]] to empty.
      3. Return true.
7. Return false.

> NOTE
>
> The value empty is used as a specification device to indicate that an entry has been deleted. Actual implementations may take other actions such as physically removing the entry from internal data structures.

#### 23.3.3.3 WeakMap.prototype.get ( key ) <div id="sec-weakmap.prototype.get"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[WeakMapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[WeakMapData]].
5. If Type(key) is not Object, return undefined.
6. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty and SameValue(p.[[Key]], key) is true, return p.[[Value]].
7. Return undefined.

#### 23.3.3.4 WeakMap.prototype.has ( key ) <div id="sec-weakmap.prototype.has"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[WeakMapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[WeakMapData]].
5. If Type(key) is not Object, return false.
6. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty and SameValue(p.[[Key]], key) is true, return true.
7. Return false.

#### 23.3.3.5 WeakMap.prototype.set ( key, value ) <div id="sec-weakmap.prototype.set"></div>

The following steps are taken:

1. Let M be the this value.
2. If Type(M) is not Object, throw a TypeError exception.
3. If M does not have a [[WeakMapData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is M.[[WeakMapData]].
5. If Type(key) is not Object, throw a TypeError exception.
6. For each Record { [[Key]], [[Value]] } p that is an element of entries, do
   1. If p.[[Key]] is not empty and SameValue(p.[[Key]], key) is true, then
      1. Set p.[[Value]] to value.
      2. Return M.
7. Let p be the Record { [[Key]]: key, [[Value]]: value }.
8. Append p as the last element of entries.
9. Return M.

#### 23.3.3.6 WeakMap.prototype [ @@toStringTag ] <div id="sec-weakmap.prototype-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value `"WeakMap"`.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

### 23.3.4 WeakMap 实例属性 <div id="sec-properties-of-weakmap-instances"></div>

WeakMap instances are ordinary objects that inherit properties from the WeakMap prototype. WeakMap instances also have a [[WeakMapData]] internal slot.

## 23.4 WeakSet 对象 <div id="sec-weakset-objects"></div>

WeakSet objects are collections of objects. A distinct object may only occur once as an element of a WeakSet's collection. A WeakSet may be queried to see if it contains a specific object, but no mechanism is provided for enumerating the objects it holds. If an object that is contained by a WeakSet is only reachable by following a chain of references that start within that WeakSet, then that object is inaccessible and is automatically removed from the WeakSet. WeakSet implementations must detect and remove such objects and any associated resources.

An implementation may impose an arbitrarily determined latency between the time an object contained in a WeakSet becomes inaccessible and the time when the object is removed from the WeakSet. If this latency was observable to ECMAScript program, it would be a source of indeterminacy that could impact program execution. For that reason, an ECMAScript implementation must not provide any means to determine if a WeakSet contains a particular object that does not require the observer to present the observed object.

WeakSet objects must be implemented using either hash tables or other mechanisms that, on average, provide access times that are sublinear on the number of elements in the collection. The data structure used in this WeakSet objects specification is only intended to describe the required observable semantics of WeakSet objects. It is not intended to be a viable implementation model.

> NOTE
>
> See the NOTE in [23.3](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-weakmap-objects).

### 23.4.1 WeakSet 构造器 <div id="sec-weakset-constructor"></div>

The WeakSet [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor):

- is the intrinsic object %WeakSet%.
- is the initial value of the `WeakSet` property of the [global object](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-global-object).
- creates and initializes a new WeakSet object when called as a [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor).
- is not intended to be called as a function and will throw an exception when called in that manner.
- is designed to be subclassable. It may be used as the value in an `extends` clause of a class definition. Subclass constructors that intend to inherit the specified `WeakSet` behaviour must include a `super` call to the `WeakSet` [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor) to create and initialize the subclass instance with the internal state necessary to support the `WeakSet.prototype` built-in methods.

#### 23.4.1.1 WeakSet ( [ iterable ] ) <div id="sec-weakset-iterable"></div>

When the WeakSet function is called with optional argument iterable, the following steps are taken:

1. If NewTarget is undefined, throw a TypeError exception.
2. Let set be ? OrdinaryCreateFromConstructor(NewTarget, "%WeakSetPrototype%", « [[WeakSetData]] »).
3. Set set.[[WeakSetData]] to a new empty List.
4. If iterable is not present, set iterable to undefined.
5. If iterable is either undefined or null, return set.
6. Let adder be ? Get(set, "add").
7. If IsCallable(adder) is false, throw a TypeError exception.
8. Let iteratorRecord be ? GetIterator(iterable).
9. Repeat,
   1. Let next be ? IteratorStep(iteratorRecord).
   2. If next is false, return set.
   3. Let nextValue be ? IteratorValue(next).
   4. Let status be Call(adder, set, « nextValue »).
   5. If status is an abrupt completion, return ? IteratorClose(iteratorRecord, status).

### 23.4.2 WeakSet 构造器属性 <div id="sec-properties-of-the-weakset-constructor"></div>

The WeakSet [constructor](http://www.ecma-international.org/ecma-262/10.0/index.html#constructor):

- has a [[Prototype]] internal slot whose value is the intrinsic object [%FunctionPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-function-prototype-object).
- has the following properties:

#### 23.4.2.1 WeakSet.prototype <div id="sec-weakset.prototype"></div>

The initial value of `WeakSet.prototype` is the intrinsic [%WeakSetPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-weakset-prototype-object) object.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 23.4.3 WeakSet 原型对象属性 <div id="sec-properties-of-the-weakset-prototype-object"></div>

The WeakSet prototype object:

- is the intrinsic object %WeakSetPrototype%.
- has a [[Prototype]] internal slot whose value is the intrinsic object [%ObjectPrototype%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-properties-of-the-object-prototype-object).
- is an ordinary object.
- does not have a [[WeakSetData]] internal slot.

#### 23.4.3.1 WeakSet.prototype.add ( value ) <div id="sec-weakset.prototype.add"></div>

The following steps are taken:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[WeakSetData]] internal slot, throw a TypeError exception.
4. If Type(value) is not Object, throw a TypeError exception.
5. Let entries be the List that is S.[[WeakSetData]].
6. For each e that is an element of entries, do
   1. If e is not empty and SameValue(e, value) is true, then
      1. Return S.
7. Append value as the last element of entries.
8. Return S.

#### 23.4.3.2 WeakSet.prototype.constructor <div id="sec-weakset.prototype.constructor"></div>

The initial value of `WeakSet.prototype.constructor` is the [%WeakSet%](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-weakset-constructor) intrinsic object.

#### 23.4.3.3 WeakSet.prototype.delete ( value ) <div id="sec-weakset.prototype.delete"></div>

The following steps are taken:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[WeakSetData]] internal slot, throw a TypeError exception.
4. If Type(value) is not Object, return false.
5. Let entries be the List that is S.[[WeakSetData]].
6. For each e that is an element of entries, do
   1. If e is not empty and SameValue(e, value) is true, then
      1. Replace the element of entries whose value is e with an element whose value is empty.
      2. Return true.
7. Return false.

> NOTE
>
> The value empty is used as a specification device to indicate that an entry has been deleted. Actual implementations may take other actions such as physically removing the entry from internal data structures.

#### 23.4.3.4 WeakSet.prototype.has ( value ) <div id="sec-weakset.prototype.has"></div>

The following steps are taken:

1. Let S be the this value.
2. If Type(S) is not Object, throw a TypeError exception.
3. If S does not have a [[WeakSetData]] internal slot, throw a TypeError exception.
4. Let entries be the List that is S.[[WeakSetData]].
5. If Type(value) is not Object, return false.
6. For each e that is an element of entries, do
   1. If e is not empty and SameValue(e, value) is true, return true.
7. Return false.

#### 23.4.3.5 WeakSet.prototype [ @@toStringTag] <div id="sec-weakset.prototype-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value `"WeakSet"`.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

### 23.4.4 WeakSet 实例属性 <div id="sec-properties-of-weakset-instances"></div>

WeakSet instances are ordinary objects that inherit properties from the WeakSet prototype. WeakSet instances also have a [[WeakSetData]] internal slot.