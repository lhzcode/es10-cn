# 12 ECMAScript语言：表达式
## 12.1 标识符 <div id="sec-identifiers"></div>

语法

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
> let
>
> await 0;

### 12.1.1 静态语义：早期错误 <div id="sec-identifiers-static-semantics-early-errors"></div>

BindingIdentifier:Identifier

- 如果与此生产式匹配的代码包含在严格模式代码中，并且Identifier的StringValue是“ arguments”或“ eval”，则是语法错误。

IdentifierReference : yield
BindingIdentifier : yield
LabelIdentifier : yield

- 如果与此生产式匹配的代码包含在严格模式代码中，则是语法错误。

IdentifierReference : await
BindingIdentifier : await
LabelIdentifier : await

- 如果句法的目标符号是“模块”，则为语法错误。

BindingIdentifier : yield

- 如果此生产式具有[Yield]参数，则是语法错误。

BindingIdentifier : await

- 如果此生产式具有[Await]参数，则是语法错误。

IdentifierReference : Identifier
BindingIdentifier : Identifier
LabelIdentifier : Identifier

- 如果此生产具有[Yield]参数，并且Identifier的StringValue为“ yield”，则为语法错误。
- 如果此生产具有[Await]参数，并且Identifier的StringValue为“ await”，则为语法错误。

Identifier : IdentifierName but not ReservedWord

- 如果此短语包含在严格模式代码中，并且IdentifierName的StringValue为："implements", "interface", "let", "package", "private", "protected", "public", "static", 或 "yield"。
- 如果语法语法的目标符号为Module且IdentifierName的StringValue为“ await”，则为语法错误。
- 如果IdentifierName的StringValue与任何ReservedWord的StringValue相同的String值（除yield或await之外）均是语法错误。

> 注意：
>
> IdentifierName的StringValue规范化了IdentifierName中的所有Unicode转义序列，因此此类转义不能用于编写其码点序列与ReservedWord相同的Identifier

### 12.1.2 静态语义：绑定名称 <div id="sec-identifiers-static-semantics-boundnames"></div>

BindingIdentifier : Identifier

1. 返回一个新列表，其中包含标识符的StringValue。

BindingIdentifier : yield

1. 返回一个包含“ yield”的新列表。

BindingIdentifier : await

1. 返回一个包含“await”的新列表。

### 12.1.3 静态语义：AssignmentTargetType <div id="sec-identifiers-static-semantics-assignmenttargettype"></div>

IdentifierReference : Identifier

1. 如果此IdentifierReference包含在严格模式代码中，并且Identifier的StringValue是“ eval”或“ arguments”，则返回 strict。
2. 返回 simple

IdentifierReference : yield

1. 返回 simple

IdentifierReference : await

1. 返回 simple

### 12.1.4 静态语义：StringValue <div id="sec-identifiers-static-semantics-stringvalue"></div>

IdentifierReference : yield
BindingIdentifier : yield
LabelIdentifier : yield

1. 返回 "yield"。

IdentifierReference : await
BindingIdentifier : await
LabelIdentifier : await

1. 返回 "await".

Identifier : IdentifierName but not ReservedWord

1. 返回IdentifierName的StringValue

### 12.1.5 运行时语义：BindingInitialization <div id="sec-identifiers-runtime-semantics-bindinginitialization"></div>

具有参数值和环境

> 注：为环境传递undefined，以指示应该使用PutValue操作来分配初始化值。某些非严格函数的var语句和形式参数列表就是这种情况（请参见9.2.15）。在这些情况下，在执行其初始值设定项之前会先挂起并预初始化词法绑定

BindingIdentifier : Identifier

1. 假设name为Identifier的StringValue。
2. 返回 ? InitializeBoundName(name, value, environment).

BindingIdentifier : yield
1. 返回 ? InitializeBoundName("yield", value, environment).

BindingIdentifier : await

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

IdentifierReference : Identifier

1. 返回 ? ResolveBinding(StringValue of Identifier).

IdentifierReference : yield

2. 返回 ? ResolveBinding("yield").

IdentifierReference : await

3. 返回 ? ResolveBinding("await").

> 注 1： 执行IdentifierReference的结果始终是Reference类型的值。
>
> 注 2：在非严格代码中，关键字yield可以用作标识符。执行IdentifierReference可以解决yield的绑定，就好像它是一个Identifier一样。早期错误限制可确保仅执行非严格代码。

## 12.2 主要表达式 <div id="sec-primary-expression"></div>

语法

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

补充语法

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

1. Return the ParenthesizedExpression that is covered by CoverParenthesizedExpressionAndArrowParameterList

#### 12.2.1.2 静态语义：HasName <div id="sec-semantics-static-semantics-hasname"></div>

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let expr be CoveredParenthesizedExpression of CoverParenthesizedExpressionAndArrowParameterList.
2. If IsFunctionDefinition of expr is false, return false.
3. Return HasName of expr.

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

1. Return false.

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let expr be CoveredParenthesizedExpression of CoverParenthesizedExpressionAndArrowParameterList.
2. Return IsFunctionDefinition of expr.

#### 12.2.1.4 静态语义：IsIdentifierRef <div id="sec-semantics-static-semantics-isidentifierref"></div>

```
PrimaryExpression : IdentifierReference
```

1. Return true

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

1. Return false.

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

1. Return invalid.

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let expr be CoveredParenthesizedExpression of CoverParenthesizedExpressionAndArrowParameterList.
2. Return AssignmentTargetType of expr.

### 12.2.2 this 关键字 <div id="sec-this-keyword"></div>

### 12.2.2.1 运行时语义：Evaluation <div id="sec-this-keyword-runtime-semantics-evaluation"></div>

```
PrimaryExpression : this
```

1. Return ? ResolveThisBinding().

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

1. Return null.

```
Literal : BooleanLiteral
```

1. If BooleanLiteral is the token false, return false.
2. If BooleanLiteral is the token true, return true.

```
Literal : NumericLiteral
```

1. Return the number whose value is MV of NumericLiteral as defined in 11.8.3.

```
Literal : StringLiteral
```

1. Return the StringValue of StringLiteral as defined in 11.8.4.1.

### 12.2.5 数组初始化 <div id="sec-array-initializer"></div>
>注：ArrayLiteral是一个表达式，它使用一个列表描述一个Array对象的初始化，该列表包含零个或多个表达式，每个表达式代表一个数组元素，并用方括号括起来。这些元素不必是文字。每次评估数组初始值设定项时都会对它们进行评估。
数组元素可以省略在元素列表的开始，中间或结尾。只要元素列表中的逗号前面没有AssignmentExpression（即开头或另一个逗号之后的逗号），丢失的数组元素就会增加Array的长度并增加后续元素的索引。未定义省略的数组元素。如果在数组的末尾省略了一个元素，则该元素不会对数组的长度产生影响。

语法

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

1. Return the numeric value 1.

```
Elision : Elision ,
```

1. Let preceding be the ElisionWidth of Elision.
2. Return preceding + 1.

#### 12.2.5.2 运行时语义：ArrayAccumulation <div id="sec-runtime-semantics-arrayaccumulation"></div>

带有参数array和nextIndex。

```
ElementList : Elision AssignmentExpression
```

1. Let padding be the ElisionWidth of Elision; if Elision is not present, use the numeric value zero.
2. Let initResult be the result of evaluating AssignmentExpression.
3. Let initValue be ? GetValue(initResult).
4. Let created be CreateDataProperty(array, ToString(ToUint32(nextIndex + padding)), initValue).
5. Assert: created is true.
6. Return nextIndex + padding + 1.

```
ElementList : Elision SpreadElement
```

1. Let padding be the ElisionWidth of Elision; if Elision is not present, use the numeric value zero.
2. Return the result of performing ArrayAccumulation for SpreadElement with arguments array and nextIndex + padding.

```
ElementList : ElementList , Elision AssignmentExpression
```

1. Let postIndex be the result of performing ArrayAccumulation for ElementList with arguments array and nextIndex.
2. ReturnIfAbrupt(postIndex).
3. Let padding be the ElisionWidth of Elision; if Elision is not present, use the numeric value zero.
4. Let initResult be the result of evaluating AssignmentExpression.
5. Let initValue be ? GetValue(initResult).
6. Let created be CreateDataProperty(array, ToString(ToUint32(postIndex + padding)), initValue).
7. Assert: created is true.
8. Return postIndex + padding + 1.

```
ElementList : ElementList , Elision SpreadElement
```

1. Let postIndex be the result of performing ArrayAccumulation for ElementList with arguments array and nextIndex.
2. ReturnIfAbrupt(postIndex).
3. Let padding be the ElisionWidth of Elision; if Elision is not present, use the numeric value zero.
4. Return the result of performing ArrayAccumulation for SpreadElement with arguments array and postIndex + padding.

```
SpreadElement : ... AssignmentExpression
```

1. Let spreadRef be the result of evaluating AssignmentExpression.
2. Let spreadObj be ? GetValue(spreadRef).
3. Let iteratorRecord be ? GetIterator(spreadObj).
4. Repeat,
a. Let next be ? IteratorStep(iteratorRecord).
b. If next is false, return nextIndex.
c. Let nextValue be ? IteratorValue(next).
d. Let status be CreateDataProperty(array, ToString(ToUint32(nextIndex)), nextValue).
e. Assert: status is true.
f. Increase nextIndex by 1.

> 注：CreateDataProperty用于确保为数组定义了自己的属性，即使已经以无法使用[[Set]]创建新的自己的属性的方式修改了标准内置Array原型对象。

#### 12.2.5.3 运行时语义：Evaluation <div id="sec-array-initializer-runtime-semantics-evaluation"></div>

```
ArrayLiteral : [ Elision ]
```

1. Let array be ! ArrayCreate(0).
2. Let pad be the ElisionWidth of Elision; if Elision is not present, use the numeric value zero.
3. Perform Set(array, "length", ToUint32(pad), false).
4. NOTE: The above Set cannot fail because of the nature of the object returned by ArrayCreate.
5. Return array

```
ArrayLiteral : [ ElementList ]
```

1. Let array be ! ArrayCreate(0).
2. Let len be the result of performing ArrayAccumulation for ElementList with arguments array and 0.
3. ReturnIfAbrupt(len).
4. Perform Set(array, "length", ToUint32(len), false).
5. NOTE: The above Set cannot fail because of the nature of the object returned by ArrayCreate.
6. Return array.

```
ArrayLiteral : [ ElementList , Elision ]
```

1. Let array be ! ArrayCreate(0).
2. Let len be the result of performing ArrayAccumulation for ElementList with arguments array and 0.
3. ReturnIfAbrupt(len).
4. Let padding be the ElisionWidth of Elision; if Elision is not present, use the numeric value zero.
5. Perform Set(array, "length", ToUint32(padding + len), false).
6. NOTE: The above Set cannot fail because of the nature of the object returned by ArrayCreate.
7. Return array.

### 12.2.6 对象初始化 <div id="sec-object-initializer"></div>

> 注1：对象初始化程序是一种描述对象初始化的表达式，以类似于文字的形式编写。它是零对或更多对属性键和相关值的列表，并用大括号括起来。这些值不必是文字。每次评估对象初始值设定项时都对它们进行评估。

语法：

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

​	如果MethodDefinition的HasDirectSuper为true，则为语法错误。

除了描述实际的对象初始化程序之外，ObjectLiteral生成还用作ObjectAssignmentPattern的封面语法，并且可以识别为CoverParenthesizedExpressionAndArrowParameterList的一部分。当在要求ObjectAssignmentPattern的上下文中出现ObjectLiteral时，则不会应用以下早期错误规则。此外，在最初解析CoverParenthesizedExpressionAndArrowParameterList或CoverCallExpressionAndAsyncArrowHead时，不会应用它们。

```
PropertyDefinition : CoverInitializedName
```

​	如果代码与此产品匹配，则始终抛出语法错误。

> 注：存在这种产生方式是为了使ObjectLiteral可以用作ObjectAssignmentPattern的封面语法。它不能在实际的对象初始化程序中发生。

#### 12.2.6.2 静态语义：ComputedPropertyContains <div id="sec-object-initializer-static-semantics-computedpropertycontains"></div>

带有参数符号。

```
PropertyName : LiteralPropertyName
```

1. Return false.

```
PropertyName : ComputedPropertyName
```

1. Return the result of ComputedPropertyName Contains symbol.

#### 12.2.6.3 静态语义：Contains <div id="sec-object-initializer-static-semantics-contains"></div>

带有参数符号。

```
PropertyDefinition : MethodDefinition
```

1. If symbol is MethodDefinition, return true.
2. Return the result of ComputedPropertyContains for MethodDefinition with argument symbol

> 注：依赖于子结构的静态语义规则通常不会考虑功能定义。

```
LiteralPropertyName : IdentifierName
```

1. If symbol is a ReservedWord, return false.
2. If symbol is an Identifier and StringValue of symbol is the same value as the StringValue of IdentifierName, return
true.
3. Return false.

#### 12.2.6.4 静态语义：IsComputedPropertyKey <div id="sec-static-semantics-iscomputedpropertykey"></div>

```
PropertyName : LiteralPropertyName
```

1. Return false.

```
PropertyName : ComputedPropertyName
```

1. Return true.

#### 12.2.6.5 静态语义：PropName <div id="sec-object-initializer-static-semantics-propname"></div>

```
PropertyDefinition : IdentifierReference
```

1. Return StringValue of IdentifierReference.

```
PropertyDefinition : ... AssignmentExpression
```

1. Return empty.

```
PropertyDefinition : PropertyName : AssignmentExpression
```

1. Return PropName of PropertyName.

```
LiteralPropertyName : IdentifierName
```

1. Return StringValue of IdentifierName.

```
LiteralPropertyName : StringLiteral
```

1. Return the String value whose code units are the SV of the StringLiteral.

```
LiteralPropertyName : NumericLiteral
```

1. Let nbr be the result of forming the value of the NumericLiteral.
2. Return ! ToString(nbr).

```
ComputedPropertyName : [ AssignmentExpression ]
```

1. Return empty

#### 12.2.6.6 静态语义：PropertyNameList <div id="sec-static-semantics-propertynamelist"></div>

```
PropertyDefinitionList : PropertyDefinition
```

1. If PropName of PropertyDefinition is empty, return a new empty List.
2. Return a new List containing PropName of PropertyDefinition.

```
PropertyDefinitionList : PropertyDefinitionList , PropertyDefinition
```

1. Let list be PropertyNameList of PropertyDefinitionList.
2. If PropName of PropertyDefinition is empty, return list.
3. Append PropName of PropertyDefinition to the end of list.
4. Return list

#### 12.2.6.7 运行时语义：Evaluation <div id="sec-object-initializer-runtime-semantics-evaluation"></div>

```
ObjectLiteral : { }
```

1. Return ObjectCreate(%ObjectPrototype%).

```
ObjectLiteral :
    { PropertyDefinitionList }
    { PropertyDefinitionList , }
```

1. Let obj be ObjectCreate(%ObjectPrototype%).
2. Perform ? PropertyDefinitionEvaluation of PropertyDefinitionList with arguments obj and true.
3. Return obj.

```
LiteralPropertyName : IdentifierName
```

1. Return StringValue of IdentifierName

```
LiteralPropertyName : StringLiteral
```

1. Return the String value whose code units are the SV of the StringLiteral.

```
LiteralPropertyName : NumericLiteral
```

1. Let nbr be the result of forming the value of the NumericLiteral.
2. Return ! ToString(nbr).

```
ComputedPropertyName : [ AssignmentExpression ]
```

1. Let exprValue be the result of evaluating AssignmentExpression.
2. Let propName be ? GetValue(exprValue).
3. Return ? ToPropertyKey(propName).

#### 12.2.6.8 运行时语义：PropertyDefinitionEvaluation <div id="sec-object-initializer-runtime-semantics-propertydefinitionevaluation"></div>

带参数的对象和枚举。

```
PropertyDefinitionList : PropertyDefinitionList , PropertyDefinition
```

1. Perform ? PropertyDefinitionEvaluation of PropertyDefinitionList with arguments object and enumerable.
2. Return the result of performing PropertyDefinitionEvaluation of PropertyDefinition with arguments object and enumerable.

```
PropertyDefinition : ... AssignmentExpression
```

1. Let exprValue be the result of evaluating AssignmentExpression.
2. Let fromValue be ? GetValue(exprValue).
3. Let excludedNames be a new empty List.
4. Return ? CopyDataProperties(object, fromValue, excludedNames).

```
PropertyDefinition : IdentifierReference
```

1. Let propName be StringValue of IdentifierReference.
2. Let exprValue be the result of evaluating IdentifierReference.
3. Let propValue be ? GetValue(exprValue).
4. Assert: enumerable is true.
5. Assert: object is an ordinary, extensible object with no non-configurable properties.
6. Return ! CreateDataPropertyOrThrow(object, propName, propValue).

```
PropertyDefinition : PropertyName : AssignmentExpression
```

1. Let propKey be the result of evaluating PropertyName.
2. ReturnIfAbrupt(propKey).
3. If IsAnonymousFunctionDefinition(AssignmentExpression) is true, then
a. Let propValue be the result of performing NamedEvaluation for AssignmentExpression with argument propKey.
4. Else,
a. Let exprValueRef be the result of evaluating AssignmentExpression.
b. Let propValue be ? GetValue(exprValueRef).
5. Assert: enumerable is true.
6. Assert: object is an ordinary, extensible object with no non-configurable properties.
7. Return ! CreateDataPropertyOrThrow(object, propKey, propValue).

> 注：B.3.1中给出了这种生产的另一种语义。

### 12.2.7 函数定义表达式 <div id="sec-function-defining-expressions"></div>

有关PrimaryExpression：FunctionExpression，请参见14.1。

有关PrimaryExpression：GeneratorExpression，请参见14.4。

有关PrimaryExpression：ClassExpression，请参见14.6。

有关PrimaryExpression：AsyncFunctionExpression，请参见14.7。

有关PrimaryExpression：AsyncGeneratorExpression，请参见14.5。

### 12.2.8 正则表达式字面量 <div id="sec-primary-expression-regular-expression-literals"></div>

语法

参见 11.8.5

#### 12.2.8.1 静态语义：Early Errors <div id="sec-primary-expression-regular-expression-literals-static-semantics-early-errors"></div>

```
PrimaryExpression : RegularExpressionLiteral
```

​	如果无法使用21.2.1中指定的ECMAScript RegExp语法的目标符号Pattern识别RegularExpressionLiteral的BodyText，则会出现语法错误。如果RegularExpressionLiteral的FlagText包含除“ g”，“ i”，“ m”，“ s”，“ u”或“ y”之外的任何代码点，或者包含的相同代码点多于，则是语法错误。一旦。

#### 12.2.8.2 运行时语义：Evaluation <div id="sec-regular-expression-literals-runtime-semantics-evaluation"></div>

```
PrimaryExpression : RegularExpressionLiteral
```

1. Let pattern be the String value consisting of the UTF16Encoding of each code point of BodyText of
RegularExpressionLiteral.
2. Let flags be the String value consisting of the UTF16Encoding of each code point of FlagText of
RegularExpressionLiteral.
3. Return RegExpCreate(pattern, flags).

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

​	如果TemplateLiteral的TemplateStrings的结果中带有参数false的元素数大于232-1，则是语法错误。
​	如果未设置[Tagged]参数并且NoSubstitutionTemplate包含NotEscapeSequence，则为语法错误。

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

1. If raw is false, then
a. Let string be the TV of NoSubstitutionTemplate.
2. Else,
a. Let string be the TRV of NoSubstitutionTemplate.
3. Return a List containing the single element, string.

```
SubstitutionTemplate : TemplateHead Expression TemplateSpans
```

1. If raw is false, then
a. Let head be the TV of TemplateHead.
2. Else,
a. Let head be the TRV of TemplateHead.
3. Let tail be TemplateStrings of TemplateSpans with argument raw.
4. Return a List containing head followed by the elements, in order, of tail.

```
TemplateSpans : TemplateTail
```

1. If raw is false, then
a. Let tail be the TV of TemplateTail.
2. Else,
a. Let tail be the TRV of TemplateTail.
3. Return a List containing the single element, tail.

```
TemplateSpans : TemplateMiddleList TemplateTail
```

1. Let middle be TemplateStrings of TemplateMiddleList with argument raw.
2. If raw is false, then
a. Let tail be the TV of TemplateTail.

3. Else,
a. Let tail be the TRV of TemplateTail.
4. Return a List containing the elements, in order, of middle followed by tail.

```
TemplateMiddleList : TemplateMiddle Expression
```

1. If raw is false, then
a. Let string be the TV of TemplateMiddle.
2. Else,
a. Let string be the TRV of TemplateMiddle.
3. Return a List containing the single element, string.

```
TemplateMiddleList : TemplateMiddleList TemplateMiddle Expression
```

1. Let front be TemplateStrings of TemplateMiddleList with argument raw.
2. If raw is false, then
a. Let last be the TV of TemplateMiddle.
3. Else,
a. Let last be the TRV of TemplateMiddle.
4. Append last as the last element of the List front.
5. Return front.

#### 12.2.9.3 运行时语义：ArgumentListEvaluation <div id="sec-template-literals-runtime-semantics-argumentlistevaluation"></div>

```
TemplateLiteral : NoSubstitutionTemplate
```

1. Let templateLiteral be this TemplateLiteral.
2. Let siteObj be GetTemplateObject(templateLiteral).
3. Return a List containing the one element which is siteObj.

```
SubstitutionTemplate : TemplateHead Expression TemplateSpans
```

1. Let templateLiteral be this TemplateLiteral.
2. Let siteObj be GetTemplateObject(templateLiteral).
3. Let firstSubRef be the result of evaluating Expression.
4. Let firstSub be ? GetValue(firstSubRef).
5. Let restSub be SubstitutionEvaluation of TemplateSpans.
6. ReturnIfAbrupt(restSub).
7. Assert: restSub is a List.
8. Return a List whose first element is siteObj, whose second elements is firstSub, and whose subsequent elements are
the elements of restSub, in order. restSub may contain no elements.

#### 12.2.9.4 运行时语义：GetTemplateObject ( templateLiteral ) <div id="sec-gettemplateobject"></div>

使用解析节点templateLiteral作为参数调用抽象操作GetTemplateObject。它执行以下步骤：

1. Let rawStrings be TemplateStrings of templateLiteral with argument true.
2. Let realm be the current Realm Record.
3. Let templateRegistry be realm.[[TemplateMap]].
4. For each element e of templateRegistry, do
    a. If e.[[Site]] is the same Parse Node as templateLiteral, then
  1. Return e.[[Array]].
5. Let cookedStrings be TemplateStrings of templateLiteral with argument false.
6. Let count be the number of elements in the List cookedStrings.
7. Assert: count ≤ 232 - 1.
8. Let template be ! ArrayCreate(count).
9. Let rawObj be ! ArrayCreate(count).
10. Let index be 0.
11. Repeat, while index < count
    a. Let prop be ! ToString(index).
    b. Let cookedValue be the String value cookedStrings[index].
    c. Call template.\[\[DefineOwnProperty]](prop, PropertyDescriptor { [[Value]]: cookedValue, [[Writable]]: false,
    [[Enumerable]]: true, [[Configurable]]: false }).
    d. Let rawValue be the String value rawStrings[index].
    e. Call rawObj.\[\[DefineOwnProperty]](prop, PropertyDescriptor { [[Value]]: rawValue, [[Writable]]: false,
    [[Enumerable]]: true, [[Configurable]]: false }).
    f. Increase index by 1.
12. Perform SetIntegrityLevel(rawObj, "frozen").
13. Call template.\[\[DefineOwnProperty]]("raw", PropertyDescriptor { [[Value]]: rawObj, [[Writable]]: false,
    [[Enumerable]]: false, [[Configurable]]: false }).
14. Perform SetIntegrityLevel(template, "frozen").
15. Append the Record { [[Site]]: templateLiteral, [[Array]]: template } to templateRegistry.
16. Return template.

> 注1：模板对象的创建不能导致突然完成。
>
> 注2：领域的程序代码中的每个TemplateLiteral都与唯一的模板对象相关联，该对象用于评估带标签的模板（12.2.9.6）。模板对象被冻结，并且每次评估特定的标记模板时都使用相同的模板对象。模板对象是在对TemplateLiteral进行第一次评估时是懒惰地创建还是在第一次评估之前急切地创建，这是ECMAScript代码无法观察到的实现选择
>
> 注3：本规范的未来版本可能会定义模板对象的其他不可枚举的属性。

#### 12.2.9.5 运行时语义：SubstitutionEvaluation <div id="sec-runtime-semantics-substitutionevaluation"></div>

```
TemplateSpans : TemplateTail
```

1. Return a new empty List.

```
TemplateSpans : TemplateMiddleList TemplateTail
```

1. Return the result of SubstitutionEvaluation of TemplateMiddleList.

```
TemplateMiddleList : TemplateMiddle Expression
```

1. Let subRef be the result of evaluating Expression.
2. Let sub be ? GetValue(subRef).
3. Return a List containing only sub.

```
TemplateMiddleList : TemplateMiddleList TemplateMiddle Expression
```

1. Let preceding be the result of SubstitutionEvaluation of TemplateMiddleList.
2. ReturnIfAbrupt(preceding).
3. Let nextRef be the result of evaluating Expression.
4. Let next be ? GetValue(nextRef).
5. Append next as the last element of the List preceding.
6. Return preceding.

#### 12.2.9.6 运行时语义：Evaluation <div id="sec-template-literals-runtime-semantics-evaluation"></div>

```
TemplateLiteral : NoSubstitutionTemplate
```

1. Return the String value whose code units are the elements of the TV of NoSubstitutionTemplate as defined in 11.8.6.

```
SubstitutionTemplate : TemplateHead Expression TemplateSpans
```

1. Let head be the TV of TemplateHead as defined in 11.8.6.
2. Let subRef be the result of evaluating Expression.
3. Let sub be ? GetValue(subRef).
4. Let middle be ? ToString(sub).
5. Let tail be the result of evaluating TemplateSpans.
6. ReturnIfAbrupt(tail).
7. Return the string-concatenation of head, middle, and tail.

> 注1：应用于Expression值的字符串转换语义类似于String.prototype.concat而不是+运算符。

```
TemplateSpans : TemplateTail
```

1. Let tail be the TV of TemplateTail as defined in 11.8.6.
2. Return the String value consisting of the code units of tail.

```
TemplateSpans : TemplateMiddleList TemplateTail
```

1. Let head be the result of evaluating TemplateMiddleList.
2. ReturnIfAbrupt(head).
3. Let tail be the TV of TemplateTail as defined in 11.8.6.
4. Return the string-concatenation of head and tail.

```
TemplateMiddleList : TemplateMiddle Expression
```

1. Let head be the TV of TemplateMiddle as defined in 11.8.6.
2. Let subRef be the result of evaluating Expression.
3. Let sub be ? GetValue(subRef).
4. Let middle be ? ToString(sub).
5. Return the sequence of code units consisting of the code units of head followed by the elements of middle.

> 注2：应用于Expression值的字符串转换语义类似于String.prototype.concat而不是+运算符。

```
TemplateMiddleList : TemplateMiddleList TemplateMiddle Expression
```

1. Let rest be the result of evaluating TemplateMiddleList.
2. ReturnIfAbrupt(rest).
3. Let middle be the TV of TemplateMiddle as defined in 11.8.6.
4. Let subRef be the result of evaluating Expression.
5. Let sub be ? GetValue(subRef).
6. Let last be ? ToString(sub).
7. Return the sequence of code units consisting of the elements of rest followed by the code units of middle followed
by the elements of last.

> 注3：应用于Expression值的字符串转换语义类似于String.prototype.concat而不是+运算符。

### 12.2.10 群组运算符 <div id="sec-grouping-operator"></div>

#### 12.2.10.1 静态语义：Early Errors <div id="sec-grouping-operator-static-semantics-early-errors"></div>

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

​	如果CoverParenthesizedExpressionAndArrowParameterList没有涵盖括号表达式，则是语法错误。

​	所有适用于括号表示的早期错误规则及其派生结果，也适用于CoverParenthesizedExpressionAndArrowParameterList的CoveredParenthesizedExpression。

#### 12.2.10.2 静态语义：IsFunctionDefinition <div id="sec-grouping-operator-static-semantics-isfunctiondefinition"></div>

```
ParenthesizedExpression : ( Expression )
```

1. Return IsFunctionDefinition of Expression.

#### 12.2.10.3 静态语义：AssignmentTargetType <div id="sec-grouping-operator-static-semantics-assignmenttargettype"></div>

```
ParenthesizedExpression : ( Expression )
```

1. Return AssignmentTargetType of Expression.

#### 12.2.10.4 运行时语义：NamedEvaluation <div id="sec-grouping-operator-runtime-semantics-namedevaluation"></div>

带有参数名称。

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let expr be CoveredParenthesizedExpression of CoverParenthesizedExpressionAndArrowParameterList.
2. Return the result of performing NamedEvaluation for expr with argument name.

```
ParenthesizedExpression : ( Expression )
```

1. Assert: IsAnonymousFunctionDefinition(Expression) is true.

2. Return the result of performing NamedEvaluation for Expression with argument name.

#### 12.2.10.5 运行时语义：Evaluation <div id="sec-grouping-operator-runtime-semantics-evaluation"></div>

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let expr be CoveredParenthesizedExpression of CoverParenthesizedExpressionAndArrowParameterList.
2. Return the result of evaluating expr.

```
ParenthesizedExpression : ( Expression )
```

1. Return the result of evaluating Expression. This may be of type Reference

> 注：该算法不会将GetValue应用于评估Expression的结果。这样做的主要动机是可以将诸如delete和typeof之类的运算符应用于带括号的表达式。

## 12.3 左值表达式 <div id="sec-left-hand-side-expressions"></div>

语法

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

1. Return the CallMemberExpression that is covered by CoverCallExpressionAndAsyncArrowHead.

#### 12.3.1.2 静态语义：Contains <div id="sec-static-semantics-static-semantics-contains"></div>

带有参数符号。

```
MemberExpression : MemberExpression . IdentifierName
```

1. If MemberExpression Contains symbol is true, return true.
2. If symbol is a ReservedWord, return false.
3. If symbol is an Identifier and StringValue of symbol is the same value as the StringValue of IdentifierName, return
true.
4. Return false

```
SuperProperty : super . IdentifierName
```

1. If symbol is the ReservedWord super, return true.
2. If symbol is a ReservedWord, return false.
3. If symbol is an Identifier and StringValue of symbol is the same value as the StringValue of IdentifierName, return
true.
4. Return false.

```
CallExpression : CallExpression . IdentifierName
```

1. If CallExpression Contains symbol is true, return true.
2. If symbol is a ReservedWord, return false.
3. If symbol is an Identifier and StringValue of symbol is the same value as the StringValue of IdentifierName, return
true.
4. Return false.s

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

1. Return false

#### 12.3.1.4 静态语义：IsDestructuring <div id="sec-static-semantics-static-semantics-isdestructuring"></div>

```
MemberExpression : PrimaryExpression
```

1. If PrimaryExpression is either an ObjectLiteral or an ArrayLiteral, return true.

2. Return false.

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

   1. Return false.

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

1. Return false.

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

1. Return simple.

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

1. Return invalid.

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
> 其中，<identifier-name-string>是对IdentifierName的StringValue求值的结果。

#### 12.3.2.1 运行时语义：Evaluation <div id="sec-property-accessors-runtime-semantics-evaluation"></div>

```
MemberExpression : MemberExpression [ Expression ]
```

1. Let baseReference be the result of evaluating MemberExpression.
2. Let baseValue be ? GetValue(baseReference).
3. Let propertyNameReference be the result of evaluating Expression.
4. Let propertyNameValue be ? GetValue(propertyNameReference).
5. Let bv be ? RequireObjectCoercible(baseValue).
6. Let propertyKey be ? ToPropertyKey(propertyNameValue).
7. If the code matched by this MemberExpression is strict mode code, let strict be true, else let strict be false.
8. Return a value of type Reference whose base value component is bv, whose referenced name component is
propertyKey, and whose strict reference flag is strict.

```
MemberExpression : MemberExpression . IdentifierName
```

1. Let baseReference be the result of evaluating MemberExpression.
2. Let baseValue be ? GetValue(baseReference).
3. Let bv be ? RequireObjectCoercible(baseValue).
4. Let propertyNameString be StringValue of IdentifierName.
5. If the code matched by this MemberExpression is strict mode code, let strict be true, else let strict be false.
6. Return a value of type Reference whose base value component is bv, whose referenced name component is
propertyNameString, and whose strict reference flag is strict.

```
CallExpression : CallExpression [ Expression ]
```

以与MemberExpression：MemberExpression [Expression]完全相同的方式求值，除了所包含的CallExpression在步骤1中求值。

```
CallExpression : CallExpression . IdentifierName
```

以与MemberExpression：MemberExpression完全相同的方式求值。IdentifierName，但所包含的CallExpression在步骤1中求值。

### 12.3.3 new 运算符 <div id="sec-new-operator"></div>

#### 12.3.3.1 运行时语义：Evaluation <div id="sec-new-operator-runtime-semantics-evaluation"></div>

```
NewExpression : new NewExpression
```

1. Return ? EvaluateNew(NewExpression, empty)

```
MemberExpression : new MemberExpression Arguments
```

1. Return ? EvaluateNew(MemberExpression, Arguments).

##### 12.3.3.1.1 运行时语义：EvaluateNew ( constructExpr, arguments ) <div id="sec-evaluatenew"></div>

抽象操作EvaluateNew带有参数ConstructExpr，而参数执行以下步骤：

1. Assert: constructExpr is either a NewExpression or a MemberExpression.
2. Assert: arguments is either empty or an Arguments.
3. Let ref be the result of evaluating constructExpr.
4. Let constructor be ? GetValue(ref).
5. If arguments is empty, let argList be a new empty List.
6. Else,
a. Let argList be ArgumentListEvaluation of arguments.
b. ReturnIfAbrupt(argList).
7. If IsConstructor(constructor) is false, throw a TypeError exception.
8. Return ? Construct(constructor, argList).

### 12.3.4 函数回调 <div id="sec-function-calls"></div>
#### 12.3.4.1 运行时语义：Evaluation <div id="sec-function-calls-runtime-semantics-evaluation"></div>

```
CallExpression : CoverCallExpressionAndAsyncArrowHead
```

1. Let expr be CoveredCallExpression of CoverCallExpressionAndAsyncArrowHead.
2. Let memberExpr be the MemberExpression of expr.
3. Let arguments be the Arguments of expr.
4. Let ref be the result of evaluating memberExpr.
5. Let func be ? GetValue(ref).
6. If Type(ref) is Reference and IsPropertyReference(ref) is false and GetReferencedName(ref) is "eval", then
   a. If SameValue(func, %eval%) is true, then
   i. Let argList be ? ArgumentListEvaluation of arguments.
   ii. If argList has no elements, return undefined.
   iii. Let evalText be the first element of argList.
   iv. If the source code matching this CallExpression is strict mode code, let strictCaller be true. Otherwise
   let strictCaller be false.
   v. Let evalRealm be the current Realm Record.
   vi. Perform ? HostEnsureCanCompileStrings(evalRealm, evalRealm).
   vii. Return ? PerformEval(evalText, evalRealm, strictCaller, true).
7. Let thisCall be this CallExpression.
8. Let tailCall be IsInTailPosition(thisCall).
9. Return ? EvaluateCall(func, ref, arguments, tailCall).

执行步骤6.a.vii的CallExpression评估是直接评估。

```
CallExpression : CallExpression Arguments
```

1. Let ref be the result of evaluating CallExpression.
2. Let func be ? GetValue(ref).
3. Let thisCall be this CallExpression.
4. Let tailCall be IsInTailPosition(thisCall).
5. Return ? EvaluateCall(func, ref, Arguments, tailCall).

#### 12.3.4.2 运行时语义：EvaluateCall ( func, ref, arguments, tailPosition ) <div id="sec-evaluatecall"></div>

抽象操作EvaluateCall将值func，值ref，解析节点参数和布尔参数tailPosition用作参数。它执行以下步骤：

1. If Type(ref) is Reference, then
a. If IsPropertyReference(ref) is true, then
i. Let thisValue be GetThisValue(ref).
b. Else the base of ref is an Environment Record,
i. Let refEnv be GetBase(ref).
ii. Let thisValue be refEnv.WithBaseObject().
2. Else Type(ref) is not Reference,
a. Let thisValue be undefined.
3. Let argList be ArgumentListEvaluation of arguments.
4. ReturnIfAbrupt(argList).
5. If Type(func) is not Object, throw a TypeError exception.
6. If IsCallable(func) is false, throw a TypeError exception.
7. If tailPosition is true, perform PrepareForTailCall().
8. Let result be Call(func, thisValue, argList).
9. Assert: If tailPosition is true, the above call will not return here, but instead evaluation will continue as if the
following return has already occurred.
10. Assert: If result is not an abrupt completion, then Type(result) is an ECMAScript language type.
11. Return result.

### 12.3.5super 关键字 <div id="sec-super-keyword"></div>
#### 12.3.5.1 运行时语义：Evaluation <div id="sec-super-keyword-runtime-semantics-evaluation"></div>

```
SuperProperty : super [ Expression ]
```

1. Let env be GetThisEnvironment().
2. Let actualThis be ? env.GetThisBinding().
3. Let propertyNameReference be the result of evaluating Expression.
4. Let propertyNameValue be ? GetValue(propertyNameReference).
5. Let propertyKey be ? ToPropertyKey(propertyNameValue).
6. If the code matched by this SuperProperty is strict mode code, let strict be true, else let strict be false.
7. Return ? MakeSuperPropertyReference(actualThis, propertyKey, strict).

```
SuperProperty : super . IdentifierName
```

1. Let env be GetThisEnvironment().
2. Let actualThis be ? env.GetThisBinding().
3. Let propertyKey be StringValue of IdentifierName.
4. If the code matched by this SuperProperty is strict mode code, let strict be true, else let strict be false.
5. Return ? MakeSuperPropertyReference(actualThis, propertyKey, strict).

```
SuperCall : super Arguments
```

1. Let newTarget be GetNewTarget().
2. Assert: Type(newTarget) is Object.
3. Let func be ? GetSuperConstructor().
4. Let argList be ArgumentListEvaluation of Arguments.
5. ReturnIfAbrupt(argList).
6. Let result be ? Construct(func, argList, newTarget).
7. Let thisER be GetThisEnvironment().
8. Return ? thisER.BindThisValue(result).

#### 12.3.5.2 运行时语义：GetSuperConstructor ( ) <div id="sec-getsuperconstructor"></div>

抽象操作GetSuperConstructor执行以下步骤：

1. Let envRec be GetThisEnvironment().
2. Assert: envRec is a function Environment Record.
3. Let activeFunction be envRec.[[FunctionObject]].
4. Assert: activeFunction is an ECMAScript function object.
5. Let superConstructor be ! activeFunction.\[\[GetPrototypeOf]]().
6. If IsConstructor(superConstructor) is false, throw a TypeError exception.
7. Return superConstructor

#### 12.3.5.3 运行时语义：MakeSuperPropertyReference ( actualThis, propertyKey, strict ) <div id="sec-makesuperpropertyreference"></div>

抽象操作MakeSuperPropertyReference带有参数ActualThis，propertyKey和strict执行以下步骤：

1. Let env be GetThisEnvironment().
2. Assert: env.HasSuperBinding() is true.
3. Let baseValue be ? env.GetSuperBase().
4. Let bv be ? RequireObjectCoercible(baseValue).
5. Return a value of type Reference that is a Super Reference whose base value component is bv, whose referenced name component is propertyKey, whose thisValue component is actualThis, and whose strict reference flag is strict.

### 12.3.6 参数列表 <div id="sec-argument-lists"></div>

> 注：对参数列表的求值将生成值列表。

#### 12.3.6.1 运行时语义：ArgumentListEvaluation <div id="sec-argument-lists-runtime-semantics-argumentlistevaluation"></div>

```
Arguments : ( )
```

1. Return a new empty List.

```
ArgumentList : AssignmentExpression
```

1. Let ref be the result of evaluating AssignmentExpression.
2. Let arg be ? GetValue(ref).
3. Return a List whose sole item is arg.

```
ArgumentList : ... AssignmentExpression
```

1. Let list be a new empty List.
2. Let spreadRef be the result of evaluating AssignmentExpression.
3. Let spreadObj be ? GetValue(spreadRef).
4. Let iteratorRecord be ? GetIterator(spreadObj).
5. Repeat,
a. Let next be ? IteratorStep(iteratorRecord).
b. If next is false, return list.
c. Let nextArg be ? IteratorValue(next).
d. Append nextArg as the last element of list

```
ArgumentList : ArgumentList , AssignmentExpression
```

1. Let precedingArgs be ArgumentListEvaluation of ArgumentList.
2. ReturnIfAbrupt(precedingArgs).
3. Let ref be the result of evaluating AssignmentExpression.
4. Let arg be ? GetValue(ref).
5. Append arg to the end of precedingArgs.
6. Return precedingArgs.

```
ArgumentList : ArgumentList , ... AssignmentExpression
```

1. Let precedingArgs be ArgumentListEvaluation of ArgumentList.

2. ReturnIfAbrupt(precedingArgs).

3. Let spreadRef be the result of evaluating AssignmentExpression.

4. Let iteratorRecord be ? GetIterator(? GetValue(spreadRef)).

5. Repeat,
  a. Let next be ? IteratorStep(iteratorRecord).
  b. If next is false, return precedingArgs.
  c. Let nextArg be ? IteratorValue(next).

  d. Append nextArg as the last element of precedingArgs.

### 12.3.7 标记模板 <div id="sec-tagged-templates"></div>

> 注：带标签的模板是一个函数调用，其中调用的参数是从TemplateLiteral（12.2.9）派生的。实际参数包括模板对象（12.2.9.4）和通过评估嵌入在TemplateLiteral中的表达式产生的值。

#### 12.3.7.1 运行时语义：Evaluation <div id="sec-tagged-templates-runtime-semantics-evaluation"></div>

```
MemberExpression : MemberExpression TemplateLiteral
```

1. Let tagRef be the result of evaluating MemberExpression.
2. Let tagFunc be ? GetValue(tagRef).
3. Let thisCall be this MemberExpression.
4. Let tailCall be IsInTailPosition(thisCall).
5. Return ? EvaluateCall(tagFunc, tagRef, TemplateLiteral, tailCall).

```
CallExpression : CallExpression TemplateLiteral
```

1. Let tagRef be the result of evaluating CallExpression.
2. Let tagFunc be ? GetValue(tagRef).
3. Let thisCall be this CallExpression.
4. Let tailCall be IsInTailPosition(thisCall).
5. Return ? EvaluateCall(tagFunc, tagRef, TemplateLiteral, tailCall).

### 12.3.8 元属性 <div id="sec-meta-properties"></div>

#### 12.3.8.1 运行时语义：Evaluation <div id="sec-meta-properties-runtime-semantics-evaluation"></div>

```
NewTarget : new . target
```

1. Return GetNewTarget().

## 12.4 更新表达式 <div id="sec-update-expressions"></div>
### 12.4.1 静态语义：Early Errors <div id="sec-update-expressions-static-semantics-early-errors"></div>
### 12.4.2 静态语义：IsFunctionDefinition <div id="sec-update-expressions-static-semantics-isfunctiondefinition"></div>
### 12.4.3 静态语义：AssignmentTargetType <div id="sec-update-expressions-static-semantics-assignmenttargettype"></div>
### 12.4.4 后缀自增运算符 <div id="sec-postfix-increment-operator"></div>
#### 12.4.4.1 运行时语义：Evaluation <div id="sec-postfix-increment-operator-runtime-semantics-evaluation"></div>
### 12.4.5 后缀自减运算符 <div id="sec-postfix-decrement-operator"></div>
#### 12.4.5.1 运行时语义：Evaluation <div id="sec-postfix-decrement-operator-runtime-semantics-evaluation"></div>
### 12.4.6 前缀自增运算符 <div id="sec-prefix-increment-operator"></div>
#### 12.4.6.1 运行时语义：Evaluation <div id="sec-prefix-increment-operator-runtime-semantics-evaluation"></div>
### 12.4.7 前缀自减运算符 <div id="sec-prefix-decrement-operator"></div>
#### 12.4.7.1 运行时语义：Evaluation <div id="sec-prefix-decrement-operator-runtime-semantics-evaluation"></div>
## 12.5 一元运算符 <div id="sec-unary-operators"></div>
### 12.5.1 静态语义：IsFunctionDefinition <div id="sec-unary-operators-static-semantics-isfunctiondefinition"></div>
### 12.5.2 静态语义：AssignmentTargetType <div id="sec-unary-operators-static-semantics-assignmenttargettype"></div>
### 12.5.3delete 运算符 <div id="sec-delete-operator"></div>
#### 12.5.3.1 静态语义：Early Errors <div id="sec-delete-operator-static-semantics-early-errors"></div>
#### 12.5.3.2 运行时语义：Evaluation <div id="sec-delete-operator-runtime-semantics-evaluation"></div>
### 12.5.4void 运算符 <div id="sec-void-operator"></div>
#### 12.5.4.1 运行时语义：Evaluation <div id="sec-void-operator-runtime-semantics-evaluation"></div>
### 12.5.5typeof 运算符 <div id="sec-typeof-operator"></div>
#### 12.5.5.1 运行时语义：Evaluation <div id="sec-typeof-operator-runtime-semantics-evaluation"></div>
### 12.5.6 一元+ 运算符 <div id="sec-unary-plus-operator"></div>
#### 12.5.6.1 运行时语义：Evaluation <div id="sec-unary-plus-operator-runtime-semantics-evaluation"></div>
### 12.5.7 一元- 运算符 <div id="sec-unary-minus-operator"></div>
#### 12.5.7.1 运行时语义：Evaluation <div id="sec-unary-minus-operator-runtime-semantics-evaluation"></div>
### 12.5.8 按位非运算符 ( ~ ) <div id="sec-bitwise-not-operator"></div>
#### 12.5.8.1 运行时语义：Evaluation <div id="sec-bitwise-not-operator-runtime-semantics-evaluation"></div>
### 12.5.9 逻辑非运算符( ! ) <div id="sec-logical-not-operator"></div>
#### 12.5.9.1 运行时语义：Evaluation <div id="sec-logical-not-operator-runtime-semantics-evaluation"></div>
## 12.6 指数运算符 <div id="sec-exp-operator"></div>
### 12.6.1 静态语义：IsFunctionDefinition <div id="sec-exp-operator-static-semantics-isfunctiondefinition"></div>
### 12.6.2 静态语义：AssignmentTargetType <div id="sec-exp-operator-static-semantics-assignmenttargettype"></div>
### 12.6.3 运行时语义：Evaluation <div id="sec-exp-operator-runtime-semantics-evaluation"></div>
### 12.6.4 使用** 运算符 <div id="sec-applying-the-exp-operator"></div>
## 12.7 乘数运算符 <div id="sec-multiplicative-operators"></div>
### 12.7.1 静态语义：IsFunctionDefinition <div id="sec-multiplicative-operators-static-semantics-isfunctiondefinition"></div>
### 12.7.2 静态语义：AssignmentTargetType <div id="sec-multiplicative-operators-static-semantics-assignmenttargettype"></div>
### 12.7.3 运行时语义：Evaluation <div id="sec-multiplicative-operators-runtime-semantics-evaluation"></div>
#### 12.7.3.1 使用* 运算符 <div id="sec-applying-the-mul-operator"></div>
#### 12.7.3.2 使用/ 运算符 <div id="sec-applying-the-div-operator"></div>
#### 12.7.3.3 使用% 运算符 <div id="sec-applying-the-mod-operator"></div>
## 12.8 加法运算符 <div id="sec-additive-operators"></div>
### 12.8.1 静态语义：IsFunctionDefinition <div id="sec-additive-operators-static-semantics-isfunctiondefinition"></div>
### 12.8.2 静态语义：AssignmentTargetType <div id="sec-additive-operators-static-semantics-assignmenttargettype"></div>
### 12.8.3 加法运算符( + ) <div id="sec-addition-operator-plus"></div>
#### 12.8.3.1 运行时语义：Evaluation <div id="sec-addition-operator-plus-runtime-semantics-evaluation"></div>
### 12.8.4 减法运算符( - ) <div id="sec-subtraction-operator-minus"></div>
#### 12.8.4.1 运行时语义：Evaluation <div id="sec-subtraction-operator-minus-runtime-semantics-evaluation"></div>
### 12.8.5 加法作用于数字 <div id="sec-applying-the-additive-operators-to-numbers"></div>
## 12.9 按位移运算符 <div id="sec-bitwise-shift-operators"></div>
### 12.9.1 静态语义：IsFunctionDefinition <div id="sec-bitwise-shift-operators-static-semantics-isfunctiondefinition"></div>
### 12.9.2 静态语义：AssignmentTargetType <div id="sec-bitwise-shift-operators-static-semantics-assignmenttargettype"></div>
### 12.9.3 左移位运算符( << ) <div id="sec-left-shift-operator"></div>
#### 12.9.3.1 运行时语义：Evaluation <div id="sec-left-shift-operator-runtime-semantics-evaluation"></div>
### 12.9.4 带号右移位运算符( >> ) <div id="sec-signed-right-shift-operator"></div>
#### 12.9.4.1 运行时语义：Evaluation <div id="sec-signed-right-shift-operator-runtime-semantics-evaluation"></div>
### 12.9.5 不带号右移位运算符( >>> ) <div id="sec-unsigned-right-shift-operator"></div>
#### 12.9.5.1 运行时语义：Evaluation <div id="sec-unsigned-right-shift-operator-runtime-semantics-evaluation"></div>
## 12.10 关系运算符 <div id="sec-relational-operators"></div>
### 12.10.1 静态语义：IsFunctionDefinition <div id="sec-relational-operators-static-semantics-isfunctiondefinition"></div>
### 12.10.2 静态语义：AssignmentTargetType <div id="sec-relational-operators-static-semantics-assignmenttargettype"></div>
### 12.10.3 运行时语义：Evaluation <div id="sec-relational-operators-runtime-semantics-evaluation"></div>
### 12.10.4 运行时语义：InstanceofOperator ( V, target ) <div id="sec-instanceofoperator"></div>
## 12.11 相等运算符 <div id="sec-equality-operators"></div>
### 12.11.1 静态语义：IsFunctionDefinition <div id="sec-equality-operators-static-semantics-isfunctiondefinition"></div>
### 12.11.2 静态语义：AssignmentTargetType <div id="sec-equality-operators-static-semantics-assignmenttargettype"></div>
### 12.11.3 运行时语义：Evaluation <div id="sec-equality-operators-runtime-semantics-evaluation"></div>
## 12.12 二进制按位运算符 <div id="sec-binary-bitwise-operators"></div>
### 12.12.1 静态语义：IsFunctionDefinition <div id="sec-binary-bitwise-operators-static-semantics-isfunctiondefinition"></div>
### 12.12.2 静态语义：AssignmentTargetType <div id="sec-binary-bitwise-operators-static-semantics-assignmenttargettype"></div>
### 12.12.3 运行时语义：Evaluation <div id="sec-binary-bitwise-operators-runtime-semantics-evaluation"></div>
## 12.13 二元逻辑运算符 <div id="sec-binary-logical-operators"></div>
### 12.13.1 静态语义：IsFunctionDefinition <div id="sec-binary-logical-operators-static-semantics-isfunctiondefinition"></div>
### 12.13.2 静态语义：AssignmentTargetType <div id="sec-binary-logical-operators-static-semantics-assignmenttargettype"></div>
### 12.13.3 运行时语义：Evaluation <div id="sec-binary-logical-operators-runtime-semantics-evaluation"></div>
## 12.14 条件运算符 <div id="sec-conditional-operator"></div>
### 12.14.1 静态语义：IsFunctionDefinition <div id="sec-conditional-operator-static-semantics-isfunctiondefinition"></div>
### 12.14.2 静态语义：AssignmentTargetType <div id="sec-conditional-operator-static-semantics-assignmenttargettype"></div>
### 12.14.3 运行时语义：Evaluation <div id="sec-conditional-operator-runtime-semantics-evaluation"></div>
## 12.15 赋值运算符 <div id="sec-assignment-operators"></div>
### 12.15.1 静态语义：Early Errors <div id="sec-assignment-operators-static-semantics-early-errors"></div>
### 12.15.2 静态语义：IsFunctionDefinition <div id="sec-assignment-operators-static-semantics-isfunctiondefinition"></div>
### 12.15.3 静态语义：AssignmentTargetType <div id="sec-assignment-operators-static-semantics-assignmenttargettype"></div>
### 12.15.4 运行时语义：Evaluation <div id="sec-assignment-operators-runtime-semantics-evaluation"></div>
### 12.15.5 解构赋值 <div id="sec-destructuring-assignment"></div>
#### 12.15.5.1 静态语义：Early Errors <div id="sec-destructuring-assignment-static-semantics-early-errors"></div>
#### 12.15.5.2 运行时语义：DestructuringAssignmentEvaluation <div id="sec-runtime-semantics-destructuringassignmentevaluation"></div>
#### 12.15.5.3 运行时语义：PropertyDestructuringAssignmentEvaluation <div id="sec-runtime-semantics-propertydestructuringassignmentevaluation"></div>
#### 12.15.5.4 运行时语义：RestDestructuringAssignmentEvaluation <div id="sec-runtime-semantics-restdestructuringassignmentevaluation"></div>
#### 12.15.5.5 运行时语义：IteratorDestructuringAssignmentEvaluation <div id="sec-runtime-semantics-iteratordestructuringassignmentevaluation"></div>
#### 12.15.5.6 运行时语义：KeyedDestructuringAssignmentEvaluation <div id="sec-runtime-semantics-keyeddestructuringassignmentevaluation"></div>
## 12.16 逗号运算符 <div id="sec-comma-operator"></div>
### 12.16.1 静态语义：IsFunctionDefinition <div id="sec-comma-operator-static-semantics-isfunctiondefinition"></div>
### 12.16.2 静态语义：AssignmentTargetType <div id="sec-comma-operator-static-semantics-assignmenttargettype"></div>
### 12.16.3 运行时语义：Evaluation <div id="sec-comma-operator-runtime-semantics-evaluation"></div>

```
description ECMAScript语言：表达式
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,expressions,ECMAScript语言：表达式
```
