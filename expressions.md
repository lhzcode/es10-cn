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

### 12.2.1 语义 <div id="sec-primary-expression-semantics"></div>
#### 12.2.1.1 静态语义：CoveredParenthesizedExpression <div id="sec-static-semantics-coveredparenthesizedexpression"></div>
#### 12.2.1.2 静态语义：HasName <div id="sec-semantics-static-semantics-hasname"></div>
#### 12.2.1.3 静态语义：IsFunctionDefinition <div id="sec-semantics-static-semantics-isfunctiondefinition"></div>
#### 12.2.1.4 静态语义：IsIdentifierRef <div id="sec-semantics-static-semantics-isidentifierref"></div>
#### 12.2.1.5 静态语义：AssignmentTargetType <div id="sec-semantics-static-semantics-assignmenttargettype"></div>
### 12.2.2 this 关键字 <div id="sec-this-keyword"></div>
### 12.2.2.1 运行时语义：Evaluation <div id="sec-this-keyword-runtime-semantics-evaluation"></div>
### 12.2.3 标识符引用 <div id="sec-identifier-reference"></div>
### 12.2.4 字面量 <div id="sec-primary-expression-literals"></div>
### 12.2.4.1 运行时语义：Evaluation <div id="sec-literals-runtime-semantics-evaluation"></div>
### 12.2.5 数组初始化 <div id="sec-array-initializer"></div>
#### 12.2.5.1 静态语义：ElisionWidth <div id="sec-static-semantics-elisionwidth"></div>
#### 12.2.5.2 运行时语义：ArrayAccumulation <div id="sec-runtime-semantics-arrayaccumulation"></div>
#### 12.2.5.3 运行时语义：Evaluation <div id="sec-array-initializer-runtime-semantics-evaluation"></div>
### 12.2.6 对象初始化 <div id="sec-object-initializer"></div>
#### 12.2.6.1 静态语义：Early Errors <div id="sec-object-initializer-static-semantics-early-errors"></div>
#### 12.2.6.2 静态语义：ComputedPropertyContains <div id="sec-object-initializer-static-semantics-computedpropertycontains"></div>
#### 12.2.6.3 静态语义：Contains <div id="sec-object-initializer-static-semantics-contains"></div>
#### 12.2.6.4 静态语义：IsComputedPropertyKey <div id="sec-static-semantics-iscomputedpropertykey"></div>
#### 12.2.6.5 静态语义：PropName <div id="sec-object-initializer-static-semantics-propname"></div>
#### 12.2.6.6 静态语义：PropertyNameList <div id="sec-static-semantics-propertynamelist"></div>
#### 12.2.6.7 运行时语义：Evaluation <div id="sec-object-initializer-runtime-semantics-evaluation"></div>
#### 12.2.6.8 运行时语义：PropertyDefinitionEvaluation <div id="sec-object-initializer-runtime-semantics-propertydefinitionevaluation"></div>
### 12.2.7 函数定义表达式 <div id="sec-function-defining-expressions"></div>
### 12.2.8 正则表达式字面量 <div id="sec-primary-expression-regular-expression-literals"></div>
#### 12.2.8.1 静态语义：Early Errors <div id="sec-primary-expression-regular-expression-literals-static-semantics-early-errors"></div>
#### 12.2.8.2 运行时语义：Evaluation <div id="sec-regular-expression-literals-runtime-semantics-evaluation"></div>
### 12.2.9 模板字面量 <div id="sec-template-literals"></div>
#### 12.2.9.1 静态语义：Early Errors <div id="sec-static-semantics-template-early-errors"></div>
#### 12.2.9.2 静态语义：TemplateStrings <div id="sec-static-semantics-templatestrings"></div>
#### 12.2.9.3 运行时语义：ArgumentListEvaluation <div id="sec-template-literals-runtime-semantics-argumentlistevaluation"></div>
#### 12.2.9.4 运行时语义：GetTemplateObject ( templateLiteral ) <div id="sec-gettemplateobject"></div>
#### 12.2.9.5 运行时语义：SubstitutionEvaluation <div id="sec-runtime-semantics-substitutionevaluation"></div>
#### 12.2.9.6 运行时语义：Evaluation <div id="sec-template-literals-runtime-semantics-evaluation"></div>
### 12.2.10 群组运算符 <div id="sec-grouping-operator"></div>
#### 12.2.10.1 静态语义：Early Errors <div id="sec-grouping-operator-static-semantics-early-errors"></div>
#### 12.2.10.2 静态语义：IsFunctionDefinition <div id="sec-grouping-operator-static-semantics-isfunctiondefinition"></div>
#### 12.2.10.3 静态语义：AssignmentTargetType <div id="sec-grouping-operator-static-semantics-assignmenttargettype"></div>
#### 12.2.10.4 运行时语义：NamedEvaluation <div id="sec-grouping-operator-runtime-semantics-namedevaluation"></div>
#### 12.2.10.5 运行时语义：Evaluation <div id="sec-grouping-operator-runtime-semantics-evaluation"></div>
## 12.3 左值表达式 <div id="sec-left-hand-side-expressions"></div>
### 12.3.1 静态语义 <div id="sec-static-semantics"></div>
#### 12.3.1.1 静态语义：CoveredCallExpression <div id="sec-left-hand-side-expressions-static-semantics-coveredcallexpression"></div>
#### 12.3.1.2 静态语义：Contains <div id="sec-static-semantics-static-semantics-contains"></div>
#### 12.3.1.3 静态语义：IsFunctionDefinition <div id="sec-static-semantics-static-semantics-isfunctiondefinition"></div>
#### 12.3.1.4 静态语义：IsDestructuring <div id="sec-static-semantics-static-semantics-isdestructuring"></div>
#### 12.3.1.5 静态语义：IsIdentifierRef <div id="sec-static-semantics-static-semantics-isidentifierref"></div>
#### 12.3.1.6 静态语义：AssignmentTargetType <div id="sec-static-semantics-static-semantics-assignmenttargettype"></div>
### 12.3.2 属性访问器 <div id="sec-property-accessors"></div>
#### 12.3.2.1 运行时语义：Evaluation <div id="sec-property-accessors-runtime-semantics-evaluation"></div>
### 12.3.3 new 运算符 <div id="sec-new-operator"></div>
#### 12.3.3.1 运行时语义：Evaluation <div id="sec-new-operator-runtime-semantics-evaluation"></div>
##### 12.3.3.1.1 运行时语义：EvaluateNew ( constructExpr, arguments ) <div id="sec-evaluatenew"></div>
### 12.3.4 函数回调 <div id="sec-function-calls"></div>
#### 12.3.4.1 运行时语义：Evaluation <div id="sec-function-calls-runtime-semantics-evaluation"></div>
#### 12.3.4.2 运行时语义：EvaluateCall ( func, ref, arguments, tailPosition ) <div id="sec-evaluatecall"></div>
### 12.3.5super 关键字 <div id="sec-super-keyword"></div>
#### 12.3.5.1 运行时语义：Evaluation <div id="sec-super-keyword-runtime-semantics-evaluation"></div>
#### 12.3.5.2 运行时语义：GetSuperConstructor ( ) <div id="sec-getsuperconstructor"></div>
#### 12.3.5.3 运行时语义：MakeSuperPropertyReference ( actualThis, propertyKey, strict ) <div id="sec-makesuperpropertyreference"></div>
### 12.3.6 参数列表 <div id="sec-argument-lists"></div>
#### 12.3.6.1 运行时语义：ArgumentListEvaluation <div id="sec-argument-lists-runtime-semantics-argumentlistevaluation"></div>
### 12.3.7 标记模板 <div id="sec-tagged-templates"></div>
#### 12.3.7.1 运行时语义：Evaluation <div id="sec-tagged-templates-runtime-semantics-evaluation"></div>
### 12.3.8 元属性 <div id="sec-meta-properties"></div>
#### 12.3.8.1 运行时语义：Evaluation <div id="sec-meta-properties-runtime-semantics-evaluation"></div>
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
