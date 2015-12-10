## 【翻译】(LRM5.1-8)函数和类型(3.7)(lua_getallocf - lua_next)

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

lua_getallocf  
[-0, +0, -]   

lua_Alloc lua_getallocf (lua_State *L, void **ud);  

Returns the memory-allocation function of a given state. If ud is not NULL, Lua stores in *ud the opaque pointer passed to lua_newstate.   

返回给定状态的内存分配函数。如果ud不是NULL，Lua把传递进lua_newstate的不透明指针存储进*ud。   

--------------------------------------------------------------------------------  

lua_getfenv  
[-0, +1, -]   

void lua_getfenv (lua_State *L, int index);  

Pushes onto the stack the environment table of the value at the given index.   

压入给定索引上的值的环境表到栈上。   

--------------------------------------------------------------------------------  

lua_getfield  
[-0, +1, e]   
 
void lua_getfield (lua_State *L, int index, const char *k);  

Pushes onto the stack the value t[k], where t is the value at the given valid index. As in Lua, this function may trigger a metamethod for the "index" event (see §2.8).   

压入值t[k]到栈上，这里t是给定可用索引上的值。如同Lua中那样，这个函数可能触发"index"事件的元方法（见§2.8）。   

--------------------------------------------------------------------------------  

lua_getglobal  
[-0, +1, e]   

void lua_getglobal (lua_State *L, const char *name);  

Pushes onto the stack the value of the global name. It is defined as a macro:   

压入全局变量name的值到栈上。它被定义为一个宏：   

     #define lua_getglobal(L,s)  lua_getfield(L, LUA_GLOBALSINDEX, s)  

--------------------------------------------------------------------------------  

lua_getmetatable  
[-0, +(0|1), -]   

int lua_getmetatable (lua_State *L, int index);  

Pushes onto the stack the metatable of the value at the given acceptable index. If the index is not valid, or if the value does not have a metatable, the function returns 0 and pushes nothing on the stack.   

压入给定可接受索引上的值的元表到栈上。如果索引是非可用的，或者如果该值没有元表，该函数返回0并且不压入东西到栈上。   

--------------------------------------------------------------------------------  

lua_gettable  
[-1, +1, e]   

void lua_gettable (lua_State *L, int index);  

Pushes onto the stack the value t[k], where t is the value at the given valid index and k is the value at the top of the stack.   

压入值t[k]到栈上，这里t是给定可用索引上的值而k是栈顶的值。   

This function pops the key from the stack (putting the resulting value in its place). As in Lua, this function may trigger a metamethod for the "index" event (see §2.8).   

这个函数从栈中弹出键（放置结果值在它的位置中）。如同在Lua中那样，这个函数可能触发一个"index"事件的元方法（见§2.8）。  

--------------------------------------------------------------------------------  

lua_gettop  
[-0, +0, -]   

int lua_gettop (lua_State *L);  

Returns the index of the top element in the stack. Because indices start at 1, this result is equal to the number of elements in the stack (and so 0 means an empty stack).   

返回栈顶元素的索引。因为索引从1开始，所以这个结果等于栈中元素的数量（所以0意味着一个空的栈）。   

--------------------------------------------------------------------------------  

lua_insert  
[-1, +1, -]   

void lua_insert (lua_State *L, int index);  

Moves the top element into the given valid index, shifting up the elements above this index to open space. Cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.   

移动栈顶元素进给定可用索引中，上移这个索引上方的元素以打开空间。不能使用伪索引调用它，因为一个伪索引不是一个实际栈位置。   

--------------------------------------------------------------------------------  

lua_Integer  

typedef ptrdiff_t lua_Integer;  

The type used by the Lua API to represent integral values.   

被Lua APU使用的类型以代表整数值。  

By default it is a ptrdiff_t, which is usually the largest signed integral type the machine handles "comfortably".   

默认它是ptrdiff_t，它通常是机器“舒服地”处理的最大有符号整型。   

--------------------------------------------------------------------------------  

lua_isboolean  
[-0, +0, -]   

int lua_isboolean (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index has type boolean, and 0 otherwise.   

返回1如果给定可接受索引上的值拥有类型boolean，否则返回0。   

--------------------------------------------------------------------------------  

lua_iscfunction  
[-0, +0, -]   

int lua_iscfunction (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is a C function, and 0 otherwise.   

返回1如果给定可接受索引上的值是一个C函数，否则返回0。  

--------------------------------------------------------------------------------  

lua_isfunction  
[-0, +0, -]   

int lua_isfunction (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is a function (either C or Lua), and 0 otherwise.   

返回1如果给定可接受索引上的值是一个函数（C或Lua），否则返回0。  

--------------------------------------------------------------------------------  

lua_islightuserdata  
[-0, +0, -]   

int lua_islightuserdata (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is a light userdata, and 0 otherwise.   

返回1如果给定可接受索引上的值是一个轻量级用户数据，否则返回0。  

--------------------------------------------------------------------------------  

lua_isnil  
[-0, +0, -]   

int lua_isnil (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is nil, and 0 otherwise.   

返回1如果给定可接受索引上的值为nil，否则返回0。  

--------------------------------------------------------------------------------  

lua_isnone  
[-0, +0, -]   

int lua_isnone (lua_State *L, int index);  

Returns 1 if the given acceptable index is not valid (that is, it refers to an element outside the current stack), and 0 otherwise.   

返回1如果给定可接受索引上的值不是可用的（就是说，它引用的元素在当前堆栈以外），否则返回0。  

--------------------------------------------------------------------------------  

lua_isnoneornil  
[-0, +0, -]   

int lua_isnoneornil (lua_State *L, int index);  

Returns 1 if the given acceptable index is not valid (that is, it refers to an element outside the current stack) or if the value at this index is nil, and 0 otherwise.   

返回1如果给定可接受索引上的值不是可用的（就是说，它引用的元素在当前堆栈以外）或者索引上的值为nil，否则返回0。  

--------------------------------------------------------------------------------  

lua_isnumber  
[-0, +0, -]   

int lua_isnumber (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is a number or a string convertible to a number, and 0 otherwise.   

返回1如果给定可接受索引上的值是一个数或一个可转换为数的字符串，否则返回0。  

--------------------------------------------------------------------------------  

lua_isstring  
[-0, +0, -]   

int lua_isstring (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is a string or a number (which is always convertible to a string), and 0 otherwise.   

返回1如果给定可接受索引上的值是一个字符串或一个数（它总是可转换为字符串），否则返回0。  

--------------------------------------------------------------------------------  

lua_istable  
[-0, +0, -]   

int lua_istable (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is a table, and 0 otherwise.   

返回1如果给定可接受索引上的值是一个表，否则返回0。  

--------------------------------------------------------------------------------  

lua_isthread  
[-0, +0, -]   

int lua_isthread (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is a thread, and 0 otherwise.   

返回1如果给定可接受索引上的值是一个线程，否则返回0。  

--------------------------------------------------------------------------------  

lua_isuserdata  
[-0, +0, -]   

int lua_isuserdata (lua_State *L, int index);  

Returns 1 if the value at the given acceptable index is a userdata (either full or light), and 0 otherwise.   

返回1如果给定可接受索引上的值是一个用户数据（完全或者轻量级），否则返回0。  

--------------------------------------------------------------------------------  

lua_lessthan  
[-0, +0, e]   

int lua_lessthan (lua_State *L, int index1, int index2);  

Returns 1 if the value at acceptable index index1 is smaller than the value at acceptable index index2, following the semantics of the Lua < operator (that is, may call metamethods). Otherwise returns 0. Also returns 0 if any of the indices is non valid.   

返回1如果给定可接受索引index1上的值小于索引index2上的值，根据Lua的小于操作符的语义（就是说，可能调用元方法）。否则返回0。也返回0，如果任意一个索引不是可用的。  


--------------------------------------------------------------------------------  

lua_load  
[-0, +1, -]   

int lua_load (lua_State *L,  
              lua_Reader reader,  
              void *data,  
              const char *chunkname);  

Loads a Lua chunk. If there are no errors, lua_load pushes the compiled chunk as a Lua function on top of the stack. Otherwise, it pushes an error message. The return values of lua_load are:   

加载一个Lua数据块。如果没有错误，lua_load压入被编译的数据块作为一个Lua函数到栈顶。否则，它压入一个错误消息。lua_load的返回值有：  

0: no errors;   

0：无错误;   

LUA_ERRSYNTAX: syntax error during pre-compilation;   

LUA_ERRSYNTAX：在预编译期间有语法错误；   

LUA_ERRMEM: memory allocation error.   

LUA_ERRMEM: 内存分配错误。  

This function only loads a chunk; it does not run it.   

这个函数只加载一个数据块；它不运行它。  

lua_load automatically detects whether the chunk is text or binary, and loads it accordingly (see program luac).   

lua_load自动检测数据块是文本还是二进制的，并相应地加载它（见程序luac）。  

The lua_load function uses a user-supplied reader function to read the chunk (see lua_Reader). The data argument is an opaque value passed to the reader function.   

lua_load函数使用一个用户提供的读取器函数读取数据块（见lua_Reader）。data参数是传递给读取器函数的一个不透明值。  

The chunkname argument gives a name to the chunk, which is used for error messages and in debug information (see §3.8).   

chunkname参数给一个名称给数据块，它被用于错误消息和用在调试信息中（见§3.8）。   

--------------------------------------------------------------------------------  

lua_newstate  
[-0, +0, -]   

lua_State *lua_newstate (lua_Alloc f, void *ud);  

Creates a new, independent state. Returns NULL if cannot create the state (due to lack of memory). The argument f is the allocator function; Lua does all memory allocation for this state through this function. The second argument, ud, is an opaque pointer that Lua simply passes to the allocator in every call.   

创建一个新的、独立的状态。返回NULL如果无法创建状态（因为缺少内存）。参数f是分配器函数；Lua通过这个函数为这个状态执行所有内存分配。第二个参数，ud，是一个不透明指针。Lua在每次调用时简单地传递它给分配器函数。  

--------------------------------------------------------------------------------  

lua_newtable  
[-0, +1, m]   

void lua_newtable (lua_State *L);  

Creates a new empty table and pushes it onto the stack. It is equivalent to lua_createtable(L, 0, 0).   

创建一个新的空表并压入它到栈上。它等效于lua_createtable(L, 0, 0)。   

--------------------------------------------------------------------------------  

lua_newthread  
[-0, +1, m]   

lua_State *lua_newthread (lua_State *L);  

Creates a new thread, pushes it on the stack, and returns a pointer to a lua_State that represents this new thread. The new state returned by this function shares with the original state all global objects (such as tables), but has an independent execution stack.   

创建一个新线程，把它压到栈上，并返回一个指向一个lua_State的指针，它代表这个新的线程。被这个函数返回的新状态与原始状态共享所有全局对象（诸如表），但拥有一个独立的执行栈。   

There is no explicit function to close or to destroy a thread. Threads are subject to garbage collection, like any Lua object.   

没有显式函数关闭或销毁一个线程。像任意Lua对象那样，线程受控于垃圾回收。   

--------------------------------------------------------------------------------  

lua_newuserdata  
[-0, +1, m]   

void *lua_newuserdata (lua_State *L, size_t size);  

This function allocates a new block of memory with the given size, pushes onto the stack a new full userdata with the block address, and returns this address.   

这个函数用给定的大小分配一个新内存块，压入一个带块地址的新的完全用户数据到栈上，并返回这个地址。   

Userdata represent C values in Lua. A full userdata represents a block of memory. It is an object (like a table): you must create it, it can have its own metatable, and you can detect when it is being collected. A full userdata is only equal to itself (under raw equality).   

用户数据代表Lua中的C值。一个完全用户数据代表一个内存块。它是一个对象（像表那样）：你必须创建它，它可以有自己的元表，而且你能检测它何时被回收。一个完全用户数据只等于它自己（依照原始的相等比较）。  

When Lua collects a full userdata with a gc metamethod, Lua calls the metamethod and marks the userdata as finalized. When this userdata is collected again then Lua frees its corresponding memory.   

当Lua用一个gc元方法回收一个完全用户数据时，Lua调用该元方法并标记用户数据为已终结。当这个用户数据被再次回收时，Lua释放它对应的内存。   

--------------------------------------------------------------------------------  

lua_next  
[-1, +(2|0), e]   

int lua_next (lua_State *L, int index);  

Pops a key from the stack, and pushes a key-value pair from the table at the given index (the "next" pair after the given key). If there are no more elements in the table, then lua_next returns 0 (and pushes nothing).   

从栈中弹出一个键，并压入来自该表的一个键值对到给定索引上（在给定键后面的“下一个”对）。如果在该表里没有更多的元素，那么lua_next返回0（而且不压入东西）。  

A typical traversal looks like this:   

一个典型的遍历看起来像这样：   

     /* table is in the stack at index 't' */  
     lua_pushnil(L);  /* first key */  
     while (lua_next(L, t) != 0) {  
       /* uses 'key' (at index -2) and 'value' (at index -1) */  
       printf("%s - %s\n",  
              lua_typename(L, lua_type(L, -2)),  
              lua_typename(L, lua_type(L, -1)));  
       /* removes 'value'; keeps 'key' for next iteration */  
       lua_pop(L, 1);  
     }  

（翻译如下：  
/* 表位于栈中的索引't'上 */  
lua_pushnil(L);  /* 第一个键 */  
while (lua_next(L, t) != 0) {  
	/* 使用“键”（在索引-2上）和‘值’(在索引-1上） */  
	printf("%s - %s\n",  
		lua_typename(L, lua_type(L, -2)),  
		lua_typename(L, lua_type(L, -1)));  
	/* 移除“值”；保留“键”给下一次迭代用 */  
	lua_pop(L, 1);  
}  
）  

While traversing a table, do not call lua_tolstring directly on a key, unless you know that the key is actually a string. Recall that lua_tolstring changes the value at the given index; this confuses the next call to lua_next.   

当遍历一个表时，不要直接在一个键上调用lua_tolstring，除非你知道该键实际上是一个字符串。回想一下lua_tolstring改变给定索引上的值；这会搞乱对lua_next的下一次调用。   

-----------------------------------------

## 参考自：  
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  

2. hshqcn  
hshqcn  
