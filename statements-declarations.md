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

如果相关 if 的选择不明确，则每个 else 应与最接近的 if 相关联，否则该 if 没有相应的 else

### 13.6.1 静态语义：Early Errors <div id="sec-if-statement-static-semantics-early-errors"></div>

```
IfStatement :
    if ( Expression ) Statement else Statement
    if ( Expression ) Statement
```

如果 IsLabelled 函数（语句）为 true，则出现语法错误

> 注：仅当实现了 B.3.2 中指定的扩展时，才需要应用此规则。

### 13.6.2 静态语义：ContainsDuplicateLabels <div id="sec-if-statement-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 hasDuplicate 为包含第一个带有参数labelSet的Statement的DuplicateLabels。
2. 若 hasDuplicate 是 true，返回 true.
3. 返回包含第二个带有参数labelSet的Statement的DuplicateLabel。

```
IfStatement : if ( Expression ) Statement
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsDuplicateLabels

### 13.6.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-if-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 hasUndefinedLabels 为包含参数labelSet的第一个语句的ContainsUndefinedBreakTarget。
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回包含第二个带有参数labelSet的语句的UndefinedBreakTarget。

```
IfStatement : if ( Expression ) Statement
```

1. 返回带有参数 labelSet 的 Statement 的 ContainsUndefinedBreakTarget

### 13.6.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-if-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 hasUndefinedLabels 为包含第一个Statement的UndefinedContinueTarget，其参数为iterationSet和«»。
2. 若 hasUndefinedLabels 是 true，返回 true.
3. 返回包含第二个Statement的UndefinedContinueTarget，其参数为iterationSet和«»。

```
IfStatement : if ( Expression ) Statement
```

1. 返回带有参数 iterationSet 和 « » 的 Statement 的 ContainsUndefinedContinueTarget

### 13.6.5 静态语义：VarDeclaredNames <div id="sec-if-statement-static-semantics-vardeclarednames"></div>

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 names 为第一条语句的VarDeclaredNames。
2. 在 names 后追加第一条语句的VarDeclaredNames的元素
3. 返回 names.

```
IfStatement : if ( Expression ) Statement
```

1. 返回  Statement 的 VarDeclaredNames

### 13.6.6 静态语义：VarScopedDeclarations <div id="sec-if-statement-static-semantics-varscopeddeclarations"></div>

```
IfStatement : if ( Expression ) Statement else Statement
```

1. 令 declarations 为第一条语句的VarScopedDeclarations。
2. 在 declarations 后追加第二条语句的VarScopedDeclarations的元素
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
a. 令 stmtCompletion 为第一条语句的运算结果
4. 否则，
a. 令 stmtCompletion 为第二条语句的运算结果
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

> 注：本节由附录B.3.6扩展。

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

> 注：本节由附录B.3.6扩展。仅当实施了B.3.2中指定的扩展时，才需要应用此规则。

#### 13.7.1.2 运行时语义：LoopContinues ( completion, labelSet ) <div id="sec-loopcontinues"></div>

通过以下步骤定义带有参数完成和labelSet的抽象操作LoopContinues：

1. 若 completion.[[Type]] 是 normal，返回 true.
2. 若 completion.[[Type]] 不是 continue，返回 false.
3. 若 completion.[[Target]] 是 empty，返回 true.
4. 若 completion.[[Target]] 是labelSet的元素，返回 true.
5. 返回 false.

> 注：在IterationStatement的Statement部分内，可以使用ContinueStatement开始新的迭代。

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
   1. 令 stmtResult 为 Statement 的运算结果
   2. 若 LoopContinues(stmtResult, labelSet) 是 false，返回 Completion(UpdateEmpty(stmtResult, V)).
   3. 若 stmtResult.[[Value]] 不是 empty, 设置 V 为 stmtResult.[[Value]].
   4. 令 exprRef 为 Expression 的运算结果
   5. 令 exprValue 为 ? GetValue(exprRef).
   6. 若 ToBoolean(exprValue) 是 false，返回 NormalCompletion(V).

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
    1. 令 exprRef 为 Expression 的运算结果
    2. 令 exprValue 为 ? GetValue(exprRef).
    3. 若 ToBoolean(exprValue) 是 false，返回 NormalCompletion(V).
    4. 令 stmtResult 为 Statement 的运算结果
    5. 若 LoopContinues(stmtResult, labelSet) 是 false，返回 Completion(UpdateEmpty(stmtResult, V)).
    6. 若 stmtResult.[[Value]] 不是 empty, 设置 V 为 stmtResult.[[Value]].

### 13.7.4 for 语句 <div id="sec-for-statement"></div>
#### 13.7.4.1 静态语义：Early Errors <div id="sec-for-statement-static-semantics-early-errors"></div>

```
IterationStatement : for ( LexicalDeclaration Expression ; Expression ) Statement
```

如果LexicalDeclaration的BoundNames的任何元素也出现在语句的VarDeclaredNames中，则是语法错误

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

1. 返回包含带有参数labelSet的语句的ContainsUndefinedBreakTarget。

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
    1. 令 exprRef 为 the first Expression 的运算结果
    2. 执行 ? GetValue(exprRef).
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
3. 令 loopEnvRec 为 loopEnv 的环境记录项
4. 令 isConst 为 执行 LexicalDeclaration 的 IsConstantDeclaration 的结果
5. 令 boundNames 为  LexicalDeclaration 的 BoundNames
6. 对于每一个 boundNames 的 dn 元素, 执行
    1. 若 isConst 是 true，那么
        1. 执行 ! loopEnvRec.CreateImmutableBinding(dn, true).
    2. 否则，
        1. 执行 ! loopEnvRec.CreateMutableBinding(dn, false).
7. 设置运行时执行上下文的词法环境 为 loopEnv.
8. 令 forDcl 为 LexicalDeclaration 的运算结果
9. 若 forDcl 是 abrupt completion，那么
      1. 设置运行时执行上下文的词法环境 为 oldEnv.
      2. 返回 Completion(forDcl).
10. 若 isConst 是 false, 令 perIterationLets 为 boundNames; 否则令 perIterationLets 为 « ».
11. 令 bodyResult 为 ForBodyEvaluation(the first Expression, the second Expression, Statement, perIterationLets, labelSet).
12. 设置运行时执行上下文的词法环境 为 oldEnv.
13. 返回 Completion(bodyResult)

#### 13.7.4.8 运行时语义：ForBodyEvaluation ( test, increment, stmt, perIterationBindings, labelSet ) <div id="sec-forbodyevaluation"></div>

带有参数test，increment，stmt，perIterationBindings和labelSet的抽象操作ForBodyEvaluation如下执行：

1. 令 V 为 undefined.
2. 执行 ? CreatePerIterationEnvironment(perIterationBindings).
3. 重复，
    1. 若 test 不是 [empty]，那么
        1. 令 testRef 为 test 的运算结果
        2. 令 testValue 为 ? GetValue(testRef).
        3. 若 ToBoolean(testValue) 是 false，返回 NormalCompletion(V).
    2. 令 result 为 stmt 的运算结果
    3. 若 LoopContinues(result, labelSet) 是 false，返回 Completion(UpdateEmpty(result, V)).
    4. 若 result.[[Value]] 不是 empty, 设置 V 为 result.[[Value]].
    5. 执行 ? CreatePerIterationEnvironment(perIterationBindings).
    6. 若 increment 不是 [empty]，那么
        1. 令 incRef 为 increment 的运算结果
        2. 执行 ? GetValue(incRef).

#### 13.7.4.9 运行时语义：CreatePerIterationEnvironment ( perIterationBindings ) <div id="sec-createperiterationenvironment"></div>

具有参数perIterationBindings的抽象操作CreatePerIterationEnvironment的执行方式如下：

1. 若 perIterationBindings 有任何元素，那么
    1. 令 lastIterationEnv 为运行时执行上下文的词法环境.
    2. 令 lastIterationEnvRec 为 lastIterationEnv 的环境记录项
    3. 令 outer 为 lastIterationEnv 的外部环境引用
    4. 断言: outer 不是 null.
    5. 令 thisIterationEnv 为 NewDeclarativeEnvironment(outer).
    6. 令 thisIterationEnvRec 为 thisIterationEnv 的环境记录项
    7. 对于 perIterationBindings 的每一个 bn 元素, 执行
        1. 执行 ! thisIterationEnvRec.CreateMutableBinding(bn, false).
        2. 令 lastValue 为 ? lastIterationEnvRec.GetBindingValue(bn, true).
        3. 执行 thisIterationEnvRec.InitializeBinding(bn, lastValue).
    8. 设置运行时执行上下文的词法环境 为 thisIterationEnv.
2. 返回 undefined.

### 13.7.5 for-in, for-of, 和for-await-of 语句 <div id="sec-for-in-and-for-of-statements"></div>
#### 13.7.5.1 静态语义：Early Errors <div id="sec-for-in-and-for-of-statements-static-semantics-early-errors"></div>

```
IterationStatement :
    for ( LeftHandSideExpression in Expression ) Statement
    for ( LeftHandSideExpression of AssignmentExpression ) Statement
    for await ( LeftHandSideExpression of AssignmentExpression ) Statement
```

- 如果LeftHandSideExpression是ObjectLiteral或ArrayLiteral，并且LeftHandSideExpression没有覆盖AssignmentPattern，则它是语法错误。

如果LeftHandSideExpression是ObjectLiteral或ArrayLiteral，并且如果LeftHandSideExpression覆盖了AssignmentPattern，则不应用以下规则。而是使用AssignmentPattern的Early Error规则。

- 如果LeftHandSideExpression的AssignmentTargetType不是simple，则是语法错误。
- 如果LeftHandSideExpression为CoverParenthesizedExpressionAndArrowParameterList : (Expression)，如果该短语替换了LeftHandSideExpression，则Expression会根据这些规则派生一个语法错误。该规则是递归应用的。

> 注：最后一条规则意味着即使括号括在Expression上，也要应用其他规则。

```
IterationStatement :
    for ( ForDeclaration in Expression ) Statement
    for ( ForDeclaration of AssignmentExpression ) Statement
    for await ( ForDeclaration of AssignmentExpression ) Statement
```

- 如果 ForDeclaration 的 BoundNames 包含"let"，则是语法错误。
- 如果 ForDeclaration 的 BoundNames 的任何元素也出现在语句的 VarDeclaredNames 中，则是语法错误。
- 如果 ForDeclaration 的 BoundNames 包含任何重复的条目，则是语法错误。

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

> 注：本节由附录B.3.6扩展。

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

> 注：本节由附录B.3.6扩展。

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

> 注：本节由附录B.3.6扩展。

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

> 注：本节由附录B.3.6扩展。

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

> 注：本节由附录B.3.6扩展。

#### 13.7.5.8 静态语义：VarScopedDeclarations <div id="sec-for-in-and-for-of-statements-static-semantics-varscopeddeclarations"></div>

```
IterationStatement : for ( LeftHandSideExpression in Expression ) Statement
```

1. 返回  Statement 的 VarScopedDeclarations

```
IterationStatement : for ( var ForBinding in Expression ) Statement
```

1. 令 declarations 为 包含 ForBinding 的列表.

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

   1. 令 declarations 为 包含 ForBinding 的列表.
   2. 在 declarations 后追加 Statement 的 VarScopedDeclarations 元素 
   3. 返回 declarations.

   ```
   IterationStatement :
       for ( ForDeclaration of AssignmentExpression ) Statement
       for await ( ForDeclaration of AssignmentExpression ) Statement
   ```

   1. 返回  Statement 的 VarScopedDeclarations

> 注：本节由附录B.3.6扩展。

#### 13.7.5.9 运行时语义：BindingInitialization <div id="sec-for-in-and-for-of-statements-runtime-semantics-bindinginitialization"></div>

使用参数value和environment

> 注：为环境传递undefined，以指示应该使用PutValue操作来分配初始化值。 var语句和某些非严格函数的形式参数列表就是这种情况（请参见9.2.15）。在这些情况下，在评估其初始值设定项之前会先悬挂并预初始化词法绑定

```
ForDeclaration : LetOrConst ForBinding
```

1. 返回为ForBinding执行BindingInitialization的结果，将 value 和 enviroment 作为参数传递。

#### 13.7.5.10 运行时语义：BindingInstantiation <div id="sec-runtime-semantics-bindinginstantiation"></div>

使用参数 environment

```
ForDeclaration : LetOrConst ForBinding
```

1. 令 envRec 为 environment 的环境记录项
2. 断言: envRec 是声明环境记录项.
3. 对于 ForBinding 的每一个 BoundNames 的 name 元素, 执行
   1. 若 LetOrConst 的 IsConstantDeclaration 是 true，那么
      1. 执行 ! envRec.CreateImmutableBinding(name, true).
   2. 否则，
      1. 执行 ! envRec.CreateMutableBinding(name, false).

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

> 注：本节由附录B.3.6扩展。

#### 13.7.5.12 运行时语义：ForIn/OfHeadEvaluation ( TDZnames, expr, iterationKind ) <div id="sec-runtime-semantics-forin-div-ofheadevaluation-tdznames-expr-iterationkind"></div>

抽象操作ForIn / OfHeadEvaluation用TDZnames，expr和erationKind自变量调用。 erationKind的值可以是枚举，迭代或异步迭代。

1. 令 oldEnv 为运行时执行上下文的词法环境.
2. 若 TDZnames 不是空列表，那么
    1. 断言: TDZnames 没有重复的条目。
    2. 令 TDZ 为 NewDeclarativeEnvironment(oldEnv).
    3. 令 TDZEnvRec 为 TDZ 的环境记录项.
    4. 对于TDZnames中的 name 字符串，执行
        1. 执行 ! TDZEnvRec.CreateMutableBinding(name, false).
    5. 设置运行时执行上下文的词法环境 为 TDZ.
3. 令 exprRef 为 expr 的运算结果
4. 设置运行时执行上下文的词法环境 为 oldEnv.
5. 令 exprValue 为 ? GetValue(exprRef).
6. 若 iterationKind 是 enumerate，那么
    1. 若 exprValue 是 undefined 或 null，那么
        1. 返回 Completion { [[Type]]: break, [[Value]]: empty, [[Target]]: empty }.
    2. 令 obj 为 ! ToObject(exprValue).
    3. 返回 ? EnumerateObjectProperties(obj).
7. 否则，
    1. 断言: iterationKind 是 iterate.
    2. 若 iterationKind 是 async-iterate, 令 iteratorHint 为 async.
    3. 否则，令 iteratorHint 为 sync.
    4. 返回 ? GetIterator(exprValue, iteratorHint).

#### 13.7.5.13 运行时语义：ForIn/OfBodyEvaluation ( lhs, stmt, iteratorRecord, iterationKind, lhsKind, labelSet [ , iteratorKind ] ) <div id="sec-runtime-semantics-forin-div-ofbodyevaluation-lhs-stmt-iterator-lhskind-labelset"></div>

抽象操作 ForIn/OfBodyEvaluation使用参数 lhs、stmt、iteratorRecord、iterationKind、lhsKind、labelSet 和可选参数iteratorKind 调用。lhsKind 的值是assignment、varBinding 或词法绑定。iteratorKind 的值是sync的或async的。

1. 若 iteratorKind 不存在, 设置 iteratorKind 为 sync.
2. 令 oldEnv 为运行时执行上下文的词法环境.
3. 令 V 为 undefined.
4. 令 destructuring 为 lhs 的 IsDestructuring
5. 若 destructuring 是 true 并且如果 lhsKind 是 assignment，那么
    1. 断言: lhs 是 LeftHandSideExpression.
    2. 令AssignmentPattern为lhs涵盖的AssignmentPattern。
6. 重复，
   1. 令 nextResult 为 ? Call(iteratorRecord.[[NextMethod]], iteratorRecord.[[Iterator]], « »).
   2. 若 iteratorKind 是 async，那么 设置 nextResult 为 ? Await(nextResult).
   3. 若 Type(nextResult) 不是 Object, 抛出 TypeError 异常.
   4. 令 done 为 ? IteratorComplete(nextResult).
   5. 若 done 是 true，返回 NormalCompletion(V).
   6. 令 nextValue 为 ? IteratorValue(nextResult).
   7. 若 lhsKind 是 assignment 或 varBinding，那么
        1. 若 destructuring 是 false，那么
            1. 令 lhsRef 为运算 lhs（可能被重复求值） 的结果
   8. 否则，
        1. 断言: lhsKind 是 lexicalBinding.
        2. 断言: lhs 是 ForDeclaration.
        3. 令 iterationEnv 为 NewDeclarativeEnvironment(oldEnv).
        4. 为LHS执行BindingInstantiation，将iterationEnv作为参数传递。
        5. 设置运行时执行上下文的词法环境 为 iterationEnv.
        6. 若 destructuring 是 false，那么
            1. 断言: lhs 绑定一个名字
            2. 令 lhsName 为 lhs 的 BoundNames 的唯一元素
            3. 令 lhsRef 为 ! ResolveBinding(lhsName).
   9. 若 destructuring 是 false，那么
        1. 若 lhsRef 是 abrupt completion，那么
            1. 令 status 为 lhsRef.
        2. 否则如果 lhsKind 是 lexicalBinding，那么
            1. 令 status 为 InitializeReferencedBinding(lhsRef, nextValue).
        3. 否则，
            1. 令 status 为 PutValue(lhsRef, nextValue).
   10. 否则，
        1. 若 lhsKind 是 assignment，那么
            1. 令 status 为使用nextValue作为参数，执行AssignmentPattern的DestructuringAssignmentEvaluation的结果
        2. 否则如果 lhsKind 是 varBinding，那么
            1. 断言: lhs 是 ForBinding.
            2. 令 status 为 lhs 传递 nextValue 和 undefined 作为参数，执行 BindingInitialization 的结果。
        3. 否则，
            1. 断言: lhsKind 是 lexicalBinding.
            2. 断言: lhs 是 a ForDeclaration.
            3. 令 status 为 lhs 传递 nextValue 和 iterationEnv 作为参数，执行BindingInitialization的结果。
   11.  若 status 是 abrupt completion，那么
        1. 设置运行时执行上下文的词法环境 为 oldEnv.
        2. 若 iteratorKind 是 async，返回 ? AsyncIteratorClose(iteratorRecord, status).
        3. 若 iterationKind 是 enumerate，那么
            1. 返回 status.
        4. 否则，
            1. 断言: iterationKind 是 iterate.
            2. 返回 ? IteratorClose(iteratorRecord, status).
   12. 令 result 为 stmt 的运算结果
   13. 设置运行时执行上下文的词法环境 为 oldEnv.
   14. 若 LoopContinues(result, labelSet) 是 false，那么
        1. 若 iterationKind 是 enumerate，那么
            1. 返回 Completion(UpdateEmpty(result, V)).
        2. 否则，
            1. 断言: iterationKind 是 iterate.
            2. 设置 status 为 UpdateEmpty(result, V).
            3. 若 iteratorKind 是 async，返回 ? AsyncIteratorClose(iteratorRecord, status).
            4. 返回 ? IteratorClose(iteratorRecord, status).
    15. 若 result.[[Value]] 不是 empty, 设置 V 为 result.[[Value]].

#### 13.7.5.14 运行时语义：Evaluation <div id="sec-for-in-and-for-of-statements-runtime-semantics-evaluation"></div>

```
ForBinding : BindingIdentifier
```

1. 令 bindingId 为 BindingIdentifier 的 StringValue
2. 返回 ? ResolveBinding(bindingId).

#### 13.7.5.15 EnumerateObjectProperties ( O ) <div id="sec-enumerate-object-properties"></div>

当使用参数OF调用抽象操作枚举对象属性时，将执行以下步骤：

1. 断言: Type(O) 是 Object.
2. 返回一个Iterator对象（25.1.1.2），其下一个方法迭代O的enumerableproperties的所有字符串值键。ECMAScript代码永远不能直接访问该迭代器对象。没有指定枚举属性的机制和顺序，但必须符合以下指定的规则。

迭代器的throw和return方法为null，并且永远不会调用。迭代器的next方法处理对象属性，以确定是否应将属性键作为迭代器值返回。返回的属性键不包括作为符号的键。在枚举过程中可能会删除目标对象的属性。在迭代器的next方法处理之前删除的属性将被忽略。如果在枚举过程中将新属性添加到目标对象，则不能保证新添加的属性将在活动枚举中进行处理。在任何枚举中，属性名称最多由迭代器的next方法返回。

枚举目标对象的属性包括递归枚举其原型的属性和原型的原型，等等。但是，如果原型的属性与迭代器的next方法已经处理过的属性具有相同的名称，则不会处理原型的属性。在确定原型对象的属性是否已被处理时，不考虑[[Enumerable]]属性的值。原型对象的可枚举属性名称必须通过调用EnumerateObjectProperties并将原型对象作为参数来获得。 EnumerateObjectProperties必须通过调用目标对象的[[OwnPropertyKeys]]内部方法来获取目标对象自己的属性键。目标对象的属性属性必须通过调用其[[GetOwnProperty]]内部方法来获取。

> 注：以下是符合这些规则的ECMAScript生成器函数的参考性定义：
>
> function* EnumerateObjectProperties(obj) {
> 	const visited = new Set();
> 	for (const Reflect 的 keyownKeys(obj)) {
> 		if (typeof key === "symbol") continue;
>    	const desc = Reflect.getOwnPropertyDescriptor(obj, key);
>    	if (desc) {
>        	visited.add(key);
>        	if (desc.enumerable) yield key;
>    	}
> 	}
> 	const proto = Reflect.getPrototypeOf(obj);
> 	if (proto === null) return;
> 	for (const protoKey of EnumerateObjectProperties(proto)) {
>     	if (!visited.has(protoKey)) yield protoKey;
> 	}
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

- 如果此ContinueStatement没有在IterationStatement中直接或间接（但不跨越函数边界）嵌套，则为语法错误。

### 13.8.2 静态语义：ContainsUndefinedContinueTarget <div id="sec-continue-statement-static-semantics-containsundefinedcontinuetarget"></div>

使用参数iterationSet和labelSet

```
ContinueStatement : continue ;
```

1. 返回 false.

```
ContinueStatement : continue LabelIdentifier ;
```

1. 若 LabelIdentifier 的 StringValue 不是 iterationSet 元素，返回 true.
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

- 如果此BreakStatement没有在IterationStatement或SwitchStatement中直接或间接（但不跨越函数边界）嵌套，则为语法错误。

### 13.9.2 静态语义：ContainsUndefinedBreakTarget <div id="sec-break-statement-static-semantics-containsundefinedbreaktarget"></div>

使用参数 labelSet

```
BreakStatement : break ;
```

1. 返回 false.

```
BreakStatement : break LabelIdentifier ;
```

1. 若 LabelIdentifier 的 StringValue 不是 labelSet 元素，返回 true.
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

> 注：return语句使函数停止执行，并且在大多数情况下，将值返回给调用方。如果省略Expression，则返回值是不确定的。否则，返回值是Expression的值。根据周围的上下文，return语句实际上可能不会将值返回给调用方。例如，在try块中，可以在评估fially块期间将return语句的完成记录替换为另一个完成记录。

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

> 注：with语句将已计算对象的对象环境记录添加到正在运行的执行上下文的词法环境中。然后，它使用此扩充词法环境执行一条语句。最后，它还原了原始词汇环境。

### 13.11.1 静态语义：Early Errors <div id="sec-with-statement-static-semantics-early-errors"></div>

```
WithStatement : with ( Expression ) Statement
```

- 如果与此模式匹配的代码包含在严格模式代码中，则是语法错误。
- 如果IsLabelledFunction（Statement）为true，则为语法错误。

> 注：如果实现了B.3.2中指定的扩展，则仅需应用第二条规则。

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

> 注：无论控件是如何离开嵌入的Statement的，无论是正常还是通过某种形式的突然完成或异常，LexicalEnvironment始终都会恢复为其以前的状态。

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

- 如果CaseBlock的LexicallyDeclaredNames包含任何重复的条目，则是语法错误。
- 如果CaseBlock的LexicallyDeclaredNames中的任何元素也出现在CaseBlock的VarDeclaredNames中，则是语法错误。

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
1. 令 hasDuplicates 为包含第一个带有参数labelSet的CaseClauses的DuplicateLabel。
2. 若 hasDuplicates 是 true，返回 true.
2. 令 hasDuplicates 为带有参数 labelSet 的 DefaultClause 的 ContainsDuplicateLabels
3. 若 hasDuplicates 是 true，返回 true.
4. 若 the second CaseClauses 不存在，返回 false.
5. 返回带有参数labelSet的第二个CaseClauses的ContainsDuplicateLabel

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
1. 令 hasUndefinedLabels 为带有参数labelSet的第一个CaseClauses的ContainsUndefinedBreakTarget若 hasUndefinedLabels 是 true，返回 true.
2. 令 hasUndefinedLabels 为带有参数 labelSet 的 DefaultClause 的 ContainsUndefinedBreakTarget
3. 若 hasUndefinedLabels 是 true，返回 true.
4. 若 the second CaseClauses 不存在，返回 false.
5. 返回带有参数labelSet的第二个CaseClauses的ContainsUndefinedBreakTarget。

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
1. 令 hasUndefinedLabels 为包含第一个CaseClauses的UndefinedContinueTarget，带有参数erationSet和«»
2. 若 hasUndefinedLabels 是 true，返回 true.
2. 令 hasUndefinedLabels 为 ContainsUndefinedContinueTarget of DefaultClause with arguments iterationSet and «».
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

1. 若 the first CaseClauses 存在, 令 name 为第一个CaseClauses的LexicallyDeclaredNames。
2. 否则， 令 names 为一个新的空列表.
3. 在 names 后追加 DefaultClause 的 LexicallyDeclaredNames 元素 
4. 若 the second CaseClauses 不存在，返回 names

5. 返回将第二个CaseClauses的LexicallyDeclaredNames元素添加到names之后的结果。

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

1. 若 the first CaseClauses 存在, 令 declarations 为第一个CaseClauses的LexicallyScopedDeclarations。
2. 否则， 令 declarations 为一个新的空列表.
3. 在 declarations 后追加 DefaultClause 的 LexicallyScopedDeclarations 元素 
4. 若 the second CaseClauses 不存在，返回 declarations.
5. 返回在declarations之后附加第二个CaseClauses的LexicalScopedDeclarations的元素的结果

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

1. 若 the first CaseClauses 存在, 令 names 为第一个CaseClauses的VarDeclaredNames。
2. 否则， 令 names 为一个新的空列表.
3. 在 names 后追加 DefaultClause 的 VarDeclaredNames 元素 
4. 若 the second CaseClauses 不存在，返回 names.
5. 返回在names后面附加第二个CaseClauses的VarDeclaredNames的元素的结果

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

1. 若 the first CaseClauses 存在, 令 declarations 为 第一个CaseClauses的VarScopedDeclaration。
2. 否则， 令 declarations 为一个新的空列表.
3. 在 declarations 后追加 DefaultClause 的 VarScopedDeclarations 元素 
4. 若 the second CaseClauses 不存在，返回 declarations.
5. 返回在声明后追加第二个CaseClauses的VarScopedDeclarations的元素的结果

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
2. 令 A 为源文本顺序排列的CaseClauses中的CaseClause项目列表
3. 令 found 为 false.
4. 对于A中的CaseClause C，执行
1. 若 found 是 false，那么
  1. 设置 found 为 ? CaseClauseIsSelected(C, input).
2. 若 found 是 true，那么
  1. 令 R 为 C 的运算结果
  2. 若 R.[[Value]] 不是 empty, 设置 V 为 R.[[Value]].
  3. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
5. 返回 NormalCompletion(V).

```
CaseBlock : { CaseClauses DefaultClause CaseClauses }
```

1. 令 V 为 undefined.
2. 若 the first CaseClauses 存在，那么
1. 令 A 为第一个CaseClauses中的CaseClause项目列表，按源文本顺序排列。
3. 否则，
  1. 令 A 为 « ».
4. 令 found 为 false.
5. 对于A中的CaseClause C，执行
  1. 若 found 是 false，那么
        1. 设置 found 为 ? CaseClauseIsSelected(C, input).
  2. 若 found 是 true，那么
        1. 令 R 为 C 的运算结果
            2. 若 R.[[Value]] 不是 empty, 设置 V 为 R.[[Value]].
                3. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
6. 令 foundInB 为 false.
7. 若 the second CaseClauses 存在，那么
  1. 令 B 为第二个CaseClauses中的CaseClause项目列表，按源文本顺序。
8. 否则，
  1. 令 B 为 « ».
9. 若 found 是 false，那么
  1. 对于B中的CaseClause C，执行
        1. 若 foundInB 是 false，那么
      1. 设置 foundInB 为 ? CaseClauseIsSelected(C, input).
  2. 若 foundInB 是 true，那么
  3. 令 R 为 CaseClause C 的运算结果
  4. 若 R.[[Value]] 不是 empty, 设置 V 为 R.[[Value]].
  5. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
10. 若 foundInB 是 true，返回 NormalCompletion(V).
11. 令 R 为 DefaultClause 的运算结果
12. 若 R.[[Value]] 不是 empty, 设置 V 为 R.[[Value]].
13. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
14. 对于在B中的每一个 CaseClause C (注: 这是第二个CaseClauses的另一个完整版本), 执行
    1. 令 R 为 CaseClause C 的运算结果
    2. 若 R.[[Value]] 不是 empty, 设置 V 为 R.[[Value]].
    3. 若 R 是 abrupt completion，返回 Completion(UpdateEmpty(R, V)).
15. 返回 NormalCompletion(V).

### 13.12.10 运行时语义：CaseClauseIsSelected ( C, input ) <div id="sec-runtime-semantics-caseclauseisselected"></div>

给定CaseClause C和值输入，抽象操作CaseClauseIsSelected确定C是否与输入匹配。

1. 断言: C 是产生式的一个实例 CaseClause : case Expression : StatementList .
2. 令 exprRef 为运算 C 的 Expression 的结果
3. 令 clauseSelector 为 ? GetValue(exprRef).
4. 返回执行严格平等比较 input === ClauseSelector 的结果

> 注：此操作不执行C的StatementList（如果有）。 CaseBlock算法使用其返回值来确定要开始执行的StatementList。

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

> 注：无论控件如何离开SwitchStatement，LexicalEnvironment始终会恢复为其以前的状态。

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

> 注：声明可以以标签为前缀。带标签的语句仅与带标签的break和Continue语句结合使用。 ECMAScript没有goto语句。语句可以是LabelledStatement的一部分，它本身可以是LabelledStatement的一部分，依此类推。在描述单个语句的语义时，以这种方式引入的标签统称为“当前标签集”。

### 13.13.1 静态语义：Early Errors <div id="sec-labelled-statements-static-semantics-early-errors"></div>

```
LabelledItem : FunctionDeclaration
```

- 如果任何源文本与该规则匹配，则是语法错误。

> 注：B.3.2提供了该规则的替代定义。

### 13.13.2 静态语义：ContainsDuplicateLabels <div id="sec-labelled-statements-static-semantics-containsduplicatelabels"></div>

使用参数 labelSet

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 令 label 为  LabelIdentifier 的 StringValue
2. 若 label 是 an element of labelSet，返回 true.
3. 令 newLabelSet 为附加了label的labelSet的副本。
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
2. 令 newLabelSet 为附加了label的labelSet的副本。
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
2. 令 newLabelSet 为附加了label的labelSet的副本。
3. 返回带有参数 iterationSet 和 newLabelSet 的 LabelledItem 的 ContainsUndefinedContinueTarget

```
LabelledItem : FunctionDeclaration
```

1. 返回 false.

### 13.13.5 静态语义：IsLabelledFunction ( stmt ) <div id="sec-islabelledfunction"></div>

具有参数stmt的抽象操作IsLabelledFunction执行以下步骤：

1. 若 stmt 不是 LabelledStatement，返回 false.
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

1. 返回包含 FunctionDeclaration 的新列表

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

1. 返回包含 FunctionDeclaration 的新列表

### 13.13.12 静态语义：VarDeclaredNames <div id="sec-labelled-statements-static-semantics-vardeclarednames"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 返回  LabelledItem 的 VarDeclaredNames

```
LabelledItem : FunctionDeclaration
```

1. 返回一个新的空列表。

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
2. 将label附加为labelSet的元素
3. 令 stmtResult 为带有参数 labelSet 的 LabelledItem 的 LabelledEvaluation
4. 若 stmtResult.[[Type]] 是 break 并且 SameValue(stmtResult.[[Target]], label) 是 true，那么
1. 设置 stmtResult 为 NormalCompletion(stmtResult.[[Value]]).
5. 返回 Completion(stmtResult).

```
LabelledItem : Statement
```

1. 若 Statement 是 LabelledStatement 或 BreakableStatement，那么
1. 返回带有参数 labelSet 的 Statement 的 LabelledEvaluation
2. 否则，
  1. 返回 Statement 的运算结果

```
LabelledItem : FunctionDeclaration
```

1. 返回 FunctionDeclaration 的运算结果

### 13.13.15 运行时语义：Evaluation <div id="sec-labelled-statements-runtime-semantics-evaluation"></div>

```
LabelledStatement : LabelIdentifier : LabelledItem
```

1. 令 newLabelSet 为一个新的空列表.
2. 返回带参数newLabelSet的LabelledStatement的LabelledEvaluation。

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

> 注：try语句包含一段代码，在其中可能发生异常情况，例如运行时错误或throw语句。 catch子句提供异常处理代码。当catch子句捕获异常时，其CatchParameter绑定到该异常。

### 13.15.1 静态语义：Early Errors <div id="sec-try-statement-static-semantics-early-errors"></div>

```
Catch : catch ( CatchParameter ) Block
```

- 如果CatchParameter的BoundNames包含任何重复的元素，则是语法错误。
- 如果CatchParameter的BoundNames的任何元素也出现在Block的LexicallyDeclaredNames中，则是语法错误。

- 如果CatchParameter的BoundNames的任何元素也出现在Block的VarDeclaredNames中，则是语法错误。

> 注：在B.3.5中给出了这种生产的另一种静态语义。

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
3. 令 catchEnvRec 为 catchEnv的环境记录项.
4. 对于 CatchParameter 的每一个 BoundNames 的 argName，执行
1. 执行 ! catchEnvRec.CreateMutableBinding(argName, false).
5. 设置运行时执行上下文的词法环境 为 catchEnv.
6. 令 status 为通过将thrownValue和catchEnv作为参数对CatchParameter执行BindingInitialization的结果。
7. 若 status 是 abrupt completion，那么
  1. 设置运行时执行上下文的词法环境为 oldEnv.
  2. 返回 Completion(status).
8. 令 B 为 Block 的运算结果
9. 设置运行时执行上下文的词法环境 为 oldEnv.
10. 返回 Completion(B).

```
Catch : catch Block
```

1. 返回 evaluating Block 的结果

> 注：无论控制如何离开LexicalEnvironment，始终将其恢复为以前的状态。

### 13.15.8 运行时语义：Evaluation <div id="sec-try-statement-runtime-semantics-evaluation"></div>

```
TryStatement : try Block Catch
```

1. 令 B 为 Block 的运算结果
2. 若 B.[[Type]] 是 throw, 令 C 为带有参数 B 的 Catch 的 CatchClauseEvaluation[[Value]].
3. 否则， 令 C 为 B.
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
2. 若 B.[[Type]] 是 throw, 令 C 为带有参数 B 的 Catch 的 CatchClauseEvaluation[[Value]].
3. 否则，令 C 为 B.
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

> 注：运算DebuggerStatement可能会使实现在调试器下运行时导致断点。如果调试器不存在或未处于活动状态，则此语句无效。

1. 若实现定义的调试工具可用并且已启用，那么
1. 执行实现定义的调试操作。
2. 令result为实现定义的完成值。
2. 否则，
  1. 令 result 为 NormalCompletion(empty).
3. 返回 result.

```
ECMAScript语言：语句和声明
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,ECMAScript语言：语句和声明,statements-and-declaraions
```