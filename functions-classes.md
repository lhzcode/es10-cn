# 14 ECMAScript语言：函数和类

> 注：各种ECMAScript语言元素导致创建ECMAScript函数对象（9.2）。对此类函数的运算始于其[[Call]]内部方法（9.2.1）的执行。
>

## 14.1 函数定义 <div id="sec-function-definitions"></div>

**语法**

```
FunctionDeclaration[Yield, Await, Default] :
    function BindingIdentifier[?Yield, ?Await] ( FormalParameters[~Yield, ~Await] ) {
    FunctionBody[~Yield, ~Await] }
    [+Default] function ( FormalParameters[~Yield, ~Await] ) { FunctionBody[~Yield, ~Await] }
FunctionExpression :
    function BindingIdentifier[~Yield, ~Await] opt ( FormalParameters[~Yield, ~Await] ) {
    FunctionBody[~Yield, ~Await] }
UniqueFormalParameters[Yield, Await] :
	FormalParameters[?Yield, ?Await]
FormalParameters[Yield, Await] :
    [empty]
    FunctionRestParameter[?Yield, ?Await]
    FormalParameterList[?Yield, ?Await]
    FormalParameterList[?Yield, ?Await] ,
    FormalParameterList[?Yield, ?Await] , FunctionRestParameter[?Yield, ?Await]
FormalParameterList[Yield, Await] :
    FormalParameter[?Yield, ?Await]
    FormalParameterList[?Yield, ?Await] , FormalParameter[?Yield, ?Await]
FunctionRestParameter[Yield, Await] :
	BindingRestElement[?Yield, ?Await]
FormalParameter[Yield, Await] :
	BindingElement[?Yield, ?Await]
FunctionBody[Yield, Await] :
	FunctionStatementList[?Yield, ?Await]
FunctionStatementList[Yield, Await] :
	StatementList[?Yield, ?Await, +返回] opt
```



### 14.1.1 指令序言和严格使用指令 <div id="sec-directive-prologues-and-the-use-strict-directive"></div>

指令序言是最长的ExpressionStatements序列，出现在FunctionBody，ScriptBody或ModuleBody的初始StatementListItems或ModuleItems上，并且序列中的每个ExpressionStatement完全由StringLiteral标记后跟一个分号组成。分号可以显式显示，也可以通过自动分号插入来插入。指令序言可能是空序列。

使用严格指令是指令序言中的ExpressionStatement，其StringLiteral是 "use strict" 或 'use strict'的确切代码单元序列。使用严格指令可能不包含EscapeSequence或LineContinuation。

指令序言中可能包含多个严格使用指令。但是，如果发生这种情况，则实现可能会发出警告。

> 注：指令序言的ExpressionStatement语句通常在包含产生式的求值过程中进行计算。实现可以为ExpressionStatement定义实现特定的含义，这些含义不是“使用严格”指令，而是在指令序言中出现。如果存在适当的通知机制，则如果实现在指令序言中遇到不是Use Strict指令且没有实现定义的含义的ExpressionStatement，则该实现应发出警告。
>

### 14.1.2 静态语义：Early Errors <div id="sec-function-definitions-static-semantics-early-errors"></div>

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

- 如果与此产生式匹配的源代码是严格模式代码，则应用 UniqueFormalParameters: FormalParameters 的早期错误规则。
- 如果与此产生式匹配的源代码是严格模式代码，那么如果BindingIdentifier是存在的，并且BindingIdentifier的StringValue是"eval"或"arguments"，那么这就是一个语法错误。
- 如果FunctionBody的ContainsUseStrict为真，FormalParameters的IsSimpleParameterList为假，则为语法错误。
- 如果FormalParameters的BoundNames的任何元素也出现在FunctionBody的LexicallyDeclaredNames中，这是一个语法错误。
- 如果包含SuperProperty的FormalParameters为真，则为语法错误。
- 如果包含SuperProperty的FunctionBody为真，则为语法错误。
- 如果包含SuperCall的FormalParameters为真，则为语法错误。
- 如果包含SuperCall的FunctionBody为真，则为语法错误。

> 注 1：FunctionBody的LexicallyDeclaredNames不包括使用var或函数声明绑定的标识符
>

```
UniqueFormalParameters : FormalParameters
```

如果FormalParameters的BoundNames包含任何重复的元素，则是语法错误。

```
FormalParameters : FormalParameterList
```

如果FormalParameterList的IsSimpleParameterList为false，并且FormalParameterList的BoundNames包含任何重复元素，则是语法错误。

> 注 2：仅对于具有简单参数列表且未在严格模式代码中定义的函数，允许FormalParameterList中多次出现同一BindingIdentifier。
>

```
FunctionBody : FunctionStatementList
```

- 如果FunctionStatementList的LexicallyDeclaredNames包含任何重复的条目，则是语法错误。
- 如果FunctionStatementList的LexicallyDeclaredNames的任何元素也出现在FunctionStatementList的VarDeclaredNames中，则是语法错误。
- 如果FunctionStatementList的ContainsDuplicateLabels和参数«»为true，则为语法错误。
- 如果FunctionStatementList的ContainsUndefinedBreakTarget带有参数«»，则为语法错误。
- 如果FunctionStatementList的ContainsUndefinedContinueTarget带有参数«»，并且«»为true，则为语法错误。

### 14.1.3 静态语义：BoundNames <div id="sec-function-definitions-static-semantics-boundnames"></div>

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. 返回 the BindingIdentifier 的 BoundNames

```
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
```

1. 返回 « "\*default\*" ».

> 注
>
> "\*default\*"在本规范中用作使用导出声明定义的可提升匿名函数的合成名称。

```
FormalParameters : [empty]
```

1. 返回一个新的空列表

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. 令 names 为 FormalParameterList 的 BoundNames
2. 在 names 后追加 FunctionRestParameter 的 BoundNames。
3. 返回 names.

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. 令 names 为 FormalParameterList 的 BoundNames
2. 在 names 后追加 FormalParameter 的 BoundNames
3. 返回 names.

### 14.1.4 静态语义：包含<div id="sec-function-definitions-static-semantics-contains"></div>

带有参数 symbol.

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. 返回 false.

> 注：依赖于子结构的静态语义规则通常不会考虑函数定义。
>

### 14.1.5 静态语义：ContainsExpression <div id="sec-function-definitions-static-semantics-containsexpression"></div>

```
FormalParameters : [empty]
```

1. 返回 false.

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. 若 FormalParameterList 的 ContainsExpression 是 true, 返回 true.
2. 返回 FunctionRestParameter 的 ContainsExpression

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. 若 FormalParameterList 的 ContainsExpression 是 true, 返回 true.
2. 返回 FormalParameter 的 ContainsExpression

### 14.1.6 静态语义：ContainsUseStrict <div id="sec-function-definitions-static-semantics-containsusestrict"></div>

```
FunctionBody : FunctionStatementList
```

1. 若 FunctionStatementList的指令序言包含一个使用严格的指令, 返回 true; 否则, 返回 false.

### 14.1.7 静态语义：ExpectedArgumentCount <div id="sec-function-definitions-static-semantics-expectedargumentcount"></div>

```
FormalParameters : [empty]
```

1. 返回 0.

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. 返回 FormalParameterList 的 ExpectedArgumentCount

> 注：FormalParameterList的ExpetedArgumentCount是REST参数左边的FormalParameters数，或者是带有初始化器的第一个FormalParameter数。在带有初始化器的第一个参数之后，允许使用没有初始化器的FormalParameters，但是这些参数被认为是可选的，undefined为它们的默认值。
>

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. 令 count 为 FormalParameterList 的 ExpectedArgumentCount
2. 若FormAlParameterList的HasInitializer设定为true，或者FormAlParameter的HasInitializer设定为true, 返回 count.
3. 返回 count + 1.

### 14.1.8 静态语义：HasInitializer <div id="sec-function-definitions-static-semantics-hasinitializer"></div>

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. 若 FormalParameterList 的 HasInitializer 是 true, 返回 true.
2. 返回 FormalParameter 的 HasInitializer

### 14.1.9 静态语义：HasName <div id="sec-function-definitions-static-semantics-hasname"></div>

```
FunctionExpression : function ( FormalParameters ) { FunctionBody }
```

1. 返回 false.

```
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. 返回 true.

### 14.1.10 静态语义：IsAnonymousFunctionDefinition ( expr ) <div id="sec-isanonymousfunctiondefinition"></div>

抽象操作IsAnonyousFunctionDefinition确定其参数是否是不绑定名称的函数定义。参数expr是AssignmentExpression或Initializer的结果。采取了以下步骤：

1. 若 expr 的 IsFunctionDefinition 是 false, 返回 false.
2. 令 hasName 为 expr 的 HasName 的结果
3. 若 hasName 是 true, 返回 false.
4. 返回 true.

### 14.1.11 静态语义：IsConstantDeclaration <div id="sec-function-definitions-static-semantics-isconstantdeclaration"></div>

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
```

1. 返回 false.

### 14.1.12 静态语义：IsFunctionDefinition <div id="sec-function-definitions-static-semantics-isfunctiondefinition"></div>

```
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. 返回 true.

### 14.1.13 静态语义：IsSimpleParameterList <div id="sec-function-definitions-static-semantics-issimpleparameterlist"></div>

```
FormalParameters : [empty]
```

1. 返回 true.

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. 返回 false

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. 若 FormalParameterList 的 IsSimpleParameterList 是 false, 返回 false.
2. 返回 FormalParameter 的 IsSimpleParameterList

```
FormalParameter : BindingElement
```

1. 返回 BindingElement 的 IsSimpleParameterList

### 14.1.14 静态语义：LexicallyDeclaredNames <div id="sec-function-definitions-static-semantics-lexicallydeclarednames"></div>

```
FunctionStatementList : [empty]
```

1. 返回一个新的空列表

```
FunctionStatementList : StatementList
```

1. 返回 TopLevelLexicallyDeclaredNames of StatementList

### 14.1.15 静态语义：LexicallyScopedDeclarations <div id="sec-function-definitions-static-semantics-lexicallyscopeddeclarations"></div>

```
FunctionStatementList : [empty]
```

1. 返回一个新的空列表.

```
FunctionStatementList : StatementList
```

1. 返回 the StatementList 的 TopLevelLexicallyScopedDeclarations

### 14.1.16 静态语义：VarDeclaredNames <div id="sec-function-definitions-static-semantics-vardeclarednames"></div>

```
FunctionStatementList : [empty]
```

1. 返回一个新的空列表.

```
FunctionStatementList : StatementList
```

1. 返回 StatementList 的 TopLevelVarDeclaredNames

### 14.1.17 静态语义：VarScopedDeclarations <div id="sec-function-definitions-static-semantics-varscopeddeclarations"></div>

```
FunctionStatementList : [empty]
```

1. 返回一个新的空列表.

```
FunctionStatementList : StatementList
```

1. 返回 the StatementList 的 TopLevelVarScopedDeclarations

### 14.1.18 运行时语义：EvaluateBody <div id="sec-function-definitions-runtime-semantics-evaluatebody"></div>

带有参数 functionObject 和 argumentsList列表.

```
FunctionBody : FunctionStatementList
```

1. 执行 ? FunctionDeclarationInstantiation(functionObject, argumentsList).
2. 返回运算 FunctionStatementList 的结果

### 14.1.19 运行时语义：IteratorBindingInitialization <div id="sec-function-definitions-runtime-semantics-iteratorbindinginitialization"></div>

带有参数 iteratorRecord 和 environment

> 注 1：当为环境传递undefined时，表明应该使用PutValue操作来分配初始化值。非严格函数的形式参数列表就是这种情况。在那种情况下，形式参数绑定是预先初始化的，以便处理多个具有相同名称的参数的可能性。
>

```
FormalParameters : [empty]
```

1. 返回 NormalCompletion(empty).

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. 执行 ? 使用iteratorRecord和环境作为参数的FormalParameterList的IteratorBindingInitialization
2. 返回使用iteratorRecord和环境作为参数对FunctionRestParameter执行IteratorBindingInitialization的结果。

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. 执行 ? 使用iteratorRecord和环境作为参数的FormalParameterList的IteratorBindingInitialization。
2. 返回使用iteratorRecord和环境作为参数对FormalParameter执行IteratorBindingInitialization的结果。

```
FormalParameter : BindingElement
```

1. 若 BindingElement 的 ContainsExpression 是 false, 返回使用iteratorRecord和环境作为参数对BindingElement执行IteratorBindingInitialization的结果。
2. 令 currentContext 为运行时上下文.
3. 令 originalEnv 为 currentContext 的 VariableEnvironment
4. 断言: currentContext的VariableEnvironment和LexicalEnvironment相同。
5. 断言: environment和originalEnv相同。
6. 令 paramVarEnv 为 NewDeclarativeEnvironment(originalEnv).
7. 设置currentContext的VariableEnvironment为paramVarEnv
8. 设置currentContext的LexicalEnvironment为paramVarEnv
9. 令 result 为使用iteratorRecord和environment作为参数对BindingElement执行IteratorBindingInitialization的结果。
10. 设置currentContext的VariableEnvironment为originalEnv
11. 设置currentContext的LexicalEnvironment为originalEnv
12. 返回 result.

> 注 2 仅当BindingElement包含直接运算时，才使用在步骤6中创建的新环境记录。
>

```
FunctionRestParameter : BindingRestElement
```

1. 若 BindingRestElement 的 ContainsExpression 是 false, 返回使用iteratorRecord和environment作为参数对BindingRestElement执行IteratorBindingInitialization的结果。
2. 令 currentContext 为 运行时上下文.
3. 令 originalEnv 为 currentContext 的 VariableEnvironment
4. 断言: currentContext的VariableEnvironment和LexicalEnvironment相同。
5. 断言: environment和originalEnv相同。
6. 令 paramVarEnv 为 NewDeclarativeEnvironment(originalEnv).
7. 设置currentContext的VariableEnvironment为paramVarEnv
8. 设置currentContext的LexicalEnvironment为paramVarEnv
9. 令 result 为使用iteratorRecord和environment作为参数对BindingRestElement执行IteratorBindingInitialization的结果。
10. 设置currentContext的VariableEnvironment为originalEnv
11. 设置currentContext的LexicalEnvironment为originalEnv
12. 返回 result.

> 注 3：仅当BindingRestElement包含直接运算时，才使用在步骤6中创建的新环境记录。
>

### 14.1.20 运行时语义：InstantiateFunctionObject <div id="sec-function-definitions-runtime-semantics-instantiatefunctionobject"></div>

带有参数 scope.

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. 若 FunctionDeclaration 的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
2. 令 name 为 BindingIdentifier 的 StringValue
3. 令 F 为 FunctionCreate(Normal, FormalParameters, FunctionBody, scope, strict).
4. 执行 MakeConstructor(F).
5. 执行 SetFunctionName(F, name).
6. 设置 F.[[SourceText]] 为由FunctionDeclaration匹配的源文本。
7. 返回 F.

```
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
```

1. 令 F 为 FunctionCreate(Normal, FormalParameters, FunctionBody, scope, true).
2. 执行 MakeConstructor(F).
3. 执行 SetFunctionName(F, "default").
4. 设置 F.[[SourceText]]由FunctionDeclaration匹配的源文本。
5. 返回 F

> 注：匿名FunctionDeclaration只能作为 export default 声明的一部分出现，因此其函数代码始终是严格模式代码。
>

### 14.1.21 运行时语义：NamedEvaluation <div id="sec-function-definitions-runtime-semantics-namedevaluation"></div>

带有参数 name.

```
FunctionExpression : function ( FormalParameters ) { FunctionBody }
```

1. 令 closure 为运算 this 的结果FunctionExpression.
2. 执行 SetFunctionName(closure, name).
3. 返回 closure.

### 14.1.22 运行时语义：Evaluation <div id="sec-function-definitions-runtime-semantics-evaluation"></div>

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. 返回 NormalCompletion(empty).

> 注 1：B.3.3中提供了另一种语义

```
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
```

1. 返回 NormalCompletion(empty).

```
FunctionExpression : function ( FormalParameters ) { FunctionBody }
```

1. 若 FunctionExpression 的函数代码是严格模式代码， 令 strict 为 true. 否则，令 strict 为 false.
2. 令 scope 为运行时上下文的 LexicalEnvironment。
3. 令 closure 为 FunctionCreate(Normal, FormalParameters, FunctionBody, scope, strict).
4. 执行 MakeConstructor(closure).
5. 设置 closure.[[SourceText]] 为由 FunctionExpression 匹配的源文本。
6. 返回 closure.

```
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. 若 FunctionExpression 的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
2. 令 scope 为运行时上下文的 LexicalEnvironment.
3. 令 funcEnv 为 NewDeclarativeEnvironment(scope).
4. 令 envRec 为 funcEnv' 的环境记录项.
5. 令 name 为 BindingIdentifier 的 StringValue
6. 执行 envRec.CreateImmutableBinding(name, false).
7. 令 closure 为 FunctionCreate(Normal, FormalParameters, FunctionBody, funcEnv, strict).
8. 执行 MakeConstructor(closure).
9. 执行 SetFunctionName(closure, name).
10. 设置 closure.[[SourceText]] 为由FunctionExpression匹配的源文本。
11. 执行 envRec.InitializeBinding(name, closure).
12. 返回 closure

> 注 2：可以从FunctionExpression的FunctionBody内部引用FunctionExpression中的BindingIdentifier，以允许该函数以递归方式调用其自身。但是，与FunctionDeclaration中的功能不同，FunctionExpression中的BindingIdentifier不能从其引用，也不影响包围FunctionExpression的作用域。

> 注 3：将为使用FunctionDeclaration或FunctionExpression定义的每个函数自动创建一个原型属性，以允许将该函数用作构造函数。

```
FunctionStatementList : [empty]
```

1. 返回 NormalCompletion(undefined).

## 14.2 箭头函数定义 <div id="sec-arrow-function-definitions"></div>

**语法**

```
ArrowFunction[In, Yield, Await] :
	ArrowParameters[?Yield, ?Await] [no LineTerminator here] => ConciseBody[?In]
ArrowParameters[Yield, Await] :
    BindingIdentifier[?Yield, ?Await]
    CoverParenthesizedExpressionAndArrowParameterList[?Yield, ?Await]
ConciseBody[In] :
    [lookahead ≠ {] AssignmentExpression[?In, ~Yield, ~Await]
    { FunctionBody[~Yield, ~Await] }
```

**补充语法**

当产生式

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

公认以下语法用于完善对

```
CoverParenthesizedExpressionAndArrowParameterList:
```

```
ArrowFormalParameters[Yield]:
(StrictFormalParameters[?Yield])
```

### 14.2.1 静态语义：Early Errors <div id="sec-arrow-function-definitions-static-semantics-early-errors"></div>

```
ArrowFunction : ArrowParameters => ConciseBody
```

- 若 ArrowParameters 包含 YieldExpression 是 true，则为语法错误
- 若 ArrowParameters 包含AwaitExpression 是 true，则为语法错误
- 若 ConciseBody 的 ContainsUseStrict 是 true 并且 ArrowParameters 的 IsSimpleParameterList 是 false，则为语法错误
- 如果ConciseBody的LexicallyDeclaredNames中也出现ArrowParameters的BoundNames的任何元素，则是语法错误。

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

- 如果CoverParenthesizedExpressionAndArrowParameterList没有覆盖ArrowFormalParameters，则是语法错误。
- ArrowFormalParameters及其派生生产式的所有早期错误规则也适用于CoverParenthesizedExpressionAndArrowParameterList的CoveredFormalsList。

### 14.2.2 静态语义：BoundNames <div id="sec-arrow-function-definitions-static-semantics-boundnames"></div>

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 formals 为 CoverParenthesizedExpressionAndArrowParameterList 的 CoveredFormalsList
2. 返回 the formals 的 BoundNames

### 14.2.3 静态语义：包含<div id="sec-arrow-function-definitions-static-semantics-contains"></div>

带有参数 symbol

```
ArrowFunction : ArrowParameters => ConciseBody
```

1. 若 symbol 不是 NewTarget, SuperProperty, SuperCall, super 或 this其中之一, 返回 false.
2. 若 ArrowParameters 包含 symbol 是 true, 返回 true.
3. 返回 ConciseBody 包含 symbol.

> 注：通常，包含不会在大多数函数形式中出现。但是，Contains用于检测ArrowFunction中的new.target，this和super用法。

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 formals 为 CoverParenthesizedExpressionAndArrowParameterList 的 CoveredFormalsList
2. 返回 formals 包含 symbol.

### 14.2.4 静态语义：ContainsExpression <div id="sec-arrow-function-definitions-static-semantics-containsexpression"></div>

```
ArrowParameters : BindingIdentifier
```

1. 返回 false.

### 14.2.5 静态语义：ContainsUseStrict <div id="sec-arrow-function-definitions-static-semantics-containsusestrict"></div>
```
ConciseBody : AssignmentExpression
```

1. 返回 false.

### 14.2.6 静态语义：ExpectedArgumentCount <div id="sec-arrow-function-definitions-static-semantics-expectedargumentcount"></div>

```
ArrowParameters : BindingIdentifier
```

1. 返回 1.

### 14.2.7 静态语义：HasName <div id="sec-arrow-function-definitions-static-semantics-hasname"></div>

```
ArrowFunction : ArrowParameters => ConciseBody
```

1. 返回 false.

### 14.2.8 静态语义：IsSimpleParameterList <div id="sec-arrow-function-definitions-static-semantics-issimpleparameterlist"></div>

```
ArrowParameters : BindingIdentifier
```

1. 返回 true.

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 formals 为 CoverParenthesizedExpressionAndArrowParameterList 的 CoveredFormalsList
2. 返回 formals 的 IsSimpleParameterList

### 14.2.9 静态语义：CoveredFormalsList <div id="sec-static-semantics-coveredformalslist"></div>

```
ArrowParameters : BindingIdentifier
```

1. 返回 this ArrowParameters.

   ```
   CoverParenthesizedExpressionAndArrowParameterList :
       ( Expression )
       ( )
       ( ... BindingIdentifier )
       ( ... BindingPattern )
       ( Expression , ... BindingIdentifier )
       ( Expression , ... BindingPattern )
   
   ```

   1. 返回CoverParenthesizedExpressionAndArrowParameterList涵盖的ArrowFormalParameters



### 14.2.10 静态语义：LexicallyDeclaredNames <div id="sec-arrow-function-definitions-static-semantics-lexicallydeclarednames"></div>

```
ConciseBody : AssignmentExpression
```

1. 返回一个新的空列表.

### 14.2.11 静态语义：LexicallyScopedDeclarations <div id="sec-arrow-function-definitions-static-semantics-lexicallyscopeddeclarations"></div>

```
ConciseBody : AssignmentExpression
```

1. 返回一个新的空列表.

### 14.2.12 静态语义：VarDeclaredNames <div id="sec-arrow-function-definitions-static-semantics-vardeclarednames"></div>

```
ConciseBody : AssignmentExpression
```

1. 返回一个新的空列表.

### 14.2.13 静态语义：VarScopedDeclarations <div id="sec-arrow-function-definitions-static-semantics-varscopeddeclarations"></div>

```
ConciseBody : AssignmentExpression
```

1. 返回一个新的空列表.

### 14.2.14 运行时语义：IteratorBindingInitialization <div id="sec-arrow-function-definitions-runtime-semantics-iteratorbindinginitialization"></div>

带有参数 iteratorRecord 和 environment.

> 注：当为环境传递undefined时，表明应该使用PutValue操作来分配初始化值。非严格函数的形式参数列表就是这种情况。在那种情况下，形式参数绑定是预先初始化的，以便处理多个具有相同名称的参数的可能性。
>

```
ArrowParameters : BindingIdentifier
```

1. 断言: iteratorRecord.[[Done]] 是 false.
2. 令 next 为 IteratorStep(iteratorRecord).
3. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
4. ReturnIfAbrupt(next).
5. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
6. 否则,
1. 令 v 为 IteratorValue(next).
2. 若 v 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
3. ReturnIfAbrupt(v).
7. 若 iteratorRecord.[[Done]] 是 true, 令 v 为 undefined.
8. 返回使用v和environment作为参数对BindingIdentifier执行BindingInitialization的结果。

### 14.2.15 运行时语义：EvaluateBody <div id="sec-arrow-function-definitions-runtime-semantics-evaluatebody"></div>

带有参数 functionObject 和 argumentsList列表.

```
ConciseBody : AssignmentExpression
```

1. 执行 ? FunctionDeclarationInstantiation(functionObject, argumentsList).
2. 令 exprRef 为运算 AssignmentExpression 的结果
3. 令 exprValue 为 ? GetValue(exprRef).
4. 返回 Completion { [[Type]]: return, [[Value]]: exprValue, [[Target]]: empty }.

### 14.2.16 运行时语义：NamedEvaluation <div id="sec-arrow-function-definitions-runtime-semantics-namedevaluation"></div>

带有参数 name.

```
ArrowFunction : ArrowParameters => ConciseBody
```

1. 令 closure 为运算此ArrowFunction的结果.
2. 执行 SetFunctionName(closure, name).
3. 返回 closure.

### 14.2.17 运行时语义：Evaluation <div id="sec-arrow-function-definitions-runtime-semantics-evaluation"></div>

```
ArrowFunction : ArrowParameters => ConciseBody
```

1. 若此ArrowFunction的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
2. 令 scope 为运行时上下文的LexicalEnvironment.
3. 令 parameters 为 ArrowParameters 的 CoveredFormalsList
4. 令 closure 为 FunctionCreate(Arrow, parameters, ConciseBody, scope, strict).
5. 设置 closure.[[SourceText]] 为ArrowFunction匹配的源文本.
6. 返回 closure.

> 注：ArrowFunction没有为arguments，super，this或new.target定义本地绑定。 ArrowFunction中对arguments，super，this或new.target的任何引用都必须解析为词法包围环境中的绑定。通常，这将是立即封闭函数的函数环境。即使ArrowFunction可能包含对super的引用，也无法通过执行MakeMethod将第4步中创建的函数对象制成方法。引用super的ArrowFunction始终包含在nonArrowFunction中，并且可以通过ArrowFunction的函数对象捕获的范围来访问实现super的必要状态。

## 14.3 方法定义 <div id="sec-method-definitions"></div>

**语法**

```
MethodDefinition[Yield, Await] :
    PropertyName[?Yield, ?Await] ( UniqueFormalParameters[~Yield, ~Await] ) {
    	FunctionBody[~Yield, ~Await] }
    GeneratorMethod[?Yield, ?Await]
    AsyncMethod[?Yield, ?Await]
    AsyncGeneratorMethod[?Yield, ?Await]
    get PropertyName[?Yield, ?Await] ( ) { FunctionBody[~Yield, ~Await] }
    set PropertyName[?Yield, ?Await] ( PropertySetParameterList ) {
    FunctionBody[~Yield, ~Await] }
PropertySetParameterList :
	FormalParameter[~Yield, ~Await]
```

### 14.3.1 静态语义：Early Errors <div id="sec-method-definitions-static-semantics-early-errors"></div>

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

- 如果FunctionBody的ContainsUseStrict为true并且UniqueFormalParameters的IsSimpleParameterList为false，则为语法错误
- 如果FunctionBody的LexicallyDeclaredNames中还出现UniqueFormalParameters的BoundNames的任何元素，则是语法错误。

```
MethodDefinition : set PropertyName ( PropertySetParameterList ) { FunctionBody }
```

- 若PropertySetParameterList的BoundNames包含任何重复的元素，则为语法错误
- 如果FunctionBody的ContainsUseStrict为true，PropertySetParameterList的IsSimpleParameterList为false，则为语法错误。
- 如果FunctionBody的LexicallyDeclaredNames中还出现了PropertySetParameterList的BoundNames的任何元素，则是语法错误。

### 14.3.2 静态语义：ComputedProperty包含<div id="sec-method-definitions-static-semantics-computedpropertycontains"></div>

带有参数 symbol.

```
MethodDefinition :
    PropertyName ( UniqueFormalParameters ) { FunctionBody }
    get PropertyName ( ) { FunctionBody }
    set PropertyName ( PropertySetParameterList ) { FunctionBody }
```

1. 返回具有symbol参数的PropertyName的ComputedPropertyContains结果。

### 14.3.3 静态语义：ExpectedArgumentCount <div id="sec-method-definitions-static-semantics-expectedargumentcount"></div>

```
PropertySetParameterList : FormalParameter
```

1. 若 FormalParameter 的 HasInitializer 是 true, 返回 0.
2. 返回 1.

### 14.3.4 静态语义：HasDirectSuper <div id="sec-method-definitions-static-semantics-hasdirectsuper"></div>

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

1. 若 UniqueFormalParameters 包含 SuperCall 是 true, 返回 true.
2. 返回 FunctionBody 包含SuperCall.

```
MethodDefinition : get PropertyName ( ) { FunctionBody }
```

1. 返回 FunctionBody 包含SuperCall

```
MethodDefinition : 设置 PropertyName ( PropertySetParameterList ) { FunctionBody }
```

1. 若 PropertySetParameterList 包含SuperCall 是 true, 返回 true.
2. 返回 FunctionBody 包含SuperCall.

### 14.3.5 静态语义：PropName <div id="sec-method-definitions-static-semantics-propname"></div>

```
MethodDefinition :
    PropertyName ( UniqueFormalParameters ) { FunctionBody }
    get PropertyName ( ) { FunctionBody }
    设置 PropertyName ( PropertySetParameterList ) { FunctionBody }
```

1. 返回 PropertyName 的 PropName

### 14.3.6 静态语义：SpecialMethod <div id="sec-static-semantics-specialmethod"></div>

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

1. 返回 false.

   ```
   MethodDefinition :
       GeneratorMethod
       AsyncMethod
       AsyncGeneratorMethod
       get PropertyName ( ) { FunctionBody }
       设置 PropertyName ( PropertySetParameterList ) { FunctionBody }
   ```

   1. 返回 true.

### 14.3.7 运行时语义：DefineMethod <div id="sec-runtime-semantics-definemethod"></div>

带有参数 object 和 optional parameter functionPrototype.

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

1. 令 propKey 为运算 PropertyName 的结果
2. ReturnIfAbrupt(propKey).
3. 若此MethodDefinition的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
4. 令 scope 为 运行时上下文的 LexicalEnvironment.
5. 若 functionPrototype 作为参数存在, 那么
     1. 令 kind 为 Normal.
     2. 令 prototype 为 functionPrototype.
6. 否则,
    1. 令 kind 为 Method.
  2. 令 prototype 为内部对象 %FunctionPrototype%.
7. 令 closure 为 FunctionCreate(kind, UniqueFormalParameters, FunctionBody, scope, strict, prototype).
8. 执行 MakeMethod(closure, object).
9. 设置 closure.[[SourceText]] 为MethodDefinition匹配的源文本.
10. 返回 the Record { [[Key]]: propKey, [[Closure]]: closure }.

### 14.3.8 运行时语义：PropertyDefinitionEvaluation <div id="sec-method-definitions-runtime-semantics-propertydefinitionevaluation"></div>

带有参数 object 和 enumerable.

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

1. 令 methodDef 为带参数对象的MethodDefinition的DefineMethod。
2. ReturnIfAbrupt(methodDef).
3. 执行 SetFunctionName(methodDef.[[Closure]], methodDef.[[Key]]).
4. 令 desc 为 the PropertyDescriptor { [[Value]]: methodDef.[[Closure]], [[Writable]]: true, [[Enumerable]]:
enumerable, [[Configurable]]: true }.
5. 返回 ? DefinePropertyOrThrow(object, methodDef.[[Key]], desc)

```
MethodDefinition : get PropertyName ( ) { FunctionBody }
```

1. 令 propKey 为运算 PropertyName 的结果
2. ReturnIfAbrupt(propKey).
3. 若此MethodDefinition的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
4. 令 scope 为 运行时上下文的 LexicalEnvironment.
5. 令 formalParameterList 为产生式的一个实例 FormalParameters : [empty] .
6. 令 closure 为 FunctionCreate(Method, formalParameterList, FunctionBody, scope, strict).
7. 执行 MakeMethod(closure, object).
8. 执行 SetFunctionName(closure, propKey, "get").
9. 设置 closure.[[SourceText]] 为MethodDefinition匹配的源文本.
10. 令 desc 为 the PropertyDescriptor { [[Get]]: closure, [[Enumerable]]: enumerable, [[Configurable]]: true }.
11. 返回 ? DefinePropertyOrThrow(object, propKey, desc).

```
MethodDefinition : set PropertyName ( PropertySetParameterList ) { FunctionBody }
```

1. 令 propKey 为运算 PropertyName 的结果
2. ReturnIfAbrupt(propKey).
3. 若此MethodDefinition的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
4. 令 scope 为 运行时上下文的 LexicalEnvironment.
5. 令 closure 为 FunctionCreate(Method, PropertySetParameterList, FunctionBody, scope, strict).
6. 执行 MakeMethod(closure, object).
7. 执行 SetFunctionName(closure, propKey, "set").
8. 设置 closure.[[SourceText]] 为MethodDefinition匹配的源文本.
9. 令 desc 为 the PropertyDescriptor { [[Set]]: closure, [[Enumerable]]: enumerable, [[Configurable]]: true }.
10. 返回 ? DefinePropertyOrThrow(object, propKey, desc).

## 14.4 Generator 函数定义 <div id="sec-generator-function-definitions"></div>

**语法**

```
GeneratorMethod[Yield, Await] :
    * PropertyName[?Yield, ?Await] ( UniqueFormalParameters[+Yield, ~Await] ) { GeneratorBody }
GeneratorDeclaration[Yield, Await, Default] :
    function * BindingIdentifier[?Yield, ?Await] ( FormalParameters[+Yield, ~Await] ) { GeneratorBody }
    [+Default] function * ( FormalParameters[+Yield, ~Await] ) { GeneratorBody } GeneratorExpression :
    function * BindingIdentifier[+Yield, ~Await] opt ( FormalParameters[+Yield, ~Await] ) { GeneratorBody }
GeneratorBody :
	FunctionBody[+Yield, ~Await]
YieldExpression[In, Await] :
    yield
    yield [no LineTerminator here] AssignmentExpression[?In, +Yield, ?Await]
    yield [no LineTerminator here] * AssignmentExpression[?In, +Yield, ?Await]
```

> 注 1 紧随产生之后的句法上下文需要使用 InputElementRegExpOrTemplateTail 词法目标。

> 注 2 YieldExpression不能在生成器函数的FormalParameters中使用，因为在生成的生成器对象处于可恢复状态之前，将计算FormalParameters组成部分的任何表达式。

> 注 3 与生成器对象有关的抽象操作在25.4.3中定义。

### 14.4.1 静态语义：Early Errors <div id="sec-generator-function-definitions-static-semantics-early-errors"></div>

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

- 若 GeneratorMethod 的 HasDirectSuper 是 true，则为语法错误
- 若 UniqueFormalParameters 包含YieldExpression 是 true，则为语法错误
- 若 GeneratorBody 的 ContainsUseStrict 是 true and IsSimpleParameterList of，则为语法错误
  UniqueFormalParameters is false.
- 若 any element of the BoundNames of UniqueFormalParameters also occurs in the，则为语法错误
  GeneratorBody 的 LexicallyDeclaredNames

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

- 若 the source code matching this production is strict mode code, the Early Error rules for UniqueFormalParameters : FormalParameters are applied.
- 若 the source code matching this production is strict mode code, 若 BindingIdentifier is present and the BindingIdentifier 的 StringValue 是 "eval" or "arguments"，则为语法错误
- 若 GeneratorBody 的 ContainsUseStrict 是 true and FormalParameters 的 IsSimpleParameterList 是 false，则为语法错误
- 若 any element of the BoundNames of FormalParameters also occurs in the GeneratorBody 的 LexicallyDeclaredNames，则为语法错误
- 若 FormalParameters 包含YieldExpression 是 true，则为语法错误
- 若 FormalParameters 包含SuperProperty 是 true，则为语法错误
- 若 GeneratorBody 包含SuperProperty 是 true，则为语法错误
- 若 FormalParameters 包含SuperCall 是 true，则为语法错误
- 若 GeneratorBody 包含SuperCall 是 true，则为语法错误

### 14.4.2 静态语义：BoundNames <div id="sec-generator-function-definitions-static-semantics-boundnames"></div>

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. 返回 the BoundNames of BindingIdentifier

```
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
```

1. 返回 « "\*default\*" ».

> 注 "\*default\*"在本规范中用作使用导出声明定义的可提升匿名函数的合成名称。.

### 14.4.3 静态语义：ComputedProperty包含<div id="sec-generator-function-definitions-static-semantics-computedpropertycontains"></div>

带有参数 symbol.

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

1. 返回 the result of ComputedProperty包含for PropertyName with argument symbol.

### 14.4.4 静态语义：包含<div id="sec-generator-function-definitions-static-semantics-contains"></div>

带有参数 symbol.

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. 返回 false.

> 注 Static semantic rules that depend upon substructure generally do not look into function definitions.

### 14.4.5 静态语义：HasDirectSuper <div id="sec-generator-function-definitions-static-semantics-hasdirectsuper"></div>

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

1. 若 UniqueFormalParameters 包含SuperCall 是 true, 返回 true.
2. 返回 GeneratorBody 包含SuperCall.

### 14.4.6 静态语义：HasName <div id="sec-generator-function-definitions-static-semantics-hasname"></div>

```
GeneratorExpression : function * ( FormalParameters ) { GeneratorBody }
```

1. 返回 false.

```
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. 返回 true

### 14.4.7 静态语义：IsConstantDeclaration <div id="sec-generator-function-definitions-static-semantics-isconstantdeclaration"></div>

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
```

1. 返回 false

### 14.4.8 静态语义：IsFunctionDefinition <div id="sec-generator-function-definitions-static-semantics-isfunctiondefinition"></div>

```
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. 返回 true.

### 14.4.9 静态语义：PropName <div id="sec-generator-function-definitions-static-semantics-propname"></div>

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

1. 返回 PropertyName 的 PropName

### 14.4.10 运行时语义：EvaluateBody <div id="sec-generator-function-definitions-runtime-semantics-evaluatebody"></div>

带有参数 functionObject 和 argumentsList列表.

```
GeneratorBody : FunctionBody
```

1. 执行 ? FunctionDeclarationInstantiation(functionObject, argumentsList).
2. 令 G 为 ? OrdinaryCreateFromConstructor(functionObject, "%GeneratorPrototype%", « [[GeneratorState]], [[GeneratorContext]] »)
3. 执行 GeneratorStart(G, FunctionBody).
4. 返回 Completion { [[Type]]: 返回, [[Value]]: G, [[Target]]: empty }.

### 14.4.11 运行时语义：InstantiateFunctionObject <div id="sec-generator-function-definitions-runtime-semantics-instantiatefunctionobject"></div>

带有参数 scope.

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. 若 the function code for GeneratorDeclaration is strict mode code, 令 strict 为 true. 否则，令 strict 为 false.
2. 令 name 为 BindingIdentifier 的 StringValue
3. 令 F 为 GeneratorFunctionCreate(Normal, FormalParameters, GeneratorBody, scope, strict).
4. 令 prototype 为 ObjectCreate(%GeneratorPrototype%).
5. 执行 DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
true, [[Enumerable]]: false, [[Configurable]]: false }).
6. 执行 SetFunctionName(F, name).
7. 设置 F.[[SourceText]] 为GeneratorDeclaration匹配的源文本.
8. 返回 F

```
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
```

1. 令 F 为 GeneratorFunctionCreate(Normal, FormalParameters, GeneratorBody, scope, true).
2. 令 prototype 为 ObjectCreate(%GeneratorPrototype%).
3. 执行 DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
true, [[Enumerable]]: false, [[Configurable]]: false }).
4. 执行 SetFunctionName(F, "default").
5. 设置 F.[[SourceText]] 为GeneratorDeclaration匹配的源文本.
6. 返回 F.

> 注 An anonymous GeneratorDeclaration can only occur as part of an export default declaration, and its function code is therefore always strict mode code.

### 14.4.12 运行时语义：PropertyDefinitionEvaluation <div id="sec-generator-function-definitions-runtime-semantics-propertydefinitionevaluation"></div>

带有参数 object 和 enumerable.

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

1. 令 propKey 为运算 PropertyName 的结果
2. ReturnIfAbrupt(propKey).
3. 若此GeneratorMethod的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
4. 令 scope 为 运行时上下文的 LexicalEnvironment.
5. 令 closure 为 GeneratorFunctionCreate(Method, UniqueFormalParameters, GeneratorBody, scope, strict).
6. 执行 MakeMethod(closure, object).
7. 令 prototype 为 ObjectCreate(%GeneratorPrototype%).
8. 执行 DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
9. 执行 SetFunctionName(closure, propKey).
10. 设置 closure.[[SourceText]] 为GeneratorMethod匹配的源文本.
11. 令 desc 为 the PropertyDescriptor { [[Value]]: closure, [[Writable]]: true, [[Enumerable]]: enumerable,
[[Configurable]]: true }.
12. 返回 ? DefinePropertyOrThrow(object, propKey, desc).

### 14.4.13 运行时语义：NamedEvaluation <div id="sec-generator-function-definitions-runtime-semantics-namedevaluation"></div>

带有参数 name.

```
GeneratorExpression : function * ( FormalParameters ) { GeneratorBody }
```

1. 令 closure 为运算 this 的结果GeneratorExpression.
2. 执行 SetFunctionName(closure, name).
3. 返回 closure.

### 14.4.14 运行时语义：Evaluation <div id="sec-generator-function-definitions-runtime-semantics-evaluation"></div>

```
GeneratorExpression : function * ( FormalParameters ) { GeneratorBody }
```

1. 若此GeneratorExpression的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
2. 令 scope 为运行时上下文的LexicalEnvironment.
3. 令 closure 为 GeneratorFunctionCreate(Normal, FormalParameters, GeneratorBody, scope, strict).
4. 令 prototype 为 ObjectCreate(%GeneratorPrototype%).
5. 执行 DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
6. 设置 closure.[[SourceText]] 为GeneratorExpression匹配的源文本.
7. 返回 closure

```
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. 若此GeneratorExpression的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为
false.
2. 令 scope 为 运行时上下文的 LexicalEnvironment.
3. 令 funcEnv 为 NewDeclarativeEnvironment(scope).
4. 令 envRec 为 funcEnv's EnvironmentRecord.
5. 令 name 为 BindingIdentifier 的 StringValue
6. 执行 envRec.CreateImmutableBinding(name, false).
7. 令 closure 为 GeneratorFunctionCreate(Normal, FormalParameters, GeneratorBody, funcEnv, strict).
8. 令 prototype 为 ObjectCreate(%GeneratorPrototype%).
9. 执行 DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
10. 执行 SetFunctionName(closure, name).
11. 执行 envRec.InitializeBinding(name, closure).
12. 设置 closure.[[SourceText]] 为GeneratorExpression匹配的源文本.
13. 返回 closure.

> 注 The BindingIdentifier in a GeneratorExpression can be referenced from inside the GeneratorExpression's FunctionBody to allow the generator code to call itself recursively. However, unlike in a GeneratorDeclaration, the BindingIdentifier in a GeneratorExpression cannot be referenced from and does not affect the scope enclosing the GeneratorExpression.

```
YieldExpression : yield
```

1. 令 generatorKind 为 ! GetGeneratorKind().
2. 若 generatorKind is async, then 返回 ? AsyncGeneratorYield(undefined).
3. 否则, 返回 ? GeneratorYield(CreateIterResultObject(undefined, false)).

```
YieldExpression : yield AssignmentExpression
```

1. 令 generatorKind 为 ! GetGeneratorKind().
2. 令 exprRef 为运算 AssignmentExpression 的结果
3. 令 value 为 ? GetValue(exprRef).
4. 若 generatorKind is async, then 返回 ? AsyncGeneratorYield(value).
5. 否则, 返回 ? GeneratorYield(CreateIterResultObject(value, false)).

```
YieldExpression : yield * AssignmentExpression
```

1. 令 generatorKind 为 ! GetGeneratorKind().
2. 令 exprRef 为运算 AssignmentExpression 的结果
3. 令 value 为 ? GetValue(exprRef).
4. 令 iteratorRecord 为 ? GetIterator(value, generatorKind).
5. 令 iterator 为 iteratorRecord.[[Iterator]].
6. 令 received 为 NormalCompletion(undefined).
7. Repeat,
a. 若 received.[[Type]] is normal, then
i. 令 innerResult 为 ? Call(iteratorRecord.[[NextMethod]], iteratorRecord.[[Iterator]], « received.
[[Value]] »).
ii. 若 generatorKind is async, then 设置 innerResult 为 ? Await(innerResult).
iii. 若 Type(innerResult) is not Object, throw a TypeError exception.
iv. 令 done 为 ? IteratorComplete(innerResult).
v. 若 done 是 true, then
1. 返回 ? IteratorValue(innerResult).
vi. 若 generatorKind is async, then 设置 received 为 AsyncGeneratorYield(? IteratorValue(innerResult)).
vii. 否则, 设置 received 为 GeneratorYield(innerResult).
b. 否则 若 received.[[Type]] is throw, then
i. 令 throw 为 ? GetMethod(iterator, "throw").
ii. 若 throw is not undefined, then
1. 令 innerResult 为 ? Call(throw, iterator, « received.[[Value]] »).
2. 若 generatorKind is async, then 设置 innerResult 为 ? Await(innerResult).
3. 注: Exceptions from the inner iterator throw method are propagated. Normal completions
from an inner throw method are processed similarly to an inner next.
4. 若 Type(innerResult) is not Object, throw a TypeError exception.
5. 令 done 为 ? IteratorComplete(innerResult).
6. 若 done 是 true, then
a. 返回 ? IteratorValue(innerResult).
7. 若 generatorKind is async, then 设置 received 为 AsyncGeneratorYield(?
IteratorValue(innerResult)).
8. 否则, 设置 received 为 GeneratorYield(innerResult).
iii. 否则,
17. 注: 若 iterator does not have a throw method, this throw is going to terminate the yield*
      loop. But first we need to give iterator a chance to clean up.
2. 令 closeCompletion 为 Completion { [[Type]]: normal, [[Value]]: empty, [[Target]]: empty }.
3. 若 generatorKind is async, 执行 ? AsyncIteratorClose(iteratorRecord, closeCompletion).
4. 否则, 执行 ? IteratorClose(iteratorRecord, closeCompletion).
5. 注: The next step throws a TypeError to indicate that there was a yield* protocol violation:
iterator does not have a throw method.
6. Throw a TypeError exception.
c. 否则,
i. 断言: received.[[Type]] is 返回.
ii. 令 返回 为 ? GetMethod(iterator, "返回").
iii. 若 返回 is undefined, then
1. 若 generatorKind is async, then 设置 received.[[Value]] 为 ? Await(received.[[Value]]).
2. 返回 Completion(received).
iv. 令 innerReturnResult 为 ? Call(返回, iterator, « received.[[Value]] »).
v. 若 generatorKind is async, then 设置 innerReturnResult 为 ? Await(innerReturnResult).
vi. 若 Type(innerReturnResult) is not Object, throw a TypeError exception.
vii. 令 done 为 ? IteratorComplete(innerReturnResult).
viii. 若 done 是 true, then
1. 令 value 为 ? IteratorValue(innerReturnResult).
2. 返回 Completion { [[Type]]: 返回, [[Value]]: value, [[Target]]: empty }.
ix. 若 generatorKind is async, then 设置 received 为 AsyncGeneratorYield(?
IteratorValue(innerReturnResult)).
x. 否则, 设置 received 为 GeneratorYield(innerReturnResult).

## 14.5 异步 Generator 函数定义 <div id="sec-async-generator-function-definitions"></div>

**语法**

```
AsyncGeneratorMethod[Yield, Await] :
    async [no LineTerminator here] * PropertyName[?Yield, ?Await] (UniqueFormalParameters[+Yield, +Await] ) { AsyncGeneratorBody }
AsyncGeneratorDeclaration[Yield, Await, Default] :
    async [no LineTerminator here] function * BindingIdentifier[?Yield, ?Await] ( FormalParameters[+Yield, +Await] ) { AsyncGeneratorBody }
	[+Default] async [no LineTerminator here] function * ( FormalParameters[+Yield, +Await] ) { AsyncGeneratorBody }
AsyncGeneratorExpression :
	async [no LineTerminator here] function * BindingIdentifier[+Yield, +Await] opt ( FormalParameters[+Yield, +Await] ) { AsyncGeneratorBody }
AsyncGeneratorBody :
	FunctionBody[+Yield, +Await]
```

> 注1 YieldExpression and AwaitExpression cannot be used within the FormalParameters of an async generator function because any expressions that are part of FormalParameters are evaluated before the resulting async generator object is in a resumable state.

> 注 2 Abstract operations relating to async generator objects are defined in 25.5.3

### 14.5.1 静态语义：Early Errors <div id="sec-async-generator-function-definitions-static-semantics-early-errors"></div>

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

- 若 AsyncGeneratorMethod 的 HasDirectSuper 是 true，则为语法错误
- 若 UniqueFormalParameters 包含YieldExpression 是 true，则为语法错误
- 若 UniqueFormalParameters 包含AwaitExpression 是 true，则为语法错误
- 若 AsyncGeneratorBody 的 ContainsUseStrict 是 true and UniqueFormalParameters 的 IsSimpleParameterList 是 false，则为语法错误
- 若 any element of the BoundNames of UniqueFormalParameters also occurs in the AsyncGeneratorBody 的 LexicallyDeclaredNames，则为语法错误

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) { AsyncGeneratorBody }
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) { AsyncGeneratorBody }
```

- 若 the source code matching this production is strict mode code, the Early Error rules for UniqueFormalParameters : FormalParameters are applied.
- 若 the source code matching this production is strict mode code, 若 BindingIdentifier is the IdentifierName eval or the IdentifierName arguments，则为语法错误
- 若 AsyncGeneratorBody 的 ContainsUseStrict 是 true and IsSimpleParameterList of，则为语法错误
  FormalParameters is false.
- 若 any element of the BoundNames of FormalParameters also occurs in the，则为语法错误
  AsyncGeneratorBody 的 LexicallyDeclaredNames
- 若 FormalParameters 包含YieldExpression 是 true，则为语法错误
- 若 FormalParameters 包含AwaitExpression 是 true，则为语法错误
- 若 FormalParameters 包含SuperProperty 是 true，则为语法错误
- 若 AsyncGeneratorBody 包含SuperProperty 是 true，则为语法错误
- 若 FormalParameters 包含SuperCall 是 true，则为语法错误
- 若 AsyncGeneratorBody 包含SuperCall 是 true，则为语法错误

### 14.5.2 静态语义：BoundNames <div id="sec-async-generator-function-definitions-static-semantics-boundnames"></div>

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. 返回 the BindingIdentifier 的 BoundNames

```
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. 返回 « "\*default\*" »

> 注 "\*default\*"在本规范中用作使用导出声明定义的可提升匿名函数的合成名称。

### 14.5.3 静态语义：ComputedProperty包含<div id="sec-async-generator-function-definitions-static-semantics-computedpropertycontains"></div>

带有参数 symbol.

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

1. 返回 the result of ComputedProperty包含for PropertyName with argument symbol.

### 14.5.4 静态语义：包含<div id="sec-async-generator-function-definitions-static-semantics-contains"></div>

带有参数 symbol.

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. 返回 false.

> 注 Static semantic rules that depend upon substructure generally do not look into function definitions.

### 14.5.5 静态语义：HasDirectSuper <div id="sec-async-generator-function-definitions-static-semantics-hasdirectsuper"></div>

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

1. 若 UniqueFormalParameters 包含SuperCall 是 true, 返回 true.
2. 返回 AsyncGeneratorBody 包含SuperCall.

### 14.5.6 静态语义：HasName <div id="sec-async-generator-function-definitions-static-semantics-hasname"></div>

```
AsyncGeneratorExpression : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. 返回 false.

```
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. 返回 true.

### 14.5.7 静态语义：IsConstantDeclaration <div id="sec-async-generator-function-definitions-static-semantics-isconstantdeclaration"></div>

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. 返回 false.

### 14.5.8 静态语义：IsFunctionDefinition <div id="sec-async-generator-function-definitions-static-semantics-isfunctiondefinition"></div>

```
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. 返回 true.

### 14.5.9 静态语义：PropName <div id="sec-async-generator-function-definitions-static-semantics-propname"></div>

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

1. 返回 PropertyName 的 PropName

### 14.5.10 运行时语义：EvaluateBody <div id="sec-asyncgenerator-definitions-evaluatebody"></div>

带有参数 functionObject 和 argumentsList列表.

```
AsyncGeneratorBody : FunctionBody
```

1. 执行 ? FunctionDeclarationInstantiation(functionObject, argumentsList).
2. 令 generator 为 ? OrdinaryCreateFromConstructor(functionObject, "%AsyncGeneratorPrototype%", «
[[AsyncGeneratorState]], [[AsyncGeneratorContext]], [[AsyncGeneratorQueue]] »).
3. 执行 ! AsyncGeneratorStart(generator, FunctionBody).
4. 返回 Completion { [[Type]]: 返回, [[Value]]: generator, [[Target]]: empty }.

### 14.5.11 运行时语义：InstantiateFunctionObject <div id="sec-asyncgenerator-definitions-instantiatefunctionobject"></div>

带有参数 scope.

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. 若 the function code for AsyncGeneratorDeclaration is strict mode code, 令 strict 为 true. 否则，令 strict 为
false.
2. 令 name 为 BindingIdentifier 的 StringValue
3. 令 F 为 ! AsyncGeneratorFunctionCreate(Normal, FormalParameters, AsyncGeneratorBody, scope, strict).
4. 令 prototype 为 ! ObjectCreate(%AsyncGeneratorPrototype%).
5. 执行 ! DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
true, [[Enumerable]]: false, [[Configurable]]: false }).
6. 执行 ! SetFunctionName(F, name).
7. 设置 F.[[SourceText]] 为AsyncGeneratorDeclaration匹配的源文本.
8. 返回 F.

```
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. 若 the function code for AsyncGeneratorDeclaration is strict mode code, 令 strict 为 true. 否则，令 strict 为 false.
2. 令 F 为 AsyncGeneratorFunctionCreate(Normal, FormalParameters, AsyncGeneratorBody, scope, strict).
3. 令 prototype 为 ObjectCreate(%AsyncGeneratorPrototype%).
4. 执行 DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
   true, [[Enumerable]]: false, [[Configurable]]: false }).
5. 执行 SetFunctionName(F, "default").
6. 设置 F.[[SourceText]] 为AsyncGeneratorDeclaration匹配的源文本.
7. 返回 F

> 注 An anonymous AsyncGeneratorDeclaration can only occur as part of an export default declaration.

### 14.5.12 运行时语义：PropertyDefinitionEvaluation <div id="sec-asyncgenerator-definitions-propertydefinitionevaluation"></div>

带有参数 object and enumerable.

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

1. 令 propKey 为运算 PropertyName 的结果
2. ReturnIfAbrupt(propKey).
3. 若此AsyncGeneratorMethod的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为
false.
4. 令 scope 为 运行时上下文的 LexicalEnvironment.
5. 令 closure 为 ! AsyncGeneratorFunctionCreate(Method, UniqueFormalParameters, AsyncGeneratorBody, scope,
strict).
6. 执行 ! MakeMethod(closure, object).
7. 令 prototype 为 ! ObjectCreate(%AsyncGeneratorPrototype%).
8. 执行 ! DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
9. 执行 ! SetFunctionName(closure, propKey).
10. 设置 closure.[[SourceText]] 为AsyncGeneratorMethod匹配的源文本.
11. 令 desc 为 PropertyDescriptor { [[Value]]: closure, [[Writable]]: true, [[Enumerable]]: enumerable,
[[Configurable]]: true }.
12. 返回 ? DefinePropertyOrThrow(object, propKey, desc).

### 14.5.13 运行时语义：NamedEvaluation <div id="sec-asyncgenerator-definitions-namedevaluation"></div>

带有参数 name.

```
AsyncGeneratorExpression : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. 令 closure 为运算 this 的结果AsyncGeneratorExpression.
2. 执行 SetFunctionName(closure, name).
3. 返回 closure.

### 14.5.14 运行时语义：Evaluation <div id="sec-asyncgenerator-definitions-evaluation"></div>

```
AsyncGeneratorExpression : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. 若此AsyncGeneratorExpression的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
2. 令 scope 为运行时上下文的LexicalEnvironment.
3. 令 closure 为 ! AsyncGeneratorFunctionCreate(Normal, FormalParameters, AsyncGeneratorBody, scope, strict).
4. 令 prototype 为 ! ObjectCreate(%AsyncGeneratorPrototype%).
5. 执行 ! DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
6. 设置 closure.[[SourceText]] 为AsyncGeneratorExpression匹配的源文本.
7. 返回 closure.

```
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. 若此AsyncGeneratorExpression的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为
false.
2. 令 scope 为 运行时上下文的 LexicalEnvironment.
3. 令 funcEnv 为 ! NewDeclarativeEnvironment(scope).
4. 令 envRec 为 funcEnv's EnvironmentRecord.
5. 令 name 为 BindingIdentifier 的 StringValue
6. 执行 ! envRec.CreateImmutableBinding(name).
7. 令 closure 为 ! AsyncGeneratorFunctionCreate(Normal, FormalParameters, AsyncGeneratorBody, funcEnv, strict).
8. 令 prototype 为 ! ObjectCreate(%AsyncGeneratorPrototype%).
9. 执行 ! DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
10. 执行 ! SetFunctionName(closure, name).
11. 执行 ! envRec.InitializeBinding(name, closure).
12. 设置 closure.[[SourceText]] 为AsyncGeneratorExpression匹配的源文本.
13. 返回 closure.

> 注 The BindingIdentifier in an AsyncGeneratorExpression can be referenced from inside the AsyncGeneratorExpression's AsyncGeneratorBody to allow the generator code to call itself recursively. However, unlike in an AsyncGeneratorDeclaration, the BindingIdentifier in an AsyncGeneratorExpression cannot be referenced from and does not affect the scope enclosing the AsyncGeneratorExpression.

## 14.6 Class 定义 <div id="sec-class-definitions"></div>

**语法**

```
ClassDeclaration[Yield, Await, Default] :
    class BindingIdentifier[?Yield, ?Await] ClassTail[?Yield, ?Await]
    [+Default] class ClassTail[?Yield, ?Await]
ClassExpression[Yield, Await] :
	class BindingIdentifier[?Yield, ?Await] opt ClassTail[?Yield, ?Await]
ClassTail[Yield, Await] :
	ClassHeritage[?Yield, ?Await] opt { ClassBody[?Yield, ?Await] opt }
ClassHeritage[Yield, Await] :
	extends LeftHandSideExpression[?Yield, ?Await]
ClassBody[Yield, Await] :
	ClassElementList[?Yield, ?Await]
ClassElementList[Yield, Await] :
	ClassElement[?Yield, ?Await]
	ClassElementList[?Yield, ?Await] ClassElement[?Yield, ?Await]
ClassElement[Yield, Await] :
	MethodDefinition[?Yield, ?Await]
    static MethodDefinition[?Yield, ?Await]
    ;
```

> 注 A class definition is always strict mode code.

### 14.6.1 静态语义：Early Errors <div id="sec-class-definitions-static-semantics-early-errors"></div>

```
ClassTail : ClassHeritage { ClassBody }
```

- 若 ClassHeritage is not present and the following algorithm evaluates to true:，则为语法错误
  1. 令 constructor 为 ClassBody 的 ConstructorMethod
  2. 若 constructor is empty, 返回 false.
  3. 返回 constructor 的 HasDirectSuper

```
ClassBody : ClassElementList
```

- 若 PrototypePropertyNameList of ClassElementList 包含more than one occurrence of "constructor"，则为语法错误

```
ClassElement : MethodDefinition
```

- 若 MethodDefinition 的 PropName 是 not "constructor" and HasDirectSuper of，则为语法错误
  MethodDefinition 是 true.
- 若 MethodDefinition 的 PropName 是 "constructor" and SpecialMethod of，则为语法错误
  MethodDefinition 是 true.

```
ClassElement : static MethodDefinition
```

- 若 MethodDefinition 的 HasDirectSuper 是 true，则为语法错误
- 若 MethodDefinition 的 PropName 是 "prototype"，则为语法错误

### 14.6.2 静态语义：BoundNames <div id="sec-class-definitions-static-semantics-boundnames"></div>

```
ClassDeclaration : class BindingIdentifier ClassTail
```

1. 返回 the BoundNames of BindingIdentifier

```
ClassDeclaration : class ClassTail
```

1. 返回 « "\*default\*" ».

### 14.6.3 静态语义：ConstructorMethod <div id="sec-static-semantics-constructormethod"></div>

```
ClassElementList : ClassElement
```

1. 若 ClassElement is ClassElement : ; , 返回 empty.
2. 若 ClassElement 的 IsStatic 是 true, 返回 empty.
3. 若 ClassElement 的 PropName 是 not "constructor", 返回 empty.
4. 返回 ClassElement.

```
ClassElementList : ClassElementList ClassElement
```

1. 令 head 为 ClassElementList 的 ConstructorMethod
2. 若 head is not empty, 返回 head.
3. 若 ClassElement is ClassElement : ; , 返回 empty.
4. 若 ClassElement 的 IsStatic 是 true, 返回 empty.
5. 若 ClassElement 的 PropName 是 not "constructor", 返回 empty.
6. 返回 ClassElement

> 注 Early Error rules ensure that there is only one method definition named "constructor" and that it is not an accessor property or generator definition.

### 14.6.4 静态语义：包含<div id="sec-class-definitions-static-semantics-contains"></div>

带有参数 symbol.

```
ClassTail : ClassHeritage { ClassBody }
```

1. 若 symbol is ClassBody, 返回 true.
2. 若 symbol is ClassHeritage, then
a. 若 ClassHeritage is present, 返回 true; 否则 返回 false.
3. 令 inHeritage 为 ClassHeritage 包含symbol.
4. 若 inHeritage 是 true, 返回 true.
5. 返回 the result of ComputedProperty包含for ClassBody with argument symbol.

> 注 Static semantic rules that depend upon substructure generally do not look into class bodies except for PropertyNames

### 14.6.5 静态语义：ComputedProperty包含<div id="sec-class-definitions-static-semantics-computedpropertycontains"></div>

带有参数 symbol.

```
ClassElementList : ClassElementList ClassElement
```

1. 令 inList 为 the result of ComputedProperty包含for ClassElementList with argument symbol.
2. 若 inList 是 true, 返回 true.
3. 返回 the result of ComputedProperty包含for ClassElement with argument symbol.

```
ClassElement : MethodDefinition
```

1. 返回 the result of ComputedProperty包含for MethodDefinition with argument symbol

```
ClassElement : static MethodDefinition
```

1. 返回 the result of ComputedProperty包含for MethodDefinition with argument symbol.

```
ClassElement : ;
```

1. 返回 false.

### 14.6.6 静态语义：HasName <div id="sec-class-definitions-static-semantics-hasname"></div>

```
ClassExpression : class ClassTail
```

1. 返回 false.

```
ClassExpression : class BindingIdentifier ClassTail
```

1. 返回 true.

### 14.6.7 静态语义：IsConstantDeclaration <div id="sec-class-definitions-static-semantics-isconstantdeclaration"></div>

```
ClassDeclaration : class BindingIdentifier ClassTail
ClassDeclaration : class ClassTail
```

1. 返回 false.

### 14.6.8 静态语义：IsFunctionDefinition <div id="sec-class-definitions-static-semantics-isfunctiondefinition"></div>

```
ClassExpression : class BindingIdentifier ClassTail
```

1. 返回 true.

### 14.6.9 静态语义：IsStatic <div id="sec-static-semantics-isstatic"></div>

```
ClassElement : MethodDefinition
```

1. 返回 false.

```
ClassElement : static MethodDefinition
```

1. 返回 true.

```
ClassElement : ;
```

1. 返回 false.

### 14.6.10 静态语义：NonConstructorMethodDefinitions <div id="sec-static-semantics-nonconstructormethoddefinitions"></div>

```
ClassElementList : ClassElement
```

1. 若 ClassElement is ClassElement : ; , 返回一个新的空列表.
2. 若 ClassElement 的 IsStatic 是 false and ClassElement 的 PropName 是 "constructor", 返回一个新的空列表.
3. 返回 a List containing ClassElement.

```
ClassElementList : ClassElementList ClassElement
```

1. 令 list 为 ClassElementList 的 NonConstructorMethodDefinitions
2. 若 ClassElement is ClassElement : ; , 返回 list.
3. 若 ClassElement 的 IsStatic 是 false and ClassElement 的 PropName 是 "constructor", 返回 list.
4. Append ClassElement to the list 的 end
5. 返回 list.

### 14.6.11 静态语义：PrototypePropertyNameList <div id="sec-static-semantics-prototypepropertynamelist"></div>

```
ClassElementList : ClassElement
```

1. 若 ClassElement 的 PropName 是 empty, 返回一个新的空列表.
2. 若 ClassElement 的 IsStatic 是 true, 返回一个新的空列表.
3. 返回 a List containing ClassElement 的 PropName

```
ClassElementList : ClassElementList ClassElement
```

1. 令 list 为 ClassElementList 的 PrototypePropertyNameList
2. 若 ClassElement 的 PropName 是 empty, 返回 list.
3. 若 ClassElement 的 IsStatic 是 true, 返回 list.
4. Append PropName of ClassElement to the list 的 end
5. 返回 list.

### 14.6.12 静态语义：PropName <div id="sec-class-definitions-static-semantics-propname"></div>

```
ClassElement : ;
```

1. 返回 empty.

### 14.6.13 运行时语义：ClassDefinitionEvaluation <div id="sec-runtime-semantics-classdefinitionevaluation"></div>

带有参数 classBinding 和 className

1. 令 lex 为运行时上下文的LexicalEnvironment.
2. 令 classScope 为 NewDeclarativeEnvironment(lex).
3. 令 classScopeEnvRec 为 classScope's EnvironmentRecord.
4. 若 classBinding is not undefined, then
a. 执行 classScopeEnvRec.CreateImmutableBinding(classBinding, true).
5. 若 ClassHeritageopt is not present, then
a. 令 protoParent 为 the intrinsic object %ObjectPrototype%.
b. 令 constructorParent 为 the intrinsic object %FunctionPrototype%.
6. 否则,
a. 设置 运行时上下文的 LexicalEnvironment 为 classScope.
b. 令 superclassRef 为运算 ClassHeritage 的结果
7. c. 设置 运行时上下文的 LexicalEnvironment 为 lex.
    d. 令 superclass 为 ? GetValue(superclassRef).
    e. 若 superclass is null, then
    i. 令 protoParent 为 null.
    ii. 令 constructorParent 为 the intrinsic object %FunctionPrototype%.
    f. 否则 若 IsConstructor(superclass) is false, throw a TypeError exception.
    g. 否则,
    i. 令 protoParent 为 ? Get(superclass, "prototype").
    ii. 若 Type(protoParent) is neither Object nor Null, throw a TypeError exception.
    iii. 令 constructorParent 为 superclass.
  7. 令 proto 为 ObjectCreate(protoParent).
  8. 若 ClassBodyopt is not present, 令 constructor 为 empty.
  9. 否则, 令 constructor 为 ClassBody 的 ConstructorMethod
  10. 若 constructor is empty, then

    a. 若 ClassHeritageopt is present, then
    i. 设置 constructor 为 the result of parsing the source text
    constructor(... args){ super (...args);}
    using the syntactic grammar with the goal symbol MethodDefinition[~Yield, ~Await] .
    b. 否则,
    i. 设置 constructor 为 the result of parsing the source text
    constructor(){ }
    using the syntactic grammar with the goal symbol MethodDefinition[~Yield, ~Await] .
  11. 设置 运行时上下文的 LexicalEnvironment 为 classScope.
  12. 令 constructorInfo 为 the result of performing DefineMethod for constructor with arguments proto and

    constructorParent as the optional functionPrototype argument.
  13. 断言: constructorInfo is not an abrupt completion.
  14. 令 F 为 constructorInfo.[[Closure]].
  15. 若 ClassHeritageopt is present, 设置 F.[[ConstructorKind]] 为 "derived".
  16. 执行 MakeConstructor(F, false, proto).
  17. 执行 MakeClassConstructor(F).
  18. 若 className is not undefined, then

    a. 执行 SetFunctionName(F, className).
  19. 执行 CreateMethodProperty(proto, "constructor", F).
  20. 若 ClassBodyopt is not present, 令 methods 为一个新的空列表.
  21. 否则, 令 methods 为 ClassBody 的 NonConstructorMethodDefinitions
  22. For each ClassElement m in order from methods, do

    a. 若 m 的 IsStatic 是 false, then
    i. 令 status 为 the result of performing PropertyDefinitionEvaluation for m with arguments proto and
    false.
    b. 否则,
    i. 令 status 为 the result of performing PropertyDefinitionEvaluation for m with arguments F and false.
    c. 若 status is an abrupt completion, then
    i. 设置 运行时上下文的 LexicalEnvironment 为 lex.
    ii. 返回 Completion(status).
  23. 设置 运行时上下文的 LexicalEnvironment 为 lex.
  24. 若 classBinding is not undefined, then
      a. 执行 classScopeEnvRec.InitializeBinding(classBinding, F).
  25. 返回 F.

### 14.6.14 运行时语义：BindingClassDeclarationEvaluation <div id="sec-runtime-semantics-bindingclassdeclarationevaluation"></div>

```
ClassDeclaration : class BindingIdentifier ClassTail
```

1. 令 className 为 BindingIdentifier 的 StringValue
2. 令 value 为 the result of ClassDefinitionEvaluation of ClassTail with arguments className and className.
3. ReturnIfAbrupt(value).
4. 设置 value.[[SourceText]] 为ClassDeclaration匹配的源文本.
5. 令 env 为 运行时上下文的 LexicalEnvironment.
6. 执行 ? InitializeBoundName(className, value, env).
7. 返回 value.

```
ClassDeclaration : class ClassTail
```

1. 令 value 为 the result of ClassDefinitionEvaluation of ClassTail with arguments undefined and "default".
2. ReturnIfAbrupt(value).
3. 设置 value.[[SourceText]] 为ClassDeclaration匹配的源文本.
4. 返回 value.

> 注 ClassDeclaration : class ClassTail only occurs as part of an ExportDeclaration and establishing its binding is handled as part of the evaluation action for that production. See 15.2.3.11.

### 14.6.15 运行时语义：NamedEvaluation <div id="sec-class-definitions-runtime-semantics-namedevaluation"></div>

带有参数 name.

```
ClassExpression : class ClassTail
```

1. 返回 the result of ClassDefinitionEvaluation of ClassTail with arguments undefined and name.

### 14.6.16 运行时语义：Evaluation <div id="sec-class-definitions-runtime-semantics-evaluation"></div>

```
ClassDeclaration : class BindingIdentifier ClassTail
```

1. 执行 ? BindingClassDeclarationEvaluation of this ClassDeclaration.
2. 返回 NormalCompletion(empty).

> 注 ClassDeclaration : class ClassTail only occurs as part of an ExportDeclaration and is never directly evaluated.

```
ClassExpression : class BindingIdentifier ClassTail
```

1. 若 BindingIdentifieropt is not present, 令 className 为 undefined.
2. 否则, 令 className 为 BindingIdentifier 的 StringValue
3. 令 value 为 the result of ClassDefinitionEvaluation of ClassTail with arguments className and className.
4. ReturnIfAbrupt(value).

5. 设置 value.[[SourceText]] 为ClassExpression匹配的源文本.
6. 返回 value.

## 14.7 Async 函数定义 <div id="sec-async-function-definitions"></div>

**语法**

```
AsyncFunctionDeclaration[Yield, Await, Default] :
    async [no LineTerminator here] function BindingIdentifier[?Yield, ?Await] (
    FormalParameters[~Yield, +Await] ) { AsyncFunctionBody }
    [+Default] async [no LineTerminator here] function ( FormalParameters[~Yield, +Await] ) { AsyncFunctionBody }
AsyncFunctionExpression :
    async [no LineTerminator here] function ( FormalParameters[~Yield, +Await] ) { AsyncFunctionBody }
    async [no LineTerminator here] function BindingIdentifier[~Yield, +Await] ( FormalParameters[~Yield, +Await] ) { AsyncFunctionBody }
AsyncMethod[Yield, Await] :
    async [no LineTerminator here] PropertyName[?Yield, ?Await] (
    UniqueFormalParameters[~Yield, +Await] ) { AsyncFunctionBody }
AsyncFunctionBody :
	FunctionBody[~Yield, +Await]
AwaitExpression[Yield] :
	await UnaryExpression[?Yield, +Await]
```

> 注 1 await is parsed as an AwaitExpression when the [Await] parameter is present. The [Await] parameter is present in the following contexts:
>
> In an AsyncFunctionBody.
> In the FormalParameters of an AsyncFunctionDeclaration, AsyncFunctionExpression, AsyncGeneratorDeclaration,
> or AsyncGeneratorExpression. AwaitExpression in this position is a Syntax error via static semantics.
>
> When Module is the syntactic goal symbol and the [Await] parameter is absent, await is parsed as a keyword and will be a Syntax error. When Script is the syntactic goal symbol, await may be parsed as an identifier when the [Await] parameter is absent. This includes the following contexts:
>
> Anywhere outside of an AsyncFunctionBody or FormalParameters of an AsyncFunctionDeclaration,
> AsyncFunctionExpression, AsyncGeneratorDeclaration, or AsyncGeneratorExpression.
> In the BindingIdentifier of a FunctionExpression, GeneratorExpression, or AsyncGeneratorExpression.

>注 2
>Unlike YieldExpression, to omit the operand of an AwaitExpression. You must await something，则为语法错误

### 14.7.1 静态语义：Early Errors <div id="sec-async-function-definitions-static-semantics-early-errors"></div>

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

- 若 AsyncFunctionBody 的 ContainsUseStrict 是 true and IsSimpleParameterList of，则为语法错误
  UniqueFormalParameters is false.
- 若 AsyncMethod 的 HasDirectSuper 是 true，则为语法错误
- 若 UniqueFormalParameters 包含AwaitExpression 是 true，则为语法错误
- 若 any element of the BoundNames of UniqueFormalParameters also occurs in the，则为语法错误
  AsyncFunctionBody 的 LexicallyDeclaredNames

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

- 若 AsyncFunctionBody 的 ContainsUseStrict 是 true and IsSimpleParameterList of，则为语法错误
  FormalParameters is false.
- 若 FormalParameters 包含AwaitExpression 是 true，则为语法错误
- 若 the source code matching this production is strict code, the Early Error rules for UniqueFormalParameters :
  FormalParameters are applied.
- 若 the source code matching this production is strict code, 若 BindingIdentifier is present and the BindingIdentifier 的 StringValue 是 "eval" or "arguments"，则为语法错误
- 若 any element of the BoundNames of FormalParameters also occurs in the，则为语法错误
  AsyncFunctionBody 的 LexicallyDeclaredNames
- 若 FormalParameters 包含SuperProperty 是 true，则为语法错误
- 若 AsyncFunctionBody 包含SuperProperty 是 true，则为语法错误
- 若 FormalParameters 包含SuperCall 是 true，则为语法错误
- 若 AsyncFunctionBody 包含SuperCall 是 true，则为语法错误

### 14.7.2 静态语义：BoundNames <div id="sec-async-function-definitions-static-semantics-BoundNames"></div>

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 the BoundNames of BindingIdentifier

```
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 « "\*default\*" ».

> 注 "\*default\*"在本规范中用作使用导出声明定义的可提升匿名函数的合成名称。.

### 14.7.3 静态语义：ComputedProperty包含<div id="sec-async-function-definitions-static-semantics-ComputedPropertyContains"></div>

带有参数 symbol.

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

1. 返回 the result of ComputedProperty包含for PropertyName with argument symbol.

### 14.7.4 静态语义：包含<div id="sec-async-function-definitions-static-semantics-Contains"></div>

带有参数 symbol.

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 false.

### 14.7.5 静态语义：HasDirectSuper <div id="sec-async-function-definitions-static-semantics-HasDirectSuper"></div>

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

1. 若 UniqueFormalParameters 包含SuperCall 是 true, 返回 true.
2. 返回 AsyncFunctionBody 包含SuperCall.

### 14.7.6 静态语义：HasName <div id="sec-async-function-definitions-static-semantics-HasName"></div>

```
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 false

```
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 true.

### 14.7.7 静态语义：IsConstantDeclaration <div id="sec-async-function-definitions-static-semantics-IsConstantDeclaration"></div>

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 false.

### 14.7.8 静态语义：IsFunctionDefinition <div id="sec-async-function-definitions-static-semantics-IsFunctionDefinition"></div>

```
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 true.

### 14.7.9 静态语义：PropName <div id="sec-async-function-definitions-static-semantics-PropName"></div>

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

1. 返回 PropertyName 的 PropName

### 14.7.10 运行时语义：InstantiateFunctionObject <div id="sec-async-function-definitions-InstantiateFunctionObject"></div>

带有参数 scope.

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. 若 the function code for AsyncFunctionDeclaration is strict mode code, 令 strict 为 true. 否则,，令 strict 为 false.
2. 令 name 为 BindingIdentifier 的 StringValue
3. 令 F 为 ! AsyncFunctionCreate(Normal, FormalParameters, AsyncFunctionBody, scope, strict).
4. 执行 ! SetFunctionName(F, name).
5. 设置 F.[[SourceText]] 为AsyncFunctionDeclaration匹配的源文本.
6. 返回 F

```
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. 若 the function code for AsyncFunctionDeclaration is strict mode code, 令 strict 为 true. 否则,，令 strict 为 false.
2. 令 F 为 ! AsyncFunctionCreate(Normal, FormalParameters, AsyncFunctionBody, scope, strict).
3. 执行 ! SetFunctionName(F, "default").
4. 设置 F.[[SourceText]] 为AsyncFunctionDeclaration匹配的源文本.
5. 返回 F.

### 14.7.11 运行时语义：EvaluateBody <div id="sec-async-function-definitions-EvaluateBody"></div>

带有参数 functionObject 和 argumentsList列表.

```
AsyncFunctionBody : FunctionBody
```

1. 令 promiseCapability 为 ! NewPromiseCapability(%Promise%).
2. 令 declResult 为 FunctionDeclarationInstantiation(functionObject, argumentsList).
3. 若 declResult is not an abrupt completion, then
a. 执行 ! AsyncFunctionStart(promiseCapability, FunctionBody).
4. 否则 declResult is an abrupt completion,
a. 执行 ! Call(promiseCapability.[[Reject]], undefined, « declResult.[[Value]] »).
5. 返回 Completion { [[Type]]: 返回, [[Value]]: promiseCapability.[[Promise]], [[Target]]: empty }.

### 14.7.12 运行时语义：PropertyDefinitionEvaluation <div id="sec-async-function-definitions-PropertyDefinitionEvaluation"></div>

带有参数 object 和 enumerable.

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

1. 令 propKey 为运算 PropertyName 的结果
2. ReturnIfAbrupt(propKey).
3. 若此AsyncMethod的函数代码是严格模式代码, 令 strict 为 true. 否则，令 strict 为 false.
4. 令 scope 为运行时上下文的LexicalEnvironment.
5. 令 closure 为 ! AsyncFunctionCreate(Method, UniqueFormalParameters, AsyncFunctionBody, scope, strict).
6. 执行 ! MakeMethod(closure, object).
7. 执行 ! SetFunctionName(closure, propKey).
8. 设置 closure.[[SourceText]] 为AsyncMethod匹配的源文本.
9. 令 desc 为 the PropertyDescriptor { [[Value]]: closure, [[Writable]]: true, [[Enumerable]]: enumerable,
[[Configurable]]: true }.
10. 返回 ? DefinePropertyOrThrow(object, propKey, desc).

### 14.7.13 运行时语义：NamedEvaluation <div id="sec-async-function-definitions-runtime-semantics-namedevaluation"></div>

带有参数 name.

```
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. 令 closure 为运算 this 的结果AsyncFunctionExpression.
2. 执行 SetFunctionName(closure, name).
3. 返回 closure

### 14.7.14 运行时语义：Evaluation <div id="sec-async-function-definitions-runtime-semantics-evaluation"></div>

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 NormalCompletion(empty) 

```
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. 返回 NormalCompletion(empty).

```
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. 若 the function code for AsyncFunctionExpression is strict mode code, 令 strict 为 true. 否则，令 strict 为
false.
2. 令 scope 为运行时上下文的LexicalEnvironment.
3. 令 closure 为 ! AsyncFunctionCreate(Normal, FormalParameters, AsyncFunctionBody, scope, strict).
4. 设置 closure.[[SourceText]] 为AsyncFunctionExpression匹配的源文本.
5. 返回 closure.

```
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. 若 the function code for AsyncFunctionExpression is strict mode code, 令 strict 为 true. 否则，令 strict 为
false.
2. 令 scope 为运行时上下文的LexicalEnvironment.
3. 令 funcEnv 为 ! NewDeclarativeEnvironment(scope).
4. 令 envRec 为 funcEnv's EnvironmentRecord.
5. 令 name 为 BindingIdentifier 的 StringValue
6. 执行 ! envRec.CreateImmutableBinding(name).
7. 令 closure 为 ! AsyncFunctionCreate(Normal, FormalParameters, AsyncFunctionBody, funcEnv, strict).
8. 执行 ! SetFunctionName(closure, name).
9. 执行 ! envRec.InitializeBinding(name, closure).
10. 设置 closure.[[SourceText]] 为AsyncFunctionExpression匹配的源文本.
11. 返回 closure.

```
AwaitExpression : await UnaryExpression
```

1. 令 exprRef 为运算 UnaryExpression 的结果
2. 令 value 为 ? GetValue(exprRef).
3. 返回 ? Await(value)

## 14.8 Async 箭头函数 <div id="sec-async-arrow-function-definitions"></div>

**语法**

```
AsyncArrowFunction[In, Yield, Await] :
    async [no LineTerminator here] AsyncArrowBindingIdentifier[?Yield] [no LineTerminator here] =>
    AsyncConciseBody[?In]
    CoverCallExpressionAndAsyncArrowHead[?Yield, ?Await] [no LineTerminator here] =>
    AsyncConciseBody[?In]
AsyncConciseBody[In] :
    [lookahead ≠ {] AssignmentExpression[?In, ~Yield, +Await]
    { AsyncFunctionBody }
AsyncArrowBindingIdentifier[Yield] :
	BindingIdentifier[?Yield, +Await]
CoverCallExpressionAndAsyncArrowHead[Yield, Await] :
	MemberExpression[?Yield, ?Await] Arguments[?Yield, ?Await]
```

**补充语法**

When processing an instance of the production AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody the CoverCallExpressionAndAsyncArrowHead 的 interpretation 是 refined using the following grammar:

```
AsyncArrowHead :
	async [no LineTerminator here] ArrowFormalParameters[~Yield, +Await]
```

### 14.8.1 静态语义：Early Errors <div id="sec-async-arrow-function-definitions-static-semantics-early-errors"></div>

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
```

- 若 any element of the BoundNames of AsyncArrowBindingIdentifier also occurs in the AsyncConciseBody 的 LexicallyDeclaredNames，则为语法错误

```
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

- 若 CoverCallExpressionAndAsyncArrowHead 包含YieldExpression 是 true，则为语法错误
- 若 CoverCallExpressionAndAsyncArrowHead 包含AwaitExpression 是 true，则为语法错误
- 若 CoverCallExpressionAndAsyncArrowHead is not covering an AsyncArrowHead，则为语法错误
- 若 any element of the BoundNames of CoverCallExpressionAndAsyncArrowHead also occurs in the AsyncConciseBody 的 LexicallyDeclaredNames，则为语法错误
- 若 AsyncConciseBody 的 ContainsUseStrict 是 true and IsSimpleParameterList of CoverCallExpressionAndAsyncArrowHead is false，则为语法错误
- All Early Error rules for AsyncArrowHead and its derived productions apply to CoverCallExpressionAndAsyncArrowHead 的 CoveredAsyncArrowHead

### 14.8.2 静态语义：CoveredAsyncArrowHead <div id="sec-async-arrow-function-definitions-static-semantics-CoveredAsyncArrowHead"></div>

```
CoverCallExpressionAndAsyncArrowHead : MemberExpression Arguments
```

1. 返回 the AsyncArrowHead that is covered by CoverCallExpressionAndAsyncArrowHead

### 14.8.3 静态语义：BoundNames <div id="sec-async-arrow-function-definitions-static-semantics-BoundNames"></div>

```
CoverCallExpressionAndAsyncArrowHead : MemberExpression Arguments
```

1. 令 head 为 CoverCallExpressionAndAsyncArrowHead 的 CoveredAsyncArrowHead
2. 返回 the head 的 BoundNames

### 14.8.4 静态语义：包含<div id="sec-async-arrow-function-definitions-static-semantics-Contains"></div>

带有参数 symbol.

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
```

1. 若 symbol is not one of NewTarget, SuperProperty, SuperCall, super, or this, 返回 false.
2. 返回 AsyncConciseBody 包含symbol.

```
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

1. 若 symbol is not one of NewTarget, SuperProperty, SuperCall, super, or this, 返回 false.
2. 令 head 为 CoverCallExpressionAndAsyncArrowHead 的 CoveredAsyncArrowHead
3. 若 head 包含symbol 是 true, 返回 true.
4. 返回 AsyncConciseBody 包含symbol.

> 注 Normally, 包含does not look inside most function forms. However, 包含is used to detect new.target, this, and super usage within an AsyncArrowFunction.

### 14.8.5 静态语义：ContainsExpression <div id="sec-async-arrow-function-definitions-static-semantics-ContainsExpression"></div>

```
AsyncArrowBindingIdentifier : BindingIdentifier
```

1. 返回 false.

### 14.8.6 静态语义：ExpectedArgumentCount <div id="sec-async-arrow-function-definitions-static-semantics-ExpectedArgumentCount"></div>

```
AsyncArrowBindingIdentifier : BindingIdentifier
```

1. 返回 1.

### 14.8.7 静态语义：HasName <div id="sec-async-arrow-function-definitions-static-semantics-HasName"></div>

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

1. 返回 false.

### 14.8.8 静态语义：IsSimpleParameterList <div id="sec-async-arrow-function-definitions-static-semantics-IsSimpleParameterList"></div>

```
AsyncArrowBindingIdentifier : BindingIdentifier
```

1. 返回 true.

```
CoverCallExpressionAndAsyncArrowHead : MemberExpression Arguments
```

1. 令 head 为 CoverCallExpressionAndAsyncArrowHead 的 CoveredAsyncArrowHead
2. 返回 head 的 IsSimpleParameterList

### 14.8.9 静态语义：LexicallyDeclaredNames <div id="sec-async-arrow-function-definitions-static-semantics-LexicallyDeclaredNames"></div>

```
AsyncConciseBody : AssignmentExpression
```

1. 返回一个新的空列表.

### 14.8.10 静态语义：LexicallyScopedDeclarations <div id="sec-async-arrow-function-definitions-static-semantics-LexicallyScopedDeclarations"></div>

```
AsyncConciseBody : AssignmentExpression
```

1. 返回一个新的空列表.

### 14.8.11 静态语义：VarDeclaredNames <div id="sec-async-arrow-function-definitions-static-semantics-VarDeclaredNames"></div>

```
AsyncConciseBody : AssignmentExpression
```

1. 返回一个新的空列表.

### 14.8.12 静态语义：VarScopedDeclarations <div id="sec-async-arrow-function-definitions-static-semantics-VarScopedDeclarations"></div>

```
AsyncConciseBody : AssignmentExpression
```

1. 返回一个新的空列表.

### 14.8.13 运行时语义：IteratorBindingInitialization <div id="sec-async-arrow-function-definitions-IteratorBindingInitialization"></div>

带有参数 iteratorRecord 和 environment.

```
AsyncArrowBindingIdentifier : BindingIdentifier
```

1. 断言: iteratorRecord.[[Done]] is false.
2. 令 next 为 IteratorStep(iteratorRecord).
3. 若 next is an abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
4. ReturnIfAbrupt(next).
5. 若 next is false, 设置 iteratorRecord.[[Done]] 为 true.
6. 否则,
a. 令 v 为 IteratorValue(next).
b. 若 v is an abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
c. ReturnIfAbrupt(v).
7. 若 iteratorRecord.[[Done]] 是 true, 令 v 为 undefined.
8. 返回使用v和environment作为参数对BindingIdentifier执行BindingInitialization的结果。

### 14.8.14 运行时语义：EvaluateBody <div id="sec-async-arrow-function-definitions-EvaluateBody"></div>

带有参数 functionObject 和 argumentsList列表.

```
AsyncConciseBody : AssignmentExpression
```

1. 令 promiseCapability 为 ! NewPromiseCapability(%Promise%).
2. 令 declResult 为 FunctionDeclarationInstantiation(functionObject, argumentsList).
3. 若 declResult is not an abrupt completion, then
a. 执行 ! AsyncFunctionStart(promiseCapability, AssignmentExpression).
4. 否则 declResult is an abrupt completion,
a. 执行 ! Call(promiseCapability.[[Reject]], undefined, « declResult.[[Value]] »).
5. 返回 Completion { [[Type]]: 返回, [[Value]]: promiseCapability.[[Promise]], [[Target]]: empty }.

```
AsyncConciseBody : { AsyncFunctionBody }
```

1. 返回 the result of EvaluateBody of AsyncFunctionBody passing functionObject and argumentsList as the arguments

### 14.8.15 运行时语义：NamedEvaluation <div id="sec-async-arrow-function-definitions-runtime-semantics-namedevaluation"></div>

带有参数 name.

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

1. 令 closure 为运算 this 的结果AsyncArrowFunction.
2. 执行 SetFunctionName(closure, name).
3. 返回 closure.

### 14.8.16 运行时语义：Evaluation <div id="sec-async-arrow-function-definitions-runtime-semantics-evaluation"></div>

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
```

1. 若此AsyncArrowFunction的函数代码是严格模式代码, 令 strict 为 true. 否则,，令 strict 为
false.
2. 令 scope 为运行时上下文的LexicalEnvironment.
3. 令 parameters 为 AsyncArrowBindingIdentifier.
4. 令 closure 为 ! AsyncFunctionCreate(Arrow, parameters, AsyncConciseBody, scope, strict).
5. 返回 closure.

```
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

1. 若此AsyncArrowFunction的函数代码是严格模式代码, 令 strict 为 true. 否则,，令 strict 为
false.
2. 令 scope 为运行时上下文的LexicalEnvironment.
3. 令 head 为 CoverCallExpressionAndAsyncArrowHead 的 CoveredAsyncArrowHead
4. 令 parameters 为 the head 的 ArrowFormalParameters
5. 令 closure 为 ! AsyncFunctionCreate(Arrow, parameters, AsyncConciseBody, scope, strict).
6. 返回 closure.

## 14.9 尾调用 <div id="sec-tail-position-calls"></div>

### 14.9.1 静态语义：IsInTailPosition ( call ) <div id="sec-isintailposition"></div>

The abstract operation IsInTailPosition with argument call performs the following steps:

1. 断言: call is a Parse Node.
2. 若 the source code matching call is non-strict code, 返回 false.
3. 若 call is not contained within a FunctionBody, ConciseBody, or AsyncConciseBody, 返回 false.
4. 令 body 为 the FunctionBody, ConciseBody, or AsyncConciseBody that most closely 包含call.
5. 若 body is the FunctionBody of a GeneratorBody, 返回 false.
6. 若 body is the FunctionBody of an AsyncFunctionBody, 返回 false.
7. 若 body is the FunctionBody of an AsyncGeneratorBody, 返回 false.
8. 若 body is an AsyncConciseBody, 返回 false.
9. 返回 the result of HasCallInTailPosition of body with argument call.

> 注 Tail Position calls are only defined in strict mode code because of a common non-standard language extension (see 9.2.9) that enables observation of the chain of caller contexts.

### 14.9.2 静态语义：HasCallInTailPosition <div id="sec-static-semantics-hascallintailposition"></div>

带有参数 call.

> 注 call is a Parse Node that represents a specific range of source text. When the following algorithms compare call to another Parse Node, it is a test of whether they represent the same source text.

#### 14.9.2.1 Statement Rules <div id="sec-statement-rules"></div>

```
ConciseBody : AssignmentExpression
```

1. 返回 HasCallInTailPosition of AssignmentExpression with argument call.

```
StatementList : StatementList StatementListItem
```

1. 令 has 为 HasCallInTailPosition of StatementList with argument call.
2. 若 has 是 true, 返回 true.
3. 返回 HasCallInTailPosition of StatementListItem with argument call.

```
FunctionStatementList : [empty]
StatementListItem : Declaration
    Statement :
        VariableStatement
        EmptyStatement
        ExpressionStatement
        ContinueStatement
        BreakStatement
        ThrowStatement
        DebuggerStatement
Block : { }
ReturnStatement : 返回 ;
LabelledItem : FunctionDeclaration
IterationStatement :
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for ( var ForBinding of AssignmentExpression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
CaseBlock : { }
```

1. 返回 false.

```
IfStatement : 若 ( Expression ) Statement 否则 Statement
```

1. 令 has 为 HasCallInTailPosition of the first Statement with argument call.
2. 若 has 是 true, 返回 true.
3. 返回 HasCallInTailPosition of the second Statement with argument call.

```
IfStatement : 若 ( Expression ) Statement
    IterationStatement :
        do Statement while ( Expression ) ;
        while ( Expression ) Statement
        for ( Expressionopt ; Expressionopt ; Expressionopt ) Statement
        for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
        for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement
        for ( LeftHandSideExpression in Expression ) Statement
        for ( var ForBinding in Expression ) Statement
    for ( ForDeclaration in Expression ) Statement
```

```
WithStatement : with ( Expression ) Statement
```

1. 返回 HasCallInTailPosition of Statement with argument call

   ```
   LabelledStatement :
   LabelIdentifier : LabelledItem
   ```

   1. 返回 HasCallInTailPosition of LabelledItem with argument call

```
ReturnStatement : 返回 Expression ;
```

1. 返回 HasCallInTailPosition of Expression with argument call.

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. 返回 HasCallInTailPosition of CaseBlock with argument call

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 令 has 为 false.
2. 若 the first CaseClauses is present, 令 has 为 HasCallInTailPosition of the first CaseClauses with argument call.
3. 若 has 是 true, 返回 true.
4. 令 has 为 HasCallInTailPosition of the DefaultClause with argument call.
5. 若 has 是 true, 返回 true.
6. 若 the second CaseClauses is present, 令 has 为 HasCallInTailPosition of the second CaseClauses with argument call.
7. 返回 has.

```
CaseClauses : CaseClauses CaseClause
```

1. 令 has 为 HasCallInTailPosition of CaseClauses with argument call.
2. 若 has 是 true, 返回 true.
3. 返回 HasCallInTailPosition of CaseClause with argument call.

```
CaseClause : case Expression : StatementList
DefaultClause : default : StatementList
```

1. 若 StatementList is present, 返回 HasCallInTailPosition of StatementList with argument call.
2. 返回 false.

```
TryStatement : try Block Catch
```

1. 返回 HasCallInTailPosition of Catch with argument call.

```
TryStatement : try Block Finally
TryStatement : try Block Catch Finally
```

1. 返回 HasCallInTailPosition of Finally with argument call

```
Catch : catch ( CatchParameter ) Block
```

1. 返回 HasCallInTailPosition of Block with argument call.

#### 14.9.2.2 Expression Rules <div id="sec-expression-rules"></div>

> 注 A potential tail position call that is immediately followed by 返回 GetValue of the call result is also a possible tail position call. Function calls cannot 返回 reference values, so such a GetValue operation will always 返回 the same value as the actual function call result.

```
AssignmentExpression :
    YieldExpression
    ArrowFunction
    AsyncArrowFunction
    LeftHandSideExpression = AssignmentExpression
    LeftHandSideExpression AssignmentOperator AssignmentExpression
BitwiseANDExpression : BitwiseANDExpression & EqualityExpression
BitwiseXORExpression : BitwiseXORExpression ^ BitwiseANDExpression
BitwiseORExpression : BitwiseORExpression | BitwiseXORExpression
    EqualityExpression :
        EqualityExpression == RelationalExpression
        EqualityExpression != RelationalExpression
        EqualityExpression === RelationalExpression
        EqualityExpression !== RelationalExpression
    RelationalExpression :
        RelationalExpression < ShiftExpression
        RelationalExpression > ShiftExpression
        RelationalExpression <= ShiftExpression
        RelationalExpression >= ShiftExpression
        RelationalExpression instanceof ShiftExpression
        RelationalExpression in ShiftExpression
    ShiftExpression :
        ShiftExpression << AdditiveExpression
        ShiftExpression >> AdditiveExpression
        ShiftExpression >>> AdditiveExpression
    AdditiveExpression :
        AdditiveExpression + MultiplicativeExpression
        AdditiveExpression - MultiplicativeExpression
    MultiplicativeExpression :
    	MultiplicativeExpression MultiplicativeOperator ExponentiationExpression
    ExponentiationExpression :
    	UpdateExpression ** ExponentiationExpression
    UpdateExpression :
        LeftHandSideExpression ++
        LeftHandSideExpression --
        ++ UnaryExpression
        -- UnaryExpression
    UnaryExpression :
        delete UnaryExpression
        void UnaryExpression
        typeof UnaryExpression
        + UnaryExpression
        - UnaryExpression
        ~ UnaryExpression
        ! UnaryExpression
        AwaitExpression
    CallExpression :
    	SuperCall
        CallExpression [ Expression ]
        CallExpression . IdentifierName
NewExpression : new NewExpression
    MemberExpression :
    	MemberExpression [ Expression ]
        MemberExpression . IdentifierName
        SuperProperty
        MetaProperty
        new MemberExpression Arguments
    PrimaryExpression :
        this
        IdentifierReference
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

1. 返回 false.

   ```
   Expression :
   AssignmentExpression
   Expression , AssignmentExpression
   ```

   1. 返回 HasCallInTailPosition of AssignmentExpression with argument call.

```
ConditionalExpression : LogicalORExpression ? AssignmentExpression : AssignmentExpression
```

1. 令 has 为 HasCallInTailPosition of the first AssignmentExpression with argument call.
2. 若 has 是 true, 返回 true.
3. 返回 HasCallInTailPosition of the second AssignmentExpression with argument call.

```
LogicalANDExpression : LogicalANDExpression && BitwiseORExpression
```

1. 返回 HasCallInTailPosition of BitwiseORExpression with argument call.

```
LogicalORExpression : LogicalORExpression || LogicalANDExpression
```

1. 返回 HasCallInTailPosition of LogicalANDExpression with argument call.

   ```
   CallExpression :
       CoverCallExpressionAndAsyncArrowHead
       CallExpression Arguments
       CallExpression TemplateLiteral
   ```

   1. 若 this CallExpression is call, 返回 true.
   2. 返回 false.

   ```
   MemberExpression :
   	MemberExpression TemplateLiteral
   ```

   1. 若 this MemberExpression is call, 返回 true.
   2. 返回 false.

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. 令 expr 为 CoverParenthesizedExpressionAndArrowParameterList 的 CoveredParenthesizedExpression

2. 返回 HasCallInTailPosition of expr with argument call.

   ```
   ParenthesizedExpression :
   ( Expression )
   ```

   1. 返回 HasCallInTailPosition of Expression with argument call

### 14.9.3 运行时语义：PrepareForTailCall ( ) <div id="sec-preparefortailcall"></div>

The abstract operation PrepareForTailCall performs the following steps:

1. 令 leafContext 为 运行时上下文.
2. Suspend leafContext.
3. Pop leafContext from the execution context stack. The execution context now on the top of the stack becomes the
running execution context.
4. 断言: leafContext has no further use. It will never be activated as 运行时上下文.