# 6 ECMAScript数据类型和值 <div id="sec-ecmascript-data-types-and-values"></div>

本规范的算法操作的值每一个都有其相应的类型。 可能的值类型正是本节中定义的值类型。类型又进一步分为ECMAScript语言类型（language types）以及规范类型（specification types）。 

在本规范中，符号“Type(`x`)”用来作为“the type of x”的简写，这里的“type”指向定义在本章中的ECMAScript语言以及规范类型。 当使用“empty”一词来命名值时，等效于说“没有任何类型的值”。

## 6.1 ECMAScript语言类型 <div id="sec-language-type"></div>

ECMAScript语言类型 对应的是程序员使用 ECMAScript 语言直接操作的值。ECMAScript语言类型 包括 Undefined、Null、Boolean、String、Number、Object。 ECMAScript语言值是一个具有ECMAScript语言类型的值。 

### 6.1.1 Undefined类型 <div id="sec-undefined-type"></div>

Undefined类型仅仅只有一个值，被称为undefined。任何没有被分配一个值的变量的值就是undefined。

### 6.1.2 Null类型 <div id="sec-null-type"></div>

Null类型仅仅只有一个值，被称为null。

### 6.1.3 Boolean类型 <div id="sec-boolean-type"></div>

Boolean类型代表一个拥有两个值的逻辑的实体，它们被称为true和false。

### 6.1.4 String类型 <div id="sec-string-type"></div>

字符串类型是所有有限的零个或多个16位无符号整数值（“元素”）的有序序列， 最大的长度是2^53 - 1个元素的长度。在运行的 ECMAScript 程序中，字符串类型常被用于表示文本数据，此时字符串中的每个元素都被视为一个 代码单元。 每个元素都被认为占有此序列中的一个位置，用非负整数索引这些位置。任何时候，第一个元素（若存在）在位置0，下一个元素（若存在）在位置1，依此类推。字符串的长度即其中元素（即16位的值）的个数。空字符串长度为零，因而不包含任何元素。

若一个字符串包含实际的文本数据，每个元素都被认为是一个单独的 UTF-16 代码单元。无论这是不是 String 实际的存储格式，String 中的字符都被当作表示为 UTF-16 来计数。除非特别声明，作用在字符串上的所有操作都视它们为无差别的 16 位无符号整数；这些操作不保证结果字符串仍为正规形式，也不保证语言敏感结果。

不解释字符串内容的ECMAScript操作不会有进一步的语义。解释字符串值的操作将每个元素视为单个UTF-16代码单元。但是，ECMAScript并不限制这些代码单元的值或它们之间的关系，因此将字符串内容进一步解释为以UTF-16编码的Unicode代码点序列的操作必须考虑格式错误的子序列。此类操作会对数字值在0xD800到0xDBFF（由Unicode标准定义为前导代理，或更正式地作为高位代理码元）范围内的每个码元和每个具有数字值的码元进行特殊处理。使用以下规则在0xDC00到0xDFFF（包括后尾代理，或更正式地定义为低位代理码元）的范围内：

​	一个不是前导代理或尾代理的码元将被解释为具有相同值的码位。

​	 一个包含两个码元的序列，第一个码元`c1`在范围0XD800到0XDBFF，第二个码元`c2`在范围0xDC00到0xDFFF，这样的称为代理对，它们被解释为一个值为（`c1` - 0xD800） * 0x400 + （`c2` - 0xDC00） + 0x10000的码点（See 10.1.2）。 

​	一个作为前导代理或尾代理但不属于代理对的代码单元，将被解释为具有相同值的代码点。

函数String.prototype.normalize（请参见21.1.3.12）可用于显式标准化String值。String.prototype.localeCompare（请参见21.1.3.10）内部对字符串进行了规范化，但是没有其他操作隐式对对其进行操作的字符串进行规范化。 只有那些被显式地指定用来处理语言或者对语言环境敏感的操作才会产生语言敏感的结果。 

> 注意 这个设计背后的基本原理是保证Strings的实现尽可能是简单和高效的。如果ECMAScript源文本在规范化的形式C内，那么字符串字面量也应当被认为是规范化的，只要它们不包含任何Unicode转移序列。 

在本说明书中，短语“ A，B，...的字符串连接”（其中每个自变量是字符串值，代码单元或代码单元序列）表示该字符串值，其代码单元序列为每个参数（按顺序）的代码单元（按顺序）的串联

### 6.1.5 Symbol类型 <div id="sec-symbol-type"></div>

Symbol类型是所有非字符串的也许被用作作为对象的属性的键的集合。

每个可能的Symbol值都是唯一的和不可变的。

每个Symbol值永远地保持一个与之关联的叫做[[Description]]的值，它要么是`undefined`要么是一个字符串值。 

#### 6.1.5.1 常见的Symbol <div id="sec-well-known-symbol"></div>

众所周知的符号是内置符号值，此规范的算法明确引用了这些值。它们通常用作属性的键，其值用作规范算法的扩展点。除非另有说明，否则所有作用域（8.2）都会共享众所周知的符号值。

在本说明书中，使用@@name形式的符号来引用常见的符号，其中“name”是 表1 。

Table 1: 常见Symbols

| 规格名称             | [[Description]]               | 值与用途                                                     |
| -------------------- | ----------------------------- | ------------------------------------------------------------ |
| @@asyncIterator      | `"Symbol.asyncIterator"`      | 返回对象的默认AsyncIterator的方法。由for-await-of语句的语义调用。 |
| @@hasInstance        | `"Symbol.hasInstance"`        | 确定构造函数对象是否将对象识别为构造函数实例之一的方法。 由` instanceof `运算符的语义调用。 |
| @@isConcatSpreadable | `"Symbol.isConcatSpreadable"` | 一个布尔值属性，如果为true，则表示一个对象可以通过` Array.prototype.concat `扁平化到其数组元素。 |
| @@iterator           | `"Symbol.iterator"`           | 一种返回对象的默认迭代器的方法。 被称为for语句的语义。       |
| @@match              | `"Symbol.match"`              | 与正则表达式匹配字符串的正则表达式方法。 由` String.prototype.match `方法调用。 |
| @@replace            | `"Symbol.replace"`            | 一个正则表达式方法，用于替换字符串的匹配子字符串。 由` String.prototype.replace `方法调用。 |
| @@search             | `"Symbol.search"`             | 一个正则表达式方法，它返回与正则表达式匹配的字符串中的索引。 由` String.prototype.search `方法调用。 |
| @@species            | `"Symbol.species"`            | 一个函数值属性，它是用于创建派生对象的构造函数。             |
| @@split              | `"Symbol.split"`              | 一个正则表达式方法，它在与正则表达式匹配的索引处分割字符串。 由` String.prototype.split `方法调用。 |
| @@toPrimitive        | `"Symbol.toPrimitive"`        | 将对象转换为相应的原始值的方法。 由 [ToPrimitive ](http://zhoushengfe.com/es6/es6-ch.html#sec-toprimitive)抽象操作调用。 |
| @@toStringTag        | `"Symbol.toStringTag"`        | 用于创建对象的默认字符串描述的String值属性。 通过内置方法访问` Object.prototype.toString `。 |
| @@unscopables        | `"Symbol.unscopables"`        | 一个对象值属性，其自有和继承的属性名称是从具有关联对象的`with`中排除的属性名称。 |

### 6.1.6 Number类型 <div id="sec-number-type"></div>

Number类型正好具有18437736874454810627（即2^64-2^53 + 3）个值，表示双精度64位格式的在IEEE标准中关于二进制浮点类型运算的IEEE 754-2008规范的值 ，除了9007199254740990（即2^53-2）与IEEE标准的不同，“ Not-a-Number”值在ECMAScript中表示为单个特殊的NaN值。（请注意，NaN值是由程序表达式NaN产生的。）在某些实现中，外部代码可能能够检测到各种Not-a-Number值之间的差异，但是这种行为取决于实现。对于ECMAScript代码，所有NaN值彼此之间是无法区分的。

> 注意：在一个Number值已经被存储后，位模式（bit pattern）也许可以通过一个ArrayBuffer或者SharedArrayBuffer观察到，但是这和Number值的内部表示一样，不是ECMAScript的实现所必须的。 

这里有两种其它的特殊值，叫做正无穷和负无穷。为了简洁和说明目的，这些值也分别用符号`+∞`和`-∞`表示。（注意这两个无穷数由程序表达式`+Infinity`（或者简单的`Infinity`）和`-Infinity`产生）

其它的18437736874454810624个值（即， , 2^64 - 2^53 ）被称作有限数（finite numbers）。其中一半是正数，一半是负数；对于每个有限正数值，这里都有一个相应量级的负数值。 

注意这里既有正0也有负0。为了简洁和说明的目的，这些值也分别用符号`+0`和`-0`表示。（注意这两个不同的零数字值由程序表达式`+0`（或者简单的`0`）和`-0`产生。） 

18437736874454810622（即 2^64 - 2^53 - 2 ）个有限非0值有下面两种类型： 

18428729675200069632 (即 2^64 - 2^54)   个值是被规范化了的，它们是下面的形式： 

```
s × m × 2^e 
```

这里的s是+1或者-1， m是一个小于2^53但是不小于2^52的正数，e是一个闭区间-1074到971的整数。 

剩下的9007199254740990（即2^53 - 2）个值是没有被范式化的，它们是下面的形式：

```
s × m × 2^e
```

这里的s是+1或者-1，m是一个小于2^52的正数，e是-1074。 

请注意，所有大小不大于2^53的正整数和负整数都可以在Number类型中表示（实际上，整数0有两种表示形式，即+0和-0）。

如果是非0的话，一个有限数有一个奇数的尾数那么它就是奇数，整数m用来表示这个尾数（即上面提到的两种形式之一）。否则，它就有一个偶数的尾数。

在本规范中，短语“the Number value for `x`”里的`x`刚好代表一个非0值的真实的数学量（也有可能是如π之类的无理数），也意味着一个Number值按照下面的方式被选择出来。首先考虑Number类型中所有的有限数的集合，除去-0，加上两个Number类型无法表示的值，被称为2^1024（即1 × 2^53 * 2^971 ）以及-2^1024，即-1 × 2^53 * 2^971)。然后选择这个集合中离`x`最近的元素。如果有两个值一样接近，那么选择有偶数尾数的那一个。处于为此目的考虑，两个额外的值2^1024和-2^1024被认为是拥有相同的偶数尾数。最后，如果选择的是2^1024，用+∞替换它。如果选择的是-2^1024，用-∞替换它。如果选择的是`+0`，只在`x`小于0时才用-0替换它。除了上面提到的这些，任意其它的选择值是不会发生变化的。最后的结果就是`x`的Number值。（这样就能刚好产生与IEEE 754-2008的“四舍五入到最接近的关系”行为模式相对应的值。） 

某些ECMAScript运算符仅处理特定范围内的整数，例如-2^31至2^31-1（含）或0至2^16-1（含）的范围。这些运算符接受Number类型的任何值，但首先将每个此类值转换为期望范围内的整数值。请参阅7.1中的数值转换操作说明。

### 6.1.7 Object类型 <div id="sec-object-type"></div>

从逻辑上讲，对象是属性的集合。每个属性都是数据属性或访问器属性：

- 一个数据属性关联一个ECMAScript语言值的key值以及一组Boolean属性的集合。
- 一个访问器属性关联一个或两个访问器函数的key值，一组Boolean属性的集合。访问器函数被用来存储或者检索与这个属性相关的ECMAScript语言值。

属性是用key值来识别的。一个属性的key值要么是一个ECMAScript String值，要么是一个Symbol值。所有的String和Symbol值，包括空字符串，都是合法的属性key值。当一个属性的key是String值的时候，这个属性的name是这个属性的key。

一个整数索引是一个字符串值的属性键，它是一个规范的数字字符串（参考7.1.16）。它的数字值要么是+0，要么是一个 ≤ 2^53-1的正整数。一个数组索引是一个整数索引，它的数字值i的范围是+0 ≤ i < 2^32-1。

属性键用于访问属性及其值。属性有两种访问方式：get和set，分别对应于值检索和赋值。通过get和set访问可访问的属性包括作为对象直接一部分的自身属性和由另一个关联对象通过属性继承关系提供的继承属性。继承的属性可以是关联对象的拥有属性或继承的属性。一个对象的每个自己的属性必须各自具有与该对象的其他自己的属性的键值不同的键值。

从逻辑上讲，所有对象都是属性的集合，但是有多种形式的对象在其访问和操作其属性时具有不同的语义。普通对象是对象的最常见形式，并具有默认的对象语义。异类对象其属性语义与默认语义在各个方面都有区别。

###  6.1.7.1 属性的特性 <div id="sec-property-attributes"></div>

本规范中的属性被用作定义和解释对象属性的状态。数据属性与表2中列出的属性的key值一一对应。

| 特性名称         | 取值范围                 | 描述                                                         |
| --------------- | ----------------------- | ----------------------------------------------------------- |
| [[Value]]        | 任意的ECMAScript语言类型 | 访问属性时被检索到的值                                       |
| [[Writable]]     | Boolean                  | 如果为`false`，任何尝试通过[[Set]]去改变这个属性的[[Value]]属性的ECMAScript代码都不会成功。 |
| [[Enumerable]]   | Boolean                  | 如果为`true`,这个属性被for-in枚举时将会是可枚举的（参考13.7.5）。否则，这个属性就是不可枚举的。 |
| [[Configurable]] | Boolean                  | 如果为`false`，任何尝试删除这个属性，把这个属性改为访问器属性，或者改变它的除了[[Value]]之外的属性，或者改变它的[[Writable]]为`false`的操作都会失败。 |

 一个访问器属性accessor property与表3中列出的属性的key值相关联。

| 特性名称 | 取值范围 | 描述                                                  |
| --------------- | ----------- | ----------------------------------------------------------- |
| [[Get]]          | Object\Undefined                                                    | 如果这个值是一个对象，那么它必须是一个函数对象。在每次访问这个属性的时候，这个函数的内置[[Call]]方法（下面的表6中）将会被调用，同时会向这个方法传入一个空的arguments。 |
| [[Set]]          | Object\Undefined                                                    | 如果这个值是一个对象，那么它必须是一个函数对象。在每次设置这个属性的时候，这个函数的内置[[Call]]方法（下面的表6中）将会被调用，同时会向这个方法传入包含所赋的值的arguments，作为这个arguments的唯一参数。[[Set]]内置方法也许会对后续的[[Get]]内置方法调用产生的返回值产生影响，但不是必须的。 |
| [[Enumerable]]   | Boolean      | 如果为`true`,这个属性被for-in枚举时将会是可枚举的（参考[13.7.5](https://tc39.github.io/ecma262/#sec-for-in-and-for-of-statements)）。否则，这个属性就是不可枚举的。 |
| [[Configurable]] | Boolean      | 如果为`false`，任何尝试删除这个属性，把这个属性改为数据属性，或者改变它的其它attributes的操作都会失败。 |

如果一个属性的attributes的初始值没有被本规范显式地指定的时候，那么使用在表4中定义的默认值：

| 特性名称         | 默认值      |
| :--------------- | :---------- |
| [[Value]]        | `undefined` |
| [[Get]]          | `undefined` |
| [[Set]]          | `undefined` |
| [[Writable]]     | `false`     |
| [[Enumerable]]   | `false`     |
| [[Configurable]] | `false`     |

### 6.1.7.2 对象的内置方法和内置槽 <div id="sec-object-internal-methods-and-slots"></div>

在ECMAScript中，对象的实际语义是通过算法来调用内置方法指定的。在ECMAScript引擎中的每个对象都与一系列的定义它的运行时行为的内置方法相关联。这些内置方法不属于ECMAScript语言的一部分。通过本规范定义它们的部分仅仅是为了解释和说明的目的。然而，在ECMAScript具体实现中的每个对象必须表现得像这些内置方法与它关联了一样。其中的准确的行为由实现来决定。

内置方法的名称是多种多样的。这意味着当一个常见的内置方法被调用的时候，不同的对象值也许会运行不同的算法。内置方法被调用时实际的对象成为这个调用的“目标”。如果在运行时一个算法的实现尝试去使用一个对象不支持的内置方法，一个`TypeError`将会被抛出。

内置槽对应相关联对象的内部状态，被ECMAScript规范中不同的算法使用。内置槽不是对象的属性，也不会被继承。根据规范中具体的内置槽，这样的内部状态也许包含任意ECMAScript语言类型的值或者指定的ECMAScript规范类型的值。除非显式地指定，否则内置槽作为创建一个对象的一部分过程被分配，并且也许不能被动态地添加到对象上。除非显式地指定，否则一个内置槽的初始值是`undefined`。本规范内不同的算法都会创建有内置槽的对象。然而，ECMAScript语言不提供直接的方法去访问一个对象的内置槽。

本规范内的内置方法和内置槽使用闭合的双方括号[[]]来标识。

下方的表5总结了被规范使用的适用于所有对象的创建或者被ECMAScript代码操作的至关重要的内置方法。每个对象对于所有至关重要的算法都必须有相应的算法。然而，所有的对象没有必要对那些方法使用相同的算法。

下面的表5中的 “Signature”列以及其它相似的表格描述了每个内置方法的调用模式。调用模式总是包含一个括起来的描述参数名的列表。如果一个参数名与ECMAScript类型的名字一样，那么代表描述的是参数值需要的类型。如果一个内置方法显式地返回了一个值，它的参数列表随后就会跟着一个“→”符合，以及返回值的类型。在signature中使用的类型的名字指向在第6章中定义的类型，另外还增加了一些下面的名称。“any”代表这个值也许是任意的ECMAScript语言值。一个内置方法会隐式地返回一个完成记录。除了它的参数，一个内置方法还总是会访问这个方法调用的对象的目标（即前面提到的target）。

| 内部方法 | 签名                                   | 描述                                                  |
| -------------------- | ------------------------------------------ | :---------------------------------------------------------- |
| [[GetPrototypeOf]]    | ( ) → Object \ Null                                                         | 确定为这个对象提供继承的属性的对象。一个`null`值代表没有继承的属性。 |
| [[SetPrototypeOf]]    | (Object \ Null) → Boolean                                              | 将这个对象与提供继承的属性的对象相关联。传递`null`表示没有继承的属性。返回`true`表示操作成功，返回`false`表示操作失败。 |
| [[IsExtensible]]      | ( ) → Boolean                               | 决定是否允许添加额外的属性到这个对象上。                     |
| [[PreventExtensions]] | ( ) → Boolean                               | 控制一个新的属性是否能加到这个对象上。返回`true`表示操作成功，返回`false`表示操作失败。 |
| [[GetOwnProperty]]    | (propertyKey) → Undefined \ Property Descriptor                                          | 返回这个对象的一个自身属性的属性描述符，它的key为propertyKey，或者undefined（如果没有这样的属性存在的话）。 |
| [[DefineOwnProperty]] | (propertyKey, PropertyDescriptor) → Boolean | 创建或者改变自身属性，它的key为propertyKey，它的状态为PropertyDescriptor。返回`true`表示属性被成功创建/更新，返回`false`表示属性不能被创建/更新。 |
| [[HasProperty]]       | (propertyKey) → Boolean                     | 返回一个Boolean值，代表这个对象是否已经有一个自身的或者继承的key为propertyKey的属性。 |
| [[Get]]               | (propertyKey, Receiver) → any               | 返回这个对象里key值为propertyKey的属性的值。如果任何的ECMAScript代码必须被运行来检索这个属性值，Receiver就会作为解析代码时的`this`值。 |
| [[Set]]               | (propertyKey, value, Receiver) → Boolean    | 设置这个对象中的key为propertyKey的属性的值为value。如果任何的ECMAScript代码必须被运行来检索这个属性值，Receiver就会作为解析代码时的`this`值。返回`true`表示这个属性能被设置，返回`false`表示不能被设置。 |
| [[Delete]]            | (propertyKey) → Boolean                     | 移除这个对象的key值为propertyKey的自身属性。返回`false`表示这个属性没有被移除，仍然存在。返回`true`表示已经被移除，不再存在。 |
| [[OwnPropertyKeys]]   | ( ) → List of propertyKey                   | 返回一个一个List，这个List里的元素都来自这个对象的自身属性的key。 |

table 6总结了可被称为函数的对象所支持的其他基本内部方法。函数对象是支持[[Call]]内部方法的对象。构造函数是支持[[Construct]]内部方法的对象。每个支持[[Construct]]的对象都必须支持[[Call]]。也就是说，每个构造函数都必须是一个函数对象。因此，构造函数也可以称为构造函数或构造函数对象。

Table 6: 功能对象的附加基本内部方法

| 内部方法      | 签名                                                         | 描述                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[Call]]      | (*any*, a List of *any*) **→** *any* | 执行与此对象关联的代码。 通过函数表达式调用。 内部方法的参数是一个this值，还有一个包含通过调用表达式传递给函数的参数的列表。 实现此内部方法的对象是*可调用的(callable)*。 |
| [[Construct]] | (a List of *any*, Object) **→** Object | 创建一个对象。 通过`new`或`super`运算符调用。 内部方法的第一个参数是包含运算符的参数的列表。 第二个参数是最初应用` new `运算符的对象。 实现此内部方法的对象称为*构造函数(constructors)*。 函数对象不一定是构造函数，而非构造函数对象没有[[Construct]]内部方法。 |

在第9节中指定了普通对象和标准异类对象的基本内部方法的语义。如果实现不支持对异类对象的内部方法的任何指定使用，则尝试使用该用法时必须抛出TypeError异常。

#### 6.1.7.3 基本内部方法的不变量 <div id="sec-invariants-of-essential-internal-methods"></div>

ECMAScript引擎中的对象的内置方法必须符合下方指定的不变量列表。普通的ECMAScript对象以及本规范中标准的异类对象维护这些不变量。ECMAScript代理（Proxy）对象通过对[[ProxyHandler]]对象被调用的结果的运行时检查来维护这些不变量。

任何提供异类对象的实现也必须维护这些对象的不变量 。违反这些不变式可能会导致ECMAScript代码具有不可预测的行为并引发安全问题。但是，违反这些不变量绝不能损害实现的内存安全性。

一个实现必须不允许以任何方式规避这些不变量，例如通过提供替代接口来实现基本内部方法的功能而不强制用不变量。

定义：

- 一个内置方法的target是一个对象，这个对象的对应的内置方法会被调用。
- 如果一个target被观察到它的[[IsExtensible]]内置方法返回`false`或者[[PreventExtensions]]返回`true`，那么它是不可扩展的。
- 一个不存在的属性是指那些在不可扩展的target的自身属性上不存在的属性。
- 所有引用SameValue（译注：即判断两个值是否相等）的地方都根据SameValue算法。

 **[[GetPrototypeOf]] ( )** 

- 返回值的类型必须是Object或者Null。
- 如果target是不可扩展的，并且[[GetPrototypeOf]]返回了一个值v，那么任何将来的对[[GetPrototypeOf]]的调用都应该返回v的SameValue。

> 注意1： 一个对象的原型链的长度应该是有限的（也就是说，从任意对象开始，递归地对结果应用[[GetPrototypeOf]]内置方法最终应该得到null）。然而，如果这个原型链包含任意的不使用普通对象的[[GetPrototypeOf]]定义的异类对象，那么作为一个对象的不变量水平，这个要求不是强制的。当访问对象属性的时候，这样的一个环形的原型链也许最终会导致无限循环。 

**[[SetPrototypeOf]] (V)**

- 返回值的类型必须是Boolean。
- 如果target是不可扩展的，[[SetPrototypeOf]]必须返回false，除非V是target观察到的[[GetPrototypeOf]]值的SameValue。

**[[IsExtensible]] ( )**

- 返回值的类型必须是Boolean。
- 如果[[IsExtensible]]返回false，所有将来的在target上的对[[IsExtensible]]的调用都必须返回false。

**[[PreventExtensions]] ( )**

- 返回值的类型必须是Boolean。
- 如果[[PreventExtensions]]返回false，所有将来的在target上的对[[IsExtensible]]的调用都必须返回false，并且现在开始target被认为是不可扩展的。

**[[GetOwnProperty]] ( P )**

- 返回值的类型必须是Property Descriptor或者Undefined。
- 如果返回值的类型为Property Descriptor，则返回值必须为完整的属性描述符。 (参考 6.2.5.6).
- 如果一个属性P被描述为一个含有Desc的数据属性。访问器 的[[Value]]等于v，访问器的[[Writable]]以及[[Configurable]]为false，那么对于访问器的[[Value]]属性，必须返回SameValue。将来对这个属性的[[Value]]属性的调用变成[[GetOwnProperty]] ( P )。
- 如果P的除了[[Writable]]之外的属性随着时间发生了变化或者这个属性消失了，那么P的[[Configurable]]属性必须变成true。
- 如果[[Writable]]属性从false变成了true，那么[[Configurable]]属性必须变成true。
- 如果target是不可扩展的，并且P是不存在的，那么所有将来在target上对[[GetOwnProperty]] (P)的调用必须把P描述为不存在的（即[[GetOwnProperty]] (P)必须返回undefined）。

> 注意2： 作为第三个不变量的结论，如果一个属性被描述为一个数据属性并且随着时间它返回不同的值，那么访问器的[[Writable]]和[[Configurable]]属性必须是true，即使没有机制去改变通过其它内置方法暴露出来的值。 

**[[DefineOwnProperty]] ( P, Desc)**

- 返回值的类型必须是Boolean。
- 如果P之前已经被观察为target的一个不可配置的自身属性，[[DefineOwnProperty]]必须返回false，除非：
- 1. P是一个不可配置的可写的自身数据属性。一个不可配置的可写数据属性能被改变为一个不可配置的不可写的数据属性。
- 2. 所有在访问器中的属性都是P的属性的SameValue。
- 如果target是不可扩展的并且P是一个不存在的自身属性，[[DefineOwnProperty]] (P, Desc)必须返回false。也就是说，一个不可扩展的对象不能扩展新的属性。

**[[HasProperty]] ( P )**

- 返回值的类型必须是Boolean。
- 如果P之前被观察到是target的一个不可配置的数据或者访问器自身属性，[[HasProperty]]必须返回true。

**[[Get]] (P, Receiver)**

- 如果P之前被观察到是target的一个value为v的不可配置且不可写的自身数据属性，那么[[Get]]必须返回SameValue。
- 如果P之前被观察到是[[Get]]属性是undefined的target的一个不可配置的自身访问器属性，那么[[Get]]操作必须返回undefined。

**[[Set]] ( P, V, Receiver)**

- 返回值的类型必须是Boolean。
- 如果P之前被观察到是target的一个value为v的不可配置且不可写的自身数据属性，那么[[Set]]必须返回false，除非V是P的[[Value]]属性的SameValue。
- 如果P之前被观察到[[Set]]属性是undefined 的target的是一个不可配置的自身访问器属性，那么[[Set]]操作必须返回undefined。

**[[Delete]] ( P )**

- 返回值的类型必须是Boolean。
- 如果P之前被观察到是target的一个不可配置的自身数据属性或者访问器属性，[[Delete]]必须返回false。

**[[OwnPropertyKeys]] ( )**

- 返回值必须是一个List。
- 返回的List中的每个元素的类型要么是String，要么是Symbol。
- 返回的List必须至少包含之前观察到的所有的不可配置的自身属性。
- 如果这个对象是不可扩展的，那么返回的List必须只能包含这个对象的所有通过[[GetOwnProperty]]观察到的自身属性的key。

**[[Construct]] ( )**

- 返回值的类型必须是Object。

#### 6.1.7.4 常见的内部对象 <div id="sec-well-known-intrinsic-objects"></div>

常见的内部函数是那些被本规范的算法显式地引用的内置对象，在这些函数中通常拥有基于特定域下的特性。除非另有说明，否则每个内部对象在每个域中都实际对应一个相似对象的集合。

在本规范中，一个像%name%这样的引用就表示关联到当前域的内部对象的对应的name。当前域的确定以及它的内部情况在8.3节中描述。常见的内部对象在下面的表7中列出。

| 内部名称                         | 全局名称                        | ECMAScript 语言意义                                          |
| :------------------------------- | :------------------------------ | :----------------------------------------------------------- |
| %Array%                          | **Array**                       | **Array**构造函数（参考[22.1.1](https://tc39.github.io/ecma262/#sec-array-constructor)） |
| %ArrayBuffer%                    | **ArrayBuffer**                 | **ArrayBuffer**构造函数（参考[24.1.2](https://tc39.github.io/ecma262/#sec-arraybuffer-constructor)） |
| %ArrayBufferPrototype%           | **ArrayBuffer.prototype**       | %ArrayBuffer%的**prototype**数据属性的初始值                 |
| %ArrayIteratorPrototype%         |                                 | Array迭代器的prototype对象（参考[22.1.5](https://tc39.github.io/ecma262/#sec-array-iterator-objects)） |
| %ArrayPrototype%                 | **Array.prototype**             | %Array%的**prototype**数据属性的初始值（参考[22.1.3](https://tc39.github.io/ecma262/#sec-properties-of-the-array-prototype-object)） |
| %ArrayProto_entries%             | **Array.prototype.entries**     | %ArrayPrototype%的**entries**数据属性的初始值（22.1.3.4）    |
| %ArrayProto_forEach%             | **Array.prototype.forEach**     | %ArrayPrototype%的**forEach**数据属性的初始值（22.1.3.12）   |
| %ArrayProto_keys%                | **Array.prototype.keys**        | %ArrayPrototype%的**keys**数据属性的初始值（22.1.3.16）      |
| %ArrayProto_values%              | **Array.prototype.values**      | %ArrayPrototype%的**prototype**数据属性的初始值（参考[22.1.3.32](https://tc39.github.io/ecma262/#sec-array.prototype.values)） |
| %AsyncFromSyncIteratorPrototype% |                                 | async-from-sync迭代器对象属性                                |
| %AsyncFunction%                  |                                 | 异步的函数对象（async function ）的构造器（参考[25.7.1](https://tc39.github.io/ecma262/#sec-async-function-constructor)） |
| %AsyncFunctionPrototype%         |                                 | %AsyncFunction%的**prototype**数据属性的初始值               |
| %AsyncGenerator%                 |                                 | %AsyncGeneratorFunction%的原型属性初始值                     |
| %AsyncGeneratorFunction%         |                                 | 异步迭代器随想的构造函数(25.3.1)                             |
| %AsyncGeneratorPrototype%        |                                 | %AsyncGenerator%的原型属性初始值                             |
| %AsyncIteratorPrototype%         |                                 | 所有标准内置异步迭代对象继承自该对象                         |
| %Atomics%                        | **Atomics**                     | **Atomic**对象（参考[24.4](https://tc39.github.io/ecma262/#sec-atomics-object)） |
| %Boolean%                        | **Boolean**                     | **Boolean**构造函数（参考[19.3.1](https://tc39.github.io/ecma262/#sec-boolean-constructor)） |
| %BooleanPrototype%               | **Boolean.prototype**           | %Boolean%的**prototype**数据属性的初始值（参考[19.3.3](https://tc39.github.io/ecma262/#sec-properties-of-the-boolean-prototype-object)） |
| %DataView%                       | **DataView**                    | **DataView**构造函数（参考[24.3.2](https://tc39.github.io/ecma262/#sec-dataview-constructor)） |
| %DataViewPrototype%              | **DataView.prototype**          | %DataView%的**prototype**数据属性的初始值                    |
| %Date%                           | **Date**                        | **Date**构造函数（参考[20.3.2](https://tc39.github.io/ecma262/#sec-date-constructor)） |
| %DatePrototype%                  | **Date.prototype**              | %Date%的**prototype**数据属性的初始值                        |
| %decodeURI%                      | **decodeURI**                   | **decodeURI**函数（参考[18.2.6.2](https://tc39.github.io/ecma262/#sec-decodeuri-encodeduri)） |
| %decodeURIComponent%             | **decodeURIComponent**          | **decodeURIComponent**函数（参考[18.2.6.3](https://tc39.github.io/ecma262/#sec-decodeuricomponent-encodeduricomponent)） |
| %encodeURI%                      | **encodeURI**                   | **encodeURI**函数（参考[18.2.6.4](https://tc39.github.io/ecma262/#sec-encodeuri-uri)） |
| %encodeURIComponent%             | **encodeURIComponent**          | **encodeURIComponent**函数（参考[18.2.6.5](https://tc39.github.io/ecma262/#sec-encodeuricomponent-uricomponent)） |
| %Error%                          | **Error**                       | **Error**构造函数（参考[19.5.1](https://tc39.github.io/ecma262/#sec-error-constructor)） |
| %ErrorPrototype%                 | **Error.prototype**             | %DataView%的**prototype**数据属性的初始值                    |
| %eval%                           | **eval**                        | **eval**函数（参考[18.2.1](https://tc39.github.io/ecma262/#sec-eval-x)） |
| %EvalError%                      | **EvalError**                   | **EvalError**构造函数（参考[19.5.5.1](https://tc39.github.io/ecma262/#sec-native-error-types-used-in-this-standard-evalerror)） |
| %EvalErrorPrototype%             | **EvalError.prototype**         | %EvalError%的**prototype**数据属性的初始值                   |
| %Float32Array%                   | **Float32Array**                | **Float32Array**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Float32ArrayPrototype%          | **Float32Array.prototype**      | %Float32Array%的**prototype**数据属性的初始值                |
| %Float64Array%                   | **Float64Array**                | **Float64Array**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Float64ArrayPrototype%          | **Float64Array.prototype**      | %Float64Array%的**prototype**数据属性的初始值                |
| %Function%                       | **Function**                    | **Function**构造函数（参考[19.2.1](https://tc39.github.io/ecma262/#sec-function-constructor)） |
| %FunctionPrototype%              | **Function.prototype**          | %Function%的**prototype**数据属性的初始值                    |
| %Generator%                      |                                 | %Generator%的**prototype**数据属性的初始值                   |
| %GeneratorFunction%              |                                 | generator对象的构造函数（参考[25.2.1](https://tc39.github.io/ecma262/#sec-generatorfunction-constructor)） |
| %GeneratorPrototype%             |                                 | %Generator%的**prototype**数据属性的初始值                   |
| %Int8Array%                      | **Int8Array**                   | **Int8Array**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Int8ArrayPrototype%             | **Int8Array.prototype**         | %Int8Array%的**prototype**数据属性的初始值                   |
| %Int16Array%                     | **Int16Array**                  | **Int16Array**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Int16ArrayPrototype%            | **Int16Array.prototype**        | %Int16Array%的**prototype**数据属性的初始值                  |
| %Int32Array%                     | **Int32Array**                  | **Int32Array**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Int32ArrayPrototype%            | **Int32Array.prototype**        | %Int32Array%的**prototype**数据属性的初始值                  |
| %isFinite%                       | **isFinite**                    | **isFinite**函数（参考[18.2.2](https://tc39.github.io/ecma262/#sec-isfinite-number)） |
| %isNaN%                          | **isNaN**                       | **isNaN**函数（参考[18.2.3](https://tc39.github.io/ecma262/#sec-isnan-number)） |
| %IteratorPrototype%              |                                 | 一个对象，所有的内置对象的迭代器对象间接地都从这个对象继承而来。 |
| %JSON%                           | **JSON**                        | **JSON**对象（参考[24.5](https://tc39.github.io/ecma262/#sec-json-object)） |
| %JSONParse%                      | **JSON.parse**                  | %JSON%的**parse**数据属性的初始值                            |
| %JSONStringify%                  | **JSON.stringify**              | %JSON%的**stringify**数据属性的初始值                        |
| %Map%                            | **Map**                         | **Map**构造函数（参考[23.1.1](https://tc39.github.io/ecma262/#sec-map-constructor)） |
| %MapIteratorPrototype%           |                                 | Map迭代器对象的原型（参考[23.1.5](https://tc39.github.io/ecma262/#sec-map-iterator-objects)） |
| %MapPrototype%                   | **Map.prototype**               | %Map%的**prototype**数据属性的初始值                         |
| %Math%                           | **Math**                        | **JSON**对象（参考[20.2](https://tc39.github.io/ecma262/#sec-math-object)） |
| %Number%                         | **Number**                      | **Number**构造函数（参考[20.1.1](https://tc39.github.io/ecma262/#sec-number-constructor)） |
| %NumberPrototype%                | **Number.prototype**            | %Number%的**prototype**数据属性的初始值                      |
| %Object%                         | **Object**                      | **Object**构造函数（参考[19.1.1](https://tc39.github.io/ecma262/#sec-object-constructor)） |
| %ObjectPrototype%                | **Object.prototype**            | %Object%的**prototype**数据属性的初始值（参考[19.1.3](https://tc39.github.io/ecma262/#sec-properties-of-the-object-prototype-object)） |
| %ObjProto_toString%              | **Object.prototype.toString**   | %Object%的**toString**数据属性的初始值（参考[19.1.3.6](https://tc39.github.io/ecma262/#sec-object.prototype.tostring)） |
| %ObjProto_valueOf%               | **Object.prototype.valueOf**    | %Object%的**valueOf**数据属性的初始值（参考[19.1.3.7](https://tc39.github.io/ecma262/#sec-object.prototype.valueof)） |
| %parseFloat%                     | **parseFloat**                  | **parseFloat**函数（参考18.2.4）                             |
| %parseInt%                       | **parseInt**                    | **parseInt**函数（参考[18.2.5](https://tc39.github.io/ecma262/#sec-parseint-string-radix)） |
| %Promise%                        | **Promise**                     | **Promise**构造函数（参考[25.4.3](https://tc39.github.io/ecma262/#sec-promise-constructor)） |
| %PromisePrototype%               | **Promise.prototype**           | %Promise%的**prototype**数据属性的初始值                     |
| %PromiseProto_then%              | **Promise.prototype.then**      | %PromisePrototype%的**then**数据属性的初始值                 |
| %Promise_all%                    | **Promise.all**                 | %Promise%的**all**数据属性的初始值                           |
| %Promise_reject%                 | **Promise.reject**              | %Promise%的**reject**数据属性的初始值                        |
| %Promise_resolve%                | **Promise.resolve**             | %Promise%的**resolve**数据属性的初始值                       |
| %Proxy%                          | **Proxy**                       | **Proxy**构造函数（参考[26.2.1](https://tc39.github.io/ecma262/#sec-proxy-constructor)） |
| %RangeError%                     | **RangeError**                  | **RangeError**构造函数（参考[19.5.2.2](https://tc39.github.io/ecma262/#sec-native-error-types-used-in-this-standard-rangeerror)） |
| %RangeErrorPrototype%            | **RangeError.prototype**        | %RangeError%的**prototype**数据属性的初始值                  |
| %ReferenceError%                 | **ReferenceError**              | **ReferenceError**构造函数（参考[19.5.5.3](https://tc39.github.io/ecma262/#sec-native-error-types-used-in-this-standard-referenceerror)） |
| %ReferenceErrorPrototype%        | **ReferenceError.prototype**    | %ReferenceError%的**prototype**数据属性的初始值              |
| %Reflect%                        | **Reflect**                     | **Reflect**对象（参考[26.1](https://tc39.github.io/ecma262/#sec-reflect-object)） |
| %RegExp%                         | **RegExp**                      | **RegExp**构造函数（参考[21.2.3](https://tc39.github.io/ecma262/#sec-regexp-constructor)） |
| %RegExpPrototype%                | **RegExp.prototype**            | %RegExp%的**prototype**数据属性的初始值                      |
| %Set%                            | **Set**                         | **Set**构造函数（参考[23.2.1](https://tc39.github.io/ecma262/#sec-set-constructor)） |
| %SetIteratorPrototype%           |                                 | Set迭代器对象的prototype（参考[23.2.5](https://tc39.github.io/ecma262/#sec-set-iterator-objects)） |
| %SetPrototype%                   | **Set.prototype**               | %Set%的**prototype**数据属性的初始值                         |
| %SharedArrayBuffer%              | **SharedArrayBuffer**           | **SharedArrayBuffer**构造函数（参考[24.2.2](https://tc39.github.io/ecma262/#sec-sharedarraybuffer-constructor)） |
| %SharedArrayBufferPrototype%     | **SharedArrayBuffer.prototype** | %SharedArrayBuffer%的**prototype**数据属性的初始值           |
| %String%                         | **String**                      | **String**构造函数（参考[21.1.1](https://tc39.github.io/ecma262/#sec-string-constructor)） |
| %StringIteratorPrototype%        |                                 | String迭代器对象的prototype（参考[21.1.5](https://tc39.github.io/ecma262/#sec-string-iterator-objects)） |
| %StringPrototype%                | **String.prototype**            | %String%的**prototype**数据属性的初始值                      |
| %Symbol%                         | **Symbol**                      | **Symbol**构造函数（参考[19.4.1](https://tc39.github.io/ecma262/#sec-symbol-constructor)） |
| %SymbolPrototype%                | **Symbol.prototype**            | %Symbol%的**prototype**数据属性的初始值（参考[19.4.3](https://tc39.github.io/ecma262/#sec-properties-of-the-symbol-prototype-object)） |
| %SyntaxError%                    | **SyntaxError**                 | **SyntaxError**构造函数（参考[19.5.5.4](https://tc39.github.io/ecma262/#sec-native-error-types-used-in-this-standard-syntaxerror)） |
| %SyntaxErrorPrototype%           | **SyntaxError.prototype**       | %SyntaxError%的**prototype**数据属性的初始值                 |
| %ThrowTypeError%                 |                                 | 一个函数对象，无条件的抛出一个%TypeError%的实例              |
| %TypedArray%                     |                                 | 所有的typed Array构造函数的父类（参考[22.2.1](https://tc39.github.io/ecma262/#sec-%typedarray%-intrinsic-object)） |
| %TypedArrayPrototype%            |                                 | %TypedArray%的**prototype**数据属性的初始值                  |
| %TypeError%                      | **TypeError**                   | **TypeError**构造函数（参考[19.5.5.5](https://tc39.github.io/ecma262/#sec-native-error-types-used-in-this-standard-typeerror)） |
| %TypeErrorPrototype%             | **TypeError.prototype**         | %TypeError%的**prototype**数据属性的初始值                   |
| %Uint8Array%                     | **Uint8Array**                  | **Uint8Array**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Uint8ArrayPrototype%            | **Uint8Array.prototype**        | %Uint8Array%的**prototype**数据属性的初始值                  |
| %Uint8ClampedArray%              | **Uint8ClampedArray**           | **Uint8ClampedArray**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Uint8ClampedArrayPrototype%     | **Uint8ClampedArray.prototype** | %Uint8ClampedArray%的**prototype**数据属性的初始值           |
| %Uint16Array%                    | **Uint16Array**                 | **Uint16Array**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Uint16ArrayPrototype%           | **Uint16Array.prototype**       | %Uint16Array%的**prototype**数据属性的初始值                 |
| %Uint32Array%                    | **Uint32Array**                 | **Uint32Array**构造函数（参考[22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects)） |
| %Uint32ArrayPrototype%           | **Uint32Array.prototype**       | %Uint32Array%的**prototype**数据属性的初始值                 |
| %URIError%                       | **URIError**                    | **URIError**构造函数（参考[19.5.5.6](https://tc39.github.io/ecma262/#sec-native-error-types-used-in-this-standard-urierror)） |
| %URIErrorPrototype%              | **URIError.prototype**          | %URIError%的**prototype**数据属性的初始值                    |
| %WeakMap%                        | **WeakMap**                     | **WeakMap**构造函数（参考[23.3.1](https://tc39.github.io/ecma262/#sec-weakmap-constructor)） |
| %WeakMapPrototype%               | **WeakMap.prototype**           | %WeakMap%的**prototype**数据属性的初始值                     |
| %WeakSet%                        | **WeakSet**                     | **WeakSet**构造函数（参考[23.4.1](https://tc39.github.io/ecma262/#sec-weakset-constructor)） |
| %WeakSetPrototype%               | **WeakSet.prototype**           | %WeakSet%的**prototype**数据属性的初始值                     |

## 6.2 ECMAScript规范类型 <div id="sec-specification-types"></div>

规范类型对应于算法中用于描述ECMAScript语言构造和ECMAScript语言类型的语义的元值。规范类型包括，[Reference](https://tc39.github.io/ecma262/#sec-reference-specification-type), [List](https://tc39.github.io/ecma262/#sec-list-and-record-specification-type), [Completion](https://tc39.github.io/ecma262/#sec-completion-record-specification-type), [Property Descriptor](https://tc39.github.io/ecma262/#sec-property-descriptor-specification-type), [Lexical Environment](https://tc39.github.io/ecma262/#sec-lexical-environments), [Environment Record](https://tc39.github.io/ecma262/#sec-environment-records)以及[Data Block](https://tc39.github.io/ecma262/#sec-data-blocks)。规范类型的值是规范里才有的，在ECMAScript实现中没有必要与任何具体的实体相对应。规范类型的值也许被用来描述ECMAScript表达式解析后的中间结果，但是这样的值不能被存储作为ECMAScript语言变量的对象属性或值的一部分。 

### 6.2.1 List和Record规范类型 <div id="sec-list-and-record"></div>

List类型被用来解释在`new`表达式中对于argument lists（参考[12.3.6](https://tc39.github.io/ecma262/#sec-argument-lists)）的解析，以及在函数调用中，或者在其它需要一个简单的有序的值的list的算法的地方。List类型的值是简单的包含各自list元素的有序序列。这些序列可能是任意的长度。一个list中的元素可以通过0到某个区间内的索引来随机访问。为了记数方便，一个类数组语法可以被用来访问List的元素。例如，`arguments`[2]是arguments List中第三个元素的简写。 

为了本规范的记法方便，可以用一个字面量语法来表示一个新的List值。例如，« 1, 2 »定义了一个有2个元素的List值，每个元素都被实例化为了一个具体的值。一个新的空的List可以用« »表示。

Record类型被用来描述在本规范算法内的数据的聚合。一个Record类型的值包含一个或者多个命名的字段。每个字段的值要么是一个ECMAScript值，要么是一个用一个关联Record类型的名称表示的抽象值。字段名总是双方括号闭合的，例如[[Value]]。

为了本规范的方便，一个类似对象字面量的语法被用来表示一个Record值。例如，{[[Field1]]: 42, [[Field2]]: false, [[Field3]]: empty}定义了一个包含3个字段的Record值，其中的每一个都被实例化为了一个具体的值。字段名的顺序是不重要的。任何没有被显式地列出的字段都被认为是不存在的。

在规范的文本和算法中，点符号也许会被用来指向一个具体的一个Record值的某个特定字段。例如，如果R是一个在之前的段落中出现的record，那么R.[[Field2]]代表“R中名称叫[[Field2]]的字段”的简写。 

可以命名常用记录字段组合的模式，并且该名称可以用作文字记录值的前缀，以标识所描述的特定聚合类型。例如： PropertyDescriptor {[[Value]]: 42, [[Writable]]: `false`, [[Configurable]]: `true`}。 

### 6.2.2 Set和关系规范类型 <div id="sec-set-and-relation"></div>

Set类型被用来解释在内存模型中无序元素的集合。Set类型的值是简单的元素的集合，其中没有任何元素出现一次以上。 元素可以添加到集合中或从集合中删除。集可以有交集，并集和补集。 

关系类型用于解释对集合的约束。关系类型的值是其值域中的有序值对的集合。例如，事件关联是一组有序的事件对。对于一个关系`R`以及在`R`的值域中的两个值`a`和`b`，`a R b`是有序对`(a, b)`是`R`的成员的简写。 满足这些条件的最小关系。

严格的偏序关系，即是满足以下条件的关系值R。

​	对于在R的域中所有a,b,c：

​		1、不存在 a R a，

​		2、如果 a R b，并且 b R c，所以 a R c

> 注1：以上两个特性依次称为反自反性和传递性。

严格的全序关系，即是满足以下条件的关系值R。

​	对于在R的域中所有a,b,c：

​		1、a等于b或 a R b 或 b R a

​		2、不存在 a R a

​		3、如果 a R b，并且 b R c，所以 a R c

> 注2：以上三个特性依次称为总体性，反自反性和传递性。

### 6.2.3 完成记录规范类型 <div id="sec-completion-record"></div>

完成类型是一个记录，用于解释某些执行非外部控制转移语句（例如break, continue, return, throw）运行时值和控制流的传播。

完成类型的值是记录值，它们的字段定义在表8中。这样的值被称为完成记录。

| 域名       | 值                                                           | 含义                          |
| :--------- | :----------------------------------------------------------- | :---------------------------- |
| [[Type]]   | normal, break, continue, return, throw其中的一个             | 完成值的类型                  |
| [[Value]]  | 一个[ECMAScript语言值](https://tc39.github.io/ecma262/#sec-ecmascript-language-types)或者空值 | 产生的值                      |
| [[Target]] | 任意ECMAScript字符串或者空值                                 | 指定的控制转移的目标标签/元素 |

#### 6.2.3.1 Await <div id="sec-await"></div>

 算法步骤如下

​	1、把 Await(value)设为completion

意思是：

	1.  令 asyncContext 成为运行时执行上下文
 	1.  令 promise 成为 ?  PromiseResolve(%Promise%, « value »).  
 	2.  令 stepsFulfilled 成为 Await Fulfilled Functions 中定义的算法步骤
 	3.   令 onFulfilled 成为 CreateBuiltinFunction(stepsFulfilled, « [[AsyncContext]] »). 
 	4.  设置 onFulfilled。设置 asyncContext 的 [[AsyncContext]] 。
 	5.  令 stepRejected 成为  Await Rejected Functions 中定义的算法步骤。
 	6.  令 onRejected 成为 CreateBuiltinFunction(stepsRejected, « [[AsyncContext]] ») 
 	7.  设置  onRejected。设置 asyncContext 的 [[AsyncContext]] 。
 	8.  执行  ! PerformPromiseThen(promise, onFulfilled, onRejected). 
 	9.   从执行上下文堆栈中删除 asyncContext，并将位于执行上下文堆栈顶部的执行上下文恢复为正在运行的执行上下文。
 	10.  设置asyncContext的代码求值状态，以使求值在完成完成后恢复时，将执行调用Await的算法的以下步骤，并提供完成功能。
 	11.  返回。
 	12.  注意：这将返回到对操作进行评估的操作，该操作之前已恢复对asyncContext的求值。

其中上述步骤中除完成之外的所有变量都是短暂的，并且仅在与Await有关的步骤中可见。

> 注意
>
> Await可以与 ? 和 ! 前缀组合起来，例如

1. 令 result 成为 ? Await(value). 

意思是：

1. 令 result 成为 Await(value). 
2. ReturnIfAbrupt(result).  

##### 6.2.3.1.1 Await Fulfilled 函数 <div id="sec-await-fulfilled-function"></div>

Await Fulfilled 是一个匿名内置函数，用作await规范的一部分，以将promise完成值作为正常完成传递给调用方。每个Await Fulfilled函数都有一个[[AsyncContext]]内部插槽。

当使用参数值调用Await Fulfilled 的函数时，将执行以下步骤：

	1. 令 F 为活动函数对象。 
 	2.  令 asyncContext 成为 F.[[AsyncContext]]. 
 	3.  令 prevContext 成为运行时执行上下文.
 	4.  挂起 prevContext.  
 	5.  将asyncContext推送到执行上下文堆栈；现在asyncContext是正在运行的执行上下文。
 	6.  使用 NormalCompletion(value) 作为暂停 asyncContext 的操作的结果，恢复其暂停的求值。
 	7.  断言：到达此步骤时，asyncContext已经从执行上下文堆栈中删除，并且prevContext是当前正在运行的执行上下文。
 	8. 返回 undefined

Await fulfilled 函数的“长度”属性为1。

#####  6.2.3.1.2 Await Rejected 函数 <div id="sec-await-rejected-functions"></div>

Await Rejected 函数是一个匿名内置函数，用作 Await 规范设备的一部分，以将Promise拒绝原因作为突然抛出完成传递给调用方。每个Await Rejected 的函数都有一个[[AsyncContext]]内部插槽。

当使用参数reason调用Await Rejected 函数时，将执行以下步骤：

1. 令 F 为活动函数对象。 
 2.  令 asyncContext 成为 F.[[AsyncContext]]. 
 3.  令 prevContext 成为运行时执行上下文.
 4.  挂起 prevContext.  
 5.  将asyncContext推送到执行上下文堆栈；现在asyncContext是正在运行的执行上下文。
 6.  使用 ThrowCompletion(reason) 作为暂停 asyncContext 的操作的结果，恢复其暂停的求值。
 7.  断言：到达此步骤时，asyncContext已经从执行上下文堆栈中删除，并且prevContext是当前正在运行的执行上下文。
 8. 返回 undefined

Await Rejected 函数的“长度”属性为1。

####  6.2.3.2 NormalCompletion <div id="sec-normalcompletion"></div>

抽象操作NormalCompletion具有单个参数，例如：

1. 返回  NormalCompletion(argument) 

是一种缩写，定义如下：

1. Return Completion { [[Type]]: normal, [[Value]]: argument, [[Target]]: empty } 

####  6.2.3.3 ThrowCompletion <div id="sec-throwcompletion"></div>

抽象操作ThrowCompletion具有单个参数，例如：

1. 返回  ThrowCompletion(argument) 

是一种缩写，定义如下：

1. Return Completion { [[Type]]: throw, [[Value]]: argument, [[Target]]: empty } 

####  6.2.3.4 UpdateEmpty ( completionRecord, value ) <div id="sec-updateempty"></div>

抽象操作UpdateEmpty（带有参数completeRecord和value）执行以下步骤：

1.  断言: 如果 completionRecord.[[Type]] 既不是 return 也不是 throw, 同时 completionRecord.[[Value]] 不是 空.  

2. 如果 completionRecord.[[Value]] 不为空, 返回 Completion(completionRecord).  

3. 返回 Completion { [[Type]]: completionRecord.[[Type]], [[Value]]: value, [[Target]]: completionRecord. [[Target]] }.  

### 6.2.4 引用规范类型 <div id="sec-reference-specification-type"></div>

> 注意：引用类型被用来解释像`delete`，`typeof`，赋值等操作符以及`super`关键字，以及其它语言特性的行为。例如，赋值操作左值应该是一个引用。

一个引用是一个解析后的名称或者属性绑定。一个引用包含三个组成部分，即基值，引用名称，以及严格引用标志（布尔值）。基础值的组件是`undefined`，Object，Boolean，String，Symbol，Number，或者一个环境记录。 **基**值是 **undefined** 表示此引用不可以解析为一个绑定。**引用名称**是一个字符串或Symbol值。 

一个super引用是一个被用来表示一个用`super`关键字表示的名称绑定的引用。一个super引用有额外的thisValue组件，它的基础值组件将永远不能是一个环境记录。

super引用是一种引用，用于表示使用super关键字表示的名称绑定。super引用具有附加的thisValue组件，其基础值组件永远不会是环境记录。

在本规范中，以下抽象操作用于对引用进行操作：

####  6.2.4.1 GetBase ( V ) <div id="sec-getbase"></div>

1. 断言: Type(V) 是一个引用。
2. 返回 V 的基值。

####  6.2.4.2 GetReferencedName ( V ) <div id="sec-getrefrencedname"></div>

1. 断言：Type(V) 是一个引用。

2. 返回 V 的引用名称。

####  6.2.4.3 IsStrictReference ( V ) <div id="sec-isstrictreference"></div>

1.  断言：Type(V) 是一个引用。 
2. 返回 V 的严格引用标志。

####  6.2.4.4 HasPrimitiveBase ( V ) <div id="sec-hasprimitivebase"></div>

1.  断言：Type(V) 是一个引用。 

2.  如果Type（V的基值）是 **Boolean**、**String**、**Symbol**或**Number**，那么返回 **true**；否则返回**false**。

####  6.2.4.5 IsPropertyReference ( V ) <div id="sec-ispropertyreference"></div>

1. 断言：Type(V) 是一个引用。 
2.  如果**基**值是个 **Object** 或 **HasPrimitiveBase**(V) 是 **true**，那么返回 **true**；否则返回 **false**。 

####  6.2.4.6 IsUnresolvableReference ( V ) <div id="sec-isunresolvablereference"></div>

1. 断言：Type(V) 是一个引用。

2. 如果基值是Undefined，那么返回true；否则返回false

####  6.2.4.7 IsSuperReference ( V ) <div id="sec-issuperreference"></div>

1.  断言：Type(V) 是一个引用。  

2. 如果 V 有 thisValue，则返回 true；否则返回false。

####  6.2.4.8 GetValue ( V ) <div id="sec-getvalue"></div>

1. ReturnIfAbrupt(V)。
2. 如果Type(V)不是引用，返回V。
3. 令base为GetBase(V)。
4. 如果IsUnresolveableReference(V)是true，抛出一个ReferenceError异常。
5. 如果 IsPropertyReference(V)是 **true**，那么
   1. 如果 HasPrimitiveBase(V) 是 **true**，那么
      1. 断言：在此情况下，base不可能是 **undefined** 或 **null**
      2. 设定 base 为 ! ToObject(base)
   2. 返回 ? base.[[Get\]\](GetReferencedName(V), GetThisValue(V)).  

6. 否则，base 必须是一个环境记录项，
   1. 返回 ? base.GetBindingValue(GetReferencedName(V), IsStrictReference(V)) (参见 8.1.1) 

> 注意
>
> 在上述抽象操作和普通对象[[Get]]内部方法之外，无法访问在步骤5.1.2中创建的对象。实现可能要选择避免实际创建对象。

####  6.2.4.9 PutValue ( V, W ) <div id="sec-putvalue"></div>

1. ReturnIfAbrupt(V)。
2. ReturnIfAbrupt(W)。
3. 如果Type(V)不是引用，抛出一个ReferenceError异常。
4. 令base为GetBase(V)。
5. 如果IsUnresolvableReference(V)是 **true**，那么
   1. 如果 IsStrictReference(V) 是 true，那么
      1.  抛出 **ReferenceError** 异常。 
   2. 令 globalObj 为 GetGlobalObject()。
   3. 返回 ? Set(globalObj, GetReferencedName(V), W, false)。

6. 否则如果 IsPropertyReference(V)，那么
   1. 如果 HasPrimitiveBase(V) 是 **false**，那么
      1. 在此情况下，base不可能是 **undefined** 或 **null**
      2. 设定 base 为 ! ToObject(base)
   2. 令 succeeded 为 ? base.[[Set]](GetReferencedName(V), W, GetThisValue(V))。
   3. 如果 successded 是 false，同时 IsStrictReference(V)  是 true，抛出 **TypeError** 异常。 
   4. 返回。

7. 否则，base必须是一个环境记录项，
   1. 返回 ? base.SetMutableBinding(GetReferencedName(V), W, IsStrictReference(V)) (参见8.1.1)。

> 注意
>
> 在上述抽象操作和普通对象[[Get]]内部方法之外，无法访问在步骤5.1.2中创建的对象。实现可能要选择避免实际创建对象。

####  6.2.4.10 GetThisValue ( V ) <div id="sec-getthisvalue"></div>

1. 断言: IsPropertyReference(V) 是 true。

2. 如果 IsSuperReference(V) 是true，则
   1. 返回引用 V 的 thisValue 值。

3. 返回 GetBase(V)。

####  6.2.4.11 InitializeReferencedBinding ( V, W ) <div id="sec-initializereferencebinding"></div>

1. ReturnIfAbrupt(V)。
2. ReturnIfAbrupt(W)。
3. 断言：Type(V)是引用。
4. 断言：IsUnresolvableReference(V) 是 false。 
5. 令 base 为 GetBase(V)。
6. 断言：base是一个环境记录项。
7. 返回 base.InitializeBinding(GetReferencedName(V), W)。

###  6.2.5 属性描述符规范类型 <div id="sec-property-descriptor"></div>

**属性描述符**类型是用来解释命名属性具体操作的特性集。属性描述符类型的值是记录项，由命名字段组成，每个字段的名称是一个特性名并且它的值是一个相应的特性值，这些特性指定在6.1.7.1。此外，任何字段都可能存在或不存在。

根据是否存在或使用了某些字段，属性描述符的值可进一步划分为**数据属性描述符**和**访问器属性描述符**。一个数据属性描述符里包括叫做 [[Value]] 或 [[Writable]]的字段。一个访问器属性描述符里包括叫做 [[Get]]或 [[Set]]的字段。任何属性描述都可能有名为 **[[Enumerable]]** 和 **[[Configurable]]** 的字段。一个属性描述符不能同时是数据属性描述符和访问器属性描述符；但是，它可能二者都不是。一个通用属性描述符是，既不是数据属性描述符也不是访问器属性描述符的属性描述符值。一个**完全填充**属性描述符是访问器属性描述符或数据属性描述符，并且拥有 表2 或 表2 里定义的所有属性特性对应的字段。

在本规范中，以下抽象操作用于对属性描述符值进行操作：

####  6.2.5.1 IsAccessorDescriptor ( Desc ) <div id="sec-isaccessordescriptor"></div>

使用属性描述符Desc调用抽象操作IsAccessorDescriptor时，将执行以下步骤：

1. 如果 Desc 是 undefined，返回 false。
2. 如果Desc.[[Get]] 和 Desc.[[Set]] 都不存在，则返回 false。

3. 返回 **true**。

####  6.2.5.2 IsDataDescriptor ( Desc ) <div id="sec-isdatadescriptor"></div>

使用属性描述符Desc调用抽象操作IsDataDescriptor时，将执行以下步骤：

1. 如果 Desc 是 undefined，那么返回 false。
2. 如果 Desc.[[Value]] 和 Desc.[[Writable]] 都不存在，则返回 false。
3. 返回 **true**。

####  6.2.5.3 IsGenericDescriptor ( Desc ) <div id="sec-isgenericdescriptor"></div>

当用属性描述符 Desc 调用抽象操作 IsGenericDescriptor，采用以下步骤：

1. 如果 Desc 是 **undefined**，那么返回 **false**。
2. 如果 IsAccessorDescriptor(Desc) 和 IsDataDescriptor(Desc) 都是 false，则返回 true。
3. 返回 **false**。

####  6.2.5.4 FromPropertyDescriptor ( Desc ) <div id="sec-frompropertydescriptor"></div>

当用属性描述符 Desc 调用抽象操作 FromPropertyDescriptor，采用以下步骤：

1. 如果 Desc 是 **undefined**，那么返回 **undefined**。
2. 令 obj 为 ObjectCreate(%ObjectPrototype%)。
3. 断言：obj是没有自己属性的可扩展普通对象。
4. 如果 Desc 有[[ Writable ]]字段，那么
   1. 执行 CreateDataProperty(obj, "value", Desc.[[Value]])。 
5. 如果 Desc 有[[ Writable ]]字段，那么
   1. 执行 CreateDataProperty(obj, " Writable ", Desc.[[Value]])。 
6. 如果 Desc 有[[ Get ]]字段，那么
   1. 执行 CreateDataProperty(obj, "Get", Desc.[[Value]])。 
7. 如果 Desc 有[[ Set ]]字段，那么
   1. 执行 CreateDataProperty(obj, " Set ", Desc.[[Value]])。 
8. 如果 Desc 有[[ Enumerable ]]字段，那么
   1. 执行 CreateDataProperty(obj, " Enumerable ", Desc.[[Value]])。 
9. 如果 Desc 有[[ Configurable ]]字段，那么
   1. 执行 CreateDataProperty(obj, " Configurable ", Desc.[[Value]])。 
10. 断言：以上所有 CreateDataProperty 操作返回 true
11. 返回 obj

####  6.2.5.5 ToPropertyDescriptor ( Obj ) <div id="sec-topropertydescriptor"></div>

1. 如果 Type(Obj) 不是对象, 抛出TypeError异常。
2. 令desc为一个新的属性描述符，该属性描述符最初没有字段。
3. 令hasEnumerable为 ? HasProperty(Obj, "enumerable")。
4. 如果hasEnumerable是true，那么
   1. 令enumerable为ToBoolean(? Get(Obj, "enumerable"))。
   2. 将 desc.[[Enumerable]] 设置为 enumerable。
5. 令 hasConfigurable 为 ? HasProperty(Obj, " configurable ")。
6. 如果 hasConfigurable 是true，那么
   1. 令 configurable 为ToBoolean(? Get(Obj, " configurable "))。
   2. 将 desc.[[ Configurable ]] 设置为  configurable . 
7. 令 hasValue 为 ? HasProperty(Obj, "value")。
8. 如果 hasValue 是true，那么
   1. 令 value 为 ? Get(Obj, "value")。
   2. 将 desc.[[ Value ]] 设置为 Value。
9. 令 hasWritable 为 ? HasProperty(Obj, " writable ")。
10. 如果 hasWritable 是true，那么
    1. 令 writable 为ToBoolean(? Get(Obj, " writable "))。
    2. 将 desc.[[ Writable ]] 设置为  writable 。
11. 令 hasGet 为 ? HasProperty(Obj, " get ")。
12. 如果 hasGet 是true，那么
    1. 令 getter 为 Get (? Get(Obj, " get "))。
    2. 如果 IsCallable(getter) 是 false 同时 getter 不是 undefined, 抛出 TypeError 异常.  
    3. 将 desc.[[ Get ]] 设置为  getter 。
13. 令 hasSet 为 ? HasProperty(Obj, " set ")。
14. 如果hasGet 是true，那么
    1. 令 setter 为 ? Get(Obj, "set")。
    2. 如果 IsCallable( setter ) 是 false 同时  setter  不是 undefined, 抛出 TypeError 异常.  
    3. 将 desc.[[ Set ]] 设置为  setter 。
15. 如果有 desc.[[Get]] 或者有 desc.[[Set]]，那么
    1.   如果有 desc.[[Value]] 或者有 desc.[[Writable]], 抛出 TypeError 异常. 
16. 返回 desc

####  6.2.5.6 CompletePropertyDescriptor ( Desc ) <div id="sec-completepropertydescriptor"></div>

当用属性描述符 Desc 调用抽象操作 CompletePropertyDescriptor，采用以下步骤：

1. 断言：Desc 是属性描述符。
2. 令 like 为 Record { [[Value]]: undefined, [[Writable]]: false, [[Get]]: undefined, [[Set]]: undefined, [[Enumerable]]: false, [[Configurable]]: false }.  

3. 如果 IsGenericDescriptor(Desc) 为 true 或者 IsDataDescriptor(Desc) 为 true，那么
   1. 如果 Desc 没有 [[Value]] 字段, 将 Desc.[[Value]] 设置为 like.[[Value]]。
   2. 如果 Desc 没有 [[Writable]] 字段, 将 Desc.[[Writable]] 设置为 like.[[Writable]]。

4. 否则。
   1.   如果 Desc 没有 [[ Get ]] 字段, 将 Desc.[[ Get ]] 设置为 like.[[ Get ]]。
   2.   如果 Desc 没有 [[ Set ]] 字段, 将 Desc.[[ Set ]] 设置为 like.[[ Set ]]。

5.  如果 Desc 没有 [[ Enumerable ]] 字段, 将 Desc.[[ Enumerable ]] 设置为 like.[[ Enumerable ]]。 
6.  如果 Desc 没有 [[ Configurable ]] 字段, 将 Desc.[[ Configurable ]] 设置为 like.[[ Configurable ]]。 
7. 返回 Desc

### 6.2.6  词法环境和环境记录项规范类型 <div id="sec-lexical-environment-and-environment-record"></div>

词法环境和环境记录项类型用于说明在嵌套的函数或块中的名称解析行为。这些类型和他们的操作定义在第 8.1 章

### 6.2.7 数据块 <div id="sec-data-block"></div>

数据块规范类型用于描述一个独特的且不可变的基于字节（8bit）数字值序列。使用固定数量的字节创建数据块值，每个字节的初始值均为0。

为方便起见，在本规范中，类似数组的语法可用于访问数据块值的各个字节。该表示法将数据块值表示为起源于0的整数索引字节序列。例如，如果db是5字节的数据块值，则db[2]可用于访问其第3个字节。

可以同时从多个代理引用的驻留在内存中的数据块称为共享数据块。共享数据块的标识（为了进行相等性测试共享数据块值）是无地址的：它不绑定到该块在任何进程中映射到的虚拟地址，而是绑定到内存中的位置集代表该块。仅当两个数据块所包含的位置集合相等时，它们才相等。否则，它们不相等，并且它们包含的位置集的交集为空。最后，可以将共享数据块与数据块区分开。

内存模型使用共享数据块事件定义共享数据块的语义。下面的抽象操作介绍了共享数据块事件， 以及在内存模型上解析语义和事件语义的接口的行为 。事件形成候选执行，内存模型在该候选执行上充当过滤器。请查阅内存模型以获取完整的语义。

共享数据块事件由记录建模，该记录在内存模型中定义。

在本规范中，以下抽象操作用于对数据块值进行操作：

####  6.2.7.1 CreateByteDataBlock ( size ) <div id="sec-createbytedatablock"></div>

当用整数size参数调用抽象操作  CreateByteDataBlock ，采用以下步骤：

1. 断言：size ≥ 0。
2. 令db为一个由 size 字节组成的新数据块值。如果不可能创建这样的数据块，抛出 RangeError 异常。

3. 将所有db 的字节大小设为0。
4. 返回 db。

####  6.2.7.2 CreateSharedByteDataBlock ( size ) <div id="sec-createsharedbytedatablock"></div>

当用整数size参数调用抽象操作  CreateSharedByteDataBlock  ，采用以下步骤：

1. 断言：size ≥ 0。
2. 令db为一个由 size 字节组成的新共享数据块值。如果不可能创建这样的共享数据块，抛出 RangeError 异常。

3. 令 execution 成为当前代理记录项的 [[CandidateExecution]]  字段
4. 令 eventList 成为 execution 中元素的  [[EventList]]  字段。 [[AgentSignifier]]是AgentSignifier()的[[EventsRecords]]。 
5.  令 zero 为 « 0 »。
6. 对于db的每个索引i，执行
   1. 添加 WriteSharedMemory { [[Order]]: "Init", [[NoTear]]: true, [[Block]]: db, [[ByteIndex]]: i, [[ElementSize]]: 1, [[Payload]]: zero } 到  eventList 
7. 返回 db。

####  6.2.7.3 CopyDataBlockBytes ( toBlock, toIndex, fromBlock, fromIndex, count ) <div id="sec-copydatablockbytes"></div>

当调用抽象操作  CreateSharedByteDataBlock  ，采用以下步骤：

1. 断言：fromBlock和toBlock是不同的数据块或共享数据块值。
2. 断言：fromIndex，toIndex和count是≥0的整数
3. 令fromSize为fromBlock中的字节数。
4. 断言：fromIndex + count ≤ fromSize。
5. 令 toSize 为 toBlock 中的字节数。
6. 断言： toIndex + count ≤ toSize。
7. 当 count > 0 重复
   1. 如果 fromBlock 是共享数据块，那么
      1. 令 execution 成为当前代理记录项的 [[CandidateExecution]]  字段。
      2. 令 eventList 成为 execution 中元素的  [[EventList]]  字段。 [[AgentSignifier]]是AgentSignifier()的[[EventsRecords]]。
      3. 令 bytes 为长度为1的 List，包含不确定地选择的字节值。
      4. 注意：在实现中，bytes 是基础硬件上非原子读取指令的结果。非确定性是内存模型的语义规定，用于描述具有弱一致性的硬件的可观察行为。
      5. 令 readEvent 为 ReadSharedMemory { [[Order]]: "Unordered", [[NoTear]]: true, [[Block]]: fromBlock, [[ByteIndex]]: fromIndex, [[ElementSize]]: 1 }。
      6. 添加 readEvent 到 eventList。
      7. 添加 Chosen Value Record { [[Event]]: readEvent, [[ChosenValue]]: bytes } 到 execution。
      8. 如果 toBlock 是共享数据块，那么
         1. 添加  WriteSharedMemory { [[Order]]: "Unordered", [[NoTear]]: true, [[Block]]: toBlock, [[ByteIndex]]: toIndex, [[ElementSize]]: 1, [[Payload]]: bytes } 到  eventList。
      9. 或者
         1. 将  toBlock[toIndex]  设置为  bytes[0] 
   2. 否则，
      1. 断言： toBlock 不是共享数据块。
      2. 将toBlock [toIndex]设置为fromBlock [fromIndex]。
   3.  将toIndex和fromIndex分别增加1。
   4. count递减1.

8. 返回 NormalCompletion(empty)。