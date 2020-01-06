# 18 全局对象

全局对象:

- 在控件进入任何执行上下文之前创建的。
- 没有[[Construct]]内部方法；它不能与new运算符一起用作构造函数。
- 没有[[Call]]内部方法；它不能作为函数调用。
- 有一个[[Prototype]]内部插槽，其值取决于实现。
- 除本规范中定义的属性外，还可能具有主机定义的属性。这可能包括其值为全局对象本身的属性。

## 18.1 全局对象的值属性 <div id="sec-value-properties-of-the-global-object"></div>

### 18.1.1 Infinity <div id="sec-value-properties-of-the-global-object-infinity"></div>

Infinity的值为+∞（请参见6.1.6）。该属性具有以下属性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 18.1.2 NaN <div id="sec-value-properties-of-the-global-object-nan"></div>

NaN的值为NaN（请参见6.1.6）。该属性具有以下属性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 18.1.3 undefined <div id="sec-undefined"></div>

undefined的值是undefined（请参见6.1.1）。该属性具有以下属性 { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

## 18.2 全局对象的函数属性 <div id="sec-function-properties-of-the-global-object"></div>

### 18.2.1 eval ( x ) <div id="sec-eval-x"></div>

eval函数是％eval％内部对象。当使用一个参数x调用eval函数时，将执行以下步骤：

1. 断言：执行上下文堆栈至少具有两个元素。
2. 令 callerContext 为执行上下文堆栈的第二个元素。
3. 令 callerRealm 为 callerContext 的 Realm.
4. 令 calleeRealm 为 当前 Realm 记录项.
5. 执行 ? HostEnsureCanCompileStrings(callerRealm, calleeRealm).
6. 返回 ? PerformEval(x, calleeRealm, false, false).

#### 18.2.1.1 运行时语义：PerformEval ( x, evalRealm, strictCaller, direct ) <div id="sec-performeval"></div>

具有参数x，evalRealm，strictCaller和direct的抽象操作PerformEval执行以下步骤：

1. 断言：若 direct 是 false, 那么 strictCaller 也是 false.
2. 若 Type(x) 不是 String, 返回 x.
3. 令 thisEnvRec 为 ! GetThisEnvironment().
4. 若 thisEnvRec 是函数环境记录项, 那么
    1. 令 F 为 thisEnvRec.[[FunctionObject]].
    2. 令 inFunction 为 true.
    3. 令 inMethod 为 thisEnvRec.HasSuperBinding().
    4. 若 F.[[ConstructorKind]] 是 "derived", 令 inDerivedConstructor 为 true; 否则, 令 inDerivedConstructor 为 false.
5. 否则，
    1. 令 inFunction 为 false.
    2. 令 inMethod 为 false.
    3. 令 inDerivedConstructor 为 false.
6. 令脚本为ECMAScript代码，它是解析x的结果，对于目标符号脚本，x解释为UTF-16编码的Unicode文本（如6.1.4中所述）。如果inFunction为false，则将应用18.2.1.1.1中的其他早期错误规则。如果inMethod为false，则将应用18.2.1.1.2中的其他早期错误规则。如果inDerivedConstructor为false，则会应用18.2.1.1.3中的其他早期错误规则。如果解析失败，则抛出SyntaxError异常。如果检测到任何早期错误，则根据错误的类型抛出SyntaxError或ReferenceError异常（但另请参见第16节）。解析和早期错误检测可以以依赖于实现的方式混合在一起。
7. 若 script 包含 ScriptBody 是 false, 返回 undefined.
8. 令 body 为 ScriptBody 的 script.
9. 若 strictCaller 是 true, 令 strictEval 为 true.
10. 否则，令 strictEval 为 script 的 IsStrict.
11. 令 ctx 为运行时执行上下文。
12. 注意:如果direct为真，则ctx将是执行直接求值的执行上下文。如果direct为false，则ctx将是调用eval函数的执行上下文。
13. 若 direct 是 true，那么
     1. 令 lexEnv 为 NewDeclarativeEnvironment(ctx 的词法环境).
     2. 令 varEnv 为 ctx 的 VariableEnvironment.
14. 否则，
     1. 令 lexEnv 为 NewDeclarativeEnvironment(evalRealm.[[GlobalEnv]]).
     2. 令 varEnv 为 evalRealm.[[GlobalEnv]].
15. 若 strictEval 是 true, 设置 varEnv 为 lexEnv.
16. 若 ctx 还没有被挂起, 挂起 ctx.
17. 令 evalCxt 为一个新的ECMAScript代码执行上下文。
18. 设置 evalCxt 的 Function 为 null.
19. 设置 evalCxt 的 Realm 为 evalRealm.
20. 设置 evalCxt 的 ScriptOrModule 为 ctx 的 ScriptOrModule.
21. 设置 evalCxt 的 VariableEnvironment 为 varEnv.
22. 设置 evalCxt 的 LexicalEnvironment 为 lexEnv.
23. Push evalCxt on to the execution context stack; evalCxt is now运行时执行上下文。
24. 令 result 为 EvalDeclarationInstantiation(body, varEnv, lexEnv, strictEval).
25. 若 result.[[Type]] 是 normal，那么
     1. 设置 result 为 the result of evaluating body.
26. 若 result.[[Type]] 是 normal 并且 result.[[Value]] 是 empty，那么
     1. 设置 result 为 NormalCompletion(undefined).
27. 挂起evalCxt并将其从执行上下文堆栈中移除。
28. 将当前位于执行上下文堆栈顶部的上下文恢复为正在运行的执行上下文
29. 返回 Completion(result).

> 注：如果调用上下文正在计算形式参数初始化器，或者调用上下文的代码或eval代码是严格模式代码，则eval代码不能在调用上下文的变量环境中实例化调用eval的变量或函数绑定。
> 相反，这样的绑定是在一个新的VariableEnvironment中实例化的，这个环境只能被eval代码访问。
> 由let、const或类声明引入的绑定总是在新的LexicalEnvironment中实例化。

##### 18.2.1.1.1 eval外部函数的附加早期错误规则 <div id="sec-performeval-rules-outside-functions"></div>

当直接eval调用发生在任何函数之外时，PerformEval将应用这些静态语义。

```
ScriptBody : StatementList
```

- 如果StatementList包含NewTarget，这是一个语法错误

##### 18.2.1.1.2 eval外部方法的附加早期错误规则 <div id="sec-performeval-rules-outside-methods"></div>

当直接eval调用发生在MethodDefinition之外时，PerformEval将应用这些静态语义。

```
ScriptBody : StatementList
```

- 如果StatementList包含SuperProperty，则为语法错误。

##### 18.2.1.1.3 eval外部构造方法的附加早期错误规则 <div id="sec-performeval-rules-outside-constructors"></div>

当直接eval调用发生在ClassDeclaration或ClassExpression的构造方法之外时，PerformEval将应用这些静态语义。

```
ScriptBody : StatementList
```

- 如果StatementList包含SuperCall，则为语法错误。

#### 18.2.1.2 HostEnsureCanCompileStrings ( callerRealm, calleeRealm ) <div id="sec-hostensurecancompilestrings"></div>

HostEnsureCanCompileStrings是实现定义的抽象操作，它允许主机环境阻止某些ECMAScript函数，这些函数使开发人员可以将字符串编译为ECMAScript代码。

HostEnsureCanCompileStrings的实现可以正常或突然完成。任何突然的完成都会传播给它的调用者。 HostEnsureCanCompileStrings的默认实现是无条件返回空的正常完成。

#### 18.2.1.3 运行时语义：EvalDeclarationInstantiation ( body, varEnv, lexEnv, strict ) <div id="sec-evaldeclarationinstantiation"></div>

当使用参数body，varEnv，lexEnv和strict调用抽象操作EvalDeclarationInstantiation时，将执行以下步骤：

1. 令 varNames 为 body 的 VarDeclaredNames 
2. 令 varDeclarations 为 body 的 VarScopedDeclarations 
3. 令 lexEnvRec 为 lexEnv 的 EnvironmentRecord.
4. 令 varEnvRec 为 varEnv 的 EnvironmentRecord.
5. 若 strict 是 false，那么
   1. 若 varEnvRec 是全局环境记录项，那么
           1. 对于每一个在 varNames 中的 name，执行
                 1. 若 varEnvRec.HasLexicalDeclaration(name) 是 true, 抛出 SyntaxError 异常
                2. 注意：eval不会创建一个全局var声明，该声明会被全局词法声明所遮盖。
   2. 令 thisLex 为 lexEnv.
   3. 断言：以下循环将终止。
   4. 重复，当 thisLex 与 varEnv 不同，
           1. 令 thisEnvRec 为 thisLex 的 EnvironmentRecord.
           2. 若 thisEnvRec 对象环境记录，那么
                 1. 注意：with语句的环境不能包含任何词法声明，因此不需要检查它是否存在 var / let 提升冲突。
                 2. 对于varNames中的每个name，执行
                     1. 若 thisEnvRec.HasBinding(name) 是 true，那么
                           1. 抛出 SyntaxError 异常
                           2. 注意：附件B.3.5定义了上述步骤的替代语义。
                     2. 注意：直接eval不会将var声明放在同名的词法声明上。
           3. 设置 thisLex 为 thisLex 的外部环境参考。
6. 令 functionsToInitialize 为新的空列表
7. 令 declaredFunctionNames 为新的空列表
8. 对于varDeclarations中的每个d，以相反的顺序排列，执行
   1. 若 d 既不是VariableDeclaration也不是ForBinding或BindingIdentifier，那么
     1. 断言：d是FunctionDeclaration，GeneratorDeclaration，AsyncFunctionDeclaration或AsyncGeneratorDeclaration。
     2. 注意：如果有多个具有相同名称的函数声明，则使用最后一个声明。
     3. 令 fn 为 d 的 BoundNames 的唯一元素。
     4. 若 fn 不是 declaredFunctionNames 的元素，那么
           1. 若 varEnvRec 是全局环境记录项，那么
             1. 令 fnDefinable 为 ? varEnvRec.CanDeclareGlobalFunction(fn).
             2. 若 fnDefinable 是 false, 抛出 TypeError 异常
           2. 将 fn 附加到声明的 FunctionNames。
           3. 插入 d 作为 functionsToInitialize 的第一个元素。
9. 注意：附件B.3.3.3在这一点上增加了其他步骤。
10. 令 declaredVarNames 为新的空列表
11. 对于varDeclarations中的每个d，执行
    1. 若 d 是 VariableDeclaration, ForBinding, 或 BindingIdentifier，那么
            1. 对于 d 的 BoundNames 中的每个字符串 vn，执行
                  1. 若 vn 不是 declaredFunctionNames 的元素，那么
                    1. 若 varEnvRec 是全局环境记录项，那么
                         1. 令 vnDefinable 为 ? varEnvRec.CanDeclareGlobalVar(vn).
                         2. 若 vnDefinable 是 false, 抛出 TypeError 异常
                    2. 若 vn 不是 declaredVarNames 的元素，那么
                         1. 将vn附加到clarifiedVarNames。
12. 注意：在该算法步骤之后，不会发生异常终止，除非varEnvRec是全局环境记录并且全局对象是Proxy异类对象。
13. 令 lexDeclarations 为 body 的 LexicallyScopedDeclarations 
14. 对于lexDeclarations中的每个元素d，执行
    1. 注意：词汇声明的名称仅在此处实例化，而不初始化。
    2. 对于d的BoundNames中的每个元素dn，执行
            1. 如果d的IsConstantDeclaration为true，那么
                  1. 执行 ? lexEnvRec.CreateImmutableBinding(dn, true).
            2. 否则，
               1. 执行 ? lexEnvRec.CreateMutableBinding(dn, false).
15. 对于functionsToInitialize中的每个解析节点f，执行
    1. 令 fn 为f的BoundNames的唯一元素
    2. 令 fo 为使用参数lexEnv为f执行InstantiateFunctionObject的结果。
    3. 若 varEnvRec 是全局环境记录项，那么
      4. 执行 ? varEnvRec.CreateGlobalFunctionBinding(fn, fo, true).
    5. 否则，
      6. 令 bindingExists 为 varEnvRec.HasBinding(fn).
      7. 若 bindingExists 是 false，那么
          1. 令 status 为 ! varEnvRec.CreateMutableBinding(fn, true).
          2. 断言：由于第12步之前的验证，状态不是突然完成。
          3. 执行 ! varEnvRec.InitializeBinding(fn, fo).
      8. 否则，
            1. 执行 ! varEnvRec.SetMutableBinding(fn, fo, false).
16. 对于clarifiedVarNames中的每个String vn，按列表顺序，执行
    1. 若 varEnvRec 是全局环境记录项，那么
      1. 执行 ? varEnvRec.CreateGlobalVarBinding(vn, true).
    2. 否则，
      1. 令 bindingExists 为 varEnvRec.HasBinding(vn).
      2. 若 bindingExists 是 false，那么
          1. 令 status 为 ! varEnvRec.CreateMutableBinding(vn, true).
          2. 断言：由于第12步之前的验证，状态不是突然完成。
          3. 执行 ! varEnvRec.InitializeBinding(vn, undefined).
17. 返回 NormalCompletion(empty).

> 注：B.3.5中描述了该算法的替代版本。

### 18.2.2 isFinite ( number ) <div id="sec-isfinite-number"></div>

isFinite函数是％isFinite％内部对象。当使用一个参数号调用isFinite函数时，将执行以下步骤：

1. 令 num 为 ? ToNumber(number).
2. 若 num 是 NaN, +∞, or -∞，返回 false.
3. 否则，返回 true.

### 18.2.3 isNaN ( number ) <div id="sec-isnan-number"></div>

isNaN函数是％isNaN％内部对象。当使用一个参数号调用isNaN函数时，将执行以下步骤：

1. 令 num 为 ? ToNumber(number).
2. 若 num 是 NaN，返回 true.
3. 否则，返回 false.

> 注：测试ECMAScript代码值X是否为NaN的可靠方式是形式为X!==X。只有当X为NaN时，结果才为true。

### 18.2.4 parseFloat ( string ) <div id="sec-parsefloat-string"></div>

parseFloat函数产生一个Number值，该值由将字符串参数的内容解释为十进制文字来指示。

parseFloat函数是％parseFloat％内部对象。当使用一个参数字符串调用parseFloat函数时，将执行以下步骤：

1. 令 inputString 为 ? ToString(string).
2. 令trimmedString为inputString的子字符串，该子字符串由不是StrWhiteSpaceChar的最左边的代码单元以及该代码单元右边的所有代码单元组成。 （换句话说，删除前导空格。）如果inputString不包含任何此类代码单元，则将trimmedString设为空字符串。
3. 如果trimmedString或trimmedString的任何前缀都不满足StrDecimalLiteral的语法（请参见7.1.3.1），则返回NaN。
4. 令numberString为trimmedString的最长前缀，它可以是trimmedString本身，它满足StrDecimalLiteral的语法。
5. 令 mathFloat 为 numberString 的MV。
6. 若 mathFloat = 0，那么
   1. 如果trimmedString的第一个代码单元是代码单元0x002D（\-），则返回-0。
   2. 返回 +0.
7. 返回 mathFloat的Number值。

> 注：parseFloat只能将字符串的开头部分解释为Number值；它忽略了不能解释为十进制文字表示法一部分的任何代码单元，也没有给出任何此类代码单元被忽略的指示。

### 18.2.5 parseInt ( string, radix ) <div id="sec-parseint-string-radix"></div>

parseInt函数产生一个整数值，该整数值是根据指定的基数解释字符串参数的内容而指定的。字符串中的前导空格将被忽略。如果基数未定义或为0，则假定为10，除非数字以代码单元对0x或0X开头（在这种情况下，基数为16）。如果基数为16，则数字也可以选择以代码单元对0x或0X开头。

parseInt函数是％parseInt％内部对象。调用parseInt函数时，将执行以下步骤：

1. 令 inputString 为 ? ToString(string).
2. 令S为新创建的inputString子字符串，它由不是StrWhiteSpaceChar的第一个代码单元以及该代码单元之后的所有代码单元组成。 （换句话说，删除前导空格。）如果inputString不包含任何此类代码单元，则使S为空字符串。
3. 令 sign 为 1.
4. 若S不为空，并且S的第一个代码单元是代码单元0x002D（-），, 设置 sign 为 -1.
5. 若S不为空并且S的第一个代码单元是代码单元0x002B（+）或代码单元0x002D(-), 从S中删除第一个代码单元。
6. 令 R 为 ? ToInt32(radix).
7. 令 stripPrefix 为 true.
8. 若 R ≠ 0，那么
   1. 若 R < 2 or R > 36，返回 NaN.
   2. 若 R ≠ 16, 设置 stripPrefix 为 false.
9. Else R = 0,
   1. 设置 R 为 10.
10. 若 stripPrefix 是 true，那么
    1. 如果S的长度至少为2，并且S的前两个代码单元为“ 0x”或“ 0X”，那么
      1. 从S中删除前两个代码单元。
      2. 设置 R 为 16.
11. 若S包含不是基数R的代码单元，则令Z为S的子字符串，该子字符串由第一个此类代码单元之前的所有代码单元组成；否则，让Z为S。
12. 若 Z 是 empty，返回 NaN.
13. 令mathInt为Z以基数R表示的数学整数值，使用字母AZ和az表示从10到35的数字。（但是，如果R为10并且Z包含20个以上有效数字，则每个有效数字根据实现的选择，第20个位之后的数字可以用0代替；并且如果R不是2、4、8、10、16或32，则mathInt可以是与实现相关的数学整数的近似值Z以基数R表示的值。）
14. 若 mathInt = 0，那么
    1. 若 sign = -1，返回 -0.
    2. 返回 +0.
15. 令 number 为mathInt的Number值
16. 返回 sign × number.

> 注：parseInt只能将字符串的开头部分解释为整数值；它会忽略无法解释为整数符号一部分的任何代码单元，并且不会给出任何此类代码单元被忽略的指示。

### 18.2.6 URI Handling Functions <div id="sec-uri-handling-functions"></div>

统一资源标识符（URI）是用于标识资源（例如网页或文件）和传输协议的字符串，通过这些协议可以在Internet上访问资源（例如HTTP或FTP）。 ECMAScript语言本身不提供对使用URI的任何支持，除了18.2.6.2、18.2.6.3、18.2.6.4和18.2.6.5中所述的编码和解码URI的函数外。

> 注：ECMAScript的许多实现都提供了操纵网页的其他功能和方法。这些功能超出了本标准的范围。

#### 18.2.6.1 URI 语法和语义 <div id="sec-uri-syntax-and-semantics"></div>

URI由由组件分隔符分隔的一系列组件组成。通用形式为：

```
Scheme : First / Second ; Third ? Fourth
```

其中斜体名称代表组件，“：”，“ /”，“;”和“？”保留用作分隔符。该encodeURI和decodeURI函数旨在与完整的URI一起使用。它们假定URI中的任何保留代码单元均具有特殊含义，因此未进行编码。该encodeURIComponent和decodeURIComponent函数旨在与URI的各个组成部分一起使用。它们假定任何保留代码单元都代表文本，因此必须进行编码，以便当该组件是完整URI的一部分时，它们不会被解释为保留代码单元。

以下词汇语法指定了已编码URI的形式。

**语法**

```
uri :::
	uriCharactersopt
uriCharacters :::
	uriCharacter uriCharactersopt
uriCharacter :::
    uriReserved
    uriUnescaped
    uriEscaped
uriReserved ::: one of
	; / ? : @ & = + $ ,
uriUnescaped :::
    uriAlpha
    DecimalDigit
    uriMark
uriEscaped :::
	% HexDigit HexDigit
uriAlpha ::: one of
	a b c d e f g h i j k l m n o p q r s t u v w x y z A B C D E F G H I J K L M N O
P Q R S T U V W X Y Z
uriMark ::: one of
	- _ . ! ~ * ' ( )
```

> 注：以上语法基于RFC 2396，并不反映最新RFC 3986引入的更改

**运行时语义**

如果上面未列出要包含在URI中的代码单元，或者有时不赋予保留的代码单元特殊含义，则必须对该代码单元进行编码。该代码单元被转换为其UTF-8编码，其中代理对首先从UTF-16转换为相应的代码点值。 （请注意，对于[0，127]范围内的代码单元，这将导致单个octet具有相同的值。）然后，将所得的octet序列转换为字符串，每个octet均以形式为“％”的转义序列表示xx”。

##### 18.2.6.1.1 运行时语义：Encode ( string, unescapedSet ) <div id="sec-encode"></div>

编码和转义过程由抽象操作Encode描述，该操作采用两个String参数string和unescapedSet。

1. 令 strLen 为字符串中的代码单位数。
2. 令 R 为空字符串
3. 令 k 为 0.
4. 重复，
   1. 若 k 等于 strLen，返回 R.
   2. 令 C 为字符串中索引k处的代码单元。
   3. 若 C 在 unescapedSet 中，那么
           1. 令 S 为仅包含代码单元C的String值。
           2. 设置 R 为R和S的先前值的字符串连接。
   4. 否则 C 不在unescapedSet中，
     1. 若 C 是 a trailing surrogate, 抛出 URIError 异常
     2. 若 C 不是前导代理，那么
           1. 令 V 为与代码单位C具有相同数值的代码点。
     3. 否则，
           1. k 增加 1.
           2. 若 k 等于 strLen, 抛出 URIError 异常
           3. 令 kChar 为字符串中索引k处的代码单元。
           4. 若 kChar 不是前导代理, 抛出 URIError 异常
           5. 令 V 为 UTF16Decode(C, kChar).
     4. 令 Octets 为通过对V应用UTF-8转换得到的八位字节列表。
     5. 对于列表顺序中octet的每个元素octet，执行
           1. 令 S 为字符串串联：
         "%"
         octet的字符串表示形式，格式为两位数的大写十六进制数字，如有必要，在左侧添加零
            2. 设置 R 为R和S的先前值的字符串连接。
   5. k 增加 1.

##### 18.2.6.1.2 运行时语义：Decode ( string, reservedSet ) <div id="sec-decode"></div>
通过使用两个字符串参数string和reservedSet的抽象操作Decode描述了转义和解码过程。

1. 令 strLen 为字符串中的代码单位数。
2. 令 R 为空字符串
3. 令 k 为 0.
4. 重复，
    1. 若 k 等于 strLen，返回 R.
        2. 令 C 为字符串中索引k处的代码单元。
        3. 若 C 不是代码单元0x0025 (%)，那么
            1. 令 S 为仅包含代码单元C的String值。
        4. 否则 C是代码单元0x0025 (PERCENT SIGN),
            1. 令 start 为 k.
            2. 若 k + 2 是 大于或等于 strLen, 抛出 URIError 异常
            3. 如果字符串中位于索引（k + 1）和（k + 2）的代码单元不表示十六进制数字，抛出 URIError 异常.
            4. 令 B 为索引（k + 1）和（k + 2）处两个十六进制数字表示的8位值
            5. k 增加 2.
            6. 若B中的最高有效位是 0，那么
                  1. 令 C 为值为B的代码单位。
                      2. 若 C 不在 reservedSet 中，那么
                          1. 令 S 为仅包含代码单元C的String值。
                      3. 否则 C 在reservedSet中,
                          1. 令 S 为字符串的子字符串，从索引开始到索引k（包括索引）。
            7. 否则 B 中的最高有效位是1
                  1. 令 n 为最小的非负整数，使得（B << n）＆0x80等于0。
                      2. 若 n 等于 1 或 n 大于 4, 抛出 URIError 异常
                      3. 令 Octets 为一个大小为n的8位整数列表。
                      4. 设置 Octets[0] 为 B.
                      5. 若 k + (3 × (n - 1)) 是大于等于strLen, 抛出 URIError 异常
                      6. 令 j 为 1.
                      7. 重复，while j < n
                          1. k 增加 1.
                          2. 若字符串中索引k处的代码单元不是代码单元 0x0025 (%), 抛出 URIError 异常
                          3. 若字符串中位于索引（k + 1）和（k + 2）的代码单元不表示十六进制数字, 抛出 URIError 异常
                          4. 令 B 为由索引（k + 1）和（k + 2）处的两个十六进制数字表示的8位值。
                          5. 若B中的两个最高有效位不是10, 抛出 URIError 异常
                          6. k 增加 2.
                          7. 设置 Octets[j] 为 B.
                          8. j 增加 1.
                      8. 若octet不包含Unicode代码点的有效UTF-8编码，引发URIError异常。
                      9. 令 V 为通过对Octets应用UTF-8转换而获得的值，即从Octets列表转换为21位值。
                      10. 令 S 为String值，其代码单位按顺序是UTF16Encoding（V）中的元素。
        5. 设置 R 为 R 和S的先前值的字符串连接。
        6. k 增加 1.

> 注：这种统一资源标识符的语法基于RFC 2396，并且不反映更新的RFC 3986（它代替RFC 2396）。RFC3629中给出了UTF-8的正式描述和实现。
>
> 在UTF-8中，使用1到6个octets的序列对字符进行编码。序列1的唯一octet将高阶位设置为0，其余7位用于编码字符值。在n个octet的序列中，n> 1，初始octet的n个高阶位设置为1，其后的位设置为0。该octet的其余位包含要编码的字符值中的位。接下来的octet的高位均设置为1，随后的位均设置为0，每个位保留6位以包含来自要编码字符的位。表46中指定了ECMAScript字符可能的UTF-8编码。
>
> |                                                     |                                                         |              |              |              |              |
> | --------------------------------------------------- | ------------------------------------------------------- | ------------ | ------------ | ------------ | ------------ |
> | Code Unit Value                                     | Representation                                          | 1st Octet    | 2nd Octet    | 3rd Octet    | 4th Octet    |
> | `0x0000 - 0x007F`                                   | `00000000 0zzzzzzz`                                     | `0zzzzzzz`   |              |              |              |
> | `0x0080 - 0x07FF`                                   | `00000yyy yyzzzzzz`                                     | `110yyyyy`   | `10zzzzzz`   |              |              |
> | `0x0800 - 0xD7FF`                                   | `xxxxyyyy yyzzzzzz`                                     | `1110xxxx`   | `10yyyyyy`  | `10zzzzzz` |              |
> | `0xD800 - 0xDBFF` followed by `0xDC00 - 0xDFFF`     | `110110vv vvwwwwxx` followed by `110111yy yyzzzzzz` | `11110uuu` | `10uuwwww` | `10xxyyyy` | `10zzzzzz` |
> | `0xD800 - 0xDBFF` not followed by `0xDC00 - 0xDFFF` | causes `URIError`                                       |              |              |              |              |
> | `0xDC00 - 0xDFFF`                                   | causes `URIError`                                       |              |              |              |              |
> | `0xE000 - 0xFFFF`                                   | `xxxxyyyy yyzzzzzz`                                   | `1110xxxx` | `10yyyyyy` | `10zzzzzz` |              |
>
> 其中，uuuuu = vvvv + 1代表Unicode标准（替代）第3.8节中添加的0x10000。
>
> 上面的转换将每个代理对（保留其在0xD800到0xDFFF范围内的代码单元值）组合为UTF-32表示形式，并将得到的21位值编码为UTF-8。解码可重建代理对。
>
> RFC 3629禁止解码无效的UTF-8八位位组序列。例如，无效序列C0 80不得解码为代码单元0x0000。遇到此类无效序列时，需要使用Decode算法的实现来引发URIError。

#### 18.2.6.2 decodeURI ( encodedURI ) <div id="sec-decodeuri-encodeduri"></div>

encodeURI函数计算URI的新版本，其中可能由encodeURI函数引入的每个转义序列和此类的UTF-8编码都用其表示的代码点的UTF-16编码代替。无法替换encodeURI无法引入的转义序列。

encodeURI函数是％decodeURI％内部对象。当用一个参数encodedURI调用decodeURI函数时，将执行以下步骤：

1. 令 uriString 为 ? ToString(encodedURI).
2. 令reservedURISet为一个字符串，其中包含在uriReserved中有效的每个代码单元的一个实例加“＃”。
3. 返回 ? Decode(uriString, reservedURISet).

> 注：即使不是保留的URI代码点，也不会从转义序列中解码代码点“＃”。

#### 18.2.6.3 decodeURIComponent ( encodedURIComponent ) <div id="sec-decodeuricomponent-encodeduricomponent"></div>

encodeURIComponent函数计算URI的新版本，其中可能由encodeURIComponent函数引入的每个转义序列和此类的UTF-8编码都用其表示的代码点的UTF-16编码代替。

encodeURIComponent函数是％decodeURIComponent％内部对象。当使用一个已编码URIComponent参数调用decodeURIComponent函数时，将执行以下步骤：

1. 令 componentString 为 ? ToString(encodedURIComponent).
2. 令 reservedURIComponentSet 为空字符串。
3. 返回 ? Decode(componentString, reservedURIComponentSet).

#### 18.2.6.4 encodeURI ( uri ) <div id="sec-encodeuri-uri"></div>

encodeURI函数计算UTF-16编码（6.1.4）URI的新版本，其中某些代码点的每个实例都被一个，两个，三个或四个表示代码点UTF-8编码的转义序列替换。

encodeURI函数是％encodeURI％内部对象。当使用一个参数uri调用encodeURI函数时，将执行以下步骤：

1. 令 uriString 为 ? ToString(uri).
2. 令 unescapedURISet 为一个字符串，其中包含在uriReserved和uriUnescaped加上“＃”有效的每个代码单元的一个实例。
3. 返回 ? Encode(uriString, unescapedURISet).

> 注：即使代码单元“＃”不是保留或未转义的URI代码点，也不会被编码为转义序列。

#### 18.2.6.5 encodeURIComponent ( uriComponent ) <div id="sec-encodeuricomponent-uricomponent"></div>

encodeURIComponent函数计算新版本的UTF-16编码（6.1.4）URI，其中某些代码点的每个实例都被一个，两个，三个或四个表示代码点UTF-8编码的转义序列替换。

encodeURIComponent函数是％encodeURIComponent％内部对象。当使用一个参数uriComponent调用encodeURIComponent函数时，将执行以下步骤：

1. 令 componentString 为 ? ToString(uriComponent).
2. 令 unescapedURIComponentSet 为一个字符串，其中包含在uriUnescapeed中有效的每个代码单元的一个实例。
3. 返回 ? Encode(componentString, unescapedURIComponentSet).

## 18.3 全局对象的构造函数属性 <div id="sec-constructor-properties-of-the-global-object"></div>
### 18.3.1 Array ( . . . ) <div id="sec-constructor-properties-of-the-global-object-array"></div>

See 22.1.1

### 18.3.2 ArrayBuffer ( . . . ) <div id="sec-constructor-properties-of-the-global-object-arraybuffer"></div>

See 24.1.2.

### 18.3.3 Boolean ( . . . ) <div id="sec-constructor-properties-of-the-global-object-boolean"></div>

See 19.3.1.

### 18.3.4 DataView ( . . . ) <div id="sec-constructor-properties-of-the-global-object-dataview"></div>

See 24.3.2.

### 18.3.5 Date ( . . . ) <div id="sec-constructor-properties-of-the-global-object-date"></div>

See 20.3.2

### 18.3.6 Error ( . . . ) <div id="sec-constructor-properties-of-the-global-object-error"></div>

See 19.5.1

### 18.3.7 EvalError ( . . . ) <div id="sec-constructor-properties-of-the-global-object-evalerror"></div>

See 19.5.5.1

### 18.3.8 Float32Array ( . . . ) <div id="sec-float32array"></div>

See 22.2.4.

### 18.3.9 Float64Array ( . . . ) <div id="sec-float64array"></div>

See 22.2.4.

### 18.3.10 Function ( . . . ) <div id="sec-constructor-properties-of-the-global-object-function"></div>

See 19.2.1.

### 18.3.11 Int8Array ( . . . ) <div id="sec-int8array"></div>

See 22.2.4.

### 18.3.12 Int16Array ( . . . ) <div id="sec-int16array"></div>

See 22.2.4.

### 18.3.13 Int32Array ( . . . ) <div id="sec-int32array"></div>

See 22.2.4.

### 18.3.14 Map ( . . . ) <div id="sec-map"></div>

See 23.1.1.

### 18.3.15 Number ( . . . ) <div id="sec-constructor-properties-of-the-global-object-number"></div>

See 20.1.1.

### 18.3.16 Object ( . . . ) <div id="sec-constructor-properties-of-the-global-object-object"></div>

See 19.1.1.

### 18.3.17 Promise ( . . . ) <div id="sec-constructor-properties-of-the-global-object-promise"></div>

See 25.6.3.

### 18.3.18 Proxy ( . . . ) <div id="sec-constructor-properties-of-the-global-object-proxy"></div>

See 26.2.1.

### 18.3.19 RangeError ( . . . ) <div id="sec-constructor-properties-of-the-global-object-rangeerror"></div>

See 19.5.5.2

### 18.3.20 ReferenceError ( . . . ) <div id="sec-constructor-properties-of-the-global-object-referenceerror"></div>

See 19.5.5.3.

### 18.3.21 RegExp ( . . . ) <div id="sec-constructor-properties-of-the-global-object-regexp"></div>

See 21.2.3.

### 18.3.22 设置 ( . . . ) <div id="sec-set"></div>

See 23.2.1.

### 18.3.23 SharedArrayBuffer ( . . . ) <div id="sec-constructor-properties-of-the-global-object-sharedarraybuffer"></div>

See 24.2.2.

### 18.3.24 String ( . . . ) <div id="sec-constructor-properties-of-the-global-object-string"></div>

See 21.1.1.

### 18.3.25 Symbol ( . . . ) <div id="sec-constructor-properties-of-the-global-object-symbol"></div>

See 19.4.1.

### 18.3.26 SyntaxError ( . . . ) <div id="sec-constructor-properties-of-the-global-object-syntaxerror"></div>

See 19.5.5.4.

### 18.3.27 TypeError ( . . . ) <div id="sec-constructor-properties-of-the-global-object-typeerror"></div>

See 19.5.5.5.

### 18.3.28 Uint8Array ( . . . ) <div id="sec-uint8array"></div>

See 22.2.4.

### 18.3.29 Uint8ClampedArray ( . . . ) <div id="sec-uint8clampedarray"></div>

See 22.2.4.

### 18.3.30 Uint16Array ( . . . ) <div id="sec-uint16array"></div>

See 22.2.4.

### 18.3.31 Uint32Array ( . . . ) <div id="sec-uint32array"></div>

See 22.2.4

### 18.3.32 URIError ( . . . ) <div id="sec-constructor-properties-of-the-global-object-urierror"></div>

See 19.5.5.6.

### 18.3.33 WeakMap ( . . . ) <div id="sec-constructor-properties-of-the-global-object-weakmap"></div>

See 23.3.1

### 18.3.34 WeakSet ( . . . ) <div id="sec-constructor-properties-of-the-global-object-weakset"></div>

See 23.4.

## 18.4 全局对象的其他属性 <div id="sec-other-properties-of-the-global-object"></div>
### 18.4.1 Atomics <div id="sec-atomics"></div>

See 24.4.

### 18.4.2 JSON <div id="sec-json"></div>

See 24.5.

### 18.4.3 Math <div id="sec-math"></div>

See 20.2.

### 18.4.4 Reflect <div id="sec-reflect"></div>

See 26.1