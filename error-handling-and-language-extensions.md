# 16 错误处理和语言扩展

在解析执行相关ECMAScript语言结构时，实现必须报告大多数错误。早期错误是一种错误，可以在解析执行包含错误的脚本中的任何构造之前检测到并报告该错误。早期错误的存在阻止了对结构的解析执行。实现必须报告脚本中的早期错误，这是在ParseScript中解析该脚本的一部分。在解析执行模块并且从未初始化模块时，报告模块中的早期错误。在调用eval时会报告eval码中的早期错误，这会阻止解析执行eval码。不是早期错误的所有错误都是运行时错误。

实现必须将本规范“静态语义：早期错误”子节中列出的任何情况的发生报告为早期错误。

即使编译器可以证明构造在任何情况下都无法执行而没有错误，实现也不应将其他类型的错误视为早期错误。在这种情况下，实现可能会发出预警，但在实际执行相关构造之前，它不应报告错误。

一个实现应报告指定的所有错误，但以下情况除外：

- 除16.2中的限制外，实现可以扩展脚本语法，模块语法以及正则表达式模式或标志语法。为此，允许被允许抛出SyntaxError的所有操作（例如调用eval，使用正则表达式文字或使用Function或RegExp构造函数）都表现出实现定义的行为，而不是在遇到实现定义的扩展时抛出SyntaxError脚本语法或正则表达式模式或标志语法
- 除16.2中的限制外，实现可以提供其他类型，值，对象，属性和
  超出本规范中描述的功能。这可能导致构造（例如在全局范围内查找变量）具有实现定义的行为，而不是引发错误（例如ReferenceError）。

## 16.1 HostReportErrors ( errorList ) <div id="sec-host-report-errors"></div>

HostReportErrors是实现定义的抽象操作，允许主机环境报告解析错误，早期错误和运行时错误。

在所有情况下，HostReportErrors的实现都必须正常完成。 HostReportErrors的默认实现是无条件返回空的正常完成。

> 注：errorList将是ECMAScript语言值的列表。如果错误是解析错误或早期错误，则这些错误始终是SyntaxError或ReferenceError对象。但是，运行时错误可以是任何ECMAScript值。

## 16.2 禁止扩展 <div id="sec-forbidden-extensions"></div>

实现不得以下列方式扩展此规范：

- 使用严格的模式代码中的语法构造函数定义的ECMAScript函数对象不能使用自己的名为“caller或“arguments”的属性来创建。也不应该为使用ArrowFunction，MethodDefinition，GeneratorDeclaration，GeneratorExpression，AsyncGeneratorDeclaration，AsyncGeneratorExpression，ClassDeclaration，ClassExpression，AsyncFunctionDeclaration，AsyncFunctionExpression或AsyncArrowFunction定义的函数对象创建此类自己的属性，无论该定义是否包含在严格模式代码中。内置函数，使用Function构造函数创建的严格函数，使用Generator构造函数创建的生成器函数，使用AsyncFunction构造函数创建的异步函数以及使用bind方法创建的函数也不得使用此类自己的属性来创建

- 如果实现使用自己的名为“caller”的属性扩展任何函数对象，则使用[[Get]]或[[GetOwnProperty]]观察到的该属性的值一定不能是严格的函数对象。如果它是访问器属性，则该属性的[[Get]]属性值的函数在调用时绝不能返回严格函数。

- 映射或未映射的参数对象都不能使用自己的名为“caller”的属性来创建。除ECMA-402中指定的以外，不得扩展以下方法的行为：

  Object.prototype.toLocaleString, Array.prototype.toLocaleString, Number.prototype.toLocaleString, Date.prototype.toLocaleDateString, Date.prototype.toLocaleString, Date.prototype.toLocaleTimeString, String.prototype.localeCompare, %TypedArray%.prototype.toLocaleString.

- 当存在[U]语法参数时，不得扩展21.2.1和B.1.4中的RegExp模式语法以将任何源字符A-Z或a-z识别为IdentityEscape[+U]。

- 语法扩展不得以任何允许token的方式扩展：立即跟随与BindingIdentifier非终结符匹配的源文本。
- 在处理严格模式代码时，如B.1.1中所述，不得将NumericLiteral的语法扩展为包括LegacyOctalIntegerLiteral，并且不得将DecimalIntegerLiteral的语法扩展为包括NonOctalDecimalIntegerLiteral。
- 不得将TemplateCharacter扩展为包括B.1.2中定义的LegacyOctalEscapeSequence。在处理严格模式代码时，不得支持B.3.2，B.3.3，B.3.4和B.3.6中定义的扩展。解析模块目标符号时，不得支持B.1.3中定义的词汇语法扩展。