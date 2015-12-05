# 【翻译】(LRM5.1-1)简介(1)、词法约定(2.1)、值与类型(2.2)、变量(2.3)  

See also:  
http://www.lua.org/manual/5.1/manual.html  

原文见  
http://www.lua.org/manual/5.1/manual.html  

-----------------------------------------

# Lua 5.1 Reference Manual  

# Lua 5.1参考手册  

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes  

作者：Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes  

Copyright ? 2006-2008 Lua.org, PUC-Rio. Freely available under the terms of the Lua license.   

版权所有 (c) 2006-2008 Lua.org, PUC-Rio. 根据Lua许可证自由地**（注：免费）**可用  

-----------------------------------------

contents · index  

目录 索引  

## 1 - Introduction  

## 1 - 简介  

Lua is an extension programming language designed to support general procedural programming with data description facilities. It also offers good support for object-oriented programming, functional programming, and data-driven programming. Lua is intended to be used as a powerful, light-weight scripting language for any program that needs one. Lua is implemented as a library, written in clean C (that is, in the common subset of ANSI C and C++).   

Lua是一种为支持带数据描述功能的泛型过程式编程而设计的扩展编程语言。它还提供对面向对象编程的良好支持，函数式编程，以及数据驱动编程。Lua致力于成为一种强大和轻量级的脚本语言供任何需要它的程序使用。Lua被实现为一个库，用纯C（即ANSI C和C++的公共子集）编写。  

Being an extension language, Lua has no notion of a "main" program: it only works embedded in a host client, called the embedding program or simply the host. This host program can invoke functions to execute a piece of Lua code, can write and read Lua variables, and can register C functions to be called by Lua code. Through the use of C functions, Lua can be augmented to cope with a wide range of different domains, thus creating customized programming languages sharing a syntactical framework. The Lua distribution includes a sample host program called lua, which uses the Lua library to offer a complete, stand-alone Lua interpreter.   

作为一种扩展语言，Lua没有“主”程序的概念：它只是嵌入在称为嵌入式语言或者简称为宿主的宿主客户端中工作。这个宿主程序可以调用函数去执行一段Lua代码，可以读写Lua变量，还可以注册C函数供Lua代码调用。虽然使用了C函数，Lua可以被扩充，以应付广泛的不同领域，从而创建共享一个语法框架的可定制编程语言。Lua发布版包含一个称为lua的示例宿主程序,使用Lua库去提供一个完整、可单独运行的Lua解析器。  

Lua is free software, and is provided as usual with no guarantees, as stated in its license. The implementation described in this manual is available at Lua's official web site, www.lua.org.   

Lua是自由（免费）软件，并且一般不作保证地提供，正如它的许可证所述。在这个手册中所描述的实现可以在Lua的官方网站www.lua.org中找到。  

Like any other reference manual, this document is dry in places. For a discussion of the decisions behind the design of Lua, see the technical papers available at Lua's web site. For a detailed introduction to programming in Lua, see Roberto's book, Programming in Lua (Second Edition).   

像其它参考手册那样，这个文档很多地方都很枯燥。关于Lua设计背后的决策的讨论，请参考Lua官网上的技术文档。关于Lua编程的详细介绍，参考Roberto的书，《Lua程序设计（第二版）》。  

## 2 - The Language  

## 2 - 语言  

This section describes the lexis, the syntax, and the semantics of Lua. In other words, this section describes which tokens are valid, how they can be combined, and what their combinations mean.   

这个部分描述词汇，语法，以及语义。换言之，这个部分描述那些符号是有效，它们如何可以组合，以及它们组合的含义。  

The language constructs will be explained using the usual extended BNF notation, in which {a} means 0 or more a's, and [a] means an optional a. Non-terminals are shown like non-terminal, keywords are shown like kword, and other terminal symbols are shown like `=′. The complete syntax of Lua can be found in §8 at the end of this manual.  

语言构造将解析通用扩展BNF表示法的使用，{a}表示0和更多a，[a]表示一个可选的a。非终结符表示为非终结符（无黑体），关键词表示为kword（黑体），其他非终结符表示为'='。完整的Lua语法可以在本手册最后的第八章找到。  

## 2.1 - Lexical Conventions  

## 2.1 - 词法约定  

Names (also called identifiers) in Lua can be any string of letters, digits, and underscores, not beginning with a digit. This coincides with the definition of names in most languages. (The definition of letter depends on the current locale: any character considered alphabetic by the current locale can be used in an identifier.) Identifiers are used to name variables and table fields.   

在Lua中，名称（也成为标识符）可以是任意字母，数字和下划线组成但不是以一个数字开头的字符串。这正好和大多数语言的名称定义相符。（这个字母的定义依赖于当前的本地环境：任何被当前本地环境当作字母的字符都可以被用于标识符）标识符用于命名变量和表的域。  

The following keywords are reserved and cannot be used as names:   

下面的关键词被保留，不可以用于名称：  

    and       break     do        else      elseif  
    end       false     for       function  if  
    in        local     nil       not       or  
    repeat    return    then      true      until     while  

   （翻译如下：  
    and（逻辑与） break（跳出循环）do（条件为真时循环的块开始）else（其它条件，无判断）elseif（其它条件的判断）  
    end（循环、条件、闭包/函数块结束）false（逻辑假） for（固定步长的数学循环或迭代器循环）   
    function（闭包/函数块的开始）if（条件判断）  
    in（for循环内的迭代器遍历）local（局部变量声明） nil（空） not（逻辑非）or（逻辑或）  
    repeat（条件为假时循环的块开始） return（函数返回） then（条件块开始）true（逻辑真）  
    until（条件为假时循环的判断）while（条件为真时循环的判断）  
    ）  

Lua is a case-sensitive language: and is a reserved word, but And and AND are two different, valid names. As a convention, names starting with an underscore followed by uppercase letters (such as _VERSION) are reserved for internal global variables used by Lua.   

Lua是大小写区分的语言：并且虽然作为保留字，但And和AND是两个与and不同的的词，也可以作为合法的名称。作为一种约定，以下环线开头并且后面是全大写的名称（例如_VERSION）是被保留作为Lua的内部全局变量使用。  

The following strings denote other tokens:   

下面的字符串表示其他标记：  

    +     -     *     /     %     ^     #  
    ==    ~=    <=    >=    <     >     =  
    (     )     {     }     [     ]  
    ;     :     ,     .     ..    ...  

    （含义如下：  
    +（加）-（减或负号）*（乘）/（除）%（取模）^（指数幂）#（字符串或表长度）  
    ==（相等）~=（不等）<=（小于等于）>=（大于等于）<（小于）>（大于）=（赋值）  
    (     )（小括号）{     }（大括号，表）[     ]（中括号，下标）  
    ;（分号，分割语句）:（冒号，取对象成员）,（逗号，参数表）.（点号，取表成员）..（双点号，字符串串联） ...（三点号，变长参数列表）  
    ）  

Literal strings can be delimited by matching single or double quotes, and can contain the following C-like escape sequences: '\a' (bell), '\b' (backspace), '\f' (form feed), '\n' (newline), '\r' (carriage return), '\t' (horizontal tab), '\v' (vertical tab), '\\' (backslash), '\"' (quotation mark [double quote]), and '\'' (apostrophe [single quote]). Moreover, a backslash followed by a real newline results in a newline in the string. A character in a string can also be specified by its numerical value using the escape sequence \ddd, where ddd is a sequence of up to three decimal digits. (Note that if a numerical escape is to be followed by a digit, it must be expressed using exactly three digits.) Strings in Lua can contain any 8-bit value, including embedded zeros, which can be specified as '\0'.   

字面值的字符串可以由匹配的单引号或双引号分隔，还可以包含下面的C风格转义字符序列：'\a'（响铃），'\b'（退格），'\f'（表格回退），'\n'（换行），'\r'作为（回车），'\t'（水平制表符），'\v'（垂直制表符），'\\'（反斜杠），'\"'（引号[双引号]），和'\''（单引号[单引号]）。此外，反斜杠后的真实换行将在字符串中产生换行。一个字符串中的字符，也可以用带转义序列\ddd的数值来指定，ddd是最多3个十进制数字的序列。（注意，如果一个数值转义后面是一个数字，则它必须准确地使用三个数字表示）在Lua中字符串可以包含任何8位数值，包括中间夹杂的零**（注：区别于C风格的必须以\0结束而且前面不能包括\0的字符串）**，可以被指定为'\0'。  

Literal strings can also be defined using a long format enclosed by long brackets. We define an opening long bracket of level n as an opening square bracket followed by n equal signs followed by another opening square bracket. So, an opening long bracket of level 0 is written as [[, an opening long bracket of level 1 is written as [=[, and so on. A closing long bracket is defined similarly; for instance, a closing long bracket of level 4 is written as ]====]. A long string starts with an opening long bracket of any level and ends at the first closing long bracket of the same level. Literals in this bracketed form can run for several lines, do not interpret any escape sequences, and ignore long brackets of any other level. They can contain anything except a closing bracket of the proper level.  

字面值的字符串还可以使用一个长括号封闭的长格式来定义。我们把一个n层的左长括号定义为一个左方括号，然后跟着另一个带相同n层的左方括号。因此，一个0层左长括号写成[[，一个1层左长括号写成[=[，如此类推。右长括号的定义类似；例如，一个4层右长括号写成]====]。一个字符串以任意层的左长括号开始，以第一个出现的带相同层数的右长括号结束。方括号内的字面值可以拆分为几行，不需要使用任何转义序列，忽略其他任何级别的长括号。它们可以包含除了合适层数的右长括号以外的其它任意字符。  

For convenience, when the opening long bracket is immediately followed by a newline, the newline is not included in the string. As an example, in a system using ASCII (in which 'a' is coded as 97, newline is coded as 10, and '1' is coded as 49), the five literal strings below denote the same string:   

为方便起见，当左长方括号后面立刻跟着一个换行时，这个换行不包括在字符串中。作为一个例子，在一个使用ASCII的系统中（其中'a'编码为97，换行编码为10，'1'编码为49），下面五个字符串字面值表示相同的字符串值：  

     a = 'alo\n123"'  
     a = "alo\n123\""  
     a = '\97lo\10\04923"'  
     a = [[alo  
     123"]]  
     a = [==[  
     alo  
     123"]==]  

A numerical constant can be written with an optional decimal part and an optional decimal exponent. Lua also accepts integer hexadecimal constants, by prefixing them with 0x. Examples of valid numerical constants are    

一个数值常数可以写成一个可选的小数部分加上一个可选的十进制指数。Lua还接受十六进制整数常量，它们以0x作为前缀。合法数值常数的例子如下    

     3   3.0   3.1416   314.16e-2   0.31416E1   0xff   0x56  

A comment starts with a double hyphen (--) anywhere outside a string. If the text immediately after -- is not an opening long bracket, the comment is a short comment, which runs until the end of the line. Otherwise, it is a long comment, which runs until the corresponding closing long bracket. Long comments are frequently used to disable code temporarily.   

注释开始于字符串以外任何地方的一个双连字符（--）。如果--后紧跟着的文本不是一个左长方括号，这个注释就是一个短注释，它的注释直到行结束为止。否则，它是一个长注释，注释直至相应的右长方括号。长注释常常用于临时地禁用代码。  

## 2.2 - Values and Types  

## 2.2 - 值与类型  

Lua is a dynamically typed language. This means that variables do not have types; only values do. There are no type definitions in the language. All values carry their own type.   

Lua是一种动态类型的语言。这意味着变量没有类型；但值有类型。语言中没有类型定义。所有值携带它们自己的类型。  

All values in Lua are first-class values. This means that all values can be stored in variables, passed as arguments to other functions, and returned as results.   

Lua中所有值都是第一类值。这意味着所有的值都可以存储变量中，作为参数传递给其他函数，以及作为结果返回。  

There are eight basic types in Lua: nil, boolean, number, string, function, userdata, thread, and table. Nil is the type of the value nil, whose main property is to be different from any other value; it usually represents the absence of a useful value. Boolean is the type of the values false and true. Both nil and false make a condition false; any other value makes it true. Number represents real (double-precision floating-point) numbers. (It is easy to build Lua interpreters that use other internal representations for numbers, such as single-precision float or long integers; see file luaconf.h.) String represents arrays of characters. Lua is 8-bit clean: strings can contain any 8-bit character, including embedded zeros ('\0') (see §2.1).   

Lua有八种基本类型：nil，boolean，number，string，function，userdata，thread，和table。类型nil是nil的类型，其主要属性是不同于其它任何值，它通常代表缺少一个有用的值。类型boolean是值false和true的类型。nil和false使条件为假；其他任何值都使条件为真。类型number表示实数（双精度浮点数）。（在构建Lua解释器时很容易为类型number使用其他内部表示，例如单精度浮点数或者长整数。参考文件luaconf.h。）类型string表示字符数组。Lua对8位字符是干净的（注：纯8位）：字符串可以包含任何8位字符，包括内嵌零（'\0'）（见§2.1）。  

Lua can call (and manipulate) functions written in Lua and functions written in C (see §2.5.8).   

Lua中可以调用（和操纵）用lua编写的函数和用C编写的函数（见§2.5.8）。   

The type userdata is provided to allow arbitrary C data to be stored in Lua variables. This type corresponds to a block of raw memory and has no pre-defined operations in Lua, except assignment and identity test. However, by using metatables, the programmer can define operations for userdata values (see §2.8). Userdata values cannot be created or modified in Lua, only through the C API. This guarantees the integrity of data owned by the host program.   

提供类型userdata，允许用户把任意C数据保存在Lua变量中。此类型对应原始内存块并且在lua中没有预定义的操作，除了赋值和唯一性检查。但是，通过使用元表，程序员可以定义用户数据值的操作（见§2.8）。用户数据值不能用Lua创建或修改，只能通过C API。这保证了宿主程序所拥有数据的完整性。   

The type thread represents independent threads of execution and it is used to implement coroutines (see §2.11). Do not confuse Lua threads with operating-system threads. Lua supports coroutines on all systems, even those that do not support threads.   

类型thread代表可执行的独立线程，它用于实现协程（见§2.11）。不要把Lua的线程和操作系统的线程混淆。Lua在所有系统上支持协程，即使在那些不支持线程的系统上。  

The type table implements associative arrays, that is, arrays that can be indexed not only with numbers, but with any value (except nil). Tables can be heterogeneous; that is, they can contain values of all types (except nil). Tables are the sole data structuring mechanism in Lua; they can be used to represent ordinary arrays, symbol tables, sets, records, graphs, trees, etc. To represent records, Lua uses the field name as an index. The language supports this representation by providing a.name as syntactic sugar for a["name"]. There are several convenient ways to create tables in Lua (see §2.5.7).   

类型table实现关联数组，即数组可以不仅仅用数字索引，还可以用任何值（除了nil）。表可以是异构的，即它们可以包含所有类型的值（除了nil）。表是在Lua中唯一的数据结构机制；它们可以被用于表示普通数组，符号表，集合，记录，图，树等。为了表示记录，Lua使用域（字段）名作为一个索引。语言通过提供a.name这种表示作为a["name"]的语法糖。用Lua有几种简便方法可以创建表（见§2.5.7）。  

Like indices, the value of a table field can be of any type (except nil). In particular, because functions are first-class values, table fields can contain functions. Thus tables can also carry methods (see §2.5.9).   

类似索引，表的字段值可以是任意类型（除了nil）。特别地，因为函数是第一类值，表的字段可以包含函数。因此，表也可以携带方法（见§2.5.9）。  

Tables, functions, threads, and (full) userdata values are objects: variables do not actually contain these values, only references to them. Assignment, parameter passing, and function returns always manipulate references to such values; these operations do not imply any kind of copy.   

表，函数，线程和（完全）用户数据的值都是对象：变量实际上并不包含这些值，只是包含他们的引用。赋值，参数传递，函数返回总是对其值的引用的操作，这些操作并不暗示有任何类型的复制**（注：副本）**。  

The library function type returns a string describing the type of a given value.   

库函数type返回一个描述所给值的类型的字符串。  

## 2.2.1 - Coercion  

## 2.2.1 - 强制转换  

Lua provides automatic conversion between string and number values at run time. Any arithmetic operation applied to a string tries to convert this string to a number, following the usual conversion rules. Conversely, whenever a number is used where a string is expected, the number is converted to a string, in a reasonable format. For complete control over how numbers are converted to strings, use the format function from the string library (see string.format).   

Lua中提供在运行时字符串和数值之间的自动转换。应用到任何一个字符串的任意算术运算都会试图按照通常的转换规则，将这个字符串转换为数字。相反，每当一个数在预计使用一个字符串的场合被使用时，这个数会以一个合理的格式转换为字符串。想知道如何把数字转换为字符串的完整控制，请使用string库的format函数（见string.format）。  

## 2.3 - Variables  

## 2.3 - 变量  

Variables are places that store values. There are three kinds of variables in Lua: global variables, local variables, and table fields.   

变量是存储值的地方。Lua有三种变量：全局变量，局部变量和表字段（表域）。  

A single name can denote a global variable or a local variable (or a function's formal parameter, which is a particular kind of local variable):   

一个单一的名称可以表示一个全局变量或一个局部变量（或函数的形式参数，它是一种特殊的局部变量）：  

    var ::= Name  

Name denotes identifiers, as defined in §2.1.   

名称表示标识符，在§2.1定义。  

Any variable is assumed to be global unless explicitly declared as a local (see §2.4.7). Local variables are lexically scoped: local variables can be freely accessed by functions defined inside their scope (see §2.6).   

任何变量都被假设为全局的，除非它被显式地声明为局部（见§2.4.7）。局部变量是词法作用域：局部变量可以被那些在它们范围内定义的函数自由访问（见§2.6）。  

Before the first assignment to a variable, its value is nil.   

在一个变量的第一次赋值之前，它的值是nil。  

Square brackets are used to index a table:   

方括号用于索引表：   

    var ::= prefixexp `[′ exp `]′  

The meaning of accesses to global variables and table fields can be changed via metatables. An access to an indexed variable t[i] is equivalent to a call gettable_event(t,i). (See §2.8 for a complete description of the gettable_event function. This function is not defined or callable in Lua. We use it here only for explanatory purposes.)   

对全局变量和表字段的访问的含义可以通过元表来改变。对一个索引变量t[i]的访问等效于对gettable_event(t,i)的调用。（请参考§2.8中关于gettable_event函数的完整说明。这个函数没有用Lua定义或者可以在Lua中调用。我们在这里用它只是便于解释。）  

The syntax var.Name is just syntactic sugar for var["Name"]:   

语法var.Name只是var["Name"]的语法糖：   

    var ::= prefixexp `.′ Name  

All global variables live as fields in ordinary Lua tables, called environment tables or simply environments (see §2.9). Each function has its own reference to an environment, so that all global variables in this function will refer to this environment table. When a function is created, it inherits the environment from the function that created it. To get the environment table of a Lua function, you call getfenv. To replace it, you call setfenv. (You can only manipulate the environment of C functions through the debug library; (see §5.9).)   

所有全局变量正如普通Lua表的字段那样存在，被称为环境表或简称环境（见§2.9）。每个函数都有一个自己的对应环境的引用，因此在这个函数中所有全局变量将参考这个环境表。当一个函数被创建时，它从创造它的函数中继承环境。要想获得一个Lua函数的环境表，你可以调用getfenv。要想替换它，你可以调用setfenv。（你只能通过debug库操作C函数的环境；（见§ 5.9）。）  

An access to a global variable x is equivalent to _env.x, which in turn is equivalent to   

访问全局变量x等效于访问_env.x，而这又相当于执行  

    gettable_event(_env, "x")  

where _env is the environment of the running function. (See §2.8 for a complete description of the gettable_event function. This function is not defined or callable in Lua. Similarly, the _env variable is not defined in Lua. We use them here only for explanatory purposes.)   

其中_env是正在运行的函数的环境。（见§2.8关于gettable_event函数的完整描述。这个函数不是用Lua定义或者被Lua代码调用。同样，_env变量也不是用Lua定义。在这里我们仅仅为了方便解析而使用它们。）  

-----------------------------------------

参考自：  
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  
2. hshqcn  
http://hshqcn.iteye.com/blog/284901  
