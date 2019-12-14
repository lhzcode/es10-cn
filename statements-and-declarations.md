# 13 ECMAScript语言：语句和声明

**语法**

```
Statement[Yield, Await, Return] :
    BlockStatement[?Yield, ?Await, ?Return]
    VariableStatement[?Yield, ?Await]
    EmptyStatement
    ExpressionStatement[?Yield, ?Await]
    IfStatement[?Yield, ?Await, ?Return]
    BreakableStatement[?Yield, ?Await, ?Return]
    ContinueStatement[?Yield, ?Await]
    BreakStatement[?Yield, ?Await]
    [+Return] ReturnStatement[?Yield, ?Await]
    WithStatement[?Yield, ?Await, ?Return]
    LabelledStatement[?Yield, ?Await, ?Return]
    ThrowStatement[?Yield, ?Await]
    TryStatement[?Yield, ?Await, ?Return]
    DebuggerStatement
Declaration[Yield, Await] :
    HoistableDeclaration[?Yield, ?Await, ~Default]
    ClassDeclaration[?Yield, ?Await, ~Default]
    LexicalDeclaration[+In, ?Yield, ?Await]
HoistableDeclaration[Yield, Await, Default] :
    FunctionDeclaration[?Yield, ?Await, ?Default]
    GeneratorDeclaration[?Yield, ?Await, ?Default]
    AsyncFunctionDeclaration[?Yield, ?Await, ?Default]
    AsyncGeneratorDeclaration[?Yield, ?Await, ?Default]
BreakableStatement[Yield, Await, Return] :
    IterationStatement[?Yield, ?Await, ?Return]
    SwitchStatement[?Yield, ?Await, ?Return]
```

## 13.1 语句和声明 <div id="sec-statement-semantics"></div>

### 13.1.1 SS: ContainsDuplicateLabels <div id="sec-statement-semantics-static-semantics-containsduplicatelabels"></div>

带有参数labelSet。

```
Statement :
    VariableStatement
    EmptyStatement
    ExpressionStatement
    ContinueStatement
    BreakStatement
    ReturnStatement
    ThrowStatement
    DebuggerStatement
```

1. Return false.

### 13.1.2 SS: ContainsUndefinedBreakTarget <div id="sec-statement-semantics-static-semantics-containsundefinedbreaktarget"></div>

带有参数labelSet。

```
Statement:
    VariableStatement
    EmptyStatement
    ExpressionStatement
    ContinueStatement
    ReturnStatement
    ThrowStatement
    DebuggerStatement
```

1. Return false.

### 13.1.3 SS: ContainsUndefinedContinueTarget <div id="sec-statement-semantics-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet。

```
Statement:
    VariableStatement
    EmptyStatement
    ExpressionStatement
    BreakStatement
    ReturnStatement
    ThrowStatement
    DebuggerStatement
```

1. Return false.

```
BreakableStatement:IterationStatement
```

1. Let newIterationSet be a copy of iterationSet with all the elements of labelSet appended.
2. Return ContainsUndefinedContinueTarget of IterationStatement with arguments newIterationSet and « ».

### 13.1.4 SS: DeclarationPart <div id="sec-static-semantics-declarationpart"></div>

```
HoistableDeclaration : FunctionDeclaration
```

1. Return FunctionDeclaration.

```
HoistableDeclaration : GeneratorDeclaration
```

1. Return GeneratorDeclaration.

```
HoistableDeclaration : AsyncFunctionDeclaration
```

1. Return AsyncFunctionDeclaration.

```
HoistableDeclaration : AsyncGeneratorDeclaration
```

1. Return AsyncGeneratorDeclaration.

```
Declaration : ClassDeclaration
```

1. Return ClassDeclaration.

```
Declaration : LexicalDeclaration
```

1. Return LexicalDeclaration.

### 13.1.5 SS: VarDeclaredNames <div id="sec-statement-semantics-static-semantics-vardeclarednames"></div>

```
Statement :
EmptyStatement
ExpressionStatement
ContinueStatement
BreakStatement
ReturnStatement
ThrowStatement
DebuggerStatement
```

1. Return a new empty List.

### 13.1.6 SS: VarScopedDeclarations <div id="sec-statement-semantics-static-semantics-varscopeddeclarations"></div>

```
Statement :
EmptyStatement
ExpressionStatement
ContinueStatement
BreakStatement
ReturnStatement
ThrowStatement
DebuggerStatement
```

1. Return a new empty List.

### 13.1.7 RS: LabelledEvaluation <div id="sec-statement-semantics-runtime-semantics-labelledevaluation"></div>

With parameter labelSet.

```
BreakableStatement:IterationStatement
```

1. Let stmtResult be the result of performing LabelledEvaluation of IterationStatement with argument labelSet.
2. If stmtResult.[[Type]] is break, then
a. If stmtResult.[[Target]] is empty, then
i. If stmtResult.[[Value]] is empty, set stmtResult to NormalCompletion(undefined).
ii. Else, set stmtResult to NormalCompletion(stmtResult.[[Value]]).
3. Return Completion(stmtResult).

```
BreakableStatement:SwitchStatement
```

1. Let stmtResult be the result of evaluating SwitchStatement.
2. If stmtResult.[[Type]] is break, then
a. If stmtResult.[[Target]] is empty, then
i. If stmtResult.[[Value]] is empty, set stmtResult to NormalCompletion(undefined).
ii. Else, set stmtResult to NormalCompletion(stmtResult.[[Value]]).
3. Return Completion(stmtResult).

> NOTE A BreakableStatement is one that can be exited via an unlabelled BreakStatement.

### 13.1.8 RS: Evaluation <div id="sec-statement-semantics-runtime-semantics-evaluation"></div>

```
HoistableDeclaration :
GeneratorDeclaration
AsyncFunctionDeclaration
AsyncGeneratorDeclaration
```

1. Return NormalCompletion(empty).

```
HoistableDeclaration : FunctionDeclaration
```

1. Return the result of evaluating FunctionDeclaration.

```
BreakableStatement:
    IterationStatement
    SwitchStatement
```

1. Let newLabelSet be a new empty List.
2. Return the result of performing LabelledEvaluation of this BreakableStatement with argument newLabelSet.

## 13.2 块 <div id="sec-block"></div>

Syntax

```
BlockStatement[Yield, Await, Return] :
    Block[?Yield, ?Await, ?Return]
Block[Yield, Await, Return] :
    { StatementList[?Yield, ?Await, ?Return] opt }
StatementList[Yield, Await, Return] :
    StatementListItem[?Yield, ?Await, ?Return]
    StatementList[?Yield, ?Await, ?Return] StatementListItem[?Yield, ?Await, ?Return]
StatementListItem[Yield, Await, Return] :
    Statement[?Yield, ?Await, ?Return]
    Declaration[?Yield, ?Await]
```

### 13.2.1 SS: Early Errors <div id="sec-block-static-semantics-early-errors"></div>

```
Block : { StatementList }
```

It is a Syntax Error if the LexicallyDeclaredNames of StatementList contains any duplicate entries.
It is a Syntax Error if any element of the LexicallyDeclaredNames of StatementList also occurs in the
VarDeclaredNames of StatementList.

### 13.2.2 SS: ContainsDuplicateLabels <div id="sec-block-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
Block : { }
```

1. Return false.

```
StatementList : StatementList StatementListItem
```

1. Let hasDuplicates be ContainsDuplicateLabels of StatementList with argument labelSet.
2. If hasDuplicates is true, return true.
3. Return ContainsDuplicateLabels of StatementListItem with argument labelSet.

```
StatementListItem : Declaration
```

1. Return false.

### 13.2.3 SS: ContainsUndefinedBreakTarget <div id="sec-block-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
Block : { }
```

1. Return false.

```
StatementList : StatementList StatementListItem
```

1. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of StatementList with argument labelSet.
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedBreakTarget of StatementListItem with argument labelSet.

```
StatementListItem : Declaration
```

1. Return false.

### 13.2.4 SS: ContainsUndefinedContinueTarget <div id="sec-block-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
Block : { }
```

1. Return false.

```
StatementList : StatementList StatementListItem
```

1. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of StatementList with arguments iterationSet and «
».
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedContinueTarget of StatementListItem with arguments iterationSet and « ».

```
StatementListItem : Declaration
```

1. Return false.

### 13.2.5 SS: LexicallyDeclaredNames <div id="sec-block-static-semantics-lexicallydeclarednames"></div>

```
Block : { }
```

1. Return a new empty List.

```
StatementList : StatementList StatementListItem
```

1. Let names be LexicallyDeclaredNames of StatementList.
2. Append to names the elements of the LexicallyDeclaredNames of StatementListItem.
3. Return names.

```
StatementListItem : Statement
```

1. If Statement is Statement : LabelledStatement , return LexicallyDeclaredNames of LabelledStatement.
2. Return a new empty List.

```
StatementListItem : Declaration
```

1. Return the BoundNames of Declaration.

### 13.2.6 SS: LexicallyScopedDeclarations <div id="sec-block-static-semantics-lexicallyscopeddeclarations"></div>

```
StatementList : StatementList StatementListItem
```

1. Let declarations be LexicallyScopedDeclarations of StatementList.
2. Append to declarations the elements of the LexicallyScopedDeclarations of StatementListItem.
3. Return declarations.

```
StatementListItem : Statement
```

1. If Statement is Statement : LabelledStatement , return LexicallyScopedDeclarations of LabelledStatement.
2. Return a new empty List.

```
StatementListItem : Declaration
```

1. Return a new List containing DeclarationPart of Declaration.

### 13.2.7 SS: TopLevelLexicallyDeclaredNames <div id="sec-block-static-semantics-toplevellexicallydeclarednames"></div>

```
StatementList : StatementList StatementListItem
```

1. Let names be TopLevelLexicallyDeclaredNames of StatementList.
2. Append to names the elements of the TopLevelLexicallyDeclaredNames of StatementListItem.
3. Return names.

```
StatementListItem : Statement
```

1. Return a new empty List.

```
StatementListItem : Declaration
```

1. If Declaration is Declaration : HoistableDeclaration , then
a. Return « ».
2. Return the BoundNames of Declaration.

> NOTE At the top level of a function, or script, function declarations are treated like var declarations rather than like lexical declarations.

### 13.2.8 SS: TopLevelLexicallyScopedDeclarations <div id="sec-block-static-semantics-toplevellexicallyscopeddeclarations"></div>

```
Block : { }
```

1. Return a new empty List.

```
StatementList : StatementList StatementListItem
```

1. Let declarations be TopLevelLexicallyScopedDeclarations of StatementList.
2. Append to declarations the elements of the TopLevelLexicallyScopedDeclarations of StatementListItem.
3. Return declarations.

```
StatementListItem : Statement
```

1. Return a new empty List.

```
StatementListItem : Declaration
```

1. If Declaration is Declaration : HoistableDeclaration , then
a. Return « ».
2. Return a new List containing Declaration.

### 13.2.9 SS: TopLevelVarDeclaredNames <div id="sec-block-static-semantics-toplevelvardeclarednames"></div>

```
Block : { }
```

1. Return a new empty List.

```
StatementList : StatementList StatementListItem
```

1. Let names be TopLevelVarDeclaredNames of StatementList.
2. Append to names the elements of the TopLevelVarDeclaredNames of StatementListItem.
3. Return names.

```
StatementListItem : Declaration
```

1. If Declaration is Declaration : HoistableDeclaration , then
a. Return the BoundNames of HoistableDeclaration.
2. Return a new empty List.

```
StatementListItem : Statement
```

1. If Statement is Statement : LabelledStatement , return TopLevelVarDeclaredNames of Statement.
2. Return VarDeclaredNames of Statement.

> NOTE At the top level of a function or script, inner function declarations are treated like var declarations.

### 13.2.10 SS: TopLevelVarScopedDeclarations <div id="sec-block-static-semantics-toplevelvarscopeddeclarations"></div>

```
Block : { }
```

1. Return a new empty List

```
StatementList : StatementList StatementListItem
```

1. Let declarations be TopLevelVarScopedDeclarations of StatementList.
2. Append to declarations the elements of the TopLevelVarScopedDeclarations of StatementListItem.
3. Return declarations.

```
StatementListItem : Statement
```

1. If Statement is Statement : LabelledStatement , return TopLevelVarScopedDeclarations of Statement.
2. Return VarScopedDeclarations of Statement.

```
StatementListItem : Declaration
```

1. If Declaration is Declaration : HoistableDeclaration , then
a. Let declaration be DeclarationPart of HoistableDeclaration.
b. Return « declaration ».
2. Return a new empty List.

### 13.2.11 SS: VarDeclaredNames <div id="sec-block-static-semantics-vardeclarednames"></div>

```
Block : { }
```

1. Return a new empty List.

```
StatementList : StatementList StatementListItem
```

1. Let names be VarDeclaredNames of StatementList.
2. Append to names the elements of the VarDeclaredNames of StatementListItem.
3. Return names.

```
StatementListItem : Declaration
```

1. Return a new empty List.

### 13.2.12 SS: VarScopedDeclarations <div id="sec-block-static-semantics-varscopeddeclarations"></div>

```
Block : { }
```

1. Return a new empty List.

```
StatementList : StatementList StatementListItem
```

1. Let declarations be VarScopedDeclarations of StatementList.
2. Append to declarations the elements of the VarScopedDeclarations of StatementListItem.
3. Return declarations.

```
StatementListItem : Declaration
```

1. Return a new empty List.

### 13.2.13 RS: Evaluation <div id="sec-block-runtime-semantics-evaluation"></div>

```
Block : { }
```

1. Return NormalCompletion(empty)

```
Block : { StatementList }
```

1. Let oldEnv be the running execution context's LexicalEnvironment.
2. Let blockEnv be NewDeclarativeEnvironment(oldEnv).
3. Perform BlockDeclarationInstantiation(StatementList, blockEnv).
4. Set the running execution context's LexicalEnvironment to blockEnv.
5. Let blockValue be the result of evaluating StatementList.
6. Set the running execution context's LexicalEnvironment to oldEnv.
7. Return blockValue.

> note1 No matter how control leaves the Block the LexicalEnvironment is always restored to its former state.

```
StatementList : StatementList StatementListItem
```

1. Let sl be the result of evaluating StatementList.
2. ReturnIfAbrupt(sl).
3. Let s be the result of evaluating StatementListItem.
4. Return Completion(UpdateEmpty(s, sl)).

> NOTE 2 The value of a StatementList is the value of the last value-producing item in the StatementList. For example, the following calls to the eval function all return the value 1:
>
> eval("1;;;;;")
> eval("1;{}")
> eval("1;var a;")

### 13.2.14 RS: BlockDeclarationInstantiation ( code, env ) <div id="sec-blockdeclarationinstantiation"></div>

> NOTE When a Block or CaseBlock is evaluated a new declarative Environment Record is created and bindings for each block scoped variable, constant, function, or class declared in the block are instantiated in the Environment Record.

BlockDeclarationInstantiation is performed as follows using arguments code and env. code is the Parse Node corresponding to the body of the block. env is the Lexical Environment in which bindings are to be created.

1. Let envRec be env's EnvironmentRecord.
2. Assert: envRec is a declarative Environment Record.
3. Let declarations be the LexicallyScopedDeclarations of code.
4. For each element d in declarations, do
a. For each element dn of the BoundNames of d, do
i. If IsConstantDeclaration of d is true, then
1. Perform ! envRec.CreateImmutableBinding(dn, true).
ii. Else,
1. Perform ! envRec.CreateMutableBinding(dn, false).
b. If d is a FunctionDeclaration, a GeneratorDeclaration, an AsyncFunctionDeclaration, or an
AsyncGeneratorDeclaration, then
i. Let fn be the sole element of the BoundNames of d.
ii. Let fo be the result of performing InstantiateFunctionObject for d with argument env.
iii. Perform envRec.InitializeBinding(fn, fo).

## 13.3 声明和变量语句 <div id="sec-declarations-and-the-variable-statement"></div>

### 13.3.1 Let 和 Const 声明 <div id="sec-let-and-const-declarations"></div>

> NOTE let and const declarations define variables that are scoped to the running execution context's LexicalEnvironment.
>
> The variables are created when their containing Lexical Environment is instantiated but may not be accessed in any way until the variable's LexicalBinding is evaluated. A variable defined by a LexicalBinding with an Initializer is assigned the value of its Initializer's AssignmentExpression when the LexicalBinding is evaluated, not when the variable is created. If a LexicalBinding in a let declaration does not have an Initializer the variable is assigned the value undefined when the LexicalBinding is evaluated.

Syntax

```
LexicalDeclaration[In, Yield, Await] :
    LetOrConst BindingList[?In, ?Yield, ?Await] ;
LetOrConst :
    let
    const
BindingList[In, Yield, Await] :
    LexicalBinding[?In, ?Yield, ?Await]
    BindingList[?In, ?Yield, ?Await] , LexicalBinding[?In, ?Yield, ?Await]
LexicalBinding[In, Yield, Await] :
    BindingIdentifier[?Yield, ?Await] Initializer[?In, ?Yield, ?Await] opt
    BindingPattern[?Yield, ?Await] Initializer[?In, ?Yield, ?Await]
```

#### 13.3.1.1 SS: Early Errors <div id="sec-let-and-const-declarations-static-semantics-early-errors"></div>

```
LexicalDeclaration : LetOrConst BindingList ;
```

It is a Syntax Error if the BoundNames of BindingList contains "let".
It is a Syntax Error if the BoundNames of BindingList contains any duplicate entries.

```
LexicalBinding : BindingIdentifier Initializer
```

It is a Syntax Error if Initializer is not present and IsConstantDeclaration of the LexicalDeclaration containing this LexicalBinding is true.

#### 13.3.1.2 SS: BoundNames <div id="sec-let-and-const-declarations-static-semantics-boundnames"></div>

```
LexicalDeclaration : LetOrConst BindingList ;
```

1. Return the BoundNames of BindingList.

```
BindingList : BindingList , LexicalBinding
```

1. Let names be the BoundNames of BindingList.
2. Append to names the elements of the BoundNames of LexicalBinding.
3. Return names

```
LexicalBinding : BindingIdentifier Initializer
```

1. Return the BoundNames of BindingIdentifier.

```
LexicalBinding : BindingPattern Initializer
```

1. Return the BoundNames of BindingPattern.

#### 13.3.1.3 SS: IsConstantDeclaration <div id="sec-let-and-const-declarations-static-semantics-isconstantdeclaration"></div>

```
LexicalDeclaration : LetOrConst BindingList ;
```

1. Return IsConstantDeclaration of LetOrConst.

```
LetOrConst : let
```

1. Return false

```
LetOrConst : const
```

1. Return true.

#### 13.3.1.4 RS: Evaluation <div id="sec-let-and-const-declarations-runtime-semantics-evaluation"></div>

```
LexicalDeclaration : LetOrConst BindingList ;
```

1. Let next be the result of evaluating BindingList.
2. ReturnIfAbrupt(next).
3. Return NormalCompletion(empty).

```
BindingList : BindingList , LexicalBinding
```

1. Let next be the result of evaluating BindingList.
2. ReturnIfAbrupt(next).
3. Return the result of evaluating LexicalBinding.

```
LexicalBinding : BindingIdentifier
```

1. Let lhs be ResolveBinding(StringValue of BindingIdentifier).
2. Return InitializeReferencedBinding(lhs, undefined).

> NOTE A static semantics rule ensures that this form of LexicalBinding never occurs in a const declaration.

```
LexicalBinding : BindingIdentifier Initializer
```

1. Let bindingId be StringValue of BindingIdentifier.
2. Let lhs be ResolveBinding(bindingId).
3. If IsAnonymousFunctionDefinition(Initializer) is true, then
a. Let value be the result of performing NamedEvaluation for Initializer with argument bindingId.
4. Else,
a. Let rhs be the result of evaluating Initializer.
b. Let value be ? GetValue(rhs).
5. Return InitializeReferencedBinding(lhs, value).

```
LexicalBinding : BindingPattern Initializer
```

1. Let rhs be the result of evaluating Initializer.
2. Let value be ? GetValue(rhs).
3. Let env be the running execution context's LexicalEnvironment.

4. Return the result of performing BindingInitialization for BindingPattern using value and env as the arguments.

### 13.3.2 变量语句 <div id="sec-variable-statement"></div>

> NOTE A var statement declares variables that are scoped to the running execution context's VariableEnvironment. Var variables are created when their containing Lexical Environment is instantiated and are initialized to undefined when created. Within the scope of any VariableEnvironment a common BindingIdentifier may appear in more than one VariableDeclaration but those declarations collectively define only one variable. A variable defined by a VariableDeclaration with an Initializer is assigned the value of its Initializer's AssignmentExpression when the VariableDeclaration is executed, not when the variable is created.

Syntax

```
VariableStatement[Yield, Await] :
    var VariableDeclarationList[+In, ?Yield, ?Await] ;
VariableDeclarationList[In, Yield, Await] :
    VariableDeclaration[?In, ?Yield, ?Await]
    VariableDeclarationList[?In, ?Yield, ?Await] , VariableDeclaration[?In, ?Yield, ?Await]
VariableDeclaration[In, Yield, Await] :
    BindingIdentifier[?Yield, ?Await] Initializer[?In, ?Yield, ?Await] opt
    BindingPattern[?Yield, ?Await] Initializer[?In, ?Yield, ?Await]
```

#### 13.3.2.1 SS: BoundNames <div id="sec-variable-statement-static-semantics-boundnames"></div>

```
VariableDeclarationList : VariableDeclarationList , VariableDeclaration
```

1. Let names be BoundNames of VariableDeclarationList.
2. Append to names the elements of BoundNames of VariableDeclaration.
3. Return names.

```
VariableDeclaration : BindingIdentifier Initializer
```

1. Return the BoundNames of BindingIdentifier.

```
VariableDeclaration : BindingPattern Initializer
```

1. Return the BoundNames of BindingPattern.

#### 13.3.2.2 SS: VarDeclaredNames <div id="sec-variable-statement-static-semantics-vardeclarednames"></div>

```
VariableStatement : var VariableDeclarationList ;
```

1. Return BoundNames of VariableDeclarationList.

#### 13.3.2.3 SS: VarScopedDeclarations <div id="sec-variable-statement-static-semantics-varscopeddeclarations"></div>

```
VariableDeclarationList : VariableDeclaration
```

1. Return a new List containing VariableDeclaration.

```
VariableDeclarationList : VariableDeclarationList , VariableDeclaration
```

1. Let declarations be VarScopedDeclarations of VariableDeclarationList.
2. Append VariableDeclaration to declarations.
3. Return declarations.

#### 13.3.2.4 RS: Evaluation <div id="sec-variable-statement-runtime-semantics-evaluation"></div>

```
VariableStatement : var VariableDeclarationList ;
```

1. Let next be the result of evaluating VariableDeclarationList.
2. ReturnIfAbrupt(next).
3. Return NormalCompletion(empty)

```
VariableDeclarationList : VariableDeclarationList , VariableDeclaration
```

1. Let next be the result of evaluating VariableDeclarationList.
2. ReturnIfAbrupt(next).
3. Return the result of evaluating VariableDeclaration

```
VariableDeclaration : BindingIdentifier
```

1. Return NormalCompletion(empty).

```
VariableDeclaration : BindingIdentifier Initializer
```

1. Let bindingId be StringValue of BindingIdentifier.
2. Let lhs be ? ResolveBinding(bindingId).
3. If IsAnonymousFunctionDefinition(Initializer) is true, then
a. Let value be the result of performing NamedEvaluation for Initializer with argument bindingId.
4. Else,
a. Let rhs be the result of evaluating Initializer.
b. Let value be ? GetValue(rhs).
5. Return ? PutValue(lhs, value).

> NOTE If a VariableDeclaration is nested within a with statement and the BindingIdentifier in the VariableDeclaration is the same as a property name of the binding object of the with statement's object Environment Record, then step 6 will assign value to the property instead of assigning to the VariableEnvironment binding of the Identifier.

```
VariableDeclaration : BindingPattern Initializer
```

1. Let rhs be the result of evaluating Initializer.
2. Let rval be ? GetValue(rhs).
3. Return the result of performing BindingInitialization for BindingPattern passing rval and undefined as arguments.

### 13.3.3 绑定模式解构 <div id="sec-destructuring-binding-patterns"></div>

Syntax

```
BindingPattern[Yield, Await] :
    ObjectBindingPattern[?Yield, ?Await]
    ArrayBindingPattern[?Yield, ?Await]
ObjectBindingPattern[Yield, Await] :
    { }
    { BindingRestProperty[?Yield, ?Await] }
    { BindingPropertyList[?Yield, ?Await] }
    { BindingPropertyList[?Yield, ?Await] , BindingRestProperty[?Yield, ?Await] opt }
ArrayBindingPattern[Yield, Await] :
    [ Elisionopt BindingRestElement[?Yield, ?Await] opt ]
    [ BindingElementList[?Yield, ?Await] ]
    [ BindingElementList[?Yield, ?Await] , Elisionopt BindingRestElement[?Yield, ?Await] opt ]
BindingRestProperty[Yield, Await] :
    ... BindingIdentifier[?Yield, ?Await]
BindingPropertyList[Yield, Await] :
    BindingProperty[?Yield, ?Await]
    BindingPropertyList[?Yield, ?Await] , BindingProperty[?Yield, ?Await]
BindingElementList[Yield, Await] :
    BindingElisionElement[?Yield, ?Await]
    BindingElementList[?Yield, ?Await] , BindingElisionElement[?Yield, ?Await]
BindingElisionElement[Yield, Await] :
    Elisionopt BindingElement[?Yield, ?Await]
BindingProperty[Yield, Await] :
    SingleNameBinding[?Yield, ?Await]
    PropertyName[?Yield, ?Await] : BindingElement[?Yield, ?Await]
BindingElement[Yield, Await] :
    SingleNameBinding[?Yield, ?Await]
    BindingPattern[?Yield, ?Await] Initializer[+In, ?Yield, ?Await] opt
SingleNameBinding[Yield, Await] :
    BindingIdentifier[?Yield, ?Await] Initializer[+In, ?Yield, ?Await] opt
BindingRestElement[Yield, Await] :
    ... BindingIdentifier[?Yield, ?Await]
    ... BindingPattern[?Yield, ?Await]
```

#### 13.3.3.1 SS: BoundNames <div id="sec-destructuring-binding-patterns-static-semantics-boundnames"></div>

```
ObjectBindingPattern : { }
```

1. Return a new empty List.

```
ArrayBindingPattern : [ Elision ]
```

1. Return a new empty List.

```
ArrayBindingPattern : [ Elision BindingRestElement ]
```

1. Return the BoundNames of BindingRestElement.

```
ArrayBindingPattern : [ BindingElementList , Elision ]
```

1. Return the BoundNames of BindingElementList.

```
ArrayBindingPattern : [ BindingElementList , Elision BindingRestElement ]
```

1. Let names be BoundNames of BindingElementList.
2. Append to names the elements of BoundNames of BindingRestElement.
3. Return names.

```
BindingPropertyList : BindingPropertyList , BindingProperty
```

1. Let names be BoundNames of BindingPropertyList.
2. Append to names the elements of BoundNames of BindingProperty.
3. Return names.

```
BindingElementList : BindingElementList , BindingElisionElement
```

1. Let names be BoundNames of BindingElementList.
2. Append to names the elements of BoundNames of BindingElisionElement.
3. Return names.

```
BindingElisionElement : Elision BindingElement
```

1. Return BoundNames of BindingElement.

```
BindingProperty : PropertyName : BindingElement
```

1. Return the BoundNames of BindingElement.

```
SingleNameBinding : BindingIdentifier Initializer
```

1. Return the BoundNames of BindingIdentifier.

```
BindingElement : BindingPattern Initializer
```

1. Return the BoundNames of BindingPattern.

#### 13.3.3.2 SS: ContainsExpression <div id="sec-destructuring-binding-patterns-static-semantics-containsexpression"></div>

```
ObjectBindingPattern : { }
```

1. Return false.

```
ArrayBindingPattern : [ Elision ]
```

1. Return false.

```
ArrayBindingPattern : [ Elision BindingRestElement ]
```

1. Return ContainsExpression of BindingRestElement.

```
ArrayBindingPattern : [ BindingElementList , Elision ]
```

1. Return ContainsExpression of BindingElementList.

```
ArrayBindingPattern : [ BindingElementList , Elision BindingRestElement ]
```

1. Let has be ContainsExpression of BindingElementList.
2. If has is true, return true.
3. Return ContainsExpression of BindingRestElement.

```
BindingPropertyList : BindingPropertyList , BindingProperty
```

1. Let has be ContainsExpression of BindingPropertyList.
2. If has is true, return true.
3. Return ContainsExpression of BindingProperty.

```
BindingElementList : BindingElementList , BindingElisionElement
```

1. Let has be ContainsExpression of BindingElementList.
2. If has is true, return true.
3. Return ContainsExpression of BindingElisionElement.

```
BindingElisionElement : Elision BindingElement
```

1. Return ContainsExpression of BindingElement.

```
BindingProperty : PropertyName : BindingElement
```

1. Let has be IsComputedPropertyKey of PropertyName.
2. If has is true, return true.
3. Return ContainsExpression of BindingElement.

```
BindingElement : BindingPattern Initializer
```

1. Return true.

```
SingleNameBinding : BindingIdentifier
```

1. Return false.

```
SingleNameBinding : BindingIdentifier Initializer
```

1. Return true.

```
BindingRestElement : ... BindingIdentifier
```

1. Return false.

```
BindingRestElement : ... BindingPattern
```

1. Return ContainsExpression of BindingPattern

#### 13.3.3.3 SS: HasInitializer <div id="sec-destructuring-binding-patterns-static-semantics-hasinitializer"></div>

```
BindingElement : BindingPattern
```

1. Return false

```
BindingElement : BindingPattern Initializer
```

1. Return true.

```
SingleNameBinding : BindingIdentifier
```

1. Return false.

```
SingleNameBinding : BindingIdentifier Initializer
```

1. Return true.

#### 13.3.3.4 SS: IsSimpleParameterList <div id="sec-destructuring-binding-patterns-static-semantics-issimpleparameterlist"></div>

```
BindingElement : BindingPattern
```

1. Return false.

```
BindingElement : BindingPattern Initializer
```

1. Return false.

```
SingleNameBinding : BindingIdentifier
```

1. Return true

```
SingleNameBinding : BindingIdentifier Initializer
```

1. Return false.

#### 13.3.3.5 RS: BindingInitialization <div id="sec-destructuring-binding-patterns-runtime-semantics-bindinginitialization"></div>

With parameters value and environment.

> NOTE When undefined is passed for environment it indicates that a PutValue operation should be used to assign the initialization value. This is the case for formal parameter lists of non-strict functions. In that case the formal parameter bindings are preinitialized in order to deal with the possibility of multiple parameters with the same name.

```
BindingPattern : ObjectBindingPattern
```

1. Perform ? RequireObjectCoercible(value).
2. Return the result of performing BindingInitialization for ObjectBindingPattern using value and environment as arguments.

```
BindingPattern : ArrayBindingPattern
```

1. Let iteratorRecord be ? GetIterator(value).
2. Let result be IteratorBindingInitialization for ArrayBindingPattern using iteratorRecord and environment as
arguments.
3. If iteratorRecord.[[Done]] is false, return ? IteratorClose(iteratorRecord, result).

4. Return result.

```
ObjectBindingPattern : { }
```

1. Return NormalCompletion(empty)

   ```
   ObjectBindingPattern :
       { BindingPropertyList }
       { BindingPropertyList , }
   ```

   1. Perform ? PropertyBindingInitialization for BindingPropertyList using value and environment as the arguments.
   2. Return NormalCompletion(empty).

```
ObjectBindingPattern : { BindingRestProperty }
```

1. Let excludedNames be a new empty List.
2. Return the result of performing RestBindingInitialization of BindingRestProperty with value, environment, and excludedNames as the arguments.

```
ObjectBindingPattern : { BindingPropertyList , BindingRestProperty }
```

1. Let excludedNames be the result of performing ? PropertyBindingInitialization of BindingPropertyList using value and environment as arguments.
2. Return the result of performing RestBindingInitialization of BindingRestProperty with value, environment, and excludedNames as the arguments.

#### 13.3.3.6 RS: PropertyBindingInitialization <div id="sec-destructuring-binding-patterns-runtime-semantics-propertybindinginitialization"></div>

With parameters value and environment.

>NOTE These collect a list of all bound property names rather than just empty completion.

```
BindingPropertyList : BindingPropertyList , BindingProperty
```

1. Let boundNames be the result of performing ? PropertyBindingInitialization for BindingPropertyList using value
and environment as arguments.
2. Let nextNames be the result of performing ? PropertyBindingInitialization for BindingProperty using value and
environment as arguments.
3. Append each item in nextNames to the end of boundNames.
4. Return boundNames.

```
BindingProperty : SingleNameBinding
```

1. Let name be the string that is the only element of BoundNames of SingleNameBinding.
2. Perform ? KeyedBindingInitialization for SingleNameBinding using value, environment, and name as the
arguments.
3. Return a new List containing name.

```
BindingProperty : PropertyName : BindingElement
```

1. Let P be the result of evaluating PropertyName.
2. ReturnIfAbrupt(P).

3. Perform ? KeyedBindingInitialization of BindingElement with value, environment, and P as the arguments.
4. Return a new List containing P.

#### 13.3.3.7 RS: RestBindingInitialization <div id="sec-destructuring-binding-patterns-runtime-semantics-restbindinginitialization"></div>

With parameters value, environment, and excludedNames.

```
BindingRestProperty : ... BindingIdentifier
```

1. Let lhs be ? ResolveBinding(StringValue of BindingIdentifier, environment).
2. Let restObj be ObjectCreate(%ObjectPrototype%).
3. Perform ? CopyDataProperties(restObj, value, excludedNames).
4. If environment is undefined, return PutValue(lhs, restObj).
5. Return InitializeReferencedBinding(lhs, restObj).

#### 13.3.3.8 RS: IteratorBindingInitialization <div id="sec-destructuring-binding-patterns-runtime-semantics-iteratorbindinginitialization"></div>

With parameters iteratorRecord and environment.

>NOTE When undefined is passed for environment it indicates that a PutValue operation should be used to assign the initialization value. This is the case for formal parameter lists of non-strict functions. In that case the formal parameter bindings are preinitialized in order to deal with the possibility of multiple parameters with the same name.

```
ArrayBindingPattern : [ ]
```

1. Return NormalCompletion(empty).

```
ArrayBindingPattern : [ Elision ]
```

1. Return the result of performing IteratorDestructuringAssignmentEvaluation of Elision with iteratorRecord as the argument.

```
ArrayBindingPattern : [ Elision BindingRestElement ]
```

1. If Elision is present, then
a. Perform ? IteratorDestructuringAssignmentEvaluation of Elision with iteratorRecord as the argument.
2. Return the result of performing IteratorBindingInitialization for BindingRestElement with iteratorRecord and environment as arguments.

```
ArrayBindingPattern : [ BindingElementList ]
```

1. Return the result of performing IteratorBindingInitialization for BindingElementList with iteratorRecord and
environment as arguments.

```
ArrayBindingPattern : [ BindingElementList , ]
```

1. Return the result of performing IteratorBindingInitialization for BindingElementList with iteratorRecord and environment as arguments.

```
ArrayBindingPattern : [ BindingElementList , Elision ]
```

1. Perform ? IteratorBindingInitialization for BindingElementList with iteratorRecord and environment as arguments.

2. Return the result of performing IteratorDestructuringAssignmentEvaluation of Elision with iteratorRecord as the argument.

```
ArrayBindingPattern : [ BindingElementList , Elision BindingRestElement ]
```

1. Perform ? IteratorBindingInitialization for BindingElementList with iteratorRecord and environment as arguments.
2. If Elision is present, then
a. Perform ? IteratorDestructuringAssignmentEvaluation of Elision with iteratorRecord as the argument.
3. Return the result of performing IteratorBindingInitialization for BindingRestElement with iteratorRecord and environment as arguments.

```
BindingElementList : BindingElisionElement
```

1. Return the result of performing IteratorBindingInitialization for BindingElisionElement with iteratorRecord and environment as arguments.

```
BindingElementList : BindingElementList , BindingElisionElement
```

1. Perform ? IteratorBindingInitialization for BindingElementList with iteratorRecord and environment as arguments.
2. Return the result of performing IteratorBindingInitialization for BindingElisionElement using iteratorRecord and environment as arguments.

```
BindingElisionElement : BindingElement
```

1. Return the result of performing IteratorBindingInitialization of BindingElement with iteratorRecord and
environment as the arguments.

```
BindingElisionElement : Elision BindingElement
```

1. Perform ? IteratorDestructuringAssignmentEvaluation of Elision with iteratorRecord as the argument.
2. Return the result of performing IteratorBindingInitialization of BindingElement with iteratorRecord and
environment as the arguments.

```
BindingElement : SingleNameBinding
```

1. Return the result of performing IteratorBindingInitialization for SingleNameBinding with iteratorRecord and environment as the arguments.

```
SingleNameBinding : BindingIdentifier Initializer
```

1. Let bindingId be StringValue of BindingIdentifier.
2. Let lhs be ? ResolveBinding(bindingId, environment).
3. If iteratorRecord.[[Done]] is false, then
a. Let next be IteratorStep(iteratorRecord).
b. If next is an abrupt completion, set iteratorRecord.[[Done]] to true.
c. ReturnIfAbrupt(next).
d. If next is false, set iteratorRecord.[[Done]] to true.
e. Else,
i. Let v be IteratorValue(next).
ii. If v is an abrupt completion, set iteratorRecord.[[Done]] to true.
iii. ReturnIfAbrupt(v).
4. If iteratorRecord.[[Done]] is true, let v be undefined.
5. If Initializer is present and v is undefined, then

   a. If IsAnonymousFunctionDefinition(Initializer) is true, then
   i. Set v to the result of performing NamedEvaluation for Initializer with argument bindingId.
   b. Else,
   i. Let defaultValue be the result of evaluating Initializer.
   ii. Set v to ? GetValue(defaultValue).

6. If environment is undefined, return ? PutValue(lhs, v).
7. Return InitializeReferencedBinding(lhs, v).

```
BindingElement : BindingPattern Initializer
```

1. If iteratorRecord.[[Done]] is false, then
a. Let next be IteratorStep(iteratorRecord).
b. If next is an abrupt completion, set iteratorRecord.[[Done]] to true.
c. ReturnIfAbrupt(next).
d. If next is false, set iteratorRecord.[[Done]] to true.
e. Else,
i. Let v be IteratorValue(next).
ii. If v is an abrupt completion, set iteratorRecord.[[Done]] to true.
iii. ReturnIfAbrupt(v).
2. If iteratorRecord.[[Done]] is true, let v be undefined.
3. If Initializer is present and v is undefined, then
a. Let defaultValue be the result of evaluating Initializer.
b. Set v to ? GetValue(defaultValue).
4. Return the result of performing BindingInitialization of BindingPattern with v and environment as the arguments.

```
BindingRestElement : ... BindingIdentifier
```

1. Let lhs be ? ResolveBinding(StringValue of BindingIdentifier, environment).
2. Let A be ! ArrayCreate(0).
3. Let n be 0.
4. Repeat,
a. If iteratorRecord.[[Done]] is false, then
i. Let next be IteratorStep(iteratorRecord).
ii. If next is an abrupt completion, set iteratorRecord.[[Done]] to true.
iii. ReturnIfAbrupt(next).
iv. If next is false, set iteratorRecord.[[Done]] to true.
b. If iteratorRecord.[[Done]] is true, then
i. If environment is undefined, return ? PutValue(lhs, A).
ii. Return InitializeReferencedBinding(lhs, A).
c. Let nextValue be IteratorValue(next).
d. If nextValue is an abrupt completion, set iteratorRecord.[[Done]] to true.
e. ReturnIfAbrupt(nextValue).
f. Let status be CreateDataProperty(A, ! ToString(n), nextValue).
g. Assert: status is true.
h. Increment n by 1.

```
BindingRestElement : ... BindingPattern
```

1. Let A be ! ArrayCreate(0).
2. Let n be 0.

3. Repeat,
a. If iteratorRecord.[[Done]] is false, then
i. Let next be IteratorStep(iteratorRecord).
ii. If next is an abrupt completion, set iteratorRecord.[[Done]] to true.
iii. ReturnIfAbrupt(next).
iv. If next is false, set iteratorRecord.[[Done]] to true.
b. If iteratorRecord.[[Done]] is true, then
i. Return the result of performing BindingInitialization of BindingPattern with A and environment as the
arguments.
c. Let nextValue be IteratorValue(next).
d. If nextValue is an abrupt completion, set iteratorRecord.[[Done]] to true.
e. ReturnIfAbrupt(nextValue).
f. Let status be CreateDataProperty(A, ! ToString(n), nextValue).
g. Assert: status is true.
h. Increment n by 1.

#### 13.3.3.9 RS: KeyedBindingInitialization <div id="sec-runtime-semantics-keyedbindinginitialization"></div>

With parameters value, environment, and propertyName.

> NOTE When undefined is passed for environment it indicates that a PutValue operation should be used to assign the initialization value. This is the case for formal parameter lists of non-strict functions. In that case the formal parameter bindings are preinitialized in order to deal with the possibility of multiple parameters with the same name.

```
BindingElement : BindingPattern Initializer
```

1. Let v be ? GetV(value, propertyName).
2. If Initializer is present and v is undefined, then
a. Let defaultValue be the result of evaluating Initializer.
b. Set v to ? GetValue(defaultValue).
3. Return the result of performing BindingInitialization for BindingPattern passing v and environment as arguments.

```
SingleNameBinding : BindingIdentifier Initializer
```

1. Let bindingId be StringValue of BindingIdentifier.
2. Let lhs be ? ResolveBinding(bindingId, environment).
3. Let v be ? GetV(value, propertyName).
4. If Initializer is present and v is undefined, then
a. If IsAnonymousFunctionDefinition(Initializer) is true, then
i. Set v to the result of performing NamedEvaluation for Initializer with argument bindingId.
b. Else,
i. Let defaultValue be the result of evaluating Initializer.
ii. Set v to ? GetValue(defaultValue).
5. If environment is undefined, return ? PutValue(lhs, v).
6. Return InitializeReferencedBinding(lhs, v).

## 13.4 空语句 <div id="sec-empty-statement"></div>

Syntax

```
EmptyStatement :
;
```

### 13.4.1 RS: Evaluation <div id="sec-empty-statement-runtime-semantics-evaluation"></div>

```
EmptyStatement : ;
```

1. Return NormalCompletion(empty).

## 13.5 表达式语句 <div id="sec-expression-statement"></div>

Syntax

```
ExpressionStatement[Yield, Await] :
    [lookahead ∉ { { , function , async [no LineTerminator here] function , class , let [ }]
        Expression[+In, ?Yield, ?Await] ;
```

>NOTE
>An ExpressionStatement cannot start with a U+007B (LEFT CURLY BRACKET) because that might make it ambiguous with a Block. An ExpressionStatement cannot start with the function or class keywords because that would make it ambiguous with a FunctionDeclaration, a GeneratorDeclaration, or a ClassDeclaration. An ExpressionStatement cannot start with async function because that would make it ambiguous with an AsyncFunctionDeclaration or a AsyncGeneratorDeclaration. An ExpressionStatement cannot start with the two token sequence let [ because that would make it ambiguous with a let LexicalDeclaration whose first LexicalBinding was an ArrayBindingPattern.

### 13.5.1 RS: Evaluation <div id="sec-expression-statement-runtime-semantics-evaluation"></div>

```
ExpressionStatement : Expression ;
```

1. Let exprRef be the result of evaluating Expression.
2. Return ? GetValue(exprRef).

## 13.6 if 语句 <div id="sec-if-statement"></div>

Syntax

```
IfStatement[Yield, Await, Return] :
    if ( Expression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return] else
    Statement[?Yield, ?Await, ?Return]
    if ( Expression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
```

Each else for which the choice of associated if is ambiguous shall be associated with the nearest possible if that would otherwise have no corresponding else.

### 13.6.1 SS: Early Errors <div id="sec-if-statement-static-semantics-early-errors"></div>

```
IfStatement :
    if ( Expression ) Statement else Statement
    if ( Expression ) Statement
```

It is a Syntax Error if IsLabelledFunction(Statement) is true.

> NOTE It is only necessary to apply this rule if the extension specified in B.3.2 is implemented.

### 13.6.2 SS: ContainsDuplicateLabels <div id="sec-if-statement-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
IfStatement : if ( Expression ) Statement else Statement
```

1. Let hasDuplicate be ContainsDuplicateLabels of the first Statement with argument labelSet.
2. If hasDuplicate is true, return true.
3. Return ContainsDuplicateLabels of the second Statement with argument labelSet.

```
IfStatement : if ( Expression ) Statement
```

1. Return ContainsDuplicateLabels of Statement with argument labelSet.

### 13.6.3 SS: ContainsUndefinedBreakTarget <div id="sec-if-statement-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
IfStatement : if ( Expression ) Statement else Statement
```

1. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of the first Statement with argument labelSet.
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedBreakTarget of the second Statement with argument labelSet.

```
IfStatement : if ( Expression ) Statement
```

1. Return ContainsUndefinedBreakTarget of Statement with argument labelSet.

### 13.6.4 SS: ContainsUndefinedContinueTarget <div id="sec-if-statement-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
IfStatement : if ( Expression ) Statement else Statement
```

1. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of the first Statement with arguments iterationSet
and « ».
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedContinueTarget of the second Statement with arguments iterationSet and « ».

```
IfStatement : if ( Expression ) Statement
```

1. Return ContainsUndefinedContinueTarget of Statement with arguments iterationSet and « ».

### 13.6.5 SS: VarDeclaredNames <div id="sec-if-statement-static-semantics-vardeclarednames"></div>

```
IfStatement : if ( Expression ) Statement else Statement
```

1. Let names be VarDeclaredNames of the first Statement.
2. Append to names the elements of the VarDeclaredNames of the second Statement.
3. Return names.

```
IfStatement : if ( Expression ) Statement
```

1. Return the VarDeclaredNames of Statement.

### 13.6.6 SS: VarScopedDeclarations <div id="sec-if-statement-static-semantics-varscopeddeclarations"></div>

```
IfStatement : if ( Expression ) Statement else Statement
```

1. Let declarations be VarScopedDeclarations of the first Statement.
2. Append to declarations the elements of the VarScopedDeclarations of the second Statement.
3. Return declarations.

```
IfStatement : if ( Expression ) Statement
```

1. Return the VarScopedDeclarations of Statement.

### 13.6.7 RS: Evaluation <div id="sec-if-statement-runtime-semantics-evaluation"></div>

```
IfStatement : if ( Expression ) Statement else Statement
```

1. Let exprRef be the result of evaluating Expression.
2. Let exprValue be ToBoolean(? GetValue(exprRef)).
3. If exprValue is true, then
a. Let stmtCompletion be the result of evaluating the first Statement.
4. Else,
a. Let stmtCompletion be the result of evaluating the second Statement.
5. Return Completion(UpdateEmpty(stmtCompletion, undefined))

```
IfStatement : if ( Expression ) Statement
```

1. Let exprRef be the result of evaluating Expression.
2. Let exprValue be ToBoolean(? GetValue(exprRef)).
3. If exprValue is false, then
a. Return NormalCompletion(undefined).
4. Else,
a. Let stmtCompletion be the result of evaluating Statement.
b. Return Completion(UpdateEmpty(stmtCompletion, undefined)).

## 13.7 迭代语句 <div id="sec-iteration-statements"></div>

Syntax

```
IterationStatement[Yield, Await, Return] :
    do Statement[?Yield, ?Await, ?Return] while ( Expression[+In, ?Yield, ?Await] ) ;
    while ( Expression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
    for ( [lookahead ∉ { let [ }] Expression[~In, ?Yield, ?Await] opt ;
    Expression[+In, ?Yield, ?Await] opt ; Expression[+In, ?Yield, ?Await] opt )
    Statement[?Yield, ?Await, ?Return]
    for ( var VariableDeclarationList[~In, ?Yield, ?Await] ;
    Expression[+In, ?Yield, ?Await] opt ; Expression[+In, ?Yield, ?Await] opt )
    Statement[?Yield, ?Await, ?Return]
    for ( LexicalDeclaration[~In, ?Yield, ?Await] Expression[+In, ?Yield, ?Await] opt ;
    Expression[+In, ?Yield, ?Await] opt ) Statement[?Yield, ?Await, ?Return]
    for ( [lookahead ∉ { let [ }] LeftHandSideExpression[?Yield, ?Await] in
    Expression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
    for ( var ForBinding[?Yield, ?Await] in Expression[+In, ?Yield, ?Await] )
    Statement[?Yield, ?Await, ?Return]
    for ( ForDeclaration[?Yield, ?Await] in Expression[+In, ?Yield, ?Await] )
    Statement[?Yield, ?Await, ?Return]
    for ( [lookahead ≠ let] LeftHandSideExpression[?Yield, ?Await] of
    AssignmentExpression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
    for ( var ForBinding[?Yield, ?Await] of AssignmentExpression[+In, ?Yield, ?Await] )
    Statement[?Yield, ?Await, ?Return]
    for ( ForDeclaration[?Yield, ?Await] of AssignmentExpression[+In, ?Yield, ?Await] )
    Statement[?Yield, ?Await, ?Return]
    [+Await] for await ( [lookahead ≠ let] LeftHandSideExpression[?Yield, ?Await] of
    AssignmentExpression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
    [+Await] for await ( var ForBinding[?Yield, ?Await] of
    AssignmentExpression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
    [+Await] for await ( ForDeclaration[?Yield, ?Await] of
    AssignmentExpression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
ForDeclaration[Yield, Await] :
    LetOrConst ForBinding[?Yield, ?Await]
ForBinding[Yield, Await] :
    BindingIdentifier[?Yield, ?Await]
    BindingPattern[?Yield, ?Await]

```

> NOTE This section is extended by Annex B.3.6

### 13.7.1 语义 <div id="sec-iteration-statements-semantics"></div>
#### 13.7.1.1 SS: Early Errors <div id="sec-semantics-static-semantics-early-errors"></div>

```
IterationStatement :
    do Statement while ( Expression ) ;
    while ( Expression ) Statement
    for ( Expressionopt ; Expressionopt ; Expressionopt ) Statement
    for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
    for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement
    for ( LeftHandSideExpression in Expression ) Statement
    for ( var ForBinding in Expression ) Statement
    for ( ForDeclaration in Expression ) Statement
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for ( var ForBinding of AssignmentExpression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
    for await ( LeftHandSideExpression of AssignmentExpression ) Statement
    for await ( var ForBinding of AssignmentExpression ) Statement
    for await ( ForDeclaration of AssignmentExpression ) Statement
```

It is a Syntax Error if IsLabelledFunction(Statement) is true.

> NOTE It is only necessary to apply this rule if the extension specified in B.3.2 is implemented.

#### 13.7.1.2 RS: LoopContinues ( completion, labelSet ) <div id="sec-loopcontinues"></div>

The abstract operation LoopContinues with arguments completion and labelSet is defined by the following steps:

1. If completion.[[Type]] is normal, return true.
2. If completion.[[Type]] is not continue, return false.
3. If completion.[[Target]] is empty, return true.
4. If completion.[[Target]] is an element of labelSet, return true.
5. Return false.

> NOTE Within the Statement part of an IterationStatement a ContinueStatement may be used to begin a new iteration.

### 13.7.2 do-while 语句 <div id="sec-do-while-statement"></div>

#### 13.7.2.1 SS: ContainsDuplicateLabels <div id="sec-do-while-statement-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
IterationStatement : do Statement while ( Expression ) ;
```

1. Return ContainsDuplicateLabels of Statement with argument labelSet.

#### 13.7.2.2 SS: ContainsUndefinedBreakTarget <div id="sec-do-while-statement-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
IterationStatement : do Statement while ( Expression ) ;
```

1. Return ContainsUndefinedBreakTarget of Statement with argument labelSet.

#### 13.7.2.3 SS: ContainsUndefinedContinueTarget <div id="sec-do-while-statement-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
IterationStatement : do Statement while ( Expression ) ;
```

1. Return ContainsUndefinedContinueTarget of Statement with arguments iterationSet and « ».

#### 13.7.2.4 SS: VarDeclaredNames <div id="sec-do-while-statement-static-semantics-vardeclarednames"></div>

```
IterationStatement : do Statement while ( Expression ) ;
```

1. Return the VarDeclaredNames of Statement.

#### 13.7.2.5 SS: VarScopedDeclarations <div id="sec-do-while-statement-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : do Statement while ( Expression ) ;
```

1. Return the VarScopedDeclarations of Statement.

#### 13.7.2.6 RS: LabelledEvaluation <div id="sec-do-while-statement-runtime-semantics-labelledevaluation"></div>

With parameter labelSet.

```
IterationStatement : do Statement while ( Expression ) ;
```

1. Let V be undefined.
2. Repeat,
a. Let stmtResult be the result of evaluating Statement.
b. If LoopContinues(stmtResult, labelSet) is false, return Completion(UpdateEmpty(stmtResult, V)).
c. If stmtResult.[[Value]] is not empty, set V to stmtResult.[[Value]].
d. Let exprRef be the result of evaluating Expression.
e. Let exprValue be ? GetValue(exprRef).
f. If ToBoolean(exprValue) is false, return NormalCompletion(V).

### 13.7.3 while 语句 <div id="sec-while-statement"></div>
#### 13.7.3.1 SS: ContainsDuplicateLabels <div id="sec-while-statement-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
IterationStatement : while ( Expression ) Statement
```

1. Return ContainsDuplicateLabels of Statement with argument labelSet.

#### 13.7.3.2 SS: ContainsUndefinedBreakTarget <div id="sec-while-statement-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
IterationStatement : while ( Expression ) Statement
```

1. Return ContainsUndefinedBreakTarget of Statement with argument labelSet.

#### 13.7.3.3 SS: ContainsUndefinedContinueTarget <div id="sec-while-statement-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
IterationStatement : while ( Expression ) Statement
```

1. Return ContainsUndefinedContinueTarget of Statement with arguments iterationSet and « ».

#### 13.7.3.4 SS: VarDeclaredNames <div id="sec-while-statement-static-semantics-vardeclarednames"></div>

```
IterationStatement : while ( Expression ) Statement
```

1. Return the VarDeclaredNames of Statement.

#### 13.7.3.5 SS: VarScopedDeclarations <div id="sec-while-statement-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : while ( Expression ) Statement
```

1. Return the VarScopedDeclarations of Statement.

#### 13.7.3.6 RS: LabelledEvaluation <div id="sec-while-statement-runtime-semantics-labelledevaluation"></div>

With parameter labelSet.

```
IterationStatement : while ( Expression ) Statement
```

1. Let V be undefined.
2. Repeat,
a. Let exprRef be the result of evaluating Expression.
b. Let exprValue be ? GetValue(exprRef).
c. If ToBoolean(exprValue) is false, return NormalCompletion(V).
d. Let stmtResult be the result of evaluating Statement.
e. If LoopContinues(stmtResult, labelSet) is false, return Completion(UpdateEmpty(stmtResult, V)).
f. If stmtResult.[[Value]] is not empty, set V to stmtResult.[[Value]].

### 13.7.4 for 语句 <div id="sec-for-statement"></div>
#### 13.7.4.1 SS: Early Errors <div id="sec-for-statement-static-semantics-early-errors"></div>

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

It is a Syntax Error if any element of the BoundNames of LexicalDeclaration also occurs in the VarDeclaredNames of Statement.

#### 13.7.4.2 SS: ContainsDuplicateLabels <div id="sec-for-statement-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
IterationStatement :
    for ( Expressionopt ; Expressionopt ; Expressionopt ) Statement
    for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
    for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement
```

1. Return ContainsDuplicateLabels of Statement with argument labelSet.

#### 13.7.4.3 SS: ContainsUndefinedBreakTarget <div id="sec-for-statement-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
IterationStatement :
    for ( Expressionopt ; Expressionopt ; Expressionopt ) Statement
    for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
    for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement
```

1. Return ContainsUndefinedBreakTarget of Statement with argument labelSet

#### 13.7.4.4 SS: ContainsUndefinedContinueTarget <div id="sec-for-statement-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
IterationStatement :
    for ( Expressionopt ; Expressionopt ; Expressionopt ) Statement
    for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
    for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement
```

1. Return ContainsUndefinedContinueTarget of Statement with arguments iterationSet and « ».

#### 13.7.4.5 SS: VarDeclaredNames <div id="sec-for-statement-static-semantics-vardeclarednames"></div>

```
IterationStatement : for ( Expression ; Expression ; Expression ) Statement
```

1. Return the VarDeclaredNames of Statement.

```
IterationStatement : for ( var VariableDeclarationList ; Expression ; Expression ) Statement
```

1. Let names be BoundNames of VariableDeclarationList.
2. Append to names the elements of the VarDeclaredNames of Statement.
3. Return names.

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

1. Return the VarDeclaredNames of Statement.

#### 13.7.4.6 SS: VarScopedDeclarations <div id="sec-for-statement-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : for ( Expression ; Expression ; Expression ) Statement
```

1. Return the VarScopedDeclarations of Statement.

```
IterationStatement : for ( var VariableDeclarationList ; Expression ; Expression ) Statement
```

1. Let declarations be VarScopedDeclarations of VariableDeclarationList.

2. Append to declarations the elements of the VarScopedDeclarations of Statement.
3. Return declarations.

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

1. Return the VarScopedDeclarations of Statement.

#### 13.7.4.7 RS: LabelledEvaluation <div id="sec-for-statement-runtime-semantics-labelledevaluation"></div>

With parameter labelSet.

```
IterationStatement : for ( Expression ; Expression ; Expression ) Statement
```

1. If the first Expression is present, then
a. Let exprRef be the result of evaluating the first Expression.
b. Perform ? GetValue(exprRef).
2. Return ? ForBodyEvaluation(the second Expression, the third Expression, Statement, « », labelSet).

```
IterationStatement : for ( var VariableDeclarationList ; Expression ; Expression ) Statement
```

1. Let varDcl be the result of evaluating VariableDeclarationList.
2. ReturnIfAbrupt(varDcl).
3. Return ? ForBodyEvaluation(the first Expression, the second Expression, Statement, « », labelSet).

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

1. Let oldEnv be the running execution context's LexicalEnvironment.
2. Let loopEnv be NewDeclarativeEnvironment(oldEnv).
3. Let loopEnvRec be loopEnv's EnvironmentRecord.
4. Let isConst be the result of performing IsConstantDeclaration of LexicalDeclaration.
5. Let boundNames be the BoundNames of LexicalDeclaration.
6. For each element dn of boundNames, do
a. If isConst is true, then
i. Perform ! loopEnvRec.CreateImmutableBinding(dn, true).
b. Else,
i. Perform ! loopEnvRec.CreateMutableBinding(dn, false).
7. Set the running execution context's LexicalEnvironment to loopEnv.
8. Let forDcl be the result of evaluating LexicalDeclaration.
9. If forDcl is an abrupt completion, then
a. Set the running execution context's LexicalEnvironment to oldEnv.
b. Return Completion(forDcl).
10. If isConst is false, let perIterationLets be boundNames; otherwise let perIterationLets be « ».
11. Let bodyResult be ForBodyEvaluation(the first Expression, the second Expression, Statement, perIterationLets,
labelSet).
12. Set the running execution context's LexicalEnvironment to oldEnv.
13. Return Completion(bodyResult)

#### 13.7.4.8 RS: ForBodyEvaluation ( test, increment, stmt, perIterationBindings, labelSet ) <div id="sec-forbodyevaluation"></div>

The abstract operation ForBodyEvaluation with arguments test, increment, stmt, perIterationBindings, and labelSet is performed as follows:

1. Let V be undefined.
2. Perform ? CreatePerIterationEnvironment(perIterationBindings).
3. Repeat,
a. If test is not [empty], then
i. Let testRef be the result of evaluating test.
ii. Let testValue be ? GetValue(testRef).
iii. If ToBoolean(testValue) is false, return NormalCompletion(V).
b. Let result be the result of evaluating stmt.
c. If LoopContinues(result, labelSet) is false, return Completion(UpdateEmpty(result, V)).
d. If result.[[Value]] is not empty, set V to result.[[Value]].
e. Perform ? CreatePerIterationEnvironment(perIterationBindings).
f. If increment is not [empty], then
i. Let incRef be the result of evaluating increment.
ii. Perform ? GetValue(incRef).

#### 13.7.4.9 RS: CreatePerIterationEnvironment ( perIterationBindings ) <div id="sec-createperiterationenvironment"></div>

The abstract operation CreatePerIterationEnvironment with argument perIterationBindings is performed as follows:

1. If perIterationBindings has any elements, then
a. Let lastIterationEnv be the running execution context's LexicalEnvironment.
b. Let lastIterationEnvRec be lastIterationEnv's EnvironmentRecord.
c. Let outer be lastIterationEnv's outer environment reference.
d. Assert: outer is not null.
e. Let thisIterationEnv be NewDeclarativeEnvironment(outer).
f. Let thisIterationEnvRec be thisIterationEnv's EnvironmentRecord.
g. For each element bn of perIterationBindings, do
i. Perform ! thisIterationEnvRec.CreateMutableBinding(bn, false).
ii. Let lastValue be ? lastIterationEnvRec.GetBindingValue(bn, true).
iii. Perform thisIterationEnvRec.InitializeBinding(bn, lastValue).
h. Set the running execution context's LexicalEnvironment to thisIterationEnv.
2. Return undefined.

### 13.7.5 for-in, for-of, 和for-await-of 语句 <div id="sec-for-in-and-for-of-statements"></div>
#### 13.7.5.1 SS: Early Errors <div id="sec-for-in-and-for-of-statements-static-semantics-early-errors"></div>

```
IterationStatement :
    for ( LeftHandSideExpression in Expression ) Statement
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for await ( LeftHandSideExpression of AssignmentExpression ) Statement
```

- It is a Syntax Error if LeftHandSideExpression is either an ObjectLiteral or an ArrayLiteral and if
  LeftHandSideExpression is not covering an AssignmentPattern.

If LeftHandSideExpression is either an ObjectLiteral or an ArrayLiteral and if LeftHandSideExpression is covering an AssignmentPattern then the following rules are not applied. Instead, the Early Error rules for AssignmentPattern are used.

- It is a Syntax Error if AssignmentTargetType of LeftHandSideExpression is not simple.
- It is a Syntax Error if the LeftHandSideExpression is CoverParenthesizedExpressionAndArrowParameterList : (Expression ) and Expression derives a phrase that would produce a Syntax Error according to these rules if that phrase were substituted for LeftHandSideExpression. This rule is recursively applied

> NOTE The last rule means that the other rules are applied even if parentheses surround Expression.

```
IterationStatement :
    for ( ForDeclaration in Expression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
    for await ( ForDeclaration of AssignmentExpression ) Statement
```

- It is a Syntax Error if the BoundNames of ForDeclaration contains "let".
- It is a Syntax Error if any element of the BoundNames of ForDeclaration also occurs in the VarDeclaredNames of Statement.
- It is a Syntax Error if the BoundNames of ForDeclaration contains any duplicate entries.

#### 13.7.5.2 SS: BoundNames <div id="sec-for-in-and-for-of-statements-static-semantics-boundnames"></div>

```
ForDeclaration : LetOrConst ForBinding
```

1. Return the BoundNames of ForBinding.

#### 13.7.5.3 SS: ContainsDuplicateLabels <div id="sec-for-in-and-for-of-statements-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
IterationStatement :
    for ( LeftHandSideExpression in Expression ) Statement
    for ( var ForBinding in Expression ) Statement
    for ( ForDeclaration in Expression ) Statement
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for ( var ForBinding of AssignmentExpression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
    for await ( LeftHandSideExpression of AssignmentExpression ) Statement
    for await ( var ForBinding of AssignmentExpression ) Statement
    for await ( ForDeclaration of AssignmentExpression ) Statement
```

1. Return ContainsDuplicateLabels of Statement with argument labelSet.

> NOTE This section is extended by Annex B.3.6.

#### 13.7.5.4 SS: ContainsUndefinedBreakTarget <div id="sec-for-in-and-for-of-statements-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
IterationStatement :
    for ( LeftHandSideExpression in Expression ) Statement
    for ( var ForBinding in Expression ) Statement
    for ( ForDeclaration in Expression ) Statement
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for ( var ForBinding of AssignmentExpression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
    for await ( LeftHandSideExpression of AssignmentExpression ) Statement
    for await ( var ForBinding of AssignmentExpression ) Statement
    for await ( ForDeclaration of AssignmentExpression ) Statement
```

> NOTE This section is extended by Annex B.3.6.

#### 13.7.5.5 SS: ContainsUndefinedContinueTarget <div id="sec-for-in-and-for-of-statements-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
IterationStatement :
    for ( LeftHandSideExpression in Expression ) Statement
    for ( var ForBinding in Expression ) Statement
    for ( ForDeclaration in Expression ) Statement
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for ( var ForBinding of AssignmentExpression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
    for await ( LeftHandSideExpression of AssignmentExpression ) Statement
    for await ( var ForBinding of AssignmentExpression ) Statement
    for await ( ForDeclaration of AssignmentExpression ) Statement
```

1. Return ContainsUndefinedContinueTarget of Statement with arguments iterationSet and « ».

> NOTE This section is extended by Annex B.3.6.

#### 13.7.5.6 SS: IsDestructuring <div id="sec-for-in-and-for-of-statements-static-semantics-isdestructuring"></div>

```
ForDeclaration : LetOrConst ForBinding
```

1. Return IsDestructuring of ForBinding.

```
ForBinding : BindingIdentifier
```

1. Return false

```
ForBinding : BindingPattern
```

1. Return true.

> NOTE This section is extended by Annex B.3.6.

#### 13.7.5.7 SS: VarDeclaredNames <div id="sec-for-in-and-for-of-statements-static-semantics-vardeclarednames"></div>

```
IterationStatement : for ( LeftHandSideExpression in Expression ) Statement
```

1. Return the VarDeclaredNames of Statement.

```
IterationStatement : for ( var ForBinding in Expression ) Statement
```

1. Let names be the BoundNames of ForBinding.
2. Append to names the elements of the VarDeclaredNames of Statement.
3. Return names.

```
IterationStatement : for ( ForDeclaration in Expression ) Statement
```

1. Return the VarDeclaredNames of Statement.

   ```
   IterationStatement :
       for ( LeftHandSideExpression of AssignmentExpression ) Statement
       for await ( LeftHandSideExpression of AssignmentExpression ) Statement
   ```

   1. Return the VarDeclaredNames of Statement.

   ```
   IterationStatement :
       for ( var ForBinding of AssignmentExpression ) Statement
       for await ( var ForBinding of AssignmentExpression ) Statement
   ```

   1. Let names be the BoundNames of ForBinding.
   2. Append to names the elements of the VarDeclaredNames of Statement.
   3. Return names.

   ```
   IterationStatement :
       for ( ForDeclaration of AssignmentExpression ) Statement
       for await ( ForDeclaration of AssignmentExpression ) Statement
   ```

   1. Return the VarDeclaredNames of Statement.

> NOTE This section is extended by Annex B.3.6.

#### 13.7.5.8 SS: VarScopedDeclarations <div id="sec-for-in-and-for-of-statements-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : for ( LeftHandSideExpression in Expression ) Statement
```

1. Return the VarScopedDeclarations of Statement.

```
IterationStatement : for ( var ForBinding in Expression ) Statement
```

1. Let declarations be a List containing ForBinding.

2. Append to declarations the elements of the VarScopedDeclarations of Statement.

3. Return declarations.

   ```
   IterationStatement :
       for ( ForDeclaration in Expression ) Statement
       for await ( LeftHandSideExpression of AssignmentExpression ) Statement
   ```

   1. Return the VarScopedDeclarations of Statement.

   ```
   IterationStatement :
       for ( LeftHandSideExpression of AssignmentExpression ) Statement
       for await ( var ForBinding of AssignmentExpression ) Statement
   ```

   1. Return the VarScopedDeclarations of Statement.

   ```
   IterationStatement :
       for ( var ForBinding of AssignmentExpression ) Statement
       for await ( var ForBinding of AssignmentExpression ) Statement
   ```

   1. Let declarations be a List containing ForBinding.
   2. Append to declarations the elements of the VarScopedDeclarations of Statement.
   3. Return declarations.

   ```
   IterationStatement :
       for ( ForDeclaration of AssignmentExpression ) Statement
       for await ( ForDeclaration of AssignmentExpression ) Statement
   ```

   1. Return the VarScopedDeclarations of Statement.

> NOTE This section is extended by Annex B.3.6.

#### 13.7.5.9 RS: BindingInitialization <div id="sec-for-in-and-for-of-statements-runtime-semantics-bindinginitialization"></div>

With parameters value and environment.

> NOTE undefined is passed for environment to indicate that a PutValue operation should be used to assign the initialization value. This is the case for var statements and the formal parameter lists of some non-strict functions (see 9.2.15). In those cases a lexical binding is hoisted and preinitialized prior to evaluation of its initializer.

```
ForDeclaration : LetOrConst ForBinding
```

1. Return the result of performing BindingInitialization for ForBinding passing value and environment as the arguments

#### 13.7.5.10 RS: BindingInstantiation <div id="sec-runtime-semantics-bindinginstantiation"></div>

With parameter environment.

```
ForDeclaration : LetOrConst ForBinding
```

1. Let envRec be environment's EnvironmentRecord.
2. Assert: envRec is a declarative Environment Record.
3. For each element name of the BoundNames of ForBinding, do
   a. If IsConstantDeclaration of LetOrConst is true, then
   i. Perform ! envRec.CreateImmutableBinding(name, true).
   b. Else,
   i. Perform ! envRec.CreateMutableBinding(name, false).

#### 13.7.5.11 RS: LabelledEvaluation <div id="sec-for-in-and-for-of-statements-runtime-semantics-labelledevaluation"></div>

With parameter labelSet.

```
IterationStatement : for ( LeftHandSideExpression in Expression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(« », Expression, enumerate).
2. Return ? ForIn/OfBodyEvaluation(LeftHandSideExpression, Statement, keyResult, enumerate, assignment, labelSet).

```
IterationStatement : for ( var ForBinding in Expression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(« », Expression, enumerate).
2. Return ? ForIn/OfBodyEvaluation(ForBinding, Statement, keyResult, enumerate, varBinding, labelSet).

```
IterationStatement : for ( ForDeclaration in Expression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(BoundNames of ForDeclaration, Expression, enumerate).
2. Return ? ForIn/OfBodyEvaluation(ForDeclaration, Statement, keyResult, enumerate, lexicalBinding, labelSet).

```
IterationStatement : for ( LeftHandSideExpression of AssignmentExpression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(« », AssignmentExpression, iterate).
2. Return ? ForIn/OfBodyEvaluation(LeftHandSideExpression, Statement, keyResult, iterate, assignment, labelSet).

```
IterationStatement : for ( var ForBinding of AssignmentExpression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(« », AssignmentExpression, iterate).
2. Return ? ForIn/OfBodyEvaluation(ForBinding, Statement, keyResult, iterate, varBinding, labelSet).

```
IterationStatement : for ( ForDeclaration of AssignmentExpression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(BoundNames of ForDeclaration, AssignmentExpression, iterate).
2. Return ? ForIn/OfBodyEvaluation(ForDeclaration, Statement, keyResult, iterate, lexicalBinding, labelSet).

```
IterationStatement : for await ( LeftHandSideExpression of AssignmentExpression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(« », AssignmentExpression, async-iterate).
2. Return ? ForIn/OfBodyEvaluation(LeftHandSideExpression, Statement, keyResult, iterate, assignment, labelSet, async).

```
IterationStatement : for await ( var ForBinding of AssignmentExpression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(« », AssignmentExpression, async-iterate).
2. Return ? ForIn/OfBodyEvaluation(ForBinding, Statement, keyResult, iterate, varBinding, labelSet, async).

```
IterationStatement : for await ( ForDeclaration of AssignmentExpression ) Statement
```

1. Let keyResult be ? ForIn/OfHeadEvaluation(BoundNames of ForDeclaration, AssignmentExpression, asynciterate).
2. Return ? ForIn/OfBodyEvaluation(ForDeclaration, Statement, keyResult, iterate, lexicalBinding, labelSet, async).

> NOTE This section is extended by Annex B.3.6.

#### 13.7.5.12 RS: ForIn/OfHeadEvaluation ( TDZnames, expr, iterationKind ) <div id="sec-runtime-semantics-forin-div-ofheadevaluation-tdznames-expr-iterationkind"></div>

The abstract operation ForIn/OfHeadEvaluation is called with arguments TDZnames, expr, and iterationKind. The value of iterationKind is either enumerate, iterate, or async-iterate.

1. Let oldEnv be the running execution context's LexicalEnvironment.
2. If TDZnames is not an empty List, then
a. Assert: TDZnames has no duplicate entries.
b. Let TDZ be NewDeclarativeEnvironment(oldEnv).
c. Let TDZEnvRec be TDZ's EnvironmentRecord.
d. For each string name in TDZnames, do
i. Perform ! TDZEnvRec.CreateMutableBinding(name, false).
e. Set the running execution context's LexicalEnvironment to TDZ.
3. Let exprRef be the result of evaluating expr.
4. Set the running execution context's LexicalEnvironment to oldEnv.
5. Let exprValue be ? GetValue(exprRef).
6. If iterationKind is enumerate, then
a. If exprValue is undefined or null, then
i. Return Completion { [[Type]]: break, [[Value]]: empty, [[Target]]: empty }.
b. Let obj be ! ToObject(exprValue).
c. Return ? EnumerateObjectProperties(obj).
7. Else,
a. Assert: iterationKind is iterate.
b. If iterationKind is async-iterate, let iteratorHint be async.
c. Else, let iteratorHint be sync.
d. Return ? GetIterator(exprValue, iteratorHint).

#### 13.7.5.13 RS: ForIn/OfBodyEvaluation ( lhs, stmt, iteratorRecord, iterationKind, lhsKind, labelSet [ , iteratorKind ] ) <div id="sec-runtime-semantics-forin-div-ofbodyevaluation-lhs-stmt-iterator-lhskind-labelset"></div>

The abstract operation ForIn/OfBodyEvaluation is called with arguments lhs, stmt, iteratorRecord, iterationKind, lhsKind, labelSet, and optional argument iteratorKind. The value of lhsKind is either assignment, varBinding or lexicalBinding. The value of iteratorKind is either sync or async.

1. If iteratorKind is not present, set iteratorKind to sync.

2. Let oldEnv be the running execution context's LexicalEnvironment.

3. Let V be undefined.

4. Let destructuring be IsDestructuring of lhs.

5. If destructuring is true and if lhsKind is assignment, then
  a. Assert: lhs is a LeftHandSideExpression.
  b. Let assignmentPattern be the AssignmentPattern that is covered by lhs.

6. Repeat,

   a. Let nextResult be ? Call(iteratorRecord.[[NextMethod]], iteratorRecord.[[Iterator]], « »).
   b. If iteratorKind is async, then set nextResult to ? Await(nextResult).
   c. If Type(nextResult) is not Object, throw a TypeError exception.
   d. Let done be ? IteratorComplete(nextResult).
   e. If done is true, return NormalCompletion(V).
   f. Let nextValue be ? IteratorValue(nextResult).
   g. If lhsKind is either assignment or varBinding, then
   i. If destructuring is false, then
   1. Let lhsRef be the result of evaluating lhs. (It may be evaluated repeatedly.)
   h. Else,
   i. Assert: lhsKind is lexicalBinding.
   ii. Assert: lhs is a ForDeclaration.
   iii. Let iterationEnv be NewDeclarativeEnvironment(oldEnv).
   iv. Perform BindingInstantiation for lhs passing iterationEnv as the argument.
   v. Set the running execution context's LexicalEnvironment to iterationEnv.
   vi. If destructuring is false, then
   1. Assert: lhs binds a single name.
   2. Let lhsName be the sole element of BoundNames of lhs.
   3. Let lhsRef be ! ResolveBinding(lhsName).
   i. If destructuring is false, then
   i. If lhsRef is an abrupt completion, then
   1. Let status be lhsRef.
   ii. Else if lhsKind is lexicalBinding, then
   1. Let status be InitializeReferencedBinding(lhsRef, nextValue).
   iii. Else,
   1. Let status be PutValue(lhsRef, nextValue).
   j. Else,
   i. If lhsKind is assignment, then
   1. Let status be the result of performing DestructuringAssignmentEvaluation of assignmentPattern
   using nextValue as the argument.
   ii. Else if lhsKind is varBinding, then
   1. Assert: lhs is a ForBinding.
   2. Let status be the result of performing BindingInitialization for lhs passing nextValue and
   undefined as the arguments.
   iii. Else,
   1. Assert: lhsKind is lexicalBinding.
   2. Assert: lhs is a ForDeclaration.
   3. Let status be the result of performing BindingInitialization for lhs passing nextValue and
   iterationEnv as arguments.
   k. If status is an abrupt completion, then
   i. Set the running execution context's LexicalEnvironment to oldEnv.
   ii. If iteratorKind is async, return ? AsyncIteratorClose(iteratorRecord, status).
   iii. If iterationKind is enumerate, then
   1. Return status.
   iv. Else,
   1. Assert: iterationKind is iterate.
   2. Return ? IteratorClose(iteratorRecord, status).
   l. Let result be the result of evaluating stmt.
   17. m. Set the running execution context's LexicalEnvironment to oldEnv.
      n. If LoopContinues(result, labelSet) is false, then
      i. If iterationKind is enumerate, then
      1. Return Completion(UpdateEmpty(result, V)).
      ii. Else,
      1. Assert: iterationKind is iterate.
      2. Set status to UpdateEmpty(result, V).
      3. If iteratorKind is async, return ? AsyncIteratorClose(iteratorRecord, status).
      4. Return ? IteratorClose(iteratorRecord, status).
      o. If result.[[Value]] is not empty, set V to result.[[Value]].

#### 13.7.5.14 RS: Evaluation <div id="sec-for-in-and-for-of-statements-runtime-semantics-evaluation"></div>

```
ForBinding : BindingIdentifier
```

1. Let bindingId be StringValue of BindingIdentifier.
2. Return ? ResolveBinding(bindingId).

#### 13.7.5.15 EnumerateObjectProperties ( O ) <div id="sec-enumerate-object-properties"></div>

When the abstract operation EnumerateObjectProperties is called with argument O, the following steps are taken:

1. Assert: Type(O) is Object.
2. Return an Iterator object (25.1.1.2) whose next method iterates over all the String-valued keys of enumerable
properties of O. The iterator object is never directly accessible to ECMAScript code. The mechanics and order of
enumerating the properties is not specified but must conform to the rules specified below.

The iterator's throw and return methods are null and are never invoked. The iterator's next method processes object properties to determine whether the property key should be returned as an iterator value. Returned property keys do not include keys that are Symbols. Properties of the target object may be deleted during enumeration. A property that is deleted before it is processed by the iterator's next method is ignored. If new properties are added to the target object during enumeration, the newly added properties are not guaranteed to be processed in the active enumeration. A property name will be returned by the iterator's next method at most once in any enumeration.

Enumerating the properties of the target object includes enumerating properties of its prototype, and the prototype of the prototype, and so on, recursively; but a property of a prototype is not processed if it has the same name as a property that has already been processed by the iterator's next method. The values of [[Enumerable]] attributes are not considered when determining if a property of a prototype object has already been processed. The enumerable property names of prototype objects must be obtained by invoking EnumerateObjectProperties passing the prototype object as the argument. EnumerateObjectProperties must obtain the own property keys of the target object by calling its [[OwnPropertyKeys]] internal method. Property attributes of the target object must be obtained by calling its [[GetOwnProperty]] internal method.

> NOTE The following is an informative definition of an ECMAScript generator function that conforms to these rules:
>
> function* EnumerateObjectProperties(obj) {
>  const visited = new Set();
>  for (const key of Reflect.ownKeys(obj)) {
>  if (typeof key === "symbol") continue;
>  const desc = Reflect.getOwnPropertyDescriptor(obj, key);
>
> if (desc) {
>  visited.add(key);
>  if (desc.enumerable) yield key;
>  }
>  }
>  const proto = Reflect.getPrototypeOf(obj);
>  if (proto === null) return;
>  for (const protoKey of EnumerateObjectProperties(proto)) {
>  if (!visited.has(protoKey)) yield protoKey;
>  }
> }

## 13.8 continue 语句 <div id="sec-continue-statement"></div>

Syntax

```
ContinueStatement[Yield, Await] :
    continue ;
    continue [no LineTerminator here] LabelIdentifier[?Yield, ?Await] ;
```

### 13.8.1 SS: Early Errors <div id="sec-continue-statement-static-semantics-early-errors"></div>

```
ContinueStatement : continue ;
ContinueStatement : continue LabelIdentifier ;
```

It is a Syntax Error if this ContinueStatement is not nested, directly or indirectly (but not crossing function
boundaries), within an IterationStatement.

### 13.8.2 SS: ContainsUndefinedContinueTarget <div id="sec-continue-statement-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
ContinueStatement : continue ;
```

1. Return false.

```
ContinueStatement : continue LabelIdentifier ;
```

1. If the StringValue of LabelIdentifier is not an element of iterationSet, return true.
2. Return false.

### 13.8.3 RS: Evaluation <div id="sec-continue-statement-runtime-semantics-evaluation"></div>

```
ContinueStatement : continue ;
```

1. Return Completion { [[Type]]: continue, [[Value]]: empty, [[Target]]: empty }.

```
ContinueStatement : continue LabelIdentifier ;
```

1. Let label be the StringValue of LabelIdentifier.
2. Return Completion { [[Type]]: continue, [[Value]]: empty, [[Target]]: label }.

## 13.9 break 语句 <div id="sec-break-statement"></div>

Syntax

```
BreakStatement[Yield, Await] :
    break ;
    break [no LineTerminator here] LabelIdentifier[?Yield, ?Await] ;
```

### 13.9.1 SS: Early Errors <div id="sec-break-statement-static-semantics-early-errors"></div>

```
BreakStatement : break ;
```

It is a Syntax Error if this BreakStatement is not nested, directly or indirectly (but not crossing function
boundaries), within an IterationStatement or a SwitchStatement.

### 13.9.2 SS: ContainsUndefinedBreakTarget <div id="sec-break-statement-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
BreakStatement : break ;
```

1. Return false.

```
BreakStatement : break LabelIdentifier ;
```

1. If the StringValue of LabelIdentifier is not an element of labelSet, return true.
2. Return false.

### 13.9.3 RS: Evaluation <div id="sec-break-statement-runtime-semantics-evaluation"></div>

```
BreakStatement : break ;
```

1. Return Completion { [[Type]]: break, [[Value]]: empty, [[Target]]: empty }.

```
BreakStatement : break LabelIdentifier ;
```

1. Let label be the StringValue of LabelIdentifier.
2. Return Completion { [[Type]]: break, [[Value]]: empty, [[Target]]: label }.

## 13.10 return 语句 <div id="sec-return-statement"></div>

Syntax

```
ReturnStatement[Yield, Await] :
    return ;
    return [no LineTerminator here] Expression[+In, ?Yield, ?Await] ;
```

> NOTE A return statement causes a function to cease execution and, in most cases, returns a value to the caller. If Expression is omitted, the return value is undefined. Otherwise, the return value is the value of Expression. A return statement may not actually return a value to the caller depending on surrounding context. For example, in a try block, a return statement's completion record may be replaced with another completion record during evaluation of the finally block.

### 13.10.1 RS: Evaluation <div id="sec-return-statement-runtime-semantics-evaluation"></div>

```
ReturnStatement : return ;
```

1. Return Completion { [[Type]]: return, [[Value]]: undefined, [[Target]]: empty }.

```
ReturnStatement : return Expression ;
```

1. Let exprRef be the result of evaluating Expression.
2. Let exprValue be ? GetValue(exprRef).
3. If ! GetGeneratorKind() is async, set exprValue to ? Await(exprValue).
4. Return Completion { [[Type]]: return, [[Value]]: exprValue, [[Target]]: empty }.

## 13.11 with 语句 <div id="sec-with-statement"></div>

Syntax

```
WithStatement[Yield, Await, Return] :
    with ( Expression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
```

> NOTE The with statement adds an object Environment Record for a computed object to the lexical environment of the running execution context. It then executes a statement using this augmented lexical environment. Finally, it restores the original lexical environment.

### 13.11.1 SS: Early Errors <div id="sec-with-statement-static-semantics-early-errors"></div>

```
WithStatement : with ( Expression ) Statement
```

It is a Syntax Error if the code that matches this production is contained in strict mode code.
It is a Syntax Error if IsLabelledFunction(Statement) is true.

> NOTE It is only necessary to apply the second rule if the extension specified in B.3.2 is implemented.

### 13.11.2 SS: ContainsDuplicateLabels <div id="sec-with-statement-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
WithStatement : with ( Expression ) Statement
```

1. Return ContainsDuplicateLabels of Statement with argument labelSe

### 13.11.3 SS: ContainsUndefinedBreakTarget <div id="sec-with-statement-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet

```
WithStatement : with ( Expression ) Statement
```

1. Return ContainsUndefinedBreakTarget of Statement with argument labelSet.

### 13.11.4 SS: ContainsUndefinedContinueTarget <div id="sec-with-statement-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
WithStatement : with ( Expression ) Statement
```

1. Return ContainsUndefinedContinueTarget of Statement with arguments iterationSet and « ».

### 13.11.5 SS: VarDeclaredNames <div id="sec-with-statement-static-semantics-vardeclarednames"></div>

```
WithStatement : with ( Expression ) Statement
```

1. Return the VarDeclaredNames of Statement.

### 13.11.6 SS: VarScopedDeclarations <div id="sec-with-statement-static-semantics-varscopeddeclarations"></div>

```
WithStatement : with ( Expression ) Statement
```

1. Return the VarScopedDeclarations of Statement.

### 13.11.7 RS: Evaluation <div id="sec-with-statement-runtime-semantics-evaluation"></div>

```
WithStatement : with ( Expression ) Statement
```

1. Let val be the result of evaluating Expression.
2. Let obj be ? ToObject(? GetValue(val)).
3. Let oldEnv be the running execution context's LexicalEnvironment.
4. Let newEnv be NewObjectEnvironment(obj, oldEnv).
5. Set the withEnvironment flag of newEnv's EnvironmentRecord to true.
6. Set the running execution context's LexicalEnvironment to newEnv.
7. Let C be the result of evaluating Statement.
8. Set the running execution context's LexicalEnvironment to oldEnv.
9. Return Completion(UpdateEmpty(C, undefined)).

> NOTE No matter how control leaves the embedded Statement, whether normally or by some form of abrupt completion or exception, the LexicalEnvironment is always restored to its former state.

## 13.12 switch 语句 <div id="sec-switch-statement"></div>

Syntax

```
SwitchStatement[Yield, Await, Return] :
    switch ( Expression[+In, ?Yield, ?Await] ) CaseBlock[?Yield, ?Await, ?Return]
CaseBlock[Yield, Await, Return] :
    { CaseClauses[?Yield, ?Await, ?Return] opt }
    { CaseClauses[?Yield, ?Await, ?Return] opt DefaultClause[?Yield, ?Await, ?Return]
    CaseClauses[?Yield, ?Await, ?Return] opt }
CaseClauses[Yield, Await, Return] :
    CaseClause[?Yield, ?Await, ?Return]
    CaseClauses[?Yield, ?Await, ?Return] CaseClause[?Yield, ?Await, ?Return]
CaseClause[Yield, Await, Return] :
    case Expression[+In, ?Yield, ?Await] : StatementList[?Yield, ?Await, ?Return] opt
DefaultClause[Yield, Await, Return] :
    default : StatementList[?Yield, ?Await, ?Return] opt
```

### 13.12.1 SS: Early Errors <div id="sec-switch-statement-static-semantics-early-errors"></div>

```
SwitchStatement : switch ( Expression ) CaseBlock
```

It is a Syntax Error if the LexicallyDeclaredNames of CaseBlock contains any duplicate entries.
It is a Syntax Error if any element of the LexicallyDeclaredNames of CaseBlock also occurs in the
VarDeclaredNames of CaseBlock.

### 13.12.2 SS: ContainsDuplicateLabels <div id="sec-switch-statement-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. Return ContainsDuplicateLabels of CaseBlock with argument labelSet.

```
CaseBlock : { }
```

1. Return false

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. If the first CaseClauses is present, then
a. Let hasDuplicates be ContainsDuplicateLabels of the first CaseClauses with argument labelSet.
b. If hasDuplicates is true, return true.
2. Let hasDuplicates be ContainsDuplicateLabels of DefaultClause with argument labelSet.
3. If hasDuplicates is true, return true.
4. If the second CaseClauses is not present, return false.
5. Return ContainsDuplicateLabels of the second CaseClauses with argument labelSet.

```
CaseClauses : CaseClauses CaseClause
```

1. Let hasDuplicates be ContainsDuplicateLabels of CaseClauses with argument labelSet.
2. If hasDuplicates is true, return true.
3. Return ContainsDuplicateLabels of CaseClause with argument labelSet.

```
CaseClause : case Expression : StatementList
```

1. If the StatementList is present, return ContainsDuplicateLabels of StatementList with argument labelSet.
2. Return false.

```
DefaultClause : default : StatementList
```

1. If the StatementList is present, return ContainsDuplicateLabels of StatementList with argument labelSet.
2. Return false

### 13.12.3 SS: ContainsUndefinedBreakTarget <div id="sec-switch-statement-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. Return ContainsUndefinedBreakTarget of CaseBlock with argument labelSet.

```
CaseBlock : { }
```

1. Return false.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. If the first CaseClauses is present, then
a. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of the first CaseClauses with argument labelSet.
b. If hasUndefinedLabels is true, return true.
2. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of DefaultClause with argument labelSet.
3. If hasUndefinedLabels is true, return true.
4. If the second CaseClauses is not present, return false.
5. Return ContainsUndefinedBreakTarget of the second CaseClauses with argument labelSet.

```
CaseClauses : CaseClauses CaseClause
```

1. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of CaseClauses with argument labelSet.
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedBreakTarget of CaseClause with argument labelSet.

```
CaseClause : case Expression : StatementList
```

1. If the StatementList is present, return ContainsUndefinedBreakTarget of StatementList with argument labelSet.
2. Return false.

```
DefaultClause : default : StatementList
```

1. If the StatementList is present, return ContainsUndefinedBreakTarget of StatementList with argument labelSet.
2. Return false.

### 13.12.4 SS: ContainsUndefinedContinueTarget <div id="sec-switch-statement-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. Return ContainsUndefinedContinueTarget of CaseBlock with arguments iterationSet and « ».

```
CaseBlock : { }
```

1. Return false

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. If the first CaseClauses is present, then
a. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of the first CaseClauses with arguments
iterationSet and « ».
b. If hasUndefinedLabels is true, return true.
2. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of DefaultClause with arguments iterationSet and «
».
3. If hasUndefinedLabels is true, return true.
4. If the second CaseClauses is not present, return false.
5. Return ContainsUndefinedContinueTarget of the second CaseClauses with arguments iterationSet and « ».

```
CaseClauses : CaseClauses CaseClause
```

1. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of CaseClauses with arguments iterationSet and « ».
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedContinueTarget of CaseClause with arguments iterationSet and « ».

```
CaseClause : case Expression : StatementList
```

1. If the StatementList is present, return ContainsUndefinedContinueTarget of StatementList with arguments iterationSet and « ».
2. Return false.

```
DefaultClause : default : StatementList
```

1. If the StatementList is present, return ContainsUndefinedContinueTarget of StatementList with arguments iterationSet and « ».
2. Return false.

### 13.12.5 SS: LexicallyDeclaredNames <div id="sec-switch-statement-static-semantics-lexicallydeclarednames"></div>

```
CaseBlock : { }
```

1. Return a new empty List.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. If the first CaseClauses is present, let names be the LexicallyDeclaredNames of the first CaseClauses.
2. Else, let names be a new empty List.
3. Append to names the elements of the LexicallyDeclaredNames of the DefaultClause.
4. If the second CaseClauses is not present, return names

5. Return the result of appending to names the elements of the LexicallyDeclaredNames of the second CaseClauses

```
CaseClauses : CaseClauses CaseClause
```

1. Let names be LexicallyDeclaredNames of CaseClauses.
2. Append to names the elements of the LexicallyDeclaredNames of CaseClause.
3. Return names.

```
CaseClause : case Expression : StatementList
```

1. If the StatementList is present, return the LexicallyDeclaredNames of StatementList.
2. Return a new empty List.

```
DefaultClause : default : StatementList
```

1. If the StatementList is present, return the LexicallyDeclaredNames of StatementList.
2. Return a new empty List.

### 13.12.6 SS: LexicallyScopedDeclarations <div id="sec-switch-statement-static-semantics-lexicallyscopeddeclarations"></div>

```
CaseBlock : { }
```

1. Return a new empty List.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. If the first CaseClauses is present, let declarations be the LexicallyScopedDeclarations of the first CaseClauses.
2. Else, let declarations be a new empty List.
3. Append to declarations the elements of the LexicallyScopedDeclarations of the DefaultClause.
4. If the second CaseClauses is not present, return declarations.
5. Return the result of appending to declarations the elements of the LexicallyScopedDeclarations of the second CaseClauses.

```
CaseClauses : CaseClauses CaseClause
```

1. Let declarations be LexicallyScopedDeclarations of CaseClauses.
2. Append to declarations the elements of the LexicallyScopedDeclarations of CaseClause.
3. Return declarations

```
CaseClause : case Expression : StatementList
```

1. If the StatementList is present, return the LexicallyScopedDeclarations of StatementList.
2. Return a new empty List.

```
DefaultClause : default : StatementList
```

1. If the StatementList is present, return the LexicallyScopedDeclarations of StatementList.
2. Return a new empty List.

### 13.12.7 SS: VarDeclaredNames <div id="sec-switch-statement-static-semantics-vardeclarednames"></div>

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. Return the VarDeclaredNames of CaseBlock.

```
CaseBlock : { }
```

1. Return a new empty List.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. If the first CaseClauses is present, let names be the VarDeclaredNames of the first CaseClauses.
2. Else, let names be a new empty List.
3. Append to names the elements of the VarDeclaredNames of the DefaultClause.
4. If the second CaseClauses is not present, return names.
5. Return the result of appending to names the elements of the VarDeclaredNames of the second CaseClauses.

```
CaseClauses : CaseClauses CaseClause
```

1. Let names be VarDeclaredNames of CaseClauses.
2. Append to names the elements of the VarDeclaredNames of CaseClause.
3. Return names.

```
CaseClause : case Expression : StatementList
```

1. If the StatementList is present, return the VarDeclaredNames of StatementList.
2. Return a new empty List.

```
DefaultClause : default : StatementList
```

1. If the StatementList is present, return the VarDeclaredNames of StatementList.
2. Return a new empty List.

### 13.12.8 SS: VarScopedDeclarations <div id="sec-switch-statement-static-semantics-varscopeddeclarations"></div>

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. Return the VarScopedDeclarations of CaseBlock.

```
CaseBlock : { }
```

1. Return a new empty List.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. If the first CaseClauses is present, let declarations be the VarScopedDeclarations of the first CaseClauses.
2. Else, let declarations be a new empty List.
3. Append to declarations the elements of the VarScopedDeclarations of the DefaultClause.
4. If the second CaseClauses is not present, return declarations.
5. Return the result of appending to declarations the elements of the VarScopedDeclarations of the second CaseClauses.

```
CaseClauses : CaseClauses CaseClause
```

1. Let declarations be VarScopedDeclarations of CaseClauses.
2. Append to declarations the elements of the VarScopedDeclarations of CaseClause.
3. Return declarations.

```
CaseClause : case Expression : StatementList
```

1. If the StatementList is present, return the VarScopedDeclarations of StatementList.
2. Return a new empty List.

```
DefaultClause : default : StatementList
```

1. If the StatementList is present, return the VarScopedDeclarations of StatementList.
2. Return a new empty List.

### 13.12.9 RS: CaseBlockEvaluation <div id="sec-runtime-semantics-caseblockevaluation"></div>

With parameter input.

```
CaseBlock : { }
```

1. Return NormalCompletion(undefined).

```
CaseBlock : { CaseClauses }
```

1. Let V be undefined.
2. Let A be the List of CaseClause items in CaseClauses, in source text order.
3. Let found be false.
4. For each CaseClause C in A, do
a. If found is false, then
i. Set found to ? CaseClauseIsSelected(C, input).
b. If found is true, then
i. Let R be the result of evaluating C.
ii. If R.[[Value]] is not empty, set V to R.[[Value]].
iii. If R is an abrupt completion, return Completion(UpdateEmpty(R, V)).
5. Return NormalCompletion(V).

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. Let V be undefined.
2. If the first CaseClauses is present, then
a. Let A be the List of CaseClause items in the first CaseClauses, in source text order.
3. Else,
a. Let A be « ».
4. Let found be false.
5. For each CaseClause C in A, do
a. If found is false, then
i. Set found to ? CaseClauseIsSelected(C, input).
b. If found is true, then
i. Let R be the result of evaluating C.
ii. If R.[[Value]] is not empty, set V to R.[[Value]].
iii. If R is an abrupt completion, return Completion(UpdateEmpty(R, V)).
6. Let foundInB be false.
7. If the second CaseClauses is present, then
a. Let B be the List of CaseClause items in the second CaseClauses, in source text order.
8. Else,
a. Let B be « ».

9. If found is false, then
a. For each CaseClause C in B, do
i. If foundInB is false, then
1. Set foundInB to ? CaseClauseIsSelected(C, input).
ii. If foundInB is true, then
1. Let R be the result of evaluating CaseClause C.
2. If R.[[Value]] is not empty, set V to R.[[Value]].
3. If R is an abrupt completion, return Completion(UpdateEmpty(R, V)).
10. If foundInB is true, return NormalCompletion(V).
11. Let R be the result of evaluating DefaultClause.
12. If R.[[Value]] is not empty, set V to R.[[Value]].
13. If R is an abrupt completion, return Completion(UpdateEmpty(R, V)).
14. For each CaseClause C in B (NOTE: this is another complete iteration of the second CaseClauses), do
a. Let R be the result of evaluating CaseClause C.
b. If R.[[Value]] is not empty, set V to R.[[Value]].
c. If R is an abrupt completion, return Completion(UpdateEmpty(R, V)).
15. Return NormalCompletion(V).

### 13.12.10 RS: CaseClauseIsSelected ( C, input ) <div id="sec-runtime-semantics-caseclauseisselected"></div>

The abstract operation CaseClauseIsSelected, given CaseClause C and value input, determines whether C matches input.

1. Assert: C is an instance of the production CaseClause : case Expression : StatementList .
2. Let exprRef be the result of evaluating the Expression of C.
3. Let clauseSelector be ? GetValue(exprRef).
4. Return the result of performing Strict Equality Comparison input === clauseSelector.

> NOTE This operation does not execute C's StatementList (if any). The CaseBlock algorithm uses its return value to determine which StatementList to start executing.

### 13.12.11 RS: Evaluation <div id="sec-switch-statement-runtime-semantics-evaluation"></div>

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. Let exprRef be the result of evaluating Expression.
2. Let switchValue be ? GetValue(exprRef).
3. Let oldEnv be the running execution context's LexicalEnvironment.
4. Let blockEnv be NewDeclarativeEnvironment(oldEnv).
5. Perform BlockDeclarationInstantiation(CaseBlock, blockEnv).
6. Set the running execution context's LexicalEnvironment to blockEnv.
7. Let R be the result of performing CaseBlockEvaluation of CaseBlock with argument switchValue.
8. Set the running execution context's LexicalEnvironment to oldEnv.
9. Return R

> NOTE No matter how control leaves the SwitchStatement the LexicalEnvironment is always restored to its former state.

```
CaseClause : case Expression :
```

1. Return NormalCompletion(empty).

```
CaseClause : case Expression : StatementList
```

1. Return the result of evaluating StatementList.

```
DefaultClause : default :
```

1. Return NormalCompletion(empty).

```
DefaultClause : default : StatementList
```

1. Return the result of evaluating StatementList.

## 13.13 带标签的语句 <div id="sec-labelled-statements"></div>

Syntax

```
LabelledStatement[Yield, Await, Return] :
    LabelIdentifier[?Yield, ?Await] : LabelledItem[?Yield, ?Await, ?Return]
LabelledItem[Yield, Await, Return] :
    Statement[?Yield, ?Await, ?Return]
    FunctionDeclaration[?Yield, ?Await, ~Default]
```

> NOTE A Statement may be prefixed by a label. Labelled statements are only used in conjunction with labelled break and continue statements. ECMAScript has no goto statement. A Statement can be part of a LabelledStatement, which itself can be part of a LabelledStatement, and so on. The labels introduced this way are collectively referred to as the “current label set” when describing the semantics of individual statements.

### 13.13.1 SS: Early Errors <div id="sec-labelled-statements-static-semantics-early-errors"></div>

```
LabelledItem : FunctionDeclaration
```

It is a Syntax Error if any source text matches this rule.

> NOTE An alternative definition for this rule is provided in B.3.2.

### 13.13.2 SS: ContainsDuplicateLabels <div id="sec-labelled-statements-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Let label be the StringValue of LabelIdentifier.
2. If label is an element of labelSet, return true.
3. Let newLabelSet be a copy of labelSet with label appended.
4. Return ContainsDuplicateLabels of LabelledItem with argument newLabelSet.

```
LabelledItem : FunctionDeclaration
```

1. Return false.

### 13.13.3 SS: ContainsUndefinedBreakTarget <div id="sec-labelled-statements-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Let label be the StringValue of LabelIdentifier.
2. Let newLabelSet be a copy of labelSet with label appended.
3. Return ContainsUndefinedBreakTarget of LabelledItem with argument newLabelSet.

```
LabelledItem : FunctionDeclaration
```

1. Return false.

### 13.13.4 SS: ContainsUndefinedContinueTarget <div id="sec-labelled-statements-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Let label be the StringValue of LabelIdentifier.
2. Let newLabelSet be a copy of labelSet with label appended.
3. Return ContainsUndefinedContinueTarget of LabelledItem with arguments iterationSet and newLabelSet.

```
LabelledItem : FunctionDeclaration
```

1. Return false.

### 13.13.5 SS: IsLabelledFunction ( stmt ) <div id="sec-islabelledfunction"></div>

The abstract operation IsLabelledFunction with argument stmt performs the following steps:

1. If stmt is not a LabelledStatement, return false.
2. Let item be the LabelledItem of stmt.
3. If item is LabelledItem : FunctionDeclaration , return true.
4. Let subStmt be the Statement of item.
5. Return IsLabelledFunction(subStmt).

### 13.13.6 SS: LexicallyDeclaredNames <div id="sec-labelled-statements-static-semantics-lexicallydeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Return the LexicallyDeclaredNames of LabelledItem.

```
LabelledItem : Statement
```

1. Return a new empty List.

```
LabelledItem : FunctionDeclaration
```

1. Return BoundNames of FunctionDeclaration.

### 13.13.7 SS: LexicallyScopedDeclarations <div id="sec-labelled-statements-static-semantics-lexicallyscopeddeclarations"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Return the LexicallyScopedDeclarations of LabelledItem.

```
LabelledItem : Statement
```

1. Return a new empty List.

```
LabelledItem : FunctionDeclaration
```

1. Return a new List containing FunctionDeclaration.

### 13.13.8 SS: TopLevelLexicallyDeclaredNames <div id="sec-labelled-statements-static-semantics-toplevellexicallydeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Return a new empty List.

### 13.13.9 SS: TopLevelLexicallyScopedDeclarations <div id="sec-labelled-statements-static-semantics-toplevellexicallyscopeddeclarations"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Return a new empty List.

### 13.13.10 SS: TopLevelVarDeclaredNames <div id="sec-labelled-statements-static-semantics-toplevelvardeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Return the TopLevelVarDeclaredNames of LabelledItem.

```
LabelledItem : Statement
```

1. If Statement is Statement : LabelledStatement , return TopLevelVarDeclaredNames of Statement.
2. Return VarDeclaredNames of Statement.

```
LabelledItem : FunctionDeclaration
```

1. Return BoundNames of FunctionDeclaration.

### 13.13.11 SS: TopLevelVarScopedDeclarations <div id="sec-labelled-statements-static-semantics-toplevelvarscopeddeclarations"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Return the TopLevelVarScopedDeclarations of LabelledItem.

```
LabelledItem : Statement
```

1. If Statement is Statement : LabelledStatement , return TopLevelVarScopedDeclarations of Statement.
2. Return VarScopedDeclarations of Statement.

```
LabelledItem : FunctionDeclaration
```

1. Return a new List containing FunctionDeclaration.

### 13.13.12 SS: VarDeclaredNames <div id="sec-labelled-statements-static-semantics-vardeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Return the VarDeclaredNames of LabelledItem.

```
LabelledItem : FunctionDeclaration
```

1. Return a new empty List.

### 13.13.13 SS: VarScopedDeclarations <div id="sec-labelled-statements-static-semantics-varscopeddeclarations"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Return the VarScopedDeclarations of LabelledItem.

```
LabelledItem : FunctionDeclaration
```

1. Return a new empty List.

### 13.13.14 RS: LabelledEvaluation <div id="sec-labelled-statements-runtime-semantics-labelledevaluation"></div>

With parameter labelSet.

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Let label be the StringValue of LabelIdentifier.
2. Append label as an element of labelSet.
3. Let stmtResult be LabelledEvaluation of LabelledItem with argument labelSet.
4. If stmtResult.[[Type]] is break and SameValue(stmtResult.[[Target]], label) is true, then
a. Set stmtResult to NormalCompletion(stmtResult.[[Value]]).
5. Return Completion(stmtResult).

```
LabelledItem : Statement
```

1. If Statement is either a LabelledStatement or a BreakableStatement, then
a. Return LabelledEvaluation of Statement with argument labelSet.
2. Else,
a. Return the result of evaluating Statement.

```
LabelledItem : FunctionDeclaration
```

1. Return the result of evaluating FunctionDeclaration.

### 13.13.15 RS: Evaluation <div id="sec-labelled-statements-runtime-semantics-evaluation"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. Let newLabelSet be a new empty List.
2. Return LabelledEvaluation of this LabelledStatement with argument newLabelSet.

## 13.14 throw 语句 <div id="sec-throw-statement"></div>

Syntax

```
ThrowStatement[Yield, Await] :
    throw [no LineTerminator here] Expression[+In, ?Yield, ?Await] ;
```

### 13.14.1 RS: Evaluation <div id="sec-throw-statement-runtime-semantics-evaluation"></div>

```
ThrowStatement : throw Expression ;
```

1. Let exprRef be the result of evaluating Expression.
2. Let exprValue be ? GetValue(exprRef).
3. Return ThrowCompletion(exprValue).

## 13.15 try 语句 <div id="sec-try-statement"></div>

Syntax

```
TryStatement[Yield, Await, Return] :
    try Block[?Yield, ?Await, ?Return] Catch[?Yield, ?Await, ?Return]
    try Block[?Yield, ?Await, ?Return] Finally[?Yield, ?Await, ?Return]
    try Block[?Yield, ?Await, ?Return] Catch[?Yield, ?Await, ?Return]
    Finally[?Yield, ?Await, ?Return]
Catch[Yield, Await, Return] :
    catch ( CatchParameter[?Yield, ?Await] ) Block[?Yield, ?Await, ?Return]
    catch Block[?Yield, ?Await, ?Return]
Finally[Yield, Await, Return] :
    finally Block[?Yield, ?Await, ?Return]
CatchParameter[Yield, Await] :
    BindingIdentifier[?Yield, ?Await]
    BindingPattern[?Yield, ?Await]
```

> NOTE The try statement encloses a block of code in which an exceptional condition can occur, such as a runtime error or a throw statement. The catch clause provides the exception-handling code. When a catch clause catches an exception, its CatchParameter is bound to that exception.

### 13.15.1 SS: Early Errors <div id="sec-try-statement-static-semantics-early-errors"></div>

```
Catch : catch ( CatchParameter ) Block
```

It is a Syntax Error if BoundNames of CatchParameter contains any duplicate elements.
It is a Syntax Error if any element of the BoundNames of CatchParameter also occurs in the
LexicallyDeclaredNames of Block.

It is a Syntax Error if any element of the BoundNames of CatchParameter also occurs in the VarDeclaredNames of Block.

> NOTE An alternative static semantics for this production is given in B.3.5.

### 13.15.2 SS: ContainsDuplicateLabels <div id="sec-try-statement-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
TryStatement : try Block Catch
```

1. Let hasDuplicates be ContainsDuplicateLabels of Block with argument labelSet.
2. If hasDuplicates is true, return true.
3. Return ContainsDuplicateLabels of Catch with argument labelSet.

```
TryStatement : try Block Finally
```

1. Let hasDuplicates be ContainsDuplicateLabels of Block with argument labelSet.
2. If hasDuplicates is true, return true.
3. Return ContainsDuplicateLabels of Finally with argument labelSet.

```
TryStatement : try Block Catch Finally
```

1. Let hasDuplicates be ContainsDuplicateLabels of Block with argument labelSet.
2. If hasDuplicates is true, return true.
3. Let hasDuplicates be ContainsDuplicateLabels of Catch with argument labelSet.
4. If hasDuplicates is true, return true.
5. Return ContainsDuplicateLabels of Finally with argument labelSet.

```
Catch : catch ( CatchParameter ) Block
```

1. Return ContainsDuplicateLabels of Block with argument labelSet.

### 13.15.3 SS: ContainsUndefinedBreakTarget <div id="sec-try-statement-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
TryStatement : try Block Catch
```

1. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of Block with argument labelSet.
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedBreakTarget of Catch with argument labelSet.

```
TryStatement : try Block Finally
```

1. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of Block with argument labelSet.
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedBreakTarget of Finally with argument labelSet.

```
TryStatement : try Block Catch Finally
```

1. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of Block with argument labelSet.
2. If hasUndefinedLabels is true, return true.
3. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of Catch with argument labelSet.
4. If hasUndefinedLabels is true, return true.
5. Return ContainsUndefinedBreakTarget of Finally with argument labelSet.

```
Catch : catch ( CatchParameter ) Block
```

1. Return ContainsUndefinedBreakTarget of Block with argument labelSet.

### 13.15.4 SS: ContainsUndefinedContinueTarget <div id="sec-try-statement-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
TryStatement : try Block Catch
```

1. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of Block with arguments iterationSet and « ».
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedContinueTarget of Catch with arguments iterationSet and « ».

```
TryStatement : try Block Finally
```

1. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of Block with arguments iterationSet and « ».
2. If hasUndefinedLabels is true, return true.
3. Return ContainsUndefinedContinueTarget of Finally with arguments iterationSet and « ».

```
TryStatement : try Block Catch Finally
```

1. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of Block with arguments iterationSet and « ».
2. If hasUndefinedLabels is true, return true.
3. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of Catch with arguments iterationSet and « ».
4. If hasUndefinedLabels is true, return true.
5. Return ContainsUndefinedContinueTarget of Finally with arguments iterationSet and « ».

```
Catch : catch ( CatchParameter ) Block
```

1. Return ContainsUndefinedContinueTarget of Block with arguments iterationSet and « ».

### 13.15.5 SS: VarDeclaredNames <div id="sec-try-statement-static-semantics-vardeclarednames"></div>

```
TryStatement : try Block Catch
```

1. Let names be VarDeclaredNames of Block.
2. Append to names the elements of the VarDeclaredNames of Catch.
3. Return names.

```
TryStatement : try Block Finally
```

1. Let names be VarDeclaredNames of Block.
2. Append to names the elements of the VarDeclaredNames of Finally.
3. Return names.

```
TryStatement : try Block Catch Finally
```

1. Let names be VarDeclaredNames of Block.
2. Append to names the elements of the VarDeclaredNames of Catch.
3. Append to names the elements of the VarDeclaredNames of Finally.
4. Return names.

```
Catch : catch ( CatchParameter ) Block
```

1. Return the VarDeclaredNames of Block.

### 13.15.6 SS: VarScopedDeclarations <div id="sec-try-statement-static-semantics-varscopeddeclarations"></div>

```
TryStatement : try Block Catch
```

1. Let declarations be VarScopedDeclarations of Block.
2. Append to declarations the elements of the VarScopedDeclarations of Catch.
3. Return declarations.

```
TryStatement : try Block Finally
```

1. Let declarations be VarScopedDeclarations of Block.
2. Append to declarations the elements of the VarScopedDeclarations of Finally.
3. Return declarations.

```
TryStatement : try Block Catch Finally
```

1. Let declarations be VarScopedDeclarations of Block.
2. Append to declarations the elements of the VarScopedDeclarations of Catch.
3. Append to declarations the elements of the VarScopedDeclarations of Finally.
4. Return declarations.

```
Catch : catch ( CatchParameter ) Block
```

1. Return the VarScopedDeclarations of Block.

### 13.15.7 RS: CatchClauseEvaluation <div id="sec-runtime-semantics-catchclauseevaluation"></div>

With parameter thrownValue.

```
Catch : catch ( CatchParameter ) Block
```

1. Let oldEnv be the running execution context's LexicalEnvironment.
2. Let catchEnv be NewDeclarativeEnvironment(oldEnv).
3. Let catchEnvRec be catchEnv's EnvironmentRecord.
4. For each element argName of the BoundNames of CatchParameter, do
a. Perform ! catchEnvRec.CreateMutableBinding(argName, false).
5. Set the running execution context's LexicalEnvironment to catchEnv.
6. Let status be the result of performing BindingInitialization for CatchParameter passing thrownValue and catchEnv
as arguments.
7. If status is an abrupt completion, then
a. Set the running execution context's LexicalEnvironment to oldEnv.
b. Return Completion(status).
8. Let B be the result of evaluating Block.
9. Set the running execution context's LexicalEnvironment to oldEnv.
10. Return Completion(B).

```
Catch : catch Block
```

1. Return the result of evaluating Block

> NOTE No matter how control leaves the Block the LexicalEnvironment is always restored to its former state.

### 13.15.8 RS: Evaluation <div id="sec-try-statement-runtime-semantics-evaluation"></div>

```
TryStatement : try Block Catch
```

1. Let B be the result of evaluating Block.
2. If B.[[Type]] is throw, let C be CatchClauseEvaluation of Catch with argument B.[[Value]].
3. Else, let C be B.
4. Return Completion(UpdateEmpty(C, undefined)).

```
TryStatement : try Block Finally
```

1. Let B be the result of evaluating Block.
2. Let F be the result of evaluating Finally.
3. If F.[[Type]] is normal, set F to B.
4. Return Completion(UpdateEmpty(F, undefined)).

```
TryStatement : try Block Catch Finally
```

1. Let B be the result of evaluating Block.
2. If B.[[Type]] is throw, let C be CatchClauseEvaluation of Catch with argument B.[[Value]].
3. Else, let C be B.
4. Let F be the result of evaluating Finally.
5. If F.[[Type]] is normal, set F to C.
6. Return Completion(UpdateEmpty(F, undefined)).

## 13.16 debugger 语句 <div id="sec-debugger-statement"></div>

Syntax

```
DebuggerStatement :
    debugger ;
```

### 13.16.1 RS: Evaluation <div id="sec-debugger-statement-runtime-semantics-evaluation"></div>

> NOTE Evaluating a DebuggerStatement may allow an implementation to cause a breakpoint when run under a debugger. If a debugger is not present or active this statement has no observable effect.

1. If an implementation-defined debugging facility is available and enabled, then
a. Perform an implementation-defined debugging action.
b. Let result be an implementation-defined Completion value.
2. Else,
a. Let result be NormalCompletion(empty).
3. Return result.