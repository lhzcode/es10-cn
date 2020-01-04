# 19 基本对象
## 19.1 Object 对象 <div id="sec-object-objects"></div>
### 19.1.1 Object 构造器 <div id="sec-object-constructor"></div>

The Object constructor:

- is the intrinsic object %Object%.
- is the initial value of the Object property of the global object.
- creates a new ordinary object when called as a constructor.
- performs a type conversion when called as a function rather than as a constructor.
- is designed to be subclassable. It may be used as the value of an extends clause of a class definition.

#### 19.1.1.1 Object ( [ value ] ) <div id="sec-object-value"></div>

When the Object function is called with optional argument value, the following steps are taken:

1. If NewTarget is neither undefined nor the active function, then
1. Return ? OrdinaryCreateFromConstructor(NewTarget, "%ObjectPrototype%").
2. If value is null, undefined or not supplied, return ObjectCreate(%ObjectPrototype%).
3. Return ! ToObject(value).

The "length" property of the Object constructor function is 1

### 19.1.2 Object 构造器属性 <div id="sec-properties-of-the-object-constructor"></div>

The Object constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has a "length" property.
- has the following additional properties:

#### 19.1.2.1 Object.assign ( target, ...sources ) <div id="sec-object.assign"></div>

The assign function is used to copy the values of all of the enumerable own properties from one or more source objects to a target object. When the assign function is called, the following steps are taken:

1. Let to be ? ToObject(target).
2. If only one argument was passed, return to.
3. Let sources be the List of argument values starting with the second argument.
4. For each element nextSource of sources, in ascending index order, do
  1. If nextSource is neither undefined nor null, then
        1. Let from be ! ToObject(nextSource).
            2. Let keys be ? from.\[\[OwnPropertyKeys]]().
                3. For each element nextKey of keys in List order, do
      1. Let desc be ? from.\[\[GetOwnProperty]](nextKey).
      2. If desc is not undefined and desc.[[Enumerable]] is true, then
        1. Let propValue be ? Get(from, nextKey).
        2. Perform ? Set(to, nextKey, propValue, true).
5. Return to.

The "length" property of the assign function is 2.

#### 19.1.2.2 Object.create ( O, Properties ) <div id="sec-object.create"></div>

The create function creates a new object with a specified prototype. When the create function is called, the following steps are taken:

1. If Type(O) is neither Object nor Null, throw a TypeError exception.
2. Let obj be ObjectCreate(O).
3. If Properties is not undefined, then
1. Return ? ObjectDefineProperties(obj, Properties).
4. Return obj.

#### 19.1.2.3 Object.defineProperties ( O, Properties ) <div id="sec-object.defineproperties"></div>

The defineProperties function is used to add own properties and/or update the attributes of existing own properties of an object. When the defineProperties function is called, the following steps are taken:

1. Return ? ObjectDefineProperties(O, Properties).

##### 19.1.2.3.1 RS: ObjectDefineProperties ( O, Properties ) <div id="sec-objectdefineproperties"></div>

The abstract operation ObjectDefineProperties with arguments O and Properties performs the following steps:

1. If Type(O) is not Object, throw a TypeError exception.
2. Let props be ? ToObject(Properties).
3. Let keys be ? props.\[\[OwnPropertyKeys]]().
4. Let descriptors be a new empty List.
5. For each element nextKey of keys in List order, do
1. Let propDesc be ? props.\[\[GetOwnProperty]](nextKey).
2. If propDesc is not undefined and propDesc.[[Enumerable]] is true, then
  1. Let descObj be ? Get(props, nextKey).
  2. Let desc be ? ToPropertyDescriptor(descObj).
  3. Append the pair (a two element List) consisting of nextKey and desc to the end of descriptors.
6. For each pair from descriptors in list order, do
  1. Let P be the first element of pair.
  2. Let desc be the second element of pair.
  3. Perform ? DefinePropertyOrThrow(O, P, desc).
7. Return O.

#### 19.1.2.4 Object.defineProperty ( O, P, Attributes ) <div id="sec-object.defineproperty"></div>

The defineProperty function is used to add an own property and/or update the attributes of an existing own property of an object. When the defineProperty function is called, the following steps are taken:

1. If Type(O) is not Object, throw a TypeError exception.
2. Let key be ? ToPropertyKey(P).
3. Let desc be ? ToPropertyDescriptor(Attributes).
4. Perform ? DefinePropertyOrThrow(O, key, desc).
5. Return O

#### 19.1.2.5 Object.entries ( O ) <div id="sec-object.entries"></div>

When the entries function is called with argument O, the following steps are taken:

1. Let obj be ? ToObject(O).
2. Let nameList be ? EnumerableOwnPropertyNames(obj, "key+value").
3. Return CreateArrayFromList(nameList).

#### 19.1.2.6 Object.freeze ( O ) <div id="sec-object.freeze"></div>

When the freeze function is called, the following steps are taken:

1. If Type(O) is not Object, return O.
2. Let status be ? SetIntegrityLevel(O, "frozen").
3. If status is false, throw a TypeError exception.
4. Return O.

#### 19.1.2.7 Object.fromEntries ( iterable ) <div id="sec-object.fromentries"></div>

When the fromEntries method is called with argument iterable, the following steps are taken:

1. Perform ? RequireObjectCoercible(iterable).
2. Let obj be ObjectCreate(%ObjectPrototype%).
3. Assert: obj is an extensible ordinary object with no own properties.
4. Let stepsDefine be the algorithm steps defined in CreateDataPropertyOnObject Functions.
5. Let adder be CreateBuiltinFunction(stepsDefine, « »).
6. Return ? AddEntriesFromIterable(obj, iterable, adder).

> NOTE The function created for adder is never directly accessible to ECMAScript code.

##### 19.1.2.7.1 CreateDataPropertyOnObject Functions <div id="sec-create-data-property-on-object-functions"></div>

A CreateDataPropertyOnObject function is an anonymous built-in function. When a CreateDataPropertyOnObject function is called with arguments key and value, the following steps are taken:

1. Let O be the this value.
2. Assert: Type(O) is Object.
3. Assert: O is an extensible ordinary object.
4. Let propertyKey be ? ToPropertyKey(key).
5. Perform ! CreateDataPropertyOrThrow(O, propertyKey, value).
6. Return undefined.

#### 19.1.2.8 Object.getOwnPropertyDescriptor ( O, P ) <div id="sec-object.getownpropertydescriptor"></div>

When the getOwnPropertyDescriptor function is called, the following steps are taken:

1. Let obj be ? ToObject(O).
2. Let key be ? ToPropertyKey(P).
3. Let desc be ? obj.\[\[GetOwnProperty]](key).
4. Return FromPropertyDescriptor(desc).

#### 19.1.2.9 Object.getOwnPropertyDescriptors ( O ) <div id="sec-object.getownpropertydescriptors"></div>

When the getOwnPropertyDescriptors function is called, the following steps are taken:

1. Let obj be ? ToObject(O).
2. Let ownKeys be ? obj.\[\[OwnPropertyKeys]]().
3. Let descriptors be ! ObjectCreate(%ObjectPrototype%).
4. For each element key of ownKeys in List order, do
1. Let desc be ? obj.\[\[GetOwnProperty]](key).
2. Let descriptor be ! FromPropertyDescriptor(desc).
3. If descriptor is not undefined, perform ! CreateDataProperty(descriptors, key, descriptor).
5. Return descriptors.

#### 19.1.2.10 Object.getOwnPropertyNames ( O ) <div id="sec-object.getownpropertynames"></div>

When the getOwnPropertyNames function is called, the following steps are taken:

1. Return ? GetOwnPropertyKeys(O, String).

#### 19.1.2.11 Object.getOwnPropertySymbols ( O ) <div id="sec-object.getownpropertysymbols"></div>

When the getOwnPropertySymbols function is called with argument O, the following steps are taken:

1. Return ? GetOwnPropertyKeys(O, Symbol).

##### 19.1.2.11.1 RS: GetOwnPropertyKeys ( O, type ) <div id="sec-getownpropertykeys"></div>

The abstract operation GetOwnPropertyKeys is called with arguments O and type where O is an Object and type is one of the ECMAScript specification types String or Symbol. The following steps are taken:

1. Let obj be ? ToObject(O).
2. Let keys be ? obj.\[\[OwnPropertyKeys]]().
3. Let nameList be a new empty List.
4. For each element nextKey of keys in List order, do
  1. If Type(nextKey) is type, then
        1. Append nextKey as the last element of nameList.
5. Return CreateArrayFromList(nameList).

#### 19.1.2.12 Object.getPrototypeOf ( O ) <div id="sec-object.getprototypeof"></div>

When the getPrototypeOf function is called with argument O, the following steps are taken:

1. Let obj be ? ToObject(O).
2. Return ? obj.\[\[GetPrototypeOf]]().

#### 19.1.2.13 Object.is ( value1, value2 ) <div id="sec-object.is"></div>

When the is function is called with arguments value1 and value2, the following steps are taken:

1. Return SameValue(value1, value2).

#### 19.1.2.14 Object.isExtensible ( O ) <div id="sec-object.isextensible"></div>

When the isExtensible function is called with argument O, the following steps are taken:

1. If Type(O) is not Object, return false.
2. Return ? IsExtensible(O).

#### 19.1.2.15 Object.isFrozen ( O ) <div id="sec-object.isfrozen"></div>
When the isFrozen function is called with argument O, the following steps are taken:

1. If Type(O) is not Object, return true.
2. Return ? TestIntegrityLevel(O, "frozen").

#### 19.1.2.16 Object.isSealed ( O ) <div id="sec-object.issealed"></div>

When the isSealed function is called with argument O, the following steps are taken:

1. If Type(O) is not Object, return true.
2. Return ? TestIntegrityLevel(O, "sealed").

#### 19.1.2.17 Object.keys ( O ) <div id="sec-object.keys"></div>

When the keys function is called with argument O, the following steps are taken:

1. Let obj be ? ToObject(O).
2. Let nameList be ? EnumerableOwnPropertyNames(obj, "key").
3. Return CreateArrayFromList(nameList).

#### 19.1.2.18 Object.preventExtensions ( O ) <div id="sec-object.preventextensions"></div>

When the preventExtensions function is called, the following steps are taken:

1. If Type(O) is not Object, return O.
2. Let status be ? O.\[[\PreventExtensions]]().
3. If status is false, throw a TypeError exception.
4. Return O.

#### 19.1.2.19 Object.prototype <div id="sec-object.prototype"></div>

The initial value of Object.prototype is the intrinsic object %ObjectPrototype%.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.1.2.20 Object.seal ( O ) <div id="sec-object.seal"></div>

When the seal function is called, the following steps are taken:

1. If Type(O) is not Object, return O.
2. Let status be ? SetIntegrityLevel(O, "sealed").
3. If status is false, throw a TypeError exception.
4. Return O.

#### 19.1.2.21 Object.setPrototypeOf ( O, proto ) <div id="sec-object.setprototypeof"></div>

When the setPrototypeOf function is called with arguments O and proto, the following steps are taken:

1. Set O to ? RequireObjectCoercible(O).
2. If Type(proto) is neither Object nor Null, throw a TypeError exception.
3. If Type(O) is not Object, return O.
4. Let status be ? O.\[\[SetPrototypeOf]](proto).
5. If status is false, throw a TypeError exception.
6. Return O.

#### 19.1.2.22 Object.values ( O ) <div id="sec-object.values"></div>

When the values function is called with argument O, the following steps are taken:

1. Let obj be ? ToObject(O).
2. Let nameList be ? EnumerableOwnPropertyNames(obj, "value").
3. Return CreateArrayFromList(nameList).

### 19.1.3 Object 原型对象属性 <div id="sec-properties-of-the-object-prototype-object"></div>

The Object prototype object:

- is the intrinsic object %ObjectPrototype%.
- is an immutable prototype exotic object.
- has a [[Prototype]] internal slot whose value is null.

#### 19.1.3.1 Object.prototype.constructor <div id="sec-object.prototype.constructor"></div>

The initial value of Object.prototype.constructor is the intrinsic object %Object%.

#### 19.1.3.2 Object.prototype.hasOwnProperty ( V ) <div id="sec-object.prototype.hasownproperty"></div>

When the hasOwnProperty method is called with argument V, the following steps are taken:

1. Let P be ? ToPropertyKey(V).
2. Let O be ? ToObject(this value).
3. Return ? HasOwnProperty(O, P).

> NOTE The ordering of steps 1 and 2 is chosen to ensure that any exception that would have been thrown by step 1 in previous editions of this specification will continue to be thrown even if the this value is undefined or null.

#### 19.1.3.3 Object.prototype.isPrototypeOf ( V ) <div id="sec-object.prototype.isprototypeof"></div>

When the isPrototypeOf method is called with argument V, the following steps are taken:

1. If Type(V) is not Object, return false.
2. Let O be ? ToObject(this value).
3. Repeat,
1. Set V to ? V.\[\[GetPrototypeOf]]().
2. If V is null, return false.
3. If SameValue(O, V) is true, return true.

> NOTE The ordering of steps 1 and 2 preserves the behaviour specified by previous editions of this specification for the case where V is not an object and the this value is undefined or null.

#### 19.1.3.4 Object.prototype.propertyIsEnumerable ( V ) <div id="sec-object.prototype.propertyisenumerable"></div>

When the propertyIsEnumerable method is called with argument V, the following steps are taken:

1. Let P be ? ToPropertyKey(V).
2. Let O be ? ToObject(this value).
3. Let desc be ? O.\[\[GetOwnProperty]](P).
4. If desc is undefined, return false.
5. Return desc.[[Enumerable]].

>NOTE 1 This method does not consider objects in the prototype chain.

> NOTE 2 The ordering of steps 1 and 2 is chosen to ensure that any exception that would have been thrown by step 1 in previous editions of this specification will continue to be thrown even if the this value is undefined or null.

#### 19.1.3.5 Object.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-object.prototype.tolocalestring"></div>

When the toLocaleString method is called, the following steps are taken:

1. Let O be the this value.
2. Return ? Invoke(O, "toString").

The optional parameters to this function are not used but are intended to correspond to the parameter pattern used by ECMA-402 toLocaleString functions. Implementations that do not include ECMA-402 support must not use those parameter positions for other purposes.

>NOTE 1 This function provides a generic toLocaleString implementation for objects that have no locale-specific toString behaviour. Array, Number, Date, and Typed Arrays provide their own locale-sensitive toLocaleString methods.

> NOTE 2 ECMA-402 intentionally does not provide an alternative to this default implementation.

#### 19.1.3.6 Object.prototype.toString ( ) <div id="sec-object.prototype.tostring"></div>

When the toString method is called, the following steps are taken:

1. If the this value is undefined, return "[object Undefined]".
2. If the this value is null, return "[object Null]".
3. Let O be ! ToObject(this value).
4. Let isArray be ? IsArray(O).
5. If isArray is true, let builtinTag be "Array".
6. Else if O is a String exotic object, let builtinTag be "String".
7. Else if O has a [[ParameterMap]] internal slot, let builtinTag be "Arguments".
8. Else if O has a [[Call]] internal method, let builtinTag be "Function".
9. Else if O has an [[ErrorData]] internal slot, let builtinTag be "Error".
10. Else if O has a [[BooleanData]] internal slot, let builtinTag be "Boolean".
11. Else if O has a [[NumberData]] internal slot, let builtinTag be "Number".
12. Else if O has a [[DateValue]] internal slot, let builtinTag be "Date".
13. Else if O has a [[RegExpMatcher]] internal slot, let builtinTag be "RegExp".
14. Else, let builtinTag be "Object".
15. Let tag be ? Get(O, @@toStringTag).
16. If Type(tag) is not String, set tag to builtinTag.
17. Return the string-concatenation of "[object ", tag, and "]".

This function is the %ObjProto_toString% intrinsic object.

> NOTE Historically, this function was occasionally used to access the String value of the [[Class]] internal slot that was used in previous editions of this specification as a nominal type tag for various built-in objects. The above definition of toString preserves compatibility for legacy code that uses toString as a test for those specific kinds of built-in objects. It does not provide a reliable type testing mechanism for other kinds of built-in or program defined objects. In addition, programs can use @@toStringTag in ways that will invalidate the reliability of such legacy type tests.

#### 19.1.3.7 Object.prototype.valueOf ( ) <div id="sec-object.prototype.valueof"></div>

When the valueOf method is called, the following steps are taken

1. Return ? ToObject(this value).

This function is the %ObjProto_valueOf% intrinsic object.

### 19.1.4 Object 实例属性 <div id="sec-properties-of-object-instances"></div>

Object instances have no special properties beyond those inherited from the Object prototype object.

## 19.2 Function 对象 <div id="sec-function-objects"></div>
### 19.2.1 Function 构造器 <div id="sec-function-constructor"></div>

The Function constructor:

- is the intrinsic object %Function%.
- is the initial value of the Function property of the global object.
- creates and initializes a new function object when called as a function rather than as a constructor. Thus the function call Function(…) is equivalent to the object creation expression new Function(…) with the same arguments.
- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. Subclass constructors that intend to inherit the specified Function behaviour must include a super call to the Function constructor to create and initialize a subclass instance with the internal slots necessary for built-in function behaviour. All ECMAScript syntactic forms for defining function objects create instances of Function.
- There is no syntactic means to create instances of Function subclasses except for the built-in GeneratorFunction, AsyncFunction, and AsyncGeneratorFunction subclasses

#### 19.2.1.1 Function ( p1, p2, … , pn, body ) <div id="sec-function-p1-p2-pn-body"></div>

The last argument specifies the body (executable code) of a function; any preceding arguments specify formal parameters.

When the Function function is called with some arguments p1, p2, … , pn, body (where n might be 0, that is, there are no “ p ” arguments, and where body might also not be provided), the following steps are taken:

1. Let C be the active function object.
2. Let args be the argumentsList that was passed to this function by [[Call]] or [[Construct]].
3. Return ? CreateDynamicFunction(C, NewTarget, "normal", args).

> NOTE It is permissible but not necessary to have one argument for each formal parameter to be specified. For example, all three of the following expressions produce the same result:
>
> new Function("a", "b", "c", "return a+b+c")
> new Function("a, b, c", "return a+b+c")
> new Function("a,b", "c", "return a+b+c")

##### 19.2.1.1.1 RS: CreateDynamicFunction ( constructor, newTarget, kind, args ) <div id="sec-createdynamicfunction"></div>

The abstract operation CreateDynamicFunction is called with arguments constructor, newTarget, kind, and args. constructor is the constructor function that is performing this action, newTarget is the constructor that new was initially applied to, kind is either "normal", "generator", "async", or "async generator", and args is a List containing the actual argument values that were passed to constructor. The following steps are taken:

1. Assert: The execution context stack has at least two elements.
2. Let callerContext be the second to top element of the execution context stack.
3. Let callerRealm be callerContext's Realm.
4. Let calleeRealm be the current Realm Record.
5. Perform ? HostEnsureCanCompileStrings(callerRealm, calleeRealm).
6. If newTarget is undefined, set newTarget to constructor.
7. If kind is "normal", then
    1. Let goal be the grammar symbol FunctionBody[~Yield, ~Await] .
    2. Let parameterGoal be the grammar symbol FormalParameters[~Yield, ~Await] .
    3. Let fallbackProto be "%FunctionPrototype%".
8. Else if kind is "generator", then
    1. Let goal be the grammar symbol GeneratorBody.
    2. Let parameterGoal be the grammar symbol FormalParameters[+Yield, ~Await] .
    3. Let fallbackProto be "%Generator%".
9. Else if kind is "async", then
    1. Let goal be the grammar symbol AsyncFunctionBody.
    2. Let parameterGoal be the grammar symbol FormalParameters[~Yield, +Await] .
    3. Let fallbackProto be "%AsyncFunctionPrototype%".
10. Else,
    1. Assert: kind is "async generator".
    2. Let goal be the grammar symbol AsyncGeneratorBody.
    3. Let parameterGoal be the grammar symbol FormalParameters[+Yield, +Await] .
    4. Let fallbackProto be "%AsyncGenerator%".
11. Let argCount be the number of elements in args.
12. Let P be the empty String.
13. If argCount = 0, let bodyText be the empty String.
14. Else if argCount = 1, let bodyText be args[0].
15. Else argCount > 1,
    1. Let firstArg be args[0].
    2. Set P to ? ToString(firstArg).
    3. Let k be 1.
    4. Repeat, while k < argCount - 1
        1. Let nextArg be args[k].
        2. Let nextArgString be ? ToString(nextArg).
        3. Set P to the string-concatenation of the previous value of P, "," (a comma), and nextArgString.
        4. Increase k by 1.
    5. Let bodyText be args[k].
16. Set bodyText to ? ToString(bodyText).
17. Let parameters be the result of parsing P, interpreted as UTF-16 encoded Unicode text as described in 6.1.4, using parameterGoal as the goal symbol. Throw a SyntaxError exception if the parse fails.
18. Let body be the result of parsing bodyText, interpreted as UTF-16 encoded Unicode text as described in 6.1.4, using goal as the goal symbol. Throw a SyntaxError exception if the parse fails.
19. Let strict be ContainsUseStrict of body.
20. If any static semantics errors are detected for parameters or body, throw a SyntaxError or a ReferenceError exception, depending on the type of the error. If strict is true, the Early Error rules for UniqueFormalParameters : FormalParameters are applied. Parsing and early error detection may be interweaved in an implementationdependent manner.
21. If strict is true and IsSimpleParameterList of parameters is false, throw a SyntaxError exception.
22. If any element of the BoundNames of parameters also occurs in the LexicallyDeclaredNames of body, throw a SyntaxError exception.
23. If body Contains SuperCall is true, throw a SyntaxError exception.
24. If parameters Contains SuperCall is true, throw a SyntaxError exception.
25. If body Contains SuperProperty is true, throw a SyntaxError exception.
26. If parameters Contains SuperProperty is true, throw a SyntaxError exception.
27. If kind is "generator" or "async generator", then
    1. If parameters Contains YieldExpression is true, throw a SyntaxError exception.
28. If kind is "async" or "async generator", then
    1. If parameters Contains AwaitExpression is true, throw a SyntaxError exception.
29. If strict is true, then
    1. If BoundNames of parameters contains any duplicate elements, throw a SyntaxError exception.
30. Let proto be ? GetPrototypeFromConstructor(newTarget, fallbackProto).
31. Let F be FunctionAllocate(proto, strict, kind).
32. Let realmF be F.[[Realm]].
33. Let scope be realmF.[[GlobalEnv]].
34. Perform FunctionInitialize(F, Normal, parameters, body, scope).
35. If kind is "generator", then
    1. Let prototype be ObjectCreate(%GeneratorPrototype%).
    2. Perform DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
36. Else if kind is "async generator", then
     1. Let prototype be ObjectCreate(%AsyncGeneratorPrototype%).
     2. Perform DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
37. Else if kind is "normal", perform MakeConstructor(F).
38. NOTE: Async functions are not constructable and do not have a [[Construct]] internal method or a "prototype" property.
39. Perform SetFunctionName(F, "anonymous").
40. Let prefix be the prefix associated with kind in Table 47.
41. Let sourceText be the string-concatenation of prefix, " anonymous(", P, 0x000A (LINE FEED), ") {",
     0x000A (LINE FEED), bodyText, 0x000A (LINE FEED), and "}".
42. Set F.[[SourceText]] to sourceText.
43. Return F.

> NOTE A prototype property is created for every non-async function created using CreateDynamicFunction to provide for the possibility that the function will be used as a constructor.

Table 47: Dynamic Function SourceText Prefixes

| Kind              | Prefix            |
| ----------------- | ----------------- |
| "normal"          | "function"        |
| "generator"       | "function*"       |
| "async"           | "async function"  |
| "async generator" | "async function*" |

### 19.2.2 Function 构造器属性 <div id="sec-properties-of-the-function-constructor"></div>

The Function constructor:

- is itself a built-in function object.
- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 19.2.2.1 Function.length <div id="sec-function.length"></div>

This is a data property with a value of 1. This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 19.2.2.2 Function.prototype <div id="sec-function.prototype"></div>

The value of Function.prototype is %FunctionPrototype%, the intrinsic Function prototype object.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 19.2.3 Function 原型对象属性 <div id="sec-properties-of-the-function-prototype-object"></div>

The Function prototype object:

- is the intrinsic object %FunctionPrototype%.
- is itself a built-in function object.
- accepts any arguments and returns undefined when invoked.
- does not have a [[Construct]] internal method; it cannot be used as a constructor with the new operator.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.
- does not have a prototype property.
- has a "length" property whose value is 0.
- has a name property whose value is the empty String.

> NOTE The Function prototype object is specified to be a function object to ensure compatibility with ECMAScript code that was created prior to the ECMAScript 2015 specification.

#### 19.2.3.1 Function.prototype.apply ( thisArg, argArray ) <div id="sec-function.prototype.apply"></div>

When the apply method is called with arguments thisArg and argArray, the following steps are taken:

1. Let func be the this value.
2. If IsCallable(func) is false, throw a TypeError exception.
3. If argArray is undefined or null, then
   1. Perform PrepareForTailCall().
   2. Return ? Call(func, thisArg).
4. Let argList be ? CreateListFromArrayLike(argArray).
5. Perform PrepareForTailCall().
6. Return ? Call(func, thisArg, argList).

>NOTE 1 The thisArg value is passed without modification as the this value. This is a change from Edition 3, where an undefined or null thisArg is replaced with the global object and ToObject is applied to all other values and that result is passed as the this value. Even though the thisArg is passed without modification, non-strict functions still perform these transformations upon entry to the function.

> NOTE 2 If func is an arrow function or a bound function then the thisArg will be ignored by the function [[Call]] in step 5.

#### 19.2.3.2 Function.prototype.bind ( thisArg, ...args ) <div id="sec-function.prototype.bind"></div>

When the bind method is called with argument thisArg and zero or more args, it performs the following steps:

1. Let Target be the this value.
2. If IsCallable(Target) is false, throw a TypeError exception.
3. Let args be a new (possibly empty) List consisting of all of the argument values provided after thisArg in order.
4. Let F be ? BoundFunctionCreate(Target, thisArg, args).
5. Let targetHasLength be ? HasOwnProperty(Target, "length").
6. If targetHasLength is true, then
1. Let targetLen be ? Get(Target, "length").
2. If Type(targetLen) is not Number, let L be 0.
3. Else,
  1. Set targetLen to ! ToInteger(targetLen).
  2. Let L be the larger of 0 and the result of targetLen minus the number of elements of args.
7. Else, let L be 0.
8. Perform ! SetFunctionLength(F, L).
9. Let targetName be ? Get(Target, "name").
10. If Type(targetName) is not String, set targetName to the empty string.
11. Perform SetFunctionName(F, targetName, "bound").
12. Return F.

> NOTE 1 Function objects created using Function.prototype.bind are exotic objects. They also do not have a prototype property.

> NOTE 2 If Target is an arrow function or a bound function then the thisArg passed to this method will not be used by subsequent calls to F.

#### 19.2.3.3 Function.prototype.call ( thisArg, ...args ) <div id="sec-function.prototype.call"></div>

When the call method is called with argument thisArg and zero or more args, the following steps are taken:

1. Let func be the this value.
2. If IsCallable(func) is false, throw a TypeError exception.
3. Let argList be a new empty List.
4. If this method was called with more than one argument, then in left to right order, starting with the second argument, append each argument as the last element of argList.
5. Perform PrepareForTailCall().
6. Return ? Call(func, thisArg, argList).

> NOTE 1 The thisArg value is passed without modification as the this value. This is a change from Edition 3, where an undefined or null thisArg is replaced with the global object and ToObject is applied to all other values and that result is passed as the this value. Even though the thisArg is passed without modification, non-strict functions still perform these transformations upon entry to the function.

> NOTE 2 If func is an arrow function or a bound function then the thisArg will be ignored by the function [[Call]] in step 5.

#### 19.2.3.4 Function.prototype.constructor <div id="sec-function.prototype.constructor"></div>

The initial value of Function.prototype.constructor is the intrinsic object %Function%.

#### 19.2.3.5 Function.prototype.toString ( ) <div id="sec-function.prototype.tostring"></div>

When the toString method is called, the following steps are taken:

1. Let func be the this value.
2. If func is a Bound Function exotic object or a built-in function object, then return an implementation-dependent String source code representation of func. The representation must have the syntax of a NativeFunction. Additionally, if func is a Well-known Intrinsic Object and is not identified as an anonymous function, the portion of the returned String that would be matched by PropertyName must be the initial value of the name property of func.
3. If Type(func) is Object and func has a [[SourceText]] internal slot and Type(func.[[SourceText]]) is String and ! HostHasSourceTextAvailable(func) is true, then return func.[[SourceText]].
4. If Type(func) is Object and IsCallable(func) is true, then return an implementation-dependent String source code representation of func. The representation must have the syntax of a NativeFunction.
5. Throw a TypeError exception.

```
NativeFunction :
	function PropertyName[~Yield, ~Await] opt ( FormalParameters[~Yield, ~Await] ) { [
native code ] }
```

#### 19.2.3.6 Function.prototype [ @@hasInstance ] ( V ) <div id="sec-function.prototype-@@hasinstance"></div>

When the @@hasInstance method of an object F is called with value V, the following steps are taken:

1. Let F be the this value.
2. Return ? OrdinaryHasInstance(F, V).

The value of the name property of this function is "[Symbol.hasInstance]".

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

> NOTE This is the default implementation of @@hasInstance that most functions inherit. @@hasInstance is called by the instanceof operator to determine whether a value is an instance of a specific constructor. An expression such as
>
> v instanceof F
>
> evaluates as
>
> F\[@@hasInstance](v)
>
> A constructor function can control which objects are recognized as its instances by instanceof by exposing a different @@hasInstance method on the function.

This property is non-writable and non-configurable to prevent tampering that could be used to globally expose the target function of a bound function.

### 19.2.4 Function 实例 <div id="sec-function-instances"></div>

Every Function instance is an ECMAScript function object and has the internal slots listed in Table 27. Function objects created using the Function.prototype.bind method (19.2.3.2) have the internal slots listed in Table 28.

Function instances have the following properties:

#### 19.2.4.1 length <div id="sec-function-instances-length"></div>

The value of the "length" property is an integer that indicates the typical number of arguments expected by the function. However, the language permits the function to be invoked with some other number of arguments. The behaviour of a function when invoked on a number of arguments other than the number specified by its "length" property depends on the function. This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 19.2.4.2 name <div id="sec-function-instances-name"></div>

Anonymous functions objects that do not have a contextual name associated with them by this specification do not have a name own property but inherit the name property of %FunctionPrototype%.

#### 19.2.4.3 prototype <div id="sec-function-instances-prototype"></div>

Function instances that can be used as a constructor have a prototype property. Whenever such a Function instance is created another ordinary object is also created and is the initial value of the function's prototype property. Unless otherwise specified, the value of the prototype property is used to initialize the [[Prototype]] internal slot of the object created when that function is invoked as a constructor.

This property has the attributes { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.

> NOTE Function objects created using Function.prototype.bind, or by evaluating a MethodDefinition (that is not a GeneratorMethod or AsyncGeneratorMethod) or an ArrowFunction do not have a prototype property.

### 19.2.5 HostHasSourceTextAvailable ( func ) <div id="sec-hosthassourcetextavailable"></div>

HostHasSourceTextAvailable is an implementation-defined abstract operation that allows host environments to prevent the source text from being provided for a given function.

An implementation of HostHasSourceTextAvailable must complete normally in all cases. This operation must be deterministic with respect to its parameters. Each time it is called with a specific func as its argument, it must return the same completion record. The default implementation of HostHasSourceTextAvailable is to unconditionally return a normal completion with a value of true.

## 19.3 Boolean 对象 <div id="sec-boolean-objects"></div>
### 19.3.1 Boolean 构造器 <div id="sec-boolean-constructor"></div>

The Boolean constructor:

- is the intrinsic object %Boolean%.

- is the initial value of the Boolean property of the global object.

- creates and initializes a new Boolean object when called as a constructor.

- performs a type conversion when called as a function rather than as a constructor.

- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. 

  Subclass constructors that intend to inherit the specified Boolean behaviour must include a super call to the Boolean constructor to create and initialize the subclass instance with a [[BooleanData]] internal slot.

#### 19.3.1.1 Boolean ( value ) <div id="sec-boolean-constructor-boolean-value"></div>

When Boolean is called with argument value, the following steps are taken:

1. Let b be ToBoolean(value).
2. If NewTarget is undefined, return b.
3. Let O be ? OrdinaryCreateFromConstructor(NewTarget, "%BooleanPrototype%", « [[BooleanData]] »).
4. Set O.[[BooleanData]] to b.
5. Return O.

### 19.3.2 Boolean 构造器属性 <div id="sec-properties-of-the-boolean-constructor"></div>

The Boolean constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 19.3.2.1 Boolean.prototype <div id="sec-boolean.prototype"></div>

The initial value of Boolean.prototype is the intrinsic object %BooleanPrototype%.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 19.3.3 Boolean 原型对象属性 <div id="sec-properties-of-the-boolean-prototype-object"></div>

The Boolean prototype object:

- is the intrinsic object %BooleanPrototype%.
- is an ordinary object.
- is itself a Boolean object; it has a [[BooleanData]] internal slot with the value false.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

The abstract operation thisBooleanValue(value) performs the following steps:

1. If Type(value) is Boolean, return value.
2. If Type(value) is Object and value has a [[BooleanData]] internal slot, then
1. Let b be value.[[BooleanData]].
2. Assert: Type(b) is Boolean.
3. Return b.
3. Throw a TypeError exception.

#### 19.3.3.1 Boolean.prototype.constructor <div id="sec-boolean.prototype.constructor"></div>

The initial value of Boolean.prototype.constructor is the intrinsic object %Boolean%.

#### 19.3.3.2 Boolean.prototype.toString ( ) <div id="sec-boolean.prototype.tostring"></div>

The following steps are taken:

1. Let b be ? thisBooleanValue(this value).
2. If b is true, return "true"; else return "false".

#### 19.3.3.3 Boolean.prototype.valueOf ( ) <div id="sec-boolean.prototype.valueof"></div>

The following steps are taken:

1. Return ? thisBooleanValue(this value).

### 19.3.4 Boolean 实例属性 <div id="sec-properties-of-boolean-instances"></div>

Boolean instances are ordinary objects that inherit properties from the Boolean prototype object. Boolean instances have a [[BooleanData]] internal slot. The [[BooleanData]] internal slot is the Boolean value represented by this Boolean object.

## 19.4 Symbol 对象 <div id="sec-symbol-objects"></div>
### 19.4.1 Symbol 构造器 <div id="sec-symbol-constructor"></div>

The Symbol constructor:

- is the intrinsic object %Symbol%.
- is the initial value of the Symbol property of the global object.
- returns a new Symbol value when called as a function.
- is not intended to be used with the new operator.
- is not intended to be subclassed.
- may be used as the value of an extends clause of a class definition but a super call to it will cause an
  exception.

#### 19.4.1.1 Symbol ( [ description ] ) <div id="sec-symbol-description"></div>

When Symbol is called with optional argument description, the following steps are taken:

1. If NewTarget is not undefined, throw a TypeError exception.
2. If description is undefined, let descString be undefined.
3. Else, let descString be ? ToString(description).
4. Return a new unique Symbol value whose [[Description]] value is descString.

### 19.4.2 Symbol 构造器属性 <div id="sec-properties-of-the-symbol-constructor"></div>

The Symbol constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 19.4.2.1 Symbol.asyncIterator <div id="sec-symbol.asynciterator"></div>

The initial value of Symbol.asyncIterator is the well known symbol @@asyncIterator (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.2 Symbol.for ( key ) <div id="sec-symbol.for"></div>

When Symbol.for is called with argument key it performs the following steps:

1. Let stringKey be ? ToString(key).
2. For each element e of the GlobalSymbolRegistry List, do
1. If SameValue(e.[[Key]], stringKey) is true, return e.[[Symbol]].
3. Assert: GlobalSymbolRegistry does not currently contain an entry for stringKey.
4. Let newSymbol be a new unique Symbol value whose [[Description]] value is stringKey.
5. Append the Record { [[Key]]: stringKey, [[Symbol]]: newSymbol } to the GlobalSymbolRegistry List.
6. Return newSymbol.

The GlobalSymbolRegistry is a List that is globally available. It is shared by all realms. Prior to the evaluation of any ECMAScript code it is initialized as a new empty List. Elements of the GlobalSymbolRegistry are Records with the structure defined in Table 48.

Table 48: GlobalSymbolRegistry Record Fields

| Field Name | Value    | Usage                                            |
| ---------- | -------- | ------------------------------------------------ |
| [[Key]]    | A String | A string key used to globally identify a Symbol. |
| [[Symbol]] | A Symbol | A symbol that can be retrieved from any realm.   |

#### 19.4.2.3 Symbol.hasInstance <div id="sec-symbol.hasinstance"></div>

The initial value of Symbol.hasInstance is the well-known symbol @@hasInstance (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.4 Symbol.isConcatSpreadable <div id="sec-symbol.isconcatspreadable"></div>

The initial value of Symbol.isConcatSpreadable is the well-known symbol @@isConcatSpreadable (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.5 Symbol.iterator <div id="sec-symbol.iterator"></div>

The initial value of Symbol.iterator is the well-known symbol @@iterator (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.6 Symbol.keyFor ( sym ) <div id="sec-symbol.keyfor"></div>

When Symbol.keyFor is called with argument sym it performs the following steps:

1. If Type(sym) is not Symbol, throw a TypeError exception.
2. For each element e of the GlobalSymbolRegistry List (see 19.4.2.2), do
1. If SameValue(e.[[Symbol]], sym) is true, return e.[[Key]].
3. Assert: GlobalSymbolRegistry does not currently contain an entry for sym.
4. Return undefined.

#### 19.4.2.7 Symbol.match <div id="sec-symbol.match"></div>

The initial value of Symbol.match is the well-known symbol @@match (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.8 Symbol.prototype <div id="sec-symbol.prototype"></div>

The initial value of Symbol.prototype is the intrinsic object %SymbolPrototype%.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.9 Symbol.replace <div id="sec-symbol.replace"></div>

The initial value of Symbol.replace is the well-known symbol @@replace (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.10 Symbol.search <div id="sec-symbol.search"></div>

The initial value of Symbol.search is the well-known symbol @@search (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.11 Symbol.species <div id="sec-symbol.species"></div>

The initial value of Symbol.species is the well-known symbol @@species (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

#### 19.4.2.12 Symbol.split <div id="sec-symbol.split"></div>

The initial value of Symbol.split is the well-known symbol @@split (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

#### 19.4.2.13 Symbol.toPrimitive <div id="sec-symbol.toprimitive"></div>

The initial value of Symbol.toPrimitive is the well-known symbol @@toPrimitive (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.14 Symbol.toStringTag <div id="sec-symbol.tostringtag"></div>

The initial value of Symbol.toStringTag is the well-known symbol @@toStringTag (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.15 Symbol.unscopables <div id="sec-symbol.unscopables"></div>

The initial value of Symbol.unscopables is the well-known symbol @@unscopables (Table 1).

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

### 19.4.3 Symbol 原型对象属性 <div id="sec-properties-of-the-symbol-prototype-object"></div>

The Symbol prototype object:

- is the intrinsic object %SymbolPrototype%.
- is an ordinary object.
- is not a Symbol instance and does not have a [[SymbolData]] internal slot.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

The abstract operation thisSymbolValue(value) performs the following steps:

1. If Type(value) is Symbol, return value.
2. If Type(value) is Object and value has a [[SymbolData]] internal slot, then
1. Let s be value.[[SymbolData]].
2. Assert: Type(s) is Symbol.
3. Return s.
3. Throw a TypeError exception.

#### 19.4.3.1 Symbol.prototype.constructor <div id="sec-symbol.prototype.constructor"></div>

The initial value of Symbol.prototype.constructor is the intrinsic object %Symbol%.

#### 19.4.3.2 get Symbol.prototype.description <div id="sec-symbol.prototype.description"></div>

Symbol.prototype.description is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. Let s be the this value.
2. Let sym be ? thisSymbolValue(s).
3. Return sym.[[Description]].

#### 19.4.3.3 Symbol.prototype.toString ( ) <div id="sec-symbol.prototype.tostring"></div>

The following steps are taken:

1. Let sym be ? thisSymbolValue(this value).
2. Return SymbolDescriptiveString(sym).

##### 19.4.3.3.1 RS: SymbolDescriptiveString ( sym ) <div id="sec-symboldescriptivestring"></div>

When the abstract operation SymbolDescriptiveString is called with argument sym, the following steps are taken:

1. Assert: Type(sym) is Symbol.
2. Let desc be sym's [[Description]] value.
3. If desc is undefined, set desc to the empty string.
4. Assert: Type(desc) is String.
5. Return the string-concatenation of "Symbol(", desc, and ")"

#### 19.4.3.4 Symbol.prototype.valueOf ( ) <div id="sec-symbol.prototype.valueof"></div>

The following steps are taken:

1. Return ? thisSymbolValue(this value).

#### 19.4.3.5 Symbol.prototype [ @@toPrimitive ] ( hint ) <div id="sec-symbol.prototype-@@toprimitive"></div>

This function is called by ECMAScript language operators to convert a Symbol object to a primitive value. The allowed values for hint are "default", "number", and "string".

When the @@toPrimitive method is called with argument hint, the following steps are taken:

1. Return ? thisSymbolValue(this value)

The value of the name property of this function is "[Symbol.toPrimitive]".

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 19.4.3.6 Symbol.prototype [ @@toStringTag ] <div id="sec-symbol.prototype-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value "Symbol".

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }

### 19.4.4 Symbol 实例属性 <div id="sec-properties-of-symbol-instances"></div>

Symbol instances are ordinary objects that inherit properties from the Symbol prototype object. Symbol instances have a [[SymbolData]] internal slot. The [[SymbolData]] internal slot is the Symbol value represented by this Symbol object.

## 19.5 Error 对象 <div id="sec-error-objects"></div>
Instances of Error objects are thrown as exceptions when runtime errors occur. The Error objects may also serve as base objects for user-defined exception classes.

### 19.5.1 The Error 构造器 <div id="sec-error-constructor"></div>

The Error constructor:

- is the intrinsic object %Error%.
- is the initial value of the Error property of the global object.
- creates and initializes a new Error object when called as a function rather than as a constructor. Thus the function call Error(…) is equivalent to the object creation expression new Error(…) with the same arguments.
- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. Subclass constructors that intend to inherit the specified Error behaviour must include a super call to the Error constructor to create and initialize subclass instances with an [[ErrorData]] internal slot.

#### 19.5.1.1 Error ( message ) <div id="sec-error-message"></div>

When the Error function is called with argument message, the following steps are taken:

1. If NewTarget is undefined, let newTarget be the active function object, else let newTarget be NewTarget.
2. Let O be ? OrdinaryCreateFromConstructor(newTarget, "%ErrorPrototype%", « [[ErrorData]] »).
3. If message is not undefined, then
  1. Let msg be ? ToString(message).
  2. Let msgDesc be the PropertyDescriptor { [[Value]]: msg, [[Writable]]: true, [[Enumerable]]: false,
    [[Configurable]]: true }.
  3. Perform ! DefinePropertyOrThrow(O, "message", msgDesc).
4. Return O.

### 19.5.2 Properties of the Error 构造器属性 <div id="sec-properties-of-the-error-constructor"></div>

The Error constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 19.5.2.1 Error.prototype <div id="sec-error.prototype"></div>

The initial value of Error.prototype is the intrinsic object %ErrorPrototype%.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 19.5.3 Properties of the Error 原型对象属性 <div id="sec-properties-of-the-error-prototype-object"></div>

The Error prototype object:

- is the intrinsic object %ErrorPrototype%.
- is an ordinary object.
- is not an Error instance and does not have an [[ErrorData]] internal slot.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

#### 19.5.3.1 Error.prototype.constructor <div id="sec-error.prototype.constructor"></div>

The initial value of Error.prototype.constructor is the intrinsic object %Error%.

#### 19.5.3.2 Error.prototype.message <div id="sec-error.prototype.message"></div>

The initial value of Error.prototype.message is the empty String.

#### 19.5.3.3 Error.prototype.name <div id="sec-error.prototype.name"></div>

The initial value of Error.prototype.name is "Error".

#### 19.5.3.4 Error.prototype.toString ( ) <div id="sec-error.prototype.tostring"></div>

The following steps are taken:

1. Let O be the this value.
2. If Type(O) is not Object, throw a TypeError exception.
3. Let name be ? Get(O, "name").
4. If name is undefined, set name to "Error"; otherwise set name to ? ToString(name).
5. Let msg be ? Get(O, "message").
6. If msg is undefined, set msg to the empty String; otherwise set msg to ? ToString(msg).
7. If name is the empty String, return msg.
8. If msg is the empty String, return name.
9. Return the string-concatenation of name, the code unit 0x003A (COLON), the code unit 0x0020 (SPACE), and msg.

### 19.5.4 Properties of Error 实例属性 <div id="sec-properties-of-error-instances"></div>

Error instances are ordinary objects that inherit properties from the Error prototype object and have an [[ErrorData]] internal slot whose value is undefined. The only specified uses of [[ErrorData]] is to identify Error and NativeError instances as Error objects within Object.prototype.toString.

### 19.5.5 本标准值中使用的原生错误类型 <div id="sec-native-error-types-used-in-this-standard"></div>

A new instance of one of the NativeError objects below is thrown when a runtime error is detected. All of these objects share the same structure, as described in 19.5.6.

#### 19.5.5.1 EvalError <div id="sec-native-error-types-used-in-this-standard-evalerror"></div>

This exception is not currently used within this specification. This object remains for compatibility with previous editions of this specification.

#### 19.5.5.2 RangeError <div id="sec-native-error-types-used-in-this-standard-rangeerror"></div>

Indicates a value that is not in the set or range of allowable values.

#### 19.5.5.3 ReferenceError <div id="sec-native-error-types-used-in-this-standard-referenceerror"></div>

Indicate that an invalid reference value has been detected.

#### 19.5.5.4 SyntaxError <div id="sec-native-error-types-used-in-this-standard-syntaxerror"></div>

Indicates that a parsing error has occurred.

#### 19.5.5.5 TypeError <div id="sec-native-error-types-used-in-this-standard-typeerror"></div>

TypeError is used to indicate an unsuccessful operation when none of the other NativeError objects are an appropriate indication of the failure cause.

#### 19.5.5.6 URIError <div id="sec-native-error-types-used-in-this-standard-urierror"></div>

Indicates that one of the global URI handling functions was used in a way that is incompatible with its definition.

### 19.5.6 NativeError 对象结构 <div id="sec-nativeerror-object-structure"></div>

When an ECMAScript implementation detects a runtime error, it throws a new instance of one of the NativeError objects defined in 19.5.5. Each of these objects has the structure described below, differing only in the name used as the constructor name instead of NativeError, in the name property of the prototype object, and in the implementationdefined message property of the prototype object.

For each error object, references to NativeError in the definition should be replaced with the appropriate error object name from 19.5.5.

#### 19.5.6.1 NativeError 构造器 <div id="sec-nativeerror-constructors"></div>

Each NativeError constructor:

- creates and initializes a new NativeError object when called as a function rather than as a constructor. A call of the object as a function is equivalent to calling it as a constructor with the same arguments. Thus the function call NativeError(…) is equivalent to the object creation expression new NativeError(…) with the same arguments.

- is designed to be subclassable. It may be used as the value of an extends clause of a class definition. Subclass constructors that intend to inherit the specified NativeError behaviour must include a super call to the NativeError constructor to create and initialize subclass instances with an [[ErrorData]] internal slot.

##### 19.5.6.1.1 NativeError ( message ) <div id="sec-nativeerror"></div>

When a NativeError function is called with argument message, the following steps are taken:

1. If NewTarget is undefined, let newTarget be the active function object, else let newTarget be NewTarget.
2. Let O be ? OrdinaryCreateFromConstructor(newTarget, "%NativeErrorPrototype%", « [[ErrorData]] »).
3. If message is not undefined, then
1. Let msg be ? ToString(message).
2. Let msgDesc be the PropertyDescriptor { [[Value]]: msg, [[Writable]]: true, [[Enumerable]]: false,
  [[Configurable]]: true }.
3. Perform ! DefinePropertyOrThrow(O, "message", msgDesc).
4. Return O.

The actual value of the string passed in step 2 is either "%EvalErrorPrototype%", "%RangeErrorPrototype%", "%ReferenceErrorPrototype%", "%SyntaxErrorPrototype%", "%TypeErrorPrototype%", or "%URIErrorPrototype%" corresponding to which NativeError constructor is being defined.

#### 19.5.6.2 NativeError 构造器属性 <div id="sec-properties-of-the-nativeerror-constructors"></div>

Each NativeError constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %Error%.
- has a name property whose value is the String value `"NativeError"`.
- has the following properties:

##### 19.5.6.2.1 NativeError.prototype <div id="sec-nativeerror.prototype"></div>

The initial value of NativeError.prototype is a NativeError prototype object (19.5.6.3). Each NativeError
constructor has a distinct prototype object.

This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.5.6.3 NativeError 原型对象属性 <div id="sec-properties-of-the-nativeerror-prototype-objects"></div>

Each NativeError prototype object:

- is an ordinary object.
- is not an Error instance and does not have an [[ErrorData]] internal slot.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ErrorPrototype%

##### 19.5.6.3.1 NativeError.prototype.constructor <div id="sec-nativeerror.prototype.constructor"></div>

The initial value of the constructor property of the prototype for a given NativeError constructor is the corresponding intrinsic object %NativeError% (19.5.6.1).

##### 19.5.6.3.2 NativeError.prototype.message <div id="sec-nativeerror.prototype.message"></div>

The initial value of the message property of the prototype for a given NativeError constructor is the empty String.

##### 19.5.6.3.3 NativeError.prototype.name <div id="sec-nativeerror.prototype.name"></div>

The initial value of the name property of the prototype for a given NativeError constructor is the String value consisting of the name of the constructor (the name used instead of NativeError).

#### 19.5.6.4 NativeError 实例属性 <div id="sec-properties-of-nativeerror-instances"></div>

NativeError instances are ordinary objects that inherit properties from their NativeError prototype object and have an [[ErrorData]] internal slot whose value is undefined. The only specified use of [[ErrorData]] is by Object.prototype.toString (19.1.3.6) to identify Error or NativeError instances.