# 22 索引集合
## 22.1 数组对象 <div id="sec-array-objects"></div>

数组对象是异类对象，可以对某些类的属性名称进行特殊处理。有关特殊处理的定义，请参见9.4.2。

### 22.1.1 Array 构造器 <div id="sec-array-constructor"></div>

数组构造函数：

- 是固有对象％Array％。
- 是全局对象的Array属性的初始值。
-  在作为构造函数调用时，创建并初始化一个新的Array奇异对象。
- 当作为函数而不是构造函数调用时，还创建并初始化一个新的Array对象。因此，函数调用“ Array（…）”等效于具有相同参数的对象创建表达式“ new Array（…）”。
- 是一个函数，其行为根据其参数的数量和类型而重载。
- 设计为可归类的。它可以用作类定义的`extends'子句的值。打算继承奇异的Array行为的子类构造函数必须包括对Array构造函数的super调用，以初始化作为Array奇异对象的子类实例。但是，大多数Array.prototype方法是通用方法，并不依赖于this值是Array外来对象。
- 具有“ length”属性，其值为1。

#### 22.1.1.1 Array ( ) <div id="sec-array-constructor-array"></div>

 仅当不带参数调用Array构造函数时，此描述才适用。

1. 令 numberOfArgs 为传递给此函数调用的参数数量。
2. 断言：numberOfArgs = 0.
3. 若 NewTarget 是 undefined, 令 newTarget 为活动函数对象，否则令 newTarget 为 NewTarget。
4. 令 proto 为 ? GetPrototypeFromConstructor(newTarget, `"%ArrayPrototype%"`).
5. 返回 ! ArrayCreate(0, proto).

#### 22.1.1.2 Array ( len ) <div id="sec-array-len"></div>

仅当仅使用一个参数调用Array构造函数时，此描述才适用。

1. 令 numberOfArgs 为传递给此函数调用的参数数量。
2. 断言：numberOfArgs = 1.
3. 若 NewTarget 是 undefined, 令 newTarget 为活动函数对象，否则令 newTarget 为 NewTarget。
4. 令 proto 为 ? GetPrototypeFromConstructor(newTarget, `"%ArrayPrototype%"`).
5. 令 array 为 ! ArrayCreate(0, proto).
6. 若 Type (len) 不是 Number，那么
   1. 令 defineStatus 为 CreateDataProperty(array, `"0"`, len).
   2. 断言：defineStatus 是 true.
   3. 令 intLen 为 1.
7. 否则，
   1. 令 intLen 为 ToUint32(len).
   2. 若 intLen ≠ len, 抛出 RangeError 异常
8. 执行 ! Set(array, `"length"`, intLen, true).
9. 返回 array.

#### 22.1.1.3 Array ( ...items ) <div id="sec-array-items"></div>

仅当使用至少两个参数调用Array构造函数时，此描述才适用。

调用Array函数时，将执行以下步骤：

1. 令 numberOfArgs 为传递给此函数调用的参数数量。
2. 断言：numberOfArgs ≥ 2.
3. 若 NewTarget 是 undefined，令 newTarget 为活动函数对象，否则令 newTarget 为 NewTarget。
4. 令 proto 为 ? GetPrototypeFromConstructor(newTarget, `"%ArrayPrototype%"`).
5. 令 array 为 ? ArrayCreate(numberOfArgs, proto).
6. 令 k 为 0.
7. 令 items 为从零开始的列表，按顺序包含自变量项。
8. 重复，直到 k < numberOfArgs
   1. 令 Pk 为 ! ToString(k).
   2. 令 itemK 为 items[k].
   3. 令 defineStatus 为 CreateDataProperty(array, Pk, itemK).
   4. 断言：defineStatus 是 true.
   5. k 增加 1。
9. 断言：数组的“ length”属性的值为numberOfArgs。
10. 返回 array.

### 22.1.2 Array 构造器属性 <div id="sec-properties-of-the-array-constructor"></div>
数组构造函数：

- 有一个[[Prototype]]内部插槽，其值是固有对象％FunctionPrototype％。
- 具有以下属性：

#### 22.1.2.1 Array.from ( items [ , mapfn [ , thisArg ] ] ) <div id="sec-array.from"></div>

当使用参数项和可选参数mapfn和thisArg调用from方法时，将执行以下步骤：

1. 令 C 为 this 值
2. 若 mapfn 是 undefined, 令 mapping 为 false.
3. 否则，
   1. 若 IsCallable(mapfn) 是 false, 抛出 TypeError 异常
   2. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
   3. 令 mapping 为 true.
4. 令 usingIterator 为 ? GetMethod(items, @@iterator).
5. 若 usingIterator 不是 undefined，那么
   1. 若 IsConstructor(C) 是 true，那么
      1. 令 A 为 ? Construct(C).
   2. 否则，
      1. 令 A 为 ! ArrayCreate(0).
   3. 令 iteratorRecord 为 ? GetIterator(items, sync, usingIterator).
   4. 令 k 为 0.
   5. 重复,
      1. 若 k ≥ 253 - 1，那么
         1. 令 error 为 ThrowCompletion(a newly created TypeError object).
         2. 返回 ? IteratorClose(iteratorRecord, error).
      2. 令 Pk 为 ! ToString(k).
      3. 令 next 为 ? IteratorStep(iteratorRecord).
      4. 若 next 是 false，那么
         1. 执行 ? Set(A, "length", k, true).
         2. 返回 A.
      5. 令 nextValue 为 ? IteratorValue(next).
      6. 若 mapping 是 true，那么
         1. 令 mappedValue 为 Call(mapfn, T, « nextValue, k »).
         2. 若 mappedValue 是突然完成, 返回 ? IteratorClose(iteratorRecord, mappedValue).
         3. 设置 mappedValue 为 mappedValue.[[Value]].
      7. 否则，令 mappedValue 为 nextValue.
      8. 令 defineStatus 为 CreateDataPropertyOrThrow(A, Pk, mappedValue).
      9. 若 defineStatus 是突然完成, 返回 ? IteratorClose(iteratorRecord, defineStatus).
      10. k 增加 1。
6. 注意：items不是Iterable，因此假设它是一个类似数组的对象。
7. 令 arrayLike 为 ! ToObject(items).
8. 令 len 为 ? ToLength(? Get(arrayLike, "length")).
9. 若 IsConstructor(C) 是 true，那么
   1. 令 A 为 ? Construct(C, « len »).
10. 否则，
    1. 令 A 为 ? ArrayCreate(len).
11. 令 k 为 0.
12. 重复，直到 k < len
    1. 令 Pk 为 ! ToString(k).
    2. 令 kValue 为 ? Get(arrayLike, Pk).
    3. 若 mapping 是 true，那么
       1. 令 mappedValue 为 ? Call(mapfn, T, « kValue, k »).
    4. 否则，令 mappedValue 为 kValue.
    5. 执行 ? CreateDataPropertyOrThrow(A, Pk, mappedValue).
    6. k 增加 1。
13. 执行 ? Set(A, "length", len, true).
14. 返回 A.

#### 22.1.2.2 Array.isArray ( arg ) <div id="sec-array.isarray"></div>

isArray函数采用一个参数arg，并执行以下步骤：

1. 返回 ? IsArray(arg).

#### 22.1.2.3 Array.of ( ...items ) <div id="sec-array.of"></div>

当使用任意数量的参数调用`of`方法时，将执行以下步骤：

1. 令 len 为传递给此函数的实际参数数。
2. 令 items 为传递给此函数的参数列表。
3. 令 C 为 this 值
4. 若 IsConstructor(C) 是 true，那么
   1. 令 A 为 ? Construct(C, « len »).
5. 否则，
   1. 令 A 为 ? ArrayCreate(len).
6. 令 k 为 0.
7. 重复，直到 k < len
   1. 令 kValue 为 items[k].
   2. 令 Pk 为 ! ToString(k).
   3. 执行 ? CreateDataPropertyOrThrow(A, Pk, kValue).
   4. k 增加 1。
8. 执行 ? Set(A, "length", len, true).
9. 返回 A.

> 注 1
>
> 假设items参数是格式正确的rest参数值。

> 注 2
>
> of函数是一种有意设计的通用工厂方法；它不需要其this值是Array构造函数。因此，它可以被其他构造函数传递或继承，这些构造函数可以使用单个数字参数调用。

#### 22.1.2.4 Array.prototype <div id="sec-array.prototype"></div>

Array.prototype的值为％ArrayPrototype％，即固有的Array原型对象。

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 22.1.2.5 获取 Array [ @@species ] <div id="sec-get-array-@@species"></div>

`Array[@@species]`是一个访问器属性，它的set访问器函数是未定义的。其get访问器函数执行以下步骤:

1. 返回 this 值

该函数的名称属性的值是“ get [Symbol.species]”。

> 注
>
>数组原型方法通常使用其“此”对象的构造函数来创建派生对象。但是，子类构造函数可以通过重新定义其@@ species属性来覆盖该默认行为。

### 22.1.3 Array 原型对象属性 <div id="sec-properties-of-the-array-prototype-object"></div>

数组原型对象：

- 是固有对象％ArrayPrototype％。
- 是一个数组奇异对象，并具有为此类对象指定的内部方法。
- 具有“length”属性，其初始值为0，其属性为{ [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.
- 有一个[[Prototype]]内部插槽，其值是固有对象％ObjectPrototype％。

> 注
>
> 将Array原型对象指定为Array奇异对象，以确保与ECMAScript 2015规范之前创建的ECMAScript代码兼容。

#### 22.1.3.1 Array.prototype.concat ( ...arguments ) <div id="sec-array.prototype.concat"></div>

当使用零个或多个参数调用`concat`方法时，它将返回一个数组，该数组包含对象的数组元素，然后依次排列每个参数的数组元素。

采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 A 为 ? ArraySpeciesCreate(O, 0).
3. 令 n 为 0.
4. 令 items 为一个List，其第一个元素为O，其后续元素按从左到右的顺序是传递给此函数调用的参数。
5. 重复，直到 items 不为空
   1. 从 items 中删除第一个元素，并让E为元素的值。
   2. 令 spreadable 为 ? IsConcatSpreadable(E).
   3. 若 spreadable 是 true，那么
      1. 令 k 为 0.
      2. 令 len 为 ? ToLength(? Get(E, "length")).
      3. 若 n + len > 253 - 1, 抛出 TypeError 异常
      4. 重复，直到 k < len
         1. 令 P 为 ! ToString(k).
         2. 令 exists 为 ? HasProperty(E, P).
         3. 若 exists 是 true，那么
            1. 令 subElement 为 ? Get(E, P).
            2. 执行 ? CreateDataPropertyOrThrow(A, ! ToString(n), subElement).
         4. n 增加 1。
         5. k 增加 1。
   4. 否则E is added as a single item rather than spread,
      1. 若 n ≥ 253 - 1, 抛出 TypeError 异常
      2. 执行 ? CreateDataPropertyOrThrow(A, ! ToString(n), E).
      3. n 增加 1。
6. 执行 ? Set(A, "length", n, true).
7. 返回 A.

`concat`方法的“ length”属性为1。

> 注 1
>
> 必须在步骤6中显式设置“length”属性，以确保在不存在结果数组的尾随元素的情况下其值正确。

> 注 2
>
> concat函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

##### 22.1.3.1.1 RS: IsConcatSpreadable ( O )<div id="sec-isconcatspreadable"></div>

带有参数O的抽象操作IsConcatSpreadable执行以下步骤:

1. 若 Type(O) 不是 Object, 返回 false.
2. 令 spreadable 为 ? Get(O, @@isConcatSpreadable).
3. 若 spreadable 不是 undefined, 返回 ToBoolean(spreadable).
4. 返回 ? IsArray(O).

#### 22.1.3.2 Array.prototype.constructor <div id="sec-array.prototype.constructor"></div>

Array.prototype.constructor 的初始值为固有对象％Array％。

#### 22.1.3.3 Array.prototype.copyWithin ( target, start [ , end ] ) <div id="sec-array.prototype.copywithin"></div>

copyWithin方法最多包含三个参数target，start和end。

> 注 1
>
> end参数是可选的，this 对象的长度是其默认值。如果target是负数，则将其视为length + target，其中length是数组的长度。如果start为负，则将其视为 length + start。如果end为负数，则视为 length + end。

采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 令 relativeTarget 为 ? ToInteger(target).
4. 若 relativeTarget < 0, 令 to 为 max((len + relativeTarget), 0); 否则令 to 为 min(relativeTarget, len).
5. 令 relativeStart 为 ? ToInteger(start).
6. 若 relativeStart < 0, 令 from 为 max((len + relativeStart), 0); 否则令 from 为 min(relativeStart, len).
7. 若 end 是 undefined, 令 relativeEnd 为 len; 否则令 relativeEnd 为 ? ToInteger(end).
8. 若 relativeEnd < 0, 令 final 为 max((len + relativeEnd), 0); 否则令 final 为 min(relativeEnd, len).
9. 令 count 为 min(final - from, len - to).
10. 若 from < to 并且 to < from + count，那么
    1. 令 direction 为 -1.
    2. 设置 from 为 from + count - 1.
    3. 设置 to to 为 + count - 1.
11. 否则，
    1. 令 direction 为 1.
12. 重复，直到 count > 0
    1. 令 fromKey 为 ! ToString(from).
    2. 令 toKey 为 ! ToString(to).
    3. 令 fromPresent 为 ? HasProperty(O, fromKey).
    4. 若 fromPresent 是 true，那么
       1. 令 fromVal 为 ? Get(O, fromKey).
       2. 执行 ? Set(O, toKey, fromVal, true).
    5. 否则fromPresent 是 false,
       1. 执行 ? DeletePropertyOrThrow(O, toKey).
    6. 设置 from 为 from + direction.
    7. 设置 to 为 to + direction.
    8. count 减少 1。
13. 返回 O.

> 注 2
>
> copyWithin 函数是故意通用的；它不要求它的这个值是一个数组对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

#### 22.1.3.4 Array.prototype.entries ( ) <div id="sec-array.prototype.entries"></div>

采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 返回 CreateArrayIterator(O, `"key+value"`).

这个函数是%ArrayProto_entries%固有对象。

#### 22.1.3.5 Array.prototype.every ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.every"></div>

> 注 1
>
> callbackfn应该是一个接受三个参数并返回可强制转换为布尔值true或false的值的函数。 `every`对数组中存在的每个元素按升序调用一次callbackfn，直到找到其中callbackfn返回false的元素为止。如果找到了这样的元素，`every`将立即返回false。否则，如果callbackfn对所有元素都返回true，则“ every”将返回true。仅对实际存在的数组元素调用callbackfn。它不要求缺少数组元素。
>
>  如果提供了thisArg参数，则它将用作每次调用callbackfn的this值。如果未提供，则使用undefined。
>
>  使用三个参数调用callbackfn：元素的值，元素的索引和要遍历的对象。
>
> “every”都不会直接改变其调用对象，但是可以通过对callbackfn的调用来改变该对象。
>
> “every”处理的元素范围是在第一次调用callbackfn之前设置的。调用`every`开始之后附加到数组的元素将不会被callbackfn访问。如果改变了数组的现有元素，则它们传递给callbackfn的值将是“every”访问它们时的值；在调用`every`之后开始并被访问之前删除的元素不会被访问。 “every”在数学上的作用类似于“为所有的”的量词。特别是对于空数组，它返回true。

当使用一个或两个参数调用“every”方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
4. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
5. 令 k 为 0.
6. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kPresent 为 ? HasProperty(O, Pk).
   3. 若 kPresent 是 true，那么
      1. 令 kValue 为 ? Get(O, Pk).
      2. 令 testResult 为 ToBoolean(? Call(callbackfn, T, « kValue, k, O »)).
      3. 若 testResult 是 false, 返回 false.
   4. k 增加 1。
7. 返回 true.

> 注 2
>
> “every”功能是有意通用的；它不需要this值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.6 Array.prototype.fill ( value [ , start [ , end ] ] ) <div id="sec-array.prototype.fill"></div>

fill 方法最多包含三个参数值：value, start 和 end.

> 注 1
>
> start和end参数是可选的，默认值为0，且此对象的长度为。如果start为负，则将其视为length + start，其中length是数组的长度。如果end为负，则将其视为length + end。

采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 令 relativeStart 为 ? ToInteger(start).
4. 若 relativeStart < 0, 令 k 为 max((len + relativeStart), 0); 否则令 k 为 min(relativeStart, len).
5. 若 end 是 undefined, 令 relativeEnd 为 len; 否则令 relativeEnd 为 ? ToInteger(end).
6. 若 relativeEnd < 0, 令 final 为 max((len + relativeEnd), 0); 否则令 final 为 min(relativeEnd, len).
7. 重复，直到 k < final
   1. 令 Pk 为 ! ToString(k).
   2. 执行 ? Set(O, Pk, value, true).
   3. k 增加 1。
8. 返回 O.

> 注 2
>
> “ fill”功能是有意通用的；它不需要this值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.7 Array.prototype.filter ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.filter"></div>

> 注 1
>
> 
> callbackfn应该是一个接受三个参数并返回可强制转换为布尔值true或false的值的函数。 “filter”对数组中的每个元素按升序调用一次callbackfn，并构造一个新数组，其中所有值均由callbackfn返回true。仅对实际存在的数组元素调用callbackfn。它不要求缺少数组元素。
>
> 如果提供了thisArg参数，则它将用作每次调用callbackfn的this值。如果未提供，则使用undefined。
>
> 使用三个参数调用callbackfn：元素的值，元素的索引和要遍历的对象。
>
> “filter”不会直接改变其调用对象，但是可以通过对callbackfn的调用来改变该对象。
>
> 在第一次调用callbackfn之前设置`filter`处理的元素范围。在调用`filter`开始之后追加到数组的元素将不会被callbackfn访问。如果改变了数组的现有元素，它们传递给callbackfn的值将是`filter`访问它们时的值。在调用`filter`之后开始访问之前删除的元素不会被访问。

When the `filter` method is called with one or two arguments, 采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
4. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
5. 令 A 为 ? ArraySpeciesCreate(O, 0).
6. 令 k 为 0.
7. 令 to 为 0.
8. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kPresent 为 ? HasProperty(O, Pk).
   3. 若 kPresent 是 true，那么
      1. 令 kValue 为 ? Get(O, Pk).
      2. 令 selected 为 ToBoolean(? Call(callbackfn, T, « kValue, k, O »)).
      3. 若 selected 是 true，那么
      4. 执行 ? CreateDataPropertyOrThrow(A, ! ToString(to), kValue).
      5. to 增加 1。
   4. k 增加 1。
9. 返回 A.

#### 22.1.3.8 Array.prototype.find ( predicate [ , thisArg ] ) <div id="sec-array.prototype.find"></div>

使用一个或两个参数谓词和thisArg调用“ find”方法。

> 注 1
>
> 
> 谓词应该是一个接受三个参数并返回可强制转换为布尔值的值的函数。 find对数组的每个元素以升序调用一次谓词，直到找到谓词返回true的谓词。如果找到了这样的元素，`find`将立即返回该元素值。否则，`find`返回undefined。
>
> 如果提供了thisArg参数，则它将用作每次谓词调用的this值。如果未提供，则使用undefined。
>
> 用三个参数调用谓词：元素的值，元素的索引和要遍历的对象。
>
> `find` 不会直接改变在其上调用它的对象，但是可以通过谓词的调用来改变该对象。
>
> 在第一次调用谓词之前设置`find`处理的元素范围。谓词不会访问在对`find`的调用开始之后追加到数组的元素。如果改变了数组中的现有元素，它们传递给谓词的值将是`find`访问它们时的值。

当调用find方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 IsCallable(predicate) 是 false, 抛出 TypeError 异常
4. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
5. 令 k 为 0.
6. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kValue 为 ? Get(O, Pk).
   3. 令 testResult 为 ToBoolean(? Call(predicate, T, « kValue, k, O »)).
   4. 若 testResult 是 true, 返回 kValue.
   5. k 增加 1。
7. 返回 undefined.

> 注 2
>
> find函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.9 Array.prototype.findIndex ( predicate [ , thisArg ] ) <div id="sec-array.prototype.findindex"></div>

> 注 1
>
> 
> 谓词应该是一个接受三个参数并返回可强制转换为布尔值true或false的值的函数。 “ findIndex”对数组中的每个元素以升序调用一次谓词，直到找到谓词返回true的谓词为止。如果找到了这样的元素，则`findIndex`立即返回该元素值的索引。否则，`findIndex`返回-1。
>
> 如果提供了thisArg参数，则它将用作每次谓词调用的this值。如果未提供，则使用undefined。
>
> 用三个参数调用谓词：元素的值，元素的索引和要遍历的对象。
>
> `findIndex` 不会直接改变在其上调用它的对象，但是可以通过谓词的调用来改变该对象。
>
> 由findIndex处理的元素范围是在第一次调用谓词之前设置的。谓词不会访问在开始调用`findIndex`之后追加到数组的元素。如果改变了数组中的现有元素，它们传递给谓词的值将是`findIndex`访问它们时的值。

当使用一个或两个参数调用`findIndex`方法时，采取以下步骤：

#### 22.1.3.10 Array.prototype.flat( [ depth ] ) <div id="sec-array.prototype.flat"></div>

当用零个或一个参数调用`flat`方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 sourceLen 为 ? ToLength(? Get(O, "length")).
3. 令 depthNum 为 1.
4. 若 depth 不是 undefined，那么
   1. 设置 depthNum 为 ? ToInteger(depth).
5. 令 A 为 ? ArraySpeciesCreate(O, 0).
6. 执行 ? FlattenIntoArray(A, O, sourceLen, 0, depthNum).
7. 返回 A.

##### 22.1.3.10.1 FlattenIntoArray(target, source, sourceLen, start, depth [ , mapperFunction, thisArg ]) <div id="sec-flattenintoarray"></div>

1. 令 targetIndex 为 start.
2. 令 sourceIndex 为 0.
3. 重复，直到 sourceIndex < sourceLen
   1. 令 P 为 ! ToString(sourceIndex).
   2. 令 exists 为 ? HasProperty(source, P).
   3. 若 exists 是 true，那么
      1. 令 element 为 ? Get(source, P).
      2. 若 mapperFunction 存在，那么
         1. 断言：thisArg is present.
         2. 设置 element 为 ? Call(mapperFunction, thisArg , « element, sourceIndex, source »).
      3. 令 shouldFlatten 为 false.
      4. 若 depth > 0，那么
      5. 设置 shouldFlatten 为 ? IsArray(element).
      6. 若 shouldFlatten 是 true，那么
      7. 令 elementLen 为 ? ToLength(? Get(element, "length")).
      8. 设置 targetIndex 为 ? FlattenIntoArray(target, element, elementLen, targetIndex, depth - 1).
      9. 否则，
      10. 若 targetIndex ≥ 253-1, 抛出 TypeError 异常
      11. 执行 ? CreateDataPropertyOrThrow(target, ! ToString(targetIndex), element).
      12. targetIndex 增加 1。
   4. sourceIndex 增加 1。
4. 返回 targetIndex.

#### 22.1.3.11 Array.prototype.flatMap ( mapperFunction [ , thisArg ] ) <div id="sec-array.prototype.flatmap"></div>

当使用一个或两个参数调用“ flatMap”方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 sourceLen 为 ? ToLength(? Get(O, `"length"`)).
3. 若 IsCallable(mapperFunction) 是 false, 抛出 TypeError 异常
4. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
5. 令 A 为 ? ArraySpeciesCreate(O, 0).
6. 执行 ? FlattenIntoArray(A, O, sourceLen, 0, 1, mapperFunction, T).
7. 返回 A.

#### 22.1.3.12 Array.prototype.forEach ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.foreach"></div>

> 注 1
>
> callbackfn应该是一个接受三个参数的函数。 `forEach`对数组中存在的每个元素以升序调用一次callbackfn。仅对实际存在的数组元素调用callbackfn。它不要求缺少数组元素。
>
> 如果提供了thisArg参数，则它将用作每次调用callbackfn的this值。如果未提供，则使用undefined。
>
> 使用三个参数调用callbackfn：元素的值，元素的索引和要遍历的对象。
>
> `forEach` 不会直接改变其调用对象，但是可以通过对callbackfn的调用来改变该对象。

当使用一个或两个参数调用forEach方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
4. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
5. 令 k 为 0.
6. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kPresent 为 ? HasProperty(O, Pk).
   3. 若 kPresent 是 true，那么
      1. 令 kValue 为 ? Get(O, Pk).
      2. 执行 ? Call(callbackfn, T, « kValue, k, O »).
   4. k 增加 1。
7. 返回 undefined.

此函数是％ArrayProto_forEach％内部对象。

> 注 2
>
> forEach函数是有意通用的；它不需要this值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.13 Array.prototype.includes ( searchElement [ , fromIndex ] ) <div id="sec-array.prototype.includes"></div>

> 注 1
>
> “ includes”使用SameValueZero算法以升序将searchElement与数组的元素进行比较，如果在任何位置找到，则返回true；否则，返回true。否则，返回false。
>
> 可选的第二个参数fromIndex默认为0（即搜索整个数组）。如果它大于或等于数组的长度，则返回false，即将不搜索数组。如果它为负数，则用作从数组末尾开始的偏移量，以计算fromIndex。如果计算的索引小于0，则将搜索整个数组。

当调用includes方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 len 是 0, 返回 false.
4. 令 n 为 ? ToInteger(fromIndex).
5. 断言：If fromIndex is undefined，那么 n is 0.
6. 若 n ≥ 0，那么
   1. 令 k 为 n.
7. 否则n < 0,
   1. 令 k 为 len + n.
   2. 若 k < 0, set k to 0.
8. 重复，直到 k < len
   1. 令 elementK 为 the result of ? Get(O, ! ToString(k)).
   2. 若 SameValueZero(searchElement, elementK) 是 true, 返回 true.
   3. k 增加 1。
9. 返回 false.

> 注2
>
> 包含函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

> 注 3
>
> “includes”方法在两个方面有意不同于类似的“ indexOf”方法。首先，它使用SameValueZero算法而不是严格相等比较，从而可以检测NaN数组元素。其次，它不会跳过缺少的数组元素，而是将它们视为undefined。

#### 22.1.3.14 Array.prototype.indexOf ( searchElement [ , fromIndex ] ) <div id="sec-array.prototype.indexof"></div>

> 注 1
>
> indexOf使用严格相等比较算法按升序将searchElement与数组的元素进行比较，如果发现一个或多个索引，则返回最小的索引；否则，返回-1。
>
> 可选的第二个参数fromIndex默认为0（即搜索整个数组）。如果它大于或等于数组的长度，则返回-1，即不会搜索该数组。如果它为负数，则用作从数组末尾开始的偏移量，以计算fromIndex。如果计算的索引小于0，则将搜索整个数组。

当使用一个或两个参数调用`indexOf`方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 len 是 0, 返回 -1.
4. 令 n 为 ? ToInteger(fromIndex).
5. 断言：If fromIndex is undefined，那么 n is 0.
6. 若 n ≥ len, 返回 -1.
7. 若 n ≥ 0，那么
   1. 若 n 是 -0, 令 k 为 +0; 否则令 k 为 n.
8. 否则n < 0,
   1. 令 k 为 len + n.
   2. 若 k < 0, set k to 0.
9. 重复，直到 k < len
   1. 令 kPresent 为 ? HasProperty(O, ! ToString(k)).
   2. 若 kPresent 是 true，那么
      1. 令 elementK 为 ? Get(O, ! ToString(k)).
      2. 令 same 为执行严格相等比较searchElement === elementK的结果。
      3. 若 same 是 true, 返回 k.
   3. k 增加 1。
10. 返回 -1.

> 注 2
>
> indexOf函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.15 Array.prototype.join ( separator ) <div id="sec-array.prototype.join"></div>

> 注 1
>
> 数组的元素将转换为字符串，然后将这些字符串连接起来，并根据出现的分隔符进行分隔。如果未提供分隔符，则使用单个逗号作为分隔符。

join方法采用一个参数，分隔符，并执行以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 separator 是 undefined, 令 sep 为  ",".
4. 否则，令 sep 为 ? ToString(separator).
5. 令 R 为空字符串。
6. 令 k 为 0.
7. 重复，直到 k < len
   1. 若 k > 0,将R设置为R和sep的字符串连接。
   2. 令 element 为 ? Get(O, ! ToString(k)).
   3. 若 element 是 undefined 或 null, 令 next 为空字符串; 否则, 令 next 为 ? ToString(element).
   4. 设置 R 为 R 和 next 的字符串连接。
   5. k 增加 1。
8. 返回 R.

> 注 2
>
> “ join”功能是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 22.1.3.16 Array.prototype.keys ( ) <div id="sec-array.prototype.keys"></div>

采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 返回 CreateArrayIterator(O, "key").

此函数是％ArrayProto_keys％内部对象。

#### 22.1.3.17 Array.prototype.lastIndexOf ( searchElement [ , fromIndex ] ) <div id="sec-array.prototype.lastindexof"></div>

> 注 1
>
> “ lastIndexOf”使用严格相等比较算法按降序将searchElement与数组元素进行比较，如果在一个或多个索引处找到，则返回最大的索引；否则，返回-1。
>
> 可选的第二个参数fromIndex默认为数组的长度减去一（即搜索整个数组）。如果它大于或等于数组的长度，则将搜索整个数组。如果它为负数，则用作从数组末尾开始的偏移量，以计算fromIndex。如果计算的索引小于0，则返回-1。

 当使用一个或两个参数调用“ lastIndexOf”方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 len 是 0, 返回 -1.
4. 若 fromIndex 存在, 令 n 为 ? ToInteger(fromIndex); 否则令 n 为 len - 1.
5. 若 n ≥ 0，那么
   1. 若 n 是 -0, 令 k 为 +0; 否则令 k 为 min(n, len - 1).
6. 否则n < 0,
   1. 令 k 为 len + n.
7. 重复，直到 k ≥ 0
   1. 令 kPresent 为 ? HasProperty(O, ! ToString(k)).
   2. 若 kPresent 是 true，那么
      1. 令 elementK 为 ? Get(O, ! ToString(k)).
      2. 令 same 为执行严格相等比较searchElement === elementK的结果。
      3. 若 same 是 true, 返回 k.
   3. k 减少 1。
8. 返回 -1.

> 注 2
>
> `lastIndexOf`函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.18 Array.prototype.map ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.map"></div>

> 注 1
>
> callbackfn应该是一个接受三个参数的函数。 map会对数组中的每个元素按升序调用一次callbackfn，然后从结果中构造一个新的Array。仅对实际存在的数组元素调用callbackfn。它不要求缺少数组元素。
>
> 如果提供了thisArg参数，则它将用作每次调用callbackfn的this值。如果未提供，则使用undefined。
>
> 使用三个参数调用callbackfn：元素的值，元素的索引和要遍历的对象。
>
> `map` 不会直接改变其调用对象，但是可以通过对callbackfn的调用来改变该对象。
>
> 在第一次调用callbackfn之前设置由map处理的元素范围。在调用map之后开始后追加到数组的元素将不会被callbackfn访问。如果改变了数组的现有元素，它们传递给callbackfn的值将是map访问它们时的值；在调用map之后开始但被访问之前删除的元素不会被访问。

 当使用一个或两个参数调用`map`方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
4. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
5. 令 A 为 ? ArraySpeciesCreate(O, len).
6. 令 k 为 0.
7. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kPresent 为 ? HasProperty(O, Pk).
   3. 若 kPresent 是 true，那么
      1. 令 kValue 为 ? Get(O, Pk).
      2. 令 mappedValue 为 ? Call(callbackfn, T, « kValue, k, O »).
      3. 执行 ? CreateDataPropertyOrThrow(A, Pk, mappedValue).
   4. k 增加 1。
8. 返回 A.

> 注 2
>
> map函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.19 Array.prototype.pop ( ) <div id="sec-array.prototype.pop"></div>

> 注 1
>
> 数组的最后一个元素从数组中删除并返回。

When the `pop` method is called, 采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 len 是 zero，那么
   1. 执行 ? Set(O, "length", 0, true).
   2. 返回 undefined.
4. 否则len > 0,
   1. 令 newLen 为 len - 1.
   2. 令 index 为 ! ToString(newLen).
   3. 令 element 为 ? Get(O, index).
   4. 执行 ? DeletePropertyOrThrow(O, index).
   5. 执行 ? Set(O, "length", newLen, true).
   6. 返回 element.

> 注 2
>
> “ pop”功能是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.20 Array.prototype.push ( ...items ) <div id="sec-array.prototype.push"></div>

> 注 1
>
> 参数按照它们出现的顺序附加到数组的末尾。调用的结果将返回数组的新长度。

当使用零个或多个参数调用`push`方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 令 items 为一个列表，其元素按从左到右的顺序是传递给此函数调用的参数。
4. 令 argCount 为 items 中元素的数量。
5. 若 len + argCount > 253 - 1, 抛出 TypeError 异常
6. 重复，直到 items 不为空
   1. 从 items 中删除第一个元素，并且令 E 为元素的值。
   2. 执行 ? Set(O, ! ToString(len), E, true).
   3. len 增加 1。
7. 执行 ? Set(O, "length", len, true).
8. 返回 len.

push方法的“ length”属性为1。

> 注 2
>
> push函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.21 Array.prototype.reduce ( callbackfn [ , initialValue ] ) <div id="sec-array.prototype.reduce"></div>

> 注 1
>
> 
> callbackfn应该是一个带有四个参数的函数。 “ reduce”作为函数，对数组中第一个元素之后的每个元素以升序调用一次回调。
>
> 使用四个参数调用callbackfn：previousValue（从上次调用callbackfn的值），currentValue（当前元素的值），currentIndex和要遍历的对象。首次调用回调时，previousValue和currentValue可以是两个值之一。如果在`reduce`的调用中提供了initialValue，则previousValue将等于initialValue，而currentValue将等于数组中的第一个值。如果未提供initialValue，则previousValue将等于数组中的第一个值，而currentValue将等于第二个值。如果数组不包含任何元素并且未提供initialValue，则为TypeError。
>
> `reduce` 不会直接改变其调用对象，但是可以通过对callbackfn的调用来改变该对象。
>
> 由reduce处理的元素范围在第一次调用callbackfn之前设置。在调用`reduce`开始之后追加到数组的元素将不会被callbackfn访问。如果数组的现有元素被更改，则传递给callbackfn的值将是`reduce`访问它们时的值；开始调用`reduce`之后被删除的元素不会被访问。

当使用一个或两个参数调用`reduce`方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
4. 若 len is 0 and initialValue 不存在, 抛出 TypeError 异常
5. 令 k 为 0.
6. 令 accumulator 为 undefined.
7. 若 initialValue 存在，那么
   1. 设置 accumulator 为 initialValue.
8. 否则initialValue不存在,
   1. 令 kPresent 为 false.
   2. 重复，直到 kPresent 是 false 并且 k < len
      1. 令 Pk 为 ! ToString(k).
      2. 设置 kPresent 为 ? HasProperty(O, Pk).
      3. 若 kPresent 是 true，那么
         1. 设置 accumulator 为 ? Get(O, Pk).
      4. k 增加 1。
   3. 若 kPresent 是 false, 抛出 TypeError 异常
9. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kPresent 为 ? HasProperty(O, Pk).
   3. 若 kPresent 是 true，那么
      1. 令 kValue 为 ? Get(O, Pk).
      2. 设置 accumulator 为 ? Call(callbackfn, undefined, « accumulator, kValue, k, O »).
   4. k 增加 1。
10. 返回 accumulator.

> 注 2
>
> “ reduce”功能是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.22 Array.prototype.reduceRight ( callbackfn [ , initialValue ] ) <div id="sec-array.prototype.reduceright"></div>

> 注 1
>
> callbackfn应该是一个带有四个参数的函数。 “ reduceRight”作为函数对数组中第一个元素之后的每个元素按降序调用一次回调。
>
> 使用四个参数调用callbackfn：previousValue（从上次调用callbackfn的值），currentValue（当前元素的值），currentIndex和要遍历的对象。第一次调用该函数时，previousValue和currentValue可以是两个值之一。如果在对`reduceRight`的调用中提供了initialValue，则previousValue将等于initialValue，而currentValue将等于数组中的最后一个值。如果未提供initialValue，则previousValue将等于数组中的最后一个值，而currentValue将等于倒数第二个值。如果数组不包含任何元素并且未提供initialValue，则为TypeError。
>
> `reduceRight` 不会直接改变其调用对象，但是可以通过对callbackfn的调用来改变该对象。
>
> 由reduceRight处理的元素范围是在第一次调用callbackfn之前设置的。在调用`reduceRight`开始之后追加到数组的元素将不会被callbackfn访问。如果数组的现有元素被callbackfn更改，则传递给callbackfn的值将是`reduceRight`访问它们时的值；在调用`reduceRight`之后并且被访问之前删除的元素不会被访问。

当使用一个或两个参数调用`reduceRight`方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
4. 若 len is 0 and initialValue 不存在, 抛出 TypeError 异常
5. 令 k 为 len - 1.
6. 令 accumulator 为 undefined.
7. 若 initialValue 存在，那么
   1. 设置 accumulator 为 initialValue.
8. 否则initialValue不存在,
   1. 令 kPresent 为 false.
   2. 重复，直到 kPresent 是 false 并且 k ≥ 0
      1. 令 Pk 为 ! ToString(k).
      2. 设置 kPresent 为 ? HasProperty(O, Pk).
      3. 若 kPresent 是 true，那么
         1. 设置 accumulator 为 ? Get(O, Pk).
      4. k 减少 1。
   3. 若 kPresent 是 false, 抛出 TypeError 异常
9. 重复，直到 k ≥ 0
   1. 令 Pk 为 ! ToString(k).
   2. 令 kPresent 为 ? HasProperty(O, Pk).
   3. 若 kPresent 是 true，那么
      1. 令 kValue 为 ? Get(O, Pk).
      2. 设置 accumulator 为 ? Call(callbackfn, undefined, « accumulator, kValue, k, O »).
   4. k 减少 1。
10. 返回 accumulator.

> 注 2
>
> “ reduceRight”功能是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 22.1.3.23 Array.prototype.reverse ( ) <div id="sec-array.prototype.reverse"></div>

> 注 1
>
> 重新排列数组的元素，以便颠倒其顺序。该对象作为调用的结果返回。

当调用“ reverse”方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 令 middle 为 floor(len / 2).
4. 令 lower 为 0.
5. 重复，直到 lower ≠ middle
   1. 令 upper 为 len - lower - 1.
   2. 令 upperP 为 ! ToString(upper).
   3. 令 lowerP 为 ! ToString(lower).
   4. 令 lowerExists 为 ? HasProperty(O, lowerP).
   5. 若 lowerExists 是 true，那么
      1. 令 lowerValue 为 ? Get(O, lowerP).
   6. 令 upperExists 为 ? HasProperty(O, upperP).
   7. 若 upperExists 是 true，那么
      1. 令 upperValue 为 ? Get(O, upperP).
   8. 若 lowerExists is true and upperExists 是 true，那么
      1. 执行 ? Set(O, lowerP, upperValue, true).
      2. 执行 ? Set(O, upperP, lowerValue, true).
   9. 否则if lowerExists is false and upperExists is true，那么
      1. 执行 ? Set(O, lowerP, upperValue, true).
      2. 执行 ? DeletePropertyOrThrow(O, upperP).
   10. 否则if lowerExists is true and upperExists is false，那么
       1. 执行 ? DeletePropertyOrThrow(O, lowerP).
       2. 执行 ? Set(O, upperP, lowerValue, true).
   11. 否则both lowerExists and upperExists are false,
       1. No action is required.
   12. lower 增加 1。
6. 返回 O.

> 注 2
>
> reverse 功能是有意通用的。它不需要此值是Array对象。因此，可以将其转移到其他种类的对象中用作方法。

#### 22.1.3.24 Array.prototype.shift ( ) <div id="sec-array.prototype.shift"></div>

> 注 1
>
> 数组的第一个元素从数组中删除并返回。s

当调用shift方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 len 是 zero，那么
   1. 执行 ? Set(O, "length", 0, true).
   2. 返回 undefined.
4. 令 first 为 ? Get(O, "0").
5. 令 k 为 1.
6. 重复，直到 k < len
   1. 令 from 为 ! ToString(k).
   2. 令 to 为 ! ToString(k - 1).
   3. 令 fromPresent 为 ? HasProperty(O, from).
   4. 若 fromPresent 是 true，那么
      1. 令 fromVal 为 ? Get(O, from).
      2. 执行 ? Set(O, to, fromVal, true).
   5. 否则fromPresent 是 false,
      1. 执行 ? DeletePropertyOrThrow(O, to).
   6. k 增加 1。
7. 执行 ? DeletePropertyOrThrow(O, ! ToString(len - 1)).
8. 执行 ? Set(O, "length", len - 1, true).
9. 返回 first.

> 注 2
>
> shift 功能是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 22.1.3.25 Array.prototype.slice ( start, end ) <div id="sec-array.prototype.slice"></div>

> 注 1
>
> slice方法有两个参数start和end，并返回一个数组，该数组包含从元素开始到元素结束（但不包括元素结束）（如果未定义end，则从数组的结尾）开始的数组的元素。如果start为负，则将其视为length + start，其中length是数组的长度。如果end为负，则将其视为length + end，其中length是数组的长度。

采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 令 relativeStart 为 ? ToInteger(start).
4. 若 relativeStart < 0, 令 k 为 max((len + relativeStart), 0); 否则令 k 为 min(relativeStart, len).
5. 若 end 是 undefined, 令 relativeEnd 为 len; 否则令 relativeEnd 为 ? ToInteger(end).
6. 若 relativeEnd < 0, 令 final 为 max((len + relativeEnd), 0); 否则令 final 为 min(relativeEnd, len).
7. 令 count 为 max(final - k, 0).
8. 令 A 为 ? ArraySpeciesCreate(O, count).
9. 令 n 为 0.
10. 重复，直到 k < final
    1. 令 Pk 为 ! ToString(k).
    2. 令 kPresent 为 ? HasProperty(O, Pk).
    3. 若 kPresent 是 true，那么
       1. 令 kValue 为 ? Get(O, Pk).
       2. 执行 ? CreateDataPropertyOrThrow(A, ! ToString(n), kValue).
    4. k 增加 1。
    5. n 增加 1。
11. 执行 ? Set(A, "length", n, true).
12. 返回 A.

> 注 2
>
> 在早期版本的ECMAScript中，必须在步骤11中显式设置结果数组的“长度”属性，以确保在不存在结果数组尾随元素的情况下其长度正确。从ES2015开始，当结果Array初始化为适当的长度而不是一个空Array时，就不必设置“ length”了，但是继续进行以保持向后兼容性。

> 注 3
>
> slice功能是有意通用的。它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 22.1.3.26 Array.prototype.some ( callbackfn [ , thisArg ] ) <div id="sec-array.prototype.some"></div>

> 注 1
>
> callbackfn应该是一个接受三个参数并返回可强制转换为布尔值true或false的值的函数。 some对数组中存在的每个元素以升序调用一次callbackfn，直到找到其中callbackfn返回true的元素为止。如果找到了这样的元素，`some`将立即返回true。否则，“ some”将返回false。仅对实际存在的数组元素调用callbackfn。它不要求缺少数组元素。
>
> 如果提供了thisArg参数，则它将用作每次调用callbackfn的this值。如果未提供，则使用undefined。
>
> 使用三个参数调用callbackfn：元素的值，元素的索引和要遍历的对象。
>
> `some` 不会直接改变其调用对象，但是可以通过对callbackfn的调用来改变该对象。
>
> 在首次调用callbackfn之前设置由some处理的元素范围。在调用some之后开始后附加到数组的元素将不会被callbackfn访问。如果数组的现有元素被更改，则传递给callbackfn的值将是“ some”访问它们时的值。在调用some之后开始但被访问之前删除的元素不会被访问。 “某些”行为就像数学中的“存在”量词一样。特别是对于空数组，它返回false。

当使用一些参数调用“ some”方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
4. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
5. 令 k 为 0.
6. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kPresent 为 ? HasProperty(O, Pk).
   3. 若 kPresent 是 true，那么
      1. 令 kValue 为 ? Get(O, Pk).
      2. 令 testResult 为 ToBoolean(? Call(callbackfn, T, « kValue, k, O »)).
      3. 若 testResult 是 true, 返回 true.
   4. k 增加 1。
7. 返回 false.

> 注 2
>
> some函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为一种方法。

#### 22.1.3.27 Array.prototype.sort ( comparefn ) <div id="sec-array.prototype.sort"></div>

此数组的元素已排序。排序必须是稳定的（也就是说，比较相等的元素必须保持其原始顺序）。如果comparefn不是undefined，则它应该是一个接受两个参数x和y的函数，如果x <y，则返回负值；如果x = y，则返回零；如果x> y，则返回正值。

输入后，将执行以下步骤来初始化对sort函数的评估：

1. 若 comparefn 不是 undefined 并且 IsCallable(comparefn) 是 false, 抛出 TypeError 异常
2. 令 obj 为 ? ToObject(this value).
3. 令 len 为 ? ToLength(? Get(obj, "length")).

在此sort方法的规范中，如果以下算法返回true，则将对象obj称为*稀疏*：

1. 对于范围 0 ≤ i < len 的每个整数 i
   1. 令 elem 为 obj.\[\[GetOwnProperty]](! ToString(i)).
   2. 若 elem 是 undefined, 返回 true.
2. 返回 false.

sort order 是此函数完成后对整数索引小于len的obj的整数索引属性值的排序。然后按以下方式确定“ sort”函数的结果：

如果comparefn不是未定义的，并且不是该数组元素的一致比较函数（请参见下文），则排序顺序是实现定义的。如果未定义comparefn并且SortCompare不用作一致的比较函数，则排序顺序也是由实现定义的。

设proto为obj.\[\[GetPrototypeOf]]()。如果proto不为空，且存在一个整数j，满足以下所有条件，则排序顺序由实现定义:

- obj 是稀疏的
- 0 ≤ j < len
- HasProperty(proto, ToString(j)) 是 true

如果obj是稀疏的，并且下列条件为真，则排序顺序也是实现定义的:

- IsExtensible(obj) 是 false.
- obj的任何整数索引属性，如果其名称是非负整数，且小于len，那么它就是一个数据属性，其[[Configurable]]属性为false。

如果下列条件为真，排序顺序也是实现定义的:

- 如果obj是一个异类对象(包括代理异类对象)，它对[[Get]]、[[Set]]、[[Delete]]和[[GetOwnProperty]]的行为不是这些内部方法的普通对象实现。
- 如果obj的任何索引属性的名称是小于len的非负整数，那么它就是一个访问器属性，或者是一个数据属性，它的[[Writable]]属性是假的。
- 如果comparefn是未定义的，并且ToString的应用程序对作为SortCompare参数传递的任何值修改obj或obj的原型链上的任何对象。
- 如果comparefn是未定义的，并且ToString的所有应用程序，对于作为参数传递给SortCompare的任何特定值，都不会产生相同的结果。

采取以下步骤：

1. 执行调用obj的[[Get]]和[[Set]]内部方法的一个与实现相关的序列，调用DeletePropertyOrThrow和HasOwnProperty抽象操作，obj作为第一个参数，并进行SortCompare(如下所述)，这样:
   - 每个调用[[Get]]、[[Set]]、HasOwnProperty或DeletePropertyOrThrow的属性键参数是小于len的非负整数的字符串表示。
   - SortCompare调用的参数是之前调用[[Get]]内部方法返回的值，除非之前调用访问的属性根据HasOwnProperty不存在。如果SortCompare的两个预期参数都对应于不存在的属性，则使用+0而不是调用SortCompare。如果第一个预期参数不存在，就使用+1。若只有第二个潜在的论点是不存在使用1。
   - 如果obj不是稀疏的，那么就不能调用DeletePropertyOrThrow。
   - 如果任何[[Set]]调用返回false，则抛出一个类型错误异常。
   - 如果从这些操作中返回一个突然完成，它将立即作为该函数的值返回。
2. 返回 obj.

除非上面指定了实现定义的排序顺序，否则返回的对象必须具有以下两个特征:

- 非负整数必须存在一些小于 len 的数学置换 π，以便对于每个小于 len 的非负整数 j，如果存在 old[j] 属性，则 new[π(j)] 与 old[j] 完全相同。但是，如果属性 old[j] 不存在，则 new[π(j)] 不存在。
- 然后，对于所有小于len的所有非负整数j和k，若 SortCompare(old[j], old[k]) < 0 (见下方的 SortCompare), 那么 new[π(j)] < new[π(k)].

这里，old[j] 表示在执行这个函数之前调用 obj.\[\[Get]](j) 的假设结果，new[j] 表示在执行这个函数之后调用 obj.\[\[Get]](j) 的假设结果。

如果集合S中的所有值A、b和c(可能是相同的值)都满足下列所有要求，则函数comparefn是一组值S的一致比较函数：符号A <CF b表示comparefn(A, b) < 0；a =CF b表示comparefn(a, b) = 0(任意符号)；而a >CF b表示比较fn(a, b) > 0。

- 调用comparefn(a, b)总是返回相同的值v，当给定一对特定的值a和b作为它的两个参数时。而且，类型(v)是Number，而v不是NaN。注意，这意味着a <CF b, a =CF b，和a >CF b中的一个对于给定的a和b是正确的。
- 调用comparefn(a, b)不会修改obj或obj原型链上的任何对象。
- a =CF a (reflexivity)
- 若 a =CF b, 那么 b =CF a (自反性)
- 若 a =CF b 并且 b =CF c, 那么 a =CF c ( =CF 的传递性)
- 若 a <CF b 并且 b <CF c, 那么 a <CF c ( <CF 的传递性)
- 若 a >CF b 并且 b >CF c, 那么 a >CF c ( >CF 的传递性)

> 注 1
>
> 上述条件是充分必要的，可以保证comparefn将集合S分成等价类，并且这些等价类是完全有序的。

> 注 2
>
> ‘sort’函数是故意通用的；它不要求它的这个值是一个数组对象。因此，它可以作为一种方法转移到其他类型的对象中去使用。

##### 22.1.3.27.1 RS: SortCompare ( x, y ) <div id="sec-sortcompare"></div>

SortCompare抽象操作由两个参数x和y调用，它还可以访问传递给当前调用的‘sort’方法的comparefn参数。采取以下步骤：

1. 若 x 和 y 都是 undefined, 返回 +0.
2. 若 x 是 undefined, 返回 1.
3. 若 y 是 undefined, 返回 -1.
4. 若 comparefn 不是 undefined，那么
   1. 令 v 为 ? ToNumber(? Call(comparefn, undefined, « x, y »)).
   2. 若 v 是 NaN, 返回 +0.
   3. 返回 v.
5. 令 xString 为 ? ToString(x).
6. 令 yString 为 ? ToString(y).
7. 令 xSmaller 为执行抽象关系比较xString < yString的结果。
8. 若 xSmaller 是 true, 返回 -1.
9. 令 ySmaller 为执行抽象关系比较yString < xString的结果。
10. 若 ySmaller 是 true, 返回 1.
11. 返回 +0.

> 注 1
>
> 因为不存在的属性值总是比未定义的属性值大，而且未定义的属性值总是比任何其他值大，所以未定义的属性值总是排序到结果的末尾，然后是不存在的属性值。

> 注 2
>
> 第5步和第7步中的ToString抽象操作执行的方法调用可能会导致SortCompare表现为不一致的比较函数。

#### 22.1.3.28 Array.prototype.splice ( start, deleteCount, ...items ) <div id="sec-array.prototype.splice"></div>

> 注 1
>
> 当使用两个或多个参数start (deleteCount和零个或多个项目)调用“splice”方法时，从整数索引开始的数组的deleteCount元素将被参数项替换。返回一个包含已删除元素(如果有)的数组对象。

采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 令 relativeStart 为 ? ToInteger(start).
4. 若 relativeStart < 0, 令 actualStart 为 max((len + relativeStart), 0); 否则令 actualStart 为 min(relativeStart, len).
5. 若实际参数的数量是 0，那么
   1. 令 insertCount 为 0.
   2. 令 actualDeleteCount 为 0.
6. 否则如果实际参数的数目为1，那么
   1. 令 insertCount 为 0.
   2. 令 actualDeleteCount 为 len - actualStart.
7. 否则，
   1. 令 insertCount 为 
   2. 令 dc 为 ? ToInteger(deleteCount).
   3. 令 actualDeleteCount 为 min(max(dc, 0), len - actualStart).
8. 若 len + insertCount - actualDeleteCount > 253 - 1, 抛出 TypeError 异常
9. 令 A 为 ? ArraySpeciesCreate(O, actualDeleteCount).
10. 令 k 为 0.
11. 重复，直到 k < actualDeleteCount
    1. 令 from 为 ! ToString(actualStart + k).
    2. 令 fromPresent 为 ? HasProperty(O, from).
    3. 若 fromPresent 是 true，那么
       1. 令 fromValue 为 ? Get(O, from).
       2. 执行 ? CreateDataPropertyOrThrow(A, ! ToString(k), fromValue).
    4. Increment k by 1.
12. 执行 ? Set(A, "length", actualDeleteCount, true).
13. 令 items 为一个列表，其元素按从左到右的顺序是实际参数列表中从第三个参数开始的部分。如果传递的参数少于三个，则该列表为空。
14. 令 itemCount 为 items 中元素的数量。
15. 若 itemCount < actualDeleteCount，那么
    1. 设置 k 为 actualStart.
    2. 重复，直到 k < (len - actualDeleteCount)
       1. 令 from 为 ! ToString(k + actualDeleteCount).
       2. 令 to 为 ! ToString(k + itemCount).
       3. 令 fromPresent 为 ? HasProperty(O, from).
       4. 若 fromPresent 是 true，那么
          1. 令 fromValue 为 ? Get(O, from).
          2. 执行 ? Set(O, to, fromValue, true).
       5. 否则fromPresent 是 false,
          1. 执行 ? DeletePropertyOrThrow(O, to).
       6. k 增加 1。
    3. 设置 k 为 len.
    4. 重复，直到 k > (len - actualDeleteCount + itemCount)
       1. 执行 ? DeletePropertyOrThrow(O, ! ToString(k - 1)).
       2. k 减少 1。
16. 否则if itemCount > actualDeleteCount，那么
    1. 设置 k 为 (len - actualDeleteCount).
    2. 重复，直到 k > actualStart
       1. 令 from 为 ! ToString(k + actualDeleteCount - 1).
       2. 令 to 为 ! ToString(k + itemCount - 1).
       3. 令 fromPresent 为 ? HasProperty(O, from).
       4. 若 fromPresent 是 true，那么
          1. 令 fromValue 为 ? Get(O, from).
          2. 执行 ? Set(O, to, fromValue, true).
       5. 否则fromPresent 是 false,
          1. 执行 ? DeletePropertyOrThrow(O, to).
       6. k 减少 1。
17. 设置 k 为 actualStart.
18. 重复，直到 items 不为空
    1. 从 items 中删除第一个元素，并且令 E 为该元素的值。
    2. 执行 ? Set(O, ! ToString(k), E, true).
    3. k 增加 1。
19. 执行 ? Set(O, "length", len - actualDeleteCount + itemCount, true).
20. 返回 A.

> 注 2
>
> 在早期版本的ECMAScript中，必须在步骤19中显式设置结果数组的“length”属性，以确保在不存在结果数组尾随元素的情况下其长度正确。从ES2015开始，当结果Array初始化为适当的长度（而不是一个空Array）时，就不必再设置“ length”了，而是继续进行以保持向后兼容性。

> 注 3
>
> splice功能是有意通用的。它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.29 Array.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-array.prototype.tolocalestring"></div>

包含ECMA-402国际化API的ECMAScript实现必须实现ECMA-402规范中指定的Array.prototype.toLocaleString方法。如果ECMAScript实现不包含ECMA-402 API，则使用以下toLocaleString方法的规范。

> 注 1
>
> ECMA-402的第一版未包含Array.prototype.toLocaleString方法的替代规范。

ECMA-402规范中定义了此方法的可选参数的含义；不包含ECMA-402支持的实现不得将这些参数位置用于其他任何用途。

采取以下步骤：

1. 令 array 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(array, "length")).
3. 令 separator 为列表分隔符String的String值，该值适用于主机环境的当前语言环境（以实现定义的方式派生）。
4. 令 R 为空字符串.
5. 令 k 为 0.
6. 重复，直到 k < len
   1. 若 k > 0，那么
      1. 设置 R 为R和分隔符的字符串连接。
   2. 令 nextElement 为 ? Get(array, ! ToString(k)).
   3. 若 nextElement 不是 undefined 或吧 null，那么
      1. 令 S 为 ? ToString(? Invoke(nextElement, "toLocaleString")).
      2. 设置 R 为R和S的字符串连接。
   4. k 增加 1。
7. 返回 R.

> 注 2
>
> 数组的元素使用其toLocaleString方法转换为String，然后将这些String串联起来，并通过出现以实现定义的特定于区域的方式派生的分隔符String进行分隔。调用此函数的结果旨在类似于“ toString”的结果，不同之处在于此函数的结果旨在特定于语言环境。

> 注 3
>
> toLocaleString函数是有意通用的。它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.30 Array.prototype.toString ( ) <div id="sec-array.prototype.tostring"></div>

当调用toString方法时，采取以下步骤：

1. 令 array 为 ? ToObject(this value).
2. 令 func 为 ? Get(array, "join").
3. 若 IsCallable(func) 是 false，将func设置为固有函数％ObjProto_toString％
4. 返回 ? Call(func, array).

> 注
>
> toString函数是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.31 Array.prototype.unshift ( ...items ) <div id="sec-array.prototype.unshift"></div>

> 注 1
>
> 自变量位于数组的开头，因此它们在数组中的顺序与在自变量列表中出现的顺序相同。

当使用零个或多个参数item1，item2等调用unshift方法时，采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 令 len 为 ? ToLength(? Get(O, "length")).
3. 令 argCount 为 the number of actual arguments.
4. 若 argCount > 0，那么
   1. 若 len + argCount > 253 - 1, 抛出 TypeError 异常
   2. 令 k 为 len.
   3. 重复，直到 k > 0,
      1. 令 from 为 ! ToString(k - 1).
      2. 令 to 为 ! ToString(k + argCount - 1).
      3. 令 fromPresent 为 ? HasProperty(O, from).
      4. 若 fromPresent 是 true，那么
         1. 令 fromValue 为 ? Get(O, from).
         2. 执行 ? Set(O, to, fromValue, true).
      5. 否则fromPresent is false,
         1. 执行 ? DeletePropertyOrThrow(O, to).
      6. k 减少 1。
   4. 令 j 为 0.
   5. 令 items 为一个列表，其元素按从左到右的顺序是传递给此函数调用的参数。
   7. 重复，直到 items 不为空
      1. 从 items 中删除第一个元素， 并且令 E 为该元素的值。
      2. 执行 ? Set(O, ! ToString(j), E, true).
      3. j 增加 1。
5. 执行 ? Set(O, "length", len + argCount, true).
6. 返回 len + argCount.

unshift方法的“ length”属性为1。

> 注 2
>
> “ unshift”功能是有意通用的；它不需要此值是Array对象。因此，可以将其转移到其他类型的对象中作为方法。

#### 22.1.3.32 Array.prototype.values ( ) <div id="sec-array.prototype.values"></div>

采取以下步骤：

1. 令 O 为 ? ToObject(this value).
2. 返回 CreateArrayIterator(O, `"value"`).

此函数是％ArrayProto_values％内部对象。

#### 22.1.3.33 Array.prototype [ @@iterator ] ( ) <div id="sec-array.prototype-@@iterator"></div>

@@iterator属性的初始值与Array.prototype.values属性的初始值是同一函数对象。

#### 22.1.3.34 Array.prototype [ @@unscopables ] <div id="sec-array.prototype-@@unscopables"></div>

@@unscopables数据属性的初始值是通过以下步骤创建的对象：

1. 令 unscopableList 为 ObjectCreate(null).
2. 执行 CreateDataProperty(unscopableList, "copyWithin", true).
3. 执行 CreateDataProperty(unscopableList, "entries", true).
4. 执行 CreateDataProperty(unscopableList, "fill", true).
5. 执行 CreateDataProperty(unscopableList, "find", true).
6. 执行 CreateDataProperty(unscopableList, "findIndex", true).
7. 执行 CreateDataProperty(unscopableList, "flat", true).
8. 执行 CreateDataProperty(unscopableList, "flatMap", true).
9. 执行 CreateDataProperty(unscopableList, "includes", true).
10. 执行 CreateDataProperty(unscopableList, "keys", true).
11. 执行 CreateDataProperty(unscopableList, "values", true).
12. 断言：Each of the above calls 返回s true.
13. 返回 unscopableList.

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

> 注
>
> 该对象自己的属性名称是ECMAScript 2015规范之前未包含在Array.prototype的标准属性中的属性名称。出于“ with”语句绑定的目的，这些名称被忽略，以保留现有代码的行为，这些代码可能将这些名称之一用作外部作用域中的绑定，而外部作用域由“ with”语句遮蔽，该对象的绑定对象是一个Array对象。 。

### 22.1.4 Array 实例属性 <div id="sec-properties-of-array-instances"></div>

数组实例是数组奇异对象，并具有为此类对象指定的内部方法。数组实例从Array原型对象继承属性。

数组实例具有“ length”属性和一组带有数组索引名称的可枚举属性。

#### 22.1.4.1 length <div id="sec-properties-of-array-instances-length"></div>

Array实例的“ length”属性是一个数据属性，其值在数值上总是大于名称为数组索引的每个可配置自己的属性的名称。

“ length”属性最初具有属性 { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.

> 注
>
> 减小“ length”属性的值具有删除自己的数组元素（其数组索引在新旧长度值之间）的副作用。但是，无法删除不可配置的属性。尝试将Array对象的“ length”属性设置为一个数值，该数值在数值上小于或等于该数组现有不可配置的数组索引属性的最大数值自身属性名称，这将导致该长度为设置为一个数字值，该数字值比不可配置的数字自身属性名称大一。见9.4.2.1。

### 22.1.5 Array 迭代器对象 <div id="sec-array-iterator-objects"></div>

数组迭代器是一个对象，它表示对某些特定Array实例对象的特定迭代。数组迭代器对象没有命名的构造函数。而是，通过调用Array实例对象的某些方法来创建Array迭代器对象。

#### 22.1.5.1 CreateArrayIterator ( array, kind ) <div id="sec-createarrayiterator"></div>

Array对象的几种方法返回Iterator对象。具有参数array和kind的抽象操作CreateArrayIterator用于创建此类迭代器对象。它执行以下步骤：

1. 断言：Type(array) 是 Object.
2. 令 iterator 为 ObjectCreate(%ArrayIteratorPrototype%, « [[IteratedObject]], [[ArrayIteratorNextIndex]], [[ArrayIterationKind]] »).
3. 设置 iterator.[[IteratedObject]] 为 array.
4. 设置 iterator.[[ArrayIteratorNextIndex]] 为 0.
5. 设置 iterator.[[ArrayIterationKind]] 为 kind.
6. 返回 iterator.

#### 22.1.5.2 The %ArrayIteratorPrototype% Object <div id="sec-%arrayiteratorprototype%-object"></div>

％ArrayIterator原型％对象：

- 具有所有数组迭代器对象继承的属性。
- 是一个普通的对象。
- 有一个[[Prototype]]内部插槽，其值是固有对象％IteratorPrototype％。
- 具有以下属性：

##### 22.1.5.2.1 %ArrayIteratorPrototype%.next ( ) <div id="sec-%arrayiteratorprototype%.next"></div>

1. 令 O 为 this 值
2. 若 Type(O) 不是 Object, 抛出 TypeError 异常
3. 若 O 没有数组迭代器实例（22.1.5.3）的所有内部插槽，抛出 TypeError 异常
4. 令 a 为 O.[[IteratedObject]].
5. 若 a 是 undefined, 返回 CreateIterResultObject(undefined, true).
6. 令 index 为 O.[[ArrayIteratorNextIndex]].
7. 令 itemKind 为 O.[[ArrayIterationKind]].
8. 若 a 有 [[TypedArrayName]] 内部插槽，那么
   1. 若 IsDetachedBuffer(a.[[ViewedArrayBuffer]]) 是 true, 抛出 TypeError 异常
   2. 令 len 为 a.[[ArrayLength]].
9. 否则，
   1. 令 len 为 ? ToLength(? Get(a, "length")).
10. 若 index ≥ len，那么
    1. 设置 O.[[IteratedObject]] 为 undefined.
    2. 返回 CreateIterResultObject(undefined, true).
11. 设置 O.[[ArrayIteratorNextIndex]] 为 index + 1.
12. 若 itemKind 是 "key", 返回 CreateIterResultObject(index, false).
13. 令 elementKey 为 ! ToString(index).
14. 令 elementValue 为 ? Get(a, elementKey).
15. 若 itemKind 是 "value", 令 result 为 elementValue.
16. 否则，
    1. 断言：itemKind is "key+value".
    2. 令 result 为 CreateArrayFromList(« index, elementValue »).
17. 返回 CreateIterResultObject(result, false).

##### 22.1.5.2.2 %ArrayIteratorPrototype% [ @@toStringTag ] <div id="sec-%arrayiteratorprototype%-@@tostringtag"></div>

@@toStringTag属性的初始值为字符串值“数组迭代器”。

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 22.1.5.3 Properties of Array Iterator Instances <div id="sec-properties-of-array-iterator-instances"></div>

数组迭代器实例是从％ArrayIteratorPrototype％内部对象继承属性的普通对象。最初使用表58中列出的内部插槽创建阵列迭代器实例。

表58：数组迭代器实例的内部插槽

| 内部插槽                   | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| [[IteratedObject]]         | 要迭代其数组元素的对象。                                     |
| [[ArrayIteratorNextIndex]] | 此迭代要检查的下一个整数索引的整数索引。                     |
| [[ArrayIterationKind]]     | 一个String值，用于标识迭代的每个元素返回的内容。可能的值为： `"key"`, `"value"`, `"key+value"`. |

## 22.2 类数组对象 <div id="sec-typedarray-objects"></div>

TypedArray objects present an array-like view of an underlying binary data buffer (24.1). Each element of a TypedArray instance has the same underlying binary scalar data type. There is a distinct TypedArray constructor, listed in Table 59, for each of the nine supported element types. Each constructor in Table 59 has a corresponding distinct prototype object.

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

The %TypedArray% constructor performs the following steps:

1. 抛出 TypeError 异常

The `"length"` property of the %TypedArray% constructor function is 0.

### 22.2.2 %TypedArray% 内部对象属性 <div id="sec-properties-of-the-%typedarray%-intrinsic-object"></div>

The %TypedArray% intrinsic object:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has a `name` property whose value is `"TypedArray"`.
- has the following properties:

#### 22.2.2.1 %TypedArray%.from ( source [ , mapfn [ , thisArg ] ] ) <div id="sec-%typedarray%.from"></div>

When the from method is called with argument source, and optional arguments mapfn and thisArg, 采取以下步骤：

1. 令 C 为 this 值
2. 若 IsConstructor(C) 是 false, 抛出 TypeError 异常
3. 若 mapfn is present and mapfn 不是 undefined，那么
   1. 若 IsCallable(mapfn) 是 false, 抛出 TypeError 异常
   2. 令 mapping 为 true.
4. 否则，令 mapping 为 false.
5. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
6. 令 usingIterator 为 ? GetMethod(source, @@iterator).
7. 若 usingIterator 不是 undefined，那么
   1. 令 values 为 ? IterableToList(source, usingIterator).
   2. 令 len 为 the number of elements in values.
   3. 令 targetObj 为 ? TypedArrayCreate(C, « len »).
   4. 令 k 为 0.
   5. 重复，直到 k < len
      1. 令 Pk 为 ! ToString(k).
      2. 令 kValue 为 the first element of values and remove that element from values.
      3. 若 mapping 是 true，那么
         1. 令 mappedValue 为 ? Call(mapfn, T, « kValue, k »).
      4. 否则，令 mappedValue 为 kValue.
      5. 执行 ? Set(targetObj, Pk, mappedValue, true).
      6. k 增加 1。
   6. 断言：values is now an empty List.
   7. 返回 targetObj.
8. NOTE: source is not an Iterable so assume it is already an array-like object.
9. 令 arrayLike 为 ! ToObject(source).
10. 令 len 为 ? ToLength(? Get(arrayLike, "length")).
11. 令 targetObj 为 ? TypedArrayCreate(C, « len »).
12. 令 k 为 0.
13. 重复，直到 k < len
    1. 令 Pk 为 ! ToString(k).
    2. 令 kValue 为 ? Get(arrayLike, Pk).
    3. 若 mapping 是 true，那么
       1. 令 mappedValue 为 ? Call(mapfn, T, « kValue, k »).
    4. 否则，令 mappedValue 为 kValue.
    5. 执行 ? Set(targetObj, Pk, mappedValue, true).
    6. k 增加 1。
14. 返回 targetObj.

##### 22.2.2.1.1Runtime Semantics: IterableToList ( items, method )<div id="sec-iterabletolist"></div>

The abstract operation IterableToList performs the following steps:

1. 令 iteratorRecord 为 ? GetIterator(items, sync, method).
2. 令 values 为 a new empty List.
3. 令 next 为 true.
4. 重复，直到 next is not false
   1. 设置 next 为 ? IteratorStep(iteratorRecord).
   2. 若 next 不是 false，那么
      1. 令 nextValue 为 ? IteratorValue(next).
      2. Append nextValue to the end of the List values.
5. 返回 values.

#### 22.2.2.2 %TypedArray%.of ( ...items ) <div id="sec-%typedarray%.of"></div>

When the `of` method is called with any number of arguments, 采取以下步骤：

1. 令 len 为 the actual number of arguments passed to this function.
2. 令 items 为 the List of arguments passed to this function.
3. 令 C 为 this 值
4. 若 IsConstructor(C) 是 false, 抛出 TypeError 异常
5. 令 newObj 为 ? TypedArrayCreate(C, « len »).
6. 令 k 为 0.
7. 重复，直到 k < len
   1. 令 kValue 为 items[k].
   2. 令 Pk 为 ! ToString(k).
   3. 执行 ? Set(newObj, Pk, kValue, true).
   4. k 增加 1。
8. 返回 newObj.

> 注
>
> The items argument is assumed to be a well-formed rest argument value.

#### 22.2.2.3 %TypedArray%.prototype <div id="sec-%typedarray%.prototype"></div>

The initial value of %TypedArray%`.prototype` is the %TypedArrayPrototype% intrinsic object.

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 22.2.2.4 get %TypedArray% [ @@species ] <div id="sec-get-%typedarray%-@@species"></div>

%TypedArray%`[@@species]` is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 返回 this 值

The value of the `name` property of this function is `"get [Symbol.species]"`.

> 注
>
>%TypedArrayPrototype% methods normally use their `this` object's constructor to create a derived object. However, a subclass constructor may over-ride that default behaviour by redefining its @@species property.

### 22.2.3 %TypedArrayPrototype% 对象属性 <div id="sec-properties-of-the-%typedarrayprototype%-object"></div>

The %TypedArrayPrototype% object:

- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.
- is an ordinary object.
- does not have a [[ViewedArrayBuffer]] or any other of the internal slots that are specific to TypedArray instance objects.

#### 22.2.3.1 获取 %TypedArray%.prototype.buffer <div id="sec-get-%typedarray%.prototype.buffer"></div>

%TypedArray%.prototype.buffer is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 O 为 this 值
2. 若 Type(O) 不是 Object, 抛出 TypeError 异常
3. 若 O does not have a [[TypedArrayName]] internal slot, 抛出 TypeError 异常
4. 断言：O has a [[ViewedArrayBuffer]] internal slot.
5. 令 buffer 为 O.[[ViewedArrayBuffer]].
6. 返回 buffer.

#### 22.2.3.2 获取 %TypedArray%.prototype.byteLength <div id="sec-get-%typedarray%.prototype.bytelength"></div>

%TypedArray%.prototype.byteLength is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 O 为 this 值
2. 若 Type(O) 不是 Object, 抛出 TypeError 异常
3. 若 O does not have a [[TypedArrayName]] internal slot, 抛出 TypeError 异常
4. 断言：O has a [[ViewedArrayBuffer]] internal slot.
5. 令 buffer 为 O.[[ViewedArrayBuffer]].
6. 若 IsDetachedBuffer(buffer) 是 true, 返回 0.
7. 令 size 为 O.[[ByteLength]].
8. 返回 size.

#### 22.2.3.3 获取 %TypedArray%.prototype.byteOffset <div id="sec-get-%typedarray%.prototype.byteoffset"></div>

%TypedArray%.prototype.byteOffset is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 O 为 this 值
2. 若 Type(O) 不是 Object, 抛出 TypeError 异常
3. 若 O does not have a [[TypedArrayName]] internal slot, 抛出 TypeError 异常
4. 断言：O has a [[ViewedArrayBuffer]] internal slot.
5. 令 buffer 为 O.[[ViewedArrayBuffer]].
6. 若 IsDetachedBuffer(buffer) 是 true, 返回 0.
7. 令 offset 为 O.[[ByteOffset]].
8. 返回 offset.

#### 22.2.3.4 %TypedArray%.prototype.constructor <div id="sec-%typedarray%.prototype.constructor"></div>

The initial value of %TypedArray%`.prototype.constructor` is the %TypedArray% intrinsic object.

#### 22.2.3.5 %TypedArray%.prototype.copyWithin ( target, start [ , end ] ) <div id="sec-%typedarray%.prototype.copywithin"></div>

The interpretation and use of the arguments of %TypedArray% `.prototype.copyWithin` are the same as for `Array.prototype.copyWithin` as defined in 22.1.3.3.

采取以下步骤：

1. 令 O 为 this value.
2. 执行 ? ValidateTypedArray(O).
3. 令 len 为 O.[[ArrayLength]].
4. 令 relativeTarget 为 ? ToInteger(target).
5. 若 relativeTarget < 0, 令 to 为 max((len + relativeTarget), 0); 否则令 to 为 min(relativeTarget, len).
6. 令 relativeStart 为 ? ToInteger(start).
7. 若 relativeStart < 0, 令 from 为 max((len + relativeStart), 0); 否则令 from 为 min(relativeStart, len).
8. 若 end 是 undefined, 令 relativeEnd 为 len; 否则令 relativeEnd 为 ? ToInteger(end).
9. 若 relativeEnd < 0, 令 final 为 max((len + relativeEnd), 0); 否则令 final 为 min(relativeEnd, len).
10. 令 count 为 min(final - from, len - to).
11. 若 count > 0，那么
    1. NOTE: The copying must be performed in a manner that preserves the bit-level encoding of the source data.
    2. 令 buffer 为 O.[[ViewedArrayBuffer]].
    3. 若 IsDetachedBuffer(buffer) 是 true, 抛出 TypeError 异常
    4. 令 typedArrayName 为 the String value of O.[[TypedArrayName]].
    5. 令 elementSize 为 the Number value of the Element Size value specified in Table 59 for typedArrayName.
    6. 令 byteOffset 为 O.[[ByteOffset]].
    7. 令 toByteIndex 为 to × elementSize + byteOffset.
    8. 令 fromByteIndex 为 from × elementSize + byteOffset.
    9. 令 countBytes 为 count × elementSize.
    10. 若 fromByteIndex < toByteIndex and toByteIndex < fromByteIndex + countBytes，那么
        1. 令 direction 为 -1.
        2. 设置 fromByteIndex 为 fromByteIndex + countBytes - 1.
        3. 设置 toByteIndex 为 toByteIndex + countBytes - 1.
    11. 否则，
        1. 令 direction 为 1.
    12. 重复，直到 countBytes > 0
        1. 令 value 为 GetValueFromBuffer(buffer, fromByteIndex, "Uint8", true, "Unordered").
        2. 执行 SetValueInBuffer(buffer, toByteIndex, "Uint8", value, true, "Unordered").
        3. 设置 fromByteIndex 为 fromByteIndex + direction.
        4. 设置 toByteIndex 为 toByteIndex + direction.
        5. countBytes 减少 1。
12. 返回 O.

##### 22.2.3.5.1 RS: ValidateTypedArray ( O ) <div id="sec-validatetypedarray"></div>

When called with argument O, 采取以下步骤：

1. 若 Type(O) 不是 Object, 抛出 TypeError 异常
2. 若 O does not have a [[TypedArrayName]] internal slot, 抛出 TypeError 异常
3. 断言：O has a [[ViewedArrayBuffer]] internal slot.
4. 令 buffer 为 O.[[ViewedArrayBuffer]].
5. 若 IsDetachedBuffer(buffer) 是 true, 抛出 TypeError 异常
6. 返回 buffer.

#### 22.2.3.6 %TypedArray%.prototype.entries ( ) <div id="sec-%typedarray%.prototype.entries"></div>

采取以下步骤：

1. 令 O 为 this 值
2. 执行 ? ValidateTypedArray(O).
3. 返回 CreateArrayIterator(O, "key+value").

#### 22.2.3.7 %TypedArray%.prototype.every ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.every"></div>

%TypedArray%`.prototype.every` is a distinct function that implements the same algorithm as `Array.prototype.every` as defined in 22.1.3.5 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.8 %TypedArray%.prototype.fill ( value [ , start [ , end ] ] ) <div id="sec-%typedarray%.prototype.fill"></div>

The interpretation and use of the arguments of %TypedArray%.prototype.fill are the same as for Array.prototype.fill as defined in 22.1.3.6.

采取以下步骤：

1. 令 O 为 this 值
2. 执行 ? ValidateTypedArray(O).
3. 令 len 为 O.[[ArrayLength]].
4. 设置 value 为 ? ToNumber(value).
5. 令 relativeStart 为 ? ToInteger(start).
6. 若 relativeStart < 0, 令 k 为 max((len + relativeStart), 0); 否则令 k 为 min(relativeStart, len).
7. 若 end 是 undefined, 令 relativeEnd 为 len; 否则令 relativeEnd 为 ? ToInteger(end).
8. 若 relativeEnd < 0, 令 final 为 max((len + relativeEnd), 0); 否则令 final 为 min(relativeEnd, len).
9. 若 IsDetachedBuffer(O.[[ViewedArrayBuffer]]) 是 true, 抛出 TypeError 异常
10. 重复，直到 k < final
    1. 令 Pk 为 ! ToString(k).
    2. 执行 ! Set(O, Pk, value, true).
    3. k 增加 1。
11. 返回 O.

#### 22.2.3.9 %TypedArray%.prototype.filter ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.filter"></div>

The interpretation and use of the arguments of %TypedArray%.prototype.filter are the same as for Array.prototype.filter as defined in 22.1.3.7.

When the filter method is called with one or two arguments, 采取以下步骤：

1. 令 O 为 this 值
2. 执行 ? ValidateTypedArray(O).
3. 令 len 为 O.[[ArrayLength]].
4. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
5. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
6. 令 kept 为 a new empty List.
7. 令 k 为 0.
8. 令 captured 为 0.
9. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kValue 为 ? Get(O, Pk).
   3. 令 selected 为 ToBoolean(? Call(callbackfn, T, « kValue, k, O »)).
   4. 若 selected 是 true，那么
      1. Append kValue to the end of kept.
      2. captured 增加 1。
   5. k 增加 1。
10. 令 A 为 ? TypedArraySpeciesCreate(O, « captured »).
11. 令 n 为 0.
12. For each element e of kept, do
    1. 执行 ! Set(A, ! ToString(n), e, true).
    2. Increment n by 1.
13. 返回 A.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.10 %TypedArray%.prototype.find ( predicate [ , thisArg ] ) <div id="sec-%typedarray%.prototype.find"></div>

%TypedArray%`.prototype.find` is a distinct function that implements the same algorithm as `Array.prototype.find` as defined in 22.1.3.8 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to predicate may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.11 %TypedArray%.prototype.findIndex ( predicate [ , thisArg ] ) <div id="sec-%typedarray%.prototype.findindex"></div>

%TypedArray%`.prototype.findIndex` is a distinct function that implements the same algorithm as `Array.prototype.findIndex` as defined in 22.1.3.9 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to predicate may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.12 %TypedArray%.prototype.forEach ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.foreach"></div>

%TypedArray%`.prototype.forEach` is a distinct function that implements the same algorithm as `Array.prototype.forEach` as defined in 22.1.3.12 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.13 %TypedArray%.prototype.includes ( searchElement [ , fromIndex ] ) <div id="sec-%typedarray%.prototype.includes"></div>

%TypedArray%`.prototype.includes` is a distinct function that implements the same algorithm as `Array.prototype.includes` as defined in 22.1.3.13 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.14 %TypedArray%.prototype.indexOf ( searchElement [ , fromIndex ] ) <div id="sec-%typedarray%.prototype.indexof"></div>

%TypedArray%`.prototype.indexOf` is a distinct function that implements the same algorithm as `Array.prototype.indexOf` as defined in 22.1.3.14 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.15 %TypedArray%.prototype.join ( separator ) <div id="sec-%typedarray%.prototype.join"></div>

%TypedArray%`.prototype.join` is a distinct function that implements the same algorithm as `Array.prototype.join` as defined in 22.1.3.15 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.16 %TypedArray%.prototype.keys ( ) <div id="sec-%typedarray%.prototype.keys"></div>

采取以下步骤：

1. 令 O 为 this 值
2. 执行 ? ValidateTypedArray(O).
3. 返回 CreateArrayIterator(O, "key").

#### 22.2.3.17 %TypedArray%.prototype.lastIndexOf ( searchElement [ , fromIndex ] ) <div id="sec-%typedarray%.prototype.lastindexof"></div>

%TypedArray%`.prototype.lastIndexOf` is a distinct function that implements the same algorithm as `Array.prototype.lastIndexOf` as defined in 22.1.3.17 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.18 获取 %TypedArray%.prototype.length <div id="sec-get-%typedarray%.prototype.length"></div>

%TypedArray%.prototype.length is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 O 为 this 值
2. 若 Type(O) 不是 Object, 抛出 TypeError 异常
3. 若 O does not have a [[TypedArrayName]] internal slot, 抛出 TypeError 异常
4. 断言：O has [[ViewedArrayBuffer]] and [[ArrayLength]] internal slots.
5. 令 buffer 为 O.[[ViewedArrayBuffer]].
6. 若 IsDetachedBuffer(buffer) 是 true, 返回 0.
7. 令 length 为 O.[[ArrayLength]].
8. 返回 length.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.19 %TypedArray%.prototype.map ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.map"></div>

The interpretation and use of the arguments of %TypedArray%.prototype.map are the same as for Array.prototype.map as defined in 22.1.3.18.

When the map method is called with one or two arguments, 采取以下步骤：

1. 令 O 为 this 值
2. 执行 ? ValidateTypedArray(O).
3. 令 len 为 O.[[ArrayLength]].
4. 若 IsCallable(callbackfn) 是 false, 抛出 TypeError 异常
5. 若 thisArg 存在, 令 T 为 thisArg; 否则令 T 为 undefined.
6. 令 A 为 ? TypedArraySpeciesCreate(O, « len »).
7. 令 k 为 0.
8. 重复，直到 k < len
   1. 令 Pk 为 ! ToString(k).
   2. 令 kValue 为 ? Get(O, Pk).
   3. 令 mappedValue 为 ? Call(callbackfn, T, « kValue, k, O »).
   4. 执行 ? Set(A, Pk, mappedValue, true).
   5. k 增加 1。
9. 返回 A.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.20 %TypedArray%.prototype.reduce ( callbackfn [ , initialValue ] ) <div id="sec-%typedarray%.prototype.reduce"></div>

%TypedArray%`.prototype.reduce` is a distinct function that implements the same algorithm as `Array.prototype.reduce` as defined in 22.1.3.21 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.21 %TypedArray%.prototype.reduceRight ( callbackfn [ , initialValue ] ) <div id="sec-%typedarray%.prototype.reduceright"></div>

%TypedArray%`.prototype.reduceRight` is a distinct function that implements the same algorithm as `Array.prototype.reduceRight` as defined in 22.1.3.22 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.22 %TypedArray%.prototype.reverse ( ) <div id="sec-%typedarray%.prototype.reverse"></div>

%TypedArray%`.prototype.reverse` is a distinct function that implements the same algorithm as `Array.prototype.reverse` as defined in 22.1.3.23 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.23 %TypedArray%.prototype.set ( overloaded [ , offset ] ) <div id="sec-%typedarray%.prototype.set-overloaded-offset"></div>

%TypedArray%`.prototype.set` is a single function whose behaviour is overloaded based upon the type of its first argument.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

##### 22.2.3.23.1 %TypedArray%.prototype.set ( array [ , offset ] ) <div id="sec-%typedarray%.prototype.set-array-offset"></div>

Sets multiple values in this TypedArray, reading the values from the object array. The optional offset value indicates the first element index in this TypedArray where values are written. If omitted, it is assumed to be 0.

1. 断言：array is any ECMAScript language value other than an Object with a [[TypedArrayName]] internal slot. If it is such an Object, the definition in 22.2.3.23.2 applies.
2. 令 target 为 this 值
3. 若 Type(target) 不是 Object, 抛出 TypeError 异常
4. 若 target does not have a [[TypedArrayName]] internal slot, 抛出 TypeError 异常
5. 断言：target has a [[ViewedArrayBuffer]] internal slot.
6. 令 targetOffset 为 ? ToInteger(offset).
7. 若 targetOffset < 0, 抛出 RangeError 异常
8. 令 targetBuffer 为 target.[[ViewedArrayBuffer]].
9. 若 IsDetachedBuffer(targetBuffer) 是 true, 抛出 TypeError 异常
10. 令 targetLength 为 target.[[ArrayLength]].
11. 令 targetName 为 the String value of target.[[TypedArrayName]].
12. 令 targetElementSize 为 the Number value of the Element Size value specified in Table 59 for targetName.
13. 令 targetType 为 the String value of the Element Type value in Table 59 for targetName.
14. 令 targetByteOffset 为 target.[[ByteOffset]].
15. 令 src 为 ? ToObject(array).
16. 令 srcLength 为 ? ToLength(? Get(src, "length")).
17. 若 srcLength + targetOffset > targetLength, 抛出 RangeError 异常
18. 令 targetByteIndex 为 targetOffset × targetElementSize + targetByteOffset.
19. 令 k 为 0.
20. 令 limit 为 targetByteIndex + targetElementSize × srcLength.
21. 重复，直到 targetByteIndex < limit
    1. 令 Pk 为 ! ToString(k).
    2. 令 kNumber 为 ? ToNumber(? Get(src, Pk)).
    3. 若 IsDetachedBuffer(targetBuffer) 是 true, 抛出 TypeError 异常
    4. 执行 SetValueInBuffer(targetBuffer, targetByteIndex, targetType, kNumber, true, "Unordered").
    5. k 增加 1。
    6. 设置 targetByteIndex 为 targetByteIndex + targetElementSize.
22. 返回 undefined.

##### 22.2.3.23.2 %TypedArray%.prototype.set ( typedArray [ , offset ] ) <div id="sec-%typedarray%.prototype.set-typedarray-offset"></div>

Sets multiple values in this TypedArray, reading the values from the typedArray argument object. The optional offset value indicates the first element index in this TypedArray where values are written. If omitted, it is assumed to be 0.

1. 断言：typedArray has a [[TypedArrayName]] internal slot. If it does not, the definition in 22.2.3.23.1 applies.
2. 令 target 为 this 值
3. 若 Type(target) 不是 Object, 抛出 TypeError 异常
4. 若 target does not have a [[TypedArrayName]] internal slot, 抛出 TypeError 异常
5. 断言：target has a [[ViewedArrayBuffer]] internal slot.
6. 令 targetOffset 为 ? ToInteger(offset).
7. 若 targetOffset < 0, 抛出 RangeError 异常
8. 令 targetBuffer 为 target.[[ViewedArrayBuffer]].
9. 若 IsDetachedBuffer(targetBuffer) 是 true, 抛出 TypeError 异常
10. 令 targetLength 为 target.[[ArrayLength]].
11. 令 srcBuffer 为 typedArray.[[ViewedArrayBuffer]].
12. 若 IsDetachedBuffer(srcBuffer) 是 true, 抛出 TypeError 异常
13. 令 targetName 为 the String value of target.[[TypedArrayName]].
14. 令 targetType 为 the String value of the Element Type value in Table 59 for targetName.
15. 令 targetElementSize 为 the Number value of the Element Size value specified in Table 59 for targetName.
16. 令 targetByteOffset 为 target.[[ByteOffset]].
17. 令 srcName 为 the String value of typedArray.[[TypedArrayName]].
18. 令 srcType 为 the String value of the Element Type value in Table 59 for srcName.
19. 令 srcElementSize 为 the Number value of the Element Size value specified in Table 59 for srcName.
20. 令 srcLength 为 typedArray.[[ArrayLength]].
21. 令 srcByteOffset 为 typedArray.[[ByteOffset]].
22. 若 srcLength + targetOffset > targetLength, 抛出 RangeError 异常
23. 若 both IsSharedArrayBuffer(srcBuffer) and IsSharedArrayBuffer(targetBuffer) are true，那么
    1. 若 srcBuffer.[[ArrayBufferData]] and targetBuffer.[[ArrayBufferData]] are the same Shared Data Block values, 令 same 为 true; 否则令 same 为 false.
24. 否则，令 same 为 SameValue(srcBuffer, targetBuffer).
25. 若 same 是 true，那么
    1. 令 srcByteLength 为 typedArray.[[ByteLength]].
    2. 设置 srcBuffer 为 ? CloneArrayBuffer(srcBuffer, srcByteOffset, srcByteLength, %ArrayBuffer%).
    3. NOTE: %ArrayBuffer% is used to clone srcBuffer because is it known to not have any observable side-effects.
    4. 令 srcByteIndex 为 0.
26. 否则，令 srcByteIndex 为 srcByteOffset.
27. 令 targetByteIndex 为 targetOffset × targetElementSize + targetByteOffset.
28. 令 limit 为 targetByteIndex + targetElementSize × srcLength.
29. 若 SameValue(srcType, targetType) 是 true，那么
    1. NOTE: If srcType and targetType are the same, the transfer must be performed in a manner that preserves the bit-level encoding of the source data.
    2. 重复，直到 targetByteIndex < limit
       1. 令 value 为 GetValueFromBuffer(srcBuffer, srcByteIndex, "Uint8", true, "Unordered").
       2. 执行 SetValueInBuffer(targetBuffer, targetByteIndex, "Uint8", value, true, "Unordered").
       3. srcByteIndex 增加 1。
       4. targetByteIndex 增加 1。
30. 否则，
    1. 重复，直到 targetByteIndex < limit
       1. 令 value 为 GetValueFromBuffer(srcBuffer, srcByteIndex, srcType, true, "Unordered").
       2. 执行 SetValueInBuffer(targetBuffer, targetByteIndex, targetType, value, true, "Unordered").
       3. 设置 srcByteIndex 为 srcByteIndex + srcElementSize.
       4. 设置 targetByteIndex 为 targetByteIndex + targetElementSize.
31. 返回 undefined.

#### 22.2.3.24 %TypedArray%.prototype.slice ( start, end ) <div id="sec-%typedarray%.prototype.slice"></div>

The interpretation and use of the arguments of %TypedArray%.prototype.slice are the same as for Array.prototype.slice as defined in 22.1.3.25. 采取以下步骤：

1. 令 O 为 this 值
2. 执行 ? ValidateTypedArray(O).
3. 令 len 为 O.[[ArrayLength]].
4. 令 relativeStart 为 ? ToInteger(start).
5. 若 relativeStart < 0, 令 k 为 max((len + relativeStart), 0); 否则令 k 为 min(relativeStart, len).
6. 若 end 是 undefined, 令 relativeEnd 为 len; 否则令 relativeEnd 为 ? ToInteger(end).
7. 若 relativeEnd < 0, 令 final 为 max((len + relativeEnd), 0); 否则令 final 为 min(relativeEnd, len).
8. 令 count 为 max(final - k, 0).
9. 令 A 为 ? TypedArraySpeciesCreate(O, « count »).
10. 令 srcName 为 the String value of O.[[TypedArrayName]].
11. 令 srcType 为 the String value of the Element Type value in Table 59 for srcName.
12. 令 targetName 为 the String value of A.[[TypedArrayName]].
13. 令 targetType 为 the String value of the Element Type value in Table 59 for targetName.
14. 若 SameValue(srcType, targetType) 是 false，那么
    1. 令 n 为 0.
    2. 重复，直到 k < final
       1. 令 Pk 为 ! ToString(k).
       2. 令 kValue 为 ? Get(O, Pk).
       3. 执行 ! Set(A, ! ToString(n), kValue, true).
       4. k 增加 1。
       5. n 增加 1。
15. 否则if count > 0，那么
    1. 令 srcBuffer 为 O.[[ViewedArrayBuffer]].
    2. 若 IsDetachedBuffer(srcBuffer) 是 true, 抛出 TypeError 异常
    3. 令 targetBuffer 为 A.[[ViewedArrayBuffer]].
    4. 令 elementSize 为 the Number value of the Element Size value specified in Table 59 for srcType.
    5. NOTE: If srcType and targetType are the same, the transfer must be performed in a manner that preserves the bit-level encoding of the source data.
    6. 令 srcByteOffet 为 O.[[ByteOffset]].
    7. 令 targetByteIndex 为 A.[[ByteOffset]].
    8. 令 srcByteIndex 为 (k × elementSize) + srcByteOffet.
    9. 令 limit 为 targetByteIndex + count × elementSize.
    10. 重复，直到 targetByteIndex < limit
        1. 令 value 为 GetValueFromBuffer(srcBuffer, srcByteIndex, "Uint8", true, "Unordered").
        2. 执行 SetValueInBuffer(targetBuffer, targetByteIndex, "Uint8", value, true, "Unordered").
        3. srcByteIndex 增加 1。
        4. targetByteIndex 增加 1。
16. 返回 A.

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.25 %TypedArray%.prototype.some ( callbackfn [ , thisArg ] ) <div id="sec-%typedarray%.prototype.some"></div>

%TypedArray%`.prototype.some` is a distinct function that implements the same algorithm as `Array.prototype.some` as defined in 22.1.3.26 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm and must take into account the possibility that calls to callbackfn may cause the this value to become detached.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

#### 22.2.3.26 %TypedArray%.prototype.sort ( comparefn ) <div id="sec-%typedarray%.prototype.sort"></div>

%TypedArray%.prototype.sort is a distinct function that, except as described below, implements the same requirements as those of Array.prototype.sort as defined in 22.1.3.27. The implementation of the %TypedArray%.prototype.sort specification may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. The only internal methods of the this object that the algorithm may call are [[Get]] and [[Set]].

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

Upon entry, the following steps are performed to initialize evaluation of the sort function. These steps are used instead of the entry steps in 22.1.3.27:

1. 若 comparefn is not undefined and IsCallable(comparefn) 是 false, 抛出 TypeError 异常
2. 令 obj 为 this 值
3. 令 buffer 为 ? ValidateTypedArray(obj).
4. 令 len 为 obj.[[ArrayLength]].

The implementation-defined sort order condition for exotic objects is not applied by %TypedArray%.prototype.sort.

The following version of SortCompare is used by %TypedArray%.prototype.sort. It performs a numeric comparison rather than the string comparison used in 22.1.3.27. SortCompare has access to the comparefn and buffer values of the current invocation of the sort method.

When the TypedArray SortCompare abstract operation is called with two arguments x and y, 采取以下步骤：

1. 断言：Both Type(x) and Type(y) is Number.
2. 若 comparefn 不是 undefined，那么
   1. 令 v 为 ? ToNumber(? Call(comparefn, undefined, « x, y »)).
   2. 若 IsDetachedBuffer(buffer) 是 true, 抛出 TypeError 异常
   3. 若 v 是 NaN, 返回 +0.
   4. 返回 v.
3. 若 x and y are both NaN, 返回 +0.
4. 若 x 是 NaN, 返回 1.
5. 若 y 是 NaN, 返回 -1.
6. 若 x < y, 返回 -1.
7. 若 x > y, 返回 1.
8. 若 x is -0 and y 是 +0, 返回 -1.
9. 若 x is +0 and y 是 -0, 返回 1.
10. 返回 +0.

> 注
>
> Because NaN always compares greater than any other value, NaN property values always sort to the end of the result when comparefn is not provided.

#### 22.2.3.27 %TypedArray%.prototype.subarray ( begin, end ) <div id="sec-%typedarray%.prototype.subarray"></div>

Returns a new TypedArray object whose element type is the same as this TypedArray and whose ArrayBuffer is the same as the ArrayBuffer of this TypedArray, referencing the elements at begin, inclusive, up to end, exclusive. If either begin or end is negative, it refers to an index from the end of the array, as opposed to from the beginning.

1. 令 O 为 this 值
2. 若 Type(O) 不是 Object, 抛出 TypeError 异常
3. 若 O does not have a [[TypedArrayName]] internal slot, 抛出 TypeError 异常
4. 断言：O has a [[ViewedArrayBuffer]] internal slot.
5. 令 buffer 为 O.[[ViewedArrayBuffer]].
6. 令 srcLength 为 O.[[ArrayLength]].
7. 令 relativeBegin 为 ? ToInteger(begin).
8. 若 relativeBegin < 0, 令 beginIndex 为 max((srcLength + relativeBegin), 0); 否则令 beginIndex 为 min(relativeBegin, srcLength).
9. 若 end 是 undefined, 令 relativeEnd 为 srcLength; 否则, 令 relativeEnd 为 ? ToInteger(end).
10. 若 relativeEnd < 0, 令 endIndex 为 max((srcLength + relativeEnd), 0); 否则令 endIndex 为 min(relativeEnd, srcLength).
11. 令 newLength 为 max(endIndex - beginIndex, 0).
12. 令 constructorName 为 the String value of O.[[TypedArrayName]].
13. 令 elementSize 为 the Number value of the Element Size value specified in Table 59 for constructorName.
14. 令 srcByteOffset 为 O.[[ByteOffset]].
15. 令 beginByteOffset 为 srcByteOffset + beginIndex × elementSize.
16. 令 argumentsList 为 « buffer, beginByteOffset, newLength ».
17. 返回 ? TypedArraySpeciesCreate(O, argumentsList).

This function is not generic. The this value must be an object with a [[TypedArrayName]] internal slot.

#### 22.2.3.28 %TypedArray%.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-%typedarray%.prototype.tolocalestring"></div>

%TypedArray%`.prototype.toLocaleString` is a distinct function that implements the same algorithm as `Array.prototype.toLocaleString` as defined in 22.1.3.29 except that the this object's [[ArrayLength]] internal slot is accessed in place of performing a [[Get]] of `"length"`. The implementation of the algorithm may be optimized with the knowledge that the this value is an object that has a fixed length and whose integer-indexed properties are not sparse. However, such optimization must not introduce any observable changes in the specified behaviour of the algorithm.

This function is not generic. ValidateTypedArray is applied to the this value prior to evaluating the algorithm. If its result is an abrupt completion that exception is thrown instead of evaluating the algorithm.

> 注
>
> If the ECMAScript implementation includes the ECMA-402 Internationalization API this function is based upon the algorithm for `Array.prototype.toLocaleString` that is in the ECMA-402 specification.

#### 22.2.3.29 %TypedArray%.prototype.toString ( ) <div id="sec-%typedarray%.prototype.tostring"></div>

The initial value of the %TypedArray%`.prototype.toString` data property is the same built-in function object as the `Array.prototype.toString` method defined in 22.1.3.30.

#### 22.2.3.30 %TypedArray%.prototype.values ( ) <div id="sec-%typedarray%.prototype.values"></div>

采取以下步骤：

1. 令 O 为 this 值
2. 执行 ? ValidateTypedArray(O).
3. 返回 CreateArrayIterator(O, "value").

#### 22.2.3.31 %TypedArray%.prototype [ @@iterator ] ( ) <div id="sec-%typedarray%.prototype-@@iterator"></div>

The initial value of the @@iterator property is the same function object as the initial value of the %TypedArray%`.prototype.values` property.

#### 22.2.3.32 获取 %TypedArray%.prototype [ @@toStringTag ] <div id="sec-get-%typedarray%.prototype-@@tostringtag"></div>

%TypedArray%.prototype[@@toStringTag] is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 O 为 this 值
2. 若 Type(O) 不是 Object, 返回 undefined.
3. 若 O does not have a [[TypedArrayName]] internal slot, 返回 undefined.
4. 令 name 为 O.[[TypedArrayName]].
5. 断言：Type(name) is String.
6.  返回 name.

此属性具有特性 { [[Enumerable]]: false, [[Configurable]]: true }.

The initial value of the name property of this function is "get [Symbol.toStringTag]".

### 22.2.4 TypedArray 构造器 <div id="sec-typedarray-constructors"></div>

Each TypedArray constructor:

- is an intrinsic object that has the structure described below, differing only in the name used as the constructor name instead of TypedArray, in Table 59.
- is a single function whose behaviour is overloaded based upon the number and types of its arguments. The actual behaviour of a call of TypedArray depends upon the number and kind of arguments that are passed to it.
- is not intended to be called as a function and will throw an exception when called in that manner.
- is designed to be subclassable. It may be used as the value of an `extends` clause of a class definition. Subclass constructors that intend to inherit the specified TypedArray behaviour must include a `super` call to the TypedArray constructor to create and initialize the subclass instance with the internal state necessary to support the %TypedArray%`.prototype` built-in methods.
- has a `"length"` property whose value is 3.

#### 22.2.4.1 TypedArray ( ) <div id="sec-typedarray"></div>

This description applies only if the TypedArray function is called with no arguments.

1. 若 NewTarget 是 undefined, 抛出 TypeError 异常
2. 令 constructorName 为 the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
3. 返回 ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%", 0).

#### 22.2.4.2 TypedArray ( length ) <div id="sec-typedarray-length"></div>

This description applies only if the TypedArray function is called with at least one argument and the Type of the first argument is not Object.

TypedArray called with argument length performs the following steps:

1. 断言：Type(length) is not Object.
2. 若 NewTarget 是 undefined, 抛出 TypeError 异常
3. 令 elementLength 为 ? ToIndex(length).
4. 令 constructorName 为 the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
5. 返回 ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%", elementLength).

##### 22.2.4.2.1 RS: AllocateTypedArray ( constructorName, newTarget, defaultProto [ , length ] ) <div id="sec-allocatetypedarray"></div>

The abstract operation AllocateTypedArray with arguments constructorName, newTarget, defaultProto and optional argument length is used to validate and create an instance of a TypedArray constructor. constructorName is required to be the name of a TypedArray constructor in Table 59. 若 the length argument is passed, an ArrayBuffer of that length is also allocated and associated with the new TypedArray instance. AllocateTypedArray provides common semantics that 是 used by all of the TypedArray overloads. AllocateTypedArray performs the following steps:

1. 令 proto 为 ? GetPrototypeFromConstructor(newTarget, defaultProto).
2. 令 obj 为 IntegerIndexedObjectCreate(proto, « [[ViewedArrayBuffer]], [[TypedArrayName]], [[ByteLength]], [[ByteOffset]], [[ArrayLength]] »).
3. 断言：obj.[[ViewedArrayBuffer]] is undefined.
4. 设置 obj.[[TypedArrayName]] 为 constructorName.
5. 若 length 不存在，那么
   1. 设置 obj.[[ByteLength]] 为 0.
   2. 设置 obj.[[ByteOffset]] 为 0.
   3. 设置 obj.[[ArrayLength]] 为 0.
6. 否则，
   1. 执行 ? AllocateTypedArrayBuffer(obj, length).
7. 返回 obj.

##### 22.2.4.2.2 RS: AllocateTypedArrayBuffer ( O, length ) <div id="sec-allocatetypedarraybuffer"></div>

The abstract operation AllocateTypedArrayBuffer with arguments O and length allocates and associates an ArrayBuffer with the TypedArray instance O. It performs the following steps:

1. 断言：O is an Object that has a [[ViewedArrayBuffer]] internal slot.
2. 断言：O.[[ViewedArrayBuffer]] is undefined.
3. 断言：length ≥ 0.
4. 令 constructorName 为 the String value of O.[[TypedArrayName]].
5. 令 elementSize 为 the Element Size value in Table 59 for constructorName.
6. 令 byteLength 为 elementSize × length.
7. 令 data 为 ? AllocateArrayBuffer(%ArrayBuffer%, byteLength).
8. 设置 O.[[ViewedArrayBuffer]] 为 data.
9. 设置 O.[[ByteLength]] 为 byteLength.
10. 设置 O.[[ByteOffset]] 为 0.
11. 设置 O.[[ArrayLength]] 为 length.
12. 返回 O.

#### 22.2.4.3 TypedArray ( typedArray ) <div id="sec-typedarray-typedarray"></div>

This description applies only if the TypedArray function is called with at least one argument and the Type of the first argument is Object and that object has a [[TypedArrayName]] internal slot.

TypedArray called with argument typedArray performs the following steps:

1. 断言：Type(typedArray) is Object and typedArray has a [[TypedArrayName]] internal slot.
2. 若 NewTarget 是 undefined, 抛出 TypeError 异常
3. 令 constructorName 为 the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
4. 令 O 为 ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%").
5. 令 srcArray 为 typedArray.
6. 令 srcData 为 srcArray.[[ViewedArrayBuffer]].
7. 若 IsDetachedBuffer(srcData) 是 true, 抛出 TypeError 异常
8. 令 elementType 为 the String value of the Element Type value in Table 59 for constructorName.
9. 令 elementLength 为 srcArray.[[ArrayLength]].
10. 令 srcName 为 the String value of srcArray.[[TypedArrayName]].
11. 令 srcType 为 the String value of the Element Type value in Table 59 for srcName.
12. 令 srcElementSize 为 the Element Size value in Table 59 for srcName.
13. 令 srcByteOffset 为 srcArray.[[ByteOffset]].
14. 令 elementSize 为 the Element Size value in Table 59 for constructorName.
15. 令 byteLength 为 elementSize × elementLength.
16. 若 IsSharedArrayBuffer(srcData) 是 false，那么
    1. 令 bufferConstructor 为 ? SpeciesConstructor(srcData, %ArrayBuffer%).
17. 否则，
    1. 令 bufferConstructor 为 %ArrayBuffer%.
18. 若 SameValue(elementType, srcType) 是 true，那么
    1. 令 data 为 ? CloneArrayBuffer(srcData, srcByteOffset, byteLength, bufferConstructor).
19. 否则，
    1. 令 data 为 ? AllocateArrayBuffer(bufferConstructor, byteLength).
    2. 若 IsDetachedBuffer(srcData) 是 true, 抛出 TypeError 异常
    3. 令 srcByteIndex 为 srcByteOffset.
    4. 令 targetByteIndex 为 0.
    5. 令 count 为 elementLength.
    6. 重复，直到 count > 0
       1. 令 value 为 GetValueFromBuffer(srcData, srcByteIndex, srcType, true, "Unordered").
       2. 执行 SetValueInBuffer(data, targetByteIndex, elementType, value, true, "Unordered").
       3. 设置 srcByteIndex 为 srcByteIndex + srcElementSize.
       4. 设置 targetByteIndex 为 targetByteIndex + elementSize.
    7. Decrement count by 1.
20. 设置 O.[[ViewedArrayBuffer]] 为 data.
21. 设置 O.[[ByteLength]] 为 byteLength.
22. 设置 O.[[ByteOffset]] 为 0.
23. 设置 O.[[ArrayLength]] 为 elementLength.
24. 返回 O.

#### 22.2.4.4 TypedArray ( object ) <div id="sec-typedarray-object"></div>

This description applies only if the TypedArray function is called with at least one argument and the Type of the first argument is Object and that object does not have either a [[TypedArrayName]] or an [[ArrayBufferData]] internal slot.

TypedArray called with argument object performs the following steps:

1. 断言：Type(object) is Object and object does not have either a [[TypedArrayName]] or an [[ArrayBufferData]] internal slot.
2. 若 NewTarget 是 undefined, 抛出 TypeError 异常
3. 令 constructorName 为 the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
4. 令 O 为 ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%").
5. 令 usingIterator 为 ? GetMethod(object, @@iterator).
6. 若 usingIterator 不是 undefined，那么
   1. 令 values 为 ? IterableToList(object, usingIterator).
   2. 令 len 为 the number of elements in values.
   3. 执行 ? AllocateTypedArrayBuffer(O, len).
   4. 令 k 为 0.
   5. 重复，直到 k < len
      1. 令 Pk 为 ! ToString(k).
      2. 令 kValue 为 the first element of values and remove that element from values.
      3. 执行 ? Set(O, Pk, kValue, true).
      4. k 增加 1。
   6. 断言：values is now an empty List.
   7. 返回 O.
7. NOTE: object is not an Iterable so assume it is already an array-like object.
8. 令 arrayLike 为 object.
9. 令 len 为 ? ToLength(? Get(arrayLike, "length")).
10. 执行 ? AllocateTypedArrayBuffer(O, len).
11. 令 k 为 0.
12. 重复，直到 k < len
    1. 令 Pk 为 ! ToString(k).
    2. 令 kValue 为 ? Get(arrayLike, Pk).
    3. 执行 ? Set(O, Pk, kValue, true).
    4. k 增加 1。
13. 返回 O.

#### 22.2.4.5 TypedArray ( buffer [ , byteOffset [ , length ] ] ) <div id="sec-typedarray-buffer-byteoffset-length"></div>

This description applies only if the TypedArray function is called with at least one argument and the Type of the first argument is Object and that object has an [[ArrayBufferData]] internal slot.

TypedArray called with at least one argument buffer performs the following steps:

1. 断言：Type(buffer) is Object and buffer has an [[ArrayBufferData]] internal slot.
2. 若 NewTarget 是 undefined, 抛出 TypeError 异常
3. 令 constructorName 为 the String value of the Constructor Name value specified in Table 59 for this TypedArray constructor.
4. 令 O 为 ? AllocateTypedArray(constructorName, NewTarget, "%TypedArrayPrototype%").
5. 令 elementSize 为 the Number value of the Element Size value in Table 59 for constructorName.
6. 令 offset 为 ? ToIndex(byteOffset).
7. 若 offset modulo elementSize ≠ 0, 抛出 RangeError 异常
8. 若 length is present and length 不是 undefined，那么
   1. 令 newLength 为 ? ToIndex(length).
9. 若 IsDetachedBuffer(buffer) 是 true, 抛出 TypeError 异常
10. 令 bufferByteLength 为 buffer.[[ArrayBufferByteLength]].
11. 若 length 是 either not present or undefined，那么
    1. 若 bufferByteLength modulo elementSize ≠ 0, 抛出 RangeError 异常
    2. 令 newByteLength 为 bufferByteLength - offset.
    3. 若 newByteLength < 0, 抛出 RangeError 异常
12. 否则，
    1. 令 newByteLength 为 newLength × elementSize.
    2. 若 offset + newByteLength > bufferByteLength, 抛出 RangeError 异常
13. 设置 O.[[ViewedArrayBuffer]] 为 buffer.
14. 设置 O.[[ByteLength]] 为 newByteLength.
15. 设置 O.[[ByteOffset]] 为 offset.
16. 设置 O.[[ArrayLength]] 为 newByteLength / elementSize.
17. 返回 O.

#### 22.2.4.6 TypedArrayCreate ( constructor, argumentList ) <div id="typedarray-create"></div>

The abstract operation TypedArrayCreate with arguments constructor and argumentList is used to specify the creation of a new TypedArray object using a constructor function. It performs the following steps:

1. 令 newTypedArray 为 ? Construct(constructor, argumentList).
2. 执行 ? ValidateTypedArray(newTypedArray).
3. 若 argumentList 是 a List of a single Number，那么
   1. 若 newTypedArray.[[ArrayLength]] < argumentList[0], 抛出 TypeError 异常
4. 返回 newTypedArray.

#### 22.2.4.7 TypedArraySpeciesCreate ( exemplar, argumentList ) <div id="typedarray-species-create"></div>

The abstract operation TypedArraySpeciesCreate with arguments exemplar and argumentList is used to specify the creation of a new TypedArray object using a constructor function that is derived from exemplar. It performs the following steps:

1. 断言：exemplar is an Object that has a [[TypedArrayName]] internal slot.
2. 令 defaultConstructor 为 the intrinsic object listed in column one of Table 59 for exemplar.[[TypedArrayName]].
3. 令 constructor 为 ? SpeciesConstructor(exemplar, defaultConstructor).
4. 返回 ? TypedArrayCreate(constructor, argumentList).

### 22.2.5 TypedArray 构造器属性 <div id="sec-properties-of-the-typedarray-constructors"></div>

Each TypedArray constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %TypedArray%.
- has a name property whose value is the String value of the constructor name specified for it in Table 59.
- has the following properties:

#### 22.2.5.1 TypedArray.BYTES_PER_ELEMENT <div id="sec-typedarray.bytes_per_element"></div>

The value of TypedArray.BYTES_PER_ELEMENT is the Number value of the Element Size value specified in Table 59 for TypedArray.

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 22.2.5.2 TypedArray.prototype <div id="sec-typedarray.prototype"></div>

The initial value of TypedArray`.prototype` is the corresponding TypedArray prototype intrinsic object (22.2.6.

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 22.2.6 TypedArray 原型对象属性 <div id="sec-properties-of-typedarray-prototype-objects"></div>

Each TypedArray prototype object:

- has a [[Prototype]] internal slot whose value is the intrinsic object %TypedArrayPrototype%.
- is an ordinary object.
- does not have a [[ViewedArrayBuffer]] or any other of the internal slots that are specific to TypedArray instance objects.

#### 22.2.6.1 TypedArray.prototype.BYTES_PER_ELEMENT <div id="sec-typedarray.prototype.bytes_per_element"></div>

The value of TypedArray.prototype.BYTES_PER_ELEMENT is the Number value of the Element Size value specified in Table 59 for TypedArray.

此属性具有特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 22.2.6.2 TypedArray.prototype.constructor <div id="sec-typedarray.prototype.constructor"></div>

The initial value of a TypedArray.prototype.constructor is the corresponding %TypedArray% intrinsic object.

### 22.2.7 TypedArray 实例属性 <div id="sec-properties-of-typedarray-instances"></div>

TypedArray instances are Integer-Indexed exotic objects. Each TypedArray instance inherits properties from the corresponding TypedArray prototype object. Each TypedArray instance has the following internal slots: [[TypedArrayName]], [[ViewedArrayBuffer]], [[ByteLength]], [[ByteOffset]], and [[ArrayLength]].