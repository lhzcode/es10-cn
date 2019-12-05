# 11 ECMAScript语言：词法

首先将ECMAScript脚本或模块的源文本转换成一系列输入元素，这些输入元素是标记，行终止符，注释或空白。从左到右扫描源文本，重复使用尽可能长的码点序列作为下一个输入元素。

在几种情况下，词法输入元素的识别对使用输入元素的句法上下文很敏感。这要求词法具有多个目标符号。InputElementRegExpOrTemplateTail 目标用于允许使用 RegularExpressionLiteral，TemplateMiddle 或 TemplateTail 的句法上下文中。InputElementRegExp 目标符号用于所有允许使用 RegularExpressionLiteral 但不允许 TemplateMiddle 或 TemplateTail 的句法上下文中。InputElementTemplateTail 目标用于所有允许使用 TemplateMiddle 或 TemplateTail 但不允许使用 RegularExpressionLiteral 的句法上下文中。在所有其他上下文中，InputElementDiv 用作词法目标符号。

> 注意
>
> 使用多个词法目标可确保不会出现会影响自动分号插入的词法歧义。例如，没有句法上下文允许同时使用前导除法或除赋值和前导RegularExpressionLiteral。这不受分号插入的影响（请参见11.9）。在如下示例中：
>
> a = b
> /hi/g.exec(c).map(d);
>
> 如果LineTerminator之后的第一个非空白，非注释码点为U+002F（/），并且语法上下文允许进行除法或除赋值，则LineTerminator上不会插入分号。也就是说，以上示例的解释方式与：
>
>  a = b /hi/ g.exec(c).map(d);  

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

Unicode 格式控制字符（即，Unicode 字符数据库中 Cf分类 里的字符，如“左至右符号”或“右至左符号”）是用来控制被更高层级协议（如标记语言）忽略的文本范围的格式的控制代码。

允许在源代码文本中出现控制字符是有利于编辑和显示的。所有格式控制字符可写入到注释、字符串字面量、模板自变量和正则表达式字面量中。

U+200C（零宽不连字）和U+200D（零宽连字符）是格式控制字符，用于在形成某些语言的单词或短语时进行必要的区分。在ECMAScript源文本中，这些码点也可以在第一个字符之后的IdentifierName中使用。

U+FEFF（零宽不换行）是一种格式控制字符，主要用于文本的开头，以将其标记为Unicode，并允许检测文本的编码和字节顺序。用于此目的的\<ZWNBSP>字符有时也可能在文本开头之后出现，例如，由于连接文件而引起的。在ECMAScript源文本中，\<ZWNBSP>码点被视为空白字符（请参见11.2）。

表31总结了注释，字符串文字和正则表达式文字之外的某些格式控制字符的特殊处理。

| 码点     | 名称       | 缩写      | 用途       |
| -------- | ---------- | --------- | ---------- |
| `U+200C` | 零宽不连字 | \<ZWNJ>   | 标识符部分 |
| `U+200D` | 零宽连字符 | \<ZWJ>    | 标识符部分 |
| `U+FEFF` | 零宽不换行 | \<ZWNBSP> | 空格       |

## 11.2 空格 <div id="white-space"></div>

空格码点用于提高源文本的可读性，并将标记（不可分割的词法单位）彼此分隔开，但在其他方面则无关紧要。空格码点可能出现在任意两个标记之间以及输入的开始或结尾。空格码点可能出现在StringLiteral，RegularExpressionLiteral，Template或TemplateSubstitutionTail中，这些空格被视为构成文字值一部分的重要码点。它们也可能出现在注释中，但不能出现在任何其他种类的标记中。

表32中列出了ECMAScript空格码点。

| 码点          | 名称                          | 缩写      |
| ------------- | ----------------------------- | --------- |
| `U+0009`      | 制表符                        | \<TAB>    |
| `U+000B`      | 行制表符                      | \<VT>     |
| `U+000C`      | 换行 (FF)                     | \<FF>     |
| `U+0020`      | 空格                          | \<SP>     |
| `U+00A0`      | 不换行空格                    | \<NBSP>   |
| `U+FEFF`      | 零宽不换行                    | \<ZWNBSP> |
| 其他类别 “Zs” | 其他Unicode的“空白分隔符”码点 | \<USP>    |

ECMAScript实现必须将“空白分隔符”（“ Zs”）类别中列出的WhiteSpace码点识别为空白。

> 注意：
>
> 除了表32中列出的码点之外，ECMAScript WhiteSpace有意排除了所有具有Unicode的“空白分隔符”属性但未归类为“ Space_Separator”（“ Zs”）类别的码点。

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

像空白字符一样，行终止字符用于改善源文本的可读性和分割 Token（不可分割的词法单位）。然而，不像空白字符，行终止符对句法的行为有一定的影响。一般情况下，行终止符可以出现在任何两个 Token 之间，但也有少数地方，句法禁止这样做。行终止符也影响自动插入分号的过程（11.9）。行终止符不能出现在 StringLiteral， 之外的任何 Token 内。\<LF>和\<CR>行终止符不能在StringLiteral的Token内出现，除非作为LineContinuation的一部分。

行终止符可以出现在 MultiLineComment 内，但不能出现在 SingleLineComment 内。

行终止符包含在\ s类在正则表达式中匹配的空白码点集中。

表33中列出了ECMAScript行终止符码点。

| Code Point | Unicode 名称 | Abbreviation |
| ---------- | ------------ | ------------ |
| `U+000A`   | 换行 (LF)    | \<LF>        |
| `U+000D`   | 回车 (CR)    | \<CR>        |
| `U+2028`   | 行分隔符     | \<LS>        |
| `U+2029`   | 段落分隔符   | \<PS>        |

仅将表33中的Unicode码点视为行终止符。如果其他新行或换行符Unicode码点满足表32中列出的要求，则不会将它们视为行终止符，而是将其视为空格。序列\<CR> \<LF>通常用作行终止符。为了记录行号，应将其视为单个SourceCharacter。

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

因为单行注释可以包含除了 LineTerminator 字符之外的任何字符，又因为有一般规则：一个 Token 总是尽可能匹配更长，所以一个单行注释总是包含从 // 到行终止符之间的所有字符。然而，在该行末尾的 LineTerminator 不被看成是单行注释的一部分，它被词法识别成句法输入元素流的一部分。这一点非常重要，因为这意味着是否存在单行注释都不影响自动分号插入进程（请参见11.9）。

像空白一样，注释会被句法简单丢弃，除了 MultiLineComment 包含行终止符字符的情况，这种情况下整个注释会当作一个 LineTerminator 提供给句法文法解析。

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

## 11.5 Token<div id="tokens"></div>

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
> DivPunctuator，RegularExpressionLiteral，RightBracePunctuator和TemplateSubstitutionTail产生式得出额外的 **Token**，不包含CommonToken产生式。

## 11.6 名称和关键字 <div id="names-and-keywords"></div>

IdentifierName和ReservedWord是根据Unicode标准附件＃31，标识符和模式语法中给出的默认标识符语法进行了解释，并进行了一些小的修改的token。ReservedWord是IdentifierName的枚举子集。句法将标识符定义为不是保留字的IdentifierName。Unicode标识符语法基于Unicode标准指定的字符属性。所有符合标准的ECMAScript实现都必须将Unicode标准最新版本中指定类别中的Unicode码点视为这些类别中的Unicode码点。ECMAScript实现可以识别在Unicode标准的更高版本中定义的标识符码点。

> 注1
> 该标准指定了特定的码点添加：在IdentifierName中的任何位置都允许使用U+0024（美元符号）和U+005F（下划线），以及码点U+200C（零宽不连字）和U+200D（零宽连字符）允许在IdentifierName的第一个码点之后的任何位置。

IdentifierName中允许使用Unicode转义序列，在该序列中，它们向IdentifierName提供单个Unicode码点。该码点由UnicodeEscapeSequence的CodePoint表示（请参见11.8.4）。UnicodeEscapeSequence之前的\和u和{}码元（如果出现）不会为IdentifierName提供码点。不能使用UnicodeEscapeSequence将码点放入否则可能是非法的IdentifierName中。换句话说，如果\ UnicodeEscapeSequence序列被其提供的SourceCharacter替换，则结果必须仍然是有效的IdentifierName，其SourceCharacter元素的序列与原始IdentifierName完全相同。本规范中对IdentifierName的所有解释均基于其实际码点，无论是否使用转义序列来提供任何特定码点。

除非按照每个UnicodeEscapeSequence替换后，它们由完全相同的码点序列表示，否则根据Unicode标准，在规范上等效的两个IdentifierName互不相等。

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
> 非终结符IdentifierPart通过UnicodeIDContinue派生"_"。
>
> 注3：
>
> 具有Unicode属性“ ID_Start”和“ ID_Continue”的码点集分别包括具有Unicode属性“ Other_ID_Start”和“ Other_ID_Continue”的码点。

### 11.6.1 标识符名称 <div id="identifier-names"></div>

#### 11.6.1.1  静态语义：Early Errors <div id="early-errors"></div>

IdentifierStart::UnicodeEscapeSequence

- 如果SV（Unicode转义序列）不为“ $”或“ _”或与UnicodeIDStart词法生成匹配的码点的UTF16Encoding，则为语法错误。

IdentifierPart::UnicodeEscapeSequence

- 如果SV（Unicode转义序列）都不是“ $”或“ _”，或者是\<ZWNJ>或\<ZWJ>的UTF16Encoding，或者是与UnicodeIDContinue词法产生式匹配的Unicode码点的UTF16Encoding，则这是语法错误。

#### 11.6.1.2  静态语义：StringValue <div id="stringvalue"></div>

​	IdentifierName::
​		IdentifierStart
​		IdentifierNameIdentifierPart

1. 返回由对应于IdentifierName的码元序列组成的String值。在确定序列时，首先用UnicodeEscapeSequence表示的码点替换\ UnicodeEscapeSequence的出现，然后通过UTF16Encoding每个码点将整个IdentifierName的码点转换为码元。

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
> ReservedWord定义被指定为特定SourceCharacter元素的文字序列。保留字中的码点不能用\ UnicodeEscapeSequence表示。

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
> 在某些情况下，yield和await被赋予标识符的语义。见12.1.1。在严格模式代码中，let和static通过静态语义限制（请参见12.1.1、13.3.1.1、13.7.5.1和14.6.1）而不是词法语法被视为保留字。

#### 11.6.2.2 未来保留关键字 <div id="future-reserved-words"></div>

保留以下标记，以在将来的语言扩展中用作关键字。

语法

```
FutureReservedWord ::
    enum
```

> 注
>
> 还保留在严格模式代码内使用以下标记。使用静态语义限制（请参见12.1.1）而不是词法来限制使用：
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

#### 11.8.3.1 静态语义：MV <div id="sec-static-semantics-mv"></div>

数字文字代表Number类型的值。该值分两个步骤确定：首先，从文字中得出数学值（MV）；第二，从公式中得出数学值。其次，该数学值按以下说明四舍五入。

- NumericLiteral :: DecimalLiteral 的数学值是 DecimalLiteral 的数学值。
- NumericLiteral :: BinaryIntegerLiteral 的数学值是 BinaryIntegerLiteral 的数学值。
- NumericLiteral :: OctalIntegerLiteral 的数学值是 OctalIntegerLiteral 的数学值。
- NumericLiteral :: HexIntegerLiteral 的数学值是 HexIntegerLiteral 的数学值。
- DecimalLiteral :: DecimalIntegerLiteral . 的数学值是 DecimalIntegerLiteral 的数学值。
- DecimalLiteral :: DecimalIntegerLiteral . DecimalDigits 的数学值是 DecimalIntegerLiteral 的数学值加上 (DecimalDigits 的数学值乘 10^-n), 这里的 n 是 DecimalDigits 的字符个数。
- DecimalLiteral :: DecimalIntegerLiteral . ExponentPart 的数学值是 DecimalIntegerLiteral 的数学值乘 10^e, 这里的 e 是 ExponentPart 的数学值。
- DecimalLiteral :: DecimalIntegerLiteral . DecimalDigits ExponentPart 的数学值是 (DecimalIntegerLiteral 的数学值加 (DecimalDigits 的数学值乘 10^-n)) 乘 10e, 这里的 n 是 DecimalDigits 的字符个数，e 是 ExponentPart 的数学值。
- DecimalLiteral :: . DecimalDigits 的数学值是 DecimalDigits 的数学值乘 10-n, 这里的 n 是 DecimalDigits 的字符个数。
- DecimalLiteral :: . DecimalDigits ExponentPart 的数学值是 DecimalDigits 的数学值乘 10e-n, 这里的 n 是 DecimalDigits 的字符个数，e 是 ExponentPart 的数学值。
- DecimalLiteral :: DecimalIntegerLiteral 的数学值是 DecimalIntegerLiteral 的数学值。
- DecimalLiteral :: DecimalIntegerLiteral ExponentPart 的数学值是 DecimalIntegerLiteral 的数学值乘 10^e, 这里的 e 是 ExponentPart 的数学值。
- DecimalIntegerLiteral :: 0 的数学值是 0。
- DecimalLiteral :: NonZeroDigit 的数学值是 NonZeroDigit 的数学值。
- DecimalIntegerLiteral :: NonZeroDigit DecimalDigits 的数学值是 (NonZeroDigit 的数学值乘 10n) 加 DecimalDigits 的数学值, 这里的 n 是 DecimalDigits 的字符个数。
- DecimalDigits :: DecimalDigit 的数学值是 DecimalDigit 的数学值。
- DecimalDigits :: DecimalDigits DecimalDigit 的数学值是 (DecimalDigits 的数学值乘 10) 加 DecimalDigit 的数学值。
- ExponentPart :: ExponentIndicator SignedInteger 的数学值是 SignedInteger 的数学值。
- SignedInteger :: DecimalDigits 的数学值是 DecimalDigits 的数学值。
- SignedInteger :: + DecimalDigits 的数学值是 DecimalDigits 的数学值。
- SignedInteger :: - DecimalDigits 的数学值是 DecimalDigits 的数学值取负。
- DecimalDigit :: 0 或 HexDigit :: 0 的数学值是 0。
- DecimalDigit :: 1 或 NonZeroDigit :: 1 或 HexDigit :: 1 的数学值是 1。
- DecimalDigit :: 2 或 NonZeroDigit :: 2 或 HexDigit :: 2 的数学值是 2。
- DecimalDigit :: 3 或 NonZeroDigit :: 3 或 HexDigit :: 3 的数学值是 3。
- DecimalDigit :: 4 或 NonZeroDigit :: 4 或 HexDigit :: 4 的数学值是 4。
- DecimalDigit :: 5 或 NonZeroDigit :: 5 或 HexDigit :: 5 的数学值是 5。
- DecimalDigit :: 6 或 NonZeroDigit :: 6 或 HexDigit :: 6 的数学值是 6。
- DecimalDigit :: 7 或 NonZeroDigit :: 7 或 HexDigit :: 7 的数学值是 7。
- DecimalDigit :: 8 或 NonZeroDigit :: 8 或 HexDigit :: 8 的数学值是 8。
- DecimalDigit :: 9 或 NonZeroDigit :: 9 或 HexDigit :: 9 的数学值是 9。
- HexDigit :: a 或 HexDigit :: A 的数学值是 10。
- HexDigit :: b 或 HexDigit :: B 的数学值是 11。
- HexDigit :: c 或 HexDigit :: C 的数学值是 12。
- HexDigit :: d 或 HexDigit :: D 的数学值是 13。
- HexDigit :: e 或 HexDigit :: E 的数学值是 14。
- HexDigit :: f 或 HexDigit :: F 的数学值是 15。
- BinaryIntegerLiteral::0bBinaryDigits 的数学值是 BinaryDigits 的数学值
- BinaryIntegerLiteral::0BBinaryDigits 的数学值是 BinaryDigits 的数学值
- BinaryDigits::BinaryDigit 的数学值是 BinaryDigit 的数学值
- BinaryDigits::BinaryDigitsBinaryDigit 的数学值是 (BinaryDigits × 2的值) 乘以 BinaryDigit 的数学值
- OctalIntegerLiteral::0oOctalDigits 的数学值是 OctalDigits 的数学值
- OctalIntegerLiteral::0OOctalDigits 的数学值是 OctalDigits 的数学值
- OctalDigits::OctalDigit 的数学值是 OctalDigit 的数学值
- OctalDigits::OctalDigitsOctalDigit 的数学值是 (OctalDigits × 8的值) 乘以 OctalDigit 的数学值
- HexIntegerLiteral::0xHexDigits 的数学值是 HexDigits 的数学值
- HexIntegerLiteral::0XHexDigits 的数学值是 HexDigits 的数学值
- HexDigits::HexDigit 的数学值是 HexDigit 的数学值
- HexDigits::HexDigitsHexDigit 的数学值 (HexDigits × 16的值) 乘以 HexDigit 的数学值.

一旦确定了数字文字的确切MV，则将其舍入为Number类型的值。如果MV为0，则舍入值为+0；否则，四舍五入的值必须是MV的Number值（如6.1.6中所指定），除非文字是DecimalLiteral并且文字具有20个以上的有效数字，在这种情况下Number值可以是Number值通过将20号之后的每个有效数字替换为0位而产生的文字的MV或通过将20号之后的每个有效数字替换为0位然后在20号增加该文字而产生的文字的MV的Number值有效数字位。如果数字不是ExpnentPart的一部分，则该数字是有效的；并且

​	不为0；或左侧有一个非零数字，右侧有一个非零数字（不在ExponentPart中）。

### 11.8.4 字符串字面量 <div id="string-literals"></div>

> 注：字符串文字是用单引号或双引号引起来的零个或多个Unicode码点。Unicode码点也可以由转义序列表示。除了结束引号码点U+005C（反斜杠），U+000D（回车）和U+000A（换行）外，所有码点都可能以字符串文字形式出现。任何码点都可能以转义序列的形式出现。字符串文字的计算结果为ECMAScript字符串值。生成这些字符串值时，Unicode码点按照10.1.1中的定义进行UTF-16编码。属于基本多语言平面的码点被编码为字符串的单个码元元素。所有其他码点均编码为字符串的两个码元元素。
>

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
> \<LF>和\<CR>不能出现在字符串文字中，除非作为LineContinuation的一部分来产生空码点序列。包含在字符串文字的String值中的正确方法是使用转义序列，例如\n或\u000A。

#### 11.8.4.1 静态语义：StringValue <div id="sec-string-literals-static-semantics-stringvalue"></div>

```
StringLiteral ::
    " DoubleStringCharactersopt "
    ' SingleStringCharactersopt '
```

1. 返回String值，其代码单位是此StringLiteral的静态语义。

#### 11.8.4.2 静态语义：SV <div id="sec-static-semantics-sv"></div>

字符串文字代表String类型的值。文字的字符串值（SV）是根据字符串文字的各个部分贡献的码元来描述的。作为此过程的一部分，字符串文字中的某些Unicode码点被解释为具有数学值（MV），如下所述或11.8.3中所述。

- StringLiteral :: "" 的SV是空码元序列。
- StringLiteral :: ''的SV是空码元序列。
- StringLiteral :: "DoubleStringCharacters"的SV是DoubleStringCharacters的SV。
- StringLiteral :: 'SingleStringCharacters'的SV是SingleStringCharacters的SV。
- DoubleStringCharacters :: DoubleStringCharacter（最多两个码元的序列）的SV是DoubleStringCharacter的SV。
- DoubleStringCharacters :: DoubleStringCharacter的SV DoubleStringCharacters是由最多两个码元组成的序列，该序列是DoubleStringCharacter的SV，按照 DoubleStringCharacters SV的码元顺序排列。
- SingleStringCharacters :: SingleStringCharacter（最多两个码元的序列）的SV是SingleStringCharacter的SV
- SingleStringCharacters :: SingleStringCharacter的SV SingleStringCharacter是由最多两个码元组成的序列，该序列是SingleStringCharacter的SV，按照 SingleStringCharacter SV的码元顺序排列。
- DoubleStringCharacter :: SourceCharacter的SV是SourceCharacter的码点值的UTF16Encoding，而不是 “ 或 \ 或LineTerminator之一。
- DoubleStringCharacter :: \<LS> 的SV是码元 0x2028 (行分隔符))
- DoubleStringCharacter :: \<PS> 的SV是码元 0x2029 (段落分隔符)
- DoubleStringCharacter :: \ EscapeSequence 的SV是EscapeSequence的SV
- DoubleStringCharacter :: LineContinuation 的SV是空的码元序列
- SingleStringCharacter :: SourceCharacter 不是 ' 或 \ 或 LineTerminator 的SV是SourceCharacter的UTF16Encoding码点值
- SingleStringCharacter :: \<LS> 的SV是码元 0x2028 (行分隔符)
- SingleStringCharacter :: \<PS> 的SV是码元 0x2029 (段落分隔符)
- SingleStringCharacter :: \ EscapeSequence 的SV是EscapeSequence的SV
- SingleStringCharacter :: LineContinuation的SV是空的码元序列
- EscapeSequence :: CharacterEscapeSequence的SV是 CharacterEscapeSequence的SV
- EscapeSequence :: 0 的SV是码元 0x0000 (NULL)。
- EscapeSequence :: HexEscapeSequence的SV是 HexEscapeSequence 的SV
- EscapeSequence :: UnicodeEscapeSequence的SV是 UnicodeEscapeSequence的SV
- CharacterEscapeSequence :: SingleEscapeCharacter 的SV是由SingleEscapeCharacter根据表34确定的码元

| 转义序列 | 代码单元值 | Unicode 编码名称 | 符号  |
| -------- | ---------- | ---------------- | ----- |
| `\b`     | `0x0008`   | **退格符**       | \<BS> |
| `\t`     | `0x0009`   | **水平制表符**   | \<HT> |
| `\n`     | `0x000A`   | **换行符**       | \<LF> |
| `\v`     | `0x000B`   | **垂直制表符**   | \<VT> |
| `\f`     | `0x000C`   | **换页符**       | \<FF> |
| `\r`     | `0x000D`   | **回车符**       | \<CR> |
| `\"`     | `0x0022`   | **双引号**       | `"`   |
| `\'`     | `0x0027`   | **单引号**       | `'`   |
| `\\`     | `0x005C`   | **反斜杠**       | `\`   |

- CharacterEscapeSequence :: NonEscapeCharacter 的SV是 NonEscapeCharacter 的SV
- NonEscapeCharacter :: SourceCharacterbut 不是 EscapeCharacter 或 LineTerminator的SV是SourceCharacter的码点值的UTF16Encoding。
- HexEscapeSequence :: x HexDigitHexDigit 的码元值，是第一个HexDigit的MV的16倍加上第二个HexDigit的MV的SV
- UnicodeEscapeSequence :: u Hex4Digits 的SV是 Hex4Digits 的SV
- Hex4Digits :: HexDigitHexDigitHexDigitHexDigit 是码元值（第一个HexDigit的MV的4096倍）加上（第二个HexDigit的MV的256倍）加上（第三个HexDigit的MV的16倍）加上第四个HexDigit的MV的SV
- UnicodeEscapeSequence :: u{ CodePoint } 的SV是UTF16Encoding码点的MV

### 11.8.5 正则表达式字面量 <div id="regular-expression-literals"></div>

> 注：正则表达式文字是一个输入元素，每次对文字进行求值时都会将其转换为RegExp对象（请参见21.2）。程序中的两个正则表达式文字求和为正则表达式对象，即使这两个文字的内容相同，它们也永远不会相互比较===。还可以在运行时通过新的RegExp或将RegExp构造函数作为函数调用来创建RegExp对象（请参见21.2.3）。
>

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
> 正则表达式文字不能为空；码元序列//而不是表示空的正则表达式文字，而是开始单行注释。要指定一个空的正则表达式，请使用：/（？：）/。

#### 11.8.5.1 静态语义：Early Errors <div id="sec-literals-regular-expression-literals-static-semantics-early-errors"></div>

RegularExpressionFlags :: RegularExpressionFlags IdentifierPart

​	如果IdentifierPart包含Unicode转义序列，则是语法错误。

#### 11.8.5.2 静态语义：BodyText <div id="sec-static-semantics-bodytext"></div>

RegularExpressionLiteral :: / RegularExpressionBody / RegularExpressionFlags

​	1. 返回被识别为RegularExpressionBody的源文本

#### 11.8.5.3 静态语义：FlagText <div id="sec-static-semantics-flagtext"></div>

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
> InputSubTemplateTail替代词法目标使用TemplateSubstitutionTail。

#### 11.8.6.1 静态语义：TV and TRV <div id="sec-static-semantics-tv-and-trv"></div>

模板文字组件被解释为Unicode码点的序列。文字组件的模板值（TV）用由模板文字组件的各个部分贡献的代码单位值（SV，11.8.4）描述。作为此过程的一部分，模板组件中的某些Unicode码点被解释为具有数学值（MV，11.8.3）。在确定电视时，将转义序列替换为由转义序列表示的Unicode码点的UTF-16码元。模板原始值（TRV）与模板值类似，不同之处在于TRV中的转义序列按字面意义进行解释。

- NoSubstitutionTemplate :: \`\` 的TV和TRV是空的码元序列.
- TemplateHead :: \`${ 的TV和TRV是空的码元序列.
- TemplateMiddle :: }${ 的TV和TRV是空的码元序列.
- TemplateTail :: }\` 的TV和TRV是空的码元序列.
- NoSubstitutionTemplate :: \`TemplateCharacters\`的TV是TemplateCharacters的TV.
- TemplateHead :: \`TemplateCharacters ${ 的TV是TemplateCharacters的TV.
- TemplateMiddle :: }TemplateCharacters ${的TV是TemplateCharacters的TV.
- TemplateTail :: } TemplateCharacters \`的TV是TemplateCharacters的TV.
- TemplateCharacters :: TemplateCharacter的TV是TemplateCharacter的TV.
- 如果TemplateCharacter的TV是undefined或TemplateCharacters是undefined，则TemplateCharacters :: TemplateCharacter TemplateCharacters 的TV是undefined，否则这是一个按照TemplateCharacters的TV的码元组成的TemplateCharacter的TV码元序列
- TemplateCharacter :: SourceCharacterbut 的TV是SourceCharacter的码点值的UTF16Encoding，而不是 \` 或 \ 或 $ 或 LineTerminator.
- TemplateCharacter :: $ 的TV是码元值0x0024（$）.
- TemplateCharacter :: \ EscapeSequence的TV是EscapeSequence的SV
- TemplateCharacter :: \ NotEscapeSequence 的TV是undefined.
- TemplateCharacter :: LineContinuation的TV是LineContinuation的TV.
- TemplateCharacter :: LineTerminatorSequence的TV是LineTerminatorSequence的TRV.
- LineContinuation :: \LineTerminatorSequence的TV是空的码元序列.
- NoSubstitutionTemplate :: \`TemplateCharacters\`的TRV是TemplateCharacters的TRV.
- TemplateHead :: \` TemplateCharacters ${的TRV是TemplateCharacters的TRV.
- TemplateMiddle :: } TemplateCharacters ${的TRV是TemplateCharacters的TRV.
- TemplateTail :: } TemplateCharacters \`的TRV是TemplateCharacters的TRV.
- TemplateCharacters :: TemplateCharacter的TRV是TemplateCharacter的TRV.
- TemplateCharacters :: TemplateCharacter TemplateCharacters 的TRV是一个按照TemplateCharacters的TRV的码元组成的TemplateCharacter的TRV码元序列
- TemplateCharacter :: SourceCharacterbut 的TRV是SourceCharacter的码点值的UTF16Encoding，而不是 \` 或 \ 或 $ 或 LineTerminator.
- \` or \ or $ or LineTerminator的TRV of TemplateCharacter :: SourceCharacterbut not one是the UTF16Encoding of the code point value of SourceCharacter.
- TemplateCharacter :: $ 的TRV是码元值0x0024（$）
- TemplateCharacter :: \EscapeSequence的TRV是the sequence consisting of the code unit value 0x005C followed by the code units of TRV of EscapeSequence.
- TemplateCharacter :: LineContinuation的TRV是TRV of LineContinuation.
- TemplateCharacter :: LineTerminatorSequence的TRV是LineTerminatorSequence的TRV.
- EscapeSequence :: CharacterEscapeSequence的TRV是TRV of the CharacterEscapeSequence.
- EscapeSequence :: 0的TRV是the code unit value 0x0030 (DIGIT ZERO).
- EscapeSequence :: HexEscapeSequence的TRV是TRV of the HexEscapeSequence.
- EscapeSequence :: UnicodeEscapeSequence的TRV是TRV of the UnicodeEscapeSequence.
- CharacterEscapeSequence :: SingleEscapeCharacter的TRV是TRV of the SingleEscapeCharacter.
- CharacterEscapeSequence :: NonEscapeCharacter的TRV是the NonEscapeCharacter的静态语义
- SingleEscapeCharacter :: one of'"\bfnrtv的TRV是the SourceCharacter that的静态语义是that single code point.
- HexEscapeSequence :: xHexDigitHexDigit的TRV是the sequence consisting of code unit value 0x0078 followed by TRV of the first HexDigit followed by TRV of the second HexDigit.
- UnicodeEscapeSequence :: uHex4Digits的TRV是the sequence consisting of code unit value 0x0075 followed by TRV of Hex4Digits.
- UnicodeEscapeSequence :: u{HexDigits}的TRV是the sequence consisting of code unit value 0x0075 followed by code unit value 0x007B followed by TRV of HexDigits followed by code unit value 0x007D.
- Hex4Digits :: HexDigitHexDigitHexDigitHexDigit的TRV是the sequence consisting of TRV of the first HexDigit followed by TRV of the second HexDigit followed by TRV of the third HexDigit followed by TRV of the fourth HexDigit.
- HexDigits :: HexDigit的TRV是TRV of HexDigit.
- HexDigits :: HexDigitsHexDigit的TRV是the sequence consisting of TRV of HexDigits followed by TRV of HexDigit.
- a HexDigit的TRV是the SourceCharacter that的静态语义是that HexDigit.
- LineContinuation :: \LineTerminatorSequence的TRV是the sequence consisting of the code unit value 0x005C followed by the code units of TRV of LineTerminatorSequence.
- LineTerminatorSequence :: \<LF>的TRV是the code unit value 0x000A.
- LineTerminatorSequence :: \<CR>的TRV是the code unit value 0x000A.
- LineTerminatorSequence :: \<LS>的TRV是the code unit value 0x2028.
- LineTerminatorSequence :: \<PS>的TRV是the code unit value 0x2029.
- LineTerminatorSequence :: \<CR>\<LF>的TRV是the sequence consisting of the code unit value 0x000A.

## 11.9 自动分号插入 <div id="automatic-semicolon-insertion"></div>

大多数ECMAScript语句和声明必须以分号终止。这样的分号可能总是显式地出现在源文本中。但是，为方便起见，在某些情况下可以从源文本中省略此类分号。通过说在这些情况下将分号自动插入到源代码令牌流中来描述这些情况。

### 11.9.1 自动分号插入规则 <div id="rules-of-automatic-semicolon-insertion"></div>

在以下规则中，“令牌”是指使用第11条中所述的当前词法目标符号确定的实际公认词法令牌。

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

```
description 首先将ECMAScript脚本或模块的源文本转换成一系列输入元素，这些输入元素是标记，行终止符，注释或空白。从左到右扫描源文本，重复使用尽可能长的码点序列作为下一个输入元素。
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,lexical-grammar
```