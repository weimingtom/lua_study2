## 【翻译】(LRM5.1-7)函数和类型(3.7)(lua_Alloc - lua_gc)

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

## 3.7 - Functions and Types  

## 3.7 - 函数和类型  

Here we list all functions and types from the C API in alphabetical order. Each function has an indicator like this: [-o, +p, x]   

这里我们以字母顺序列举所有CAPI的函数和类型。每个函数有一个好像这样子的指示符：[-o, +p, x]  

The first field, o, is how many elements the function pops from the stack. The second field, p, is how many elements the function pushes onto the stack. (Any function always pushes its results after popping its arguments.) A field in the form x|y means the function can push (or pop) x or y elements, depending on the situation; an interrogation mark '?' means that we cannot know how many elements the function pops/pushes by looking only at its arguments (e.g., they may depend on what is on the stack). The third field, x, tells whether the function may throw errors: '-' means the function never throws any error; 'm' means the function may throw an error only due to not enough memory; 'e' means the function may throw other kinds of errors; 'v' means the function may throw an error on purpose.   

第一个字段o是指有多少元素从堆栈中弹出。第二个字段p是指有多少元素压入堆栈中。（如何函数总是在弹出它的参数之后压入它的结果。）以x|y形式表示的字段意思是函数可以压入（或弹出）x或y个元素，依赖于实际情况；一个问号'?'意思是只通过看它的参数我们无法知道函数弹出或压入多少元素（例如，它们可能依赖于堆栈里的东西）。第三个字段x告知函数是否抛出错误：'-'表示函数从不抛出错误；'m'表示函数只会因为不够内存才会抛出错误；'e'表示函数可能会抛出其它类型的错误；'v'表示函数可能会故意抛出错误。  

--------------------------------------------------------------------------------  

lua_Alloc  
typedef void * (*lua_Alloc) (void *ud,  
                             void *ptr,  
                             size_t osize,  
                             size_t nsize);  

The type of the memory-allocation function used by Lua states. The allocator function must provide a functionality similar to realloc, but not exactly the same. Its arguments are ud, an opaque pointer passed to lua_newstate; ptr, a pointer to the block being allocated/reallocated/freed; osize, the original size of the block; nsize, the new size of the block. ptr is NULL if and only if osize is zero. When nsize is zero, the allocator must return NULL; if osize is not zero, it should free the block pointed to by ptr. When nsize is not zero, the allocator returns NULL if and only if it cannot fill the request. When nsize is not zero and osize is zero, the allocator should behave like malloc. When nsize and osize are not zero, the allocator behaves like realloc. Lua assumes that the allocator never fails when osize >= nsize.   

这种类型的内存分配函数由Lua状态机使用。分配器函数必须提供类似realloc的功能，但是不完全相同。它的参数为ud，一个由lua_newstate传入的不透明指针**（注：不透明指针，是指指向不确定类型的指针）**；ptr，一个指向即将被分配/重分配/释放的内存块的指针；osize，内存块原来的尺寸；nsize，内存块的新尺寸。ptr为NULL，当且仅当osize是0时。当nsize是0时，分配器必须返回NULL；如果osize非0，它应该释放ptr指向的内存块。当nsize不是0时，分配器返回NULL，当且仅当它不能满足请求时。当nsize不是0而osize是0时，分配器应该像malloc那样行为。当nsize和osize都不是0时，分配器像realloc那样行为。Lua假设分配器从不失败，当osize >= nsize时。  

Here is a simple implementation for the allocator function. It is used in the auxiliary library by luaL_newstate.   

这里有一个分配器函数的简单实现。在辅助库中它被luaL_newstate使用。  

     static void *l_alloc (void *ud, void *ptr, size_t osize,  
                                                size_t nsize) {  
       (void)ud;  (void)osize;  /* not used */  
       if (nsize == 0) {  
         free(ptr);  
         return NULL;  
       }  
       else  
         return realloc(ptr, nsize);  
     }  

（注：翻译如下：  
static void *l_alloc (void *ud, void *ptr, size_t osize,  
										size_t nsize) {  
	(void)ud;  (void)osize;  /* 不使用 */  
	if (nsize == 0) {  
		free(ptr);  
		return NULL;  
	}  
	else  
		return realloc(ptr, nsize);  
}  
）  

This code assumes that free(NULL) has no effect and that realloc(NULL, size) is equivalent to malloc(size). ANSI C ensures both behaviors.   

该代码假设free(NULL)不起作用以及realloc(NULL, size)等价于malloc(size)。ANSI C确保这两种行为。   

--------------------------------------------------------------------------------  

lua_atpanic  
[-0, +0, -]   

lua_CFunction lua_atpanic (lua_State *L, lua_CFunction panicf);  

Sets a new panic function and returns the old one.   

设置新的紧急函数并返回旧的。   

If an error happens outside any protected environment, Lua calls a panic function and then calls exit(EXIT_FAILURE), thus exiting the host application. Your panic function can avoid this exit by never returning (e.g., doing a long jump).   

如果错误发生在任意受保护环境以外，Lua调用一个紧急函数然后调用exit(EXIT_FAILURE)，从而退出宿主程序。你的紧急函数可以避免这个exit，通过从不返回（例如，执行长跳转）来做到。   

The panic function can access the error message at the top of the stack.   

紧急函数可以访问栈顶的错误消息。  

--------------------------------------------------------------------------------  

lua_call  
[-(nargs + 1), +nresults, e]   

void lua_call (lua_State *L, int nargs, int nresults);  

Calls a function.   

调用一个函数。   

To call a function you must use the following protocol: first, the function to be called is pushed onto the stack; then, the arguments to the function are pushed in direct order; that is, the first argument is pushed first. Finally you call lua_call; nargs is the number of arguments that you pushed onto the stack. All arguments and the function value are popped from the stack when the function is called. The function results are pushed onto the stack when the function returns. The number of results is adjusted to nresults, unless nresults is LUA_MULTRET. In this case, all results from the function are pushed. Lua takes care that the returned values fit into the stack space. The function results are pushed onto the stack in direct order (the first result is pushed first), so that after the call the last result is on the top of the stack.   

为了调用一个函数，你必须使用**（注：遵守）**以下协议：首先，要被调用的函数被压到栈上；然后，将传给函数的参数以直接的顺序被压入；就是说，第一个参数最先被压入。最后，你调用lua_call；nargs是你压入栈的参数的个数。所有参数和函数值从栈中被弹出，当函数被调用时。函数结果被压到栈上，当函数返回时。结果的数量被调整为nresults个，除非nresults是LUA_MULTRET。在这种情况下，来自函数的所有结果被压入。Lua注意被返回的值**（注：的空间）**适合栈的空间。函数结果以直接顺序被压在栈上（第一个结果被首先压入），致使在调用后最后的结果处于栈顶。  

Any error inside the called function is propagated upwards (with a longjmp).   

被调用的函数内的任意错误被向上传送（使用longjmp）。  

The following example shows how the host program can do the equivalent to this Lua code:   

下面的例子展示宿主程序可以做与这段Lua代码等效的操作：   

     a = f("how", t.x, 14)  

Here it is in C:   

这里用C写：  

     lua_getfield(L, LUA_GLOBALSINDEX, "f"); /* function to be called */  
     lua_pushstring(L, "how");                        /* 1st argument */  
     lua_getfield(L, LUA_GLOBALSINDEX, "t");   /* table to be indexed */  
     lua_getfield(L, -1, "x");        /* push result of t.x (2nd arg) */  
     lua_remove(L, -2);                  /* remove 't' from the stack */  
     lua_pushinteger(L, 14);                          /* 3rd argument */  
     lua_call(L, 3, 1);     /* call 'f' with 3 arguments and 1 result */  
     lua_setfield(L, LUA_GLOBALSINDEX, "a");        /* set global 'a' */  

（注：翻译如下：  
     lua_getfield(L, LUA_GLOBALSINDEX, "f");          /* 要被调用的函数 */  
     lua_pushstring(L, "how");                             /* 第1个参数 */  
     lua_getfield(L, LUA_GLOBALSINDEX, "t");            /* 要被索引的表 */  
     lua_getfield(L, -1, "x");           /* 压入t.x的结果（第2个参数） */  
     lua_remove(L, -2);                  /* 从栈中删除't' */  
     lua_pushinteger(L, 14);                               /* 第3个参数 */  
     lua_call(L, 3, 1);               /* 用3个参数和1个结果调用'f' */  
     lua_setfield(L, LUA_GLOBALSINDEX, "a");        /* 设置全局变量'a' */  
）  

Note that the code above is "balanced": at its end, the stack is back to its original configuration. This is considered good programming practice.   

注意上面的代码是“平衡的”：在它的最后，栈恢复它原来的配置。这被认为是一条良好的编程实践。   

--------------------------------------------------------------------------------  

lua_CFunction  

typedef int (*lua_CFunction) (lua_State *L);  

Type for C functions.   

C函数的类型。   

In order to communicate properly with Lua, a C function must use the following protocol, which defines the way parameters and results are passed: a C function receives its arguments from Lua in its stack in direct order (the first argument is pushed first). So, when the function starts, lua_gettop(L) returns the number of arguments received by the function. The first argument (if any) is at index 1 and its last argument is at index lua_gettop(L). To return values to Lua, a C function just pushes them onto the stack, in direct order (the first result is pushed first), and returns the number of results. Any other value in the stack below the results will be properly discarded by Lua. Like a Lua function, a C function called by Lua can also return many results.   

为了与Lua正确地通讯，一个C函数必须使用以下协议，它定义参数和结果被传递的方式：一个C函数以直接顺序从Lua中接收它的参数（第一个参数被首先压入）。所以，当函数开始时，lua_gettop(L)返回被函数接收的参数的数量。第一参数（如果有的话）是在索引1上而它的最后参数是在索引lua_gettop(L)上。为了返回值到Lua，一个C函数只是压入它们到栈上，以直接的顺序（第一个结果被首先压入），并且返回结果的数量。在栈中结果下方的其它任意值将被Lua正确地丢弃。像一个Lua函数那样，一个被Lua调用的C函数也可以返回许多结果。  

As an example, the following function receives a variable number of numerical arguments and returns their average and sum:   

作为一个例子，以下函数接收一些数量的数字型参数，并且返回它们的平均值与总和：   

     static int foo (lua_State *L) {  
       int n = lua_gettop(L);    /* number of arguments */  
       lua_Number sum = 0;  
       int i;  
       for (i = 1; i <= n; i++) {  
         if (!lua_isnumber(L, i)) {  
           lua_pushstring(L, "incorrect argument");  
           lua_error(L);  
         }  
         sum += lua_tonumber(L, i);  
       }  
       lua_pushnumber(L, sum/n);        /* first result */  
       lua_pushnumber(L, sum);         /* second result */  
       return 2;                   /* number of results */  
     }  

（注：翻译如下：  
     static int foo (lua_State *L) {  
       int n = lua_gettop(L);    /* 参数的数量 */  
       lua_Number sum = 0;  
       int i;  
       for (i = 1; i <= n; i++) {  
         if (!lua_isnumber(L, i)) {  
           lua_pushstring(L, "incorrect argument");   
           lua_error(L);  
         }  
         sum += lua_tonumber(L, i);  
       }  
       lua_pushnumber(L, sum/n);        /* 第1个结果 */  
       lua_pushnumber(L, sum);         /* 第2个结果 */  
       return 2;                   /* 结果的个数 */  
     }  
）  

--------------------------------------------------------------------------------  

lua_checkstack  
[-0, +0, m]   

int lua_checkstack (lua_State *L, int extra);  

Ensures that there are at least extra free stack slots in the stack. It returns false if it cannot grow the stack to that size. This function never shrinks the stack; if the stack is already larger than the new size, it is left unchanged.   

确保在栈中有至少extra个空闲栈槽。返回false如果它不能增长栈到那个大小。这个函数从不缩小栈；如果栈已经大于新的大小，那么它保持不变。  

--------------------------------------------------------------------------------  

lua_close  
[-0, +0, -]   

void lua_close (lua_State *L);  

Destroys all objects in the given Lua state (calling the corresponding garbage-collection metamethods, if any) and frees all dynamic memory used by this state. On several platforms, you may not need to call this function, because all resources are naturally released when the host program ends. On the other hand, long-running programs, such as a daemon or a web server, might need to release states as soon as they are not needed, to avoid growing too large.   

销毁给定Lua状态中的全部对象（调用响应的垃圾收集元方法，如果有的话）并且释放被这个状态使用的所有动态内存。在一些平台上，你可能不需要调用这个函数，因为当宿主程序结束时，所有资源自然地被释放。另一方面，长期运行的程序，比如一个后台程序或网页服务器，可能需要当不需要它们时就马上释放它们，以避免增长过大。   

--------------------------------------------------------------------------------  

lua_concat  
[-n, +1, e]   

void lua_concat (lua_State *L, int n);  

Concatenates the n values at the top of the stack, pops them, and leaves the result at the top. If n is 1, the result is the single value on the stack (that is, the function does nothing); if n is 0, the result is the empty string. Concatenation is performed following the usual semantics of Lua (see §2.5.4).   

拼接栈顶的n个值，弹出它们，并且保留结果在栈顶。如果n是1，那么结果就是栈上的单一值（就是说，函数什么都不做）；如果n是0，那么结果是空的字符串。拼接操作依照Lua的常规语义被执行（见§2.5.4）。   

--------------------------------------------------------------------------------  

lua_cpcall  
[-0, +(0|1), -]   

int lua_cpcall (lua_State *L, lua_CFunction func, void *ud);  

Calls the C function func in protected mode. func starts with only one element in its stack, a light userdata containing ud. In case of errors, lua_cpcall returns the same error codes as lua_pcall, plus the error object on the top of the stack; otherwise, it returns zero, and does not change the stack. All values returned by func are discarded.   

在保护模式中调用C函数func。func在它的栈中以唯一一个元素开始，一个包含ud的轻量级用户数据。在出现错误的情况下，lua_cpcall返回与lua_pcall相同的错误码，加上栈顶的错误对象；否则，它返回0，且不改变栈。被func返回的所有值都被丢弃。   

--------------------------------------------------------------------------------  

lua_createtable  
[-0, +1, m]   

void lua_createtable (lua_State *L, int narr, int nrec);  

Creates a new empty table and pushes it onto the stack. The new table has space pre-allocated for narr array elements and nrec non-array elements. This pre-allocation is useful when you know exactly how many elements the table will have. Otherwise you can use the function lua_newtable.   

创建一个新的空表并压入它到栈上。该新表拥有为narr个数组元素和nrec个非数组元素的预分配的空间，这个预分配是有用的，当你准确地知道该表将拥有有多少元素。否则你可以使用函数lua_newtable。  

--------------------------------------------------------------------------------  

lua_dump  
[-0, +0, m]   

int lua_dump (lua_State *L, lua_Writer writer, void *data);  

Dumps a function as a binary chunk. Receives a Lua function on the top of the stack and produces a binary chunk that, if loaded again, results in a function equivalent to the one dumped. As it produces parts of the chunk, lua_dump calls function writer (see lua_Writer) with the given data to write them.   

转储一个函数为一个二进制数据块。接收栈顶的一个Lua函数并产生一个二进制数据块，如果它被再次加载，它导致一个等效于被转储数据块的函数。当它产生部分数据块时，lua_dump用给定数据调用函数writer（见lua_Writer）以写入它们。  

The value returned is the error code returned by the last call to the writer; 0 means no errors.   

返回的值是被最后对写入器的调用返回的错误码；0意味着无错误。  

This function does not pop the Lua function from the stack.   

这个函数不会从栈中弹出Lua函数。   

--------------------------------------------------------------------------------  

lua_equal  
[-0, +0, e]   

int lua_equal (lua_State *L, int index1, int index2);  

Returns 1 if the two values in acceptable indices index1 and index2 are equal, following the semantics of the Lua == operator (that is, may call metamethods). Otherwise returns 0. Also returns 0 if any of the indices is non valid.   

返回1如果两个在可接受索引上的值index1和index2是相等的，遵循Lua的==操作符的语义（就是说，可能调用元方法）。否则返回0，如果任意索引是非可用的。  

--------------------------------------------------------------------------------  

lua_error  
[-1, +0, v]   

int lua_error (lua_State *L);  

Generates a Lua error. The error message (which can actually be a Lua value of any type) must be on the stack top. This function does a long jump, and therefore never returns. (see luaL_error).   

生成一个Lua错误。错误消息（它实际上可以是任何类型的Lua值）必须在栈顶。这个函数执行一次长跳转，因此从不返回。（见luaL_error）。   

--------------------------------------------------------------------------------  

lua_gc  
[-0, +0, e]   

int lua_gc (lua_State *L, int what, int data);  

Controls the garbage collector.   

控制垃圾回收器。   

This function performs several tasks, according to the value of the parameter what:   

这个函数执行几个任务，根据参数what的值：   

LUA_GCSTOP: stops the garbage collector.   

LUA_GCSTOP: 停止垃圾回收器。   

LUA_GCRESTART: restarts the garbage collector.   

LUA_GCRESTART: 重新启动垃圾回收器。   

LUA_GCCOLLECT: performs a full garbage-collection cycle.   

LUA_GCCOLLECT: 执行一次完全垃圾回收周期。   

LUA_GCCOUNT: returns the current amount of memory (in Kbytes) in use by Lua.   

LUA_GCCOUNT: 返回Lua当前使用的内存数量（以Kbyte为单位）**（注：除于1024）**。   

LUA_GCCOUNTB: returns the remainder of dividing the current amount of bytes of memory in use by Lua by 1024.   

LUA_GCCOUNTB: 返回Lua当前内存使用字节数除于1024的余数。   

LUA_GCSTEP: performs an incremental step of garbage collection. The step "size" is controlled by data (larger values mean more steps) in a non-specified way. If you want to control the step size you must experimentally tune the value of data. The function returns 1 if the step finished a garbage-collection cycle.   

LUA_GCSTEP: 执行一次增量步进的垃圾收集。步进“大小”被参数data以非指定的方式控制（越大的值意味着越多步数）。如果你想控制步长大小，你必须试验性地调整data的值。函数返回1如果该步骤完成一次垃圾回收周期。  

LUA_GCSETPAUSE: sets data as the new value for the pause of the collector (see §2.10). The function returns the previous value of the pause.   

LUA_GCSETPAUSE: 设置data作为用于回收器暂停的新值（见§2.10）。返回暂停的前一个值。  

LUA_GCSETSTEPMUL: sets data as the new value for the step multiplier of the collector (see §2.10). The function returns the previous value of the step multiplier.   

LUA_GCSETSTEPMUL: 设置参数data作为回收器的步长乘数的新值（见§2.10）。该函数返回步长常数的前一个值。   

-----------------------------------------

## 参考自：  
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  

2. hshqcn  
hshqcn  
