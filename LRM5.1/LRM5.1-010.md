## 【翻译】(LRM5.1-10)函数和类型(3.7)(lua_Reader - lua_yield)

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

lua_Reader  
typedef const char * (*lua_Reader) (lua_State *L,  
                                    void *data,  
                                    size_t *size);  

The reader function used by lua_load. Every time it needs another piece of the chunk, lua_load calls the reader, passing along its data parameter. The reader must return a pointer to a block of memory with a new piece of the chunk and set size to the block size. The block must exist until the reader function is called again. To signal the end of the chunk, the reader must return NULL or set size to zero. The reader function may return pieces of any size greater than zero.   

被lua_load使用的读取器函数。每当它需要另一块数据块时，lua_load调用读取器，传入它的data参数。读取器必须返回一个指向一块带有新数据块的内存的指针并且设置大小到块大小。块必须存在直至读取器函数被再次调用。为了通知数据块的结束，读取器必须返回NULL或设置大小为0。读取器函数可能返回比0大的任意大小的块。  

--------------------------------------------------------------------------------  

lua_register  
[-0, +0, e]   

void lua_register (lua_State *L,  
                   const char *name,  
                   lua_CFunction f);  

Sets the C function f as the new value of global name. It is defined as a macro:   

设置C函数f为全局变量name的新值。它被定义为一个宏：   

     #define lua_register(L,n,f) \  
            (lua_pushcfunction(L, f), lua_setglobal(L, n))  

--------------------------------------------------------------------------------  

lua_remove  
[-1, +0, -]   

void lua_remove (lua_State *L, int index);  

Removes the element at the given valid index, shifting down the elements above this index to fill the gap. Cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.   

移除给定可用索引上的元素，并下移这个索引上方的元素以填充空白。不能用伪索引调用它，因为伪索引不是一个实际栈位置。   

--------------------------------------------------------------------------------  

lua_replace  
[-1, +0, -]   

void lua_replace (lua_State *L, int index);  

Moves the top element into the given position (and pops it), without shifting any element (therefore replacing the value at the given position).   

移动栈顶元素进给定位置中（并弹出它），不平移任意元素（因此替换给定位置上的值）。   

--------------------------------------------------------------------------------  

lua_resume  
[-?, +?, -]   

int lua_resume (lua_State *L, int narg);  

Starts and resumes a coroutine in a given thread.   

启动并恢复一个给定线程中的一个协程。  

To start a coroutine, you first create a new thread (see lua_newthread); then you push onto its stack the main function plus any arguments; then you call lua_resume, with narg being the number of arguments. This call returns when the coroutine suspends or finishes its execution. When it returns, the stack contains all values passed to lua_yield, or all values returned by the body function. lua_resume returns LUA_YIELD if the coroutine yields, 0 if the coroutine finishes its execution without errors, or an error code in case of errors (see lua_pcall). In case of errors, the stack is not unwound, so you can use the debug API over it. The error message is on the top of the stack. To restart a coroutine, you put on its stack only the values to be passed as results from yield, and then call lua_resume.   

为了启动一个协程，你首先创建一个新线程（见lua_newthread）；然后你把主函数和任意参数压进它的栈上；然后你调用lua_resume，参数narg为参数的数量。这个调用返回，当协程暂停或完成它的执行。当它返回时，栈包含传给lua_yield的所有值，或者被本体函数返回的所有值。lua_resume返回LUA_YIELD如果协程挂起，返回0如果协程不带错误地完成它的执行，或一个错误码如果有错误发生（见lua_pcall）。在出错的情况下，栈不是展开**（注：unwind有解开的意思）**的，所以你可以在它上面使用调试API）。错误消息位于栈顶。为了重启一个协程，你只把要被传递作为挂起结果的值放在它的栈上，然后调用lua_resume。  

--------------------------------------------------------------------------------  

lua_setallocf  
[-0, +0, -]   

void lua_setallocf (lua_State *L, lua_Alloc f, void *ud);  

Changes the allocator function of a given state to f with user data ud.   

改变一个给定状态的分配器函数为f，附带用户数据ud。  

--------------------------------------------------------------------------------  

lua_setfenv  
[-1, +0, -]   

int lua_setfenv (lua_State *L, int index);  

Pops a table from the stack and sets it as the new environment for the value at the given index. If the value at the given index is neither a function nor a thread nor a userdata, lua_setfenv returns 0. Otherwise it returns 1.   

从栈中弹出一个表并且把设置它为给定索引上的值的新环境。如果给定索引上的值不是一个函数、一个线程或一个用户数据，那么它返回0。否则它返回1。   

--------------------------------------------------------------------------------  

lua_setfield  
[-1, +0, e]   

void lua_setfield (lua_State *L, int index, const char *k);  

Does the equivalent to t[k] = v, where t is the value at the given valid index and v is the value at the top of the stack.   

执行t[k] = v的等价操作，其中t是给定可用索引上的值，而v是栈顶上的值。   

This function pops the value from the stack. As in Lua, this function may trigger a metamethod for the "newindex" event (see §2.8).   

这个函数从栈中弹出值。正如在Lua中那样，此函数可能触发"newindex"事件的元方法（见§2.8）。   

--------------------------------------------------------------------------------  

lua_setglobal  
[-1, +0, e]   

void lua_setglobal (lua_State *L, const char *name);  

Pops a value from the stack and sets it as the new value of global name. It is defined as a macro:   

从栈中弹出一个值并且设置它作为全局变量name的新值。它被定义为一个宏：   

     #define lua_setglobal(L,s)   lua_setfield(L, LUA_GLOBALSINDEX, s)  

--------------------------------------------------------------------------------  

lua_setmetatable  
[-1, +0, -]   

int lua_setmetatable (lua_State *L, int index);  

Pops a table from the stack and sets it as the new metatable for the value at the given acceptable index.   

从栈中弹出一个表并且设置它为所给可接受索引上的值的新元表。  

--------------------------------------------------------------------------------  

lua_settable  
[-2, +0, e]   

void lua_settable (lua_State *L, int index);  

Does the equivalent to t[k] = v, where t is the value at the given valid index, v is the value at the top of the stack, and k is the value just below the top.   

执行t[k] = v的等价操作，其中t是给定有效索引上的值，v是栈顶的值，而k是栈顶正下方的值。   

This function pops both the key and the value from the stack. As in Lua, this function may trigger a metamethod for the "newindex" event (see §2.8).   

此函数将键和值都弹出栈。正如在Lua中那样，此函数可能触发"newindex"事件的元方法（见§2.8）。   

--------------------------------------------------------------------------------  

lua_settop  
[-?, +?, -]   

void lua_settop (lua_State *L, int index);  

Accepts any acceptable index, or 0, and sets the stack top to this index. If the new top is larger than the old one, then the new elements are filled with nil. If index is 0, then all stack elements are removed.   

接受任意可接受索引，或者0，并且设置栈顶为此索引。如果新栈顶大于旧栈顶，那么新元素被填充为nil。如果索引为0，那么所有栈元素被移除。   

--------------------------------------------------------------------------------  

lua_State  

typedef struct lua_State lua_State;  

Opaque structure that keeps the whole state of a Lua interpreter. The Lua library is fully reentrant: it has no global variables. All information about a state is kept in this structure.   

保存整个Lua解析器状态的不透明结构。Lua库是完全可重入的：它没有全局变量。关于一个状态的所有信息被保存在这个结构体中。  

A pointer to this state must be passed as the first argument to every function in the library, except to lua_newstate, which creates a Lua state from scratch.   

指向这个状态的指针必须被传递作为库中每一个函数的第一个参数，除了lua_newstate，最开始它创建一个Lua状态。  

--------------------------------------------------------------------------------  

lua_status  
[-0, +0, -]   

int lua_status (lua_State *L);  

Returns the status of the thread L.   

返回线程L的状态。  

The status can be 0 for a normal thread, an error code if the thread finished its execution with an error, or LUA_YIELD if the thread is suspended.   

状态可能是0表示一个正常线程，可能是一个错误值如果该线程带一个错误地完成它的执行，或者可能是LUA_YIELD如果线程被挂起**（注：暂停）**。  

--------------------------------------------------------------------------------  

lua_toboolean  
[-0, +0, -]   

int lua_toboolean (lua_State *L, int index);  

Converts the Lua value at the given acceptable index to a C boolean value (0 or 1). Like all tests in Lua, lua_toboolean returns 1 for any Lua value different from false and nil; otherwise it returns 0. It also returns 0 when called with a non-valid index. (If you want to accept only actual boolean values, use lua_isboolean to test the value's type.)   

把所给可接受索引上的Lua值转换为一个C布尔值（0或1）。就像Lua中的所有测试**（注：这里指比较操作）**那样，返回1，对于任何不是false和nil的值；否则它返回0。它还返回0，当用一个不可用索引调用时。（如果你想只接受实际的布尔值，请使用lua_isboolean来测试该值的类型。）**（注：lua_isboolean用于判断是否为布尔类型）**  

--------------------------------------------------------------------------------  

lua_tocfunction  
[-0, +0, -]   

lua_CFunction lua_tocfunction (lua_State *L, int index);  

Converts a value at the given acceptable index to a C function. That value must be a C function; otherwise, returns NULL.   

转换所给可接受索引上的值为一个C函数。那个值必须是一个C函数；否则，返回NULL。  

--------------------------------------------------------------------------------   

lua_tointeger  
[-0, +0, -]   

lua_Integer lua_tointeger (lua_State *L, int index);  

Converts the Lua value at the given acceptable index to the signed integral type lua_Integer. The Lua value must be a number or a string convertible to a number (see §2.2.1); otherwise, lua_tointeger returns 0.   

转换所给可接受索引上的值为带符号整型lua_Integer**（注：一个定义在C中的typedef类型）**。该Lua值必须是一个数或一个可转换为数的字符串（参考§2.2.1）；否则，lua_tointeger返回0。  

If the number is not an integer, it is truncated in some non-specified way.   

如果该数不是一个整数，它以一些不特定的方式被截断。  

--------------------------------------------------------------------------------  

lua_tolstring  
[-0, +0, m]   

const char *lua_tolstring (lua_State *L, int index, size_t *len);  

Converts the Lua value at the given acceptable index to a C string. If len is not NULL, it also sets *len with the string length. The Lua value must be a string or a number; otherwise, the function returns NULL. If the value is a number, then lua_tolstring also changes the actual value in the stack to a string. (This change confuses lua_next when lua_tolstring is applied to keys during a table traversal.)   

转换所给可接受索引上的值为一个C字符串。如果参数len不是NULL，它还设置*len为字符串长度。该Lua值必须是一个字符串或一个数；否则，函数返回NULL。如果该值是一个数，那么lua_tolstring还改变栈中的实际值为字符串。（这个改变会搞乱lua_next，当在一次表遍历期间lua_tolstring被应用到键时。）  

lua_tolstring returns a fully aligned pointer to a string inside the Lua state. This string always has a zero ('\0') after its last character (as in C), but can contain other zeros in its body. Because Lua has garbage collection, there is no guarantee that the pointer returned by lua_tolstring will be valid after the corresponding value is removed from the stack.   

lua_tolstring返回一个完全对齐的指向Lua状态内部的字符串的指针。因为Lua有垃圾回收，所以不保证lua_tolstring所返回的指针在相应值从堆栈中删除后仍合法。  

--------------------------------------------------------------------------------  

lua_tonumber  
[-0, +0, -]   

lua_Number lua_tonumber (lua_State *L, int index);  

Converts the Lua value at the given acceptable index to the C type lua_Number (see lua_Number). The Lua value must be a number or a string convertible to a number (see §2.2.1); otherwise, lua_tonumber returns 0.   

把所给可接受索引上的值转换为C类型lua_Number（参考lua_Number）**（注：lua_Number是一个typedef，默认为double型）**。Lua值必须为一个数或可以转换为数的字符串（参考§2.2.1）；否则，lua_tonumber返回0。  

--------------------------------------------------------------------------------  

lua_topointer  
[-0, +0, -]   

const void *lua_topointer (lua_State *L, int index);  

Converts the value at the given acceptable index to a generic C pointer (void*). The value can be a userdata, a table, a thread, or a function; otherwise, lua_topointer returns NULL. Different objects will give different pointers. There is no way to convert the pointer back to its original value.   

把所给可接受索引上的值转换为通用C指针（void*）。这个值可以是一个用户定义数据，表，线程，或者是函数；否则，lua_topointer返回NULL。不同的对象会给出不同的指针。无法转换指针回它原来的值。  

Typically this function is used only for debug information.   

通常这个函数只用于获取调试信息。  

--------------------------------------------------------------------------------  

lua_tostring  
[-0, +0, m]   

const char *lua_tostring (lua_State *L, int index);  

Equivalent to lua_tolstring with len equal to NULL.   

等效于参数len等于NULL的lua_tolstring。   

--------------------------------------------------------------------------------  

lua_tothread  
[-0, +0, -]   

lua_State *lua_tothread (lua_State *L, int index);  

Converts the value at the given acceptable index to a Lua thread (represented as lua_State*). This value must be a thread; otherwise, the function returns NULL.   

转换给定的可接受索引上的值为一个Lua线程（表示为lua_State*）。这个值必须是一个类型为thread的值；否则，函数返回NULL。  

--------------------------------------------------------------------------------  

lua_touserdata  
[-0, +0, -]   

void *lua_touserdata (lua_State *L, int index);  

If the value at the given acceptable index is a full userdata, returns its block address. If the value is a light userdata, returns its pointer. Otherwise, returns NULL.   

如果给定可接受索引上的值是一个完全用户数据，返回它的块**（注：这里应该是指数据的内存块）**地址。如果该值为轻量级用户数据，返回它的指针。否则，返回NULL。   

--------------------------------------------------------------------------------  

lua_type  
[-0, +0, -]   

int lua_type (lua_State *L, int index);  

Returns the type of the value in the given acceptable index, or LUA_TNONE for a non-valid index (that is, an index to an "empty" stack position). The types returned by lua_type are coded by the following constants defined in lua.h: LUA_TNIL, LUA_TNUMBER, LUA_TBOOLEAN, LUA_TSTRING, LUA_TTABLE, LUA_TFUNCTION, LUA_TUSERDATA, LUA_TTHREAD, and LUA_TLIGHTUSERDATA.   

返回给定可接受索引上的值的类型，或者LUA_TNONE表示非可用索引（就是说，指向“空的”栈位置的索引）。被lua_type返回的类型被定义在lua.h中的以下常量编码：LUA_TNIL，LUA_TNUMBER，LUA_TBOOLEAN，LUA_TSTRING，LUA_TTABLE，LUA_TFUNCTION，LUA_TUSERDATA，LUA_TTHREAD和LUA_TLIGHTUSERDATA。  

--------------------------------------------------------------------------------  

lua_typename  
[-0, +0, -]   

const char *lua_typename  (lua_State *L, int tp);  

Returns the name of the type encoded by the value tp, which must be one the values returned by lua_type.   

返回被值tp编码**（注：这里指把字符串映射为整数）**的类型的名称，它必须是被lua_type返回的值之一。   

--------------------------------------------------------------------------------  

lua_Writer  
typedef int (*lua_Writer) (lua_State *L,  
                           const void* p,  
                           size_t sz,  
                           void* ud);  

The type of the writer function used by lua_dump. Every time it produces another piece of chunk, lua_dump calls the writer, passing along the buffer to be written (p), its size (sz), and the data parameter supplied to lua_dump.   

被lua_dump使用的写入器函数的类型。每当它产生另一块chunk块时，lua_dump调用写入器，传入要被写入的缓冲（p），它的大小（sz），以及提供给lua_dump的数据参数。  

The writer returns an error code: 0 means no errors; any other value means an error and stops lua_dump from calling the writer again.   

写入器返回一个错误代码：0意味着无错误；其它任意值意味着一个错误并且阻止lua_dump再次调用写入器。  

--------------------------------------------------------------------------------  

lua_xmove  
[-?, +?, -]   

void lua_xmove (lua_State *from, lua_State *to, int n);  

Exchange values between different threads of the same global state.   

在同一个全局状态的不同线程之间交换值。  

This function pops n values from the stack from, and pushes them onto the stack to.   

这个函数从栈from中弹出n个值，并且把它们压到栈to上。  

--------------------------------------------------------------------------------  

lua_yield  
[-?, +?, -]   

int lua_yield  (lua_State *L, int nresults);  

Yields a coroutine.   

挂起**（注：暂停）**一个协程   

This function should only be called as the return expression of a C function, as follows:   

这个函数应该只作为C函数的return表达式被调用**（注：即尾调用）**，如下：  

     return lua_yield (L, nresults);  

When a C function calls lua_yield in that way, the running coroutine suspends its execution, and the call to lua_resume that started this coroutine returns. The parameter nresults is the number of values from the stack that are passed as results to lua_resume.   

当一个C函数以那种方式调用lua_yield时，正在运行的协程暂停它的执行，而启动这个协程的对lua_resume的调用会返回。参数nresults是来自栈的值的数量，它们被传递作为传给lua_resume的结果。  


-----------------------------------------

## 参考自：  
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  

2. hshqcn  
hshqcn  
