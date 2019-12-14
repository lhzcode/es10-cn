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

### 13.1.1 静态语义：ContainsDuplicateLabels <div id="sec-statement-semantics-static-semantics-containsduplicatelabels"></div>

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

1. 返回 false.

### 13.1.2 静态语义：ContainsUndefinedBreakTarget <div id="sec-statement-semantics-static-semantics-containsundefinedbreaktarget"></div>

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

1. 返回 false.

### 13.1.3 静态语义：ContainsUndefinedContinueTarget <div id="sec-statement-semantics-static-semantics-containsundefinedcontinuetarget"></div>

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

1. 返回 false.

```
BreakableStatement:IterationStatement
```

1. 令 newIterationSet 为附加了labelSet的所有元素的迭代集的副本。
2. 返回包含带有参数newIterationSet和«»的IterationStatement的UndefinedContinueTarget。

### 13.1.4 静态语义：DeclarationPart <div id="sec-static-semantics-declarationpart"></div>

```
HoistableDeclaration : FunctionDeclaration
```

1. 返回 FunctionDeclaration.

```
HoistableDeclaration : GeneratorDeclaration
```

1. 返回 GeneratorDeclaration.

```
HoistableDeclaration : AsyncFunctionDeclaration
```

1. 返回 AsyncFunctionDeclaration.

```
HoistableDeclaration : AsyncGeneratorDeclaration
```

1. 返回 AsyncGeneratorDeclaration.

```
Declaration : ClassDeclaration
```

1. 返回 ClassDeclaration.

```
Declaration : LexicalDeclaration
```

1. 返回 LexicalDeclaration.

### 13.1.5 静态语义：VarDeclaredNames <div id="sec-statement-semantics-static-semantics-vardeclarednames"></div>

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

1. 返回一个新的空列表.

### 13.1.6 静态语义：VarScopedDeclarations <div id="sec-statement-semantics-static-semantics-varscopeddeclarations"></div>

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

1. 返回一个新的空列表.

### 13.1.7 运行时语义：LabelledEvaluation <div id="sec-statement-semantics-runtime-semantics-labelledevaluation"></div>

使用参数 labelSet

```
BreakableStatement:IterationStatement
```

1. 令 stmtResult 为使用参数labelSet执行IterationStatement的LabelledEvaluation的结果
2. 若 stmtResult.[[Type]] 是 break，那么
   1. 若 stmtResult.[[Target]] 是 empty，那么
        1. 若 stmtResult.[[Value]] 是 empty, 设置 stmtResult 为 NormalCompletion(undefined).
        2. 否则，设置 stmtResult 为 NormalCompletion(stmtResult.[[Value]]).
3. 返回 Completion(stmtResult).

```
BreakableStatement:SwitchStatement
```

1. 令 stmtResult 为 SwitchStatement 的运算结果
2. 若 stmtResult.[[Type]] 是 break，那么
   1. 若 stmtResult.[[Target]] 是 empty，那么
        1. 若 stmtResult.[[Value]] 是 empty, 设置 stmtResult 为 NormalCompletion(undefined).
        2. 否则，设置 stmtResult 为 NormalCompletion(stmtResult.[[Value]]).
3. 返回 Completion(stmtResult).

> 注：BreakableStatement 是可以通过未标记的BreakStatement退出

### 13.1.8 运行时语义：Evaluation <div id="sec-statement-semantics-runtime-semantics-evaluation"></div>

```
HoistableDeclaration :
GeneratorDeclaration
AsyncFunctionDeclaration
AsyncGeneratorDeclaration
```

1. 返回 NormalCompletion(empty).

```
HoistableDeclaration : FunctionDeclaration
```

1. 返回 FunctionDeclaration 的运算结果

```
BreakableStatement:
    IterationStatement
    SwitchStatement
```

1. 令 newLabelSet 为一个新的空列表.
2. 返回使用参数newLabelSet执行此BreakableStatement的LabelledEvaluation的结果

## 13.2 块 <div id="sec-block"></div>

**语法**

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

### 13.2.1 静态语义：Early Errors <div id="sec-block-static-semantics-early-errors"></div>

```
Block : { StatementList }
```

- 如果StatementList的LexicallyDeclaredNames包含任何重复的条目，则是语法错误。
- 如果StatementList的LexicallyDeclaredNames中的任何元素也出现在StatementList的VarDeclaredNames中，则是语法错误。

### 13.2.2 静态语义：ContainsDuplicateLabels <div id="sec-block-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
Block : { }
```

1. 返回 false.

```
StatementList : StatementList StatementListItem
```

1. 令 hasDuplicates 为带有参数labelSet的StatementList的ContainsDuplicateLabels。
2. 若 hasDuplicates 是 true，返回 true.
3. 返回带有参数labelSet的 StatementListItem 的ContainsDuplicateLabels。

```
StatementListItem : Declaration
```

1. 返回 false.

### 13.2.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-block-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
Block : { }
```

1. 返回 false.

```
StatementList : StatementList StatementListItem
```

1. 令 hasUndefinedLabels 为带有参数labelSet的StatementList的ContainsUndefinedBreakTarget。
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回带有参数 labelSet 的 StatementListItem 的 ContainsUndefinedBreakTarget

```
StatementListItem : Declaration
```

1. 返回 false.

### 13.2.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-block-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
Block : { }
```

1. 返回 false.

```
StatementList : StatementList StatementListItem
```

1. 令 hasUndefinedLabels 为带有参数 iterationSet 和 « » 的 StatementList 的 ContainsUndefinedContinueTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回带有参数 iterationSet 和 « » 的 StatementListItem 的 ContainsUndefinedContinueTarget

```
StatementListItem : Declaration
```

1. 返回 false.

### 13.2.5 静态语义：LexicallyDeclaredNames <div id="sec-block-static-semantics-lexicallydeclarednames"></div>

```
Block : { }
```

1. 返回一个新的空列表.

```
StatementList : StatementList StatementListItem
```

1. 令 names 为 StatementList 的 LexicallyDeclaredNames
2. 在 names 后追加 StatementListItem 的 LexicallyDeclaredNames 的元素
3. 返回 names.

```
StatementListItem : Statement
```

1. 若 Statement 是 Statement : LabelledStatement ，返回 LabelledStatement 的 LexicallyDeclaredNames
2. 返回一个新的空列表.

```
StatementListItem : Declaration
```

1. 返回  Declaration 的 BoundNames

### 13.2.6 静态语义：LexicallyScopedDeclarations <div id="sec-block-static-semantics-lexicallyscopeddeclarations"></div>

```
StatementList : StatementList StatementListItem
```

1. 令 declarations 为 StatementList 的 LexicallyScopedDeclarations
2. 在 declarations 后追加 StatementListItem 的 LexicallyScopedDeclarations 元素 
3. 返回 declarations.

```
StatementListItem : Statement
```

1. 若 Statement 是 Statement : LabelledStatement ，返回 LabelledStatement 的 LexicallyScopedDeclarations
2. 返回一个新的空列表.

```
StatementListItem : Declaration
```

1. 返回包含 Declaration 的 DeclarationPart 的新列表

### 13.2.7 静态语义：TopLevelLexicallyDeclaredNames <div id="sec-block-static-semantics-toplevellexicallydeclarednames"></div>

```
StatementList : StatementList StatementListItem
```

1. 令 names 为 StatementList 的 TopLevelLexicallyDeclaredNames
2. 在 names 后追加 StatementListItem 的 TopLevelLexicallyDeclaredNames 元素 
3. 返回 names.

```
StatementListItem : Statement
```

1. 返回一个新的空列表.

```
StatementListItem : Declaration
```

1. 若 Declaration 是 Declaration : HoistableDeclaration ，那么
1.  返回 « ».
2. 返回  Declaration 的 BoundNames

> 注：在函数或脚本的顶层，函数声明被视为var声明，而不是词法声明。

### 13.2.8 静态语义：TopLevelLexicallyScopedDeclarations <div id="sec-block-static-semantics-toplevellexicallyscopeddeclarations"></div>

```
Block : { }
```

1. 返回一个新的空列表.

```
StatementList : StatementList StatementListItem
```

1. 令 declarations 为 StatementList 的 TopLevelLexicallyScopedDeclarations
2. 在 declarations 后追加 StatementListItem 的 TopLevelLexicallyScopedDeclarations 元素 
3. 返回 declarations.

```
StatementListItem : Statement
```

1. 返回一个新的空列表.

```
StatementListItem : Declaration
```

1. 若 Declaration 是 Declaration : HoistableDeclaration ，那么
1. 返回 « ».
2. 返回包含 Declaration 的新列表

### 13.2.9 静态语义：TopLevelVarDeclaredNames <div id="sec-block-static-semantics-toplevelvardeclarednames"></div>

```
Block : { }
```

1. 返回一个新的空列表.

```
StatementList : StatementList StatementListItem
```

1. 令 names 为 StatementList 的 TopLevelVarDeclaredNames
2. 在 names 后追加 StatementListItem 的 TopLevelVarDeclaredNames 元素 
3. 返回 names.

```
StatementListItem : Declaration
```

1. 若 Declaration 是 Declaration : HoistableDeclaration ，那么
1. 返回  HoistableDeclaration 的 BoundNames
2. 返回一个新的空列表.

```
StatementListItem : Statement
```

1. 若 Statement 是 Statement : LabelledStatement ，返回 Statement 的 TopLevelVarDeclaredNames
2. 返回 Statement 的 VarDeclaredNames

> 注：在函数或脚本的顶层，内部函数声明被视为var声明。

### 13.2.10 静态语义：TopLevelVarScopedDeclarations <div id="sec-block-static-semantics-toplevelvarscopeddeclarations"></div>

```
Block : { }
```

1. 返回一个新的空列表

```
StatementList : StatementList StatementListItem
```

1. 令 declarations 为 StatementList 的 TopLevelVarScopedDeclarations
2. 在 declarations 后追加 StatementListItem 的 TopLevelVarScopedDeclarations 元素 
3. 返回 declarations.

```
StatementListItem : Statement
```

1. 若 Statement 是 Statement : LabelledStatement ，返回 Statement 的 TopLevelVarScopedDeclarations
2. 返回 Statement 的 VarScopedDeclarations

```
StatementListItem : Declaration
```

1. 若 Declaration 是 Declaration : HoistableDeclaration ，那么
1. 令 declaration 为 HoistableDeclaration 的 DeclarationPart
2. 返回 « declaration ».
2. 返回一个新的空列表.

### 13.2.11 静态语义：VarDeclaredNames <div id="sec-block-static-semantics-vardeclarednames"></div>

```
Block : { }
```

1. 返回一个新的空列表.

```
StatementList : StatementList StatementListItem
```

1. 令 names 为 StatementList 的 VarDeclaredNames
2. 在 names 后追加 StatementListItem 的 VarDeclaredNames 元素 
3. 返回 names.

```
StatementListItem : Declaration
```

1. 返回一个新的空列表.

### 13.2.12 静态语义：VarScopedDeclarations <div id="sec-block-static-semantics-varscopeddeclarations"></div>

```
Block : { }
```

1. 返回一个新的空列表.

```
StatementList : StatementList StatementListItem
```

1. 令 declarations 为 StatementList 的 VarScopedDeclarations
2. 在 declarations 后追加 StatementListItem 的 VarScopedDeclarations 元素 
3. 返回 declarations.

```
StatementListItem : Declaration
```

1. 返回一个新的空列表.

### 13.2.13 运行时语义：Evaluation <div id="sec-block-runtime-semantics-evaluation"></div>

```
Block : { }
```

1. 返回 NormalCompletion(empty)

```
Block : { StatementList }
```

1. 令 oldEnv 为运行时执行上下文的词法环境.
2. 令 blockEnv 为 NewDeclarativeEnvironment(oldEnv).
3. 执行 BlockDeclarationInstantiation(StatementList, blockEnv).
4. 设置运行时执行上下文的词法环境 为 blockEnv.
5. 令 blockValue 为 StatementList 的运算结果
6. 设置运行时执行上下文的词法环境 为 oldEnv.
7. 返回 blockValue.

> 注1：无论控制如何离开Block，词法环境总是恢复到原来的状态。

```
StatementList : StatementList StatementListItem
```

1. 令 sl 为 StatementList 的运算结果
2. ReturnIfAbrupt(sl).
3. 令 s 为 StatementListItem 的运算结果
4. 返回 Completion(UpdateEmpty(s, sl)).

> 注2： StatementList的值是StatementList中最后一个值生成项的值。例如，对val函数的以下调用都返回值1：
>
> eval("1;;;;;")
> eval("1;{}")
> eval("1;var a;")

### 13.2.14 运行时语义：BlockDeclarationInstantiation ( code, env ) <div id="sec-blockdeclarationinstantiation"></div>

> 注：在计算块Block或CaseBlock时，将创建新的声明式环境记录，并在环境记录中实例化块中声明的每个块作用域变量、常量、函数或类的绑定。

BlockDeclarationInstantiation使用参数code和env执行如下。code是与块的主体相对应的Parse节点。Env是创建绑定的词汇环境。

1. 令 envRec 为 env 的环境记录项.
2. 断言: envRec 是声明环境记录项.
3. 令 declarations 为  code 的 LexicallyScopedDeclarations
4. 对于declarations中的元素d，执行
  1. 对于d的绑定名称中的元素d，执行
        1. 若 d 的 IsConstantDeclaration 是 true，那么
      1. 执行 ! envRec.CreateImmutableBinding(dn, true).
        2. 否则，
      1. 执行 ! envRec.CreateMutableBinding(dn, false).
  2.  若 d 是 FunctionDeclaration, GeneratorDeclaration, AsyncFunctionDeclaration, 或 

    AsyncGeneratorDeclaration，那么
    1. 令 fn 为 d 的绑定名称的唯一元素
    2. 令 fo 为使用参数env为d执行InstantiateFunctionObject的结果
    3. 执行 envRec.InitializeBinding(fn, fo).

## 13.3 声明和变量语句 <div id="sec-declarations-and-the-variable-statement"></div>

### 13.3.1 Let 和 Const 声明 <div id="sec-let-and-const-declarations"></div>

> 注：let 和 const 声明定义范围为运行时的执行上下文的词法环境的变量。实例化其包含的词法环境时创建了变量，但可能无法以任何方式对其进行访问直到运算变量的LexicalBinding。由带有初始化程序的LexicalBinding定义的变量被分配给在评估LexicalBinding时而不是在创建变量时，其初始化程序的AssignmentExpression的值。如果let声明中的LexicalBinding没有初始化程序，则在运算LexicalBinding时会为变量分配undefined的值。

**语法**

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

#### 13.3.1.1 静态语义：Early Errors <div id="sec-let-and-const-declarations-static-semantics-early-errors"></div>

```
LexicalDeclaration : LetOrConst BindingList ;
```

- 如果BindingList的BoundNames包含“ let”，则是语法错误。
- 如果BindingList的BoundNames包含任何重复的条目，则是语法错误。

```
LexicalBinding : BindingIdentifier Initializer
```

- 如果没有初始化程序，并且包含此LexicalBinding的LexicalDeclaration的IsConstantDeclaration为true，则为语法错误。

#### 13.3.1.2 静态语义：BoundNames <div id="sec-let-and-const-declarations-static-semantics-boundnames"></div>

```
LexicalDeclaration : LetOrConst BindingList ;
```

1. 返回  BindingList 的 BoundNames

```
BindingList : BindingList , LexicalBinding
```

1. 令 names 为  BindingList 的 BoundNames
2. 在 names 后追加 LexicalBinding 的 BoundNames 元素 
3. 返回 names

```
LexicalBinding : BindingIdentifier Initializer
```

1. 返回  BindingIdentifier 的 BoundNames

```
LexicalBinding : BindingPattern Initializer
```

1. 返回  BindingPattern 的 BoundNames

#### 13.3.1.3 静态语义：IsConstantDeclaration <div id="sec-let-and-const-declarations-static-semantics-isconstantdeclaration"></div>

```
LexicalDeclaration : LetOrConst BindingList ;
```

1. 返回 LetOrConst 的 IsConstantDeclaration

```
LetOrConst : let
```

1. 返回 false

```
LetOrConst : const
```

1. 返回 true.

#### 13.3.1.4 运行时语义：Evaluation <div id="sec-let-and-const-declarations-runtime-semantics-evaluation"></div>

```
LexicalDeclaration : LetOrConst BindingList ;
```

1. 令 next 为 BindingList 的运算结果
2. ReturnIfAbrupt(next).
3. 返回 NormalCompletion(empty).

```
BindingList : BindingList , LexicalBinding
```

1. 令 next 为 BindingList 的运算结果
2. ReturnIfAbrupt(next).
3. 返回 LexicalBinding 的运算结果

```
LexicalBinding : BindingIdentifier
```

1. 令 lhs 为 ResolveBinding(StringValue of BindingIdentifier).
2. 返回 InitializeReferencedBinding(lhs, undefined).

> 注：静态语义规则确保LexicalBinding的这种形式永远不会在const声明中发生。

```
LexicalBinding : BindingIdentifier Initializer
```

1. 令 bindingId 为 BindingIdentifier 的 StringValue
2. 令 lhs 为 ResolveBinding(bindingId).
3. 若 IsAnonymousFunctionDefinition(Initializer) 是 true，那么
1. 令 value 为使用参数bindingId为Initializer执行NamedEvaluation的结果
4. 否则，
  1. 令 rhs 为 Initializer 的运算结果
  2. 令 value 为 ? GetValue(rhs).
5. 返回 InitializeReferencedBinding(lhs, value).

```
LexicalBinding : BindingPattern Initializer
```

1. 令 rhs 为 Initializer 的运算结果
2. 令 value 为 ? GetValue(rhs).
3. 令 env 为运行时执行上下文的词法环境.

4. 返回使用value和env作为参数对BindingPattern执行BindingInitialization的结果

### 13.3.2 变量语句 <div id="sec-variable-statement"></div>

> 注：var语句声明范围为运行时的执行上下文的VariableEnvironment的变量。 Var变量在实例化其包含的词法环境时创建，并在创建时初始化为undefined。在任何VariableEnvironment的范围内，一个公共的BindingIdentifier可能会出现在多个VariableDeclaration中，但是这些声明仅共同定义了一个变量。由VariableDeclaration与Initializer定义的变量在执行VariableDeclaration时（而不是在创建变量时）被分配其Initializer的AssignmentExpression的值。

**语法**

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

#### 13.3.2.1 静态语义：BoundNames <div id="sec-variable-statement-static-semantics-boundnames"></div>

```
VariableDeclarationList : VariableDeclarationList , VariableDeclaration
```

1. 令 names 为 VariableDeclarationList 的 BoundNames
2. 在 names 后追加 VariableDeclaration 的 BoundNames 元素
3. 返回 names.

```
VariableDeclaration : BindingIdentifier Initializer
```

1. 返回  BindingIdentifier 的 BoundNames

```
VariableDeclaration : BindingPattern Initializer
```

1. 返回  BindingPattern 的 BoundNames

#### 13.3.2.2 静态语义：VarDeclaredNames <div id="sec-variable-statement-static-semantics-vardeclarednames"></div>

```
VariableStatement : var VariableDeclarationList ;
```

1. 返回 VariableDeclarationList 的 BoundNames

#### 13.3.2.3 静态语义：VarScopedDeclarations <div id="sec-variable-statement-static-semantics-varscopeddeclarations"></div>

```
VariableDeclarationList : VariableDeclaration
```

1. 返回包含 VariableDeclaration. 的新列表

```
VariableDeclarationList : VariableDeclarationList , VariableDeclaration
```

1. 令 declarations 为 VariableDeclarationList 的 VarScopedDeclarations
2. 将VariableDeclaration追加到declarations.
3. 返回 declarations.

#### 13.3.2.4 运行时语义：Evaluation <div id="sec-variable-statement-runtime-semantics-evaluation"></div>

```
VariableStatement : var VariableDeclarationList ;
```

1. 令 next 为 VariableDeclarationList 的运算结果
2. ReturnIfAbrupt(next).
3. 返回 NormalCompletion(empty)

```
VariableDeclarationList : VariableDeclarationList , VariableDeclaration
```

1. 令 next 为 VariableDeclarationList 的运算结果
2. ReturnIfAbrupt(next).
3. 返回 evaluating VariableDeclaration 的结果

```
VariableDeclaration : BindingIdentifier
```

1. 返回 NormalCompletion(empty).

```
VariableDeclaration : BindingIdentifier Initializer
```

1. 令 bindingId 为 BindingIdentifier 的 StringValue
2. 令 lhs 为 ? ResolveBinding(bindingId).
3. 若 IsAnonymousFunctionDefinition(Initializer) 是 true，那么
a. 令 value 为使用参数bindingId为Initializer执行NamedEvaluation的结果
4. 否则，
a. 令 rhs 为 Initializer 的运算结果
b. 令 value 为 ? GetValue(rhs).
5. 返回 ? PutValue(lhs, value).

> 注：如果VariableDeclaration嵌套在with语句中，并且VariableDeclaration中的BindingIdentifier与with语句的对象Environment Record的绑定对象的属性名称相同，则第6步将为该属性分配值，而不是分配给VariableEnvironment绑定标识符的

```
VariableDeclaration : BindingPattern Initializer
```

1. 令 rhs 为 Initializer 的运算结果
2. 令 rval 为 ? GetValue(rhs).
3. 返回为传递rval和undefined为参数的BindingPattern执行BindingInitialization的结果

### 13.3.3 绑定模式解构 <div id="sec-destructuring-binding-patterns"></div>

**语法**

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

#### 13.3.3.1 静态语义：BoundNames <div id="sec-destructuring-binding-patterns-static-semantics-boundnames"></div>

```
ObjectBindingPattern : { }
```

1. 返回一个新的空列表.

```
ArrayBindingPattern : [ Elision ]
```

1. 返回一个新的空列表.

```
ArrayBindingPattern : [ Elision BindingRestElement ]
```

1. 返回  BindingRestElement 的 BoundNames

```
ArrayBindingPattern : [ BindingElementList , Elision ]
```

1. 返回  BindingElementList 的 BoundNames

```
ArrayBindingPattern : [ BindingElementList , Elision BindingRestElement ]
```

1. 令 names 为 BindingElementList 的 BoundNames
2. 在 names 后追加 BindingRestElement 的 BoundNames 元素
3. 返回 names.

```
BindingPropertyList : BindingPropertyList , BindingProperty
```

1. 令 names 为 BindingPropertyList 的 BoundNames
2. 在 names 后追加 BindingProperty 的 BoundNames 元素
3. 返回 names.

```
BindingElementList : BindingElementList , BindingElisionElement
```

1. 令 names 为 BindingElementList 的 BoundNames
2. 在 names 后追加 BindingElisionElement 的 BoundNames 元素
3. 返回 names.

```
BindingElisionElement : Elision BindingElement
```

1. 返回 BindingElement 的 BoundNames

```
BindingProperty : PropertyName : BindingElement
```

1. 返回  BindingElement 的 BoundNames

```
SingleNameBinding : BindingIdentifier Initializer
```

1. 返回  BindingIdentifier 的 BoundNames

```
BindingElement : BindingPattern Initializer
```

1. 返回  BindingPattern 的 BoundNames

#### 13.3.3.2 静态语义：ContainsExpression <div id="sec-destructuring-binding-patterns-static-semantics-containsexpression"></div>

```
ObjectBindingPattern : { }
```

1. 返回 false.

```
ArrayBindingPattern : [ Elision ]
```

1. 返回 false.

```
ArrayBindingPattern : [ Elision BindingRestElement ]
```

1. 返回 BindingRestElement 的 ContainsExpression

```
ArrayBindingPattern : [ BindingElementList , Elision ]
```

1. 返回 BindingElementList 的 ContainsExpression

```
ArrayBindingPattern : [ BindingElementList , Elision BindingRestElement ]
```

1. 令 has 为 BindingElementList 的 ContainsExpression
2. 若 has 是 true，返回 true.
3. 返回 BindingRestElement 的 ContainsExpression

```
BindingPropertyList : BindingPropertyList , BindingProperty
```

1. 令 has 为 BindingPropertyList 的 ContainsExpression
2. 若 has 是 true，返回 true.
3. 返回 BindingProperty 的 ContainsExpression

```
BindingElementList : BindingElementList , BindingElisionElement
```

1. 令 has 为 BindingElementList 的 ContainsExpression
2. 若 has 是 true，返回 true.
3. 返回 BindingElisionElement 的 ContainsExpression

```
BindingElisionElement : Elision BindingElement
```

1. 返回 BindingElement 的 ContainsExpression

```
BindingProperty : PropertyName : BindingElement
```

1. 令 has 为 PropertyName 的 IsComputedPropertyKey
2. 若 has 是 true，返回 true.
3. 返回 BindingElement 的 ContainsExpression

```
BindingElement : BindingPattern Initializer
```

1. 返回 true.

```
SingleNameBinding : BindingIdentifier
```

1. 返回 false.

```
SingleNameBinding : BindingIdentifier Initializer
```

1. 返回 true.

```
BindingRestElement : ... BindingIdentifier
```

1. 返回 false.

```
BindingRestElement : ... BindingPattern
```

1. 返回 BindingPattern 的 ContainsExpression 

#### 13.3.3.3 静态语义：HasInitializer <div id="sec-destructuring-binding-patterns-static-semantics-hasinitializer"></div>

```
BindingElement : BindingPattern
```

1. 返回 false

```
BindingElement : BindingPattern Initializer
```

1. 返回 true.

```
SingleNameBinding : BindingIdentifier
```

1. 返回 false.

```
SingleNameBinding : BindingIdentifier Initializer
```

1. 返回 true.

#### 13.3.3.4 静态语义：IsSimpleParameterList <div id="sec-destructuring-binding-patterns-static-semantics-issimpleparameterlist"></div>

```
BindingElement : BindingPattern
```

1. 返回 false.

```
BindingElement : BindingPattern Initializer
```

1. 返回 false.

```
SingleNameBinding : BindingIdentifier
```

1. 返回 true

```
SingleNameBinding : BindingIdentifier Initializer
```

1. 返回 false.

#### 13.3.3.5 运行时语义：BindingInitialization <div id="sec-destructuring-binding-patterns-runtime-semantics-bindinginitialization"></div>

使用参数value和environment

> 注：当为环境传递undefined时，表明应该使用PutValue操作来分配初始化值。非严格函数的形式参数列表就是这种情况。在那种情况下，形式参数绑定是预先初始化的，以便处理多个具有相同名称的参数的可能性。

```
BindingPattern : ObjectBindingPattern
```

1. 执行 ? RequireObjectCoercible(value).
2. 返回使用value和environment作为参数对ObjectBindingPattern执行BindingInitialization的结果

```
BindingPattern : ArrayBindingPattern
```

1. 令 iteratorRecord 为 ? GetIterator(value).
2. 令 result 为使用iteratorRecord和环境作为参数的ArrayBindingPattern的IteratorBindingInitialization。
3. 若 iteratorRecord.[[Done]] 是 false，返回 ? IteratorClose(iteratorRecord, result).

4. 返回 result.

```
ObjectBindingPattern : { }
```

1. 返回 NormalCompletion(empty)

   ```
   ObjectBindingPattern :
       { BindingPropertyList }
       { BindingPropertyList , }
   ```

   1. 执行 ? 使用value和environment作为参数的BindingPropertyList的PropertyBindingInitialization。
   2. 返回 NormalCompletion(empty).

```
ObjectBindingPattern : { BindingRestProperty }
```

1. 令 excludedNames 为一个新的空列表.
2. 返回使用value，environment和excludeedNames作为参数执行BindingRestProperty的RestBindingInitialization的结果

```
ObjectBindingPattern : { BindingPropertyList , BindingRestProperty }
```

1. 令 excludedNames 为 执行 ? 使用value和environment作为参数的BindingPropertyList的PropertyBindingInitialization的结果
2. 返回以value，environment和excludeedNames作为参数执行BindingRestProperty的初始化的结果

#### 13.3.3.6 运行时语义：PropertyBindingInitialization <div id="sec-destructuring-binding-patterns-runtime-semantics-propertybindinginitialization"></div>

使用参数value和environment

>注：它们收集所有绑定属性名称的列表，而不仅仅是空完成。

```
BindingPropertyList : BindingPropertyList , BindingProperty
```

1. 令 boundNames 为 执行 ? 使用value和environment作为参数的BindingPropertyList的PropertyBindingInitialization。
2. 令 nextNames 为 执行 ? 使用value和environment作为参数的BindingProperty的PropertyBindingInitialization。
3. 将nextNames中的每一个项目追加到 boundNames 的末尾
4. 返回 boundNames.

```
BindingProperty : SingleNameBinding
```

1. 令 name 为 string，即是SingleNameBinding 的 BoundNames的唯一元素
2. 执行 ? 使用value和environment和name作为参数的SingleNameBinding的KeyedBindingInitialization。
3. 返回包含 name 的新列表

```
BindingProperty : PropertyName : BindingElement
```

1. 令 P 为 PropertyName 的运算结果
2. ReturnIfAbrupt(P).

3. 执行 ? 以value、environment和P作为参数的BindingElement的KeyedBindingInitialization。
4. 返回包含 P 的新列表

#### 13.3.3.7 运行时语义：RestBindingInitialization <div id="sec-destructuring-binding-patterns-runtime-semantics-restbindinginitialization"></div>

使用参数value, environment和excludedNames

```
BindingRestProperty : ... BindingIdentifier
```

1. 令 lhs 为 ? ResolveBinding(StringValue of BindingIdentifier, environment).
2. 令 restObj 为 ObjectCreate(%ObjectPrototype%).
3. 执行 ? CopyDataProperties(restObj, value, excludedNames).
4. 若 environment 是 undefined，返回 PutValue(lhs, restObj).
5. 返回 InitializeReferencedBinding(lhs, restObj).

#### 13.3.3.8 运行时语义：IteratorBindingInitialization <div id="sec-destructuring-binding-patterns-runtime-semantics-iteratorbindinginitialization"></div>

使用参数iteratorRecord和environment

>注：当为环境传递undefined时，表明应该使用PutValue操作来分配初始化值。非严格函数的形式参数列表就是这种情况。在那种情况下，形式参数绑定是预先初始化的，以便处理多个具有相同名称的参数的可能性。

```
ArrayBindingPattern : [ ]
```

1. 返回 NormalCompletion(empty).

```
ArrayBindingPattern : [ Elision ]
```

1. 返回以iteratorRecord作为参数执行Elision的IteratorDestructuringAssignmentEvaluation的结果

```
ArrayBindingPattern : [ Elision BindingRestElement ]
```

1. 若 Elision 存在，那么
1. 执行 ? 以iteratorRecord作为参数的Elision的IteratorDestructuringAssignmentEvaluation
2. 返回使用iteratorRecord和environment作为参数对BindingRestElement执行IteratorBindingInitialization的结果

```
ArrayBindingPattern : [ BindingElementList ]
```

1. 返回使用iteratorRecord和environment作为参数对BindingElementList执行IteratorBindingInitialization的结果。

```
ArrayBindingPattern : [ BindingElementList , ]
```

1. 返回使用iteratorRecord和environment作为参数对BindingElementList执行IteratorBindingInitialization的结果

```
ArrayBindingPattern : [ BindingElementList , Elision ]
```

1. 执行 ? 带有IteratorRecord和environment作为参数的BindingElementList的IteratorBindingInitialization。

2. 返回以iteratorRecord作为参数执行Elision的IteratorDestructuringAssignmentEvaluation的结果

```
ArrayBindingPattern : [ BindingElementList , Elision BindingRestElement ]
```

1. 执行 ? 带有IteratorRecord和environment作为参数的BindingElementList的IteratorBindingInitialization。
2. 若 Elision 存在，那么
1. 执行 ? 以iteratorRecord作为参数的Elision的IteratorDestructuringAssignmentEvaluation
3. 返回使用iteratorRecord和environment环境作为参数对BindingRestElement执行IteratorBindingInitialization的结果

```
BindingElementList : BindingElisionElement
```

1. 返回使用iteratorRecord和environment环境作为参数对BindingElisionElement执行IteratorBindingInitialization的结果

```
BindingElementList : BindingElementList , BindingElisionElement
```

1. 执行 ? IteratorBindingInitialization for BindingElementList with iteratorRecord and environment as arguments.
2. 返回使用iteratorRecord和environment作为参数对BindingElisionElement执行IteratorBindingInitialization的结果

```
BindingElisionElement : BindingElement
```

1. 返回使用iteratorRecord和environment作为参数执行BindingElement的IteratorBindingInitialization。

```
BindingElisionElement : Elision BindingElement
```

1. 执行 ? 以iteratorRecord作为参数的Elision的IteratorDestructuringAssignmentEvaluation
2. 返回使用iteratorRecord和environment作为参数执行BindingElement的IteratorBindingInitialization的结果

```
BindingElement : SingleNameBinding
```

1. 返回使用iteratorRecord和环境作为参数对SingleNameBinding执行IteratorBindingInitialization的结果

```
SingleNameBinding : BindingIdentifier Initializer
```

1. 令 bindingId 为 BindingIdentifier 的 StringValue
2. 令 lhs 为 ? ResolveBinding(bindingId, environment).
3. 若 iteratorRecord.[[Done]] 是 false，那么
  1. 令 next 为 IteratorStep(iteratorRecord).
  2. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
  3. ReturnIfAbrupt(next).
  4. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
  5. 否则，
        1. 令 v 为 IteratorValue(next).
            2. 若 v 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
                3. ReturnIfAbrupt(v).
4. 若 iteratorRecord.[[Done]] 是 true, 令 v 为 undefined.
5. 若 Initializer 存在 并且 v 是 undefined，那么
   1. 若 IsAnonymousFunctionDefinition(Initializer) 是 true，那么
      1. 设置 v 为使用参数bindingId为Initializer执行NamedEvaluation的结果
   2. 否则，
      1. 令 defaultValue 为 Initializer 的运算结果
      2. 设置 v 为 ? GetValue(defaultValue).
6. 若 environment 是 undefined，返回 ? PutValue(lhs, v).
7. 返回 InitializeReferencedBinding(lhs, v).

```
BindingElement : BindingPattern Initializer
```

1. 若 iteratorRecord.[[Done]] 是 false，那么
   1. 令 next 为 IteratorStep(iteratorRecord).
   2. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
   3. ReturnIfAbrupt(next).
   4. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
   5. 否则，
        1. 令 v 为 IteratorValue(next).
        2. 若 v 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
        3. ReturnIfAbrupt(v).
2. 若 iteratorRecord.[[Done]] 是 true, 令 v 为 undefined.
3. 若 Initializer 存在 并且 v 是 undefined，那么
     1. 令 defaultValue 为 Initializer 的运算结果
     2. 设置 v 为 ? GetValue(defaultValue).
4. 返回使用v和environment作为参数执行BindingPattern的BindingInitialization的结果

```
BindingRestElement : ... BindingIdentifier
```

1. 令 lhs 为 ? ResolveBinding(StringValue of BindingIdentifier, environment).
2. 令 A 为 ! ArrayCreate(0).
3. 令 n 为 0.
4. 重复，
   1. 若 iteratorRecord.[[Done]] 是 false，那么
        1. 令 next 为 IteratorStep(iteratorRecord).
        2. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
        3. ReturnIfAbrupt(next).
        4. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
   2. 若 iteratorRecord.[[Done]] 是 true，那么
        1. 若 environment 是 undefined，返回 ? PutValue(lhs, A).
        2. 返回 InitializeReferencedBinding(lhs, A).
   3. 令 nextValue 为 IteratorValue(next).
   4. 若 nextValue 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
   5. ReturnIfAbrupt(nextValue).
   6. 令 status 为 CreateDataProperty(A, ! ToString(n), nextValue).
   7. 断言: status 是 true.
   8. n增加1.

```
BindingRestElement : ... BindingPattern
```

1. 令 A 为 ! ArrayCreate(0).
2. 令 n 为 0.
3. 重复，
  1. 若 iteratorRecord.[[Done]] 是 false，那么
        1. 令 next 为 IteratorStep(iteratorRecord).
            2. 若 next 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
                3. ReturnIfAbrupt(next).
                    4. 若 next 是 false, 设置 iteratorRecord.[[Done]] 为 true.
  2. 若 iteratorRecord.[[Done]] 是 true，那么
        1. 使用A和environment作为参数执行BindingPattern的BindingInitialization的结果。
  3. 令 nextValue 为 IteratorValue(next).
  4. 若 nextValue 是 abrupt completion, 设置 iteratorRecord.[[Done]] 为 true.
  5. ReturnIfAbrupt(nextValue).
  6. 令 status 为 CreateDataProperty(A, ! ToString(n), nextValue).
  7. 断言: status 是 true.
  8. n增加1.

#### 13.3.3.9 运行时语义：KeyedBindingInitialization <div id="sec-runtime-semantics-keyedbindinginitialization"></div>

使用参数value, environment,和propertyName

> 注：当为环境传递undefined时，表明应该使用PutValue操作来分配初始化值。非严格函数的形式参数列表就是这种情况。在那种情况下，形式参数绑定是预先初始化的，以便处理多个具有相同名称的参数的可能性。

```
BindingElement : BindingPattern Initializer
```

1. 令 v 为 ? GetV(value, propertyName).
2. 若 Initializer 存在，并且 v 是 undefined，那么
1. 令 defaultValue 为 Initializer 的运算结果
2. 设置 v 为 ? GetValue(defaultValue).
3. 返回通过将v和environment作为参数对BindingPattern执行BindingInitialization的结果

```
SingleNameBinding : BindingIdentifier Initializer
```

1. 令 bindingId 为 BindingIdentifier 的 StringValue
2. 令 lhs 为 ? ResolveBinding(bindingId, environment).
3. 令 v 为 ? GetV(value, propertyName).
4. 若 Initializer 存在，并且 v 是 undefined，那么
1. 若 IsAnonymousFunctionDefinition(Initializer) 是 true，那么
  1. 设置 v 为使用参数bindingId为Initializer执行NamedEvaluation的结果
2. 否则，
  1. 令 defaultValue 为 Initializer 的运算结果
  2. 设置 v 为 ? GetValue(defaultValue).
5. 若 environment 是 undefined，返回 ? PutValue(lhs, v).
6. 返回 InitializeReferencedBinding(lhs, v).

## 13.4 空语句 <div id="sec-empty-statement"></div>

**语法**

```
EmptyStatement :
;
```

### 13.4.1 运行时语义：Evaluation <div id="sec-empty-statement-runtime-semantics-evaluation"></div>

```
EmptyStatement : ;
```

1. 返回 NormalCompletion(empty).

## 13.5 表达式语句 <div id="sec-expression-statement"></div>

**语法**

```
ExpressionStatement[Yield, Await] :
    [lookahead ∉ { { , function , async [no LineTerminator here] function , class , let [ }]
        Expression[+In, ?Yield, ?Await] ;
```

>注：ExpressionStatement不能以U+007B（{）开头，因为这可能会使它与Block产生歧义。 ExpressionStatement不能以function或class关键字开头，因为这会使它与FunctionDeclaration，GeneratorDeclaration或ClassDeclaration产生歧义。 ExpressionStatement不能以异步函数开头，因为这会使它与AsyncFunctionDeclaration或AsyncGeneratorDeclaration产生歧义。 ExpressionStatement不能以两个令牌序列let [开头，因为这会使它与let LexicalDeclaration产生歧义，后者的第一个LexicalBinding是ArrayBindingPattern。

### 13.5.1 运行时语义：Evaluation <div id="sec-expression-statement-runtime-semantics-evaluation"></div>

```
ExpressionStatement : Expression ;
```

1. 令 exprRef 为 Expression 的运算结果
2. 返回 ? GetValue(exprRef).

## 13.6 if 语句 <div id="sec-if-statement"></div>

**语法**

```
IfStatement[Yield, Await, Return] :
    if ( Expression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return] else
    Statement[?Yield, ?Await, ?Return]
    if ( Expression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
```

Each else for which the choice of associated if 是 ambiguous shall be associated with the nearest possible if that would otherwise have no corresponding else.

### 13.6.1 静态语义：Early Errors <div id="sec-if-statement-static-semantics-early-errors"></div>

```
IfStatement :
    if ( Expression ) Statement else Statement
    if ( Expression ) Statement
```

It 是 a Syntax Error if IsLabelledFunction(Statement) 是 true.

> 注：It 是 only necessary to apply this rule if the extension specified in B.3.2 是 implemented.

### 13.6.2 静态语义：ContainsDuplicateLabels <div id="sec-if-statement-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 hasDuplicate 为 ContainsDuplicateLabels of the first Statement with argument labelSet.
2. 若 hasDuplicate 是 true，返回 true.
3. 返回 ContainsDuplicateLabels of the second Statement with argument labelSet.

```
IfStatement : if ( Expression ) Statement
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsDuplicateLabels

### 13.6.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-if-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 hasUndefinedLabels 为 ContainsUndefinedBreakTarget of the first Statement with argument labelSet.
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回 ContainsUndefinedBreakTarget of the second Statement with argument labelSet.

```
IfStatement : if ( Expression ) Statement
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsUndefinedBreakTarget

### 13.6.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-if-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 hasUndefinedLabels 为 ContainsUndefinedContinueTarget of the first Statement with arguments iterationSet
and « ».
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回 ContainsUndefinedContinueTarget of the second Statement with arguments iterationSet and « ».

```
IfStatement : if ( Expression ) Statement
```

1. 返回带有参数 iterationSet 和 « » 的 Statement 的 ContainsUndefinedContinueTarget

### 13.6.5 静态语义：VarDeclaredNames <div id="sec-if-statement-static-semantics-vardeclarednames"></div>

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 names 为 VarDeclaredNames of the first Statement.
2. 在 names 后追加 the VarDeclaredNames of the second Statement. 元素
3. 返回 names.

```
IfStatement : if ( Expression ) Statement
```

1. 返回  Statement 的 VarDeclaredNames

### 13.6.6 静态语义：VarScopedDeclarations <div id="sec-if-statement-static-semantics-varscopeddeclarations"></div>

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 declarations 为 VarScopedDeclarations of the first Statement.
2. 在 declarations 后追加 the VarScopedDeclarations of the second Statement. 元素
3. 返回 declarations.

```
IfStatement : if ( Expression ) Statement
```

1. 返回  Statement 的 VarScopedDeclarations

### 13.6.7 运行时语义：Evaluation <div id="sec-if-statement-runtime-semantics-evaluation"></div>

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 exprRef 为 Expression 的运算结果
2. 令 exprValue 为 ToBoolean(? GetValue(exprRef)).
3. 若 exprValue 是 true，那么
a. 令 stmtCompletion 为 the first Statement 的运算结果
4. 否则，
a. 令 stmtCompletion 为 the second Statement 的运算结果
5. 返回 Completion(UpdateEmpty(stmtCompletion, undefined))

```
IfStatement : if ( Expression ) Statement
```

1. 令 exprRef 为 Expression 的运算结果
2. 令 exprValue 为 ToBoolean(? GetValue(exprRef)).
3. 若 exprValue 是 false，那么
a. 返回 NormalCompletion(undefined).
4. 否则，
a. 令 stmtCompletion 为 Statement 的运算结果
b. 返回 Completion(UpdateEmpty(stmtCompletion, undefined)).

## 13.7 迭代语句 <div id="sec-iteration-statements"></div>

**语法**

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

> 注：This section 是 extended by Annex B.3.6

### 13.7.1 语义 <div id="sec-iteration-statements-semantics"></div>
#### 13.7.1.1 静态语义：Early Errors <div id="sec-semantics-static-semantics-early-errors"></div>

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

It 是 a Syntax Error if IsLabelledFunction(Statement) 是 true.

> 注：It 是 only necessary to apply this rule if the extension specified in B.3.2 是 implemented.

#### 13.7.1.2 运行时语义：LoopContinues ( completion, labelSet ) <div id="sec-loopcontinues"></div>

The abstract operation LoopContinues with arguments completion and labelSet 是 defined by the following steps:

1. 若 completion.[[Type]] 是 normal，返回 true.
2. 若 completion.[[Type]] 是 not continue，返回 false.
3. 若 completion.[[Target]] 是 empty，返回 true.
4. 若 completion.[[Target]] 是 an element of labelSet，返回 true.
5. 返回 false.

> 注：Within the Statement part of an IterationStatement a ContinueStatement may be used to begin a new iteration.

### 13.7.2 do-while 语句 <div id="sec-do-while-statement"></div>

#### 13.7.2.1 静态语义：ContainsDuplicateLabels <div id="sec-do-while-statement-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
IterationStatement : do Statement while ( Expression ) ;
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsDuplicateLabels

#### 13.7.2.2 静态语义：ContainsUndefinedBreakTarget <div id="sec-do-while-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
IterationStatement : do Statement while ( Expression ) ;
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsUndefinedBreakTarget

#### 13.7.2.3 静态语义：ContainsUndefinedContinueTarget <div id="sec-do-while-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
IterationStatement : do Statement while ( Expression ) ;
```

1. 返回带有参数 iterationSet 和 « » 的 Statement 的 ContainsUndefinedContinueTarget

#### 13.7.2.4 静态语义：VarDeclaredNames <div id="sec-do-while-statement-static-semantics-vardeclarednames"></div>

```
IterationStatement : do Statement while ( Expression ) ;
```

1. 返回  Statement 的 VarDeclaredNames

#### 13.7.2.5 静态语义：VarScopedDeclarations <div id="sec-do-while-statement-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : do Statement while ( Expression ) ;
```

1. 返回  Statement 的 VarScopedDeclarations

#### 13.7.2.6 运行时语义：LabelledEvaluation <div id="sec-do-while-statement-runtime-semantics-labelledevaluation"></div>

使用参数 labelSet

```
IterationStatement : do Statement while ( Expression ) ;
```

1. 令 V 为 undefined.
2. 重复，
a. 令 stmtResult 为 Statement 的运算结果
b. 若 LoopContinues(stmtResult, labelSet) 是 false，返回 Completion(UpdateEmpty(stmtResult, V)).
c. 若 stmtResult.[[Value]] 是 not empty, 设置 V 为 stmtResult.[[Value]].
d. 令 exprRef 为 Expression 的运算结果
e. 令 exprValue 为 ? GetValue(exprRef).
f. 若 ToBoolean(exprValue) 是 false，返回 NormalCompletion(V).

### 13.7.3 while 语句 <div id="sec-while-statement"></div>
#### 13.7.3.1 静态语义：ContainsDuplicateLabels <div id="sec-while-statement-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
IterationStatement : while ( Expression ) Statement
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsDuplicateLabels

#### 13.7.3.2 静态语义：ContainsUndefinedBreakTarget <div id="sec-while-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
IterationStatement : while ( Expression ) Statement
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsUndefinedBreakTarget

#### 13.7.3.3 静态语义：ContainsUndefinedContinueTarget <div id="sec-while-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
IterationStatement : while ( Expression ) Statement
```

1. 返回带有参数 iterationSet 和 « » 的 Statement 的 ContainsUndefinedContinueTarget

#### 13.7.3.4 静态语义：VarDeclaredNames <div id="sec-while-statement-static-semantics-vardeclarednames"></div>

```
IterationStatement : while ( Expression ) Statement
```

1. 返回  Statement 的 VarDeclaredNames

#### 13.7.3.5 静态语义：VarScopedDeclarations <div id="sec-while-statement-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : while ( Expression ) Statement
```

1. 返回  Statement 的 VarScopedDeclarations

#### 13.7.3.6 运行时语义：LabelledEvaluation <div id="sec-while-statement-runtime-semantics-labelledevaluation"></div>

使用参数 labelSet

```
IterationStatement : while ( Expression ) Statement
```

1. 令 V 为 undefined.
2. 重复，
a. 令 exprRef 为 Expression 的运算结果
b. 令 exprValue 为 ? GetValue(exprRef).
c. 若 ToBoolean(exprValue) 是 false，返回 NormalCompletion(V).
d. 令 stmtResult 为 Statement 的运算结果
e. 若 LoopContinues(stmtResult, labelSet) 是 false，返回 Completion(UpdateEmpty(stmtResult, V)).
f. 若 stmtResult.[[Value]] 是 not empty, 设置 V 为 stmtResult.[[Value]].

### 13.7.4 for 语句 <div id="sec-for-statement"></div>
#### 13.7.4.1 静态语义：Early Errors <div id="sec-for-statement-static-semantics-early-errors"></div>

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

It 是 a Syntax Error if any element of the BoundNames of LexicalDeclaration also occurs in  Statement 的 VarDeclaredNames

#### 13.7.4.2 静态语义：ContainsDuplicateLabels <div id="sec-for-statement-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
IterationStatement :
    for ( Expressionopt ; Expressionopt ; Expressionopt ) Statement
    for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
    for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsDuplicateLabels

#### 13.7.4.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-for-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
IterationStatement :
    for ( Expressionopt ; Expressionopt ; Expressionopt ) Statement
    for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
    for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement
```

1. 返回 ContainsUndefinedBreakTarget of Statement with argument labelSet

#### 13.7.4.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-for-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
IterationStatement :
    for ( Expressionopt ; Expressionopt ; Expressionopt ) Statement
    for ( var VariableDeclarationList ; Expressionopt ; Expressionopt ) Statement
    for ( LexicalDeclaration Expressionopt ; Expressionopt ) Statement
```

1. 返回带有参数 iterationSet 和 « » 的 Statement 的 ContainsUndefinedContinueTarget

#### 13.7.4.5 静态语义：VarDeclaredNames <div id="sec-for-statement-static-semantics-vardeclarednames"></div>

```
IterationStatement : for ( Expression ; Expression ; Expression ) Statement
```

1. 返回  Statement 的 VarDeclaredNames

```
IterationStatement : for ( var VariableDeclarationList ; Expression ; Expression ) Statement
```

1. 令 names 为 VariableDeclarationList 的 BoundNames
2. 在 names 后追加 Statement 的 VarDeclaredNames 元素 
3. 返回 names.

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

1. 返回  Statement 的 VarDeclaredNames

#### 13.7.4.6 静态语义：VarScopedDeclarations <div id="sec-for-statement-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : for ( Expression ; Expression ; Expression ) Statement
```

1. 返回  Statement 的 VarScopedDeclarations

```
IterationStatement : for ( var VariableDeclarationList ; Expression ; Expression ) Statement
```

1. 令 declarations 为 VariableDeclarationList 的 VarScopedDeclarations

2. 在 declarations 后追加 Statement 的 VarScopedDeclarations 元素 
3. 返回 declarations.

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

1. 返回  Statement 的 VarScopedDeclarations

#### 13.7.4.7 运行时语义：LabelledEvaluation <div id="sec-for-statement-runtime-semantics-labelledevaluation"></div>

使用参数 labelSet

```
IterationStatement : for ( Expression ; Expression ; Expression ) Statement
```

1. 若 the first Expression 存在，那么
a. 令 exprRef 为 the first Expression 的运算结果
b. 执行 ? GetValue(exprRef).
2. 返回 ? ForBodyEvaluation(the second Expression, the third Expression, Statement, « », labelSet).

```
IterationStatement : for ( var VariableDeclarationList ; Expression ; Expression ) Statement
```

1. 令 varDcl 为 VariableDeclarationList 的运算结果
2. ReturnIfAbrupt(varDcl).
3. 返回 ? ForBodyEvaluation(the first Expression, the second Expression, Statement, « », labelSet).

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

1. 令 oldEnv 为运行时执行上下文的词法环境.
2. 令 loopEnv 为 NewDeclarativeEnvironment(oldEnv).
3. 令 loopEnvRec 为 loopEnv's EnvironmentRecord.
4. 令 isConst 为 performing LexicalDeclaration 的 IsConstantDeclaration 的结果
5. 令 boundNames 为  LexicalDeclaration 的 BoundNames
6. For each element dn of boundNames, do
a. 若 isConst 是 true，那么
i. 执行 ! loopEnvRec.CreateImmutableBinding(dn, true).
b. 否则，
i. 执行 ! loopEnvRec.CreateMutableBinding(dn, false).
7. 设置运行时执行上下文的词法环境 为 loopEnv.
8. 令 forDcl 为 LexicalDeclaration 的运算结果
9. 若 forDcl 是 abrupt completion，那么
a. 设置运行时执行上下文的词法环境 为 oldEnv.
b. 返回 Completion(forDcl).
10. 若 isConst 是 false, let perIterationLets be boundNames; otherwise let perIterationLets be « ».
11. 令 bodyResult 为 ForBodyEvaluation(the first Expression, the second Expression, Statement, perIterationLets,
labelSet).
12. 设置运行时执行上下文的词法环境 为 oldEnv.
13. 返回 Completion(bodyResult)

#### 13.7.4.8 运行时语义：ForBodyEvaluation ( test, increment, stmt, perIterationBindings, labelSet ) <div id="sec-forbodyevaluation"></div>

The abstract operation ForBodyEvaluation with arguments test, increment, stmt, perIterationBindings, and labelSet 是 performed as follows:

1. 令 V 为 undefined.
2. 执行 ? CreatePerIterationEnvironment(perIterationBindings).
3. 重复，
a. 若 test 是 not [empty]，那么
i. 令 testRef 为 test 的运算结果
ii. 令 testValue 为 ? GetValue(testRef).
iii. 若 ToBoolean(testValue) 是 false，返回 NormalCompletion(V).
b. 令 result 为 stmt 的运算结果
c. 若 LoopContinues(result, labelSet) 是 false，返回 Completion(UpdateEmpty(result, V)).
d. 若 result.[[Value]] 是 not empty, 设置 V 为 result.[[Value]].
e. 执行 ? CreatePerIterationEnvironment(perIterationBindings).
f. 若 increment 是 not [empty]，那么
i. 令 incRef 为 increment 的运算结果
ii. 执行 ? GetValue(incRef).

#### 13.7.4.9 运行时语义：CreatePerIterationEnvironment ( perIterationBindings ) <div id="sec-createperiterationenvironment"></div>

The abstract operation CreatePerIterationEnvironment with argument perIterationBindings 是 performed as follows:

1. 若 perIterationBindings has any elements，那么
a. 令 lastIterationEnv 为运行时执行上下文的词法环境.
b. 令 lastIterationEnvRec 为 lastIterationEnv's EnvironmentRecord.
c. 令 outer 为 lastIterationEnv's outer environment reference.
d. 断言: outer 是 not null.
e. 令 thisIterationEnv 为 NewDeclarativeEnvironment(outer).
f. 令 thisIterationEnvRec 为 thisIterationEnv's EnvironmentRecord.
g. For each element bn of perIterationBindings, do
i. 执行 ! thisIterationEnvRec.CreateMutableBinding(bn, false).
ii. 令 lastValue 为 ? lastIterationEnvRec.GetBindingValue(bn, true).
iii. 执行 thisIterationEnvRec.InitializeBinding(bn, lastValue).
h. 设置运行时执行上下文的词法环境 为 thisIterationEnv.
2. 返回 undefined.

### 13.7.5 for-in, for-of, 和for-await-of 语句 <div id="sec-for-in-and-for-of-statements"></div>
#### 13.7.5.1 静态语义：Early Errors <div id="sec-for-in-and-for-of-statements-static-semantics-early-errors"></div>

```
IterationStatement :
    for ( LeftHandSideExpression in Expression ) Statement
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for await ( LeftHandSideExpression of AssignmentExpression ) Statement
```

- It 是 a Syntax Error if LeftHandSideExpression 是 either an ObjectLiteral or an ArrayLiteral and if
  LeftHandSideExpression 是 not covering an AssignmentPattern.

If LeftHandSideExpression 是 either an ObjectLiteral or an ArrayLiteral and if LeftHandSideExpression 是 covering an AssignmentPattern then the following rules are not applied. Instead, the Early Error rules for AssignmentPattern are used.

- It 是 a Syntax Error if AssignmentTargetType of LeftHandSideExpression 是 not simple.
- It 是 a Syntax Error if the LeftHandSideExpression 是 CoverParenthesizedExpressionAndArrowParameterList : (Expression ) and Expression derives a phrase that would produce a Syntax Error according to these rules if that phrase were substituted for LeftHandSideExpression. This rule 是 recursively applied

> 注：The last rule means that the other rules are applied even if parentheses surround Expression.

```
IterationStatement :
    for ( ForDeclaration in Expression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
    for await ( ForDeclaration of AssignmentExpression ) Statement
```

- It 是 a Syntax Error if the BoundNames of ForDeclaration contains "let".
- It 是 a Syntax Error if any element of the BoundNames of ForDeclaration also occurs in  Statement 的 VarDeclaredNames
- It 是 a Syntax Error if the BoundNames of ForDeclaration contains any duplicate entries.

#### 13.7.5.2 静态语义：BoundNames <div id="sec-for-in-and-for-of-statements-static-semantics-boundnames"></div>

```
ForDeclaration : LetOrConst ForBinding
```

1. 返回  ForBinding 的 BoundNames

#### 13.7.5.3 静态语义：ContainsDuplicateLabels <div id="sec-for-in-and-for-of-statements-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

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

1. 返回带有参数 labelSet 的 Statement 的 ContainsDuplicateLabels

> 注：This section 是 extended by Annex B.3.6.

#### 13.7.5.4 静态语义：ContainsUndefinedBreakTarget <div id="sec-for-in-and-for-of-statements-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

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

> 注：This section 是 extended by Annex B.3.6.

#### 13.7.5.5 静态语义：ContainsUndefinedContinueTarget <div id="sec-for-in-and-for-of-statements-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

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

1. 返回带有参数 iterationSet 和 « » 的 Statement 的 ContainsUndefinedContinueTarget

> 注：This section 是 extended by Annex B.3.6.

#### 13.7.5.6 静态语义：IsDestructuring <div id="sec-for-in-and-for-of-statements-static-semantics-isdestructuring"></div>

```
ForDeclaration : LetOrConst ForBinding
```

1. 返回 ForBinding 的 IsDestructuring

```
ForBinding : BindingIdentifier
```

1. 返回 false

```
ForBinding : BindingPattern
```

1. 返回 true.

> 注：This section 是 extended by Annex B.3.6.

#### 13.7.5.7 静态语义：VarDeclaredNames <div id="sec-for-in-and-for-of-statements-static-semantics-vardeclarednames"></div>

```
IterationStatement : for ( LeftHandSideExpression in Expression ) Statement
```

1. 返回  Statement 的 VarDeclaredNames

```
IterationStatement : for ( var ForBinding in Expression ) Statement
```

1. 令 names 为  ForBinding 的 BoundNames
2. 在 names 后追加 Statement 的 VarDeclaredNames 元素 
3. 返回 names.

```
IterationStatement : for ( ForDeclaration in Expression ) Statement
```

1. 返回  Statement 的 VarDeclaredNames

   ```
   IterationStatement :
       for ( LeftHandSideExpression of AssignmentExpression ) Statement
       for await ( LeftHandSideExpression of AssignmentExpression ) Statement
   ```

   1. 返回  Statement 的 VarDeclaredNames

   ```
   IterationStatement :
       for ( var ForBinding of AssignmentExpression ) Statement
       for await ( var ForBinding of AssignmentExpression ) Statement
   ```

   1. 令 names 为  ForBinding 的 BoundNames
   2. 在 names 后追加 Statement 的 VarDeclaredNames 元素 
   3. 返回 names.

   ```
   IterationStatement :
       for ( ForDeclaration of AssignmentExpression ) Statement
       for await ( ForDeclaration of AssignmentExpression ) Statement
   ```

   1. 返回  Statement 的 VarDeclaredNames

> 注：This section 是 extended by Annex B.3.6.

#### 13.7.5.8 静态语义：VarScopedDeclarations <div id="sec-for-in-and-for-of-statements-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : for ( LeftHandSideExpression in Expression ) Statement
```

1. 返回  Statement 的 VarScopedDeclarations

```
IterationStatement : for ( var ForBinding in Expression ) Statement
```

1. 令 declarations 为 a List containing ForBinding.

2. 在 declarations 后追加 Statement 的 VarScopedDeclarations 元素 

3. 返回 declarations.

   ```
   IterationStatement :
       for ( ForDeclaration in Expression ) Statement
       for await ( LeftHandSideExpression of AssignmentExpression ) Statement
   ```

   1. 返回  Statement 的 VarScopedDeclarations

   ```
   IterationStatement :
       for ( LeftHandSideExpression of AssignmentExpression ) Statement
       for await ( var ForBinding of AssignmentExpression ) Statement
   ```

   1. 返回  Statement 的 VarScopedDeclarations

   ```
   IterationStatement :
       for ( var ForBinding of AssignmentExpression ) Statement
       for await ( var ForBinding of AssignmentExpression ) Statement
   ```

   1. 令 declarations 为 a List containing ForBinding.
   2. 在 declarations 后追加 Statement 的 VarScopedDeclarations 元素 
   3. 返回 declarations.

   ```
   IterationStatement :
       for ( ForDeclaration of AssignmentExpression ) Statement
       for await ( ForDeclaration of AssignmentExpression ) Statement
   ```

   1. 返回  Statement 的 VarScopedDeclarations

> 注：This section 是 extended by Annex B.3.6.

#### 13.7.5.9 运行时语义：BindingInitialization <div id="sec-for-in-and-for-of-statements-runtime-semantics-bindinginitialization"></div>

使用参数value和environment

> 注：undefined 是 passed for environment to indicate that a PutValue operation should be used to assign the initialization value. This 是 the case for var statements and the formal parameter lists of some non-strict functions (see 9.2.15). In those cases a lexical binding 是 hoisted and preinitialized prior to evaluation of its initializer.

```
ForDeclaration : LetOrConst ForBinding
```

1. 返回 performing BindingInitialization for ForBinding passing value and environment as the arguments 的结果

#### 13.7.5.10 运行时语义：BindingInstantiation <div id="sec-runtime-semantics-bindinginstantiation"></div>

使用参数 environment

```
ForDeclaration : LetOrConst ForBinding
```

1. 令 envRec 为 environment's EnvironmentRecord.
2. 断言: envRec 是声明环境记录项.
3. For each element name of the BoundNames of ForBinding, do
   a. 若 IsConstantDeclaration of LetOrConst 是 true，那么
   i. 执行 ! envRec.CreateImmutableBinding(name, true).
   b. 否则，
   i. 执行 ! envRec.CreateMutableBinding(name, false).

#### 13.7.5.11 运行时语义：LabelledEvaluation <div id="sec-for-in-and-for-of-statements-runtime-semantics-labelledevaluation"></div>

使用参数 labelSet

```
IterationStatement : for ( LeftHandSideExpression in Expression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(« », Expression, enumerate).
2. 返回 ? ForIn/OfBodyEvaluation(LeftHandSideExpression, Statement, keyResult, enumerate, assignment, labelSet).

```
IterationStatement : for ( var ForBinding in Expression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(« », Expression, enumerate).
2. 返回 ? ForIn/OfBodyEvaluation(ForBinding, Statement, keyResult, enumerate, varBinding, labelSet).

```
IterationStatement : for ( ForDeclaration in Expression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(BoundNames of ForDeclaration, Expression, enumerate).
2. 返回 ? ForIn/OfBodyEvaluation(ForDeclaration, Statement, keyResult, enumerate, lexicalBinding, labelSet).

```
IterationStatement : for ( LeftHandSideExpression of AssignmentExpression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(« », AssignmentExpression, iterate).
2. 返回 ? ForIn/OfBodyEvaluation(LeftHandSideExpression, Statement, keyResult, iterate, assignment, labelSet).

```
IterationStatement : for ( var ForBinding of AssignmentExpression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(« », AssignmentExpression, iterate).
2. 返回 ? ForIn/OfBodyEvaluation(ForBinding, Statement, keyResult, iterate, varBinding, labelSet).

```
IterationStatement : for ( ForDeclaration of AssignmentExpression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(BoundNames of ForDeclaration, AssignmentExpression, iterate).
2. 返回 ? ForIn/OfBodyEvaluation(ForDeclaration, Statement, keyResult, iterate, lexicalBinding, labelSet).

```
IterationStatement : for await ( LeftHandSideExpression of AssignmentExpression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(« », AssignmentExpression, async-iterate).
2. 返回 ? ForIn/OfBodyEvaluation(LeftHandSideExpression, Statement, keyResult, iterate, assignment, labelSet, async).

```
IterationStatement : for await ( var ForBinding of AssignmentExpression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(« », AssignmentExpression, async-iterate).
2. 返回 ? ForIn/OfBodyEvaluation(ForBinding, Statement, keyResult, iterate, varBinding, labelSet, async).

```
IterationStatement : for await ( ForDeclaration of AssignmentExpression ) Statement
```

1. 令 keyResult 为 ? ForIn/OfHeadEvaluation(BoundNames of ForDeclaration, AssignmentExpression, asynciterate).
2. 返回 ? ForIn/OfBodyEvaluation(ForDeclaration, Statement, keyResult, iterate, lexicalBinding, labelSet, async).

> 注：This section 是 extended by Annex B.3.6.

#### 13.7.5.12 运行时语义：ForIn/OfHeadEvaluation ( TDZnames, expr, iterationKind ) <div id="sec-runtime-semantics-forin-div-ofheadevaluation-tdznames-expr-iterationkind"></div>

The abstract operation ForIn/OfHeadEvaluation 是 called with arguments TDZnames, expr, and iterationKind. The value of iterationKind 是 either enumerate, iterate, or async-iterate.

1. 令 oldEnv 为运行时执行上下文的词法环境.
2. 若 TDZnames 是 not an empty List，那么
a. 断言: TDZnames has no duplicate entries.
b. 令 TDZ 为 NewDeclarativeEnvironment(oldEnv).
c. 令 TDZEnvRec 为 TDZ's EnvironmentRecord.
d. 对于TDZnames中的string name，执行
i. 执行 ! TDZEnvRec.CreateMutableBinding(name, false).
e. 设置运行时执行上下文的词法环境 为 TDZ.
3. 令 exprRef 为 expr 的运算结果
4. 设置运行时执行上下文的词法环境 为 oldEnv.
5. 令 exprValue 为 ? GetValue(exprRef).
6. 若 iterationKind 是 enumerate，那么
a. 若 exprValue 是 undefined or null，那么
i. 返回 Completion { [[Type]]: break, [[Value]]: empty, [[Target]]: empty }.
b. 令 obj 为 ! ToObject(exprValue).
c. 返回 ? EnumerateObjectProperties(obj).
7. 否则，
a. 断言: iterationKind 是 iterate.
b. 若 iterationKind 是 async-iterate, let iteratorHint be async.
c. 否则， let iteratorHint be sync.
d. 返回 ? GetIterator(exprValue, iteratorHint).

#### 13.7.5.13 运行时语义：ForIn/OfBodyEvaluation ( lhs, stmt, iteratorRecord, iterationKind, lhsKind, labelSet [ , iteratorKind ] ) <div id="sec-runtime-semantics-forin-div-ofbodyevaluation-lhs-stmt-iterator-lhskind-labelset"></div>

The abstract operation ForIn/OfBodyEvaluation 是 called with arguments lhs, stmt, iteratorRecord, iterationKind, lhsKind, labelSet, and optional argument iteratorKind. The value of lhsKind 是 either assignment, varBinding or lexicalBinding. The value of iteratorKind 是 either sync or async.

1. 若 iteratorKind 不存在, 设置 iteratorKind 为 sync.

2. 令 oldEnv 为运行时执行上下文的词法环境.

3. 令 V 为 undefined.

4. 令 destructuring 为 lhs 的 IsDestructuring

5. 若 destructuring 是 true and if lhsKind 是 assignment，那么
    a. 断言: lhs 是 a LeftHandSideExpression.
    b. 令 assignmentPattern 为 the AssignmentPattern that 是 covered by lhs.

6. 重复，

   a. 令 nextResult 为 ? Call(iteratorRecord.[[NextMethod]], iteratorRecord.[[Iterator]], « »).
   b. 若 iteratorKind 是 async，那么 设置 nextResult 为 ? Await(nextResult).
   c. 若 Type(nextResult) 是 not Object, throw a TypeError exception.
   d. 令 done 为 ? IteratorComplete(nextResult).
   e. 若 done 是 true，返回 NormalCompletion(V).
   f. 令 nextValue 为 ? IteratorValue(nextResult).
   g. 若 lhsKind 是 either assignment or varBinding，那么
   i. 若 destructuring 是 false，那么
   1. 令 lhsRef be evaluating lhs. (It may 为 evaluated repeatedly.) 的结果
   h. 否则，
   i. 断言: lhsKind 是 lexicalBinding.
   ii. 断言: lhs 是 a ForDeclaration.
   iii. 令 iterationEnv 为 NewDeclarativeEnvironment(oldEnv).
   iv. 执行 BindingInstantiation for lhs passing iterationEnv as the argument.
   v. 设置运行时执行上下文的词法环境 为 iterationEnv.
   vi. 若 destructuring 是 false，那么
   1. 断言: lhs binds a single name.
   2. 令 lhsName 为 the sole element of lhs 的 BoundNames
   3. 令 lhsRef 为 ! ResolveBinding(lhsName).
   i. 若 destructuring 是 false，那么
   i. 若 lhsRef 是 abrupt completion，那么
   1. 令 status 为 lhsRef.
   ii. Else if lhsKind 是 lexicalBinding，那么
   1. 令 status 为 InitializeReferencedBinding(lhsRef, nextValue).
   iii. 否则，
   1. 令 status 为 PutValue(lhsRef, nextValue).
   j. 否则，
   i. 若 lhsKind 是 assignment，那么
   1. 令 status 为 performing DestructuringAssignmentEvaluation of assignmentPattern 的结果
   using nextValue as the argument.
   ii. Else if lhsKind 是 varBinding，那么
   1. 断言: lhs 是 a ForBinding.
   2. 令 status 为 performing BindingInitialization for lhs passing nextValue and 的结果
   undefined as the arguments.
   iii. 否则，
   1. 断言: lhsKind 是 lexicalBinding.
   2. 断言: lhs 是 a ForDeclaration.
   3. 令 status 为 performing BindingInitialization for lhs passing nextValue and 的结果
   iterationEnv as arguments.
   k. 若 status 是 abrupt completion，那么
   i. 设置运行时执行上下文的词法环境 为 oldEnv.
   ii. 若 iteratorKind 是 async，返回 ? AsyncIteratorClose(iteratorRecord, status).
   iii. 若 iterationKind 是 enumerate，那么
   1. 返回 status.
   iv. 否则，
   1. 断言: iterationKind 是 iterate.
   2. 返回 ? IteratorClose(iteratorRecord, status).
   l. 令 result 为 stmt 的运算结果
   17. m. 设置运行时执行上下文的词法环境 为 oldEnv.
         n. 若 LoopContinues(result, labelSet) 是 false，那么
         i. 若 iterationKind 是 enumerate，那么
      1. 返回 Completion(UpdateEmpty(result, V)).
         ii. 否则，
      1. 断言: iterationKind 是 iterate.
      2. 设置 status 为 UpdateEmpty(result, V).
      3. 若 iteratorKind 是 async，返回 ? AsyncIteratorClose(iteratorRecord, status).
      4. 返回 ? IteratorClose(iteratorRecord, status).
         o. 若 result.[[Value]] 是 not empty, 设置 V 为 result.[[Value]].

#### 13.7.5.14 运行时语义：Evaluation <div id="sec-for-in-and-for-of-statements-runtime-semantics-evaluation"></div>

```
ForBinding : BindingIdentifier
```

1. 令 bindingId 为 BindingIdentifier 的 StringValue
2. 返回 ? ResolveBinding(bindingId).

#### 13.7.5.15 EnumerateObjectProperties ( O ) <div id="sec-enumerate-object-properties"></div>

When the abstract operation EnumerateObjectProperties 是 called with argument O, the following steps are taken:

1. 断言: Type(O) 是 Object.
2. 返回 an Iterator object (25.1.1.2) whose next method iterates over all the String-valued keys of enumerable
properties of O. The iterator object 是 never directly accessible to ECMAScript code. The mechanics and order of
enumerating the properties 是 not specified but must conform to the rules specified below.

The iterator's throw and return methods are null and are never invoked. The iterator's next method processes object properties to determine whether the property key should be returned as an iterator value. Returned property keys do not include keys that are Symbols. Properties of the target object may be deleted during enumeration. A property that 是 deleted before it 是 processed by the iterator's next method 是 ignored. 若 new properties are added to the target object during enumeration, the newly added properties are not guaranteed to be processed in the active enumeration. A property name will be returned by the iterator's next method at most once in any enumeration.

Enumerating the properties of the target object includes enumerating properties of its prototype, and the prototype of the prototype, and so on, recursively; but a property of a prototype 是 not processed if it has the same name as a property that has already been processed by the iterator's next method. The values of [[Enumerable]] attributes are not considered when determining if a property of a prototype object has already been processed. The enumerable property names of prototype objects must be obtained by invoking EnumerateObjectProperties passing the prototype object as the argument. EnumerateObjectProperties must obtain the own property keys of the target object by calling its [[OwnPropertyKeys]] internal method. Property attributes of the target object must be obtained by calling its [[GetOwnProperty]] internal method.

> 注：The following 是 an informative definition of an ECMAScript generator function that conforms to these rules:
>
> function* EnumerateObjectProperties(obj) {
>  const visited = new Set();
>  for (const Reflect 的 keyownKeys(obj)) {
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

**语法**

```
ContinueStatement[Yield, Await] :
    continue ;
    continue [no LineTerminator here] LabelIdentifier[?Yield, ?Await] ;
```

### 13.8.1 静态语义：Early Errors <div id="sec-continue-statement-static-semantics-early-errors"></div>

```
ContinueStatement : continue ;
ContinueStatement : continue LabelIdentifier ;
```

It 是 a Syntax Error if this ContinueStatement 是 not nested, directly or indirectly (but not crossing function
boundaries), within an IterationStatement.

### 13.8.2 静态语义：ContainsUndefinedContinueTarget <div id="sec-continue-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
ContinueStatement : continue ;
```

1. 返回 false.

```
ContinueStatement : continue LabelIdentifier ;
```

1. 若 the StringValue of LabelIdentifier 是 not an element of iterationSet，返回 true.
2. 返回 false.

### 13.8.3 运行时语义：Evaluation <div id="sec-continue-statement-runtime-semantics-evaluation"></div>

```
ContinueStatement : continue ;
```

1. 返回 Completion { [[Type]]: continue, [[Value]]: empty, [[Target]]: empty }.

```
ContinueStatement : continue LabelIdentifier ;
```

1. 令 label 为  LabelIdentifier 的 StringValue
2. 返回 Completion { [[Type]]: continue, [[Value]]: empty, [[Target]]: label }.

## 13.9 break 语句 <div id="sec-break-statement"></div>

**语法**

```
BreakStatement[Yield, Await] :
    break ;
    break [no LineTerminator here] LabelIdentifier[?Yield, ?Await] ;
```

### 13.9.1 静态语义：Early Errors <div id="sec-break-statement-static-semantics-early-errors"></div>

```
BreakStatement : break ;
```

It 是 a Syntax Error if this BreakStatement 是 not nested, directly or indirectly (but not crossing function
boundaries), within an IterationStatement or a SwitchStatement.

### 13.9.2 静态语义：ContainsUndefinedBreakTarget <div id="sec-break-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
BreakStatement : break ;
```

1. 返回 false.

```
BreakStatement : break LabelIdentifier ;
```

1. 若 the StringValue of LabelIdentifier 是 not an element of labelSet，返回 true.
2. 返回 false.

### 13.9.3 运行时语义：Evaluation <div id="sec-break-statement-runtime-semantics-evaluation"></div>

```
BreakStatement : break ;
```

1. 返回 Completion { [[Type]]: break, [[Value]]: empty, [[Target]]: empty }.

```
BreakStatement : break LabelIdentifier ;
```

1. 令 label 为  LabelIdentifier 的 StringValue
2. 返回 Completion { [[Type]]: break, [[Value]]: empty, [[Target]]: label }.

## 13.10 return 语句 <div id="sec-return-statement"></div>

**语法**

```
ReturnStatement[Yield, Await] :
    return ;
    return [no LineTerminator here] Expression[+In, ?Yield, ?Await] ;
```

> 注：A return statement causes a function to cease execution and, in most cases, returns a value to the caller. 若 Expression 是 omitted, the return value 是 undefined. Otherwise, the return value 是  Expression 的 value A return statement may not actually return a value to the caller depending on surrounding context. For example, in a try block, a return statement's completion record may be replaced with another completion record during evaluation of the finally block.

### 13.10.1 运行时语义：Evaluation <div id="sec-return-statement-runtime-semantics-evaluation"></div>

```
ReturnStatement : return ;
```

1. 返回 Completion { [[Type]]: return, [[Value]]: undefined, [[Target]]: empty }.

```
ReturnStatement : return Expression ;
```

1. 令 exprRef 为 Expression 的运算结果
2. 令 exprValue 为 ? GetValue(exprRef).
3. 若 ! GetGeneratorKind() 是 async, 设置 exprValue 为 ? Await(exprValue).
4. 返回 Completion { [[Type]]: return, [[Value]]: exprValue, [[Target]]: empty }.

## 13.11 with 语句 <div id="sec-with-statement"></div>

**语法**

```
WithStatement[Yield, Await, Return] :
    with ( Expression[+In, ?Yield, ?Await] ) Statement[?Yield, ?Await, ?Return]
```

> 注：The with statement adds an object Environment Record for a computed object to the lexical environment of the running execution context. It then executes a statement using this augmented lexical environment. Finally, it restores the original lexical environment.

### 13.11.1 静态语义：Early Errors <div id="sec-with-statement-static-semantics-early-errors"></div>

```
WithStatement : with ( Expression ) Statement
```

It 是 a Syntax Error if the code that matches this production 是 contained in strict mode code.
It 是 a Syntax Error if IsLabelledFunction(Statement) 是 true.

> 注：It 是 only necessary to apply the second rule if the extension specified in B.3.2 是 implemented.

### 13.11.2 静态语义：ContainsDuplicateLabels <div id="sec-with-statement-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
WithStatement : with ( Expression ) Statement
```

1. 返回 ContainsDuplicateLabels of Statement with argument labelSe

### 13.11.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-with-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSe

```
WithStatement : with ( Expression ) Statement
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsUndefinedBreakTarget

### 13.11.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-with-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
WithStatement : with ( Expression ) Statement
```

1. 返回带有参数 iterationSet 和 « » 的 Statement 的 ContainsUndefinedContinueTarget

### 13.11.5 静态语义：VarDeclaredNames <div id="sec-with-statement-static-semantics-vardeclarednames"></div>

```
WithStatement : with ( Expression ) Statement
```

1. 返回  Statement 的 VarDeclaredNames

### 13.11.6 静态语义：VarScopedDeclarations <div id="sec-with-statement-static-semantics-varscopeddeclarations"></div>

```
WithStatement : with ( Expression ) Statement
```

1. 返回  Statement 的 VarScopedDeclarations

### 13.11.7 运行时语义：Evaluation <div id="sec-with-statement-runtime-semantics-evaluation"></div>

```
WithStatement : with ( Expression ) Statement
```

1. 令 val 为 Expression 的运算结果
2. 令 obj 为 ? ToObject(? GetValue(val)).
3. 令 oldEnv 为运行时执行上下文的词法环境.
4. 令 newEnv 为 NewObjectEnvironment(obj, oldEnv).
5. 设置 the withEnvironment flag of newEnv's EnvironmentRecord 为 true.
6. 设置运行时执行上下文的词法环境 为 newEnv.
7. 令 C 为 Statement 的运算结果
8. 设置运行时执行上下文的词法环境 为 oldEnv.
9. 返回 Completion(UpdateEmpty(C, undefined)).

> 注：No matter how control leaves the embedded Statement, whether normally or by some form of abrupt completion or exception, the LexicalEnvironment 是 always restored to its former state.

## 13.12 switch 语句 <div id="sec-switch-statement"></div>

**语法**

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

### 13.12.1 静态语义：Early Errors <div id="sec-switch-statement-static-semantics-early-errors"></div>

```
SwitchStatement : switch ( Expression ) CaseBlock
```

It 是 a Syntax Error if the LexicallyDeclaredNames of CaseBlock contains any duplicate entries.
It 是 a Syntax Error if any element of the LexicallyDeclaredNames of CaseBlock also occurs in the
VarDeclaredNames of CaseBlock.

### 13.12.2 静态语义：ContainsDuplicateLabels <div id="sec-switch-statement-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. 返回带有参数 labelSet 的 CaseBlock 的 ContainsDuplicateLabels

```
CaseBlock : { }
```

1. 返回 false

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 若 the first CaseClauses 存在，那么
a. 令 hasDuplicates 为 ContainsDuplicateLabels of the first CaseClauses with argument labelSet.
b. 若 hasDuplicates 是 true，返回 true.
2. 令 hasDuplicates 为带有参数 labelSet 的 DefaultClause 的 ContainsDuplicateLabels
3. 若 hasDuplicates 是 true，返回 true.
4. 若 the second CaseClauses 不存在，返回 false.
5. 返回 ContainsDuplicateLabels of the second CaseClauses with argument labelSet.

```
CaseClauses : CaseClauses CaseClause
```

1. 令 hasDuplicates 为带有参数 labelSet 的 CaseClauses 的 ContainsDuplicateLabels
2. 若 hasDuplicates 是 true，返回 true.
3. 返回带有参数 labelSet 的 CaseClause 的 ContainsDuplicateLabels

```
CaseClause : case Expression : StatementList
```

1. 若 the StatementList 存在，返回带有参数 labelSet 的 StatementList 的 ContainsDuplicateLabels
2. 返回 false.

```
DefaultClause : default : StatementList
```

1. 若 the StatementList 存在，返回带有参数 labelSet 的 StatementList 的 ContainsDuplicateLabels
2. 返回 false

### 13.12.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-switch-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. 返回带有参数 labelSet 的 CaseBlock 的 ContainsUndefinedBreakTarget

```
CaseBlock : { }
```

1. 返回 false.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 若 the first CaseClauses 存在，那么
a. 令 hasUndefinedLabels 为 ContainsUndefinedBreakTarget of the first CaseClauses with argument labelSet.
b. 若 hasUndefinedLabels 是 true，返回 true.
2. 令 hasUndefinedLabels 为带有参数 labelSet 的 DefaultClause 的 ContainsUndefinedBreakTarget
3. 若 hasUndefinedLabels 是 true，返回 true.
4. 若 the second CaseClauses 不存在，返回 false.
5. 返回 ContainsUndefinedBreakTarget of the second CaseClauses with argument labelSet.

```
CaseClauses : CaseClauses CaseClause
```

1. 令 hasUndefinedLabels 为带有参数 labelSet 的 CaseClauses 的 ContainsUndefinedBreakTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回带有参数 labelSet 的 CaseClause 的 ContainsUndefinedBreakTarget

```
CaseClause : case Expression : StatementList
```

1. 若 the StatementList 存在，返回带有参数 labelSet 的 StatementList 的 ContainsUndefinedBreakTarget
2. 返回 false.

```
DefaultClause : default : StatementList
```

1. 若 the StatementList 存在，返回带有参数 labelSet 的 StatementList 的 ContainsUndefinedBreakTarget
2. 返回 false.

### 13.12.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-switch-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. 返回带有参数 iterationSet 和 « » 的 CaseBlock 的 ContainsUndefinedContinueTarget

```
CaseBlock : { }
```

1. 返回 false

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 若 the first CaseClauses 存在，那么
a. 令 hasUndefinedLabels 为 ContainsUndefinedContinueTarget of the first CaseClauses with arguments
iterationSet and « ».
b. 若 hasUndefinedLabels 是 true，返回 true.
2. 令 hasUndefinedLabels 为 ContainsUndefinedContinueTarget of DefaultClause with arguments iterationSet and «
».
3. 若 hasUndefinedLabels 是 true，返回 true.
4. 若 the second CaseClauses 不存在，返回 false.
5. 返回 ContainsUndefinedContinueTarget of the second CaseClauses with arguments iterationSet and « ».

```
CaseClauses : CaseClauses CaseClause
```

1. 令 hasUndefinedLabels 为带有参数 iterationSet 和 « » 的 CaseClauses 的 ContainsUndefinedContinueTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回带有参数 iterationSet 和 « » 的 CaseClause 的 ContainsUndefinedContinueTarget

```
CaseClause : case Expression : StatementList
```

1. 若 the StatementList 存在，返回带有参数 iterationSet 和 « » 的 StatementList 的 ContainsUndefinedContinueTarget
2. 返回 false.

```
DefaultClause : default : StatementList
```

1. 若 the StatementList 存在，返回带有参数 iterationSet 和 « » 的 StatementList 的 ContainsUndefinedContinueTarget
2. 返回 false.

### 13.12.5 静态语义：LexicallyDeclaredNames <div id="sec-switch-statement-static-semantics-lexicallydeclarednames"></div>

```
CaseBlock : { }
```

1. 返回一个新的空列表.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 若 the first CaseClauses 存在, let names be the LexicallyDeclaredNames of the first CaseClauses.
2. 否则， let names be一个新的空列表.
3. 在 names 后追加 DefaultClause 的 LexicallyDeclaredNames 元素 
4. 若 the second CaseClauses 不存在，返回 names

5. 返回 appending to names the elements of the LexicallyDeclaredNames of the second CaseClauses 的结果

```
CaseClauses : CaseClauses CaseClause
```

1. 令 names 为 CaseClauses 的 LexicallyDeclaredNames
2. 在 names 后追加 CaseClause 的 LexicallyDeclaredNames 元素 
3. 返回 names.

```
CaseClause : case Expression : StatementList
```

1. 若 the StatementList 存在，返回  StatementList 的 LexicallyDeclaredNames
2. 返回一个新的空列表.

```
DefaultClause : default : StatementList
```

1. 若 the StatementList 存在，返回  StatementList 的 LexicallyDeclaredNames
2. 返回一个新的空列表.

### 13.12.6 静态语义：LexicallyScopedDeclarations <div id="sec-switch-statement-static-semantics-lexicallyscopeddeclarations"></div>

```
CaseBlock : { }
```

1. 返回一个新的空列表.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 若 the first CaseClauses 存在, let declarations be the LexicallyScopedDeclarations of the first CaseClauses.
2. 否则， let declarations be一个新的空列表.
3. 在 declarations 后追加 DefaultClause 的 LexicallyScopedDeclarations 元素 
4. 若 the second CaseClauses 不存在，返回 declarations.
5. 返回 appending to declarations the elements of the LexicallyScopedDeclarations of the second CaseClauses. 的结果

```
CaseClauses : CaseClauses CaseClause
```

1. 令 declarations 为 CaseClauses 的 LexicallyScopedDeclarations
2. 在 declarations 后追加 CaseClause 的 LexicallyScopedDeclarations 元素 
3. 返回 declarations

```
CaseClause : case Expression : StatementList
```

1. 若 the StatementList 存在，返回  StatementList 的 LexicallyScopedDeclarations
2. 返回一个新的空列表.

```
DefaultClause : default : StatementList
```

1. 若 the StatementList 存在，返回  StatementList 的 LexicallyScopedDeclarations
2. 返回一个新的空列表.

### 13.12.7 静态语义：VarDeclaredNames <div id="sec-switch-statement-static-semantics-vardeclarednames"></div>

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. 返回  CaseBlock 的 VarDeclaredNames

```
CaseBlock : { }
```

1. 返回一个新的空列表.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 若 the first CaseClauses 存在, let names be the VarDeclaredNames of the first CaseClauses.
2. 否则， let names be一个新的空列表.
3. 在 names 后追加 DefaultClause 的 VarDeclaredNames 元素 
4. 若 the second CaseClauses 不存在，返回 names.
5. 返回 appending to names the elements of the VarDeclaredNames of the second CaseClauses. 的结果

```
CaseClauses : CaseClauses CaseClause
```

1. 令 names 为 CaseClauses 的 VarDeclaredNames
2. 在 names 后追加 CaseClause 的 VarDeclaredNames 元素 
3. 返回 names.

```
CaseClause : case Expression : StatementList
```

1. 若 the StatementList 存在，返回  StatementList 的 VarDeclaredNames
2. 返回一个新的空列表.

```
DefaultClause : default : StatementList
```

1. 若 the StatementList 存在，返回  StatementList 的 VarDeclaredNames
2. 返回一个新的空列表.

### 13.12.8 静态语义：VarScopedDeclarations <div id="sec-switch-statement-static-semantics-varscopeddeclarations"></div>

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. 返回  CaseBlock 的 VarScopedDeclarations

```
CaseBlock : { }
```

1. 返回一个新的空列表.

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 若 the first CaseClauses 存在, let declarations be the VarScopedDeclarations of the first CaseClauses.
2. 否则， let declarations be一个新的空列表.
3. 在 declarations 后追加 DefaultClause 的 VarScopedDeclarations 元素 
4. 若 the second CaseClauses 不存在，返回 declarations.
5. 返回 appending to declarations the elements of the VarScopedDeclarations of the second CaseClauses. 的结果

```
CaseClauses : CaseClauses CaseClause
```

1. 令 declarations 为 CaseClauses 的 VarScopedDeclarations
2. 在 declarations 后追加 CaseClause 的 VarScopedDeclarations 元素 
3. 返回 declarations.

```
CaseClause : case Expression : StatementList
```

1. 若 the StatementList 存在，返回  StatementList 的 VarScopedDeclarations
2. 返回一个新的空列表.

```
DefaultClause : default : StatementList
```

1. 若 the StatementList 存在，返回  StatementList 的 VarScopedDeclarations
2. 返回一个新的空列表.

### 13.12.9 运行时语义：CaseBlockEvaluation <div id="sec-runtime-semantics-caseblockevaluation"></div>

使用参数 input

```
CaseBlock : { }
```

1. 返回 NormalCompletion(undefined).

```
CaseBlock : { CaseClauses }
```

1. 令 V 为 undefined.
2. 令 A 为 the List of CaseClause items in CaseClauses, in source text order.
3. 令 found 为 false.
4. 对于A中的CaseClause C，执行
a. 若 found 是 false，那么
i. 设置 found 为 ? CaseClauseIsSelected(C, input).
b. 若 found 是 true，那么
i. 令 R 为 C 的运算结果
ii. 若 R.[[Value]] 是 not empty, 设置 V 为 R.[[Value]].
iii. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
5. 返回 NormalCompletion(V).

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 令 V 为 undefined.
2. 若 the first CaseClauses 存在，那么
a. 令 A 为 the List of CaseClause items in the first CaseClauses, in source text order.
3. 否则，
a. 令 A 为 « ».
4. 令 found 为 false.
5. 对于A中的CaseClause C，执行
a. 若 found 是 false，那么
i. 设置 found 为 ? CaseClauseIsSelected(C, input).
b. 若 found 是 true，那么
i. 令 R 为 C 的运算结果
ii. 若 R.[[Value]] 是 not empty, 设置 V 为 R.[[Value]].
iii. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
6. 令 foundInB 为 false.
7. 若 the second CaseClauses 存在，那么
a. 令 B 为 the List of CaseClause items in the second CaseClauses, in source text order.
8. 否则，
a. 令 B 为 « ».

9. 若 found 是 false，那么
a. 对于B中的CaseClause C，执行
i. 若 foundInB 是 false，那么
1. 设置 foundInB 为 ? CaseClauseIsSelected(C, input).
ii. 若 foundInB 是 true，那么
1. 令 R 为 CaseClause C 的运算结果
2. 若 R.[[Value]] 是 not empty, 设置 V 为 R.[[Value]].
3. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
10. 若 foundInB 是 true，返回 NormalCompletion(V).
11. 令 R 为 DefaultClause 的运算结果
12. 若 R.[[Value]] 是 not empty, 设置 V 为 R.[[Value]].
13. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
14. For each CaseClause C in B (注: this 是 another complete iteration of the second CaseClauses), do
a. 令 R 为 CaseClause C 的运算结果
b. 若 R.[[Value]] 是 not empty, 设置 V 为 R.[[Value]].
c. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
15. 返回 NormalCompletion(V).

### 13.12.10 运行时语义：CaseClauseIsSelected ( C, input ) <div id="sec-runtime-semantics-caseclauseisselected"></div>

The abstract operation CaseClauseIsSelected, given CaseClause C and value input, determines whether C matches input.

1. 断言: C 是 an instance of the production CaseClause : case Expression : StatementList .
2. 令 exprRef 为 evaluating  C 的 Expression 的结果
3. 令 clauseSelector 为 ? GetValue(exprRef).
4. 返回 performing Strict Equality Comparison input === clauseSelector. 的结果

> 注：This operation does not execute C's StatementList (if any). The CaseBlock algorithm uses its return value to determine which StatementList to start executing.

### 13.12.11 运行时语义：Evaluation <div id="sec-switch-statement-runtime-semantics-evaluation"></div>

```
SwitchStatement : switch ( Expression ) CaseBlock
```

1. 令 exprRef 为 Expression 的运算结果
2. 令 switchValue 为 ? GetValue(exprRef).
3. 令 oldEnv 为运行时执行上下文的词法环境.
4. 令 blockEnv 为 NewDeclarativeEnvironment(oldEnv).
5. 执行 BlockDeclarationInstantiation(CaseBlock, blockEnv).
6. 设置运行时执行上下文的词法环境 为 blockEnv.
7. 令 R 为 performing带有参数 switchValue 的 CaseBlock 的 CaseBlockEvaluation 的结果
8. 设置运行时执行上下文的词法环境 为 oldEnv.
9. 返回 R

> 注：No matter how control leaves the SwitchStatement the LexicalEnvironment 是 always restored to its former state.

```
CaseClause : case Expression :
```

1. 返回 NormalCompletion(empty).

```
CaseClause : case Expression : StatementList
```

1. 返回 StatementList 的运算结果

```
DefaultClause : default :
```

1. 返回 NormalCompletion(empty).

```
DefaultClause : default : StatementList
```

1. 返回 StatementList 的运算结果

## 13.13 带标签的语句 <div id="sec-labelled-statements"></div>

**语法**

```
LabelledStatement[Yield, Await, Return] :
    LabelIdentifier[?Yield, ?Await] : LabelledItem[?Yield, ?Await, ?Return]
LabelledItem[Yield, Await, Return] :
    Statement[?Yield, ?Await, ?Return]
    FunctionDeclaration[?Yield, ?Await, ~Default]
```

> 注：A Statement may be prefixed by a label. Labelled statements are only used in conjunction with labelled break and continue statements. ECMAScript has no goto statement. A Statement can be part of a LabelledStatement, which itself can be part of a LabelledStatement, and so on. The labels introduced this way are collectively referred to as the “current label set” when describing the semantics of individual statements.

### 13.13.1 静态语义：Early Errors <div id="sec-labelled-statements-static-semantics-early-errors"></div>

```
LabelledItem : FunctionDeclaration
```

It 是 a Syntax Error if any source text matches this rule.

> 注：An alternative definition for this rule 是 provided in B.3.2.

### 13.13.2 静态语义：ContainsDuplicateLabels <div id="sec-labelled-statements-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 令 label 为  LabelIdentifier 的 StringValue
2. 若 label 是 an element of labelSet，返回 true.
3. 令 newLabelSet 为 a copy of labelSet with label appended.
4. 返回带有参数 newLabelSet 的 LabelledItem 的 ContainsDuplicateLabels

```
LabelledItem : FunctionDeclaration
```

1. 返回 false.

### 13.13.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-labelled-statements-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 令 label 为  LabelIdentifier 的 StringValue
2. 令 newLabelSet 为 a copy of labelSet with label appended.
3. 返回带有参数 newLabelSet 的 LabelledItem 的 ContainsUndefinedBreakTarget

```
LabelledItem : FunctionDeclaration
```

1. 返回 false.

### 13.13.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-labelled-statements-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 令 label 为  LabelIdentifier 的 StringValue
2. 令 newLabelSet 为 a copy of labelSet with label appended.
3. 返回带有参数 iterationSet 和 newLabelSet 的 LabelledItem 的 ContainsUndefinedContinueTarget

```
LabelledItem : FunctionDeclaration
```

1. 返回 false.

### 13.13.5 静态语义：IsLabelledFunction ( stmt ) <div id="sec-islabelledfunction"></div>

The abstract operation IsLabelledFunction with argument stmt performs the following steps:

1. 若 stmt 是 not a LabelledStatement，返回 false.
2. 令 item 为  stmt 的 LabelledItem
3. 若 item 是 LabelledItem : FunctionDeclaration ，返回 true.
4. 令 subStmt 为  item 的 Statement
5. 返回 IsLabelledFunction(subStmt).

### 13.13.6 静态语义：LexicallyDeclaredNames <div id="sec-labelled-statements-static-semantics-lexicallydeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回  LabelledItem 的 LexicallyDeclaredNames

```
LabelledItem : Statement
```

1. 返回一个新的空列表.

```
LabelledItem : FunctionDeclaration
```

1. 返回 FunctionDeclaration 的 BoundNames

### 13.13.7 静态语义：LexicallyScopedDeclarations <div id="sec-labelled-statements-static-semantics-lexicallyscopeddeclarations"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回  LabelledItem 的 LexicallyScopedDeclarations

```
LabelledItem : Statement
```

1. 返回一个新的空列表.

```
LabelledItem : FunctionDeclaration
```

1. 返回包含 FunctionDeclaration. 的新列表

### 13.13.8 静态语义：TopLevelLexicallyDeclaredNames <div id="sec-labelled-statements-static-semantics-toplevellexicallydeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回一个新的空列表.

### 13.13.9 静态语义：TopLevelLexicallyScopedDeclarations <div id="sec-labelled-statements-static-semantics-toplevellexicallyscopeddeclarations"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回一个新的空列表.

### 13.13.10 静态语义：TopLevelVarDeclaredNames <div id="sec-labelled-statements-static-semantics-toplevelvardeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回  LabelledItem 的 TopLevelVarDeclaredNames

```
LabelledItem : Statement
```

1. 若 Statement 是 Statement : LabelledStatement ，返回 Statement 的 TopLevelVarDeclaredNames
2. 返回 Statement 的 VarDeclaredNames

```
LabelledItem : FunctionDeclaration
```

1. 返回 FunctionDeclaration 的 BoundNames

### 13.13.11 静态语义：TopLevelVarScopedDeclarations <div id="sec-labelled-statements-static-semantics-toplevelvarscopeddeclarations"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回  LabelledItem 的 TopLevelVarScopedDeclarations

```
LabelledItem : Statement
```

1. 若 Statement 是 Statement : LabelledStatement ，返回 Statement 的 TopLevelVarScopedDeclarations
2. 返回 Statement 的 VarScopedDeclarations

```
LabelledItem : FunctionDeclaration
```

1. 返回包含 FunctionDeclaration. 的新列表

### 13.13.12 静态语义：VarDeclaredNames <div id="sec-labelled-statements-static-semantics-vardeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回  LabelledItem 的 VarDeclaredNames

```
LabelledItem : FunctionDeclaration
```

1. 返回一个新的空列表.

### 13.13.13 静态语义：VarScopedDeclarations <div id="sec-labelled-statements-static-semantics-varscopeddeclarations"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回  LabelledItem 的 VarScopedDeclarations

```
LabelledItem : FunctionDeclaration
```

1. 返回一个新的空列表.

### 13.13.14 运行时语义：LabelledEvaluation <div id="sec-labelled-statements-runtime-semantics-labelledevaluation"></div>

使用参数 labelSet

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 令 label 为  LabelIdentifier 的 StringValue
2. Append label as an labelSet 的 element
3. 令 stmtResult 为带有参数 labelSet 的 LabelledItem 的 LabelledEvaluation
4. 若 stmtResult.[[Type]] 是 break and SameValue(stmtResult.[[Target]], label) 是 true，那么
a. 设置 stmtResult 为 NormalCompletion(stmtResult.[[Value]]).
5. 返回 Completion(stmtResult).

```
LabelledItem : Statement
```

1. 若 Statement 是 either a LabelledStatement or a BreakableStatement，那么
a. 返回带有参数 labelSet 的 Statement 的 LabelledEvaluation
2. 否则，
a. 返回 Statement 的运算结果

```
LabelledItem : FunctionDeclaration
```

1. 返回 FunctionDeclaration 的运算结果

### 13.13.15 运行时语义：Evaluation <div id="sec-labelled-statements-runtime-semantics-evaluation"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 令 newLabelSet 为一个新的空列表.
2. 返回 LabelledEvaluation of this LabelledStatement with argument newLabelSet.

## 13.14 throw 语句 <div id="sec-throw-statement"></div>

**语法**

```
ThrowStatement[Yield, Await] :
    throw [no LineTerminator here] Expression[+In, ?Yield, ?Await] ;
```

### 13.14.1 运行时语义：Evaluation <div id="sec-throw-statement-runtime-semantics-evaluation"></div>

```
ThrowStatement : throw Expression ;
```

1. 令 exprRef 为 Expression 的运算结果
2. 令 exprValue 为 ? GetValue(exprRef).
3. 返回 ThrowCompletion(exprValue).

## 13.15 try 语句 <div id="sec-try-statement"></div>

**语法**

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

> 注：The try statement encloses a block of code in which an exceptional condition can occur, such as a runtime error or a throw statement. The catch clause provides the exception-handling code. When a catch clause catches an exception, its CatchParameter 是 bound to that exception.

### 13.15.1 静态语义：Early Errors <div id="sec-try-statement-static-semantics-early-errors"></div>

```
Catch : catch ( CatchParameter ) Block
```

It 是 a Syntax Error if BoundNames of CatchParameter contains any duplicate elements.
It 是 a Syntax Error if any element of the BoundNames of CatchParameter also occurs in the
LexicallyDeclaredNames of Block.

It 是 a Syntax Error if any element of the BoundNames of CatchParameter also occurs in  Block 的 VarDeclaredNames

> 注：An alternative static semantics for this production 是 given in B.3.5.

### 13.15.2 静态语义：ContainsDuplicateLabels <div id="sec-try-statement-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
TryStatement : try Block Catch
```

1. 令 hasDuplicates 为带有参数 labelSet 的 Block 的 ContainsDuplicateLabels
2. 若 hasDuplicates 是 true，返回 true.
3. 返回带有参数 labelSet 的 Catch 的 ContainsDuplicateLabels

```
TryStatement : try Block Finally
```

1. 令 hasDuplicates 为带有参数 labelSet 的 Block 的 ContainsDuplicateLabels
2. 若 hasDuplicates 是 true，返回 true.
3. 返回带有参数 labelSet 的 Finally 的 ContainsDuplicateLabels

```
TryStatement : try Block Catch Finally
```

1. 令 hasDuplicates 为带有参数 labelSet 的 Block 的 ContainsDuplicateLabels
2. 若 hasDuplicates 是 true，返回 true.
3. 令 hasDuplicates 为带有参数 labelSet 的 Catch 的 ContainsDuplicateLabels
4. 若 hasDuplicates 是 true，返回 true.
5. 返回带有参数 labelSet 的 Finally 的 ContainsDuplicateLabels

```
Catch : catch ( CatchParameter ) Block
```

1. 返回带有参数 labelSet 的 Block 的 ContainsDuplicateLabels

### 13.15.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-try-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
TryStatement : try Block Catch
```

1. 令 hasUndefinedLabels 为带有参数 labelSet 的 Block 的 ContainsUndefinedBreakTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回带有参数 labelSet 的 Catch 的 ContainsUndefinedBreakTarget

```
TryStatement : try Block Finally
```

1. 令 hasUndefinedLabels 为带有参数 labelSet 的 Block 的 ContainsUndefinedBreakTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回带有参数 labelSet 的 Finally 的 ContainsUndefinedBreakTarget

```
TryStatement : try Block Catch Finally
```

1. 令 hasUndefinedLabels 为带有参数 labelSet 的 Block 的 ContainsUndefinedBreakTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 令 hasUndefinedLabels 为带有参数 labelSet 的 Catch 的 ContainsUndefinedBreakTarget
4. 若 hasUndefinedLabels 是 true，返回 true.
5. 返回带有参数 labelSet 的 Finally 的 ContainsUndefinedBreakTarget

```
Catch : catch ( CatchParameter ) Block
```

1. 返回带有参数 labelSet 的 Block 的 ContainsUndefinedBreakTarget

### 13.15.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-try-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
TryStatement : try Block Catch
```

1. 令 hasUndefinedLabels 为带有参数 iterationSet 和 « » 的 Block 的 ContainsUndefinedContinueTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回带有参数 iterationSet 和 « » 的 Catch 的 ContainsUndefinedContinueTarget

```
TryStatement : try Block Finally
```

1. 令 hasUndefinedLabels 为带有参数 iterationSet 和 « » 的 Block 的 ContainsUndefinedContinueTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回带有参数 iterationSet 和 « » 的 Finally 的 ContainsUndefinedContinueTarget

```
TryStatement : try Block Catch Finally
```

1. 令 hasUndefinedLabels 为带有参数 iterationSet 和 « » 的 Block 的 ContainsUndefinedContinueTarget
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 令 hasUndefinedLabels 为带有参数 iterationSet 和 « » 的 Catch 的 ContainsUndefinedContinueTarget
4. 若 hasUndefinedLabels 是 true，返回 true.
5. 返回带有参数 iterationSet 和 « » 的 Finally 的 ContainsUndefinedContinueTarget

```
Catch : catch ( CatchParameter ) Block
```

1. 返回带有参数 iterationSet 和 « » 的 Block 的 ContainsUndefinedContinueTarget

### 13.15.5 静态语义：VarDeclaredNames <div id="sec-try-statement-static-semantics-vardeclarednames"></div>

```
TryStatement : try Block Catch
```

1. 令 names 为 Block 的 VarDeclaredNames
2. 在 names 后追加 Catch 的 VarDeclaredNames 元素 
3. 返回 names.

```
TryStatement : try Block Finally
```

1. 令 names 为 Block 的 VarDeclaredNames
2. 在 names 后追加 Finally 的 VarDeclaredNames 元素 
3. 返回 names.

```
TryStatement : try Block Catch Finally
```

1. 令 names 为 Block 的 VarDeclaredNames
2. 在 names 后追加 Catch 的 VarDeclaredNames 元素 
3. 在 names 后追加 Finally 的 VarDeclaredNames 元素 
4. 返回 names.

```
Catch : catch ( CatchParameter ) Block
```

1. 返回  Block 的 VarDeclaredNames

### 13.15.6 静态语义：VarScopedDeclarations <div id="sec-try-statement-static-semantics-varscopeddeclarations"></div>

```
TryStatement : try Block Catch
```

1. 令 declarations 为 Block 的 VarScopedDeclarations
2. 在 declarations 后追加 Catch 的 VarScopedDeclarations 元素 
3. 返回 declarations.

```
TryStatement : try Block Finally
```

1. 令 declarations 为 Block 的 VarScopedDeclarations
2. 在 declarations 后追加 Finally 的 VarScopedDeclarations 元素 
3. 返回 declarations.

```
TryStatement : try Block Catch Finally
```

1. 令 declarations 为 Block 的 VarScopedDeclarations
2. 在 declarations 后追加 Catch 的 VarScopedDeclarations 元素 
3. 在 declarations 后追加 Finally 的 VarScopedDeclarations 元素 
4. 返回 declarations.

```
Catch : catch ( CatchParameter ) Block
```

1. 返回  Block 的 VarScopedDeclarations

### 13.15.7 运行时语义：CatchClauseEvaluation <div id="sec-runtime-semantics-catchclauseevaluation"></div>

使用参数 thrownValue

```
Catch : catch ( CatchParameter ) Block
```

1. 令 oldEnv 为运行时执行上下文的词法环境.
2. 令 catchEnv 为 NewDeclarativeEnvironment(oldEnv).
3. 令 catchEnvRec 为 catchEnv's EnvironmentRecord.
4. For each element argName of the BoundNames of CatchParameter, do
a. 执行 ! catchEnvRec.CreateMutableBinding(argName, false).
5. 设置运行时执行上下文的词法环境 为 catchEnv.
6. 令 status 为 performing BindingInitialization for CatchParameter passing thrownValue and catchEnv 的结果
as arguments.
7. 若 status 是 abrupt completion，那么
a. 设置运行时执行上下文的词法环境 为 oldEnv.
b. 返回 Completion(status).
8. 令 B 为 Block 的运算结果
9. 设置运行时执行上下文的词法环境 为 oldEnv.
10. 返回 Completion(B).

```
Catch : catch Block
```

1. 返回 evaluating Block 的结果

> 注：No matter how control leaves the Block the LexicalEnvironment 是 always restored to its former state.

### 13.15.8 运行时语义：Evaluation <div id="sec-try-statement-runtime-semantics-evaluation"></div>

```
TryStatement : try Block Catch
```

1. 令 B 为 Block 的运算结果
2. 若 B.[[Type]] 是 throw, let C be带有参数 B 的 Catch 的 CatchClauseEvaluation[[Value]].
3. 否则， let C be B.
4. 返回 Completion(UpdateEmpty(C, undefined)).

```
TryStatement : try Block Finally
```

1. 令 B 为 Block 的运算结果
2. 令 F 为 Finally 的运算结果
3. 若 F.[[Type]] 是 normal, 设置 F 为 B.
4. 返回 Completion(UpdateEmpty(F, undefined)).

```
TryStatement : try Block Catch Finally
```

1. 令 B 为 Block 的运算结果
2. 若 B.[[Type]] 是 throw, let C be带有参数 B 的 Catch 的 CatchClauseEvaluation[[Value]].
3. 否则， let C be B.
4. 令 F 为 Finally 的运算结果
5. 若 F.[[Type]] 是 normal, 设置 F 为 C.
6. 返回 Completion(UpdateEmpty(F, undefined)).

## 13.16 debugger 语句 <div id="sec-debugger-statement"></div>

**语法**

```
DebuggerStatement :
    debugger ;
```

### 13.16.1 运行时语义：Evaluation <div id="sec-debugger-statement-runtime-semantics-evaluation"></div>

> 注：Evaluating a DebuggerStatement may allow an implementation to cause a breakpoint when run under a debugger. 若 a debugger 不存在 or active this statement has no observable effect.

1. 若 an implementation-defined debugging facility 是 available and enabled，那么
a. 执行 an implementation-defined debugging action.
b. 令 result 为 an implementation-defined Completion value.
2. 否则，
a. 令 result 为 NormalCompletion(empty).
3. 返回 result.