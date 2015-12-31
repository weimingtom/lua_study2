## 【翻译】(LRM5.2-1)

See also:  
http://www.lua.org/manual/5.2/manual.html  

原文见  
http://www.lua.org/manual/5.2/manual.html  

-----------------------------------------

## Lua 5.2 Reference Manual  

## Lua 5.2参考手册  

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes

作者：Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes  

Copyright ? 2011–2013 Lua.org, PUC-Rio. Freely available under the terms of the Lua license.

版权所有 (c) 2011-2013 Lua.org, PUC-Rio. 根据Lua许可证自由地**（注：免费）**可用  

-----------------------------------------

contents · index · other versions · português  

目录 索引 其他版本 葡萄牙语  

## 1 – Introduction  

## 1 - 简介  

Lua is an extension programming language designed to support general procedural programming with data description facilities. It also offers good support for object-oriented programming, functional programming, and data-driven programming. Lua is intended to be used as a powerful, lightweight, embeddable scripting language for any program that needs one. Lua is implemented as a library, written in clean C, the common subset of Standard C and C++.  

Lua是一种为支持带数据描述功能的通用过程式编程而设计的扩展编程语言。它还提供对面向对象编程、函数式编程，以及数据驱动编程的良好支持。Lua致力于成为一种强大的、轻量级的、可嵌入的脚本语言供任何需要它的程序使用。Lua作为一个库被实现，用纯C编写，即标准C和C++的公共子集。 

Being an extension language, Lua has no notion of a "main" program: it only works embedded in a host client, called the embedding program or simply the host. The host program can invoke functions to execute a piece of Lua code, can write and read Lua variables, and can register C functions to be called by Lua code. Through the use of C functions, Lua can be augmented to cope with a wide range of different domains, thus creating customized programming languages sharing a syntactical framework. The Lua distribution includes a sample host program called lua, which uses the Lua library to offer a complete, standalone Lua interpreter, for interactive or batch use.  

作为一种扩展语言，Lua没有“主”程序的概念：它只是嵌入在被称为嵌入式程序或简单说是宿主的宿主客户端中工作。宿主程序可以调用函数去执行一段Lua代码，可以读写Lua变量，还可以注册C函数供Lua代码调用。虽然使用了C函数，Lua可以被扩充，以应付广泛范围的不同领域，从而创建共享一个语法框架的定制编程语言。Lua发布版包含一个称为lua的示例宿主程序，它使用Lua库去提供一个完整、单独运行的Lua解析器。 

Lua is free software, and is provided as usual with no guarantees, as stated in its license. The implementation described in this manual is available at Lua's official web site, www.lua.org.  

Lua是自由（免费）软件，并且一般不作保证地提供，正如它的许可证所述。在这个手册中所描述的实现可以在Lua的官方网站www.lua.org中找到。  

Like any other reference manual, this document is dry in places. For a discussion of the decisions behind the design of Lua, see the technical papers available at Lua's web site. For a detailed introduction to programming in Lua, see Roberto's book, Programming in Lua.  

像其他参考手册那样，这个文档很多地方都很枯燥。关于Lua设计背后的决策的讨论，请参考Lua网站上的技术文档。关于Lua编程的详细介绍，参考Roberto的书，《Lua程序设计》。  

# 2 – Basic Concepts    

# 2 - 基本概念  

This section describes the basic concepts of the language.  

这个部分描述语言的基本概念。  

2.1 – Values and Types  

2.1 - 值与类型  

Lua is a dynamically typed language. This means that variables do not have types; only values do. There are no type definitions in the language. All values carry their own type.  

Lua是一种动态类型语言。这意味着变量没有类型；但值有类型。语言中没有类型定义。所有值携带它们自己的类型。  

All values in Lua are first-class values. This means that all values can be stored in variables, passed as arguments to other functions, and returned as results.  

Lua中所有值都是第一类**（注：一等公民）**值。这意味着所有的值都可以被存储在变量中，作为参数传递给其他函数，以及作为结果返回。  

There are eight basic types in Lua: nil, boolean, number, string, function, userdata, thread, and table. Nil is the type of the value nil, whose main property is to be different from any other value; it usually represents the absence of a useful value. Boolean is the type of the values false and true. Both nil and false make a condition false; any other value makes it true. Number represents real (double-precision floating-point) numbers. Operations on numbers follow the same rules of the underlying C implementation, which, in turn, usually follows the IEEE 754 standard. (It is easy to build Lua interpreters that use other internal representations for numbers, such as single-precision floats or long integers; see file luaconf.h.) String represents immutable sequences of bytes. Lua is 8-bit clean: strings can contain any 8-bit value, including embedded zeros ('\0').  

Lua有八种基本类型：nil，boolean，number，string，function，userdata，thread，和table。类型nil是nil的类型，其主要属性是不同于其他任何值，它通常代表缺少一个有用的值。类型boolean是值false和true的类型。nil和false使条件为假；其他任何值都使条件为真。类型number表示实数（双精度浮点数）。在数上的操作遵循底层C实现的相同规则，即对应地，通常遵循IEEE 754标准。（这便于构建使用其他内部数表示的Lua解释器，诸如单精度浮点数或长整数；参考文件luaconf.h。）类型string表示不可变字节序列。Lua对8位字符是干净的**（注：纯8位）**：字符串可以包含任何8位值，包括内嵌零（'\0'）。  

Lua can call (and manipulate) functions written in Lua and functions written in C (see §3.4.9).  

Lua可以调用（和操纵）用lua编写的函数和用C编写的函数（见§2.5.8）。   

The type userdata is provided to allow arbitrary C data to be stored in Lua variables. A userdata value is a pointer to a block of raw memory. There are two kinds of userdata: full userdata, where the block of memory is managed by Lua, and light userdata, where the block of memory is managed by the host. Userdata has no predefined operations in Lua, except assignment and identity test. By using metatables, the programmer can define operations for full userdata values (see §2.4). Userdata values cannot be created or modified in Lua, only through the C API. This guarantees the integrity of data owned by the host program.  

提供类型userdata，允许用户把任意C数据保存在Lua变量中。一个userdata值是一个指针指向一块原始内存。有两类userdata：完全userdata，在那里内存块由Lua管理，以及轻量级userdata，在那里内存块由宿主管理。userdata在Lua中没有预定义的操作，除了赋值和唯一性测试**（注：相等测试）**。通过使用元表，程序员可以为完全userdata值定义操作（见§2.4）。userdata值不能用Lua创建或修改，只能通过C API。这确保宿主程序所拥有数据的完整性。  

The type thread represents independent threads of execution and it is used to implement coroutines (see §2.6). Do not confuse Lua threads with operating-system threads. Lua supports coroutines on all systems, even those that do not support threads.  

类型thread表示执行的独立线程，并且用于实现协程（见§2.6）。不要把Lua线程和操作系统线程混淆。Lua在所有系统上支持协程，即使在那些不支持线程的系统上。  

The type table implements associative arrays, that is, arrays that can be indexed not only with numbers, but with any Lua value except nil and NaN (Not a Number, a special numeric value used to represent undefined or unrepresentable results, such as 0/0). Tables can be heterogeneous; that is, they can contain values of all types (except nil). Any key with value nil is not considered part of the table. Conversely, any key that is not part of a table has an associated value nil.  

类型table实现关联数组，即数组可以不仅仅用数索引，还可以用任意Lua值除了nil和NaN（不是一个数，一个特殊数值用于表示未定义或不可表示的结果，诸如0/0）。表可以是异构的；即它们可以包含所有类型的值（除了nil）。任意对应值为nil的键不被认为是表的一部分。相反地，任意不属于表的一部分的键拥有一个关联值nil。  

Tables are the sole data structuring mechanism in Lua; they can be used to represent ordinary arrays, sequences, symbol tables, sets, records, graphs, trees, etc. To represent records, Lua uses the field name as an index. The language supports this representation by providing a.name as syntactic sugar for a["name"]. There are several convenient ways to create tables in Lua (see §3.4.8).  

表在Lua中是唯一的数据结构机制；它们可以被用于表示一般数组、序列、符号表、集合、记录、图、树，等等。为了表示记录，Lua使用字段名称作为索引。语言支持这种表示，通过提供a.name作为a["name"]的语法糖。在Lua中有几种便利方式创建表（见§3.4.8）。  

We use the term sequence to denote a table where the set of all positive numeric keys is equal to {1..n} for some integer n, which is called the length of the sequence (see §3.4.6).  

我们使用术语序列以表示一个表，在那里所有正数键的集合等于{1..n}，关于一些整数n，它被称为序列的长度（见§3.4.6）。  

Like indices, the values of table fields can be of any type. In particular, because functions are first-class values, table fields can contain functions. Thus tables can also carry methods (see §3.4.10).  

像索引那样，表字段的值可以是任意类型的。特别地，因为函数是第一类值**（注：一等公民）**，表字段可以包含函数。因此表也可以携带方法（见§3.4.10）。  

The indexing of tables follows the definition of raw equality in the language. The expressions a[i] and a[j] denote the same table element if and only if i and j are raw equal (that is, equal without metamethods).  

表的索引遵循语言中原始相等的定义。表达式a[i]和a[j]指示相同的表元素，当且仅当i和j是原始相等的（即，不含元方法的相等）。  

Tables, functions, threads, and (full) userdata values are objects: variables do not actually contain these values, only references to them. Assignment, parameter passing, and function returns always manipulate references to such values; these operations do not imply any kind of copy.  

表，函数，线程和（完全）用户数据的值都是对象：变量实际上并不包含这些值，只是包含他们的引用。赋值，参数传递，以及函数返回总是操纵这些值的引用；这些操作并不暗示有任何类型的复制**（注：副本）**。  

The library function type returns a string describing the type of a given value (see §6.1).    

库函数type返回一个描述所给值的类型的字符串（见§6.1）。   

2.2 – Environments and the Global Environment  

2.2 - 环境和全局环境  

As will be discussed in §3.2 and §3.3.3, any reference to a global name var is syntactically translated to _ENV.var. Moreover, every chunk is compiled in the scope of an external local variable called _ENV (see §3.3.2), so _ENV itself is never a global name in a chunk.  

正如将在§3.2和§3.3.3中讨论的那样，任意指向一个全局名称var的引用在语法上被翻译成_ENV.var。此外，每个块在一个被称为_ENV（见§3.3.2）的外部局部变量的范围内被编译，所以_ENV自身在一个块内从不是一个全局名称。  

Despite the existence of this external _ENV variable and the translation of global names, _ENV is a completely regular name. In particular, you can define new variables and parameters with that name. Each reference to a global name uses the _ENV that is visible at that point in the program, following the usual visibility rules of Lua (see §3.5).  

尽管这个外部_ENV变量的存在和全局名称的翻译，_ENV仍然是一个完全常规的名称。特别地，你可以以那个名称定义新的变量和参数。每个指向全局名称的引用使用_ENV，它在程序中那个点上是可见的，遵循Lua的一般的可见性规则（见§3.5）。  

Any table used as the value of _ENV is called an environment.  

任何用于作为_ENV的值的表被称为环境。  

Lua keeps a distinguished environment called the global environment. This value is kept at a special index in the C registry (see §4.5). In Lua, the variable _G is initialized with this same value.  

Lua保留一个不同的环境，被称为全局环境。这个值被保留在C注册表的一个特殊索引上（见§4.5）。在Lua中，变量_G以相同的值被初始化。  

When Lua compiles a chunk, it initializes the value of its _ENV upvalue with the global environment (see load). Therefore, by default, global variables in Lua code refer to entries in the global environment. Moreover, all standard libraries are loaded in the global environment and several functions there operate on that environment. You can use load (or loadfile) to load a chunk with a different environment. (In C, you have to load the chunk and then change the value of its first upvalue.)    

当Lua编译一个块时，它用全局环境初始化它的_ENV上值（见load）。因此，默认，在Lua代码中的全局变量指向全局环境的记录，另外，所有标准库被加载在全局环境中，而在那里有几个函数操作那个环境。你可以使用load（或loadfile）以用不同的环境加载一个块。（在C中，你不得不加载一个块然后改变它的第一个上值的值。）  

If you change the global environment in the registry (through C code or the debug library), all chunks loaded after the change will get the new environment. Previously loaded chunks are not affected, however, as each has its own reference to the environment in its _ENV variable. Moreover, the variable _G (which is stored in the original global environment) is never updated by Lua.  

如果你改变注册表中的全局环境（通过C代码或调试库），那么所有在改变后加载的块将获得新的环境。但是，之前加载的块不受影响，因为每一个块都拥有它自己的指向环境的引用，在它的_ENV环境中。此外，变量_G（它被存储在原来的全局变量中）从不被Lua更新。  

2.3 – Error Handling  

2.3 - 错误处理  

Because Lua is an embedded extension language, all Lua actions start from C code in the host program calling a function from the Lua library (see lua_pcall). Whenever an error occurs during the compilation or execution of a Lua chunk, control returns to the host, which can take appropriate measures (such as printing an error message).  

因为Lua是一种嵌入扩展语言，所以Lua的动作开始于宿主程序中C代码，它调用一个来自Lua库的函数（见lua_pcall）。不论一个错误何时发生，在编译期还是在Lua块的执行期，控制权都会返回给宿主，它可以采取适当的措施（如打印一条错误信息）。  

Lua code can explicitly generate an error by calling the error function. If you need to catch errors in Lua, you can use pcall or xpcall to call a given function in protected mode.  

Lua代码可以通过调用error函数显式地生成错误。如果你需要在Lua中捕获错误，你可以使用pcall或xpcall在保护模式下调用一个给定函数。  

Whenever there is an error, an error object (also called an error message) is propagated with information about the error. Lua itself only generates errors where the error object is a string, but programs may generate errors with any value for the error object.  

每当出现一个错误时，一个错误对象（也被称为错误消息）会被传播，它携带有关错误的信息。Lua自身只生成错误，在那里错误对象是一个字符串，但程序可以生成以任意值作为错误对象的错误。  

When you use xpcall or lua_pcall, you may give a message handler to be called in case of errors. This function is called with the original error message and returns a new error message. It is called before the error unwinds the stack, so that it can gather more information about the error, for instance by inspecting the stack and creating a stack traceback. This message handler is still protected by the protected call; so, an error inside the message handler will call the message handler again. If this loop goes on, Lua breaks it and returns an appropriate message.  

当你使用xpcall或lua_pcall时，你可以指定一个错误处理器，如果错误发生时它会被调用。这个函数被调用，带有原来的错误消息，并返回一个新的错误消息。它在错误从栈中解开之前被调用，因此它可以收集关于错误的更多信息，例如通过检查栈和创建一个栈回溯。这个消息处理器仍然被受保护的调用保护；所以，一个在消息处理器中的错误将再次调用消息处理器。如果这个循环持续，Lua会中断它并返回一个合适的消息。  
