【翻译】(LRM5.1-14)标准库的基础函数(5.1)  

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

5 - Standard Libraries

5 - 标准库

The standard Lua libraries provide useful functions that are implemented directly through the C API. Some of these functions provide essential services to the language (e.g., type and getmetatable); others provide access to "outside" services (e.g., I/O); and others could be implemented in Lua itself, but are quite useful or have critical performance requirements that deserve an implementation in C (e.g., table.sort). 

标准Lua库提供直接通过C API实现的一些有用函数。这些函数中有一些提供对语言的必要（注：本质）服务（例如，type和getmetatable）；其它则提供对“外部”服务的访问（例如，输入输出）；还有一些可以用Lua自身来实现，但非常有用或者有重要的性能要求值得用C来实现（例如，table.sort）。 

All libraries are implemented through the official C API and are provided as separate C modules. Currently, Lua has the following standard libraries: 

所有库通过官方C API实现，并被提供作为独立的C模块。当前，Lua拥有以下标准库： 

basic library, which includes the coroutine sub-library; 

基础库，它包括协程子库；

package library; 

包库；

string manipulation; 

字符串操纵；

table manipulation; 

表操纵；

mathematical functions (sin, log, etc.); 

数学函数（sin，log，等等）；

input and output; 

输入和输出；

operating system facilities; 

操作系统工具；

debug facilities. 

调试工具。

Except for the basic and package libraries, each library provides all its functions as fields of a global table or as methods of its objects. 

除了基础库和包库以外，每个库提供它的所有函数作为一个全局表的域或它的对象的方法。

To have access to these libraries, the C host program should call the luaL_openlibs function, which opens all standard libraries. Alternatively, it can open them individually by calling luaopen_base (for the basic library), luaopen_package (for the package library), luaopen_string (for the string library), luaopen_table (for the table library), luaopen_math (for the mathematical library), luaopen_io (for the I/O library), luaopen_os (for the Operating System library), and luaopen_debug (for the debug library). These functions are declared in lualib.h and should not be called directly: you must call them like any other Lua C function, e.g., by using lua_call. 

要想拥有对这些库的访问权，C宿主程序应该调用luaL_openlibs函数，它打开所有标准库。可选地，它可以通过调用luaopen_base（对于基础库），luaopen_package（对于包库），luaopen_string（对于字符串库），luaopen_table（对于表库），luaopen_math（对于数学库），luaopen_io（对于输入输出库），luaopen_os（对于操作系统库），和luaopen_debug（对于调试库），单独地打开它们。这些函数被声明在lualib.h中，并且不应该直接被调用：你必须像其它任意Lua的C函数那样调用它们，例如，通过使用lua_call。

5.1 - Basic Functions

5.1 - 基础函数

The basic library provides some core functions to Lua. If you do not include this library in your application, you should check carefully whether you need to provide implementations for some of its facilities. 

基础库提供一些核心函数给Lua。如果在你的应用程序中你不包含这个库，你应该仔细地检查你是否需要提供它的其中一些工具的实现。

--------------------------------------------------------------------------------

assert (v [, message])

Issues an error when the value of its argument v is false (i.e., nil or false); otherwise, returns all its arguments. message is an error message; when absent, it defaults to "assertion failed!" 

发出一个错误，当它的参数v的值为false（例如，nil或false）时；否则，返回它的所有参数。message是一个错误消息；如果不存在，它默认为“诊断失败！”

--------------------------------------------------------------------------------

collectgarbage (opt [, arg])

This function is a generic interface to the garbage collector. It performs different functions according to its first argument, opt: 

这个函数是对于垃圾回收器的一个通用接口。它执行不同的函数，根据它的第一个参数，opt：

"stop": stops the garbage collector. 

"stop"：停止垃圾回收器。 

"restart": restarts the garbage collector. 

"restart"：重启垃圾回收器。

"collect": performs a full garbage-collection cycle. 

"collect"：执行一个完全垃圾回收周期。 

"count": returns the total memory in use by Lua (in Kbytes). 

"count"：返回正在被Lua使用的总内存（单位千字节）. 

"step": performs a garbage-collection step. The step "size" is controlled by arg (larger values mean more steps) in a non-specified way. If you want to control the step size you must experimentally tune the value of arg. Returns true if the step finished a collection cycle. 

"step"：执行一次垃圾回收步长。步长“大小”以一种非特定的方式被arg控制（较大的值意味着更多步）。如果你想控制步长大小，你必须试验地调节arg的值。返回true，如果该步完成了一次回收周期。

"setpause": sets arg as the new value for the pause of the collector (see §2.10). Returns the previous value for pause. 

"setpause"：设置arg作为回收器新的暂停的值（参考§2.10）。返回用于暂停的前一个值。

"setstepmul": sets arg as the new value for the step multiplier of the collector (see §2.10). Returns the previous value for step. 

"setstepmul"：设置arg作为回收器步长乘数的新值。返回用于步进的前一个值。

--------------------------------------------------------------------------------

dofile (filename)

Opens the named file and executes its contents as a Lua chunk. When called without arguments, dofile executes the contents of the standard input (stdin). Returns all values returned by the chunk. In case of errors, dofile propagates the error to its caller (that is, dofile does not run in protected mode). 

打开具名文件并执行它的内容作为Lua的chunk块。当不带参数地调用它时，dofile执行标准输入（stdin）的内容。返回chunk块返回的所有值。如果出错，dofile传播错误给它的调用方（就是说，dofile不运行在保护模式中）。

--------------------------------------------------------------------------------

error (message [, level])

Terminates the last protected function called and returns message as the error message. Function error never returns. 

终止最后被调用的被保护函数，并返回message作为错误消息。函数error从不返回。

Usually, error adds some information about the error position at the beginning of the message. The level argument specifies how to get the error position. With level 1 (the default), the error position is where the error function was called. Level 2 points the error to where the function that called error was called; and so on. Passing a level 0 avoids the addition of error position information to the message. 

通常，error添加一些关于错误位置的信息在消息的开头。level参数指定如何获得错误位置。使用级别1（默认），错误位置是error函数被调用的地方。级别2把错误指向调用error的函数被调用的地方；如此类推。传递一个级别0避免添加额外的错误位置信息到message。

--------------------------------------------------------------------------------

_G

A global variable (not a function) that holds the global environment (that is, _G._G = _G). Lua itself does not use this variable; changing its value does not affect any environment, nor vice-versa. (Use setfenv to change environments.) 

一个全局变量（不是一个函数），它持有全局环境的（就是说，_G._G = _G）。Lua自身不使用这个变量；改变它的值不影响任何环境，反之亦然。（使用setfenv以改变环境。） 

--------------------------------------------------------------------------------

getfenv ([f])

Returns the current environment in use by the function. f can be a Lua function or a number that specifies the function at that stack level: Level 1 is the function calling getfenv. If the given function is not a Lua function, or if f is 0, getfenv returns the global environment. The default for f is 1. 

返回正在被函数使用的当前环境。f可以是一个Lua函数或一个指定那个栈级别上函数的数字：级别1是调用getfenv的函数。如果给定函数不是一个Lua函数，或者如果f为0，那么getfenv返回全局环境。f的缺省值为1。 

--------------------------------------------------------------------------------

getmetatable (object)

If object does not have a metatable, returns nil. Otherwise, if the object's metatable has a "__metatable" field, returns the associated value. Otherwise, returns the metatable of the given object. 

如果object没有一个元表，则返回nil。否则，如果object的元表拥有一个“__metatable”字段，则返回被关联的值。否则，返回给定对象的元表。 

--------------------------------------------------------------------------------

ipairs (t)

Returns three values: an iterator function, the table t, and 0, so that the construction 

返回三个值：迭代器函数、表t和0，以致使结构

     for i,v in ipairs(t) do body end

will iterate over the pairs (1,t[1]), (2,t[2]), ···, up to the first integer key absent from the table. 

将迭代键值对(1,t[1]), (2,t[2]), ……，直至不存在于表中的第一个整数键。 

--------------------------------------------------------------------------------

load (func [, chunkname])

Loads a chunk using function func to get its pieces. Each call to func must return a string that concatenates with previous results. A return of an empty string, nil, or no value signals the end of the chunk. 

加载一个chunk块，通过使用函数func以获得它的块。每个对func的调用必须返回一个字符串，它拼接前一个结果。一个空字符串，nil，或没有值的返回通知chunk块的结束。

If there are no errors, returns the compiled chunk as a function; otherwise, returns nil plus the error message. The environment of the returned function is the global environment. 

如果没有错误，则返回被编译的chunk块作为一个函数；否则，返回nil和错误消息。被返回的函数的环境是全局环境。 

chunkname is used as the chunk name for error messages and debug information. When absent, it defaults to "=(load)". 

chunkname被用作错误消息和调试信息的chunk块名。当不存在时，它缺省为“=(load)”。 

--------------------------------------------------------------------------------

loadfile ([filename])

Similar to load, but gets the chunk from file filename or from the standard input, if no file name is given. 

类似于load，但获得chunk块是从文件filename中，或者从标准输入中如果没有给定文件名。

--------------------------------------------------------------------------------

loadstring (string [, chunkname])

Similar to load, but gets the chunk from the given string. 

类似于load，但从给定字符串中获得chunk块。 

To load and run a given string, use the idiom 

为了加载和运行给定的字符串，请使用惯常用法 

     assert(loadstring(s))()

When absent, chunkname defaults to the given string. 

当不存在时，chunkname缺省为给定的字符串。 

--------------------------------------------------------------------------------

next (table [, index])

Allows a program to traverse all fields of a table. Its first argument is a table and its second argument is an index in this table. next returns the next index of the table and its associated value. When called with nil as its second argument, next returns an initial index and its associated value. When called with the last index, or with nil in an empty table, next returns nil. If the second argument is absent, then it is interpreted as nil. In particular, you can use next(t) to check whether a table is empty. 

允许一个程序遍历一个表的所有域。它的第一参数是一个表，而它的第二参数是这个表中的一个索引。next返回表的下一个索引和它关联的值。当用nil作为它的第二参数调用它时，next返回一个初始索引和它关联的值。当用最后的索引调用它，或在一个空表中用nil调用它时，next返回nil。如果第二参数不存在，那么它被解释作为nil。特别地，你可以使用next(t)来检查一个表是否为空。 

The order in which the indices are enumerated is not specified, even for numeric indices. (To traverse a table in numeric order, use a numerical for or the ipairs function.) 

索引被枚举的次序是非特定的，即使对于数字型索引。（为了用数字顺序来遍历一个表，请使用一个数字型for或ipairs函数。） 

The behavior of next is undefined if, during the traversal, you assign any value to a non-existent field in the table. You may however modify existing fields. In particular, you may clear existing fields. 

next的行为是未定义的，如果，在遍历期间，你赋予任意值给表中一个不存在域。然而你可以修改现存域。特别地，你可以清空现存域。

--------------------------------------------------------------------------------

pairs (t)

Returns three values: the next function, the table t, and nil, so that the construction 

返回三个值：next函数，表t，和nil，以致使结构

     for k,v in pairs(t) do body end

will iterate over all key–value pairs of table t. 

将一直迭代表t的所有键值对。 

See function next for the caveats of modifying the table during its traversal. 

参见函数next以获知在它遍历期间修改表的警告。

--------------------------------------------------------------------------------

pcall (f, arg1, ···)

Calls function f with the given arguments in protected mode. This means that any error inside f is not propagated; instead, pcall catches the error and returns a status code. Its first result is the status code (a boolean), which is true if the call succeeds without errors. In such case, pcall also returns all results from the call, after this first result. In case of any error, pcall returns false plus the error message. 

在保护模式下用给定的参数调用函数f。这意味着f内的任意错误不被传播；相反，pcall捕获错误并返回一个状态码。它的第一结果是状态码（一个boolean），它是true如果调用不带错误地成功。在这种情况下，pcall还返回调用的所有结果，在这个第一结果之后。在出现任意错误的情况下，pcall返回false以及错误消息。 

--------------------------------------------------------------------------------

print (···)

Receives any number of arguments, and prints their values to stdout, using the tostring function to convert them to strings. print is not intended for formatted output, but only as a quick way to show a value, typically for debugging. For formatted output, use string.format. 

接受任意数量的参数，并打印它们的值到stdout，通过使用tostring函数转换它们为字符串。print不倾向于用来格式化输出，而只是作为显示一个值的一种快速方式，特别是用于调试。对于被格式化的输出，请使用string.format。 

--------------------------------------------------------------------------------

rawequal (v1, v2)

Checks whether v1 is equal to v2, without invoking any metamethod. Returns a boolean. 

检查v1与v2是否相等，不调用任意元方法。返回一个boolean值。 

--------------------------------------------------------------------------------

rawget (table, index)

Gets the real value of table[index], without invoking any metamethod. table must be a table; index may be any value. 

获取table[index]的实际值，不调用任意元方法。table必须是一个表；index可以是任意值。 

--------------------------------------------------------------------------------

rawset (table, index, value)

Sets the real value of table[index] to value, without invoking any metamethod. table must be a table, index any value different from nil, and value any Lua value. 

设置table[index]的实际值为value，不调用任何元方法。table必须是一个表，index是不是nil的任意值，而value是任意Lua值。

This function returns table. 

这个函数返回table。

--------------------------------------------------------------------------------

select (index, ···)

If index is a number, returns all arguments after argument number index. Otherwise, index must be the string "#", and select returns the total number of extra arguments it received. 

如果index是一个数，返回参数数量index之后的所有参数。否则index必须是字符串"#"，而select返回它接收的额外参数的总数。

--------------------------------------------------------------------------------

setfenv (f, table)

Sets the environment to be used by the given function. f can be a Lua function or a number that specifies the function at that stack level: Level 1 is the function calling setfenv. setfenv returns the given function. 

设置要被给定函数使用的环境。f可以是一个Lua函数或一个指定那个栈级别上函数的数：级别1是调用setfenv的函数。setfenv返回给定函数。 

As a special case, when f is 0 setfenv changes the environment of the running thread. In this case, setfenv returns no values. 

作为一个特殊情况，当f是0时，setfenv改变正在运行的线程的环境。在这种情况下，setfenv不返回值。 

--------------------------------------------------------------------------------

setmetatable (table, metatable)

Sets the metatable for the given table. (You cannot change the metatable of other types from Lua, only from C.) If metatable is nil, removes the metatable of the given table. If the original metatable has a "__metatable" field, raises an error. 

设置给定表的元表。（你无法修改来自Lua的其它类型的元表，只能修改来自C的。）如果元表是nil，移除给定表的元表。如果原来的元表拥有一个"__metatable"域，则触发一个错误。

This function returns table. 

这个函数返回table。

--------------------------------------------------------------------------------

tonumber (e [, base])

Tries to convert its argument to a number. If the argument is already a number or a string convertible to a number, then tonumber returns this number; otherwise, it returns nil. 

尝试转换它的参数为一个数。如果参数已经是一个数或一个可转换为数的字符串，那么tonumber返回这个数；否则，它返回nil。 

An optional argument specifies the base to interpret the numeral. The base may be any integer between 2 and 36, inclusive. In bases above 10, the letter 'A' (in either upper or lower case) represents 10, 'B' represents 11, and so forth, with 'Z' representing 35. In base 10 (the default), the number can have a decimal part, as well as an optional exponent part (see §2.1). In other bases, only unsigned integers are accepted. 

一个可选参数指定解释该数的基数（注：进制）。基数可能是2和36之间的任意整数，包含2和36在内。在10以上的基的情况下，字母'A'（不管大小写）代表10，'B'代表11，如此类推，直至'Z'代表35。在基数10的情况下，数可以拥有一个小数部分，还有一个可选的指数部分（见§2.1）。在其它基数的情况下，只接受无符号整数。

--------------------------------------------------------------------------------

tostring (e)

Receives an argument of any type and converts it to a string in a reasonable format. For complete control of how numbers are converted, use string.format. 

接收一个任意类型的参数，并以合理的格式转换它为一个字符串。想取得数是如何转换的完全控制，请使用string.format。 

If the metatable of e has a "__tostring" field, then tostring calls the corresponding value with e as argument, and uses the result of the call as its result. 

如果e的元表有"__tostring"字段，那么tostring以e作为参数来调用对应的值，并使用该调用的结果作为它的结果。 

--------------------------------------------------------------------------------

type (v)

Returns the type of its only argument, coded as a string. The possible results of this function are "nil" (a string, not the value nil), "number", "string", "boolean", "table", "function", "thread", and "userdata". 

返回它唯一参数的类型，编码成一个字符串。这个函数的可能结果有"nil"（一个字符串，不是值nil），"number"，"string"，"boolean"，"table"，"function"，"thread"，和"userdata"。 

--------------------------------------------------------------------------------

unpack (list [, i [, j]])

Returns the elements from the given table. This function is equivalent to 

返回来自给定表的元素。这个函数等价于

     return list[i], list[i+1], ···, list[j]

except that the above code can be written only for a fixed number of elements. By default, i is 1 and j is the length of the list, as defined by the length operator (see §2.5.5). 

除了写上面的代码只能用于一个固定数量的元素。缺省，i是1而j是列表的长度，正如长度操作符（见§2.5.5）定义的那样。

     return list[i], list[i+1], ···, list[j]

--------------------------------------------------------------------------------

_VERSION

A global variable (not a function) that holds a string containing the current interpreter version. The current contents of this variable is "Lua 5.1". 

一个全局变量（不是一个函数），它持有包含当前解释器版本的一个字符串。这个变量的当前内容是“Lua 5.1”。 

--------------------------------------------------------------------------------

xpcall (f, err)

This function is similar to pcall, except that you can set a new error handler. 

这个函数类似于pcall，除了你可以设置一个新的错误处理器。 

xpcall calls function f in protected mode, using err as the error handler. Any error inside f is not propagated; instead, xpcall catches the error, calls the err function with the original error object, and returns a status code. Its first result is the status code (a boolean), which is true if the call succeeds without errors. In this case, xpcall also returns all results from the call, after this first result. In case of any error, xpcall returns false plus the result from err. 

xpcall以保护模式调用函数f，使用err作为错误处理器。在f中的任意错误不被传播；相反，xpcall捕捉该错误，用原始错误对象调用err函数，并返回一个状态码。它的第一结果是状态码（一个boolean值），它是true如果调用不带错误地成功。在这种情况下，xpcall还返回来自该调用所有结果，在这个第一结果之后。在有任意错误的情况下，xpcall返回false以及来自err的结果。

-----------------------------------------

参考自：
1. Lua 5.1 参考手册 （云风译）
http://www.codingnow.com/2000/download/lua_manual.html
2. hshqcn
hshqcn  
