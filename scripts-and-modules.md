# 15 ECMAScript语言：脚本和模块
## 15.1 脚本 <div id="sec-function-definitions"></div>

**语法**

```
Script :
ScriptBodyopt
ScriptBody :
StatementList[~Yield, ~Await, ~return]
```

### 15.1.1 静态语义：Early Errors <div id="sec-scripts-static-semantics-early-errors"></div>

```
Script : ScriptBody
```

若ScriptBody的LexicallyDeclaredNames包含任何重复的条目，这是一个语法错误。

若ScriptBody的LexicallyDeclaredNames的任何元素也出现在ScriptBody的VarDeclaredNames中，这是一个语法错误。

```
ScriptBody : StatementList
```

若StatementList包含super，则是语法错误，除非包含super的源代码是由直接eval处理的eval代码。在18.2.1.1中定义了直接求值的super额外早期错误规则。
若StatementList包含了NewTarget，这是一个语法错误，除非包含NewTarget的源代码是由直接eval处理的eval代码。在18.2.1.1中定义了直接求值的super额外早期错误规则。
若包含带有参数«»的重复声明列表为真，这是一个语法错误。
若带有参数«»的StatementList的ContainsUndefinedBreakTarget为真，这是一个语法错误。
若带有参数«»和«»的StatementList的ContainsUndefinedContinueTarget为真，则为语法错误。

### 15.1.2 静态语义：IsStrict <div id="sec-static-semantics-isstrict"></div>

```
ScriptBody : StatementList
```

1. 若指令的声明列表的序言包含一个使用严格指令，返回true；否则,返回false。

### 15.1.3 静态语义：LexicallyDeclaredNames <div id="sec-scripts-static-semantics-lexicallydeclarednames"></div>

```
ScriptBody : StatementList
```

1. 返回StatementList的TopLevelLexicallyDeclaredNames

> 注：在Script的顶层，将函数声明视为var声明，而不是词法声明。

### 15.1.4 静态语义：LexicallyScopedDeclarations <div id="sec-scripts-static-semantics-lexicallyscopeddeclarations"></div>

```
ScriptBody : StatementList
```

1. 返回StatementList的TopLevelLexicallyScopedDeclarations。

### 15.1.5 静态语义：VarDeclaredNames <div id="sec-scripts-static-semantics-vardeclarednames"></div>

```
ScriptBody : StatementList
```

1. 返回StatementList的TopLevelVarDeclaredNames 。

### 15.1.6 静态语义：VarScopedDeclarations <div id="sec-scripts-static-semantics-varscopeddeclarations"></div>

```
ScriptBody : StatementList
```

1. 返回StatementList的TopLevelVarScopedDeclarations 。

### 15.1.7 运行时语义：Evaluation <div id="sec-script-semantics-runtime-semantics-evaluation"></div>

```
Script : [empty]
```

1. 返回 NormalCompletion(undefined).

### 15.1.8 脚本记录 <div id="sec-script-records"></div>

脚本记录封装了有关正在求值的脚本的信息。每个脚本记录都包含表36中列出的字段。

表格 36: Script Record Fields

| 字段名             | 值类型                    | 意义                                                     |
| ------------------ | ------------------------- | -------------------------------------------------------- |
| [[Realm]]          | 作用域记录项 \| undefined | 在其中创建此脚本的作用域。undefined（若尚未分配）。    |
| [[Environment]]    | 词法环境 \| undefined     | 包含此脚本的顶级绑定的词法环境。实例化脚本时设置此字段。 |
| [[ECMAScriptCode]] | 解析节点                  | 使用脚本作为目标符号解析此模块的源文本的结果。           |
| [[HostDefined]]    | 任意，默认值为undefined   | 保留给需要将其他信息与脚本关联的主机环境使用的字段。     |

### 15.1.9 ParseScript ( sourceText, realm, hostDefined ) <div id="sec-parse-script"></div>

具有参数sourceText，realm和hostDefined的抽象操作ParseScript会基于将sourceText解析为脚本的结果来创建脚本记录。 ParseScript执行以下步骤：

1. 断言: sourceText是ECMAScript源文本（请参见第10节）。
2. 使用Script作为目标符号来解析sourceText，并分析任何早期错误条件的解析结果。若解析成功并且未发现早期错误，则将body作为结果解析树。否则，将body作为代表解析错误和/或早期错误的一个或多个SyntaxError或ReferenceError对象的列表。解析和早期错误检测可以以依赖于实现的方式进行交织。若存在多个解析错误或早期错误，则列表中错误对象的数量和顺序与实现有关，但必须至少存在一个。
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

### 15.1.11 运行时语义：GlobalDeclarationInstantiation ( script, env ) <div id="sec-globaldeclarationinstantiation"></div>

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
          2. 注: 若有多个相同名称的函数声明，则使用最后一个声明。
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
13. 注: 若全局对象是普通对象，则在此算法步骤之后不会发生异常终止。但是，若全局对象是Proxy外来对象，则在以下某些步骤中，它可能会表现出导致异常终止的行为。
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

> 注 2：15.1.1中指定的早期错误可防止 function/ var声明与let / const / class声明之间的名称冲突，以及防止为单个脚本中包含的声明重新声明let / const / class绑定。但是，在GlobalDeclarationInstantiation期间将跨越多个脚本的此类冲突和重新声明检测为运行时错误。若检测到任何此类错误，则不会实例化脚本的绑定。但是，若使用代理外来对象定义了全局对象，则针对冲突声明的运行时测试可能不可靠，从而导致突然完成，并且某些全局声明未实例化。若发生这种情况，则不会执行脚本的代码。
>
> 与显式var或function声明不同，直接在全局对象上创建的属性会导致全局绑定，而这些绑定可能会被let / const / class声明所遮盖。

### 15.1.12 运行时语义：ScriptEvaluationJob ( sourceText, hostDefined ) <div id="sec-scriptevaluationjob"></div>

具有参数sourceText和hostDefined的任务ScriptEvaluationJob解析、验证和运算sourceText作为脚本

1. 断言: sourceText 是ECMAScript源文本 (见第10条).
2. 令 realm 为当前 Realm Record.
3. 令 s 为 ParseScript(sourceText, realm, hostDefined).
4. 若 s 是 errors 的 列表，那么
   1. 执行 HostReportErrors(s).
   2. 返回 NormalCompletion(undefined).
5. 返回 ? ScriptEvaluation(s).

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
#### 15.2.1.1 静态语义：Early Errors <div id="sec-module-semantics-static-semantics-early-errors"></div>

```
ModuleBody : ModuleItemList
```

若ModuleItemList的LexicallyDeclaredNames包含任何重复的条目，则是语法错误。
若ModuleItemList的LexicallyDeclaredNames的任何元素也出现在ModuleItemList的VarDeclaredNames中，则是语法错误。
若ModuleItemList的ExportedNames包含任何重复的条目，则是语法错误。
若ModuleItemList的VarDeclaredNames或ModuleItemList的LexicallyDeclaredNames中也没有出现ModuleItemList的ExportedBindings的任何元素，则是语法错误。
若ModuleItemList包含super，则为语法错误。
若ModuleItemList包含NewTarget，则为语法错误。
若带有参数«»的ModuleItemList的ContainsDuplicateLabels为true，则为语法错误。
若带有参数«»的ModuleItemList的ContainsUndefinedBreakTarget为true，则为语法错误。
若带有参数«»和«»的ModuleItemList的ContainsUndefinedContinueTarget为true，则为语法错误。

> 注：重复的ExportedNames规则意味着ModuleBody中的多个export default 的 ExportDeclaration项目是语法错误。在运算模块之前，在模块链接期间检查与冲突或重复声明有关的其他错误条件。若检测到任何此类错误，则不会运算模块。

#### 15.2.1.2 静态语义：ContainsDuplicateLabels <div id="sec-module-semantics-static-semantics-containsduplicatelabels"></div>

带参数 labelSet。

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 hasDuplicates 为带有参数labelSet的ModuleItemList的ContainsDuplicateLabels。

2. 若 hasDuplicates 是 true，返回 true.

3. 返回带有参数 labelSet 的 ModuleItem 的 ContainsDuplicateLabels

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. 返回 false.

#### 15.2.1.3 静态语义：ContainsUndefinedBreakTarget <div id="sec-module-semantics-static-semantics-containsundefinedbreaktarget"></div>

带参数 labelSet。

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 hasUndefinedLabels 为带有参数 labelSet 的 ModuleItemList 的 ContainsUndefinedBreakTarget

2. 若 hasUndefinedLabels 是 true，返回 true.

3. 返回带有参数 labelSet 的 ModuleItem 的 ContainsUndefinedBreakTarget

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. 返回 false.

#### 15.2.1.4 静态语义：ContainsUndefinedContinueTarget <div id="sec-module-semantics-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 hasUndefinedLabels 为带有参数 iterationSet 和 « » 的 ModuleItemList 的 ContainsUndefinedContinueTarget .

2. 若 hasUndefinedLabels 是 true，返回 true.

3. 返回参数 iterationSet 和 « » 的 ModuleItem 的 ContainsUndefinedContinueTarget .

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. 返回 false.

#### 15.2.1.5 静态语义：ExportedBindings <div id="sec-module-semantics-static-semantics-exportedbindings"></div>

> 注：ExportedBindings是与模块的ExportedNames显式关联的本地绑定名称。

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 names 为ModuleItemList 的 ExportedBindings

2. 追加到ModuleItem的ExportedBindings元素的名称

3. 返回 names

   ```
   ModuleItem :
   ImportDeclaration
   StatementListItem
   ```

   1. 返回新的空列表.

#### 15.2.1.6 静态语义：ExportedNames <div id="sec-module-semantics-static-semantics-exportednames"></div>

> 注：ExportedNames是模块显式映射到其本地名称绑定之一的外部可见名称。

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 names 为 ModuleItemList 的 ExportedNames
2. 追加到ModuleItem的ExportedNames元素的名称。
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

#### 15.2.1.7 静态语义：ExportEntries <div id="sec-module-semantics-static-semantics-exportentries"></div>

```
Module : [empty]
```

1. 返回新的空列表.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 entries 为 ModuleItemList 的 ExportEntries

2. 在 entries 后追加 ModuleItem 的 ExportEntries

3. 返回 entries.

   ```
   ModuleItem :
   ImportDeclaration
   StatementListItem
   ```

   1. 返回新的空列表.

#### 15.2.1.8 静态语义：ImportEntries <div id="sec-module-semantics-static-semantics-importentries"></div>

```
Module : [empty]
```

1. 返回新的空列表.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 entries 为 ModuleItemList 的 ImportEntries

2. 在 entries 后追加 ModuleItem 的 ImportEntries

3. 返回 entries

   ```
   ModuleItem :
   ExportDeclaration
   StatementListItem
   ```

   1. 返回新的空列表

#### 15.2.1.9 静态语义：ImportedLocalNames ( importEntries ) <div id="sec-importedlocalnames"></div>

具有参数importEntries的抽象操作ImportedLocalNames创建一个由ImportEntry记录列表定义的所有本地名称绑定的列表（请参见表42）。 ImportedLocalNames执行以下步骤：

1. 令 localNames 为新的空列表.
2. 对于在importEntries中的每一个ImportEntry 记录 i，执行
1. 将 i.[[LocalName]] 添加到 localNames.
3. 返回 localNames.

#### 15.2.1.10 静态语义：ModuleRequests <div id="sec-module-semantics-static-semantics-modulerequests"></div>

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
3. 附加到moduleNames的additionalNames的每个元素还不是moduleNames的元素。
4. 返回 moduleNames.

```
ModuleItem : StatementListItem
```

1. 返回新的空列表.

#### 15.2.1.11 静态语义：LexicallyDeclaredNames <div id="sec-module-semantics-static-semantics-lexicallydeclarednames"></div>

> 注 1：模块的LexicallyDeclaredNames包括其所有导入的绑定的名称。

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 names 为 ModuleItemList 的 LexicallyDeclaredNames
2. 在 names 后追加 ModuleItem 的 LexicallyDeclaredNames
3. 返回 names.

```
ModuleItem : ImportDeclaration
```

1. 返回 ImportDeclaration 的 BoundNames 

```
ModuleItem : ExportDeclaration
```

1. 若 ExportDeclaration 是 export VariableStatement, 返回新的空列表.
2. 返回 ExportDeclaration 的 BoundNames

```
ModuleItem : StatementListItem
```

1. 返回 StatementListItem 的 LexicallyDeclaredNames

> 注 2：在模块的顶层，将函数声明视为词法声明而不是var声明。

#### 15.2.1.12 静态语义：LexicallyScopedDeclarations <div id="sec-module-semantics-static-semantics-lexicallyscopeddeclarations"></div>

```
Module : [empty]
```

1. 返回新的空列表.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 declarations 为 ModuleItemList 的 LexicallyScopedDeclarations
2. 在 declarations 后追加 ModuleItem 的 LexicallyScopedDeclarations
3. 返回 declarations.

```
ModuleItem : ImportDeclaration
```

1. 返回新的空列表

#### 15.2.1.13 静态语义：VarDeclaredNames <div id="sec-module-semantics-static-semantics-vardeclarednames"></div>

```
Module : [empty]
```

1. 返回新的空列表

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 names 为 ModuleItemList 的 VarDeclaredNames
2. 在 names 后追加 ModuleItem 的 VarDeclaredNames
3. 返回 names.

```
ModuleItem : ImportDeclaration
```

1. 返回新的空列表.

```
ModuleItem : ExportDeclaration
```

1. 若 ExportDeclaration 是 export VariableStatement，返回 ExportDeclaration 的 BoundNames
2. 返回新的空列表.

#### 15.2.1.14 静态语义：VarScopedDeclarations <div id="sec-module-semantics-static-semantics-varscopeddeclarations"></div>

```
Module : [empty]
```

1. 返回新的空列表

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 declarations 为 ModuleItemList 的 VarScopedDeclarations
2. 在 declarations 后追加 ModuleItem 的 VarScopedDeclarations
3. 返回 declarations.

```
ModuleItem : ImportDeclaration
```

1. 返回新的空列表.

```
ModuleItem : ExportDeclaration
```

1. 若 ExportDeclaration 是 export VariableStatement，返回 VariableStatement 的 VarScopedDeclarations
2. 返回新的空列表.

#### 15.2.1.15 抽象模块记录 <div id="sec-abstract-module-records"></div>

模块记录封装有关单个模块的导入和导出的结构信息。此信息用于链接已连接模块集的导入和导出。模块记录包含四个字段，仅在运算模块时使用。

出于规范目的，“模块记录”值是记录规范类型的值，可以视为存在于简单的面向对象的层次结构中，其中“模块记录”是具有抽象和具体子类的抽象类。该规范定义了名为循环模块记录的抽象子类及其名为源文本模块记录的具体子类。其他规范和实现可以定义与它们定义的替代模块定义功能相对应的其他模块记录子类。

模块记录定义了表37中列出的字段。所有模块定义子类至少包括那些字段。模块记录还定义了表38中的抽象方法列表。所有模块定义子类都必须提供这些抽象方法的具体实现。

表37：模块记录字段

| 字段命长        | 值类型                           | 意义                                                         |
| --------------- | -------------------------------- | ------------------------------------------------------------ |
| [[Realm]]       | 作用域记录项\| undefined   | 在其中创建此模块的作用域。undfined（若尚未分配）。         |
| [[Environment]] | 词法环境 \| undefined | 包含此模块的顶级绑定的词法环境。实例化脚本时设置此字段 |
| [[Namespace]]   | 对象\| undefined              | 模块命名空间对象（26.3）（若已为此模块创建一个）。否则为undefined。 |
| [[HostDefined]] | 任意，默认值是 undefined. | 保留给需要其他信息与模块关联的主机环境使用的字段。 |

表38：模块记录的抽象方法

| 方法                                                 | 目的                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| GetExportedNames(exportStarSet)                      | 返回从此模块直接或间接导出的所有名称的列表                   |
| ResolveExport(exportName, resolveSet, exportStarSet) | 返回此模块导出的名称的绑定。绑定由格式为{[[[Module]]：Module Record，[[BindingName]]：String}的ResolvedBinding Record表示。若名称无法解析，则返回null；若找到多个绑定，则返回“ambiguous”<br />若该操作正常完成，则必须是幂等的。每次使用特定的exportName，resolveSet对作为参数调用时，它必须返回相同的结果。 |
| Instantiate()                                        | 通过暂时解决所有模块依赖性并创建模块环境记录来准备要运算的模块。 |
| Evaluate()                                           | 若已经成功解析执行了此模块，则返回undefined；否则返回false。若尚未解析执行成功，则抛出所产生的异常。否则，可传递地解析执行此模块的所有模块依赖性，然后解析执行此模块<br />实例化必须在调用此方法之前成功完成。 |



#### 15.2.1.16 循环模块记录 <div id="sec-cyclic-module-records"></div>

循环模块记录用于表示有关模块的信息，这些模块可以与作为循环模块记录类型子类的其他模块一起参与依赖循环。不是循环模块记录类型的子类的模块记录不能与源文本模块记录一起参与依赖循环。

除了表37中定义的字段外，循环模块记录还有表39中列出的其他字段

表39：循环模块记录的其他字段

| 字段名称             | 值类型                          | 意义                                                         |
| -------------------- | ------------------------------- | ------------------------------------------------------------ |
| [[Status]]           | String                          | 最初是“uninstantiated”。随着模块在其整个生命周期中的进展，转换为“instantiating”，“instantiated”，“evaluating”，“evaluated”（按此顺序）。 |
| [[EvaluationError]]  | An abrupt completion\|undefined | 类型throw的完成，表示运算期间发生的异常。若未发生异常或未对[[Status]]进行“envaluation”，则为undefined。 |
| [[DFSIndex]]         | Integer\|undefined              | 仅在实例化和解析执行期间使用的辅助字段。若[[Status]]是“instantiating”或“evaluating”，则此非负数记录在进行依赖图的深度优先遍历过程中首次访问模块的点。 |
| [[DFSAncestorIndex]] | Integer\|undefined              | 仅在实例化和运算期间使用的辅助字段。若[[Status]]是“instantiating”或“evaluating”，则这是模块自己的[[DFSIndex]]或同一强连接组件中“较早”模块的名称。 |
| [[RequestedModules]] | List of String                  | 此记录表示的模块用于请求导入模块的所有ModuleSpecifier字符串的列表。列表是源代码出现顺序。 |

除表38中定义的方法外，循环模块记录还有表40中列出的其他方法

表40：循环模块记录的其他抽象方法

| 方法                    | 目的                                           |
| ----------------------- | ---------------------------------------------- |
| InitializeEnvironment() | 初始化模块的词法环境，包括解析所有导入的绑定。 |
| ExecuteModule()         | 初始化模块的执行上下文，并运算其中的模块代码。 |

##### 15.2.1.16.1 Instantiate ( ) 具体方法 <div id="sec-moduledeclarationinstantiation"></div>

循环模块记录的实例化具体方法实现了相应的模块记录抽象方法。

成功完成后，Instantiate将该模块的[[Status]]从“uninstantiated”转换为“instantiated”。失败时，将引发异常，并且该模块的[[Status]]保持“uninstantiated”。

此抽象方法执行以下步骤（大部分工作由辅助函数InnerModuleInstantiation完成）：

1. 令 module 为此 Cyclic 模块记录项.
2. 断言: module.[[Status]] 不是 "instantiating" 或 "evaluating".
3. 令 stack 为新的空列表.
4. 令 result 为 InnerModuleInstantiation(module, stack, 0).
5. 若 result 是 abrupt completion，那么
1. 对于在stack中的每一个模块m，执行
  1. 断言: m.[[Status]] 是 "instantiating".
  2. 设置 m.[[Status]] 为 "uninstantiated".
  3. 设置 m.[[Environment]] 为 undefined.
  4. 设置 m.[[DFSIndex]] 为 undefined.
  5. 设置 m.[[DFSAncestorIndex]] 为 undefined.
2. 断言: module.[[Status]] 是 "uninstantiated".
3. 返回 result.
6. 断言: module.[[Status]] 是 "instantiated" 或 "evaluated".
7. 断言: stack 是 empty.
8. 返回 undefined.

###### 15.2.1.16.1.1 InnerModuleInstantiation ( module, stack, index ) <div id="sec-innermoduleinstantiation"></div>

实例化使用InnerModuleInstantiation抽象操作为Cyclic Module Record模块以及依赖关系图中的所有其他模块递归执行实际的实例化过程。堆栈和索引参数，以及模块的[[DFSIndex]]和[[DFSAncestorIndex]]字段可跟踪深度优先搜索（DFS）遍历。特别地，[[DFSAncestorIndex]]用于发现强连接的组件（SCC），以使SCC中的所有模块一起过渡到“instantiated”。

此抽象操作执行以下步骤：

1. 若 module 不是 Cyclic 模块记录项，那么
1. 执行 ? module.Instantiate().
2. 返回 index.
2. 若 module.[[Status]] 是 "instantiating", "instantiated", 或 "evaluated"，那么
  1. 返回 index.
3. 断言: module.[[Status]] 是 "uninstantiated".
4. 设置 module.[[Status]] 为 "instantiating".
5. 设置 module.[[DFSIndex]] 为 index.
6. 设置 module.[[DFSAncestorIndex]] 为 index.
7. index 增加 1.
8. 将 module 添加到 stack.
9. 对于每个需要的module.[[RequestedModules]]元素, 执行
  1. 令 requiredModule 为 ? HostResolveImportedModule(module, required).
  2. 设置 index 为 ? InnerModuleInstantiation(requiredModule, stack, index).
  3. 断言: requiredModule.[[Status]] 是 "instantiating", "instantiated", 或 "evaluated".
  4. 断言: requiredModule.[[Status]] 是 "instantiating" 若 and only 若 requiredModule 是 in stack.
  5. 若 requiredModule.[[Status]] 是 "instantiating"，那么
        1. 断言: requiredModule 是 a Cyclic Module Record.
            2. 设置 module.[[DFSAncestorIndex]] 为 min(module.[[DFSAncestorIndex]], requiredModule.
      [[DFSAncestorIndex]]).
10. 执行 ? module.InitializeEnvironment().
11. 断言: 模块恰好在堆栈中发生一次
12. 断言: module.[[DFSAncestorIndex]] 小于等于 module.[[DFSIndex]].
13. 若 module.[[DFSAncestorIndex]] 等于 module.[[DFSIndex]]，那么
    1. 令 done 为 false.
    2. 重复, 直到 done 是 false,
       1. 令 requiredModule 为 stack 的最后一个元素.
       2. 删除堆栈的最后一个元素。
       3. 设置 requiredModule.[[Status]] 为 "instantiated".
       4. 若requiredModule和module是相同的模块记录, 设置 done 为 true.
14. 返回 index

##### 15.2.1.16.2 Evaluate ( ) 具体方法 <div id="sec-moduleevaluation"></div>

循环模块记录的运算具体方法实现了相应的模块记录抽象方法。

运算将模块的[[Status]]从“instantiated”转换为“evaluated”。

若执行导致异常，则将该异常记录在[[EvaluationError]]字段中，并在以后调用Evaluate时将其重新抛出。

该抽象方法执行以下步骤(大部分工作由辅助函数InnerModuleEvaluation完成):

1. 令 module 为此 Cyclic 模块记录项.
2. 断言: module.[[Status]] 是 "instantiated" 或 "evaluated".
3. 令 stack 为新的空列表.
4. 令 result 为 InnerModuleEvaluation(module, stack, 0).
5. 若 result 是 abrupt completion，那么
  1. 对于在stack中的每一个模块 m，执行
        1. 断言: m.[[Status]] 是 "evaluating".
            2. 设置 m.[[Status]] 为 "evaluated".
                3. 设置 m.[[EvaluationError]] 为 result.
  2. 断言: module.[[Status]] 是 "evaluated"，并且 module.[[EvaluationError]] 是 result.
  3. 返回 result.
6. 断言: module.[[Status]] 是 "evaluated"，并且 module.[[EvaluationError]] 是 undefined.
7. 断言: stack 是 empty.
8. 返回 undefined.

###### 15.2.1.16.2.1 InnerModuleEvaluation ( module, stack, index ) <div id="sec-innermoduleevaluation"></div>

Evaluate使用InnerModuleEvaluation抽象操作来执行源文本模块记录module的实际解释执行过程，并递归地处理依赖关系图中的所有其他模块。堆栈和索引参数以及模块的[[DFSIndex]]和[[DFSAncestoreIndex]]字段的使用方式与InnerModuleInstantiation相同。

这个抽象操作执行以下步骤：

1. 若 module 不是 Cyclic 模块记录项，那么
1. 执行 ? module.Evaluate().
2. 返回 index.
2. 若 module.[[Status]] 是 "evaluated"，那么
  1. 若 module.[[EvaluationError]] 是 undefined，返回 index.
  2. 否则，返回 module.[[EvaluationError]].
3. 若 module.[[Status]] 是 "evaluating"，返回 index.
4. 断言: module.[[Status]] 是 "instantiated".
5. 设置 module.[[Status]] 为 "evaluating".
6. 设置 module.[[DFSIndex]] 为 index.
7. 设置 module.[[DFSAncestorIndex]] 为 index.
8. index 增加 1.
9. 将 module 添加到 stack.
10. 对于每个需要的module.[[RequestedModules]]元素, 执行
    1. 令 requiredModule 为 ! HostResolveImportedModule(module, required).
    2. 注: 在调用这个方法之前，必须成功地完成实例化，这样才能保证每个请求的模块都能成功解析。
    3. 设置 index 为 ? InnerModuleEvaluation(requiredModule, stack, index).
    4. 断言: requiredModule.[[Status]] 是 "evaluating" 或 "evaluated".
    5. 断言: requiredModule.[[Status]] 是 "evaluating" 当且仅当 requiredModule 在 stack 中.
    6. 若 requiredModule.[[Status]] 是 "evaluating"，那么
       1. 断言: requiredModule 是 Cyclic 模块记录项。
       2. 设置 module.[[DFSAncestorIndex]] 为 min(module.[[DFSAncestorIndex]], requiredModule.
          [[DFSAncestorIndex]]).
11. 执行 ? module.ExecuteModule().
12. 断言:module恰好在stack中出现一次。
13. 断言: module.[[DFSAncestorIndex]] 小于等于 module.[[DFSIndex]].
14. 若 module.[[DFSAncestorIndex]] 等于 module.[[DFSIndex]]，那么
    1. 令 done 为 false.
    2. 重复, 直到 done 是 false,
       1. 令 requiredModule 为 stack 中的最后一个元素
       2. 移除 stack 的最后一个元素
       3. 设置 requiredModule.[[Status]] 为 "evaluated".
       4. 若 requiredModule 和 module 的模块记录项相同, 设置 done 为 true.
15. 返回 index

##### 15.2.1.16.3 循环模块记录示例图 <div id="sec-example-cyclic-module-record-graphs"></div>

本非规范性部分提供了一些示例和一些常见模块图的解释执行的示例，并特别关注错误的发生方式。

首先考虑以下简单模块图：

图2：一个简单的模块图

首先，假设没有错误条件。当主机首先调用 A.Instantiate() 时，这将通过假设成功完成，并递归实例化模块B和C，使得A.[[Status]] = B.[[Status]] = C.[[Status]] =“instantiated”。该准备步骤可以随时执行。稍后，当主机准备好承担模块的任何可能的副作用时，它可以调用A.Evaluate()，该操作将成功完成（再次假设），递归地首先解析执行C，然后解析执行B。每个模块的[[Status]]此时将被“evaluated”。

然后考虑涉及实例化错误的情况。若C的InnerModuleInstantiation成功但此后对B失败，例如因为它导入了C无法提供的内容，则原始 A.Instantiate() 将失败，并且A和B的[[Status]]都保持“uninstantiated” 。但是，C的[[Status]]已“instantiated”。

最后，考虑涉及解析执行错误的情况。若C的InnerModuleEvaluation成功但之后对B失败，例如，因为B包含引发异常的代码，则原始A.Evaluate() 将失败。结果异常将记录在A和B的[[EvaluationError]]字段中，并且它们的[[Status]]将变为“evaluated”。C也将变为“evaluated”，但与A和B相比，它将保持不包含一个状态[[EvaluationError]]，因为它成功完成了解析执行。存储异常可确保主机每次通过调用其Evaluate（）方法尝试重用A或B时，都会遇到相同的异常（不需要主机重用循环模块记录；类似地，不需要主机公开该异常这些方法抛出的对象。但是，规范允许此类使用。）

实例化错误和解析执行错误之间的差异是由于解析执行仅能执行一次的原因，因为解析执行会导致副作用。因此，重要的是要记住解析执行是否已经执行，即使解析执行未成功。 （在错误情况下，还记得该异常是有意义的，因为否则，随后的Evaluate() 调用将不得不合成一个新的异常。）另一方面，实例化是无副作用的，因此即使失败了，以后可以重试而不会出现任何问题

现在考虑另一种错误条件：

图3：带有不可解析模块的模块图

在这种情况下，模块A声明了对某个其他模块的依赖关系，但是该模块不存在任何模块记录，即HostResolveImportedModule在被询问时引发异常。发生这种情况的原因可能多种多样，例如不存在相应的资源，或者存在现有资源，但ParseModule在尝试解析结果源文本时抛出异常。主机可以选择通过从HostResolveImportedModule引发的异常来揭示失败原因。在任何情况下，此异常都会导致实例化失败，这与以前一样导致A的[[Status]]保持“uninstantiated”。

最后，考虑带有循环的模块图：

图4：循环模块图

在这里，我们假设入口点是模块A，以便主机通过调用 A.Instantiate() 进行操作，该A.Instantiate()在A上执行InnerModuleInstantiation。这又在B上调用InnerModuleInstantiation。由于周期的缘故，这再次触发了A上的InnerModuleInstantiation，但是由于A.[[Status]]已经在“instantiating”，因此这是空操作。当控制权返回到A并且在C上触发InnerModuleInstantiation时，B.[[Status]]本身保持“instantiating”。在C.[[Status]]返回“instantiated”之后，A和B一起从“instantiating”过渡到“instantiated”。这是设计使然，因为它们形成了牢固连接的组件。

在成功的情况下，循环模块图的解析执行阶段发生了类似的情况。

现在考虑A具有实例化错误的情况；例如，它尝试从C中导入一个不存在的绑定。在那种情况下，上述步骤仍然会发生，包括从第二次调用A上的InnerModuleInstantiation提前返回。但是，一旦我们退回到A上的原始InnerModuleInstantiation，它在InitializeEnvironment期间即C.ResolveExport（）之后就失败了。引发的SyntaxError异常会传播到A.Instantiate，该异常会重置其堆栈上当前存在的所有模块（这些模块始终都是仍在“instantiating”的模块）。因此，A和B都变为“uninstantiated”。请注意，C保留为“instantiated”。

最后，考虑A解析执行错误的情况；例如，其源代码会抛出异常。在那种情况下，上述步骤的解析执行时间模拟仍然会发生，包括从第二个调用提前返回到A上的InnerModuleEvaluation。但是，一旦我们退回到A上的原始InnerModuleEvaluation，它就会由于假设而失败。引发的异常会传播到A.Evaluate（），该异常会在当前堆栈中的所有模块（即仍在“evaluating”中的模块）中记录错误。因此，A和B都变为“evaluated”，并且异常记录在A和B的[[EvaluationError]]字段中，而C保留为“evaluated”，没有[[EvaluationError]]。

#### 15.2.1.17 源文本模块记录 <div id="sec-source-text-module-records"></div>

源文本模块记录用于表示有关由ECMAScript源文本（10）定义的模块的信息，该文本使用目标符号模块进行了解析。其字段包含有关模块导入的名称的摘要信息，其具体方法使用此摘要链接，实例化和解析执行模块。

源文本模块记录可以与抽象模块记录类型的其他子类一起存在于模块图中，并且可以与循环模块记录类型的其他子类一起参与循环。

除了表39中定义的字段之外，源文本模块记录还有表41中列出的其他字段。这些字段中的每个字段最初都是在ParseModule中设置的。

表41：源文本模块记录的其他字段

| 字段名                | 值类型                                                   | 意义                                                      |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[ECMAScriptCode]]        | 解析节点                                                 | 使用Module作为目标符号解析此模块的源文本的结果 |
| [[ImportEntries]]         | ImportEntry 记录项的列表 | 从此模块的代码派生的ImportEntry记录列表。 |
| [[LocalExportEntries]]    |  ExportEntry 记录项的列表 | 从此模块的代码派生的ExportEntry记录的列表，这些记录与模块中发生的声明相对应。 |
| [[IndirectExportEntries]] | ExportEntry 记录项的列表 | 从此模块的代码派生的ExportEntry记录列表，这些记录与模块中发生的重新导出的导入相对应。 |
| [[StarExportEntries]]     | ExportEntry  记录项的列表 | 从此模块的代码派生的ExportEntry记录列表，与该模块内发生的export *声明相对应。 |

ImportEntry记录是一种记录，用于摘要有关单个声明性导入的信息。每个ImportEntry记录均具有表42中定义的字段：

表42：ImportEntry记录字段

| 字段名        | 值类型 | 意义                                                     |
| ----------------- | ---------- | ------------------------------------------------------------ |
| [[ModuleRequest]] | String     | ImportDeclaration的ModuleSpecifier的字符串值。 |
| [[ImportName]]    | String     | 由[[ModuleRequest]]标识的模块导出所需绑定的名称。值`"*"`表示导入请求是针对目标模块的名称空间对象的。 |
| [[LocalName]]     | String     | 用于从导入模块内部本地访问导入值的名称。 |

>注 1：表43给出了用于表示语法导入形式的ImportEntry记录字段的示例：
>
>| Import 声明格式               | [[ModuleRequest]] | [[ImportName]] | [[LocalName]] |
>| ----------------------------- | ----------------- | -------------- | ------------- |
>| `import v from "mod";`        | `"mod"`           | `"default"`    | `"v"`         |
>| `import * as ns from "mod";`  | `"mod"`           | `"*"`          | `"ns"`        |
>| `import {x} from "mod";`      | `"mod"`           | `"x"`          | `"x"`         |
>| `import {x as v} from "mod";` | `"mod"`           | `"x"`          | `"v"`         |
>| `import "mod";`               | 没有创建          | ImportEntry    | 记录。        |

ExportEntry记录是一个记录，它摘要了有关单个声明性导出的信息。每个ExportEntry Record都有表44中定义的字段：

| 字段名            | 值类型         | 意义                                                         |
| ----------------- | -------------- | ------------------------------------------------------------ |
| [[ExportName]]    | String         | 此模块用于导出此绑定的名称。                                 |
| [[ModuleRequest]] | String \| null | 导出声明的ModuleSspecifier的字符串值。若导出声明没有Module Specifier，则为NULL。 |
| [[ImportName]]    | String \| null | 由[[Module Request]]标识的模块导出所需绑定的名称。若导出声明没有Module Specifier，则为NULL。“*”表示出口请求为所有出口绑定。 |
| [[LocalName]]     | String \| null | 用于从导入模块内本地访问导出值的名称。若导出的值不能从模块内本地访问，则为NULL。 |

>注 2：表45给出了用于表示语法导出表单的ExportEntry记录字段的示例：
>
>表45（信息）：导出表格
>
>| Export 声明格式                 | [[ExportName]] | [[ModuleRequest]] | [[ImportName]] | [[LocalName]] |
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

以下定义规定了源文本模块记录所需的具体方法和其他抽象操作

##### 15.2.1.17.1 ParseModule ( sourceText, realm, hostDefined ) <div id="sec-parsemodule"></div>

具有参数sourceText，realm和hostDefined的抽象操作ParseModule基于将sourceText作为模块进行解析的结果来创建源文本模块记录。 ParseModule执行以下步骤：

1. 断言: sourceText是ECMAScript源文本（请参见第10节）
2. 使用Module作为目标符号来解析sourceText并分析任何Early Error条件的解析结果。若解析成功并且未发现早期错误，则将body作为结果解析树。否则，将body作为代表解析错误和/或早期错误的一个或多个SyntaxError或ReferenceError对象的列表。解析和早期错误检测可以以依赖于实现的方式进行交织。若存在多个解析错误或早期错误，则列表中错误对象的数量和顺序与实现有关，但必须至少存在一个。
3. 若 body 是 errors 的列表，返回 body.
4. 令 requestedModules 为 body 的 ModuleRequests
5. 令 importEntries 为 body 的 ImportEntries
6. 令 importedBoundNames 为 ImportedLocalNames(importEntries).
7. 令 indirectExportEntries 为新的空列表.
8. 令 localExportEntries 为新的空列表.
9. 令 starExportEntries 为新的空列表.
10. 令 exportEntries 为 body 的 ExportEntries
11. 对于在exportEntries中的每一个ExportEntry 记录项 ee，执行
    1. 若 ee.[[ModuleRequest]] 是 null，那么
       1. 若 ee.[[LocalName]] 不是 importedBoundNames 的元素，那么
          1. 将 ee 添加到 localExportEntries.
       2. 否则，
         1. 令ie为importEntries的元素，该元素的[[LocalName]]与ee.[[LocalName]]相同。
         2. 若 ie.[[ImportName]] 是 "*"，那么
               1. 断言: 这是导入的模块名称空间对象的重新导出。
                   2. 将 ee 添加到 localExportEntries.
         3. 否则这是单个名称的重新导出，
               1. 添加 ExportEntry Record { [[ModuleRequest]]: ie.[[ModuleRequest]],
             [[ImportName]]: ie.[[ImportName]], [[LocalName]]: null, [[ExportName]]: ee.
             [[ExportName]] } to indirectExportEntries.
    2. 否则若 ee.[[ImportName]] 是 "*"，那么
      1. 将 ee 添加到 starExportEntries.
    3. 否则，
      1. 将 ee 添加到 indirectExportEntries.
12. 返回 源文本模块记录项 { [[Realm]]: realm, [[Environment]]: undefined, [[Namespace]]: undefined, [[Status]]: "uninstantiated", [[EvaluationError]]: undefined, [[HostDefined]]: hostDefined,[[ECMAScriptCode]]: body, [[RequestedModules]]: requestedModules, [[ImportEntries]]: importEntries, [[LocalExportEntries]]: localExportEntries, [[IndirectExportEntries]]: indirectExportEntries, [[StarExportEntries]]: starExportEntries, [[DFSIndex]]: undefined, [[DFSAncestorIndex]]: undefined }.

> 注：一个实现可以解析模块源文本，并在为该模块源文本解析执行ParseModule之前针对早期错误条件对其进行分析。但是，任何错误的报告都必须推迟到该规范对该源文本实际执行ParseModule为止。

##### 15.2.1.17.2 GetExportedNames ( exportStarSet ) Concrete Method <div id="sec-getexportednames"></div>

源文本模块记录的GetExportedNames具体方法实现了相应的模块记录抽象方法。

它执行以下步骤：

1. 令 module 为此源文本模块记录。
2. 若 exportStarSet contains module，那么
1. 断言: 我们已经达到了 import * 循环的起点
2. 返回新的空列表.
3. 将 module 添加到 exportStarSet.
4. 令 exportedNames 为新的空列表.
5. 对于在module.[[LocalExportEntries]]中的每一个ExportEntry 记录 e，执行
  1. 断言: 模块为此导出提供直接绑定。
  2. 将 e.[[ExportName]] 添加到 exportedNames.
6. 对于在module.[[IndirectExportEntries]]中的每一个ExportEntry 记录 e，执行
  1. 断言: 模块为此导出导入特定的绑定。
  2. 将 e.[[ExportName]] 添加到 exportedNames.
7. 对于在module.[[StarExportEntries]]中的每一个ExportEntry 记录 e，执行
  1. 令 requestedModule 为 ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
  2. 令 starNames 为 ? requestedModule.GetExportedNames(exportStarSet).
  3. 对于每一个 starNames 的 n，执行
      1. 若 SameValue(n, "default") 是 false, 那么
            1. 若 n 不是 exportedNames 的元素，那么
              1. 将 n 添加到 exportedNames.
8. 返回 exportedNames.

> 注：GetExported Names不会筛选出或抛出具有模糊星型导出绑定的名称的异常。

##### 15.2.1.17.3 ResolveExport ( exportName, resolveSet ) Concrete Method <div id="sec-resolveexport"></div>

源文本模块记录的解决输出具体方法实现了相应的模块记录抽象方法。

解析Export尝试解析导入的绑定到实际定义模块和本地绑定名称。定义模块可以是由模块记录表示的模块，该方法是在该模块上调用的，也可以是由该模块导入的其他模块。参数解析集用于检测未解决的循环导入/导出路径。若到达由特定模块记录和exportName组成的对，并且已经在solutionSet中，则已经遇到了导入循环。在递归调用ResolveExport之前，添加一对由模块和exportName组成的resolveSet。

若找到定义模块，则返回一个ResolvedBinding Record {[Module]，[[BindingName]]}。此记录标识原始请求导出的解析绑定。若没有找到定义或发现请求是循环的，则返回NULL。若发现请求是模糊的，则返回字符串“ambiguous”。

此抽象方法执行以下步骤：

1. 令 module 为此源文本模块记录。
2. 对于在resolveSet中的每一个Record { [[Module]], [[ExportName]] } r，执行
1. 若模块和r.[[Module]]是相同的模块记录，并且SameValue（exportName，r.[[ExportName]]）为true，则
  1. 断言: 这是一个循环导入请求。
  2. 返回 null.
3. 将 Record { [[Module]]: module, [[ExportName]]: exportName } 添加到 resolveSet.
4. 对于在module.[[LocalExportEntries]]中的每一个ExportEntry 记录 e，执行
  1. 若 SameValue(exportName, e.[[ExportName]]) 是 true，那么
        1. 断言: module provides the direct binding for this export.
            2. 返回 ResolvedBinding Record { [[Module]]: module, [[BindingName]]: e.[[LocalName]] }.
5. 对于在module.[[IndirectExportEntries]]中的每一个ExportEntry 记录 e，执行
  1. 若 SameValue(exportName, e.[[ExportName]]) 是 true，那么
        1. 断言: module imports a specific binding for this export.
            2. 令 importedModule 为 ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
                3. 返回 importedModule.ResolveExport(e.[[ImportName]], resolveSet).
6. 若 SameValue(exportName, "default") 是 true，那么
  1. 断言: 此模块未明确定义默认导出。
  2. 返回 null.
  3. 注：export*无法提供 default export。
7. 令 starResolution 为 null.
8. 对于在module.[[StarExportEntries]]中的每一个ExportEntry 记录 e，执行
   1. 令 importedModule 为 ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
   2. 令 resolution 为 ? importedModule.ResolveExport(exportName, resolveSet).
   3. 若 resolution 是 "ambiguous"，返回 "ambiguous".
   4. 若 resolution 不是 null，那么
      1. 断言: resolution 是 a ResolvedBinding Record.
      2. 若 starResolution 是 null，设置 starResolution 为 resolution.
      3. 否则，
         1. 断言：一个以上的*import包含所请求的名称
         2. 若 resolution.[[Module]] 和 starResolution.[[Module]]  Module 记录不相同，或
             SameValue(resolution.[[BindingName]], starResolution.[[BindingName]]) 是 false，返回"ambiguous".
9. 返回 starResolution.

##### 15.2.1.17.4 InitializeEnvironment ( ) Concrete Method <div id="sec-source-text-module-record-initialize-environment"></div>

源文本模块记录的InitializeEnvironment具体方法实现了相应的循环模块记录抽象方法。

 此抽象方法执行以下步骤：

1. 令 module 为此源文本模块记录。
2. 对于在module.[[IndirectExportEntries]]中的每一个ExportEntry 记录 e，执行
1. 令 resolution 为 ? module.ResolveExport(e.[[ExportName]], « »).
2. 若 resolution 是 null 或 "ambiguous", 抛出 SyntaxError 异常
3. 断言: resolution 是 ResolvedBinding 记录项.
3. 断言: 模块中所有已命名的导出都是可解析的。
4. 令 realm 为 module.[[Realm]].
5. 断言: realm 不是 undefined.
6. 令 env 为 NewModuleEnvironment(realm.[[GlobalEnv]]).
7. 设置 module.[[Environment]] 为 env.
8. 令 envRec 为 env的 EnvironmentRecord.
9. 对于在module.[[ImportEntries]]中的每一个ImportEntry 记录 in，执行
  1. 令 importedModule 为 ! HostResolveImportedModule(module, in.[[ModuleRequest]]).
  2. 注: 上面的调用不会失败，因为导入的模块请求是module.[[RequestedModules]]的子集，并且这些已在此算法中得到了较早的解决。
  3. 若 in.[[ImportName]] 是 "*"，那么
        1. 令 namespace 为 ? GetModuleNamespace(importedModule).
            2. 执行 ! envRec.CreateImmutableBinding(in.[[LocalName]], true).
                3. 调用 envRec.InitializeBinding(in.[[LocalName]], namespace).
  4. 否则，
        1. 令 resolution 为 ? importedModule.ResolveExport(in.[[ImportName]], « »).
            2.  若 resolution 是 null 或 "ambiguous", 抛出 SyntaxError 异常
                3. 调用 envRec.CreateImportBinding(in.[[LocalName]], resolution.[[Module]], resolution.
      [[BindingName]]).
10. 令 code 为 module.[[ECMAScriptCode]].
11. 令 varDeclarations 为 code 的 VarScopedDeclarations
12. 令 declaredVarNames 为新的空列表.
13. 对于在varDeclarations中的每一个element d，执行
    1. 对于在d的BoundNames的每一个元素dn，执行
       1. 若 dn 不是 declaredVarNames 的元素，那么
          1. 执行 ! envRec.CreateMutableBinding(dn, false).
          2. 调用 envRec.InitializeBinding(dn, undefined).
          3. 将 dn 添加到 declaredVarNames.
14. 令 lexDeclarations 为 code 的 LexicallyScopedDeclarations
15. 对于在lexDeclarations中的每一个元素d，执行
    1. 对于d 的 BoundNames的每一个元素d，执行
       1. 若 d的IsConstantDeclaration 是 true，那么
       2. 执行 ! envRec.CreateImmutableBinding(dn, true).
    2. 否则，
      1. 执行 ! envRec.CreateMutableBinding(dn, false).
    3. 若 d 是 FunctionDeclaration, GeneratorDeclaration, AsyncFunctionDeclaration, 或
        AsyncGeneratorDeclaration，那么
      1. 令fo为使用参数env为d执行InstantiateFunctionObject的结果
      2. 调用 envRec.InitializeBinding(dn, fo).
16. 返回 NormalCompletion(empty).

##### 15.2.1.17.5 ExecuteModule ( ) Concrete Method <div id="sec-source-text-module-record-execute-module"></div>

源文本模块记录的ExecuteModule具体方法实现了相应的循环模块记录抽象方法。

此抽象方法执行以下步骤：

1. 令 module 为此源文本模块记录
2. 令 moduleCxt 为新的ECMAScript代码执行上下文。
3. 设置 moduleCxt 的 Function 为 null.
4. 断言: module.[[Realm]] 不是 undefined.
5. 设置 moduleCxt 的 Realm 为 module.[[Realm]].
6. 设置 moduleCxt 的 ScriptOrModule 为 module.
7. 断言: 已链接模块，并且已在其模块环境中实例化了声明。
8. 设置 moduleCxt 的 VariableEnvironment 为 module.[[Environment]].
9. 设置 moduleCxt 的 LexicalEnvironment 为 module.[[Environment]].
10. 挂起当前正在运行的执行上下文。
11. 将moduleCxt推入执行上下文堆栈；现在，moduleCxt是正在运行的执行上下文。
12. 令 result 为解析执行 module.[[ECMAScriptCode]] 的结果
13. 挂起moduleCxt并将其从执行上下文堆栈中删除。
14. 将现在位于执行上下文堆栈顶部的上下文恢复为运行中的执行上下文。
15. 返回 Completion(result).

#### 15.2.1.18 运行时语义：HostResolveImportedModule ( referencingModule, specifier ) <div id="sec-hostresolveimportedmodule"></div>

HostResolveImportedModule是一个实现定义的抽象操作，它提供与ModuleSpecifier字符串（说明符）相对应的具体Module Record子类实例，该实例出现在Module Record referenceencingModule表示的模块的上下文中。

HostResolveImportedModule的实现必须符合以下要求：

- 正常返回值必须是Module Record的具体子类的实例。
- 若对应于对referencelingModule对的模块记录，说明符不存在或无法创建，则必须引发异常。若该操作正常完成，则必须是幂等的。
- 每次使用特定的referencecingModule，说明符对作为参数调用时，它必须返回相同的Module Record实例。

多个不同的referencelingModule，说明符对可以映射到同一Module Record实例。实际的映射语义是实现定义的，但通常将规范化过程作为映射过程的一部分应用于说明符。典型的规范化过程将包括诸如字母大小写折叠以及相对和缩写路径说明符扩展的动作。

#### 15.2.1.19 运行时语义：GetModuleNamespace ( module ) <div id="sec-getmodulenamespace"></div>

GetModuleNamespace抽象操作检索表示模块导出的Module Namespace Exotic对象，在第一次请求时延迟创建它，并将其存储在module。[[Namespace]]中，以备将来检索。

此抽象操作执行以下步骤：

1. 断言: module 是Module Record的具体子类的实例。
2. 断言: module.[[Status]] 不是 "uninstantiated".
3. 令 namespace 为 module.[[Namespace]].
4. 若 namespace 是 undefined，那么
   1. 令 exportedNames 为 ? module.GetExportedNames(« »).
   2. 令 unambiguousNames 为新的空列表
   3. 对于每个是exportNames元素的names, 执行
      1. 令 resolution 为 ? module.ResolveExport(name, « »).
      2. 如果resolution是ResolvedBinding Record，则将名称附加到unambiguousNames。
   4.  设置namespace为ModuleNamespaceCreate（module，unambiguousNames）。
5. 令 unambiguousNames 为新的空列表.
6. 对于每个是exportNames元素的名称，执行
  7. 令 resolution 为 ? module.ResolveExport(name, « »).
  8. 若 resolution 是 ResolvedBinding 记录, 将 name 添加到 unambiguousNames.
9. 设置 namespace 为 ModuleNamespaceCreate(module, unambiguousNames).
10. 返回 namespace.

> 注：GetModuleNamespace抛出的唯一方法是通过触发的HostResolveImportedModule调用之一。此时，无法解析的名称仅从名称空间中排除。除非它们都是模棱两可的恒星输出，并且在任何地方都没有明确要求，否则它们将导致实际实例化错误。

#### 15.2.1.20 运行时语义：TopLevelModuleEvaluationJob ( sourceText, hostDefined ) <div id="sec-toplevelmoduleevaluationjob"></div>

参数sourceText和hostDefined的TopLevelModuleEvaluationJob是一项将SourceText作为模块进行解析，验证和解析执行的工作。

1. 断言: sourceText 是ECMAScript源文本（请参见第10节）。
2. 令 realm 为当前 Realm 记录项.
3. 令 m 为 ParseModule(sourceText, realm, hostDefined).
4. 若 m 是 errors 的列表，那么
   1. 执行 HostReportErrors(m).
   2. 返回 NormalCompletion(undefined).
5. 执行 ? m.Instantiate().
6. 断言: m的所有依赖关系都已通过传递方式解决，并且m可供解析执行
7. 返回 ? m.Evaluate().

> 注：一个实现可以将sourceText解析为一个模块，分析它的Early Error条件，并在对该SourceText执行TopLevelModuleEvaluationJob之前实例化它。实现也可以解析，预先分析和预先分析以及预先实例化sourceText的模块依赖性。但是，必须推迟报告由这些操作检测到的任何错误，直到实际执行TopLevelModuleEvaluationJob为止。

#### 15.2.1.21 运行时语义：Evaluation <div id="sec-module-semantics-runtime-semantics-evaluation"></div>

```
Module : [empty]
```

1. 返回 NormalCompletion(undefined).

```
ModuleBody : ModuleItemList
```

1. 令 result 为解析执行ModuleItemList的结果。
2. 若 result.[[Type]] 是 normal，并且 result.[[Value]] 是 empty，那么
   1. 返回 NormalCompletion(undefined).
3. 返回 Completion(result).

```
ModuleItemList : ModuleItemList ModuleItem
```

1. 令 sl 为解析执行ModuleItemList的结果。
2. ReturnIfAbrupt(sl).
3. 令 s 为解析执行ModuleItem的结果。
4. 返回 Completion(UpdateEmpty(s, sl)).

> 注：ModuleItemList的值是ModuleItemList中最后一个产生值的项目的值。

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

#### 15.2.2.1 静态语义：Early Errors <div id="sec-imports-static-semantics-early-errors"></div>

```
ModuleItem : ImportDeclaration
```

若ImportDeclaration的BoundNames包含任何重复的条目，则是语法错误。

#### 15.2.2.2 静态语义：BoundNames <div id="sec-imports-static-semantics-boundnames"></div>

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
2. 在 names 后追加 NameSpaceImport 的 BoundNames
3. 返回 names.

```
ImportClause : ImportedDefaultBinding , NamedImports
```

1. 令 names 为 ImportedDefaultBinding 的 BoundNames
2. 在 names 后追加 NamedImports 的 BoundNames
3. 返回 names.

```
NamedImports : { }
```

1. 返回新的空列表

```
ImportsList : ImportsList , ImportSpecifier
```

1. 令 names 为 ImportsList 的 BoundNames
2. 在 names 后追加 ImportSpecifier 的 BoundNames
3. 返回 names.

```
ImportSpecifier : IdentifierName as ImportedBinding
```

1. 返回 the BoundNames of ImportedBinding

#### 15.2.2.3 静态语义：ImportEntries <div id="sec-imports-static-semantics-importentries"></div>

```
ImportDeclaration : import ImportClause FromClause ;
```

1. 令 module 为FromClause的ModuleRequests的唯一元素。
2. 返回带有参数 modul 的 ImportClause 的 ImportEntriesForModule

```
ImportDeclaration : import ModuleSpecifier ;
```

1. 返回新的空列表.

#### 15.2.2.4 静态语义：ImportEntriesForModule <div id="sec-static-semantics-importentriesformodule"></div>

带参数 module。

```
ImportClause : ImportedDefaultBinding , NameSpaceImport
```

1. 令 entries 为带有参数 module 的 ImportedDefaultBinding 的 ImportEntriesForModule
2. 在 entries 后追加 带有参数 module 的 NameSpaceImport 的 ImportEntriesForModule
3. 返回 entries.

```
ImportClause : ImportedDefaultBinding , NamedImports
```

1. 令 entries 为带有参数 module 的 ImportedDefaultBinding 的 ImportEntriesForModule
2. 在 entries 后追加 带有参数 module 的 NamedImports 的 ImportEntriesForModule
3. 返回 entries

```
ImportedDefaultBinding : ImportedBinding
```

1. 令 localName 为 ImportedBinding 的 BoundNames 的唯一元素
2. 令 defaultEntry 为 the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "default",
[[LocalName]]: localName }.
3. 返回一个包含defaultEntry的新列表。

```
NameSpaceImport : * as ImportedBinding
```

1. 令 localName 为 ImportedBinding 的 StringValue
2. 令 entry 为 the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "*", [[LocalName]]:
localName }.
3. 返回一个包含entry的新列表。

```
NamedImports : { }
```

1. 返回新的空列表.

```
ImportsList : ImportsList , ImportSpecifier
```

1. 令 specs 为 the带有参数 module 的 ImportsList 的 ImportEntriesForModule
2. 在 specs 后追加 带有参数 module 的 ImportSpecifier 的 ImportEntriesForModule
3. 返回 specs.

```
ImportSpecifier : ImportedBinding
```

1. 令 localName 为 ImportedBinding 的 BoundNames 的单个元素
2. 令 entry 为 ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: localName, [[LocalName]]: localName }.
3. 返回一个包含entry的新列表。

```
ImportSpecifier : IdentifierName as ImportedBinding
```

1. 令 importName 为 IdentifierName 的 StringValue
2. 令 localName 为 ImportedBinding 的 StringValue
3. 令 entry 为 the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]: localName }.
4. 返回一个包含entry的新列表。

#### 15.2.2.5 静态语义：ModuleRequests <div id="sec-imports-static-semantics-modulerequests"></div>

```
ImportDeclaration : import ImportClause FromClause ;
```

1. 返回 FromClause 的 ModuleRequests

```
ModuleSpecifier : StringLiteral
```

1. 返回包含 StringLiteral 的 StringValue 的列表

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

#### 15.2.3.1 静态语义：Early Errors <div id="sec-exports-static-semantics-early-errors"></div>

```
ExportDeclaration : export ExportClause ;
```

对于ExportClause的ReferencedBindings中的每个IdentifierName n：若n的StringValue是ReservedWord或n的StringValue是以下之一，则为语法错误："implements", "interface", "let", "package", "private", "protected", "public", or "static".

> 注：上面的规则意味着ExportClause的每个ReferencedBindings都被视为IdentifierReference。

#### 15.2.3.2 静态语义：BoundNames <div id="sec-exports-static-semantics-boundnames"></div>

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
2. 若clarificationNames不包含元素"* default *"，则将" * default *"附加到clarificationNames。
3. 返回 declarationNames.

```
ExportDeclaration : export default ClassDeclaration
```

1. 令 declarationNames 为 ClassDeclaration 的 BoundNames
2. 若 declarationNames 不包含 "\*default\*" 元素, 将 "\*default\*" 添加到 declarationNames.
3. 返回 declarationNames.

```
ExportDeclaration : export default AssignmentExpression ;
```

1. 返回 « "\*default\*" ».

#### 15.2.3.3 静态语义：ExportedBindings <div id="sec-exports-static-semantics-exportedbindings"></div>

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
2. 在 names 后追加 ExportSpecifier 的 ExportedBindings
3. 返回 names.

```
ExportSpecifier : IdentifierName
```

1. 返回包含IdentifierName的StringValue的列表

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. 返回包含第一个IdentifierName的StringValue的列表

#### 15.2.3.4 静态语义：ExportedNames <div id="sec-exports-static-semantics-exportednames"></div>

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
2. 在 names 后追加 ExportSpecifier 的 ExportedNames
3. 返回 names

```
ExportSpecifier : IdentifierName
```

1. 返回包含 IdentifierName 的 StringValue 的列表

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. 返回一个包含第二个IdentifierName的StringValue的列表。

#### 15.2.3.5 静态语义：ExportEntries <div id="sec-exports-static-semantics-exportentries"></div>

```
ExportDeclaration : export * FromClause ;
```

1. 令 module 为FromClause的ModuleRequests的唯一元素。
2. 令 entry 为 the ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "*", [[LocalName]]: null, [[ExportName]]: null }.
3. 返回一个包含entry的新列表。

```
ExportDeclaration : export ExportClause FromClause ;
```

1. 令 module 为FromClause的ModuleRequests的唯一元素。
2. 返回带有参数 module 的 ExportClause 的 ExportEntriesForModule

```
ExportDeclaration : export ExportClause ;
```

1. 返回带有参数 null 的 ExportClause 的 ExportEntriesForModule

```
ExportDeclaration : export VariableStatement
```

1. 令 entries 为新的空列表.
2. 令 names 为 VariableStatement 的 BoundNames
3. 对于在names中的每一个name，执行
   1. 添加 the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]: name, [[ExportName]]: name } 到 entries.
4. 返回 entries.

```
ExportDeclaration : export Declaration
```

1. 令 entries 为新的空列表.
2. 令 names 为 Declaration 的 BoundNames
3. 对于在names中的每一个name，执行
   1. 将 ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]: name, [[ExportName]]: name } 添加到 entries.
4. 返回 entries

```
ExportDeclaration : export default HoistableDeclaration
```

1. 令 names 为 HoistableDeclaration 的 BoundNames
2. 令 localName 为names的唯一元素。
3. 返回包含 ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null,
[[LocalName]]: localName, [[ExportName]]: "default" } 的新列表

```
ExportDeclaration : export default ClassDeclaration
```

1. 令 names 为 ClassDeclaration 的 BoundNames
2. 令 localName 为names的唯一元素。
3. 返回包含 ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null,
[[LocalName]]: localName, [[ExportName]]: "default" } 的新列表

```
ExportDeclaration : export default AssignmentExpression ;
```

1. 令 entry 为 ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]:
"\*default\*", [[ExportName]]: "default" }.
2. 返回一个包含entry的新列表。

> 注：在本规范中，“ * default *”用作匿名默认导出值的综合名称。

#### 15.2.3.6 静态语义：ExportEntriesForModule <div id="sec-static-semantics-exportentriesformodule"></div>

带参数 module。

```
ExportClause : { }
```

1. 返回新的空列表.

```
ExportsList : ExportsList , ExportSpecifier
```

1. 令 specs 为 the带有参数 module 的 ExportsList 的 ExportEntriesForModule
2. 在 specs 后追加 带有参数 module 的 ExportSpecifier 的 ExportEntriesForModule
3. 返回 specs.

```
ExportSpecifier : IdentifierName
```

1. 令 sourceName 为 IdentifierName 的 StringValue
2. 若 module 是 null，那么
   1. 令 localName 为 sourceName.
   2. 令 importName 为 null.
3. 否则，
     1. 令 localName 为 null.
     2. 令 importName 为 sourceName.
4. 返回包含 ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]: localName, [[ExportName]]: sourceName } 的新列表

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. 令 sourceName 为第一个IdentifierName的StringValue。
2. 令 exportName 为第二个IdentifierName的StringValue。
3. 若 module 是 null，那么
    1. 令 localName 为 sourceName.
    2. 令 importName 为 null.
4. 否则，
    1. 令 localName 为 null.
    2. 令 importName 为 sourceName.
5. 返回包含 ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]: localName, [[ExportName]]: exportName } 的新列表

#### 15.2.3.7 静态语义：IsConstantDeclaration <div id="sec-exports-static-semantics-isconstantdeclaration"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
export default AssignmentExpression ;
```

1. 返回 false.

> 注：不必将 export default 的AssignmentExpression视为常量声明，因为没有语法允许分配给用于引用模块默认对象的内部绑定名称。

#### 15.2.3.8 静态语义：LexicallyScopedDeclarations <div id="sec-exports-static-semantics-lexicallyscopeddeclarations"></div>

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

1. 返回包含 Declaration 的 DeclarationPart 的新列表

```
ExportDeclaration : export default HoistableDeclaration
```

1. 返回包含 HoistableDeclaration 的 DeclarationPart 的新列表

```
ExportDeclaration : export default ClassDeclaration
```

1. 返回包含 ClassDeclaration 的新列表

```
ExportDeclaration : export default AssignmentExpression ;
```

1. 返回包含 this ExportDeclaration 的新列表

#### 15.2.3.9 静态语义：ModuleRequests <div id="sec-exports-static-semantics-modulerequests"></div>

```
ExportDeclaration : export * FromClause ;
ExportDeclaration : export ExportClause FromClause ;
```

1. 返回 FromClause 的 ModuleRequests 

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

#### 15.2.3.10 静态语义：ReferencedBindings <div id="sec-static-semantics-referencedbindings"></div>

```
ExportClause : { }
```

1. 返回新的空列表.

```
ExportsList : ExportsList , ExportSpecifier
```

1. 令 names 为 ExportsList 的 ReferencedBindings
2. 在 names 后追加 ExportSpecifier 的 ReferencedBindings
3. 返回 names.

```
ExportSpecifier : IdentifierName
```

1. 返回包含 IdentifierName 的列表

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. 返回包含第一个 IdentifierName 的列表

#### 15.2.3.11 运行时语义：Evaluation <div id="sec-exports-runtime-semantics-evaluation"></div>

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

1. 返回解析执行VariableStatement的结果

```
ExportDeclaration : export Declaration
```

1. 返回解析执行Declaration的结果

```
ExportDeclaration : export default HoistableDeclaration
```

1. 返回解析执行HoistableDeclaration的结果

```
ExportDeclaration : export default ClassDeclaration
```

1. 令 value 为 ClassDeclaration 的 BindingClassDeclarationEvaluation 的结果
2. ReturnIfAbrupt(value).
3. 令 className 为 ClassDeclaration 的 BoundNames 的单个元素
4. 若 className 是 "\*default\*"，那么
   1. 令 env 为运行时的执行上下文的 LexicalEnvironment.
   2. 执行 ? InitializeBoundName("*default*", value, env).
5. 返回 NormalCompletion(empty).

```
ExportDeclaration : export default AssignmentExpression ;
```

1. 若 IsAnonymousFunctionDefinition(AssignmentExpression) 是 true，那么
   1. 令value为使用参数“ default”为AssignmentExpression执行NamedEvaluation的结果
2. 否则，
     1. 令 rhs 为解析执行AssignmentExpression的结果
     2. 令 value 为 ? GetValue(rhs).
3. 令 env 为运行时的执行上下文的 LexicalEnvironment.
4. 执行 ? InitializeBoundName("\*default\*", value, env).
5. 返回 NormalCompletion(empty).