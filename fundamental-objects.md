# 19 基本对象
## 19.1 Object 对象 <div id="sec-object-objects"></div>
### 19.1.1 Object 构造器 <div id="sec-object-constructor"></div>

对象构造函数：

- 是内部对象％Object％。
- 是全局对象的对象属性的初始值。
- 在作为构造函数调用时创建一个新的普通对象。
- 在作为函数而不是构造函数调用时执行类型转换。
- 被设计为可子类化。它可以用作类定义的extends子句的值。

#### 19.1.1.1 Object ( [ value ] ) <div id="sec-object-value"></div>

使用可选参数值调用对象函数时，采取以下步骤:

1. 如果NewTarget既不是未定义的，也不是活动函数，则
   1. 返回 ? OrdinaryCreateFromConstructor(NewTarget, "%ObjectPrototype%").
2. 若 value 是 null, undefined 或不提供, 返回 ObjectCreate(%ObjectPrototype%).
3. 返回 ! ToObject(value).

对象构造函数的“length”属性是1

### 19.1.2 Object 构造器属性 <div id="sec-properties-of-the-object-constructor"></div>

对象构造函数：

- 有一个[[Prototype]]内部插槽，其值是内部对象％FunctionPrototype％。
- 具有“length”属性。
- 具有以下附加属性：

#### 19.1.2.1 Object.assign ( target, ...sources ) <div id="sec-object.assign"></div>

Assign函数用于将所有可枚举的自身属性的值从一个或多个源对象复制到目标对象。调用assign函数时，将执行以下步骤：

1. 令 to 为 ? ToObject(target).
2. 如果仅传递了一个参数, 返回 to.
3. 令 sources 为从第二个参数开始的参数值列表。
4. 对于每个元素nextsource的源，按升序排列，执行
    1. 如果nextSource既未定义也不为null，那么
        1. 令 from 为 ! ToObject(nextSource).
        2. 令 keys 为 ? from.\[\[OwnPropertyKeys]]().
        3. 对于按列表顺序的键的每个元素nextKey，执行
            1. 令 desc 为 ? from.\[\[GetOwnProperty]](nextKey).
            2. 如果desc不是undefined并且desc.[[Enumerable]]为true，那么
5. 返回 to.

分配函数的“length”属性为2。

#### 19.1.2.2 Object.create ( O, Properties ) <div id="sec-object.create"></div>

create函数使用指定的原型创建一个新对象。调用create函数时，将执行以下步骤：

1. 若 Type(O) 不是 Object 或 Null, 抛出 TypeError 异常。
2. 令 obj 为 ObjectCreate(O).
3. 若 Properties 不是 undefined，那么
1. 返回 ? ObjectDefineProperties(obj, Properties).
4. 返回 obj.

#### 19.1.2.3 Object.defineProperties ( O, Properties ) <div id="sec-object.defineproperties"></div>

defineProperties函数用于添加自己的属性和/或更新对象现有的自己属性的属性。调用defineProperties函数时，将执行以下步骤：

1. 返回 ? ObjectDefineProperties(O, Properties).

##### 19.1.2.3.1 RS: ObjectDefineProperties ( O, Properties ) <div id="sec-objectdefineproperties"></div>

具有参数O和属性的抽象操作ObjectDefineProperties执行以下步骤：

1. 若 Type(O) 不是 Object, 抛出 TypeError 异常。
2. 令 props 为 ? ToObject(Properties).
3. 令 keys 为 ? props.\[\[OwnPropertyKeys]]().
4. 令 descriptors 为新的空列表
5. 对于按列表顺序的键的每个元素nextKey，执行
   1. 令 propDesc 为 ? props.\[\[GetOwnProperty]](nextKey).
   2. 若 propDesc 不是 undefined 并且 propDesc.[[Enumerable]] 是 true，那么
        1. 令 descObj 为 ? Get(props, nextKey).
        2. 令 desc 为 ? ToPropertyDescriptor(descObj).
        3. 将由nextKey和desc组成的对（一个由两个元素组成的List）附加到描述符的末尾。
6. 对于列表中描述符中的每一对，执行
     1. 令 P 为对的第一个元素。
     2. 令 desc 为对的第二个元素。
     3. 执行 ? DefinePropertyOrThrow(O, P, desc).
7. 返回 O.

#### 19.1.2.4 Object.defineProperty ( O, P, Attributes ) <div id="sec-object.defineproperty"></div>

defineProperty函数用于添加自己的属性和/或更新对象现有的自己属性的属性。调用defineProperty函数时，将执行以下步骤：

1. 若 Type(O) 不是 Object, 抛出 TypeError 异常。
2. 令 key 为 ? ToPropertyKey(P).
3. 令 desc 为 ? ToPropertyDescriptor(Attributes).
4. 执行 ? DefinePropertyOrThrow(O, key, desc).
5. 返回 O

#### 19.1.2.5 Object.entries ( O ) <div id="sec-object.entries"></div>

当使用参数O调用entrys函数时，将执行以下步骤：

1. 令 obj 为 ? ToObject(O).
2. 令 nameList 为 ? EnumerableOwnPropertyNames(obj, "key+value").
3. 返回 CreateArrayFromList(nameList).

#### 19.1.2.6 Object.freeze ( O ) <div id="sec-object.freeze"></div>

调用冻结函数时，将执行以下步骤：

1. 若 Type(O) 不是 Object, 返回 O.
2. 令 status 为 ? SetIntegrityLevel(O, "frozen").
3. 若 status 是 false, 抛出 TypeError 异常。
4. 返回 O.

#### 19.1.2.7 Object.fromEntries ( iterable ) <div id="sec-object.fromentries"></div>

当使用参数iterable调用fromEntries方法时，将执行以下步骤：

1. 执行 ? RequireObjectCoercible(iterable).
2. 令 obj 为 ObjectCreate(%ObjectPrototype%).
3. 断言：obj是没有自己属性的可扩展普通对象。
4. 令 stepsDefine 为CreateDataPropertyOnObject函数中定义的算法步骤。
5. 令 adder 为 CreateBuiltinFunction(stepsDefine, « »).
6. 返回 ? AddEntriesFromIterable(obj, iterable, adder).

> 注：ECMAScript代码绝对不能直接访问为adder创建的函数。

##### 19.1.2.7.1 CreateDataPropertyOnObject Functions <div id="sec-create-data-property-on-object-functions"></div>

CreateDataPropertyOnObject函数是匿名内置函数。当使用参数key和value调用CreateDataPropertyOnObject函数时，将执行以下步骤：

1. 令 O 为 this 值.
2. 断言：Type(O) 是 Object.
3. 断言：O是可扩展的普通对象。
4. 令 propertyKey 为 ? ToPropertyKey(key).
5. 执行 ! CreateDataPropertyOrThrow(O, propertyKey, value).
6. 返回 undefined.

#### 19.1.2.8 Object.getOwnPropertyDescriptor ( O, P ) <div id="sec-object.getownpropertydescriptor"></div>

调用getOwnPropertyDescriptor函数时，将执行以下步骤：

1. 令 obj 为 ? ToObject(O).
2. 令 key 为 ? ToPropertyKey(P).
3. 令 desc 为 ? obj.\[\[GetOwnProperty]](key).
4. 返回 FromPropertyDescriptor(desc).

#### 19.1.2.9 Object.getOwnPropertyDescriptors ( O ) <div id="sec-object.getownpropertydescriptors"></div>

调用getOwnPropertyDescriptors函数时，将执行以下步骤：

1. 令 obj 为 ? ToObject(O).
2. 令 ownKeys 为 ? obj.\[\[OwnPropertyKeys]]().
3. 令 descriptors 为 ! ObjectCreate(%ObjectPrototype%).
4. 对于List顺序中ownKeys的每个元素键，执行
   1. 令 desc 为 ? obj.\[\[GetOwnProperty]](key).
   2. 令 descriptor 为 ! FromPropertyDescriptor(desc).
   3. 若 descriptor 不是 undefined, 执行 ! CreateDataProperty(descriptors, key, descriptor).
5. 返回 descriptors.

#### 19.1.2.10 Object.getOwnPropertyNames ( O ) <div id="sec-object.getownpropertynames"></div>

调用getOwnPropertyNames函数时，将执行以下步骤：

1. 返回 ? GetOwnPropertyKeys(O, String).

#### 19.1.2.11 Object.getOwnPropertySymbols ( O ) <div id="sec-object.getownpropertysymbols"></div>

使用参数O调用getOwnPropertySymbols函数时，将执行以下步骤：

1. 返回 ? GetOwnPropertyKeys(O, Symbol).

##### 19.1.2.11.1 RS: GetOwnPropertyKeys ( O, type ) <div id="sec-getownpropertykeys"></div>

使用参数O和类型调用抽象操作GetOwnPropertyKeys，其中类型O为对象，类型为ECMAScript规范类型String或Symbol之一。采取以下步骤：

1. 令 obj 为 ? ToObject(O).
2. 令 keys 为 ? obj.\[\[OwnPropertyKeys]]().
3. 令 nameList 为新的空列表
4. 对于按列表顺序的键的每个元素nextKey，执行
        1. 若 Type(nextKey) 是 type，那么
              1. 追加nextKey作为nameList的最后一个元素。
5. 返回 CreateArrayFromList(nameList).

#### 19.1.2.12 Object.getPrototypeOf ( O ) <div id="sec-object.getprototypeof"></div>

使用参数OF调用getPrototypeOf函数时，将执行以下步骤：

1. 令 obj 为 ? ToObject(O).
2. 返回 ? obj.\[\[GetPrototypeOf]]().

#### 19.1.2.13 Object.is ( value1, value2 ) <div id="sec-object.is"></div>

当使用参数value1和value2调用is函数时，将执行以下步骤：

1. 返回 SameValue(value1, value2).

#### 19.1.2.14 Object.isExtensible ( O ) <div id="sec-object.isextensible"></div>

当使用参数O调用is可扩展函数时，将执行以下步骤：

1. 若 Type(O) 不是 Object, 返回 false.
2. 返回 ? IsExtensible(O).

#### 19.1.2.15 Object.isFrozen ( O ) <div id="sec-object.isfrozen"></div>
 使用参数O调用isFrozen函数时，将执行以下步骤：

1. 若 Type(O) 不是 Object, 返回 true.
2. 返回 ? TestIntegrityLevel(O, "frozen").

#### 19.1.2.16 Object.isSealed ( O ) <div id="sec-object.issealed"></div>

当使用参数O调用is Sealed函数时，将执行以下步骤：

1. 若 Type(O) 不是 Object, 返回 true.
2. 返回 ? TestIntegrityLevel(O, "sealed").

#### 19.1.2.17 Object.keys ( O ) <div id="sec-object.keys"></div>

当使用参数O调用keys函数时，将执行以下步骤：

1. 令 obj 为 ? ToObject(O).
2. 令 nameList 为 ? EnumerableOwnPropertyNames(obj, "key").
3. 返回 CreateArrayFromList(nameList).

#### 19.1.2.18 Object.preventExtensions ( O ) <div id="sec-object.preventextensions"></div>

调用preventExtensions函数时，将执行以下步骤：

1. 若 Type(O) 不是 Object, 返回 O.
2. 令 status 为 ? O.\[\[PreventExtensions]]().
3. 若 status 是 false, 抛出 TypeError 异常。
4. 返回 O.

#### 19.1.2.19 Object.prototype <div id="sec-object.prototype"></div>

Object.prototype的初始值为内部对象％ObjectPrototype％。

该属性具有以下属性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.1.2.20 Object.seal ( O ) <div id="sec-object.seal"></div>

调用seal函数时，将执行以下步骤：

1. 若 Type(O) 不是 Object, 返回 O.
2. 令 status 为 ? SetIntegrityLevel(O, "sealed").
3. 若 status 是 false, 抛出 TypeError 异常。
4. 返回 O.

#### 19.1.2.21 Object.setPrototypeOf ( O, proto ) <div id="sec-object.setprototypeof"></div>

当使用参数O和proto调用setPrototypeOf函数时，将执行以下步骤：

1. 设置 O 为 ? RequireObjectCoercible(O).
2. 若 Type(proto) 不是 Object 或 Null, 抛出 TypeError 异常。
3. 若 Type(O) 不是 Object, 返回 O.
4. 令 status 为 ? O.\[\[SetPrototypeOf]](proto).
5. 若 status 是 false, 抛出 TypeError 异常。
6. 返回 O.

#### 19.1.2.22 Object.values ( O ) <div id="sec-object.values"></div>

当使用参数O调用values函数时，将执行以下步骤：

1. 令 obj 为 ? ToObject(O).
2. 令 nameList 为 ? EnumerableOwnPropertyNames(obj, "value").
3. 返回 CreateArrayFromList(nameList).

### 19.1.3 Object 原型对象属性 <div id="sec-properties-of-the-object-prototype-object"></div>

对象原型对象：

- 是内部对象％ObjectPrototype％。
- 是不变的原型异类对象
- 有一个[[Prototype]]内部插槽，其值为null。

#### 19.1.3.1 Object.prototype.constructor <div id="sec-object.prototype.constructor"></div>

Object.prototype.constructor的初始值为内部对象％Object％。

#### 19.1.3.2 Object.prototype.hasOwnProperty ( V ) <div id="sec-object.prototype.hasownproperty"></div>

当使用参数V调用hasOwnProperty方法时，将执行以下步骤：

1. 令 P 为 ? ToPropertyKey(V).
2. 令 O 为 ? ToObject(this value).
3. 返回 ? HasOwnProperty(O, P).

> 注：选择步骤1和2的顺序是为了确保即使this值undefined或为null，在本规范的先前版本中由步骤1引发的任何异常都将继续引发。

#### 19.1.3.3 Object.prototype.isPrototypeOf ( V ) <div id="sec-object.prototype.isprototypeof"></div>

使用参数V调用isPrototypeOf方法时，将执行以下步骤：

1. 若 Type(V) 不是 Object, 返回 false.
2. 令 O 为 ? ToObject(this value).
3. 重复，
   1. 设置 V 为 ? V.\[\[GetPrototypeOf]]().
   2. 若 V 是 null, 返回 false.
   3. 若 SameValue(O, V) 是 true, 返回 true.

> 注：对于V不是对象且this值undefined或为null的情况，步骤1和2的顺序保留了本规范先前版本指定的行为。

#### 19.1.3.4 Object.prototype.propertyIsEnumerable ( V ) <div id="sec-object.prototype.propertyisenumerable"></div>

当使用参数V调用propertyIsEnumerable方法时，将执行以下步骤：

1. 令 P 为 ? ToPropertyKey(V).
2. 令 O 为 ? ToObject(this value).
3. 令 desc 为 ? O.\[\[GetOwnProperty]](P).
4. 若 desc 是 undefined, 返回 false.
5. 返回 desc.[[Enumerable]].

>注1：该方法不考虑原型链中的对象。

> 注2：选择步骤1和2的顺序是为了确保即使this值为undefined或null，在本规范先前版本中由步骤1引发的任何异常都将继续引发。

#### 19.1.3.5 Object.prototype.toLocaleString ( [ reserved1 [ , reserved2 ] ] ) <div id="sec-object.prototype.tolocalestring"></div>

调用toLocaleString方法时，将执行以下步骤：

1. 令 O 为 this 值.
2. 返回 ? Invoke(O, "toString").

该函数的可选参数未使用，但旨在与ECMA-402 toLocaleString函数使用的参数模式相对应。不包含ECMA-402支持的实现不得将这些参数位置用于其他目的。

>注1：该函数为没有特定于语言环境的toString行为的对象提供通用的toLocaleString实现。数组，数字，日期和类型数组提供了它们自己的区域设置敏感的toLocaleString方法。

> 注2：ECMA-402有意不提供此默认实施方式的替代方法。

#### 19.1.3.6 Object.prototype.toString ( ) <div id="sec-object.prototype.tostring"></div>

调用toString方法时，将执行以下步骤：

1. 若 this 值是 undefined, 返回 "[object Undefined]".
2. 若 this 值是 null, 返回 "[object Null]".
3. 令 O 为 ! ToObject(this value).
4. 令 isArray 为 ? IsArray(O).
5. 若 isArray 是 true, 令 builtinTag 为 "Array".
6. 否则，若 O 是 String 异类对象, 令 builtinTag 为 "String".
7. 否则，若 O 具有 [[ParameterMap]] 内部插槽, 令 builtinTag 为 "Arguments".
8. 否则，若 O 具有 [[Call]] 内部方法, 令 builtinTag 为 "Function".
9. 否则，若 O 具有 [[ErrorData]] 内部插槽, 令 builtinTag 为 "Error".
10. 否则，若 O 具有 [[BooleanData]] 内部插槽, 令 builtinTag 为 "Boolean".
11. 否则，若 O 具有 [[NumberData]] 内部插槽, 令 builtinTag 为 "Number".
12. 否则，若 O 具有 [[DateValue]] 内部插槽, 令 builtinTag 为 "Date".
13. 否则，若 O 具有 [[RegExpMatcher]] 内部插槽, 令 builtinTag 为 "RegExp".
14. 否则, 令 builtinTag 为 "Object".
15. 令 tag 为 ? Get(O, @@toStringTag).
16. 若 Type(tag) 不是 String, 设置 tag 为 builtinTag.
17. 返回 "[object ", tag, and "]" 的字符串连接

此函数是％ObjProto_toString％内部对象。

> 注：从历史上看，此函数有时用于访问[[Class]]内部插槽的String值，该内部插槽在本规范的先前版本中用作各种内置对象的标称类型标记。上面对toString的定义保留了与使用toString来测试那些特定种类的内置对象的旧代码的兼容性。它没有为其他类型的内置或程序定义的对象提供可靠的类型测试机制。另外，程序可以使用@@ toStringTag，这将使此类旧式测试的可靠性无效。

#### 19.1.3.7 Object.prototype.valueOf ( ) <div id="sec-object.prototype.valueof"></div>

调用valueOf方法时，将执行以下步骤

1. 返回 ? ToObject(this value).

此函数是％ObjProto_valueOf％内部对象。

### 19.1.4 Object 实例属性 <div id="sec-properties-of-object-instances"></div>

对象实例除了从Object原型对象继承的属性外，没有其他特殊属性。

## 19.2 Function 对象 <div id="sec-function-objects"></div>
### 19.2.1 Function 构造器 <div id="sec-function-constructor"></div>

函数构造函数：

- 是内部对象％Function％。
- 是全局对象的Function属性的初始值。
- 当作为函数而不是构造函数调用时，创建并初始化一个新的函数对象。因此，函数调用Function（…）等效于具有相同参数的对象创建表达式new Function（…）。
- 设计为可归类的。它可以用作类定义的extends子句的值。打算继承指定Function行为的子类构造函数必须包括对Function构造函数的super调用，以使用内置函数行为所需的内部插槽来创建和初始化子类实例。用于定义函数对象的所有ECMAScript语法形式都将创建Function的实例。
- 除了内置的GeneratorFunction，AsyncFunction和AsyncGeneratorFunction子类之外，没有语法方法可以创建Function子类的实例。

#### 19.2.1.1 Function ( p1, p2, … , pn, body ) <div id="sec-function-p1-p2-pn-body"></div>

最后一个参数指定函数的主体（可执行代码）；前面的任何参数都指定形式参数。

当使用某些参数p1，p2，…，pn主体（其中n可能为0，即没有“ p”参数，也可能不提供主体）调用Function函数时，将执行以下步骤采取：

1. 令 C 为活动函数对象。
2. 令 args 为由[[Call]]或[[Construct]]传递给此函数的argumentsList。
3. 返回 ? CreateDynamicFunction(C, NewTarget, "normal", args).

> 注：允许但不必为每个要指定的形式参数使用一个参数。例如，以下所有三个表达式产生相同的结果：
>
> new Function("a", "b", "c", "return a+b+c")
> new Function("a, b, c", "return a+b+c")
> new Function("a,b", "c", "return a+b+c")

##### 19.2.1.1.1 RS: CreateDynamicFunction ( constructor, newTarget, kind, args ) <div id="sec-createdynamicfunction"></div>

抽象操作CreateDynamicFunction用参数构造函数，newTarget，kind和args调用。构造函数是执行此操作的构造函数，newTarget是new最初应用于的构造函数，kind是“ normal”，“ generator”，“ async”或“ async generator”，而args是包含以下内容的List传递给构造函数的实际参数值。采取以下步骤：

1. 断言：执行上下文堆栈至少具有两个元素。
2. 令 callerContext 为执行上下文堆栈的第二个元素。
3. 令 callerRealm 为 callerContext 的作用域.
4. 令 calleeRealm 为大年作用域记录
5. 执行 ? HostEnsureCanCompileStrings(callerRealm, calleeRealm).
6. 若 newTarget 是 undefined, 设置 newTarget 为 constructor.
7. 若 kind 是 "normal"，那么
    1. 令 goal 为语法标记 FunctionBody[~Yield, ~Await] .
    2. 令 parameterGoal 为语法标记 FormalParameters[~Yield, ~Await] .
    3. 令 fallbackProto 为 "%FunctionPrototype%".
8. 否则，若 kind 是 "generator"，那么
    1. 令 goal 为语法标记 GeneratorBody.
    2. 令 parameterGoal 为语法标记 FormalParameters[+Yield, ~Await] .
    3. 令 fallbackProto 为 "%Generator%".
9. 否则，若 kind 是 "async"，那么
    1. 令 goal 为语法标记 AsyncFunctionBody.
    2. 令 parameterGoal 为语法标记 FormalParameters[~Yield, +Await] .
    3. 令 fallbackProto 为 "%AsyncFunctionPrototype%".
10. 否则，
    1. 断言：kind 是 "async generator".
    2. 令 goal 为语法标记 AsyncGeneratorBody.
    3. 令 parameterGoal 为语法标记 FormalParameters[+Yield, +Await] .
    4. 令 fallbackProto 为 "%AsyncGenerator%".
11. 令 argCount 为 args 中的元素数。
12. 令 P 为空字符串。
13. 若 argCount = 0, 令 bodyText 为空字符串
14. 否则，若 argCount = 1, 令 bodyText 为 args[0].
15. 否则，argCount > 1,
    1. 令 firstArg 为 args[0].
    2. 设置 P 为 ? ToString(firstArg).
    3. 令 k 为 1.
    4. 重复， 当 k < argCount - 1
        1. 令 nextArg 为 args[k].
        2. 令 nextArgString 为 ? ToString(nextArg).
        3. 设置 P 为 P, "," (a comma), and nextArgString 的先前值的字符串连接的值
        4. k 增加 1.
    5. 令 bodyText 为 args[k].
16. 设置 bodyText 为 ? ToString(bodyText).
17. 令 parameters 为使用parameterGoal作为目标符号的解析P的结果，解释为6.1.4中描述的UTF-16编码的Unicode文本。如果解析失败，则抛出SyntaxError异常。
18. 令 body 为解析bodyText的结果，使用目标作为目标符号，解释为6.1.4中描述的UTF-16编码的Unicode文本。如果解析失败，则抛出SyntaxError异常。
19. 令 strict 为 body 的 ContainsUseStrict。
20. 如果检测到参数或主体的任何静态语义错误，则根据错误的类型，抛出SyntaxError或ReferenceError异常。如果strict为true，则将应用UniqueFormalParameters：FormalParameters的Early Error规则。解析和早期错误检测可以以依赖于实现的方式混合在一起。
21. 如果strict为true而IsSimpleParameterList为假, 抛出 SyntaxError 异常。
22. 如果parameters的BoundNames的任何元素也出现在body的LexicallyDeclaredNames中，抛出 SyntaxError 异常。
23. 若 body 包含 SuperCall 是 true, 抛出 SyntaxError 异常。
24. 若 parameters 包含 SuperCall 是 true, 抛出 SyntaxError 异常。
25. 若 body 包含 SuperProperty 是 true, 抛出 SyntaxError 异常。
26. 若 parameters 包含 SuperProperty 是 true, 抛出 SyntaxError 异常。
27. 若 kind 是 "generator" 或  "async generator"，那么
    1. 若 parameters 包含 YieldExpression 是 true, 抛出 SyntaxError 异常。
28. 若 kind 是 "async" 或 "async generator"，那么
    1. 若 parameters 包含 AwaitExpression 是 true, 抛出 SyntaxError 异常。
29. 若 strict 是 true，那么
    1. 如果parameters的BoundNames包含任何重复的元素，抛出 SyntaxError 异常。
30. 令 proto 为 ? GetPrototypeFromConstructor(newTarget, fallbackProto).
31. 令 F 为 FunctionAllocate(proto, strict, kind).
32. 令 realmF 为 F.[[Realm]].
33. 令 scope 为 realmF.[[GlobalEnv]].
34. 执行 FunctionInitialize(F, Normal, parameters, body, scope).
35. 若 kind 是 "generator"，那么
    1. 令 prototype 为 ObjectCreate(%GeneratorPrototype%).
    2. 执行 DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
36. 否则，若 kind 是 "async generator"，那么
     1. 令 prototype 为 ObjectCreate(%AsyncGeneratorPrototype%).
     2. 执行 DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
37. 否则，若 kind 是 "normal", 执行 MakeConstructor(F).
38. 注意：异步函数不是可构造的，也没有[[Construct]]内部方法或“prototype”属性
39. 执行 SetFunctionName(F, "anonymous").
40. 令 prefix 为表47中与kind相关的前缀。
41. 令 sourceText 为 prefix, " anonymous(", P, 0x000A (LINE FEED), ") {", 0x000A (LINE FEED), bodyText, 0x000A (LINE FEED), and "}" 的字符串连接
42. 设置 F.[[SourceText]] 为 sourceText.
43. 返回 F.

> 注意，对于使用CreateDynamicFunction创建的每个非异步函数，都会创建一个prototype属性，以提供将该函数用作构造函数的可能性。

表47:动态函数SourceText前缀

| Kind              | Prefix            |
| ----------------- | ----------------- |
| "normal"          | "function"        |
| "generator"       | "function*"       |
| "async"           | "async function"  |
| "async generator" | "async function*" |

### 19.2.2 Function 构造器属性 <div id="sec-properties-of-the-function-constructor"></div>

构造函数的函数:

- 本身就是一个内置函数对象。
- 有一个[[Prototype]]内部槽，其值为内部对象%FunctionPrototype%。
- 具有以下特性:

#### 19.2.2.1 Function.length <div id="sec-function.length"></div>

这是一个值为1的数据属性。该属性的属性为 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }。

#### 19.2.2.2 Function.prototype <div id="sec-function.prototype"></div>

Function.prototyp的值为%FunctionPrototype%，即内部函数原型对象。

此属性具有属性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 19.2.3 Function 原型对象属性 <div id="sec-properties-of-the-function-prototype-object"></div>

函数原型对象：

- 是内部对象%FunctionPrototype%。
- 本身就是一个内置函数对象。
- 接受任何参数并在调用时返回未定义的值。
- 没有[[Construct]]内部方法；它不能与新操作符一起用作构造函数。
- 有一个[[Prototype]]内部槽，其值为内部对象%ObjectPrototype%。
- 没有原型属性。
- 具有“length”属性，其值为0。
- 具有name属性，其值为空字符串。

> NOTE The Function prototype object is specified to be a function object to ensure compatibility with ECMAScript code that was created prior to the ECMAScript 2015 specification.

#### 19.2.3.1 Function.prototype.apply ( thisArg, argArray ) <div id="sec-function.prototype.apply"></div>

当使用参数thisArg和argArray调用apply方法时，需要执行以下步骤:

1. 令 func 为 this 值.
2. 若 IsCallable(func) 是 false, 抛出 TypeError 异常。
3. 若 argArray 是 undefined 或 null，那么
   1. 执行 PrepareForTailCall().
   2. 返回 ? Call(func, thisArg).
4. 令 argList 为 ? CreateListFromArrayLike(argArray).
5. 执行 PrepareForTailCall().
6. 返回 ? Call(func, thisArg, argList).

>注 1：将thisArg值作为this值传递，而不进行任何修改。这是版本3的一个变化，其中undefined或null thisArg被替换为全局对象，ToObject应用于所有其他值，结果作为This值传递。即使没有修改就传递了thisArg，非严格函数仍然在进入函数时执行这些转换。

> 注 2：如果func是一个箭头函数或绑定函数，那么这个thisArg将被第5步中的函数[[Call]]忽略

#### 19.2.3.2 Function.prototype.bind ( thisArg, ...args ) <div id="sec-function.prototype.bind"></div>

当绑定方法被参数thisArg和零个或多个args调用时，它执行以下步骤:

1. 令 Target 为 this 值.
2. 若 IsCallable(Target) 是 false, 抛出 TypeError 异常。
3. 令 args 为一个新的(可能是空的)列表，由thisArg之后提供的所有参数值按顺序组成。
4. 令 F 为 ? BoundFunctionCreate(Target, thisArg, args).
5. 令 targetHasLength 为 ? HasOwnProperty(Target, "length").
6. 若 targetHasLength 是 true，那么
   1. 令 targetLen 为 ? Get(Target, "length").
   2. 若 Type(targetLen) 不是 Number, 令 L 为 0.
   3. 否则,
        1. 设置 targetLen 为 ! ToInteger(targetLen).
        2. 令 L 为0和targetLen的结果减去args的元素数中较大的数。
7. 否则，令 L 为 0.
8. 执行 ! SetFunctionLength(F, L).
9. 令 targetName 为 ? Get(Target, "name").
10. 若 Type(targetName) 不是 String, 设置 targetName 为空字符串
11. 执行 SetFunctionName(F, targetName, "bound").
12. 返回 F.

> 注 1：使用Function.prototype创建的Function.prototype.bind是异类对象。它们也没有原型属性。

> 注 2：如果Target是一个箭头函数或绑定函数，那么传递给这个方法的thisArg将不会被后续的F调用所使用

#### 19.2.3.3 Function.prototype.call ( thisArg, ...args ) <div id="sec-function.prototype.call"></div>

当使用参数thisArg和零个或多个args调用调用方法时，将采取以下步骤:

1. 令 func 为 this 值.
2. 若 IsCallable(func) 是 false, 抛出 TypeError 异常。
3. 令 argList 为新的空列表
4. 如果使用多个参数调用此方法，则按照从左到右的顺序，从第二个参数开始，将每个参数附加为argList的最后一个元素。
5. 执行 PrepareForTailCall().
6. 返回 ? Call(func, thisArg, argList).

> 注 1：将thisArg值作为this值传递，而不进行任何修改。这是版本3的一个变化，其中未定义的或null thisArg被替换为全局对象，ToObject应用于所有其他值，结果作为This值传递。即使没有修改就传递了thisArg，非严格函数仍然在进入函数时执行这些转换。

> 注 2：如果func是一个箭头函数或绑定函数，那么这个thisArg将被第5步中的函数[[Call]]忽略。

#### 19.2.3.4 Function.prototype.constructor <div id="sec-function.prototype.constructor"></div>

Function.prototype.constructor的初始值是内部对象%Function%。

#### 19.2.3.5 Function.prototype.toString ( ) <div id="sec-function.prototype.tostring"></div>

当toString方法被调用时，需要执行以下步骤:

1. 令 func 为 this 值.
2. 如果func是绑定函数异类对象或内置函数对象，则返回依赖于实现的 func 的字符串源代码表示。该表示形式必须具有NativeFunction的语法。此外，如果func是众所周知的内在对象，并且未标识为匿名函数，则返回的String中与PropertyName匹配的部分必须是func的name属性的初始值。
3. 如果Type(func)是Object并且func具有[[SourceText]]内部插槽，并且Type(func.[[SourceText]])是String和 ! HostHasSourceTextAvailable(func)为true，那么返回func.[[SourceText]]
4. 若 Type(func) 是 Object 并且 IsCallable(func) 是 true，那么 返回返回func的与实现相关的String源代码表示形式。该表示形式必须具有NativeFunction的语法。
5. 抛出 TypeError 异常。

```
NativeFunction :
	function PropertyName[~Yield, ~Await] opt ( FormalParameters[~Yield, ~Await] ) { [
native code ] }
```

#### 19.2.3.6 Function.prototype [ @@hasInstance ] ( V ) <div id="sec-function.prototype-@@hasinstance"></div>

当使用值V调用对象F的@@hasInstance方法时，将执行以下步骤：

1. 令 F 为 this 值.
2. 返回 ? OrdinaryHasInstance(F, V).

该函数的名称属性的值为“ [Symbol.hasInstance]”。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

> 注：这是大多数函数继承的@@hasInstance的默认实现。 @@hasInstance由instanceof运算符调用，以确定某个值是否为特定构造函数的实例。诸如
>
> v instanceof F
>
> 解释执行为
>
> F\[@@hasInstance](v)
>
> 构造函数可以通过在函数上暴露不同的@@hasInstance方法来控制instanceof将哪些对象识别为其实例。

此属性是不可写的且不可配置的，以防止可能被用来全局公开绑定函数的目标函数的篡改。

### 19.2.4 Function 实例 <div id="sec-function-instances"></div>

每个Function实例都是ECMAScript函数对象，并且具有在表27中列出的内部插槽。使用Function.prototype.bind方法（19.2.3.2）创建的函数对象具有在表28中列出的内部插槽。

函数实例具有以下属性：

#### 19.2.4.1 length <div id="sec-function-instances-length"></div>

“ length”属性的值是一个整数，表示该函数期望的典型参数数量。但是，该语言允许使用其他一些参数来调用该函数。当函数调用多个参数而不是由其“ length”属性指定的数字时，其行为取决于函数。此属性具有属性{[[[Writable]]：false，[[Enumerable]]：false，[[Configurable]]：true}。

#### 19.2.4.2 name <div id="sec-function-instances-name"></div>

没有与此规范关联的上下文名称的匿名函数对象不具有名称拥有的属性，而是继承％FunctionPrototype％的name属性。

#### 19.2.4.3 prototype <div id="sec-function-instances-prototype"></div>

可用作构造函数的函数实例具有prototype属性。每当创建这样的Function实例时，也会创建另一个普通对象，该对象是函数的prototype属性的初始值。除非另有说明，否则原型属性的值将用于初始化在将该函数作为构造函数调用时创建的对象的[[Prototype]]内部插槽。

该属性具有以下特性 { [[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }.

> 注：使用Function.prototype.bind或通过解释执行MethodDefinition（不是GeneratorMethod或AsyncGeneratorMethod）或ArrowFunction创建的函数对象没有原型属性。

### 19.2.5 HostHasSourceTextAvailable ( func ) <div id="sec-hosthassourcetextavailable"></div>

HostHasSourceTextAvailable是实现定义的抽象操作，允许主机环境阻止为给定功能提供源文本。

在所有情况下，HostHasSourceTextAvailable的实现都必须正常完成。该操作必须在参数方面具有确定性。每次使用特定函数作为参数调用它时，它都必须返回相同的完成记录。 HostHasSourceTextAvailable的默认实现是无条件返回值为true的正常完成。

## 19.3 Boolean 对象 <div id="sec-boolean-objects"></div>
### 19.3.1 Boolean 构造器 <div id="sec-boolean-constructor"></div>

布尔构造函数:

- 是内部对象％Boolean％。

- 是全局对象的Boolean属性的初始值。

- 在作为构造函数调用时创建并初始化一个新的布尔对象。

- 当作为函数而不是构造函数调用时执行类型转换。

- 设计为可归类的。它可以用作类定义的extends子句的值。打算继承指定的Boolean行为的子类构造函数必须包括对Boolean构造函数的super调用，以使用[[BooleanData]]内部插槽创建和初始化子类实例。


#### 19.3.1.1 Boolean ( value ) <div id="sec-boolean-constructor-boolean-value"></div>

当使用参数值调用Boolean时，将执行以下步骤：

1. 令 b 为 ToBoolean(value).
2. 若 NewTarget 是 undefined, 返回 b.
3. 令 O 为 ? OrdinaryCreateFromConstructor(NewTarget, "%BooleanPrototype%", « [[BooleanData]] »).
4. 设置 O.[[BooleanData]] 为 b.
5. 返回 O.

### 19.3.2 Boolean 构造器属性 <div id="sec-properties-of-the-boolean-constructor"></div>

布尔构造函数：

- 有一个[[Prototype]]内部插槽，其值是内部对象％FunctionPrototype％。
- 具有以下属性：

#### 19.3.2.1 Boolean.prototype <div id="sec-boolean.prototype"></div>

Boolean.prototype的初始值为内部对象％BooleanPrototype％。

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 19.3.3 Boolean 原型对象属性 <div id="sec-properties-of-the-boolean-prototype-object"></div>

布尔原型对象：

- 是内部对象％BooleanPrototype％。
- 是一个普通的对象。
- 它本身是一个布尔对象；它具有一个[[BooleanData]]内部插槽，其值为false。
- 有一个[[Prototype]]内部插槽，其值是内部对象％ObjectPrototype％。

抽象操作thisBooleanValue（value）执行以下步骤：

1. 若 Type(value) 是 Boolean, 返回 value.
2. 若 Type(value) 是 Object and value has a [[BooleanData]] internal slot，那么
    1. 令 b 为 value.[[BooleanData]].
    2. 断言：Type(b) is Boolean.
    3. 返回 b.
3. 抛出 TypeError 异常。

#### 19.3.3.1 Boolean.prototype.constructor <div id="sec-boolean.prototype.constructor"></div>

Boolean.prototype.constructor的初始值为内部对象％Boolean％。

#### 19.3.3.2 Boolean.prototype.toString ( ) <div id="sec-boolean.prototype.tostring"></div>

采取以下步骤：

1. 令 b 为 ? thisBooleanValue(this value).
2. 若 b 是 true, 返回 "true"; 否则，返回 "false".

#### 19.3.3.3 Boolean.prototype.valueOf ( ) <div id="sec-boolean.prototype.valueof"></div>

采取以下步骤：

1. 返回 ? thisBooleanValue(this value).

### 19.3.4 Boolean 实例属性 <div id="sec-properties-of-boolean-instances"></div>

布尔实例是从布尔原型对象继承属性的普通对象。布尔型实例具有一个[[BooleanData]]内部插槽。内部插槽[[BooleanData]]是此布尔对象表示的布尔值。

## 19.4 Symbol 对象 <div id="sec-symbol-objects"></div>
### 19.4.1 Symbol 构造器 <div id="sec-symbol-constructor"></div>

Symbol构造函数：

- 是内部对象％Symbol％。
- 是全局对象的Symbol属性的初始值。
- 作为函数调用时，返回一个新的Symbol值。
- 不适用于新运算符。
- 不打算被子类化。
- 可以用作类定义的extends子句的值，但是对其的超级调用将导致例外。

#### 19.4.1.1 Symbol ( [ description ] ) <div id="sec-symbol-description"></div>

使用可选参数description调用Symbol时, 采取以下步骤：

1. 若 NewTarget 不是 undefined, 抛出 TypeError 异常。
2. 若 description 是 undefined, 令 descString 为 undefined.
3. 否则，令 descString 为 ? ToString(description).
4. 返回 a new unique Symbol value whose [[Description]] value is descString.

### 19.4.2 Symbol 构造器属性 <div id="sec-properties-of-the-symbol-constructor"></div>

The Symbol constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 19.4.2.1 Symbol.asyncIterator <div id="sec-symbol.asynciterator"></div>

The initial value of Symbol.asyncIterator is the well known symbol @@asyncIterator (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.2 Symbol.for ( key ) <div id="sec-symbol.for"></div>

When Symbol.for is called with argument key it performs the following steps:

1. 令 stringKey 为 ? ToString(key).
2. For each element e of the GlobalSymbolRegistry List, do
1. 若 SameValue(e.[[Key]], stringKey) 是 true, 返回 e.[[Symbol]].
3. 断言：GlobalSymbolRegistry does not currently contain an entry for stringKey.
4. 令 newSymbol 为 a new unique Symbol value whose [[Description]] value is stringKey.
5. Append the Record { [[Key]]: stringKey, [[Symbol]]: newSymbol } to the GlobalSymbolRegistry List.
6. 返回 newSymbol.

The GlobalSymbolRegistry is a List that is globally available. It is shared by all realms. Prior to the evaluation of any ECMAScript code it is initialized as新的空列表 Elements of the GlobalSymbolRegistry are Records with the structure defined in Table 48.

Table 48: GlobalSymbolRegistry Record Fields

| Field Name | Value    | Usage                                            |
| ---------- | -------- | ------------------------------------------------ |
| [[Key]]    | A String | A string key used to globally identify a Symbol. |
| [[Symbol]] | A Symbol | A symbol that can be retrieved from any realm.   |

#### 19.4.2.3 Symbol.hasInstance <div id="sec-symbol.hasinstance"></div>

The initial value of Symbol.hasInstance is the well-known symbol @@hasInstance (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.4 Symbol.isConcatSpreadable <div id="sec-symbol.isconcatspreadable"></div>

The initial value of Symbol.isConcatSpreadable is the well-known symbol @@isConcatSpreadable (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.5 Symbol.iterator <div id="sec-symbol.iterator"></div>

The initial value of Symbol.iterator is the well-known symbol @@iterator (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.6 Symbol.keyFor ( sym ) <div id="sec-symbol.keyfor"></div>

When Symbol.keyFor is called with argument sym it performs the following steps:

1. 若 Type(sym) 不是 Symbol, 抛出 TypeError 异常。
2. For each element e of the GlobalSymbolRegistry List (see 19.4.2.2), do
1. 若 SameValue(e.[[Symbol]], sym) 是 true, 返回 e.[[Key]].
3. 断言：GlobalSymbolRegistry does not currently contain an entry for sym.
4. 返回 undefined.

#### 19.4.2.7 Symbol.match <div id="sec-symbol.match"></div>

The initial value of Symbol.match is the well-known symbol @@match (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.8 Symbol.prototype <div id="sec-symbol.prototype"></div>

The initial value of Symbol.prototype is the intrinsic object %SymbolPrototype%.

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.9 Symbol.replace <div id="sec-symbol.replace"></div>

The initial value of Symbol.replace is the well-known symbol @@replace (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.10 Symbol.search <div id="sec-symbol.search"></div>

The initial value of Symbol.search is the well-known symbol @@search (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.11 Symbol.species <div id="sec-symbol.species"></div>

The initial value of Symbol.species is the well-known symbol @@species (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

#### 19.4.2.12 Symbol.split <div id="sec-symbol.split"></div>

The initial value of Symbol.split is the well-known symbol @@split (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

#### 19.4.2.13 Symbol.toPrimitive <div id="sec-symbol.toprimitive"></div>

The initial value of Symbol.toPrimitive is the well-known symbol @@toPrimitive (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.14 Symbol.toStringTag <div id="sec-symbol.tostringtag"></div>

The initial value of Symbol.toStringTag is the well-known symbol @@toStringTag (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.4.2.15 Symbol.unscopables <div id="sec-symbol.unscopables"></div>

The initial value of Symbol.unscopables is the well-known symbol @@unscopables (Table 1).

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }

### 19.4.3 Symbol 原型对象属性 <div id="sec-properties-of-the-symbol-prototype-object"></div>

The Symbol prototype object:

- is the intrinsic object %SymbolPrototype%.
- is an ordinary object.
- is not a Symbol instance and does not have a [[SymbolData]] internal slot.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

The abstract operation thisSymbolValue(value) performs the following steps:

1. 若 Type(value) 是 Symbol, 返回 value.
2. 若 Type(value) 是 Object and value has a [[SymbolData]] internal slot，那么
1. 令 s 为 value.[[SymbolData]].
2. 断言：Type(s) is Symbol.
3. 返回 s.
3. 抛出 TypeError 异常。

#### 19.4.3.1 Symbol.prototype.constructor <div id="sec-symbol.prototype.constructor"></div>

The initial value of Symbol.prototype.constructor is the intrinsic object %Symbol%.

#### 19.4.3.2 get Symbol.prototype.description <div id="sec-symbol.prototype.description"></div>

Symbol.prototype.description is an accessor property whose set accessor function is undefined. Its get accessor function performs the following steps:

1. 令 s 为 this 值.
2. 令 sym 为 ? thisSymbolValue(s).
3. 返回 sym.[[Description]].

#### 19.4.3.3 Symbol.prototype.toString ( ) <div id="sec-symbol.prototype.tostring"></div>

采取以下步骤：

1. 令 sym 为 ? thisSymbolValue(this value).
2. 返回 SymbolDescriptiveString(sym).

##### 19.4.3.3.1 RS: SymbolDescriptiveString ( sym ) <div id="sec-symboldescriptivestring"></div>

When the abstract operation SymbolDescriptiveString is called with argument sym, 采取以下步骤：

1. 断言：Type(sym) is Symbol.
2. 令 desc 为 sym's [[Description]] value.
3. 若 desc 是 undefined, 设置 desc 为空字符串
4. 断言：Type(desc) is String.
5. 返回 the string-concatenation of "Symbol(", desc, and ")"

#### 19.4.3.4 Symbol.prototype.valueOf ( ) <div id="sec-symbol.prototype.valueof"></div>

采取以下步骤：

1. 返回 ? thisSymbolValue(this value).

#### 19.4.3.5 Symbol.prototype [ @@toPrimitive ] ( hint ) <div id="sec-symbol.prototype-@@toprimitive"></div>

This function is called by ECMAScript language operators to convert a Symbol object to a primitive value. The allowed values for hint are "default", "number", and "string".

When the @@toPrimitive method is called with argument hint, 采取以下步骤：

1. 返回 ? thisSymbolValue(this value)

The value of the name property of this function is "[Symbol.toPrimitive]".

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }.

#### 19.4.3.6 Symbol.prototype [ @@toStringTag ] <div id="sec-symbol.prototype-@@tostringtag"></div>

The initial value of the @@toStringTag property is the String value "Symbol".

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: true }

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

When the Error function is called with argument message, 采取以下步骤：

1. 若 NewTarget 是 undefined, 令 newTarget 为 the active function object, 否则，令 newTarget 为 NewTarget.
2. 令 O 为 ? OrdinaryCreateFromConstructor(newTarget, "%ErrorPrototype%", « [[ErrorData]] »).
3. 若 message 不是 undefined，那么
  1. 令 msg 为 ? ToString(message).
  2. 令 msgDesc 为 the PropertyDescriptor { [[Value]]: msg, [[Writable]]: true, [[Enumerable]]: false,

    [[Configurable]]: true }.
  3. 执行 ! DefinePropertyOrThrow(O, "message", msgDesc).
4. 返回 O.

### 19.5.2 Properties of the Error 构造器属性 <div id="sec-properties-of-the-error-constructor"></div>

The Error constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %FunctionPrototype%.
- has the following properties:

#### 19.5.2.1 Error.prototype <div id="sec-error.prototype"></div>

The initial value of Error.prototype is the intrinsic object %ErrorPrototype%.

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 19.5.3 Properties of the Error 原型对象属性 <div id="sec-properties-of-the-error-prototype-object"></div>

The Error prototype object:

- is the intrinsic object %ErrorPrototype%.
- is an ordinary object.
- is not an Error instance and does not have an [[ErrorData]] internal slot.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ObjectPrototype%.

#### 19.5.3.1 Error.prototype.constructor <div id="sec-error.prototype.constructor"></div>

The initial value of Error.prototype.constructor is the intrinsic object %Error%.

#### 19.5.3.2 Error.prototype.message <div id="sec-error.prototype.message"></div>

The initial value of Error.prototype.message is空字符串

#### 19.5.3.3 Error.prototype.name <div id="sec-error.prototype.name"></div>

The initial value of Error.prototype.name is "Error".

#### 19.5.3.4 Error.prototype.toString ( ) <div id="sec-error.prototype.tostring"></div>

采取以下步骤：

1. 令 O 为 this 值.
2. 若 Type(O) 不是 Object, 抛出 TypeError 异常。
3. 令 name 为 ? Get(O, "name").
4. 若 name 是 undefined, set name to "Error"; otherwise set name to ? ToString(name).
5. 令 msg 为 ? Get(O, "message").
6. 若 msg 是 undefined, set msg to空字符串 otherwise set msg to ? ToString(msg).
7. 若 name 是空字符串 返回 msg.
8. 若 msg 是空字符串 返回 name.
9. 返回 the string-concatenation of name, the code unit 0x003A (COLON), the code unit 0x0020 (SPACE), and msg.

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

When a NativeError function is called with argument message, 采取以下步骤：

1. 若 NewTarget 是 undefined, 令 newTarget 为 the active function object, 否则，令 newTarget 为 NewTarget.
2. 令 O 为 ? OrdinaryCreateFromConstructor(newTarget, "%NativeErrorPrototype%", « [[ErrorData]] »).
3. 若 message 不是 undefined，那么
1. 令 msg 为 ? ToString(message).
2. 令 msgDesc 为 the PropertyDescriptor { [[Value]]: msg, [[Writable]]: true, [[Enumerable]]: false,
    [[Configurable]]: true }.
3. 执行 ! DefinePropertyOrThrow(O, "message", msgDesc).
4. 返回 O.

The actual value of the string passed in step 2 is either "%EvalErrorPrototype%", "%RangeErrorPrototype%", "%ReferenceErrorPrototype%", "%SyntaxErrorPrototype%", "%TypeErrorPrototype%", or "%URIErrorPrototype%" corresponding to which NativeError constructor is being defined.

#### 19.5.6.2 NativeError 构造器属性 <div id="sec-properties-of-the-nativeerror-constructors"></div>

Each NativeError constructor:

- has a [[Prototype]] internal slot whose value is the intrinsic object %Error%.
- has a name property whose value is the String value `"NativeError"`.
- has the following properties:

##### 19.5.6.2.1 NativeError.prototype <div id="sec-nativeerror.prototype"></div>

The initial value of NativeError.prototype is a NativeError prototype object (19.5.6.3). Each NativeError
constructor has a distinct prototype object.

该属性具有以下特性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

#### 19.5.6.3 NativeError 原型对象属性 <div id="sec-properties-of-the-nativeerror-prototype-objects"></div>

Each NativeError prototype object:

- is an ordinary object.
- is not an Error instance and does not have an [[ErrorData]] internal slot.
- has a [[Prototype]] internal slot whose value is the intrinsic object %ErrorPrototype%

##### 19.5.6.3.1 NativeError.prototype.constructor <div id="sec-nativeerror.prototype.constructor"></div>

The initial value of the constructor property of the prototype for a given NativeError constructor is the corresponding intrinsic object %NativeError% (19.5.6.1).

##### 19.5.6.3.2 NativeError.prototype.message <div id="sec-nativeerror.prototype.message"></div>


- is the intrinsic object %Object%.
- is the initial value of the Object property of the global object.
- creates The Object constructor:

##### 19.5.6.3.3 NativeError.prototype.name <div id="sec-nativeerror.prototype.name"></div>

The initial value of the name property of the prototype for a given NativeError constructor is the String value consisting of the name of the constructor (the name used instead of NativeError).

#### 19.5.6.4 NativeError 实例属性 <div id="sec-properties-of-nativeerror-instances"></div>

NativeError instances are ordinary objects that inherit properties from their NativeError prototype object and have an [[ErrorData]] internal slot whose value is undefined. The only specified use of [[ErrorData]] is by Object.prototype.toString (19.1.3.6) to identify Error or NativeError instances.