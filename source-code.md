```
description ECMAScript代码使用Unicode表示。ECMAScript源文本是一系列代码点。从U+0000到U+10FFFF的所有Unicode码点，包括代理码点，都可能出现在ECMAScript语法允许的源文本中。用于存储和交换ECMAScript源文本的实际编码与该规范无关。不管外部源文本如何编码，符合ECMAScript的实现都将源文本当作源字符值的等效序列来处理，每个源字符都是一个Unicode码点。不需要执行ECMAScript一致性实现即可对源代码进行任何标准化文本，或者表现得好像他们正在对源文本进行规范化。
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,ECMAScript语言 源代码,source-code
```
# 10 ECMAScript语言：源代码
## 10.1 源文本 <div id="source-text"></div>

语法：

```
SourceCharacter::
	any Unicode code point
```

ECMAScript代码使用Unicode表示。ECMAScript源文本是一系列代码点。从U+0000到U+10FFFF的所有Unicode码点，包括代理码点，都可能出现在ECMAScript语法允许的源文本中。用于存储和交换ECMAScript源文本的实际编码与该规范无关。不管外部源文本如何编码，符合ECMAScript的实现都将源文本当作源字符值的等效序列来处理，每个源字符都是一个Unicode码点。不需要执行ECMAScript一致性实现即可对源代码进行任何标准化文本，或者表现得好像他们正在对源文本进行规范化。

即使用户可能会将整个序列视为单个字符，也将组合字符序列的组成部分视为单独的Unicode码点。

> 注意：
>
> 在字符串文字，正则表达式文字，模板文字和标识符中，任何Unicode码点也可以使用Unicode转义序列表示，该转义序列明确表示代码点的数值。在注释中，这样的转义序列实际上会被忽略为注释的一部分。
>
> ECMAScript在Unicode转义序列的行为方面与Java编程语言不同。例如，在Java程序中，如果Unicode转义序列\ u000A出现在单行注释中，它将被解释为行终止符（Unicode代码点U + 000A为LINE FEED（LF）），因此下一个代码重点不是评论的一部分。同样，如果Unicode转义序列\ u000A出现在Java程序的字符串文字中，则它也被解释为行终止符，这在字符串文字中是不允许的-必须写\ n而不是\ u000A才能引起LINEFEED（LF）成为字符串文字的String值的一部分。在ECMAScript程序中，注释中出现的Unicode转义序列永远不会被解释，因此不会有助于注释的终止。同样，在ECMAScript程序的字符串文字中出现的Unicode转义序列始终对文字有帮助，并且永远不会解释为行终止符或可能会终止字符串文字的代码点。

### 10.1.1 SS: UTF16Encoding ( cp ) <div id="sec-utf16encoding"></div>

数字代码点值cp的UTF16Encoding如下确定：

1. 断言: 0 ≤ cp ≤ 0x10FFFF.
2. 若 cp ≤ 0xFFFF, 返回 cp.
3. 令 cu1 为 floor((cp - 0x10000) / 0x400) + 0xD800.
4. 令 cu2 为 ((cp - 0x10000) modulo 0x400) + 0xDC00.
5. 返回由 cu1 和 cu2 组成的码元序列。

### 10.1.2 SS: UTF16Decode ( lead, trail ) <div id="sec-utf16decode"></div>

通过执行以下步骤，将形成UTF-16代理对的两个代码单元（前导和尾随）转换为代码点：

1. 断言: lead 是先行代理，trail 是尾代理
2. 令 cp 为 (lead - 0xD800) × 0x400 + (trail - 0xDC00) + 0x10000.
3. 返回码点 cp

## 10.2 源代码类型 <div id="type-of-source-code"></div>

ECMAScript代码有四种类型：

全局代码是被视为ECMAScript脚本的源文本。特定脚本的全局代码不包含作为FunctionDeclaration，FunctionExpression，GeneratorDeclaration，GeneratorExpression，AsyncFunctionDeclaration，AsyncFunctionExpression，AsyncGeneratorDeclaration，AsyncGeneratorExpression，MethodDefinition，ArrowFunction，AsyncArrowFunction，ClassDeclaration 或 ClassExpression。

Eval代码是提供给内置 eval 函数的源文本。更准确地说，如果内置eval函数的参数是String，则将其视为ECMAScript脚本。特定eval调用的eval代码是该脚本的全局代码部分。

函数代码是经过解析的源文本，以提供ECMAScript功能对象的[[ECMAScriptCode]]和[[FormalParameters]]内部插槽的值（请参见9.2）。特定ECMAScript函数的函数代码不包含任何源文本，这些源文本被解析为嵌套的FunctionDeclaration，FunctionExpression，GeneratorDeclaration，GeneratorExpression，AsyncFunctionDeclaration, AsyncFunctionExpression, AsyncGeneratorDeclaration, AsyncGeneratorExpression, MethodDefinition, ArrowFunction, AsyncArrowFunction, ClassDeclaration, 或 ClassExpression. 

模块代码是源文本，它是作为ModuleBody提供的代码。初始化模块时直接执行该代码。特定模块的模块代码不包含作为嵌套的FunctionDeclaration，FunctionExpression，GeneratorDeclaration，GeneratorExpression，AsyncFunctionDeclaration，AsyncFunctionExpression，AsyncGeneratorDeclaration，AsyncGeneratorExpression，MethodDefinition，ArrowFunction，AsyncArrowFunction，ClassDeclaration或ClassExpression的一部分解析的任何源文本。

> 注意：
>
> 通常将函数代码作为函数定义（14.1），箭头函数定义（14.2），方法定义（14.3），生成器函数定义（14.4），异步函数定义（14.7），异步生成器函数定义（14.5），和异步箭头函数（14.8）。函数代码还从 Function 构造函数（19.2.1.1），GeneratorFunction构造函数（25.2.1.1）和 AsyncFunction 构造函数（25.7.1.1）的参数派生。

### 10.2.1 严格模式代码 <div id="strict-mode-code"></div>

可以使用非限制或严格模式的语法和语义来处理ECMAScript脚本语法单元。在以下情况下，代码被解释为严格模式代码：

如果全局代码以包含使用严格指令的指令序言开头，则它是严格模式代码。

模块代码始终是严格模式代码。

ClassDeclaration或ClassExpression的所有部分都是严格模式代码。

如果Eval代码以包含使用严格指令的指令序言开头，或者如果eval的调用是严格模式代码中包含的直接eval，则Eval代码为严格模式代码。

如果关联的FunctionDeclaration，FunctionExpression，GeneratorDeclaration，GeneratorExpression，AsyncFunctionDeclaration，AsyncFunctionExpression，AsyncGeneratorDeclaration，AsyncGeneratorExpression，MethodDefinition，ArrowFunction或AsyncArrowFunction包含在严格模式代码中，或者产生该函数的[[[ECMAScriptCode]]内部插槽以包含使用严格指令的指令序言开头，则函数代码是严格模式代码。

如果最后一个参数是一个字符串，则该函数代码作为内置函数，生成器，AsyncFunction和AsyncGenerator构造函数的参数提供的函数代码是严格模式代码，该字符串在处理时是一个以包含使用严格性的指令序言开头的FunctionBody指示。 

不是严格模式代码的ECMAScript代码称为非严格代码。

### 10.2.2 非ECMAScript函数 <div id="non-ecmascript-functions"></div>

ECMAScript实现可能支持对其可求值行为是异类对象的函数求值，除了通过ECMAScript代码之外，还可以用一些可执行代码的实现定义形式来表示。从调用或由此类非ECMAScript函数调用的ECMAScript代码函数的角度来看，从语义上无法观察到函数对象是ECMAScript代码函数还是非ECMAScript函数。