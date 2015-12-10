【翻译】(LRM5.1-12)辅助库(4)

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

4 - The Auxiliary Library

4 - 辅助库

The auxiliary library provides several convenient functions to interface C with Lua. While the basic API provides the primitive functions for all interactions between C and Lua, the auxiliary library provides higher-level functions for some common tasks. 

辅助库提供几个便利函数以对接C和Lua。虽然基本API为C和Lua之间的所有接口而提供原始函数，但辅助库为一些一般任务提供较高层次的函数。

All functions from the auxiliary library are defined in header file lauxlib.h and have a prefix luaL_. 

来自辅助库的所有函数被定义在头文件lauxlib.h中并拥有一个前缀luaL_。 

All functions in the auxiliary library are built on top of the basic API, and so they provide nothing that cannot be done with this API. 

辅助库中的所有函数被建立在基本API上，所以它们不提供使用这套API无法处理的东西。 

Several functions in the auxiliary library are used to check C function arguments. Their names are always luaL_check* or luaL_opt*. All of these functions throw an error if the check is not satisfied. Because the error message is formatted for arguments (e.g., "bad argument #1"), you should not use these functions for other stack values. 

在辅助库中几个函数被用于检查C函数参数。它们的名称总是luaL_check*或luaL_opt*。所有这些函数抛出一个错误，如果检查不被满足。因为错误消息为参数而被格式化（例如，“不正确的参数#1”，所以你不应该为其它栈值而使用这些函数。

4.1 - Functions and Types

4.1 - 函数与类型

Here we list all functions and types from the auxiliary library in alphabetical order. 

这里我们按字母顺序列举来自辅助库的所有函数和类型 

--------------------------------------------------------------------------------

luaL_addchar
[-0, +0, m] 

void luaL_addchar (luaL_Buffer *B, char c);

Adds the character c to the buffer B (see luaL_Buffer). 

添加字符c到缓冲B（见luaL_Buffer）。

--------------------------------------------------------------------------------

luaL_addlstring
[-0, +0, m] 

void luaL_addlstring (luaL_Buffer *B, const char *s, size_t l);

Adds the string pointed to by s with length l to the buffer B (see luaL_Buffer). The string may contain embedded zeros. 

添加s指向的长度为l的字符串到缓冲区B（见luaL_Buffer）。该字符串可以包含内嵌0。

--------------------------------------------------------------------------------

luaL_addsize
[-0, +0, m] 

void luaL_addsize (luaL_Buffer *B, size_t n);

Adds to the buffer B (see luaL_Buffer) a string of length n previously copied to the buffer area (see luaL_prepbuffer). 

添加前一个被复制到缓冲区域（见luaL_prepbuffer）的长度为n的字符串到缓冲B（参考luaL_Buffer）

--------------------------------------------------------------------------------

luaL_addstring
[-0, +0, m] 

void luaL_addstring (luaL_Buffer *B, const char *s);

Adds the zero-terminated string pointed to by s to the buffer B (see luaL_Buffer). The string may not contain embedded zeros. 

添加s指向的0终结字符串到缓冲B（见luaL_Buffer）。该字符串不可以包含内嵌0。 

--------------------------------------------------------------------------------

luaL_addvalue
[-1, +0, m] 

void luaL_addvalue (luaL_Buffer *B);

Adds the value at the top of the stack to the buffer B (see luaL_Buffer). Pops the value. 

添加栈顶的值到缓冲B（见luaL_Buffer）。弹出该值。 

This is the only function on string buffers that can (and must) be called with an extra element on the stack, which is the value to be added to the buffer. 

这是仅有的可以（且必须）使用一个栈上的额外元素来被调用的关于字符串缓冲的函数，它（注：栈上元素）是要被添加到缓冲的值。

--------------------------------------------------------------------------------

luaL_argcheck
[-0, +0, v] 

void luaL_argcheck (lua_State *L,
                    int cond,
                    int narg,
                    const char *extramsg);

Checks whether cond is true. If not, raises an error with the following message, where func is retrieved from the call stack: 

检查cond是否为真。如果不是，用以下消息触发一个错误，这里func从调用栈中被取出。

     bad argument #<narg> to <func> (<extramsg>)

	 不正确的参数#<参数序号>对应<函数>（额外消息）

--------------------------------------------------------------------------------

luaL_argerror
[-0, +0, v] 

int luaL_argerror (lua_State *L, int narg, const char *extramsg);

Raises an error with the following message, where func is retrieved from the call stack: 

用以下消息触发一个错误，这里func取自调用栈： 

     bad argument #<narg> to <func> (<extramsg>)
     
     不正确的参数#<参数序号>给<函数>（<额外消息>）

This function never returns, but it is an idiom to use it in C functions as return luaL_argerror(args). 

本函数从不返回，但在C函数中使用它的惯常用法是return luaL_argerror(args)。

--------------------------------------------------------------------------------

luaL_Buffer
typedef struct luaL_Buffer luaL_Buffer;

Type for a string buffer. 

字符串缓冲的类型。 

A string buffer allows C code to build Lua strings piecemeal. Its pattern of use is as follows: 

字符串缓冲允许C代码逐块地构建Lua字符串。它的使用模式如下： 

First you declare a variable b of type luaL_Buffer. 

首先你声明类型luaL_Buffer的一个变量b。 

Then you initialize it with a call luaL_buffinit(L, &b). 

然后你使用一个调用luaL_buffinit(L, &b)初始化它。

Then you add string pieces to the buffer calling any of the luaL_add* functions. 

然后你通过调用任意一个luaL_add*函数添加字符串块到缓冲。 

You finish by calling luaL_pushresult(&b). This call leaves the final string on the top of the stack. 

你通过调用luaL_pushresult(&b)结束。这个调用把最终的字符串留在栈顶。

During its normal operation, a string buffer uses a variable number of stack slots. So, while using a buffer, you cannot assume that you know where the top of the stack is. You can use the stack between successive calls to buffer operations as long as that use is balanced; that is, when you call a buffer operation, the stack is at the same level it was immediately after the previous buffer operation. (The only exception to this rule is luaL_addvalue.) After calling luaL_pushresult the stack is back to its level when the buffer was initialized, plus the final string on its top. 

在它的正常操作期间，一个字符串缓冲使用一个可变数量的栈槽。因此，当使用一个缓冲时，你无法假设你知道栈顶在哪儿。你可以在连续多个对缓冲操作的调用之间使用栈，只要使用是平衡的；就是说，当你调用一个缓冲操作时，栈处于与前一个缓冲操纵之后即时所处的相同级别上。（这个规则的唯一例外是luaL_addvalue。）在调用luaL_pushresult之后，栈返回它在缓冲被初始化时的级别，而且最终字符串在它的顶部。

--------------------------------------------------------------------------------

luaL_buffinit
[-0, +0, -] 

void luaL_buffinit (lua_State *L, luaL_Buffer *B);

Initializes a buffer B. This function does not allocate any space; the buffer must be declared as a variable (see luaL_Buffer). 

初始化一个缓冲B。这个函数不分配任何空间；缓冲必须被声明为变量（见luaL_Buffer）。 

--------------------------------------------------------------------------------

luaL_callmeta
[-0, +(0|1), e] 

int luaL_callmeta (lua_State *L, int obj, const char *e);

Calls a metamethod. 

调用一个元方法。 

If the object at index obj has a metatable and this metatable has a field e, this function calls this field and passes the object as its only argument. In this case this function returns 1 and pushes onto the stack the value returned by the call. If there is no metatable or no metamethod, this function returns 0 (without pushing any value on the stack). 

如果处于索引obj的该对象有一个元表且这个元表由一个域e，那么这个函数调用这个字段并传递该对象作为它的唯一参数。在这种情况下这个函数返回1并压入被调用返回的值到栈上。如果没有元表或没有元方法，这个函数返回0（不压入任何值到栈上）。

--------------------------------------------------------------------------------

luaL_checkany
[-0, +0, v] 

void luaL_checkany (lua_State *L, int narg);

Checks whether the function has an argument of any type (including nil) at position narg. 

检查函数是否有一个任意类型（包括nil）的参数在位置narg上。 

--------------------------------------------------------------------------------

luaL_checkint
[-0, +0, v] 

int luaL_checkint (lua_State *L, int narg);

Checks whether the function argument narg is a number and returns this number cast to an int. 

检查函数参数narg是否为一个数，并返回这个数转换成的一个整数。

--------------------------------------------------------------------------------

luaL_checkinteger
[-0, +0, v] 

lua_Integer luaL_checkinteger (lua_State *L, int narg);

Checks whether the function argument narg is a number and returns this number cast to a lua_Integer. 

检查函数参数narg是否是一个数，并把返回这个数转换成的一个lua_Integer。

--------------------------------------------------------------------------------

luaL_checklong
[-0, +0, v] 

long luaL_checklong (lua_State *L, int narg);

Checks whether the function argument narg is a number and returns this number cast to a long. 

检查函数参数narg是否是一个数，并且返回这个数转换成的长整型。

--------------------------------------------------------------------------------

luaL_checklstring
[-0, +0, v] 

const char *luaL_checklstring (lua_State *L, int narg, size_t *l);

Checks whether the function argument narg is a string and returns this string; if l is not NULL fills *l with the string's length. 

检查函数参数narg是否是一个字符串，并返回这个字符串；如果l不是NULL，用字符串的长度填充*l。

This function uses lua_tolstring to get its result, so all conversions and caveats of that function apply here. 

这个函数使用lua_tolstring获得它的结果，所以那个函数的所有转换和注意事项都适用于这里。

--------------------------------------------------------------------------------

luaL_checknumber
[-0, +0, v] 

lua_Number luaL_checknumber (lua_State *L, int narg);

Checks whether the function argument narg is a number and returns this number. 

检查函数参数narg是否是一个数，并返回这个数。

--------------------------------------------------------------------------------

luaL_checkoption
[-0, +0, v] 

int luaL_checkoption (lua_State *L,
                      int narg,
                      const char *def,
                      const char *const lst[]);

Checks whether the function argument narg is a string and searches for this string in the array lst (which must be NULL-terminated). Returns the index in the array where the string was found. Raises an error if the argument is not a string or if the string cannot be found. 

检查函数参数narg是否是一个字符串，并在数组lst（必须是NULL终结）中搜索这个字符串。返回在数组中该字符串被找到的索引。触发错误如果参数不是一个字符串或如果该字符串不能被找到。

If def is not NULL, the function uses def as a default value when there is no argument narg or if this argument is nil. 

如果def不是NULL，该函数使用def作为一个默认值当没有参数narg时，或者如果这个参数是nil。

This is a useful function for mapping strings to C enums. (The usual convention in Lua libraries is to use strings instead of numbers to select options.) 

这对于映射字符串到C枚举来说是一个有用的参数。（在Lua库中通常的约定是使用字符串代替数字来选择选项。）

--------------------------------------------------------------------------------

luaL_checkstack
[-0, +0, v] 

void luaL_checkstack (lua_State *L, int sz, const char *msg);

Grows the stack size to top + sz elements, raising an error if the stack cannot grow to that size. msg is an additional text to go into the error message. 

增长栈尺寸到top + sz个元素，触发一个错误如果栈不能增长到那个大小。msg是一个额外的文本以放入错误消息中。 

--------------------------------------------------------------------------------

luaL_checkstring
[-0, +0, v] 

const char *luaL_checkstring (lua_State *L, int narg);

Checks whether the function argument narg is a string and returns this string. 

检查函数参数narg是否是一个字符串并返回这个字符串。 

This function uses lua_tolstring to get its result, so all conversions and caveats of that function apply here. 

这个函数用lua_tolstring以得到它的结果，所以那个函数的所有转换和注意事项应用到这里。 

--------------------------------------------------------------------------------

luaL_checktype
[-0, +0, v] 

void luaL_checktype (lua_State *L, int narg, int t);

Checks whether the function argument narg has type t. See lua_type for the encoding of types for t. 

检查该函数参数narg是否拥有类型t。见lua_type以获得t的类型编码。

--------------------------------------------------------------------------------

luaL_checkudata
[-0, +0, v] 

void *luaL_checkudata (lua_State *L, int narg, const char *tname);

Checks whether the function argument narg is a userdata of the type tname (see luaL_newmetatable). 

检查函数参数narg是否是类型tname的一个用户数据（见luaL_newmetatable）

--------------------------------------------------------------------------------

luaL_dofile
[-0, +?, m] 

int luaL_dofile (lua_State *L, const char *filename);

Loads and runs the given file. It is defined as the following macro: 

加载并运行给定的文件。它被定义为以下宏：

     (luaL_loadfile(L, filename) || lua_pcall(L, 0, LUA_MULTRET, 0))

It returns 0 if there are no errors or 1 in case of errors. 

它返回0如果没有错误，或者返回1如果出错。

--------------------------------------------------------------------------------

luaL_dostring
[-0, +?, m] 

int luaL_dostring (lua_State *L, const char *str);

Loads and runs the given string. It is defined as the following macro: 

加载并运行所给的字符串。它被定义为以下宏：

     (luaL_loadstring(L, str) || lua_pcall(L, 0, LUA_MULTRET, 0))

It returns 0 if there are no errors or 1 in case of errors. 

它返回0如果没有错误，或者返回1如果出错。

--------------------------------------------------------------------------------

luaL_error
[-0, +0, v] 

int luaL_error (lua_State *L, const char *fmt, ...);

Raises an error. The error message format is given by fmt plus any extra arguments, following the same rules of lua_pushfstring. It also adds at the beginning of the message the file name and the line number where the error occurred, if this information is available. 

触发一个错误。错误消息的格式由fmt加上任意额外的参数给定，遵循lua_pushfstring的相同规则。它还会在消息开头添加错误发生所在的文件名和行号，如果这个信息是可用的。

This function never returns, but it is an idiom to use it in C functions as return luaL_error(args). 

这个函数从不返回，但在C函数中使用它的习惯用法是return luaL_error(args)。

--------------------------------------------------------------------------------

luaL_getmetafield
[-0, +(0|1), m] 

int luaL_getmetafield (lua_State *L, int obj, const char *e);

Pushes onto the stack the field e from the metatable of the object at index obj. If the object does not have a metatable, or if the metatable does not have this field, returns 0 and pushes nothing. 

压入来自索引obj上对象的元表的字段e到栈上。如果该对象没有元表，或者如果该元表没有这个字段，返回0并且不压入东西。 

--------------------------------------------------------------------------------

luaL_getmetatable
[-0, +1, -] 

void luaL_getmetatable (lua_State *L, const char *tname);

Pushes onto the stack the metatable associated with name tname in the registry (see luaL_newmetatable). 

压入在注册表中与名称tname关联的元表到栈上。（见luaL_newmetatable）。 

--------------------------------------------------------------------------------

luaL_gsub
[-0, +1, m] 

const char *luaL_gsub (lua_State *L,
                       const char *s,
                       const char *p,
                       const char *r);

Creates a copy of string s by replacing any occurrence of the string p with the string r. Pushes the resulting string on the stack and returns it. 

创建字符串s的副本，通过用字符串r替换字符串p的任意出现。压入结果字符串在栈上并返回它。 

--------------------------------------------------------------------------------

luaL_loadbuffer
[-0, +1, m] 

int luaL_loadbuffer (lua_State *L,
                     const char *buff,
                     size_t sz,
                     const char *name);

Loads a buffer as a Lua chunk. This function uses lua_load to load the chunk in the buffer pointed to by buff with size sz. 

载入一个缓冲作为一个Lua数据块。这个函数使用lua_load以加载缓冲中被buff指向的大小为sz的数据块。 

This function returns the same results as lua_load. name is the chunk name, used for debug information and error messages. 

这个函数返回与lua_load相同的结果。name是数据块名字，被用于调试信息和错误消息。 

--------------------------------------------------------------------------------

luaL_loadfile
[-0, +1, m] 

int luaL_loadfile (lua_State *L, const char *filename);

Loads a file as a Lua chunk. This function uses lua_load to load the chunk in the file named filename. If filename is NULL, then it loads from the standard input. The first line in the file is ignored if it starts with a #. 

加载一个文件作为一个数据块。这个函数使用lua_load加载名为filename的文件中的chunk块。如果filename是NULL，那么从标准输入中加载。文件中第一行被忽略，如果它以#开头。 

This function returns the same results as lua_load, but it has an extra error code LUA_ERRFILE if it cannot open/read the file. 

这个函数返回与lua_load相同的结果，但它有一个额外的错误码LUA_ERRFILE，如果它无法打开/读取该文件。 

As lua_load, this function only loads the chunk; it does not run it. 

正如lua_load那样，这个函数只加载那个数据块；它不运行它。 

--------------------------------------------------------------------------------

luaL_loadstring
[-0, +1, m] 

int luaL_loadstring (lua_State *L, const char *s);

Loads a string as a Lua chunk. This function uses lua_load to load the chunk in the zero-terminated string s. 

加载一个字符串作为一个Lua数据块。这个函数使用lua_load加载0终结的字符串s中的chunk块。 

This function returns the same results as lua_load. 

这个函数返回与lua_load相同的结果。 

Also as lua_load, this function only loads the chunk; it does not run it. 

正如lua_load那样，这个函数只加载该chunk块；它不运行它。 

--------------------------------------------------------------------------------

luaL_newmetatable
[-0, +1, m] 

int luaL_newmetatable (lua_State *L, const char *tname);

If the registry already has the key tname, returns 0. Otherwise, creates a new table to be used as a metatable for userdata, adds it to the registry with key tname, and returns 1. 

如果注册表已经拥有键tname，那么返回0。否则，创建一个新的表以将用作用户数据的一个元表，添加它到注册表带有键tname，并返回1。

In both cases pushes onto the stack the final value associated with tname in the registry. 

在两种情况下，均压入注册表中与tname关联的最终值到栈上。 

--------------------------------------------------------------------------------

luaL_newstate
[-0, +0, -] 

lua_State *luaL_newstate (void);

Creates a new Lua state. It calls lua_newstate with an allocator based on the standard C realloc function and then sets a panic function (see lua_atpanic) that prints an error message to the standard error output in case of fatal errors. 

创建一个新的Lua状态。它用一个基于标准C的realloc函数的分配器调用lua_newstate，然后设置一个紧急函数（见lua_atpanic），它打印一个错误消息到标准错误输出，在出现致命错误的情况下。

Returns the new state, or NULL if there is a memory allocation error. 

返回新的状态，或者NULL如果存在一个内存分配错误。 

--------------------------------------------------------------------------------

luaL_openlibs
[-0, +0, m] 

void luaL_openlibs (lua_State *L);

Opens all standard Lua libraries into the given state. 

打开所有标准Lua库进给定的状态中。 

--------------------------------------------------------------------------------

luaL_optint
[-0, +0, v] 

int luaL_optint (lua_State *L, int narg, int d);

If the function argument narg is a number, returns this number cast to an int. If this argument is absent or is nil, returns d. Otherwise, raises an error. 

如果函数参数narg是一个数，返回这个数字所转换的一个整型。如果这个参数不存在或为nil，则返回d。否则，触发一个错误。 

--------------------------------------------------------------------------------

luaL_optinteger
[-0, +0, v] 

lua_Integer luaL_optinteger (lua_State *L,
                             int narg,
                             lua_Integer d);

If the function argument narg is a number, returns this number cast to a lua_Integer. If this argument is absent or is nil, returns d. Otherwise, raises an error. 

如果函数参数narg是一个数，返回这个数所转换成的lua_Integer。如果这个参数不存在或为nil，则返回d。否则，触发一个错误。 

--------------------------------------------------------------------------------

luaL_optlong
[-0, +0, v] 

long luaL_optlong (lua_State *L, int narg, long d);

If the function argument narg is a number, returns this number cast to a long. If this argument is absent or is nil, returns d. Otherwise, raises an error. 

如果函数参数narg是一个数，返回这个数所转换成的长整型。如果这个参数不存在或为nil，则返回d。否则，触发一个错误。 

--------------------------------------------------------------------------------

luaL_optlstring
[-0, +0, v] 

const char *luaL_optlstring (lua_State *L,
                             int narg,
                             const char *d,
                             size_t *l);

If the function argument narg is a string, returns this string. If this argument is absent or is nil, returns d. Otherwise, raises an error. 

如果函数参数narg是一个字符串，返回这个字符串。如果这个参数不存在或为nil，则返回d。否则，触发一个错误。 

If l is not NULL, fills the position *l with the results's length. 

如果l不是NULL，则用结果的长度填充位置*l。 

--------------------------------------------------------------------------------

luaL_optnumber
[-0, +0, v] 

lua_Number luaL_optnumber (lua_State *L, int narg, lua_Number d);

If the function argument narg is a number, returns this number. If this argument is absent or is nil, returns d. Otherwise, raises an error. 

如果函数参数narg是一个数，返回这个数。如果这个参数不存在或为nil，则返回d。否则触发一个错误。 

--------------------------------------------------------------------------------

luaL_optstring
[-0, +0, v] 

const char *luaL_optstring (lua_State *L,
                            int narg,
                            const char *d);

If the function argument narg is a string, returns this string. If this argument is absent or is nil, returns d. Otherwise, raises an error. 

如果函数参数narg是一个字符串，则返回它。如果这个参数不存在或为nil，则返回d。否则，触发一个错误。 

--------------------------------------------------------------------------------

luaL_prepbuffer
[-0, +0, -] 

char *luaL_prepbuffer (luaL_Buffer *B);

Returns an address to a space of size LUAL_BUFFERSIZE where you can copy a string to be added to buffer B (see luaL_Buffer). After copying the string into this space you must call luaL_addsize with the size of the string to actually add it to the buffer. 

返回大小为LUAL_BUFFERSIZE的空间的地址，在那里你可以复制要被添加到缓冲B的一个字符串（见luaL_Buffer）。在复制该字符串进这个空间中之后，你必须用字符串的大小调用luaL_addsize以实际地添加它到缓冲。 

--------------------------------------------------------------------------------

luaL_pushresult
[-?, +1, m] 

void luaL_pushresult (luaL_Buffer *B);

Finishes the use of buffer B leaving the final string on the top of the stack. 

完成对缓冲B的使用，保留最终字符串在栈顶。 

--------------------------------------------------------------------------------

luaL_ref
[-1, +0, m] 

int luaL_ref (lua_State *L, int t);

Creates and returns a reference, in the table at index t, for the object at the top of the stack (and pops the object). 

创建并返回栈顶的对象的一个引用，在索引t上的表中（并弹出该对象）。 

A reference is a unique integer key. As long as you do not manually add integer keys into table t, luaL_ref ensures the uniqueness of the key it returns. You can retrieve an object referred by reference r by calling lua_rawgeti(L, t, r). Function luaL_unref frees a reference and its associated object. 

一个引用是一个唯一整数键。只要你不手工添加整数键进表t中，luaL_ref保证它返回的键的唯一性。你可以通过调用lua_rawgeti(L, t, r)取出被引用r引用的一个对象。函数luaL_unref释放一个引用以及它关联的对象。 

If the object at the top of the stack is nil, luaL_ref returns the constant LUA_REFNIL. The constant LUA_NOREF is guaranteed to be different from any reference returned by luaL_ref. 

如果栈顶的对象是nil，则luaL_ref返回常量LUA_REFNIL。常量LUA_NOREF被确保不同于被luaL_ref返回的任意引用。 

--------------------------------------------------------------------------------

luaL_Reg
typedef struct luaL_Reg {
  const char *name;
  lua_CFunction func;
} luaL_Reg;

Type for arrays of functions to be registered by luaL_register. name is the function name and func is a pointer to the function. Any array of luaL_Reg must end with an sentinel entry in which both name and func are NULL. 

用于要被luaL_register注册的函数数组的类型。name是函数名而func是一个指向该函数的指针。任意luaL_Reg数组必须以其中的name和func均为NULL的一个岗哨记录结束。 

--------------------------------------------------------------------------------

luaL_register
[-(0|1), +1, m] 

void luaL_register (lua_State *L,
                    const char *libname,
                    const luaL_Reg *l);

Opens a library. 

打开一个库。 

When called with libname equal to NULL, it simply registers all functions in the list l (see luaL_Reg) into the table on the top of the stack. 

当以libname等于NULL来调用时，它简单地注册列表l里的所有函数（见luaL_Reg）进栈顶的表中。 

When called with a non-null libname, luaL_register creates a new table t, sets it as the value of the global variable libname, sets it as the value of package.loaded[libname], and registers on it all functions in the list l. If there is a table in package.loaded[libname] or in variable libname, reuses this table instead of creating a new one. 

当以一个非空libname来调用时，luaL_register创建一个新表t，设置它作为全局变量libname的值，设置它作为package.loaded[libname]的值，并在它上面注册列表l中的所有函数。如果存在一个表在package.loaded[libname]中或在变量libname中，则重用这个表而非创建一个新的。 

In any case the function leaves the table on the top of the stack. 

任何情况下函数保留该表在栈顶。 

--------------------------------------------------------------------------------

luaL_typename
[-0, +0, -] 

const char *luaL_typename (lua_State *L, int index);

Returns the name of the type of the value at the given index. 

返回给定索引上的值的类型的名称。 

--------------------------------------------------------------------------------

luaL_typerror
[-0, +0, v] 

int luaL_typerror (lua_State *L, int narg, const char *tname);
Generates an error with a message like the following: 

用类似下面的消息产生一个错误： 

     location: bad argument narg to 'func' (tname expected, got rt)
	
	 location：传给'func'的不正确参数narg（期待类型tname，但得到类型rt）

where location is produced by luaL_where, func is the name of the current function, and rt is the type name of the actual argument. 

其中location是由luaL_where产生，func是当前函数的名称，而rt是实际参数的类型名称。 

--------------------------------------------------------------------------------

luaL_unref
[-0, +0, -] 

void luaL_unref (lua_State *L, int t, int ref);

Releases reference ref from the table at index t (see luaL_ref). The entry is removed from the table, so that the referred object can be collected. The reference ref is also freed to be used again. 

释放来自在索引t上的表的引用ref（见luaL_ref）。该记录从表中被删除，致使被引用的对象可以被回收。引用ref也总是被释放以被再次使用。

If ref is LUA_NOREF or LUA_REFNIL, luaL_unref does nothing. 

如果ref是LUA_NOREF或LUA_REFNIL，那么luaL_unref什么也不做。 

--------------------------------------------------------------------------------

luaL_where
[-0, +1, m] 

void luaL_where (lua_State *L, int lvl);

Pushes onto the stack a string identifying the current position of the control at level lvl in the call stack. Typically this string has the following format: 

压入一个标识调用栈中在级别lvl上的控制的当前位置。通常这个字符串拥有以下格式：

     chunkname:currentline:

     块名:当前行:

Level 0 is the running function, level 1 is the function that called the running function, etc. 

级别0是正在运行的函数，级别1是调用正在运行函数的函数，如此类推。

This function is used to build a prefix for error messages. 

这个函数被用于为错误消息而构建一个前缀。

-----------------------------------------

参考自：
1. Lua 5.1 参考手册 （云风译）
http://www.codingnow.com/2000/download/lua_manual.html
2. hshqcn
hshqcn  

