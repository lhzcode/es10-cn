```
description 首先将ECMAScript脚本或模块的源文本转换成一系列输入元素，这些输入元素是标记，行终止符，注释或空白。从左到右扫描源文本，重复使用尽可能长的代码点序列作为下一个输入元素。
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,lexical-grammar
```

# 11 ECMAScript语言：词法语法

首先将ECMAScript脚本或模块的源文本转换成一系列输入元素，这些输入元素是标记，行终止符，注释或空白。从左到右扫描源文本，重复使用尽可能长的代码点序列作为下一个输入元素。

在几种情况下，词汇输入元素的识别对使用输入元素的语法语法上下文很敏感。这要求词汇语法具有多个目标符号。InputElementRegExpOrTemplateTail 目标用于允许使用 RegularExpressionLiteral，TemplateMiddle 或 TemplateTail 的语法语法上下文中。InputElementRegExp 目标符号用于所有允许使用 RegularExpressionLiteral 但不允许 TemplateMiddle 或 TemplateTail 的语法语法上下文中。InputElementTemplateTail 目标用于所有允许使用 TemplateMiddle 或 TemplateTail 但不允许使用 RegularExpressionLiteral 的语法语法上下文中。在所有其他上下文中，InputElementDiv 用作词汇目标符号。

> 注意
>
> 使用多个词汇目标可确保不会出现会影响自动分号插入的词汇歧义。例如，没有语法语法上下文允许同时使用前导除法或除法分配和前导RegularExpressionLiteral。这不受分号插入的影响（请参见11.9）。在如下示例中：
>
> a = b
> /hi/g.exec(c).map(d);
>
> 如果LineTerminator之后的第一个非空白，非注释代码点为U + 002F（SOLIDUS），并且语法上下文允许进行除法或除法分配，则LineTerminator上不会插入分号。也就是说，以上示例的解释方式与：
>
>  a = b / hi / g.exec(c).map(d);  

语法

```
InputElementDiv ::
    WhiteSpace
    LineTerminator
    Comment
    CommonToken
    DivPunctuator
    RightBracePunctuator
InputElementRegExp ::
    WhiteSpace
    LineTerminator
    Comment
    CommonToken
    RightBracePunctuator
    RegularExpressionLiteral
InputElementRegExpOrTemplateTail ::
    WhiteSpace
    LineTerminator
    Comment
    CommonToken
    RegularExpressionLiteral
    TemplateSubstitutionTail
InputElementTemplateTail ::
    WhiteSpace
    LineTerminator
    Comment
    CommonToken
    DivPunctuator
    TemplateSubstitutionTail
```

## 11.1 Unicode格式控制字符 <div id="unicode-format-control-characters"></div>

Unicode格式控制字符（即Unicode字符数据库中“ Cf”类别中的字符，例如LEFT-TO-RIGHT MARK或RIGHT-TO-LEFT MARK）是用于控制文本范围格式的控制代码缺少高级协议（例如标记语言）。

允许源文本中的格式控制字符有助于编辑和显示是很有用的。所有格式控制字符都可以在注释中以及字符串文字，模板文字和正则表达式文字中使用。

U + 200C（零宽度非JOINER）和U + 200D（零宽度JOINER）是格式控制字符，用于在形成某些语言的单词或短语时进行必要的区分。在ECMAScript源文本中，这些代码点也可以在第一个字符之后的IdentifierName中使用。

U + FEFF（零宽度无间断空格）是一种格式控制字符，主要用于文本的开头，以将其标记为Unicode，并允许检测文本的编码和字节顺序。用于此目的的<ZWNBSP>字符有时也可能在文本开头之后出现，例如，由于连接文件而引起的。在ECMAScript源文本中，<ZWNBSP>代码点被视为空白字符（请参见11.2）。

表31总结了注释，字符串文字和正则表达式文字之外的某些格式控制字符的特殊处理。

| Code Point | Name                      | Abbreviation | Usage          |
| ---------- | ------------------------- | ------------ | -------------- |
| `U+200C`   | ZERO WIDTH NON-JOINER     | \<ZWNJ>      | IdentifierPart |
| `U+200D`   | ZERO WIDTH JOINER         | \<ZWJ>       | IdentifierPart |
| `U+FEFF`   | ZERO WIDTH NO-BREAK SPACE | \<ZWNBSP>    | WhiteSpace     |

## 11.2 空格 <div id="white-space"></div>

空格代码点用于提高源文本的可读性，并将标记（不可分割的词法单位）彼此分隔开，但在其他方面则无关紧要。空格代码点可能出现在任意两个标记之间以及输入的开始或结尾。空格代码点可能出现在StringLiteral，RegularExpressionLiteral，Template或TemplateSubstitutionTail中，这些空格被视为构成文字值一部分的重要代码点。它们也可能出现在注释中，但不能出现在任何其他种类的标记中。

表32中列出了ECMAScript空格代码点。

| Code Point          | Name                                            | Abbreviation |
| ------------------- | ----------------------------------------------- | ------------ |
| `U+0009`            | CHARACTER TABULATION                            | \<TAB>       |
| `U+000B`            | LINE TABULATION                                 | \<VT>        |
| `U+000C`            | FORM FEED (FF)                                  | \<FF>        |
| `U+0020`            | SPACE                                           | \<SP>        |
| `U+00A0`            | NO-BREAK SPACE                                  | \<NBSP>      |
| `U+FEFF`            | ZERO WIDTH NO-BREAK SPACE                       | \<ZWNBSP>    |
| Other category “Zs” | Any other Unicode “Separator, space” code point | \<USP>       |

ECMAScript实现必须将“ Space_Separator”（“ Zs”）类别中列出的WhiteSpace代码点识别为空白。

> 注意：
>
> 除了表32中列出的代码点之外，ECMAScript WhiteSpace有意排除了所有具有Unicode“ White_Space”属性但未归类为“ Space_Separator”（“ Zs”）类别的代码点。

语法

```
WhiteSpace::
    <TAB>
    <VT>
    <FF>
    <SP>
    <NBSP>
    <ZWNBSP>
    <USP>
```



## 11.3 行终止符 <div id="line-terminators"></div>

像空白代码点一样，行终止符代码点用于提高源文本的可读性并将标记（不可分割的词法单位）彼此分开。但是，与空白代码点不同，行终止符对语法的行为有一些影响。通常，行终止符可以出现在任何两个标记之间，但是在某些地方语法语法禁止它们。行终止符还会影响自动分号插入的过程（11.9）。除StringLiteral，Template或TemplateSubstitutionTail外，任何其他令牌中都不能出现行终止符。\<LF>和\<CR>行终止符不能在StringLiteral令牌内出现，除非作为LineContinuation的一部分。

行终止符可以出现在MultiLineComment中，而不能出现在SingleLineComment中。

行终止符包含在\ s类在正则表达式中匹配的空白代码点集中。

表33中列出了ECMAScript行终止符代码点。

| Code Point | Unicode Name              | Abbreviation |
| ---------- | ------------------------- | ------------ |
| `U+000A`   | LINE FEED (LF) 换行       | \<LF>        |
| `U+000D`   | CARRIAGE RETURN (CR) 回车 | \<CR>        |
| `U+2028`   | LINE SEPARATOR            | \<LS>        |
| `U+2029`   | PARAGRAPH SEPARATOR       | \<PS>        |

仅将表33中的Unicode代码点视为行终止符。如果其他新行或换行符Unicode代码点满足表32中列出的要求，则不会将它们视为行终止符，而是将其视为空格。序列\<CR> \<LF>通常用作行终止符。为了报告行号，应将其视为单个SourceCharacter。

语法

```
LineTerminator::
    <LF>
    <CR>
    <LS>
    <PS>
LineTerminatorSequence::
    <LF>
    <CR>[lookahead ≠ <LF>]
    <LS>
    <PS>
    <CR><LF>
```



## 11.4 注释 <div id="comments"></div>

注释可以是单行或多行。多行注释不能嵌套。

因为单行注释可以包含LineTerminator代码点以外的任何Unicode代码点，并且由于通常的规则是令牌始终尽可能长，所以单行注释总是由//标记中的所有代码点组成到行尾但是，行尾的LineTerminator不被视为单行注释的一部分；它被词法语法单独识别，并成为句法语法的输入元素流的一部分。这一点非常重要，因为它暗示单行注释的存在或不存在不会影响自动分号插入的过程（请参见11.9）。

注释的行为类似于空白，并被丢弃，除非，如果MultiLineComment包含行终止符代码点，则出于语法语法分析的目的，整个注释都被视为LineTerminator。

语法：

```
Comment::
    MultiLineComment
    SingleLineComment
MultiLineComment::
    /*MultiLineCommentCharsopt*/
MultiLineCommentChars::
    MultiLineNotAsteriskCharMultiLineCommentCharsopt
    *PostAsteriskCommentCharsopt
PostAsteriskCommentChars::
    MultiLineNotForwardSlashOrAsteriskCharMultiLineCommentCharsopt
    *PostAsteriskCommentCharsopt
MultiLineNotAsteriskChar::
    SourceCharacterbut not *
    MultiLineNotForwardSlashOrAsteriskChar::
    SourceCharacterbut not one of / or *
SingleLineComment::
    //SingleLineCommentCharsopt
SingleLineCommentChars::
    SingleLineCommentCharSingleLineCommentCharsopt
SingleLineCommentChar::
    SourceCharacterbut not LineTerminator
```

## 11.5 词条 <div id="tokens"></div>

语法

```
CommonToken::
    IdentifierName
    Punctuator
    NumericLiteral
    StringLiteral
    Template
```

> 注意
>
> DivPunctuator，RegularExpressionLiteral，RightBracePunctuator和TemplateSubstitutionTail生成可派生出CommonToken生成中未包含的其他令牌。

## 11.6 名称和关键字 <div id="names-and-keywords"></div>

IdentifierName和ReservedWord是根据Unicode标准附件＃31，标识符和模式语法中给出的默认标识符语法进行了一些小的修改后解释的令牌。ReservedWord是IdentifierName的枚举子集。语法语法将标识符定义为不是保留字的IdentifierName。Unicode标识符语法基于Unicode标准指定的字符属性。所有符合标准的ECMAScript实现都必须将Unicode标准最新版本中指定类别中的Unicode代码点视为这些类别中的Unicode代码点。ECMAScript实现可以识别在Unicode标准的更高版本中定义的标识符代码点。

> 注1
> 该标准指定了特定的代码点添加：在IdentifierName中的任何位置都允许使用U + 0024（美元符号）和U + 005F（低线），以及代码点U + 200C（零宽度非JOINER）和U + 200D（零）WIDTH JOINER）允许在IdentifierName的第一个代码点之后的任何位置。

IdentifierName中允许使用Unicode转义序列，在该序列中，它们向IdentifierName贡献单个Unicode代码点。该代码点由UnicodeEscapeSequence的CodePoint表示（请参见11.8.4）。UnicodeEscapeSequence之前的\和u和{}代码单元（如果出现）不会为IdentifierName提供代码点。不能使用UnicodeEscapeSequence将代码点放入否则可能是非法的IdentifierName中。换句话说，如果\ UnicodeEscapeSequence序列被其贡献的SourceCharacter替换，则结果必须仍然是有效的IdentifierName，其SourceCharacter元素的序列与原始IdentifierName完全相同。本规范中对IdentifierName的所有解释均基于其实际代码点，无论是否使用转义序列来贡献任何特定代码点。

除非按照每个UnicodeEscapeSequence替换后，它们由完全相同的代码点序列表示，否则根据Unicode标准在规范上等效的两个IdentifierName不相等。

语法

```
IdentifierName::
    IdentifierStart
    IdentifierNameIdentifierPart
IdentifierStart::
    UnicodeIDStart
    $
    _
    \UnicodeEscapeSequence
IdentifierPart::
    UnicodeIDContinue
    $
    _
    \UnicodeEscapeSequence
    <ZWNJ>
    <ZWJ>
UnicodeIDStart::
    any Unicode code point with the Unicode property “ID_Start”
UnicodeIDContinue::
    any Unicode code point with the Unicode property “ID_Continue”
```

非终结UnicodeEscapeSequence的定义在11.8.4中给出。

> 注2：
>
> 非终结符IdentifierPart通过UnicodeIDContinue派生_。
>
> 注3：
>
> 具有Unicode属性“ ID_Start”和“ ID_Continue”的代码点集分别包括具有Unicode属性“ Other_ID_Start”和“ Other_ID_Continue”的代码点。

### 11.6.1 标识符名称 <div id="identifier-names"></div>

#### 11.6.1.1  SS: Early Errors <div id="early-errors"></div>

IdentifierStart::\[UnicodeEscapeSequence](#prod-UnicodeEscapeSequence)

- It is a Syntax Error if SV(UnicodeEscapeSequence) is none of `"$"`, or `"_"`, or the UTF16Encoding of a code point matched by the UnicodeIDStart lexical grammar production.

IdentifierPart::\[UnicodeEscapeSequence](#prod-UnicodeEscapeSequence)

- It is a Syntax Error if SV(UnicodeEscapeSequence) is none of `"$"`, or `"_"`, or the UTF16Encoding of either \<ZWNJ> or \<ZWJ>, or the UTF16Encoding of a Unicode code point that would be matched by the UnicodeIDContinue lexical grammar production.

#### 11.6.1.2  SS: StringValue <div id="stringvalue"></div>

​	IdentifierName::
​		IdentifierStart
​		IdentifierNameIdentifierPart

1. 返回由对应于IdentifierName的代码单元序列组成的String值。在确定序列时，首先用UnicodeEscapeSequence表示的代码点替换\ UnicodeEscapeSequence的出现，然后通过UTF16Encoding每个代码点将整个IdentifierName的代码点转换为代码单元。

### 11.6.2 保留关键字 <div id="reserved-words"></div>

保留字是不能用作标识符的IdentifierName。

语法

```
ReservedWord ::
    Keyword
    FutureReservedWord
    NullLiteral
    BooleanLiteral
```

> 注意
>
> ReservedWord定义被指定为特定SourceCharacter元素的文字序列。保留字中的代码点不能用\ UnicodeEscapeSequence表示。

#### 11.6.2.1 关键字 <div id="keywords"></div>

以下标记是ECMAScript关键字，不能在ECMAScript程序中用作标识符。

语法

```
Keyword :: one of
    await break case catch class const continue debugger default delete do
    else export extends finally for function if import in instanceof new
    return super switch this throw try typeof var void while with yield
```

> 注
>
> 在某些情况下，yield和await被赋予标识符的语义。见12.1.1。在严格模式代码中，let和static通过静态语义限制（请参见12.1.1、13.3.1.1、13.7.5.1和14.6.1）而不是词汇语法被视为保留字。

#### 11.6.2.2 未来保留关键字 <div id="future-reserved-words"></div>

保留以下标记，以在将来的语言扩展中用作关键字。

语法

```
FutureReservedWord ::
    enum
```

> 注
>
> 还保留在严格模式代码内使用以下标记。使用静态语义限制（请参见12.1.1）而不是词汇语法来限制使用：
>
> implements	package	protected
> interface		  private	  public

## 11.7 标点符号 <div id="punctuators"></div>

语法

```
Punctuator :: one of
    { ( ) [ ] . ... ; , < > <= >= == != === !== + - * % ** ++ -- << >> >>> & | ^ ! ~
    && || ? : = += -= *= %= **= <<= >>= >>>= &= |= ^= =>
DivPunctuator ::
    /
    /=
RightBracePunctuator ::
    }

```

## 11.8 字面量 <div id="literals"></div>
### 11.8.1 空字面量 <div id="null-literals"></div>

语法

```
NullLiteral ::
    null
```

### 11.8.2 布尔字面量 <div id="boolean-literals"></div>

语法

```
BooleanLiteral ::
    true
    false
```

### 11.8.3 数字字面量 <div id="numeric-literals"></div>
语法

```
NumericLiteral ::
    DecimalLiteral
    BinaryIntegerLiteral
    OctalIntegerLiteral
    HexIntegerLiteral
DecimalLiteral ::
    DecimalIntegerLiteral . DecimalDigitsopt ExponentPartopt
    . DecimalDigits ExponentPartopt
    DecimalIntegerLiteral ExponentPartopt
DecimalIntegerLiteral ::
    0
    NonZeroDigit DecimalDigitsopt
DecimalDigits ::
    DecimalDigit
    DecimalDigits DecimalDigit
DecimalDigit :: one of
    0 1 2 3 4 5 6 7 8 9
NonZeroDigit :: one of
    1 2 3 4 5 6 7 8 9
ExponentPart ::
    ExponentIndicator SignedInteger
ExponentIndicator :: one of
    e E
SignedInteger ::
    DecimalDigits
    + DecimalDigits
    - DecimalDigits
BinaryIntegerLiteral ::
    0b BinaryDigits
    0B BinaryDigits
BinaryDigits ::
    BinaryDigit
    BinaryDigits BinaryDigit
BinaryDigit :: one of
    0 1
OctalIntegerLiteral ::
    0o OctalDigits
    0O OctalDigits
OctalDigits ::
    OctalDigit
    OctalDigits OctalDigit
OctalDigit :: one of
    0 1 2 3 4 5 6 7
HexIntegerLiteral ::
    0x HexDigits
    0X HexDigits
HexDigits ::
    HexDigit
    HexDigits HexDigit
HexDigit :: one of
	0 1 2 3 4 5 6 7 8 9 a b c d e f A B C D E F
```

紧跟在NumericLiteral之后的SourceCharacter一定不能是IdentifierStart或DecimalDigit。

如B.1.1所述，在处理严格模式代码时，符合规范的实现不得扩展NumericLiteral的语法以包括LegacyOctalIntegerLiteral，也不得扩展DecimalIntegerLiteral的语法以包括NonOctalDecimalIntegerLiteral。

#### 11.8.3.1 SS: MV <div id="sec-static-semantics-mv"></div>

数字文字代表Number类型的值。该值分两个步骤确定：首先，从文字中得出数学值（MV）；第二，从公式中得出数学值。其次，该数学值按以下说明四舍五入。

The MV of NumericLiteral::DecimalLiteral is the MV of DecimalLiteral.
The MV of NumericLiteral::BinaryIntegerLiteral is the MV of BinaryIntegerLiteral.
The MV of NumericLiteral::OctalIntegerLiteral is the MV of OctalIntegerLiteral.
The MV of NumericLiteral::HexIntegerLiteral is the MV of HexIntegerLiteral.
The MV of DecimalLiteral::DecimalIntegerLiteral. is the MV of DecimalIntegerLiteral.
The MV of DecimalLiteral::DecimalIntegerLiteral.DecimalDigits is the MV of DecimalIntegerLiteral plus (the MV of DecimalDigits × 10-n), where n is the number of code points in DecimalDigits.
The MV of DecimalLiteral::DecimalIntegerLiteral.ExponentPart is the MV of DecimalIntegerLiteral × 10e, where e is the MV of ExponentPart.
The MV of DecimalLiteral::DecimalIntegerLiteral.DecimalDigitsExponentPart is (the MV of DecimalIntegerLiteral plus (the MV of DecimalDigits × 10-n)) × 10e, where n is the number of code points in DecimalDigits and e is the MV of ExponentPart.
The MV of DecimalLiteral::.DecimalDigits is the MV of DecimalDigits × 10-n, where n is the number of code points in DecimalDigits.
The MV of DecimalLiteral::.DecimalDigitsExponentPart is the MV of DecimalDigits × 10e-n, where n is the number of code points in DecimalDigits and e is the MV of ExponentPart.
The MV of DecimalLiteral::DecimalIntegerLiteral is the MV of DecimalIntegerLiteral.
The MV of DecimalLiteral::DecimalIntegerLiteralExponentPart is the MV of DecimalIntegerLiteral × 10e, where e is the MV of ExponentPart.
The MV of DecimalIntegerLiteral::0 is 0.
The MV of DecimalIntegerLiteral::NonZeroDigit is the MV of NonZeroDigit.
The MV of DecimalIntegerLiteral::NonZeroDigitDecimalDigits is (the MV of NonZeroDigit × 10n) plus the MV of DecimalDigits, where n is the number of code points in DecimalDigits.
The MV of DecimalDigits::DecimalDigit is the MV of DecimalDigit.
The MV of DecimalDigits::DecimalDigitsDecimalDigit is (the MV of DecimalDigits × 10) plus the MV of DecimalDigit.
The MV of ExponentPart::ExponentIndicatorSignedInteger is the MV of SignedInteger.
The MV of SignedInteger::DecimalDigits is the MV of DecimalDigits.
The MV of SignedInteger::+DecimalDigits is the MV of DecimalDigits.
The MV of SignedInteger::-DecimalDigits is the negative of the MV of DecimalDigits.
The MV of DecimalDigit::0 or of HexDigit::0 or of OctalDigit::0 or of BinaryDigit::0 is 0.
The MV of DecimalDigit::1 or of NonZeroDigit::1 or of HexDigit::1 or of OctalDigit::1 or of BinaryDigit::1 is 1.
The MV of DecimalDigit::2 or of NonZeroDigit::2 or of HexDigit::2 or of OctalDigit::2 is 2.
The MV of DecimalDigit::3 or of NonZeroDigit::3 or of HexDigit::3 or of OctalDigit::3 is 3.
The MV of DecimalDigit::4 or of NonZeroDigit::4 or of HexDigit::4 or of OctalDigit::4 is 4.
The MV of DecimalDigit::5 or of NonZeroDigit::5 or of HexDigit::5 or of OctalDigit::5 is 5.
The MV of DecimalDigit::6 or of NonZeroDigit::6 or of HexDigit::6 or of OctalDigit::6 is 6.
The MV of DecimalDigit::7 or of NonZeroDigit::7 or of HexDigit::7 or of OctalDigit::7 is 7.
The MV of DecimalDigit::8 or of NonZeroDigit::8 or of HexDigit::8 is 8.
The MV of DecimalDigit::9 or of NonZeroDigit::9 or of HexDigit::9 is 9.
The MV of HexDigit::a or of HexDigit::A is 10.
The MV of HexDigit::b or of HexDigit::B is 11.
The MV of HexDigit::c or of HexDigit::C is 12.
The MV of HexDigit::d or of HexDigit::D is 13.
The MV of HexDigit::e or of HexDigit::E is 14.
The MV of HexDigit::f or of HexDigit::F is 15.
The MV of BinaryIntegerLiteral::0bBinaryDigits is the MV of BinaryDigits.
The MV of BinaryIntegerLiteral::0BBinaryDigits is the MV of BinaryDigits.
The MV of BinaryDigits::BinaryDigit is the MV of BinaryDigit.
The MV of BinaryDigits::BinaryDigitsBinaryDigit is (the MV of BinaryDigits × 2) plus the MV of BinaryDigit.
The MV of OctalIntegerLiteral::0oOctalDigits is the MV of OctalDigits.
The MV of OctalIntegerLiteral::0OOctalDigits is the MV of OctalDigits.
The MV of OctalDigits::OctalDigit is the MV of OctalDigit.
The MV of OctalDigits::OctalDigitsOctalDigit is (the MV of OctalDigits × 8) plus the MV of OctalDigit.
The MV of HexIntegerLiteral::0xHexDigits is the MV of HexDigits.
The MV of HexIntegerLiteral::0XHexDigits is the MV of HexDigits.
The MV of HexDigits::HexDigit is the MV of HexDigit.
The MV of HexDigits::HexDigitsHexDigit is (the MV of HexDigits × 16) plus the MV of HexDigit.

一旦确定了数字文字的确切MV，则将其舍入为Number类型的值。如果MV为0，则舍入值为+0；否则，四舍五入的值必须是MV的Number值（如6.1.6中所指定），除非文字是DecimalLiteral并且文字具有20个以上的有效数字，在这种情况下Number值可以是Number值通过将20号之后的每个有效数字替换为0位而产生的文字的MV或通过将20号之后的每个有效数字替换为0位然后在20号增加该文字而产生的文字的MV的Number值有效数字位。如果数字不是ExpnentPart的一部分，则该数字是有效的；并且

​	不为0；或左侧有一个非零数字，右侧有一个非零数字（不在ExponentPart中）。

### 11.8.4 字符串字面量 <div id="string-literals"></div>

> 注 1
>
> 字符串文字是用单引号或双引号引起来的零个或多个Unicode代码点。Unicode代码点也可以由转义序列表示。除了结束引号代码点U + 005C（REVERSE SOLIDUS），U + 000D（回车）和U + 000A（LINE FEED）外，所有代码点都可能以字符串文字形式出现。任何代码点都可能以转义序列的形式出现。字符串文字的计算结果为ECMAScript字符串值。生成这些字符串值时，Unicode代码点按照10.1.1中的定义进行UTF-16编码。属于的代码点基本多语言平面被编码为字符串的单个代码单元元素。所有其他代码点均编码为字符串的两个代码单元元素。

语法

```
StringLiteral::
    "DoubleStringCharactersopt"
    'SingleStringCharactersopt'
DoubleStringCharacters::
    DoubleStringCharacterDoubleStringCharactersopt
SingleStringCharacters::
    SingleStringCharacterSingleStringCharactersopt
DoubleStringCharacter::
    SourceCharacterbut not one of " or \ or LineTerminator
    \EscapeSequence
    LineContinuation
SingleStringCharacter::
    SourceCharacterbut not one of ' or \ or LineTerminator
    \EscapeSequence
    LineContinuation
LineContinuation::
    \LineTerminatorSequence
EscapeSequence::
    CharacterEscapeSequence
    0[lookahead ∉ DecimalDigit]
    HexEscapeSequence
    UnicodeEscapeSequence
```

在处理严格模式代码时，符合标准的实现不得将EscapeSequence的语法扩展为包括B.1.2中所述的LegacyOctalEscapeSequence。

```
CharacterEscapeSequence::
    SingleEscapeCharacter
    NonEscapeCharacter
SingleEscapeCharacter::one of
    '"\bfnrtv
NonEscapeCharacter::
    SourceCharacterbut not one of EscapeCharacter or LineTerminator
EscapeCharacter::
    SingleEscapeCharacter
    DecimalDigit
    x
    u
HexEscapeSequence::
    xHexDigitHexDigit
UnicodeEscapeSequence::
    uHex4Digits
    u{HexDigits}
Hex4Digits::
    HexDigitHexDigitHexDigitHexDigit
```

非终结符HexDigit的定义在11.8.3中给出。SourceCharacter在10.1中定义。

> 注 2
>
> <LF>和<CR>不能出现在字符串文字中，除非作为LineContinuation的一部分来产生空代码点序列。包含在字符串文字的String值中的正确方法是使用转义序列，例如\ n或\ u000A。

#### 11.8.4.1 SS: StringValue <div id="sec-string-literals-static-semantics-stringvalue"></div>

```
StringLiteral ::
    " DoubleStringCharactersopt "
    ' SingleStringCharactersopt '
```

1. 返回String值，其代码单位是此StringLiteral的SV。

#### 11.8.4.2 SS: SV <div id="sec-static-semantics-sv"></div>

字符串文字代表String类型的值。文字的字符串值（SV）是根据字符串文字的各个部分贡献的代码单位值来描述的。作为此过程的一部分，字符串文字中的某些Unicode代码点被解释为具有数学值（MV），如下所述或11.8.3中所述。

The SV of StringLiteral::"" 是空代码单元序列(empty code unit sequence).
The SV of StringLiteral::'' is the empty code unit sequence.
The SV of StringLiteral::"DoubleStringCharacters" is the SV of DoubleStringCharacters.
The SV of StringLiteral::'SingleStringCharacters' is the SV of SingleStringCharacters.
The SV of DoubleStringCharacters::DoubleStringCharacter is a sequence of one or two code units that is the SV of DoubleStringCharacter.
The SV of DoubleStringCharacters::DoubleStringCharacterDoubleStringCharacters is a sequence of one or two code units that is the SV of DoubleStringCharacter followed by all the code units in the SV of DoubleStringCharacters in order.
The SV of SingleStringCharacters::SingleStringCharacter is a sequence of one or two code units that is the SV of SingleStringCharacter.
The SV of SingleStringCharacters::SingleStringCharacterSingleStringCharacters is a sequence of one or two code units that is the SV of SingleStringCharacter followed by all the code units in the SV of SingleStringCharacters in order.
The SV of DoubleStringCharacter::SourceCharacterbut not one of " or \ or LineTerminator is the UTF16Encoding of the code point value of SourceCharacter.
The SV of DoubleStringCharacter::\EscapeSequence is the SV of the EscapeSequence.
The SV of DoubleStringCharacter::LineContinuation is the empty code unit sequence.
The SV of SingleStringCharacter::SourceCharacterbut not one of ' or \ or LineTerminator is the UTF16Encoding of the code point value of SourceCharacter.
The SV of SingleStringCharacter::\EscapeSequence is the SV of the EscapeSequence.
The SV of SingleStringCharacter::LineContinuation is the empty code unit sequence.
The SV of EscapeSequence::CharacterEscapeSequence is the SV of the CharacterEscapeSequence.
The SV of EscapeSequence::0 is the code unit value 0.
The SV of EscapeSequence::HexEscapeSequence is the SV of the HexEscapeSequence.
The SV of EscapeSequence::UnicodeEscapeSequence is the SV of the UnicodeEscapeSequence.
The SV of CharacterEscapeSequence::SingleEscapeCharacter is the code unit whose value is determined by the SingleEscapeCharacter according to Table 34.

| Escape Sequence | Code Unit Value | Unicode Character Name         | Symbol |
| --------------- | --------------- | ------------------------------ | ------ |
| `\b`            | `0x0008`        | BACKSPACE(回退)                | \<BS>  |
| `\t`            | `0x0009`        | CHARACTER TABULATION(水平制表) | \<HT>  |
| `\n`            | `0x000A`        | LINE FEED (LF)(换行)           | \<LF>  |
| `\v`            | `0x000B`        | LINE TABULATION(垂直制表)      | \<VT>  |
| `\f`            | `0x000C`        | FORM FEED (FF)(换页)           | \<FF>  |
| `\r`            | `0x000D`        | CARRIAGE RETURN (CR)(回车)     | \<CR>  |
| `\"`            | `0x0022`        | QUOTATION MARK                 | `"`    |
| `\'`            | `0x0027`        | APOSTROPHE                     | `'`    |
| `\\`            | `0x005C`        | REVERSE SOLIDUS                | `\`    |

The SV of CharacterEscapeSequence::NonEscapeCharacter is the SV of the NonEscapeCharacter.
The SV of NonEscapeCharacter::SourceCharacterbut not one of EscapeCharacter or LineTerminator is the UTF16Encoding of the code point value of SourceCharacter.
The SV of HexEscapeSequence::xHexDigitHexDigit 的代码单位值，是第一个HexDigit的MV的16倍加上第二个HexDigit的MV。
The SV of UnicodeEscapeSequence::uHex4Digits is the SV of Hex4Digits.
The SV of Hex4Digits::HexDigitHexDigitHexDigitHexDigit 是代码单位值（第一个HexDigit的MV的4096倍）加上（第二个HexDigit的MV的256倍）加上（第三个HexDigit的MV的16倍）加上第四个HexDigit的MV。
The SV of UnicodeEscapeSequence::u{HexDigits} is the UTF16Encoding of the MV of HexDigits.

### 11.8.5 正则表达式字面量 <div id="regular-expression-literals"></div>

> 注 1
>
> 正则表达式文字是一个输入元素，每次对文字进行求值时都会将其转换为RegExp对象（请参见21.2）。程序中的两个正则表达式文字求和为正则表达式对象，即使这两个文字的内容相同，它们也永远不会相互比较===。还可以在运行时通过新的RegExp或将RegExp构造函数作为函数调用来创建RegExp对象（请参见21.2.3）。

下面的生成描述了正则表达式文字的语法，并被输入元素扫描程序用来查找正则表达式文字的结尾。随后，使用更严格的ECMAScript正则表达式语法（21.2.1）再次解析包含RegularExpressionBody和RegularExpressionFlags的源文本。

一个实现可以扩展在21.2.1中定义的ECMAScript正则表达式语法，但不能扩展下面定义的RegularExpressionBody和RegularExpressionFlags生产或这些生产使用的生产。

语法

```
RegularExpressionLiteral::
    /RegularExpressionBody/RegularExpressionFlags
RegularExpressionBody::
    RegularExpressionFirstCharRegularExpressionChars
RegularExpressionChars::
    [empty]
    RegularExpressionCharsRegularExpressionChar
RegularExpressionFirstChar::
    RegularExpressionNonTerminatorbut not one of * or \ or / or [
    RegularExpressionBackslashSequence
    RegularExpressionClass
RegularExpressionChar::
    RegularExpressionNonTerminatorbut not one of \ or / or [
    RegularExpressionBackslashSequence
    RegularExpressionClass
RegularExpressionBackslashSequence::
    \RegularExpressionNonTerminator
RegularExpressionNonTerminator::
    SourceCharacterbut not LineTerminator
RegularExpressionClass::
    [RegularExpressionClassChars]
RegularExpressionClassChars::
    [empty]
    RegularExpressionClassCharsRegularExpressionClassChar
RegularExpressionClassChar::
    RegularExpressionNonTerminatorbut not one of ] or \
    RegularExpressionBackslashSequence
RegularExpressionFlags::
    [empty]
    RegularExpressionFlagsIdentifierPart
```

> 注 2
>
> 正则表达式文字不能为空；代码单元序列//而不是表示空的正则表达式文字，而是开始单行注释。要指定一个空的正则表达式，请使用：/（？：）/。

#### 11.8.5.1 SS: Early Errors <div id="sec-literals-regular-expression-literals-static-semantics-early-errors"></div>

RegularExpressionFlags :: RegularExpressionFlags IdentifierPart

​	如果IdentifierPart包含Unicode转义序列，则是语法错误。

#### 11.8.5.2 SS: BodyText <div id="sec-static-semantics-bodytext"></div>

RegularExpressionLiteral :: / RegularExpressionBody / RegularExpressionFlags

​	1. 返回被识别为RegularExpressionBody的源文本

#### 11.8.5.3 SS: FlagText <div id="sec-static-semantics-flagtext"></div>

RegularExpressionLiteral :: / RegularExpressionBody / RegularExpressionFlags

​	1.返回被识别为RegularExpressionFlags的源文本。

### 11.8.6 模板字面量文字组件 <div id="template-literal-lexical-components"></div>

语法

```
Template::
    NoSubstitutionTemplate
    TemplateHead
NoSubstitutionTemplate::
    `TemplateCharactersopt`
TemplateHead::
    `TemplateCharactersopt${
TemplateSubstitutionTail::
    TemplateMiddle
    TemplateTail
TemplateMiddle::
    }TemplateCharactersopt${
TemplateTail::
    }TemplateCharactersopt`
TemplateCharacters::
    TemplateCharacterTemplateCharactersopt
TemplateCharacter::
    $[lookahead ≠ {]
    \EscapeSequence
    LineContinuation
    LineTerminatorSequence
    SourceCharacterbut not one of ` or \ or $ or LineTerminator
NotEscapeSequence ::
    0 DecimalDigit
    DecimalDigit but not 0
    x [lookahead ∉ HexDigit]
    x HexDigit [lookahead ∉ HexDigit]
    u [lookahead ∉ HexDigit] [lookahead ≠ {]
    u HexDigit [lookahead ∉ HexDigit]
    u HexDigit HexDigit [lookahead ∉ HexDigit]
    u HexDigit HexDigit HexDigit [lookahead ∉ HexDigit]
    u { [lookahead ∉ HexDigit]
    u { NotCodePoint [lookahead ∉ HexDigit]
    u { CodePoint [lookahead ∉ HexDigit] [lookahead ≠ }]
NotCodePoint ::
    HexDigits but only if MV of HexDigits > 0x10FFFF
CodePoint ::
    HexDigits but only if MV of HexDigits ≤ 0x10FFFF
```

符合规范的实现在解析TemplateCharacter时不得使用B.1.2中描述的EscapeSequence的扩展定义。

> 注
>
> InputSubTemplateTail替代词汇目标使用TemplateSubstitutionTail。

#### 11.8.6.1 SS: TV and TRV <div id="sec-static-semantics-tv-and-trv"></div>

模板文字组件被解释为Unicode代码点的序列。文字组件的模板值（TV）用由模板文字组件的各个部分贡献的代码单位值（SV，11.8.4）描述。作为此过程的一部分，模板组件中的某些Unicode代码点被解释为具有数学值（MV，11.8.3）。在确定电视时，将转义序列替换为由转义序列表示的Unicode代码点的UTF-16代码单元。模板原始值（TRV）与模板值类似，不同之处在于TRV中的转义序列按字面意义进行解释。

The TV and TRV of NoSubstitutionTemplate::`` is the empty code unit sequence.
The TV and TRV of TemplateHead::`${ is the empty code unit sequence.
The TV and TRV of TemplateMiddle::}${ is the empty code unit sequence.
The TV and TRV of TemplateTail::}` is the empty code unit sequence.
The TV of NoSubstitutionTemplate::`TemplateCharacters` is the TV of TemplateCharacters.
The TV of TemplateHead::`TemplateCharacters${ is the TV of TemplateCharacters.
The TV of TemplateMiddle::}TemplateCharacters${ is the TV of TemplateCharacters.
The TV of TemplateTail::}TemplateCharacters` is the TV of TemplateCharacters.
The TV of TemplateCharacters::TemplateCharacter is the TV of TemplateCharacter.
The TV of TemplateCharacters::TemplateCharacterTemplateCharacters is a sequence consisting of the code units in the TV of TemplateCharacter followed by all the code units in the TV of TemplateCharacters in order.
The TV of TemplateCharacter::SourceCharacterbut not one of ` or \ or $ or LineTerminator is the UTF16Encoding of the code point value of SourceCharacter.
The TV of TemplateCharacter::$ is the code unit value 0x0024.
The TV of TemplateCharacter::\EscapeSequence is the SV of EscapeSequence.
The TV of TemplateCharacter::LineContinuation is the TV of LineContinuation.
The TV of TemplateCharacter::LineTerminatorSequence is the TRV of LineTerminatorSequence.
The TV of LineContinuation::\LineTerminatorSequence is the empty code unit sequence.
The TRV of NoSubstitutionTemplate::`TemplateCharacters` is the TRV of TemplateCharacters.
The TRV of TemplateHead::`TemplateCharacters${ is the TRV of TemplateCharacters.
The TRV of TemplateMiddle::}TemplateCharacters${ is the TRV of TemplateCharacters.
The TRV of TemplateTail::}TemplateCharacters` is the TRV of TemplateCharacters.
The TRV of TemplateCharacters::TemplateCharacter is the TRV of TemplateCharacter.
The TRV of TemplateCharacters::TemplateCharacterTemplateCharacters is a sequence consisting of the code units in the TRV of TemplateCharacter followed by all the code units in the TRV of TemplateCharacters, in order.
The TRV of TemplateCharacter::SourceCharacterbut not one of ` or \ or $ or LineTerminator is the UTF16Encoding of the code point value of SourceCharacter.
The TRV of TemplateCharacter::$ is the code unit value 0x0024.
The TRV of TemplateCharacter::\EscapeSequence is the sequence consisting of the code unit value 0x005C followed by the code units of TRV of EscapeSequence.
The TRV of TemplateCharacter::LineContinuation is the TRV of LineContinuation.
The TRV of TemplateCharacter::LineTerminatorSequence is the TRV of LineTerminatorSequence.
The TRV of EscapeSequence::CharacterEscapeSequence is the TRV of the CharacterEscapeSequence.
The TRV of EscapeSequence::0 is the code unit value 0x0030 (DIGIT ZERO).
The TRV of EscapeSequence::HexEscapeSequence is the TRV of the HexEscapeSequence.
The TRV of EscapeSequence::UnicodeEscapeSequence is the TRV of the UnicodeEscapeSequence.
The TRV of CharacterEscapeSequence::SingleEscapeCharacter is the TRV of the SingleEscapeCharacter.
The TRV of CharacterEscapeSequence::NonEscapeCharacter is the SV of the NonEscapeCharacter.
The TRV of SingleEscapeCharacter::one of'"\bfnrtv is the SV of the SourceCharacter that is that single code point.
The TRV of HexEscapeSequence::xHexDigitHexDigit is the sequence consisting of code unit value 0x0078 followed by TRV of the first HexDigit followed by the TRV of the second HexDigit.
The TRV of UnicodeEscapeSequence::uHex4Digits is the sequence consisting of code unit value 0x0075 followed by TRV of Hex4Digits.
The TRV of UnicodeEscapeSequence::u{HexDigits} is the sequence consisting of code unit value 0x0075 followed by code unit value 0x007B followed by TRV of HexDigits followed by code unit value 0x007D.
The TRV of Hex4Digits::HexDigitHexDigitHexDigitHexDigit is the sequence consisting of the TRV of the first HexDigit followed by the TRV of the second HexDigit followed by the TRV of the third HexDigit followed by the TRV of the fourth HexDigit.
The TRV of HexDigits::HexDigit is the TRV of HexDigit.
The TRV of HexDigits::HexDigitsHexDigit is the sequence consisting of TRV of HexDigits followed by TRV of HexDigit.
The TRV of a HexDigit is the SV of the SourceCharacter that is that HexDigit.
The TRV of LineContinuation::\LineTerminatorSequence is the sequence consisting of the code unit value 0x005C followed by the code units of TRV of LineTerminatorSequence.
The TRV of LineTerminatorSequence::\<LF> is the code unit value 0x000A.
The TRV of LineTerminatorSequence::\<CR> is the code unit value 0x000A.
The TRV of LineTerminatorSequence::\<LS> is the code unit value 0x2028.
The TRV of LineTerminatorSequence::\<PS> is the code unit value 0x2029.
The TRV of LineTerminatorSequence::\<CR>\<LF> is the sequence consisting of the code unit value 0x000A.

## 11.9 自动分号插入 <div id="automatic-semicolon-insertion"></div>

大多数ECMAScript语句和声明必须以分号终止。这样的分号可能总是显式地出现在源文本中。但是，为方便起见，在某些情况下可以从源文本中省略此类分号。通过说在这些情况下将分号自动插入到源代码令牌流中来描述这些情况。

### 11.9.1 自动分号插入规则 <div id="rules-of-automatic-semicolon-insertion"></div>

在以下规则中，“令牌”是指使用第11条中所述的当前词汇目标符号确定的实际公认词汇令牌。

分号插入有三个基本规则：

1. 当源文本从左到右进行解析时，遇到语法产生的任何形式都不允许的标记（称为冒犯标记）时，如果一个或多个冒号包含一个分号，则会自动在该冒犯标记之前插入分号。满足以下条件：
   - 至少一个LineTerminator将违规token与先前的token分开。
   - 违规token是`}`。
   - 之前的令牌是)，然后插入的分号将被解析为do-while语句的终止分号（13.7.2）。

2. 当源文本从左到右进行解析时，遇到令牌输入流的末尾，并且解析器无法将输入令牌流解析为目标非终结符的单个实例，则分号会自动插入输入流的末尾。
3. 当按照从左到右的顺序解析源文本时，遇到某种语法产生所允许的标记，但是产生是受限产生，并且该标记将成为紧随该标记之后的终端或非终端的第一个标记受限生产中的注释“ [此处没有LineTerminator]”（因此，此类令牌称为受限令牌），并且受限令牌与至少一个LineTerminator与前一个令牌分开，然后在受限之前自动插入分号令牌。

但是，上述规则还有一个额外的优先条件：如果分号随后将被解析为空语句，或者该分号成为for语句的标头中的两个分号之一，则永远不会自动插入分号。13.7.4）。

> 注
>
> 以下是语法中仅有的限定词：

```
UpdateExpression[Yield]:
    LeftHandSideExpression[?Yield][no LineTerminator here]++
    LeftHandSideExpression[?Yield][no LineTerminator here]--
ContinueStatement[Yield]:
    continue;
    continue[no LineTerminator here]LabelIdentifier[?Yield];
BreakStatement[Yield]:
    break;
    break[no LineTerminator here]LabelIdentifier[?Yield];
ReturnStatement[Yield]:
    return;
    return[no LineTerminator here]Expression[In, ?Yield];
ThrowStatement[Yield]:
    throw[no LineTerminator here]Expression[In, ?Yield];
ArrowFunction[In, Yield]:
    ArrowParameters[?Yield][no LineTerminator here]=>ConciseBody[?In]
YieldExpression[In]:
    yield[no LineTerminator here]*AssignmentExpression[?In, Yield]
    yield[no LineTerminator here]AssignmentExpression[?In, Yield]
```

这些限制生产的实际效果如下：

- 当遇到一个++或 -- token时，解析器将其视为后缀操作符，并且至少有一个LineTerminator发生在前面的token和++或 --标记之间，则分号将自动插入到++或 -- token之前
- 当遇到continue，break，return，throw或yield 词条时，在下一个词条(token)之前遇到一个LineTerminator，则在continue，break，return，throw或yield令牌之后会自动插入分号。

由此给ECMAScript程序员带来的实用建议是：

- 后缀++或 -- 运算符应该出现在与其操作数相同的行上。
- return或throw语句中的表达式或yield表达式中的AssignmentExpression应该在与return，throw或yield词条相同的行上开始。
- break或continue语句中的LabelIdentifier应与break或continue标记位于同一行。

### 11.9.2 自动分号插入规则示例 <div id="examples-of-automatic-semicolon-insertion"></div>

The source

```
{ 1 2 } 3
```

即使使用自动分号插入规则，ECMAScript语法中也不是有效的句子。 相反，来源

```
{ 1
2 } 3    
```

也不是有效的ECMAScript句子，但是通过自动分号插入到以下变量中：

```
{ 1
;2 ;} 3;  
```

这是一个有效的ECMAScript句子。

The source

```
for (a; b
) 
```

不是一个有效的ECMAScript句子，并且不会被自动分号插入更改，因为for语句的标题需要分号。 自动分号插入不会在for语句的标题中插入两个分号中的一个。

The source

```
return
a + b
```

通过自动分号插入转换为以下内容：

```
return;
a + b;
```

NOTE 1

表达式a + b不被视为返回语句返回的值，因为LineTerminator将其从token return中分离出来。

The source

```
a = b
++c
```

通过自动分号插入转换为以下内容：

```
a = b;
++c;
```

NOTE 2

token ++不被视为应用于变量b的后缀运算符，因为LineTerminator发生在b和++之间。

The source

```
if (a > b)
else c = d
```

不是一个有效的ECMAScript句子，并且不会在else标记之前自动分号插入更改，即使在该点不会生成语法，因为自动插入的分号将被解析为空语句。

The source

```
a = b + c
(d + e).print()
```

不会通过自动分号插入进行转换，因为开始第二行的括号表达式可以解释为函数调用的参数列表：

```
a = b + c(d + e).print()
```

在赋值语句必须以左括号开头的情况下，程序员在前一个语句的末尾提供一个明确的分号，而不是依靠自动分号插入是一个好主意。