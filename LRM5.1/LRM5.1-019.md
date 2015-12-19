## 【翻译】(LRM5.1-19)调试库(5.9)、Lua的独立程序(6)    

See also:  
http://www.lua.org/manual/5.1/manual.html  

原文见  
http://www.lua.org/manual/5.1/manual.html  

-----------------------------------------  

## Lua 5.1 Reference Manual   

## Lua 5.1参考手册  

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes   

作者：Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes  

Copyright ? 2006-2008 Lua.org, PUC-Rio. Freely available under the terms of the Lua license.   

版权所有 (c) 2006-2008 Lua.org, PUC-Rio. 根据Lua许可证自由地**（注：免费）**可用  

-----------------------------------------  

## 5.9 - The Debug Library  

## 5.9 - 调试库  

This library provides the functionality of the debug interface to Lua programs. You should exert care when using this library. The functions provided here should be used exclusively for debugging and similar tasks, such as profiling. Please resist the temptation to use them as a usual programming tool: they can be very slow. Moreover, several of these functions violate some assumptions about Lua code (e.g., that variables local to a function cannot be accessed from outside or that userdata metatables cannot be changed by Lua code) and therefore can compromise otherwise secure code.   

这个库提供Lua程序的调试界面工具。用这个库时应该尽量小心。这里提供的函数应该仅仅用于调试和相关的任务，例如性能剖析。请避免像普通的编程工具那样滥用它们：它们可能会变得非常慢。此外，这几个函数破坏关于Lua代码部分的假设（例如，函数的局部变量不能在外部访问，或者userdata的元表不能被Lua代码修改），并因此可能会危及其它安全的代码。  

All functions in this library are provided inside the debug table. All functions that operate over a thread have an optional first argument which is the thread to operate over. The default is always the current thread.   

这个库的所有函数在debug表中提供。所有跨线程的函数带有一个可选的第一参数，允许传入需要操作的线程。默认总是当前线程。  

--------------------------------------------------------------------------------  

debug.debug ()  

Enters an interactive mode with the user, running each string that the user enters. Using simple commands and other debug facilities, the user can inspect global and local variables, change their values, evaluate expressions, and so on. A line containing only the word cont finishes this function, so that the caller continues its execution.   

进入同用户的交互模式，运行用户输入的每个字符串。使用简单的命令和其他调试设备，用户能够检查全局和局部变量，改变它们的值，计算表达式，等等。只含有单词cont的行终止该函数，这样调用者继续它的执行。   

Note that commands for debug.debug are not lexically nested within any function, and so have no direct access to local variables.   

注意，debug.debug的命令不是任何函数的内部词汇，因此没有对本地变量的直接访问。   

--------------------------------------------------------------------------------  

debug.getfenv (o)  

Returns the environment of object o.   

返回对象o的环境表。  

--------------------------------------------------------------------------------  
 
debug.gethook ([thread])  

Returns the current hook settings of the thread, as three values: the current hook function, the current hook mask, and the current hook count (as set by the debug.sethook function).   

返回线程的当前钩子设置，有三个值：当前钩子函数，当前钩子掩码，以及当前钩子数目（对应debug.sethook函数的设置）。  

--------------------------------------------------------------------------------  

debug.getinfo ([thread,] function [, what])  

Returns a table with information about a function. You can give the function directly, or you can give a number as the value of function, which means the function running at level function of the call stack of the given thread: level 0 is the current function (getinfo itself); level 1 is the function that called getinfo; and so on. If function is a number larger than the number of active functions, then getinfo returns nil.   

返回关于一个函数的带信息的表。你可以直接指定函数，或者给定一个代表函数运行于所给线程的调用堆栈的function层的数作为函数的值：层0是当前函数（getinfo自身）；层1是调用getinfo的函数；如此类推。如果function是一个大于激活函数个数的数，那么getinfo返回nil。  

The returned table can contain all the fields returned by lua_getinfo, with the string what describing which fields to fill in. The default for what is to get all information available, except the table of valid lines. If present, the option 'f' adds a field named func with the function itself. If present, the option 'L' adds a field named activelines with the table of valid lines.   

返回的表可能包含所有lua_getinfo所返回的域，根据描述要填充哪些域的字符串what。what缺省是获取所有可用信息，除了合法行的表以外。如果存在，选项'f'添加一个值为function自身的名叫func的域。如果存在，选项'L'添加值为包含合法行的表的名叫acivelines的域。  

For instance, the expression debug.getinfo(1,"n").name returns a table with a name for the current function, if a reasonable name can be found, and the expression debug.getinfo(print) returns a table with all available information about the print function.   

例如，表达式debug.getinfo(1,"n").name返回一个当前函数的带有name名称（？）的一个表，如果一个合理的名称可以被找到，则表达式debug.getinfo(print)会返回一个带有关于print函数的所有可用信息的表。  

--------------------------------------------------------------------------------  

debug.getlocal ([thread,] level, local)  

This function returns the name and the value of the local variable with index local of the function at level level of the stack. (The first parameter or local variable has index 1, and so on, until the last active local variable.) The function returns nil if there is no local variable with the given index, and raises an error when called with a level out of range. (You can call debug.getinfo to check whether the level is valid.)   

这个函数返回名称以及带索引local的在堆栈level层的局部变量的值。（第一个参数或局部变量索引为1，如此类推，直到最后激活的局部变量）如果参数索引处没有局部变量，这个函数返回空，并且在一层范围以外调用时引生一个错误。（你可以调用debug.getinfo去检查level是否合法。）  

Variable names starting with '(' (open parentheses) represent internal variables (loop control variables, temporaries, and C function locals).   

变量名以'('（左括号）开始表示内部变量（循环控制变量，临时变量，和C函数局部变量）  

--------------------------------------------------------------------------------  

debug.getmetatable (object)  

Returns the metatable of the given object or nil if it does not have a metatable.   

返回参数对象的元表，如果没有元表则返回空。   

--------------------------------------------------------------------------------  

debug.getregistry ()  

Returns the registry table (see §3.5).   

返回注册表(参考§3.5).   

--------------------------------------------------------------------------------  

debug.getupvalue (func, up)  

This function returns the name and the value of the upvalue with index up of the function func. The function returns nil if there is no upvalue with the given index.   

这个函数返回名称以及函数func的上的带索引upvalue的值。如果指定的索引没有upvalue的话，这个函数返回空值。  

--------------------------------------------------------------------------------  

debug.setfenv (object, table)  

Sets the environment of the given object to the given table. Returns object.   

设置所给对象的环境到给定的表中。返回对象.   

--------------------------------------------------------------------------------  

debug.sethook ([thread,] hook, mask [, count])  

Sets the given function as a hook. The string mask and the number count describe when the hook will be called. The string mask may have the following characters, with the given meaning:   

把所给函数设置为钩子。字符串mask和数count描述钩子何时被调用。字符串mask可以拥有以下字符，带有所给出的意思：  

"c": the hook is called every time Lua calls a function;   

"c": 钩子在每次Lua调用一个函数时调用；   

"r": the hook is called every time Lua returns from a function;   

"r": 钩子在每次Lua从一个函数返回时调用；   

"l": the hook is called every time Lua enters a new line of code.   

"l": 钩子在每次Lua进入新的一行代码时调用。  

With a count different from zero, the hook is called after every count instructions.   

当count不等于0时，钩子在每次count到达（？）时被调用。   

When called without arguments, debug.sethook turns off the hook.   

当不带参数调用时，debug.sethook关闭钩子。  

When the hook is called, its first parameter is a string describing the event that has triggered its call: "call", "return" (or "tail return", when simulating a return from a tail call), "line", and "count". For line events, the hook also gets the new line number as its second parameter. Inside a hook, you can call getinfo with level 2 to get more information about the running function (level 0 is the getinfo function, and level 1 is the hook function), unless the event is "tail return". In this case, Lua is only simulating the return, and a call to getinfo will return invalid data.   

当钩子被调用时，它第一个参数是一个描述触发这次调用的事件的字符串："call","return"(或"tail return"，当模拟一次从尾调用产生的返回），"line"，以及"count"。对于行事件，钩子还可以获得新的行数作为其第二参数。在钩子内部，你可以调用带层2的getinfo去获得更多关于运行中函数的信息（0层是getinfo函数，1层是钩子函数），除非事件是“尾返回”。在这种情况下，Lua只是模拟返回，而调用getinfo将返回非法的数据。  

--------------------------------------------------------------------------------  

debug.setlocal ([thread,] level, local, value)  

This function assigns the value value to the local variable with index local of the function at level level of the stack. The function returns nil if there is no local variable with the given index, and raises an error when called with a level out of range. (You can call getinfo to check whether the level is valid.) Otherwise, it returns the name of the local variable.   

这个函数把值value赋给在索引local处的局部变量给堆栈中处于level层的函数。如果所给的索引处没有局部变量，则函数返回nil，并且当调用超出范围的一个层时引发一个错误。（你可以调用getinfo去检查level是否合法。）否则，它返回局部变量的名称。  

--------------------------------------------------------------------------------  

debug.setmetatable (object, table)  

Sets the metatable for the given object to the given table (which can be nil).   

设置所给对象的元表为所给的表（可以为nil）。   

--------------------------------------------------------------------------------  

debug.setupvalue (func, up, value)  

This function assigns the value value to the upvalue with index up of the function func. The function returns nil if there is no upvalue with the given index. Otherwise, it returns the name of the upvalue.   

这个函数把值value赋给函数func以上带索引up的upvalue。如果所给索引上没有upvalue则这个函数返回nil。否则，返回upvalue的名称。  

--------------------------------------------------------------------------------  

debug.traceback ([thread,] [message] [, level])  

Returns a string with a traceback of the call stack. An optional message string is appended at the beginning of the traceback. An optional level number tells at which level to start the traceback (default is 1, the function calling traceback).   

返回一个带有调用堆栈的traceback信息的字符串。一个可选的message字符串追加在traceback信息的开头。一个可选的level数指明从哪一层开始traceback（默认是1，该函数调用的traceback）。  

## 6 - Lua Stand-alone  

## 6 - Lua的独立程序  

Although Lua has been designed as an extension language, to be embedded in a host C program, it is also frequently used as a stand-alone language. An interpreter for Lua as a stand-alone language, called simply lua, is provided with the standard distribution. The stand-alone interpreter includes all standard libraries, including the debug library. Its usage is:   

虽然Lua被设计为一种嵌入C宿主程序的扩展语言，但它还常常被当作一种独立运行的语言来使用。Lua解释器作为一种可独立运行的语言，简称lua，作为标准发布的一部分提供。独立运行的解释器包含所有标准库，包含调试库。它的用法是：  

     lua [options] [script [args]]  

The options are:   

其中options选项包括  

-e stat: executes string stat;   

-e stat: 执行字符串stat；   

-l mod: "requires" mod;   

-l mod: 包含库mod；   

-i: enters interactive mode after running script;   

-i: 在执行脚本后进入交互模式；   

-v: prints version information;   

-v: 打印版本信息；   

--: stops handling options;   

--: 停止处理选项；   

-: executes stdin as a file and stops handling options.   

-: 把stdin作为文件来执行，并且停止处理选项。  

After handling its options, lua runs the given script, passing to it the given args as string arguments. When called without arguments, lua behaves as lua -v -i when the standard input (stdin) is a terminal, and as lua - otherwise.   

在处理选项后，lua运行所给的脚本，传递所给的args作为它的字符串参数。当不带参数调用时，如果标准输入（stdin）是一个终端则lua的行为如lua -v -i那样，否则如lua -那样。  

Before running any argument, the interpreter checks for an environment variable LUA_INIT. If its format is @filename, then lua executes the file. Otherwise, lua executes the string itself.   

在执行任何参数前，解释器会检查环境变量LUA_INIT。如果它的格式是@文件名，那么lua执行该文件。否则，lua执行字符串本身。  

All options are handled in order, except -i. For instance, an invocation like   

所有选项都是按顺序处理，除了-i以外。例如，像这样的调用    

     $ lua -e'a=1' -e 'print(a)' script.lua  

will first set a to 1, then print the value of a (which is '1'), and finally run the file script.lua with no arguments. (Here $ is the shell prompt. Your prompt may be different.)   

首先把a设置为1，然后打印a的值（即'1'），最后不带参数地运行文件script.lua。（这里$是shell命令提示符。你的提示可能会不同。）  

Before starting to run the script, lua collects all arguments in the command line in a global table called arg. The script name is stored at index 0, the first argument after the script name goes to index 1, and so on. Any arguments before the script name (that is, the interpreter name plus the options) go to negative indices. For instance, in the call   

在开始运行脚本前，Lua把命令行中的所有参数收集到一个叫arg的全局表中。脚本名存储在索引0，脚本名后的第一个参数为索引1，如此类推。任何在脚本名前的参数（即解释器名和选项参数）以负索引排列。例如在如下调用中：  

     $ lua -la b.lua t1 t2  

the interpreter first runs the file a.lua, then creates a table   

解析器首先运行文件a.lua，然后创建一个表  

     arg = { [-2] = "lua", [-1] = "-la",  
             [0] = "b.lua",  
             [1] = "t1", [2] = "t2" }  

and finally runs the file b.lua. The script is called with arg[1], arg[2], ··· as arguments; it can also access these arguments with the vararg expression '...'.   

最后运行文件b.lua。该脚本以arg[1]，arg[2]，···作为参数来调用；它也可以通过变长参数表达式'...'访问这些参数。  

In interactive mode, if you write an incomplete statement, the interpreter waits for its completion by issuing a different prompt.   

在交互模式下，如果你写了一个不完整的语句，解释器通过发出不同的提示符等待它的完成。  

If the global variable _PROMPT contains a string, then its value is used as the prompt. Similarly, if the global variable _PROMPT2 contains a string, its value is used as the secondary prompt (issued during incomplete statements). Therefore, both prompts can be changed directly on the command line or in any Lua programs by assigning to _PROMPT. See the next example:   

如果全局变量_PROMPT包含一个字符串，那么它的值用作提示符。同样，如果全局变量_PROMPT2包含一个字符串，那么它的值是用作第二提示符（在出现不完整语句时发出）。因此，可以通过给_PROMPT赋值而直接在命令行中或者任何Lua程序中改变这两个提示符。看下面的例子：  

     $ lua -e"_PROMPT='myprompt> '" -i  

(The outer pair of quotes is for the shell, the inner pair is for Lua.) Note the use of -i to enter interactive mode; otherwise, the program would just end silently right after the assignment to _PROMPT.   

（外引号对用于shell，内引号对用于lua。）注意使用-i进入交互模式；否则程序在赋值给_PROMPT后就安静地结束。  

To allow the use of Lua as a script interpreter in Unix systems, the stand-alone interpreter skips the first line of a chunk if it starts with #. Therefore, Lua scripts can be made into executable programs by using chmod +x and the #! form, as in   

为了让Lua在Unix系统中作为脚本解释器来使用，如果以#开始，独立运行解释器会跳过chunk块的第一行。因此，Lua脚本可以通过使用chmod +x和#!形式变成可执行程序，例如  

     #!/usr/local/bin/lua  

(Of course, the location of the Lua interpreter may be different in your machine. If lua is in your PATH, then   

（当然，Lua解释器的位置可能和你机器的不同。如果lua在你的PATH中，那么  

     #!/usr/bin/env lua  

is a more portable solution.)   

是一种更方便的解决方案。）   
