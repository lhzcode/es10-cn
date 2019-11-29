```
description ECMAScript是一种在宿主环境中执行计算并处理计算对象的面向对象编程语言。这里的ECMAScript 并不是计算性自完备的；事实上，本规范并没有规定外部数据输入和计算结果输出。相反，我们期望ECMAScript程序的计算环境不仅提供本规范中描述的对象和其他设施，还能提供某些特定环境下的宿主对象；除非为了说明宿主对象可能提供某些属性和方法以供ECMAScript程序访问，这些宿主对象描述和行为都超出了本规范的范围。
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,ES概述,overview
```

# 4 概述

本节包含对ECMAScript语言非规范性的概述。

ECMAScript是一种在宿主环境中执行计算并处理计算对象的面向对象编程语言。这里的ECMAScript 并不是计算性自完备的；事实上，本规范并没有规定外部数据输入和计算结果输出。相反，我们期望ECMAScript程序的计算环境不仅提供本规范中描述的对象和其他设施，还能提供某些特定环境下的宿主对象；除非为了说明宿主对象可能提供某些属性和方法以供ECMAScript程序访问，这些宿主对象描述和行为都超出了本规范的范围。 

ECMAScript最初被设计用来作为一门脚本语言，但是现在已经被广泛地作为一门通用语言使用。 **脚本语言**是一种用于操作、自定义、自动化现有系统设施的编程语言。在这种系统中，用户已经可以通过一个用户界面使用可用功能，脚本语言正是暴露这些有用功能给程序控制的一种机制。这样，现有系统可以说是提供了对象和设施的一种宿主环境，以完善脚本语言能力。因此，设计脚本语言时，就考虑到了可被专业和非专业程序员都能使用。 

ECMAScript最初被设计为**Web脚本语言**，提供一种机制，使浏览器中的网页更加活跃，使基于Web的客户端/服务器架构能够执行服务器计算。ECMAScript 可以为各种宿主环境提供核心的脚本功能，因此本文档为不依赖特定宿主环境的核心脚本语言作出规范。 

ECMAScript的用法已经远远超过了普通的脚本语言，现在它被用作许多不同环境下的编程任务的各个方面。随着ECMAScript用法的扩充，它也拥有越来越多的特性。现在，ECMAScript已经是一门功能完备的通用语言。

ECMAScript的一些机能和其他编程语言的类似；特别是下列文献所描述的Java™、Self和Scheme：

ISO/IEC 9899:1996, Programming Languages – C.

Gosling, James, Bill Joy and Guy Steele. The Java™ Language Specification. Addison Wesley Publishing Co., 1996.

Ungar, David, and Smith, Randall B. Self: The Power of Simplicity. OOPSLA '87 Conference Proceedings, pp. 227-241, Orlando, FL, October 1987.

IEEE Standard for the Scheme Programming Language. IEEE Std 1178-1990. 

## 4.1 Web 脚本 <div id="sec-web-scripting"></div>

WEB 浏览器为了增加客户端的计算能力而引入 ECMAScript 宿主环境，例如，它提供的对象有：windows，menus，pop-ups，dialog boxes，text areas，anchors，frames，history，cookies 及输入 / 输出等等。进一步来说，WEB 浏览器中提供的这种宿主环境可以让脚本代码处理诸如改变焦点、页面和图片的加载、卸载、错误和放弃，选择，表单提交和鼠标交互等等事件。脚本代码出现在 HTML 中，显示出来的页面则是一个由用户接口元素、已确定计算出来的文本以及图片的集合。脚本代码根据用户的交互而做出反应，并不需要存在一个主程序。 

WEB 服务器为了服务端的计算提供了完全不一样的宿主环境，包括的对象有：requrests，clients，files 以及数据锁定和分享机制。通过浏览器端脚本及服务端脚本的配合使用，在为基于 WEB 方式的应用程序提供可定制的用户接口的同时时，也将计算分布到客户端和服务端进行。 

每一种支持 ECMAScript 的 WEB 浏览器和服务器都将它们自身的宿主环境作为 ECMAScript 的补充，以使得 ECMAScript 的执行环境变得完整。 

## 4.2 ECMAScript 概览 <div id="sec-ecmascript-overview"></div>

下面是非正式的 ECMAScript 概述 —— 并未描述语言的所有部分。此概述并非标准的一部分。 

ECMAScript 是基于对象的：基本语言和宿主设施都由对象提供，ECMAScript 程序是一组可通信的对象。ECMAScript **对象**是**属性**的集合，每个属性有零个或多个特性，以及属性的使用方式。例如，当设置一个属性的Writable特性为 **false** 时，任何试图更改此属性值的 ECMAScript 代码的都会执行失败。属性是一个容器，它可以存放其他 **对象**、**原始值**、**函数**。原始值是以下内置类型之一的成员：Undefined、Null、Boolean、Number、String；对象是剩下的内置类型Object的成员；函数是**可调用的对象**。**方法**是通过属性与对象关联的函数。 

ECMAScript 定义了一组**内置对象**，完善了 ECMAScript 实体的定义。这些内置对象包括全局对象；语言运行时语义基础的对象，包括 Object, Function, Boolean, Symbol和各种Error对象；代表和操纵数值的对象，包括Math, Number和Date；文本处理对象String和RegExp；索引集合值的对象，包括数组和九种不同类型的类型化数组，它们的元素都具有特定的数值数据表示形式；键控集合，包括Map和Set对象； 支持结构化数据的对象，包括JSON对象，ArrayBuffer，SharedArrayBuffer和DataView；支持控件抽象的对象，包括生成器函数和Promise对象； 反射对象，包括Proxy和Reflect。

ECMAScript 中还定义一组内置运算符。ECMAScript 运算符包括 一元运算符、乘法运算符、加法运算符、位移运算符、关系运算符、等值运算符、二元按位运算符、二元逻辑运算符、赋值运算符、逗号运算符。

大型ECMAScript程序需要模块支持，这些模块允许将程序分为多个语句和声明的序列。 每个模块显式地识别它用到的由其它模块提供的声明，同时它自己内部的声明也能被其他模块所用。

ECMAScript 语法有意设计成与 Java 语法类似。ECMAScript 的语法是松散的，使其能够作为一个易于使用的脚本语言。例如，一个变量不需要有类型声明，属性也不需要与类型关联，定义的函数也不需要声明在函数调用语句的前面。

### 4.2.1 Objects <div id="sec-objects"></div>

ECMAScript 不使用诸如 C++、Smalltalk、Java 中的类。相反，对象可以通过各种方式创建，包括字面量符号、**构造器** 创建对象然后运行代码初始化其全部或部分属性值，并为这些属性分配初始值。每个构造器是一个拥有名为“**prototype**”属性的函数。此属性用于实现 **原型继承** 和 **属性共享**。构造器通过 **new** 表达式创建对象：例如，**new Date(2009,11)** 创建一个新 **Date** 对象。不使用 **new** 调用一个构造器的结果，依赖构造器本身。例如，**Date()** 产生一个表示当前日期时间的字符串，而不是一个对象。 

每个由构造器创建的对象都有一个隐式引用（叫做对象的原型）链接到构造器的“**prototype**”属性值。再者，原型可能有一个非空隐式引用链接到它自己的原型，以此类推，这叫做 **原型链**。当一个引用被连接到对象的属性上时，引用会指向原型链中包含此属性名的第一个对象对应的属性。换句话说，首先检查对象直接的同名属性，如果对象包含同名的属性，引用即指向此属性，如果该对象不包含同名的属性，则下一步检查对象的原型，以此类推。 

![图1——对象与原型的关系图](http://www.ecma-international.org/ecma-262/10.0/img/figure-1.png)

通常在基于类的面向对象语言中，实例拥有状态，类拥有方法，并且只能继承结构和行为。在 ECMAScript 中，对象拥有状态和方法，并且结构，行为，状态全都可继承。

所有不直接包含原型中包含的特定属性的对象会共享此属性及属性值。**图1** 说明了这一点：

CF 是一个构造器（也是一个对象）。五个对象已用 **new** 表达式创建 : cf1、cf2、cf3、cf4、cf5。每个对象都有名为 q1 和 q2 的属性。虚线表示隐式原型关系；例如：cf3 的原型是 CFp。构造器 CF 自己有名为 P1 和 P2 的两个属性，这对 CFp、cf1、cf2、cf3、cf4、cf5 是不可见的。CFp 中名为 CFP1 的属性和任何在 CFp 的隐式原型链中能找到且不名为 q1、q2、CFP1 的属性都被 cf1、cf2、cf3、cf4、cf5 所共享（但不被 CF 共享）。请注意 CF 和 CFp 之间没有隐式原型链接。 

不同于基于类的对象语言，属性可以通过赋值的方式动态添加给对象。也就是说，构造器并不是非要对构造的对象的全部或任何属性命名或赋值。上图中，可以给 CFp 添加新属性值的方式为 cf1、cf2、cf3、cf4、cf5 添加一个新的共享属性。

尽管ECMAScript对象并不是天生就基于类的，但通常可以根据构造函数，原型对象和方法的通用模式来定义“模拟类”抽象。ECMAScript内置对象本身遵循这样的“模拟类”模式。从ECMAScript 2015开始，ECMAScript语言包含了类定义语法，允许程序员简洁的定义符合"模拟类"抽象模式的对象，内置对象也采取了这种方式来定义。

### 4.2.2 ECMAScript严格变体 <div id="sec-strict-variant-of-ecmascript"></div>

ECMAScript 语言认为存在部分希望限制语言中某些功能的用户。他们这样做可能是为了安全考虑，规避那些他们认为容易出错的功能，从而获得更强的错误检查，或者其他原因。为了支持这种可能的情况，ECMAScript 定义了语言的严格变体。语言的严格变体，排除了 ECMAScript 语言中某些特定的句法和语义特征，还修改了某些功能的详细语义。严格变体还指定了额外一些必须抛出错误异常报告的错误条件，而在非严格的语言模式下这些条件不属于错误。 

ECMAScript 的严格变体通常被称为语言的 **严格模式**。严格模式选择使用 ECMAScript 严格模式的语法和语义，明确地适用于个别 ECMAScript 代码单元级别。由于严格模式适用于选择的语法代码单元级别，严格模式只会在这个代码单元内施加带有局部效果的限制。严格模式不在语义层面限制或修改任何必须在多个代码单元运行的 ECMAScript 代码。一个 ECMAScript 程序可由严格模式和非严格模式的代码单元组成。在这种情况下，严格的模式只适用于严格模式代码单元内实际执行的代码。

为了符合本规范，ECMAScript的实现必须实现两套，非严格模式ECMAScript语言和ECMAScript语言严格模式变体，作为这份规范的定义。此外，实现必须支持非严格模式和严格模式代码单元组合成一个单独的复合程序。

### 4.3 术语定义 <div id="sec-terms-and-definitions"></div>

本文档将使用下列术语和定义。

### 4.3.1 类型 <div id="sec-type"></div>

本规范 [第6章]() 定义数据的集合。

### 4.3.2 原始值 <div id="sec-primitive-value"></div>

在本规范的 [第6章]() 定义的 **Undefined**、**Null**、**Boolean**、**Number**、**String** 类型之一的成员

> 注： 原始值可以直接表示语言实现的最底层数据。

### 4.3.3 对象 <div id="sec-terms-and-definitions-object"></div>

对象类型的成员。

> 注： 对象是属性的集合，并有一个原型对象。原型可以是空值。

### 4.3.4 构造器 <div id="sec-constructor"></div>

创建和初始化对象的函数对象。

> 注： 构造器的“**prototype**”属性值是一个原型对象，它用来实现继承和共享属性。

### 4.3.5 原型 <div id="sec-prototype"></div>

为其他对象提供共享属性的对象。

注： 当构造器创建一个对象时，为了解决对象的属性引用，该对象会隐式引用构造器的“**prototype**”属性。通过程序表达式 **constructor.prototype** 可以引用到构造器的“**prototype**”属性。并且，添加到对象原型里的属性会通过继承的方式与所有共享此原型的对象共享。另外，可使用 **Object.create** 内置函数，通过明确指定原型来创建一个新对象。

### 4.3.6 普通对象 <div id="sec-ordinary-object"></div>

具有所有对象支持的基本内部方法的对象

### 4.3.7 异类对象 <div id="sec-exotic-object"></div>

没有一个或多个基本内部默认方法的对象

> 注：任何对象不是普通对象就是异类对象

### 4.3.8 标准对象 <div id="sec-standard-object"></div>

其语义由本规范定义的对象

### 4.3.9 内置对象 <div id="sec-build-in-object"></div>

由ECMAScript的实现指定和提供的对象。

> 注：标准的内置对象被定义在本规范中。一个ECMAScript实现也许会指定和提供额外的某些内置对象。一个内置构造函数是一个内置对象，同时也是一个构造函数。

### 4.3.10 undfined 值 <div id="sec-undefined-value"></div>

说明一个变量没有被分配值的一个原始值。 

### 4.3.11 Undefined 类型 <div id="sec-undefined-type"></div>

**undefined** 值是 **Undefined** 类型绝无仅有的一个值。

### 4.3.12 null 值 <div id="sec-null-value"></div>

代表对象值故意留空的一个原始值。

### 4.3.13 Null 类型 <div id="sec-null-type"></div>

**null** 值是 **Null** 类型绝无仅有的一个值。

### 4.3.14 Boolean 值 <div id="sec-boolean-value"></div>

**Boolean** 类型的成员。

> 注： 只有两个 **Boolean** 值，**true** 和 **false** 。

### 4.3.15 Boolean 类型 <div id="sec-boolean-type"></div>

由原始值 **true** 和 **false** 组成的类型。

### 4.3.16 Boolean 对象 <div id="sec-boolean-object"></div>

Object 类型的成员，它是标准内置构造器 Boolean 的实例。

> 注： 通过使用 **new** 表达式，以一个 **Boolean** 值作为参数调用 **Boolean** 构造器来创建 **Boolean** 对象。由此产生的对象包含一个值为此 **Boolean** 值的内部属性。**Boolean** 对象可以强制转换为 **Boolean** 值。 

### 4.3.17 String 值 <div id="sec-string-value"></div>

原始值，它是零个或多个16位无符号整数组成的有限有序序列。

> 注： **String** 值是 **String** 类型的成员。通常序列中的每个整数值代表 UTF-16 文本的单个16位单元。然而，对于其值，ECMAScript 只要求必须是16位无符号整数，除此之外没有任何限制或要求。

### 4.3.18 String 类型 <div id="sec-string-type"></div>

所有可能的 **String** 值的集合。

### 4.3.19 String 对象 <div id="sec-string-object"></div>

Object类型的成员，它是标准的内置`String`构造函数的实例。 

> 注：通过使用 **new** 表达式，以一个 **String** 值作为参数调用 **String** 构造器来创建 **String** 对象。由此产生的对象包含一个值为此 **String** 值的内部属性。**String** 对象可以强制转换为 **String** 值（21.1.1.1）。 

### 4.3.20 Number 值 <div id="sec-number-value"></div>

原始值，对应一个64位双精度二进制 IEEE 754-2008 值。

> 注： **Number** 值是 **Number** 类型的成员，是一个数字的明确表示法。

### 4.3.21 Number 类型 <div id="sec-number-type"></div>

所有可能的数字值的集合，包括特殊的“Not-a-Number”(**NaN**) 值、正无穷、负无穷。

### 4.3.22 Number 对象 <div id="sec-number-object"></div>

对象类型的成员，它是标准内置构造器 Number 的一个实例。

> 注： 通过使用 **new** 表达式，以一个数字值为参数调用 **Number** 构造器来创建数字对象。由此产生的对象包含一个值为此数字值的内部属性。将 **Number** 构造器作为一个函数来调用，可将一个 **Number** 对象强制转换为一个数字值（20.1.1.1）。

### 4.3.23 Infinity <div id="sec-infinity"></div>

正无穷 **Number** 值。

### 4.3.24 NaN <div id="sec-nan"></div>

值为 IEEE 754-2008 “Not-a-Number”的 **Number** 值。

### 4.3.25 Symbol 值 <div id="sec-symbol-value"></div>

原始值，代表一个唯一的，非字符串对象的属性的键（key）。

### 4.3.26 Symbol 类型 <div id="sec-symbol-type"></div>

所有可能的Symbol值的集合。

### 4.3.27 Symbol 对象 <div id="sec-symbol-object"></div>

对象类型的成员，是标准的内置**Symbol**构造函数的实例

### 4.3.28 函数 <div id="sec-function"></div>

Object 类型的成员，标准内置构造器 Function 的一个实例，并且可作为子程序被调用。

> 注： 函数除了拥有命名的属性，还包含可执行代码、状态，用来确定被调用时的行为。函数的代码不限于 ECMAScript。

### 4.3.29 内置函数 <div id="sec-build-in-function"></div>

属于函数的内置对象。

> 注： 如 **parseInt** 和 **Math.exp** 就是内置函数。一个实现可以提供本规范没有描述的依赖于实现的内置函数。

### 4.3.30 属性 <div id="sec-property"></div>

是对象的一部分，关联键（要么是一个字符串，要么是一个Symbol值）和值 

> 注： 根据属性形式的不同，其值可以直接表现为一个数据值（原始值、对象、函数对象）或间接地通过一对访问器函数来表现。

### 4.3.31 方法 <div id="sec-method"></div>

作为属性值的函数。

> 注： 当一个函数被作为一个对象的方法调用时，此对象将作为 **this** 值传递给函数。

### 4.3.32 内置方法 <div id="sec-build-in-method"></div>

属于内置函数的方法。

> 注： 标准内置方法由本规范定义，一个 ECMAScript 实现可指定，提供其他额外的内置方法。

### 4.3.33 特性 <div id="sec-attribute"></div>

用于定义属性的一些特征的内部值。

### 4.3.34 自身属性 <div id="sec-own-property"></div>

对象直接拥有的属性。

### 4.3.35 继承属性 <div id="sec-inherited-property"></div>

不是对象的自身属性，但是对象原型的属性（可以是自身或继承的）。

## 4.4 本规范的章节组织 <div id="sec-organization-of-this-specification"></div>

本规范剩余部分的组织如下:

第5章定义了在本规范中使用的一些符号或者语法的约定。

第6-9章定义了ECMAScript程序操作包含的执行环境。

第10-16章定义了实际的ECMAScript语言，包括它的语法编码以及语言特性的执行语义。

第17-26章定义了ECMAScript标准库。它们包括所有当ECMAScript程序执行时可用的标准对象的定义。

第27章描述了对SharedArrayBuffer支持的内存的访问的内存一致性模型以及Atomics对象的方法。 

