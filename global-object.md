# 18 全局对象

The global object:

- is created before control enters any execution context.
- does not have a [[Construct]] internal method; it cannot be used as a constructor with the new operator.
- does not have a [[Call]] internal method; it cannot be invoked as a function
- has a [[Prototype]] internal slot whose value is implementation-dependent.
- may have host defined properties in addition to the properties defined in this specification. This may include a property whose value is the global object itself.

## 18.1 全局对象的值属性 <div id="sec-value-properties-of-the-global-object"></div>
### 18.1.1 Infinity <div id="sec-value-properties-of-the-global-object-infinity"></div>

The value of Infinity is +∞ (see 6.1.6). This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 18.1.2 NaN <div id="sec-value-properties-of-the-global-object-nan"></div>

The value of NaN is NaN (see 6.1.6). This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

### 18.1.3 undefined <div id="sec-undefined"></div>

The value of undefined is undefined (see 6.1.1). This property has the attributes { [[Writable]]: false, [[Enumerable]]: false, [[Configurable]]: false }.

## 18.2 全局对象的函数属性 <div id="sec-function-properties-of-the-global-object"></div>

### 18.2.1 eval ( x ) <div id="sec-eval-x"></div>

The eval function is the %eval% intrinsic object. When the eval function is called with one argument x, the following steps are taken:

1. Assert: The execution context stack has at least two elements.
2. Let callerContext be the second to top element of the execution context stack.
3. Let callerRealm be callerContext's Realm.
4. Let calleeRealm be the current Realm Record.
5. Perform ? HostEnsureCanCompileStrings(callerRealm, calleeRealm).
6. Return ? PerformEval(x, calleeRealm, false, false).

#### 18.2.1.1 运行时语义：PerformEval ( x, evalRealm, strictCaller, direct ) <div id="sec-performeval"></div>

The abstract operation PerformEval with arguments x, evalRealm, strictCaller, and direct performs the following steps:

1. Assert: If direct is false, then strictCaller is also false.
2. If Type(x) is not String, return x.
3. Let thisEnvRec be ! GetThisEnvironment().
4. If thisEnvRec is a function Environment Record, then
   1. Let F be thisEnvRec.[[FunctionObject]].
   2. Let inFunction be true.
   3. Let inMethod be thisEnvRec.HasSuperBinding().
   4. If F.[[ConstructorKind]] is "derived", let inDerivedConstructor be true; otherwise, let
     inDerivedConstructor be false.
5. Else,
  1. Let inFunction be false.
  2. Let inMethod be false.
  3. Let inDerivedConstructor be false.
6. Let script be the ECMAScript code that is the result of parsing x, interpreted as UTF-16 encoded Unicode text as described in 6.1.4, for the goal symbol Script. If inFunction is false, additional early error rules from 18.2.1.1.1 are applied. If inMethod is false, additional early error rules from 18.2.1.1.2 are applied. If inDerivedConstructor is false, additional early error rules from 18.2.1.1.3 are applied. If the parse fails, throw a SyntaxError exception. If any early errors are detected, throw a SyntaxError or a ReferenceError exception, depending on the type of the error (but see also clause 16). Parsing and early error detection may be interweaved in an implementationdependent manner.
7. If script Contains ScriptBody is false, return undefined.
8. Let body be the ScriptBody of script.
9. If strictCaller is true, let strictEval be true.
10. Else, let strictEval be IsStrict of script.
11. Let ctx be the running execution context.
12. NOTE: If direct is true, ctx will be the execution context that performed the direct eval. If direct is false, ctx will be the execution context for the invocation of the eval function.
13. f direct is true, then
    1. Let lexEnv be NewDeclarativeEnvironment(ctx's LexicalEnvironment).
    2. Let varEnv be ctx's VariableEnvironment.
14. Else,
    1. Let lexEnv be NewDeclarativeEnvironment(evalRealm.[[GlobalEnv]]).
    2. Let varEnv be evalRealm.[[GlobalEnv]].
15. If strictEval is true, set varEnv to lexEnv.
16. If ctx is not already suspended, suspend ctx.
17. Let evalCxt be a new ECMAScript code execution context.
18. Set the evalCxt's Function to null.
19. Set the evalCxt's Realm to evalRealm.
20. Set the evalCxt's ScriptOrModule to ctx's ScriptOrModule.
21. Set the evalCxt's VariableEnvironment to varEnv.
22. Set the evalCxt's LexicalEnvironment to lexEnv.
23. Push evalCxt on to the execution context stack; evalCxt is now the running execution context.
24. Let result be EvalDeclarationInstantiation(body, varEnv, lexEnv, strictEval).
25. If result.[[Type]] is normal, then
    1. Set result to the result of evaluating body.
26. If result.[[Type]] is normal and result.[[Value]] is empty, then
    1. Set result to NormalCompletion(undefined).
27. Suspend evalCxt and remove it from the execution context stack.
28. Resume the context that is now on the top of the execution context stack as the running execution context.
29. Return Completion(result).

> NOTE The eval code cannot instantiate variable or function bindings in the variable environment of the calling context that invoked the eval if the calling context is evaluating formal parameter initializers or if either the code of the calling context or the eval code is strict mode code. Instead such bindings are instantiated in a new VariableEnvironment that is only accessible to the eval code. Bindings introduced by let, const, or class declarations are always instantiated in a new LexicalEnvironment.

##### 18.2.1.1.1 eval外部函数的附加早期错误规则 <div id="sec-performeval-rules-outside-functions"></div>

These static semantics are applied by PerformEval when a direct eval call occurs outside of any function.

```
ScriptBody : StatementList
```

- It is a Syntax Error if StatementList Contains NewTarget

##### 18.2.1.1.2 eval外部方法的附加早期错误规则 <div id="sec-performeval-rules-outside-methods"></div>

These static semantics are applied by PerformEval when a direct eval call occurs outside of a MethodDefinition.

```
ScriptBody : StatementList
```

- It is a Syntax Error if StatementList Contains SuperProperty.

##### 18.2.1.1.3 eval外部构造方法的附加早期错误规则 <div id="sec-performeval-rules-outside-constructors"></div>

These static semantics are applied by PerformEval when a direct eval call occurs outside of the constructor method of a ClassDeclaration or ClassExpression.

```
ScriptBody : StatementList
```

- It is a Syntax Error if StatementList Contains SuperCall.

#### 18.2.1.2 HostEnsureCanCompileStrings ( callerRealm, calleeRealm ) <div id="sec-hostensurecancompilestrings"></div>

HostEnsureCanCompileStrings is an implementation-defined abstract operation that allows host environments to block certain ECMAScript functions which allow developers to compile strings into ECMAScript code.

An implementation of HostEnsureCanCompileStrings may complete normally or abruptly. Any abrupt completions will be propagated to its callers. The default implementation of HostEnsureCanCompileStrings is to unconditionally return an empty normal completion.

#### 18.2.1.3 运行时语义：EvalDeclarationInstantiation ( body, varEnv, lexEnv, strict ) <div id="sec-evaldeclarationinstantiation"></div>

When the abstract operation EvalDeclarationInstantiation is called with arguments body, varEnv, lexEnv, and strict, the following steps are taken:

1. Let varNames be the VarDeclaredNames of body.
2. Let varDeclarations be the VarScopedDeclarations of body.
3. Let lexEnvRec be lexEnv's EnvironmentRecord.
4. Let varEnvRec be varEnv's EnvironmentRecord.
5. If strict is false, then
   1. If varEnvRec is a global Environment Record, then
     1. For each name in varNames, do
       1. If varEnvRec.HasLexicalDeclaration(name) is true, throw a SyntaxError exception.
       2. NOTE: eval will not create a global var declaration that would be shadowed by a global lexical
         declaration.
   2. Let thisLex be lexEnv.
   3. Assert: The following loop will terminate.
   4. Repeat, while thisLex is not the same as varEnv,
     1. Let thisEnvRec be thisLex's EnvironmentRecord.
     2. If thisEnvRec is not an object Environment Record, then
       1. NOTE: The environment of with statements cannot contain any lexical declaration so it doesn't need to be checked for var/let hoisting conflicts.
       2. For each name in varNames, do
          1. If thisEnvRec.HasBinding(name) is true, then
            1. Throw a SyntaxError exception.
            2. NOTE: Annex B.3.5 defines alternate semantics for the above step.
          2. NOTE: A direct eval will not hoist var declaration over a like-named lexical declaration.
     3. Set thisLex to thisLex's outer environment reference.
6. Let functionsToInitialize be a new empty List.
7. Let declaredFunctionNames be a new empty List.
8. For each d in varDeclarations, in reverse list order, do
   1. If d is neither a VariableDeclaration nor a ForBinding nor a BindingIdentifier, then
     1. Assert: d is either a FunctionDeclaration, a GeneratorDeclaration, an AsyncFunctionDeclaration, or an AsyncGeneratorDeclaration.
     2. NOTE: If there are multiple function declarations for the same name, the last declaration is used.
     3. Let fn be the sole element of the BoundNames of d.
     4. If fn is not an element of declaredFunctionNames, then
       1. If varEnvRec is a global Environment Record, then
          1. Let fnDefinable be ? varEnvRec.CanDeclareGlobalFunction(fn).
          2. If fnDefinable is false, throw a TypeError exception.
       2. Append fn to declaredFunctionNames.
       3. Insert d as the first element of functionsToInitialize.
9. NOTE: Annex B.3.3.3 adds additional steps at this point.
10. Let declaredVarNames be a new empty List.
11. For each d in varDeclarations, do
    1. If d is a VariableDeclaration, a ForBinding, or a BindingIdentifier, then
      1. For each String vn in the BoundNames of d, do
        1. If vn is not an element of declaredFunctionNames, then
           1. If varEnvRec is a global Environment Record, then
             1. Let vnDefinable be ? varEnvRec.CanDeclareGlobalVar(vn).
             2. If vnDefinable is false, throw a TypeError exception.
           2. If vn is not an element of declaredVarNames, then
             1. Append vn to declaredVarNames.
12. NOTE: No abnormal terminations occur after this algorithm step unless varEnvRec is a global Environment Record and the global object is a Proxy exotic object.
13. Let lexDeclarations be the LexicallyScopedDeclarations of body.
14. For each element d in lexDeclarations, do
    1. NOTE: Lexically declared names are only instantiated here but not initialized.
    2. For each element dn of the BoundNames of d, do
      1. If IsConstantDeclaration of d is true, then
        1. Perform ? lexEnvRec.CreateImmutableBinding(dn, true).
      2. Else,
         1. Perform ? lexEnvRec.CreateMutableBinding(dn, false).
15. For each Parse Node f in functionsToInitialize, do
    1. Let fn be the sole element of the BoundNames of f.
    2. Let fo be the result of performing InstantiateFunctionObject for f with argument lexEnv.
    3. If varEnvRec is a global Environment Record, then
      1. Perform ? varEnvRec.CreateGlobalFunctionBinding(fn, fo, true).
    4. Else,
      1. Let bindingExists be varEnvRec.HasBinding(fn).
      2. If bindingExists is false, then
        1. Let status be ! varEnvRec.CreateMutableBinding(fn, true).
        2. Assert: status is not an abrupt completion because of validation preceding step 12.
        3. Perform ! varEnvRec.InitializeBinding(fn, fo).
      3. Else,
        1. Perform ! varEnvRec.SetMutableBinding(fn, fo, false).
16. For each String vn in declaredVarNames, in list order, do
    1. If varEnvRec is a global Environment Record, then
      1. Perform ? varEnvRec.CreateGlobalVarBinding(vn, true).
    2. Else,
      1. Let bindingExists be varEnvRec.HasBinding(vn).
      2. If bindingExists is false, then
        1. Let status be ! varEnvRec.CreateMutableBinding(vn, true).
        2. Assert: status is not an abrupt completion because of validation preceding step 12.
        3. Perform ! varEnvRec.InitializeBinding(vn, undefined).
17. Return NormalCompletion(empty).

> NOTE An alternative version of this algorithm is described in B.3.5

### 18.2.2 isFinite ( number ) <div id="sec-isfinite-number"></div>

The isFinite function is the %isFinite% intrinsic object. When the isFinite function is called with one argument number, the following steps are taken:

1. Let num be ? ToNumber(number).
2. If num is NaN, +∞, or -∞, return false.
3. Otherwise, return true.

### 18.2.3 isNaN ( number ) <div id="sec-isnan-number"></div>

The isNaN function is the %isNaN% intrinsic object. When the isNaN function is called with one argument number, the following steps are taken:

1. Let num be ? ToNumber(number).
2. If num is NaN, return true.
3. Otherwise, return false.

> NOTE A reliable way for ECMAScript code to test if a value X is a NaN is an expression of the form X !== X. The result will be true if and only if X is a NaN.

### 18.2.4 parseFloat ( string ) <div id="sec-parsefloat-string"></div>

The parseFloat function produces a Number value dictated by interpretation of the contents of the string argument as a decimal literal.

The parseFloat function is the %parseFloat% intrinsic object. When the parseFloat function is called with one argument string, the following steps are taken:

1. Let inputString be ? ToString(string).
2. Let trimmedString be a substring of inputString consisting of the leftmost code unit that is not a StrWhiteSpaceChar and all code units to the right of that code unit. (In other words, remove leading white space.) If inputString does not contain any such code units, let trimmedString be the empty string.
3. If neither trimmedString nor any prefix of trimmedString satisfies the syntax of a StrDecimalLiteral (see 7.1.3.1), return NaN.
4. Let numberString be the longest prefix of trimmedString, which might be trimmedString itself, that satisfies the syntax of a StrDecimalLiteral.
5. Let mathFloat be MV of numberString.
6. If mathFloat = 0, then
   1. If the first code unit of trimmedString is the code unit 0x002D (HYPHEN-MINUS), return -0.
   2. Return +0.
7. Return the Number value for mathFloat.

> NOTE parseFloat may interpret only a leading portion of string as a Number value; it ignores any code units that cannot be interpreted as part of the notation of a decimal literal, and no indication is given that any such code units were ignored.

### 18.2.5 parseInt ( string, radix ) <div id="sec-parseint-string-radix"></div>

The parseInt function produces an integer value dictated by interpretation of the contents of the string argument according to the specified radix. Leading white space in string is ignored. If radix is undefined or 0, it is assumed to be 10 except when the number begins with the code unit pairs 0x or 0X, in which case a radix of 16 is assumed. If radix is 16, the number may also optionally begin with the code unit pairs 0x or 0X.

The parseInt function is the %parseInt% intrinsic object. When the parseInt function is called, the following steps are taken:

1. Let inputString be ? ToString(string).
2. Let S be a newly created substring of inputString consisting of the first code unit that is not a StrWhiteSpaceChar and all code units following that code unit. (In other words, remove leading white space.) If inputString does not contain any such code unit, let S be the empty string.
3. Let sign be 1.
4. If S is not empty and the first code unit of S is the code unit 0x002D (HYPHEN-MINUS), set sign to -1.
5. If S is not empty and the first code unit of S is the code unit 0x002B (PLUS SIGN) or the code unit 0x002D (HYPHEN-MINUS), remove the first code unit from S.
6. Let R be ? ToInt32(radix).
7. Let stripPrefix be true.
8. If R ≠ 0, then
   1. If R < 2 or R > 36, return NaN.
   2. If R ≠ 16, set stripPrefix to false.
9. Else R = 0,
   1. Set R to 10.
10. If stripPrefix is true, then
    1. If the length of S is at least 2 and the first two code units of S are either "0x" or "0X", then
      1. Remove the first two code units from S.
      2. Set R to 16.
11. If S contains a code unit that is not a radix-R digit, let Z be the substring of S consisting of all code units before the first such code unit; otherwise, let Z be S.
12. If Z is empty, return NaN.
13. Let mathInt be the mathematical integer value that is represented by Z in radix-R notation, using the letters A-Z and a-z for digits with values 10 through 35. (However, if R is 10 and Z contains more than 20 significant digits, every significant digit after the 20th may be replaced by a 0 digit, at the option of the implementation; and if R is not 2, 4, 8, 10, 16, or 32, then mathInt may be an implementation-dependent approximation to the mathematical integer value that is represented by Z in radix-R notation.)
14. If mathInt = 0, then
    1. If sign = -1, return -0.
    2. Return +0.
15. Let number be the Number value for mathInt.
16. Return sign × number.

> NOTE parseInt may interpret only a leading portion of string as an integer value; it ignores any code units that cannot be interpreted as part of the notation of an integer, and no indication is given that any such code units were ignored.

### 18.2.6 URI Handling Functions <div id="sec-uri-handling-functions"></div>

Uniform Resource Identifiers, or URIs, are Strings that identify resources (e.g. web pages or files) and transport protocols by which to access them (e.g. HTTP or FTP) on the Internet. The ECMAScript language itself does not provide any support for using URIs except for functions that encode and decode URIs as described in 18.2.6.2, 18.2.6.3, 18.2.6.4 and 18.2.6.5

> NOTE Many implementations of ECMAScript provide additional functions and methods that manipulate web pages; these functions are beyond the scope of this standard.

#### 18.2.6.1 URI 语法和语义 <div id="sec-uri-syntax-and-semantics"></div>

A URI is composed of a sequence of components separated by component separators. The general form is:

```
Scheme : First / Second ; Third ? Fourth
```

where the italicized names represent components and “:”, “/”, “;” and “?” are reserved for use as separators. The encodeURI and decodeURI functions are intended to work with complete URIs; they assume that any reserved code units in the URI are intended to have special meaning and so are not encoded. The encodeURIComponent and decodeURIComponent functions are intended to work with the individual component parts of a URI; they assume that any reserved code units represent text and so must be encoded so that they are not interpreted as reserved code units when the component is part of a complete URI.

The following lexical grammar specifies the form of encoded URIs.

Syntax

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

> NOTE The above syntax is based upon RFC 2396 and does not reflect changes introduced by the more recent RFC 3986

Runtime Semantics

When a code unit to be included in a URI is not listed above or is not intended to have the special meaning sometimes given to the reserved code units, that code unit must be encoded. The code unit is transformed into its UTF-8 encoding, with surrogate pairs first converted from UTF-16 to the corresponding code point value. (Note that for code units in the range [0, 127] this results in a single octet with the same value.) The resulting sequence of octets is then transformed into a String with each octet represented by an escape sequence of the form "%xx".

##### 18.2.6.1.1 运行时语义：Encode ( string, unescapedSet ) <div id="sec-encode"></div>

The encoding and escaping process is described by the abstract operation Encode taking two String arguments string and unescapedSet.

1. Let strLen be the number of code units in string.
2. Let R be the empty String.
3. Let k be 0.
4. Repeat,
   1. If k equals strLen, return R.
   2. Let C be the code unit at index k within string.
   3. If C is in unescapedSet, then
     1. Let S be the String value containing only the code unit C.
     2. Set R to the string-concatenation of the previous value of R and S.
   4. Else C is not in unescapedSet,
     1. If C is a trailing surrogate, throw a URIError exception.
     2. If C is not a leading surrogate, then
       1. Let V be the code point with the same numeric value as code unit C.
     3. Else,
       1. Increase k by 1.
       2. If k equals strLen, throw a URIError exception.
       3. Let kChar be the code unit at index k within string.
       4. If kChar is not a trailing surrogate, throw a URIError exception.
       5. Let V be UTF16Decode(C, kChar).
     4. Let Octets be the List of octets resulting by applying the UTF-8 transformation to V.
     5. For each element octet of Octets in List order, do
       1. Let S be the string-concatenation of:
         "%"
         the String representation of octet, formatted as a two-digit uppercase hexadecimal number,padded to the left with a zero if necessary
       2. Set R to the string-concatenation of the previous value of R and S.
   5. Increase k by 1.

##### 18.2.6.1.2 运行时语义：Decode ( string, reservedSet ) <div id="sec-decode"></div>
The unescaping and decoding process is described by the abstract operation Decode taking two String arguments string and reservedSet.

1. Let strLen be the number of code units in string.
2. Let R be the empty String.
3. Let k be 0.
4. Repeat,
        1. If k equals strLen, return R.
        2. Let C be the code unit at index k within string.
        3. If C is not the code unit 0x0025 (PERCENT SIGN), then
               1. Let S be the String value containing only the code unit C.
        4. Else C is the code unit 0x0025 (PERCENT SIGN),
               1. Let start be k.
               2. If k + 2 is greater than or equal to strLen, throw a URIError exception.
               3. If the code units at index (k + 1) and (k + 2) within string do not represent hexadecimal digits, throw aURIError exception.
               4. Let B be the 8-bit value represented by the two hexadecimal digits at index (k + 1) and (k + 2).
               5. Increment k by 2.
               6. If the most significant bit in B is 0, then
                      1. Let C be the code unit whose value is B.
                      2. If C is not in reservedSet, then
                             1. Let S be the String value containing only the code unit C.
                      3. Else C is in reservedSet,
                             1. Let S be the substring of string from index start to index k inclusive.
               7. Else the most significant bit in B is 1,
                      1. Let n be the smallest nonnegative integer such that (B << n) & 0x80 is equal to 0.
                      2. If n equals 1 or n is greater than 4, throw a URIError exception.
                      3. Let Octets be a List of 8-bit integers of size n.
                      4. Set Octets[0] to B.
                      5. If k + (3 × (n - 1)) is greater than or equal to strLen, throw a URIError exception.
                      6. Let j be 1.
                      7. Repeat, while j < n
                             1. Increment k by 1.
                             2. If the code unit at index k within string is not the code unit 0x0025 (PERCENT SIGN), throw a URIError exception.
                             3. If the code units at index (k + 1) and (k + 2) within string do not represent hexadecimal digits, throw a URIError exception.
                             4. Let B be the 8-bit value represented by the two hexadecimal digits at index (k + 1) and (k + 2).
                             5. If the two most significant bits in B are not 10, throw a URIError exception.
                             6. Increment k by 2.
                             7. Set Octets[j] to B.
                             8. Increment j by 1.
                      8. If Octets does not contain a valid UTF-8 encoding of a Unicode code point, throw a URIError
                           exception.
                      9. Let V be the value obtained by applying the UTF-8 transformation to Octets, that is, from a List of octets into a 21-bit value.
                      10. Let S be the String value whose code units are, in order, the elements in UTF16Encoding(V).
        5. Set R to the string-concatenation of the previous value of R and S.
        6. Increase k by 1.

> NOTE This syntax of Uniform Resource Identifiers is based upon RFC 2396 and does not reflect the more recent RFC 3986 which replaces RFC 2396. A formal description and implementation of UTF-8 is given in RFC 3629.
>
> In UTF-8, characters are encoded using sequences of 1 to 6 octets. The only octet of a sequence of one has the higherorder bit set to 0, the remaining 7 bits being used to encode the character value. In a sequence of n octets, n > 1, the initial octet has the n higher-order bits set to 1, followed by a bit set to 0. The remaining bits of that octet contain bits from the value of the character to be encoded. The following octets all have the higher-order bit set to 1 and the following bit set to 0, leaving 6 bits in each to contain bits from the character to be encoded. The possible UTF-8 encodings of ECMAScript characters are specified in Table 46.
>
> |                                                     |                                                         |              |              |              |              |
> | --------------------------------------------------- | ------------------------------------------------------- | ------------ | ------------ | ------------ | ------------ |
> | Code Unit Value                                     | Representation                                          | 1st Octet    | 2nd Octet    | 3rd Octet    | 4th Octet    |
> | `0x0000 - 0x007F`                                   | `00000000 0*zzzzzzz*`                                   | `0*zzzzzzz*` |              |              |              |
> | `0x0080 - 0x07FF`                                   | `00000*yyy yyzzzzzz*`                                   | `110*yyyyy*` | `10*zzzzzz*` |              |              |
> | `0x0800 - 0xD7FF`                                   | `*xxxxyyyy yyzzzzzz*`                                   | `1110*xxxx*` | `10*yyyyyy*` | `10*zzzzzz*` |              |
> | `0xD800 - 0xDBFF` followed by `0xDC00 - 0xDFFF`     | `110110*vv vvwwwwxx*` followed by `110111*yy yyzzzzzz*` | `11110*uuu*` | `10*uuwwww*` | `10*xxyyyy*` | `10*zzzzzz*` |
> | `0xD800 - 0xDBFF` not followed by `0xDC00 - 0xDFFF` | causes `URIError`                                       |              |              |              |              |
> | `0xDC00 - 0xDFFF`                                   | causes `URIError`                                       |              |              |              |              |
> | `0xE000 - 0xFFFF`                                   | `*xxxxyyyy yyzzzzzz*`                                   | `1110*xxxx*` | `10*yyyyyy*` | `10*zzzzzz*` |              |
>
> Where uuuuu = vvvv + 1 to account for the addition of 0x10000 as in section 3.8 of the Unicode Standard (Surrogates).
>
> The above transformation combines each surrogate pair (for which code unit values in the inclusive range 0xD800 to 0xDFFF are reserved) into a UTF-32 representation and encodes the resulting 21-bit value into UTF-8. Decoding reconstructs the surrogate pair.
>
> RFC 3629 prohibits the decoding of invalid UTF-8 octet sequences. For example, the invalid sequence C0 80 must not decode into the code unit 0x0000. Implementations of the Decode algorithm are required to throw a URIError when encountering such invalid sequences.

#### 18.2.6.2 decodeURI ( encodedURI ) <div id="sec-decodeuri-encodeduri"></div>

The decodeURI function computes a new version of a URI in which each escape sequence and UTF-8 encoding of the sort that might be introduced by the encodeURI function is replaced with the UTF-16 encoding of the code points that it represents. Escape sequences that could not have been introduced by encodeURI are not replaced.

The decodeURI function is the %decodeURI% intrinsic object. When the decodeURI function is called with one argument encodedURI, the following steps are taken:

1. Let uriString be ? ToString(encodedURI).
2. Let reservedURISet be a String containing one instance of each code unit valid in uriReserved plus "#".
3. Return ? Decode(uriString, reservedURISet).

> NOTE The code point "#" is not decoded from escape sequences even though it is not a reserved URI code point.

#### 18.2.6.3 decodeURIComponent ( encodedURIComponent ) <div id="sec-decodeuricomponent-encodeduricomponent"></div>

The decodeURIComponent function computes a new version of a URI in which each escape sequence and UTF-8 encoding of the sort that might be introduced by the encodeURIComponent function is replaced with the UTF-16 encoding of the code points that it represents.

The decodeURIComponent function is the %decodeURIComponent% intrinsic object. When the decodeURIComponent function is called with one argument encodedURIComponent, the following steps are taken:

1. Let componentString be ? ToString(encodedURIComponent).
2. Let reservedURIComponentSet be the empty String.
3. Return ? Decode(componentString, reservedURIComponentSet).

#### 18.2.6.4 encodeURI ( uri ) <div id="sec-encodeuri-uri"></div>

The encodeURI function computes a new version of a UTF-16 encoded (6.1.4) URI in which each instance of certain code points is replaced by one, two, three, or four escape sequences representing the UTF-8 encoding of the code points.

The encodeURI function is the %encodeURI% intrinsic object. When the encodeURI function is called with one argument uri, the following steps are taken:

1. Let uriString be ? ToString(uri).
2. Let unescapedURISet be a String containing one instance of each code unit valid in uriReserved and uriUnescaped
plus "#".
3. Return ? Encode(uriString, unescapedURISet).

> NOTE The code unit "#" is not encoded to an escape sequence even though it is not a reserved or unescaped URI code point.

#### 18.2.6.5 encodeURIComponent ( uriComponent ) <div id="sec-encodeuricomponent-uricomponent"></div>

The encodeURIComponent function computes a new version of a UTF-16 encoded (6.1.4) URI in which each instance of certain code points is replaced by one, two, three, or four escape sequences representing the UTF-8 encoding of the code point.

The encodeURIComponent function is the %encodeURIComponent% intrinsic object. When the encodeURIComponent function is called with one argument uriComponent, the following steps are taken:

1. Let componentString be ? ToString(uriComponent).
2. Let unescapedURIComponentSet be a String containing one instance of each code unit valid in uriUnescaped.
3. Return ? Encode(componentString, unescapedURIComponentSet).

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

### 18.3.22 Set ( . . . ) <div id="sec-set"></div>

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