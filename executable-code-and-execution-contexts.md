## 8 可执行代码和可执行上下文
## 8.1 词法环境 <div id="sec-lexical-environments.md"></div>

词法环境是一个用于定义特定变量和函数标识符在 ECMAScript 代码的词法嵌套结构上关联关系的规范类型。一个词法环境由一个环境记录项和可能为空的外部词法环境引用构成。通常词法环境会与 ECMAScript 代码诸如 FunctionDeclaration、WithStatement 或者 TryStatement 的 Catch 块这样的特定句法结构相联系，且类似代码每次执行都会有一个新的词法环境被创建出来。

环境记录记录了在其关联词法环境范围内创建的标识符绑定。它被称为词法环境的EnvironmentRecord。

外部词法环境引用用于表示词法环境的逻辑嵌套关系模型。（内部）词法环境的外部引用是逻辑上包含内部词法环境的词法环境。外部词法环境当然可以具有自己的外部词法环境。词法环境可以用作多个内部词法环境的外部环境。例如，如果一个 FunctionDeclaration 包含两个嵌套的 FunctionDeclaration，那么每个内嵌函数的词法环境都是外部函数本次执行所产生的词法环境。

全局环境是没有外部环境的词法环境。全局环境的外部环境引用为null。全局环境的EnvironmentRecord可能预填充了标识符绑定，并包括一个关联的全局对象，该对象的属性提供了某些全局环境的标识符绑定。在执行ECMAScript代码时，可以将其他属性添加到全局对象，并且可以修改初始属性。

模块环境是一个词法环境，其中包含模块顶级声明的绑定。它还包含模块显式导入的绑定。模块环境的外部环境是全局环境。

函数环境是一个词法环境，它与ECMAScript函数对象的调用相对应。一个function环境可以建立一个新的this绑定。函数环境还捕获支持super方法调用所需的状态。

词法环境和环境记录的值纯粹是规范机制，不需要与任何具体的ECMAScript实现相对应。ECMAScript程序无法直接访问或操纵这些值。

### 8.1.1 环境记录 <div id="sec-environment-records"></div>

在本标准中，共有两类环境记录项：声明式环境记录项和对象式环境记录项。声明式环境记录项用于定义那些将标识符与语言值直接绑定的 ECMA 脚本语法元素，例如 FunctionDeclaration、VariableDeclaration 以及 Catch 语句。对象式环境记录项用于定义那些将标识符与具体对象的属性绑定的 ECMA 脚本元素，例如 WithStatement 表达式。全局环境记录和函数环境记录是专门用于Script全局声明和函数内顶级声明的规范化。

出于规范目的，环境记录值是记录(Record)规范类型的值，可以认为是存在于简单的面向对象的层次结构中，其中环境记录是具有三个具体子类的抽象类，即声明性环境记录，对象环境记录和全局环境记录。功能环境记录和模块环境记录是声明性环境记录的子类。抽象类包括表14中定义的抽象规范方法。这些抽象方法针对每个具体子类具有不同的具体算法。

| 方法                         | 作用                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| HasBinding(N)                | 确定环境记录是否具有字符串 N 的绑定。 如果没有，请返回 true ，否则为 false |
| CreateMutableBinding(N, D)   | 在环境记录中创建一个新的但未初始化的可变绑定。 字符串 N 是绑定名称的文本。 如果布尔参数 D 是 true ，则绑定可能随后被删除. |
| CreateImmutableBinding(N, S) | 在环境记录中创建一个新的但未初始化的不可变绑定。 字符串 N 是绑定名称的文本。 如果 S 是 true ，那么无论严格模式是否设置，对于该绑定，在初始化之前访问绑定的值，或者在初始化之后对其进行设置将引发异常. |
| InitializeBinding(N, V)      | 在环境记录中设置已存在但未初始化的绑定的值。 字符串 N 是绑定名称的文本。 V 是绑定的值，是任何ECMAScript语言类型 . |
| SetMutableBinding(N, V, S)   | 在环境记录中设置已经存在的可变绑定的值。 字符串 N 是绑定名称的文本。 V 是绑定的值，可以是任何ECMAScript语言类型 。 S 是一个布尔标志。 如果 S 是 true ，并且绑定不能设置，则抛出一个 TypeError 异常. |
| GetBindingValue(N, S)        | 从环境记录中返回已经存在的绑定的值。 字符串 N 是绑定名称的文本。 S 用于标识源自严格模式代码(strict mode code)，否则需要严格的模式引用语义。 如果 S 是 true ，绑定不存在则引发一个 ReferenceError 异常。 如果绑定存在但未初始化，不管 S 的值，都会抛出 ReferenceError |
| DeleteBinding(N)             | 从环境记录中删除绑定。 字符串 N 是绑定名称的文本。 如果存在 N 的绑定，则删除绑定并返回 true 。 如果绑定存在但不能被删除返回 false 。 如果绑定不存在返回 true . |
| HasThisBinding()             | 确定环境记录是否建立了一个`this`绑定。 如果是，则返回true；否则，则返回false |
| HasSuperBinding()            | 确定环境记录是否建立了一个`super`方法绑定。 如果是，则返回true；否则，则返回false |
| WithBaseObject()             | 如果此环境记录与`with`语句相关联，则返回with对象。 否则返回 undefined . |

#### 8.1.1.1 声明环境记录 <div id="sec-declarative-environment-records"></div>

每个声明性环境记录都与包含变量，常量，let，class, module, import，与或函数声明的ECMAScript程序范围相关联。声明式环境记录项用于绑定作用域内定义的一系列标识符。

声明性环境记录的具体规范方法的行为由以下算法定义。

##### 8.1.1.1.1 HasBinding ( N ) <div id="sec-declarative-environment-records-hasbinding-n"></div>

声明式环境记录项的 HasBinding 具体方法用于简单地判断作为参数的标识符是否是当前对象绑定的标识符之一：

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 如果 envRec 有一个名称为 N 的绑定，返回 true。
3. 如果没有该绑定，返回 false。

##### 8.1.1.1.2 CreateMutableBinding ( N, D ) <div id="sec-declarative-environment-records-createmutablebinding-n-d"></div>

声明式环境记录项的 CreateMutableBinding 具体方法会创建一个名称为 N 的绑定，并初始化其值为 undefined。方法调用时，当前环境记录项中不能存在 N 的绑定。如果调用时提供了布尔类型的参数 D 且其值为 true，则新建的绑定被标记为可删除。

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 断言：envRec 没有 N 的绑定。
3. 在 envRec 中为 N 创建一个可变绑定，并将绑定的值设置为 **undefined**。如果 D 为 **true** 则新创建的绑定可在后续操作中通过调用 DeleteBinding 删除。
4. 返回 NormalCompletion(empty) 

##### 8.1.1.1.3 CreateImmutableBinding ( N, S ) <div id="sec-declarative-environment-records-createimmutablebinding-n-s"></div>

声明性环境记录的具体环境记录方法CreateImmutableBinding为未初始化的名称N创建一个新的不可变绑定。N的此环境记录中必须没有绑定。如果布尔参数S的值为true，则将新绑定标记为严格绑定。

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 断言：envRec 不存在 N 的绑定。
3. 在 envRec 中为 N 创建一个不可变绑定，并记录为未初始化。如果S为true，则记录新创建的绑定是严格绑定
4. 返回 NormalCompletion(empty)

##### 8.1.1.1.4 InitializeBinding ( N, V ) <div id="sec-declarative-environment-records-initializebinding-n-v"></div>

声明性环境记录的具体环境记录方法InitializeBinding用于将名称为参数N的值的标识符的当前绑定的绑定值设置为参数V的值。N的未初始化绑定必须已经存在。

1. Let envRec be the declarative Environment Record for which the method was invoked.
2. Assert: envRec must have an uninitialized binding for N.
3. Set the bound value for N in envRec to V.
4. Record that the binding for N in envRec has been initialized.
5. Return NormalCompletion(empty).

##### 8.1.1.1.5 SetMutableBinding ( N, V, S ) <div id="sec-declarative-environment-records-setmutablebinding-n-v-s"></div>

用于声明性环境记录的具体环境记录方法SetMutableBinding试图将名称为参数N的值的标识符的当前绑定的绑定值更改为参数V的值。N的绑定通常已经存在，但很少见情况可能并非如此。如果绑定是不可变的绑定，则在S为true时引发TypeError。

1. Let envRec be the declarative Environment Record for which the method was invoked.
2. If envRec does not have a binding for N, then
a. If S is true, throw a ReferenceError exception.
b. Perform envRec.CreateMutableBinding(N, true).
c. Perform envRec.InitializeBinding(N, V).
d. Return NormalCompletion(empty).
3. If the binding for N in envRec is a strict binding, set S to true.
4. If the binding for N in envRec has not yet been initialized, throw a ReferenceError exception.
5. Else if the binding for N in envRec is a mutable binding, change its bound value to V.
6. Else,
a. Assert: This is an attempt to change the value of an immutable binding.
b. If S is true, throw a TypeError exception.
7. Return NormalCompletion(empty).

> 注：导致步骤2缺少绑定的ECMAScript代码示例为：
>
>  function f(){eval("var x; x = (delete x, 0);")}  

##### 8.1.1.1.6 GetBindingValue ( N, S ) <div id="sec-declarative-environment-records-getbindingvalue-n-s"></div>

声明性环境记录的具体环境记录方法GetBindingValue只是返回其绑定标识符的值，该标识符的名称是参数N的值。如果绑定存在但未初始化，则无论S的值如何，都会引发ReferenceError。

1. Let envRec be the declarative Environment Record for which the method was invoked.
2. Assert: envRec has a binding for N.
3. If the binding for N in envRec is an uninitialized binding, throw a ReferenceError exception.
4. Return the value currently bound to N in envRec.

##### 8.1.1.1.7 DeleteBinding ( N ) <div id="sec-declarative-environment-records-deletebinding-n"></div>

声明性环境记录的具体环境记录方法DeleteBinding只能删除已明确指定要删除的绑定。

1. Let envRec be the declarative Environment Record for which the method was invoked.
2. Assert: envRec has a binding for the name that is the value of N.
3. If the binding for N in envRec cannot be deleted, return false.
4. Remove the binding for N from envRec.
5. Return true.

##### 8.1.1.1.8 HasThisBinding ( ) <div id="sec-declarative-environment-records-hasthisbinding"></div>

常规的声明性环境记录不提供this绑定。

1. Return false.  

##### 8.1.1.1.9 HasSuperBinding ( ) <div id="sec-declarative-environment-records-hassuperbinding"></div>

常规的声明性环境记录不提供super绑定。

1. Return false.

##### 8.1.1.1.10 WithBaseObject ( ) <div id="sec-declarative-environment-records-withbaseobject"></div>

声明性环境记录始终返回未定义的WithBaseObject。

1. Return undefined.  

#### 8.1.1.2 对象环境记录 <div id="sec-object-environment-records"></div>

每个对象环境记录都与一个称为其绑定对象的对象相关联。对象环境记录绑定直接与其绑定对象的属性名称相对应的一组字符串标识符名称。不是以IdentifierName形式出现的字符串的属性键不包含在绑定标识符集中。不论其[[Enumerable]]属性的设置如何，自己的属性和继承的属性都包括在该集合中。因为可以动态地从对象中添加和删除属性，所以对象环境记录所绑定的标识符集可能会因添加或删除属性的任何操作的副作用而发生更改。由于这种副作用而创建的任何绑定都被视为可变绑定，即使相应属性的Writable属性的值为false。对象环境记录不存在不可变的绑定。

为with语句（13.11）创建的对象环境记录可以提供其绑定对象作为此值的隐式供函数调用使用。该功能由与每个对象环境记录关联的withEnvironment布尔值控制。默认情况下，任何对象环境记录的withEnvironment的值为false。

对象环境记录的具体规范方法的行为由以下算法定义。

##### 8.1.1.2.1 HasBinding ( N ) <div id="sec-object-environment-records-hasbinding-n"></div>

对象Environment Records的具体Environment Record方法HasBinding确定其关联的绑定对象是否具有名称为参数N的值的属性：

1. Let envRec be the object Environment Record for which the method was invoked.
2. Let bindings be the binding object for envRec.
3. Let foundBinding be ? HasProperty(bindings, N).
4. If foundBinding is false, return false.
5. If the withEnvironment flag of envRec is false, return true.
6. Let unscopables be ? Get(bindings, @@unscopables).
7. If Type(unscopables) is Object, then
a. Let blocked be ToBoolean(? Get(unscopables, N)).
b. If blocked is true, return false.
8. Return true

##### 8.1.1.1.2 CreateMutableBinding ( N, D ) <div id="sec-declarative-environment-records-createmutablebinding-n-d"></div>

对象Environment Records的具体Environment Record方法CreateMutableBinding在Environment Record的关联绑定对象中创建一个名称为String值的属性，并将其初始化为未定义的值。如果布尔参数D的值为true，则将新属性的[[Configurable]]属性设置为true；否则，将属性设置为true。否则将其设置为false。

1. Let envRec be the object Environment Record for which the method was invoked.
2. Let bindings be the binding object for envRec.
3. Return ? DefinePropertyOrThrow(bindings, N, PropertyDescriptor { [[Value]]: undefined, [[Writable]]: true,
[[Enumerable]]: true, [[Configurable]]: D }).

> 注：通常，envRec没有N的绑定，但是如果绑定N，则DefinePropertyOrThrow的语义可能会导致现有绑定被替换或隐藏或导致突然完成返回。

##### 8.1.1.2.3 CreateImmutableBinding ( N, S ) <div id="sec-object-environment-records-createimmutablebinding-n-s"></div>

具体的环境记录方法CreateImmutableBinding从未在此规范中与对象环境记录关联使用。

##### 8.1.1.2.4 InitializeBinding ( N, V ) <div id="sec-object-environment-records-initializebinding-n-v"></div>

对象环境记录的具体环境记录方法InitializeBinding用于将名称为参数N的值的标识符的当前绑定的绑定值设置为参数V的值。N的未初始化绑定必须已经存在。

1. Let envRec be the object Environment Record for which the method was invoked.
2. Assert: envRec must have an uninitialized binding for N.
3. Record that the binding for N in envRec has been initialized.
4. Return ? envRec.SetMutableBinding(N, V, false).

> 注：在此规范中，对对象环境记录的CreateMutableBinding的所有使用都将紧随其后的是对具有相同名称的InitializeBinding的调用。因此，实现无需显式跟踪单个对象环境记录绑定的初始化状态。

##### 8.1.1.2.5 SetMutableBinding ( N, V, S ) <div id="sec-object-environment-records-setmutablebinding-n-v-s"></div>

对象Environment Records的具体Environment Record方法SetMutableBinding尝试将名称为参数N的值的环境记录的关联绑定对象的属性的值设置为参数V的值。通常已经存在一个名为N的属性，但是如果存在不是或当前不可写，错误处理由布尔参数S的值确定。

1. Let envRec be the object Environment Record for which the method was invoked.
2. Let bindings be the binding object for envRec.
3. Return ? Set(bindings, N, V, S).

##### 8.1.1.2.6 GetBindingValue ( N, S ) <div id="sec-object-environment-records-getbindingvalue-n-s"></div>

对象Environment Records的具体Environment Record方法GetBindingValue返回其关联的绑定对象的属性的值，该属性的名称为参数标识符N的String值。该属性应该已经存在，但如果不存在，则结果取决于S的值参数：

1. Let envRec be the object Environment Record for which the method was invoked.
2. Let bindings be the binding object for envRec.
3. Let value be ? HasProperty(bindings, N).
4. If value is false, then
a. If S is false, return the value undefined; otherwise throw a ReferenceError exception.
5. Return ? Get(bindings, N).

##### 8.1.1.2.7 DeleteBinding ( N ) <div id="sec-object-environment-records-deletebinding-n"></div>

对象环境记录的具体环境记录方法DeleteBinding只能删除与[[Configurable]]属性值为true的环境对象的属性对应的绑定。

1. Let envRec be the object Environment Record for which the method was invoked.
2. Let bindings be the binding object for envRec.
3. Return ? bindings.\[\[Delete]](N).

##### 8.1.1.2.8 HasThisBinding ( ) <div id="sec-object-environment-records-hasthisbinding"></div>

常规对象环境记录不提供this绑定。

1. Return false.

##### 8.1.1.2.9 HasSuperBinding ( ) <div id="sec-object-environment-records-hassuperbinding"></div>

常规对象环境记录不提供super绑定。

1. Return false.

##### 8.1.1.2.10 WithBaseObject ( ) <div id="sec-object-environment-records-withbaseobject"></div>

除非其withEnvironment标志为true，否则对象环境记录将返回undefined作为其WithBaseObject。

1. Let envRec be the object Environment Record for which the method was invoked.
2. If the withEnvironment flag of envRec is true, return the binding object for envRec.
3. Otherwise, return undefined.

#### 8.1.1.3 函数环境记录 <div id="function-enviroment-record"></div>

函数环境记录是声明性环境记录，用于表示函数的顶级范围，如果函数不是ArrowFunction，则提供此绑定。如果一个函数不是ArrowFunction函数，并且引用了super，则其函数Environment Record也包含用于从函数内部执行super方法调用的状态。

功能环境记录具有表15中列出的其他状态字段。

| 字段名                | 值                                                | 含义                                                         |
| --------------------- | ------------------------------------------------- | ------------------------------------------------------------ |
| [[ThisValue]]         | Any                                               | 这是用于此函数调用的this值.                                  |
| [[ThisBindingStatus]] | `"lexical"` | `"initialized"` | `"uninitialized"` | 如果值为`"lexical"`，则这是ArrowFunction，并且没有本地this值。 |
| [[FunctionObject]]    | Object                                            | 函数对象被调用导致这个Environment Record 被创建。            |
| [[HomeObject]]        | Object \| undefined                               | 如果相关函数具有`super`属性访问，并且不是ArrowFunction，[[HomeObject ]]是函数绑定到一个方法的对象。 [[HomeObject]]的默认值为 undefined 。 |
| [[NewTarget]]         | Object \| undefined                               | 如果这个Environment Record 是由[[Construct] ]内部方法创建，[[NewTarget]]是[[Construct]] newTarget 参数的值。 否则，其值为 undefined 。 |

函数环境记录支持表14中列出的所有声明性环境记录方法，并且对所有这些方法共享相同的规范，但HasThisBinding和HasSuperBinding除外。另外，功能Environment Records支持表16中列出的方法：

| 方法             | 目的                                                         |
| ---------------- | ------------------------------------------------------------ |
| BindThisValue(V) | 设置[[ThisValue]]并记录它已被初始化。                        |
| GetThisBinding() | 返回此Environment Record`this绑定。 如果this绑定尚未初始化，则抛出 ReferenceError 。                            ` |
| GetSuperBase()   | 返回在Environment Record中作为` super `属性访问绑定的基础的对象。该对象源于此Environment Record的[[ HomeObject]]字段。 值 undefined 表示`super`属性访问将产生运行时错误. |

功能环境记录的其他具体规范方法的行为由以下算法定义：

##### 8.1.1.3.1 BindThisValue ( V ) <div id="sec-function-environment-records-bindthisvalue"></div>

1. Let envRec be the function Environment Record for which the method was invoked.
2. Assert: envRec.[[ThisBindingStatus]] is not "lexical".
3. If envRec.[[ThisBindingStatus]] is "initialized", throw a ReferenceError exception.
4. Set envRec.[[ThisValue]] to V.
5. Set envRec.[[ThisBindingStatus]] to "initialized".
6. Return V

##### 8.1.1.3.2 HasThisBinding ( ) <div id="sec-function-environment-records-hasthisbinding"></div>

1. Let envRec be the function Environment Record for which the method was invoked.
2. If envRec.[[ThisBindingStatus]] is "lexical", return false; otherwise, return true.

##### 8.1.1.3.3 HasSuperBinding ( ) <div id="sec-function-environment-records-hassuperbinding"></div>

1. Let envRec be the function Environment Record for which the method was invoked.
2. If envRec.[[ThisBindingStatus]] is "lexical", return false.
3. If envRec.[[HomeObject]] has the value undefined, return false; otherwise, return true

##### 8.1.1.3.4 GetThisBinding ( ) <div id="sec-function-environment-records-getthisbinding"></div>

1. Let envRec be the function Environment Record for which the method was invoked.
2. Assert: envRec.[[ThisBindingStatus]] is not "lexical".
3. If envRec.[[ThisBindingStatus]] is "uninitialized", throw a ReferenceError exception.
4. Return envRec.[[ThisValue]].

##### 8.1.1.3.5 GetSuperBase ( ) <div id="sec-getsuperbase"></div>

1. Let envRec be the function Environment Record for which the method was invoked.
2. Let home be envRec.[[HomeObject]].
3. If home has the value undefined, return undefined.
4. Assert: Type(home) is Object.
5. Return ? home.\[\[GetPrototypeOf]]().

#### 8.1.1.4 全局环境记录 <div id="global-environment-records"></div>

全局环境记录用于表示在共同领域中处理的所有ECMAScript脚本元素共享的最外部作用域。全局环境记录为内置全局变量（第18节），全局对象的属性以及脚本中发生的所有顶级声明（13.2.8、13.2.10）提供了绑定。

逻辑上，全局环境记录是单个记录，但是它被指定为封装对象的复合记录环境记录和声明性环境记录。对象环境记录的基本对象是相关领域记录的全局对象。此全局对象是全局环境返回的值记录的GetThisBinding具体方法。全局环境记录的对象环境记录组件包含所有内置全局变量的绑定（第18节）以及FunctionDeclaration引入的所有绑定，包含在其中的GeneratorDeclaration，AsyncFunctionDeclaration，AsyncGeneratorDeclaration或VariableStatement全局代码。全局代码中所有其他ECMAScript声明的绑定包含在全局环境记录的声明性环境记录组件中。

可以直接在全局对象上创建属性。因此，全局环境记录的对象环境记录组件可能包含由FunctionDeclaration，GeneratorDeclaration，AsyncFunctionDeclaration，AsyncGeneratorDeclaration或VariableDeclaration声明显式创建的绑定，以及隐式创建为全局对象属性的绑定。为了识别使用声明显式创建的绑定，全局环境记录使用其CreateGlobalVarBinding和CreateGlobalFunctionBinding具体方法维护绑定名称的列表。

全球环境记录具有表17中列出的其他字段和表18中列出的其他方法。

| 字段名                | 值                             | 含义                                                         |
| --------------------- | ------------------------------ | ------------------------------------------------------------ |
| [[ObjectRecord]]      | Object Environment Record      | 绑定对象是global object。 它包含全局内置绑定以及FunctionDeclaration, GeneratorDeclaration, and VariableDeclaration在全局代码中绑定 相关联的领域(realm). |
| [[GlobalThisValue]]   | Object                         | 在全局范围内返回的值。 主机(hosts)可以提供任何ECMAScript对象值。 |
| [[DeclarativeRecord]] | Declarative Environment Record | 包含除了FunctionDeclaration，GeneratorDeclaration和VariableDeclaration绑定之外的关联领域代码的全局代码中的所有声明的绑定. |
| [[VarNames]]          | List of String                 | 由相关领域的全局代码中的FunctionDeclaration，GeneratorDeclaration和VariableDeclaration声明绑定的字符串名称。 |

| 方法                                 | 目的                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| GetThisBinding()                     | 返回此环境记录的this绑定的值。                               |
| HasVarDeclaration (N)                | 确定参数标识符是否在此环境记录中使用VariableDeclaration，FunctionDeclaration或GeneratorDeclaration创建的绑定。 |
| HasLexicalDeclaration (N)            | 确定参数标识符是否在此环境记录中使用词法声明（如LexicalDeclaration或ClassDeclaration）创建的绑定。 |
| HasRestrictedGlobalProperty (N)      | 确定参数是否是全局对象属性的名称，该属性可能不被全局词法绑定所遮蔽。 |
| CanDeclareGlobalVar (N)              | 确定相应的CreateGlobalVarBinding调用是否成功，如果调用相同的参数N. |
| CanDeclareGlobalFunction (N)         | 确定如果相同的参数N被调用，相应的CreateGlobalFunctionBinding调用将成功。 |
| CreateGlobalVarBinding(N, D)         | 用于在全局环境记录的[[ObjectRecord]]组件中创建和初始化未定义的全局变量绑定。 绑定将是一个可变绑定。 相应的全局对象属性将具有适用于var的属性值。 字符串值N是绑定名称。 如果D为真，绑定可能会被删除。 逻辑上等同于CreateMutableBinding，后跟SetMutableBinding，但它允许var声明接受特殊处理。 |
| CreateGlobalFunctionBinding(N, V, D) | 在全局环境记录的[[ObjectRecord]]组件中创建并初始化全局函数绑定。 绑定将是一个可变绑定。 相应的全局对象属性将具有适用于某个函数的属性值。 字符串值N是绑定名称。 V是初始化值。 如果布尔参数D为真，绑定可能会被删除。 逻辑上等同于CreateMutableBinding，后跟SetMutableBinding，但它允许函数声明接受特殊处理。 |

全局环境记录的具体规范方法的行为由以下算法定义。

##### 8.1.1.4.1 HasBinding ( N ) <div id="sec-global-environment-records-hasbinding-n"></div>

全局环境记录的具体环境记录方法HasBinding仅确定参数标识符是否是记录绑定的标识符之一：

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let DclRec be envRec.[[DeclarativeRecord]].
3. If DclRec.HasBinding(N) is true, return true.
4. Let ObjRec be envRec.[[ObjectRecord]].
5. Return ? ObjRec.HasBinding(N).

##### 8.1.1.4.2 CreateMutableBinding ( N, D ) <div id="sec-global-environment-records-createmutablebinding-n-d"></div>

全局环境记录的具体环境记录方法CreateMutableBinding为未初始化的名称N创建新的可变绑定。绑定在关联的DeclarativeRecord中创建。N的绑定必须在DeclarativeRecord中不存在。如果布尔参数D的值为true，则将新绑定标记为要删除。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let DclRec be envRec.[[DeclarativeRecord]].
3. If DclRec.HasBinding(N) is true, throw a TypeError exception.
4. Return DclRec.CreateMutableBinding(N, D).

##### 8.1.1.4.3 CreateImmutableBinding ( N, S ) <div id="sec-global-environment-records-createimmutablebinding-n-s"></div>

用于全局环境记录的具体环境记录方法CreateImmutableBinding创建一个新的未初始化的名称N的不可变绑定。N的此环境记录中必须没有绑定。如果布尔参数S的值为true，则将新绑定标记为严格绑定。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let DclRec be envRec.[[DeclarativeRecord]].
3. If DclRec.HasBinding(N) is true, throw a TypeError exception.
4. Return DclRec.CreateImmutableBinding(N, S).

##### 8.1.1.4.4 InitializeBinding ( N, V ) <div id="sec-global-environment-records-initializebinding-n-v"></div>

用于全局环境记录的具体环境记录方法InitializeBinding用于将名称为自变量N的名称的标识符的当前绑定的绑定值设置为自变量V的值。N的未初始化绑定必须已经存在。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let DclRec be envRec.[[DeclarativeRecord]].
3. If DclRec.HasBinding(N) is true, then
a. Return DclRec.InitializeBinding(N, V).
4. Assert: If the binding exists, it must be in the object Environment Record.
5. Let ObjRec be envRec.[[ObjectRecord]].
6. Return ? ObjRec.InitializeBinding(N, V).

##### 8.1.1.4.5 SetMutableBinding ( N, V, S ) <div id="sec-global-environment-records-setmutablebinding-n-v-s"></div>

全局环境记录的具体环境记录方法SetMutableBinding尝试将名称为参数N的值的标识符的当前绑定的绑定值更改为参数V的值。如果绑定是不可变的绑定，则TypeError为如果S为真，则抛出该异常。通常存在一个名为N的属性，但是如果该属性不存在或当前不可写，则错误处理由布尔参数S的值确定。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let DclRec be envRec.[[DeclarativeRecord]].
3. If DclRec.HasBinding(N) is true, then
a. Return DclRec.SetMutableBinding(N, V, S).
4. Let ObjRec be envRec.[[ObjectRecord]].
5. Return ? ObjRec.SetMutableBinding(N, V, S).

##### 8.1.1.4.6 GetBindingValue ( N, S ) <div id="sec-global-environment-records-getbindingvalue-n-s"></div>

全局环境记录的具体环境记录方法GetBindingValue返回其绑定标识符的值，该标识符的名称为参数N的值。如果绑定是未初始化的绑定，则抛出ReferenceError异常。通常存在一个名为N的属性，但是如果该属性不存在或当前不可写，则错误处理由布尔参数S的值确定。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let DclRec be envRec.[[DeclarativeRecord]].
3. If DclRec.HasBinding(N) is true, then
a. Return DclRec.GetBindingValue(N, S).
4. Let ObjRec be envRec.[[ObjectRecord]].
5. Return ? ObjRec.GetBindingValue(N, S).

##### 8.1.1.4.7 DeleteBinding ( N ) <div id="sec-global-environment-records-deletebinding-n"></div>

全局环境记录的具体环境记录方法DeleteBinding仅可以删除已明确指定要删除的绑定。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let DclRec be envRec.[[DeclarativeRecord]].
3. If DclRec.HasBinding(N) is true, then
a. Return DclRec.DeleteBinding(N).
4. Let ObjRec be envRec.[[ObjectRecord]].
5. Let globalObject be the binding object for ObjRec.
6. Let existingProp be ? HasOwnProperty(globalObject, N).
7. If existingProp is true, then
a. Let status be ? ObjRec.DeleteBinding(N).
b. If status is true, then
i. Let varNames be envRec.[[VarNames]].
ii. If N is an element of varNames, remove that element from the varNames.
c. Return status.
8. Return true.

##### 8.1.1.4.8 HasThisBinding ( ) <div id="sec-global-environment-records-hasthisbinding"></div>

1. Return true.

##### 8.1.1.4.9 HasSuperBinding ( ) <div id="sec-global-environment-records-hassuperbinding"></div>

1. Return false.

##### 8.1.1.4.10 WithBaseObject ( ) <div id="sec-global-environment-records-withbaseobject"></div>

全局环境记录始终返回未定义的WithBaseObject。

1. Return undefined.

##### 8.1.1.4.11 GetThisBinding ( ) <div id="sec-global-environment-records-getthisbinding"></div>

1. Let envRec be the global Environment Record for which the method was invoked.
2. Return envRec.[[GlobalThisValue]].

##### 8.1.1.4.12 HasVarDeclaration ( N ) <div id="sec-hasvardeclaration"></div>

全局环境记录的具体环境记录方法HasVarDeclaration确定在此记录中参数变量是否具有使用VariableStatement或FunctionDeclaration创建的绑定：

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let varDeclaredNames be envRec.[[VarNames]].
3. If varDeclaredNames contains N, return true.
4. Return false.

##### 8.1.1.4.13 HasLexicalDeclaration ( N ) <div id="sec-haslexicaldeclaration"></div>

全局环境记录的具体环境记录方法HasLexicalDeclaration确定在此记录中参数标识符是否具有绑定，该绑定是使用词法声明（例如LexicalDeclaration或ClassDeclaration）创建的：

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let DclRec be envRec.[[DeclarativeRecord]].
3. Return DclRec.HasBinding(N).

##### 8.1.1.4.14 HasRestrictedGlobalProperty ( N ) <div id="sec-hasrestrictedglobalproperty"></div>

全局环境记录的具体环境记录方法HasRestrictedGlobalProperty确定参数标识符是否为全局对象的属性名称，该属性不能被全局词法绑定所遮盖：

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let ObjRec be envRec.[[ObjectRecord]].
3. Let globalObject be the binding object for ObjRec.
4. Let existingProp be ? globalObject.[[GetOwnProperty]](N).
5. If existingProp is undefined, return false.
6. If existingProp.[[Configurable]] is true, return false.
7. Return true.

> 注：属性可能存在于直接创建的全局对象上，而不是使用var或function声明来声明。可能不会创建与全局对象的不可配置属性同名的全局词法绑定。未定义的全局属性是此类属性的示例。

##### 8.1.1.4.15 CanDeclareGlobalVar ( N ) <div id="sec-candeclareglobalvar"></div>

全局环境记录的具体环境记录方法CanDeclareGlobalVar确定是否对相同的参数N调用相应的CreateGlobalVarBinding调用是否将成功。允许冗余的var声明和用于预先存在的全局对象属性的var声明。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let ObjRec be envRec.[[ObjectRecord]].
3. Let globalObject be the binding object for ObjRec.
4. Let hasProperty be ? HasOwnProperty(globalObject, N).
5. If hasProperty is true, return true.
6. Return ? IsExtensible(globalObject).

##### 8.1.1.4.16 CanDeclareGlobalFunction ( N ) <div id="sec-candeclareglobalfunction"></div>

全局环境记录的具体环境记录方法CanDeclareGlobalFunction确定如果对相同的参数N进行调用，相应的CreateGlobalFunctionBinding调用是否将成功。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let ObjRec be envRec.[[ObjectRecord]].
3. Let globalObject be the binding object for ObjRec.
4. Let existingProp be ? globalObject.[[GetOwnProperty]](N).
5. If existingProp is undefined, return ? IsExtensible(globalObject).
6. If existingProp.[[Configurable]] is true, return true.
7. If IsDataDescriptor(existingProp) is true and existingProp has attribute values { [[Writable]]: true, [[Enumerable]]: true }, return true. 
8.  Return false 

##### 8.1.1.4.17 CreateGlobalVarBinding ( N, D ) <div id="sec-createglobalvarbinding"></div>

全局环境记录的具体环境记录方法CreateGlobalVarBinding创建并初始化关联对象环境记录中的可变绑定，并将绑定名称记录在关联的[[VarNames]]列表中。如果绑定已经存在，则将重用它并假定已初始化。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let ObjRec be envRec.[[ObjectRecord]].
3. Let globalObject be the binding object for ObjRec.
4. Let hasProperty be ? HasOwnProperty(globalObject, N).
5. Let extensible be ? IsExtensible(globalObject).
6. If hasProperty is false and extensible is true, then
a. Perform ? ObjRec.CreateMutableBinding(N, D).
b. Perform ? ObjRec.InitializeBinding(N, undefined).
7. Let varDeclaredNames be envRec.[[VarNames]].
8. If varDeclaredNames does not contain N, then
a. Append N to varDeclaredNames.
9. Return NormalCompletion(empty).

##### 8.1.1.4.18 CreateGlobalFunctionBinding ( N, V, D ) <div id="sec-createglobalfunctionbinding"></div>

全局环境记录的具体环境记录方法CreateGlobalFunctionBinding创建并初始化关联对象环境记录中的可变绑定，并将绑定名称记录在关联的[[VarNames]]列表中。如果绑定已经存在，则将其替换。

1. Let envRec be the global Environment Record for which the method was invoked.
2. Let ObjRec be envRec.[[ObjectRecord]].
3. Let globalObject be the binding object for ObjRec.
4. Let existingProp be ? globalObject.\[\[GetOwnProperty]](N).
5. If existingProp is undefined or existingProp.[[Configurable]] is true, then
a. Let desc be the PropertyDescriptor { [[Value]]: V, [[Writable]]: true, [[Enumerable]]: true, [[Configurable]]:
D }.
6. Else,
a. Let desc be the PropertyDescriptor { [[Value]]: V }.
7. Perform ? DefinePropertyOrThrow(globalObject, N, desc).
8. Record that the binding for N in ObjRec has been initialized.
9. Perform ? Set(globalObject, N, V, false).
10. Let varDeclaredNames be envRec.[[VarNames]].
11. If varDeclaredNames does not contain N, then
a. Append N to varDeclaredNames.
12. Return NormalCompletion(empty).

> 注：全局函数声明始终表示为全局对象的自身属性。如果可能，将现有的自己的属性重新配置为具有一组标准的属性值。步骤8-9等效于调用InitializeBinding具体方法将执行的操作，并且如果globalObject是Proxy，将产生相同的Proxy trap调用序列。

#### 8.1.1.5 模块环境记录 <div id="module-environment-records"></div>

模块环境记录是声明性环境记录，用于表示ECMAScript模块的外部范围。除了正常的可变和不可变绑定之外，模块环境记录还提供不可变导入绑定，这些导入绑定提供了对另一个环境记录中存在的目标绑定的间接访问。

模块环境记录支持表14中列出的所有声明性环境记录方法，并且对所有这些方法都具有相同的规范，但GetBindingValue，DeleteBinding，HasThisBinding和GetThisBinding除外。此外，模块环境记录支持表19中列出的方法：

| 方法                          | 目的                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| CreateImportBinding(N, M, N2) | 在模块环境记录中创建不可变的间接绑定。 字符串值N是绑定名称的文本。 M是模块记录，N2是M的模块环境记录中存在的绑定。 |
| GetThisBinding()              | 返回此环境记录的this绑定的值。                               |

模块环境记录的其他具体规范方法的行为由以下算法定义：

##### 8.1.1.5.1 GetBindingValue ( N, S ) <div id="sec-module-environment-records-getbindingvalue-n-s"></div>

模块Environment Records的具体Environment Record方法GetBindingValue返回其绑定标识符的值，该标识符的名称为参数N的值。但是，如果绑定是间接绑定，则返回目标绑定的值。如果绑定存在但未初始化，则抛出ReferenceError。

1. Assert: S is true.
2. Let envRec be the module Environment Record for which the method was invoked.
3. Assert: envRec has a binding for N.
4. If the binding for N is an indirect binding, then
a. Let M and N2 be the indirection values provided when this binding for N was created.
b. Let targetEnv be M.[[Environment]].
c. If targetEnv is undefined, throw a ReferenceError exception.
d. Let targetER be targetEnv's EnvironmentRecord.
e. Return ? targetER.GetBindingValue(N2, true).
5. If the binding for N in envRec is an uninitialized binding, throw a ReferenceError exception.
6. Return the value currently bound to N in envRec.

> 注：因为模块始终是严格模式代码，所以S始终为true。

##### 8.1.1.5.2 DeleteBinding ( N ) <div id="sec-module-environment-records-deletebinding-n"></div>

模块环境记录的具体环境记录方法DeleteBinding拒绝删除绑定。

1. Assert: This method is never invoked. See 12.5.3.1.

> 注：模块环境记录仅在严格的代码内使用，并且早期错误规则可防止在严格的代码中将delete运算符应用于将解析为模块环境记录绑定的引用。见12.5.3.1。

##### 8.1.1.5.3 HasThisBinding ( ) <div id="sec-module-environment-records-hasthisbinding"></div>

模块环境记录提供this绑定。

1. Return true.

##### 8.1.1.5.4 GetThisBinding ( ) <div id="sec-module-environment-records-getthisbinding"></div>

1. Return undefined.

##### 8.1.1.5.5 CreateImportBinding ( N, M, N2 ) <div id="sec-createimportbinding"></div>

模块环境记录的具体环境记录方法CreateImportBinding为名称N创建一个新的初始化的不可变间接绑定。此环境记录中对于N的绑定必须已经不存在。M是模块记录，而N2是该绑定的名称存在于M的模块环境记录中。访问新绑定的值将间接访问目标绑定的绑定值。

1. Let envRec be the module Environment Record for which the method was invoked.
2. Assert: envRec does not already have a binding for N.
3. Assert: M is a Module Record.
4. Assert: When M.[[Environment]] is instantiated it will have a direct binding for N2.
5. Create an immutable indirect binding in envRec for N that references M and N2 as its target binding and record that
the binding is initialized.
6. Return NormalCompletion(empty).

### 8.1.2 词法环境操作 <div id="lexical-environment-operations"></div>

本规范中使用以下抽象操作在词汇环境上进行操作：

#### 8.1.2.1 GetIdentifierReference ( lex, name, strict ) <div id="sec-getidentifierreference"></div>

抽象操作GetIdentifierReference用词法环境lex，字符串名称和布尔标志strict调用。lex的值可以为null。调用时，将执行以下步骤：

1. If lex is the value null, then
    a. Return a value of type Reference whose base value component is undefined, whose referenced name
    component is name, and whose strict reference flag is strict.

2. Let envRec be lex's EnvironmentRecord.

3. Let exists be ? envRec.HasBinding(name).

4. If exists is true, then
    a. Return a value of type Reference whose base value component is envRec, whose referenced name component
    is name, and whose strict reference flag is strict.

5. Else,

   a. Let outer be the value of lex's outer environment reference.
   b. Return ? GetIdentifierReference(outer, name, strict).

#### 8.1.2.2 NewDeclarativeEnvironment ( E ) <div id="sec-newdeclarativeenvironment"></div>

当使用词法环境作为参数E调用抽象操作NewDeclarativeEnvironment时，将执行以下步骤：

1. Let env be a new Lexical Environment.
2. Let envRec be a new declarative Environment Record containing no bindings.
3. Set env's EnvironmentRecord to envRec.
4. Set the outer lexical environment reference of env to E.
5. Return env.

#### 8.1.2.3 NewObjectEnvironment ( O, E ) <div id="sec-newobjectenvironment"></div>

以对象O和词法环境E作为参数调用抽象操作NewObjectEnvironment时，将执行以下步骤：

1. Let env be a new Lexical Environment.
2. Let envRec be a new object Environment Record containing O as the binding object.
3. Set env's EnvironmentRecord to envRec.
4. Set the outer lexical environment reference of env to E.
5. Return env.

#### 8.1.2.4 NewFunctionEnvironment ( F, newTarget ) <div id="sec-newfunctionenvironment"></div>

当使用参数F和newTarget调用抽象操作NewFunctionEnvironment时，将执行以下步骤：

1. Assert: F is an ECMAScript function.
2. Assert: Type(newTarget) is Undefined or Object.
3. Let env be a new Lexical Environment.
4. Let envRec be a new function Environment Record containing no bindings.
5. Set envRec.[[FunctionObject]] to F.
6. If F.[[ThisMode]] is lexical, set envRec.[[ThisBindingStatus]] to "lexical".
7. Else, set envRec.[[ThisBindingStatus]] to "uninitialized".
8. Let home be F.[[HomeObject]].
9. Set envRec.[[HomeObject]] to home.
10. Set envRec.[[NewTarget]] to newTarget.
11. Set env's EnvironmentRecord to envRec.
12. Set the outer lexical environment reference of env to F.[[Environment]].
13. Return env.

#### 8.1.2.5 NewGlobalEnvironment ( G, thisValue ) <div id="sec-newglobalenvironment"></div>

当使用参数G和thisValue调用抽象操作NewGlobalEnvironment时，将执行以下步骤：

1. Let env be a new Lexical Environment.

2. Let objRec be a new object Environment Record containing G as the binding object.
3. Let dclRec be a new declarative Environment Record containing no bindings.
4. Let globalRec be a new global Environment Record.
5. Set globalRec.[[ObjectRecord]] to objRec.
6. Set globalRec.[[GlobalThisValue]] to thisValue.
7. Set globalRec.[[DeclarativeRecord]] to dclRec.
8. Set globalRec.[[VarNames]] to a new empty List.
9. Set env's EnvironmentRecord to globalRec.
10. Set the outer lexical environment reference of env to null.
11. Return env.

#### 8.1.2.6 NewModuleEnvironment ( E ) <div id="sec-newmoduleenvironment"></div>

当使用词法环境参数E调用抽象操作NewModuleEnvironment时，将执行以下步骤：

1. Let env be a new Lexical Environment.
2. Let envRec be a new module Environment Record containing no bindings.
3. Set env's EnvironmentRecord to envRec.
4. Set the outer lexical environment reference of env to E.
5. Return env.

## 8.2 领域 <div id="realms"></div>
### 8.2.1 CreateRealm ( ) <div id="sec-createrealm"></div>
### 8.2.2 CreateIntrinsics ( realmRec ) <div id="sec-createintrinsics"></div>
### 8.2.3 SetRealmGlobalObject ( realmRec, globalObj, thisValue ) <div id="sec-setrealmglobalobject"></div>
### 8.2.4 SetDefaultGlobalBindings ( realmRec ) <div id="sec-setdefaultglobalbindings"></div>
## 8.3 可执行上下文 <div id="execution-contexts"></div>
### 8.3.1 GetActiveScriptOrModule ( ) <div id="sec-getactivescriptormodule"></div>
### 8.3.2 ResolveBinding ( name [ , env ]) <div id="sec-resolvebinding"></div>
### 8.3.3 GetThisEnvironment ( ) <div id="sec-getthisenvironment"></div>
### 8.3.4 ResolveThisBinding ( ) <div id="sec-resolvethisbinding"></div>
### 8.3.5 GetNewTarget ( ) <div id="sec-getnewtarget"></div>
### 8.3.6 GetGlobalObject ( ) <div id="sec-getglobalobject"></div>
## 8.4 任务和任务队列 <div id="jobs-and-job-queues"></div>
### 8.4.1 EnqueueJob ( queueName, job, arguments ) <div id="sec-enqueuejob"></div>
## 8.5 InitializeHostDefinedRealm ( ) <div id="initializehostdefinedrealm"></div>
## 8.6 RunJobs ( ) <div id="runjobs"></div>
## 8.7 代理 <div id="agents"></div>
### 8.7.1 AgentSignifier ( ) <div id="sec-agentsignifier"></div>
### 8.7.2 AgentCanSuspend ( ) <div id="sec-agentcansuspend"></div>
## 8.8 代理集群 <div id="agent-clusters"></div>
## 8.9 前进进度 <div id="forward-progress"></div>