【翻译】(LRM5.1-6)堆栈(3.1)、堆栈大小(3.2)、伪索引(3.3)、C闭包(3.4)、注册表(3.5)、C的错误处理(3.6)

See also:
http://www.lua.org/manual/5.1/manual.html

原文见
http://www.lua.org/manual/5.1/manual.html

-----------------------------------------

Lua 5.1 Reference Manual 

Lua 5.1参考手册

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes 

作者：Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes

Copyright ? 2006-2008 Lua.org, PUC-Rio. Freely available under the terms of the Lua license. 

版权所有 (c) 2006-2008 Lua.org, PUC-Rio. 根据Lua许可证自由地（注：免费）可用

-----------------------------------------

3 - The Application Program Interface

3 - 应用程序接口

This section describes the C API for Lua, that is, the set of C functions available to the host program to communicate with Lua. All API functions and related types and constants are declared in the header file lua.h. 

本节描述Lua的C API，即提供给宿主程序和Lua通信的有效的C函数集合。所有API函数和相关类型以及常量在头文件lua.h中声明. 

Even when we use the term "function", any facility in the API may be provided as a macro instead. All such macros use each of their arguments exactly once (except for the first argument, which is always a Lua state), and so do not generate any hidden side-effects. 

虽然我们使用术语“函数”，任何提供的API工具都可能是一个宏。所有这些宏准确地使用一次它们的参数（除了第一个参数，它始终是一个Lua状态），所以不会产生任何隐藏的副作用。 

As in most C libraries, the Lua API functions do not check their arguments for validity or consistency. However, you can change this behavior by compiling Lua with a proper definition for the macro luai_apicheck, in file luaconf.h. 

正如大多数C库，Lua的API函数不检查它们参数的有效性或一致性。但是，你可以通过合适的luai_apicheck宏定义编译Lua来改变这种行为，该宏定义在文件luaconf.h。

3.1 - The Stack

3.1 - 堆栈

Lua uses a virtual stack to pass values to and from C. Each element in this stack represents a Lua value (nil, number, string, etc.). 

Lua使用一个虚拟栈与C来回传递值。堆栈中每个元素代表一个Lua值（空值，数，字符串等）。 

Whenever Lua calls C, the called function gets a new stack, which is independent of previous stacks and of stacks of C functions that are still active. This stack initially contains any arguments to the C function and it is where the C function pushes its results to be returned to the caller (see lua_CFunction). 

每当Lua中调用C，被调用函数获得一个新的堆栈，独立于仍旧激活的前一个堆栈和前一个C函数的堆栈。这个堆栈最初包含传递给C函数的任何参数，它也是C函数压入结果以返回给它的调用者的地方（见见lua_CFunction）。

For convenience, most query operations in the API do not follow a strict stack discipline. Instead, they can refer to any element in the stack by using an index: A positive index represents an absolute stack position (starting at 1); a negative index represents an offset relative to the top of the stack. More specifically, if the stack has n elements, then index 1 represents the first element (that is, the element that was pushed onto the stack first) and index n represents the last element; index -1 also represents the last element (that is, the element at the top) and index -n represents the first element. We say that an index is valid if it lies between 1 and the stack top (that is, if 1 ≤ abs(index) ≤ top). 

方便起见，大多数API的查询操作不遵循严格的堆栈约定。取而代之，他们可以通过使用索引来引用堆栈中的任意元素：正索引代表绝对的堆栈位置（从1开始）；一个负索引代表相对栈顶的偏移。更具体来说，如果栈有n个元素，那么索引1代表第一个元素（即第一个压入堆栈的元素）而索引n表示最后一个元素；索引-1也代表最后一个元素（即栈顶的元素）而索引n代表第一个元素。如果一个索引在1和栈顶之间（即如果1 <= index的绝对值 <= 栈顶索引），则我们说这个索引是合法的。

3.2 - Stack Size

3.2 - 堆栈大小 

When you interact with Lua API, you are responsible for ensuring consistency. In particular, you are responsible for controlling stack overflow. You can use the function lua_checkstack to grow the stack size. 

当你与Lua的API交互时，你要负责确保一致性。特别地，你要负责控制堆栈的溢出。你可以使用函数lua_checkstack增加栈的大小。

Whenever Lua calls C, it ensures that at least LUA_MINSTACK stack positions are available. LUA_MINSTACK is defined as 20, so that usually you do not have to worry about stack space unless your code has loops pushing elements onto the stack. 

当Lua调用C时，可确保至少有LUA_MINSTACK个堆栈位置可用。LUA_MINSTACK被定义为20，所以你通常不必担心堆栈空间，除非你的代码循环把元素压入堆栈。

Most query functions accept as indices any value inside the available stack space, that is, indices up to the maximum stack size you have set through lua_checkstack. Such indices are called acceptable indices. More formally, we define an acceptable index as follows: 

大多数请求函数把有效堆栈空间中的任意值以索引的形式接受（注：以索引来表示值的引用），索引不超过你通过lua_checkstack设置的最大堆栈大小。这种索引被称为可接受索引。更正式地，我们以如下方式定义可接受索引：

     (index < 0 && abs(index) <= top) ||
     (index > 0 && index <= stackspace)

Note that 0 is never an acceptable index. 

注意0不是一个可接受索引。

3.3 - Pseudo-Indices

3.3 - 伪索引 

Unless otherwise noted, any function that accepts valid indices can also be called with pseudo-indices, which represent some Lua values that are accessible to C code but which are not in the stack. Pseudo-indices are used to access the thread environment, the function environment, the registry, and the upvalues of a C function (see §3.4). 

除非另外说明，任何接受合法索引的函数还可以用伪索引调用，伪索引代表一些不在堆栈中但可以被C代码访问的Lua值。伪索引用于访问线程环境，函数环境，注册表，以及一个C函数的upvalue（见§3.4）。

The thread environment (where global variables live) is always at pseudo-index LUA_GLOBALSINDEX. The environment of the running C function is always at pseudo-index LUA_ENVIRONINDEX. 

线程环境（全局变量所在的地方）总是在伪索引LUA_GLOBALSINDEX处。正在运行的C函数的环境总是在伪索引LUA_ENVIRONINDEX处。 

To access and change the value of global variables, you can use regular table operations over an environment table. For instance, to access the value of a global variable, do 

访问和改变全局变量的值，你可以在一个环境表上执行常规的表操作。例如，要访问一个全局变量的值，可以执行 

     lua_getfield(L, LUA_GLOBALSINDEX, varname);

3.4 - C Closures

3.4 - C闭包（注：指用C实现而在Lua中使用的闭包）

When a C function is created, it is possible to associate some values with it, thus creating a C closure; these values are called upvalues and are accessible to the function whenever it is called (see lua_pushcclosure). 

当一个C函数被创建，它有可能关联一些值，从而建立一个C闭包，这些值被称为upvalues，并且在调用时可访问该函数（见lua_pushcclosure）。（注：类似于C的静态局部变量）

Whenever a C function is called, its upvalues are located at specific pseudo-indices. These pseudo-indices are produced by the macro lua_upvalueindex. The first value associated with a function is at position lua_upvalueindex(1), and so on. Any access to lua_upvalueindex(n), where n is greater than the number of upvalues of the current function (but not greater than 256), produces an acceptable (but invalid) index. 

当一个C函数被调用时，它的upvalue位于特定的伪索引处。这些伪索引是由宏lua_upvalueindex产生。第一个被函数关联的值在lua_upvalueindex(1)处，如此类推。任何对lua_upvalueindex(n)的访问，如果n大于当前函数的upvalue个数（但不超过256），将产生一个可以接受的（但非法）的索引。 

3.5 - Registry

3.5 - 注册表

Lua provides a registry, a pre-defined table that can be used by any C code to store whatever Lua value it needs to store. This table is always located at pseudo-index LUA_REGISTRYINDEX. Any C library can store data into this table, but it should take care to choose keys different from those used by other libraries, to avoid collisions. Typically, you should use as key a string containing your library name or a light userdata with the address of a C object in your code. 

Lua提供注册表，一个预定义的表，任何C代码可以用它来存储任何需要存储的Lua值。这个表总是位于伪索引LUA_REGISTRYINDEX处。任何C库都可以存储数据进这个表，但应注意选择不同于其它库所用的键，以避免冲突。通常，你应该使用一个包含库名称的键或那些携带你代码中C对象地址的轻量级userdata。 

The integer keys in the registry are used by the reference mechanism, implemented by the auxiliary library, and therefore should not be used for other purposes. 

注册表中的整数键用于引用机制，由辅助库实现，因此不应用于其他用途。 

3.6 - Error Handling in C

3.6 - C的错误处理 

Internally, Lua uses the C longjmp facility to handle errors. (You can also choose to use exceptions if you use C++; see file luaconf.h.) When Lua faces any error (such as memory allocation errors, type errors, syntax errors, and runtime errors) it raises an error; that is, it does a long jump. A protected environment uses setjmp to set a recover point; any error jumps to the most recent active recover point. 

在实现内部，Lua使用C的longjmp工具处理错误。（如果你使用C++；您也可以选择使用异常；见luaconf.h。）当Lua遇到任意错误时（例如内存分配错误，类型错误，语法错误和运行时错误）它会引发一个错误，即它会执行一个长跳转。一个受保护的环境使用setjmp设置一个恢复点；任何错误会跳转到最近激活的恢复点。

Most functions in the API can throw an error, for instance due to a memory allocation error. The documentation for each function indicates whether it can throw errors. 

大部分API的函数都可能抛出一个错误，比如因为内存分配错误。每个函数的文档会指出它是否可能抛出错误。

Inside a C function you can throw an error by calling lua_error. 

在C函数内部，你可以通过调用lua_error抛出错误。

-----------------------------------------

参考自：
1. Lua 5.1 参考手册 （云风译）
http://www.codingnow.com/2000/download/lua_manual.html
2. hshqcn
hshqcn  

