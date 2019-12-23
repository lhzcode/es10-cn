14 ECMAScript语言：函数和类

> NOTE
>
> Various ECMAScript language elements cause the creation of ECMAScript function objects (9.2). Evaluation of such functions starts with the execution of their [[Call]] internal method (9.2.1).

## 14.1 函数定义 <div id="sec-function-definitions"></div>

**Syntax**

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
	StatementList[?Yield, ?Await, +Return] opt
```



### 14.1.1 指令序言和严格使用指令 <div id="sec-directive-prologues-and-the-use-strict-directive"></div>

A Directive Prologue is the longest sequence of ExpressionStatements occurring as the initial StatementListItems or ModuleItems of a FunctionBody, a ScriptBody, or a ModuleBody and where each ExpressionStatement in the sequence consists entirely of a StringLiteral token followed by a semicolon. The semicolon may appear explicitly or may be inserted by automatic semicolon insertion. A Directive Prologue may be an empty sequence.

A Use Strict Directive is an ExpressionStatement in a Directive Prologue whose StringLiteral is either the exact code unit sequences "use strict" or 'use strict'. A Use Strict Directive may not contain an EscapeSequence or LineContinuation.

A Directive Prologue may contain more than one Use Strict Directive. However, an implementation may issue a warning if this occurs.

> NOTE
>
> The ExpressionStatements of a Directive Prologue are evaluated normally during evaluation of the containing production. Implementations may define implementation specific meanings for ExpressionStatements which are not a Use Strict Directive and which occur in a Directive Prologue. If an appropriate notification mechanism exists, an implementation should issue a warning if it encounters in a Directive Prologue an ExpressionStatement that is not a Use Strict Directive and which does not have a meaning defined by the implementation.

### 14.1.2 SS: Early Errors <div id="sec-function-definitions-static-semantics-early-errors"></div>

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

- If the source code matching this production is strict mode code, the Early Error rules for UniqueFormalParameters : FormalParameters are applied.
- If the source code matching this production is strict mode code, it is a Syntax Error if BindingIdentifier is present and the StringValue of BindingIdentifier is "eval" or "arguments".
- It is a Syntax Error if ContainsUseStrict of FunctionBody is true and IsSimpleParameterList of FormalParameters is false.
- It is a Syntax Error if any element of the BoundNames of FormalParameters also occurs in the
  LexicallyDeclaredNames of FunctionBody.
- It is a Syntax Error if FormalParameters Contains SuperProperty is true.
- It is a Syntax Error if FunctionBody Contains SuperProperty is true.
- It is a Syntax Error if FormalParameters Contains SuperCall is true.
- It is a Syntax Error if FunctionBody Contains SuperCall is true.

> NOTE 1
>
> The LexicallyDeclaredNames of a FunctionBody does not include identifiers bound using var or function declarations

```
UniqueFormalParameters : FormalParameters
```

It is a Syntax Error if BoundNames of FormalParameters contains any duplicate elements.

```
FormalParameters : FormalParameterList
```

It is a Syntax Error if IsSimpleParameterList of FormalParameterList is false and BoundNames of FormalParameterList contains any duplicate elements.

> NOTE 2
>
> Multiple occurrences of the same BindingIdentifier in a FormalParameterList is only allowed for functions which have simple parameter lists and which are not defined in strict mode code.

```
FunctionBody : FunctionStatementList
```

- It is a Syntax Error if the LexicallyDeclaredNames of FunctionStatementList contains any duplicate entries.
- It is a Syntax Error if any element of the LexicallyDeclaredNames of FunctionStatementList also occurs in the VarDeclaredNames of FunctionStatementList. 
- It is a Syntax Error if ContainsDuplicateLabels of FunctionStatementList with argument « » is true.
- It is a Syntax Error if ContainsUndefinedBreakTarget of FunctionStatementList with argument « » is true.
- It is a Syntax Error if ContainsUndefinedContinueTarget of FunctionStatementList with arguments « » and « » is true.

### 14.1.3 SS: BoundNames <div id="sec-function-definitions-static-semantics-boundnames"></div>

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. Return the BoundNames of BindingIdentifier.

```
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
```

1. Return « "\*default\*" ».

> NOTE
>
> "\*default\*" is used within this specification as a synthetic name for hoistable anonymous functions that are defined using export declarations.

```
FormalParameters : [empty]
```

1. Return a new empty List

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. Let names be BoundNames of FormalParameterList.
2. Append to names the BoundNames of FunctionRestParameter.
3. Return names.

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. Let names be BoundNames of FormalParameterList.
2. Append to names the BoundNames of FormalParameter.
3. Return names.

### 14.1.4 SS: Contains <div id="sec-function-definitions-static-semantics-contains"></div>

With parameter symbol.

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. Return false.

> NOTE
>
> Static semantic rules that depend upon substructure generally do not look into function definitions.

### 14.1.5 SS: ContainsExpression <div id="sec-function-definitions-static-semantics-containsexpression"></div>

```
FormalParameters : [empty]
```

1. Return false.

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. If ContainsExpression of FormalParameterList is true, return true.
2. Return ContainsExpression of FunctionRestParameter.

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. If ContainsExpression of FormalParameterList is true, return true.
2. Return ContainsExpression of FormalParameter

### 14.1.6 SS: ContainsUseStrict <div id="sec-function-definitions-static-semantics-containsusestrict"></div>

```
FunctionBody : FunctionStatementList
```

1. If the Directive Prologue of FunctionStatementList contains a Use Strict Directive, return true; otherwise, return false.

### 14.1.7 SS: ExpectedArgumentCount <div id="sec-function-definitions-static-semantics-expectedargumentcount"></div>

```
FormalParameters : [empty]
```

1. Return 0.

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. Return ExpectedArgumentCount of FormalParameterList.

> NOTE
>
> The ExpectedArgumentCount of a FormalParameterList is the number of FormalParameters to the left of either the rest parameter or the first FormalParameter with an Initializer. A FormalParameter without an initializer is allowed after the first parameter with an initializer but such parameters are considered to be optional with undefined as their default value

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. Let count be ExpectedArgumentCount of FormalParameterList.
2. If HasInitializer of FormalParameterList is true or HasInitializer of FormalParameter is true, return count.
3. Return count + 1.

### 14.1.8 SS: HasInitializer <div id="sec-function-definitions-static-semantics-hasinitializer"></div>

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. If HasInitializer of FormalParameterList is true, return true.
2. Return HasInitializer of FormalParameter.

### 14.1.9 SS: HasName <div id="sec-function-definitions-static-semantics-hasname"></div>

```
FunctionExpression : function ( FormalParameters ) { FunctionBody }
```

1. Return false.

```
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. Return true.

### 14.1.10 SS: IsAnonymousFunctionDefinition ( expr ) <div id="sec-isanonymousfunctiondefinition"></div>

The abstract operation IsAnonymousFunctionDefinition determines if its argument is a function definition that does not bind a name. The argument expr is the result of parsing an AssignmentExpression or Initializer. The following steps are taken:

1. If IsFunctionDefinition of expr is false, return false.
2. Let hasName be the result of HasName of expr.
3. If hasName is true, return false.
4. Return true.

### 14.1.11 SS: IsConstantDeclaration <div id="sec-function-definitions-static-semantics-isconstantdeclaration"></div>

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
```

1. Return false.

### 14.1.12 SS: IsFunctionDefinition <div id="sec-function-definitions-static-semantics-isfunctiondefinition"></div>

```
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. Return true.

### 14.1.13 SS: IsSimpleParameterList <div id="sec-function-definitions-static-semantics-issimpleparameterlist"></div>

```
FormalParameters : [empty]
```

1. Return true.

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. Return false

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. If IsSimpleParameterList of FormalParameterList is false, return false.
2. Return IsSimpleParameterList of FormalParameter.

```
FormalParameter : BindingElement
```

1. Return IsSimpleParameterList of BindingElement.

### 14.1.14 SS: LexicallyDeclaredNames <div id="sec-function-definitions-static-semantics-lexicallydeclarednames"></div>

```
FunctionStatementList : [empty]
```

1. Return a new empty List

```
FunctionStatementList : StatementList
```

1. Return TopLevelLexicallyDeclaredNames of StatementList

### 14.1.15 SS: LexicallyScopedDeclarations <div id="sec-function-definitions-static-semantics-lexicallyscopeddeclarations"></div>

```
FunctionStatementList : [empty]
```

1. Return a new empty List.

```
FunctionStatementList : StatementList
```

1. Return the TopLevelLexicallyScopedDeclarations of StatementList.

### 14.1.16 SS: VarDeclaredNames <div id="sec-function-definitions-static-semantics-vardeclarednames"></div>

```
FunctionStatementList : [empty]
```

1. Return a new empty List.

```
FunctionStatementList : StatementList
```

1. Return TopLevelVarDeclaredNames of StatementList.

### 14.1.17 SS: VarScopedDeclarations <div id="sec-function-definitions-static-semantics-varscopeddeclarations"></div>

```
FunctionStatementList : [empty]
```

1. Return a new empty List.

```
FunctionStatementList : StatementList
```

1. Return the TopLevelVarScopedDeclarations of StatementList.

### 14.1.18 RS: EvaluateBody <div id="sec-function-definitions-runtime-semantics-evaluatebody"></div>

With parameters functionObject and List argumentsList.

```
FunctionBody : FunctionStatementList
```

1. Perform ? FunctionDeclarationInstantiation(functionObject, argumentsList).
2. Return the result of evaluating FunctionStatementList.

### 14.1.19 RS: IteratorBindingInitialization <div id="sec-function-definitions-runtime-semantics-iteratorbindinginitialization"></div>

```
With parameters iteratorRecord and environment.
```

> NOTE 1
>
> When undefined is passed for environment it indicates that a PutValue operation should be used to assign the initialization value. This is the case for formal parameter lists of non-strict functions. In that case the formal parameter bindings are preinitialized in order to deal with the possibility of multiple parameters with the same name.

```
FormalParameters : [empty]
```

1. Return NormalCompletion(empty).

```
FormalParameters : FormalParameterList , FunctionRestParameter
```

1. Perform ? IteratorBindingInitialization for FormalParameterList using iteratorRecord and environment as the arguments.
2. Return the result of performing IteratorBindingInitialization for FunctionRestParameter using iteratorRecord and environment as the arguments.

```
FormalParameterList : FormalParameterList , FormalParameter
```

1. Perform ? IteratorBindingInitialization for FormalParameterList using iteratorRecord and environment as the arguments.
2. Return the result of performing IteratorBindingInitialization for FormalParameter using iteratorRecord and environment as the arguments.

```
FormalParameter : BindingElement
```

1. If ContainsExpression of BindingElement is false, return the result of performing IteratorBindingInitialization for BindingElement using iteratorRecord and environment as the arguments.
2. Let currentContext be the running execution context.
3. Let originalEnv be the VariableEnvironment of currentContext.
4. Assert: The VariableEnvironment and LexicalEnvironment of currentContext are the same.
5. Assert: environment and originalEnv are the same.
6. Let paramVarEnv be NewDeclarativeEnvironment(originalEnv).
7. Set the VariableEnvironment of currentContext to paramVarEnv.
8. Set the LexicalEnvironment of currentContext to paramVarEnv.
9. Let result be the result of performing IteratorBindingInitialization for BindingElement using iteratorRecord and environment as the arguments.
10. Set the VariableEnvironment of currentContext to originalEnv.
11. Set the LexicalEnvironment of currentContext to originalEnv.
12. Return result.

> NOTE 2
>
> The new Environment Record created in step 6 is only used if the BindingElement contains a direct eval.

```
FunctionRestParameter : BindingRestElement
```

1. If ContainsExpression of BindingRestElement is false, return the result of performing IteratorBindingInitialization for BindingRestElement using iteratorRecord and environment as the arguments.
2. Let currentContext be the running execution context.
3. Let originalEnv be the VariableEnvironment of currentContext.
4. Assert: The VariableEnvironment and LexicalEnvironment of currentContext are the same.
5. Assert: environment and originalEnv are the same.
6. Let paramVarEnv be NewDeclarativeEnvironment(originalEnv).
7. Set the VariableEnvironment of currentContext to paramVarEnv.
8. Set the LexicalEnvironment of currentContext to paramVarEnv.
9. Let result be the result of performing IteratorBindingInitialization for BindingRestElement using iteratorRecord and environment as the arguments.
10. Set the VariableEnvironment of currentContext to originalEnv.
11. Set the LexicalEnvironment of currentContext to originalEnv.
12. Return result.

> NOTE 3
>
> The new Environment Record created in step 6 is only used if the BindingRestElement contains a direct eval.

### 14.1.20 RS: InstantiateFunctionObject <div id="sec-function-definitions-runtime-semantics-instantiatefunctionobject"></div>

With parameter scope.

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. If the function code for FunctionDeclaration is strict mode code, let strict be true. Otherwise let strict be false.
2. Let name be StringValue of BindingIdentifier.
3. Let F be FunctionCreate(Normal, FormalParameters, FunctionBody, scope, strict).
4. Perform MakeConstructor(F).
5. Perform SetFunctionName(F, name).
6. Set F.[[SourceText]] to the source text matched by FunctionDeclaration.
7. Return F.

```
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
```

1. Let F be FunctionCreate(Normal, FormalParameters, FunctionBody, scope, true).
2. Perform MakeConstructor(F).
3. Perform SetFunctionName(F, "default").
4. Set F.[[SourceText]] to the source text matched by FunctionDeclaration.
5. Return F

> NOTE
>
> An anonymous FunctionDeclaration can only occur as part of an export default declaration, and its function code is therefore always strict mode code.

### 14.1.21 RS: NamedEvaluation <div id="sec-function-definitions-runtime-semantics-namedevaluation"></div>

With parameter name.

```
FunctionExpression : function ( FormalParameters ) { FunctionBody }
```

1. Let closure be the result of evaluating this FunctionExpression.
2. Perform SetFunctionName(closure, name).
3. Return closure.

### 14.1.22 RS: Evaluation <div id="sec-function-definitions-runtime-semantics-evaluation"></div>

```
FunctionDeclaration : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. Return NormalCompletion(empty).

> NOTE 1
> An alternative semantics is provided in B.3.3

```
FunctionDeclaration : function ( FormalParameters ) { FunctionBody }
```

1. Return NormalCompletion(empty).

```
FunctionExpression : function ( FormalParameters ) { FunctionBody }
```

1. If the function code for FunctionExpression is strict mode code, let strict be true. Otherwise let strict be false.
2. Let scope be the LexicalEnvironment of the running execution context.
3. Let closure be FunctionCreate(Normal, FormalParameters, FunctionBody, scope, strict).
4. Perform MakeConstructor(closure).
5. Set closure.[[SourceText]] to the source text matched by FunctionExpression.
6. Return closure.

```
FunctionExpression : function BindingIdentifier ( FormalParameters ) { FunctionBody }
```

1. If the function code for FunctionExpression is strict mode code, let strict be true. Otherwise let strict be false.
2. Let scope be the running execution context's LexicalEnvironment.
3. Let funcEnv be NewDeclarativeEnvironment(scope).
4. Let envRec be funcEnv's EnvironmentRecord.
5. Let name be StringValue of BindingIdentifier.
6. Perform envRec.CreateImmutableBinding(name, false).
7. Let closure be FunctionCreate(Normal, FormalParameters, FunctionBody, funcEnv, strict).
8. Perform MakeConstructor(closure).
9. Perform SetFunctionName(closure, name).
10. Set closure.[[SourceText]] to the source text matched by FunctionExpression.
11. Perform envRec.InitializeBinding(name, closure).
12. Return closure

> NOTE 2 The BindingIdentifier in a FunctionExpression can be referenced from inside the FunctionExpression's FunctionBody to allow the function to call itself recursively. However, unlike in a FunctionDeclaration, the BindingIdentifier in a FunctionExpression cannot be referenced from and does not affect the scope enclosing the FunctionExpression.

> NOTE 3 A prototype property is automatically created for every function defined using a FunctionDeclaration or FunctionExpression, to allow for the possibility that the function will be used as a constructor.

```
FunctionStatementList : [empty]
```

1. Return NormalCompletion(undefined).

## 14.2 箭头函数定义 <div id="sec-arrow-function-definitions"></div>

**Syntax**

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

**Supplemental Syntax**

When the production 

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

is recognized the following grammar is used to refine the interpretation of 

```
CoverParenthesizedExpressionAndArrowParameterList:
```

```
ArrowFormalParameters[Yield]:
(StrictFormalParameters[?Yield])
```

### 14.2.1 SS: Early Errors <div id="sec-arrow-function-definitions-static-semantics-early-errors"></div>

```
ArrowFunction : ArrowParameters => ConciseBody
```

- It is a Syntax Error if ArrowParameters Contains YieldExpression is true.
- It is a Syntax Error if ArrowParameters Contains AwaitExpression is true.
- It is a Syntax Error if ContainsUseStrict of ConciseBody is true and IsSimpleParameterList of ArrowParameters is false.
- It is a Syntax Error if any element of the BoundNames of ArrowParameters also occurs in the
  LexicallyDeclaredNames of ConciseBody.

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

- It is a Syntax Error if CoverParenthesizedExpressionAndArrowParameterList is not covering an
  ArrowFormalParameters.
- All early error rules for ArrowFormalParameters and its derived productions also apply to CoveredFormalsList of CoverParenthesizedExpressionAndArrowParameterList

### 14.2.2 SS: BoundNames <div id="sec-arrow-function-definitions-static-semantics-boundnames"></div>

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let formals be CoveredFormalsList of CoverParenthesizedExpressionAndArrowParameterList.
2. Return the BoundNames of formals.

### 14.2.3 SS: Contains <div id="sec-arrow-function-definitions-static-semantics-contains"></div>

With parameter symbol

```
ArrowFunction : ArrowParameters => ConciseBody
```

1. If symbol is not one of NewTarget, SuperProperty, SuperCall, super or this, return false.
2. If ArrowParameters Contains symbol is true, return true.
3. Return ConciseBody Contains symbol.

> NOTE Normally, Contains does not look inside most function forms. However, Contains is used to detect new.target, this, and super usage within an ArrowFunction.

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let formals be CoveredFormalsList of CoverParenthesizedExpressionAndArrowParameterList.
2. Return formals Contains symbol.

### 14.2.4 SS: ContainsExpression <div id="sec-arrow-function-definitions-static-semantics-containsexpression"></div>

```
ArrowParameters : BindingIdentifier
```

1. Return false.

### 14.2.5 SS: ContainsUseStrict <div id="sec-arrow-function-definitions-static-semantics-containsusestrict"></div>
```
ConciseBody : AssignmentExpression
```

1. Return false.

### 14.2.6 SS: ExpectedArgumentCount <div id="sec-arrow-function-definitions-static-semantics-expectedargumentcount"></div>

```
ArrowParameters : BindingIdentifier
```

1. Return 1.

### 14.2.7 SS: HasName <div id="sec-arrow-function-definitions-static-semantics-hasname"></div>

```
ArrowFunction : ArrowParameters => ConciseBody
```

1. Return false.

### 14.2.8 SS: IsSimpleParameterList <div id="sec-arrow-function-definitions-static-semantics-issimpleparameterlist"></div>

```
ArrowParameters : BindingIdentifier
```

1. Return true.

```
ArrowParameters : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let formals be CoveredFormalsList of CoverParenthesizedExpressionAndArrowParameterList.
2. Return IsSimpleParameterList of formals.

### 14.2.9 SS: CoveredFormalsList <div id="sec-static-semantics-coveredformalslist"></div>

```
ArrowParameters : BindingIdentifier
```

1. Return this ArrowParameters.

   ```
   CoverParenthesizedExpressionAndArrowParameterList :
       ( Expression )
       ( )
       ( ... BindingIdentifier )
       ( ... BindingPattern )
       ( Expression , ... BindingIdentifier )
       ( Expression , ... BindingPattern )
   
   ```

   1. Return the ArrowFormalParameters that is covered by CoverParenthesizedExpressionAndArrowParameterList.



### 14.2.10 SS: LexicallyDeclaredNames <div id="sec-arrow-function-definitions-static-semantics-lexicallydeclarednames"></div>

```
ConciseBody : AssignmentExpression
```

1. Return a new empty List.

### 14.2.11 SS: LexicallyScopedDeclarations <div id="sec-arrow-function-definitions-static-semantics-lexicallyscopeddeclarations"></div>

```
ConciseBody : AssignmentExpression
```

1. Return a new empty List.

### 14.2.12 SS: VarDeclaredNames <div id="sec-arrow-function-definitions-static-semantics-vardeclarednames"></div>

```
ConciseBody : AssignmentExpression
```

1. Return a new empty List.

### 14.2.13 SS: VarScopedDeclarations <div id="sec-arrow-function-definitions-static-semantics-varscopeddeclarations"></div>

```
ConciseBody : AssignmentExpression
```

1. Return a new empty List.

### 14.2.14 RS: IteratorBindingInitialization <div id="sec-arrow-function-definitions-runtime-semantics-iteratorbindinginitialization"></div>

With parameters iteratorRecord and environment.

> NOTE
>
> When undefined is passed for environment it indicates that a PutValue operation should be used to assign the initialization value. This is the case for formal parameter lists of non-strict functions. In that case the formal parameter bindings are preinitialized in order to deal with the possibility of multiple parameters with the same name.

```
ArrowParameters : BindingIdentifier
```

1. Assert: iteratorRecord.[[Done]] is false.
2. Let next be IteratorStep(iteratorRecord).
3. If next is an abrupt completion, set iteratorRecord.[[Done]] to true.
4. ReturnIfAbrupt(next).
5. If next is false, set iteratorRecord.[[Done]] to true.
6. Else,
a. Let v be IteratorValue(next).
b. If v is an abrupt completion, set iteratorRecord.[[Done]] to true.
c. ReturnIfAbrupt(v).
7. If iteratorRecord.[[Done]] is true, let v be undefined.
8. Return the result of performing BindingInitialization for BindingIdentifier using v and environment as the arguments.

### 14.2.15 RS: EvaluateBody <div id="sec-arrow-function-definitions-runtime-semantics-evaluatebody"></div>

With parameters functionObject and List argumentsList.

```
ConciseBody : AssignmentExpression
```

1. Perform ? FunctionDeclarationInstantiation(functionObject, argumentsList).
2. Let exprRef be the result of evaluating AssignmentExpression.
3. Let exprValue be ? GetValue(exprRef).
4. Return Completion { [[Type]]: return, [[Value]]: exprValue, [[Target]]: empty }.

### 14.2.16 RS: NamedEvaluation <div id="sec-arrow-function-definitions-runtime-semantics-namedevaluation"></div>

With parameter name.

```
ArrowFunction : ArrowParameters => ConciseBody
```

1. Let closure be the result of evaluating this ArrowFunction.
2. Perform SetFunctionName(closure, name).
3. Return closure.

### 14.2.17 RS: Evaluation <div id="sec-arrow-function-definitions-runtime-semantics-evaluation"></div>

```
ArrowFunction : ArrowParameters => ConciseBody
```

1. If the function code for this ArrowFunction is strict mode code, let strict be true. Otherwise let strict be false.
2. Let scope be the LexicalEnvironment of the running execution context.
3. Let parameters be CoveredFormalsList of ArrowParameters.
4. Let closure be FunctionCreate(Arrow, parameters, ConciseBody, scope, strict).
5. Set closure.[[SourceText]] to the source text matched by ArrowFunction.
6. Return closure.

> NOTE An ArrowFunction does not define local bindings for arguments, super, this, or new.target. Any reference to arguments, super, this, or new.target within an ArrowFunction must resolve to a binding in a lexically enclosing environment. Typically this will be the Function Environment of an immediately enclosing function. Even though an ArrowFunction may contain references to super, the function object created in step 4 is not made into a method by performing MakeMethod. An ArrowFunction that references super is always contained within a nonArrowFunction and the necessary state to implement super is accessible via the scope that is captured by the function object of the ArrowFunction.

## 14.3 方法定义 <div id="sec-method-definitions"></div>

**Syntax**

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

### 14.3.1 SS: Early Errors <div id="sec-method-definitions-static-semantics-early-errors"></div>

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

- It is a Syntax Error if ContainsUseStrict of FunctionBody is true and IsSimpleParameterList of
  UniqueFormalParameters is false.
- It is a Syntax Error if any element of the BoundNames of UniqueFormalParameters also occurs in the
  LexicallyDeclaredNames of FunctionBody

```
MethodDefinition : set PropertyName ( PropertySetParameterList ) { FunctionBody }
```

- It is a Syntax Error if BoundNames of PropertySetParameterList contains any duplicate elements.
- It is a Syntax Error if ContainsUseStrict of FunctionBody is true and IsSimpleParameterList of
  PropertySetParameterList is false.
- It is a Syntax Error if any element of the BoundNames of PropertySetParameterList also occurs in the
  LexicallyDeclaredNames of FunctionBody.

### 14.3.2 SS: ComputedPropertyContains <div id="sec-method-definitions-static-semantics-computedpropertycontains"></div>

With parameter symbol.

```
MethodDefinition :
    PropertyName ( UniqueFormalParameters ) { FunctionBody }
    get PropertyName ( ) { FunctionBody }
    set PropertyName ( PropertySetParameterList ) { FunctionBody }
```

1. Return the result of ComputedPropertyContains for PropertyName with argument symbol.

### 14.3.3 SS: ExpectedArgumentCount <div id="sec-method-definitions-static-semantics-expectedargumentcount"></div>

```
PropertySetParameterList : FormalParameter
```

1. If HasInitializer of FormalParameter is true, return 0.
2. Return 1.

### 14.3.4 SS: HasDirectSuper <div id="sec-method-definitions-static-semantics-hasdirectsuper"></div>

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

1. If UniqueFormalParameters Contains SuperCall is true, return true.
2. Return FunctionBody Contains SuperCall.

```
MethodDefinition : get PropertyName ( ) { FunctionBody }
```

1. Return FunctionBody Contains SuperCall

```
MethodDefinition : set PropertyName ( PropertySetParameterList ) { FunctionBody }
```

1. If PropertySetParameterList Contains SuperCall is true, return true.
2. Return FunctionBody Contains SuperCall.

### 14.3.5 SS: PropName <div id="sec-method-definitions-static-semantics-propname"></div>

```
MethodDefinition :
    PropertyName ( UniqueFormalParameters ) { FunctionBody }
    get PropertyName ( ) { FunctionBody }
    set PropertyName ( PropertySetParameterList ) { FunctionBody }
```

1. Return PropName of PropertyName.

### 14.3.6 SS: SpecialMethod <div id="sec-static-semantics-specialmethod"></div>

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

1. Return false.

   ```
   MethodDefinition :
       GeneratorMethod
       AsyncMethod
       AsyncGeneratorMethod
       get PropertyName ( ) { FunctionBody }
       set PropertyName ( PropertySetParameterList ) { FunctionBody }
   ```

   1. Return true.

### 14.3.7 RS: DefineMethod <div id="sec-runtime-semantics-definemethod"></div>

With parameters object and optional parameter functionPrototype.

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

1. Let propKey be the result of evaluating PropertyName.
2. ReturnIfAbrupt(propKey).
3. If the function code for this MethodDefinition is strict mode code, let strict be true. Otherwise let strict be false.
4. Let scope be the running execution context's LexicalEnvironment.
5. If functionPrototype is present as a parameter, then
a. Let kind be Normal.
b. Let prototype be functionPrototype.
6. Else,
a. Let kind be Method.
b. Let prototype be the intrinsic object %FunctionPrototype%.
7. Let closure be FunctionCreate(kind, UniqueFormalParameters, FunctionBody, scope, strict, prototype).
8. Perform MakeMethod(closure, object).
9. Set closure.[[SourceText]] to the source text matched by MethodDefinition.
10. Return the Record { [[Key]]: propKey, [[Closure]]: closure }.

### 14.3.8 RS: PropertyDefinitionEvaluation <div id="sec-method-definitions-runtime-semantics-propertydefinitionevaluation"></div>

With parameters object and enumerable.

```
MethodDefinition : PropertyName ( UniqueFormalParameters ) { FunctionBody }
```

1. Let methodDef be DefineMethod of MethodDefinition with argument object.
2. ReturnIfAbrupt(methodDef).
3. Perform SetFunctionName(methodDef.[[Closure]], methodDef.[[Key]]).
4. Let desc be the PropertyDescriptor { [[Value]]: methodDef.[[Closure]], [[Writable]]: true, [[Enumerable]]:
enumerable, [[Configurable]]: true }.
5. Return ? DefinePropertyOrThrow(object, methodDef.[[Key]], desc)

```
MethodDefinition : get PropertyName ( ) { FunctionBody }
```

1. Let propKey be the result of evaluating PropertyName.
2. ReturnIfAbrupt(propKey).
3. If the function code for this MethodDefinition is strict mode code, let strict be true. Otherwise let strict be false.
4. Let scope be the running execution context's LexicalEnvironment.
5. Let formalParameterList be an instance of the production FormalParameters : [empty] .
6. Let closure be FunctionCreate(Method, formalParameterList, FunctionBody, scope, strict).
7. Perform MakeMethod(closure, object).
8. Perform SetFunctionName(closure, propKey, "get").
9. Set closure.[[SourceText]] to the source text matched by MethodDefinition.
10. Let desc be the PropertyDescriptor { [[Get]]: closure, [[Enumerable]]: enumerable, [[Configurable]]: true }.
11. Return ? DefinePropertyOrThrow(object, propKey, desc).

```
MethodDefinition : set PropertyName ( PropertySetParameterList ) { FunctionBody }
```

1. Let propKey be the result of evaluating PropertyName.
2. ReturnIfAbrupt(propKey).
3. If the function code for this MethodDefinition is strict mode code, let strict be true. Otherwise let strict be false.
4. Let scope be the running execution context's LexicalEnvironment.
5. Let closure be FunctionCreate(Method, PropertySetParameterList, FunctionBody, scope, strict).
6. Perform MakeMethod(closure, object).
7. Perform SetFunctionName(closure, propKey, "set").
8. Set closure.[[SourceText]] to the source text matched by MethodDefinition.
9. Let desc be the PropertyDescriptor { [[Set]]: closure, [[Enumerable]]: enumerable, [[Configurable]]: true }.
10. Return ? DefinePropertyOrThrow(object, propKey, desc).

## 14.4 Generator 函数定义 <div id="sec-generator-function-definitions"></div>

**Syntax**

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

> NOTE 1 The syntactic context immediately following yield requires use of the InputElementRegExpOrTemplateTail lexical goal

> NOTE 2 YieldExpression cannot be used within the FormalParameters of a generator function because any expressions that are part of FormalParameters are evaluated before the resulting generator object is in a resumable state.

> NOTE 3 Abstract operations relating to generator objects are defined in 25.4.3.

### 14.4.1 SS: Early Errors <div id="sec-generator-function-definitions-static-semantics-early-errors"></div>

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

- It is a Syntax Error if HasDirectSuper of GeneratorMethod is true.
- It is a Syntax Error if UniqueFormalParameters Contains YieldExpression is true.
- It is a Syntax Error if ContainsUseStrict of GeneratorBody is true and IsSimpleParameterList of
  UniqueFormalParameters is false.
- It is a Syntax Error if any element of the BoundNames of UniqueFormalParameters also occurs in the
  LexicallyDeclaredNames of GeneratorBody.

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

- If the source code matching this production is strict mode code, the Early Error rules for UniqueFormalParameters : FormalParameters are applied.
- If the source code matching this production is strict mode code, it is a Syntax Error if BindingIdentifier is present and the StringValue of BindingIdentifier is "eval" or "arguments".
- It is a Syntax Error if ContainsUseStrict of GeneratorBody is true and IsSimpleParameterList of FormalParameters is false.
- It is a Syntax Error if any element of the BoundNames of FormalParameters also occurs in the LexicallyDeclaredNames of GeneratorBody.
- It is a Syntax Error if FormalParameters Contains YieldExpression is true.
- It is a Syntax Error if FormalParameters Contains SuperProperty is true.
- It is a Syntax Error if GeneratorBody Contains SuperProperty is true.
- It is a Syntax Error if FormalParameters Contains SuperCall is true.
- It is a Syntax Error if GeneratorBody Contains SuperCall is true.

### 14.4.2 SS: BoundNames <div id="sec-generator-function-definitions-static-semantics-boundnames"></div>

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. Return the BoundNames of BindingIdentifier

```
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
```

1. Return « "\*default\*" ».

> NOTE "\*default\*" is used within this specification as a synthetic name for hoistable anonymous functions that are defined using export declarations.

### 14.4.3 SS: ComputedPropertyContains <div id="sec-generator-function-definitions-static-semantics-computedpropertycontains"></div>

With parameter symbol.

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

1. Return the result of ComputedPropertyContains for PropertyName with argument symbol.

### 14.4.4 SS: Contains <div id="sec-generator-function-definitions-static-semantics-contains"></div>

With parameter symbol.

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. Return false.

> NOTE Static semantic rules that depend upon substructure generally do not look into function definitions.

### 14.4.5 SS: HasDirectSuper <div id="sec-generator-function-definitions-static-semantics-hasdirectsuper"></div>

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

1. If UniqueFormalParameters Contains SuperCall is true, return true.
2. Return GeneratorBody Contains SuperCall.

### 14.4.6 SS: HasName <div id="sec-generator-function-definitions-static-semantics-hasname"></div>

```
GeneratorExpression : function * ( FormalParameters ) { GeneratorBody }
```

1. Return false.

```
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. Return true

### 14.4.7 SS: IsConstantDeclaration <div id="sec-generator-function-definitions-static-semantics-isconstantdeclaration"></div>

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
```

1. Return false

### 14.4.8 SS: IsFunctionDefinition <div id="sec-generator-function-definitions-static-semantics-isfunctiondefinition"></div>

```
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. Return true.

### 14.4.9 SS: PropName <div id="sec-generator-function-definitions-static-semantics-propname"></div>

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

1. Return PropName of PropertyName.

### 14.4.10 RS: EvaluateBody <div id="sec-generator-function-definitions-runtime-semantics-evaluatebody"></div>

With parameters functionObject and List argumentsList.

```
GeneratorBody : FunctionBody
```

1. Perform ? FunctionDeclarationInstantiation(functionObject, argumentsList).
2. Let G be ? OrdinaryCreateFromConstructor(functionObject, "%GeneratorPrototype%", « [[GeneratorState]], [[GeneratorContext]] »)
3. Perform GeneratorStart(G, FunctionBody).
4. Return Completion { [[Type]]: return, [[Value]]: G, [[Target]]: empty }.

### 14.4.11 RS: InstantiateFunctionObject <div id="sec-generator-function-definitions-runtime-semantics-instantiatefunctionobject"></div>

With parameter scope.

```
GeneratorDeclaration : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. If the function code for GeneratorDeclaration is strict mode code, let strict be true. Otherwise let strict be false.
2. Let name be StringValue of BindingIdentifier.
3. Let F be GeneratorFunctionCreate(Normal, FormalParameters, GeneratorBody, scope, strict).
4. Let prototype be ObjectCreate(%GeneratorPrototype%).
5. Perform DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
true, [[Enumerable]]: false, [[Configurable]]: false }).
6. Perform SetFunctionName(F, name).
7. Set F.[[SourceText]] to the source text matched by GeneratorDeclaration.
8. Return F

```
GeneratorDeclaration : function * ( FormalParameters ) { GeneratorBody }
```

1. Let F be GeneratorFunctionCreate(Normal, FormalParameters, GeneratorBody, scope, true).
2. Let prototype be ObjectCreate(%GeneratorPrototype%).
3. Perform DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
true, [[Enumerable]]: false, [[Configurable]]: false }).
4. Perform SetFunctionName(F, "default").
5. Set F.[[SourceText]] to the source text matched by GeneratorDeclaration.
6. Return F.

> NOTE An anonymous GeneratorDeclaration can only occur as part of an export default declaration, and its function code is therefore always strict mode code.

### 14.4.12 RS: PropertyDefinitionEvaluation <div id="sec-generator-function-definitions-runtime-semantics-propertydefinitionevaluation"></div>

With parameters object and enumerable.

```
GeneratorMethod : * PropertyName ( UniqueFormalParameters ) { GeneratorBody }
```

1. Let propKey be the result of evaluating PropertyName.
2. ReturnIfAbrupt(propKey).
3. If the function code for this GeneratorMethod is strict mode code, let strict be true. Otherwise let strict be false.
4. Let scope be the running execution context's LexicalEnvironment.
5. Let closure be GeneratorFunctionCreate(Method, UniqueFormalParameters, GeneratorBody, scope, strict).
6. Perform MakeMethod(closure, object).
7. Let prototype be ObjectCreate(%GeneratorPrototype%).
8. Perform DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
9. Perform SetFunctionName(closure, propKey).
10. Set closure.[[SourceText]] to the source text matched by GeneratorMethod.
11. Let desc be the PropertyDescriptor { [[Value]]: closure, [[Writable]]: true, [[Enumerable]]: enumerable,
[[Configurable]]: true }.
12. Return ? DefinePropertyOrThrow(object, propKey, desc).

### 14.4.13 RS: NamedEvaluation <div id="sec-generator-function-definitions-runtime-semantics-namedevaluation"></div>

With parameter name.

```
GeneratorExpression : function * ( FormalParameters ) { GeneratorBody }
```

1. Let closure be the result of evaluating this GeneratorExpression.
2. Perform SetFunctionName(closure, name).
3. Return closure.

### 14.4.14 RS: Evaluation <div id="sec-generator-function-definitions-runtime-semantics-evaluation"></div>

```
GeneratorExpression : function * ( FormalParameters ) { GeneratorBody }
```

1. If the function code for this GeneratorExpression is strict mode code, let strict be true. Otherwise let strict be false.
2. Let scope be the LexicalEnvironment of the running execution context.
3. Let closure be GeneratorFunctionCreate(Normal, FormalParameters, GeneratorBody, scope, strict).
4. Let prototype be ObjectCreate(%GeneratorPrototype%).
5. Perform DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
6. Set closure.[[SourceText]] to the source text matched by GeneratorExpression.
7. Return closure

```
GeneratorExpression : function * BindingIdentifier ( FormalParameters ) { GeneratorBody }
```

1. If the function code for this GeneratorExpression is strict mode code, let strict be true. Otherwise let strict be
false.
2. Let scope be the running execution context's LexicalEnvironment.
3. Let funcEnv be NewDeclarativeEnvironment(scope).
4. Let envRec be funcEnv's EnvironmentRecord.
5. Let name be StringValue of BindingIdentifier.
6. Perform envRec.CreateImmutableBinding(name, false).
7. Let closure be GeneratorFunctionCreate(Normal, FormalParameters, GeneratorBody, funcEnv, strict).
8. Let prototype be ObjectCreate(%GeneratorPrototype%).
9. Perform DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
10. Perform SetFunctionName(closure, name).
11. Perform envRec.InitializeBinding(name, closure).
12. Set closure.[[SourceText]] to the source text matched by GeneratorExpression.
13. Return closure.

> NOTE The BindingIdentifier in a GeneratorExpression can be referenced from inside the GeneratorExpression's FunctionBody to allow the generator code to call itself recursively. However, unlike in a GeneratorDeclaration, the BindingIdentifier in a GeneratorExpression cannot be referenced from and does not affect the scope enclosing the GeneratorExpression.

```
YieldExpression : yield
```

1. Let generatorKind be ! GetGeneratorKind().
2. If generatorKind is async, then return ? AsyncGeneratorYield(undefined).
3. Otherwise, return ? GeneratorYield(CreateIterResultObject(undefined, false)).

```
YieldExpression : yield AssignmentExpression
```

1. Let generatorKind be ! GetGeneratorKind().
2. Let exprRef be the result of evaluating AssignmentExpression.
3. Let value be ? GetValue(exprRef).
4. If generatorKind is async, then return ? AsyncGeneratorYield(value).
5. Otherwise, return ? GeneratorYield(CreateIterResultObject(value, false)).

```
YieldExpression : yield * AssignmentExpression
```

1. Let generatorKind be ! GetGeneratorKind().
2. Let exprRef be the result of evaluating AssignmentExpression.
3. Let value be ? GetValue(exprRef).
4. Let iteratorRecord be ? GetIterator(value, generatorKind).
5. Let iterator be iteratorRecord.[[Iterator]].
6. Let received be NormalCompletion(undefined).
7. Repeat,
a. If received.[[Type]] is normal, then
i. Let innerResult be ? Call(iteratorRecord.[[NextMethod]], iteratorRecord.[[Iterator]], « received.
[[Value]] »).
ii. If generatorKind is async, then set innerResult to ? Await(innerResult).
iii. If Type(innerResult) is not Object, throw a TypeError exception.
iv. Let done be ? IteratorComplete(innerResult).
v. If done is true, then
1. Return ? IteratorValue(innerResult).
vi. If generatorKind is async, then set received to AsyncGeneratorYield(? IteratorValue(innerResult)).
vii. Else, set received to GeneratorYield(innerResult).
b. Else if received.[[Type]] is throw, then
i. Let throw be ? GetMethod(iterator, "throw").
ii. If throw is not undefined, then
1. Let innerResult be ? Call(throw, iterator, « received.[[Value]] »).
2. If generatorKind is async, then set innerResult to ? Await(innerResult).
3. NOTE: Exceptions from the inner iterator throw method are propagated. Normal completions
from an inner throw method are processed similarly to an inner next.
4. If Type(innerResult) is not Object, throw a TypeError exception.
5. Let done be ? IteratorComplete(innerResult).
6. If done is true, then
a. Return ? IteratorValue(innerResult).
7. If generatorKind is async, then set received to AsyncGeneratorYield(?
IteratorValue(innerResult)).
8. Else, set received to GeneratorYield(innerResult).
iii. Else,
17. NOTE: If iterator does not have a throw method, this throw is going to terminate the yield*
      loop. But first we need to give iterator a chance to clean up.
2. Let closeCompletion be Completion { [[Type]]: normal, [[Value]]: empty, [[Target]]: empty }.
3. If generatorKind is async, perform ? AsyncIteratorClose(iteratorRecord, closeCompletion).
4. Else, perform ? IteratorClose(iteratorRecord, closeCompletion).
5. NOTE: The next step throws a TypeError to indicate that there was a yield* protocol violation:
iterator does not have a throw method.
6. Throw a TypeError exception.
c. Else,
i. Assert: received.[[Type]] is return.
ii. Let return be ? GetMethod(iterator, "return").
iii. If return is undefined, then
1. If generatorKind is async, then set received.[[Value]] to ? Await(received.[[Value]]).
2. Return Completion(received).
iv. Let innerReturnResult be ? Call(return, iterator, « received.[[Value]] »).
v. If generatorKind is async, then set innerReturnResult to ? Await(innerReturnResult).
vi. If Type(innerReturnResult) is not Object, throw a TypeError exception.
vii. Let done be ? IteratorComplete(innerReturnResult).
viii. If done is true, then
1. Let value be ? IteratorValue(innerReturnResult).
2. Return Completion { [[Type]]: return, [[Value]]: value, [[Target]]: empty }.
ix. If generatorKind is async, then set received to AsyncGeneratorYield(?
IteratorValue(innerReturnResult)).
x. Else, set received to GeneratorYield(innerReturnResult).

## 14.5 异步 Generator 函数定义 <div id="sec-async-generator-function-definitions"></div>

**Syntax**

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

> NOTE1 YieldExpression and AwaitExpression cannot be used within the FormalParameters of an async generator function because any expressions that are part of FormalParameters are evaluated before the resulting async generator object is in a resumable state.

> NOTE 2 Abstract operations relating to async generator objects are defined in 25.5.3

### 14.5.1 SS: Early Errors <div id="sec-async-generator-function-definitions-static-semantics-early-errors"></div>

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

- It is a Syntax Error if HasDirectSuper of AsyncGeneratorMethod is true.
- It is a Syntax Error if UniqueFormalParameters Contains YieldExpression is true.
- It is a Syntax Error if UniqueFormalParameters Contains AwaitExpression is true.
- It is a Syntax Error if ContainsUseStrict of AsyncGeneratorBody is true and IsSimpleParameterList of UniqueFormalParameters is false.
- It is a Syntax Error if any element of the BoundNames of UniqueFormalParameters also occurs in the LexicallyDeclaredNames of AsyncGeneratorBody.

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) { AsyncGeneratorBody }
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) { AsyncGeneratorBody }
```

- If the source code matching this production is strict mode code, the Early Error rules for UniqueFormalParameters : FormalParameters are applied.
- If the source code matching this production is strict mode code, it is a Syntax Error if BindingIdentifier is the IdentifierName eval or the IdentifierName arguments.
- It is a Syntax Error if ContainsUseStrict of AsyncGeneratorBody is true and IsSimpleParameterList of
  FormalParameters is false.
- It is a Syntax Error if any element of the BoundNames of FormalParameters also occurs in the
  LexicallyDeclaredNames of AsyncGeneratorBody.
- It is a Syntax Error if FormalParameters Contains YieldExpression is true.
- It is a Syntax Error if FormalParameters Contains AwaitExpression is true.
- It is a Syntax Error if FormalParameters Contains SuperProperty is true.
- It is a Syntax Error if AsyncGeneratorBody Contains SuperProperty is true.
- It is a Syntax Error if FormalParameters Contains SuperCall is true.
- It is a Syntax Error if AsyncGeneratorBody Contains SuperCall is true

### 14.5.2 SS: BoundNames <div id="sec-async-generator-function-definitions-static-semantics-boundnames"></div>

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. Return the BoundNames of BindingIdentifier.

```
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. Return « "\*default\*" »

> NOTE "\*default\*" is used within this specification as a synthetic name for hoistable anonymous functions that are defined using export declarations

### 14.5.3 SS: ComputedPropertyContains <div id="sec-async-generator-function-definitions-static-semantics-computedpropertycontains"></div>

With parameter symbol.

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

1. Return the result of ComputedPropertyContains for PropertyName with argument symbol.

### 14.5.4 SS: Contains <div id="sec-async-generator-function-definitions-static-semantics-contains"></div>

With parameter symbol.

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. Return false.

> NOTE Static semantic rules that depend upon substructure generally do not look into function definitions.

### 14.5.5 SS: HasDirectSuper <div id="sec-async-generator-function-definitions-static-semantics-hasdirectsuper"></div>

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

1. If UniqueFormalParameters Contains SuperCall is true, return true.
2. Return AsyncGeneratorBody Contains SuperCall.

### 14.5.6 SS: HasName <div id="sec-async-generator-function-definitions-static-semantics-hasname"></div>

```
AsyncGeneratorExpression : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. Return false.

```
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. Return true.

### 14.5.7 SS: IsConstantDeclaration <div id="sec-async-generator-function-definitions-static-semantics-isconstantdeclaration"></div>

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. Return false.

### 14.5.8 SS: IsFunctionDefinition <div id="sec-async-generator-function-definitions-static-semantics-isfunctiondefinition"></div>

```
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. Return true.

### 14.5.9 SS: PropName <div id="sec-async-generator-function-definitions-static-semantics-propname"></div>

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

1. Return PropName of PropertyName.

### 14.5.10 RS: EvaluateBody <div id="sec-asyncgenerator-definitions-evaluatebody"></div>

With parameters functionObject and List argumentsList.

```
AsyncGeneratorBody : FunctionBody
```

1. Perform ? FunctionDeclarationInstantiation(functionObject, argumentsList).
2. Let generator be ? OrdinaryCreateFromConstructor(functionObject, "%AsyncGeneratorPrototype%", «
[[AsyncGeneratorState]], [[AsyncGeneratorContext]], [[AsyncGeneratorQueue]] »).
3. Perform ! AsyncGeneratorStart(generator, FunctionBody).
4. Return Completion { [[Type]]: return, [[Value]]: generator, [[Target]]: empty }.

### 14.5.11 RS: InstantiateFunctionObject <div id="sec-asyncgenerator-definitions-instantiatefunctionobject"></div>

With parameter scope.

```
AsyncGeneratorDeclaration : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. If the function code for AsyncGeneratorDeclaration is strict mode code, let strict be true. Otherwise let strict be
false.
2. Let name be StringValue of BindingIdentifier.
3. Let F be ! AsyncGeneratorFunctionCreate(Normal, FormalParameters, AsyncGeneratorBody, scope, strict).
4. Let prototype be ! ObjectCreate(%AsyncGeneratorPrototype%).
5. Perform ! DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
true, [[Enumerable]]: false, [[Configurable]]: false }).
6. Perform ! SetFunctionName(F, name).
7. Set F.[[SourceText]] to the source text matched by AsyncGeneratorDeclaration.
8. Return F.

```
AsyncGeneratorDeclaration : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. If the function code for AsyncGeneratorDeclaration is strict mode code, let strict be true. Otherwise let strict be false.
2. Let F be AsyncGeneratorFunctionCreate(Normal, FormalParameters, AsyncGeneratorBody, scope, strict).
3. Let prototype be ObjectCreate(%AsyncGeneratorPrototype%).
4. Perform DefinePropertyOrThrow(F, "prototype", PropertyDescriptor { [[Value]]: prototype, [[Writable]]:
   true, [[Enumerable]]: false, [[Configurable]]: false }).
5. Perform SetFunctionName(F, "default").
6. Set F.[[SourceText]] to the source text matched by AsyncGeneratorDeclaration.
7. Return F

> NOTE An anonymous AsyncGeneratorDeclaration can only occur as part of an export default declaration.

### 14.5.12 RS: PropertyDefinitionEvaluation <div id="sec-asyncgenerator-definitions-propertydefinitionevaluation"></div>

With parameter object and enumerable.

```
AsyncGeneratorMethod : async * PropertyName ( UniqueFormalParameters ) { AsyncGeneratorBody }
```

1. Let propKey be the result of evaluating PropertyName.
2. ReturnIfAbrupt(propKey).
3. If the function code for this AsyncGeneratorMethod is strict mode code, let strict be true. Otherwise let strict be
false.
4. Let scope be the running execution context's LexicalEnvironment.
5. Let closure be ! AsyncGeneratorFunctionCreate(Method, UniqueFormalParameters, AsyncGeneratorBody, scope,
strict).
6. Perform ! MakeMethod(closure, object).
7. Let prototype be ! ObjectCreate(%AsyncGeneratorPrototype%).
8. Perform ! DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
9. Perform ! SetFunctionName(closure, propKey).
10. Set closure.[[SourceText]] to the source text matched by AsyncGeneratorMethod.
11. Let desc be PropertyDescriptor { [[Value]]: closure, [[Writable]]: true, [[Enumerable]]: enumerable,
[[Configurable]]: true }.
12. Return ? DefinePropertyOrThrow(object, propKey, desc).

### 14.5.13 RS: NamedEvaluation <div id="sec-asyncgenerator-definitions-namedevaluation"></div>

With parameter name.

```
AsyncGeneratorExpression : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. Let closure be the result of evaluating this AsyncGeneratorExpression.
2. Perform SetFunctionName(closure, name).
3. Return closure.

### 14.5.14 RS: Evaluation <div id="sec-asyncgenerator-definitions-evaluation"></div>

```
AsyncGeneratorExpression : async function * ( FormalParameters ) { AsyncGeneratorBody }
```

1. If the function code for this AsyncGeneratorExpression is strict mode code, let strict be true. Otherwise let strict be false.
2. Let scope be the LexicalEnvironment of the running execution context.
3. Let closure be ! AsyncGeneratorFunctionCreate(Normal, FormalParameters, AsyncGeneratorBody, scope, strict).
4. Let prototype be ! ObjectCreate(%AsyncGeneratorPrototype%).
5. Perform ! DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
6. Set closure.[[SourceText]] to the source text matched by AsyncGeneratorExpression.
7. Return closure.

```
AsyncGeneratorExpression : async function * BindingIdentifier ( FormalParameters ) {
AsyncGeneratorBody }
```

1. If the function code for this AsyncGeneratorExpression is strict mode code, let strict be true. Otherwise let strict be
false.
2. Let scope be the running execution context's LexicalEnvironment.
3. Let funcEnv be ! NewDeclarativeEnvironment(scope).
4. Let envRec be funcEnv's EnvironmentRecord.
5. Let name be StringValue of BindingIdentifier.
6. Perform ! envRec.CreateImmutableBinding(name).
7. Let closure be ! AsyncGeneratorFunctionCreate(Normal, FormalParameters, AsyncGeneratorBody, funcEnv, strict).
8. Let prototype be ! ObjectCreate(%AsyncGeneratorPrototype%).
9. Perform ! DefinePropertyOrThrow(closure, "prototype", PropertyDescriptor { [[Value]]: prototype,
[[Writable]]: true, [[Enumerable]]: false, [[Configurable]]: false }).
10. Perform ! SetFunctionName(closure, name).
11. Perform ! envRec.InitializeBinding(name, closure).
12. Set closure.[[SourceText]] to the source text matched by AsyncGeneratorExpression.
13. Return closure.

> NOTE The BindingIdentifier in an AsyncGeneratorExpression can be referenced from inside the AsyncGeneratorExpression's AsyncGeneratorBody to allow the generator code to call itself recursively. However, unlike in an AsyncGeneratorDeclaration, the BindingIdentifier in an AsyncGeneratorExpression cannot be referenced from and does not affect the scope enclosing the AsyncGeneratorExpression.

## 14.6 Class 定义 <div id="sec-class-definitions"></div>

**Syntax**

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

> NOTE A class definition is always strict mode code.

### 14.6.1 SS: Early Errors <div id="sec-class-definitions-static-semantics-early-errors"></div>

```
ClassTail : ClassHeritage { ClassBody }
```

- It is a Syntax Error if ClassHeritage is not present and the following algorithm evaluates to true:
  1. Let constructor be ConstructorMethod of ClassBody.
  2. If constructor is empty, return false.
  3. Return HasDirectSuper of constructor.

```
ClassBody : ClassElementList
```

- It is a Syntax Error if PrototypePropertyNameList of ClassElementList contains more than one occurrence of "constructor".

```
ClassElement : MethodDefinition
```

- It is a Syntax Error if PropName of MethodDefinition is not "constructor" and HasDirectSuper of
  MethodDefinition is true.
- It is a Syntax Error if PropName of MethodDefinition is "constructor" and SpecialMethod of
  MethodDefinition is true.

```
ClassElement : static MethodDefinition
```

- It is a Syntax Error if HasDirectSuper of MethodDefinition is true.
- It is a Syntax Error if PropName of MethodDefinition is "prototype".

### 14.6.2 SS: BoundNames <div id="sec-class-definitions-static-semantics-boundnames"></div>

```
ClassDeclaration : class BindingIdentifier ClassTail
```

1. Return the BoundNames of BindingIdentifier

```
ClassDeclaration : class ClassTail
```

1. Return « "\*default\*" ».

### 14.6.3 SS: ConstructorMethod <div id="sec-static-semantics-constructormethod"></div>

```
ClassElementList : ClassElement
```

1. If ClassElement is ClassElement : ; , return empty.
2. If IsStatic of ClassElement is true, return empty.
3. If PropName of ClassElement is not "constructor", return empty.
4. Return ClassElement.

```
ClassElementList : ClassElementList ClassElement
```

1. Let head be ConstructorMethod of ClassElementList.
2. If head is not empty, return head.
3. If ClassElement is ClassElement : ; , return empty.
4. If IsStatic of ClassElement is true, return empty.
5. If PropName of ClassElement is not "constructor", return empty.
6. Return ClassElement

> NOTE Early Error rules ensure that there is only one method definition named "constructor" and that it is not an accessor property or generator definition.

### 14.6.4 SS: Contains <div id="sec-class-definitions-static-semantics-contains"></div>

With parameter symbol.

```
ClassTail : ClassHeritage { ClassBody }
```

1. If symbol is ClassBody, return true.
2. If symbol is ClassHeritage, then
a. If ClassHeritage is present, return true; otherwise return false.
3. Let inHeritage be ClassHeritage Contains symbol.
4. If inHeritage is true, return true.
5. Return the result of ComputedPropertyContains for ClassBody with argument symbol.

> NOTE Static semantic rules that depend upon substructure generally do not look into class bodies except for PropertyNames

### 14.6.5 SS: ComputedPropertyContains <div id="sec-class-definitions-static-semantics-computedpropertycontains"></div>

With parameter symbol.

```
ClassElementList : ClassElementList ClassElement
```

1. Let inList be the result of ComputedPropertyContains for ClassElementList with argument symbol.
2. If inList is true, return true.
3. Return the result of ComputedPropertyContains for ClassElement with argument symbol.

```
ClassElement : MethodDefinition
```

1. Return the result of ComputedPropertyContains for MethodDefinition with argument symbol

```
ClassElement : static MethodDefinition
```

1. Return the result of ComputedPropertyContains for MethodDefinition with argument symbol.

```
ClassElement : ;
```

1. Return false.

### 14.6.6 SS: HasName <div id="sec-class-definitions-static-semantics-hasname"></div>

```
ClassExpression : class ClassTail
```

1. Return false.

```
ClassExpression : class BindingIdentifier ClassTail
```

1. Return true.

### 14.6.7 SS: IsConstantDeclaration <div id="sec-class-definitions-static-semantics-isconstantdeclaration"></div>

```
ClassDeclaration : class BindingIdentifier ClassTail
ClassDeclaration : class ClassTail
```

1. Return false.

### 14.6.8 SS: IsFunctionDefinition <div id="sec-class-definitions-static-semantics-isfunctiondefinition"></div>

```
ClassExpression : class BindingIdentifier ClassTail
```

1. Return true.

### 14.6.9 SS: IsStatic <div id="sec-static-semantics-isstatic"></div>

```
ClassElement : MethodDefinition
```

1. Return false.

```
ClassElement : static MethodDefinition
```

1. Return true.

```
ClassElement : ;
```

1. Return false.

### 14.6.10 SS: NonConstructorMethodDefinitions <div id="sec-static-semantics-nonconstructormethoddefinitions"></div>

```
ClassElementList : ClassElement
```

1. If ClassElement is ClassElement : ; , return a new empty List.
2. If IsStatic of ClassElement is false and PropName of ClassElement is "constructor", return a new empty List.
3. Return a List containing ClassElement.

```
ClassElementList : ClassElementList ClassElement
```

1. Let list be NonConstructorMethodDefinitions of ClassElementList.
2. If ClassElement is ClassElement : ; , return list.
3. If IsStatic of ClassElement is false and PropName of ClassElement is "constructor", return list.
4. Append ClassElement to the end of list.
5. Return list.

### 14.6.11 SS: PrototypePropertyNameList <div id="sec-static-semantics-prototypepropertynamelist"></div>

```
ClassElementList : ClassElement
```

1. If PropName of ClassElement is empty, return a new empty List.
2. If IsStatic of ClassElement is true, return a new empty List.
3. Return a List containing PropName of ClassElement.

```
ClassElementList : ClassElementList ClassElement
```

1. Let list be PrototypePropertyNameList of ClassElementList.
2. If PropName of ClassElement is empty, return list.
3. If IsStatic of ClassElement is true, return list.
4. Append PropName of ClassElement to the end of list.
5. Return list.

### 14.6.12 SS: PropName <div id="sec-class-definitions-static-semantics-propname"></div>

```
ClassElement : ;
```

1. Return empty.

### 14.6.13 RS: ClassDefinitionEvaluation <div id="sec-runtime-semantics-classdefinitionevaluation"></div>

With parameters classBinding and className

1. Let lex be the LexicalEnvironment of the running execution context.
2. Let classScope be NewDeclarativeEnvironment(lex).
3. Let classScopeEnvRec be classScope's EnvironmentRecord.
4. If classBinding is not undefined, then
a. Perform classScopeEnvRec.CreateImmutableBinding(classBinding, true).
5. If ClassHeritageopt is not present, then
a. Let protoParent be the intrinsic object %ObjectPrototype%.
b. Let constructorParent be the intrinsic object %FunctionPrototype%.
6. Else,
a. Set the running execution context's LexicalEnvironment to classScope.
b. Let superclassRef be the result of evaluating ClassHeritage.
7. c. Set the running execution context's LexicalEnvironment to lex.
    d. Let superclass be ? GetValue(superclassRef).
    e. If superclass is null, then
    i. Let protoParent be null.
    ii. Let constructorParent be the intrinsic object %FunctionPrototype%.
    f. Else if IsConstructor(superclass) is false, throw a TypeError exception.
    g. Else,
    i. Let protoParent be ? Get(superclass, "prototype").
    ii. If Type(protoParent) is neither Object nor Null, throw a TypeError exception.
    iii. Let constructorParent be superclass.
  7. Let proto be ObjectCreate(protoParent).
  8. If ClassBodyopt is not present, let constructor be empty.
  9. Else, let constructor be ConstructorMethod of ClassBody.
  10. If constructor is empty, then
    a. If ClassHeritageopt is present, then
    i. Set constructor to the result of parsing the source text
    constructor(... args){ super (...args);}
    using the syntactic grammar with the goal symbol MethodDefinition[~Yield, ~Await] .
    b. Else,
    i. Set constructor to the result of parsing the source text
    constructor(){ }
    using the syntactic grammar with the goal symbol MethodDefinition[~Yield, ~Await] .
  11. Set the running execution context's LexicalEnvironment to classScope.
  12. Let constructorInfo be the result of performing DefineMethod for constructor with arguments proto and
    constructorParent as the optional functionPrototype argument.
  13. Assert: constructorInfo is not an abrupt completion.
  14. Let F be constructorInfo.[[Closure]].
  15. If ClassHeritageopt is present, set F.[[ConstructorKind]] to "derived".
  16. Perform MakeConstructor(F, false, proto).
  17. Perform MakeClassConstructor(F).
  18. If className is not undefined, then
    a. Perform SetFunctionName(F, className).
  19. Perform CreateMethodProperty(proto, "constructor", F).
  20. If ClassBodyopt is not present, let methods be a new empty List.
  21. Else, let methods be NonConstructorMethodDefinitions of ClassBody.
  22. For each ClassElement m in order from methods, do
    a. If IsStatic of m is false, then
    i. Let status be the result of performing PropertyDefinitionEvaluation for m with arguments proto and
    false.
    b. Else,
    i. Let status be the result of performing PropertyDefinitionEvaluation for m with arguments F and false.
    c. If status is an abrupt completion, then
    i. Set the running execution context's LexicalEnvironment to lex.
    ii. Return Completion(status).
  23. Set the running execution context's LexicalEnvironment to lex.
  24. If classBinding is not undefined, then
      a. Perform classScopeEnvRec.InitializeBinding(classBinding, F).
  25. Return F.

### 14.6.14 RS: BindingClassDeclarationEvaluation <div id="sec-runtime-semantics-bindingclassdeclarationevaluation"></div>

```
ClassDeclaration : class BindingIdentifier ClassTail
```

1. Let className be StringValue of BindingIdentifier.
2. Let value be the result of ClassDefinitionEvaluation of ClassTail with arguments className and className.
3. ReturnIfAbrupt(value).
4. Set value.[[SourceText]] to the source text matched by ClassDeclaration.
5. Let env be the running execution context's LexicalEnvironment.
6. Perform ? InitializeBoundName(className, value, env).
7. Return value.

```
ClassDeclaration : class ClassTail
```

1. Let value be the result of ClassDefinitionEvaluation of ClassTail with arguments undefined and "default".
2. ReturnIfAbrupt(value).
3. Set value.[[SourceText]] to the source text matched by ClassDeclaration.
4. Return value.

> NOTE ClassDeclaration : class ClassTail only occurs as part of an ExportDeclaration and establishing its binding is handled as part of the evaluation action for that production. See 15.2.3.11.

### 14.6.15 RS: NamedEvaluation <div id="sec-class-definitions-runtime-semantics-namedevaluation"></div>

With parameter name.

```
ClassExpression : class ClassTail
```

1. Return the result of ClassDefinitionEvaluation of ClassTail with arguments undefined and name.

### 14.6.16 RS: Evaluation <div id="sec-class-definitions-runtime-semantics-evaluation"></div>

```
ClassDeclaration : class BindingIdentifier ClassTail
```

1. Perform ? BindingClassDeclarationEvaluation of this ClassDeclaration.
2. Return NormalCompletion(empty).

> NOTE ClassDeclaration : class ClassTail only occurs as part of an ExportDeclaration and is never directly evaluated.

```
ClassExpression : class BindingIdentifier ClassTail
```

1. If BindingIdentifieropt is not present, let className be undefined.
2. Else, let className be StringValue of BindingIdentifier.
3. Let value be the result of ClassDefinitionEvaluation of ClassTail with arguments className and className.
4. ReturnIfAbrupt(value).

5. Set value.[[SourceText]] to the source text matched by ClassExpression.
6. Return value.

## 14.7 Async 函数定义 <div id="sec-async-function-definitions"></div>

**Syntax**

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

> NOTE 1 await is parsed as an AwaitExpression when the [Await] parameter is present. The [Await] parameter is present in the following contexts:
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

>NOTE 2
>Unlike YieldExpression, it is a Syntax Error to omit the operand of an AwaitExpression. You must await something.

### 14.7.1 SS: Early Errors <div id="sec-async-function-definitions-static-semantics-early-errors"></div>

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

- It is a Syntax Error if ContainsUseStrict of AsyncFunctionBody is true and IsSimpleParameterList of
  UniqueFormalParameters is false.
- It is a Syntax Error if HasDirectSuper of AsyncMethod is true.
- It is a Syntax Error if UniqueFormalParameters Contains AwaitExpression is true.
- It is a Syntax Error if any element of the BoundNames of UniqueFormalParameters also occurs in the
  LexicallyDeclaredNames of AsyncFunctionBody.

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

- It is a Syntax Error if ContainsUseStrict of AsyncFunctionBody is true and IsSimpleParameterList of
  FormalParameters is false.
- It is a Syntax Error if FormalParameters Contains AwaitExpression is true.
- If the source code matching this production is strict code, the Early Error rules for UniqueFormalParameters :
  FormalParameters are applied.
- If the source code matching this production is strict code, it is a Syntax Error if BindingIdentifier is present and the StringValue of BindingIdentifier is "eval" or "arguments".
- It is a Syntax Error if any element of the BoundNames of FormalParameters also occurs in the
  LexicallyDeclaredNames of AsyncFunctionBody.
- It is a Syntax Error if FormalParameters Contains SuperProperty is true.
- It is a Syntax Error if AsyncFunctionBody Contains SuperProperty is true.
- It is a Syntax Error if FormalParameters Contains SuperCall is true.
- It is a Syntax Error if AsyncFunctionBody Contains SuperCall is true.

### 14.7.2 SS: BoundNames <div id="sec-async-function-definitions-static-semantics-BoundNames"></div>

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. Return the BoundNames of BindingIdentifier

```
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. Return « "\*default\*" ».

> NOTE "\*default\*" is used within this specification as a synthetic name for hoistable anonymous functions that are defined using export declarations.

### 14.7.3 SS: ComputedPropertyContains <div id="sec-async-function-definitions-static-semantics-ComputedPropertyContains"></div>

With parameter symbol.

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

1. Return the result of ComputedPropertyContains for PropertyName with argument symbol.

### 14.7.4 SS: Contains <div id="sec-async-function-definitions-static-semantics-Contains"></div>

With parameter symbol.

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. Return false.

### 14.7.5 SS: HasDirectSuper <div id="sec-async-function-definitions-static-semantics-HasDirectSuper"></div>

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

1. If UniqueFormalParameters Contains SuperCall is true, return true.
2. Return AsyncFunctionBody Contains SuperCall.

### 14.7.6 SS: HasName <div id="sec-async-function-definitions-static-semantics-HasName"></div>

```
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. Return false

```
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. Return true.

### 14.7.7 SS: IsConstantDeclaration <div id="sec-async-function-definitions-static-semantics-IsConstantDeclaration"></div>

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. Return false.

### 14.7.8 SS: IsFunctionDefinition <div id="sec-async-function-definitions-static-semantics-IsFunctionDefinition"></div>

```
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. Return true.

### 14.7.9 SS: PropName <div id="sec-async-function-definitions-static-semantics-PropName"></div>

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

1. Return PropName of PropertyName.

### 14.7.10 RS: InstantiateFunctionObject <div id="sec-async-function-definitions-InstantiateFunctionObject"></div>

With parameter scope.

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. If the function code for AsyncFunctionDeclaration is strict mode code, let strict be true. Otherwise, let strict be false.
2. Let name be StringValue of BindingIdentifier.
3. Let F be ! AsyncFunctionCreate(Normal, FormalParameters, AsyncFunctionBody, scope, strict).
4. Perform ! SetFunctionName(F, name).
5. Set F.[[SourceText]] to the source text matched by AsyncFunctionDeclaration.
6. Return F

```
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. If the function code for AsyncFunctionDeclaration is strict mode code, let strict be true. Otherwise, let strict be false.
2. Let F be ! AsyncFunctionCreate(Normal, FormalParameters, AsyncFunctionBody, scope, strict).
3. Perform ! SetFunctionName(F, "default").
4. Set F.[[SourceText]] to the source text matched by AsyncFunctionDeclaration.
5. Return F.

### 14.7.11 RS: EvaluateBody <div id="sec-async-function-definitions-EvaluateBody"></div>

With parameters functionObject and List argumentsList.

```
AsyncFunctionBody : FunctionBody
```

1. Let promiseCapability be ! NewPromiseCapability(%Promise%).
2. Let declResult be FunctionDeclarationInstantiation(functionObject, argumentsList).
3. If declResult is not an abrupt completion, then
a. Perform ! AsyncFunctionStart(promiseCapability, FunctionBody).
4. Else declResult is an abrupt completion,
a. Perform ! Call(promiseCapability.[[Reject]], undefined, « declResult.[[Value]] »).
5. Return Completion { [[Type]]: return, [[Value]]: promiseCapability.[[Promise]], [[Target]]: empty }.

### 14.7.12 RS: PropertyDefinitionEvaluation <div id="sec-async-function-definitions-PropertyDefinitionEvaluation"></div>

With parameters object and enumerable.

```
AsyncMethod : async PropertyName ( UniqueFormalParameters ) { AsyncFunctionBody }
```

1. Let propKey be the result of evaluating PropertyName.
2. ReturnIfAbrupt(propKey).
3. If the function code for this AsyncMethod is strict mode code, let strict be true. Otherwise let strict be false.
4. Let scope be the LexicalEnvironment of the running execution context.
5. Let closure be ! AsyncFunctionCreate(Method, UniqueFormalParameters, AsyncFunctionBody, scope, strict).
6. Perform ! MakeMethod(closure, object).
7. Perform ! SetFunctionName(closure, propKey).
8. Set closure.[[SourceText]] to the source text matched by AsyncMethod.
9. Let desc be the PropertyDescriptor { [[Value]]: closure, [[Writable]]: true, [[Enumerable]]: enumerable,
[[Configurable]]: true }.
10. Return ? DefinePropertyOrThrow(object, propKey, desc).

### 14.7.13 RS: NamedEvaluation <div id="sec-async-function-definitions-runtime-semantics-namedevaluation"></div>

With parameter name.

```
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. Let closure be the result of evaluating this AsyncFunctionExpression.
2. Perform SetFunctionName(closure, name).
3. Return closure

### 14.7.14 RS: Evaluation <div id="sec-async-function-definitions-runtime-semantics-evaluation"></div>

```
AsyncFunctionDeclaration : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. Return NormalCompletion(empty) 

```
AsyncFunctionDeclaration : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. Return NormalCompletion(empty).

```
AsyncFunctionExpression : async function ( FormalParameters ) { AsyncFunctionBody }
```

1. If the function code for AsyncFunctionExpression is strict mode code, let strict be true. Otherwise let strict be
false.
2. Let scope be the LexicalEnvironment of the running execution context.
3. Let closure be ! AsyncFunctionCreate(Normal, FormalParameters, AsyncFunctionBody, scope, strict).
4. Set closure.[[SourceText]] to the source text matched by AsyncFunctionExpression.
5. Return closure.

```
AsyncFunctionExpression : async function BindingIdentifier ( FormalParameters ) { AsyncFunctionBody }
```

1. If the function code for AsyncFunctionExpression is strict mode code, let strict be true. Otherwise let strict be
false.
2. Let scope be the LexicalEnvironment of the running execution context.
3. Let funcEnv be ! NewDeclarativeEnvironment(scope).
4. Let envRec be funcEnv's EnvironmentRecord.
5. Let name be StringValue of BindingIdentifier.
6. Perform ! envRec.CreateImmutableBinding(name).
7. Let closure be ! AsyncFunctionCreate(Normal, FormalParameters, AsyncFunctionBody, funcEnv, strict).
8. Perform ! SetFunctionName(closure, name).
9. Perform ! envRec.InitializeBinding(name, closure).
10. Set closure.[[SourceText]] to the source text matched by AsyncFunctionExpression.
11. Return closure.

```
AwaitExpression : await UnaryExpression
```

1. Let exprRef be the result of evaluating UnaryExpression.
2. Let value be ? GetValue(exprRef).
3. Return ? Await(value)

## 14.8 Async 箭头函数 <div id="sec-async-arrow-function-definitions"></div>

**Syntax**

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

**Supplemental Syntax**

When processing an instance of the production AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody the interpretation of CoverCallExpressionAndAsyncArrowHead is refined using the following grammar:

```
AsyncArrowHead :
	async [no LineTerminator here] ArrowFormalParameters[~Yield, +Await]
```

### 14.8.1 SS: Early Errors <div id="sec-async-arrow-function-definitions-static-semantics-early-errors"></div>

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
```

- It is a Syntax Error if any element of the BoundNames of AsyncArrowBindingIdentifier also occurs in the LexicallyDeclaredNames of AsyncConciseBody.

```
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

- It is a Syntax Error if CoverCallExpressionAndAsyncArrowHead Contains YieldExpression is true.
- It is a Syntax Error if CoverCallExpressionAndAsyncArrowHead Contains AwaitExpression is true.
- It is a Syntax Error if CoverCallExpressionAndAsyncArrowHead is not covering an AsyncArrowHead.
- It is a Syntax Error if any element of the BoundNames of CoverCallExpressionAndAsyncArrowHead also occurs in the LexicallyDeclaredNames of AsyncConciseBody.
- It is a Syntax Error if ContainsUseStrict of AsyncConciseBody is true and IsSimpleParameterList of
  CoverCallExpressionAndAsyncArrowHead is false.
- All Early Error rules for AsyncArrowHead and its derived productions apply to CoveredAsyncArrowHead of CoverCallExpressionAndAsyncArrowHead.

### 14.8.2 SS: CoveredAsyncArrowHead <div id="sec-async-arrow-function-definitions-static-semantics-CoveredAsyncArrowHead"></div>

```
CoverCallExpressionAndAsyncArrowHead : MemberExpression Arguments
```

1. Return the AsyncArrowHead that is covered by CoverCallExpressionAndAsyncArrowHead

### 14.8.3 SS: BoundNames <div id="sec-async-arrow-function-definitions-static-semantics-BoundNames"></div>

```
CoverCallExpressionAndAsyncArrowHead : MemberExpression Arguments
```

1. Let head be CoveredAsyncArrowHead of CoverCallExpressionAndAsyncArrowHead.
2. Return the BoundNames of head.

### 14.8.4 SS: Contains <div id="sec-async-arrow-function-definitions-static-semantics-Contains"></div>

With parameter symbol.

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
```

1. If symbol is not one of NewTarget, SuperProperty, SuperCall, super, or this, return false.
2. Return AsyncConciseBody Contains symbol.

```
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

1. If symbol is not one of NewTarget, SuperProperty, SuperCall, super, or this, return false.
2. Let head be CoveredAsyncArrowHead of CoverCallExpressionAndAsyncArrowHead.
3. If head Contains symbol is true, return true.
4. Return AsyncConciseBody Contains symbol.

> NOTE Normally, Contains does not look inside most function forms. However, Contains is used to detect new.target, this, and super usage within an AsyncArrowFunction.

### 14.8.5 SS: ContainsExpression <div id="sec-async-arrow-function-definitions-static-semantics-ContainsExpression"></div>

```
AsyncArrowBindingIdentifier : BindingIdentifier
```

1. Return false.

### 14.8.6 SS: ExpectedArgumentCount <div id="sec-async-arrow-function-definitions-static-semantics-ExpectedArgumentCount"></div>

```
AsyncArrowBindingIdentifier : BindingIdentifier
```

1. Return 1.

### 14.8.7 SS: HasName <div id="sec-async-arrow-function-definitions-static-semantics-HasName"></div>

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

1. Return false.

### 14.8.8 SS: IsSimpleParameterList <div id="sec-async-arrow-function-definitions-static-semantics-IsSimpleParameterList"></div>

```
AsyncArrowBindingIdentifier : BindingIdentifier
```

1. Return true.

```
CoverCallExpressionAndAsyncArrowHead : MemberExpression Arguments
```

1. Let head be CoveredAsyncArrowHead of CoverCallExpressionAndAsyncArrowHead.
2. Return IsSimpleParameterList of head.

### 14.8.9 SS: LexicallyDeclaredNames <div id="sec-async-arrow-function-definitions-static-semantics-LexicallyDeclaredNames"></div>

```
AsyncConciseBody : AssignmentExpression
```

1. Return a new empty List.

### 14.8.10 SS: LexicallyScopedDeclarations <div id="sec-async-arrow-function-definitions-static-semantics-LexicallyScopedDeclarations"></div>

```
AsyncConciseBody : AssignmentExpression
```

1. Return a new empty List.

### 14.8.11 SS: VarDeclaredNames <div id="sec-async-arrow-function-definitions-static-semantics-VarDeclaredNames"></div>

```
AsyncConciseBody : AssignmentExpression
```

1. Return a new empty List.

### 14.8.12 SS: VarScopedDeclarations <div id="sec-async-arrow-function-definitions-static-semantics-VarScopedDeclarations"></div>

```
AsyncConciseBody : AssignmentExpression
```

1. Return a new empty List.

### 14.8.13 RS: IteratorBindingInitialization <div id="sec-async-arrow-function-definitions-IteratorBindingInitialization"></div>

With parameters iteratorRecord and environment.

```
AsyncArrowBindingIdentifier : BindingIdentifier
```

1. Assert: iteratorRecord.[[Done]] is false.
2. Let next be IteratorStep(iteratorRecord).
3. If next is an abrupt completion, set iteratorRecord.[[Done]] to true.
4. ReturnIfAbrupt(next).
5. If next is false, set iteratorRecord.[[Done]] to true.
6. Else,
a. Let v be IteratorValue(next).
b. If v is an abrupt completion, set iteratorRecord.[[Done]] to true.
c. ReturnIfAbrupt(v).
7. If iteratorRecord.[[Done]] is true, let v be undefined.
8. Return the result of performing BindingInitialization for BindingIdentifier using v and environment as the arguments.

### 14.8.14 RS: EvaluateBody <div id="sec-async-arrow-function-definitions-EvaluateBody"></div>

With parameters functionObject and List argumentsList.

```
AsyncConciseBody : AssignmentExpression
```

1. Let promiseCapability be ! NewPromiseCapability(%Promise%).
2. Let declResult be FunctionDeclarationInstantiation(functionObject, argumentsList).
3. If declResult is not an abrupt completion, then
a. Perform ! AsyncFunctionStart(promiseCapability, AssignmentExpression).
4. Else declResult is an abrupt completion,
a. Perform ! Call(promiseCapability.[[Reject]], undefined, « declResult.[[Value]] »).
5. Return Completion { [[Type]]: return, [[Value]]: promiseCapability.[[Promise]], [[Target]]: empty }.

```
AsyncConciseBody : { AsyncFunctionBody }
```

1. Return the result of EvaluateBody of AsyncFunctionBody passing functionObject and argumentsList as the arguments

### 14.8.15 RS: NamedEvaluation <div id="sec-async-arrow-function-definitions-runtime-semantics-namedevaluation"></div>

With parameter name.

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

1. Let closure be the result of evaluating this AsyncArrowFunction.
2. Perform SetFunctionName(closure, name).
3. Return closure.

### 14.8.16 RS: Evaluation <div id="sec-async-arrow-function-definitions-runtime-semantics-evaluation"></div>

```
AsyncArrowFunction : async AsyncArrowBindingIdentifier => AsyncConciseBody
```

1. If the function code for this AsyncArrowFunction is strict mode code, let strict be true. Otherwise, let strict be
false.
2. Let scope be the LexicalEnvironment of the running execution context.
3. Let parameters be AsyncArrowBindingIdentifier.
4. Let closure be ! AsyncFunctionCreate(Arrow, parameters, AsyncConciseBody, scope, strict).
5. Return closure.

```
AsyncArrowFunction : CoverCallExpressionAndAsyncArrowHead => AsyncConciseBody
```

1. If the function code for this AsyncArrowFunction is strict mode code, let strict be true. Otherwise, let strict be
false.
2. Let scope be the LexicalEnvironment of the running execution context.
3. Let head be CoveredAsyncArrowHead of CoverCallExpressionAndAsyncArrowHead.
4. Let parameters be the ArrowFormalParameters of head.
5. Let closure be ! AsyncFunctionCreate(Arrow, parameters, AsyncConciseBody, scope, strict).
6. Return closure.

## 14.9 尾调用 <div id="sec-tail-position-calls"></div>

### 14.9.1 SS: IsInTailPosition ( call ) <div id="sec-isintailposition"></div>

The abstract operation IsInTailPosition with argument call performs the following steps:

1. Assert: call is a Parse Node.
2. If the source code matching call is non-strict code, return false.
3. If call is not contained within a FunctionBody, ConciseBody, or AsyncConciseBody, return false.
4. Let body be the FunctionBody, ConciseBody, or AsyncConciseBody that most closely contains call.
5. If body is the FunctionBody of a GeneratorBody, return false.
6. If body is the FunctionBody of an AsyncFunctionBody, return false.
7. If body is the FunctionBody of an AsyncGeneratorBody, return false.
8. If body is an AsyncConciseBody, return false.
9. Return the result of HasCallInTailPosition of body with argument call.

> NOTE Tail Position calls are only defined in strict mode code because of a common non-standard language extension (see 9.2.9) that enables observation of the chain of caller contexts.

### 14.9.2 SS: HasCallInTailPosition <div id="sec-static-semantics-hascallintailposition"></div>

With parameter call.

> NOTE call is a Parse Node that represents a specific range of source text. When the following algorithms compare call to another Parse Node, it is a test of whether they represent the same source text.

#### 14.9.2.1 Statement Rules <div id="sec-statement-rules"></div>

```
ConciseBody : AssignmentExpression
```

1. Return HasCallInTailPosition of AssignmentExpression with argument call.

```
StatementList : StatementList StatementListItem
```

1. Let has be HasCallInTailPosition of StatementList with argument call.
2. If has is true, return true.
3. Return HasCallInTailPosition of StatementListItem with argument call.

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
ReturnStatement : return ;
LabelledItem : FunctionDeclaration
IterationStatement :
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for ( var ForBinding of AssignmentExpression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
CaseBlock : { }
```

1. Return false.

```
IfStatement : if ( Expression ) Statement else Statement
```

1. Let has be HasCallInTailPosition of the first Statement with argument call.
2. If has is true, return true.
3. Return HasCallInTailPosition of the second Statement with argument call.

```
IfStatement : if ( Expression ) Statement
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

1. Return HasCallInTailPosition of Statement with argument call

   ```
   LabelledStatement :
   LabelIdentifier : LabelledItem
   ```

   1. Return HasCallInTailPosition of LabelledItem with argument call

```
ReturnStatement : return Expression ;
```

1. Return HasCallInTailPosition of Expression with argument call.

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. Return HasCallInTailPosition of CaseBlock with argument call

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. Let has be false.
2. If the first CaseClauses is present, let has be HasCallInTailPosition of the first CaseClauses with argument call.
3. If has is true, return true.
4. Let has be HasCallInTailPosition of the DefaultClause with argument call.
5. If has is true, return true.
6. If the second CaseClauses is present, let has be HasCallInTailPosition of the second CaseClauses with argument call.
7. Return has.

```
CaseClauses : CaseClauses CaseClause
```

1. Let has be HasCallInTailPosition of CaseClauses with argument call.
2. If has is true, return true.
3. Return HasCallInTailPosition of CaseClause with argument call.

```
CaseClause : case Expression : StatementList
DefaultClause : default : StatementList
```

1. If StatementList is present, return HasCallInTailPosition of StatementList with argument call.
2. Return false.

```
TryStatement : try Block Catch
```

1. Return HasCallInTailPosition of Catch with argument call.

```
TryStatement : try Block Finally
TryStatement : try Block Catch Finally
```

1. Return HasCallInTailPosition of Finally with argument call

```
Catch : catch ( CatchParameter ) Block
```

1. Return HasCallInTailPosition of Block with argument call.

#### 14.9.2.2 Expression Rules <div id="sec-expression-rules"></div>

> NOTE A potential tail position call that is immediately followed by return GetValue of the call result is also a possible tail position call. Function calls cannot return reference values, so such a GetValue operation will always return the same value as the actual function call result.

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

1. Return false.

   ```
   Expression :
   AssignmentExpression
   Expression , AssignmentExpression
   ```

   1. Return HasCallInTailPosition of AssignmentExpression with argument call.

```
ConditionalExpression : LogicalORExpression ? AssignmentExpression : AssignmentExpression
```

1. Let has be HasCallInTailPosition of the first AssignmentExpression with argument call.
2. If has is true, return true.
3. Return HasCallInTailPosition of the second AssignmentExpression with argument call.

```
LogicalANDExpression : LogicalANDExpression && BitwiseORExpression
```

1. Return HasCallInTailPosition of BitwiseORExpression with argument call.

```
LogicalORExpression : LogicalORExpression || LogicalANDExpression
```

1. Return HasCallInTailPosition of LogicalANDExpression with argument call.

   ```
   CallExpression :
       CoverCallExpressionAndAsyncArrowHead
       CallExpression Arguments
       CallExpression TemplateLiteral
   ```

   1. If this CallExpression is call, return true.
   2. Return false.

   ```
   MemberExpression :
   	MemberExpression TemplateLiteral
   ```

   1. If this MemberExpression is call, return true.
   2. Return false.

```
PrimaryExpression : CoverParenthesizedExpressionAndArrowParameterList
```

1. Let expr be CoveredParenthesizedExpression of CoverParenthesizedExpressionAndArrowParameterList.

2. Return HasCallInTailPosition of expr with argument call.

   ```
   ParenthesizedExpression :
   ( Expression )
   ```

   1. Return HasCallInTailPosition of Expression with argument call

### 14.9.3 RS: PrepareForTailCall ( ) <div id="sec-preparefortailcall"></div>

The abstract operation PrepareForTailCall performs the following steps:

1. Let leafContext be the running execution context.
2. Suspend leafContext.
3. Pop leafContext from the execution context stack. The execution context now on the top of the stack becomes the
running execution context.
4. Assert: leafContext has no further use. It will never be activated as the running execution context.