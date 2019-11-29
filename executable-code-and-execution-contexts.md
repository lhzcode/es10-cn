# 8 可执行代码和可执行上下文

## 8.1 词法环境 <div id="sec-lexical-environments.md"></div>

词法环境是一个用于定义特定变量和函数标识符在 ECMAScript 代码的词法嵌套结构上关联关系的规范类型。一个词法环境由一个环境记录项和可能为空的外部词法环境引用构成。通常词法环境会与 ECMAScript 代码诸如 FunctionDeclaration、WithStatement 或者 TryStatement 的 Catch 块这样的特定句法结构相联系，且类似代码每次执行都会有一个新的词法环境被创建出来。

环境记录记录了在其关联词法环境范围内创建的标识符绑定。它被称为词法环境的环境记录项。

外部词法环境引用用于表示词法环境的逻辑嵌套关系模型。（内部）词法环境的外部引用是逻辑上包含内部词法环境的词法环境。外部词法环境当然可以具有自己的外部词法环境。词法环境可以用作多个内部词法环境的外部环境。例如，如果一个 FunctionDeclaration 包含两个嵌套的 FunctionDeclaration，那么每个内嵌函数的词法环境都是外部函数本次执行所产生的词法环境。

全局环境是没有外部环境的词法环境。全局环境的外部环境引用为null。全局环境的环境记录项可能预填充了标识符绑定，并包括一个关联的全局对象，该对象的属性提供了某些全局环境的标识符绑定。在执行ECMAScript代码时，可以将其他属性添加到全局对象，并且可以修改初始属性。

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

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 断言: envRec 必须具有N的未初始化绑定。
3. 将envRec中N的绑定值设置为V 。
4. 记录envRec中已初始化的N的绑定。 
5. 返回 NormalCompletion(empty).

##### 8.1.1.1.5 SetMutableBinding ( N, V, S ) <div id="sec-declarative-environment-records-setmutablebinding-n-v-s"></div>

用于声明性环境记录的具体环境记录方法SetMutableBinding试图将名称为参数N的值的标识符的当前绑定的绑定值更改为参数V的值。N的绑定通常已经存在，但很少见情况可能并非如此。如果绑定是不可变的绑定，则在S为true时引发TypeError。

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 若 envRec 不存在 N 的绑定, 那么
1. 若 S 是 true, 抛出 ReferenceError 异常.
2. 执行 envRec.CreateMutableBinding(N, true).
3. 执行 envRec.InitializeBinding(N, V).
4. 返回 NormalCompletion(empty).
3. 若 envRec中N的绑定是严格绑定, 设置 S 为 true.
4. 若 envRec中N的绑定未被初始化, 抛出 ReferenceError 异常.
5. 否则 若 envRec中N的绑定是易变的绑定,  将绑定值改为 V.
6. 否则,
    1. 断言: 尝试更改不可变绑定的值.
    2. 若 S 是 true, 抛出 TypeError 异常.
7. 返回 NormalCompletion(empty).

> 注：导致步骤2缺少绑定的ECMAScript代码示例为：
>
>  function f(){eval("var x; x = (delete x, 0);")}  

##### 8.1.1.1.6 GetBindingValue ( N, S ) <div id="sec-declarative-environment-records-getbindingvalue-n-s"></div>

声明性环境记录的具体环境记录方法GetBindingValue只是返回其绑定标识符的值，该标识符的名称是参数N的值。如果绑定存在但未初始化，则无论S的值如何，都会引发ReferenceError。

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 断言: envRec 具有N的绑定.
3. 若 envRec中的N绑定是未初始化的绑定, 抛出 ReferenceError 异常.
4. 返回 envRec 中的N的当前绑定值

##### 8.1.1.1.7 DeleteBinding ( N ) <div id="sec-declarative-environment-records-deletebinding-n"></div>

声明性环境记录的具体环境记录方法DeleteBinding只能删除已明确指定要删除的绑定。

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 断言: envRec绑定的名称是N的值。
3. 若 envRec中N不能被删除, 返回 false.
4. 从envRec中删除N的绑定.
5. 返回 true.

##### 8.1.1.1.8 HasThisBinding ( ) <div id="sec-declarative-environment-records-hasthisbinding"></div>

常规的声明性环境记录不提供this绑定。

1. 返回 false.  

##### 8.1.1.1.9 HasSuperBinding ( ) <div id="sec-declarative-environment-records-hassuperbinding"></div>

常规的声明性环境记录不提供super绑定。

1. 返回 false.

##### 8.1.1.1.10 WithBaseObject ( ) <div id="sec-declarative-environment-records-withbaseobject"></div>

声明性环境记录始终返回 undefined 作为 WithBaseObject。

1. 返回 undefined.  

#### 8.1.1.2 对象环境记录 <div id="sec-object-environment-records"></div>

每一个对象式环境记录项都有一个关联的对象，这个对象被称作绑定对象 。对象式环境记录项直接将一系列标识符与其绑定对象的属性名称建立一一对应关系。不符合 IdentifierName 的属性名不会作为绑定的标识符使用。无论是对象自身的，还是继承的属性都会作为绑定，无论该属性的 [[Enumerable]] 特性的值是什么。由于对象的属性可以动态的增减，因此对象式环境记录项所绑定的标识符集合也会隐匿地变化，这是增减绑定对象的属性而产生的副作用。通过以上描述的副作用而建立的绑定，均被视为可变绑定，即使该绑定对应的属性的 [[Writable]] 特性的值为 false。对象式环境记录项没有不可变绑定。

为with语句（13.11）创建的对象环境记录可以提供其绑定对象作为此值的隐式供函数调用使用。该功能由与每个对象环境记录关联的withEnvironment布尔值控制。默认情况下，任何对象环境记录的withEnvironment的值为false。

对象环境记录的具体规范方法的行为由以下算法定义。

##### 8.1.1.2.1 HasBinding ( N ) <div id="sec-object-environment-records-hasbinding-n"></div>

对象式环境记录项的 HasBinding 具体方法判断其关联的绑定对象是否有名为 N 的属性：

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 令 bindings 为 envRec的绑定对象.
3. 令 foundBinding 为 ? HasProperty(bindings, N).
4. 若 foundBinding 是 false, 返回 false.
5. 若 envRec 的 withEnvironment 标识是 false, 返回 true.
6. 令 unscopables 为 ? Get(bindings, @@unscopables).
7. 若 Type(unscopables) 是 Object, 那么
1. 令 blocked 为 ToBoolean(? Get(unscopables, N)).
2. 若 blocked 是 true, 返回 false.
8. 返回 true

##### 8.1.1.1.2 CreateMutableBinding ( N, D ) <div id="sec-declarative-environment-records-createmutablebinding-n-d"></div>

对象式环境记录项的 CreateMutableBinding具体方法会在其关联的绑定对象上创建一个名称为 N 的属性，并初始化其值为 undefined。调用方法时，绑定对象不得包含名称为 N 的属性。如果调用方法时提供了布尔类型的参数 D 且其值为 true，则设置新创建的属性的 [[Configurable]] 特性的值为 true，否则设置为 false。

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 令 bindings 为 envRec的绑定对象.
3. 返回 ? DefinePropertyOrThrow(bindings, N, PropertyDescriptor { [[Value]]: undefined, [[Writable]]: true,
[[Enumerable]]: true, [[Configurable]]: D }).

> 注：通常，envRec没有N的绑定，但是如果绑定N，则DefinePropertyOrThrow的语义可能会导致现有绑定被替换或隐藏或导致突然完成返回。

##### 8.1.1.2.3 CreateImmutableBinding ( N, S ) <div id="sec-object-environment-records-createimmutablebinding-n-s"></div>

具体的环境记录方法CreateImmutableBinding从未在此规范中与对象环境记录关联使用。

##### 8.1.1.2.4 InitializeBinding ( N, V ) <div id="sec-object-environment-records-initializebinding-n-v"></div>

对象环境记录的具体环境记录方法InitializeBinding用于将名称为参数N的值的标识符的当前绑定的绑定值设置为参数V的值。N的未初始化绑定必须已经存在。

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 断言: envRec 必须具有N的未初始化绑定。
3. 记录envRec中已初始化的N的绑定
4. 返回 ? envRec.SetMutableBinding(N, V, false).

> 注：在此规范中，对对象环境记录的CreateMutableBinding的所有使用都将紧随其后的是对具有相同名称的InitializeBinding的调用。因此，实现无需显式跟踪单个对象环境记录绑定的初始化状态。

##### 8.1.1.2.5 SetMutableBinding ( N, V, S ) <div id="sec-object-environment-records-setmutablebinding-n-v-s"></div>

对象式环境记录项的 SetMutableBinding 具体方法会尝试设置其关联的绑定对象中名为 N 的属性的值为 V。方法调用时，绑定对象中应当存在该属性，如果该属性不存在或属性不可写，则根据 S 参数的值来执行错误处理。

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 令 bindings 为 envRec 的绑定对象
3. 返回 ? 设置(bindings, N, V, S).

##### 8.1.1.2.6 GetBindingValue ( N, S ) <div id="sec-object-environment-records-getbindingvalue-n-s"></div>

对象式环境记录项的 GetBindingValue 具体方法返回其关联的绑定对象中名为 N 的属性的值。方法调用时，绑定对象中应当存在该属性，如果该属性不存在，则方法的返回值由 S 参数决定：

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 令 bindings 为 envRec 的绑定对象
3. 令 value 为 ? HasProperty(bindings, N).
4. 若 value 是 false, 那么
1. 若 S 是 false, 返回 undefined 值; 否则，抛出 ReferenceError 异常.
5. 返回 ? Get(bindings, N).

##### 8.1.1.2.7 DeleteBinding ( N ) <div id="sec-object-environment-records-deletebinding-n"></div>

对象式环境记录项的 DeleteBinding 具体方法只能用于删除其关联的绑定对象上 [[Configurable]] 特性的值为 true 的属性所对应的绑定。

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 令 bindings 为 envRec 的绑定对象
3. 返回 ? bindings.\[\[Delete]](N).

##### 8.1.1.2.8 HasThisBinding ( ) <div id="sec-object-environment-records-hasthisbinding"></div>

常规对象环境记录不提供this绑定。

1. 返回 false.

##### 8.1.1.2.9 HasSuperBinding ( ) <div id="sec-object-environment-records-hassuperbinding"></div>

常规对象环境记录不提供super绑定。

1. 返回 false.

##### 8.1.1.2.10 WithBaseObject ( ) <div id="sec-object-environment-records-withbaseobject"></div>

除非其withEnvironment标志为true，否则对象环境记录将返回undefined作为其WithBaseObject。

1. 令 envRec 为函数调用时对应的声明式环境记录项。
2. 若 envRec 的 withEnviroment 标志 是 true, 返回 envRec 的绑定对象.
3. 否则, 返回 undefined.

#### 8.1.1.3 函数环境记录 <div id="function-enviroment-record"></div>

函数环境记录是声明性环境记录，用于表示函数的顶级作用域，如果函数不是箭头函数，则提供此绑定。如果一个函数不是ArrowFunction函数，并且引用了super，则其函数环境记录项也包含用于从函数内部执行super方法调用的状态。

函数环境记录具有表15中列出的其他状态字段。

| 字段名                | 值                                              | 含义                                                         |
| --------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| [[ThisValue]]         | 任意值                                          | 这是用于此函数调用的this值.                                  |
| [[ThisBindingStatus]] | "lexical" \| "initialized"   \| "uninitialized" | 如果值为"lexical"，这是一个箭头函数，没有本地this值。        |
| [[FunctionObject]]    | Object                                          | 函数对象被调用导致这个环境记录项被创建。                     |
| [[HomeObject]]        | Object \| undefined                             | 如果相关函数具有`super`属性访问，并且不是箭头函数，[[HomeObject ]]是函数绑定到一个方法的对象。 [[HomeObject]]的默认值为 undefined 。 |
| [[NewTarget]]         | Object \| undefined                             | 如果这个环境记录项是由[[Construct] ]内部方法创建，[[NewTarget]]是[[Construct]] newTarget 参数的值。 否则，其值为 undefined 。 |

函数环境记录支持表14中列出的所有声明性环境记录方法，并且对所有这些方法共享相同的规范，但HasThisBinding和HasSuperBinding除外。另外，函数环境记录项支持表16中列出的方法：

| 方法             | 目的                                                         |
| ---------------- | ------------------------------------------------------------ |
| BindThisValue(V) | 设置[[ThisValue]]并记录它已被初始化。                        |
| GetThisBinding() | 返回此环境记录项的`this`绑定。 如果this绑定尚未初始化，则抛出 ReferenceError 异常。 |
| GetSuperBase()   | 返回在环境记录项中作为` super `属性访问绑定的基础的对象。该对象源于此环境及记录的[[ HomeObject]]字段。 undefined值表示`super`属性访问将产生运行时错误. |

环境环境记录的其他具体规范方法的行为由以下算法定义：

##### 8.1.1.3.1 BindThisValue ( V ) <div id="sec-function-environment-records-bindthisvalue"></div>

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 断言: envRec.[[ThisBindingStatus]] 不是 "lexical".
3. 若 envRec.[[ThisBindingStatus]] 是 "initialized", 抛出 ReferenceError 异常.
4. 设置 envRec.[[ThisValue]] 为 V.
5. 设置 envRec.[[ThisBindingStatus]] 为 "initialized".
6. 返回 V

##### 8.1.1.3.2 HasThisBinding ( ) <div id="sec-function-environment-records-hasthisbinding"></div>

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 若 envRec.[[ThisBindingStatus]] 是 "lexical", 返回 false; 否则, 返回 true.

##### 8.1.1.3.3 HasSuperBinding ( ) <div id="sec-function-environment-records-hassuperbinding"></div>

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 若 envRec.[[ThisBindingStatus]] 是 "lexical", 返回 false.
3. 若 envRec.[[HomeObject]] has the value undefined, 返回 false; 否则, 返回 true

##### 8.1.1.3.4 GetThisBinding ( ) <div id="sec-function-environment-records-getthisbinding"></div>

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 断言: envRec.[[ThisBindingStatus]] 不是 "lexical".
3. 若 envRec.[[ThisBindingStatus]] 是 "uninitialized", 抛出 ReferenceError 异常.
4. 返回 envRec.[[ThisValue]].

##### 8.1.1.3.5 GetSuperBase ( ) <div id="sec-getsuperbase"></div>

1. 令 envRec 为函数调用时对应的声明式环境记录项.
2. 令 home 为 envRec.[[HomeObject]].
3. 若 home has the value undefined, 返回 undefined.
4. 断言: Type(home) 是 Object.
5. 返回 ? home.\[\[GetPrototypeOf]]().

#### 8.1.1.4 全局环境记录 <div id="global-environment-records"></div>

全局环境记录用于表示在共同作用域中处理的所有ECMAScript脚本元素共享的最外部作用域。全局环境记录为内置全局变量（第18节），全局对象的属性以及脚本中发生的所有顶级声明（13.2.8、13.2.10）提供了绑定。

逻辑上，全局环境记录是单个记录，但是它被指定为封装对象的复合记录环境记录和声明性环境记录。对象环境记录的基本对象是相关作用域记录的全局对象。此全局对象是全局环境返回的值记录的GetThisBinding具体方法。全局环境记录的对象环境记录组件包含所有内置全局变量的绑定（第18节）以及FunctionDeclaration引入的所有绑定，包含在其中的GeneratorDeclaration，AsyncFunctionDeclaration，AsyncGeneratorDeclaration或VariableStatement全局代码。全局代码中所有其他ECMAScript声明的绑定包含在全局环境记录的声明性环境记录组件中。

可以直接在全局对象上创建属性。因此，全局环境记录的对象环境记录组件可能包含由FunctionDeclaration，GeneratorDeclaration，AsyncFunctionDeclaration，AsyncGeneratorDeclaration或VariableDeclaration声明显式创建的绑定，以及隐式创建为全局对象属性的绑定。为了识别使用声明显式创建的绑定，全局环境记录使用其CreateGlobalVarBinding和CreateGlobalFunctionBinding具体方法维护绑定名称的列表。

全球环境记录具有表17中列出的其他字段和表18中列出的其他方法。

| 字段名                | 值                             | 含义                                                         |
| --------------------- | ------------------------------ | ------------------------------------------------------------ |
| [[ObjectRecord]]      | Object Environment Record      | 绑定对象是global object。 它包含全局内置绑定以及FunctionDeclaration, GeneratorDeclaration, and VariableDeclaration在全局代码中绑定 相关联的作用域(realm). |
| [[GlobalThisValue]]   | Object                         | 在全局范围内返回的值。 主机(hosts)可以提供任何ECMAScript对象值。 |
| [[DeclarativeRecord]] | Declarative Environment Record | 包含除了FunctionDeclaration，GeneratorDeclaration和VariableDeclaration绑定之外的关联作用域代码的全局代码中的所有声明的绑定. |
| [[VarNames]]          | List of String                 | 由相关作用域的全局代码中的FunctionDeclaration，GeneratorDeclaration和VariableDeclaration声明绑定的字符串名称。 |

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

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 DclRec 为 envRec.[[DeclarativeRecord]].
3. 若 DclRec.HasBinding(N) 是 true, 返回 true.
4. 令 ObjRec 为 envRec.[[ObjectRecord]].
5. 返回 ? ObjRec.HasBinding(N).

##### 8.1.1.4.2 CreateMutableBinding ( N, D ) <div id="sec-global-environment-records-createmutablebinding-n-d"></div>

全局环境记录的具体环境记录方法CreateMutableBinding为未初始化的名称N创建新的可变绑定。绑定在关联的DeclarativeRecord中创建。N的绑定必须在DeclarativeRecord中不存在。如果布尔参数D的值为true，则将新绑定标记为要删除。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 DclRec 为 envRec.[[DeclarativeRecord]].
3. 若 DclRec.HasBinding(N) 是 true, 抛出 TypeError 异常.
4. 返回 DclRec.CreateMutableBinding(N, D).

##### 8.1.1.4.3 CreateImmutableBinding ( N, S ) <div id="sec-global-environment-records-createimmutablebinding-n-s"></div>

用于全局环境记录的具体环境记录方法CreateImmutableBinding创建一个新的未初始化的名称N的不可变绑定。N的此环境记录中必须没有绑定。如果布尔参数S的值为true，则将新绑定标记为严格绑定。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 DclRec 为 envRec.[[DeclarativeRecord]].
3. 若 DclRec.HasBinding(N) 是 true, 抛出 TypeError 异常.
4. 返回 DclRec.CreateImmutableBinding(N, S).

##### 8.1.1.4.4 InitializeBinding ( N, V ) <div id="sec-global-environment-records-initializebinding-n-v"></div>

用于全局环境记录的具体环境记录方法InitializeBinding用于将名称为自变量N的名称的标识符的当前绑定的绑定值设置为自变量V的值。N的未初始化绑定必须已经存在。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 DclRec 为 envRec.[[DeclarativeRecord]].
3. 若 DclRec.HasBinding(N) 是 true, 那么
1. 返回 DclRec.InitializeBinding(N, V).
4. 断言: 若存在绑定, 必须为对象环境记录项.
5. 令 ObjRec 为 envRec.[[ObjectRecord]].
6. 返回 ? ObjRec.InitializeBinding(N, V).

##### 8.1.1.4.5 SetMutableBinding ( N, V, S ) <div id="sec-global-environment-records-setmutablebinding-n-v-s"></div>

全局环境记录的具体环境记录方法SetMutableBinding尝试将名称为参数N的值的标识符的当前绑定的绑定值更改为参数V的值。如果绑定是不可变的绑定，则TypeError为如果S为真，则抛出该异常。通常存在一个名为N的属性，但是如果该属性不存在或当前不可写，则错误处理由布尔参数S的值确定。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 DclRec 为 envRec.[[DeclarativeRecord]].
3. 若 DclRec.HasBinding(N) 是 true, 那么
1. s返回 DclRec.SetMutableBinding(N, V, S).
4. 令 ObjRec 为 envRec.[[ObjectRecord]].
5. 返回 ? ObjRec.SetMutableBinding(N, V, S).

##### 8.1.1.4.6 GetBindingValue ( N, S ) <div id="sec-global-environment-records-getbindingvalue-n-s"></div>

全局环境记录的具体环境记录方法GetBindingValue返回其绑定标识符的值，该标识符的名称为参数N的值。如果绑定是未初始化的绑定，则抛出ReferenceError异常。通常存在一个名为N的属性，但是如果该属性不存在或当前不可写，则错误处理由布尔参数S的值确定。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 DclRec 为 envRec.[[DeclarativeRecord]].
3. 若 DclRec.HasBinding(N) 是 true, 那么
1. 返回 DclRec.GetBindingValue(N, S).
4. 令 ObjRec 为 envRec.[[ObjectRecord]].
5. 返回 ? ObjRec.GetBindingValue(N, S).

##### 8.1.1.4.7 DeleteBinding ( N ) <div id="sec-global-environment-records-deletebinding-n"></div>

全局环境记录的具体环境记录方法DeleteBinding仅可以删除已明确指定要删除的绑定。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.

2. 令 DclRec 为 envRec.[[DeclarativeRecord]].

3. 若 DclRec.HasBinding(N) 是 true, 那么

  1. 返回 DclRec.DeleteBinding(N).

4. 令 ObjRec 为 envRec.[[ObjectRecord]].

5. 令 globalObject 为 ObjRec 的绑定对象.

6. 令 existingProp 为 ? HasOwnProperty(globalObject, N).

7. 若 existingProp 是 true, 那么

  1. 令 status 为 ? ObjRec.DeleteBinding(N).
  2.  若 status 是 true, 那么
     1. 令 varNames 为 envRec.[[VarNames]].
     2. 若 N 是 varNames中的元素, 移除 varNames 中的元素.

  3. 返回 status.

8. 返回 true.

##### 8.1.1.4.8 HasThisBinding ( ) <div id="sec-global-environment-records-hasthisbinding"></div>

1. 返回 true.

##### 8.1.1.4.9 HasSuperBinding ( ) <div id="sec-global-environment-records-hassuperbinding"></div>

1. 返回 false.

##### 8.1.1.4.10 WithBaseObject ( ) <div id="sec-global-environment-records-withbaseobject"></div>

全局环境记录的WithBaseObject始终返回undefined。

1. 返回 undefined.

##### 8.1.1.4.11 GetThisBinding ( ) <div id="sec-global-environment-records-getthisbinding"></div>

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 返回 envRec.[[GlobalThisValue]].

##### 8.1.1.4.12 HasVarDeclaration ( N ) <div id="sec-hasvardeclaration"></div>

全局环境记录的具体环境记录方法HasVarDeclaration确定在此记录中参数变量是否具有使用VariableStatement或FunctionDeclaration创建的绑定：

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 varDeclaredNames 为 envRec.[[VarNames]].
3. 若 varDeclaredNames contains N, 返回 true.
4. 返回 false.

##### 8.1.1.4.13 HasLexicalDeclaration ( N ) <div id="sec-haslexicaldeclaration"></div>

全局环境记录的具体环境记录方法HasLexicalDeclaration确定在此记录中参数标识符是否具有绑定，该绑定是使用词法声明（例如LexicalDeclaration或ClassDeclaration）创建的：

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 DclRec 为 envRec.[[DeclarativeRecord]].
3. 返回 DclRec.HasBinding(N).

##### 8.1.1.4.14 HasRestrictedGlobalProperty ( N ) <div id="sec-hasrestrictedglobalproperty"></div>

全局环境记录的具体环境记录方法HasRestrictedGlobalProperty确定参数标识符是否为全局对象的属性名称，该属性不能被全局词法绑定所遮盖：

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 ObjRec 为 envRec.[[ObjectRecord]].
3. 令 globalObject 为 ObjRec 的绑定对象.
4. 令 existingProp 为 ? globalObject.\[\[GetOwnProperty]](N).
5. 若 existingProp 是 undefined, 返回 false.
6. 若 existingProp.[[Configurable]] 是 true, 返回 false.
7. 返回 true.

> 注：属性可能存在于直接创建的全局对象上，而不是使用var或function声明来声明。可能不会创建与全局对象的不可配置属性同名的全局词法绑定。未定义的全局属性是此类属性的示例。

##### 8.1.1.4.15 CanDeclareGlobalVar ( N ) <div id="sec-candeclareglobalvar"></div>

全局环境记录的具体环境记录方法CanDeclareGlobalVar确定是否对相同的参数N调用相应的CreateGlobalVarBinding调用是否将成功。允许冗余的var声明和用于预先存在的全局对象属性的var声明。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 ObjRec 为 envRec.[[ObjectRecord]].
3. 令 globalObject 为 ObjRec 的绑定对象.
4. 令 hasProperty 为 ? HasOwnProperty(globalObject, N).
5. 若 hasProperty 是 true, 返回 true.
6. 返回 ? IsExtensible(globalObject).

##### 8.1.1.4.16 CanDeclareGlobalFunction ( N ) <div id="sec-candeclareglobalfunction"></div>

全局环境记录的具体环境记录方法CanDeclareGlobalFunction确定如果对相同的参数N进行调用，相应的CreateGlobalFunctionBinding调用是否将成功。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 ObjRec 为 envRec.[[ObjectRecord]].
3. 令 globalObject 为 ObjRec的绑定对象.
4. 令 existingProp 为 ? globalObject.\[\[GetOwnProperty]](N).
5. 若 existingProp 是 undefined, 返回 ? IsExtensible(globalObject).
6. 若 existingProp.[[Configurable]] 是 true, 返回 true.
7. 若 IsDataDescriptor(existingProp) 是 true 同时 existingProp 是 { [[Writable]]: true, [[Enumerable]]: true } 的属性值, 返回 true. 
8.  返回 false 

##### 8.1.1.4.17 CreateGlobalVarBinding ( N, D ) <div id="sec-createglobalvarbinding"></div>

全局环境记录的具体环境记录方法CreateGlobalVarBinding创建并初始化关联对象环境记录中的可变绑定，并将绑定名称记录在关联的[[VarNames]]列表中。如果绑定已经存在，则将重用它并假定已初始化。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 ObjRec 为 envRec.[[ObjectRecord]].
3. 令 globalObject 为 ObjRec的绑定对象.
4. 令 hasProperty 为 ? HasOwnProperty(globalObject, N).
5. 令 extensible 为 ? IsExtensible(globalObject).
6. 若 hasProperty 是 false 同时 extensible 是 true, 那么
1. 执行 ? ObjRec.CreateMutableBinding(N, D).
2. 执行 ? ObjRec.InitializeBinding(N, undefined).
7. 令 varDeclaredNames 为 envRec.[[VarNames]].
8. 若 varDeclaredNames 不包含 N, 那么
  1. Append N 为 varDeclaredNames.
9. 返回 NormalCompletion(empty).

##### 8.1.1.4.18 CreateGlobalFunctionBinding ( N, V, D ) <div id="sec-createglobalfunctionbinding"></div>

全局环境记录的具体环境记录方法CreateGlobalFunctionBinding创建并初始化关联对象环境记录中的可变绑定，并将绑定名称记录在关联的[[VarNames]]列表中。如果绑定已经存在，则将其替换。

1. 令 envRec 为 为函数调用时对应的全局式环境记录项.
2. 令 ObjRec 为 envRec.[[ObjectRecord]].
3. 令 globalObject 为 ObjRec的绑定对象.
4. 令 existingProp 为 ? globalObject.\[\[GetOwnProperty]](N).
5. 若 existingProp 是 undefined 或者 existingProp.[[Configurable]] 是 true, 那么
1. 令 desc 为 the PropertyDescriptor { [[Value]]: V, [[Writable]]: true, [[Enumerable]]: true, [[Configurable]]: D }.
6. 否则,
  1. 令 desc 为 the PropertyDescriptor { [[Value]]: V }.
7. 执行 ? DefinePropertyOrThrow(globalObject, N, desc).
8. 记录 ObjRec 中 N 的绑定已被初始化.
9. 执行 ? 设置(globalObject, N, V, false).
10. 令 varDeclaredNames 为 envRec.[[VarNames]].
11. 若 varDeclaredNames 不包含 N, 那么
    1. Append N 为 varDeclaredNames.
12. 返回 NormalCompletion(empty).

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

模块环境记录的具体环境记录方法GetBindingValue只是返回其绑定标识符的值，该标识符的名称为参数N的值。但是，如果绑定是间接绑定，则返回目标绑定的值。如果绑定存在但未初始化，则抛出ReferenceError。

1. 断言: S 是 true.
2. 令 envRec 为 为函数调用时对应的模块环境记录项.
3. 断言: envRec 有 N 的绑定.
4. 若 N 的绑定是间接绑定, 那么
a. 令M和N2为创建N的绑定时提供的间接值
b. 令 targetEnv 为 M.[[Environment]].
c. 若 targetEnv 是 undefined, 抛出 ReferenceError 异常.
d. 令 targetER 为 targetEnv's 环境记录项.
e. 返回 ? targetER.GetBindingValue(N2, true).
5. 若 envRec中N的绑定是未初始化绑定, 抛出 ReferenceError 异常.
6. 返回envRec中当前绑定到N的值。

> 注：因为模块始终是严格模式代码，所以S始终为true。

##### 8.1.1.5.2 DeleteBinding ( N ) <div id="sec-module-environment-records-deletebinding-n"></div>

模块环境记录的具体环境记录方法DeleteBinding拒绝删除绑定。

1. 断言: 此方法不会被调用. See 12.5.3.1.

> 注：模块环境记录仅在严格的代码内使用，并且早期错误规则可防止在严格的代码中将delete运算符应用于将解析为模块环境记录绑定的引用。见12.5.3.1。

##### 8.1.1.5.3 HasThisBinding ( ) <div id="sec-module-environment-records-hasthisbinding"></div>

模块环境记录提供this绑定。

1. 返回 true.

##### 8.1.1.5.4 GetThisBinding ( ) <div id="sec-module-environment-records-getthisbinding"></div>

1. 返回 undefined.

##### 8.1.1.5.5 CreateImportBinding ( N, M, N2 ) <div id="sec-createimportbinding"></div>

模块环境记录的具体环境记录方法CreateImportBinding为名称N创建一个新的初始化的不可变间接绑定。此环境记录中对于N的绑定必须已经不存在。M是模块记录，而N2是该绑定的名称存在于M的模块环境记录中。访问新绑定的值将间接访问目标绑定的绑定值。

1. 令 envRec 为 为函数调用时对应的模块环境记录项.
2. 断言: envRec 当前没有N的绑定.
3. 断言: M 是 模块记录项.
4. 断言: 实例化M。[[Environment]]时，它将直接绑定N2。
5. 在envRec中为N创建一个不变的间接绑定，该间接绑定引用M和N2作为其目标绑定，并记录该绑定已初始化。
6. 返回 NormalCompletion(empty).

### 8.1.2 词法环境操作 <div id="lexical-environment-operations"></div>

本规范中使用以下抽象操作在词汇环境上进行操作：

#### 8.1.2.1 GetIdentifierReference ( lex, name, strict ) <div id="sec-getidentifierreference"></div>

抽象操作GetIdentifierReference用词法环境lex，字符串name和布尔标志strict调用。lex的值可以为null。调用时，将执行以下步骤：

1. 若 lex 的值为 null, 那么
    1. 返回类型为Reference的值，其基值部分为undefined，其引用名称部分为name，其严格引用标志为strict。
2. 令 envRec 为 lex 的环境记录项.
3. 令 exists 为 ? envRec.HasBinding(name).
4. 若 exists 是 true, 那么
    1. 返回类型为Reference的值，其基值部分为envRec，其引用名称部分为name，其严格引用标志为strict。
5. 否则,
   1. 令 outer 为 lex 的外部环境引用的值.
   2. 返回 ? GetIdentifierReference(outer, name, strict).

#### 8.1.2.2 NewDeclarativeEnvironment ( E ) <div id="sec-newdeclarativeenvironment"></div>

当使用词法环境作为参数E调用抽象操作NewDeclarativeEnvironment时，将执行以下步骤：

1. 令 env 为 新的词法环境
2. 令 envRec 为 a new declarative Environment Record containing no bindings.
3. 设置 env's 环境记录项 为 envRec.
4. 设置 env的外部词法环境引用 为 E.
5. 返回 env.

#### 8.1.2.3 NewObjectEnvironment ( O, E ) <div id="sec-newobjectenvironment"></div>

以对象O和词法环境E作为参数调用抽象操作NewObjectEnvironment时，将执行以下步骤：

1. 令 env 为新的词法环境.
2. 令 envRec 为新对象环境记录项，其中包含O作为绑定对象
3. 设置 env's 环境记录项为 envRec.
4. 设置 env 的外部词法环境引用为 E.
5. 返回 env.

#### 8.1.2.4 NewFunctionEnvironment ( F, newTarget ) <div id="sec-newfunctionenvironment"></div>

当使用参数F和newTarget调用抽象操作NewFunctionEnvironment时，将执行以下步骤：

1. 断言: F 是 ECMAScript 函数.
2. 断言: Type(newTarget) 是 Undefined 或 Object.
3. 令 env 为新的词法环境.
4. 令 envRec 为 没有绑定的新函数环境记录项.
5. 设置 envRec.[[FunctionObject]] 为 F.
6. 若 F.[[ThisMode]] 是 lexical, 设置 envRec.[[ThisBindingStatus]] 为 "lexical".
7. 否则, 设置 envRec.[[ThisBindingStatus]] 为 "uninitialized".
8. 令 home 为 F.[[HomeObject]].
9. 设置 envRec.[[HomeObject]] 为 home.
10. 设置 envRec.[[NewTarget]] 为 newTarget.
11. 设置 env's 环境记录项为 envRec.
12. 设置 env 的外部词法环境记录项为 F.[[Environment]].
13. 返回 env.

#### 8.1.2.5 NewGlobalEnvironment ( G, thisValue ) <div id="sec-newglobalenvironment"></div>

当使用参数G和thisValue调用抽象操作NewGlobalEnvironment时，将执行以下步骤：

1. 令 env 为新的词法环境.

2. 令 objRec 为包含G作为绑定对象的新对象环境记录项.
3. 令dclRec为不包含绑定的新的声明性环境记录。
4. 令 globalRec 为新的全局环境记录项
5. 设置 globalRec.[[ObjectRecord]] 为 objRec.
6. 设置 globalRec.[[GlobalThisValue]] 为 thisValue.
7. 设置 globalRec.[[DeclarativeRecord]] 为 dclRec.
8. 设置 globalRec.[[VarNames]] 为空数组
9. 设置 env's 环境记录项为 globalRec.
10. 设置 env 的外部词法环境引用为 null.
11. 返回 env.

#### 8.1.2.6 NewModuleEnvironment ( E ) <div id="sec-newmoduleenvironment"></div>

当使用词法环境参数E调用抽象操作NewModuleEnvironment时，将执行以下步骤：

1. 令 env 为新的词法环境.
2. 令 envRec 为不包含绑定的新模块环境记录项。
3. 设置 env's 环境记录项 为 envRec.
4. 设置 env的外部词法环境引用为 E.
5. 返回 env.

## 8.2 作用域 <div id="realms"></div>

在执行之前，必须将所有ECMAScript代码与一个作用域相关联。从概念上讲，作用域由一组内部对象，一个ECMAScript全局环境，在该全局环境范围内加载的所有ECMAScript代码以及其他关联的状态和资源组成。

在本规范中，作用域以表20中指定的字段表示为作用域记录：

| 字段名           | 值                                                       | 含义                                                         |
| ---------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| [[Intrinsics]]   | 记录其字段名称是内在键且其值是对象的记录                 | 与该作用域相关联的代码使用的内在值                             |
| [[GlobalObject]] | Object                                                   | 此作用域的全局对象                                             |
| [[GlobalEnv]]    | 词法环境                                                 | 这个作用域的全局环境                                           |
| [[TemplateMap]]  | 一组 Record { [[Site]]: Parse Node, [[Array]]: Object }. | 使用其作用域记录的[[TemplateMap]]为每个作用域分别规范化模板对象。每个[[Site]]值都是作为TemplateLiteral的解析节点。关联的[[Array]]值是传递给标签函数的相应模板对象。<br />注：一旦解析节点变得不可访问，相应的[[Array]]也将不可访问，并且如果实现从[[TemplateMap]]列表中删除该对，则将不可观察。 |
| [[HostDefined]]  | 任何，默认值是不确定的。                                 | 保留该字段供需要将其他信息与作用域记录项关联的主机环境使用。   |

### 8.2.1 CreateRealm ( ) <div id="sec-createrealm"></div>

不带参数的抽象操作CreateRealm执行以下步骤：

1. 令 realmRec 为新的作用域记录项.
2. 执行 CreateIntrinsics(realmRec).
3. 设置 realmRec.[[GlobalObject]] 为 undefined.d
4. 设置 realmRec.[[GlobalEnv]] 为 undefined.
5. 设置 realmRec.[[TemplateMap]] 为新的空数组.
6. 返回 realmRec

### 8.2.2 CreateIntrinsics ( realmRec ) <div id="sec-createintrinsics"></div>

具有参数realmRec的抽象操作CreateIntrinsics执行以下步骤：

1. 令 intrinsics 为新的记录项.

2. 设置 realmRec.[[Intrinsics]] 为 intrinsics.
3. 令 objProto 为 ObjectCreate(null).
4. 设置 intrinsics.[[%ObjectPrototype%]] 为 objProto.
5.  令throwerSteps为9.2.9.1中为％ThrowTypeError％函数指定的算法步骤。 
6. 令 thrower 为 CreateBuiltinFunction(throwerSteps, « », realmRec, null).
7. 设置 intrinsics.[[%ThrowTypeError%]] 为 thrower.
8. 令 noSteps 为空的算法步骤序列。 
9. 令 funcProto 为 CreateBuiltinFunction(noSteps, « », realmRec, objProto).
10. 设置 intrinsics.[[%FunctionPrototype%]] 为 funcProto.
11. 调用 thrower.\[\[SetPrototypeOf]](funcProto).
12. 执行 AddRestrictedFunctionProperties(funcProto, realmRec).
13. 使用表7中列出的尚未设置的内部值设置内部字段。字段名称是表第一列中列出的名称。每个字段的值是一个新的对象值，该对象值完全并递归地填充有第18-26节中每个对象的规范所定义的属性值。所有对象属性值都是新创建的对象值。通过执行CreateBuiltinFunction（\<steps>，\<slots>，realmRec，\<prototype>）创建所有内置函数对象的值，其中\<steps>是此规范提供的该函数的定义，\<slots>是列表函数的指定内部插槽的名称（如果有的话）的名称，\<prototype>是函数的[[Prototype]]内部插槽的指定值。必须对内部函数及其属性的创建进行排序，以避免依赖于尚未创建的对象。
14.  返回 intrinsics.  

### 8.2.3 SetRealmGlobalObject ( realmRec, globalObj, thisValue ) <div id="sec-setrealmglobalobject"></div>

具有参数realmRec，globalObj和thisValue的抽象操作SetRealmGlobalObject执行以下步骤：

1. 若 globalObj 是 undefined, 那么
   1. 令 intrinsics 为 realmRec.[[Intrinsics]].
   2. 设置 globalObj 为 ObjectCreate(intrinsics.[[%ObjectPrototype%]]).
2. 断言: Type(globalObj) 是 Object.
3. 若 thisValue is undefined, 设置 thisValue 为 globalObj.
4. 设置 realmRec.[[GlobalObject]] 为 globalObj.
5. 令 newGlobalEnv 为 NewGlobalEnvironment(globalObj, thisValue).
6. 设置 realmRec.[[GlobalEnv]] 为 newGlobalEnv.
7. 返回 realmRec.

### 8.2.4 SetDefaultGlobalBindings ( realmRec ) <div id="sec-setdefaultglobalbindings"></div>

具有参数realmRec的抽象操作SetDefaultGlobalBindings执行以下步骤：

1. 令 global 为 realmRec.[[GlobalObject]].
2. 对于第18条中指定的全局对象的每个属性，执行
1. 令 name 为属性名的String值。
2. 令desc为该属性的完全填充的数据属性描述符，其中包含该属性的指定属性。对于在18.2、18.3或18.4中列出的属性，[[Value]]属性的值是realmRec中对应的固有对象。
3. 执行 ? DefinePropertyOrThrow(global, name, desc).
3. 返回 global.

## 8.3 可执行上下文 <div id="execution-contexts"></div>

执行上下文是一种规范，用于追踪ECMAScript规范的运行时代码。在任何时间点，每个实际执行代码的代理程序最多有一个执行上下文。这称为代理的运行执行上下文。本规范中对运行中执行上下文的所有引用均表示周围代理的运行中执行上下文。

执行上下文堆栈用于跟踪执行上下文。运行中的执行上下文始终是此堆栈的顶部元素。每当控制权从与当前正在运行的执行上下文关联的可执行代码转移到与该执行上下文不关联的可执行代码时，都会创建一个新的执行上下文。新创建的执行上下文被压入堆栈，并成为正在运行的执行上下文。

执行上下文包含跟踪其关联代码的执行进度所需的任何特定于实现的状态。每个执行上下文至少具有表21中列出的状态组件。

| 组件                  | 目的                                                         |
| --------------------- | ------------------------------------------------------------ |
| code evaluation state | 执行，暂停和恢复与该执行上下文相关联的代码的执行所需的任何状态。 |
| Function              | 如果这个执行环境正在执行函数对象的代码，那么这个组件的值就是该函数对象。 如果上下文执行脚本或模块的代码，则该值为null。 |
| Realm                 | 相关代码访问ECMAScript资源的作用域记录项。                     |
| ScriptOrModule        | 关联代码源自的模块记录或脚本记录。 如果没有原始脚本或模块，那么在 InitializeHostDefinedRealm，值为null |

通过运行的执行上下文对代码的评估可以在本规范中定义的各个点暂停。一旦正在运行的执行上下文已被挂起，则其他执行上下文可能会成为正在运行的执行上下文并开始评估其代码。在稍后的某个时间，挂起的执行上下文可能会再次变为运行中的执行上下文，并在先前被挂起的位置继续评估其代码。执行上下文之间正在运行的执行上下文状态的转换通常以类似堆栈的后进先出方式发生。但是，某些ECMAScript功能要求正在运行的执行上下文进行非LIFO转换。

正在运行的执行上下文的作用域组件的值也称为当前作用域记录。正在运行的执行上下文的Function组件的值也称为活动函数对象。

ECMAScript代码的执行上下文具有表22中列出的其他状态组件。

| 组件                | 目的                                                         |
| ------------------- | ------------------------------------------------------------ |
| LexicalEnvironment  | 标识用于解析在此执行上下文中由代码创建的标识符引用的词汇环境。 |
| VariableEnvironment | 标识其环境记录项保存由VariableStatements在此执行上下文中创建的绑定的词汇环境。 |

执行上下文的LexicalEnvironment和VariableEnvironment组件始终是词法环境

表示生成器对象评估的执行上下文具有表23中列出的其他状态组件。

| 组件      | 目的                                    |
| --------- | --------------------------------------- |
| Generator | 该执行上下文正在执行的GeneratorObject。 |

在大多数情况下，仅运行中的执行上下文（执行上下文堆栈的顶部）由该规范中的算法直接操作。因此，当使用术语“ LexicalEnvironment”和“ VariableEnvironment”而没有限定条件时，它们是指正在运行的执行上下文的那些组件。

执行上下文纯粹是一种规范机制，不需要与ECMAScript实现的任何特定工件相对应。ECMAScript代码不可能直接访问或观察执行上下文。

### 8.3.1 GetActiveScriptOrModule ( ) <div id="sec-getactivescriptormodule"></div>

GetActiveScriptOrModule抽象操作用于根据运行的执行上下文确定运行的脚本或模块。GetActiveScriptOrModule执行以下步骤：

1. 若执行上下文堆栈为空, 返回 null.
2. 令 ec 为 ScriptOrModule 组件不为 null 的执行上下文堆栈上的最高执行上下文。
3. 若不存在这样的执行上下文, 返回 null. Otherwise, 返回 ec的 ScriptOrModule 组件.

### 8.3.2 ResolveBinding ( name [ , env ]) <div id="sec-resolvebinding"></div>

ResolveBinding抽象操作用于确定作为字符串值传递的名称的绑定。可选参数env可用于显式提供要搜索绑定的词法环境。在执行ECMAScript代码期间，使用以下算法执行ResolveBinding：

1. 若 env 不存在或者 env 是 undefined, 那么
1. 设置 env 为运行时执行上下文的词法环境
2. 断言: env 是一个词法环境
3. 如果在严格模式代码中包含与要评估的语法产生匹配的代码，则令strict为true，否则令strict为false。
4. 返回 ? GetIdentifierReference(env, name, strict).

> 注：ResolveBinding的结果始终是一个Reference值，其引用的名称分量等于name参数。

### 8.3.3 GetThisEnvironment ( ) <div id="sec-getthisenvironment"></div>

抽象操作GetThisEnvironment查找当前提供关键字this绑定的环境记录。GetThisEnvironment执行以下步骤：

1. 令 lex 为 运行时执行上下文的词法环境
2. 重复,
1. 令 envRec 为 lex的环境记录项.
2. 令 exists 为 envRec.HasThisBinding().
3. 若 exists is true, 返回 envRec.
4. 令 outer 为 lex的外部环境引用的值
5. 断言: outer 不是null
6. 设置 lex 为 outer.

> 注：步骤2中的循环将始终终止，因为环境列表始终以具有this绑定的全局环境结尾

### 8.3.4 ResolveThisBinding ( ) <div id="sec-resolvethisbinding"></div>

抽象操作ResolveThisBinding使用正在运行的执行上下文的LexicalEnvironment确定关键字this的绑定。ResolveThisBinding执行以下步骤：

1. 令 envRec 为 GetThisEnvironment().
2. 返回 ? envRec.GetThisBinding().

### 8.3.5 GetNewTarget ( ) <div id="sec-getnewtarget"></div>

抽象操作GetNewTarget使用正在运行的执行上下文的LexicalEnvironment确定NewTarget值。GetNewTarget执行以下步骤：

1. 令 envRec 为 GetThisEnvironment().
2. 断言: envRec 有 [[NewTarget]] 字段.
3. 返回 envRec.[[NewTarget]].

### 8.3.6 GetGlobalObject ( ) <div id="sec-getglobalobject"></div>

抽象操作GetGlobalObject返回当前运行的执行上下文使用的全局对象。GetGlobalObject执行以下步骤：

1. 令 ctx 为运行时执行上下文
2. 令 currentRealm 为 ctx's 作用域.
3. 返回 currentRealm.[[GlobalObject]].

## 8.4 任务和任务队列 <div id="jobs-and-job-queues"></div>

Job是抽象操作，当目前没有其他ECMAScript计算正在进行时，它将启动ECMAScript计算。可以将任务抽象操作定义为接受任意一组任务参数。

仅当没有正在运行的执行上下文并且执行上下文堆栈为空时，才能启动任务的执行。PendingJob是对将来执行任务的请求。PendingJob是内部记录，其字段在表24中指定。启动任务的执行后，任务将始终执行到完成。当前正在运行的任务完成之前，无法启动其他任务。但是，当前正在运行的任务或外部事件可能会导致其他PendingJob排队，这些任务可能在当前正在运行的任务完成后的某个时间启动。

| 字段名             | 值                       | 含义                                                         |
| ------------------ | ------------------------ | ------------------------------------------------------------ |
| [[Job]]            | 任务抽象操作的名称       | 这是在启动此PendingJob的执行时执行的抽象操作。 任务是使用NextJob而不是返回表示它们已经完成的抽象操作。 |
| [[Arguments]]      | 一个数组                 | 激活时要传递给[[Job]]的参数值列表。                          |
| [[Realm]]          | 一个 作用域 记录           | 当此PendingJob启动时，用于初始执行上下文的作用域记录。         |
| [[ScriptOrModule]] | 脚本记录或者模块记录     | 当此PendingJob启动时，用于初始执行上下文的脚本或模块。       |
| [[HostDefined]]    | 任意，默认值为 undefined | 字段保留供主要环境使用，需要将附加信息与挂起的任务相关联。   |

任务队列是PendingJob记录的FIFO队列。每个任务队列都有一个名称，并且可用的任务队列的完整集合由ECMAScript实现定义。每个ECMAScript实现都至少具有表25中定义的任务队列。

每个代理都有其自己的一组命名任务队列。本规范中对命名任务队列的所有引用均表示周围代理的命名任务队列。

| 名称        | 目的                                                         |
| ----------- | ------------------------------------------------------------ |
| ScriptJobs  | 验证和执行ECMAScript脚本和模块源文本的作业。 见第10和15章节。 |
| PromiseJobs | 解决承诺(Promise)的作业（见25.4）。                          |

通过在任务队列上使包含任务抽象操作名称和任何必要参数值的PendingJob记录入队，来请求将来执行任务。当没有正在运行的执行上下文并且执行上下文堆栈为空时，ECMAScript实现将从任务队列中删除第一个PendingJob并使用其中包含的信息来创建执行上下文并开始执行关联的Job抽象操作

来自单个任务队列的PendingJob记录始终以FIFO顺序启动。该规范未定义服务多个任务队列的顺序。ECMAScript实现可以将任务队列的PendingJob记录的FIFO评估与一个或多个其他任务队列的PendingJob记录的评估交织在一起。一个实现必须定义在没有运行的执行上下文并且所有任务队列为空时会发生什么

> 注：通常，ECMAScript实现将使用至少一个PendingJob预先初始化其任务队列，并且其中一个任务将是第一个要执行的任务。如果当前任务完成并且所有任务队列为空，则实现可以选择释放所有资源并终止。或者，它可以选择等待某些特定于实现的代理或机制来排队新的PendingJob请求。

以下抽象操作用于创建和管理任务和任务队列：

### 8.4.1 EnqueueJob ( queueName, job, arguments ) <div id="sec-enqueuejob"></div>

EnqueueJob抽象操作需要三个参数：queueName，job和arguments。它执行以下步骤：

1. 断言: Type(queueName) 为String，其值为该实现识别的任务队列的名称。
2. 断言: job 是任务的名称.
3. 断言: arguments是一个List，其元素数与job所需的参数数相同。
4. 令 callerContext 为运行时执行上下文.
5. 令 callerRealm 为 callerContext's 作用域.
6. 令 callerScriptOrModule 为 callerContext's ScriptOrModule.
7. 令 pending 为 PendingJob { [[Job]]: job, [[Arguments]]: arguments, [[Realm]]: callerRealm, [[ScriptOrModule]]:
callerScriptOrModule, [[HostDefined]]: undefined }.
8. 执行任何实现或宿主环境定义的pending处理。这可能包括修改[[HostDefined]]字段或任何其他未决字段。
9. 在由queueName命名的任务队列的后面添加pending
10. 返回 NormalCompletion(empty).

## 8.5 InitializeHostDefinedRealm ( ) <div id="initializehostdefinedrealm"></div>

抽象操作InitializeHostDefinedRealm执行以下步骤：

1. 令 realm 为 CreateRealm().
2. 令 newContext 为新的执行上下文.
3. 设置 newContext 的函数为 null.
4. 设置 newContext 的作用域为 realm.
5. 设置 the ScriptOrModule of newContext 为 null.
6. 将newContext推入执行上下文堆栈；newContext现在是运行中的执行上下文
7. 如果宿主要求使用异类对象作为作用域的全局对象，则将global设为以实现定义的方式创建的对象。否则，请使global为undefined，表示应将普通对象创建为全局对象
8. 如果宿主要求作用域的全局范围中的this绑定返回除全局对象之外的对象，则使thisValue为以实现定义的方式创建的此类对象。否则，请使thisValue处于未定义状态，指示作用域的全局this绑定应为全局对象。
9. 执行 SetRealmGlobalObject(realm, global, thisValue).
10. 令 globalObj 为 ? SetDefaultGlobalBindings(realm).
11. 在globalObj上创建任何实现定义的全局对象属性。
12. 返回 NormalCompletion(empty).

## 8.6 RunJobs ( ) <div id="runjobs"></div>

抽象操作RunJobs执行以下步骤：

1. 执行 ? InitializeHostDefinedRealm().
2. 以依赖于实现的方式，获取ECMAScript源文本（请参见第10节）以及零个或多个ECMAScript脚本和/或ECMAScript模块的任何主机定义的关联值。对于每个这样的sourceText和hostDefined，执行
    1. 若 sourceText 是脚本的源码，那么
       1. 执行 EnqueueJob("ScriptJobs", ScriptEvaluationJob, « sourceText, hostDefined »).
    2. Else sourceText 是模块的源码
       1. 执行 EnqueueJob("ScriptJobs", TopLevelModuleEvaluationJob, « sourceText, hostDefined »).
3. 重复,
    1. 挂起正在运行的执行上下文，并将其从执行上下文堆栈中删除。
    2. 断言: 执行上下文堆栈现在为空。
    3. 令nextQueue为以实现定义的方式选择的非空任务队列。如果所有任务队列都为空，则结果是实现定义的。
    4. 令nextPending为nextQueue前面的PendingJob记录。从nextQueue中删除该记录。
    5. 令 newContext 为新的执行上下文.
    6. 设置 newContext's Function 为 null.
    7. 设置 newContext's Realm 为 nextPending.[[Realm]].
    8. 设置 newContext's ScriptOrModule 为 nextPending.[[ScriptOrModule]].
    9. 将newContext推入执行上下文堆栈；newContext现在是运行中的执行上下文。
    10. 使用nextPending执行任何实现或宿主环境定义的任务初始化
    11. 令result为使用nextPending。[[Arguments]]的元素作为其参数执行nextPending。[[Job]]命名的抽象操作的结果。
    12. 若 result 是 abrupt completion, 执行 HostReportErrors(« result.[[Value]] »)

## 8.7 代理 <div id="agents"></div>

代理包括一组ECMAScript执行上下文，一个执行上下文堆栈，一个正在运行的执行上下文，一组命名任务队列，一个代理记录和一个执行线程。除执行线程外，代理的组成部分完全属于该代理。

代理程序的执行线程独立于其他代理程序在代理程序的执行上下文上执行该代理程序的工作队列中的任务，除非多个线程可以将一个执行线程用作执行线程，前提是共享该线程的所有代理都不具有代理程序记录其[[CanBlock]]属性为true。

> 注：例如，某些Web浏览器跨浏览器窗口的多个不相关的选项卡共享一个执行线程。

当代理的执行线程执行代理任务队列中的任务时，该代理是这些任务中代码的周围代理。该代码使用周围的代理访问该代理内保存的规范级别执行对象：正在运行的执行上下文，执行上下文堆栈，命名的任务队列和代理记录的字段。

| 字段名                 | 值             | 含义                                                         |
| ---------------------- | -------------- | ------------------------------------------------------------ |
| [[LittleEndian]]       | Boolean        | 算法GetValueFromBuffer和SetValueInBuffer在需要时为isLittleEndian参数计算的默认值。选择取决于实现，并且应该是对实现最有效的选择。一旦观察到该值，就无法更改。 |
| [[CanBlock]]           | Boolean        | 确定代理是否可以阻止。                                       |
| [[Signifier]]          | 任何全局唯一值 | 在其代理群集中唯一标识代理。                                 |
| [[IsLockFree1]]        | Boolean        | 如果对一字节值的原子操作是无锁的，则为true，否则为false。    |
| [[IsLockFree2]]        | Boolean        | 如果对一字节值的原子操作是无锁的，则为true，否则为false。    |
| [[CandidateExecution]] | 候选执行记录   | 请参阅内存模型。                                             |

一旦代理群集中的任何代理观察到[[Signifier]]，[[IsLockFree1]]和[[IsLockFree2]]的值，它们就无法更改。

> 注2：[[IsLockFree1]]和[[IsLockFree2]]的值不一定由硬件确定，但也可能反映了实现选择，这些选择会随着时间的推移以及ECMAScript实现之间的变化而变化。
>
> 没有[[IsLockFree4]]属性：4字节原子操作始终是无锁的。
>
> 实际上，如果用任何类型的锁来实现原子操作，则该操作不是无锁的。无锁并不意味着无等待：完成无锁原子操作可能需要多少机器步骤没有上限。
>
> 大小为n的原子访问是无锁的，并不意味着有关大小为n的非原子访问的（感知）原子性，特别是，非原子访问仍可以作为几个单独的内存访问的序列来执行。有关详细信息，请参见ReadSharedMemory和WriteSharedMemory。
>
> 注3：代理是一种规范机制，不需要与ECMAScript实现的任何特定工件相对应。

### 8.7.1 AgentSignifier ( ) <div id="sec-agentsignifier"></div>

抽象操作AgentSignifier不接受任何参数。它执行以下步骤：

1. 令 AR 为周围代理的代理记录项
2. 返回 AR.[[Signifier]].

### 8.7.2 AgentCanSuspend ( ) <div id="sec-agentcansuspend"></div>

抽象操作AgentCanSuspend不接受任何参数。它执行以下步骤：

> 注：在某些环境中，暂停给定的代理可能并不合理。例如，在Web浏览器环境中，禁止挂起文档的主事件处理线程，同时仍然允许工作者的事件处理线程挂起，可能是合理的。

## 8.8 代理集群 <div id="agent-clusters"></div>

代理群集是可以通过在共享内存上进行操作进行通信的最大代理集。

> 注1：不同代理中的程序可能通过未指定的方式共享内存。至少，可以在群集中的代理之间共享SharedArrayBuffer对象的后备内存。
>
> 可能有一些代理可以通过消息传递进行通信，而这些消息不能共享内存。他们永远不在同一座席集群中

每个代理都完全属于一个代理群集。

> 注2：集群中的代理不必在某个特定时间点都处于活动状态。如果代理A创建另一个代理B，然后A终止并且B创建了代理C，则如果A可以与B共享一些内存并且B可以与C共享一些内存，则这三个代理位于同一群集中。

群集中所有代理的各自代理记录中的[[LittleEndian]]属性必须具有相同的值。

> 注3：如果代理群集中的不同代理具有不同的[[LittleEndian]]值，则很难将共享内存用于多字节数据。

群集中的所有代理程序在其各自的代理程序记录中的[[IsLockFree1]]属性必须具有相同的值；对于[[IsLockFree2]]属性也是如此。

群集中的所有代理程序在其各自的代理程序记录中的[[Signifier]]属性必须具有不同的值。

在代理不知情或不合作的情况下，嵌入可能会使其无效（停止前进）或激活（继续前进）。如果嵌入这样做，则一定不能使集群中的某些代理处于活动状态，而集群中的其他代理会无限期停用。

> 注4：前述限制的目的是避免由于另一个代理已被停用而导致代理死锁或饥饿的情况。例如，如果允许一个寿命独立于任何窗口中的文档的HTML共享工作程序与这样一个独立文档的专用工作程序共享内存，并且在专用工作程序保持不动的同时，将停用该文档及其专用工作程序。锁定（例如，文档被推入其窗口的历史记录中），然后共享工作器尝试获取该锁定，然后共享工作器将被阻塞，直到再次激活专用工作器为止（如果有的话）。同时，其他尝试从其他窗口访问共享工作者的工作者也会挨饿。
>
> 限制的含义是，不可能在嵌入中不属于同一挂起/唤醒集合的代理之间共享内存。

嵌入可能会在没有代理集群其他代理的任何先验知识或合作的情况下终止代理。如果代理不是通过自身或集群中其他代理的编程动作而是通过集群外部的作用来终止的，则嵌入必须选择以下两种策略之一：终止集群中的所有代理，或提供可靠的API允许群集中的代理进行协调，以便群集中至少有一个剩余成员能够检测到终止，终止数据包含足够的信息以标识已终止的代理。

> 注5：这种终止的例子有：操作系统或用户终止在单独进程中运行的代理；当每个代理程序资源记帐指示该代理程序失控时，嵌入本身将终止与其他代理程序一起在进程中运行的代理程序。

在集群中的任何代理对任何ECMAScript代码进行任何评估之前，将集群中所有代理的“代理记录”的[[CandidateExecution]]字段设置为初始候选执行。最初的候选执行是一个空的候选执行，其[[EventsRecords]]字段是一个列表，对于每个代理，该列表包含一个Agent事件记录，其[[AgentSignifier]]字段是该代理的指示符，并且其[[EventList]]和[[AgentSynchronizesWith]]字段为空列表

> 注6：代理群集中的所有代理在其代理记录[[候选执行]]字段中共享相同的候选执行。候选执行是内存模型使用的指定机制。

> 注7：代理集群是一种规范机制，不需要与ECMAScript实现的任何特定工件相对应。

## 8.9 前进进度 <div id="forward-progress"></div>

代理商要取得进步，就是要执行根据此规范的评估步骤。

当代理的正在运行的执行上下文无限期地同步等待外部事件时，该代理将被阻塞。在这种情况下，只有座席记录的[[CanBlock]]属性为true的座席才能被阻止。未被阻止的代理是未被阻止的代理。

实施必须确保：

​	每个具有专用执行线程的未阻塞代理程序最终都会在共享执行线程的一组代理程序中前进，一个代理程序最终会使代理程序向前前进，除非通过提供阻塞的显式API，否则该代理程序不会导致另一个代理程序被阻塞。

> 注：这与内存模型中的活动性保证一起，确保所有“ SeqCst”写入最终对于所有代理都是可见的。

```
description 词法环境是一个用于定义特定变量和函数标识符在 ECMAScript 代码的词法嵌套结构上关联关系的规范类型。一个词法环境由一个环境记录项和可能为空的外部词法环境引用构成。通常词法环境会与 ECMAScript 代码诸如 FunctionDeclaration、WithStatement 或者 TryStatement 的 Catch 块这样的特定句法结构相联系，且类似代码每次执行都会有一个新的词法环境被创建出来。
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,可执行代码和可执行上下文,lexical-environments
```