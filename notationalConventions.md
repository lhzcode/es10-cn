# 5 记法约定

## 5.1 句法和文法 <div id="sec-algorithm-conventions-abstract-operations"></div>

### 5.1.1 上下文无关文法 <div id="sec-context-free-grammars"></div>

一个上下文无关文法由一定数量的产生式组成。每个产生式的左边部分是一个被称为非终结符的抽象符号，右边部分是零或多个非终结符和终结符的有序排列。任何文法，它的终结符都来自指定的字母集。

一个链产生式只含有一个非终结符以及零个或多个终结符作为它的右值。

当从一个叫做目标符的特殊非终结符组成的句子起始，那么给出的上下文无关文法就表示语言，即：将产生式右边序列的非终结符当作左边，进行反复替换的结果就成为可能的终结符序列集合（可能无限）。

### 5.1.2 词法和正则的文法 <div id="sec-lexica-and-regexp-grammars"></div>

第11章给出了 ECMAScript 的词法。此文法的终结符字符（Unicode 代码单元）符合第10.1章定义的 SourceCharacter 的规则。它定义了一套产生式，从目标符 InputElementDiv 或 InputElementRegExp 起始，描述了如何将这样的字符序列转换成一个输入元素序列。

空白字符和注释之外的输入元素构成 ECMAScript 句法的终结符，它们被称为 ECMAScript 的 Token。这些 Token 是，ECMAScript 语言的保留字、标识符、字面量、标点符号。此外，行终止符虽然不被视为 Token，但会成为输入元素流的一部分，用于引导处理 自动插入分号（11.9）。空白字符和单行注释会被简单地丢弃，不会出现在句法的输入元素流中。如果一个多行注释（即形式为“/*...*/”的注释，不管是否跨越多行）不包含行终止符也会简单地丢弃，但如果一个多行注释包含一个或多个行终止符，那么，注释会被替换为一个行终止符，成为句法的输入元素流的一部分。

第21.2.1章 给出了 ECMAScript 的 正则文法。此文法的终结符也由 SourceCharacter 定义。它定义了一套产生式，从目标符 Pattern 起始，描述了如何将这样的字符序列翻译成一个正则表达式模式。

两个冒号“::”作为分隔符分割词法和正则的文法产生式。词法和正则的文法共享某些产生式。

### 5.1.3 数字字符串文法 <div id="sec-numeric-string-grammar"></div>

用于将字符串转换为数字值的另一种文法。此文法与词法的一部分类似，都与数字字面量有关，并且它有作为终结符的SourceCharacter。此文法出现在 7.1.3.1。

三个冒号“:::”作为分隔符分割数字字符串文法的产生式。

### 5.1.4 句法 <div id="sec-syntactic-grammar"></div>

第 11、12、13、14和15 章给出了 ECMAScript 的句法。词法定义的 ECMAScript Token 是此文法的终结符（5.1.2）。它定义了一组起始于 两个可选的目标符 Script 以及 Module 的产生式，描述了怎样的 Token 序列才能形成句法上正确的 ECMAScript 程序。

当一个字符流被解析为 ECMAScript 的脚本或者模块时，它首先通过词法应用程序反复转换为一个输入元素流；然后再用一个句法应用程序解析这个输入元素流。当输入元素流没有更多 Token 时，如果 Token 不能解析为 目标非终结符（脚本或者模块）的单一实例，那么程序在句法上存在错误。

当一个解析成功的时候，它会构造一颗解析树，这颗有根的树每个节点都是一个解析节点。每个解析节点是一个文法中的符号的实例。它代表了一段从这样的符号中派生出来的源代码文本。解析树的根节点代表着整个的源代码文本，是一个解析的目标符号的实例。当一个解析节点是一个非终结符的实例的时候，它也是一个某些将这个非终结符作为左值的产生式的实例。此外，它有零个或多个子节点，产生式右值中的每个符号：每个子节点都是一个是相应符号的实例的解析节点。

每次解析器调用都会实例化新的解析节点，并且即使在相同的源文本的解析之间也不会重用。当且仅当它们代表源文本的相同范围，是相同语法符号的实例且是由相同的解析器调用产生的，解析节点才被视为相同的解析节点。

> 注意 1：多次分析同一个String会导致不同的解析节点，例如：
>
>  eval(str); eval(str); 

> 注意 2：解析节点是规范产物，使用类似的数据结构不需要实现。

只用一个冒号“:”作为分隔符分割句法的产生式。

事实上第 11、12、13、14和15章给出的句法，并不能完全说明一个正确的 ECMAScript 脚本或模块能接受的 Token 序列。一些额外的 Token 序列也被接受，即某些特殊位置（如行结束符前）加入分号可以被文法接受。此外，文法描述的某些 Token 序列不被文法接受，如一个行结束符出现在了“尴尬”的位置。

在某些案例中，为了避免歧义，句法使用广义的产生式，允许token序列不形成一个合法的ECMAScript 脚本或者模块。例如，这项技术被用作对象字面量和对象解构模式中。在这样的场景中，更多的限制性的补充的文法被提供，进一步限制了可接受的token序列。通常，如果“ P未覆盖N”，则早期错误规则将定义错误条件，其中P是解析节点（广义生成的实例），而N是补充语法的非终结符。在此，使用N作为目标符号再次解析最初与P匹配的token序列（如果N使用语法参数，则将它们设置为最初解析P时所使用的值）。如果无法将标记序列解析为N的单个实例且没有标记残留，则会发生错误。随后，算法使用形式为“ P覆盖的N”的短语访问解析结果。这将始终是一个解析节点（N的实例，对于给定的P唯一），因为任何解析失败都会被早期的错误规则检测到。

### 5.1.5 文法标记法 <div id="sec-grammar-notation"></div>

词法、正则表达式文法、字符串数字文法，以及一些其它文法，每当这些文法的终结符被文本直接涉及到时，使用**等宽**字符来显示，它们都在文法产生式中，并且贯穿这份文档。他们表示程序书写正确。所有以这种方式指定的终结符，都可以理解为 Unicode 字符的完整的 ASCII 范围，不是任何其他乌焉成马的 Unicode 范围字符。

**非终结符**以*斜体*显示。一个**非终结符**的定义由**非终结符**名称和其后定义的一个或多个冒号给出。（冒号的数量表示产生式所属的文法。）**非终结符**的右侧有一个或多个替代子紧跟在下一行。 例如，句法定义：

```
 WhileStatement :
   while ( Expression ) Statement
```

表示这个非终结符 WhileStatement 代表 while Token，其后跟左括号 Token，其后跟 Expression，其后跟右括号 Token，其后跟 Statement。这里出现的 Expression 和 Statement 本身是非终结符。另一个例子，句法定义：

```
ArgumentList :
   AssignmentExpression
   ArgumentList , AssignmentExpression
```

表示这个 *ArgumentList* 可以代表一个 *AssignmentExpression*，或 *ArgumentList*，其后跟一个逗号，其后跟一个 *AssignmentExpression*。这个 *ArgumentList* 的定义是递归的，也就是说，它定义它自身。其结果是，一个 *ArgumentList* 可能包含用逗号隔开的任意正数个参数，每个参数表达式是一个 *AssignmentExpression*。这样，非终结符共用了递归的定义。 

终结符或非终结符可能会出现后缀下标 “opt”，表示它是可选符号。实际上包含可选符号的**替代子**包含两个右边部分，一个是省略可选元素的，另一个是包含可选元素的。这意味着：

```
 VariableDeclaration :
   Identifier Initialiser opt
```

是以下的一种缩写：

```
VariableDeclaration:
    BindingIdentifier
    BindingIdentifier Initializer
```

并且：

```
IterationStatement:
    for(LexicalDeclaration Expression opt ; Expression opt)Statement
```

是以下的一种缩写：

```
IterationStatement:
    for(LexicalDeclaration ; Expression opt)Statement
    for(LexicalDeclaration Expression ; Expression opt)Statement
```

是以下的一种缩写：

```
IterationStatement:
    for(LexicalDeclaration ;)Statement
    for(LexicalDeclaration ; Expression)Statement
    for(LexicalDeclaration Expression ;)Statement
    for(LexicalDeclaration Expression ; Expression)Statement
```

所以，在这个例子中，非终结符IterationStatement实际上有4个可选的右值。

一个产生式也许会通过形如“[parameters]”这样的下标注释被参数化，它们也许会作为后缀出现在产生式定义的非终结符中。“parameters”也许是单独的名称，或者一个逗号分隔的名称列表。一个被参数化的产生式是一系列定义参数名称的所有组合的产生式的缩写，以下划线开头，被追加到参数化的非终结符后面。这就意味着：

```
StatementList[Return]: （注意这里的[Return]是下标）
    ReturnStatement
    ExpressionStatement
```

是下面的缩写：

```
StatementList:
    ReturnStatement
    ExpressionStatement
    
StatementList_Return:
    ReturnStatement
    ExpressionStatement
```

再比如： 

```
StatementList[Return, In]:
    ReturnStatement
    ExpressionStatement
```

是下面的缩写： 

```
StatementList:
    ReturnStatement
    ExpressionStatement
    
StatementList_Return:
    ReturnStatement
    ExpressionStatement

StatementList_In:
    ReturnStatement
    ExpressionStatement

StatementList_Return_In:
    ReturnStatement
    ExpressionStatement
```

多个参数产生一个组合后的产生式，但是在一个复杂的语法中，并不一定需要引用它们中的全部内容。 

在一个产生式的右值中引用非终结符也能被参数化，例如： 

```
StatementList:
    ReturnStatement
    ExpressionStatement[+In]
```

 再比如： 

```
StatementList:
    ReturnStatement
    ExpressionStatement[~In]
```

 是下面的缩写： 

```
StatementList:
    ReturnStatement
    ExpressionStatement
```

一个非终结符引用也许既有一个参数列表，又有一个“opt”后缀，例如： 

```
VariableDeclaration:
    BindingIdentifier Initializer[+In]opt  （注意[+In]和opt都是下标）
```

是下面的缩写： 

```
VariableDeclaration:
    BindingIdentifier
    BindingIdentifier Initializer_In
```

 给一个参数在右值的非终结符引用加上前缀“?”使得那个参数依赖于引用当前产生式左值符合的参数名，例如： 

```
VariableDeclaration[In]:
    BindingIdentifier Initializer[?In]
```

 是下面的缩写： 

```
VariableDeclaration:
    BindingIdentifier Initializer

VariableDeclaration_In:
    BindingIdentifier Initializer_In
```

如果一个右值以“[+parameter]”作为前缀，那么那个可选的只有在命名的参数在引用产生式的非终结符时被使用时才可用。如果一个可选的右值以“[~parameter]”作为前缀，那么那个可选的只有在命名的参数在引用产生式的非终结符时没有被使用时才可用。（译注：即`+`表示上下的`[]`必须同时出现，`～`表示不能同时出现）这意味着： 

```
StatementList[Return]:
    [+Return]ReturnStatement
    ExpressionStatement
```

是下面的缩写：

```
StatementList:
    ExpressionStatement
    
StatementList_Return:
    ReturnStatement
    ExpressionStatement
```

如果文法定义的冒号后面出现文字 “**one of**”，那么其后一行或多行出现的每个终结符都是一个选择定义。例如，ECMAScript 包含的词法生产器：

```
 NonZeroDigit :: one of
   1 2 3 4 5 6 7 8 9
```

这仅仅下面写法的一种缩写：

```
NonZeroDigit ::
   1
   2
   3
   4
   5
   6
   7
   8
   9
```

如果产生式的右侧是出现 “[empty]”，它表明，生产器的右侧不包含终结符或非终结符。

如果产生式的右侧出现 “[lookahead ∉ set]”，它表明，给定 set 的成员不得成为产生式紧随其后的 Token。这个 set 可以写成一个大括号括起来的终结符列表。为方便起见，set 也可以写成一个非终结符，在这种情况下，它代表了这个非终结符 set 可扩展所有终结符。 如果set由单个终结符组成，则可以使用“ [lookahead≠terminal]”。 

例如，给出定义

```
 DecimalDigit :: one of
   0 1 2 3 4 5 6 7 8 9
```

```
DecimalDigits ::
   DecimalDigit
   DecimalDigits DecimalDigit
```

 再定义 

```
LookaheadExample ::
   n [lookahead ∉ {1 , 3 , 5 , 7 , 9}] DecimalDigits
   DecimalDigit [lookahead ∉ DecimalDigit]
```

匹配，字母 **n** 后跟随由偶数起始的一个或多个十进制数字，或一个十进制数字后面跟随一个非十进制数字。

类似地，如果“ [lookahead∈set]”出现在产生式的右侧，则表示仅当紧随其后的输入token序列是给定集的成员时，才可以使用产生式。如果set由单个终结符组成，则可以使用“ [lookahead=terminal]”。

如果产生式的右侧出现 “[no LineTerminator here]”，那么它表示此产生式是个受限的产生式：如果  行终结符（LineTerminator）在输入流的指定位置出现，那么此产生式将不会被适用。例如，产生式：

```
ThrowStatement :
   throw [no LineTerminator here] Expression ;
```

表示如果程序中 throw Token 和 Expression 之间的出现行终结符，那么不得使用此产生式。

行终结符除了禁止出现在受限的产生式，可以在输入元素流的任何两个 Token 之间出现任意次数，而不会影响程序的句法验证。

当一个词法产生式或数字字符串文法中出现多字符 Token，它表示此字符序列将注册一个 Token。

使用词组 “but not” 可以指定某些不允许在产生式右侧的扩展，它说明排除这个扩展。例如，产生式：

```
 Identifier ::
   IdentifierName but not ReservedWord
```

此非终结符 Identifier 可以由可替换成 IdentifierName 的字符序列替换，相同的字符序列不能替换 ReservedWord。

最后，对于实际上不可能列出全部可变元的少量非终结符，我们用普通字体写出描述性的短语来描述它们：

```
 SourceCharacter ::
   any Unicode code unit
```

## 5.2 算法约定 <div id="sec-algorithm-conventions"></div>

此规范通常使用带编号的列表来指定算法的步骤。这些算法是用来精确地指定 ECMAScript 语言结构所需的语义。该算法无意暗示任何具体实现使用的技术。在实践中，也许可用更有效的算法实现一个给定功能。 

算法也许被显式的参数化，在这些案例中参数的名称和用法必须作为算法定义的一部分提供。  

算法的步骤可细分为有序的子步骤。子步骤被缩进，可以将自身进一步划分为缩进子步骤。大纲编号约定用于识别分步骤，第一层次的子步骤适用小写字母标记，第二层次的子步骤使用小写罗马数字标记。如果需要超过三个层次，则重复这些规则，第四层次使用数字标记。例如： 

```
1.Top-level step
    a.Substep.
    b.Substep.
        i.Subsubstep.
            1.Subsubsubstep
                a.Subsubsubsubstep
                    i.Subsubsubsubsubstep
```

一个步骤或子步骤可使用谓词“**if**”作为其子步骤的条件。在这种情况下，当谓词为真时子步骤才适用。如果一个步骤或子步骤由单词“**else**”开始，那么它也是一个谓词，否定前面的同一层级的谓词“**if**”。 

一个步骤可以指定其子步骤的迭代应用程序。

一个步骤以“Assert:”开始，断言了其算法的不变条件。此类断言用于产生显式的算法不变量，否则它们将是隐式的。这样的断言不增加任何附加的语义要求，因此不需要由实现来检查。它们仅用于阐明算法。

算法步骤可以使用“ “Let x be someValue ”的形式为任何值声明命名别名。这些别名与引用类似，因为x和someValue都引用相同的基础数据，并且对两者的修改都可见。想要避免这种类似引用行为的算法步骤应显式地复制右侧：“ Let x be a copy of someValue ”会创建someValue的浅拷贝。

声明后，别名可以在任何后续步骤中引用，并且一定不能从别名声明之前的步骤中引用。别名也许会通过形如“Set x to someOtherValue”这样的形式被修改。 

### 5.2.1 抽象操作 <div id="sec-abstract-operations"></div>

为了便于在本规范的多个部分中使用它们，某些算法（称为抽象操作）以参数化功能形式进行命名和编写，以便可以从其他算法中按名称进行引用。通常使用功能性应用程序风格（例如OperationName（arg1，arg2））来引用抽象操作。一些抽象操作被视为模拟类规范抽象的多态调度方法。通常使用诸如someValue.OperationName（arg1，arg2）之类的方法应用程序样式来引用类似于方法的抽象操作。

### 5.2.2 特定语法操作 <div id="sec-syntax-directed-operations"></div>

一个特定的语法操作是一个具有名称的操作，其定义由算法组成，每个算法都与一种ECMAScript语法中的一个或多个生成相关联。具有多个可选定义的产生式通常会对每个可选部分使用不同的算法。当算法与语法产生相关联时，它可以引用产生替代项的结束符和非结束符，就像它们是算法的参数一样。当以这种方式使用时，非终止符号指的是在解析源文本时匹配的实际替代定义。由语法生成匹配的源文本是源文本的一部分，该部分从匹配的第一个终端的开头开始，到匹配的最后一个终端的结尾结束。

当一个算法与一个产生式的可选部分关联的时候，这个可选部分通常不会带上“[ ]” 文法注释。这样的注释只应该影响可选部分的语法识别，不应该对相关的可选部分的语义有影响。

通过使用以下算法中的步骤1、3和4的约定，使用解析节点和（可选）其他参数来调用基于语法的操作：

```
1. Let `status` be the result of performing SyntaxDirectedOperation of SomeNonTerminal.

2. Let `someParseNode` be the parse of some source text.

3. Perform SyntaxDirectedOperation of `someParseNode`.

4. Perform SyntaxDirectedOperation of `someParseNode` passing
"value" as the argument.
```

除非显式地指定，否则，所有相关联的产生式对于每个也许会被应用到这个产生式左值的非终结符的操作都有一个隐式的定义。如果存在的话，这个隐式的定义简单地再次对同样的参数运用这个相同的定义，对于这些相关联的产生式唯一的右值非终结符，随后返回处理结果。例如，假设一些算法有如下形式的步骤：“返回被解析的块的结果（Return the result of evaluating Block）”，并且这里有一个产生式：

```
Block :
    { StatementList }
```

但是解析操作并没有关联这个产生式的算法。那么在这样的案例中，解析操作隐式地包含了下面形式的关联：

**运行时语义: 解析（Runtime Semantics: Evaluation）**

```
Block : { StatementList }

  1. Return the result of evaluating StatementList.
```

### 5.2.3 运行时语义 <div id="sec-runtime-semantics"></div>

必须在运行时被调用的指定语义的算法就叫运行时语义。运行时语义通过抽象操作或者指定语法操作来定义。这样的算法必须返回一个完成记录。

#### 5.2.3.1 隐式完成值 <div id="sec-implicit-completion-values"></div>

本规范的算法常常隐式地返回一个[[Type]]值为normal的完成记录。除非它是上下文中另外非常明显的部分，一个返回一个值的算法语句，那么这个值将不是一个完成值。例如：

1. Return "Infinity".

与下面的是同一个含义：

1. Return NormalCompletion("Infinity").

然而，如果一个“return”语句的值表达式是一个完成记录构造的字面量，那么完成记录的结果将被返回。如果这个值表达式是一个对抽象操作的调用，那么“return”语句简单地返回这个抽象操作产生的完成记录。

抽象操作完成（完成记录）被用作强调之前计算的完成记录被返回。完成（Completion）这个抽象操作接收一个参数，即完成记录（completionRecord），然后执行下面的步骤：

```
1. Assert: completionRecord is a Completion Record.
2. Return completionRecord as the Completion Record of this abstract operation.
```

一个在算法步骤中没有值的“return”语句与下面的是同样的意思：

1. Return NormalCompletion(undefined).

对于任意的在一个上下文中没有显示地需要一个完整的完成记录值的引用，等价于一个显式地对完成记录的[[Value]]字段的引用，除非完成记录是一个打断/中断（abrupt）完成。

#### 5.2.3.2 抛出一个异常 <div id="sec-throw-an-exception"></div>

描述抛出一个异常的算法步骤，例如：

1. Throw a TypeError exception 

意思就是：

1. Return ThrowCompletion(a newly created TypeError object).

#### 5.2.3.3 如果被打断则返回 <div id="sec-returnifabrupt"></div>

算法步骤说明或者等同于： 

1. ReturnIfAbrupt(argument).

 意思就是：

```
1. If argument is an abrupt completion, return argument.
2. Else if argument is a Completion Record, let argument be argument.[[Value]].
```

 算法步骤说明或者等同于： 

```
1. ReturnIfAbrupt(AbstractOperation()).
```

 意思就是：

```
1. Let hygienicTemp be AbstractOperation().
2. If hygienicTemp is an abrupt completion, return hygienicTemp.
3. Else if hygienicTemp is a Completion Record, let hygienicTemp be hygienicTemp.[[Value]].
```

这里的`hygienicTemp`是短暂的（临时的），并且只在ReturnIfAbrupt有关的步骤中中可见。 

算法步骤说明或者等同于： 

```
1. Let result be AbstractOperation(ReturnIfAbrupt(argument)).
```

意思就是： 

```
1. If argument is an abrupt completion, return argument.
2. If argument is a Completion Record, let argument be argument.[[Value]].
3. Let result be AbstractOperation(argument).
```

#### 5.2.3.4 如果被打断则返回缩写 <div id="sec-returnifabrupt-shorthands"></div>

对抽象操作和以?为前缀的指定语法操作的调用暗示着ReturnIfAbrupt需要被应用到结果的完成记录中。例如，步骤：

1. ? OperationName().

等价于下面的步骤：

1. ReturnIfAbrupt(OperationName()).

相似的，对于程序方法的风格，步骤：

1. ? someValue.OperationName().

等价于

1. ReturnIfAbrupt(someValue.OperationName()).

相似的，前缀！被用作暗示下列的对于抽象或者指定语法操作的调用将不会返回一个打断的（abrupt）完成值，并且作为结果的完成记录的[[Value]]字段应该被用作替换操作的返回值，例如，步骤：

1. Let val be ! OperationName().

等价于下面的步骤：

```
1. Let val be OperationName().
2. Assert: val is never an abrupt completion.
3. If val is a Completion Record, set val to val.[[Value]].
```

对于运行时语义的指定语法的操作通过在调用操作前替换！或者？来利用这个缩写。

1. Perform ! SyntaxDirectedOperation of NonTerminal.

### 5.2.4 静态语义 <div id="sec-static-semantics"></div>

上下文无关文法不能足够好的去表达所有定义的规则，不管是一个输入元素的流形成的一个合法的将被解析的ECMAScript脚本（Script）还是模块（Module）。在一些情况中，需要额外的规则通过使用ECMAScript算法约定或者散文要求（prose requirements）来展示。这样的规则总是与一个文法的产生式相关联，被称作产生式的静态语义。

静态语义规则拥有名称，并且通常用一个算法来定义。具有名称的静态语义规则与文法产生式相关联，对于每个可采用的具有名称的静态语义规则，一个包含多个可选部分定义的产生式通常会对每个可选部分定义一个独有的算法。

除非在本规范中其它指定的文法产生式可选部分隐式地包含一个叫做Contains的接收一个值为包含相关产生式的文法的终结符或者非终结符的symbol参数静态语义规则的定义。默认的包含定义为：

```
1. For each child node child of this Parse Node, do
    a. If child is an instance of symbol, return true.
    b. If child is an instance of a nonterminal, then
        i. Let contained be the result of child Contains symbol.
        ii. If contained is true, return true.
2. Return false.
```

上面的定义是显式地重写了规范的产生式。 

一个特殊的静态语义规则是早期（提前）错误规则（Early Error Rule）。早期错误规则定了早期错误条件（参见16章），它们与具体的文法产生式相关联。大多数早期错误规则的解析不是在本规范的算法中显式地被调用。一个符合规范的实现必须在第一次解析脚本或者模块之前，验证所有被用作解析脚本或者模块的产生式的早期错误规则。如果违反了任意的早期错误规则，那么脚本或者模块就是不合法的，并且不能被解析执行。 

### 5.2.5 数学操作 <div id="sec-mathematical-operations"></div>

除非其它地方特别注明不包括无限并且不包括负0（为了与正0区分），数学操作，例如加，减，否定（逻辑非），乘，除以及随后在条款中定义的数学函数应总是被当作是对于数学中所有真实数字计算出来的准确的结果。本规范算法中的浮点运算包含显式地的步骤，它们对于处理无穷，有符号的0，以及舍入是必要的。如果一个数学操作或者函数被应用在一个浮点数上，那么它必须被当作应用到用浮点数表示的具体的数学值；这样的浮点数必须是有限的，并且如果它是+0或者-0的话，那么相应的数学值就是简简单单的0。

数学函数abx(`x`)用来计算`x`的绝对值，如果`x`是负数（小于0），那么结果就是`-x`，否则结果就是`x`本身。 

数学函数min(`x1`, `x2`, ..., `xN`)计算从`x`到`xN`的最小值。数学函数max(`x1`, `x2`, ..., `xN`)计算从`x`到`xN`的最大值。这些数学函数的域（译注：即定义域和值域）以及范围包括+∞和-∞。 

符号“x modulo y”（译注：即x模y）（`y`必须是有限的且是非0的）计算出一个值`k`，它的符号与`y`一致（或者是0），这样的k满足abs(`k`) < abs(`y`)，以及能找到某个整数y，使得`x`-`k` = `q` * `y`。
The mathematical function floor(x) produces the largest integer (closest to positive infinity) that is not larger than x.

> 注意 floor(x) = x-(x modulo 1). （译注：即模运算是向0方向舍入） 

