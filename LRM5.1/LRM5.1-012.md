## 【翻译】(LRM5.1-12)辅助库(4)(luaL_addchar - luaL_getmetafield)  

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

## 4 - The Auxiliary Library  

## 4 - 辅助库  

The auxiliary library provides several convenient functions to interface C with Lua. While the basic API provides the primitive functions for all interactions between C and Lua, the auxiliary library provides higher-level functions for some common tasks.   

辅助库提供几个便利函数以对接C和Lua。虽然基本API为C和Lua之间的所有接口而提供原始函数，但辅助库为一些一般任务提供较高层次的函数。  

All functions from the auxiliary library are defined in header file lauxlib.h and have a prefix luaL_.   

来自辅助库的所有函数被定义在头文件lauxlib.h中并拥有一个前缀luaL_。   

All functions in the auxiliary library are built on top of the basic API, and so they provide nothing that cannot be done with this API.   

辅助库中的所有函数被建立在基本API上，所以它们不提供使用这套API无法处理的东西。   

Several functions in the auxiliary library are used to check C function arguments. Their names are always luaL_check* or luaL_opt*. All of these functions throw an error if the check is not satisfied. Because the error message is formatted for arguments (e.g., "bad argument #1"), you should not use these functions for other stack values.   

在辅助库中几个函数被用于检查C函数参数。它们的名称总是luaL_check*或luaL_opt*。所有这些函数抛出一个错误，如果检查不被满足。因为错误消息为参数而被格式化（例如，“不正确的参数#1”，所以你不应该为其它栈值而使用这些函数。  

## 4.1 - Functions and Types  

## 4.1 - 函数与类型  

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

这是仅有的可以（且必须）使用一个栈上的额外元素来被调用的关于字符串缓冲的函数，它**（注：栈上元素）**是要被添加到缓冲的值。  

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


-----------------------------------------  

## 参考自：  
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  

2. hshqcn  
hshqcn  

