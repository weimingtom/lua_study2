Lua 5.2 Reference Manual

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes

Copyright ? 2011–2013 Lua.org, PUC-Rio. Freely available under the terms of the Lua license.

contents · index · other versions · português
1 – Introduction

Lua is an extension programming language designed to support general procedural programming with data description facilities. It also offers good support for object-oriented programming, functional programming, and data-driven programming. Lua is intended to be used as a powerful, lightweight, embeddable scripting language for any program that needs one. Lua is implemented as a library, written in clean C, the common subset of Standard C and C++.

Being an extension language, Lua has no notion of a "main" program: it only works embedded in a host client, called the embedding program or simply the host. The host program can invoke functions to execute a piece of Lua code, can write and read Lua variables, and can register C functions to be called by Lua code. Through the use of C functions, Lua can be augmented to cope with a wide range of different domains, thus creating customized programming languages sharing a syntactical framework. The Lua distribution includes a sample host program called lua, which uses the Lua library to offer a complete, standalone Lua interpreter, for interactive or batch use.

Lua is free software, and is provided as usual with no guarantees, as stated in its license. The implementation described in this manual is available at Lua's official web site, www.lua.org.

Like any other reference manual, this document is dry in places. For a discussion of the decisions behind the design of Lua, see the technical papers available at Lua's web site. For a detailed introduction to programming in Lua, see Roberto's book, Programming in Lua.

2 – Basic Concepts

This section describes the basic concepts of the language.

2.1 – Values and Types

Lua is a dynamically typed language. This means that variables do not have types; only values do. There are no type definitions in the language. All values carry their own type.

All values in Lua are first-class values. This means that all values can be stored in variables, passed as arguments to other functions, and returned as results.

There are eight basic types in Lua: nil, boolean, number, string, function, userdata, thread, and table. Nil is the type of the value nil, whose main property is to be different from any other value; it usually represents the absence of a useful value. Boolean is the type of the values false and true. Both nil and false make a condition false; any other value makes it true. Number represents real (double-precision floating-point) numbers. Operations on numbers follow the same rules of the underlying C implementation, which, in turn, usually follows the IEEE 754 standard. (It is easy to build Lua interpreters that use other internal representations for numbers, such as single-precision floats or long integers; see file luaconf.h.) String represents immutable sequences of bytes. Lua is 8-bit clean: strings can contain any 8-bit value, including embedded zeros ('\0').

Lua can call (and manipulate) functions written in Lua and functions written in C (see §3.4.9).

The type userdata is provided to allow arbitrary C data to be stored in Lua variables. A userdata value is a pointer to a block of raw memory. There are two kinds of userdata: full userdata, where the block of memory is managed by Lua, and light userdata, where the block of memory is managed by the host. Userdata has no predefined operations in Lua, except assignment and identity test. By using metatables, the programmer can define operations for full userdata values (see §2.4). Userdata values cannot be created or modified in Lua, only through the C API. This guarantees the integrity of data owned by the host program.

The type thread represents independent threads of execution and it is used to implement coroutines (see §2.6). Do not confuse Lua threads with operating-system threads. Lua supports coroutines on all systems, even those that do not support threads.

The type table implements associative arrays, that is, arrays that can be indexed not only with numbers, but with any Lua value except nil and NaN (Not a Number, a special numeric value used to represent undefined or unrepresentable results, such as 0/0). Tables can be heterogeneous; that is, they can contain values of all types (except nil). Any key with value nil is not considered part of the table. Conversely, any key that is not part of a table has an associated value nil.

Tables are the sole data structuring mechanism in Lua; they can be used to represent ordinary arrays, sequences, symbol tables, sets, records, graphs, trees, etc. To represent records, Lua uses the field name as an index. The language supports this representation by providing a.name as syntactic sugar for a["name"]. There are several convenient ways to create tables in Lua (see §3.4.8).

We use the term sequence to denote a table where the set of all positive numeric keys is equal to {1..n} for some integer n, which is called the length of the sequence (see §3.4.6).

Like indices, the values of table fields can be of any type. In particular, because functions are first-class values, table fields can contain functions. Thus tables can also carry methods (see §3.4.10).

The indexing of tables follows the definition of raw equality in the language. The expressions a[i] and a[j] denote the same table element if and only if i and j are raw equal (that is, equal without metamethods).

Tables, functions, threads, and (full) userdata values are objects: variables do not actually contain these values, only references to them. Assignment, parameter passing, and function returns always manipulate references to such values; these operations do not imply any kind of copy.

The library function type returns a string describing the type of a given value (see §6.1).

2.2 – Environments and the Global Environment

As will be discussed in §3.2 and §3.3.3, any reference to a global name var is syntactically translated to _ENV.var. Moreover, every chunk is compiled in the scope of an external local variable called _ENV (see §3.3.2), so _ENV itself is never a global name in a chunk.

Despite the existence of this external _ENV variable and the translation of global names, _ENV is a completely regular name. In particular, you can define new variables and parameters with that name. Each reference to a global name uses the _ENV that is visible at that point in the program, following the usual visibility rules of Lua (see §3.5).

Any table used as the value of _ENV is called an environment.

Lua keeps a distinguished environment called the global environment. This value is kept at a special index in the C registry (see §4.5). In Lua, the variable _G is initialized with this same value.

When Lua compiles a chunk, it initializes the value of its _ENV upvalue with the global environment (see load). Therefore, by default, global variables in Lua code refer to entries in the global environment. Moreover, all standard libraries are loaded in the global environment and several functions there operate on that environment. You can use load (or loadfile) to load a chunk with a different environment. (In C, you have to load the chunk and then change the value of its first upvalue.)

If you change the global environment in the registry (through C code or the debug library), all chunks loaded after the change will get the new environment. Previously loaded chunks are not affected, however, as each has its own reference to the environment in its _ENV variable. Moreover, the variable _G (which is stored in the original global environment) is never updated by Lua.

2.3 – Error Handling

Because Lua is an embedded extension language, all Lua actions start from C code in the host program calling a function from the Lua library (see lua_pcall). Whenever an error occurs during the compilation or execution of a Lua chunk, control returns to the host, which can take appropriate measures (such as printing an error message).

Lua code can explicitly generate an error by calling the error function. If you need to catch errors in Lua, you can use pcall or xpcall to call a given function in protected mode.

Whenever there is an error, an error object (also called an error message) is propagated with information about the error. Lua itself only generates errors where the error object is a string, but programs may generate errors with any value for the error object.

When you use xpcall or lua_pcall, you may give a message handler to be called in case of errors. This function is called with the original error message and returns a new error message. It is called before the error unwinds the stack, so that it can gather more information about the error, for instance by inspecting the stack and creating a stack traceback. This message handler is still protected by the protected call; so, an error inside the message handler will call the message handler again. If this loop goes on, Lua breaks it and returns an appropriate message.

