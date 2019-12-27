# 15 ECMAScript语言：脚本和模块
## 15.1 脚本 <div id="sec-function-definitions"></div>

**语法**

```
Script :
ScriptBodyopt
ScriptBody :
StatementList[~Yield, ~Await, ~返回]
```

### 15.1.1 SS: Early Errors <div id="sec-scripts-static-semantics-early-errors"></div>

```
Script : ScriptBody
```

如果ScriptBody的LexicallyDeclaredNames包含任何重复的条目，这是一个语法错误。

如果ScriptBody的LexicallyDeclaredNames的任何元素也出现在ScriptBody的VarDeclaredNames中，这是一个语法错误。

```
ScriptBody : StatementList
```

如果StatementList包含super，则是语法错误，除非包含super的源代码是由直接eval处理的eval代码。在18.2.1.1中定义了直接求值的super额外早期错误规则。
如果StatementList包含了NewTarget，这是一个语法错误，除非包含NewTarget的源代码是由直接eval处理的eval代码。在18.2.1.1中定义了直接求值的super额外早期错误规则。
如果包含带有参数«»的重复声明列表为真，这是一个语法错误。
如果带有参数«»的StatementList的ContainsUndefinedBreakTarget为真，这是一个语法错误。
如果带有参数«»和«»的ContainsUndefinedContinueTarget of StatementList为真，则为语法错误。

### 15.1.2 SS: IsStrict <div id="sec-static-semantics-isstrict"></div>

```
ScriptBody : StatementList
```

1. 如果指令的声明列表的序言包含一个使用严格指令，返回true；否则,返回false。

### 15.1.3 SS: LexicallyDeclaredNames <div id="sec-scripts-static-semantics-lexicallydeclarednames"></div>

```
ScriptBody : StatementList
```

1. 返回StatementList的TopLevelLexicallyDeclaredNames

> 注：在Script的顶层，将函数声明视为var声明，而不是词汇声明。

### 15.1.4 SS: LexicallyScopedDeclarations <div id="sec-scripts-static-semantics-lexicallyscopeddeclarations"></div>

```
ScriptBody : StatementList
```

1. 返回StatementList的TopLevelLexicallyScopedDeclarations。

### 15.1.5 SS: VarDeclaredNames <div id="sec-scripts-static-semantics-vardeclarednames"></div>

```
ScriptBody : StatementList
```

1. 返回StatementList的TopLevelVarDeclaredNames 。

### 15.1.6 SS: VarScopedDeclarations <div id="sec-scripts-static-semantics-varscopeddeclarations"></div>

```
ScriptBody : StatementList
```

1. 返回StatementList的TopLevelVarScopedDeclarations 。

### 15.1.7 RS: Evaluation <div id="sec-script-semantics-runtime-semantics-evaluation"></div>

```
Script : [empty]
```

1. 返回 NormalCompletion(undefined).

### 15.1.8 脚本记录 <div id="sec-script-records"></div>

脚本记录封装了有关正在求值的脚本的信息。每个脚本记录都包含表36中列出的字段。

表格 36: Script Record Fields

| 字段名             | 值类型                           | 意义                                                     |
| ------------------ | -------------------------------- | -------------------------------------------------------- |
| [[Realm]]          | Realm Record \| undefined        | 在其中创建此脚本的作用域。undefined（如果尚未分配）。    |
| [[Environment]]    | Lexical Environment \| undefined | 包含此脚本的顶级绑定的词法环境。实例化脚本时设置此字段。 |
| [[ECMAScriptCode]] | a parse Node                     | 使用脚本作为目标符号解析此模块的源文本的结果。           |
| [[HostDefined]]    | Any, default value 是 undefined. | 保留给需要将其他信息与脚本关联的主机环境使用的字段。     |

### 15.1.9 ParseScript ( sourceText, realm, hostDefined ) <div id="sec-parse-script"></div>

具有参数sourceText，realm和hostDefined的抽象操作ParseScript会基于将sourceText解析为脚本的结果来创建脚本记录。 ParseScript执行以下步骤：

1. 断言: sourceText是ECMAScript源文本（请参见第10节）。
2. 使用Script作为目标符号来解析sourceText，并分析任何早期错误条件的解析结果。如果解析成功并且未发现早期错误，则将body作为结果解析树。否则，将body作为代表解析错误和/或早期错误的一个或多个SyntaxError或ReferenceError对象的列表。解析和早期错误检测可以以依赖于实现的方式进行交织。如果存在多个解析错误或早期错误，则列表中错误对象的数量和顺序与实现有关，但必须至少存在一个。
3. 若 body 是 errors 列表, 返回 body.
4. 返回 Script Record { [[Realm]]: realm, [[Environment]]: undefined, [[ECMAScriptCode]]: body,[[HostDefined]]: hostDefined }.

> 注：在运算ParseScript脚本源文本之前，实现可能会解析脚本源文本并针对早期错误条件对其进行分析。但是，任何错误的报告都必须推迟到该规范对该源文本实际执行ParseScript为止。

### 15.1.10 ScriptEvaluation ( scriptRecord ) <div id="sec-runtime-semantics-scriptevaluation"></div>

1. 令 globalEnv 为 scriptRecord.[[Realm]].[[GlobalEnv]].
2. 令 scriptCxt 为新的ECMAScript代码执行上下文。
3. 设置 scriptCxt 的 Function 为 null.
4. 设置 scriptCxt 的 Realm 为 scriptRecord.[[Realm]].
5. 设置 scriptCxt 的 ScriptOrModule 为 scriptRecord.
6. 设置 scriptCxt 的 VariableEnvironment 为 globalEnv.
7. 设置 scriptCxt 的 LexicalEnvironment 为 globalEnv.
8. 挂起当前正在运行的执行上下文
9. 将scriptCxt推入执行上下文堆栈；现在，scriptCxt是正在运行的执行上下文。
10. 令 scriptBody 为 scriptRecord.[[ECMAScriptCode]].
11. 令 result 为 GlobalDeclarationInstantiation(scriptBody, globalEnv).
12. 若 result.[[Type]] 是 normal，那么
    1. 设置 result 为运算scriptBody的结果。
13. 若 result.[[Type]] 是 normal，并且 result.[[Value]] 是 empty，那么
    1. 设置 result 为 NormalCompletion(undefined).
14. 挂起scriptCxt并将其从执行上下文堆栈中删除。
15. 断言: 执行上下文堆栈不为空。
16. 将现在位于执行上下文堆栈顶部的上下文恢复为运行中的执行上下文。
17. 返回 Completion(result).

### 15.1.11 RS: GlobalDeclarationInstantiation ( script, env ) <div id="sec-globaldeclarationinstantiation"></div>

> 注 1：建立用于运算脚本的执行上下文时，将在当前全局环境中实例化声明。代码中声明的每个全局绑定都被实例化。

GlobalDeclarationInstantiation使用参数脚本和env如下执行。脚本是为其建立执行上下文的ScriptBody。 env是要在其中创建绑定的全局词汇环境。

1. 令 envRec 为 env 的 EnvironmentRecord.
2. 断言: envRec 是 a global Environment Record.
3. 令 lexNames 为 script 的 LexicallyDeclaredNames
4. 令 varNames 为 script 的 VarDeclaredNames
5. 对于在lexNames中的每一个name，执行
     1. 若 envRec.HasVarDeclaration(name) 是 true, 抛出 SyntaxError 异常
     2. 若 envRec.HasLexicalDeclaration(name) 是 true, 抛出 SyntaxError 异常
     3. 令 hasRestrictedGlobal 为 ? envRec.HasRestrictedGlobalProperty(name).
     4. 若 hasRestrictedGlobal 是 true, 抛出 SyntaxError 异常
6. 对于在varNames中的每一个name，执行
    1. 若 envRec.HasLexicalDeclaration(name) 是 true, 抛出 SyntaxError 异常
7. 令 varDeclarations 为 script 的 VarScopedDeclarations
8. 令 functionsToInitialize 为新的空列表.
9. 令 declaredFunctionNames 为新的空列表.
10. 对于varDeclarations中的每个d，以反向列表顺序，执行
     1. 若 d 是 不是 VariableDeclaration 或 ForBinding 或 BindingIdentifier，那么
          1. 断言: d 是 FunctionDeclaration, GeneratorDeclaration, AsyncFunctionDeclaration, 或 AsyncGeneratorDeclaration.
          2. 注: 如果有多个相同名称的函数声明，则使用最后一个声明。
          3. 令 fn 为 d 的BoundNames的唯一元素。
          4. 若 fn 不是 declaredFunctionNames 的 element，那么
               1. 令 fnDefinable 为 ? envRec.CanDeclareGlobalFunction(fn).
               2. 若 fnDefinable 是 false, 抛出 TypeError 异常
               3. 将 fn 添加到 declaredFunctionNames.
               4. 插入d作为functionsToInitialize的第一个元素。
11. 令 declaredVarNames 为新的空列表.
12. 对于在varDeclarations中的每一个d，执行
    1. 若 d 是 VariableDeclaration, ForBinding, 或 BindingIdentifier，那么
        1. 对于在 d 的 BoundNames 中的每一个字符串 vn，执行
    
            1. 若 vn 不是 declaredFunctionNames 的元素，那么
              1. 令 vnDefinable 为 ? envRec.CanDeclareGlobalVar(vn).
              2. 若 vnDefinable 是 false, 抛出 TypeError 异常
              3. 若 vn 不是 an declaredVarNames 的元素，那么
                    1. 将 vn 添加到 declaredVarNames.
13. 注: 如果全局对象是普通对象，则在此算法步骤之后不会发生异常终止。但是，如果全局对象是Proxy外来对象，则在以下某些步骤中，它可能会表现出导致异常终止的行为。
14. 注: 附件B.3.3.2在这一点上增加了其他步骤。
15. 令 lexDeclarations 为 script 的 LexicallyScopedDeclarations
16. 对于在lexDeclarations中的每一个元素 d，执行
     1. 注: 词汇声明的名称仅在此处实例化，而不初始化。
     2. 对于在 d 的 BoundNames 的每一个元素 d，执行
         1. 若 d 的 IsConstantDeclaration 是 true，那么
            1. 执行 ? envRec.CreateImmutableBinding(dn, true).
         2. 否则，
            1. 执行 ? envRec.CreateMutableBinding(dn, false).
19. 对于在functionsToInitialize中的每一个解析节点 f，执行
    1. 令 fn 为 f 的 BoundNames 的唯一元素。
    2. 令 fo 为使用参数 env 为 f 执行InstantiateFunctionObject的结果。
    3. 执行 ? envRec.CreateGlobalFunctionBinding(fn, fo, false).
18. 对于clarifiedVarNames中的每个String vn，按列表顺序，执行
     1. 执行 ? envRec.CreateGlobalVarBinding(vn, false).
19. 返回 NormalCompletion(empty).

> 注 2：15.1.1中指定的早期错误可防止 function/ var声明与let / const / class声明之间的名称冲突，以及防止为单个脚本中包含的声明重新声明let / const / class绑定。但是，在GlobalDeclarationInstantiation期间将跨越多个脚本的此类冲突和重新声明检测为运行时错误。如果检测到任何此类错误，则不会实例化脚本的绑定。但是，如果使用代理外来对象定义了全局对象，则针对冲突声明的运行时测试可能不可靠，从而导致突然完成，并且某些全局声明未实例化。如果发生这种情况，则不会执行脚本的代码。
>
> 与显式var或function声明不同，直接在全局对象上创建的属性会导致全局绑定，而这些绑定可能会被let / const / class声明所遮盖。

### 15.1.12 RS: ScriptEvaluationJob ( sourceText, hostDefined ) <div id="sec-scriptevaluationjob"></div>

具有参数sourceText和hostDefined的任务ScriptEvaluationJob解析、验证和运算sourceText作为脚本

1. 断言: sourceText 是ECMAScript源文本 (见第10条).
2. 令 realm 为当前 Realm Record.
3. 令 s 为 ParseScript(sourceText, realm, hostDefined).
4. 若 s 是 errors 的 列表，那么
5. a. 执行 HostReportErrors(s).
6. b. 返回 NormalCompletion(undefined).
  7. 返回 ? ScriptEvaluation(s).

## 15.2 模块 <div id="sec-modules"></div>

**语法**

```
Module :
ModuleBodyopt
ModuleBody :
ModuleItemList
ModuleItemList :
ModuleItem
ModuleItemList ModuleItem
ModuleItem :
ImportDeclaration
ExportDeclaration
StatementListItem[~Yield, ~Await, ~返回]
```



### 15.2.1 模块语义 <div id="sec-module-semantics"></div>
#### 15.2.1.1 SS: Early Errors <div id="sec-module-semantics-static-semantics-early-errors"></div>

```
ModuleBody : ModuleItemList
```

It 是 a Syntax Error 若 the LexicallyDeclaredNames of ModuleItemList contains any duplicate entries.
It 是 a Syntax Error 若 any element of the LexicallyDeclaredNames of ModuleItemList also occurs in ModuleItemList 的 VarDeclaredNames
It 是 a Syntax Error 若 the ExportedNames of ModuleItemList contains any duplicate entries.
It 是 a Syntax Error 若 any element of the ExportedBindings of ModuleItemList does not also occur in either ModuleItemList 的 VarDeclaredNames or ModuleItemList 的 LexicallyDeclaredNames
It 是 a Syntax Error 若 ModuleItemList Contains super.
It 是 a Syntax Error 若 ModuleItemList Contains NewTarget.
It 是 a Syntax Error 若 ContainsDuplicateLabels of ModuleItemList with argument « » 是 true.
It 是 a Syntax Error 若 ContainsUndefinedBreakTarget of ModuleItemList with argument « » 是 true.
It 是 a Syntax Error 若 ContainsUndefinedContinueTarget of ModuleItemList with arguments « » and « » 是 true.

> 注 The duplicate ExportedNames rule implies that multiple export default ExportDeclaration items within a ModuleBody 是 a Syntax Error. Additional error conditions relating to conflicting or duplicate declarations are checked during module linking prior to evaluation of a Module. 若 any such errors are detected the Module 不是 evaluated.

#### 15.2.1.2 SS: ContainsDuplicateLabels <div id="sec-module-semantics-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 hasDuplicates 为 ContainsDuplicateLabels of ModuleItemList with argument labelSet.

2. 若 hasDuplicates 是 true, return true.

3. 返回 ContainsDuplicateLabels of ModuleItem with argument labelSet.

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. 返回 false.

#### 15.2.1.3 SS: ContainsUndefinedBreakTarget <div id="sec-module-semantics-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 hasUndefinedLabels 为 ContainsUndefinedBreakTarget of ModuleItemList with argument labelSet.

2. 若 hasUndefinedLabels 是 true, return true.

3. 返回 ContainsUndefinedBreakTarget of ModuleItem with argument labelSet.

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. 返回 false.

#### 15.2.1.4 SS: ContainsUndefinedContinueTarget <div id="sec-module-semantics-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 hasUndefinedLabels 为 ContainsUndefinedContinueTarget of ModuleItemList with arguments iterationSet and « ».

2. 若 hasUndefinedLabels 是 true, return true.

3. 返回 ContainsUndefinedContinueTarget of ModuleItem with arguments iterationSet and « ».

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. 返回 false.

#### 15.2.1.5 SS: ExportedBindings <div id="sec-module-semantics-static-semantics-exportedbindings"></div>

> 注 ExportedBindings are the locally bound names that are explicitly associated with a Module 的 ExportedNames.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 names 为 ExportedBindings of ModuleItemList

2. Append to names the elements of ModuleItem 的 ExportedBindings

3. 返回 names

   ```
   ModuleItem :
   ImportDeclaration
   StatementListItem
   ```

   1. 返回新的空列表.

#### 15.2.1.6 SS: ExportedNames <div id="sec-module-semantics-static-semantics-exportednames"></div>

> 注 ExportedNames are the externally visible names that a Module explicitly maps to one of its local name bindings.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 names 为 ModuleItemList 的 ExportedNames
2. Append to names the elements of ModuleItem 的 ExportedNames
3. 返回 names.

```
ModuleItem : ExportDeclaration
```

1. 返回 ExportDeclaration 的 ExportedNames

   ```
   ModuleItem :
   ImportDeclaration
   StatementListItem
   ```

   1. 返回新的空列表.

#### 15.2.1.7 SS: ExportEntries <div id="sec-module-semantics-static-semantics-exportentries"></div>

```
Module : [empty]
```

1. 返回新的空列表.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 entries 为 ModuleItemList 的 ExportEntries

2. Append to entries the elements of ModuleItem 的 ExportEntries

3. 返回 entries.

   ```
   ModuleItem :
   ImportDeclaration
   StatementListItem
   ```

   1. 返回新的空列表.

#### 15.2.1.8 SS: ImportEntries <div id="sec-module-semantics-static-semantics-importentries"></div>

```
Module : [empty]
```

1. 返回新的空列表.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 entries 为 ModuleItemList 的 ImportEntries

2. Append to entries the elements of ModuleItem 的 ImportEntries

3. 返回 entries

   ```
   ModuleItem :
   ExportDeclaration
   StatementListItem
   ```

   1. 返回新的空列表

#### 15.2.1.9 SS: ImportedLocalNames ( importEntries ) <div id="sec-importedlocalnames"></div>

The abstract operation ImportedLocalNames with argument importEntries creates a List of all of the local name bindings defined by a List of ImportEntry Records (see Table 42). ImportedLocalNames performs the following steps:

1. 令 localNames 为新的空列表.
2. 对于在importEntries中的每一个ImportEntry Record i，执行
a. 将 i.[[LocalName]] 添加到 localNames.
3. 返回 localNames.

#### 15.2.1.10 SS: ModuleRequests <div id="sec-module-semantics-static-semantics-modulerequests"></div>

```
Module : [empty]
```

1. 返回新的空列表.

```
ModuleItemList : ModuleItem
```

1. 返回 ModuleItem 的 ModuleRequests

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 moduleNames 为 ModuleItemList 的 ModuleRequests
2. 令 additionalNames 为 ModuleItem 的 ModuleRequests
3. Append to moduleNames each element of additionalNames that 不是 already an moduleNames 的 element
4. 返回 moduleNames.

```
ModuleItem : StatementListItem
```

1. 返回新的空列表.

#### 15.2.1.11 SS: LexicallyDeclaredNames <div id="sec-module-semantics-static-semantics-lexicallydeclarednames"></div>

> 注 1 The LexicallyDeclaredNames of a Module includes the names of all of its imported bindings.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 names 为 ModuleItemList 的 LexicallyDeclaredNames
2. Append to names the elements of ModuleItem 的 LexicallyDeclaredNames
3. 返回 names.

```
ModuleItem : ImportDeclaration
```

1. 返回 the BoundNames of ImportDeclaration

```
ModuleItem : ExportDeclaration
```

1. 若 ExportDeclaration 是 export VariableStatement, return新的空列表.
2. 返回 ExportDeclaration 的 BoundNames

```
ModuleItem : StatementListItem
```

1. 返回 StatementListItem 的 LexicallyDeclaredNames

> 注 2 At the top level of a Module, function declarations are treated like lexical declarations rather than like var declarations.

#### 15.2.1.12 SS: LexicallyScopedDeclarations <div id="sec-module-semantics-static-semantics-lexicallyscopeddeclarations"></div>

```
Module : [empty]
```

1. 返回新的空列表.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 declarations 为 ModuleItemList 的 LexicallyScopedDeclarations
2. Append to declarations the elements of ModuleItem 的 LexicallyScopedDeclarations
3. 返回 declarations.

```
ModuleItem : ImportDeclaration
```

1. 返回新的空列表

#### 15.2.1.13 SS: VarDeclaredNames <div id="sec-module-semantics-static-semantics-vardeclarednames"></div>

```
Module : [empty]
```

1. 返回新的空列表

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 names 为 ModuleItemList 的 VarDeclaredNames
2. Append to names the elements of ModuleItem 的 VarDeclaredNames
3. 返回 names.

```
ModuleItem : ImportDeclaration
```

1. 返回新的空列表.

```
ModuleItem : ExportDeclaration
```

1. 若 ExportDeclaration 是 export VariableStatement, return ExportDeclaration 的 BoundNames
2. 返回新的空列表.

#### 15.2.1.14 SS: VarScopedDeclarations <div id="sec-module-semantics-static-semantics-varscopeddeclarations"></div>

```
Module : [empty]
```

1. 返回新的空列表

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 declarations 为 ModuleItemList 的 VarScopedDeclarations
2. Append to declarations the elements of ModuleItem 的 VarScopedDeclarations
3. 返回 declarations.

```
ModuleItem : ImportDeclaration
```

1. 返回新的空列表.

```
ModuleItem : ExportDeclaration
```

1. 若 ExportDeclaration 是 export VariableStatement, return VariableStatement 的 VarScopedDeclarations
2. 返回新的空列表.

#### 15.2.1.15 抽象模块记录 <div id="sec-abstract-module-records"></div>

A Module Record encapsulates structural information about the imports and exports of a single module. This information 是 used to link the imports and exports of sets of connected modules. A Module Record includes four fields that are only used when evaluating a module

For specification purposes Module Record values are values of the Record specification type and can be thought of as existing in a simple object-oriented hierarchy where Module Record 是 an abstract class with both abstract and concrete subclasses. This specification defines the abstract subclass named Cyclic Module Record and its concrete subclass named Source Text Module Record. Other specifications and implementations may define additional Module Record subclasses corresponding to alternative module definition facilities that they defined.

Module Record defines the fields listed in Table 37. All Module Definition subclasses include at least those fields. Module Record also defines the abstract method list in Table 38. All Module definition subclasses must provide concrete implementations of these abstract methods.

Table 37: Module Record Fields

| Field Name      | Value Type                                                   | Meaning                                                      |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[Realm]]       | [Realm Record](#realm-record) \| undefined                   | The [Realm](#realm) within which this module was created. undefined 若 not yet assigned. |
| [[Environment]] | [Lexical Environment](#sec-lexical-environments) \| undefined | The Lexical Environment containing the top level bindings for this module. This field 是 set when the module 是 instantiated. |
| [[Namespace]]   | Object \| undefined                                          | The Module Namespace Object ([26.3](#sec-module-namespace-objects)) 若 one has been created for this module. Otherwise undefined. |
| [[HostDefined]] | Any, default value 是 undefined.                             | Field reserved for use by host environments that need to associate additional information with a module. |

Table 38: Abstract Methods of Module Records

| Method                                               | Purpose                                                      |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| GetExportedNames(exportStarSet)                      | 返回 a list of all names that are either directly or indirectly exported from this module. |
| ResolveExport(exportName, resolveSet, exportStarSet) | 返回 the binding of a name exported by this module. Bindings are represented by a ResolvedBinding Record, of the form { [[Module]]: Module Record, [[BindingName]]: String }. 返回 null 若 the name cannot be resolved, or "ambiguous" 若 multiple bindings were found.<br />This operation must be idempotent 若 it completes normally. Each time it 是 called with a specific exportName, resolveSet pair as arguments it must return the same result. |
| Instantiate()                                        | Prepare the module for evaluation by transitively resolving all module dependencies and creating a module Environment Record. |
| Evaluate()                                           | 若 this module has already been evaluated successfully, return undefined; 若 it has already been evaluated unsuccessfully, throw the exception that was produced. Otherwise, transitively evaluate all module dependencies of this module and then evaluate this module. <br />Instantiate must have completed successfully prior to invoking this method. |



#### 15.2.1.16 循环模块记录 <div id="sec-cyclic-module-records"></div>

A Cyclic Module Record 是 used to represent information about a module that can participate in dependency cycles with other modules that are subclasses of the Cyclic Module Record type. Module Records that are not subclasses of the Cyclic Module Record type must not participate in dependency cycles with Source Text Module Records

In addition to the fields defined in Table 37 Cyclic Module Records have the additional fields listed in Table 39

Table 39: Additional Fields of Cyclic Module Records

| Field Name           | Value Type                      | Meaning                                                      |
| -------------------- | ------------------------------- | ------------------------------------------------------------ |
| [[Status]]           | String                          | Initially "uninstantiated". Transitions to "instantiating", "instantiated", "evaluating", "evaluated" (in that order) as the module progresses throughout its lifecycle. |
| [[EvaluationError]]  | An abrupt completion\|undefined | A completion of type throw representing the exception that occurred during evaluation. undefined 若 no exception occurred or 若 [[Status]] 不是 "evaluated". |
| [[DFSIndex]]         | Integer\|undefined              | Auxiliary field used during Instantiate and Evaluate only. 若 [[Status]] 是 "instantiating" or "evaluating", this nonnegative number records the point at which the module was first visited during the ongoing depth-first traversal of the dependency graph. |
| [[DFSAncestorIndex]] | Integer\|undefined              | Auxiliary field used during Instantiate and Evaluate only. 若 [[Status]] 是 "instantiating" or "evaluating", this 是 either the module的 own [[DFSIndex]] or that of an "earlier" module in the same strongly connected component |
| [[RequestedModules]] | List of String                  | A List of all the ModuleSpecifier strings used by the module represented by this record to request the importation of a module. The List 是 source code occurrence ordered. |

In addition to the methods defined in Table 38 Cyclic Module Records have the additional methods listed in Table 40

Table 40: Additional Abstract Methods of Cyclic Module Records

| Method                  | Method                                                       |
| ----------------------- | ------------------------------------------------------------ |
| InitializeEnvironment() | Initialize the Lexical Environment of the module, including resolving all imported bindings. |
| ExecuteModule()         | Initialize the execution context of the module and evaluate the module的 code within it. |

##### 15.2.1.16.1 Instantiate ( ) 具体方法 <div id="sec-moduledeclarationinstantiation"></div>

The Instantiate concrete method of a Cyclic Module Record implements the corresponding Module Record abstract method.

On success, Instantiate transitions this module的 [[Status]] from "uninstantiated" to "instantiated". On failure, an exception 是 thrown and this module的 [[Status]] remains "uninstantiated"

This abstract method performs the following steps (most of the work 是 done by the auxiliary function InnerModuleInstantiation):

1. 令 module 为 this Cyclic Module Record.
2. 断言: module.[[Status]] 不是 "instantiating" or "evaluating".
3. 令 stack 为新的空列表.
4. 令 result 为 InnerModuleInstantiation(module, stack, 0).
5. 若 result 是 an abrupt completion，那么
a. 对于在stack中的每一个module m，执行
i. 断言: m.[[Status]] 是 "instantiating".
ii. 设置 m.[[Status]] 为 "uninstantiated".
iii. 设置 m.[[Environment]] 为 undefined.
iv. 设置 m.[[DFSIndex]] 为 undefined.
v. 设置 m.[[DFSAncestorIndex]] 为 undefined.
b. 断言: module.[[Status]] 是 "uninstantiated".
c. 返回 result.
6. 断言: module.[[Status]] 是 "instantiated" or "evaluated".
7. 断言: stack 是 empty.
8. 返回 undefined.

###### 15.2.1.16.1.1 InnerModuleInstantiation ( module, stack, index ) <div id="sec-innermoduleinstantiation"></div>

The InnerModuleInstantiation abstract operation 是 used by Instantiate to 执行 the actual instantiation process for the Cyclic Module Record module, as well as recursively on all other modules in the dependency graph. The stack and index parameters, as well as a module的 [[DFSIndex]] and [[DFSAncestorIndex]] fields, keep track of the depth-first search (DFS) traversal. In particular, [[DFSAncestorIndex]] 是 used to discover strongly connected components (SCCs), such that all modules in an SCC transition to "instantiated" together.

This abstract operation performs the following steps:

1. 若 module 不是 a Cyclic Module Record，那么
a. 执行 ? module.Instantiate().
b. 返回 index.
2. 若 module.[[Status]] 是 "instantiating", "instantiated", or "evaluated"，那么
a. 返回 index.
3. 断言: module.[[Status]] 是 "uninstantiated".
4. 设置 module.[[Status]] 为 "instantiating".
5. 设置 module.[[DFSIndex]] 为 index.
6. 设置 module.[[DFSAncestorIndex]] 为 index.
7. Increase index by 1.
8. 将 module 添加到 stack.
9. For each String required that 是 an module 的 element[[RequestedModules]], do
a. 令 requiredModule 为 ? HostResolveImportedModule(module, required).
b. 设置 index 为 ? InnerModuleInstantiation(requiredModule, stack, index).
c. 断言: requiredModule.[[Status]] 是 either "instantiating", "instantiated", or "evaluated".
d. 断言: requiredModule.[[Status]] 是 "instantiating" 若 and only 若 requiredModule 是 in stack.
e. 若 requiredModule.[[Status]] 是 "instantiating"，那么
i. 断言: requiredModule 是 a Cyclic Module Record.
ii. 设置 module.[[DFSAncestorIndex]] 为 min(module.[[DFSAncestorIndex]], requiredModule.
[[DFSAncestorIndex]]).
10. 执行 ? module.InitializeEnvironment().
11. 断言: module occurs exactly once in stack.
12. 断言: module.[[DFSAncestorIndex]] 是 less than or equal to module.[[DFSIndex]].
13. 若 module.[[DFSAncestorIndex]] equals module.[[DFSIndex]]，那么
a. 令 done 为 false.
b. Repeat, while done 是 false,
i. 令 requiredModule 为 the last element in stack.
ii. Remove the last stack 的 element
iii. 设置 requiredModule.[[Status]] 为 "instantiated".
iv. 若 requiredModule and module are the same Module Record, 设置 done 为 true.
14. 返回 index

##### 15.2.1.16.2 Evaluate ( ) 具体方法 <div id="sec-moduleevaluation"></div>

The Evaluate concrete method of a Cyclic Module Record implements the corresponding Module Record abstract method.

Evaluate transitions this module的 [[Status]] from "instantiated" to "evaluated".
若 execution results in an exception, that exception 是 recorded in the [[EvaluationError]] field and rethrown by future Evaluate 的 invocations

This abstract method performs the following steps (most of the work 是 done by the auxiliary function
InnerModuleEvaluation):

1. 令 module 为 this Cyclic Module Record.
2. 断言: module.[[Status]] 是 "instantiated" or "evaluated".
3. 令 stack 为新的空列表.
4. 令 result 为 InnerModuleEvaluation(module, stack, 0).
5. 若 result 是 an abrupt completion，那么
a. 对于在stack中的每一个module m，执行
i. 断言: m.[[Status]] 是 "evaluating".
ii. 设置 m.[[Status]] 为 "evaluated".
iii. 设置 m.[[EvaluationError]] 为 result.
b. 断言: module.[[Status]] 是 "evaluated" and module.[[EvaluationError]] 是 result.
c. 返回 result.
6. 断言: module.[[Status]] 是 "evaluated" and module.[[EvaluationError]] 是 undefined.
7. 断言: stack 是 empty.
8. 返回 undefined.

###### 15.2.1.16.2.1 InnerModuleEvaluation ( module, stack, index ) <div id="sec-innermoduleevaluation"></div>

The InnerModuleEvaluation abstract operation 是 used by Evaluate to 执行 the actual evaluation process for the Source Text Module Record module, as well as recursively on all other modules in the dependency graph. The stack and index parameters, as well as module的 [[DFSIndex]] and [[DFSAncestoreIndex]] fields, are used the same way as in InnerModuleInstantiation

This abstract operation performs the following steps:

1. 若 module 不是 a Cyclic Module Record，那么
a. 执行 ? module.Evaluate().
b. 返回 index.
2. 若 module.[[Status]] 是 "evaluated"，那么
a. 若 module.[[EvaluationError]] 是 undefined, return index.
b. Otherwise return module.[[EvaluationError]].
3. 若 module.[[Status]] 是 "evaluating", return index.
4. 断言: module.[[Status]] 是 "instantiated".
5. 设置 module.[[Status]] 为 "evaluating".
6. 设置 module.[[DFSIndex]] 为 index.
7. 设置 module.[[DFSAncestorIndex]] 为 index.
8. Increase index by 1.
9. 将 module 添加到 stack.
10. For each String required that 是 an module 的 element[[RequestedModules]], do
a. 令 requiredModule 为 ! HostResolveImportedModule(module, required).
b. 注: Instantiate must be completed successfully prior to invoking this method, so every requested module
is guaranteed to resolve successfully.
c. 设置 index 为 ? InnerModuleEvaluation(requiredModule, stack, index).
11. d. 断言: requiredModule.[[Status]] 是 either "evaluating" or "evaluated".
    e. 断言: requiredModule.[[Status]] 是 "evaluating" 若 and only 若 requiredModule 是 in stack.
    f. 若 requiredModule.[[Status]] 是 "evaluating"，那么
    i. 断言: requiredModule 是 a Cyclic Module Record.
    ii. 设置 module.[[DFSAncestorIndex]] 为 min(module.[[DFSAncestorIndex]], requiredModule.
    [[DFSAncestorIndex]]).
    11. 执行 ? module.ExecuteModule().
    12. 断言: module occurs exactly once in stack.
    13. 断言: module.[[DFSAncestorIndex]] 是 less than or equal to module.[[DFSIndex]].
    14. 若 module.[[DFSAncestorIndex]] equals module.[[DFSIndex]]，那么
    a. 令 done 为 false.
    b. Repeat, while done 是 false,
    i. 令 requiredModule 为 the last element in stack.
    ii. Remove the last stack 的 element
    iii. 设置 requiredModule.[[Status]] 为 "evaluated".
    iv. 若 requiredModule and module are the same Module Record, 设置 done 为 true.
    15. 返回 index

##### 15.2.1.16.3 循环模块记录示例图 <div id="sec-example-cyclic-module-record-graphs"></div>

This non-normative section gives a series of examples of the instantiation and evaluation of a few common module graphs, with a specific focus on how errors can occur.

First consider the following simple module graph:

Figure 2: A simple module graph

Let的 first assume that there are no error conditions. When a host first calls A.Instantiate(), this will complete successfully by assumption, and recursively instantiate modules B and C as well, such that A.[[Status]] = B.[[Status]] = C.[[Status]] = "instantiated". This preparatory step can be performed at any time. Later, when the host 是 ready to incur any possible side effects of the modules, it can call A.Evaluate(), which will complete successfully (again by assumption), recursively having evaluated first C and then B. Each module的 [[Status]] at this point will be "evaluated".

Consider then cases involving instantiation errors. 若 InnerModuleInstantiation of C succeeds but, thereafter, fails for B, for example because it imports something that C does not provide，那么 the original A.Instantiate() will fail, and both A and B的 [[Status]] remain "uninstantiated". C的 [[Status]] has become "instantiated", though.

Finally, consider a case involving evaluation errors. 若 InnerModuleEvaluation of C succeeds but, thereafter, fails for B, for example because B contains code that throws an exception，那么 the original A.Evaluate() will fail. The resulting exception will be recorded in both A and B的 [[EvaluationError]] fields, and their [[Status]] will become "evaluated".C will also become "evaluated" but, in contrast to A and B, will remain without an [[EvaluationError]], as it successfully completed evaluation. Storing the exception ensures that any time a host tries to reuse A or B by calling their Evaluate() method, it will encounter the same 异常 (Hosts are not required to reuse Cyclic Module Records; similarly, hosts are not required to expose the exception objects thrown by these methods. However, the specification enables such uses.)

The difference here between instantiation and evaluation errors 是 due to how evaluation must be only performed once, as it can cause side effects; it 是 thus important to remember whether evaluation has already been performed, even 若 unsuccessfully. (In the error case, it makes sense to also remember the exception because otherwise subsequent Evaluate() calls would have to synthesize a new one.) Instantiation, on the other hand, 是 side-effect-free, and thus even 若 it fails, it can be retried at a later time with no issues.

Now consider a different type of error condition:

Figure 3: A module graph with an unresolvable module

In this scenario, module A declares a dependency on some other module, but no Module Record exists for that module, i.e. HostResolveImportedModule throws an exception when asked for it. This could occur for a reasons 的 variety such as the corresponding resource not existing, or the resource existing but ParseModule throwing an exception when trying to parse the resulting source text. Hosts can choose to expose the cause of failure via the exception they throw from HostResolveImportedModule. In any case, this exception causes an instantiation failure, which as before results in A的 [[Status]] remaining "uninstantiated".

Lastly, consider a module graph with a cycle:

Figure 4: A cyclic module graph

Here we assume that the entry point 是 module A, so that the host proceeds by calling A.Instantiate(), which performs InnerModuleInstantiation on A. This in turn calls InnerModuleInstantiation on B. Because of the cycle, this again triggers InnerModuleInstantiation on A, but at this point it 是 a no-op since A.[[Status]] 是 already "instantiating". B. [[Status]] itself remains "instantiating" when control gets back to A and InnerModuleInstantiation 是 triggered on C. After this returns with C.[[Status]] being "instantiated" , both A and B transition from "instantiating" to "instantiated" together; this 是 by design, since they form a strongly connected component.

An analogous story occurs for the evaluation phase of a cyclic module graph, in the success case.

Now consider a case where A has an instantiation error; for example, it tries to import a binding from C that does not exist. In that case, the above steps still occur, including the early return from the second call to InnerModuleInstantiation on A. However, once we unwind back to the original InnerModuleInstantiation on A, it fails during InitializeEnvironment, namely right after C.ResolveExport(). The thrown SyntaxError exception propagates up to A.Instantiate, which resets all modules that are currently on its stack (these are always exactly the modules that are still "instantiating"). Hence both A and B become "uninstantiated". 注 that C 是 left as "instantiated".

Finally, consider a case where A has an evaluation error; for example, its source code throws an 异常 In that case, the evaluation-time analog of the above steps still occurs, including the early return from the second call to InnerModuleEvaluation on A. However, once we unwind back to the original InnerModuleEvaluation on A, it fails by assumption. The exception thrown propagates up to A.Evaluate(), which records the error in all modules that are currently on its stack (i.e., the modules that are still "evaluating"). Hence both A and B become "evaluated" and the exception 是 recorded in both A and B的 [[EvaluationError]] fields, while C 是 left as "evaluated" with no [[EvaluationError]].

#### 15.2.1.17 源文本模块记录 <div id="sec-source-text-module-records"></div>

A Source Text Module Record 是 used to represent information about a module that was defined from ECMAScript source text (10) that was parsed using the goal symbol Module. Its fields contain digested information about the names that are imported by the module and its concrete methods use this digest to link, instantiate, and evaluate the module.

A Source Text Module Record can exist in a module graph with other subclasses of the abstract Module Record type, and can participate in cycles with other subclasses of the Cyclic Module Record type.

In addition to the fields defined in Table 39, Source Text Module Records have the additional fields listed in Table 41. Each of these fields 是 initially set in ParseModule.

Table 41: Additional Fields of Source Text Module Records

| Field Name                | Value Type                                                   | Meaning                                                      |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[ECMAScriptCode]]        | a Parse Node                                                 | The result of parsing the source text of this module using Module as the goal symbol。 |
| [[ImportEntries]]         | [List](#sec-list-and-record-specification-type) of ImportEntry Records | A [List](#sec-list-and-record-specification-type) of ImportEntry records derived from the code of this module. |
| [[LocalExportEntries]]    | [List](#sec-list-and-record-specification-type) of ExportEntry Records | A [List](#sec-list-and-record-specification-type) of ExportEntry records derived from the code of this module that correspond to declarations that occur within the module. |
| [[IndirectExportEntries]] | [List](#sec-list-and-record-specification-type) of ExportEntry Records | A [List](#sec-list-and-record-specification-type) of ExportEntry records derived from the code of this module that correspond to reexported imports that occur within the module. |
| [[StarExportEntries]]     | [List](#sec-list-and-record-specification-type) of ExportEntry Records | A [List](#sec-list-and-record-specification-type) of ExportEntry records derived from the code of this module that correspond to export * declarations that occur within the module. |

An ImportEntry Record 是 a Record that digests information about a single declarative import. Each ImportEntry Record has the fields defined in Table 42:

Table 42: ImportEntry Record Fields

| Field Name        | Value Type | Meaning                                                      |
| ----------------- | ---------- | ------------------------------------------------------------ |
| [[ModuleRequest]] | String     | String value of the [ModuleSpecifier](#prod-ModuleSpecifier) of the [ImportDeclaration](#prod-ImportDeclaration). |
| [[ImportName]]    | String     | The name under which the desired binding 是 exported by the module identified by [[ModuleRequest]]. The value `"*"` indicates that the import request 是 for the target module的 namespace object. |
| [[LocalName]]     | String     | The name that 是 used to locally access the imported value from within the importing module. |

>注 1 Table 43 gives examples of ImportEntry records fields used to represent the syntactic import forms:
>
>| Import Statement Form import语句格式 | [[ModuleRequest]]                                            | [[ImportName]] | [[LocalName]] |
>| ------------------------------------ | ------------------------------------------------------------ | -------------- | ------------- |
>| `import v from "mod";`               | `"mod"`                                                      | `"default"`    | `"v"`         |
>| `import * as ns from "mod";`         | `"mod"`                                                      | `"*"`          | `"ns"`        |
>| `import {x} from "mod";`             | `"mod"`                                                      | `"x"`          | `"x"`         |
>| `import {x as v} from "mod";`        | `"mod"`                                                      | `"x"`          | `"v"`         |
>| `import "mod";`                      | An ImportEntry [Record](#sec-list-and-record-specification-type) 不是 created. |                |               |

An ExportEntry Record 是 a Record that digests information about a single declarative export. Each ExportEntry Record has the fields defined in Table 44:

| Field Name        | Value Type     | Meaning                                                      |
| ----------------- | -------------- | ------------------------------------------------------------ |
| [[ExportName]]    | String         | The name used to export this binding by this module.         |
| [[ModuleRequest]] | String \| null | The String value of the [ModuleSpecifier](#prod-ModuleSpecifier) of the [ExportDeclaration](#prod-ExportDeclaration). null 若 the [ExportDeclaration](#prod-ExportDeclaration) does not have a [ModuleSpecifier](#prod-ModuleSpecifier). |
| [[ImportName]]    | String \| null | The name under which the desired binding 是 exported by the module identified by [[ModuleRequest]]. null 若 the [ExportDeclaration](#prod-ExportDeclaration) does not have a [ModuleSpecifier](#prod-ModuleSpecifier). `"*"` indicates that the export request 是 for all exported bindings. |
| [[LocalName]]     | String \| null | The name that 是 used to locally access the exported value from within the importing module. null 若 the exported value 不是 locally accessible from within the module. |

>注 2 Table 45 gives examples of the ExportEntry record fields used to represent the syntactic export forms:
>
>Table 45 (Informative): Export Forms Mappings to ExportEntry Records
>
>| Export Statement Form           | [[ExportName]] | [[ModuleRequest]] | [[ImportName]] | [[LocalName]] |
>| ------------------------------- | -------------- | ----------------- | -------------- | ------------- |
>| `export var v;`                 | `"v"`          | null              | null           | `"v"`         |
>| `export default function f(){}` | `"default"`    | null              | null           | `"f"`         |
>| `export default function(){}`   | `"default"`    | null              | null           | `"*default*"` |
>| `export default 42;`            | `"default"`    | null              | null           | `"*default*"` |
>| `export {x};`                   | `"x"`          | null              | null           | `"x"`         |
>| `export {v as x};`              | `"x"`          | null              | null           | `"v"`         |
>| `export {x} from "mod";`        | `"x"`          | `"mod"`           | `"x"`          | null          |
>| `export {v as x} from "mod";`   | `"x"`          | `"mod"`           | `"v"`          | null          |
>| `export * from "mod";`          | null           | `"mod"`           | `"*"`          | null          |

The following definitions specify the required concrete methods and other abstract operations for Source Text Module Records

##### 15.2.1.17.1 ParseModule ( sourceText, realm, hostDefined ) <div id="sec-parsemodule"></div>

The abstract operation ParseModule with arguments sourceText, realm, and hostDefined creates a Source Text Module Record based upon the result of parsing sourceText as a Module. ParseModule performs the following steps:

1. 断言: sourceText 是 an ECMAScript source text (see clause 10).
2. Parse sourceText using Module as the goal symbol and analyse the parse result for any Early Error conditions. 若 the
parse was successful and no early errors were found, 令 body 为 the resulting parse tree. Otherwise, 令 body 为 a
List of one or more SyntaxError or ReferenceError objects representing the parsing errors and/or early errors.
Parsing and early error detection may be interweaved in an implementation-dependent manner. 若 more than one
parsing error or early error 是 present, the number and ordering of error objects in the list 是 implementationdependent, but at least one must be present.
3. 若 body 是 a errors 的 List return body.
4. 令 requestedModules 为 body 的 ModuleRequests
5. 令 importEntries 为 body 的 ImportEntries
6. 令 importedBoundNames 为 ImportedLocalNames(importEntries).
7. 令 indirectExportEntries 为新的空列表.
8. 令 localExportEntries 为新的空列表.
9. 令 starExportEntries 为新的空列表.
10. 令 exportEntries 为 body 的 ExportEntries
11. 对于在exportEntries中的每一个ExportEntry Record ee，执行
a. 若 ee.[[ModuleRequest]] 是 null，那么
12. i. 若 ee.[[LocalName]] 不是 an importedBoundNames 的 element then
    1. 将 ee 添加到 localExportEntries.
    ii. 否则，
    1. 令 ie 为 the element of importEntries whose [[LocalName]] 是 the same as ee.[[LocalName]].
    2. 若 ie.[[ImportName]] 是 "*"，那么
    a. 断言: This 是 a re-export of an imported module namespace object.
    b. 将 ee 添加到 localExportEntries.
    3. Else this 是 a re-export of a single name,
    a. Append the ExportEntry Record { [[ModuleRequest]]: ie.[[ModuleRequest]],
    [[ImportName]]: ie.[[ImportName]], [[LocalName]]: null, [[ExportName]]: ee.
    [[ExportName]] } to indirectExportEntries.
    b. Else 若 ee.[[ImportName]] 是 "*"，那么
    i. 将 ee 添加到 starExportEntries.
    c. 否则，
    i. 将 ee 添加到 indirectExportEntries.
    12. 返回 Source Text Module Record { [[Realm]]: realm, [[Environment]]: undefined, [[Namespace]]: undefined,
    [[Status]]: "uninstantiated", [[EvaluationError]]: undefined, [[HostDefined]]: hostDefined,
    [[ECMAScriptCode]]: body, [[RequestedModules]]: requestedModules, [[ImportEntries]]: importEntries,
    [[LocalExportEntries]]: localExportEntries, [[IndirectExportEntries]]: indirectExportEntries, [[StarExportEntries]]:
    starExportEntries, [[DFSIndex]]: undefined, [[DFSAncestorIndex]]: undefined }.

> 注 An implementation may parse module source text and analyse it for Early Error conditions prior to the evaluation of ParseModule for that module source text. However, the reporting of any errors must be deferred until the point where this specification actually performs ParseModule upon that source text.

##### 15.2.1.17.2 GetExportedNames ( exportStarSet ) Concrete Method <div id="sec-getexportednames"></div>

The GetExportedNames concrete method of a Source Text Module Record implements the corresponding Module Record abstract method.

It performs the following steps:

1. 令 module 为 this Source Text Module Record.
2. 若 exportStarSet contains module，那么
a. 断言: We've reached the starting point of an import * circularity.
b. 返回新的空列表.
3. 将 module 添加到 exportStarSet.
4. 令 exportedNames 为新的空列表.
5. 对于在module.[[LocalExportEntries]]中的每一个ExportEntry Record e，执行
a. 断言: module provides the direct binding for this export.
b. 将 e.[[ExportName]] 添加到 exportedNames.
6. 对于在module.[[IndirectExportEntries]]中的每一个ExportEntry Record e，执行
a. 断言: module imports a specific binding for this export.
b. 将 e.[[ExportName]] 添加到 exportedNames.
7. 对于在module.[[StarExportEntries]]中的每一个ExportEntry Record e，执行
a. 令 requestedModule 为 ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
b. 令 starNames 为 ? requestedModule.GetExportedNames(exportStarSet).
8. c. For each element starNames 的 n do
    i. 若 SameValue(n, "default") 是 false, then3
  1. 若 n 不是 an exportedNames 的 element then

    a. 将 n 添加到 exportedNames.
  8. 返回 exportedNames.

> 注 GetExportedNames does not filter out or throw an exception for names that have ambiguous star export bindings.

##### 15.2.1.17.3 ResolveExport ( exportName, resolveSet ) Concrete Method <div id="sec-resolveexport"></div>

The ResolveExport concrete method of a Source Text Module Record implements the corresponding Module Record abstract method.

ResolveExport attempts to resolve an imported binding to the actual defining module and local binding name. The defining module may be the module represented by the Module Record this method was invoked on or some other module that 是 imported by that module. The parameter resolve设置 是 used to detect unresolved circular import/export paths. 若 a pair consisting of specific Module Record and exportName 是 reached that 是 already in resolveSet, an import circularity has been encountered. Before recursively calling ResolveExport, a pair consisting of module and exportName 是 added 为 resolveSet.

若 a defining module 是 found, a ResolvedBinding Record { [[Module]], [[BindingName]] } 是 returned. This record identifies the resolved binding of the originally requested export. 若 no definition was found or the request 是 found to be circular, null 是 returned. 若 the request 是 found to be ambiguous, the string "ambiguous" 是 returned.

This abstract method performs the following steps:

1. 令 module 为 this Source Text Module Record.
2. 对于在resolveSet中的每一个Record { [[Module]], [[ExportName]] } r，执行
a. 若 module and r.[[Module]] are the same Module Record and SameValue(exportName, r.[[ExportName]]) is
true，那么
i. 断言: This 是 a circular import request.
ii. 返回 null.
3. 将 the Record { [[Module]]: module, [[ExportName]]: exportName } 添加到 resolveSet.
4. 对于在module.[[LocalExportEntries]]中的每一个ExportEntry Record e，执行
a. 若 SameValue(exportName, e.[[ExportName]]) 是 true，那么
i. 断言: module provides the direct binding for this export.
ii. 返回 ResolvedBinding Record { [[Module]]: module, [[BindingName]]: e.[[LocalName]] }.
5. 对于在module.[[IndirectExportEntries]]中的每一个ExportEntry Record e，执行
a. 若 SameValue(exportName, e.[[ExportName]]) 是 true，那么
i. 断言: module imports a specific binding for this export.
ii. 令 importedModule 为 ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
iii. 返回 importedModule.ResolveExport(e.[[ImportName]], resolveSet).
6. 若 SameValue(exportName, "default") 是 true，那么
a. 断言: A default export was not explicitly defined by this module.
b. 返回 null.
c. 注: A default export cannot be provided by an export *.
7. 令 starResolution 为 null.
8. 对于在module.[[StarExportEntries]]中的每一个ExportEntry Record e，执行
9. a. 令 importedModule 为 ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
   b. 令 resolution 为 ? importedModule.ResolveExport(exportName, resolveSet).
   c. 若 resolution 是 "ambiguous", return "ambiguous".
   d. 若 resolution 不是 null，那么
   i. 断言: resolution 是 a ResolvedBinding Record.
   ii. 若 starResolution 是 null,设置 starResolution 为 resolution.
   iii. 否则，
   1. 断言: There 是 more than one * import that includes the requested name.
   2. 若 resolution.[[Module]] and starResolution.[[Module]] are not the same Module Record or
   SameValue(resolution.[[BindingName]], starResolution.[[BindingName]]) 是 false, return
   "ambiguous".
   9. 返回 starResolution.

##### 15.2.1.17.4 InitializeEnvironment ( ) Concrete Method <div id="sec-source-text-module-record-initialize-environment"></div>

The InitializeEnvironment concrete method of a Source Text Module Record implements the corresponding Cyclic Module Record abstract method.

This abstract method performs the following steps:

1. 令 module 为 this Source Text Module Record.
2. 对于在module.[[IndirectExportEntries]]中的每一个ExportEntry Record e，执行
a. 令 resolution 为 ? module.ResolveExport(e.[[ExportName]], « »).
b. 若 resolution 是 null or "ambiguous", 抛出 SyntaxError 异常
c. 断言: resolution 是 a ResolvedBinding Record.
3. 断言: All named exports from module are resolvable.
4. 令 realm 为 module.[[Realm]].
5. 断言: realm 不是 undefined.
6. 令 env 为 NewModuleEnvironment(realm.[[GlobalEnv]]).
7. 设置 module.[[Environment]] 为 env.
8. 令 envRec 为 env的 EnvironmentRecord.
9. 对于在module.[[ImportEntries]]中的每一个ImportEntry Record in，执行
a. 令 importedModule 为 ! HostResolveImportedModule(module, in.[[ModuleRequest]]).
b. 注: The above call cannot fail because imported module requests are a module 的 subset
[[RequestedModules]], and these have been resolved earlier in this algorithm.
c. 若 in.[[ImportName]] 是 "*"，那么
i. 令 namespace 为 ? GetModuleNamespace(importedModule).
ii. 执行 ! envRec.CreateImmutableBinding(in.[[LocalName]], true).
iii. Call envRec.InitializeBinding(in.[[LocalName]], namespace).
d. 否则，
i. 令 resolution 为 ? importedModule.ResolveExport(in.[[ImportName]], « »).
ii. 若 resolution 是 null or "ambiguous", 抛出 SyntaxError 异常
iii. Call envRec.CreateImportBinding(in.[[LocalName]], resolution.[[Module]], resolution.
[[BindingName]]).
10. 令 code 为 module.[[ECMAScriptCode]].
11. 令 varDeclarations 为 code 的 VarScopedDeclarations
12. 令 declaredVarNames 为新的空列表.
13. 对于在varDeclarations中的每一个element d，执行
a. For each element dn of d 的 BoundNames do
14. i. 若 dn 不是 an declaredVarNames 的 element then
    1. 执行 ! envRec.CreateMutableBinding(dn, false).
    2. Call envRec.InitializeBinding(dn, undefined).
    3. 将 dn 添加到 declaredVarNames.
    14. 令 lexDeclarations 为 code 的 LexicallyScopedDeclarations
    15. 对于在lexDeclarations中的每一个element d，执行
    a. For each element dn of d 的 BoundNames do
    i. 若 IsConstantDeclaration of d 是 true，那么
    1. 执行 ! envRec.CreateImmutableBinding(dn, true).
    ii. 否则，
    1. 执行 ! envRec.CreateMutableBinding(dn, false).
    iii. 若 d 是 a FunctionDeclaration, a GeneratorDeclaration, an AsyncFunctionDeclaration, or an
    AsyncGeneratorDeclaration，那么
    1. 令 fo 为 the result of performing InstantiateFunctionObject for d with argument env.
    2. Call envRec.InitializeBinding(dn, fo).
    16. 返回 NormalCompletion(empty).

##### 15.2.1.17.5 ExecuteModule ( ) Concrete Method <div id="sec-source-text-module-record-execute-module"></div>

The ExecuteModule concrete method of a Source Text Module Record implements the corresponding Cyclic Module Record abstract method.

This abstract method performs the following steps:

1. 令 module 为 this Source Text Module Record.
2. 令 moduleCxt 为 a new ECMAScript code execution context.
3. 设置 moduleCxt 的 Function 为 null.
4. 断言: module.[[Realm]] 不是 undefined.
5. 设置 moduleCxt 的 Realm 为 module.[[Realm]].
6. 设置 moduleCxt 的 ScriptOrModule 为 module.
7. 断言: module has been linked and declarations in its module environment have been instantiated.
8. 设置 moduleCxt 的 VariableEnvironment 为 module.[[Environment]].
9. 设置 moduleCxt 的 LexicalEnvironment 为 module.[[Environment]].
10. Suspend the currently running execution context.
11. Push moduleCxt on to the execution context stack; moduleCxt 是 now the running execution context.
12. 令 result 为 the result of evaluating module.[[ECMAScriptCode]].
13. Suspend moduleCxt and remove it from the execution context stack.
14. Resume the context that 是 now on the top of the execution context stack as the running execution context.
15. 返回 Completion(result).

#### 15.2.1.18 RS: HostResolveImportedModule ( referencingModule, specifier ) <div id="sec-hostresolveimportedmodule"></div>

HostResolveImportedModule 是 an implementation-defined abstract operation that provides the concrete Module Record subclass instance that corresponds to the ModuleSpecifier String, specifier, occurring within the context of the module represented by the Module Record referencingModule.

The implementation of HostResolveImportedModule must conform to the following requirements:

The normal return value must be an instance of a concrete subclass of Module Record.
若 a Module Record corresponding to the pair referencingModule, specifier does not exist or cannot be created, an exception must be thrown. This operation must be idempotent 若 it completes normally. Each time it 是 called with a specific referencingModule, specifier pair as arguments it must return the same Module Record instance.

Multiple different referencingModule, specifier pairs may map to the same Module Record instance. The actual mapping semantic 是 implementation-defined but typically a normalization process 是 applied to specifier as part of the mapping process. A typical normalization process would include actions such as alphabetic case folding and expansion of relative and abbreviated path specifiers.

#### 15.2.1.19 RS: GetModuleNamespace ( module ) <div id="sec-getmodulenamespace"></div>

The GetModuleNamespace abstract operation retrieves the Module Namespace Exotic object representing module的 exports, lazily creating it the first time it was requested, and storing it in module.[[Namespace]] for future retrieval.

This abstract operation performs the following steps:

1. 断言: module 是 an instance of a concrete subclass of Module Record.
2. 断言: module.[[Status]] 不是 "uninstantiated".
3. 令 namespace 为 module.[[Namespace]].
4. 若 namespace 是 undefined，那么
a. 令 exportedNames 为 ? module.GetExportedNames(« »).
b. 令 unambiguousNames 为新的空列表.
c. For each name that 是 an exportedNames 的 element do
i. 令 resolution 为 ? module.ResolveExport(name, « »).
ii. 若 resolution 是 a ResolvedBinding Record, 将 name 添加到 unambiguousNames.
d. 设置 namespace 为 ModuleNamespaceCreate(module, unambiguousNames).
5. 返回 namespace.

> 注 The only way GetModuleNamespace can throw 是 via one of the triggered HostResolveImportedModule calls. Unresolvable names are simply excluded from the namespace at this point. They will lead to a real instantiation error later unless they are all ambiguous star exports that are not explicitly requested anywhere.

#### 15.2.1.20 RS: TopLevelModuleEvaluationJob ( sourceText, hostDefined ) <div id="sec-toplevelmoduleevaluationjob"></div>

A TopLevelModuleEvaluationJob with parameters sourceText and hostDefined 是 a job that parses, validates, and evaluates sourceText as a Module.

1. 断言: sourceText 是 an ECMAScript source text (see clause 10).
2. 令 realm 为 the current Realm Record.
3. 令 m 为 ParseModule(sourceText, realm, hostDefined).
4. 若 m 是 a errors 的 List then
a. 执行 HostReportErrors(m).
b. 返回 NormalCompletion(undefined).
5. 执行 ? m.Instantiate().
6. 断言: All dependencies of m have been transitively resolved and m 是 ready for evaluation.
7. 返回 ? m.Evaluate().

> 注 An implementation may parse a sourceText as a Module, analyse it for Early Error conditions, and instantiate it prior to the execution of the TopLevelModuleEvaluationJob for that sourceText. An implementation may also resolve, pre-parse and pre-analyse, and pre-instantiate module sourceText 的 dependencies However, the reporting of any errors detected by these actions must be deferred until the TopLevelModuleEvaluationJob 是 actually executed.

#### 15.2.1.21 RS: Evaluation <div id="sec-module-semantics-runtime-semantics-evaluation"></div>

```
Module : [empty]
```

1. 返回 NormalCompletion(undefined).

```
ModuleBody : ModuleItemList
```

1. 令 result 为 the result of evaluating ModuleItemList.
2. 若 result.[[Type]] 是 normal and result.[[Value]] 是 empty，那么
a. 返回 NormalCompletion(undefined).
3. 返回 Completion(result).

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 sl 为 the result of evaluating ModuleItemList.
2. ReturnIfAbrupt(sl).
3. 令 s 为 the result of evaluating ModuleItem.
4. 返回 Completion(UpdateEmpty(s, sl)).

> 注 The value of a ModuleItemList 是 the value of the last value-producing item in the ModuleItemList.

```
ModuleItem : ImportDeclaration
```

1. 返回 NormalCompletion(empty)

### 15.2.2 导入 <div id="sec-imports"></div>

**语法**

```
ImportDeclaration :
import ImportClause FromClause ;
import ModuleSpecifier ;
ImportClause :
ImportedDefaultBinding
NameSpaceImport
NamedImports
ImportedDefaultBinding , NameSpaceImport
ImportedDefaultBinding , NamedImports
ImportedDefaultBinding :
ImportedBinding
NameSpaceImport :
* as ImportedBinding
NamedImports :
{ }
{ ImportsList }
{ ImportsList , }
FromClause :
from ModuleSpecifier
ImportsList :
ImportSpecifier
ImportsList , ImportSpecifier
ImportSpecifier :
ImportedBinding
IdentifierName as ImportedBinding
ModuleSpecifier :
StringLiteral
ImportedBinding :
BindingIdentifier[~Yield, ~Await]
```

#### 15.2.2.1 SS: Early Errors <div id="sec-imports-static-semantics-early-errors"></div>

```
ModuleItem : ImportDeclaration
```

It 是 a Syntax Error 若 the BoundNames of ImportDeclaration contains any duplicate entries.

#### 15.2.2.2 SS: BoundNames <div id="sec-imports-static-semantics-boundnames"></div>

```
ImportDeclaration : import ImportClause FromClause ;
```

1. 返回 ImportClause 的 BoundNames

```
ImportDeclaration : import ModuleSpecifier ;
```

1. 返回新的空列表.

```
ImportClause : ImportedDefaultBinding , NameSpaceImport
```

1. 令 names 为 ImportedDefaultBinding 的 BoundNames
2. Append to names the elements of NameSpaceImport 的 BoundNames
3. 返回 names.

```
ImportClause : ImportedDefaultBinding , NamedImports
```

1. 令 names 为 ImportedDefaultBinding 的 BoundNames
2. Append to names the elements of NamedImports 的 BoundNames
3. 返回 names.

```
NamedImports : { }
```

1. 返回新的空列表

```
ImportsList : ImportsList , ImportSpecifier
```

1. 令 names 为 ImportsList 的 BoundNames
2. Append to names the elements of ImportSpecifier 的 BoundNames
3. 返回 names.

```
ImportSpecifier : IdentifierName as ImportedBinding
```

1. 返回 the BoundNames of ImportedBinding

#### 15.2.2.3 SS: ImportEntries <div id="sec-imports-static-semantics-importentries"></div>

```
ImportDeclaration : import ImportClause FromClause ;
```

1. 令 module 为 the sole element of FromClause 的 ModuleRequests
2. 返回 ImportEntriesForModule of ImportClause with argument module

```
ImportDeclaration : import ModuleSpecifier ;
```

1. 返回新的空列表.

#### 15.2.2.4 SS: ImportEntriesForModule <div id="sec-static-semantics-importentriesformodule"></div>

With parameter module.

```
ImportClause : ImportedDefaultBinding , NameSpaceImport
```

1. 令 entries 为 ImportEntriesForModule of ImportedDefaultBinding with argument module.
2. Append to entries the elements of the ImportEntriesForModule of NameSpaceImport with argument module.
3. 返回 entries.

```
ImportClause : ImportedDefaultBinding , NamedImports
```

1. 令 entries 为 ImportEntriesForModule of ImportedDefaultBinding with argument module.
2. Append to entries the elements of the ImportEntriesForModule of NamedImports with argument module.
3. 返回 entries

```
ImportedDefaultBinding : ImportedBinding
```

1. 令 localName 为 the sole element of ImportedBinding 的 BoundNames
2. 令 defaultEntry 为 the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "default",
[[LocalName]]: localName }.
3. 返回 a new List containing defaultEntry.

```
NameSpaceImport : * as ImportedBinding
```

1. 令 localName 为 ImportedBinding 的 StringValue
2. 令 entry 为 the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "*", [[LocalName]]:
localName }.
3. 返回 a new List containing entry.

```
NamedImports : { }
```

1. 返回新的空列表.

```
ImportsList : ImportsList , ImportSpecifier
```

1. 令 specs 为 the ImportEntriesForModule of ImportsList with argument module.
2. Append to specs the elements of the ImportEntriesForModule of ImportSpecifier with argument module.
3. 返回 specs.

```
ImportSpecifier : ImportedBinding
```

1. 令 localName 为 the sole element of ImportedBinding 的 BoundNames
2. 令 entry 为 the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: localName, [[LocalName]]: localName }.
3. 返回 a new List containing entry.

```
ImportSpecifier : IdentifierName as ImportedBinding
```

1. 令 importName 为 IdentifierName 的 StringValue
2. 令 localName 为 ImportedBinding 的 StringValue
3. 令 entry 为 the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]:
localName }.
4. 返回 a new List containing entry.

#### 15.2.2.5 SS: ModuleRequests <div id="sec-imports-static-semantics-modulerequests"></div>

```
ImportDeclaration : import ImportClause FromClause ;
```

1. 返回 FromClause 的 ModuleRequests

```
ModuleSpecifier : StringLiteral
```

1. 返回 a List containing StringLiteral 的 StringValue

### 15.2.3 导出 <div id="sec-exports"></div>

**语法**

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
export VariableStatement[~Yield, ~Await]
export Declaration[~Yield, ~Await]
export default HoistableDeclaration[~Yield, ~Await, +Default]
export default ClassDeclaration[~Yield, ~Await, +Default]
export default [lookahead ∉ { function , async [no LineTerminator here] function ,
class }] AssignmentExpression[+In, ~Yield, ~Await] ;
ExportClause :
{ }
{ ExportsList }
{ ExportsList , }
ExportsList :
ExportSpecifier
ExportsList , ExportSpecifier
ExportSpecifier :
IdentifierName
IdentifierName as IdentifierName
```

#### 15.2.3.1 SS: Early Errors <div id="sec-exports-static-semantics-early-errors"></div>

```
ExportDeclaration : export ExportClause ;
```

For each IdentifierName n in ReferencedBindings of ExportClause: It 是 a Syntax Error 若 StringValue of n 是 a
ReservedWord or 若 the StringValue of n 是 one of: "implements", "interface", "let", "package",
"private", "protected", "public", or "static".

> 注 The above rule means that each ReferencedBindings of ExportClause 是 treated as an IdentifierReference.

#### 15.2.3.2 SS: BoundNames <div id="sec-exports-static-semantics-boundnames"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
```

1. 返回新的空列表.

```
ExportDeclaration : export VariableStatement
```

1. 返回 VariableStatement 的 BoundNames

```
ExportDeclaration : export Declaration
```

1. 返回 Declaration 的 BoundNames

```
ExportDeclaration : export default HoistableDeclaration
```

1. 令 declarationNames 为 HoistableDeclaration 的 BoundNames
2. 若 declarationNames does not include the element "*default*", 将 "*default*" 添加到 declarationNames.
3. 返回 declarationNames.

```
ExportDeclaration : export default ClassDeclaration
```

1. 令 declarationNames 为 ClassDeclaration 的 BoundNames
2. 若 declarationNames does not include the element "*default*", 将 "*default*" 添加到 declarationNames.
3. 返回 declarationNames.

```
ExportDeclaration : export default AssignmentExpression ;
```

1. 返回 « "\*default\*" ».

#### 15.2.3.3 SS: ExportedBindings <div id="sec-exports-static-semantics-exportedbindings"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
```

1. 返回新的空列表.

```
ExportDeclaration : export ExportClause ;
```

1. 返回 ExportClause 的 ExportedBindings

```
ExportDeclaration : export VariableStatement
```

1. 返回 VariableStatement 的 BoundNames

```
ExportDeclaration : export Declaration
```

1. 返回 the BoundNames of Declaration

```
ExportDeclaration : export default HoistableDeclaration
ExportDeclaration : export default ClassDeclaration
ExportDeclaration : export default AssignmentExpression ;
```

1. 返回 the BoundNames of this ExportDeclaration.

```
ExportClause : { }
```

1. 返回新的空列表.

```
ExportsList : ExportsList , ExportSpecifier
```

1. 令 names 为 ExportsList 的 ExportedBindings
2. Append to names the elements of ExportSpecifier 的 ExportedBindings
3. 返回 names.

```
ExportSpecifier : IdentifierName
```

1. 返回 a List containing the StringValue of IdentifierName

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. 返回 a List containing the StringValue of the first IdentifierName

#### 15.2.3.4 SS: ExportedNames <div id="sec-exports-static-semantics-exportednames"></div>

```
ExportDeclaration : export * FromClause ;
```

1. 返回新的空列表.

```
ExportDeclaration : export ExportClause FromClause ;
ExportDeclaration : export ExportClause ;
```

1. 返回 ExportClause 的 ExportedNames

```
ExportDeclaration : export VariableStatement
```

1. 返回 the BoundNames of VariableStatement

```
ExportDeclaration : export Declaration
```

1. 返回 Declaration 的 BoundNames

```
ExportDeclaration : export default HoistableDeclaration
ExportDeclaration : export default ClassDeclaration
ExportDeclaration : export default AssignmentExpression ;
```

1. 返回 « "default" ».

```
ExportClause : { }
```

1. 返回新的空列表.

```
ExportsList : ExportsList , ExportSpecifier
```

1. 令 names 为 ExportsList 的 ExportedNames
2. Append to names the elements of ExportSpecifier 的 ExportedNames
3. 返回 names

```
ExportSpecifier : IdentifierName
```

1. 返回 a List containing IdentifierName 的 StringValue

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. 返回 a List containing the StringValue of the second IdentifierName.

#### 15.2.3.5 SS: ExportEntries <div id="sec-exports-static-semantics-exportentries"></div>

```
ExportDeclaration : export * FromClause ;
```

1. 令 module 为 the sole element of FromClause 的 ModuleRequests
2. 令 entry 为 the ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "*", [[LocalName]]: null, [[ExportName]]: null }.
3. 返回 a new List containing entry.

```
ExportDeclaration : export ExportClause FromClause ;
```

1. 令 module 为 the sole element of FromClause 的 ModuleRequests
2. 返回 ExportEntriesForModule of ExportClause with argument module.

```
ExportDeclaration : export ExportClause ;
```

1. 返回 ExportEntriesForModule of ExportClause with argument null.

```
ExportDeclaration : export VariableStatement
```

1. 令 entries 为新的空列表.
2. 令 names 为 VariableStatement 的 BoundNames
3. 对于在names中的每一个name，执行

a. Append the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]: name,
[[ExportName]]: name } to entries.

4. 返回 entries.

```
ExportDeclaration : export Declaration
```

1. 令 entries 为新的空列表.
2. 令 names 为 Declaration 的 BoundNames
3. 对于在names中的每一个name，执行
a. 将 the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]: name, [[ExportName]]: name } 添加到 entries.
4. 返回 entries

```
ExportDeclaration : export default HoistableDeclaration
```

1. 令 names 为 HoistableDeclaration 的 BoundNames
2. 令 localName 为 the sole names 的 element
3. 返回 a new List containing the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null,
[[LocalName]]: localName, [[ExportName]]: "default" }.

```
ExportDeclaration : export default ClassDeclaration
```

1. 令 names 为 ClassDeclaration 的 BoundNames
2. 令 localName 为 the sole names 的 element
3. 返回 a new List containing the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null,
[[LocalName]]: localName, [[ExportName]]: "default" }.

```
ExportDeclaration : export default AssignmentExpression ;
```

1. 令 entry 为 the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]:
"\*default\*", [[ExportName]]: "default" }.
2. 返回 a new List containing entry.

> 注 "\*default\*" 是 used within this specification as a synthetic name for anonymous default export values.

#### 15.2.3.6 SS: ExportEntriesForModule <div id="sec-static-semantics-exportentriesformodule"></div>

With parameter module.

```
ExportClause : { }
```

1. 返回新的空列表.

```
ExportsList : ExportsList , ExportSpecifier
```

1. 令 specs 为 the ExportEntriesForModule of ExportsList with argument module.
2. Append to specs the elements of the ExportEntriesForModule of ExportSpecifier with argument module.
3. 返回 specs.

```
ExportSpecifier : IdentifierName
```

1. 令 sourceName 为 IdentifierName 的 StringValue
2. 若 module 是 null，那么
a. 令 localName 为 sourceName.
b. 令 importName 为 null.
3. 否则，
a. 令 localName 为 null.
b. 令 importName 为 sourceName.
4. 返回 a new List containing the ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]: localName, [[ExportName]]: sourceName }.

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. 令 sourceName 为 the StringValue of the first IdentifierName.
2. 令 exportName 为 the StringValue of the second IdentifierName.
3. 若 module 是 null，那么
a. 令 localName 为 sourceName.
b. 令 importName 为 null.
4. 否则，
a. 令 localName 为 null.
b. 令 importName 为 sourceName.
5. 返回 a new List containing the ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]: localName, [[ExportName]]: exportName }.

#### 15.2.3.7 SS: IsConstantDeclaration <div id="sec-exports-static-semantics-isconstantdeclaration"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
export default AssignmentExpression ;
```

1. 返回 false.

> 注 It 不是 necessary to treat export default AssignmentExpression as a constant declaration because there 是 no syntax that permits assignment to the internal bound name used to reference a module的 default object.

#### 15.2.3.8 SS: LexicallyScopedDeclarations <div id="sec-exports-static-semantics-lexicallyscopeddeclarations"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
export VariableStatement
```

1. 返回新的空列表.

```
ExportDeclaration : export Declaration
```

1. 返回 a new List containing Declaration 的 DeclarationPart

```
ExportDeclaration : export default HoistableDeclaration
```

1. 返回 a new List containing HoistableDeclaration 的 DeclarationPart

```
ExportDeclaration : export default ClassDeclaration
```

1. 返回 a new List containing ClassDeclaration.

```
ExportDeclaration : export default AssignmentExpression ;
```

1. 返回 a new List containing this ExportDeclaration.

#### 15.2.3.9 SS: ModuleRequests <div id="sec-exports-static-semantics-modulerequests"></div>

```
ExportDeclaration : export * FromClause ;
ExportDeclaration : export ExportClause FromClause ;
```

1. 返回 the ModuleRequests of FromClause

   ```
   ExportDeclaration :
   export ExportClause ;
   export VariableStatement
   export Declaration
   export default HoistableDeclaration
   export default ClassDeclaration
   export default AssignmentExpression ;
   ```

   1. 返回新的空列表.

#### 15.2.3.10 SS: ReferencedBindings <div id="sec-static-semantics-referencedbindings"></div>

```
ExportClause : { }
```

1. 返回新的空列表.

```
ExportsList : ExportsList , ExportSpecifier
```

1. 令 names 为 ExportsList 的 ReferencedBindings
2. Append to names the elements of ExportSpecifier 的 ReferencedBindings
3. 返回 names.

```
ExportSpecifier : IdentifierName
```

1. 返回 a List containing the IdentifierName.

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. 返回 a List containing the first IdentifierName.

#### 15.2.3.11 RS: Evaluation <div id="sec-exports-runtime-semantics-evaluation"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
```

1. 返回 NormalCompletion(empty).

```
ExportDeclaration : export VariableStatement
```

1. 返回 the result of evaluating VariableStatement.

```
ExportDeclaration : export Declaration
```

1. 返回 the result of evaluating Declaration.

```
ExportDeclaration : export default HoistableDeclaration
```

1. 返回 the result of evaluating HoistableDeclaration.

```
ExportDeclaration : export default ClassDeclaration
```

1. 令 value 为 the result of ClassDeclaration 的 BindingClassDeclarationEvaluation
2. ReturnIfAbrupt(value).
3. 令 className 为 the sole element of ClassDeclaration 的 BoundNames
4. 若 className 是 "\*default\*"，那么
a. 令 env 为 the running execution context的 LexicalEnvironment.
b. 执行 ? InitializeBoundName("*default*", value, env).
5. 返回 NormalCompletion(empty).

```
ExportDeclaration : export default AssignmentExpression ;
```

1. 若 IsAnonymousFunctionDefinition(AssignmentExpression) 是 true，那么
a. 令 value 为 the result of performing NamedEvaluation for AssignmentExpression with argument
"default".
2. 否则，
a. 令 rhs 为 the result of evaluating AssignmentExpression.
b. 令 value 为 ? GetValue(rhs).
3. 令 env 为 the running execution context的 LexicalEnvironment.
4. 执行 ? InitializeBoundName("\*default\*", value, env).
5. 返回 NormalCompletion(empty).