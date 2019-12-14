# 12 ECMAScript语言：表达式

## 12.1 标识符 <div id="sec-identifiers"></div>

**语法**

```
IdentifierReference[Yield, Await] :
    Identifier
    [~Yield] yield
    [~Await] await
BindingIdentifier[Yield, Await] :
    Identifier
    yield
    await
LabelIdentifier[Yield, Await] :
    Identifier
    [~Yield] yield
    [~Await] await
Identifier :
    IdentifierName but not ReservedWord
```

> 注意
>
> 在语法中允许yield和await作为BindingIdentifier，并在下面的静态语义中禁止使用yield和await，以防止在一下情况下自动插入分号：
>
> 令
>
> await 0;

### 12.1.1 静态语义：早期错误 <div id="sec-identifiers-static-semantics-early-errors"></div>

```
BindingIdentifier:Identifier
```

- 如果与此生产式匹配的代码包含在严格模式代码中，并且Identifier的StringValue是“ arguments”或“ eval”，则是语法错误。

```
IdentifierReference : yield
BindingIdentifier : yield
LabelIdentifier : yield
```

- 如果与此生产式匹配的代码包含在严格模式代码中，则是语法错误。

```
IdentifierReference : await
BindingIdentifier : await
LabelIdentifier : await
```

- 如果句法的目标符号是“模块”，则为语法错误。

```
BindingIdentifier : yield
```

- 如果此生产式具有[Yield]参数，则是语法错误。

```
BindingIdentifier : await
```

- 如果此生产式具有[Await]参数，则是语法错误。

```
IdentifierReference : Identifier
BindingIdentifier : Identifier
LabelIdentifier : Identifier
```

- 如果此生产具有[Yield]参数，并且Identifier的StringValue为“ yield”，则为语法错误。
- 如果此生产具有[Await]参数，并且Identifier的StringValue为“ await”，则为语法错误。

```
Identifier : IdentifierName but not ReservedWord
```

- 如果此短语包含在严格模式代码中，并且IdentifierName的StringValue为："implements", "interface", "令", "package", "private", "protected", "public", "static", 或 "yield"。
- 如果语法语法的目标符号为Module且IdentifierName的StringValue为“ await”，则为语法错误。
- 如果IdentifierName的StringValue与任何ReservedWord的StringValue相同的String值（除yield或await之外）均是语法错误。

> 注意：
>
> IdentifierName的StringValue规范化了IdentifierName中的所有Unicode转义序列，因此此类转义不能用于编写其码点序列与ReservedWord相同的Identifier

### 12.1.2 静态语义：绑定名称 <div id="sec-identifiers-static-semantics-boundnames"></div>

```
BindingIdentifier : Identifier
```

1. 返回一个新列表，其中包含标识符的StringValue。

```
BindingIdentifier : yield
```

1. 返回一个包含“ yield”的新列表。

```
BindingIdentifier : await
```

1. 返回一个包含“await”的新列表。

### 12.1.3 静态语义：AssignmentTargetType <div id="sec-identifiers-static-semantics-assignmenttargettype"></div>

```
IdentifierReference : Identifier
```

1. 如果此IdentifierReference包含在严格模式代码中，并且Identifier的StringValue是“ eval”或“ arguments”，则返回 strict。
2. 返回 simple

```
IdentifierReference : yield
```

1. 返回 simple

```
IdentifierReference : await
```

1. 返回 simple

### 12.1.4 静态语义：StringValue <div id="sec-identifiers-static-semantics-stringvalue"></div>

```
IdentifierReference : yield
BindingIdentifier : yield
LabelIdentifier : yield
```

1. 返回 "yield"。

```
IdentifierReference : await
BindingIdentifier : await
LabelIdentifier : await
```

1. 返回 "await".

```
Identifier : IdentifierName but not ReservedWord
```

1. 返回IdentifierName的StringValue

### 12.1.5 运行时语义：BindingInitialization <div id="sec-identifiers-runtime-semantics-bindinginitialization"></div>

具有参数 value和environment

> 注：为环境传递undefined，以指示应该使用PutValue操作来分配初始化值。某些非严格函数的var语句和形式参数列表就是这种情况（请参见9.2.15）。在这些情况下，在执行其初始值设定项之前会先挂起并预初始化词法绑定

```
BindingIdentifier : Identifier
```

1. 假设name为Identifier的StringValue。
2. 返回 ? InitializeBoundName(name, value, environment).

```
BindingIdentifier : yield
```

1. 返回 ? InitializeBoundName("yield", value, environment).

```
BindingIdentifier : await
```

2. 返回 ? InitializeBoundName("await", value, environment).

#### 12.1.5.1 运行时语义：InitializeBoundName ( name, value, environment ) <div id="sec-initializeboundname"></div>

1. 断言: Type(name) 是字符串.
2. 如果 enviroment 不是 undefined, 那么
    1. 令 env 为环境的EnvironmentRecord组件。
    2. 执行 env.InitializeBinding(name, value).
    3. 返回 NormalCompletion(undefined).
3. 否则,
1. 令 lhs 为 ResolveBinding(name).
2. 返回 ? PutValue(lhs, value)

### 12.1.6 运行时语义：Evaluation <div id="sec-identifiers-runtime-semantics-evaluation"></div>

```
IdentifierReference : Identifier
```

1. 返回 ? ResolveBinding(StringValue of Identifier).

```
IdentifierReference : yield
```

2. 返回 ? ResolveBinding("yield").

```
IdentifierReference : await
```

3. 返回 ? ResolveBinding("await").

> 注 1： 执行IdentifierReference的结果始终是Reference类型的值。
>
> 注 2：在非严格代码中，关键字yield可以用作标识符。执行IdentifierReference可以解决yield的绑定，就好像它是一个Identifier一样。早期错误限制可确保仅执行非严格代码。

## 12.2 主要表达式 <div id="sec-primary-expression"></div>

**语法**

```
PrimaryExpression[Yield, Await] :
    this
    IdentifierReference[?Yield, ?Await]
    Literal
    ArrayLiteral[?Yield, ?Await]
    ObjectLiteral[?Yield, ?Await]
    FunctionExpression
    ClassExpression[?Yield, ?Await]
    GeneratorExpression
    AsyncFunctionExpression
    AsyncGeneratorExpression
    RegularExpressionLiteral
    TemplateLiteral[?Yield, ?Await, ~Tagged]
    CoverParenthesizedExpressionAndArrowParameterList[?Yield, ?Await]
CoverParenthesizedExpressionAndArrowParameterList[Yield, Await] :
    ( Expression[+In, ?Yield, ?Await] )
    ( Expression[+In, ?Yield, ?Await] , )
    ( )
    ( ... BindingIdentifier[?Yield, ?Await] )
    ( ... BindingPattern[?Yield, ?Await] )
    ( Expression[+In, ?Yield, ?Await] , ... BindingIdentifier[?Yield, ?Await] )
    ( Expression[+In, ?Yield, ?Await] , ... BindingPattern[?Yield, ?Await] )
```

**补充语法**

处理生产实例时

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

使用以下语法完善CoverParenthesizedExpressionAndArrowParameterList的解释：

```
ParenthesizedExpression[Yield, Await] :
    ( Expression[+In, ?Yield, ?Await] )
```

### 12.2.1 语义 <div id="sec-primary-expression-semantics"></div>
#### 12.2.1.1 静态语义：CoveredParenthesizedExpression <div id="sec-static-semantics-coveredparenthesizedexpression"></div>

```
CoverParenthesizedExpressionAndArrowParameterList : ( Expression )
```

1. 返回涵盖 CoverParenthesizedExpressionAndArrowParameterList 的 ParenthesizedExpression

#### 12.2.1.2 静态语义：HasName <div id="sec-semantics-static-semantics-hasname"></div>

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 expr 为 CoverParenthesizedExpressionAndArrowParameterList 的 CoveredParenthesizedExpression。
2. 若 expr 的 IsFunctionDefinition 是 false, 返回 false.
3. 返回 expr 的 HasName.

#### 12.2.1.3 静态语义：IsFunctionDefinition <div id="sec-semantics-static-semantics-isfunctiondefinition"></div>

```
PrimaryExpression :
    this
    IdentifierReference
    Literal
    ArrayLiteral
    ObjectLiteral
    RegularExpressionLiteral
    TemplateLiteral
```

1. 返回 false.

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 expr 为CoverParenthesizedExpressionAndArrowParameterList的CoveredParenthesizedExpression
2. 返回expr的IsFunctionDefinition

#### 12.2.1.4 静态语义：IsIdentifierRef <div id="sec-semantics-static-semantics-isidentifierref"></div>

```
PrimaryExpression : IdentifierReference
```

1. 返回 true

```
PrimaryExpression :
    this
    Literal
    ArrayLiteral
    ObjectLiteral
    FunctionExpression
    ClassExpression
    GeneratorExpression
    AsyncFunctionExpression
    AsyncGeneratorExpression
    RegularExpressionLiteral
    TemplateLiteral
    CoverParenthesizedExpressionAndArrowParameterList
```

1. 返回 false.

#### 12.2.1.5 静态语义：AssignmentTargetType <div id="sec-semantics-static-semantics-assignmenttargettype"></div>

```
PrimaryExpression :
    this
    Literal
    ArrayLiteral
    ObjectLiteral
    FunctionExpression
    ClassExpression
    GeneratorExpression
    AsyncFunctionExpression
    AsyncGeneratorExpression
    RegularExpressionLiteral
    TemplateLiteral
```

1. 返回 invalid.

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 expr 为CoverParenthesizedExpressionAndArrowParameterList的CoveredParenthesizedExpression
2. 返回expr的AssignmentTargetType

### 12.2.2 this 关键字 <div id="sec-this-keyword"></div>

### 12.2.2.1 运行时语义：Evaluation <div id="sec-this-keyword-runtime-semantics-evaluation"></div>

```
PrimaryExpression : this
```

1. 返回 ? ResolveThisBinding().

### 12.2.3 标识符引用 <div id="sec-identifier-reference"></div>

See 12.1 for IdentifierReference.

### 12.2.4 字面量 <div id="sec-primary-expression-literals"></div>

语法

```
Literal :
    NullLiteral
    BooleanLiteral
    NumericLiteral
    StringLiteral
```

### 12.2.4.1 运行时语义：Evaluation <div id="sec-literals-runtime-semantics-evaluation"></div>

```
Literal : NullLiteral
```

1. 返回 null.

```
Literal : BooleanLiteral
```

1. 若 BooleanLiteral 是 token false, 返回 false.
2. 若 BooleanLiteral 是 token true, 返回 true.

```
Literal : NumericLiteral
```

1. 返回 NumericLiteral 的 MV（定义在11.8.3）值的数字

```
Literal : StringLiteral
```

1. 返回StringLiteral（定义在11.8.4.1）的StringValue。

### 12.2.5 数组初始化 <div id="sec-array-initializer"></div>
>注：ArrayLiteral是一个表达式，它使用一个列表描述一个Array对象的初始化。它是一个零个或者多个表达式的序列，其中每一个表示一个数组元素，并且用方括号括起来。元素并不一定要是字面量，每次数组初始化执行时它们都会被执行一次。

数组元素可能在元素列表的开始、结束，或者中间位置以逗号取代。当元素列表中的一个逗号的前面没有 AssignmentExpression（如，一个逗号在另一个逗号之前。）的情况下，缺失的数组元素仍然会对数组长度有贡献，并且增加后续元素的索引值。以逗号省略的数组元素是 undefined。假如元素在数组末尾被省略，那么元素不会贡献数组长度。

**语法**

```
ArrayLiteral[Yield, Await] :
    [ Elisionopt ]
    [ ElementList[?Yield, ?Await] ]
    [ ElementList[?Yield, ?Await] , Elisionopt ]
ElementList[Yield, Await] :
    Elisionopt AssignmentExpression[+In, ?Yield, ?Await]
    Elisionopt SpreadElement[?Yield, ?Await]
    ElementList[?Yield, ?Await] , Elisionopt AssignmentExpression[+In, ?Yield, ?Await]
    ElementList[?Yield, ?Await] , Elisionopt SpreadElement[?Yield, ?Await]
Elision :
    ,
    Elision ,
SpreadElement[Yield, Await] :
    ... AssignmentExpression[+In, ?Yield, ?Await]
```

#### 12.2.5.1 静态语义：ElisionWidth <div id="sec-static-semantics-elisionwidth"></div>

```
Elision : ,
```

1. 返回数学值1.

```
Elision : Elision ,
```

1. 令 preceding 为 Elision 的 ElisionWidth
2. 返回 preceding + 1.

#### 12.2.5.2 运行时语义：ArrayAccumulation <div id="sec-runtime-semantics-arrayaccumulation"></div>

带有参数array和nextIndex。

```
ElementList : Elision AssignmentExpression
```

1. 令 padding 为 Elision 的 ElisionWidth; 若 Elision 不存在, 使用数学值0.
2. 令 initResult 为 执行 AssignmentExpression 的结果.
3. 令 initValue 为 ? GetValue(initResult).
4. 令 created 为 CreateDataProperty(array, ToString(ToUint32(nextIndex + padding)), initValue).
5. 断言: created 是 true.
6. 返回 nextIndex + padding + 1.

```
ElementList : Elision SpreadElement
```

1. 令 padding 为Elision的ElisionWidth; 若 Elision 不存在, 使用数学值0
2. 返回对有 array 和 nextIndex + padding 的参数的 SpreadElement 执行 ArrayAccumulation 的结果。

```
ElementList : ElementList , Elision AssignmentExpression
```

1. 令 postIndex 为对带有参数array和nextIndex的ElementList执行ArrayAccumulation的结果。
2. ReturnIfAbrupt(postIndex).
3. 令 padding 为Elision的ElisionWidth; 若 Elision 不存在, 使用数学值0
4. 令 initResult 为执行 AssignmentExpression 的结果.
5. 令 initValue 为 ? GetValue(initResult).
6. 令 created 为 CreateDataProperty(array, ToString(ToUint32(postIndex + padding)), initValue).
7. 断言: created 是 true.
8. 返回 postIndex + padding + 1.

```
ElementList : ElementList , Elision SpreadElement
```

1. 令 postIndex 为对带有参数array和nextIndex的ElementList执行ArrayAccumulation的结果。
2. ReturnIfAbrupt(postIndex).
3. 令 padding 为Elision的ElisionWidth; 若 Elision 不存在, 使用数学值0
4. 返回使用参数array和postIndex + padding对SpreadElement执行ArrayAccumulation的结果。

```
SpreadElement : ... AssignmentExpression
```

1. 令 spreadRef 为执行AssignmentExpression的结果.
2. 令 spreadObj 为 ? GetValue(spreadRef).
3. 令 iteratorRecord 为 ? GetIterator(spreadObj).
4. 重复,
   1. 令 next 为 ? IteratorStep(iteratorRecord).
   2. 若 next 是 false, 返回 nextIndex.
   3. 令 nextValue 为 ? IteratorValue(next).
   4. 令 status 为 CreateDataProperty(array, ToString(ToUint32(nextIndex)), nextValue).
   5. 断言: status 是 true.
   6. nextIndex 加 1.

> 注：CreateDataProperty用于确保为数组定义了自己的属性，即使已修改标准内置Array原型对象的方式，也无法使用[[Set]]创建自己的新属性

#### 12.2.5.3 运行时语义：Evaluation <div id="sec-array-initializer-runtime-semantics-evaluation"></div>

```
ArrayLiteral : [ Elision ]
```

1. 令 array 为 ! ArrayCreate(0).
2. 令 pad 为 Elision 的 ElisionWidth; 若 Elision 不存在, 使用数学值0
3. 执行 Set(array, "length", ToUint32(pad), false).
4. 注意: 由于ArrayCreate返回的对象的性质，上述Set不会失败
5. 返回 array

```
ArrayLiteral : [ ElementList ]
```

1. 令 array 为 ! ArrayCreate(0).
2. 令 len 为对带有参数array和0的ElementList执行ArrayAccumulation的结果。
3. ReturnIfAbrupt(len).
4. 执行 Set(array, "length", ToUint32(len), false).
5. 注意: 由于ArrayCreate返回的对象的性质，上述Set不会失败
6. 返回 array.

```
ArrayLiteral : [ ElementList , Elision ]
```

1. 令 array 为 ! ArrayCreate(0).
2. 令 len 为对带有参数array和0的ElementList执行ArrayAccumulation的结果。
3. ReturnIfAbrupt(len).
4. 令 padding 为Elision的ElisionWidth; 若 Elision 不存在, 使用数学值0
5. 执行 Set(array, "length", ToUint32(padding + len), false).
6. 注意: 由于ArrayCreate返回的对象的性质，上述Set不会失败
7. 返回 array.

### 12.2.6 对象初始化 <div id="sec-object-initializer"></div>

> 注1：对象初始化程序是一种描述对象初始化的表达式，以类似于文字的形式编写。它是零对或更多对属性键和相关值的列表，并用大括号括起来。这些值不必是文字。每次执行对象初始值设定项时都对它们进行执行。

**语法**

```
ObjectLiteral[Yield, Await] :
    { }
    { PropertyDefinitionList[?Yield, ?Await] }
    { PropertyDefinitionList[?Yield, ?Await] , }
PropertyDefinitionList[Yield, Await] :
    PropertyDefinition[?Yield, ?Await]
    PropertyDefinitionList[?Yield, ?Await] , PropertyDefinition[?Yield, ?Await]
PropertyDefinition[Yield, Await] :
    IdentifierReference[?Yield, ?Await]
    CoverInitializedName[?Yield, ?Await]
    PropertyName[?Yield, ?Await] : AssignmentExpression[+In, ?Yield, ?Await]
    MethodDefinition[?Yield, ?Await]
    ... AssignmentExpression[+In, ?Yield, ?Await]
PropertyName[Yield, Await] :
    LiteralPropertyName
    ComputedPropertyName[?Yield, ?Await]
LiteralPropertyName :
    IdentifierName
    StringLiteral
    NumericLiteral
ComputedPropertyName[Yield, Await] :
    [ AssignmentExpression[+In, ?Yield, ?Await] ]
CoverInitializedName[Yield, Await] :
    IdentifierReference[?Yield, ?Await] Initializer[+In, ?Yield, ?Await]
Initializer[In, Yield, Await] :
    = AssignmentExpression[?In, ?Yield, ?Await]

```

> 注2：方法定义在14.3中定义。

> 注3：在某些情况下，ObjectLiteral用作覆盖语法，用于更严格的辅助语法。必须完全使用CoverInitializedName才能完全覆盖这些辅助语法。但是，在通常需要实际ObjectLiteral的正常情况下，使用这种产生方式会导致早期的语法错误。

#### 12.2.6.1 静态语义：Early Errors <div id="sec-object-initializer-static-semantics-early-errors"></div>

```
PropertyDefinition : MethodDefinition
```

- 如果MethodDefinition的HasDirectSuper为true，则为语法错误。

除了描述实际的对象初始化程序之外，ObjectLiteral生成还用作ObjectAssignmentPattern的覆盖语法，并且可以识别为CoverParenthesizedExpressionAndArrowParameterList的一部分。当在要求ObjectAssignmentPattern的上下文中出现ObjectLiteral时，则不会应用以下早期错误规则。此外，在最初解析CoverParenthesizedExpressionAndArrowParameterList或CoverCallExpressionAndAsyncArrowHead时，不会应用它们。

```
PropertyDefinition : CoverInitializedName
```

​	如果代码与此产生式匹配，则始终抛出语法错误。

> 注：存在这种产生式是为了使ObjectLiteral可以用作ObjectAssignmentPattern的覆盖语法。它不能在实际的对象初始化程序中发生。

#### 12.2.6.2 静态语义：ComputedPropertyContains <div id="sec-object-initializer-static-semantics-computedpropertycontains"></div>

带有参数符号。

```
PropertyName : LiteralPropertyName
```

1. 返回 false.

```
PropertyName : ComputedPropertyName
```

1. 返回包含symbol的ComputedPropertyName结果。

#### 12.2.6.3 静态语义：Contains <div id="sec-object-initializer-static-semantics-contains"></div>

带有symbol符号。

```
PropertyDefinition : MethodDefinition
```

1. 若 symbol 是 MethodDefinition, 返回 true.
2. 返回带symbol参数的MethodDefinition的ComputedPropertyContains结果

> 注：依赖于子结构的静态语义规则通常不会考虑函数定义。

```
LiteralPropertyName : IdentifierName
```

1. 若 symbol 是 ReservedWord, 返回 false.
2. 若 symbol 是一个 Identifier 并且 symbol 的 StringValue 和 IdentifierName 的 StringValue 值相同, 返回 true.
3. 返回 false.

#### 12.2.6.4 静态语义：IsComputedPropertyKey <div id="sec-static-semantics-iscomputedpropertykey"></div>

```
PropertyName : LiteralPropertyName
```

1. 返回 false.

```
PropertyName : ComputedPropertyName
```

1. 返回 true.

#### 12.2.6.5 静态语义：PropName <div id="sec-object-initializer-static-semantics-propname"></div>

```
PropertyDefinition : IdentifierReference
```

1. 返回IdentifierReference的StringValue

```
PropertyDefinition : ... AssignmentExpression
```

1. 返回 empty.

```
PropertyDefinition : PropertyName : AssignmentExpression
```

1. 返回PropertyName的PropName

```
LiteralPropertyName : IdentifierName
```

1. 返回IdentifierName的StringValue

```
LiteralPropertyName : StringLiteral
```

1. 返回字符串值，其码元是 StringLiteral 的SV。

```
LiteralPropertyName : NumericLiteral
```

1. 令 nbr 为组成NumericLiteral的值的结果。
2. 返回 ! ToString(nbr).

```
ComputedPropertyName : [ AssignmentExpression ]
```

1. 返回 empty

#### 12.2.6.6 静态语义：PropertyNameList <div id="sec-static-semantics-propertynamelist"></div>

```
PropertyDefinitionList : PropertyDefinition
```

1. 若 PropertyDefinition 的 PropName 是 empty, 返回一个新的空列表
2. 返回 a new List containing PropName of PropertyDefinition.

```
PropertyDefinitionList : PropertyDefinitionList , PropertyDefinition
```

1. 令 list 为PropertyDefinitionList的PropertyNameList
2. 若 PropertyDefinition 的 PropName  是 empty, 返回 list.
3. 将PropertyDefinition的PropName追加到列表的末尾。
4. 返回 list

#### 12.2.6.7 运行时语义：Evaluation <div id="sec-object-initializer-runtime-semantics-evaluation"></div>

```
ObjectLiteral : { }
```

1. 返回 ObjectCreate(%ObjectPrototype%).

```
ObjectLiteral :
    { PropertyDefinitionList }
    { PropertyDefinitionList , }
```

1. 令 obj 为 ObjectCreate(%ObjectPrototype%).
2. 执行 ? 带有参数 obj 和 true 的 PropertyDefinitionList 的 PropertyDefinitionEvaluation
3. 返回 obj.

```
LiteralPropertyName : IdentifierName
```

1. 返回 IdentifierName 的 StringValue。

```
LiteralPropertyName : StringLiteral
```

1. 返回 String值，其码元是StringLiteral的SV。

```
LiteralPropertyName : NumericLiteral
```

1. 令 nbr 为形成NumericLiteral的值的结果。
2. 返回 ! ToString(nbr).

```
ComputedPropertyName : [ AssignmentExpression ]
```

1. 令 exprValue 为AssignmentExpression的运算结果。
2. 令 propName 为 ? GetValue(exprValue).
3. 返回 ? ToPropertyKey(propName).

#### 12.2.6.8 运行时语义：PropertyDefinitionEvaluation <div id="sec-object-initializer-runtime-semantics-propertydefinitionevaluation"></div>

带 object 和 enumerable 的参数。

```
PropertyDefinitionList : PropertyDefinitionList , PropertyDefinition
```

1. 执行 ? 带有参数 object 和 enumerable 的 PropertyDefinitionList 的 PropertyDefinitionEvaluation.
2. 返回用参数 object 和 enumerable 执行 PropertyDefinition 的 PropertyDefinitionEvaluation 的结果。

```
PropertyDefinition : ... AssignmentExpression
```

1. 令 exprValue 为 AssignmentExpression 的运算结果.
2. 令 fromValue 为 ? GetValue(exprValue).
3. 令 excludedNames 为一个新的空列表
4. 返回 ? CopyDataProperties(object, fromValue, excludedNames).

```
PropertyDefinition : IdentifierReference
```

1. 令 propName 为 IdentifierReference 的 StringValue
2. 令 exprValue 为 IdentifierReference 的运算结果
3. 令 propValue 为 ? GetValue(exprValue).
4. 断言: enumerable 是 true.
5. 断言: object 是一个没有不可配置属性的普通可扩展对象
6. 返回 ! CreateDataPropertyOrThrow(object, propName, propValue).

```
PropertyDefinition : PropertyName : AssignmentExpression
```

1. 令 propKey 为PropertyName的运算结果
2. ReturnIfAbrupt(propKey).
3. 若 IsAnonymousFunctionDefinition(AssignmentExpression) 是 true, 那么
1. 令 propValue 为使用参数propKey为AssignmentExpression执行NamedEvaluation的结果。
4. 否则,
1. 令 exprValueRef 为AssignmentExpression的运算结果
2. 令 propValue 为 ? GetValue(exprValueRef).
5. 断言: enumerable 是 true.
6. 断言: object 是一个没有不可配置属性的普通可扩展对象
7. 返回 ! CreateDataPropertyOrThrow(object, propKey, propValue).

> 注：B.3.1中给出了这种生产的另一种语义。

### 12.2.7 函数定义表达式 <div id="sec-function-defining-expressions"></div>

有关PrimaryExpression：FunctionExpression，请参见14.1。

有关PrimaryExpression：GeneratorExpression，请参见14.4。

有关PrimaryExpression：ClassExpression，请参见14.6。

有关PrimaryExpression：AsyncFunctionExpression，请参见14.7。

有关PrimaryExpression：AsyncGeneratorExpression，请参见14.5。

### 12.2.8 正则表达式字面量 <div id="sec-primary-expression-regular-expression-literals"></div>

**语法**

参见 11.8.5

#### 12.2.8.1 静态语义：Early Errors <div id="sec-primary-expression-regular-expression-literals-static-semantics-early-errors"></div>

```
PrimaryExpression : RegularExpressionLiteral
```

- 如果无法使用21.2.1中指定的ECMAScript RegExp语法的目标符号模式识别RegularExpressionLiteral的BodyText，则会出现语法错误。
- 如果RegularExpressionLiteral的FlagText包含除“ g”，“ i”，“ m”，“ s”，“ u”或“ y”之外的任何代码点，或者包含的相同代码点多于一次，则是语法错误。

#### 12.2.8.2 运行时语义：Evaluation <div id="sec-regular-expression-literals-runtime-semantics-evaluation"></div>

```
PrimaryExpression : RegularExpressionLiteral
```

1. 令 pattern 为 String值，它由RegularExpressionLiteral的BodyText的每个代码点的UTF16Encoding组成。
2. 令 flags 为 String 值，该值由RegularExpressionLiteral的FlagText的每个代码点的UTF16Encoding组成。
3. 返回 RegExpCreate(pattern, flags).

### 12.2.9 模板字面量 <div id="sec-template-literals"></div>

语法

```
TemplateLiteral[Yield, Await, Tagged] :
    NoSubstitutionTemplate
    SubstitutionTemplate[?Yield, ?Await, ?Tagged]
SubstitutionTemplate[Yield, Await, Tagged] :
    TemplateHead Expression[+In, ?Yield, ?Await] TemplateSpans[?Yield, ?Await, ?Tagged]
TemplateSpans[Yield, Await, Tagged] :
    TemplateTail
    TemplateMiddleList[?Yield, ?Await, ?Tagged] TemplateTail
TemplateMiddleList[Yield, Await, Tagged] :
    TemplateMiddle Expression[+In, ?Yield, ?Await]
    TemplateMiddleList[?Yield, ?Await, ?Tagged] TemplateMiddle Expression[+In, ?Yield, ?Await]
```

#### 12.2.9.1 静态语义：Early Errors <div id="sec-static-semantics-template-early-errors"></div>

```
TemplateLiteral : NoSubstitutionTemplate
```

- 如果TemplateLiteral的TemplateStrings的结果中带有参数false的元素数大于2^32-1，则是语法错误。
- 如果未设置[Tagged]参数并且NoSubstitutionTemplate包含NotEscapeSequence，则为语法错误。

```
SubstitutionTemplate : TemplateHead Expression TemplateSpans
```

​	如果未设置[Tagged]参数并且TemplateHead包含NotEscapeSequence，则为语法错误。

```
TemplateSpans : TemplateTail
```

​	如果未设置[Tagged]参数并且TemplateTail包含NotEscapeSequence，则为语法错误。

```
TemplateMiddleList[Yield, Await, Tagged] :
    TemplateMiddle Expression[+In, ?Yield, ?Await]
    TemplateMiddleList[?Yield, ?Await, ?Tagged] TemplateMiddle Expression[+In, ?Yield, ?Await]
```

​	如果未设置[Tagged]参数并且TemplateMiddle包含NotEscapeSequence，则为语法错误。

#### 12.2.9.2 静态语义：TemplateStrings <div id="sec-static-semantics-templatestrings"></div>

带有参数raw。

```
TemplateLiteral : NoSubstitutionTemplate
```

1. 若 raw 是 false, 那么
1. 令 string 为NoSubstitutionTemplate的TV
2. 否则,
1. 令 string 为NoSubstitutionTemplate的TRV
3. 返回包含单个元素，string的List

```
SubstitutionTemplate : TemplateHead Expression TemplateSpans
```

1. 若 raw 是 false, 那么
1. 令 head 为TemplateHead的TV
2. 否则,
1. 令 head 为TemplateHead的TRV
3. 令 tail 为带有参数raw的TemplateSpans的TemplateStrings。
4. 返回一个包含头部的列表，其后依次包含尾部元素。

```
TemplateSpans : TemplateTail
```

1. 若 raw 是 false, 那么
1. 令 tail 为TemplateTail的TV
2. 否则,
1. 令 tail 为TemplateTail的TRV
3. 返回一个包含单个元素tail的列表。

```
TemplateSpans : TemplateMiddleList TemplateTail
```

1. 令 middle 为带参数 raw 的 TemplateMiddleList 的 TemplateStrings。
2. 若 raw 是 false, 那么
1. 令 tail 为TemplateTail的TV

3. 否则,
1. 令 tail 为TemplateTail的TRV
4. 返回一个列表，按顺序包含中间元素和尾部元素。

```
TemplateMiddleList : TemplateMiddle Expression
```

1. 若 raw 是 false, 那么
1. 令 string 为TemplateMiddle的TV
2. 否则,
1. 令 string 为TemplateMiddle的TRV
3. 返回一个包含单个元素字符串的列表。

```
TemplateMiddleList : TemplateMiddleList TemplateMiddle Expression
```

1. 令 front 为带参数 raw 的 TemplateMiddleList 的 TemplateStrings。
2. 若 raw 是 false, 那么
a. 令 last 为TemplateMiddle的TV
3. 否则,
a. 令 last 为TemplateMiddle的TRV
4. 将last作为List的最后一个元素追加。
5. 返回 front.

#### 12.2.9.3 运行时语义：ArgumentListEvaluation <div id="sec-template-literals-runtime-semantics-argumentlistevaluation"></div>

```
TemplateLiteral : NoSubstitutionTemplate
```

1. 令 templateLiteral 为这个 TemplateLiteral.
2. 令 siteObj 为 GetTemplateObject(templateLiteral).
3. 返回一个包含一个元素的列表，该元素是siteObj。

```
SubstitutionTemplate : TemplateHead Expression TemplateSpans
```

1. 令 templateLiteral 为这个 TemplateLiteral.
2. 令 siteObj 为 GetTemplateObject(templateLiteral).
3. 令 firstSubRef 为Expression的运算结果
4. 令 firstSub 为 ? GetValue(firstSubRef).
5. 令 restSub 为 TemplateSpans 的 SubstitutionEvaluation
6. ReturnIfAbrupt(restSub).
7. 断言: restSub 是一个List.
8. 返回一个列表，其第一个元素是siteObj，其第二个元素是firstSub，其后续元素是 restSub 的元素按顺序排列。 restSub可能不包含任何元素。

#### 12.2.9.4 运行时语义：GetTemplateObject ( templateLiteral ) <div id="sec-gettemplateobject"></div>

使用解析节点templateLiteral作为参数调用抽象操作GetTemplateObject。它执行以下步骤：

1. 令 rawStrings 为带参数 true 的 templateLiteral 的 TemplateStrings。
2. 令 realm 为当前作用域记录项。
3. 令 templateRegistry 为 realm.[[TemplateMap]].
4. 对每一个templateRegistry中的元素 e, 执行
    1. 若 e.[[Site]] 是与 templateLiteral 相同的解析节点, 那么
         1. 返回 e.[[Array]].
5. 令 cookedStrings 为 templateLiteral 的 TemplateStrings，参数为false。
6. 令 count 为列表中CookedStrings中元素的数量。
7. 断言: count ≤ 2^32 - 1.
8. 令 template 为 ! ArrayCreate(count).
9. 令 rawObj 为 ! ArrayCreate(count).
10. 令 index 为 0.
11. 重复, 当 index < count 时，
    1. 令 prop 为 ! ToString(index).
    2. 令 cookedValue 为 cookedStrings[index] 的字符串值.
    3. 调用 template.\[\[DefineOwnProperty]](prop, PropertyDescriptor { [[Value]]: cookedValue, [[Writable]]: false,[[Enumerable]]: true, [[Configurable]]: false }).
    4. 令 rawValue 为 rawStrings[index] 的字符串值.
    5. 调用 rawObj.\[\[DefineOwnProperty]](prop, PropertyDescriptor { [[Value]]: rawValue, [[Writable]]: false, [[Enumerable]]: true, [[Configurable]]: false }).
    6. index 增加1。
12. 执行 SetIntegrityLevel(rawObj, "frozen").
13. 调用 template.\[\[DefineOwnProperty]]("raw", PropertyDescriptor { [[Value]]: rawObj, [[Writable]]: false,
    [[Enumerable]]: false, [[Configurable]]: false }).
14. 执行 SetIntegrityLevel(template, "frozen").
15. 添加 Record { [[Site]]: templateLiteral, [[Array]]: template } 到 templateRegistry.
16. 返回 template.

> 注1：模板对象的创建不能导致突然完成。
>
> 注2：作用域的程序代码中的每个TemplateLiteral都与唯一的模板对象相关联，该对象用于执行带标签的模板（12.2.9.6）。模板对象被冻结，并且每次执行特定的标记模板时都使用相同的模板对象。模板对象是在对TemplateLiteral进行第一次执行时是懒惰地创建还是在第一次执行之前急切地创建，这是ECMAScript代码无法观察到的实现选择
>
> 注3：本规范的未来版本可能会定义模板对象的其他不可枚举的属性。

#### 12.2.9.5 运行时语义：SubstitutionEvaluation <div id="sec-runtime-semantics-substitutionevaluation"></div>

```
TemplateSpans : TemplateTail
```

1. 返回一个新的空列表

```
TemplateSpans : TemplateMiddleList TemplateTail
```

1. 返回 TemplateMiddleList 的 SubstitutionEvaluation 结果.

```
TemplateMiddleList : TemplateMiddle Expression
```

1. 令 subRef 为 Expression 的运算结果
2. 令 sub 为 ? GetValue(subRef).
3. 返回只包含sub的列表

```
TemplateMiddleList : TemplateMiddleList TemplateMiddle Expression
```

1. 令 preceding 为 TemplateMiddleList 的 SubstitutionEvaluation 的结果.
2. ReturnIfAbrupt(preceding).
3. 令 nextRef 为Expression的运算结果
4. 令 next 为 ? GetValue(nextRef).
5. 将next附加到List的最后一个元素的前面。
6. 返回 preceding.

#### 12.2.9.6 运行时语义：Evaluation <div id="sec-template-literals-runtime-semantics-evaluation"></div>

```
TemplateLiteral : NoSubstitutionTemplate
```

1. 返回字符串，其码元是 NoSubstitutionTemplate 元素的 TV（定义在11.8.6）。

```
SubstitutionTemplate : TemplateHead Expression TemplateSpans
```

1. 令 head 为 TemplateHead 的 TV（定义在11.8.6）。
2. 令 subRef 为Expression的运算结果
3. 令 sub 为 ? GetValue(subRef).
4. 令 middle 为 ? ToString(sub).
5. 令 tail 为TemplateSpans的运算结果
6. ReturnIfAbrupt(tail).
7. 返回 head，middle 和 tail 的字符串连接

> 注1：应用于Expression值的字符串转换语义类似于String.prototype.concat而不是+运算符。

```
TemplateSpans : TemplateTail
```

1. 令 tail 为 TemplateTail 的 TV（定义在11.8.6）
2. 返回由tail的码元组成的String值 

```
TemplateSpans : TemplateMiddleList TemplateTail
```

1. 令 head 为TemplateMiddleList的运算结果
2. ReturnIfAbrupt(head).
3. 令 tail 为 TemplateTail 的 TV（定义在11.8.6）
4. 返回 head 和 tail 的字符串连接.

```
TemplateMiddleList : TemplateMiddle Expression
```

1. 令 head 为 TemplateMiddle 的 TV（定义在11.8.6）
2. 令 subRef 为Expression的运算结果
3. 令 sub 为 ? GetValue(subRef).
4. 令 middle 为 ? ToString(sub).
5. 返回由head码元和middle元素组成的码元序列。

> 注2：应用于Expression值的字符串转换语义类似于String.prototype.concat而不是+运算符。

```
TemplateMiddleList : TemplateMiddleList TemplateMiddle Expression
```

1. 令 rest 为TemplateMiddleList的运算结果
2. ReturnIfAbrupt(rest).
3. 令 middle 为 the TV of TemplateMiddle as defined in 11.8.6.
4. 令 subRef 为Expression的运算结果
5. 令 sub 为 ? GetValue(subRef).
6. 令 last 为 ? ToString(sub).
7. 返回由rest元素组成的码元序列，其后是middle码元通过last的元素。

> 注3：应用于Expression值的字符串转换语义类似于String.prototype.concat而不是+运算符。

### 12.2.10 群组运算符 <div id="sec-grouping-operator"></div>

#### 12.2.10.1 静态语义：Early Errors <div id="sec-grouping-operator-static-semantics-early-errors"></div>

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

- 如果CoverParenthesizedExpressionAndArrowParameterList没有涵盖括号表达式，则是语法错误。

- 所有适用于括号表示的早期错误规则及其派生结果，也适用于CoverParenthesizedExpressionAndArrowParameterList的CoveredParenthesizedExpression。

#### 12.2.10.2 静态语义：IsFunctionDefinition <div id="sec-grouping-operator-static-semantics-isfunctiondefinition"></div>

```
ParenthesizedExpression : ( Expression )
```

1. 返回Expression的IsFunctionDefinition

#### 12.2.10.3 静态语义：AssignmentTargetType <div id="sec-grouping-operator-static-semantics-assignmenttargettype"></div>

```
ParenthesizedExpression : ( Expression )
```

1. 返回Expression的AssignmentTargetType

#### 12.2.10.4 运行时语义：NamedEvaluation <div id="sec-grouping-operator-runtime-semantics-namedevaluation"></div>

带有参数名称。

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 expr 为CoverParenthesizedExpressionAndArrowParameterList的CoveredParenthesizedExpression
2. 返回使用参数名称对expr执行NamedEvaluation的结果。

```
ParenthesizedExpression : ( Expression )
```

1. 断言: IsAnonymousFunctionDefinition(Expression) 是 true.

2. 返回使用参数名称执行Expression的NamedEvaluation的结果。

#### 12.2.10.5 运行时语义：Evaluation <div id="sec-grouping-operator-runtime-semantics-evaluation"></div>

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 expr 为CoverParenthesizedExpressionAndArrowParameterList的CoveredParenthesizedExpression
2. 返回expr的运算结果

```
ParenthesizedExpression : ( Expression )
```

1. 返回Expression的运算结果。这可能为引用类型。

> 注：该算法不会将GetValue应用于执行Expression的结果。这样做的主要动机是可以将诸如delete和typeof之类的运算符应用于带括号的表达式。

## 12.3 左值表达式 <div id="sec-left-hand-side-expressions"></div>

**语法**

```
MemberExpression[Yield, Await] :
    PrimaryExpression[?Yield, ?Await]
    MemberExpression[?Yield, ?Await] [ Expression[+In, ?Yield, ?Await] ]
    MemberExpression[?Yield, ?Await] . IdentifierName
    MemberExpression[?Yield, ?Await] TemplateLiteral[?Yield, ?Await, +Tagged]
    SuperProperty[?Yield, ?Await]
    MetaProperty
    new MemberExpression[?Yield, ?Await] Arguments[?Yield, ?Await]
SuperProperty[Yield, Await] :
    super [ Expression[+In, ?Yield, ?Await] ]
    super . IdentifierName
MetaProperty :
    NewTarget
NewTarget :
    new . target
NewExpression[Yield, Await] :
    MemberExpression[?Yield, ?Await]
    new NewExpression[?Yield, ?Await]
CallExpression[Yield, Await] :
    CoverCallExpressionAndAsyncArrowHead[?Yield, ?Await]
    SuperCall[?Yield, ?Await]
    CallExpression[?Yield, ?Await] Arguments[?Yield, ?Await]
    CallExpression[?Yield, ?Await] [ Expression[+In, ?Yield, ?Await] ]
    CallExpression[?Yield, ?Await] . IdentifierName
    CallExpression[?Yield, ?Await] TemplateLiteral[?Yield, ?Await, +Tagged]
SuperCall[Yield, Await] :
    super Arguments[?Yield, ?Await]
Arguments[Yield, Await] :
    ( )
    ( ArgumentList[?Yield, ?Await] )
    ( ArgumentList[?Yield, ?Await] , )
ArgumentList[Yield, Await] :
    AssignmentExpression[+In, ?Yield, ?Await]
    ... AssignmentExpression[+In, ?Yield, ?Await]
    ArgumentList[?Yield, ?Await] , AssignmentExpression[+In, ?Yield, ?Await]
    ArgumentList[?Yield, ?Await] , ... AssignmentExpression[+In, ?Yield, ?Await]
LeftHandSideExpression[Yield, Await] :
    NewExpression[?Yield, ?Await]
    CallExpression[?Yield, ?Await]
```

补充语法

处理生产CallExpression的实例时：CoverCallExpressionAndAsyncArrowHead，使用以下语法完善CoverCallExpressionAndAsyncArrowHead的解释：

```
CallMemberExpression[Yield, Await] :
    MemberExpression[?Yield, ?Await] Arguments[?Yield, ?Await]
```

### 12.3.1 静态语义 <div id="sec-static-semantics"></div>
#### 12.3.1.1 静态语义：CoveredCallExpression <div id="sec-left-hand-side-expressions-static-semantics-coveredcallexpression"></div>

```
CallExpression : CoverCallExpressionAndAsyncArrowHead
```

1. 返回 the CallMemberExpression that 是 covered by CoverCallExpressionAndAsyncArrowHead.

#### 12.3.1.2 静态语义：Contains <div id="sec-static-semantics-static-semantics-contains"></div>

带有参数symbol。

```
MemberExpression : MemberExpression . IdentifierName
```

1. 若 MemberExpression 包含 symbol 是 true, 返回 true.
2. 若 symbol 是 ReservedWord, 返回 false.
3. 若 symbol 是 Identifier，并且 symbol 的 StringValue 和 IdentifierName 的 StringValue 值相同 , 返回
true.
4. 返回 false

```
SuperProperty : super . IdentifierName
```

1. 若 symbol 是 ReservedWord super, 返回 true.
2. 若 symbol 是 ReservedWord, 返回 false.
3. 若 symbol 是 Identifier，并且 symbol 的 StringValue 和 IdentifierName 的 StringValue 值相同 , 返回
true.
4. 返回 false.

```
CallExpression : CallExpression . IdentifierName
```

1. 若 CallExpression 包含 symbol 是 true, 返回 true.
2. 若 symbol 是 ReservedWord, 返回 false.
3. 若 symbol 是 Identifier，并且 symbol 的 StringValue 和 IdentifierName 的 StringValue 值相同 , 返回
true.
4. 返回 false.

#### 12.3.1.3 静态语义：IsFunctionDefinition <div id="sec-static-semantics-static-semantics-isfunctiondefinition"></div>

```
MemberExpression :
    MemberExpression [ Expression ]
    MemberExpression . IdentifierName
    MemberExpression TemplateLiteral
    SuperProperty
    MetaProperty
new MemberExpression Arguments
NewExpression :
    new NewExpression
LeftHandSideExpression :
    CallExpression

```

1. 返回 false

#### 12.3.1.4 静态语义：IsDestructuring <div id="sec-static-semantics-static-semantics-isdestructuring"></div>

```
MemberExpression : PrimaryExpression
```

1. 若 PrimaryExpression 是 ObjectLiteral 或 ArrayLiteral, 返回 true.

2. 返回 false.

   ```
   MemberExpression :
       MemberExpression [ Expression ]
       MemberExpression . IdentifierName
       MemberExpression TemplateLiteral
       SuperProperty
       MetaProperty
       new MemberExpression Arguments
   NewExpression :
       new NewExpression
   LeftHandSideExpression :
       CallExpression
   ```

   1. 返回 false.

#### 12.3.1.5 静态语义：IsIdentifierRef <div id="sec-static-semantics-static-semantics-isidentifierref"></div>

```
MemberExpression :
    MemberExpression [ Expression ]
    MemberExpression . IdentifierName
    MemberExpression TemplateLiteral
    SuperProperty
    MetaProperty
    new MemberExpression Arguments
NewExpression :
    new NewExpression
LeftHandSideExpression :
    CallExpression
```

1. 返回 false.

#### 12.3.1.6 静态语义：AssignmentTargetType <div id="sec-static-semantics-static-semantics-assignmenttargettype"></div>

```
CallExpression :
    CallExpression [ Expression ]
    CallExpression . IdentifierName
MemberExpression :
    MemberExpression [ Expression ]
    MemberExpression . IdentifierName
    SuperProperty
```

1. 返回 simple.

```
CallExpression :
    CoverCallExpressionAndAsyncArrowHead
    SuperCall
    CallExpression Arguments
    CallExpression TemplateLiteral
NewExpression :
    new NewExpression
MemberExpression :
    MemberExpression TemplateLiteral
    new MemberExpression Arguments
NewTarget :
    new . target
```

1. 返回 invalid.

### 12.3.2 属性访问器 <div id="sec-property-accessors"></div>

> 注：使用点符号按名称访问属性：
>
> ```
> MemberExpression . IdentifierName
> CallExpression . IdentifierName
> ```
>
> 或方括号表示法：
>
> ```
> MemberExpression [ Expression ]
> CallExpression [ Expression ]
> ```
>
> 点符号通过以下语法转换进行解释：
>
> ```
> MemberExpression . IdentifierName
> ```
>
> 在行为上与
>
> ```
> MemberExpression [ <identifier-name-string> ]
> ```
>
> 同样
>
> ```
> CallExpression . IdentifierName
> ```
>
> 在行为上与
>
> ```
> CallExpression [ <identifier-name-string> ]
> ```
>
> 其中，\<identifier-name-string>是对IdentifierName的StringValue求值的结果。

#### 12.3.2.1 运行时语义：Evaluation <div id="sec-property-accessors-runtime-semantics-evaluation"></div>

```
MemberExpression : MemberExpression [ Expression ]
```

1. 令 baseReference 为MemberExpression的运算结果
2. 令 baseValue 为 ? GetValue(baseReference).
3. 令 propertyNameReference 为Expression的运算结果
4. 令 propertyNameValue 为 ? GetValue(propertyNameReference).
5. 令 bv 为 ? RequireObjectCoercible(baseValue).
6. 令 propertyKey 为 ? ToPropertyKey(propertyNameValue).
7. 若通过这个 MemberExpression 匹配到的是严格模式代码, 令 strict 为 true, 否则 令 strict 为 false.
8. 返回类型为Reference的值，其基本值成分为bv，其引用名称成分为 propertyKey，其严格引用标志为strict。

```
MemberExpression : MemberExpression . IdentifierName
```

1. 令 baseReference 为MemberExpression的运算结果
2. 令 baseValue 为 ? GetValue(baseReference).
3. 令 bv 为 ? RequireObjectCoercible(baseValue).
4. 令 propertyNameString 为IdentifierName的StringValue
5. 若通过这个 MemberExpression 匹配到的是严格模式代码, 令 strict 为 true, 否则 令 strict 为 false.
6. 返回类型为Reference的值，其基本值成分为bv，其引用名称成分为 propertyKey，其严格引用标志为strict。

```
CallExpression : CallExpression [ Expression ]
```

除了所包含的CallExpression在步骤1中求值，以与MemberExpression：MemberExpression [Expression]完全相同的方式求值。

```
CallExpression : CallExpression . IdentifierName
```

除了所包含的CallExpression在步骤1中求值，以与MemberExpression：MemberExpression . IdentifierName 完全相同的方式求值。

### 12.3.3 new 运算符 <div id="sec-new-operator"></div>

#### 12.3.3.1 运行时语义：Evaluation <div id="sec-new-operator-runtime-semantics-evaluation"></div>

```
NewExpression : new NewExpression
```

1. 返回 ? EvaluateNew(NewExpression, empty)

```
MemberExpression : new MemberExpression Arguments
```

1. 返回 ? EvaluateNew(MemberExpression, Arguments).

##### 12.3.3.1.1 运行时语义：EvaluateNew ( constructExpr, arguments ) <div id="sec-evaluatenew"></div>

抽象操作EvaluateNew带有参数ConstructExpr，而参数执行以下步骤：

1. 断言: constructExpr 是 NewExpression 或 MemberExpression.
2. 断言: arguments 是 empty 或 Arguments.
3. 令 ref 为 constructExpr 的运算结果
4. 令 constructor 为 ? GetValue(ref).
5. 若 arguments 是 empty, 令 argList 为一个新的空列表
6. 否则,
a. 令 argList 为arguments的ArgumentListEvaluation
b. ReturnIfAbrupt(argList).
7. 若 IsConstructor(constructor) 是 false,抛出 TypeError 异常.
8. 返回 ? Construct(constructor, argList).

### 12.3.4 函数回调 <div id="sec-function-calls"></div>
#### 12.3.4.1 运行时语义：Evaluation <div id="sec-function-calls-runtime-semantics-evaluation"></div>

```
CallExpression : CoverCallExpressionAndAsyncArrowHead
```

1. 令 expr 为CoverCallExpressionAndAsyncArrowHead的CoveredCallExpression
2. 令 memberExpr 为expr的MemberExpression
3. 令 arguments 为expr的Arguments
4. 令 ref 为memberExpr的运算结果
5. 令 func 为 ? GetValue(ref).
6. 若 Type(ref) 是 Reference 同时 IsPropertyReference(ref) 是 false 同时 GetReferencedName(ref) 是 "eval", 那么
   1. 若 SameValue(func, %eval%) 是 true, 那么
      2. 令 argList 为 ? arguments的ArgumentListEvaluation.
      2. 若 argList 没有元素, 返回 undefined.
      3. 令 evalText 为 argList 的第一个元素.
      4. 若匹配这个 CallExpression 的源代码是严格模式代码, 令 strictCaller 为 true. 否则，令 strictCaller 为 false.
      5. 令 evalRealm 为当前作用域记录项.
      6. 执行 ? HostEnsureCanCompileStrings(evalRealm, evalRealm).
      7. 返回 ? PerformEval(evalText, evalRealm, strictCaller, true).
7. 令 thisCall 为这个 CallExpression.
8. 令 tailCall 为 IsInTailPosition(thisCall).
9. 返回 ? EvaluateCall(func, ref, arguments, tailCall).

执行步骤6.1.7的CallExpression执行是直接执行。

```
CallExpression : CallExpression Arguments
```

1. 令 ref 为CallExpression的运算结果
2. 令 func 为 ? GetValue(ref).
3. 令 thisCall 为 this CallExpression.
4. 令 tailCall 为 IsInTailPosition(thisCall).
5. 返回 ? EvaluateCall(func, ref, Arguments, tailCall).

#### 12.3.4.2 运行时语义：EvaluateCall ( func, ref, arguments, tailPosition ) <div id="sec-evaluatecall"></div>

抽象操作EvaluateCall将值func，值ref，解析节点参数和布尔参数tailPosition用作参数。它执行以下步骤：

1. 若 Type(ref) 是 Reference, 那么
1. 若 IsPropertyReference(ref) 是 true, 那么
  1. 令 thisValue 为 GetThisValue(ref).
2.  否则 ref 的基础是环境记录项,
  1. 令 refEnv 为 GetBase(ref).
  2. 令 thisValue 为 refEnv.WithBaseObject().
2. 否则 Type(ref) 不是 Reference,
  1. 令 thisValue 为 undefined.
3. 令 argList 为arguments的ArgumentListEvaluation
4. ReturnIfAbrupt(argList).
5. 若 Type(func) 不是 Object,抛出 TypeError 异常.
6. 若 IsCallable(func) 是 false,抛出 TypeError 异常.
7. 若 tailPosition 是 true, 执行 PrepareForTailCall().
8. 令 result 为 Call(func, thisValue, argList).
9. 断言: 若 tailPosition 是 true, 上述回调不会在此处返回, 相反，运算将会继续，就像以下的返回已经发生。
10. 断言: 若 result 不是 abrupt completion, 那么 Type(result) 是 ECMAScript 语言类型.
11. 返回 result.

### 12.3.5super 关键字 <div id="sec-super-keyword"></div>
#### 12.3.5.1 运行时语义：Evaluation <div id="sec-super-keyword-runtime-semantics-evaluation"></div>

```
SuperProperty : super [ Expression ]
```

1. 令 env 为 GetThisEnvironment().
2. 令 actualThis 为 ? env.GetThisBinding().
3. 令 propertyNameReference 为Expression的运算结果
4. 令 propertyNameValue 为 ? GetValue(propertyNameReference).
5. 令 propertyKey 为 ? ToPropertyKey(propertyNameValue).
6. 若通过这个 SuperProperty 匹配到的是严格模式代码, 令 strict 为 true, 否则 令 strict 为 false.
7. 返回 ? MakeSuperPropertyReference(actualThis, propertyKey, strict).

```
SuperProperty : super . IdentifierName
```

1. 令 env 为 GetThisEnvironment().
2. 令 actualThis 为 ? env.GetThisBinding().
3. 令 propertyKey 为IdentifierName的StringValue
4. 若通过这个 SuperProperty 匹配到的是严格模式代码, 令 strict 为 true, 否则 令 strict 为 false.
5. 返回 ? MakeSuperPropertyReference(actualThis, propertyKey, strict).

```
SuperCall : super Arguments
```

1. 令 newTarget 为 GetNewTarget().
2. 断言: Type(newTarget) 是 Object.
3. 令 func 为 ? GetSuperConstructor().
4. 令 argList 为Arguments的ArgumentListEvaluation
5. ReturnIfAbrupt(argList).
6. 令 result 为 ? Construct(func, argList, newTarget).
7. 令 thisER 为 GetThisEnvironment().
8. 返回 ? thisER.BindThisValue(result).

#### 12.3.5.2 运行时语义：GetSuperConstructor ( ) <div id="sec-getsuperconstructor"></div>

抽象操作GetSuperConstructor执行以下步骤：

1. 令 envRec 为 GetThisEnvironment().
2. 断言: envRec 是 a function Environment Record.
3. 令 activeFunction 为 envRec.[[FunctionObject]].
4. 断言: activeFunction 是 an ECMAScript function object.
5. 令 superConstructor 为 ! activeFunction.\[\[GetPrototypeOf]]().
6. 若 IsConstructor(superConstructor) 是 false,抛出 TypeError 异常.
7. 返回 superConstructor

#### 12.3.5.3 运行时语义：MakeSuperPropertyReference ( actualThis, propertyKey, strict ) <div id="sec-makesuperpropertyreference"></div>

抽象操作MakeSuperPropertyReference带有参数ActualThis，propertyKey和strict执行以下步骤：

1. 令 env 为 GetThisEnvironment().
2. 断言: env.HasSuperBinding() 是 true.
3. 令 baseValue 为 ? env.GetSuperBase().
4. 令 bv 为 ? RequireObjectCoercible(baseValue).
5. 返回类型为Reference的值，它是super引用，其基本值部分是bv，其引用名称部分为propertyKey，其thisValue部分为ActualThis，其严格引用标志为strict

### 12.3.6 参数列表 <div id="sec-argument-lists"></div>

> 注：对参数列表的求值将生成值列表。

#### 12.3.6.1 运行时语义：ArgumentListEvaluation <div id="sec-argument-lists-runtime-semantics-argumentlistevaluation"></div>

```
Arguments : ( )
```

1. 返回一个新的空列表

```
ArgumentList : AssignmentExpression
```

1. 令 ref 为AssignmentExpression的运算结果
2. 令 arg 为 ? GetValue(ref).
3. 返回唯一项目是 arg的列表.

```
ArgumentList : ... AssignmentExpression
```

1. 令 list 为一个新的空列表
2. 令 spreadRef 为AssignmentExpression的运算结果
3. 令 spreadObj 为 ? GetValue(spreadRef).
4. 令 iteratorRecord 为 ? GetIterator(spreadObj).
5. 重复,
1. 令 next 为 ? IteratorStep(iteratorRecord).
2. 若 next 是 false, 返回 list.
3. 令 nextArg 为 ? IteratorValue(next).
4. 添加nextArg到列表的最后一个元素

```
ArgumentList : ArgumentList , AssignmentExpression
```

1. 令 precedingArgs 为ArgumentList的ArgumentListEvaluation
2. ReturnIfAbrupt(precedingArgs).
3. 令 ref 为AssignmentExpression的运算结果
4. 令 arg 为 ? GetValue(ref).
5. 添加 arg 到 precedingArgs 的末尾.
6. 返回 precedingArgs.

```
ArgumentList : ArgumentList , ... AssignmentExpression
```

1. 令 precedingArgs 为ArgumentList的ArgumentListEvaluation
2. ReturnIfAbrupt(precedingArgs).
3. 令 spreadRef 为AssignmentExpression的运算结果
4. 令 iteratorRecord 为 ? GetIterator(? GetValue(spreadRef)).
5. 重复,
    1. 令 next 为 ? IteratorStep(iteratorRecord).
    2. 若 next 是 false, 返回 precedingArgs.
    3. 令 nextArg 为 ? IteratorValue(next).
    4. 添加 nextArg 到 precedingArgs 的最后一个元素.

### 12.3.7 标记模板 <div id="sec-tagged-templates"></div>

> 注：带标签的模板是一个函数调用，其中调用的参数是从TemplateLiteral（12.2.9）派生的。实际参数包括模板对象（12.2.9.4）和通过执行嵌入在TemplateLiteral中的表达式产生的值。

#### 12.3.7.1 运行时语义：Evaluation <div id="sec-tagged-templates-runtime-semantics-evaluation"></div>

```
MemberExpression : MemberExpression TemplateLiteral
```

1. 令 tagRef 为MemberExpression的运算结果
2. 令 tagFunc 为 ? GetValue(tagRef).
3. 令 thisCall 为这个 MemberExpression.
4. 令 tailCall 为 IsInTailPosition(thisCall).
5. 返回 ? EvaluateCall(tagFunc, tagRef, TemplateLiteral, tailCall).

```
CallExpression : CallExpression TemplateLiteral
```

1. 令 tagRef 为CallExpression的运算结果
2. 令 tagFunc 为 ? GetValue(tagRef).
3. 令 thisCall 为 这个 CallExpression.
4. 令 tailCall 为 IsInTailPosition(thisCall).
5. 返回 ? EvaluateCall(tagFunc, tagRef, TemplateLiteral, tailCall).

### 12.3.8 元属性 <div id="sec-meta-properties"></div>

#### 12.3.8.1 运行时语义：Evaluation <div id="sec-meta-properties-runtime-semantics-evaluation"></div>

```
NewTarget : new . target
```

1. 返回 GetNewTarget().

## 12.4 更新表达式 <div id="sec-update-expressions"></div>

**语法**

```
UpdateExpression[Yield, Await] :
    LeftHandSideExpression[?Yield, ?Await]
    LeftHandSideExpression[?Yield, ?Await] [no LineTerminator here] ++
    LeftHandSideExpression[?Yield, ?Await] [no LineTerminator here] --
    ++ UnaryExpression[?Yield, ?Await]
    -- UnaryExpression[?Yield, ?Await]
```

### 12.4.1 静态语义：Early Errors <div id="sec-update-expressions-static-semantics-early-errors"></div>

```
UpdateExpression :
    LeftHandSideExpression ++
    LeftHandSideExpression --
```

如果LeftHandSideExpression的AssignmentTargetType是invalid，则是早期引用错误。

如果LeftHandSideExpression的AssignmentTargetType是strict，则这是早期的语法错误。

```
UpdateExpression :
    ++ UnaryExpression
    -- UnaryExpression
```

如果UnaryExpression的AssignmentTargetType是invalid，则是早期引用错误。

如果UnaryExpression的AssignmentTargetType是strict，则这是早期的语法错误。

### 12.4.2 静态语义：IsFunctionDefinition <div id="sec-update-expressions-static-semantics-isfunctiondefinition"></div>

```
UpdateExpression :
    LeftHandSideExpression ++
    LeftHandSideExpression --
    ++ UnaryExpression
    -- UnaryExpression
```

1. 返回 false.

### 12.4.3 静态语义：AssignmentTargetType <div id="sec-update-expressions-static-semantics-assignmenttargettype"></div>

```
UpdateExpression :
    LeftHandSideExpression ++
    LeftHandSideExpression --
    ++ UnaryExpression
    -- UnaryExpression
```

1. 返回 invalid.

### 12.4.4 后缀自增运算符 <div id="sec-postfix-increment-operator"></div>
#### 12.4.4.1 运行时语义：Evaluation <div id="sec-postfix-increment-operator-runtime-semantics-evaluation"></div>

```
UpdateExpression : LeftHandSideExpression ++
```

1. 令 lhs 为LeftHandSideExpression的运算结果
2. 令 oldValue 为 ? ToNumber(? GetValue(lhs)).
3. 令 newValue 为使用与+运算符相同的规则将值1添加到oldValue的结果（参见 12.8.5）
4. 执行 ? PutValue(lhs, newValue).
5. 返回 oldValue

### 12.4.5 后缀自减运算符 <div id="sec-postfix-decrement-operator"></div>
#### 12.4.5.1 运行时语义：Evaluation <div id="sec-postfix-decrement-operator-runtime-semantics-evaluation"></div>

```
UpdateExpression : LeftHandSideExpression --
```

1. 令 lhs 为LeftHandSideExpression的运算结果
2. 令 oldValue 为 ? ToNumber(? GetValue(lhs)).
3. 令 newValue使用与-运算符相同的规则将值1添加到oldValue的结果（参见 12.8.5）
4. 执行 ? PutValue(lhs, newValue).
5. 返回 oldValue.

### 12.4.6 前缀自增运算符 <div id="sec-prefix-increment-operator"></div>
#### 12.4.6.1 运行时语义：Evaluation <div id="sec-prefix-increment-operator-runtime-semantics-evaluation"></div>

```
UpdateExpression : ++ UnaryExpression
```

1. 令 expr 为UnaryExpression的运算结果
2. 令 oldValue 为 ? ToNumber(? GetValue(expr)).
3. 令 newValue 为使用与+运算符相同的规则将值1添加到oldValue的结果（参见 12.8.5）
4. 执行 ? PutValue(expr, newValue).
5. 返回 newValue.

### 12.4.7 前缀自减运算符 <div id="sec-prefix-decrement-operator"></div>
#### 12.4.7.1 运行时语义：Evaluation <div id="sec-prefix-decrement-operator-runtime-semantics-evaluation"></div>

```
UpdateExpression : -- UnaryExpression
```

1. 令 expr 为UnaryExpression的运算结果
2. 令 oldValue 为 ? ToNumber(? GetValue(expr)).
3. 令 newValue 为与-运算符相同的规则将值1添加到oldValue的结果（参见 12.8.5）
4. 执行 ? PutValue(expr, newValue).
5. 返回 newValue.

## 12.5 一元运算符 <div id="sec-unary-operators"></div>

**语法**

```
UnaryExpression[Yield, Await] :
    UpdateExpression[?Yield, ?Await]
    delete UnaryExpression[?Yield, ?Await]
    void UnaryExpression[?Yield, ?Await]
    typeof UnaryExpression[?Yield, ?Await]
    + UnaryExpression[?Yield, ?Await]
    - UnaryExpression[?Yield, ?Await]
    ~ UnaryExpression[?Yield, ?Await]
    ! UnaryExpression[?Yield, ?Await]
    [+Await] AwaitExpression[?Yield]
```

### 12.5.1 静态语义：IsFunctionDefinition <div id="sec-unary-operators-static-semantics-isfunctiondefinition"></div>

```
UnaryExpression :
    delete UnaryExpression
    void UnaryExpression
    typeof UnaryExpression
    + UnaryExpression
    - UnaryExpression
    ~ UnaryExpression
    ! UnaryExpression
    AwaitExpression
```

1. 返回 false.

### 12.5.2 静态语义：AssignmentTargetType <div id="sec-unary-operators-static-semantics-assignmenttargettype"></div>

```
UnaryExpression :
    delete UnaryExpression
    void UnaryExpression
    typeof UnaryExpression
    + UnaryExpression
    - UnaryExpression
    ~ UnaryExpression
    ! UnaryExpression
    AwaitExpression
```

1. 返回 invalid

### 12.5.3delete 运算符 <div id="sec-delete-operator"></div>
#### 12.5.3.1 静态语义：Early Errors <div id="sec-delete-operator-static-semantics-early-errors"></div>

```
UnaryExpression : delete UnaryExpression
```

如果严格模式代码中包含UnaryExpression，并且派生的UnaryExpression为，则会出现语法错误。

```
PrimaryExpression : IdentifierReference .
```

如果派生的UnaryExpression为

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

和CoverParenthesizedExpressionAndArrowParameterList最终派生一个短语，如果代替UnaryExpression使用，则会根据这些规则产生语法错误。该规则是递归应用的。

> 注意
>
> 最后一条规则意味着诸如delete（（（（foo）））之类的表达式会由于第一个规则的递归应用而产生早期错误。

#### 12.5.3.2 运行时语义：Evaluation <div id="sec-delete-operator-runtime-semantics-evaluation"></div>

```
UnaryExpression : delete UnaryExpression
```

1. 令 ref 为UnaryExpression的运算结果
2. ReturnIfAbrupt(ref).
3. 若 Type(ref) 不是 Reference, 返回 true.
4. 若 IsUnresolvableReference(ref) 是 true, 那么
1. 断言: IsStrictReference(ref) 是 false.
2. 返回 true.
5. 若 IsPropertyReference(ref) 是 true, 那么
  1. 若 IsSuperReference(ref) 是 true,抛出 ReferenceError 异常.
  2. 令 baseObj 为 ! ToObject(GetBase(ref)).
  3. 令 deleteStatus 为 ? baseObj.\[\[Delete]](GetReferencedName(ref)).
  4. 若 deleteStatus 是 false 并且 IsStrictReference(ref) 是 true,抛出 TypeError 异常.
  5. 返回 deleteStatus.
6. 否则 ref 是对环境记录绑定的引用，
  1. 令 bindings 为 GetBase(ref).
  2. 返回 ? bindings.DeleteBinding(GetReferencedName(ref)).

> 注意
>
> 在严格模式代码中发生删除运算符时，如果其UnaryExpression是对变量，函数参数或函数名称的直接引用，则会引发SyntaxError异常。另外，如果在严格模式代码内发生删除运算符，并且要删除的属性具有属性{[[Configurable]]：false}，则会引发TypeError异常。

### 12.5.4void 运算符 <div id="sec-void-operator"></div>

#### 12.5.4.1 运行时语义：Evaluation <div id="sec-void-operator-runtime-semantics-evaluation"></div>

```
UnaryExpression : void UnaryExpression
```

1. 令 expr 为UnaryExpression的运算结果
2. 执行 ? GetValue(expr).
3. 返回 undefined.

> 注意
>
> 即使没有使用GetValue，也必须调用它，因为它可能会有明显的副作用。

### 12.5.5typeof 运算符 <div id="sec-typeof-operator"></div>

#### 12.5.5.1 运行时语义：Evaluation <div id="sec-typeof-operator-runtime-semantics-evaluation"></div>

```
UnaryExpression : typeof UnaryExpression
```

1. 令 val 为UnaryExpression的运算结果
2. 若 Type(val) 是 Reference, 那么
1. 若 IsUnresolvableReference(val) 是 true, 返回 "undefined".
3. 设置 val 为 ? GetValue(val).
4. 返回字符串，根据Table 35.

| val类型                               | 结果                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| Undefined                             | `"undefined"`                                                |
| Null                                  | `"object"`                                                   |
| Boolean                               | `"boolean"`                                                  |
| Number                                | `"number"`                                                   |
| String                                | `"string"`                                                   |
| Symbol                                | `"symbol"`                                                   |
| Object (普通且不实现[[Call]])         | `"object"`                                                   |
| Object (标准异类且不实现[[Call]])     | `"object"`                                                   |
| Object (实现[[Call]])                 | `"function"`                                                 |
| Object (非标准异类且没有实现[[Call]]) | 实现定义。 不能是“undefined”，“boolean”，“function”，“number”，“symbol”或“string”。 |

> 注意：不建议为非标准异类对象定义新类型的结果值。如果可能，应将“object”用于此类对象。

### 12.5.6 一元+ 运算符 <div id="sec-unary-plus-operator"></div>
> 注意
>
> 一元+运算符将其操作数转换为Number类型。

#### 12.5.6.1 运行时语义：Evaluation <div id="sec-unary-plus-operator-runtime-semantics-evaluation"></div>

```
UnaryExpression : + UnaryExpression
```

1. 令 expr 为UnaryExpression的运算结果
2. 返回 ? ToNumber(? GetValue(expr)).

### 12.5.7 一元- 运算符 <div id="sec-unary-minus-operator"></div>

> 注意
>
> 一元运算符将其操作数转换为数字类型，然后取反。取负+0产生-0，取负-0产生+0。

#### 12.5.7.1 运行时语义：Evaluation <div id="sec-unary-minus-operator-runtime-semantics-evaluation"></div>

```
UnaryExpression : - UnaryExpression
```

1. 令 expr 为UnaryExpression的运算结果
2. 令 oldValue 为 ? ToNumber(? GetValue(expr)).
3. 若 oldValue 是 NaN, 返回 NaN.
4. 返回否定oldValue的结果; 也就是说，计算一个具有相同大小但符号相反的数字。

### 12.5.8 按位非运算符 ( ~ ) <div id="sec-bitwise-not-operator"></div>
#### 12.5.8.1 运行时语义：Evaluation <div id="sec-bitwise-not-operator-runtime-semantics-evaluation"></div>

```
UnaryExpression : ~ UnaryExpression
```

1. 令 expr 为UnaryExpression的运算结果
2. 令 oldValue 为 ? ToInt32(? GetValue(expr)).
3. 返回对oldValue部分按位补码的结果。结果是一个有符号的32位整数。

### 12.5.9 逻辑非运算符( ! ) <div id="sec-logical-not-operator"></div>
#### 12.5.9.1 运行时语义：Evaluation <div id="sec-logical-not-operator-runtime-semantics-evaluation"></div>

```
UnaryExpression : ! UnaryExpression
```

1. 令 expr 为UnaryExpression的运算结果
2. 令 oldValue 为 ToBoolean(? GetValue(expr)).
3. 若 oldValue 是 true, 返回 false.
4. 返回 true

## 12.6 指数运算符 <div id="sec-exp-operator"></div>

**语法**

```
ExponentiationExpression[Yield, Await] :
    UnaryExpression[?Yield, ?Await]
    UpdateExpression[?Yield, ?Await] ** ExponentiationExpression[?Yield, ?Await]
```

### 12.6.1 静态语义：IsFunctionDefinition <div id="sec-exp-operator-static-semantics-isfunctiondefinition"></div>

```
ExponentiationExpression :
    UpdateExpression ** ExponentiationExpression
```

1. 返回 false

### 12.6.2 静态语义：AssignmentTargetType <div id="sec-exp-operator-static-semantics-assignmenttargettype"></div>

```
ExponentiationExpression :
    UpdateExpression ** ExponentiationExpression
```

1. 返回 invalid.

### 12.6.3 运行时语义：Evaluation <div id="sec-exp-operator-runtime-semantics-evaluation"></div>

```
ExponentiationExpression : UpdateExpression ** ExponentiationExpression
```

1. 令 left 为UpdateExpression的运算结果
2. 令 leftValue 为 ? GetValue(left).
3. 令 right 为ExponentiationExpression的运算结果
4. 令 rightValue 为 ? GetValue(right).
5. 令 base 为 ? ToNumber(leftValue).
6. 令 exponent 为 ? ToNumber(rightValue).
7. 返回将**运算符与12.6.4中指定的base和exponent相乘的结果。

### 12.6.4 使用** 运算符 <div id="sec-applying-the-exp-operator"></div>

返回将底数提高到幂指数的结果的与实现有关的近似值。

若 exponent 是 NaN, 结果是 NaN.
若 exponent 是 +0, 结果是 1, 即使 base 是 NaN.
若 exponent 是 -0, 结果是 1, 即使 base 是 NaN.
若 base 是 NaN 并且 exponent 是 nonzero, 结果是 NaN.
若 abs(base) > 1 并且 exponent 是 +∞, 结果是 +∞.
若 abs(base) > 1 并且 exponent 是 -∞, 结果是 +0.
若 abs(base) 是 1 并且 exponent 是 +∞, 结果是 NaN.
若 abs(base) 是 1 并且 exponent 是 -∞, 结果是 NaN.
若 abs(base) < 1 并且 exponent 是 +∞, 结果是 +0.
若 abs(base) < 1 并且 exponent 是 -∞, 结果是 +∞.
若 base 是 +∞ 并且 exponent > 0, 结果是 +∞.
若 base 是 +∞ 并且 exponent < 0, 结果是 +0.
若 base 是 -∞ 并且 exponent > 0 并且 exponent 是奇数整数, 结果是 -∞.
若 base 是 -∞ 并且 exponent > 0 并且 exponent 不是奇数整数, 结果是 +∞.
若 base 是 -∞ 并且 exponent < 0 并且 exponent 是奇数整数, 结果是 -0.
若 base 是 -∞ 并且 exponent < 0 并且 exponent 不是奇数整数, 结果是 +0.
若 base 是 +0 并且 exponent > 0, 结果是 +0.
若 base 是 +0 并且 exponent < 0, 结果是 +∞.
若 base 是 -0 并且 exponent > 0 并且 exponent 是奇数整数, 结果是 -0.
若 base 是 -0 并且 exponent > 0 并且 exponent 不是奇数整数, 结果是 +0.
若 base 是 -0 并且 exponent < 0 并且 exponent 是奇数整数, 结果是 -∞.
若 base 是 -0 并且 exponent < 0 并且 exponent 不是奇数整数, 结果是 +∞.
若 base < 0 并且 base 是有限的 并且 exponent 是有限的，并且 exponent 不是整数, 结果是 NaN.

> 注意
>
> 当base为1或-1且指数为+ Infinity或-Infinity时，base **指数的结果与IEEE 754-2008不同。ECMAScript的第一版为此操作指定了NaN的结果，而IEEE 754-2008的更高版本则指定了1。出于兼容性原因，保留了历史ECMAScript行为。

## 12.7 乘数运算符 <div id="sec-multiplicative-operators"></div>

语法

```
MultiplicativeExpression[Yield, Await] :
    ExponentiationExpression[?Yield, ?Await]
    MultiplicativeExpression[?Yield, ?Await] MultiplicativeOperator
    ExponentiationExpression[?Yield, ?Await]
MultiplicativeOperator : one of
    * / %
```

### 12.7.1 静态语义：IsFunctionDefinition <div id="sec-multiplicative-operators-static-semantics-isfunctiondefinition"></div>

```
MultiplicativeExpression : MultiplicativeExpression MultiplicativeOperator ExponentiationExpression
```

1. 返回 false.

### 12.7.2 静态语义：AssignmentTargetType <div id="sec-multiplicative-operators-static-semantics-assignmenttargettype"></div>

```
MultiplicativeExpression : MultiplicativeExpression MultiplicativeOperator ExponentiationExpression
```

1. 返回 invalid.

### 12.7.3 运行时语义：Evaluation <div id="sec-multiplicative-operators-runtime-semantics-evaluation"></div>

```
MultiplicativeExpression : MultiplicativeExpression MultiplicativeOperator ExponentiationExpression
```

1. 令 left 为MultiplicativeExpression的运算结果
2. 令 leftValue 为 ? GetValue(left).
3. 令 right 为ExponentiationExpression的运算结果
4. 令 rightValue 为 ? GetValue(right).
5. 令 lnum 为 ? ToNumber(leftValue).
6. 令 rnum 为 ? ToNumber(rightValue).
7. 返回将12.7.3.1、12.7.3.2或12.7.3.3中指定的MultiplicativeOperator（*，/或％）应用于lnum和rnum的结果。

#### 12.7.3.1 使用* 运算符 <div id="sec-applying-the-mul-operator"></div>

\* MultiplicativeOperator执行乘法，产生其操作数的乘积。乘法具有交换律。由于有限的精度，乘法在ECMAScript中并不总是有结合律。

浮点乘法的结果受IEEE 754-2008二进制双精度算法的规则支配：

- 如果任一操作数为NaN，则结果为NaN。

- 如果两个操作数具有相同的符号，则结果的符号为正；如果两个操作数具有不同的符号，则结果为负。
- 无穷大乘以零会产生NaN。

- 无穷大与无穷大相乘会导致无穷大。该标志由上述规则确定。

- 无穷大与有限非零值的乘积导致有符号无穷大。该标志由上述规则确定。

- 在其余情况下，不涉及无穷大或NaN时，将使用IEEE 754-2008四舍五入到偶数模式来计算乘积并四舍五入到最接近的可表示值。如果幅度太大而无法表示，则结果是适当符号的无穷大。如果幅度太小而无法表示，则结果为适当符号的零。ECMAScript语言需要支持IEEE 754-2008所定义的渐进式下溢。

#### 12.7.3.2 使用/ 运算符 <div id="sec-applying-the-div-operator"></div>

/ MultiplicativeOperator执行除法，产生其操作数的商。左操作数是除数，右操作数是除数。ECMAScript不执行整数除法。所有除法运算的操作数和结果均为双精度浮点数。除法的结果由IEEE 754-2008算法的规范确定：

- 如果任一操作数为NaN，则结果为NaN。

- 如果两个操作数具有相同的符号，则结果的符号为正；如果两个操作数具有不同的符号，则结果为负。

- 无限除以无限会得到NaN。

- 无穷大除以零会导致无穷大。该标志由上述规则确定。

- 无穷大除以非零有限值会导致有符号无穷大。该标志由上述规则确定。

- 有限值除以无穷大将得出零。该标志由上述规则确定。

- 将零除以零会产生NaN；将零除以任何其他有限值将得出零，其符号由上述规则确定。

- 非零有限值除以零会导致有符号无穷大。该标志由上述规则确定。

- 在其余情况下，既不涉及无穷大，也不涉及零，也不涉及NaN，则使用IEEE 754-2008四舍五入到偶数模式来计算商并四舍五入到最接近的可表示值。如果幅度太大而无法表示，则操作会溢出；结果是适当符号的无穷大。如果幅度太小而无法表示，则操作会下溢，并且结果为适当符号的零。ECMAScript语言需要支持IEEE 754-2008所定义的渐进式下溢。

#### 12.7.3.3 使用% 运算符 <div id="sec-applying-the-mod-operator"></div>

％MultiplicativeOperator从隐式除法中得出其其余操作数；左操作数是除数，右操作数是除数。

> 注意
>
> 在C和C ++中，其余运算符仅接受整数操作数；请参见在ECMAScript中，它也接受浮点操作数。

由％运算符计算的浮点余数运算的结果与IEEE 754-2008定义的“余数”运算不同。IEEE 754-2008“余数”运算是根据舍入除法而不是截断除法计算余数的，因此其行为与通常的整数余数运算符的行为并不相似。相反，ECMAScript语言在浮点运算上定义了％，其行为类似于Java整数余数运算符；可以将其与C库函数fmod进行比较。

ECMAScript浮点余数运算的结果由IEEE算术规则确定：

- 如果任一操作数为NaN，则结果为NaN。

- 结果的符号等于被除数的符号。

- 如果被除数是无穷大，或者除数是零，或者两者都是，则结果为NaN。如果除数是有限的，并且除数是无穷大，则结果等于除数。如果被除数为零，并且除数为非零且有限，则结果与被除数相同。在其余情况下，既不涉及无穷大，也不涉及零，也不涉及NaN，则除数n和除数d的浮点余数r由数学关系r = n-（d×q）定义，其中q是一个整数，仅当n / d为负时才为负，而只有当n / d为正时才为正，并且其大小应尽可能大而不会超过n和d的真数学商。使用IEEE 754-2008将r计算为四舍五入并舍入为最接近的可表示值，并舍入为偶数模式

## 12.8 加法运算符 <div id="sec-additive-operators"></div>

语法

```
AdditiveExpression[Yield, Await] :
    MultiplicativeExpression[?Yield, ?Await]
    AdditiveExpression[?Yield, ?Await] + MultiplicativeExpression[?Yield, ?Await]
    AdditiveExpression[?Yield, ?Await] - MultiplicativeExpression[?Yield, ?Await]
```

### 12.8.1 静态语义：IsFunctionDefinition <div id="sec-additive-operators-static-semantics-isfunctiondefinition"></div>

```
AdditiveExpression :
    AdditiveExpression + MultiplicativeExpression
    AdditiveExpression - MultiplicativeExpression
```

1. 返回 false.

### 12.8.2 静态语义：AssignmentTargetType <div id="sec-additive-operators-static-semantics-assignmenttargettype"></div>

```
AdditiveExpression :
    AdditiveExpression + MultiplicativeExpression
    AdditiveExpression - MultiplicativeExpression
```

1. 返回 invalid.

### 12.8.3 加法运算符( + ) <div id="sec-addition-operator-plus"></div>

>注意
>
>加法运算符可以执行字符串连接或数字加法。

#### 12.8.3.1 运行时语义：Evaluation <div id="sec-addition-operator-plus-runtime-semantics-evaluation"></div>

```
AdditiveExpression : AdditiveExpression + MultiplicativeExpression
```

1. 令 lref 为AdditiveExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为MultiplicativeExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 lprim 为 ? ToPrimitive(lval).
6. 令 rprim 为 ? ToPrimitive(rval).
7. 若 Type(lprim) 是 String or Type(rprim) 是 String, 那么
1. 令 lstr 为 ? ToString(lprim).
2. 令 rstr 为 ? ToString(rprim).
3. 返回 lstr 和 rstr 的 string-concatenation.
8. 令 lnum 为 ? ToNumber(lprim).
9. 令 rnum 为 ? ToNumber(rprim).
10. 返回将加法运算应用于lnum和rnum的结果。参见 12.8.5下的注意事项.

> 注1：在第5步和第6步中对ToPrimitive的调用中未提供任何提示。除Date对象外，所有标准对象都处理没有提示的情况，就好像给出了提示编号一样。Date对象处理提示的缺失，就像提示字符串已给出一样。异类对象可以其他某种方式处理提示的缺失。
>
> 注2：步骤7与抽象关系比较算法的步骤3不同，它使用逻辑或运算代替逻辑与运算。

### 12.8.4 减法运算符( - ) <div id="sec-subtraction-operator-minus"></div>

#### 12.8.4.1 运行时语义：Evaluation <div id="sec-subtraction-operator-minus-runtime-semantics-evaluation"></div>

```
AdditiveExpression : AdditiveExpression - MultiplicativeExpression
```

1. 令 lref 为AdditiveExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为MultiplicativeExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 lnum 为 ? ToNumber(lval).
6. 令 rnum 为 ? ToNumber(rval).
7. 返回将减法运算应用于lnum和rnum的结果。参见 12.8.5下的注意事项.

### 12.8.5 加法作用于数字 <div id="sec-applying-the-additive-operators-to-numbers"></div>

当将+运算符应用于两个数字类型的操作数时，它们将执行加法运算，从而产生操作数之和。-运算符执行减法运算，产生两个数字操作数的差。

加法是可交换的运算，但并不总是可结合的。

加法的结果使用IEEE 754-2008二进制双精度算法的规则确定：

- 如果任一操作数为NaN，则结果为NaN。

- 具有相反符号的两个无穷大之和为NaN。

- 同一符号的两个无穷大之和就是该符号的无穷大。

- 无穷大与有限值之和等于无穷操作数。

- 两个负零的总和为-0。两个正零或符号相反的两个零的总和为+0。

- 零和非零有限值之和等于非零操作数。

- 两个大小相同且符号相反的非零有限值的总和为+0。

- 在其余情况下，既不涉及无穷大，也不涉及零，也不涉及NaN，并且操作数具有相同的符号或具有不同的大小，则使用IEEE 754-2008舍入到最接近的值，计算总和并舍入到最接近的可表示值。，与偶数模式相关。如果幅度太大而无法表示，则操作会溢出结果就是适当符号的无穷大。ECMAScript语言需要逐步支持IEEE 754-2008定义的下溢。

> 注意
>
> -运算符应用于两个数字类型的操作数时会进行减法运算，从而产生其操作数之差；左边的操作数是被减数，右边的操作数是被减数。给定数值操作数a和b，a - b总是产生与a +（-b）相同的结果

## 12.9 按位移运算符 <div id="sec-bitwise-shift-operators"></div>

**语法**

```
ShiftExpression[Yield, Await] :
    AdditiveExpression[?Yield, ?Await]
    ShiftExpression[?Yield, ?Await] << AdditiveExpression[?Yield, ?Await]
    ShiftExpression[?Yield, ?Await] >> AdditiveExpression[?Yield, ?Await]
    ShiftExpression[?Yield, ?Await] >>> AdditiveExpression[?Yield, ?Await]
```

### 12.9.1 静态语义：IsFunctionDefinition <div id="sec-bitwise-shift-operators-static-semantics-isfunctiondefinition"></div>

```
ShiftExpression :
    ShiftExpression << AdditiveExpression
    ShiftExpression >> AdditiveExpression
    ShiftExpression >>> AdditiveExpression
```

1. 返回 false.

### 12.9.2 静态语义：AssignmentTargetType <div id="sec-bitwise-shift-operators-static-semantics-assignmenttargettype"></div>

```
ShiftExpression :
    ShiftExpression << AdditiveExpression
    ShiftExpression >> AdditiveExpression
    ShiftExpression >>> AdditiveExpression
```

1. 返回 invalid.

### 12.9.3 左移位运算符( << ) <div id="sec-left-shift-operator"></div>

> 注意
>
> 在左操作数上按右操作数指定的数量执行按位左移运算。

#### 12.9.3.1 运行时语义：Evaluation <div id="sec-left-shift-operator-runtime-semantics-evaluation"></div>

```
ShiftExpression : ShiftExpression << AdditiveExpression
```

1. 令 lref 为ShiftExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为AdditiveExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 lnum 为 ? ToInt32(lval).
6. 令 rnum 为 ? ToUint32(rval).
7. 令 shiftCount 为屏蔽掉rnum的除最低有效5位以外的所有位的结果，即计算rnum和 0x1F。
8. 返回将lnum左移shiftCount位的结果。结果是一个有符号的32位整数。

### 12.9.4 带号右移位运算符( >> ) <div id="sec-signed-right-shift-operator"></div>

> 注意
>
> 对左操作数执行符号填充，按右操作数指定的数量进行右移。

#### 12.9.4.1 运行时语义：Evaluation <div id="sec-signed-right-shift-operator-runtime-semantics-evaluation"></div>

```
ShiftExpression : ShiftExpression >> AdditiveExpression
```

1. 令 lref 为ShiftExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为AdditiveExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 lnum 为 ? ToInt32(lval).
6. 令 rnum 为 ? ToUint32(rval).
7. 令 shiftCount 为屏蔽掉rnum的除最低有效5位以外的所有位的结果，即计算rnum＆0x1F。
8. 返回通过shiftCount位执行lnum的符号扩展右移的结果。最高有效位被传递。结果是一个有符号的32位整数

### 12.9.5 不带号右移位运算符( >>> ) <div id="sec-unsigned-right-shift-operator"></div>

> 注意：对左操作数执行按右操作数指定的数量的零填充按位右移运算。

#### 12.9.5.1 运行时语义：Evaluation <div id="sec-unsigned-right-shift-operator-runtime-semantics-evaluation"></div>

```
ShiftExpression : ShiftExpression >>> AdditiveExpression
```

1. 令 lref 为ShiftExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为AdditiveExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 lnum 为 ? ToUint32(lval).
6. 令 rnum 为 ? ToUint32(rval).
7. 令 shiftCount 为屏蔽掉rnum的除最低有效5位以外的所有位的结果，即计算 rnum 和 0x1F。
8. 返回通过shiftCount位执行lnum的零填充右移的结果。空位用零填充，结果是32位无符号整数。

## 12.10 关系运算符 <div id="sec-relational-operators"></div>

> 注1：关系运算符的求值结果始终为布尔类型，反映了运算符命名的关系是否在其两个操作数之间成立

**语法**

```
RelationalExpression[In, Yield, Await] :
    ShiftExpression[?Yield, ?Await]
    RelationalExpression[?In, ?Yield, ?Await] < ShiftExpression[?Yield, ?Await]
    RelationalExpression[?In, ?Yield, ?Await] > ShiftExpression[?Yield, ?Await]
    RelationalExpression[?In, ?Yield, ?Await] <= ShiftExpression[?Yield, ?Await]
    RelationalExpression[?In, ?Yield, ?Await] >= ShiftExpression[?Yield, ?Await]
    RelationalExpression[?In, ?Yield, ?Await] instanceof ShiftExpression[?Yield, ?Await]
    [+In] RelationalExpression[+In, ?Yield, ?Await] in ShiftExpression[?Yield, ?Await]
```

> 注2：需要使用[In]语法参数，以避免将关系表达式中的in运算符与for语句中的in运算符混淆。

### 12.10.1 静态语义：IsFunctionDefinition <div id="sec-relational-operators-static-semantics-isfunctiondefinition"></div>

```
RelationalExpression :
    RelationalExpression < ShiftExpression
    RelationalExpression > ShiftExpression
    RelationalExpression <= ShiftExpression
    RelationalExpression >= ShiftExpression
    RelationalExpression instanceof ShiftExpression
    RelationalExpression in ShiftExpression
```

1. 返回 false.

### 12.10.2 静态语义：AssignmentTargetType <div id="sec-relational-operators-static-semantics-assignmenttargettype"></div>

```
RelationalExpression :
    RelationalExpression < ShiftExpression
    RelationalExpression > ShiftExpression
    RelationalExpression <= ShiftExpression
    RelationalExpression >= ShiftExpression
    RelationalExpression instanceof ShiftExpression
    RelationalExpression in ShiftExpression
```

1. 返回 invalid.

### 12.10.3 运行时语义：Evaluation <div id="sec-relational-operators-runtime-semantics-evaluation"></div>

```
RelationalExpression : RelationalExpression < ShiftExpression
```

1. 令 lref 为RelationalExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为ShiftExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 r 为执行抽象关系比较 lval <rval 的结果。
6. ReturnIfAbrupt(r).
7. 若 r 是 undefined, 返回 false. 否则, 返回 r.

```
RelationalExpression : RelationalExpression > ShiftExpression
```

1. 令 lref 为RelationalExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为ShiftExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 r 为在LeftFirst等于false的情况下执行抽象关系比较rval <lval的结果。
6. ReturnIfAbrupt(r).
7. 若 r 是 undefined, 返回 false. 否则, 返回 r

```
RelationalExpression : RelationalExpression <= ShiftExpression
```

1. 令 lref 为RelationalExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为ShiftExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 r 为在LeftFirst等于false的情况下执行抽象关系比较rval <lval的结果。
6. ReturnIfAbrupt(r).
7. 若 r 是 true or undefined, 返回 false. 否则, 返回 true.

```
RelationalExpression : RelationalExpression >= ShiftExpression
```

1. 令 lref 为RelationalExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为ShiftExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 r 为执行抽象关系比较 lval <rval 的结果。
6. ReturnIfAbrupt(r).
7. 若 r 是 true or undefined, 返回 false. 否则, 返回 true.

```
RelationalExpression : RelationalExpression instanceof ShiftExpression
```

1. 令 lref 为RelationalExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为ShiftExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 返回 ? InstanceofOperator(lval, rval)

```
RelationalExpression : RelationalExpression in ShiftExpression
```

1. 令 lref 为RelationalExpression的运算结果
2. 令 lval 为 ? GetValue(lref).

3. 令 rref 为ShiftExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 若 Type(rval) 不是 Object,抛出 TypeError 异常.
6. 返回 ? HasProperty(rval, ToPropertyKey(lval)).

### 12.10.4 运行时语义：InstanceofOperator ( V, target ) <div id="sec-instanceofoperator"></div>

抽象操作InstanceofOperator（V，target）实现了通用算法，可通过询问目标的@@ hasinstance方法来确定ECMAScript值V是否是对象目标的实例，或者如果不存在，则确定V的目标属性是否存在目标原型属性原型链。此抽象操作执行以下步骤：

1. 若 Type(target) 不是 Object,抛出 TypeError 异常.
2. 令 instOfHandler 为 ? GetMethod(target, @@hasInstance).
3. 若 instOfHandler 不是 undefined, 那么
1. 返回 ToBoolean(? Call(instOfHandler, target, « V »)).
4. 若 IsCallable(target) 是 false,抛出 TypeError 异常.
5. 返回 ? OrdinaryHasInstance(target, V).

> 注意
>
> 步骤4和5提供了与ECMAScript以前版本的兼容性，该版本未使用@@ hasInstance方法来定义instanceof运算符语义。如果对象未定义或继承@@ hasInstance，则它将使用默认的instanceof语义。

## 12.11 相等运算符 <div id="sec-equality-operators"></div>

> 注意
>
> 计算相等运算符的结果始终是布尔类型，反映了该运算符命名的关系是否在其两个操作数之间成立。

**语法**

```
EqualityExpression[In, Yield, Await] :
    RelationalExpression[?In, ?Yield, ?Await]
    EqualityExpression[?In, ?Yield, ?Await] == RelationalExpression[?In, ?Yield, ?Await]
    EqualityExpression[?In, ?Yield, ?Await] != RelationalExpression[?In, ?Yield, ?Await]
    EqualityExpression[?In, ?Yield, ?Await] === RelationalExpression[?In, ?Yield, ?Await]
    EqualityExpression[?In, ?Yield, ?Await] !== RelationalExpression[?In, ?Yield, ?Await]
```

### 12.11.1 静态语义：IsFunctionDefinition <div id="sec-equality-operators-static-semantics-isfunctiondefinition"></div>

```
EqualityExpression :
    EqualityExpression == RelationalExpression
    EqualityExpression != RelationalExpression
    EqualityExpression === RelationalExpression
    EqualityExpression !== RelationalExpression
```

1. 返回 false.

### 12.11.2 静态语义：AssignmentTargetType <div id="sec-equality-operators-static-semantics-assignmenttargettype"></div>

```
EqualityExpression :
    EqualityExpression == RelationalExpression
    EqualityExpression != RelationalExpression
    EqualityExpression === RelationalExpression
    EqualityExpression !== RelationalExpression
```

1. 返回 invalid.

### 12.11.3 运行时语义：Evaluation <div id="sec-equality-operators-runtime-semantics-evaluation"></div>

```
EqualityExpression : EqualityExpression == RelationalExpression
```

1. 令 lref 为EqualityExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为RelationalExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 返回执行抽象相等比较 rval == lval 的结果。

```
EqualityExpression : EqualityExpression != RelationalExpression
```

1. 令 lref 为EqualityExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为RelationalExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 r 为执行抽象相等比较 rval == lval 的结果。
6. 若 r 是 true, 返回 false. 否则, 返回 true.

```
EqualityExpression : EqualityExpression === RelationalExpression
```

1. 令 lref 为EqualityExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为RelationalExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 返回执行严格相等比较 rval === lval 的结果。

```
EqualityExpression : EqualityExpression !== RelationalExpression
```

1. 令 lref 为EqualityExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为RelationalExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 r 为执行严格相等比较 rval === lval 的结果。
6. 若 r 是 true, 返回 false. 否则, 返回 true.

> 注1：
>
> 鉴于以上对平等的定义：
>
> ​	可以通过以下方式强制进行字符串比较：“” + a ==“” + b。
>
> ​	可以通过以下方式强制进行数字比较：+ a == + b。
>
> ​	可以通过以下方式强制进行布尔比较：！a ==！b。
>
> 注2：
>
> 相等运算符维护以下不变式：
>
> ​	A != B 等于 !(A == B)。
>
> ​	A == B 等于 B == A，除了A和B的取值顺序。
>
> 注3：
>
> 相等运算符并不总是可传递的。例如，可能有两个不同的String对象，每个对象代表相同的String值；==运算符将认为每个String对象都等于String值，但是两个String对象将彼此不相等。例如：
>
> ​	new String（"a"）=="a"和"a" == new String（"a"）均为true。
>
> ​	new String（"a"）== new String（"a"）为false。
>
> 注4：
>
> 字符串比较对代码单元值的序列使用简单的相等性测试。没有尝试使用更复杂的，面向语义的字符或字符串相等性定义以及Unicode规范中定义的整理顺序。因此，根据Unicode标准规范相等的字符串值可以测试为不相等。实际上，该算法假定两个字符串都已经处于规范化形式

## 12.12 二进制按位运算符 <div id="sec-binary-bitwise-operators"></div>

**语法**

```
BitwiseANDExpression[In, Yield, Await] :
    EqualityExpression[?In, ?Yield, ?Await]
    BitwiseANDExpression[?In, ?Yield, ?Await] & EqualityExpression[?In, ?Yield, ?Await]
BitwiseXORExpression[In, Yield, Await] :
    BitwiseANDExpression[?In, ?Yield, ?Await]
    BitwiseXORExpression[?In, ?Yield, ?Await] ^ BitwiseANDExpression[?In, ?Yield, ?Await]
BitwiseORExpression[In, Yield, Await] :
    BitwiseXORExpression[?In, ?Yield, ?Await]
    BitwiseORExpression[?In, ?Yield, ?Await] | BitwiseXORExpression[?In, ?Yield, ?Await]
```

### 12.12.1 静态语义：IsFunctionDefinition <div id="sec-binary-bitwise-operators-static-semantics-isfunctiondefinition"></div>

```
BitwiseANDExpression : BitwiseANDExpression & EqualityExpression
BitwiseXORExpression : BitwiseXORExpression ^ BitwiseANDExpression
BitwiseORExpression : BitwiseORExpression | BitwiseXORExpression
```

1. 返回 false.

### 12.12.2 静态语义：AssignmentTargetType <div id="sec-binary-bitwise-operators-static-semantics-assignmenttargettype"></div>

```
BitwiseANDExpression : BitwiseANDExpression & EqualityExpression
BitwiseXORExpression : BitwiseXORExpression ^ BitwiseANDExpression
BitwiseORExpression : BitwiseORExpression | BitwiseXORExpression
```

1. 返回 invalid.

### 12.12.3 运行时语义：Evaluation <div id="sec-binary-bitwise-operators-runtime-semantics-evaluation"></div>

生产A：A @ B，其中@是上述生产中按位运算符之一，其计算如下：

1. 令 lref 为A的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为B的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 lnum 为 ? ToInt32(lval).
6. 令 rnum 为 ? ToInt32(rval).
7. 返回将按位运算符@应用于lnum和rnum的结果。结果是一个有符号的32位整数。

## 12.13 二元逻辑运算符 <div id="sec-binary-logical-operators"></div>

**语法**

```
LogicalANDExpression[In, Yield, Await] :
    BitwiseORExpression[?In, ?Yield, ?Await]
    LogicalANDExpression[?In, ?Yield, ?Await] && BitwiseORExpression[?In, ?Yield, ?Await]
LogicalORExpression[In, Yield, Await] :
    LogicalANDExpression[?In, ?Yield, ?Await]
    LogicalORExpression[?In, ?Yield, ?Await] || LogicalANDExpression[?In, ?Yield, ?Await]
```

> 注意
>
> 由&&或||产生的值运算符不一定是布尔类型。产生的值将始终是两个操作数表达式之一的值。

### 12.13.1 静态语义：IsFunctionDefinition <div id="sec-binary-logical-operators-static-semantics-isfunctiondefinition"></div>

```
LogicalANDExpression : LogicalANDExpression && BitwiseORExpression
LogicalORExpression : LogicalORExpression || LogicalANDExpression
```

1. 返回 false.

### 12.13.2 静态语义：AssignmentTargetType <div id="sec-binary-logical-operators-static-semantics-assignmenttargettype"></div>

```
LogicalANDExpression : LogicalANDExpression && BitwiseORExpression
LogicalORExpression : LogicalORExpression || LogicalANDExpression
```

1. 返回 invalid.

### 12.13.3 运行时语义：Evaluation <div id="sec-binary-logical-operators-runtime-semantics-evaluation"></div>

```
LogicalANDExpression : LogicalANDExpression && BitwiseORExpression
```

1. 令 lref 为LogicalANDExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 lbool 为 ToBoolean(lval).
4. 若 lbool 是 false, 返回 lval.
5. 令 rref 为BitwiseORExpression的运算结果
6. 返回 ? GetValue(rref).

```
LogicalORExpression : LogicalORExpression || LogicalANDExpression
```

1. 令 lref 为LogicalORExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 lbool 为 ToBoolean(lval).
4. 若 lbool 是 true, 返回 lval.
5. 令 rref 为LogicalANDExpression的运算结果
6. 返回 ? GetValue(rref).

## 12.14 条件运算符 <div id="sec-conditional-operator"></div>

**语法**

```
ConditionalExpression[In, Yield, Await] :
    LogicalORExpression[?In, ?Yield, ?Await]
    LogicalORExpression[?In, ?Yield, ?Await] ? AssignmentExpression[+In, ?Yield, ?Await] :
        AssignmentExpression[?In, ?Yield, ?Await]
```

> 注意
>
> ECMAScript中的ConditionalExpression的语法与C和Java中的略有不同，后者分别允许第二个子表达式为Expression，但将第三个表达式限制为ConditionalExpression。ECMAScript中这种差异的动机是允许赋值表达式由条件的任一分支支配，并消除以逗号表达式为中心表达式的令人困惑且相当无用的情况。

### 12.14.1 静态语义：IsFunctionDefinition <div id="sec-conditional-operator-static-semantics-isfunctiondefinition"></div>

```
ConditionalExpression : LogicalORExpression ? AssignmentExpression : AssignmentExpression
```

1. 返回 false.

### 12.14.2 静态语义：AssignmentTargetType <div id="sec-conditional-operator-static-semantics-assignmenttargettype"></div>

```
ConditionalExpression : LogicalORExpression ? AssignmentExpression : AssignmentExpression
```

1. 返回 invalid.

### 12.14.3 运行时语义：Evaluation <div id="sec-conditional-operator-runtime-semantics-evaluation"></div>

```
ConditionalExpression : LogicalORExpression ? AssignmentExpression : AssignmentExpression
```

1. 令 lref 为LogicalORExpression的运算结果
2. 令 lval 为 ToBoolean(? GetValue(lref)).
3. 若 lval 是 true, 那么
a. 令 trueRef 为运算第一个AssignmentExpression的结果。
b. 返回 ? GetValue(trueRef).
4. 否则,
a. 令 falseRef 为运算第二个AssignmentExpression的结果。
b. 返回 ? GetValue(falseRef).

## 12.15 赋值运算符 <div id="sec-assignment-operators"></div>

**语法**

```
AssignmentExpression[In, Yield, Await] :
    ConditionalExpression[?In, ?Yield, ?Await]
    [+Yield] YieldExpression[?In, ?Await]
    ArrowFunction[?In, ?Yield, ?Await]
    AsyncArrowFunction[?In, ?Yield, ?Await]
    LeftHandSideExpression[?Yield, ?Await] = AssignmentExpression[?In, ?Yield, ?Await]
    LeftHandSideExpression[?Yield, ?Await] AssignmentOperator
        AssignmentExpression[?In, ?Yield, ?Await]
AssignmentOperator : one of
    *= /= %= += -= <<= >>= >>>= &= ^= |= **=
```



### 12.15.1 静态语义：Early Errors <div id="sec-assignment-operators-static-semantics-early-errors"></div>

```
AssignmentExpression : LeftHandSideExpression = AssignmentExpression
```

- 如果LeftHandSideExpression是ObjectLiteral或ArrayLiteral，并且LeftHandSideExpression没有覆盖AssignmentPattern，则这是语法错误。

- 如果LeftHandSideExpression既不是ObjectLiteral也不是ArrayLiteral且LeftHandSideExpression的AssignmentTargetType是invalid，则这是早期参考错误。

- 如果LeftHandSideExpression既不是ObjectLiteral也不是ArrayLiteral且LeftHandSideExpression的AssignmentTargetType是strict，则这是早期的语法错误。

```
AssignmentExpression : LeftHandSideExpression AssignmentOperator AssignmentExpression
```

- 如果LeftHandSideExpression的AssignmentTargetType是invalid，则是早期参考错误。

- 如果LeftHandSideExpression的AssignmentTargetType是strict，则这是早期的语法错误。

### 12.15.2 静态语义：IsFunctionDefinition <div id="sec-assignment-operators-static-semantics-isfunctiondefinition"></div>

```
AssignmentExpression :
    ArrowFunction
    AsyncArrowFunction
```

1. 返回 true.

```
AssignmentExpression :
    YieldExpression
    LeftHandSideExpression = AssignmentExpression
    LeftHandSideExpression AssignmentOperator AssignmentExpression
```

1. 返回 false

### 12.15.3 静态语义：AssignmentTargetType <div id="sec-assignment-operators-static-semantics-assignmenttargettype"></div>

```
AssignmentExpression :
    YieldExpression
    ArrowFunction
    AsyncArrowFunction
    LeftHandSideExpression = AssignmentExpression
    LeftHandSideExpression AssignmentOperator AssignmentExpression
```

1. 返回 invalid.

### 12.15.4 运行时语义：Evaluation <div id="sec-assignment-operators-runtime-semantics-evaluation"></div>

```
AssignmentExpression : LeftHandSideExpression = AssignmentExpression
```

1. 若 LeftHandSideExpression 不是 ObjectLiteral 或 ArrayLiteral, 那么
   1. 令 lref 为LeftHandSideExpression的运算结果
   2. ReturnIfAbrupt(lref).
   3. 若 IsAnonymousFunctionDefinition(AssignmentExpression) 和 LeftHandSideExpression 的 IsIdentifierRef 都为 true, 那么
        1. 令 rval 为对带有参数GetReferencedName(lref) 的 AssignmentExpression  执行 NamedEvaluation 的结果
   4. 否则,
          1. 令 rref 为AssignmentExpression的运算结果
          2. 令 rval 为 ? GetValue(rref).
   5. 执行 ? PutValue(lref, rval).
   6. 返回 rval.
2. 令 assignmentPattern 为被 LeftHandSideExpression 覆盖的  AssignmentPattern。
3. 令 rref 为AssignmentExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 执行 ? 使用rval作为参数的 assignmentPattern 的 DestructuringAssignmentEvaluation 
6. 返回 rval.

```
AssignmentExpression : LeftHandSideExpression AssignmentOperator AssignmentExpression
```

1. 令 lref 为LeftHandSideExpression的运算结果
2. 令 lval 为 ? GetValue(lref).
3. 令 rref 为AssignmentExpression的运算结果
4. 令 rval 为 ? GetValue(rref).
5. 令 op 为 @ ，其中 AssignmentOperator 是 @=.
6. 令 r 为将op应用于lval和Rval的结果，就像计算表达式lval OP rval一样。
7. 执行 ? PutValue(lref, r).
8. 返回 r.

> 注意：
>
> 如果在严格模式代码中发生分配，则如果第一个算法的步骤1.f或第二个算法的步骤7中的lref是不可解析的引用，则是运行时错误。如果是，则抛出ReferenceError异常。LeftHandSideExpression也可能不是对属性值为{[[Writable]]：false的数据属性的引用，对属性值为{[[Set]]：undefined}的访问器属性的引用，也不是对不存在的引用IsExtensible谓词为其返回false值的对象的属性。在这些情况下，将引发TypeError异常。

### 12.15.5 解构赋值 <div id="sec-destructuring-assignment"></div>

**补充语法**

在某些情况下，当处理生产AssignmentExpression的实例时：LeftHandSideExpression = AssignmentExpression，以下语法用于完善LeftHandSideExpression的解释。

```
AssignmentPattern[Yield, Await] :
    ObjectAssignmentPattern[?Yield, ?Await]
    ArrayAssignmentPattern[?Yield, ?Await]
ObjectAssignmentPattern[Yield, Await] :
    { }
    { AssignmentRestProperty[?Yield, ?Await] }
    { AssignmentPropertyList[?Yield, ?Await] }
    { AssignmentPropertyList[?Yield, ?Await] , AssignmentRestProperty[?Yield, ?Await] opt }
ArrayAssignmentPattern[Yield, Await] :
    [ Elisionopt AssignmentRestElement[?Yield, ?Await] opt ]
    [ AssignmentElementList[?Yield, ?Await] ]
    [ AssignmentElementList[?Yield, ?Await] , Elisionopt
    AssignmentRestElement[?Yield, ?Await] opt ]
AssignmentRestProperty[Yield, Await] :
    ... DestructuringAssignmentTarget[?Yield, ?Await]
AssignmentPropertyList[Yield, Await] :
    AssignmentProperty[?Yield, ?Await]
    AssignmentPropertyList[?Yield, ?Await] , AssignmentProperty[?Yield, ?Await]
AssignmentElementList[Yield, Await] :
    AssignmentElisionElement[?Yield, ?Await]
    AssignmentElementList[?Yield, ?Await] , AssignmentElisionElement[?Yield, ?Await]
AssignmentElisionElement[Yield, Await] :
    Elisionopt AssignmentElement[?Yield, ?Await]
AssignmentProperty[Yield, Await] :
    IdentifierReference[?Yield, ?Await] Initializer[+In, ?Yield, ?Await] opt
PropertyName[?Yield, ?Await] : AssignmentElement[?Yield, ?Await]
AssignmentElement[Yield, Await] :
    DestructuringAssignmentTarget[?Yield, ?Await] Initializer[+In, ?Yield, ?Await] opt
AssignmentRestElement[Yield, Await] :
    ... DestructuringAssignmentTarget[?Yield, ?Await]
DestructuringAssignmentTarget[Yield, Await] :
    LeftHandSideExpression[?Yield, ?Await]
```

#### 12.15.5.1 静态语义：Early Errors <div id="sec-destructuring-assignment-static-semantics-early-errors"></div>

```
AssignmentProperty : IdentifierReference Initializer
```

- 如果IdentifierReference的AssignmentTargetType不是simple，则是语法错误

```
AssignmentRestProperty : ... DestructuringAssignmentTarget
```

- 如果DestructuringAssignmentTarget是ArrayLiteral或ObjectLiteral，则为语法错误

```
DestructuringAssignmentTarget : LeftHandSideExpression
```

- 如果LeftHandSideExpression是ObjectLiteral或ArrayLiteral，并且LeftHandSideExpression没有覆盖AssignmentPattern，则它是语法错误。

- 如果LeftHandSideExpression既不是ObjectLiteral也不是ArrayLiteral，并且AssignmentTargetType（LeftHandSideExpression）不是simple，则会出现语法错误。

#### 12.15.5.2 运行时语义：DestructuringAssignmentEvaluation <div id="sec-runtime-semantics-destructuringassignmentevaluation"></div>

带参数value。

```
ObjectAssignmentPattern : { }
```

1. 执行 ? RequireObjectCoercible(value).

2. 返回 NormalCompletion(empty).

   ```
   ObjectAssignmentPattern :
   { AssignmentPropertyList }
   { AssignmentPropertyList , }
   ```

   1. 执行 ? RequireObjectCoercible(value).
   2. 执行 ? 使用value作为参数的AssignmentPropertyList 的 PropertyDestructuringAssignmentEvaluation 
   3. 返回 NormalCompletion(empty).

```
ArrayAssignmentPattern : [ ]
```

1. 令 iteratorRecord 为 ? GetIterator(value).
2. 返回 ? IteratorClose(iteratorRecord, NormalCompletion(empty)).

```
ArrayAssignmentPattern : [ Elision ]
```

1. 令 iteratorRecord 为 ? GetIterator(value).
2. 令 result 为使用 iteratorRecord 作为参数执行 Elision 的 IteratorDestructuringAssignmentEvaluation结果。
3. 若 iteratorRecord.[[Done]] 是 false, 返回 ? IteratorClose(iteratorRecord, result).
4. 返回 result.

```
ArrayAssignmentPattern : [ Elision AssignmentRestElement ]
```

1. 令 iteratorRecord 为 ? GetIterator(value).
2. 若 Elision 存在, 那么
     1. 令 status 为使用 iteratorRecord 作为参数执行 Elision 的 IteratorDestructuringAssignmentEvaluation结果。
     2. 若 status 是 abrupt completion, 那么
          1. 断言: iteratorRecord.[[Done]] 是 true.
          2. 返回 Completion(status).

3. 令 result 为使用 iteratorRecord 作为参数执行 AssignmentRestElement 的 IteratorDestructuringAssignmentEvaluation结果。
4. 若 iteratorRecord.[[Done]] 是 false, 返回 ? IteratorClose(iteratorRecord, result).
5. 返回 result.

```
ArrayAssignmentPattern : [ AssignmentElementList ]
```

1. 令 iteratorRecord 为 ? GetIterator(value).
2. 令 result 为使用 iteratorRecord 作为参数执行 AssignmentRestElement 的 IteratorDestructuringAssignmentEvaluation结果。
3. 若 iteratorRecord.[[Done]] 是 false, 返回 ? IteratorClose(iteratorRecord, result).
4. 返回 result

```
ArrayAssignmentPattern : [ AssignmentElementList , Elision AssignmentRestElement ]
```

1. 令 iteratorRecord 为 ? GetIterator(value).
2. 令 status 为使用 iteratorRecord 作为参数执行 AssignmentRestElement 的 IteratorDestructuringAssignmentEvaluation结果。
3. 若 status 是 an abrupt completion, 那么
  1. 若 iteratorRecord.[[Done]] 是 false, 返回 ? IteratorClose(iteratorRecord, status).
  2. 返回 Completion(status).
4. 若 Elision 存在, 那么
  1. 设置 status 为使用 iteratorRecord 作为参数执行 Elision 的 IteratorDestructuringAssignmentEvaluation结果。
  2. 若 status 是 an abrupt completion, 那么
        1. 断言: iteratorRecord.[[Done]] 是 true.
            2. 返回 Completion(status).
5. 若 AssignmentRestElement 存在, 那么
  1. 设置 status 为使用 iteratorRecord 作为参数执行 AssignmentRestElement 的 IteratorDestructuringAssignmentEvaluation结果。
6. 若 iteratorRecord.[[Done]] 是 false, 返回 ? IteratorClose(iteratorRecord, status).
7. 返回 Completion(status).

```
ObjectAssignmentPattern : { AssignmentRestProperty }
```

1. 执行 ? RequireObjectCoercible(value).
2. 令 excludedNames 为一个新的空列表
3.  返回以value和excludeedNames为参数执行AssignmentRestProperty的RestDestructuringAssignmentEvaluation的结果。

```
ObjectAssignmentPattern : { AssignmentPropertyList , AssignmentRestProperty }
```

1. 执行 ? RequireObjectCoercible(value).
2. 令 excludedNames 为执行 ? 使用value作为参数的AssignmentPropertyList的PropertyDestructuringAssignmentEvaluation的结果
3. 返回以value和excludeedNames为参数执行AssignmentRestProperty的RestDestructuringAssignmentEvaluation的结果。

#### 12.15.5.3 运行时语义：PropertyDestructuringAssignmentEvaluation <div id="sec-runtime-semantics-propertydestructuringassignmentevaluation"></div>

带参数value

>注意
>
>以下操作收集所有已解构的属性名称的列表。

```
AssignmentPropertyList : AssignmentPropertyList , AssignmentProperty
```

1. 令 propertyNames 为执行 ? 使用value作为参数的AssignmentPropertyList的PropertyDestructuringAssignmentEvaluation的结果
2. 令 nextNames 为 执行 ? 以value为参数执行AssignmentRestProperty的RestDestructuringAssignmentEvaluation的结果。
3. 将nextNames中的每个项目追加到propertyNames的末尾。
4. 返回 propertyNames.

```
AssignmentProperty : IdentifierReference Initializer
```

1. 令 P 为IdentifierReference的StringValue
2. 令 lref 为 ? ResolveBinding(P).
3. 令 v 为 ? GetV(value, P).
4. 若 Initializeropt 存在，并且 v 是 undefined, 那么
1. 若 IsAnonymousFunctionDefinition(Initializer) 是 true, 那么
  1. 设置 v 为对带有参数 P 的 Intializer 执行 NamedEvaluation 的结果。
2. 否则,
  1. 令 defaultValue 为Initializer的运算结果
  2. 设置 v to ? GetValue(defaultValue).
5. 执行 ? PutValue(lref, v).
6. 返回包含P的新列表.

```
AssignmentProperty : PropertyName : AssignmentElement
```

1. 令 name 为PropertyName的运算结果
2. ReturnIfAbrupt(name).
3. 执行 ? 以value和name为参数的AssignedElement的KeyedDestructuringAssignmentEvaluation。
4. 返回包含name的新列表.

#### 12.15.5.4 运行时语义：RestDestructuringAssignmentEvaluation <div id="sec-runtime-semantics-restdestructuringassignmentevaluation"></div>

具有参数value和excludeedNames。

```
AssignmentRestProperty : ... DestructuringAssignmentTarget
```

1. 令 lref 为DestructuringAssignmentTarget的运算结果
2. ReturnIfAbrupt(lref).
3. 令 restObj 为 ObjectCreate(%ObjectPrototype%).
4. 执行 ? CopyDataProperties(restObj, value, excludedNames).
5. 返回 PutValue(lref, restObj)

#### 12.15.5.5 运行时语义：IteratorDestructuringAssignmentEvaluation <div id="sec-runtime-semantics-iteratordestructuringassignmentevaluation"></div>

使用参数iteratorRecord。

```
AssignmentElementList : AssignmentElisionElement
```

1. 返回使用iteratorRecord作为参数执行AssignmentElisionElement的IteratorDestructuringAssignmentEvaluation的结果。

```
AssignmentElementList : AssignmentElementList , AssignmentElisionElement
```

1. 执行 ? 使用iteratorRecord作为参数的AssignmentElementList的IteratorDestructuringAssignmentEvaluation。
2. 返回使用iteratorRecord作为参数执行AssignmentElisionElement的IteratorDestructuringAssignmentEvaluation的结果。

```
AssignmentElisionElement : AssignmentElement
```

1. 返回用 iteratorRecord 作为参数执行 AssignmentElement 的 IteratorDestructuringAssignmentEvaluation 的结果

```
AssignmentElisionElement : Elision AssignmentElement
```

1. 执行 ? 以IteratorRecord作为参数的Elision的IteratorDestructuringAssignmentEvaluation。
2. 返回用 iteratorRecord 作为参数执行 AssignmentElement 的 IteratorDestructuringAssignmentEvaluation 的结果

```
Elision : ,
```

1. 若 iteratorRecord.[[Done]] 是 false, 那么
1. 令 next 为 IteratorStep(iteratorRecord).
2. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
3. ReturnIfAbrupt(next).
4. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
2. 返回 NormalCompletion(empty).

```
Elision : Elision ,
```

1. 执行 ? 以IteratorRecord作为参数的Elision的IteratorDestructuringAssignmentEvaluation。
2. 若 iteratorRecord.[[Done]] 是 false, 那么
    a. 令 next 为 IteratorStep(iteratorRecord).
    b. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
    c. ReturnIfAbrupt(next).
    d. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
3. 返回 NormalCompletion(empty)

```
AssignmentElement : DestructuringAssignmentTarget Initializer
```

1. 若 DestructuringAssignmentTarget 不是 ObjectLiteral 或 ArrayLiteral, 那么
1. 令 lref 为DestructuringAssignmentTarget的运算结果
2. ReturnIfAbrupt(lref).
2. 若 iteratorRecord.[[Done]] 是 false, 那么
  1. 令 next 为 IteratorStep(iteratorRecord).
  2. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
  3. ReturnIfAbrupt(next).
  4. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
  5. 否则,
        1. 令 value 为 IteratorValue(next).
            2. 若 value 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
                3. ReturnIfAbrupt(value).
3. 若 iteratorRecord.[[Done]] 是 true, 令 value 为 undefined.
4. 若 Initializer 存在，并且 value 是 undefined, 那么
  1. 若 IsAnonymousFunctionDefinition(Initializer) 和 DestructuringAssignmentTarget 的 IsIdentifierRef 都是 true, 那么
        1. 令 v 为使用GetReferencedName(lref)参数对Initializer执行NamedEvaluation的结果
  2. 否则,
        1. 令 defaultValue 为Initializer的运算结果
            2. 令 v 为 ? GetValue(defaultValue).
5. 否则, 令 v 为 value.
6. 若 DestructuringAssignmentTarget 是 ObjectLiteral 或 ArrayLiteral, 那么
  1. 令 nestedAssignmentPattern 为 AssignmentPattern，即是被 DestructuringAssignmentTarget 涵盖的。
  2. 返回以v为参数执行nestedAssignmentPattern的DestructuringAssignmentEvaluation的结果。
7. 返回 ? PutValue(lref, v).

> 注意
>
> 在访问迭代器或执行初始化器之前，通过执行不是析构模式的DestructuringAssignmentTarget来保持从左到右的执行顺序。

```
AssignmentRestElement : ... DestructuringAssignmentTarget
```

1. 若 DestructuringAssignmentTarget 不是 ObjectLiteral 或 ArrayLiteral, 那么
    1. 令 lref 为DestructuringAssignmentTarget的运算结果
    2. ReturnIfAbrupt(lref).
2. 令 A 为 ! ArrayCreate(0).
3. 令 n 为 0.
4. 重复, 当 iteratorRecord.[[Done]] 是 false,
    1. 令 next 为 IteratorStep(iteratorRecord).
    2. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
    3. ReturnIfAbrupt(next).
    4. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
    5. 否则,
        1. 令 nextValue 为 IteratorValue(next).
        2. 若 nextValue 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
        3. ReturnIfAbrupt(nextValue).
        4. 令 status 为 CreateDataProperty(A, ! ToString(n), nextValue).
        5. 断言: status 是 true.
        6.  n 增加 1.
5. 若 DestructuringAssignmentTarget 不是 ObjectLiteral 或 ArrayLiteral, 那么
    1. 返回 ? PutValue(lref, A).
6. 令 nestedAssignmentPattern 为 the AssignmentPattern，即是被 DestructuringAssignmentTarget 涵盖的
7. 返回以A为参数执行nestedAssignmentPattern的DestructuringAssignmentEvaluation的结果。

#### 12.15.5.6 运行时语义：KeyedDestructuringAssignmentEvaluation <div id="sec-runtime-semantics-keyeddestructuringassignmentevaluation"></div>

具有参数value和propertyName。

```
AssignmentElement : DestructuringAssignmentTarget Initializer
```

1. 若 DestructuringAssignmentTarget 不是 ObjectLiteral 或 ArrayLiteral, 那么
1. 令 lref 为DestructuringAssignmentTarget的运算结果
2. ReturnIfAbrupt(lref).
2. 令 v 为 ? GetV(value, propertyName).
3. 若 Initializer 存在，并且 v 是 undefined, 那么
  1. 若 IsAnonymousFunctionDefinition(Initializer) 和 DestructuringAssignmentTarget 的 IsIdentifierRef 都为 true, 那么
    1. 令 rhsValue 为使用GetReferencedName(lref)参数对Initializer执行NamedEvaluation的结果。
  2. 否则,
    1. 令 defaultValue 为Initializer的运算结果
    2. 令 rhsValue 为 ? GetValue(defaultValue).
4. 否则, 令 rhsValue 为 v.
5. 若 DestructuringAssignmentTarget 是 ObjectLiteral 或 ArrayLiteral, 那么
  1. 令 assignmentPattern 为 the AssignmentPattern，即是被 DestructuringAssignmentTarget 涵盖的
  2. 返回使用rhsValue作为的执行DestructuringAssignmentEvaluation of AssignmentPattern的结果。
6. 返回 ? PutValue(lref, rhsValue).

## 12.16 逗号运算符 <div id="sec-comma-operator"></div>

语法

```
Expression[In, Yield, Await] :
    AssignmentExpression[?In, ?Yield, ?Await]
    Expression[?In, ?Yield, ?Await] , AssignmentExpression[?In, ?Yield, ?Await]
```

### 12.16.1 静态语义：IsFunctionDefinition <div id="sec-comma-operator-static-semantics-isfunctiondefinition"></div>

```
Expression : Expression , AssignmentExpression
```

1. 返回 false.

### 12.16.2 静态语义：AssignmentTargetType <div id="sec-comma-operator-static-semantics-assignmenttargettype"></div>

```
Expression : Expression , AssignmentExpression
```

1. 返回 invalid.

### 12.16.3 运行时语义：Evaluation <div id="sec-comma-operator-runtime-semantics-evaluation"></div>

```
Expression : Expression , AssignmentExpression
```

1. 令 lref 为Expression的运算结果
2. 执行 ? GetValue(lref).
3. 令 rref 为AssignmentExpression的运算结果
4. 返回 ? GetValue(rref).

> 注意
>
> 即使没有使用GetValue，也必须调用它，因为它可能会有明显的副作用。

```
description ECMAScript语言：表达式
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,expressions,ECMAScript语言：表达式
```
