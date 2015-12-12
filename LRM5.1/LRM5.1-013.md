## 【翻译】(LRM5.1-13)辅助库(4)(luaL_getmetatable - luaL_where)  

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

## 参考自：  
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  

2. hshqcn  
hshqcn  

