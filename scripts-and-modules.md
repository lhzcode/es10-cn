# 15 ECMAScript语言：脚本和模块
## 15.1 脚本 <div id="sec-function-definitions"></div>

**Syntax**

```
Script :
ScriptBodyopt
ScriptBody :
StatementList[~Yield, ~Await, ~Return]
```

### 15.1.1 SS: Early Errors <div id="sec-scripts-static-semantics-early-errors"></div>

```
Script : ScriptBody
```

It is a Syntax Error if the LexicallyDeclaredNames of ScriptBody contains any duplicate entries.

 It is a Syntax Error if any element of the LexicallyDeclaredNames of ScriptBody also occurs in the VarDeclaredNames of ScriptBody.

```
ScriptBody : StatementList
```

It is a Syntax Error if StatementList Contains super unless the source code containing super is eval code that is being processed by a direct eval. Additional early error rules for super within direct eval are defined in 18.2.1.1.
It is a Syntax Error if StatementList Contains NewTarget unless the source code containing NewTarget is eval code that is being processed by a direct eval. Additional early error rules for NewTarget in direct eval are defined in 18.2.1.1.
It is a Syntax Error if ContainsDuplicateLabels of StatementList with argument « » is true.
It is a Syntax Error if ContainsUndefinedBreakTarget of StatementList with argument « » is true.
It is a Syntax Error if ContainsUndefinedContinueTarget of StatementList with arguments « » and « » is true.

### 15.1.2 SS: IsStrict <div id="sec-static-semantics-isstrict"></div>

```
ScriptBody : StatementList
```

1. If the Directive Prologue of StatementList contains a Use Strict Directive, return true; otherwise, return false.

### 15.1.3 SS: LexicallyDeclaredNames <div id="sec-scripts-static-semantics-lexicallydeclarednames"></div>

```
ScriptBody : StatementList
```

1. Return TopLevelLexicallyDeclaredNames of StatementList

> NOTE At the top level of a Script, function declarations are treated like var declarations rather than like lexical declarations.

### 15.1.4 SS: LexicallyScopedDeclarations <div id="sec-scripts-static-semantics-lexicallyscopeddeclarations"></div>

```
ScriptBody : StatementList
```

1. Return TopLevelLexicallyScopedDeclarations of StatementList

### 15.1.5 SS: VarDeclaredNames <div id="sec-scripts-static-semantics-vardeclarednames"></div>

```
ScriptBody : StatementList
```

1. Return TopLevelVarDeclaredNames of StatementList

### 15.1.6 SS: VarScopedDeclarations <div id="sec-scripts-static-semantics-varscopeddeclarations"></div>

```
ScriptBody : StatementList
```

1. Return TopLevelVarScopedDeclarations of StatementList

### 15.1.7 RS: Evaluation <div id="sec-script-semantics-runtime-semantics-evaluation"></div>

```
Script : [empty]
```

1. Return NormalCompletion(undefined).

### 15.1.8 脚本记录 <div id="sec-script-records"></div>

A Script Record encapsulates information about a script being evaluated. Each script record contains the fields listed in Table 36.

Table 36: Script Record Fields

| 字段名             | 值类型                                                       | 意义                                                         |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[Realm]]          | [Realm Record](#realm-record) \| undefined                   | The [realm](#realm) within which this script was created. undefined if not yet assigned. |
| [[Environment]]    | [Lexical Environment](#sec-lexical-environments) \| undefined | The [Lexical Environment](#sec-lexical-environments) containing the top level bindings for this script. This field is set when the script is instantiated. |
| [[ECMAScriptCode]] | a parse result                                               | The result of parsing the source text of this module using [Script](#prod-Script) as the goal symbol. |
| [[HostDefined]]    | Any, default value is undefined.                             | Field reserved for use by host environments that need to associate additional information with a script. |

### 15.1.9 ParseScript ( sourceText, realm, hostDefined ) <div id="sec-parse-script"></div>

The abstract operation ParseScript with arguments sourceText, realm, and hostDefined creates a Script Record based upon the result of parsing sourceText as a Script. ParseScript performs the following steps:

1. Assert: sourceText is an ECMAScript source text (see clause 10).
2. Parse sourceText using Script as the goal symbol and analyse the parse result for any Early Error conditions. If the parse was successful and no early errors were found, let body be the resulting parse tree. Otherwise, let body be a List of one or more SyntaxError or ReferenceError objects representing the parsing errors and/or early errors. Parsing and early error detection may be interweaved in an implementation-dependent manner. If more than one parsing error or early error is present, the number and ordering of error objects in the list is implementationdependent, but at least one must be present.
3. If body is a List of errors, return body.
4. Return Script Record { [[Realm]]: realm, [[Environment]]: undefined, [[ECMAScriptCode]]: body,[[HostDefined]]: hostDefined }.

> NOTE An implementation may parse script source text and analyse it for Early Error conditions prior to evaluation of ParseScript for that script source text. However, the reporting of any errors must be deferred until the point where this specification actually performs ParseScript upon that source text.

### 15.1.10 ScriptEvaluation ( scriptRecord ) <div id="sec-runtime-semantics-scriptevaluation"></div>

1. Let globalEnv be scriptRecord.[[Realm]].[[GlobalEnv]].
2. Let scriptCxt be a new ECMAScript code execution context.
3. Set the Function of scriptCxt to null.
4. Set the Realm of scriptCxt to scriptRecord.[[Realm]].
5. Set the ScriptOrModule of scriptCxt to scriptRecord.
6. Set the VariableEnvironment of scriptCxt to globalEnv.
7. Set the LexicalEnvironment of scriptCxt to globalEnv.
8. Suspend the currently running execution context.
9. Push scriptCxt on to the execution context stack; scriptCxt is now the running execution context.
10. Let scriptBody be scriptRecord.[[ECMAScriptCode]].
11. Let result be GlobalDeclarationInstantiation(scriptBody, globalEnv).
12. If result.[[Type]] is normal, then
a. Set result to the result of evaluating scriptBody.
13. If result.[[Type]] is normal and result.[[Value]] is empty, then
a. Set result to NormalCompletion(undefined).
14. Suspend scriptCxt and remove it from the execution context stack.
15. Assert: The execution context stack is not empty.
16. Resume the context that is now on the top of the execution context stack as the running execution context.
17. Return Completion(result).

### 15.1.11 RS: GlobalDeclarationInstantiation ( script, env ) <div id="sec-globaldeclarationinstantiation"></div>

> NOTE 1 When an execution context is established for evaluating scripts, declarations are instantiated in the current global environment. Each global binding declared in the code is instantiated.

GlobalDeclarationInstantiation is performed as follows using arguments script and env. script is the ScriptBody for which the execution context is being established. env is the global lexical environment in which bindings are to be created.

1. Let envRec be env's EnvironmentRecord.
2. Assert: envRec is a global Environment Record.
3. Let lexNames be the LexicallyDeclaredNames of script.
4. Let varNames be the VarDeclaredNames of script.
5. For each name in lexNames, do
a. If envRec.HasVarDeclaration(name) is true, throw a SyntaxError exception.
b. If envRec.HasLexicalDeclaration(name) is true, throw a SyntaxError exception.
c. Let hasRestrictedGlobal be ? envRec.HasRestrictedGlobalProperty(name).
d. If hasRestrictedGlobal is true, throw a SyntaxError exception.
6. For each name in varNames, do
a. If envRec.HasLexicalDeclaration(name) is true, throw a SyntaxError exception.
7. Let varDeclarations be the VarScopedDeclarations of script.
8. Let functionsToInitialize be a new empty List.
9. Let declaredFunctionNames be a new empty List.
10. For each d in varDeclarations, in reverse list order, do
a. If d is neither a VariableDeclaration nor a ForBinding nor a BindingIdentifier, then
i. Assert: d is either a FunctionDeclaration, a GeneratorDeclaration, an AsyncFunctionDeclaration, or an
AsyncGeneratorDeclaration.
ii. NOTE: If there are multiple function declarations for the same name, the last declaration is used.
iii. Let fn be the sole element of the BoundNames of d.
iv. If fn is not an element of declaredFunctionNames, then
1. Let fnDefinable be ? envRec.CanDeclareGlobalFunction(fn).
2. If fnDefinable is false, throw a TypeError exception.
3. Append fn to declaredFunctionNames.
4. Insert d as the first element of functionsToInitialize.
11. Let declaredVarNames be a new empty List.
12. For each d in varDeclarations, do
a. If d is a VariableDeclaration, a ForBinding, or a BindingIdentifier, then

i. For each String vn in the BoundNames of d, do
1. If vn is not an element of declaredFunctionNames, then
a. Let vnDefinable be ? envRec.CanDeclareGlobalVar(vn).
b. If vnDefinable is false, throw a TypeError exception.
c. If vn is not an element of declaredVarNames, then
i. Append vn to declaredVarNames.
13. NOTE: No abnormal terminations occur after this algorithm step if the global object is an ordinary object.
However, if the global object is a Proxy exotic object it may exhibit behaviours that cause abnormal terminations in
some of the following steps.
14. NOTE: Annex B.3.3.2 adds additional steps at this point.
15. Let lexDeclarations be the LexicallyScopedDeclarations of script.
16. For each element d in lexDeclarations, do
a. NOTE: Lexically declared names are only instantiated here but not initialized.
b. For each element dn of the BoundNames of d, do
i. If IsConstantDeclaration of d is true, then
1. Perform ? envRec.CreateImmutableBinding(dn, true).
ii. Else,
1. Perform ? envRec.CreateMutableBinding(dn, false).
17. For each Parse Node f in functionsToInitialize, do
a. Let fn be the sole element of the BoundNames of f.
b. Let fo be the result of performing InstantiateFunctionObject for f with argument env.
c. Perform ? envRec.CreateGlobalFunctionBinding(fn, fo, false).
18. For each String vn in declaredVarNames, in list order, do
a. Perform ? envRec.CreateGlobalVarBinding(vn, false).
19. Return NormalCompletion(empty).

> NOTE 2 Early errors specified in 15.1.1 prevent name conflicts between function/var declarations and let/const/class declarations as well as redeclaration of let/const/class bindings for declaration contained within a single Script. However, such conflicts and redeclarations that span more than one Script are detected as runtime errors during GlobalDeclarationInstantiation. If any such errors are detected, no bindings are instantiated for the script. However, if the global object is defined using Proxy exotic objects then the runtime tests for conflicting declarations may be unreliable resulting in an abrupt completion and some global declarations not being instantiated. If this occurs, the code for the Script is not evaluated.
>
> Unlike explicit var or function declarations, properties that are directly created on the global object result in global bindings that may be shadowed by let/const/class declarations.

### 15.1.12 RS: ScriptEvaluationJob ( sourceText, hostDefined ) <div id="sec-scriptevaluationjob"></div>

The job ScriptEvaluationJob with parameters sourceText and hostDefined parses, validates, and evaluates sourceText as a Script.

1. Assert: sourceText is an ECMAScript source text (see clause 10).
2. Let realm be the current Realm Record.
3. Let s be ParseScript(sourceText, realm, hostDefined).
4. If s is a List of errors, then
a. Perform HostReportErrors(s).
5. b. Return NormalCompletion(undefined).
  5. Return ? ScriptEvaluation(s).

## 15.2 模块 <div id="sec-modules"></div>

**Syntax**

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
StatementListItem[~Yield, ~Await, ~Return]
```



### 15.2.1 模块语义 <div id="sec-module-semantics"></div>
#### 15.2.1.1 SS: Early Errors <div id="sec-module-semantics-static-semantics-early-errors"></div>

```
ModuleBody : ModuleItemList
```

It is a Syntax Error if the LexicallyDeclaredNames of ModuleItemList contains any duplicate entries.
It is a Syntax Error if any element of the LexicallyDeclaredNames of ModuleItemList also occurs in the VarDeclaredNames of ModuleItemList.
It is a Syntax Error if the ExportedNames of ModuleItemList contains any duplicate entries.
It is a Syntax Error if any element of the ExportedBindings of ModuleItemList does not also occur in either the VarDeclaredNames of ModuleItemList, or the LexicallyDeclaredNames of ModuleItemList.
It is a Syntax Error if ModuleItemList Contains super.
It is a Syntax Error if ModuleItemList Contains NewTarget.
It is a Syntax Error if ContainsDuplicateLabels of ModuleItemList with argument « » is true.
It is a Syntax Error if ContainsUndefinedBreakTarget of ModuleItemList with argument « » is true.
It is a Syntax Error if ContainsUndefinedContinueTarget of ModuleItemList with arguments « » and « » is true.

> NOTE The duplicate ExportedNames rule implies that multiple export default ExportDeclaration items within a ModuleBody is a Syntax Error. Additional error conditions relating to conflicting or duplicate declarations are checked during module linking prior to evaluation of a Module. If any such errors are detected the Module is not evaluated.

#### 15.2.1.2 SS: ContainsDuplicateLabels <div id="sec-module-semantics-static-semantics-containsduplicatelabels"></div>

With parameter labelSet.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let hasDuplicates be ContainsDuplicateLabels of ModuleItemList with argument labelSet.

2. If hasDuplicates is true, return true.

3. Return ContainsDuplicateLabels of ModuleItem with argument labelSet.

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. Return false.

#### 15.2.1.3 SS: ContainsUndefinedBreakTarget <div id="sec-module-semantics-static-semantics-containsundefinedbreaktarget"></div>

With parameter labelSet.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let hasUndefinedLabels be ContainsUndefinedBreakTarget of ModuleItemList with argument labelSet.

2. If hasUndefinedLabels is true, return true.

3. Return ContainsUndefinedBreakTarget of ModuleItem with argument labelSet.

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. Return false.

#### 15.2.1.4 SS: ContainsUndefinedContinueTarget <div id="sec-module-semantics-static-semantics-containsundefinedcontinuetarget"></div>

With parameters iterationSet and labelSet.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let hasUndefinedLabels be ContainsUndefinedContinueTarget of ModuleItemList with arguments iterationSet and « ».

2. If hasUndefinedLabels is true, return true.

3. Return ContainsUndefinedContinueTarget of ModuleItem with arguments iterationSet and « ».

   ```
   ModuleItem :
   ImportDeclaration
   ExportDeclaration
   ```

   1. Return false.

#### 15.2.1.5 SS: ExportedBindings <div id="sec-module-semantics-static-semantics-exportedbindings"></div>

> NOTE ExportedBindings are the locally bound names that are explicitly associated with a Module's ExportedNames.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let names be ExportedBindings of ModuleItemList

2. Append to names the elements of the ExportedBindings of ModuleItem.

3. Return names

   ```
   ModuleItem :
   ImportDeclaration
   StatementListItem
   ```

   1. Return a new empty List.

#### 15.2.1.6 SS: ExportedNames <div id="sec-module-semantics-static-semantics-exportednames"></div>

> NOTE ExportedNames are the externally visible names that a Module explicitly maps to one of its local name bindings.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let names be ExportedNames of ModuleItemList.
2. Append to names the elements of the ExportedNames of ModuleItem.
3. Return names.

```
ModuleItem : ExportDeclaration
```

1. Return the ExportedNames of ExportDeclaration.

   ```
   ModuleItem :
   ImportDeclaration
   StatementListItem
   ```

   1. Return a new empty List.

#### 15.2.1.7 SS: ExportEntries <div id="sec-module-semantics-static-semantics-exportentries"></div>

```
Module : [empty]
```

1. Return a new empty List.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let entries be ExportEntries of ModuleItemList.

2. Append to entries the elements of the ExportEntries of ModuleItem.

3. Return entries.

   ```
   ModuleItem :
   ImportDeclaration
   StatementListItem
   ```

   1. Return a new empty List.

#### 15.2.1.8 SS: ImportEntries <div id="sec-module-semantics-static-semantics-importentries"></div>

```
Module : [empty]
```

1. Return a new empty List.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let entries be ImportEntries of ModuleItemList.

2. Append to entries the elements of the ImportEntries of ModuleItem.

3. Return entries

   ```
   ModuleItem :
   ExportDeclaration
   StatementListItem
   ```

   1. Return a new empty List

#### 15.2.1.9 SS: ImportedLocalNames ( importEntries ) <div id="sec-importedlocalnames"></div>

The abstract operation ImportedLocalNames with argument importEntries creates a List of all of the local name bindings defined by a List of ImportEntry Records (see Table 42). ImportedLocalNames performs the following steps:

1. Let localNames be a new empty List.
2. For each ImportEntry Record i in importEntries, do
a. Append i.[[LocalName]] to localNames.
3. Return localNames.

#### 15.2.1.10 SS: ModuleRequests <div id="sec-module-semantics-static-semantics-modulerequests"></div>

```
Module : [empty]
```

1. Return a new empty List.

```
ModuleItemList : ModuleItem
```

1. Return ModuleRequests of ModuleItem.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let moduleNames be ModuleRequests of ModuleItemList.
2. Let additionalNames be ModuleRequests of ModuleItem.
3. Append to moduleNames each element of additionalNames that is not already an element of moduleNames.
4. Return moduleNames.

```
ModuleItem : StatementListItem
```

1. Return a new empty List.

#### 15.2.1.11 SS: LexicallyDeclaredNames <div id="sec-module-semantics-static-semantics-lexicallydeclarednames"></div>

> NOTE 1 The LexicallyDeclaredNames of a Module includes the names of all of its imported bindings.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let names be LexicallyDeclaredNames of ModuleItemList.
2. Append to names the elements of the LexicallyDeclaredNames of ModuleItem.
3. Return names.

```
ModuleItem : ImportDeclaration
```

1. Return the BoundNames of ImportDeclaration

```
ModuleItem : ExportDeclaration
```

1. If ExportDeclaration is export VariableStatement, return a new empty List.
2. Return the BoundNames of ExportDeclaration.

```
ModuleItem : StatementListItem
```

1. Return LexicallyDeclaredNames of StatementListItem.

> NOTE 2 At the top level of a Module, function declarations are treated like lexical declarations rather than like var declarations.

#### 15.2.1.12 SS: LexicallyScopedDeclarations <div id="sec-module-semantics-static-semantics-lexicallyscopeddeclarations"></div>

```
Module : [empty]
```

1. Return a new empty List.

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let declarations be LexicallyScopedDeclarations of ModuleItemList.
2. Append to declarations the elements of the LexicallyScopedDeclarations of ModuleItem.
3. Return declarations.

```
ModuleItem : ImportDeclaration
```

1. Return a new empty List

#### 15.2.1.13 SS: VarDeclaredNames <div id="sec-module-semantics-static-semantics-vardeclarednames"></div>

```
Module : [empty]
```

1. Return a new empty List

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let names be VarDeclaredNames of ModuleItemList.
2. Append to names the elements of the VarDeclaredNames of ModuleItem.
3. Return names.

```
ModuleItem : ImportDeclaration
```

1. Return a new empty List.

```
ModuleItem : ExportDeclaration
```

1. If ExportDeclaration is export VariableStatement, return BoundNames of ExportDeclaration.
2. Return a new empty List.

#### 15.2.1.14 SS: VarScopedDeclarations <div id="sec-module-semantics-static-semantics-varscopeddeclarations"></div>

```
Module : [empty]
```

1. Return a new empty List

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let declarations be VarScopedDeclarations of ModuleItemList.
2. Append to declarations the elements of the VarScopedDeclarations of ModuleItem.
3. Return declarations.

```
ModuleItem : ImportDeclaration
```

1. Return a new empty List.

```
ModuleItem : ExportDeclaration
```

1. If ExportDeclaration is export VariableStatement, return VarScopedDeclarations of VariableStatement.
2. Return a new empty List.

#### 15.2.1.15 抽象模块记录 <div id="sec-abstract-module-records"></div>

A Module Record encapsulates structural information about the imports and exports of a single module. This information is used to link the imports and exports of sets of connected modules. A Module Record includes four fields that are only used when evaluating a module

For specification purposes Module Record values are values of the Record specification type and can be thought of as existing in a simple object-oriented hierarchy where Module Record is an abstract class with both abstract and concrete subclasses. This specification defines the abstract subclass named Cyclic Module Record and its concrete subclass named Source Text Module Record. Other specifications and implementations may define additional Module Record subclasses corresponding to alternative module definition facilities that they defined.

Module Record defines the fields listed in Table 37. All Module Definition subclasses include at least those fields. Module Record also defines the abstract method list in Table 38. All Module definition subclasses must provide concrete implementations of these abstract methods.

Table 37: Module Record Fields

| Field Name      | Value Type                                                   | Meaning                                                      |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[Realm]]       | [Realm Record](#realm-record) \| undefined                   | The [Realm](#realm) within which this module was created. undefined if not yet assigned. |
| [[Environment]] | [Lexical Environment](#sec-lexical-environments) \| undefined | The Lexical Environment containing the top level bindings for this module. This field is set when the module is instantiated. |
| [[Namespace]]   | Object \| undefined                                          | The Module Namespace Object ([26.3](#sec-module-namespace-objects)) if one has been created for this module. Otherwise undefined. |
| [[HostDefined]] | Any, default value is undefined.                             | Field reserved for use by host environments that need to associate additional information with a module. |

Table 38: Abstract Methods of Module Records

| Method                                               | Purpose                                                      |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| GetExportedNames(exportStarSet)                      | Return a list of all names that are either directly or indirectly exported from this module. |
| ResolveExport(exportName, resolveSet, exportStarSet) | Return the binding of a name exported by this module. Bindings are represented by a ResolvedBinding Record, of the form { [[Module]]: Module Record, [[BindingName]]: String }. Return null if the name cannot be resolved, or "ambiguous" if multiple bindings were found.<br />This operation must be idempotent if it completes normally. Each time it is called with a specific exportName, resolveSet pair as arguments it must return the same result. |
| Instantiate()                                        | Prepare the module for evaluation by transitively resolving all module dependencies and creating a module Environment Record. |
| Evaluate()                                           | If this module has already been evaluated successfully, return undefined; if it has already been evaluated unsuccessfully, throw the exception that was produced. Otherwise, transitively evaluate all module dependencies of this module and then evaluate this module. <br />Instantiate must have completed successfully prior to invoking this method. |



#### 15.2.1.16 循环模块记录 <div id="sec-cyclic-module-records"></div>

A Cyclic Module Record is used to represent information about a module that can participate in dependency cycles with other modules that are subclasses of the Cyclic Module Record type. Module Records that are not subclasses of the Cyclic Module Record type must not participate in dependency cycles with Source Text Module Records

In addition to the fields defined in Table 37 Cyclic Module Records have the additional fields listed in Table 39

Table 39: Additional Fields of Cyclic Module Records

| Field Name           | Value Type                      | Meaning                                                      |
| -------------------- | ------------------------------- | ------------------------------------------------------------ |
| [[Status]]           | String                          | Initially "uninstantiated". Transitions to "instantiating", "instantiated", "evaluating", "evaluated" (in that order) as the module progresses throughout its lifecycle. |
| [[EvaluationError]]  | An abrupt completion\|undefined | A completion of type throw representing the exception that occurred during evaluation. undefined if no exception occurred or if [[Status]] is not "evaluated". |
| [[DFSIndex]]         | Integer\|undefined              | Auxiliary field used during Instantiate and Evaluate only. If [[Status]] is "instantiating" or "evaluating", this nonnegative number records the point at which the module was first visited during the ongoing depth-first traversal of the dependency graph. |
| [[DFSAncestorIndex]] | Integer\|undefined              | Auxiliary field used during Instantiate and Evaluate only. If [[Status]] is "instantiating" or "evaluating", this is either the module's own [[DFSIndex]] or that of an "earlier" module in the same strongly connected component |
| [[RequestedModules]] | List of String                  | A List of all the ModuleSpecifier strings used by the module represented by this record to request the importation of a module. The List is source code occurrence ordered. |

In addition to the methods defined in Table 38 Cyclic Module Records have the additional methods listed in Table 40

Table 40: Additional Abstract Methods of Cyclic Module Records

| Method                  | Method                                                       |
| ----------------------- | ------------------------------------------------------------ |
| InitializeEnvironment() | Initialize the Lexical Environment of the module, including resolving all imported bindings. |
| ExecuteModule()         | Initialize the execution context of the module and evaluate the module's code within it. |

##### 15.2.1.16.1 Instantiate ( ) 具体方法 <div id="sec-moduledeclarationinstantiation"></div>

The Instantiate concrete method of a Cyclic Module Record implements the corresponding Module Record abstract method.

On success, Instantiate transitions this module's [[Status]] from "uninstantiated" to "instantiated". On failure, an exception is thrown and this module's [[Status]] remains "uninstantiated"

This abstract method performs the following steps (most of the work is done by the auxiliary function InnerModuleInstantiation):

1. Let module be this Cyclic Module Record.
2. Assert: module.[[Status]] is not "instantiating" or "evaluating".
3. Let stack be a new empty List.
4. Let result be InnerModuleInstantiation(module, stack, 0).
5. If result is an abrupt completion, then
a. For each module m in stack, do
i. Assert: m.[[Status]] is "instantiating".
ii. Set m.[[Status]] to "uninstantiated".
iii. Set m.[[Environment]] to undefined.
iv. Set m.[[DFSIndex]] to undefined.
v. Set m.[[DFSAncestorIndex]] to undefined.
b. Assert: module.[[Status]] is "uninstantiated".
c. Return result.
6. Assert: module.[[Status]] is "instantiated" or "evaluated".
7. Assert: stack is empty.
8. Return undefined.

###### 15.2.1.16.1.1 InnerModuleInstantiation ( module, stack, index ) <div id="sec-innermoduleinstantiation"></div>

The InnerModuleInstantiation abstract operation is used by Instantiate to perform the actual instantiation process for the Cyclic Module Record module, as well as recursively on all other modules in the dependency graph. The stack and index parameters, as well as a module's [[DFSIndex]] and [[DFSAncestorIndex]] fields, keep track of the depth-first search (DFS) traversal. In particular, [[DFSAncestorIndex]] is used to discover strongly connected components (SCCs), such that all modules in an SCC transition to "instantiated" together.

This abstract operation performs the following steps:

1. If module is not a Cyclic Module Record, then
a. Perform ? module.Instantiate().
b. Return index.
2. If module.[[Status]] is "instantiating", "instantiated", or "evaluated", then
a. Return index.
3. Assert: module.[[Status]] is "uninstantiated".
4. Set module.[[Status]] to "instantiating".
5. Set module.[[DFSIndex]] to index.
6. Set module.[[DFSAncestorIndex]] to index.
7. Increase index by 1.
8. Append module to stack.
9. For each String required that is an element of module.[[RequestedModules]], do
a. Let requiredModule be ? HostResolveImportedModule(module, required).
b. Set index to ? InnerModuleInstantiation(requiredModule, stack, index).
c. Assert: requiredModule.[[Status]] is either "instantiating", "instantiated", or "evaluated".
d. Assert: requiredModule.[[Status]] is "instantiating" if and only if requiredModule is in stack.
e. If requiredModule.[[Status]] is "instantiating", then
i. Assert: requiredModule is a Cyclic Module Record.
ii. Set module.[[DFSAncestorIndex]] to min(module.[[DFSAncestorIndex]], requiredModule.
[[DFSAncestorIndex]]).
10. Perform ? module.InitializeEnvironment().
11. Assert: module occurs exactly once in stack.
12. Assert: module.[[DFSAncestorIndex]] is less than or equal to module.[[DFSIndex]].
13. If module.[[DFSAncestorIndex]] equals module.[[DFSIndex]], then
a. Let done be false.
b. Repeat, while done is false,
i. Let requiredModule be the last element in stack.
ii. Remove the last element of stack.
iii. Set requiredModule.[[Status]] to "instantiated".
iv. If requiredModule and module are the same Module Record, set done to true.
14. Return index

##### 15.2.1.16.2 Evaluate ( ) 具体方法 <div id="sec-moduleevaluation"></div>

The Evaluate concrete method of a Cyclic Module Record implements the corresponding Module Record abstract method.

Evaluate transitions this module's [[Status]] from "instantiated" to "evaluated".
If execution results in an exception, that exception is recorded in the [[EvaluationError]] field and rethrown by future invocations of Evaluate.

This abstract method performs the following steps (most of the work is done by the auxiliary function
InnerModuleEvaluation):

1. Let module be this Cyclic Module Record.
2. Assert: module.[[Status]] is "instantiated" or "evaluated".
3. Let stack be a new empty List.
4. Let result be InnerModuleEvaluation(module, stack, 0).
5. If result is an abrupt completion, then
a. For each module m in stack, do
i. Assert: m.[[Status]] is "evaluating".
ii. Set m.[[Status]] to "evaluated".
iii. Set m.[[EvaluationError]] to result.
b. Assert: module.[[Status]] is "evaluated" and module.[[EvaluationError]] is result.
c. Return result.
6. Assert: module.[[Status]] is "evaluated" and module.[[EvaluationError]] is undefined.
7. Assert: stack is empty.
8. Return undefined.

###### 15.2.1.16.2.1 InnerModuleEvaluation ( module, stack, index ) <div id="sec-innermoduleevaluation"></div>

The InnerModuleEvaluation abstract operation is used by Evaluate to perform the actual evaluation process for the Source Text Module Record module, as well as recursively on all other modules in the dependency graph. The stack and index parameters, as well as module's [[DFSIndex]] and [[DFSAncestoreIndex]] fields, are used the same way as in InnerModuleInstantiation

This abstract operation performs the following steps:

1. If module is not a Cyclic Module Record, then
a. Perform ? module.Evaluate().
b. Return index.
2. If module.[[Status]] is "evaluated", then
a. If module.[[EvaluationError]] is undefined, return index.
b. Otherwise return module.[[EvaluationError]].
3. If module.[[Status]] is "evaluating", return index.
4. Assert: module.[[Status]] is "instantiated".
5. Set module.[[Status]] to "evaluating".
6. Set module.[[DFSIndex]] to index.
7. Set module.[[DFSAncestorIndex]] to index.
8. Increase index by 1.
9. Append module to stack.
10. For each String required that is an element of module.[[RequestedModules]], do
a. Let requiredModule be ! HostResolveImportedModule(module, required).
b. NOTE: Instantiate must be completed successfully prior to invoking this method, so every requested module
is guaranteed to resolve successfully.
c. Set index to ? InnerModuleEvaluation(requiredModule, stack, index).
11. d. Assert: requiredModule.[[Status]] is either "evaluating" or "evaluated".
    e. Assert: requiredModule.[[Status]] is "evaluating" if and only if requiredModule is in stack.
    f. If requiredModule.[[Status]] is "evaluating", then
    i. Assert: requiredModule is a Cyclic Module Record.
    ii. Set module.[[DFSAncestorIndex]] to min(module.[[DFSAncestorIndex]], requiredModule.
    [[DFSAncestorIndex]]).
    11. Perform ? module.ExecuteModule().
    12. Assert: module occurs exactly once in stack.
    13. Assert: module.[[DFSAncestorIndex]] is less than or equal to module.[[DFSIndex]].
    14. If module.[[DFSAncestorIndex]] equals module.[[DFSIndex]], then
    a. Let done be false.
    b. Repeat, while done is false,
    i. Let requiredModule be the last element in stack.
    ii. Remove the last element of stack.
    iii. Set requiredModule.[[Status]] to "evaluated".
    iv. If requiredModule and module are the same Module Record, set done to true.
    15. Return index

##### 15.2.1.16.3 循环模块记录示例图 <div id="sec-example-cyclic-module-record-graphs"></div>

This non-normative section gives a series of examples of the instantiation and evaluation of a few common module graphs, with a specific focus on how errors can occur.

First consider the following simple module graph:

Figure 2: A simple module graph

Let's first assume that there are no error conditions. When a host first calls A.Instantiate(), this will complete successfully by assumption, and recursively instantiate modules B and C as well, such that A.[[Status]] = B.[[Status]] = C.[[Status]] = "instantiated". This preparatory step can be performed at any time. Later, when the host is ready to incur any possible side effects of the modules, it can call A.Evaluate(), which will complete successfully (again by assumption), recursively having evaluated first C and then B. Each module's [[Status]] at this point will be "evaluated".

Consider then cases involving instantiation errors. If InnerModuleInstantiation of C succeeds but, thereafter, fails for B, for example because it imports something that C does not provide, then the original A.Instantiate() will fail, and both A and B's [[Status]] remain "uninstantiated". C's [[Status]] has become "instantiated", though.

Finally, consider a case involving evaluation errors. If InnerModuleEvaluation of C succeeds but, thereafter, fails for B, for example because B contains code that throws an exception, then the original A.Evaluate() will fail. The resulting exception will be recorded in both A and B's [[EvaluationError]] fields, and their [[Status]] will become "evaluated".C will also become "evaluated" but, in contrast to A and B, will remain without an [[EvaluationError]], as it successfully completed evaluation. Storing the exception ensures that any time a host tries to reuse A or B by calling their Evaluate() method, it will encounter the same exception. (Hosts are not required to reuse Cyclic Module Records; similarly, hosts are not required to expose the exception objects thrown by these methods. However, the specification enables such uses.)

The difference here between instantiation and evaluation errors is due to how evaluation must be only performed once, as it can cause side effects; it is thus important to remember whether evaluation has already been performed, even if unsuccessfully. (In the error case, it makes sense to also remember the exception because otherwise subsequent Evaluate() calls would have to synthesize a new one.) Instantiation, on the other hand, is side-effect-free, and thus even if it fails, it can be retried at a later time with no issues.

Now consider a different type of error condition:

Figure 3: A module graph with an unresolvable module

In this scenario, module A declares a dependency on some other module, but no Module Record exists for that module, i.e. HostResolveImportedModule throws an exception when asked for it. This could occur for a variety of reasons, such as the corresponding resource not existing, or the resource existing but ParseModule throwing an exception when trying to parse the resulting source text. Hosts can choose to expose the cause of failure via the exception they throw from HostResolveImportedModule. In any case, this exception causes an instantiation failure, which as before results in A's [[Status]] remaining "uninstantiated".

Lastly, consider a module graph with a cycle:

Figure 4: A cyclic module graph

Here we assume that the entry point is module A, so that the host proceeds by calling A.Instantiate(), which performs InnerModuleInstantiation on A. This in turn calls InnerModuleInstantiation on B. Because of the cycle, this again triggers InnerModuleInstantiation on A, but at this point it is a no-op since A.[[Status]] is already "instantiating". B. [[Status]] itself remains "instantiating" when control gets back to A and InnerModuleInstantiation is triggered on C. After this returns with C.[[Status]] being "instantiated" , both A and B transition from "instantiating" to "instantiated" together; this is by design, since they form a strongly connected component.

An analogous story occurs for the evaluation phase of a cyclic module graph, in the success case.

Now consider a case where A has an instantiation error; for example, it tries to import a binding from C that does not exist. In that case, the above steps still occur, including the early return from the second call to InnerModuleInstantiation on A. However, once we unwind back to the original InnerModuleInstantiation on A, it fails during InitializeEnvironment, namely right after C.ResolveExport(). The thrown SyntaxError exception propagates up to A.Instantiate, which resets all modules that are currently on its stack (these are always exactly the modules that are still "instantiating"). Hence both A and B become "uninstantiated". Note that C is left as "instantiated".

Finally, consider a case where A has an evaluation error; for example, its source code throws an exception. In that case, the evaluation-time analog of the above steps still occurs, including the early return from the second call to InnerModuleEvaluation on A. However, once we unwind back to the original InnerModuleEvaluation on A, it fails by assumption. The exception thrown propagates up to A.Evaluate(), which records the error in all modules that are currently on its stack (i.e., the modules that are still "evaluating"). Hence both A and B become "evaluated" and the exception is recorded in both A and B's [[EvaluationError]] fields, while C is left as "evaluated" with no [[EvaluationError]].

#### 15.2.1.17 源文本模块记录 <div id="sec-source-text-module-records"></div>

A Source Text Module Record is used to represent information about a module that was defined from ECMAScript source text (10) that was parsed using the goal symbol Module. Its fields contain digested information about the names that are imported by the module and its concrete methods use this digest to link, instantiate, and evaluate the module.

A Source Text Module Record can exist in a module graph with other subclasses of the abstract Module Record type, and can participate in cycles with other subclasses of the Cyclic Module Record type.

In addition to the fields defined in Table 39, Source Text Module Records have the additional fields listed in Table 41. Each of these fields is initially set in ParseModule.

Table 41: Additional Fields of Source Text Module Records

| Field Name                | Value Type                                                   | Meaning                                                      |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[ECMAScriptCode]]        | a Parse Node                                                 | The result of parsing the source text of this module using Module as the goal symbol。 |
| [[ImportEntries]]         | [List](#sec-list-and-record-specification-type) of ImportEntry Records | A [List](#sec-list-and-record-specification-type) of ImportEntry records derived from the code of this module. |
| [[LocalExportEntries]]    | [List](#sec-list-and-record-specification-type) of ExportEntry Records | A [List](#sec-list-and-record-specification-type) of ExportEntry records derived from the code of this module that correspond to declarations that occur within the module. |
| [[IndirectExportEntries]] | [List](#sec-list-and-record-specification-type) of ExportEntry Records | A [List](#sec-list-and-record-specification-type) of ExportEntry records derived from the code of this module that correspond to reexported imports that occur within the module. |
| [[StarExportEntries]]     | [List](#sec-list-and-record-specification-type) of ExportEntry Records | A [List](#sec-list-and-record-specification-type) of ExportEntry records derived from the code of this module that correspond to export * declarations that occur within the module. |

An ImportEntry Record is a Record that digests information about a single declarative import. Each ImportEntry Record has the fields defined in Table 42:

Table 42: ImportEntry Record Fields

| Field Name        | Value Type | Meaning                                                      |
| ----------------- | ---------- | ------------------------------------------------------------ |
| [[ModuleRequest]] | String     | String value of the [ModuleSpecifier](#prod-ModuleSpecifier) of the [ImportDeclaration](#prod-ImportDeclaration). |
| [[ImportName]]    | String     | The name under which the desired binding is exported by the module identified by [[ModuleRequest]]. The value `"*"` indicates that the import request is for the target module's namespace object. |
| [[LocalName]]     | String     | The name that is used to locally access the imported value from within the importing module. |

>NOTE 1 Table 43 gives examples of ImportEntry records fields used to represent the syntactic import forms:
>
>| Import Statement Form import语句格式 | [[ModuleRequest]]                                            | [[ImportName]] | [[LocalName]] |
>| ------------------------------------ | ------------------------------------------------------------ | -------------- | ------------- |
>| `import v from "mod";`               | `"mod"`                                                      | `"default"`    | `"v"`         |
>| `import * as ns from "mod";`         | `"mod"`                                                      | `"*"`          | `"ns"`        |
>| `import {x} from "mod";`             | `"mod"`                                                      | `"x"`          | `"x"`         |
>| `import {x as v} from "mod";`        | `"mod"`                                                      | `"x"`          | `"v"`         |
>| `import "mod";`                      | An ImportEntry [Record](#sec-list-and-record-specification-type) is not created. |                |               |

An ExportEntry Record is a Record that digests information about a single declarative export. Each ExportEntry Record has the fields defined in Table 44:

| Field Name        | Value Type     | Meaning                                                      |
| ----------------- | -------------- | ------------------------------------------------------------ |
| [[ExportName]]    | String         | The name used to export this binding by this module.         |
| [[ModuleRequest]] | String \| null | The String value of the [ModuleSpecifier](#prod-ModuleSpecifier) of the [ExportDeclaration](#prod-ExportDeclaration). null if the [ExportDeclaration](#prod-ExportDeclaration) does not have a [ModuleSpecifier](#prod-ModuleSpecifier). |
| [[ImportName]]    | String \| null | The name under which the desired binding is exported by the module identified by [[ModuleRequest]]. null if the [ExportDeclaration](#prod-ExportDeclaration) does not have a [ModuleSpecifier](#prod-ModuleSpecifier). `"*"` indicates that the export request is for all exported bindings. |
| [[LocalName]]     | String \| null | The name that is used to locally access the exported value from within the importing module. null if the exported value is not locally accessible from within the module. |

>NOTE 2 Table 45 gives examples of the ExportEntry record fields used to represent the syntactic export forms:
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

1. Assert: sourceText is an ECMAScript source text (see clause 10).
2. Parse sourceText using Module as the goal symbol and analyse the parse result for any Early Error conditions. If the
parse was successful and no early errors were found, let body be the resulting parse tree. Otherwise, let body be a
List of one or more SyntaxError or ReferenceError objects representing the parsing errors and/or early errors.
Parsing and early error detection may be interweaved in an implementation-dependent manner. If more than one
parsing error or early error is present, the number and ordering of error objects in the list is implementationdependent, but at least one must be present.
3. If body is a List of errors, return body.
4. Let requestedModules be the ModuleRequests of body.
5. Let importEntries be ImportEntries of body.
6. Let importedBoundNames be ImportedLocalNames(importEntries).
7. Let indirectExportEntries be a new empty List.
8. Let localExportEntries be a new empty List.
9. Let starExportEntries be a new empty List.
10. Let exportEntries be ExportEntries of body.
11. For each ExportEntry Record ee in exportEntries, do
a. If ee.[[ModuleRequest]] is null, then
12. i. If ee.[[LocalName]] is not an element of importedBoundNames, then
    1. Append ee to localExportEntries.
    ii. Else,
    1. Let ie be the element of importEntries whose [[LocalName]] is the same as ee.[[LocalName]].
    2. If ie.[[ImportName]] is "*", then
    a. Assert: This is a re-export of an imported module namespace object.
    b. Append ee to localExportEntries.
    3. Else this is a re-export of a single name,
    a. Append the ExportEntry Record { [[ModuleRequest]]: ie.[[ModuleRequest]],
    [[ImportName]]: ie.[[ImportName]], [[LocalName]]: null, [[ExportName]]: ee.
    [[ExportName]] } to indirectExportEntries.
    b. Else if ee.[[ImportName]] is "*", then
    i. Append ee to starExportEntries.
    c. Else,
    i. Append ee to indirectExportEntries.
    12. Return Source Text Module Record { [[Realm]]: realm, [[Environment]]: undefined, [[Namespace]]: undefined,
    [[Status]]: "uninstantiated", [[EvaluationError]]: undefined, [[HostDefined]]: hostDefined,
    [[ECMAScriptCode]]: body, [[RequestedModules]]: requestedModules, [[ImportEntries]]: importEntries,
    [[LocalExportEntries]]: localExportEntries, [[IndirectExportEntries]]: indirectExportEntries, [[StarExportEntries]]:
    starExportEntries, [[DFSIndex]]: undefined, [[DFSAncestorIndex]]: undefined }.

> NOTE An implementation may parse module source text and analyse it for Early Error conditions prior to the evaluation of ParseModule for that module source text. However, the reporting of any errors must be deferred until the point where this specification actually performs ParseModule upon that source text.

##### 15.2.1.17.2 GetExportedNames ( exportStarSet ) Concrete Method <div id="sec-getexportednames"></div>

The GetExportedNames concrete method of a Source Text Module Record implements the corresponding Module Record abstract method.

It performs the following steps:

1. Let module be this Source Text Module Record.
2. If exportStarSet contains module, then
a. Assert: We've reached the starting point of an import * circularity.
b. Return a new empty List.
3. Append module to exportStarSet.
4. Let exportedNames be a new empty List.
5. For each ExportEntry Record e in module.[[LocalExportEntries]], do
a. Assert: module provides the direct binding for this export.
b. Append e.[[ExportName]] to exportedNames.
6. For each ExportEntry Record e in module.[[IndirectExportEntries]], do
a. Assert: module imports a specific binding for this export.
b. Append e.[[ExportName]] to exportedNames.
7. For each ExportEntry Record e in module.[[StarExportEntries]], do
a. Let requestedModule be ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
b. Let starNames be ? requestedModule.GetExportedNames(exportStarSet).
8. c. For each element n of starNames, do
    i. If SameValue(n, "default") is false, then
  1. If n is not an element of exportedNames, then
    a. Append n to exportedNames.
  8. Return exportedNames.

> NOTE GetExportedNames does not filter out or throw an exception for names that have ambiguous star export bindings.

##### 15.2.1.17.3 ResolveExport ( exportName, resolveSet ) Concrete Method <div id="sec-resolveexport"></div>

The ResolveExport concrete method of a Source Text Module Record implements the corresponding Module Record abstract method.

ResolveExport attempts to resolve an imported binding to the actual defining module and local binding name. The defining module may be the module represented by the Module Record this method was invoked on or some other module that is imported by that module. The parameter resolveSet is used to detect unresolved circular import/export paths. If a pair consisting of specific Module Record and exportName is reached that is already in resolveSet, an import circularity has been encountered. Before recursively calling ResolveExport, a pair consisting of module and exportName is added to resolveSet.

If a defining module is found, a ResolvedBinding Record { [[Module]], [[BindingName]] } is returned. This record identifies the resolved binding of the originally requested export. If no definition was found or the request is found to be circular, null is returned. If the request is found to be ambiguous, the string "ambiguous" is returned.

This abstract method performs the following steps:

1. Let module be this Source Text Module Record.
2. For each Record { [[Module]], [[ExportName]] } r in resolveSet, do
a. If module and r.[[Module]] are the same Module Record and SameValue(exportName, r.[[ExportName]]) is
true, then
i. Assert: This is a circular import request.
ii. Return null.
3. Append the Record { [[Module]]: module, [[ExportName]]: exportName } to resolveSet.
4. For each ExportEntry Record e in module.[[LocalExportEntries]], do
a. If SameValue(exportName, e.[[ExportName]]) is true, then
i. Assert: module provides the direct binding for this export.
ii. Return ResolvedBinding Record { [[Module]]: module, [[BindingName]]: e.[[LocalName]] }.
5. For each ExportEntry Record e in module.[[IndirectExportEntries]], do
a. If SameValue(exportName, e.[[ExportName]]) is true, then
i. Assert: module imports a specific binding for this export.
ii. Let importedModule be ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
iii. Return importedModule.ResolveExport(e.[[ImportName]], resolveSet).
6. If SameValue(exportName, "default") is true, then
a. Assert: A default export was not explicitly defined by this module.
b. Return null.
c. NOTE: A default export cannot be provided by an export *.
7. Let starResolution be null.
8. For each ExportEntry Record e in module.[[StarExportEntries]], do
9. a. Let importedModule be ? HostResolveImportedModule(module, e.[[ModuleRequest]]).
   b. Let resolution be ? importedModule.ResolveExport(exportName, resolveSet).
   c. If resolution is "ambiguous", return "ambiguous".
   d. If resolution is not null, then
   i. Assert: resolution is a ResolvedBinding Record.
   ii. If starResolution is null, set starResolution to resolution.
   iii. Else,
   1. Assert: There is more than one * import that includes the requested name.
   2. If resolution.[[Module]] and starResolution.[[Module]] are not the same Module Record or
   SameValue(resolution.[[BindingName]], starResolution.[[BindingName]]) is false, return
   "ambiguous".
   9. Return starResolution.

##### 15.2.1.17.4 InitializeEnvironment ( ) Concrete Method <div id="sec-source-text-module-record-initialize-environment"></div>

The InitializeEnvironment concrete method of a Source Text Module Record implements the corresponding Cyclic Module Record abstract method.

This abstract method performs the following steps:

1. Let module be this Source Text Module Record.
2. For each ExportEntry Record e in module.[[IndirectExportEntries]], do
a. Let resolution be ? module.ResolveExport(e.[[ExportName]], « »).
b. If resolution is null or "ambiguous", throw a SyntaxError exception.
c. Assert: resolution is a ResolvedBinding Record.
3. Assert: All named exports from module are resolvable.
4. Let realm be module.[[Realm]].
5. Assert: realm is not undefined.
6. Let env be NewModuleEnvironment(realm.[[GlobalEnv]]).
7. Set module.[[Environment]] to env.
8. Let envRec be env's EnvironmentRecord.
9. For each ImportEntry Record in in module.[[ImportEntries]], do
a. Let importedModule be ! HostResolveImportedModule(module, in.[[ModuleRequest]]).
b. NOTE: The above call cannot fail because imported module requests are a subset of module.
[[RequestedModules]], and these have been resolved earlier in this algorithm.
c. If in.[[ImportName]] is "*", then
i. Let namespace be ? GetModuleNamespace(importedModule).
ii. Perform ! envRec.CreateImmutableBinding(in.[[LocalName]], true).
iii. Call envRec.InitializeBinding(in.[[LocalName]], namespace).
d. Else,
i. Let resolution be ? importedModule.ResolveExport(in.[[ImportName]], « »).
ii. If resolution is null or "ambiguous", throw a SyntaxError exception.
iii. Call envRec.CreateImportBinding(in.[[LocalName]], resolution.[[Module]], resolution.
[[BindingName]]).
10. Let code be module.[[ECMAScriptCode]].
11. Let varDeclarations be the VarScopedDeclarations of code.
12. Let declaredVarNames be a new empty List.
13. For each element d in varDeclarations, do
a. For each element dn of the BoundNames of d, do
14. i. If dn is not an element of declaredVarNames, then
    1. Perform ! envRec.CreateMutableBinding(dn, false).
    2. Call envRec.InitializeBinding(dn, undefined).
    3. Append dn to declaredVarNames.
    14. Let lexDeclarations be the LexicallyScopedDeclarations of code.
    15. For each element d in lexDeclarations, do
    a. For each element dn of the BoundNames of d, do
    i. If IsConstantDeclaration of d is true, then
    1. Perform ! envRec.CreateImmutableBinding(dn, true).
    ii. Else,
    1. Perform ! envRec.CreateMutableBinding(dn, false).
    iii. If d is a FunctionDeclaration, a GeneratorDeclaration, an AsyncFunctionDeclaration, or an
    AsyncGeneratorDeclaration, then
    1. Let fo be the result of performing InstantiateFunctionObject for d with argument env.
    2. Call envRec.InitializeBinding(dn, fo).
    16. Return NormalCompletion(empty).

##### 15.2.1.17.5 ExecuteModule ( ) Concrete Method <div id="sec-source-text-module-record-execute-module"></div>

The ExecuteModule concrete method of a Source Text Module Record implements the corresponding Cyclic Module Record abstract method.

This abstract method performs the following steps:

1. Let module be this Source Text Module Record.
2. Let moduleCxt be a new ECMAScript code execution context.
3. Set the Function of moduleCxt to null.
4. Assert: module.[[Realm]] is not undefined.
5. Set the Realm of moduleCxt to module.[[Realm]].
6. Set the ScriptOrModule of moduleCxt to module.
7. Assert: module has been linked and declarations in its module environment have been instantiated.
8. Set the VariableEnvironment of moduleCxt to module.[[Environment]].
9. Set the LexicalEnvironment of moduleCxt to module.[[Environment]].
10. Suspend the currently running execution context.
11. Push moduleCxt on to the execution context stack; moduleCxt is now the running execution context.
12. Let result be the result of evaluating module.[[ECMAScriptCode]].
13. Suspend moduleCxt and remove it from the execution context stack.
14. Resume the context that is now on the top of the execution context stack as the running execution context.
15. Return Completion(result).

#### 15.2.1.18 RS: HostResolveImportedModule ( referencingModule, specifier ) <div id="sec-hostresolveimportedmodule"></div>

HostResolveImportedModule is an implementation-defined abstract operation that provides the concrete Module Record subclass instance that corresponds to the ModuleSpecifier String, specifier, occurring within the context of the module represented by the Module Record referencingModule.

The implementation of HostResolveImportedModule must conform to the following requirements:

The normal return value must be an instance of a concrete subclass of Module Record.
If a Module Record corresponding to the pair referencingModule, specifier does not exist or cannot be created, an exception must be thrown. This operation must be idempotent if it completes normally. Each time it is called with a specific referencingModule, specifier pair as arguments it must return the same Module Record instance.

Multiple different referencingModule, specifier pairs may map to the same Module Record instance. The actual mapping semantic is implementation-defined but typically a normalization process is applied to specifier as part of the mapping process. A typical normalization process would include actions such as alphabetic case folding and expansion of relative and abbreviated path specifiers.

#### 15.2.1.19 RS: GetModuleNamespace ( module ) <div id="sec-getmodulenamespace"></div>

The GetModuleNamespace abstract operation retrieves the Module Namespace Exotic object representing module's exports, lazily creating it the first time it was requested, and storing it in module.[[Namespace]] for future retrieval.

This abstract operation performs the following steps:

1. Assert: module is an instance of a concrete subclass of Module Record.
2. Assert: module.[[Status]] is not "uninstantiated".
3. Let namespace be module.[[Namespace]].
4. If namespace is undefined, then
a. Let exportedNames be ? module.GetExportedNames(« »).
b. Let unambiguousNames be a new empty List.
c. For each name that is an element of exportedNames, do
i. Let resolution be ? module.ResolveExport(name, « »).
ii. If resolution is a ResolvedBinding Record, append name to unambiguousNames.
d. Set namespace to ModuleNamespaceCreate(module, unambiguousNames).
5. Return namespace.

> NOTE The only way GetModuleNamespace can throw is via one of the triggered HostResolveImportedModule calls. Unresolvable names are simply excluded from the namespace at this point. They will lead to a real instantiation error later unless they are all ambiguous star exports that are not explicitly requested anywhere.

#### 15.2.1.20 RS: TopLevelModuleEvaluationJob ( sourceText, hostDefined ) <div id="sec-toplevelmoduleevaluationjob"></div>

A TopLevelModuleEvaluationJob with parameters sourceText and hostDefined is a job that parses, validates, and evaluates sourceText as a Module.

1. Assert: sourceText is an ECMAScript source text (see clause 10).
2. Let realm be the current Realm Record.
3. Let m be ParseModule(sourceText, realm, hostDefined).
4. If m is a List of errors, then
a. Perform HostReportErrors(m).
b. Return NormalCompletion(undefined).
5. Perform ? m.Instantiate().
6. Assert: All dependencies of m have been transitively resolved and m is ready for evaluation.
7. Return ? m.Evaluate().

> NOTE An implementation may parse a sourceText as a Module, analyse it for Early Error conditions, and instantiate it prior to the execution of the TopLevelModuleEvaluationJob for that sourceText. An implementation may also resolve, pre-parse and pre-analyse, and pre-instantiate module dependencies of sourceText. However, the reporting of any errors detected by these actions must be deferred until the TopLevelModuleEvaluationJob is actually executed.

#### 15.2.1.21 RS: Evaluation <div id="sec-module-semantics-runtime-semantics-evaluation"></div>

```
Module : [empty]
```

1. Return NormalCompletion(undefined).

```
ModuleBody : ModuleItemList
```

1. Let result be the result of evaluating ModuleItemList.
2. If result.[[Type]] is normal and result.[[Value]] is empty, then
a. Return NormalCompletion(undefined).
3. Return Completion(result).

```
ModuleItemList : ModuleItemList ModuleItem
```

1. Let sl be the result of evaluating ModuleItemList.
2. ReturnIfAbrupt(sl).
3. Let s be the result of evaluating ModuleItem.
4. Return Completion(UpdateEmpty(s, sl)).

> NOTE The value of a ModuleItemList is the value of the last value-producing item in the ModuleItemList.

```
ModuleItem : ImportDeclaration
```

1. Return NormalCompletion(empty)

### 15.2.2 导入 <div id="sec-imports"></div>

**Syntax**

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

It is a Syntax Error if the BoundNames of ImportDeclaration contains any duplicate entries.

#### 15.2.2.2 SS: BoundNames <div id="sec-imports-static-semantics-boundnames"></div>

```
ImportDeclaration : import ImportClause FromClause ;
```

1. Return the BoundNames of ImportClause.

```
ImportDeclaration : import ModuleSpecifier ;
```

1. Return a new empty List.

```
ImportClause : ImportedDefaultBinding , NameSpaceImport
```

1. Let names be the BoundNames of ImportedDefaultBinding.
2. Append to names the elements of the BoundNames of NameSpaceImport.
3. Return names.

```
ImportClause : ImportedDefaultBinding , NamedImports
```

1. Let names be the BoundNames of ImportedDefaultBinding.
2. Append to names the elements of the BoundNames of NamedImports.
3. Return names.

```
NamedImports : { }
```

1. Return a new empty List

```
ImportsList : ImportsList , ImportSpecifier
```

1. Let names be the BoundNames of ImportsList.
2. Append to names the elements of the BoundNames of ImportSpecifier.
3. Return names.

```
ImportSpecifier : IdentifierName as ImportedBinding
```

1. Return the BoundNames of ImportedBinding

#### 15.2.2.3 SS: ImportEntries <div id="sec-imports-static-semantics-importentries"></div>

```
ImportDeclaration : import ImportClause FromClause ;
```

1. Let module be the sole element of ModuleRequests of FromClause.
2. Return ImportEntriesForModule of ImportClause with argument module

```
ImportDeclaration : import ModuleSpecifier ;
```

1. Return a new empty List.

#### 15.2.2.4 SS: ImportEntriesForModule <div id="sec-static-semantics-importentriesformodule"></div>

With parameter module.

```
ImportClause : ImportedDefaultBinding , NameSpaceImport
```

1. Let entries be ImportEntriesForModule of ImportedDefaultBinding with argument module.
2. Append to entries the elements of the ImportEntriesForModule of NameSpaceImport with argument module.
3. Return entries.

```
ImportClause : ImportedDefaultBinding , NamedImports
```

1. Let entries be ImportEntriesForModule of ImportedDefaultBinding with argument module.
2. Append to entries the elements of the ImportEntriesForModule of NamedImports with argument module.
3. Return entries

```
ImportedDefaultBinding : ImportedBinding
```

1. Let localName be the sole element of BoundNames of ImportedBinding.
2. Let defaultEntry be the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "default",
[[LocalName]]: localName }.
3. Return a new List containing defaultEntry.

```
NameSpaceImport : * as ImportedBinding
```

1. Let localName be the StringValue of ImportedBinding.
2. Let entry be the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "*", [[LocalName]]:
localName }.
3. Return a new List containing entry.

```
NamedImports : { }
```

1. Return a new empty List.

```
ImportsList : ImportsList , ImportSpecifier
```

1. Let specs be the ImportEntriesForModule of ImportsList with argument module.
2. Append to specs the elements of the ImportEntriesForModule of ImportSpecifier with argument module.
3. Return specs.

```
ImportSpecifier : ImportedBinding
```

1. Let localName be the sole element of BoundNames of ImportedBinding.
2. Let entry be the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: localName, [[LocalName]]: localName }.
3. Return a new List containing entry.

```
ImportSpecifier : IdentifierName as ImportedBinding
```

1. Let importName be the StringValue of IdentifierName.
2. Let localName be the StringValue of ImportedBinding.
3. Let entry be the ImportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]:
localName }.
4. Return a new List containing entry.

#### 15.2.2.5 SS: ModuleRequests <div id="sec-imports-static-semantics-modulerequests"></div>

```
ImportDeclaration : import ImportClause FromClause ;
```

1. Return ModuleRequests of FromClause.

```
ModuleSpecifier : StringLiteral
```

1. Return a List containing the StringValue of StringLiteral.

### 15.2.3 导出 <div id="sec-exports"></div>

**Syntax**

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

For each IdentifierName n in ReferencedBindings of ExportClause: It is a Syntax Error if StringValue of n is a
ReservedWord or if the StringValue of n is one of: "implements", "interface", "let", "package",
"private", "protected", "public", or "static".

> NOTE The above rule means that each ReferencedBindings of ExportClause is treated as an IdentifierReference.

#### 15.2.3.2 SS: BoundNames <div id="sec-exports-static-semantics-boundnames"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
```

1. Return a new empty List.

```
ExportDeclaration : export VariableStatement
```

1. Return the BoundNames of VariableStatement.

```
ExportDeclaration : export Declaration
```

1. Return the BoundNames of Declaration.

```
ExportDeclaration : export default HoistableDeclaration
```

1. Let declarationNames be the BoundNames of HoistableDeclaration.
2. If declarationNames does not include the element "*default*", append "*default*" to declarationNames.
3. Return declarationNames.

```
ExportDeclaration : export default ClassDeclaration
```

1. Let declarationNames be the BoundNames of ClassDeclaration.
2. If declarationNames does not include the element "*default*", append "*default*" to declarationNames.
3. Return declarationNames.

```
ExportDeclaration : export default AssignmentExpression ;
```

1. Return « "\*default\*" ».

#### 15.2.3.3 SS: ExportedBindings <div id="sec-exports-static-semantics-exportedbindings"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
```

1. Return a new empty List.

```
ExportDeclaration : export ExportClause ;
```

1. Return the ExportedBindings of ExportClause.

```
ExportDeclaration : export VariableStatement
```

1. Return the BoundNames of VariableStatement.

```
ExportDeclaration : export Declaration
```

1. Return the BoundNames of Declaration

```
ExportDeclaration : export default HoistableDeclaration
ExportDeclaration : export default ClassDeclaration
ExportDeclaration : export default AssignmentExpression ;
```

1. Return the BoundNames of this ExportDeclaration.

```
ExportClause : { }
```

1. Return a new empty List.

```
ExportsList : ExportsList , ExportSpecifier
```

1. Let names be the ExportedBindings of ExportsList.
2. Append to names the elements of the ExportedBindings of ExportSpecifier.
3. Return names.

```
ExportSpecifier : IdentifierName
```

1. Return a List containing the StringValue of IdentifierName

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. Return a List containing the StringValue of the first IdentifierName

#### 15.2.3.4 SS: ExportedNames <div id="sec-exports-static-semantics-exportednames"></div>

```
ExportDeclaration : export * FromClause ;
```

1. Return a new empty List.

```
ExportDeclaration : export ExportClause FromClause ;
ExportDeclaration : export ExportClause ;
```

1. Return the ExportedNames of ExportClause.

```
ExportDeclaration : export VariableStatement
```

1. Return the BoundNames of VariableStatement

```
ExportDeclaration : export Declaration
```

1. Return the BoundNames of Declaration.

```
ExportDeclaration : export default HoistableDeclaration
ExportDeclaration : export default ClassDeclaration
ExportDeclaration : export default AssignmentExpression ;
```

1. Return « "default" ».

```
ExportClause : { }
```

1. Return a new empty List.

```
ExportsList : ExportsList , ExportSpecifier
```

1. Let names be the ExportedNames of ExportsList.
2. Append to names the elements of the ExportedNames of ExportSpecifier.
3. Return names

```
ExportSpecifier : IdentifierName
```

1. Return a List containing the StringValue of IdentifierName.

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. Return a List containing the StringValue of the second IdentifierName.

#### 15.2.3.5 SS: ExportEntries <div id="sec-exports-static-semantics-exportentries"></div>

```
ExportDeclaration : export * FromClause ;
```

1. Let module be the sole element of ModuleRequests of FromClause.
2. Let entry be the ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: "*", [[LocalName]]: null, [[ExportName]]: null }.
3. Return a new List containing entry.

```
ExportDeclaration : export ExportClause FromClause ;
```

1. Let module be the sole element of ModuleRequests of FromClause.
2. Return ExportEntriesForModule of ExportClause with argument module.

```
ExportDeclaration : export ExportClause ;
```

1. Return ExportEntriesForModule of ExportClause with argument null.

```
ExportDeclaration : export VariableStatement
```

1. Let entries be a new empty List.
2. Let names be the BoundNames of VariableStatement.
3. For each name in names, do

a. Append the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]: name,
[[ExportName]]: name } to entries.

4. Return entries.

```
ExportDeclaration : export Declaration
```

1. Let entries be a new empty List.
2. Let names be the BoundNames of Declaration.
3. For each name in names, do
a. Append the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]: name, [[ExportName]]: name } to entries.
4. Return entries

```
ExportDeclaration : export default HoistableDeclaration
```

1. Let names be BoundNames of HoistableDeclaration.
2. Let localName be the sole element of names.
3. Return a new List containing the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null,
[[LocalName]]: localName, [[ExportName]]: "default" }.

```
ExportDeclaration : export default ClassDeclaration
```

1. Let names be BoundNames of ClassDeclaration.
2. Let localName be the sole element of names.
3. Return a new List containing the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null,
[[LocalName]]: localName, [[ExportName]]: "default" }.

```
ExportDeclaration : export default AssignmentExpression ;
```

1. Let entry be the ExportEntry Record { [[ModuleRequest]]: null, [[ImportName]]: null, [[LocalName]]:
"\*default\*", [[ExportName]]: "default" }.
2. Return a new List containing entry.

> NOTE "\*default\*" is used within this specification as a synthetic name for anonymous default export values.

#### 15.2.3.6 SS: ExportEntriesForModule <div id="sec-static-semantics-exportentriesformodule"></div>

With parameter module.

```
ExportClause : { }
```

1. Return a new empty List.

```
ExportsList : ExportsList , ExportSpecifier
```

1. Let specs be the ExportEntriesForModule of ExportsList with argument module.
2. Append to specs the elements of the ExportEntriesForModule of ExportSpecifier with argument module.
3. Return specs.

```
ExportSpecifier : IdentifierName
```

1. Let sourceName be the StringValue of IdentifierName.
2. If module is null, then
a. Let localName be sourceName.
b. Let importName be null.
3. Else,
a. Let localName be null.
b. Let importName be sourceName.
4. Return a new List containing the ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]: localName, [[ExportName]]: sourceName }.

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. Let sourceName be the StringValue of the first IdentifierName.
2. Let exportName be the StringValue of the second IdentifierName.
3. If module is null, then
a. Let localName be sourceName.
b. Let importName be null.
4. Else,
a. Let localName be null.
b. Let importName be sourceName.
5. Return a new List containing the ExportEntry Record { [[ModuleRequest]]: module, [[ImportName]]: importName, [[LocalName]]: localName, [[ExportName]]: exportName }.

#### 15.2.3.7 SS: IsConstantDeclaration <div id="sec-exports-static-semantics-isconstantdeclaration"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
export default AssignmentExpression ;
```

1. Return false.

> NOTE It is not necessary to treat export default AssignmentExpression as a constant declaration because there is no syntax that permits assignment to the internal bound name used to reference a module's default object.

#### 15.2.3.8 SS: LexicallyScopedDeclarations <div id="sec-exports-static-semantics-lexicallyscopeddeclarations"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
export VariableStatement
```

1. Return a new empty List.

```
ExportDeclaration : export Declaration
```

1. Return a new List containing DeclarationPart of Declaration.

```
ExportDeclaration : export default HoistableDeclaration
```

1. Return a new List containing DeclarationPart of HoistableDeclaration.

```
ExportDeclaration : export default ClassDeclaration
```

1. Return a new List containing ClassDeclaration.

```
ExportDeclaration : export default AssignmentExpression ;
```

1. Return a new List containing this ExportDeclaration.

#### 15.2.3.9 SS: ModuleRequests <div id="sec-exports-static-semantics-modulerequests"></div>

```
ExportDeclaration : export * FromClause ;
ExportDeclaration : export ExportClause FromClause ;
```

1. Return the ModuleRequests of FromClause

   ```
   ExportDeclaration :
   export ExportClause ;
   export VariableStatement
   export Declaration
   export default HoistableDeclaration
   export default ClassDeclaration
   export default AssignmentExpression ;
   ```

   1. Return a new empty List.

#### 15.2.3.10 SS: ReferencedBindings <div id="sec-static-semantics-referencedbindings"></div>

```
ExportClause : { }
```

1. Return a new empty List.

```
ExportsList : ExportsList , ExportSpecifier
```

1. Let names be the ReferencedBindings of ExportsList.
2. Append to names the elements of the ReferencedBindings of ExportSpecifier.
3. Return names.

```
ExportSpecifier : IdentifierName
```

1. Return a List containing the IdentifierName.

```
ExportSpecifier : IdentifierName as IdentifierName
```

1. Return a List containing the first IdentifierName.

#### 15.2.3.11 RS: Evaluation <div id="sec-exports-runtime-semantics-evaluation"></div>

```
ExportDeclaration :
export * FromClause ;
export ExportClause FromClause ;
export ExportClause ;
```

1. Return NormalCompletion(empty).

```
ExportDeclaration : export VariableStatement
```

1. Return the result of evaluating VariableStatement.

```
ExportDeclaration : export Declaration
```

1. Return the result of evaluating Declaration.

```
ExportDeclaration : export default HoistableDeclaration
```

1. Return the result of evaluating HoistableDeclaration.

```
ExportDeclaration : export default ClassDeclaration
```

1. Let value be the result of BindingClassDeclarationEvaluation of ClassDeclaration.
2. ReturnIfAbrupt(value).
3. Let className be the sole element of BoundNames of ClassDeclaration.
4. If className is "\*default\*", then
a. Let env be the running execution context's LexicalEnvironment.
b. Perform ? InitializeBoundName("*default*", value, env).
5. Return NormalCompletion(empty).

```
ExportDeclaration : export default AssignmentExpression ;
```

1. If IsAnonymousFunctionDefinition(AssignmentExpression) is true, then
a. Let value be the result of performing NamedEvaluation for AssignmentExpression with argument
"default".
2. Else,
a. Let rhs be the result of evaluating AssignmentExpression.
b. Let value be ? GetValue(rhs).
3. Let env be the running execution context's LexicalEnvironment.
4. Perform ? InitializeBoundName("\*default\*", value, env).
5. Return NormalCompletion(empty).