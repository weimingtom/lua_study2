【翻译】(LRM5.1-8)调试接口(3.8)

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

3.8 - The Debug Interface

3.8 - 调试接口

Lua has no built-in debugging facilities. Instead, it offers a special interface by means of functions and hooks. This interface allows the construction of different kinds of debuggers, profilers, and other tools that need "inside information" from the interpreter. 

Lua没有内置的调试设施。取而代之，它通过一些函数和钩子来提供一个特殊接口。这个接口允许不同种类的调试器、性能剖析工具，以及其它需要解析器中“内部信息”的工具的构造。

--------------------------------------------------------------------------------

lua_Debug

typedef struct lua_Debug {
  int event;
  const char *name;           /* (n) */
  const char *namewhat;       /* (n) */
  const char *what;           /* (S) */
  const char *source;         /* (S) */
  int currentline;            /* (l) */
  int nups;                   /* (u) number of upvalues */
  int linedefined;            /* (S) */
  int lastlinedefined;        /* (S) */
  char short_src[LUA_IDSIZE]; /* (S) */
  /* private part */
  other fields
} lua_Debug;

（注：翻译如下：
typedef struct lua_Debug {
  int event;
  const char *name;           /* (n) */
  const char *namewhat;       /* (n) */
  const char *what;           /* (S) */
  const char *source;         /* (S) */
  int currentline;            /* (l) */
  int nups;                   /* (u) 上值的数量 */
  int linedefined;            /* (S) */
  int lastlinedefined;        /* (S) */
  char short_src[LUA_IDSIZE]; /* (S) */
  /* 私有部分 */
  其它字段
} lua_Debug;
）

A structure used to carry different pieces of information about an active function. lua_getstack fills only the private part of this structure, for later use. To fill the other fields of lua_Debug with useful information, call lua_getinfo. 

用于携带关于一个激活函数的不同信息的一个结构体。lua_getstack只填充这个结构体的私有部分，供以后使用。要想用有用的信息填充lua_Debug的其它字段，请调用lua_getinfo。 

The fields of lua_Debug have the following meaning: 

lua_Debug的字段拥有以下含义： 

source: If the function was defined in a string, then source is that string. If the function was defined in a file, then source starts with a '@' followed by the file name. 

source: 如果该函数被定义在一个字符串中，则source是那个字符串。如果该函数被定义在一个文件中，则source以'@'开头后跟文件名。 

short_src: a "printable" version of source, to be used in error messages. 

short_src：source字段的“可打印”版本，被用在错误消息中。 

linedefined: the line number where the definition of the function starts. 

linedefined：函数定义开始所在的行号。 

lastlinedefined: the line number where the definition of the function ends. 

lastlinedefined：函数定义结束所在的行号。 

what: the string "Lua" if the function is a Lua function, "C" if it is a C function, "main" if it is the main part of a chunk, and "tail" if it was a function that did a tail call. In the latter case, Lua has no other information about the function. 

what：字符串"Lua"如果该函数是一个Lua函数，"C"如果它是一个C函数，"main"如果它是一个chunk块的主要部分，以及"tail"如果它是一个执行尾调用的函数。在最后一种情况下，Lua没有关于该函数的其它信息。 

currentline: the current line where the given function is executing. When no line information is available, currentline is set to -1. 

currentline：给定函数正在执行在的当前行。当没有可用的行信息时，currentline被设置为-1。 

name: a reasonable name for the given function. Because functions in Lua are first-class values, they do not have a fixed name: some functions can be the value of multiple global variables, while others can be stored only in a table field. The lua_getinfo function checks how the function was called to find a suitable name. If it cannot find a name, then name is set to NULL. 

name：给定函数的合适名称。因为在Lua中函数是第一类值，所以它们没有一个固定的名称：一些函数可以是多个全局变量的值，而其它可以只存储于一个表的字段中。lua_getinfo函数检查该函数是如何被调用的以找到一个适合的名称。如果它无法找到一个名称，则name被设置为NULL。 

namewhat: explains the name field. The value of namewhat can be "global", "local", "method", "field", "upvalue", or "" (the empty string), according to how the function was called. (Lua uses the empty string when no other option seems to apply.) 

namewhat：解释name字段。namewhat的值可以是"global"，"local"，"method"，"field"，"upvalue"，或""（空字符串），根据函数是如何被调用的。（Lua使用空串，当似乎没有其它选项要应用时。） 

nups: the number of upvalues of the function. 

nups：函数的上值的数量。 

--------------------------------------------------------------------------------

lua_gethook
[-0, +0, -] 

lua_Hook lua_gethook (lua_State *L);

Returns the current hook function. 

返回当前钩子函数。 

--------------------------------------------------------------------------------

lua_gethookcount
[-0, +0, -] 

int lua_gethookcount (lua_State *L);

Returns the current hook count. 

返回当前钩子数量。

--------------------------------------------------------------------------------

lua_gethookmask
[-0, +0, -] 

int lua_gethookmask (lua_State *L);

Returns the current hook mask. 

返回当前钩子掩码。 

--------------------------------------------------------------------------------

lua_getinfo
[-(0|1), +(0|1|2), m] 

int lua_getinfo (lua_State *L, const char *what, lua_Debug *ar);

Returns information about a specific function or function invocation. 

返回关于一个指定函数或函数调用的信息。

To get information about a function invocation, the parameter ar must be a valid activation record that was filled by a previous call to lua_getstack or given as argument to a hook (see lua_Hook). 

想要获得关于一个函数调用的信息，参数ar必须是一个可用的激活记录，它被前一次对lua_getstack的调用填充或被给定作为传给一个钩子的参数（见lua_Hook）。

To get information about a function you push it onto the stack and start the what string with the character '>'. (In that case, lua_getinfo pops the function in the top of the stack.) For instance, to know in which line a function f was defined, you can write the following code: 

想要获得关于一个函数的信息，你压入它到栈上并让what字符串以字符'>'开始。（在那种情况下，lua_getinfo从栈顶中弹出该函数。）例如，想要知道一个函数f被定义在哪一行中，你可以编写以下代码：

     lua_Debug ar;
     lua_getfield(L, LUA_GLOBALSINDEX, "f");  /* get global 'f' */
     lua_getinfo(L, ">S", &ar);
     printf("%d\n", ar.linedefined);

（翻译如下：
lua_Debug ar;
lua_getfield(L, LUA_GLOBALSINDEX, "f");  /* 获取全局变量'f' */
lua_getinfo(L, ">S", &ar);
printf("%d\n", ar.linedefined);
）

Each character in the string what selects some fields of the structure ar to be filled or a value to be pushed on the stack: 

字符串what中的每个字符选择结构体ar中要被填充的一些字段或一个要被压入到栈上的值：

'n': fills in the field name and namewhat; 

'n': 填充字段name和namewhat； 

'S': fills in the fields source, short_src, linedefined, lastlinedefined, and what; 

'S': 填充字段source，short_src，linedefined，lastlinedefined，和what； 

'l': fills in the field currentline; 

'l': 填充字段currentline； 

'u': fills in the field nups; 

'u': 填充字段nups；

'f': pushes onto the stack the function that is running at the given level; 

'f': 压入函数到栈上，它正在运行在给定级别上； 

'L': pushes onto the stack a table whose indices are the numbers of the lines that are valid on the function. (A valid line is a line with some associated code, that is, a line where you can put a break point. Non-valid lines include empty lines and comments.) 

'L': 压入一个表到栈上，它的索引是在函数上可用的行数（一个可用行是一个带一些关联代码的行，就是说，你可以在它上面放置一个断点的行。非可用行包括空行和注释。）

This function returns 0 on error (for instance, an invalid option in what). 

这个函数在出错时返回0（例如，what中有一个不可用选项）

--------------------------------------------------------------------------------

lua_getlocal
[-0, +(0|1), -] 

const char *lua_getlocal (lua_State *L, lua_Debug *ar, int n);

Gets information about a local variable of a given activation record. The parameter ar must be a valid activation record that was filled by a previous call to lua_getstack or given as argument to a hook (see lua_Hook). The index n selects which local variable to inspect (1 is the first parameter or active local variable, and so on, until the last active local variable). lua_getlocal pushes the variable's value onto the stack and returns its name. 

获得关于一个给定激活记录的一个局部变量的信息。参数ar必须是一个可用的激活记录，它被前一次对lua_getstack的调用所填充，或被给定作为一个钩子的参数（见lua_Hook）。索引n选择要检查哪个局部变量（1是第一个参数或激活的局部变量，如此类推，直至最后一个激活的局部变量）。lua_getlocal压入变量的值到栈上并返回它的名称。 

Variable names starting with '(' (open parentheses) represent internal variables (loop control variables, temporaries, and C function locals). 

以'('（左小括号）开头的变量名称代表内部变量（循环控制变量，临时变量，和C函数局部变量）。 

Returns NULL (and pushes nothing) when the index is greater than the number of active local variables. 

返回NULL（且不压入东西）当索引大于激活局部变量的数量时。 

--------------------------------------------------------------------------------

lua_getstack
[-0, +0, -] 

int lua_getstack (lua_State *L, int level, lua_Debug *ar);

Get information about the interpreter runtime stack. 

得到关于解释器运行时栈的信息。 

This function fills parts of a lua_Debug structure with an identification of the activation record of the function executing at a given level. Level 0 is the current running function, whereas level n+1 is the function that has called level n. When there are no errors, lua_getstack returns 1; when called with a level greater than the stack depth, it returns 0. 

这个函数填充一个lua_Debug结构体的部分，使用在一个给定级别上执行的函数的激活记录的一个标识符。级别0是当前正在运行的函数，而级别n+1是曾经调用级别n的函数。当没有错误时，lua_getstack返回1；当使用一个大于栈深度的级别来调用时，它返回0。

--------------------------------------------------------------------------------

lua_getupvalue
[-0, +(0|1), -] 

const char *lua_getupvalue (lua_State *L, int funcindex, int n);

Gets information about a closure's upvalue. (For Lua functions, upvalues are the external local variables that the function uses, and that are consequently included in its closure.) lua_getupvalue gets the index n of an upvalue, pushes the upvalue's value onto the stack, and returns its name. funcindex points to the closure in the stack. (Upvalues have no particular order, as they are active through the whole function. So, they are numbered in an arbitrary order.) 

得到关于一个闭包的上值的信息。（对于Lua函数，上值是函数使用的外部局部变量，并因此它被包含在它的闭包中）lua_getupvalue得到一个上值的索引n，压入上值的值在栈上，并返回它的名称。funcindex指向栈中的闭包。（上值没有特定的顺序，因为它们在整个函数中一直都是激活的，所以，它们按任意顺序被编号。） 

Returns NULL (and pushes nothing) when the index is greater than the number of upvalues. For C functions, this function uses the empty string "" as a name for all upvalues. 

返回NULL（且不压入东西）当索引大于上值的数量时。对于C函数，这个函数使用空字符串""作为所有上值的名称。 

--------------------------------------------------------------------------------

lua_Hook

typedef void (*lua_Hook) (lua_State *L, lua_Debug *ar);

Type for debugging hook functions. 

调试钩子函数的类型。 

Whenever a hook is called, its ar argument has its field event set to the specific event that triggered the hook. Lua identifies these events with the following constants: LUA_HOOKCALL, LUA_HOOKRET, LUA_HOOKTAILRET, LUA_HOOKLINE, and LUA_HOOKCOUNT. Moreover, for line events, the field currentline is also set. To get the value of any other field in ar, the hook must call lua_getinfo. For return events, event can be LUA_HOOKRET, the normal value, or LUA_HOOKTAILRET. In the latter case, Lua is simulating a return from a function that did a tail call; in this case, it is useless to call lua_getinfo. 

每当一个钩子被调用时，它的ar参数把它的字段event设置为触发该钩子的特定事件。Lua用以下常量标识这些事件：LUA_HOOKCALL、LUA_HOOKRET、LUA_HOOKTAILRET、LUA_HOOKLINE和LUA_HOOKCOUNT。此外，对于行事件，字段currentline也被设置。要想得到ar中其它任意字段的值，钩子必须调用lua_getinfo。对于返回事件，event可以是LUA_HOOKRET，通常的值，或是LUA_HOOKTAILRET。对于后者情况，Lua正在模拟一个来自执行一个尾调用的一个函数的返回；在这种情况下，调用lua_getinfo是无用的。 

While Lua is running a hook, it disables other calls to hooks. Therefore, if a hook calls back Lua to execute a function or a chunk, this execution occurs without any calls to hooks. 

当Lua正在运行一个钩子时，它禁用其它钩子调用。因此，如果一个钩子回调Lua以执行一个函数或一个chunk块，那么这个执行不带任何钩子地发生。 

--------------------------------------------------------------------------------

lua_sethook
[-0, +0, -] 

int lua_sethook (lua_State *L, lua_Hook f, int mask, int count);

Sets the debugging hook function. 

设置调试钩子函数。 

Argument f is the hook function. mask specifies on which events the hook will be called: it is formed by a bitwise or of the constants LUA_MASKCALL, LUA_MASKRET, LUA_MASKLINE, and LUA_MASKCOUNT. The count argument is only meaningful when the mask includes LUA_MASKCOUNT. For each event, the hook is called as explained below: 

参数f是钩子函数。参数mask指定hook将被调用在哪个事件上：它由常量LUA_MASKCALL、LUA_MASKRET、LUA_MASKLINE和LUA_MASKCOUNT的按位或组成。count参数才有意义，仅当mask包含LUA_MASKCOUNT。对于每个事件，该钩子被调用正如下面所解释的：

The call hook: is called when the interpreter calls a function. The hook is called just after Lua enters the new function, before the function gets its arguments. 

调用钩子：被调用当解释器调用一个函数时。钩子被调用，正好在Lua进入该新函数之后、在函数得到它的参数之前。 

The return hook: is called when the interpreter returns from a function. The hook is called just before Lua leaves the function. You have no access to the values to be returned by the function. 

返回钩子：被调用当解释器从一个函数中返回时。钩子被调用在Lua离开函数之前。你没有要被函数返回的值的访问权。 

The line hook: is called when the interpreter is about to start the execution of a new line of code, or when it jumps back in the code (even to the same line). (This event only happens while Lua is executing a Lua function.) 

行钩子：被调用当解释器打算开始一个新代码行的执行，或当它跳回到代码中（甚至是同一行）时。（这个事件只发生在当Lua正执行一个Lua函数时。） 

The count hook: is called after the interpreter executes every count instructions. (This event only happens while Lua is executing a Lua function.) 

个数（注：指令个数）钩子：被调用在解释器执行每count条指令后。（这个事件只发生在当Lua正在执行一个Lua函数时。） 

A hook is disabled by setting mask to zero. 

通过设定参数mask为0来禁用一个钩子。

--------------------------------------------------------------------------------

lua_setlocal
[-(0|1), +0, -] 

const char *lua_setlocal (lua_State *L, lua_Debug *ar, int n);

Sets the value of a local variable of a given activation record. Parameters ar and n are as in lua_getlocal (see lua_getlocal). lua_setlocal assigns the value at the top of the stack to the variable and returns its name. It also pops the value from the stack. 

设置一个局部变量的值为一个给定激活记录。参数ar和n与lua_getlocal中的相同（见lua_getlocal）。lua_setlocal赋予栈顶的值到该变量并返回它的名称。它还从栈中弹出值。 

Returns NULL (and pops nothing) when the index is greater than the number of active local variables. 

返回NULL（且不弹出东西）当索引大于激活局部变量的数量时。 

--------------------------------------------------------------------------------

lua_setupvalue
[-(0|1), +0, -] 

const char *lua_setupvalue (lua_State *L, int funcindex, int n);

Sets the value of a closure's upvalue. It assigns the value at the top of the stack to the upvalue and returns its name. It also pops the value from the stack. Parameters funcindex and n are as in the lua_getupvalue (see lua_getupvalue). 

设置一个闭包上值的值。它赋予栈顶的值给上值并返回它的名称。它还从栈中弹出值。参数funcindex和n和lua_getupvalue中的相同（见lua_getupvalue）。 

Returns NULL (and pops nothing) when the index is greater than the number of upvalues. 

返回NULL（且不弹出东西）当索引大于上值的数量时。 

-----------------------------------------

参考自：
1. Lua 5.1 参考手册 （云风译）
http://www.codingnow.com/2000/download/lua_manual.html
2. hshqcn
hshqcn  

