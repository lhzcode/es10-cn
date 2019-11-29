# 介绍

Ecma标准定义了2019年ECMAScript语言。这是第十版的ECMAScript语言规范。自1997年出版的第一版，ECMAScript已经是世界上最广泛使用的通用编程语言。它是众所周知的嵌入在Web浏览器中的语言，但也已广泛用于服务器和嵌入式应用程序

本Ecma标准建立在几项开创性技术上，其中最为著名的是JavaScript（网景）和JScript （微软）。JavaScript语言由网景的Brendan Eich发明而第一次出现在该公司的Navigator 2.0浏览器中。此后该语言出现在网景公司后续所有浏览器中，同时出现在微软公司自Internet Explorer 3.0始的所有浏览器中。

本标准的开发始于1996年11月，第一版在1997年6月被Ecma General Assembly采纳。

这项标准随后提交至ISO/IEC JTC 1，并在1998年4月被接纳为国际标准ISO/IEC 16262，其推进过程是相当快的。1998年6月Ecma General Assembly通过了ECMA-262第二版以保持其与ISO/IEC 16262的完全一致性。实际上，第一版到第二版的变更仅仅是编辑性的。

 第三版标准引入了强大的正则表达式、更佳的字符串处理、新的控制语句、try/catch异常处理、更严密地错误定义、格式化的数字输出以及一些为国际化和未来语言成长预留的小变更。1999年12月ECMAScript标准第三版被Ecma General Assembly采纳，并于2002年6月作为ISO/IEC 16262:2002发布。

自第三版发布以来，ECMAScript因其与万维网的关联而获得了广泛应用，它实质上已经成为所有web浏览器都提供的一种编程语言。为编制第四版ECMAScript，人们做了大量的工作。尽管这些工作没能完成，也没有发布第四版标准，但其中部分促进了第六版的发展。

 ECMAScript第五版（发布为ECMA-262 5th edition）编入了很多事实上已经在浏览器实现中形成共识的语言规范解析，并且增加了对自第三版发布以来出现的新功能的支持。这些新功能包括访问器属性、反射创建和对象检测、属性特性的程序控制、新增数组操作函数、JSON对象编码格式、以及提供了改进的错误检查和程序安全性的严格模式。第五版在2009年12月的Ecma大会上被采用。

第五版被提交给ISO/IEC JTC 1，并且在2011年的 ISO/IEC 16262:2011 上被作为国际标准被通过。ECMAScript标准的5.1版进行了一些小的修正，并与ISO/IEC 16262:2011保持一致。第5.1版在2011年6月底Ecma大会上被采纳。 

备受关注的第6版的开发于2009年开始，当时第5版正准备发布。然而，一些重大的实验和那些可以追溯到1999年第3版的发布时的语言增强方面的设计工作却使得第6版领先于第5版。  从真正意义上来讲，第6版的完成将这15年来的努力推向了顶峰。增加这些东西的初衷和目标有很多，包括对大型应用提供更好的支持，库的创建，还有将ECMAScript作为其他语言的编译结果的用法。同时，也包括一些重大的增强，如模块系统，类（class）声明，词法块级作用域，迭代器（iterator）和生成器（generator），为异步编程而生的promise，解构，以及适当的尾部调用。ECMAScript中内置的库得以扩充，目的是提供更多的数据抽象功能。如，map，set，数组二进制操作（TypedArray），字符串填充，正则表达式改进。内置库同样也通过子类来扩充。第6版是增量式的语言和库增强，于2015年6月在大会上被采纳。 

ECMAScript 2016 规范是Ecam TC39的第一个发布版，新的发布策略将会每年发布一次新版本，并且公布进展过程。纯文本的源文档从ECMAScript 2015源文档开始时在[Github](https://github.com/tc39/ecma262/releases)上建立，目的是为更进一步的发展提供一个基础。 在标准发展的这些年，提交了数以百计的pr和issue，它们为我们修复了很多bug，编辑错误以及其他方面的提升。除此之外，众多的软件工具被创造，帮助了我们。如Ecmarkup, Ecmarkdown, 以及Grammarkdown。规范同时也提供了对新的求幂运算符（`**`）的以及数组的`includes`方法的支持。 

ECMAScript 2017 介绍了异步（Async）函数、共享内存、原子（Atomics）以及较小的语言和库的功能增强、错误修复和编辑上的更新。异步函数通过promise返回函数语法，改善了异步编程体验。共享内存和原子技术引入了一种新的内存模型，该模型允许多主体程序使用原子操作进行通信，以确保即使在并行CPU。该规范还包括有关Object的新静态方法：Object.values，Object.entries，和Object.getOwnPropertyDescriptors。

ECMAScript 2018 介绍了由异步迭代协议和异步生成器提供的异步迭代支持。它还包括四个新的正则表达式功能：dotAll标志，命名捕获组，Unicode属性转义和后置断言。最后，它包括rest参数和对对象属性的扩展运算符支持。

该规范（第10版）引入了一些新的内置函数：Array.prototype上用于扁平化数组的flat和flatMap，用于将Object.entries的返回值直接转换为新的Object的Object.fromEntries，以及广泛实施但非标准的String.prototype.trimLeft和trimRight内置函数的更好的替代方案，即String.prototype中的trimStart和trimEnd。另外，该规范包括对语法和语义的一些次要更新。更新的语法包括可选的catch绑定参数，并允许字符串文字中的U+2028（行分隔符）和U+2029（参数分隔符）与JSON对齐。其他更新包括：Array.prototype.sort稳定排序；JSON.stringify返回格式正确的UTF-8（无论输入如何）；以及Function.prototype.toString返回相应的原始源文本或标准占位符。

代表不同组织的众多个人也为Ecma TC39现在及之前的发展做出来很多显著的贡献。除此之外，也出现了一个支持TC39并为ECMAScript努力的充满活力的社区。这个社区review了大量的草案，报告了大量的bug，并对实现进行了实验，贡献了测试相关的工具，使全世界的ECMAScript的开发者从中受益。原谅我们，无法对这些做出努力的贡献者们一一感谢。 

Allen Wirfs-Brock

ECMA-262, Project Editor, 6th Edition

Brian Terlson

ECMA-262, Project Editor, 7th through 10th Editions 

```
description Ecma标准定义了2019年ECMAScript语言。这是第十版的ECMAScript语言规范。自1997年出版的第一版，ECMAScript已经是世界上最广泛使用的通用编程语言。它是众所周知的嵌入在Web浏览器中的语言，但也已广泛用于服务器和嵌入式应用程序
```
```
keywords ES10,ES中文,ES翻译,ES2019,ES文档,ES规范,ECMAScript10,ECMAScript中文,ECMAScript翻译,ECMAScript2019,ECMAScript文档,ECMAScript规范,Ecma介绍,introduction
```