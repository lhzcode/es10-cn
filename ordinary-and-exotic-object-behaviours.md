# 9 普通和异类对象行为

## 9.1 普通对象内部方法和内部插槽 <div id="ordinary-object-internal-methods-and-internal-slots"></div>

所有普通对象都有一个称为[[Prototype]]的内部插槽。此内部插槽的值可以为null或一个对象，用于实现继承。[[Prototype]]对象的数据属性被继承（作为子对象的属性可见），是为了取值权限，而不是赋值权限。访问器属性被继承，同时获得取值权限和赋值权限。

每个普通对象都有一个布尔值的[[Extensible]]内部插槽，该插槽用于实现6.1.7.3中指定的与扩展性相关的内部方法不变量。即，一旦将对象的[[Extensible]]内部插槽的值设置为false，就无法再向该对象添加属性、修改该对象的[[Prototype]]内部插槽的值或随后将[[Extensible]]的值更改为true。

在以下算法描述中，假定O为普通对象，P为属性键值，V为任何ECMAScript语言值，而Desc为属性描述符记录。

每个普通对象的内部方法都委托一个名称相似的抽象操作。如果这样的抽象操作依赖于另一个内部方法，则在O上调用内部方法，而不是直接调用类似名称的抽象操作。这些语义确保将普通对象内部方法应用于异类对象时，将调用其覆盖的内部方法。

### 9.1.1 [[GetPrototypeOf]] ( ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-getprototypeof"></div>

调用O的[[GetPrototypeOf]]内部方法时，将执行以下步骤：

1. 返回 ! OrdinaryGetPrototypeOf(O).  

#### 9.1.1.1 OrdinaryGetPrototypeOf ( O ) <div id="sec-ordinarygetprototypeof"></div>

当使用对象O调用抽象操作 OrdinaryGetPrototypeOf 时，将执行以下步骤：

1. 返回 O.[[Prototype]]

### 9.1.2 [[SetPrototypeOf]] ( V ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-setprototypeof-v"></div>

使用参数V调用O的[[SetPrototypeOf]]内部方法时，将执行以下步骤：

1. 返回 ! OrdinarySetPrototypeOf(O, V).

#### 9.1.2.1 OrdinarySetPrototypeOf ( O, V ) <div id="sec-ordinarysetprototypeof"></div>

当使用对象O和值V调用抽象操作OrdinarySetPrototypeOf时，将执行以下步骤：

1. 断言:Type(V) 是 Object 或 Null.
2. 令 extensible 为 O.[[Extensible]].
3. 令 current 为 O.[[Prototype]].
4. 若 SameValue(V, current) 是 true, 返回 true.
5. 若 extensible 是 false, 返回 false.
6. 令 p 为 V.
7. 令 done 为 false.
8. 重复, 直到 done 是 false,

  1. 若 p 是 null, 设置 done 为 true.

  2. 否则如果 SameValue(p, O) 是 true, 返回 false.

  3. 否则,

     1. 若 p.[[GetPrototypeOf]] 不是在 9.1.1 定义的普通内部方法, 设置 done 为 true.

     2. 否则, 设置 p 为 p.[[Prototype]].

9. 设置 O.[[Prototype]] 为 V.
10. 返回 true.

> 注：步骤8中保证在任何原型链中都不会存在循环，该原型链仅包括使用[[GetPrototypeOf]]和[[SetPrototypeOf]]的普通对象定义的对象。

### 9.1.3 [[IsExtensible]] ( ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-isextensible"></div>

调用O的[[IsExtensible]]内部方法时，将执行以下步骤：

1. 返回 ! OrdinaryIsExtensible(O).

#### 9.1.3.1 OrdinaryIsExtensible ( O ) <div id="sec-ordinaryisextensible"></div>

当使用对象O调用抽象操作OrdinaryIsExtensible时，将执行以下步骤：

1. 返回 O.[[Extensible]].

### 9.1.4 [[PreventExtensions]] ( ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-preventextensions"></div>

调用O的[[PreventExtensions]]内部方法时，将执行以下步骤：

1. 返回 ! OrdinaryPreventExtensions(O)

#### 9.1.4.1 OrdinaryPreventExtensions ( O ) <div id="sec-ordinarypreventextensions"></div>

当使用对象 O 调用抽象操作 OrdinaryPreventExtensions 时，将采取以下步骤：

1. 设置 O.[[Extensible]] 为 false.
2. 返回 true.

### 9.1.5 [[GetOwnProperty]] ( P ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-getownproperty-p"></div>

使用属性键P调用O的[[GetOwnProperty]]内部方法时，将执行以下步骤：

1. 返回 ! OrdinaryGetOwnProperty(O, P).

#### 9.1.5.1 OrdinaryGetOwnProperty ( O, P ) <div id="sec-ordinarygetownproperty"></div>

当使用对象O和属性键P调用抽象操作OrdinaryGetOwnProperty时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 如果 O 没有使用键P的私有属性，返回 undefined.
3. 令 D 为没有字段的新创建的属性描述符 
4. 令 X 为 O 的自有属性，其键为 P。
5. 若 X 是一个数据属性, 那么
a. 设置 D.[[Value]] 为 X 的 [[Value]] 属性值.
2. 设置 D.[[Writable]] 为X 的 [[Writable]] 属性值

6. 否则 X 是访问器属性,
a. 设置 D.[[Get]] 为X 的 [[Get]] 属性值
2. 设置 D.[[Set]] 为X 的 [[Set]] 属性值.
7. 设置 D.[[Enumerable]] 为X 的 [[Enumerable]] 属性值
8. 设置 D.[[Configurable]] 为X 的 [[Configurable]] 属性值
9. 返回 D.

### 9.1.6 [[DefineOwnProperty]] ( P, Desc ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-defineownproperty-p-desc"></div>

使用属性键P和属性描述符Desc调用O的[[DefineOwnProperty]]内部方法时，将执行以下步骤：

1. 返回 ? OrdinaryDefineOwnProperty(O, P, Desc).

#### 9.1.6.1 OrdinaryDefineOwnProperty ( O, P, Desc ) <div id="sec-ordinarydefineownproperty"></div>

当使用对象O，属性键P和属性描述符Desc调用抽象操作OrdinaryDefineOwnProperty时，将执行以下步骤：

1. 令 current 为 ? O.\[\[GetOwnProperty]](P).
2. 令 extensible 为 ? IsExtensible(O).
3. 返回 ValidateAndApplyPropertyDescriptor(O, P, extensible, Desc, current).

#### 9.1.6.2 IsCompatiblePropertyDescriptor ( Extensible, Desc, Current ) <div id="sec-iscompatiblepropertydescriptor"></div>

当使用布尔值Extensible、属性描述符Desc和Current调用抽象操作IsCompatiblePropertyDescriptor时，将执行以下步骤：

1. 返回 ValidateAndApplyPropertyDescriptor(undefined, undefined, Extensible, Desc, Current).

#### 9.1.6.3 ValidateAndApplyPropertyDescriptor ( O, P, extensible, Desc, current ) <div id="sec-validateandapplypropertydescriptor"></div>

当使用对象O，属性键P，布尔值可扩展，属性描述符Desc和当前调用抽象操作ValidateAndApplyPropertyDescriptor时，将执行以下步骤：

> 注：如果将undefined传递为O，则仅执行验证，并且不执行对象更新。

1. 断言: 若 O 不是 undefined, 那么 IsPropertyKey(P) 是 true.
2. 若 current 是 undefined, 那么
   
    1. 若 extensible 是 false, 返回 false.
    2. 断言: extensible 是 true.
    3. 若 IsGenericDescriptor(Desc) 是 true 或 IsDataDescriptor(Desc) 是 true, 那么
       1. 若 O 不是 undefined, 创建一个名为P的私有数据属性，对象 O 的[[Value]], [[Writable]],[[Enumerable]] 和 [[Configurable]] 属性值用Desc来描述。若Desc的属性字段值不存在, 新创建的属性的属性设置为其默认值。
    4. 否则 Desc 必须为访问器属性，
       1. 若 O 不是 undefined, 创建一个名为P的私有访问器属性，对象 O 的[[Get]], [[Set]],[[Enumerable]] 和 [[Configurable]] 属性用Desc来描述。若Desc的属性字段值不存在, 新创建的属性的属性设置为其默认值。
    5. 返回 true.
    
3. 若 Desc 的每一个字段都不存在, 返回 true.
4. 若 current.[[Configurable]] 是 false, 那么

  1. 若 Desc.[[Configurable]] 存在且值为 true, 返回 false.
  2. 若 Desc.[[Enumerable]] 存在，并且[[Enumerable]] 字段的 current 和 Desc 布尔值互斥，返回 false.
5. 若 IsGenericDescriptor(Desc) 是 true,  无需进一步验证。 
6. 否则若 IsDataDescriptor(current) 和 IsDataDescriptor(Desc) 结果不一样, 那么

  1. 若 current.[[Configurable]] 是 false, 返回 false.

  2. 若 IsDataDescriptor(current) 是 true, 那么
     1. 若 O 不是undefined，则将对象 O 的名为 P 的属性从数据属性转换为访问器属性。保留转换后属性的[[Configurable]]和[[Enumerable]]特性的现有值，并将该属性的其余特性设置为其默认值。

  3. 否则,
     1. 若 O 不是 undefined，则将对象 O 的名为 P 的属性从访问器属性转换为数据属性。保留转换后属性的[[Configurable]]和[[Enumerable]]特性的现有值，并将该属性的其余特性设置为其默认值。
7. 否则若 IsDataDescriptor(current) 和 IsDataDescriptor(Desc) 都是 true, 那么

  1. 若 current.[[Configurable]] 是 false ，同时 current.[[Writable]] 是 false, 那么
     1. 若 Desc.[[Writable]] 存在， 并且  Desc.[[Writable]] 是 true，返回 false.
     2. 若 Desc.[[Value]] 存在，并且 SameValue(Desc.[[Value]], current.[[Value]]) 是 false, 返回 false.
     3. . 返回 true.
8. 否则 IsAccessorDescriptor(current) and IsAccessorDescriptor(Desc) are both true,

  1. 若 current.[[Configurable]] 是 false, 那么
     1. 若 Desc.[[Set]] 存在 and SameValue(Desc.[[Set]], current.[[Set]]) 是 false, 返回 false.
     2. 若 Desc.[[Get]] 存在 and SameValue(Desc.[[Get]], current.[[Get]]) 是 false, 返回 false.
     3. 返回 true.
9. 若 O 不是 undefined, 那么

1. 对于存在的Desc的每个字段，将对象 O 的名为 P 的属性的相应属性设置为该字段的值。
10. 返回 true.

### 9.1.7 [[HasProperty]] ( P ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-hasproperty-p"></div>

使用属性键P调用O的[[HasProperty]]内部方法时，将执行以下步骤：

1. 返回 ? OrdinaryHasProperty(O, P).

#### 9.1.7.1 OrdinaryHasProperty ( O, P ) <div id="sec-ordinaryhasproperty"></div>

当使用对象O和属性键P调用抽象操作OrdinaryHasProperty时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.

2. 令 hasOwn 为 ? O.\[\[GetOwnProperty]](P).

3. 若 hasOwn 不是 undefined, 返回 true.

4. 令 parent 为 ? O.\[\[GetPrototypeOf]]().

5. 若 parent 不是 null, 那么

   a. 返回 ? parent.\[\[HasProperty]](P). 

6. 返回 false.  

### 9.1.8 [[Get]] ( P, Receiver ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-get-p-receiver"></div>

使用属性键P和ECMAScript语言值Receiver调用O的[[Get]]内部方法时，将执行以下步骤：

1. 返回 ? OrdinaryGet(O, P, Receiver).

#### 9.1.8.1 OrdinaryGet ( O, P, Receiver ) <div id="sec-ordinaryget"></div>

当使用对象O，属性键P和ECMAScript语言值Receiver调用抽象操作OrdinaryGet时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 desc 为 ? O.\[\[GetOwnProperty]](P).
3. 若 desc 是 undefined, 那么
  1. 令 parent 为 ? O.\[\[GetPrototypeOf]]().
  2. 若 parent 是 null, 返回 undefined.
  3.  返回 ? parent.\[\[Get]](P, Receiver).
4. 若 IsDataDescriptor(desc) 是 true, 返回 desc.[[Value]].
5. 断言: IsAccessorDescriptor(desc) 是 true.
6. 令 getter 为 desc.[[Get]].
7. 若 getter 是 undefined, 返回 undefined.
8. 返回 ? Call(getter, Receiver).

### 9.1.9 [[Set]] ( P, V, Receiver ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-设置-p-v-receiver"></div>

当使用属性键P，值V和ECMAScript语言值Receiver调用O的[[Set]]内部方法时，将执行以下步骤：

1. 返回 ? OrdinarySet(O, P, V, Receiver).

#### 9.1.9.1 OrdinarySet ( O, P, V, Receiver ) <div id="sec-ordinaryset"></div>

当使用对象O，属性键P，值V和ECMAScript语言值Receiver调用抽象操作OrdinarySet时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 ownDesc 为 ? O.\[\[GetOwnProperty]](P).
3. 返回 OrdinarySetWithOwnDescriptor(O, P, V, Receiver, ownDesc).

#### 9.1.9.2 OrdinarySetWithOwnDescriptor ( O, P, V , Receiver, ownDesc ) <div id="sec-ordinarysetwithowndescriptor"></div>

当使用对象O，属性键P，值V，ECMAScript语言值Receiver和属性描述符（可能为undefined）ownDesc调用抽象操作OrdinarySetWithOwnDescriptor时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 若 ownDesc 是 undefined, 那么

   1. 令 parent 为 ? O.\[\[GetPrototypeOf]]().
2. 若 parent 不是 null, 那么
      1. 返回 ? parent.\[\[Set]](P, V, Receiver).
   3. 否则,
      1. 设置 ownDesc 为 the PropertyDescriptor { [[Value]]: undefined, [[Writable]]: true， [[Enumerable]]: true, [[Configurable]]: true }.
3. 若 IsDataDescriptor(ownDesc) 是 true, 那么
  1. 若 ownDesc.[[Writable]] 是 false, 返回 false。
  2. 若 Type(Receiver) 不是 Object, 返回 false.
  3. 令 existingDescriptor 为 ? Receiver.\[\[GetOwnProperty]](P).
  4. 若 existingDescriptor 不是 undefined, 那么
     1. 若 IsAccessorDescriptor(existingDescriptor) 是 true, 返回 false.
     2. 若 existingDescriptor.[[Writable]] 是 false, 返回 false.
     3. 令 valueDesc 为 the PropertyDescriptor { [[Value]]: V }.
     4. 返回 ? Receiver.\[\[DefineOwnProperty]](P, valueDesc).
  5. 否则 Receiver 当前没有属性 P
     1. 返回 ? CreateDataProperty(Receiver, P, V).
4. 断言: IsAccessorDescriptor(ownDesc) 是 true.
5. 令 setter 为 ownDesc.[[Set]].
6. 若 setter 是 undefined, 返回 false.
7. 执行 ? Call(setter, Receiver, « V »).
8. 返回 true

### 9.1.10 [[Delete]] ( P ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-delete-p"></div>

使用属性键P调用O的[[Delete]]内部方法时，将执行以下步骤：

1. 返回 ? OrdinaryDelete(O, P).

#### 9.1.10.1 OrdinaryDelete ( O, P ) <div id="sec-ordinarydelete"></div>

当使用对象O和属性键P调用抽象操作OrdinaryDelete时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 desc 为 ? O.\[\[GetOwnProperty]](P).
3. 若 desc 是 undefined, 返回 true.
4. 若 desc.[[Configurable]] 是 true, 那么
  1. 从 O 中移除名为 P 的私有属性.
  2. 返回 true.
5. 返回 false.

### 9.1.11 [[OwnPropertyKeys]] ( ) <div id="sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys"></div>

调用O的[[OwnPropertyKeys]]内部方法时，将执行以下步骤：

1. 返回 ! OrdinaryOwnPropertyKeys(O).

#### 9.1.11.1 OrdinaryOwnPropertyKeys ( O ) <div id="sec-ordinaryownpropertykeys"></div>

使用Object OF调用抽象操作“普通OwnProperty Keys”时，将执行以下步骤：

1. 令 keys 为一个新的空列表.
2. 对于作为数组索引的 O 的每个私有的属性键P，按数字索引升序排列, 执行
1. 将 P 添加为 keys 的最后一个元素。
3. 对于每个字符串 O 的私有属性键 P（不是数组索引），按属性创建的时间顺序升序排列，执行
1. 将 P 添加为 keys 的最后一个元素。
4. 对于每个 Symbol 类型的 O 的私有属性键P，按属性创建的升序排列, 执行
1. 将 P 添加为 keys 的最后一个元素。
5. 返回 keys.

### 9.1.12 ObjectCreate ( proto [ , internalSlotsList ] ) <div id="sec-objectcreate"></div>

带 proto 参数（对象或null）的抽象操作 ObjectCreate 用于指定新的普通对象的运行时创建。可选参数internalSlotsList是额外内部插槽名称的列表，必须定义为对象一部分的。如果未提供列表，则使用新的空列表。此抽象操作执行以下步骤：

1. 若 internalSlotsList 不存在, 设置 internalSlotsList 为一个新的空列表.
2. 令 obj 为一个新创建的对象，其中 internalSlotsList 中的每个名称都有一个内部插槽。
3. 设置 obj 的基本内部方法为 9.1中指定的默认普通对象定义。
4. 设置 obj.[[Prototype]] 为 proto.
5. 设置 obj.[[Extensible]] 为 true.
6. 返回 obj.

### 9.1.13 OrdinaryCreateFromConstructor ( constructor, intrinsicDefaultProto [ , internalSlotsList ] ) <div id="sec-ordinarycreatefromconstructor"></div>

抽象操作OrdinaryCreateFromConstructor创建一个普通对象，该对象的[[Prototype]]值从构造器的prototype属性中获取（如果存在）。否则，将由internalDefaultProto命名的内部函数用于[[Prototype]]。可选的internalSlotsList是额外内部插槽名称的列表，必须定义为对象一部分的。如果未提供列表，则使用新的空列表。此抽象操作执行以下步骤：

1. 断言: intrinsicDefaultProto 是一个String值，它是此规范的内部对象名称。相应的对象必须是打算用作对象的[[Prototype]]值的内部函数。 
2. 令 proto 为 ? GetPrototypeFromConstructor(constructor, intrinsicDefaultProto).
3. 返回 ObjectCreate(proto, internalSlotsList).

### 9.1.14 GetPrototypeFromConstructor ( constructor,  intrinsicDefaultProto ) <div id="sec-getprototypefromconstructor"></div>

抽象操作GetPrototypeFromConstructor确定用于创建与特定构造函数相对应的对象的[[Prototype]]值。该值从构造函数的prototype属性中获取（如果存在）。否则，将由internalDefaultProto命名的内部函数用于[[Prototype]]。此抽象操作执行以下步骤：

1. 断言: intrinsicDefaultProto 是一个String值，它是此规范的内部对象名称。

2. 断言: IsCallable(constructor) 是 true.
3. 令 proto 为 ? Get(constructor, "prototype").
4. 若 Type(proto) 不是 Object, 那么
  1. 令 realm 为 ? GetFunctionRealm(constructor).
  2. 设置 proto 为 realm's intrinsic object named intrinsicDefaultProto.  
2. 设置 proto 为 realm 的内部对象名 intrinsicDefaultProto.
5. 返回 proto.

> 注：如果构造函数未提供[[Prototype]]值，则使用的默认值是从构造函数的作用域而不是从运行的执行上下文中获得的。

## 9.2 ECMAScript函数对象 <div id="function-objects"></div>

ECMAScript函数对象封装了在词法环境中封闭的参数化ECMAScript代码，并支持对该代码的动态执行。ECMAScript函数对象是一个普通对象，并且具有与其他普通对象相同的内部插槽和相同的内部方法。ECMAScript函数对象的代码可以是严格模式代码（10.2.1）或非严格代码。代码为严格模式代码的ECMAScript函数对象称为严格函数。代码不是严格模式代码的代码称为非严格函数。

ECMAScript函数对象具有表27中列出的其他内部插槽。

| 内部插槽             | 类型                      | 描述                                                         |
| -------------------- | ------------------------- | ------------------------------------------------------------ |
| [[Environment]]      | 词法环境                  | 函数封闭的词法环境。在执行函数代码时用作外部环境。           |
| [[FormalParameters]] | 解析节点                  | 源文本的根解析节点，用于定义函数的形式参数列表。             |
| [[FunctionKind]]     | 字符串                    | "normal", "classConstructor", "generator", "async" 或 "async generator" |
| [[ECMAScriptCode]]   | 解析节点                  | 定义函数体的源文本的根解析节点。                             |
| [[ConstructorKind]]  | 字符串                    | "base" 或 "derived"                                          |
| [[Realm]]            | 作用域记录项              | 创建函数的作用域，并提供在执行函数时访问的任何内部对象。     |
| [[ScriptOrModule]]   | 脚本记录项或模块记录项    | 创建函数的脚本或模块。                                       |
| [[ThisMode]]         | (lexical, strict, global) | 定义如何在函数的形式参数和代码体内解释引用this。 lexical意味着this是词法闭包函数的this值。 strict意味着this值完全按照函数调用的方式使用。 global意味着undefined的this值被解释为对全局对象的引用。 |
| [[Strict]]           | Boolean                   | 如果这是严格的模式函数，则为真，如果不是严格的模式函数，则为false。 |
| [[HomeObject]]       | Object                    | 如果函数使用super，这是[[GetPrototypeOf]]提供super属性查找开始的对象。 |
| [[SourceText]]       | String                    | 源文本定义了函数                                             |

所有ECMAScript函数对象都具有此处定义的[[Call]]内部方法。ECMAScript函数也是构造函数，另外还具有[[Construct]]内部方法。

### 9.2.1 [[Call]] ( thisArgument, argumentsList ) <div id="sec-ecmascript-function-objects-call-thisargument-argumentslist"></div>

使用参数thisArgument和argumentsList（ECMAScript语言值列表）来调用ECMAScript函数对象F的[[Call]]内部方法。采取以下步骤：

1. 断言: F 是ECMAScript函数对象.
2. 若 F.[[FunctionKind]] 是 "classConstructor", 抛出 TypeError 异常.
3. 令 callerContext 为运行时执行上下文.
4. 令 calleeContext 为 PrepareForOrdinaryCall(F, undefined).
5. 断言: calleeContext 是当前的运行时执行上下文.
6. 执行 OrdinaryCallBindThis(F, calleeContext, thisArgument).
7. 令 result 为 OrdinaryCallEvaluateBody(F, argumentsList).
8. 从执行上下文堆栈中删除calleeContext，并将callerContext还原为正在运行的执行上下文。
9. 若 result.[[Type]] 是 return, 返回 NormalCompletion(result.[[Value]]).
10. ReturnIfAbrupt(result).
11. 返回 NormalCompletion(undefined).

> 注：当在步骤8中从执行上下文堆栈中删除calleeContext时，如果将其暂停并保留以供以后由可访问的生成器对象恢复，则不得销毁它。

#### 9.2.1.1 PrepareForOrdinaryCall ( F, newTarget ) <div id="sec-prepareforordinarycall"></div>

使用函数对象F和ECMAScript语言值newTarget调用抽象操作PrepareForOrdinaryCall时，将执行以下步骤：

1. 断言: Type(newTarget) 是 Undefined 或 Object.
2. 令 callerContext 为运行时执行上下文.
3. 令 calleeContext 为新的ECMAScript代码执行上下文.
4. 设置 calleeContext 的函数为 F.
5. 令 calleeRealm 为 F.[[Realm]].
6. 设置 calleeContext 的作用域为 calleeRealm.
7. 设置 calleeContext 的 ScriptOrModule  为 F.[[ScriptOrModule]].
8. 令 localEnv 为 NewFunctionEnvironment(F, newTarget).
9. 设置 calleeContext 的词法环境为 localEnv.
10. 设置 calleeContext 变量环境为 localEnv.
11. 若 callerContext 不是已经挂起, 挂起 callerContext.

12. 将calleeContext推入执行上下文堆栈；calleeContext现在是运行中的执行上下文。
13. 注意: 此后产生的任何异常对象都与calleeRealm相关联
14. 返回 calleeContext.

#### 9.2.1.2 OrdinaryCallBindThis ( F, calleeContext, thisArgument ) <div id="sec-ordinarycallbindthis"></div>

当使用函数对象F，执行上下文calleeContext和ECMAScript值thisArgument调用抽象操作OrdinaryCallBindThis时，将执行以下步骤：

1. 令 thisMode 为 F.[[ThisMode]].
2. 若 thisMode 是 lexical, 返回 NormalCompletion(undefined).
3. 令 calleeRealm 为 F.[[Realm]].
4. 令 localEnv 为 calleeContext的词法环境.
5. 若 thisMode 是 strict, 令 thisValue 为 thisArgument.
6. 否则,
  1. 若 thisArgument 是 undefined 或 null, 那么
     1. 令 globalEnv 为 calleeRealm.[[GlobalEnv]].
     2. 令 globalEnvRec 为 globalEnv的环境记录项.
     3. 断言: globalEnvRec 是全局环境记录项.
     4. 令 thisValue 为 globalEnvRec.[[GlobalThisValue]].
  2. 否则,
        1. 令 thisValue 为 ! ToObject(thisArgument).
            2. 注意: ToObject 使用 calleeRealm生成包装对象.
7. 令 envRec 为 localEnv 的环境记录项.
8. 断言: envRec 是函数环境记录项.
9. 断言: 下一步永远不会返回突然完成，因为 envRec.[[ThisBindingStatus]] 不是
"initialized".
10. 返回 envRec.BindThisValue(thisValue).

#### 9.2.1.3 OrdinaryCallEvaluateBody ( F, argumentsList ) <div id="sec-ordinarycallevaluatebody"></div>

当使用函数对象 F 和列表 argumentsList 调用抽象操作 OrdinaryCallEvaluateBody 时，将执行以下步骤：

1. 返回已解析代码的 EvaluateBody 的结果，即 F.[[ECMAScriptCode]] 传递 F 和 argumentsList 作为参数。

### 9.2.2 [[Construct]] ( argumentsList, newTarget ) <div id="sec-ecmascript-function-objects-construct-argumentslist-newtarget"></div>

使用参数argumentsList和newTarget调用ECMAScript函数对象F的[[Construct]]内部方法。argumentsList是一个可能为空的ECMAScript语言值列表。采取以下步骤：

1. 断言: F 是ECMAScript函数对象.
2. 断言: Type(newTarget) 是 Object.
3. 令 callerContext 为运行时执行上下文.
4. 令 kind 为 F.[[ConstructorKind]].
5. 若 kind 是 "base", 那么
  1. 令 thisArgument 为 ? OrdinaryCreateFromConstructor(newTarget, "%ObjectPrototype%").
6. 令 calleeContext 为 PrepareForOrdinaryCall(F, newTarget).

7. 断言: calleeContext 是当前的运行时执行上下文.
8. 若 kind 是 "base", 执行 OrdinaryCallBindThis(F, calleeContext, thisArgument).
9. 令 constructorEnv 为 calleeContext 的词法环境.
10. 令 envRec 为 constructorEnv 的环境记录项.
11. 令 result 为 OrdinaryCallEvaluateBody(F, argumentsList).
12. 从执行上下文堆栈中删除calleeContext，并将callerContext还原为正在运行的执行上下文
13. 若 result.[[Type]] 是 return, 那么
    1. 若 Type(result.[[Value]]) 是 Object, 返回 NormalCompletion(result.[[Value]]).
    2. 若 kind 是 "base", 返回 NormalCompletion(thisArgument).
    3. 若 result.[[Value]] 不是 undefined, 抛出 TypeError 异常.
14. 否则, ReturnIfAbrupt(result).
15. 返回 ? envRec.GetThisBinding().

### 9.2.3 FunctionAllocate ( functionPrototype, strict, functionKind ) <div id="sec-functionallocate"></div>

抽象操作FunctionAllocate需要三个参数functionPrototype，strict和functionKind。FunctionAllocate执行以下步骤：

1. 断言: Type(functionPrototype) 是 Object.
2. 断言: functionKind 是 "normal", "non-constructor", "generator", "async", 或
"async generator".
3. 若 functionKind 是 "normal", 令 needsConstruct 为 true.
4. 否则, 令 needsConstruct 为 false.
5. 若 functionKind 是 "non-constructor", 设置 functionKind 为 "normal".
6. 令 F 为一个新创建的 ECMAScript 函数对象，其内部插槽在表27中列出。所有这些内部插槽均被初始化为undefined
7. 设置 F 的基本内部方法为9.1中指定的默认普通对象定义
8. 设置 F.[[Call]] 为9.2.1中指定的定义.
9. 若 needsConstruct 是 true, 那么
  1. 设置 F.[[Construct]] 为9.2.2指定的定义.
  2. 设置 F.[[ConstructorKind]] 为 "base".
10. 设置 F.[[Strict]] 为 strict.
11. 设置 F.[[FunctionKind]] 为 functionKind.
12. 设置 F.[[Prototype]] 为 functionPrototype.
13. 设置 F.[[Extensible]] 为 true.
14. 设置 F.[[Realm]] 为当前作用域记录项.
15. 返回 F.

### 9.2.4 FunctionInitialize ( F, kind, ParameterList, Body, Scope ) <div id="sec-functioninitialize"></div>

抽象操作FunctionInitialize要求使用以下参数：函数对象F，类型为（Normal，Method，Arrow）之一，由ParameterList指定的参数列表Parse Node，由Body指定的主体Parse Node，由Scope指定的词法环境。FunctionInitialize执行以下步骤：

1. 令 len 为 ParameterList 的 ExpectedArgumentCount.
2. 执行 ! SetFunctionLength(F, len).
3. 令 Strict 为 F.[[Strict]].
4. 设置 F.[[Environment]] 为 Scope

5. 设置 F.[[FormalParameters]] 为 ParameterList.
6. 设置 F.[[ECMAScriptCode]] 为 Body.
7. 设置 F.[[ScriptOrModule]] 为 GetActiveScriptOrModule().
8. 若 kind 是 Arrow, 设置 F.[[ThisMode]] 为 lexical.
9. 否则 若 Strict 是 true, 设置 F.[[ThisMode]] 为 strict.
10. 否则, 设置 F.[[ThisMode]] 为 global.
11. 返回 F.

### 9.2.5 FunctionCreate ( kind, ParameterList, Body, Scope, Strict [ , prototype ] ) <div id="sec-functioncreate"></div>

抽象操作FunctionCreate需要以下参数：kind（以下类型之一）：（ Normal, Method, Arrow ），由ParameterList指定的参数列表解析节点，由Body指定的主体解析节点，由Scope指定的词法环境，布尔标志Strict，以及可选的对象原型。FunctionCreate执行以下步骤：

1. 若 prototype 不存在, 那么
1. 设置 prototype 为内部对象 %FunctionPrototype%.
2. 若 kind 不是 Normal, 令 allocKind 为 "non-constructor".
3. 否则, 令 allocKind 为 "normal".
4. 令 F 为 FunctionAllocate(prototype, Strict, allocKind).
5. 返回 FunctionInitialize(F, kind, ParameterList, Body, Scope).

### 9.2.6 GeneratorFunctionCreate ( kind, ParameterList, Body, Scope, Strict ) <div id="sec-generatorfunctioncreate"></div>

抽象操作GeneratorFunctionCreate要求使用以下参数：kind是（ Normal, Method ）之一，由ParameterList指定的参数列表Parse Node，由Body指定的主体Parse Node，由Scope指定的词法环境和布尔标志Strict。GeneratorFunctionCreate执行以下步骤：

1. 令 functionPrototype 为内部对象 %Generator%.
2. 令 F 为 FunctionAllocate(functionPrototype, Strict, "generator").
3. 返回 FunctionInitialize(F, kind, ParameterList, Body, Scope).

### 9.2.7 AsyncGeneratorFunctionCreate ( kind, ParameterList, Body, Scope, Strict ) <div id="sec-asyncgeneratorfunctioncreate"></div>

抽象操作AsyncGeneratorFunctionCreate要求使用以下参数：kind（以下之一）：（ Normal, Method ），由ParameterList指定的参数列表Parse Node，由Body指定的主体Parse Node，由Scope指定的词法环境以及布尔标志Strict。AsyncGeneratorFunctionCreate执行以下步骤：

1. 令 functionPrototype 为内部对象 %AsyncGenerator%.
2. 令 F 为 ! FunctionAllocate(functionPrototype, Strict, "generator").
3. 返回 ! FunctionInitialize(F, kind, ParameterList, Body, Scope).

### 9.2.8 AsyncFunctionCreate ( kind, parameters, body, Scope, Strict ) <div id="sec-async-functions-abstract-operations-async-function-create"></div>

抽象操作AsyncFunctionCreate要求使用以下参数：kind（以下类型之一）：（ Normal, Method, Arrow ），参数列表所指定的参数解析节点，主体所指定的主体解析节点，Scope所指定的词法环境以及布尔值标志Strict。AsyncFunctionCreate执行以下步骤：

1. 令 functionPrototype 为内部对象 %AsyncFunctionPrototype%.

2. 令 F 为 ! FunctionAllocate(functionPrototype, Strict, "async").
3. 返回 ! FunctionInitialize(F, kind, parameters, body, Scope).

### 9.2.9 AddRestrictedFunctionProperties ( F, realm ) <div id="sec-addrestrictedfunctionproperties"></div>

以函数对象 F 和作用域记录项 Realm 作为其参数调用抽象操作AddRestrictedFunctionProperties。它执行以下步骤：

1. 断言: realm.[[Intrinsics]].[[%ThrowTypeError%]] 存在并且已经初始化.
2. 令 thrower 为 realm.[[Intrinsics]].[[%ThrowTypeError%]].
3. 执行 ! DefinePropertyOrThrow(F, "caller", PropertyDescriptor { [[Get]]: thrower, [[Set]]: thrower,
[[Enumerable]]: false, [[Configurable]]: true }).
4. 返回 ! DefinePropertyOrThrow(F, "arguments", PropertyDescriptor { [[Get]]: thrower, [[Set]]: thrower,
[[Enumerable]]: false, [[Configurable]]: true }).

#### 9.2.9.1 %ThrowTypeError% ( ) <div id="sec-%throwtypeerror%"></div>

％ThrowTypeError％内部函数是一个匿名内置函数对象，为每个作用域定义一次。调用％ThrowTypeError％时，它将执行以下步骤：

1. 抛出 TypeError 异常.

％ThrowTypeError％函数的[[Extensible]]内部插槽的值为false。

％ThrowTypeError％函数的“ length”属性具有属性{[[[Writable]]：false，[[Enumerable]]：false，[[Configurable]]：false}。

### 9.2.10 MakeConstructor ( F [ , writablePrototype [ , prototype ] ] ) <div id="sec-makeconstructor"></div>

抽象操作MakeConstructor需要一个Function参数 F 、一个可选的布尔值 writablePrototype 和一个对象原型。如果提供了原型，则假定已包含（如果需要）值为“ F”的“constructor”属性。此操作通过执行以下步骤将F转换为构造函数：

1. 断言: F 是ECMAScript函数对象.
2. 断言: IsConstructor(F) 是 true.
3. 断言: F 是没有prototype私有属性的可扩展对象
4. 若 writablePrototype 不存在, 设置 writablePrototype 为 true.
5. 若 prototype 不存在, 那么
  1. 设置 prototype 为 ObjectCreate(%ObjectPrototype%).
  2. 执行 ! DefinePropertyOrThrow(prototype, "constructor", PropertyDescriptor { [[Value]]: F,[[Writable]]: writablePrototype, [[Enumerable]]: false, [[Configurable]]: true }).
6. 执行 ! DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
writablePrototype, [[Enumerable]]: false, [[Configurable]]: false }).
7. 返回 NormalCompletion(undefined).

### 9.2.11 MakeClassConstructor ( F ) <div id="sec-makeclassconstructor"></div>

具有参数F的抽象操作MakeClassConstructor执行以下步骤：

1. 断言: F 是ECMAScript函数对象.

2. 断言: F.[[FunctionKind]] 是 "normal".
3. 设置 F.[[FunctionKind]] 为 "classConstructor".
4. 返回 NormalCompletion(undefined).

### 9.2.12 MakeMethod ( F, homeObject ) <div id="sec-makemethod"></div>

具有参数 F 和 homeObject 的抽象操作 MakeMethod 通过执行以下步骤将F配置为方法：

1. 断言: F 是ECMAScript函数对象.
2. 断言: Type(homeObject) 是 Object.
3. 设置 F.[[HomeObject]] 为 homeObject.
4. 返回 NormalCompletion(undefined)

### 9.2.13 SetFunctionName ( F, name [ , prefix ] ) <div id="sec-setfunctionname"></div>

抽象操作SetFunctionName需要一个Function参数F，一个String或Symbol参数名称以及一个可选的String参数前缀。该操作通过执行以下步骤将名称属性添加到F：

1. 断言: F 是没有name私有属性的可扩展对象.
2. 断言: Type(name) 是 Symbol 或 String.
3. 断言: 若 prefix 存在, 那么 Type(prefix) 是 String.
4. 若 Type(name) 是 Symbol, 那么
  1. 令 description 为 name's [[Description]] value.
  2. 若 description 是 undefined, 设置 name 为 the empty String.
  3. 否则, 设置 name 为 the string-concatenation of "[", description, and "]".
5. 若 prefix 存在, 那么
1. 设置 name 为前缀的字符串连接（码元0x0020（空格））和name
6. 返回 ! DefinePropertyOrThrow(F, "name", PropertyDescriptor { [[Value]]: name, [[Writable]]: false,
[[Enumerable]]: false, [[Configurable]]: true }).

### 9.2.14 SetFunctionLength ( F, length ) <div id="sec-setfunctionlength"></div>

抽象操作SetFunctionLength需要一个Function参数F和一个Number参数长度。此操作通过执行以下步骤为F添加一个“length”属性：

1. 断言: F 是没有"length"私有属性的可扩展对象.
2. 断言: Type(length) 是 Number.
3. 断言: length ≥ 0 并且 ! ToInteger(length) 等于 length.
4. 返回 ! DefinePropertyOrThrow(F, "length", PropertyDescriptor { [[Value]]: length, [[Writable]]: false,
[[Enumerable]]: false, [[Configurable]]: true }).

### 9.2.15 FunctionDeclarationInstantion ( func, argumentsList ) <div id="sec-functiondeclarationinstantiation"></div>

> 注：当建立用于执行ECMAScript函数的执行上下文时，将创建一个新函数环境记录项，并在该环境记录项中实例化每个形式参数的绑定。函数体中的每个声明也都被实例化。如果函数的形参不包含任何默认值初始化程序，则主体声明将在与参数相同的环境记录中实例化。如果存在默认值参数初始化程序，则会为主体声明创建第二个环境记录。正式的参数和函数是初始化为FunctionDeclarationInstantiation的一部分。在函数体执行期间初始化所有其他绑定。

使用参数func和argumentsList如下执行FunctionDeclarationInstantiation。func是为其建立执行上下文的函数对象。

1. 令 calleeContext 为运行时执行上下文.
2. 令 env 为 calleeContext 的词法环境.
3. 令 envRec 为 env 的环境记录项.
4. 令 code 为 func.[[ECMAScriptCode]].
5. 令 strict 为 func.[[Strict]].
6. 令 formals 为 func.[[FormalParameters]].
7. 令 parameterNames 为 formals 的绑定名称.
8. 若 parameterNames 有任何重复条目, 令 hasDuplicates 为 true. 否则, 令 hasDuplicates 为 false.
9. 令 simpleParameterList 为 formals 的 IsSimpleParameterList .
10. 令 hasParameterExpressions 为 formals 的 ContainsExpression.
11. 令 varNames 为 code 的 VarDeclaredNames.
12. 令 varDeclarations 为 code 的 VarScopedDeclarations.
13. 令 lexicalNames 为 code 的 LexicallyDeclaredNames .
14. 令 functionNames 为一个新的空列表.
15. 令 functionsToInitialize 为一个新的空列表.
16. 对于在 varDeclarations 中的每一个 d, 倒序排列, 执行
    1. 若 d 不是 VariableDeclaration、 ForBinding 或 BindingIdentifier, 那么
       1. 断言: d 是 FunctionDeclaration, GeneratorDeclaration, AsyncFunctionDeclaration, 或 AsyncGeneratorDeclaration.
       2. 令 fn 为 d 的绑定名称的唯一元素.
       3. 若 fn 不是 functionNames 元素之一, 那么
          1. 插入fn 作为functionNames的第一个元素。
          2. 注意: 如果有多个相同名称的函数声明，则使用最后一个声明。
          3. 插入d 作为functionsToInitialize的第一个元素。
17. 令 argumentsObjectNeeded 为 true.
18. 若 func.[[ThisMode]] 是 lexical, 那么
    1. 注意: 箭头函数从不具有参数对象。
    2. 设置 argumentsObjectNeeded 为 false.
19. 否则 若 "arguments" 是  parameterNames 中的一个元素, 那么
    1. 设置 argumentsObjectNeeded 为 false.
20. 否则 若 hasParameterExpressions 是 false, 那么
    1. 若 "arguments" 是 functionNames 的一个元素，或者若 "arguments" 是 lexicalNames 中的一个元素, 那么
       1. 设置 argumentsObjectNeeded 为 false.
21. 对于在 parameterNames 中的每一个字符串 paramName , 执行
    1. 令 alreadyDeclared 为 envRec.HasBinding(paramName).
    2. 注意：早期错误确保重复的参数名称只能在没有参数默认值或其余参数的非严格函数中出现。
    3. 若 alreadyDeclared 是 false, 那么
         1. 执行 ! envRec.CreateMutableBinding(paramName, false).
         2. 若 hasDuplicates 是 true, 那么
            1. 执行 ! envRec.InitializeBinding(paramName, undefined).
22. 若 argumentsObjectNeeded 是 true, 那么
    1. 若 strict 是 true 或 若 simpleParameterList 是 false, 那么
       1. 令 ao 为 CreateUnmappedArgumentsObject(argumentsList).
    2. 否则,
        1. 注意: 映射的参数对象仅提供给没有剩余参数、任何参数默认值初始化程序或任何非结构化参数的非严格函数。
      2. 令 ao 为 CreateMappedArgumentsObject(func, formals, argumentsList, envRec).
    3. 若 strict 是 true, 那么
         1. 执行 ! envRec.CreateImmutableBinding("arguments", false).
    4. 否则,
         1. 执行 ! envRec.CreateMutableBinding("arguments", false).
    5. 调用 envRec.InitializeBinding("arguments", ao).
    6. 令 parameterBindings 为一个新的附加“ arguments ”的parameterNames列表。
23. 否则,
    1. 令 parameterBindings 为 parameterNames.
24. 令 iteratorRecord 为 CreateListIteratorRecord(argumentsList).
25. 若 hasDuplicates 是 true, 那么
    1. 为有 iteratorRecord 和 undefined 参数的 formals，执行 ? IteratorBindingInitialization。
26. 否则,
    1. 为有 iteratorRecord 和 env 参数的 formals， 执行 ? IteratorBindingInitialization。
27. 若 hasParameterExpressions 是 false, 那么
    1. 注意: 参数和顶级变量仅需要一个词法环境。
    2. 令 instantiatedVarNames 为 parameterBindings 列表的一个拷贝.
    3. 对于 varNames 中的每一个 n, 执行
         1. 若 n 不是 instantiatedVarNames 中的一个元素, 那么
            1. 添加 n 到 instantiatedVarNames.
            2. 执行 ! envRec.CreateMutableBinding(n, false).
            3. 调用 envRec.InitializeBinding(n, undefined).
     4. 令 varEnv 为 env.
     5. 令 varEnvRec 为 envRec.
30. 否则,
    1. 注意: 需要单独的环境记录，以确保由形参列表中的表达式创建的闭包在函数体中不具有声明的可见性。
    2. 令 varEnv 为 NewDeclarativeEnvironment(env).
    3. 令 varEnvRec 为 varEnv's 环境记录项.
    4. 设置 calleeContext 的可变环境为 varEnv.
    5. 令 instantiatedVarNames 为一个新的空列表.
    6. 对于 varNames 中的每一个 n, 执行
       1.  若 n 不是 instantiatedVarNames 的一个元素, 那么
          1. Append n 为 instantiatedVarNames.
          2. 执行 ! varEnvRec.CreateMutableBinding(n, false).
          3. 若 n 不是 parameterBindings 的一个元素，或者若 n 是 functionNames 的一个元素, 令 initialValue为 undefined.
          4. 否则,
             1. 令 initialValue 为 ! envRec.GetBindingValue(n, false).
          5. 调用 varEnvRec.InitializeBinding(n, initialValue).
          6. 注意: 名称与形参相同的var，最初时与对应的初始化参数具有相同的值。
29. 注意: 附录 2.3.3.1 此时会添加其他步骤。
30. 若 strict 是 false, 那么
    1. 令 lexEnv 为 NewDeclarativeEnvironment(varEnv).
    2. 注意: 非严格函数将单独的词法环境记录用于顶级词法声明，以便直接eval可以确定由eval代码引入的任何var范围声明是否与预先存在的顶级词法范围声明冲突。严格的函数不需要这样做，因为严格的直接执行总是将所有声明放入新的环境记录中。
31. 否则, 令 lexEnv 为 varEnv.
32. 令 lexEnvRec 为 lexEnv's 环境记录项.
33. 设置 the LexicalEnvironment of calleeContext 为 lexEnv.
34. 令 lexDeclarations 为 code 的  LexicallyScopedDeclarations 
35. 对于每一个 lexDeclarations 中的元素 d, 执行
    1. 注意: 词法声明的名称不能与函数/生成器声明，形参或var名称相同。词汇声明的名称仅在此处实例化，而不初始化。
    2. 对于 d 的绑定名称的每一个元素 dn, 执行
       1. 若 d 的 IsConstantDeclaration 是 true, 那么
          1. 执行 ! lexEnvRec.CreateImmutableBinding(dn, true).
       2. 否则,
          1. 执行 ! lexEnvRec.CreateMutableBinding(dn, false).
36. 对于 functionsToInitialize 的每一个解释节点 f，执行
    1. 令 fn 为 f 的绑定名称的唯一元素
    2. 令 fo 为对用参数 LexEnv 执行 f 的 InstantiateFunctionObject 的结果
    3. 执行 ! varEnvRec.SetMutableBinding(fn, fo, false).
37. 返回 NormalCompletion(empty).

> 注2：2.3.3提供了上述算法的扩展，此扩展是向后兼容ECMAScript 2015之前的ECMAScript的Web浏览器实现所必需的。

> 注3：参数初始化程序可能包含直接的eval表达式。此类评估的任何顶级声明仅对执行代码（10.2）可见。14.1.19中描述了为此类声明创建环境的过程。

## 9.3 内置函数对象 <div id="built-in-function-objects"></div>

本规范中定义的内置函数对象可以实现为ECMAScript函数对象（9.2），其行为使用ECMAScript代码提供，也可以实现为提供的函数异类对象，其行为以其他方式提供。无论哪种情况，调用此类函数的效果都必须符合其规范。一个实现也可以提供本规范中未定义的其他内置函数对象。

如果将内置函数对象实现为异类对象，则它必须具有9.1中指定的普通对象行为。所有此类函数异类对象还具有[[Prototype]]，[[Extensible]]，[[Realm]]和[[ScriptOrModule]]内部插槽

除非另有说明，否则每个内置函数对象都将％FunctionPrototype％对象作为其[[Prototype]]内部插槽的初始值。

通过算法步骤或其他方式为每个内置函数指定的行为是该函数的[[Call]]和[[Construct]]调用的函数主体行为的规范。但是，[[Construct]]调用不是所有内置函数都支持。对于每个内置函数，当使用[[Call]]调用时，[[Call]] thisArgument提供this值，[[Call]] argumentsList提供命名参数，NewTarget值为undefined。当使用[[Construct]]调用时，此值未初始化，[[Construct]] argumentsList提供了命名参数，而[[Construct]] newTarget参数提供NewTarget值。如果内置函数是作为ECMAScript函数对象实现，则此指定的行为必须由ECMAScript实现该函数的主体代码。作为ECMAScript函数对象的内置函数必须是严格函数。如果内置构造函数具有任何[[Call]]行为，除了抛出TypeError异常，必须以不会导致函数的[[FunctionKind]]内部插槽的值为“ classConstructor”的方式完成函数的ECMAScript实现。

除非在特定函数的说明中另有指定，否则未被标识为构造函数的内置函数对象不会实现[[Construct]]内部方法。当将内置构造函数作为新表达式的一部分调用时，被调用的[[Construct]]内部方法的argumentsList参数将提供内置构造函数的命名参数的值。

非构造函数的内置函数没有原型属性，除非在特定函数的说明中另有指定。

如果内置函数对象未实现为ECMAScript函数，则必须提供符合以下定义的[[Call]]和[[Construct]]内部方法：

### 9.3.1 [[Call]] ( thisArgument, argumentsList ) <div id="sec-built-in-function-objects-call-thisargument-argumentslist"></div>

使用参数thisArgument和argumentsList（ECMAScript语言值列表）来调用内置函数对象F的[[Call]]内部方法。采取以下步骤：

1. 令 callerContext 为运行时执行上下文.
2. 若 callerContext 不是已经挂起, 挂起 callerContext.
3. 令 calleeContext 为新的ECMAScript代码执行上下文.
4. 设置 the Function of calleeContext 为 F.
5. 令 calleeRealm 为 F.[[Realm]].
6. 设置 calleeContext 的作用域为 calleeRealm.
7. 设置 calleeContext 的 ScriptOrModule 为 F.[[ScriptOrModule]].
8. 对 calleeContext 执行任何必要的实现定义的初始化。
9. 将 calleeContext 推入执行上下文堆栈；calleeContext现在是运行中的执行上下文。
10. 令 result 为完成记录项，它是按照符合 F 规范的实现定义的方式执行 F 的结果。thisArgument 是 this 值，argumentsList提供命名的参数，而NewTarget值是undefined。
11. 从执行上下文堆栈中删除calleeContext，并将callerContext还原为正在运行的执行上下文。
12. 返回 result.

> 注：从执行上下文堆栈中删除 calleeContext 时，如果可访问的生成器对象已将其暂停并保留以供以后恢复，则不能销毁它。

### 9.3.2 [[Construct]] ( argumentsList, newTarget ) <div id="sec-built-in-function-objects-construct-argumentslist-newtarget"></div>

使用参数argumentsList和newTarget调用内置函数对象F的[[Construct]]内部方法。执行的步骤与[[Call]]（请参阅9.3.1）相同，但步骤10替换为：

10. 令 result 为完成记录项，它是按照符合 F 规范的实现定义方式执行 F 的结果。this 值未初始化，argumentsList 提供命名的参数，newTarget 提供 NewTarget 的值。

### 9.3.3 CreateBuiltinFunction ( steps, internalSlotsList [ , realm [ , prototype ] ] ) <div id="sec-createbuiltinfunction"></div>

抽象操作CreateBuiltinFunction接受参数步骤，internalSlotsList，作用域和原型。参数internalSlotsList是必须定义为对象一部分的其他内部插槽名称的列表。CreateBuiltinFunction返回通过以下步骤创建的内置函数对象：

1. 断言: step 是本规范中提供的一组算法步骤或函数行为的其他定义。
2. 若 realm 不存在, 设置 realm 为当前作用域记录项.
3. 断言: realm 是作用域记录项.
4. 若 prototype 不存在, 设置 prototype 为 realm.[[Intrinsics]].[[%FunctionPrototype%]].
5. 令 func 是一个新的内置函数对象，当调用该函数对象时，它执行 step 描述的操作。新函数对象具有内部插槽，其名称是 internalSlotsList 的元素。这些内部插槽中每个插槽的初始值均为undefined。
6. 设置 func.[[Realm]] 为 realm.
7. 设置 func.[[Prototype]] 为 prototype.
8. 设置 func.[[Extensible]] 为 true.
9. 设置 func.[[ScriptOrModule]] 为 null.
10. 返回 func.

通过调用CreateBuiltinFunction抽象操作来创建本规范中定义的每个内置函数。

## 9.4 内置异类对象内部方法和插槽 <div id="built-in-exotic-object-internal-methods-and-slots"></div>

该规范定义了几种内置的异类对象。这些对象的行为通常与普通对象相似，除了一些特定的情况。下列异类对象使用普通对象内部方法，除非在下面另行明确指定：

### 9.4.1 绑定函数异类对象 <div id="bound-function-exotic-objects"></div>

绑定函数是包装另一个函数对象的异类对象。绑定函数是可调用的（它具有[[Call]]内部方法，并且可能具有[[Construct]]内部方法）。调用绑定函数通常会导致调用其包装函数。

绑定函数对象没有表27中定义的ECMAScript功能对象的内部插槽。相反，它们具有表28中定义的内部插槽。

| 内部插槽                | 类型       | 描述                                                         |
| ----------------------- | ---------- | ------------------------------------------------------------ |
| [[BoundTargetFunction]] | 可调用对象 | 包装的函数对象。                                             |
| [[BoundThis]]           | 任何       | 当调用包装函数时，始终作为this值传递的值。                   |
| [[BoundArguments]]      | 任何列表   | 一个值的列表，其元素被用作对包装函数的任何调用的第一个参数。 |

绑定函数对象提供了9.1中指定的所有基本内部方法。但是，对于函数对象的基本内部方法，它们使用以下定义。

#### 9.4.1.1 [[Call]] ( thisArgument, argumentsList ) <div id="sec-bound-function-exotic-objects-call-thisargument-argumentslist"></div>

当使用参数thisArgument和argumentsList（ECMAScript语言值列表）调用使用绑定函数创建的绑定函数异类对象F的[[Call]]内部方法时，将执行以下步骤：

1. 令 target 为 F.[[BoundTargetFunction]].
2. 令 boundThis 为 F.[[BoundThis]].
3. 令 boundArgs 为 F.[[BoundArguments]].
4. 令 args 为一个新列表，其包含与列表boundArgs相同的值（顺序相同），然后与列表argumentsList的顺序相同。
5. 返回 ? Call(target, boundThis, args).

#### 9.4.1.2 [[Construct]] ( argumentsList, newTarget ) <div id="sec-bound-function-exotic-objects-construct-argumentslist-newtarget"></div>

当使用参数列表arguestList和newTarget调用使用绑定函数创建的绑定函数外部对象的[[Construct]]内部方法时，将执行以下步骤：

1. 令 target 为 F.[[BoundTargetFunction]].
2. 断言: IsConstructor(target) 是 true.
3. 令 boundArgs 为 F.[[BoundArguments]].
4. 令 args 为一个新列表，其包含与列表boundArgs相同的值（顺序相同），然后与列表argumentsList的顺序相同。
5. 若 SameValue(F, newTarget) 是 true, 设置 newTarget 为 target.
6. 返回 ? Construct(target, args, newTarget).

#### 9.4.1.3 BoundFunctionCreate ( targetFunction, boundThis, boundArgs ) <div id="sec-boundfunctioncreate"></div>

具有targetFunction，boundThis和boundArgs参数的抽象操作BoundFunctionCreate用于指定创建新的绑定函数异类对象。它执行以下步骤：

1. 断言: Type(targetFunction) 是 Object.
2. 令 proto 为 ? targetFunction.\[\[GetPrototypeOf]]().
3. 令 obj 为新创建的对象
4. 设置 obj 的基本内部方法为9.1中指定的默认普通对象定义。
5. 设置 obj.[[Call]] 为 9.4.1.1 所描述的.
6. 若 IsConstructor(targetFunction) 是 true, 那么
   1.  设置 obj.[[Construct]] 为 9.4.1.2 所述的.  

7. 设置 obj.[[Prototype]] 为 proto.
8. 设置 obj.[[Extensible]] 为 true.
9. 设置 obj.[[BoundTargetFunction]] 为 targetFunction.
10. 设置 obj.[[BoundThis]] 为 boundThis.
11. 设置 obj.[[BoundArguments]] 为 boundArgs.
12. 返回 obj.

### 9.4.2 数组异类对象 <div id="array-exotic-objects"></div>

Array对象是一个特殊对象，它对数组索引属性键（请参见6.1.7）进行特殊处理。属性名称是数组索引的属性也称为元素。每个Array对象都有一个不可配置的“ length”属性，该属性的值始终是一个小于2^32的非负整数。“ length”属性的值在数值上大于每个其名称为数组索引的属性的名称；每当创建或更改Array对象的自身属性时，都会根据需要调整其他属性以保持该不变性。具体来说，每当添加一个自己的名称为数组索引的属性时，如果需要，将“ length”属性的值更改为比该数组索引的数值大一；并且，只要更改“ length”属性的值，就会删除名称为数组索引且其值不小于新长度的每个自己的属性。此约束仅适用于Array对象自身的属性，不受可能从其原型继承的“length”或数组索引属性的影响。

> 注：仅当ToString（ToUint32（P））等于P并且ToUint32（P）不等于2^32-1时，字符串属性名称P是数组索引。

数组异类对象为[[DefineOwnProperty]]内部方法提供了替代定义。除了该内部方法外，Array外部对象提供了9.1中指定的所有其他基本内部方法。

#### 9.4.2.1 [[DefineOwnProperty]] ( P, Desc ) <div id="sec-array-exotic-objects-defineownproperty-p-desc"></div>

当使用属性键P和属性描述符Desc调用Array异类对象A的[[DefineOwnProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.

2. 若 P 是 "length", 那么
    a. 返回 ? ArraySetLength(A, Desc).

3. 否则若 P 是一个数组索引, 那么
   
    1. 令 oldLenDesc 为 OrdinaryGetOwnProperty(A, "length").
    
    2. 断言: 因为数组对象是用无法删除或重新配置的length数据属性创建的，所以oldLenDesc将永远不会是未undefined的或访问者描述符。
    
    3.  令 oldLen 为 oldLenDesc.[[Value]].
    
    4. 令 index 为 ! ToUint32(P).
    
    5. 若 index ≥ oldLen 同时 oldLenDesc.[[Writable]] 是 false, 返回 false.
    
    6. 令 succeeded 为 ! OrdinaryDefineOwnProperty(A, P, Desc).
    
    7. 若 succeeded 是 false, 返回 false.
    
    8.  若 index ≥ oldLen, 那么
    
        1. 设置 oldLenDesc.[[Value]] 为 index + 1.
        2. 令 succeeded 为 OrdinaryDefineOwnProperty(A, "length", oldLenDesc).
    
        3. 断言: succeeded 是 true.
    
    9. 返回 true.
    
4. 返回 OrdinaryDefineOwnProperty(A, P, Desc).

#### 9.4.2.2 ArrayCreate ( length [ , proto ] ) <div id="sec-arraycreate"></div>

具有参数长度（0或正整数）和可选参数proto的抽象操作ArrayCreate用于指定创建新的Array异类对象。它执行以下步骤：

1. 断言: length 是大于等于零的整数
2. 若 length 是 -0, 设置 length 为 +0.
3. 若 length > 2^32 - 1, 抛出 RangeError 异常.
4. 若 proto 不存在, 设置 proto 为内部对象 %ArrayPrototype%.
5. 令 A 为新创建的数组异类对象.
6. 将除[[DefineOwnProperty]]之外的 A 的基本内部方法设置为9.1中指定的默认普通对象定义。
7. 设置 A.[[DefineOwnProperty]] 为 9.4.2.1 的规范.
8. 设置 A.[[Prototype]] 为 proto.
9. 设置 A.[[Extensible]] 为 true.
10. 执行 ! OrdinaryDefineOwnProperty(A, "length", PropertyDescriptor { [[Value]]: length, [[Writable]]: true,
[[Enumerable]]: false, [[Configurable]]: false }).
11. 返回 A.

#### 9.4.2.3 ArraySpeciesCreate ( originalArray, length ) <div id="sec-arrayspeciescreate"></div>

具有参数originalArray和length的抽象操作ArraySpeciesCreate用于使用从originalArray派生的构造函数来指定新Array对象的创建。它执行以下步骤：

1. 断言: length 是大于等于零的整数
2. 若 length 是 -0, 设置 length 为 +0.
3. 令 isArray 为 ? IsArray(originalArray).
4. 若 isArray 是 false, 返回 ? ArrayCreate(length).
5. 令 C 为 ? Get(originalArray, "constructor").
6. 若 IsConstructor(C) 是 true, 那么

  1. 令 thisRealm 为当前作用域记录项

  2. 令 realmC 为 ? GetFunctionRealm(C).
  3. 若 thisRealm 和 realmC 不是同一个作用域记录项, 那么
        1. 若 SameValue(C, realmC.[[Intrinsics]].[[%Array%]]) 是 true, 设置 C 为 undefined.
7. 若 Type(C) 是 Object, 那么

  1. 设置 C 为 ? Get(C, @@species).
  2. 若 C 是 null, 设置 C 为 undefined.
8. 若 C 是 undefined, 返回 ? ArrayCreate(length).
9. 若 IsConstructor(C) 是 false, 抛出 TypeError 异常.
10. 返回 ? Construct(C, « length »).

> 注：如果使用标准内置Array构造函数为不是正在运行的执行上下文的作用域的作用域创建了originalArray，则将使用正在运行的执行上下文的作用域创建一个新的Array。这可以与以前具有Array.prototype方法的行为的Web浏览器保持兼容性，该行为现在使用ArraySpeciesCreate定义。

#### 9.4.2.4 ArraySetLength ( A, Desc ) <div id="sec-arraysetlength"></div>

当使用Array异类对象A和属性描述符Desc调用抽象操作ArraySetLength时，将执行以下步骤：

1. 若 Desc.[[Value]] 存在, 那么
1. 返回 OrdinaryDefineOwnProperty(A, "length", Desc).
2. 令 newLenDesc 为Desc的一个拷贝。
3. 令 newLen 为 ? ToUint32(Desc.[[Value]]).
4. 令 numberLen 为 ? ToNumber(Desc.[[Value]]).
5. 若 newLen ≠ numberLen, 抛出 RangeError 异常.
6. 设置 newLenDesc.[[Value]] 为 newLen.
7. 令 oldLenDesc 为 OrdinaryGetOwnProperty(A, "length").
8. 断言: 因为将使用无法删除或重新配置的length数据属性创建Array对象，所以oldLenDesc将永远不会是未定义的或访问器描述符。
9. 令 oldLen 为 oldLenDesc.[[Value]].
10. 若 newLen ≥ oldLen, 那么
a. 返回 OrdinaryDefineOwnProperty(A, "length", newLenDesc).
11. 若 oldLenDesc.[[Writable]] 是 false, 返回 false.
12. 若 newLenDesc.[[Writable]] 存在，或者值为true, 令 newWritable 为 true.
13. 否则,
    1. 如果无法删除任何元素，则需要推迟将[[Writable]]属性设置为false。
    2. 令 newWritable 为 false.
    3. 设置 newLenDesc.[[Writable]] 为 true.
14. 令 succeeded 为 ! OrdinaryDefineOwnProperty(A, "length", newLenDesc).
15. 若 succeeded 是 false, 返回 false.
16. 重复, 直到 newLen < oldLen,
    1. oldLen减一
    2. 令 deleteSucceeded 为 ! A.\[\[Delete]](! ToString(oldLen)).
    3. 若 deleteSucceeded 是 false, 那么
       1. 设置 newLenDesc.[[Value]] 为 oldLen + 1.
       2. 若 newWritable 是 false, 设置 newLenDesc.[[Writable]] 为 false.
       3. 执行 ! OrdinaryDefineOwnProperty(A, "length", newLenDesc).
       4. 返回 false.
17. 若 newWritable 是 false, 那么
    1. 返回 OrdinaryDefineOwnProperty(A, "length", PropertyDescriptor { [[Writable]]: false })。回调一直都为 true
18. 返回 true.

> 注：在步骤3和4中，如果Desc.[[Value]]是一个对象，则其valueOf方法将被调用两次。这是从本规范的第二版开始具有此效果的历史遗留行为。

### 9.4.3 字符串异类对象 <div id="string-exotic-objects"></div>

字符串对象是一个异类对象，它封装了一个字符串值，并公开了与该字符串值的各个代码单元元素相对应的虚拟整数索引数据属性。字符串异类对象始终具有一个名为“length”的数据属性，其值是封装的字符串值中代码单元元素的数量。代码单元数据属性和“ length”属性都是不可写和不可配置的。

字符串异类对象具有与普通对象相同的内部插槽。它们还具有[[StringData]]内部插槽。

字符串异类对象为以下内部方法提供替代定义。以下未定义的所有其他其他String异类对象基本内部方法均在9.1中指定。

#### 9.4.3.1 [[GetOwnProperty]] ( P ) <div id="sec-string-exotic-objects-getownproperty-p"></div>

使用属性键P调用String异类对象S的[[GetOwnProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 desc 为 OrdinaryGetOwnProperty(S, P).
3. 若 desc 不是 undefined, 返回 desc.
4. 返回 ! StringGetOwnProperty(S, P)

#### 9.4.3.2 [[DefineOwnProperty]] ( P, Desc ) <div id="sec-string-exotic-objects-defineownproperty-p-desc"></div>

当使用属性键P和属性描述符Desc调用String异类对象S的[[DefineOwnProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 stringDesc 为 ! StringGetOwnProperty(S, P).
3. 若 stringDesc 不是 undefined, 那么
  1. 令 extensible 为 S.[[Extensible]].
  2. 返回 ! IsCompatiblePropertyDescriptor(extensible, Desc, stringDesc).
4. 返回 ! OrdinaryDefineOwnProperty(S, P, Desc).

#### 9.4.3.3 [[OwnPropertyKeys]] ( ) <div id="sec-string-exotic-objects-ownpropertykeys"></div>

当调用String异类对象O的[[OwnPropertyKeys]]内部方法时，将执行以下步骤：

1. 令 keys 为一个新的空列表.

2. 令 str 为 O.[[StringData]].

3. 断言: Type(str) 是 String.

4. 令 len 为 str 的长度.

5. 对于每个以0开头的整数，i≤len，按照升序排序, 执行
    1. 把 ! ToString(i) 添加到 keys 的最后一个元素.

6. 对于 O 的每个私有属性键P，使得P是数组索引，并且ToInteger（P）≥len，以数字索引升序排列，执行
    1. 将P添加到键的最后一个元素。
    
7. 对于 O 的每个私有属性键P，使得Type（P）为String且P不是数组索引，按属性创建的时间顺序升序，执行
    1. 将P添加到键的最后一个元素。
    
8. 对于 O 的每个私有属性键P， 使得Type（P）为Symbol，以属性创建的时间顺序升序 ，执行
   
1. 将P添加到键的最后一个元素。 
   
9. 返回 keys.

#### 9.4.3.4 StringCreate ( value, prototype ) <div id="sec-stringcreate"></div>

具有参数value和prototype的抽象操作StringCreate用于指定创建新的String异类对象。它执行以下步骤：

1. 断言: Type(value) 是 String.
2. 令 S 为新创建的String异类对象.
3. 设置 S.[[StringData]] 为 value.
4. 设置 S 的基本内部方法为9.1中指定的默认普通对象定义
5. 设置 S.[[GetOwnProperty]] 为 9.4.3.1 的规范
6. 设置 S.[[DefineOwnProperty]] 9.4.3.2 的规范.
7. 设置 S.[[OwnPropertyKeys]] 9.4.3.3 的规范.
8. 设置 S.[[Prototype]] 为 prototype.
9. 设置 S.[[Extensible]] 为 true.
10. 令 length 为 value 的码元元素的数量
11. 执行 ! DefinePropertyOrThrow(S, "length", PropertyDescriptor { [[Value]]: length, [[Writable]]: false,
[[Enumerable]]: false, [[Configurable]]: false }).
12. 返回 S.

#### 9.4.3.5 StringGetOwnProperty ( S, P ) <div id="sec-stringgetownproperty"></div>

使用参数S和P调用的抽象操作StringGetOwnProperty执行以下步骤：

1. 断言: S 是对象，并且有 [[StringData]] 内置插槽.
2. 断言: IsPropertyKey(P) 是 true.
3. 若 Type(P) 不是 String, 返回 undefined.
4. 令 index 为 ! CanonicalNumericIndexString(P).
5. 若 index 是 undefined, 返回 undefined.
6. 若 IsInteger(index) 是 false, 返回 undefined.
7. 若 index = -0, 返回 undefined.
8. 令 str 为 S.[[StringData]].
9. 断言: Type(str) 是 String.
10. 令 len 为 str 的长度.
11. 若 index < 0 或 len ≤ index, 返回 undefined.
12. 令 resultStr 为长度为1的String值，包含来自str的一个代码单元，特别是索引index处的代码单元
13. 返回 a PropertyDescriptor { [[Value]]: resultStr, [[Writable]]: false, [[Enumerable]]: true, [[Configurable]]: false}.

### 9.4.4 参数异类对象 <div id="arguments-exotic-objects"></div>

大多数ECMAScript函数使参数对象可用于其代码。根据函数定义的特性，其参数对象可以是普通对象，也可以是参数异类对象。参数异类对象是一个异类对象，其数组索引属性映射到与其关联的ECMAScript函数的调用的形式参数绑定。

参数异类对象具有与普通对象相同的内部插槽。它们还具有一个[[ParameterMap]]内部插槽。普通参数对象还具有一个[[ParameterMap]]内部插槽，其值始终是未定义的。对于普通自变量对象，[[ParameterMap]]内部插槽仅由Object.prototype.toString（19.1.3.6）用来标识它们。

参数异类对象为以下内部方法提供替代定义。以下未定义的所有其他参数异类对象基本内部方法均在9.1中指定

> 注1：参数异类对象的整数索引数据属性（其数字名称值小于相应函数对象的形参的数量）最初在函数的执行上下文中与相应的参数绑定共享其值。这意味着更改属性会更改参数绑定的相应值，反之亦然。如果删除此类属性然后重新定义，或者将该属性更改为访问器属性，则此对应关系将被破坏。如果arguments对象是普通对象，则其属性的值只是传递给函数的参数的副本，并且属性值和形式参数值之间没有动态链接。
>
> 注2：ParameterMap对象及其属性值用作指定参数对象与对应参数绑定。无法从ECMAScript代码直接观察ParameterMap对象和作为其属性值的对象。ECMAScript实现不需要实际创建或使用此类对象来实现指定的语义。
>
> 注3：普通参数对象定义了一个名为“ callee”的不可配置的访问器属性，该属性在访问时引发TypeError异常。“ callee”属性对于仅针对某些非严格函数类创建的异类对象参数具有更特定的含义。普通变体中存在此属性的定义，以确保通过遵循ECMAScript实现不以任何其他方式定义它。
>
> 注4：ECMAScript自变量对象的实现在历史上一直包含一个名为“ caller”的访问器属性。在ECMAScript 2017之前，此规范包括在普通参数对象上定义引发“调用者”属性的定义。由于实现不再包含此扩展，因此ECMAScript 2017放弃了对抛出"caller''访问器的要求。

#### 9.4.4.1 [[GetOwnProperty]] ( P ) <div id="sec-arguments-exotic-objects-getownproperty-p"></div>

使用属性键P调用时，参数异类对象的[[GetOwnProperty]]内部方法执行以下步骤：

1. 令 args 为参数对象
2. 令 desc 为 OrdinaryGetOwnProperty(args, P).
3. 若 desc 是 undefined, 返回 desc.
4. 令 map 为 args.[[ParameterMap]].
5. 令 isMapped 为 ! HasOwnProperty(map, P).
6. 若 isMapped 是 true, 那么
1. 设置 desc.[[Value]] 为 Get(map, P).
7. 返回 desc.

#### 9.4.4.2 [[DefineOwnProperty]] ( P, Desc ) <div id="sec-arguments-exotic-objects-defineownproperty-p-desc"></div>

使用属性键P和属性描述符Desc调用时，参数异类对象的[[DefineOwnProperty]]内部方法将执行以下步骤：

1. 令 args 为参数对象.

2. 令 map 为 args.[[ParameterMap]].

3. 令 isMapped 为 HasOwnProperty(map, P).

4. 令 newArgDesc 为 Desc.

5. 若 isMapped 是 true 同时 IsDataDescriptor(Desc) 是 true, 那么
      1. 若 Desc.[[Value]] 不存在，同时 Desc.[[Writable]] 存在，值为 false, 那么
         1. 设置 newArgDesc 为 Desc 的一个拷贝
         2. 设置 newArgDesc.[[Value]] 为 Get(map, P).
      
6. 令 allowed 为 ? OrdinaryDefineOwnProperty(args, P, newArgDesc).

7. 若 allowed 是 false, 返回 false.

8. 若 isMapped 是 true, 那么
   1. 若 IsAccessorDescriptor(Desc) 是 true, 那么
      1. 调用 map.\[\[Delete]](P).
   2. 否则,
      1. 若 Desc.[[Value]] 存在, 那么
         1. 令 setStatus 为 Set(map, P, Desc.[[Value]], false).
         2. 断言: setStatus为true，因为由参数对象映射的形式参数始终是可写的。
   2. 若 Desc.[[Writable]] 存在，值为 false, 那么
         1. 调用 map.\[\[Delete]](P).
   
9. 返回 true.

#### 9.4.4.3 [[Get]] ( P, Receiver ) <div id="sec-arguments-exotic-objects-get-p-receiver"></div>

使用属性键P和ECMAScript语言值Receiver调用时，参数异类对象的[[Get]]内部方法执行以下步骤：

1. 令 args 为参数对象.
2. 令 map 为 args.[[ParameterMap]].
3. 令 isMapped 为 ! HasOwnProperty(map, P).
4. 若 isMapped 是 false, 那么
1. 返回 ? OrdinaryGet(args, P, Receiver).
5. 否则映射包含用于P的形式参数映射，
1. 返回 Get(map, P).

#### 9.4.4.4 [[Set]] ( P, V, Receiver ) <div id="sec-arguments-exotic-objects-设置-p-v-receiver"></div>

使用属性键P，值V和ECMAScript语言值Receiver调用时，参数异类对象的[[Set]]内部方法：

1. 令 args 为参数对象.
2. 若 SameValue(args, Receiver) 是 false, 那么
1. 令 isMapped 为 false.
3. 否则,
  1. 令 map 为 args.[[ParameterMap]].
  2. 令 isMapped 为 ! HasOwnProperty(map, P).

4. 若 isMapped 是 true, 那么
  1. 令 setStatus 为 Set(map, P, V, false).
  2. 断言: setStatus为true，因为参数对象映射的形参始终可写
5. 返回 ? OrdinarySet(args, P, V, Receiver).

#### 9.4.4.5 [[Delete]] ( P ) <div id="sec-arguments-exotic-objects-delete-p"></div>

用属性键P调用时，参数异类对象的[[Delete]]内部方法执行以下步骤：

1. 令 args 为参数对象.
2. 令 map 为 args.[[ParameterMap]].
3. 令 isMapped 为 ! HasOwnProperty(map, P).
4. 令 result 为 ? OrdinaryDelete(args, P).
5. 若 result 是 true 同时 isMapped 是 true, 那么
1. 调用 map.\[\[Delete]](P).
6. 返回 result.

#### 9.4.4.6 CreateUnmappedArgumentsObject ( argumentsList ) <div id="sec-createunmappedargumentsobject"></div>

使用参数argumentsList调用的抽象操作CreateUnmappedArgumentsObject执行以下步骤：

1. 令 len 为argumentsList中元素的数量。
2. 令 obj 为 ObjectCreate(%ObjectPrototype%, « [[ParameterMap]] »).
3. 设置 obj.[[ParameterMap]] 为 undefined.
4. 执行 DefinePropertyOrThrow(obj, "length", PropertyDescriptor { [[Value]]: len, [[Writable]]: true,
[[Enumerable]]: false, [[Configurable]]: true }).
5. 令 index 为 0.
6. 重复, 直到 index < len,
  1. 令 val 为 argumentsList[index].
  2. 执行 CreateDataProperty(obj, ! ToString(index), val).
  3. index增加一
7. 执行 ! DefinePropertyOrThrow(obj, @@iterator, PropertyDescriptor { [[Value]]: %ArrayProto_values%,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: true }).
8. 执行 ! DefinePropertyOrThrow(obj, "callee", PropertyDescriptor { [[Get]]: %ThrowTypeError%, [[Set]]:
%ThrowTypeError%, [[Enumerable]]: false, [[Configurable]]: false }).
9. 返回 obj.

#### 9.4.4.7 CreateMappedArgumentsObject ( func, formals, argumentsList, env ) <div id="sec-createmappedargumentsobject"></div>

抽象操作CreateMappedArgumentsObject与对象函数，解析节点形式，列表argumentsList和环境记录环境一起调用。执行以下步骤：

1. 断言: formals 不包含rest参数，任何绑定模式或任何初始化程序。它可能包含重复的标识符。
2. 令 len 为argumentsList中元素的数量.
3. 令 obj 为带有 [[ParameterMap]] 内部插槽的新创建的参数异类对象
4. 设置 obj.[[GetOwnProperty]] 为 9.4.4.1 的规范.
5. 设置 obj.[[DefineOwnProperty]] 的 9.4.4.2 的规范.

6. 设置 obj.[[Get]] 为 9.4.4.3 的规范.
7. 设置 obj.[[Set]] 为 9.4.4.4  的规范 .
8. 设置 obj.[[Delete]] 为 9.4.4.5  的规范.
9. 设置 obj 的其余基本内部方法为9.1中指定的默认普通对象定义。
10. 设置 obj.[[Prototype]] 为 %ObjectPrototype%.
11. 设置 obj.[[Extensible]] 为 true.
12. 令 map 为 ObjectCreate(null).
13. 设置 obj.[[ParameterMap]] 为 map.
14. 令 parameterNames 为 formals 的绑定名称.
15. 令 numberOfParameters 为 parameterNames 的元素数量.
16. 令 index 为 0.
17. 重复, 直到 index < len,
    1. 令 val 为 argumentsList[index].
    2. 执行 CreateDataProperty(obj, ! ToString(index), val).
    3. index增加1.
18. 执行 DefinePropertyOrThrow(obj, "length", PropertyDescriptor { [[Value]]: len, [[Writable]]: true,
[[Enumerable]]: false, [[Configurable]]: true }).
19. 令 mappedNames 为一个新的空列表.
20. 令 index 为 numberOfParameters - 1.
21. 重复, 直到 index ≥ 0,
    1. 令 name 为 parameterNames[index].
    2. 若 name 不是 mappedNames 中的一个元素, 那么
      1. 添加 name，作为mappedNames列表的一个元素
      2. 若 index < len, 那么
         1. 令 g 为 MakeArgGetter(name, env).
         2. 令 p 为 MakeArgSetter(name, env).
         3. 执行 map.\[\[DefineOwnProperty]](! ToString(index), PropertyDescriptor { [[Set]]: p, [[Get]]: g,
             [[Enumerable]]: false, [[Configurable]]: true }).
         4. index减一。
22. 执行 ! DefinePropertyOrThrow(obj, @@iterator, PropertyDescriptor { [[Value]]: %ArrayProto_values%,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: true }).
23. 执行 ! DefinePropertyOrThrow(obj, "callee", PropertyDescriptor { [[Value]]: func, [[Writable]]: true,
[[Enumerable]]: false, [[Configurable]]: true }).
24. 返回 obj.

##### 9.4.4.7.1 MakeArgGetter ( name, env ) <div id="sec-makearggetter"></div>

使用String name和环境记录项 env调用的抽象操作MakeArgGetter创建一个内置函数对象，该对象在执行时返回env中为name绑定的值。它执行以下步骤：

1. 令 steps 为下面指定的ArgGetter函数的步骤。 
2. 令 getter 为 CreateBuiltinFunction(steps, « [[Name]], [[Env]] »).
3. 设置 getter.[[Name]] 为 name.
4. 设置 getter.[[Env]] 为 env.
5. 返回 getter.

ArgGetter函数是具有[[Name]]和[[Env]]内部插槽的匿名内置函数。当调用不期望参数的ArgGetter函数时，它将执行以下步骤：

1. 令 f 为活动的函数对象
2. 令 name 为 f.[[Name]].
3. 令 env 为 f.[[Env]].
4. 返回 env.GetBindingValue(name, false).

> 注：ArgGetter函数永远不能直接由ECMAScript代码访问。

##### 9.4.4.7.2 MakeArgSetter ( name, env ) <div id="sec-makeargsetter"></div>

使用String name和环境记录项 env调用的抽象操作MakeArgSetter创建一个内置函数对象，该对象在执行时设置env中为name绑定的值。它执行以下步骤：

1. 令 steps 为下面指定的ArgGetter函数的步骤。 
2. 令 setter 为 CreateBuiltinFunction(steps, « [[Name]], [[Env]] »).
3. 设置 setter.[[Name]] 为 name.
4. 设置 setter.[[Env]] 为 env.
5. 返回 setter.

ArgSetter函数是具有[[Name]]和[[Env]]内部插槽的匿名内置函数。当使用参数值调用ArgSetter函数时，它将执行以下步骤：

1. 令 f 为活动的函数对象
2. 令 name 为 f.[[Name]].
3. 令 env 为 f.[[Env]].
4. 返回 env.SetMutableBinding(name, value, false).

> 注：ArgSetter函数永远不能直接由ECMAScript代码访问。

### 9.4.5 整数索引异类对象 <div id="integer-indexed-exotic-objects"></div>

整数索引的异类对象是对整数索引属性键执行特殊处理的异类对象。

整数索引的异类对象具有与普通对象相同的内部插槽，另外还具有[[ViewedArrayBuffer]]，[[ArrayLength]]，[[ByteOffset]]和[[TypedArrayName]]内部插槽。

整数索引的异类对象为以下内部方法提供了替代定义。以下未定义的所有其他IntegerIndexed异类对象基本内部方法均在9.1中指定。

#### 9.4.5.1 [[GetOwnProperty]] ( P ) <div id="sec-integer-indexed-exotic-objects-getownproperty-p"></div>

当使用属性键P调用索引为整数的异类对象O的[[GetOwnProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.

2. 断言: O 是一个具有 [[ViewedArrayBuffer]] 内部插槽的对象。

3. 若 Type(P) 是 String, 那么
    1. 令 numericIndex 为 ! CanonicalNumericIndexString(P).

    2. 若 numericIndex 不是 undefined, 那么
       1. 令 value 为 ? IntegerIndexedElementGet(O, numericIndex).
    2. 若 value 是 undefined, 返回 undefined.
    3. 返回 PropertyDescriptor { [[Value]]: value, [[Writable]]: true, [[Enumerable]]: true,
      [[Configurable]]: false }.

4. 返回 OrdinaryGetOwnProperty(O, P)

#### 9.4.5.2 [[HasProperty]] ( P ) <div id="sec-integer-indexed-exotic-objects-hasproperty-p"></div>

当使用属性键P调用整数索引的异类对象O的[[HasProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 断言: O 是一个具有 [[ViewedArrayBuffer]] 内部插槽的对象。
3. 若 Type(P) 是 String, 那么
  1. 令 numericIndex 为 ! CanonicalNumericIndexString(P).
  2. 若 numericIndex 不是 undefined, 那么
    1. 令 buffer 为 O.[[ViewedArrayBuffer]].
    2. 若 IsDetachedBuffer(buffer) 是 true, 抛出 TypeError 异常.
    3. 若 IsInteger(numericIndex) 是 false, 返回 false.
    4. 若 numericIndex = -0, 返回 false.
    5. 若 numericIndex < 0, 返回 false.
    6. 若 numericIndex ≥ O.[[ArrayLength]], 返回 false.
    7. 返回 true.
4. 返回 ? OrdinaryHasProperty(O, P).

#### 9.4.5.3 [[DefineOwnProperty]] ( P, Desc ) <div id="sec-integer-indexed-exotic-objects-defineownproperty-p-desc"></div>

当使用属性键P和属性描述符Desc调用整数索引的异类对象O的[[DefineOwnProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 断言: O 是一个具有 [[ViewedArrayBuffer]] 内部插槽的对象。
3. 若 Type(P) 是 String, 那么
    
    1. 令 numericIndex 为 ! CanonicalNumericIndexString(P).
    
    2. 若 numericIndex 不是 undefined, 那么
    
      1. 若 IsInteger(numericIndex) 是 false, 返回 false.
      2. 若 numericIndex = -0, 返回 false.
      3. 若 numericIndex < 0, 返回 false.
      4. 令 length 为 O.[[ArrayLength]].
      5. 若 numericIndex ≥ length, 返回 false.
      6. 若 IsAccessorDescriptor(Desc) 是 true, 返回 false.
      7. 若 Desc 有 [[Configurable]] 字段，并且若 Desc.[[Configurable]] 是 true, 返回 false.
      8. 若 Desc 有 [[Enumerable]] 字段，并且若 Desc.[[Enumerable]] 是 false, 返回 false.
      9. 若 Desc 有 [[Writable]] 字段，并且若 Desc.[[Writable]] 是 false, 返回 false.
      10. 若 Desc 有 [[Value]] 字段, 那么
          1. 令 value 为 Desc.[[Value]].
          2. 返回 ? IntegerIndexedElementSet(O, numericIndex, value).
    
      11. 返回 true.
    
4. 返回 ! OrdinaryDefineOwnProperty(O, P, Desc)

#### 9.4.5.4 [[Get]] ( P, Receiver ) <div id="sec-integer-indexed-exotic-objects-get-p-receiver"></div>

使用属性键P和ECMAScript语言值Receiver调用整数索引的异类对象O的[[Get]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 若 Type(P) 是 String, 那么
  1. 令 numericIndex 为 ! CanonicalNumericIndexString(P).
  2. 若 numericIndex 不是 undefined, 那么
    1. 返回 ? IntegerIndexedElementGet(O, numericIndex).
3. 返回 ? OrdinaryGet(O, P, Receiver).

#### 9.4.5.5 [[Set]] ( P, V, Receiver ) <div id="sec-integer-indexed-exotic-objects-设置-p-v-receiver"></div>

当使用属性键P，值V和ECMAScript语言值Receiver调用整数索引的异类对象O的[[Set]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 若 Type(P) 是 String, 那么
  1. 令 numericIndex 为 ! CanonicalNumericIndexString(P).
  2. 若 numericIndex 不是 undefined, 那么
    1. 返回 ? IntegerIndexedElementSet(O, numericIndex, V).
3. 返回 ? OrdinarySet(O, P, V, Receiver).

#### 9.4.5.6 [[OwnPropertyKeys]] ( ) <div id="sec-integer-indexed-exotic-objects-ownpropertykeys"></div>

当调用整数索引的异类对象O的[[OwnPropertyKeys]]内部方法时，将执行以下步骤：

1. 令 keys 为一个新的空列表.
2. 断言: O 是一个对象，具有 [[ViewedArrayBuffer]], [[ArrayLength]], [[ByteOffset]], and [[TypedArrayName]] 内部插槽.
3. 令 len 为 O.[[ArrayLength]].
4. 对于每个以0开头的整数，i≤len，升序排列, 执行
1. 添加 ! ToString(i) 到 keys 的最后一个元素
5. 对于 O 的每个自己的属性键P，使得Type（P）为String且P不是整数索引，以属性创建的时间顺序升序, 执行
1. 将P添加到键的最后一个元素。
6. 对于 O 的每个自己的属性键P，以使Type（P）为Symbol，以属性创建的时间顺序升序, 执行
1. 将P添加到键的最后一个元素。
7. 返回 keys.

#### 9.4.5.7 IntegerIndexedObjectCreate ( prototype, internalSlotsList ) <div id="sec-integerindexedobjectcreate"></div>

带参数prototype和internalSlotsList的抽象操作IntegerIndexedObjectCreate用于指定创建新的以整数索引表示的异类对象。参数internalSlotsList是必须定义为对象一部分的其他内部插槽名称的列表。IntegerIndexedObjectCreate执行以下步骤：

1. 断言: internalSlotsList 包含名为 [[ViewedArrayBuffer]], [[ArrayLength]], [[ByteOffset]] 和
[[TypedArrayName]].
2. 令 A 为一个新创建的对象，该对象的内部插槽为internalSlotsList中的每个名称
3. 设置 A's essential internal methods 为 the default ordinary object definitions specified in 9.1.
4. 设置 A.[[GetOwnProperty]] 按照 9.4.5.1的规范.
5. 设置 A.[[HasProperty]] 按照 9.4.5.2 的规范.
6. 设置 A.[[DefineOwnProperty]] 按照 9.4.5.3 的规范.
7. 设置 A.[[Get]] 按照 9.4.5.4 的规范.
8. 设置 A.[[Set]] 按照 9.4.5.5 的规范.
9. 设置 A.[[OwnPropertyKeys]] 按照 9.4.5.6 的规范.
10. 设置 A.[[Prototype]] 为 prototype.
11. 设置 A.[[Extensible]] 为 true.
12. 返回 A.

#### 9.4.5.8 IntegerIndexedElementGet ( O, index ) <div id="sec-integerindexedelementget"></div>

具有参数O和index的抽象操作IntegerIndexedElementGet执行以下步骤：

1. 断言: Type(index) 是 Number.
2. 断言: O 是一个对象，拥有 [[ViewedArrayBuffer]], [[ArrayLength]], [[ByteOffset]], and [[TypedArrayName]]
内部插槽.
3. 令 buffer 为 O.[[ViewedArrayBuffer]].
4. 若 IsDetachedBuffer(buffer) 是 true, 抛出 TypeError 异常.
5. 若 IsInteger(index) 是 false, 返回 undefined.
6. 若 index = -0, 返回 undefined.
7. 令 length 为 O.[[ArrayLength]].
8. 若 index < 0 或 index ≥ length, 返回 undefined.
9. 令 offset 为 O.[[ByteOffset]].
10. 令 arrayTypeName 为 O.[[TypedArrayName]] 的 String 值.
11. 令 elementSize 为表59中为 arrayTypeName 指定的元素大小的Number值。
12. 令 indexedPosition 为 (index × elementSize) + offset.
13. 令 elementType 为表59中arrayTypeName的元素类型的String值。
14. 返回 GetValueFromBuffer(buffer, indexedPosition, elementType, true, "Unordered").

#### 9.4.5.9 IntegerIndexedElementSet ( O, index, value ) <div id="sec-integerindexedelementset"></div>

具有参数O，索引和值的抽象操作IntegerIndexedElementSet执行以下步骤：

1. 断言: Type(index) 是 Number.
2. 断言: O 是对象，拥有[[ViewedArrayBuffer]], [[ArrayLength]], [[ByteOffset]], and [[TypedArrayName]]
内部插槽.
3. 令 numValue 为 ? ToNumber(value).
4. 令 buffer 为 O.[[ViewedArrayBuffer]].
5. 若 IsDetachedBuffer(buffer) 是 true, 抛出 TypeError 异常.
6. 若 IsInteger(index) 是 false, 返回 false.
7. 若 index = -0, 返回 false.
8. 令 length 为 O.[[ArrayLength]].
9. 若 index < 0 或 index ≥ length, 返回 false.
10. 令 offset 为 O.[[ByteOffset]].

11. 令 arrayTypeName 为 O.[[TypedArrayName]] 的String值.
12. 令 elementSize 为表59中为 arrayTypeName 指定的元素大小的Number值。
13. 令 indexedPosition 为 (index × elementSize) + offset.
14. 令 elementType 为表59中arrayTypeName的元素类型的String值。
15. 执行 SetValueInBuffer(buffer, indexedPosition, elementType, numValue, true, "Unordered").
16. 返回 true

### 9.4.6 模块命名空间异类对象 <div id="module-namespace-exotic-objects"></div>

模块名称空间对象是一个异类对象，它公开了从ECMAScript模块导出的绑定（请参见15.2.3）。模块名称空间异类对象的字符串键自己的属性与模块导出的绑定名称之间存在一一对应的关系。导出的绑定包括使用 export* 导出项间接导出的任何绑定。每个String值自己的属性键是相应的导出绑定名称的StringValue。这些是模块名称空间异类对象的唯一String-keyed属性。每个此类属性都具有属性{[[[Writable]]：true，[[Enumerable]]：true，[[Configurable]]：false}。模块名称空间对象不可扩展。

| 内部插槽    | 类型       | 描述                                                         |
| ----------- | ---------- | ------------------------------------------------------------ |
| [[Module]]  | 模块记录项 | 此名称空间公开的模块记录                                     |
| [[Exports]] | String列表 | 一个列表，其中包含作为该对象的属性公开的导出名称的String值。该列表被排序，就好像使用Array.prototype.sort使用undefined作为comparefn对相同值的Array进行了排序一样。 |

模块名称空间异类对象为[[GetPrototypeOf]]以外的所有内部方法提供了替代定义，其行为符合9.1.1的定义。

#### 9.4.6.1 [[SetPrototypeOf]] ( V ) <div id="sec-module-namespace-exotic-objects-setprototypeof-v"></div>

当使用参数V调用模块名称空间异类对象O的[[SetPrototypeOf]]内部方法时，将执行以下步骤：

1. 返回 ? SetImmutablePrototype(O, V).

#### 9.4.6.2 [[IsExtensible]] ( ) <div id="sec-module-namespace-exotic-objects-isextensible"></div>

当调用模块名称空间异类对象O的[[IsExtensible]]内部方法时，将执行以下步骤：

1. 返回 false.

#### 9.4.6.3 [[PreventExtensions]] ( ) <div id="sec-module-namespace-exotic-objects-preventextensions"></div>

调用模块名称空间异类对象O的[[PreventExtensions]]内部方法时，将执行以下步骤：

1. 返回 true.

#### 9.4.6.4 [[GetOwnProperty]] ( P ) <div id="sec-module-namespace-exotic-objects-getownproperty-p"></div>

当使用属性键P调用模块名称空间异类对象O的[[GetOwnProperty]]内部方法时，将执行以下步骤：

1. 若 Type(P) 是 Symbol, 返回 OrdinaryGetOwnProperty(O, P).
2. 令 exports 为 O.[[Exports]].
3. 若 P 不是 exports 的一个元素, 返回 undefined.
4. 令 value 为 ? O.\[\[Get]](P, O).
5. 返回 PropertyDescriptor { [[Value]]: value, [[Writable]]: true, [[Enumerable]]: true, [[Configurable]]: false }.

#### 9.4.6.5 [[DefineOwnProperty]] ( P, Desc ) <div id="sec-module-namespace-exotic-objects-defineownproperty-p-desc"></div>

当使用属性键P和属性描述符Desc调用模块名称空间异类对象O的[[DefineOwnProperty]]内部方法时，将执行以下步骤：

1. 若 Type(P) 是 Symbol, 返回 OrdinaryDefineOwnProperty(O, P, Desc).
2. 令 current 为 ? O.\[\[GetOwnProperty]](P).
3. 若 current 是 undefined, 返回 false.
4. 若 IsAccessorDescriptor(Desc) 是 true, 返回 false.
5. 若 Desc.[[Writable]] 存在，值为false, 返回 false.
6. 若 Desc.[[Enumerable]] 存在，值为false, 返回 false.
7. 若 Desc.[[Configurable]] 存在，值为true, 返回 false.
8. 若 Desc.[[Value]] 存在, 返回 SameValue(Desc.[[Value]], current.[[Value]]).
9. 返回 true.

#### 9.4.6.6 [[HasProperty]] ( P ) <div id="sec-module-namespace-exotic-objects-hasproperty-p"></div>

当使用属性键P调用模块名称空间异类对象O的[[HasProperty]]内部方法时，将执行以下步骤：

1. 若 Type(P) 是 Symbol, 返回 OrdinaryHasProperty(O, P).
2. 令 exports 为 O.[[Exports]].
3. 若 P 是 exports 的一个元素, 返回 true.
4. 返回 false

#### 9.4.6.7 [[Get]] ( P, Receiver ) <div id="sec-module-namespace-exotic-objects-get-p-receiver"></div>

当使用属性键P和ECMAScript语言值Receiver调用模块名称空间异类对象O的[[Get]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 若 Type(P) 是 Symbol, 那么
1. 返回 ? OrdinaryGet(O, P, Receiver).

3. 令 exports 为 O.[[Exports]].
4. 若 P 不是 exports 的一个元素, 返回 undefined.
5. 令 m 为 O.[[Module]].
6. 令 binding 为 ! m.ResolveExport(P, « »).
7. 断言: binding 是 ResolvedBinding 记录项.
8. 令 targetModule 为 binding.[[Module]].
9. 断言: targetModule 不是 undefined.
10. 令 targetEnv 为 targetModule.[[Environment]].
11. 若 targetEnv 是 undefined, 抛出 ReferenceError 异常.
12. 令 targetEnvRec 为 targetEnv 的环境记录项.
13. 返回 ? targetEnvRec.GetBindingValue(binding.[[BindingName]], true).

> 注：ResolveExport是幂等且无副作用的。一个实现可能选择为每个模块名称空间异类对象的[[Exports]]预计算或缓存ResolveExport结果。

#### 9.4.6.8 [[Set]] ( P, V, Receiver ) <div id="sec-module-namespace-exotic-objects-设置-p-v-receiver"></div>

当使用属性键P，值V和ECMAScript语言值Receiver调用模块名称空间异类对象O的[[Set]]内部方法时，将执行以下步骤：

1. 返回 false.

#### 9.4.6.9 [[Delete]] ( P ) <div id="sec-module-namespace-exotic-objects-delete-p"></div>

当使用属性键P调用模块名称空间异类对象O的[[Delete]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 若 Type(P) 是 Symbol, 那么
1. 返回 ? OrdinaryDelete(O, P).
3. 令 exports 为 O.[[Exports]].
4. 若 P 是 exports 的一个元素, 返回 false.
5. 返回 true.

#### 9.4.6.10 [[OwnPropertyKeys]] ( ) <div id="sec-module-namespace-exotic-objects-ownpropertykeys"></div>

当调用模块名称空间异类对象O的[[OwnPropertyKeys]]内部方法时，将执行以下步骤：

1. 令 exports 为 O.[[Exports]] 的一个拷贝.
2. 令 symbolKeys 为 ! OrdinaryOwnPropertyKeys(O).
3. 将 symbolKeys 的所有条目追加到导出的末尾。
4. 返回 exports.

#### 9.4.6.11 ModuleNamespaceCreate ( module, exports ) <div id="sec-modulenamespacecreate"></div>

带参数模块的抽象操作ModuleNamespaceCreate和export用于指定创建新模块名称空间的异类对象。它执行以下步骤：

1. 断言: module 是模块记录项.
2. 断言: module.[[Namespace]] 是 undefined.
3. 断言: exports 是String值的一个列表.
4. 令 M 为新创建的对象.
5. 设置 M 的基本内部方法为9.4.6中指定的定义
6. 设置 M.[[Module]] 为 module.
7. 令 sortedExports 为一个新列表，其包含与列表导出相同的值，列表中的值按顺序排列，就好像使用Array.prototype.sort使用undefined作为comparefn对相同值的Array进行了排序一样。
8. 设置 M.[[Exports]] 为 sortedExports.
9. 创建与26.3中的定义相对应的 M 的私有属性
10. 设置 module.[[Namespace]] 为 M.
11. 返回 M

### 9.4.7 不变原型异类对象 <div id="immutable-prototype-exotic-objects"></div>

不变的原型异类对象是具有[[Prototype]]内部插槽的异类对象，该内部插槽一旦初始化就不会更改。

不可变的原型异物具有与普通对象相同的内部插槽。它们仅在以下内部方法中才是异类的。下面未明确定义的不变原型奇特对象的所有其他内部方法改为在普通对象中定义。

#### 9.4.7.1 [[SetPrototypeOf]] ( V ) <div id="sec-immutable-prototype-exotic-objects-setprototypeof-v"></div>

当使用参数V调用不可变原型异类对象O的[[SetPrototypeOf]]内部方法时，将执行以下步骤：

1. 返回 ? SetImmutablePrototype(O, V)

#### 9.4.7.2 SetImmutablePrototype ( O, V ) <div id="sec-设置-immutable-prototype"></div>

当使用参数O和V调用SetImmutablePrototype抽象操作时，将执行以下步骤：

1. 断言: Either Type(V) 是 Object 或 Type(V) 是 Null.
2. 令 current 为 ? O.\[\[GetPrototypeOf]]().
3. 若 SameValue(V, current) 是 true, 返回 true.
4. 返回 false.

## 9.5 代理对象内部方法和内部插槽 <div id="proxy-object-internal-methods-and-internal-slots"></div>

代理对象是一个异类对象，其基本内部方法使用ECMAScript代码部分实现。每个代理对象都有一个名为[[ProxyHandler]]的内部插槽。[[ProxyHandler]]的值是一个对象，称为代理的处理程序对象，或者为null。处理程序对象的方法（请参见表30）可以用于增强代理对象的一个或多个内部方法的实现。每个代理对象还具有一个名为[[ProxyTarget]]的内部插槽，其值可以是对象或null值。该对象称为代理的目标对象。

| 内部方法              | 处理方法                   |
| --------------------- | -------------------------- |
| [[GetPrototypeOf]]    | `getPrototypeOf`           |
| [[SetPrototypeOf]]    | `setPrototypeOf`           |
| [[IsExtensible]]      | `isExtensible`             |
| [[PreventExtensions]] | `preventExtensions`        |
| [[GetOwnProperty]]    | `getOwnPropertyDescriptor` |
| [[HasProperty]]       | `has`                      |
| [[Get]]               | `get`                      |
| [[Set]]               | `set`                      |
| [[Delete]]            | `deleteProperty`           |
| [[DefineOwnProperty]] | `defineProperty`           |
| [[OwnPropertyKeys]]   | `ownKeys`                  |
| [[Call]]              | `apply`                    |
| [[Construct]]         | `construct`                |

当调用处理程序方法以提供代理对象内部方法的实现时，会将处理程序方法作为参数传递给代理的目标对象。代理的处理程序对象不一定具有对应于每个基本内部方法的方法。如果处理程序对象没有对应于内部陷阱的方法，则在代理上调用内部方法将导致在代理的目标对象上调用相应的内部方法。

代理对象的[[ProxyHandler]]和[[ProxyTarget]]内部插槽在创建对象时总是被初始化，通常不能修改。某些代理对象的创建方式允许它们随后被吊销。吊销代理后，其[[ProxyHandler]]和[[ProxyTarget]]内部插槽设置为null，从而导致对该代理对象的内部方法的后续调用引发TypeError异常。

由于代理对象允许通过任意ECMAScript代码提供内部方法的实现，因此可以定义其处理程序方法违反6.1.7.3中定义的不变量的代理对象。6.1.7.3中定义的一些内部方法不变式是必不可少的完整性不变式。这些不变量由本节中指定的代理对象内部方法明确实施。在存在所有可能的不变违规的情况下，ECMAScript实现必须具有鲁棒性。

在以下算法描述中，假设O是ECMAScript代理对象，P是属性键值，V是任何ECMAScript语言值，而Desc是属性描述符记录

### 9.5.1 [[GetPrototypeOf]] ( ) <div id="sec-proxy-object-internal-methods-and-internal-slots-getprototypeof"></div>

当调用代理异类对象O的[[GetPrototypeOf]]内部方法时，将执行以下步骤：

1. 令 handler 为 O.[[ProxyHandler]].
2. 若 handler 是 null, 抛出 TypeError 异常.
3. 断言: Type(handler) 是 Object.
4. 令 target 为 O.[[ProxyTarget]].
5. 令 trap 为 ? GetMethod(handler, "getPrototypeOf").
6. 若 trap 是 undefined, 那么
1. 返回 ? target.\[\[GetPrototypeOf]]().
7. 令 handlerProto 为 ? Call(trap, handler, « target »).
8. 若 Type(handlerProto) 不是 Object 或 Null, 抛出 TypeError 异常.
9. 令 extensibleTarget 为 ? IsExtensible(target).
10. 若 extensibleTarget 是 true, 返回 handlerProto.
11. 令 targetProto 为 ? target.\[\[GetPrototypeOf]]().
12. 若 SameValue(handlerProto, targetProto) 是 false, 抛出 TypeError 异常.
13. 返回 handlerProto

> 注：代理对象的[[GetPrototypeOf]]强制执行以下不变量：
>
> [[GetPrototypeOf]]的结果必须是Object或null。如果目标对象不可扩展，则应用于代理对象的[[GetPrototypeOf]]必须返回与应用于代理对象的目标对象的[[GetPrototypeOf]]相同的值。

### 9.5.2 [[SetPrototypeOf]] ( V ) <div id="sec-proxy-object-internal-methods-and-internal-slots-setprototypeof-v"></div>

当使用参数V调用代理异类对象O的[[SetPrototypeOf]]内部方法时，将执行以下步骤：

1. 断言: Type(V) 是 Object 或 Null.
2. 令 handler 为 O.[[ProxyHandler]].
3. 若 handler 是 null, 抛出 TypeError 异常.
4. 断言: Type(handler) 是 Object.
5. 令 target 为 O.[[ProxyTarget]].
6. 令 trap 为 ? GetMethod(handler, "setPrototypeOf").
7. 若 trap 是 undefined, 那么
1. 返回 ? target.\[\[SetPrototypeOf]](V).
8. 令 booleanTrapResult 为 ToBoolean(? Call(trap, handler, « target, V »)).
9. 若 booleanTrapResult 是 false, 返回 false.
10. 令 extensibleTarget 为 ? IsExtensible(target).
11. 若 extensibleTarget 是 true, 返回 true.
12. 令 targetProto 为 ? target.\[\[GetPrototypeOf]]().
13. 若 SameValue(V, targetProto) 是 false, 抛出 TypeError 异常.
14. 返回 true.

> 注：代理对象的[[SetPrototypeOf]]强制执行以下不变量：
>
> [[SetPrototypeOf]]的结果是一个布尔值。如果目标对象不可扩展，则参数值必须与应用于目标对象的[[GetPrototypeOf]]的结果相同。

### 9.5.3 [[IsExtensible]] ( ) <div id="sec-proxy-object-internal-methods-and-internal-slots-isextensible"></div>

当调用代理异类对象O的[[IsExtensible]]内部方法时，将执行以下步骤：

1. 令 handler 为 O.[[ProxyHandler]].
2. 若 handler 是 null, 抛出 TypeError 异常.
3. 断言: Type(handler) 是 Object.
4. 令 target 为 O.[[ProxyTarget]].
5. 令 trap 为 ? GetMethod(handler, "isExtensible").
6. 若 trap 是 undefined, 那么
a. 返回 ? target.\[\[IsExtensible]]().
7. 令 booleanTrapResult 为 ToBoolean(? Call(trap, handler, « target »)).
8. 令 targetResult 为 ? target.\[\[IsExtensible]]().
9. 若 SameValue(booleanTrapResult, targetResult) 是 false, 抛出 TypeError 异常.
10. 返回 booleanTrapResult.

> 注：代理对象的[[IsExtensible]]强制执行以下不变量：
>
> [[IsExtensible]]的结果是一个布尔值。应用于代理对象的[[IsExtensible]]必须返回与具有相同参数的应用于代理对象的目标对象的[[IsExtensible]]相同的值。

### 9.5.4 [[PreventExtensions]] ( ) <div id="sec-proxy-object-internal-methods-and-internal-slots-preventextensions"></div>

当调用代理异类对象O的[[PreventExtensions]]内部方法时，将执行以下步骤：

1. 令 handler 为 O.[[ProxyHandler]].
2. 若 handler 是 null, 抛出 TypeError 异常.
3. 断言: Type(handler) 是 Object.
4. 令 target 为 O.[[ProxyTarget]].
5. 令 trap 为 ? GetMethod(handler, "preventExtensions").
6. 若 trap 是 undefined, 那么
1. 返回 ? target.\[\[PreventExtensions]]().
7. 令 booleanTrapResult 为 ToBoolean(? Call(trap, handler, « target »)).
8. 若 booleanTrapResult 是 true, 那么
1. 令 targetIsExtensible 为 ? target.\[\[IsExtensible]]().
2. 若 targetIsExtensible 是 true, 抛出 TypeError 异常.
9. 返回 booleanTrapResult.

> 注：代理对象的[[PreventExtensions]]强制执行以下不变量：
>
> [[PreventExtensions]]的结果是一个布尔值。仅当应用于代理对象目标对象的[[IsExtensible]]为false时，应用于代理对象的[[PreventExtensions]]才返回true。

### 9.5.5 [[GetOwnProperty]] ( P ) <div id="sec-proxy-object-internal-methods-and-internal-slots-getownproperty-p"></div>

当使用属性键P调用Proxy异类对象O的[[GetOwnProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 handler 为 O.[[ProxyHandler]].
3. 若 handler 是 null, 抛出 TypeError 异常.
4. 断言: Type(handler) 是 Object.
5. 令 target 为 O.[[ProxyTarget]].
6. 令 trap 为 ? GetMethod(handler, "getOwnPropertyDescriptor").
7. 若 trap 是 undefined, 那么
1. 返回 ? target.\[\[GetOwnProperty]](P).
8. 令 trapResultObj 为 ? Call(trap, handler, « target, P »).
9. 若 Type(trapResultObj) 不是 Object 或 Undefined, 抛出 TypeError 异常.
10. 令 targetDesc 为 ? target.\[\[GetOwnProperty]](P).
11. 若 trapResultObj 是 undefined, 那么
    1. 若 targetDesc 是 undefined, 返回 undefined.
    2. 若 targetDesc.[[Configurable]] 是 false, 抛出 TypeError 异常.
    3. 令 extensibleTarget 为 ? IsExtensible(target).
    4. 若 extensibleTarget 是 false, 抛出 TypeError 异常.
    5. 返回 undefined.
12. 令 extensibleTarget 为 ? IsExtensible(target).
13. 令 resultDesc 为 ? ToPropertyDescriptor(trapResultObj).
14. 调用 CompletePropertyDescriptor(resultDesc).
15. 令 valid 为 IsCompatiblePropertyDescriptor(extensibleTarget, resultDesc, targetDesc).
16. 若 valid 是 false, 抛出 TypeError 异常.
17. 若 resultDesc.[[Configurable]] 是 false, 那么
1. 若 targetDesc 是 undefined 或 targetDesc.[[Configurable]] 是 true, 那么
   1. 抛出 TypeError 异常.
18. 返回 resultDesc.

> 注：代理对象的[[GetOwnProperty]]强制执行以下不变量：
>
> [[GetOwnProperty]]的结果必须是Object或undefined。
>
> 如果属性作为目标对象的不可配置的自身属性存在，则不能将其报告为不存在。
>
> 如果属性作为目标对象的自身属性存在并且目标对象不可扩展，则不能将其报告为不存在。
>
> 如果属性不作为目标对象的自身属性存在并且目标对象不可扩展，则不能将其报告为存在。
>
> 如果属性不作为目标对象的自身属性存在，或者作为目标对象的可配置自身属性存在，则不能将其报告为不可配置。

### 9.5.6 [[DefineOwnProperty]] ( P, Desc ) <div id="sec-proxy-object-internal-methods-and-internal-slots-defineownproperty-p-desc"></div>

当使用属性键P和属性描述符Desc调用代理异类对象O的[[DefineOwnProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 handler 为 O.[[ProxyHandler]].
3. 若 handler 是 null, 抛出 TypeError 异常.
4. 断言: Type(handler) 是 Object.
5. 令 target 为 O.[[ProxyTarget]].
6. 令 trap 为 ? GetMethod(handler, "defineProperty").
7. 若 trap 是 undefined, 那么
  1. 返回 ? target.\[\[DefineOwnProperty]](P, Desc).
8. 令 descObj 为 FromPropertyDescriptor(Desc).
9. 令 booleanTrapResult 为 ToBoolean(? Call(trap, handler, « target, P, descObj »)).
10. 若 booleanTrapResult 是 false, 返回 false.
11. 令 targetDesc 为 ? target.\[\[GetOwnProperty]](P).
12. 令 extensibleTarget 为 ? IsExtensible(target).
13. 若 Desc 有 [[Configurable]] 字段 and 若 Desc.[[Configurable]] 是 false, 那么
    1. 令 settingConfigFalse 为 true.
14. 否则, 令 settingConfigFalse 为 false.
15. 若 targetDesc 是 undefined, 那么
    1. 若 extensibleTarget 是 false, 抛出 TypeError 异常.
    2. 若 settingConfigFalse 是 true, 抛出 TypeError 异常.
16. 否则 targetDesc 不是 undefined,
    1. 若 IsCompatiblePropertyDescriptor(extensibleTarget, Desc, targetDesc) 是 false, 抛出 TypeError
       异常.
    2. 若 settingConfigFalse 是 true and targetDesc.[[Configurable]] 是 true, 抛出 TypeError 异常.
17. 返回 true.

> 注：代理对象的[[DefineOwnProperty]]强制执行以下不变量：
>
> [[DefineOwnProperty]]的结果是一个布尔值。
>
> 如果目标对象不可扩展，则无法添加属性。
>
> 除非目标对象存在相应的不可配置的自身属性，否则属性不能不可配置。
>
> 如果属性具有对应的目标对象属性，则使用[[DefineOwnProperty]]将属性的属性描述符应用于目标对象将不会引发异常。

### 9.5.7 [[HasProperty]] ( P ) <div id="sec-proxy-object-internal-methods-and-internal-slots-hasproperty-p"></div>

当使用属性键P调用Proxy异类对象O的[[HasProperty]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 handler 为 O.[[ProxyHandler]].
3. 若 handler 是 null, 抛出 TypeError 异常.
4. 断言: Type(handler) 是 Object.
5. 令 target 为 O.[[ProxyTarget]].
6. 令 trap 为 ? GetMethod(handler, "has").
7. 若 trap 是 undefined, 那么
   1. 返回 ? target.\[\[HasProperty]](P) 

8. 令 booleanTrapResult 为 ToBoolean(? Call(trap, handler, « target, P »)).

9. 若 booleanTrapResult 是 false, 那么
  1. 令 targetDesc 为 ? target.\[\[GetOwnProperty]](P).
  2. 若 targetDesc 不是 undefined, 那么
     1. 若 targetDesc.[[Configurable]] 是 false, 抛出 TypeError 异常.
     2. 令 extensibleTarget 为 ? IsExtensible(target).
     3. 若 extensibleTarget 是 false, 抛出 TypeError 异常.
10. 返回 booleanTrapResult.

> 注：代理对象的[[HasProperty]]强制执行以下不变量：
>
> [[HasProperty]]的结果是一个布尔值。
>
> 如果属性作为目标对象的不可配置的自身属性存在，则不能将其报告为不存在。
>
> 如果属性作为目标对象的自身属性存在并且目标对象不可扩展，则不能将其报告为不存在。

### 9.5.8 [[Get]] ( P, Receiver ) <div id="sec-proxy-object-internal-methods-and-internal-slots-get-p-receiver"></div>

使用属性键P和ECMAScript语言值Receiver调用代理异类对象O的[[Get]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 handler 为 O.[[ProxyHandler]].
3. 若 handler 是 null, 抛出 TypeError 异常.
4. 断言: Type(handler) 是 Object.
5. 令 target 为 O.[[ProxyTarget]].
6. 令 trap 为 ? GetMethod(handler, "get").
7. 若 trap 是 undefined, 那么
1. 返回 ? target.\[\[Get]](P, Receiver).
8. 令 trapResult 为 ? Call(trap, handler, « target, P, Receiver »).
9. 令 targetDesc 为 ? target.\[\[GetOwnProperty]](P).
10. 若 targetDesc 不是 undefined 并且 targetDesc.[[Configurable]] 是 false, 那么
    1. 若 IsDataDescriptor(targetDesc) 是 true 并且 targetDesc.[[Writable]] 是 false, 那么
       1. 若 SameValue(trapResult, targetDesc.[[Value]]) 是 false, 抛出 TypeError 异常.
    2. 若 IsAccessorDescriptor(targetDesc) 是 true 并且 targetDesc.[[Get]] 是 undefined, 那么
      1. 若 trapResult 不是 undefined, 抛出 TypeError 异常.
11. 返回 trapResult.

> 注：代理对象的[[Get]]强制执行以下不变量：
>
> 如果目标对象属性是不可写的，不可配置的自身数据属性，则为属性报告的值必须与相应目标对象属性的值相同。如果相应的目标对象属性是不可定义的自己的访问器属性，并且其[[Get]]属性未定义，则该属性的报告值必须是未定义的。

### 9.5.9 [[Set]] ( P, V, Receiver ) <div id="sec-proxy-object-internal-methods-and-internal-slots-设置-p-v-receiver"></div>

使用属性键P，值V和ECMAScript语言值Receiver调用代理异类对象O的[[Set]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 handler 为 O.[[ProxyHandler]].
3. 若 handler 是 null, 抛出 TypeError 异常.
4. 断言: Type(handler) 是 Object.
5. 令 target 为 O.[[ProxyTarget]].
6. 令 trap 为 ? GetMethod(handler, "设置").
7. 若 trap 是 undefined, 那么
1. 返回 ? target.\[\[Set]](P, V, Receiver).
8. 令 booleanTrapResult 为 ToBoolean(? Call(trap, handler, « target, P, V, Receiver »)).
9. 若 booleanTrapResult 是 false, 返回 false.
10. 令 targetDesc 为 ? target.\[\[GetOwnProperty]](P).
11. 若 targetDesc 不是 undefined 并且 targetDesc.[[Configurable]] 是 false, 那么
    1. 若 IsDataDescriptor(targetDesc) 是 true 并且 targetDesc.[[Writable]] 是 false, 那么
       1. 若 SameValue(V, targetDesc.[[Value]]) 是 false, 抛出 TypeError 异常.
    2. 若 IsAccessorDescriptor(targetDesc) 是 true, 那么
      1. 若 targetDesc.[[Set]] 是 undefined, 抛出 TypeError 异常.
12. 返回 true

> 注：代理对象的[[Set]]强制执行以下不变量：
>
> [[Set]]的结果是布尔值。如果相应的目标对象属性是不可写的，不可配置的自身数据属性，则无法将属性的值更改为与相应目标对象属性的值不同的值。如果相应的目标对象属性是其[[Set]]属性未定义的不可配置的自身访问器属性，则无法设置属性的值。

### 9.5.10 [[Delete]] ( P ) <div id="sec-proxy-object-internal-methods-and-internal-slots-delete-p"></div>

当使用属性键P调用Proxy异类对象O的[[Delete]]内部方法时，将执行以下步骤：

1. 断言: IsPropertyKey(P) 是 true.
2. 令 handler 为 O.[[ProxyHandler]].
3. 若 handler 是 null, 抛出 TypeError 异常.
4. 断言: Type(handler) 是 Object.
5. 令 target 为 O.[[ProxyTarget]].
6. 令 trap 为 ? GetMethod(handler, "deleteProperty").
7. 若 trap 是 undefined, 那么
1. 返回 ? target.\[\[Delete]](P)

8. 令 booleanTrapResult 为 ToBoolean(? Call(trap, handler, « target, P »)).
9. 若 booleanTrapResult 是 false, 返回 false.
10. 令 targetDesc 为 ? target.\[\[GetOwnProperty]](P).
11. 若 targetDesc 是 undefined, 返回 true.
12. 若 targetDesc.[[Configurable]] 是 false, 抛出 TypeError 异常.
13. 返回 true.

> 注：代理对象的[[Delete]]强制执行以下不变量：
>
> [[Delete]]的结果是一个布尔值。
>
> 如果属性作为目标对象的不可配置的自身属性存在，则不能将其报告为已删除

### 9.5.11 [[OwnPropertyKeys]] ( ) <div id="sec-proxy-object-internal-methods-and-internal-slots-ownpropertykeys"></div>

当调用代理异类对象O的[[OwnPropertyKeys]]内部方法时，将执行以下步骤：

1. 令 handler 为 O.[[ProxyHandler]].

2. 若 handler 是 null, 抛出 TypeError 异常.

3. 断言: Type(handler) 是 Object.

4. 令 target 为 O.[[ProxyTarget]].

5. 令 trap 为 ? GetMethod(handler, "ownKeys").

6. 若 trap 是 undefined, 那么

  1. 返回 ? target.\[\[OwnPropertyKeys]]().

7. 令 trapResultArray 为 ? Call(trap, handler, « target »).

8. 令 trapResult 为 ? CreateListFromArrayLike(trapResultArray, « String, Symbol »).

9. 若 trapResult  包含任何重复的条目, 抛出 TypeError 异常.

10. 令 extensibleTarget 为 ? IsExtensible(target).

11. 令 targetKeys 为 ? target.\[\[OwnPropertyKeys]]().

12. 断言: targetKeys 是一个仅包含 String 和 Symbol 的列表。

13. 断言: targetKeys 包含不重复的条目.

14. 令 targetConfigurableKeys 为一个新的空列表.

15. 令 targetNonconfigurableKeys 为一个新的空列表.

16. 对于targetKeys的每个key元素, 执行

    1. 令 desc 为 ? target.\[\[GetOwnProperty]](key).
    2. 若 desc 不是 undefined 同时 desc.[[Configurable]] 是 false, 那么
      1. 添加 key，作为 targetNonconfigurableKeys 的一个元素.
    3. 否则,
      1. 添加 key，作为 targetConfigurableKeys 的一个元素

17. 若 extensibleTarget 是 true 并且 targetNonconfigurableKeys 是 empty, 那么

    1. 返回 trapResult.

18. 令 uncheckedResultKeys 为新列表，它是 trapResult 的副本

19. 对于 targetNonconfigurableKeys 的每一个元素 key, 执行

    1. 若 key 不是 uncheckedResultKeys 的一个元素, 抛出 TypeError 异常.
    2. 从 uncheckedResultKeys 中移除key.

20. 若 extensibleTarget 是 true, 返回 trapResult.

21. 对于 targetConfigurableKeys 中的每一个元素key, 执行

    1. 若 key 不是 uncheckedResultKeys 中的一个元素, 抛出 TypeError 异常.

    2. 从 uncheckedResultKeys 中移除key.

22. 若 uncheckedResultKeys 不是 empty, 抛出 TypeError 异常.

23. 返回 trapResult.

> 注：代理对象的[[OwnPropertyKeys]]强制执行以下不变量：
>
> [[OwnPropertyKeys]]的结果是一个列表。
>
> 返回的列表不包含重复的条目。
>
> 每个结果列表元素的类型是String或Symbol。
>
> 结果列表必须包含目标对象所有不可配置的自身属性的键。
>
> 如果目标对象不可扩展，则结果列表必须包含目标对象自身属性的所有键，并且不包含其他值。

### 9.5.12 [[Call]] ( thisArgument, argumentsList ) <div id="sec-proxy-object-internal-methods-and-internal-slots-call-thisargument-argumentslist"></div>

代理异类对象O的[[Call]]内部方法使用参数thisArgument和argumentsList（ECMAScript语言值列表）来调用。采取以下步骤：

1. 令 handler 为 O.[[ProxyHandler]].
2. 若 handler 是 null, 抛出 TypeError 异常.
3. 断言: Type(handler) 是 Object.
4. 令 target 为 O.[[ProxyTarget]].
5. 令 trap 为 ? GetMethod(handler, "apply").
6. 若 trap 是 undefined, 那么
1. 返回 ? Call(target, thisArgument, argumentsList).
7. 令 argArray 为 CreateArrayFromList(argumentsList).
8. 返回 ? Call(trap, handler, « target, thisArgument, argArray »).

> 注：如果Proxy异类对象的[[ProxyTarget]]内部插槽的初始值是具有[[Call]]内部方法的对象，则仅具有[[Call]]内部方法。

### 9.5.13 [[Construct]] ( argumentsList, newTarget ) <div id="sec-proxy-object-internal-methods-and-internal-slots-construct-argumentslist-newtarget"></div>

代理异类对象O的[[Construct]]内部方法用参数argumentsList调用，该参数可能是ECMAScript语言值和newTarget的可能为空的列表。采取以下步骤：

1. 令 handler 为 O.[[ProxyHandler]].
2. 若 handler 是 null, 抛出 TypeError 异常.
3. 断言: Type(handler) 是 Object.
4. 令 target 为 O.[[ProxyTarget]].
5. 断言: IsConstructor(target) 是 true.
6. 令 trap 为 ? GetMethod(handler, "construct").
7. 若 trap 是 undefined, 那么
1. 返回 ? Construct(target, argumentsList, newTarget).

8. 令 argArray 为 CreateArrayFromList(argumentsList).
9. 令 newObj 为 ? Call(trap, handler, « target, argArray, newTarget »).
10. 若 Type(newObj) 不是 Object, 抛出 TypeError 异常.
11. 返回 newObj.

> 注1：如果代理异类对象的[[ProxyTarget]]内部插槽的初始值是具有[[Construct]]内部方法的对象，则该对象仅具有[[Construct]]内部方法。
>
> 注2：代理对象的[[Construct]]强制执行以下不变量：
>
> ​	[[Construct]]的结果必须是一个Object。

### 9.5.14 ProxyCreate ( target, handler ) <div id="sec-proxycreate"></div>

具有参数target和handler的抽象操作ProxyCreate用于指定创建新的Proxy异类对象。它执行以下步骤：

1. 若 Type(target) 不是 Object, 抛出 TypeError 异常.
2. 若 target 是代理异类对象，并且 target.[[ProxyHandler]] 是 null, 抛出 TypeError 异常.
3. 若 Type(handler) 不是 Object, 抛出 TypeError 异常.
4. 若 handler 是代理异类对象，并且 handler.[[ProxyHandler]] 是 null, 抛出 TypeError 异常.
5. 令 P 为新创建的对象.
6. 设置 P的基本内部方法（[[Call]]和[[Construct]]除外）为9.5中指定的定义
7. 若 IsCallable(target) 是 true, 那么
  1. 设置 P.[[Call]] 按照 9.5.12 的规范.
  2. 若 IsConstructor(target) 是 true, 那么
    1. 设置 P.[[Construct]] 按照 9.5.13 的规范.
8. 设置 P.[[ProxyTarget]] 为 target.
9. 设置 P.[[ProxyHandler]] 为 handler.
10. 返回 P.

```
description 所有普通对象都有一个称为[[Prototype]]的内部插槽。此内部插槽的值可以为null或一个对象，用于实现继承。[[Prototype]]对象的数据属性被继承（作为子对象的属性可见），是为了取值权限，而不是赋值权限。访问器属性被继承，同时获得取值权限和赋值权限。
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,普通和异类对象行为,ordinary-and-exotic-object-behaviours
```