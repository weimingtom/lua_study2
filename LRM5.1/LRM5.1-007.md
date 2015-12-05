﻿【翻译】(LRM5.1-7)函数和类型(3.7)

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

3.7 - Functions and Types

3.7 - 函数和类型

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

这种类型的内存分配函数由Lua状态机使用。分配器函数必须提供类似realloc的功能，但是不完全相同。它的参数为ud，一个由lua_newstate传入的不透明指针（注：不透明指针，是指指向不确定类型的指针）；ptr，一个指向即将被分配/重分配/释放的内存块的指针；osize，内存块原来的尺寸；nsize，内存块的新尺寸。ptr为NULL，当且仅当osize是0时。当nsize是0时，分配器必须返回NULL；如果osize非0，它应该释放ptr指向的内存块。当nsize不是0时，分配器返回NULL，当且仅当它不能满足请求时。当nsize不是0而osize是0时，分配器应该像malloc那样行为。当nsize和osize都不是0时，分配器像realloc那样行为。Lua假设分配器从不失败，当osize >= nsize时。

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

为了调用一个函数，你必须使用（注：遵守）以下协议：首先，要被调用的函数被压到栈上；然后，将传给函数的参数以直接的顺序被压入；就是说，第一个参数最先被压入。最后，你调用lua_call；nargs是你压入栈的参数的个数。所有参数和函数值从栈中被弹出，当函数被调用时。函数结果被压到栈上，当函数返回时。结果的数量被调整为nresults个，除非nresults是LUA_MULTRET。在这种情况下，来自函数的所有结果被压入。Lua注意被返回的值（注：的空间）适合栈的空间。函数结果以直接顺序被压在栈上（第一个结果被首先压入），致使在调用后最后的结果处于栈顶。

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

LUA_GCCOUNT: 返回Lua当前使用的内存数量（以Kbyte为单位）（注：除于1024）。 

LUA_GCCOUNTB: returns the remainder of dividing the current amount of bytes of memory in use by Lua by 1024. 

LUA_GCCOUNTB: 返回Lua当前内存使用字节数除于1024的余数。 

LUA_GCSTEP: performs an incremental step of garbage collection. The step "size" is controlled by data (larger values mean more steps) in a non-specified way. If you want to control the step size you must experimentally tune the value of data. The function returns 1 if the step finished a garbage-collection cycle. 

LUA_GCSTEP: 执行一次增量步进的垃圾收集。步进“大小”被参数data以非指定的方式控制（越大的值意味着越多步数）。如果你想控制步长大小，你必须试验性地调整data的值。函数返回1如果该步骤完成一次垃圾回收周期。

LUA_GCSETPAUSE: sets data as the new value for the pause of the collector (see §2.10). The function returns the previous value of the pause. 

LUA_GCSETPAUSE: 设置data作为用于回收器暂停的新值（见§2.10）。返回暂停的前一个值。

LUA_GCSETSTEPMUL: sets data as the new value for the step multiplier of the collector (see §2.10). The function returns the previous value of the step multiplier. 

LUA_GCSETSTEPMUL: 设置参数data作为回收器的步长乘数的新值（见§2.10）。该函数返回步长常数的前一个值。 

--------------------------------------------------------------------------------

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

--------------------------------------------------------------------------------

lua_Number

typedef double lua_Number;

The type of numbers in Lua. By default, it is double, but that can be changed in luaconf.h. 

Lua中的数的类型。缺省，它是double（注：双精型），但在luaconf.h中可以改变它。

Through the configuration file you can change Lua to operate with another type for numbers (e.g., float or long). 

通过配置文件你能改变Lua用另一个类型操作数（例如，float（注：浮点型）或long（注：长整型））。 

--------------------------------------------------------------------------------

lua_objlen
[-0, +0, -] 

size_t lua_objlen (lua_State *L, int index);

Returns the "length" of the value at the given acceptable index: for strings, this is the string length; for tables, this is the result of the length operator ('#'); for userdata, this is the size of the block of memory allocated for the userdata; for other values, it is 0. 

返回给定可接受索引上的值的“长度”：对于字符串，这是字符串长度；对于表，这是长度操作符（"#"）的结果；对于用户数据，这是为该用户数据分配的内存块的大小；对于其它类型，它是0。

--------------------------------------------------------------------------------

lua_pcall
[-(nargs + 1), +(nresults|1), -] 

int lua_pcall (lua_State *L, int nargs, int nresults, int errfunc);

Calls a function in protected mode. 

在保护模式下调用一个函数。 

Both nargs and nresults have the same meaning as in lua_call. If there are no errors during the call, lua_pcall behaves exactly like lua_call. However, if there is any error, lua_pcall catches it, pushes a single value on the stack (the error message), and returns an error code. Like lua_call, lua_pcall always removes the function and its arguments from the stack. 

nargs和nresults与lua_call中的拥有相同的意思。如果在该调用期间没有错误，那么lua_pcall的行为完全像lua_call。然而，如果有任意错误，那么lua_pcall捕捉它，压入一个单一值到栈上（错误消息），并返回一个错误码。像lua_call那样，lua_pcall总是从栈中移除该函数及它的参数。

If errfunc is 0, then the error message returned on the stack is exactly the original error message. Otherwise, errfunc is the stack index of an error handler function. (In the current implementation, this index cannot be a pseudo-index.) In case of runtime errors, this function will be called with the error message and its return value will be the message returned on the stack by lua_pcall. 

如果errfunc是0，那么在栈上返回的错误消息正好是原始的错误消息。否则，errfunc是一个错误句柄函数的栈索引。（在当前实现中，这个索引不能是伪索引。）在发生运行时错误的情况下，这个函数将用错误消息被调用，而它的返回值将是在栈上被lua_pcall返回的消息。

Typically, the error handler function is used to add more debug information to the error message, such as a stack traceback. Such information cannot be gathered after the return of lua_pcall, since by then the stack has unwound. 

通常，错误句柄函数被用于添加更多调试信息到错误消息，诸如一个栈回溯。这些信息无法在lua_pcall返回后被收集，因为到那时候栈已经被展开。 

The lua_pcall function returns 0 in case of success or one of the following error codes (defined in lua.h): 

lua_pcall函数返回0如果成功，或返回以下其中一个错误码（定义在lua.h中）： 

LUA_ERRRUN: a runtime error. 

LUA_ERRRUN：一个运行时错误。

LUA_ERRMEM: memory allocation error. For such errors, Lua does not call the error handler function. 

LUA_ERRMEM：内存分配错误。对于这些错误，Lua不调用错误句柄函数。 

LUA_ERRERR: error while running the error handler function. 

LUA_ERRERR：错误，当运行错误句柄函数时。 

--------------------------------------------------------------------------------

lua_pop
[-n, +0, -] 

void lua_pop (lua_State *L, int n);

Pops n elements from the stack. 

从栈中弹出n个元素。 

--------------------------------------------------------------------------------

lua_pushboolean
[-0, +1, -] 

void lua_pushboolean (lua_State *L, int b);

Pushes a boolean value with value b onto the stack. 

压入带值b的一个布尔值到栈上。

--------------------------------------------------------------------------------

lua_pushcclosure
[-n, +1, m] 

void lua_pushcclosure (lua_State *L, lua_CFunction fn, int n);

Pushes a new C closure onto the stack. 

压入新的C闭包到栈上。

When a C function is created, it is possible to associate some values with it, thus creating a C closure (see §3.4); these values are then accessible to the function whenever it is called. To associate values with a C function, first these values should be pushed onto the stack (when there are multiple values, the first value is pushed first). Then lua_pushcclosure is called to create and push the C function onto the stack, with the argument n telling how many values should be associated with the function. lua_pushcclosure also pops these values from the stack. 

当一个C函数被创建时，有可能关联一些值到它，因此创建一个C闭包（见§3.4）；然后这些值对于该函数是可访问的，不管它合适被调用。为了关联值与一个C函数，首先这些值应该被压入到栈上（当有多个值时，第一个值被首先压入）。然后lua_pushcclosure被调用以创建和压入C函数到栈上，用参数n告知有多少值应该与该函数关联。lua_pushcclosure还从栈中弹出这些值。

The maximum value for n is 255. 

n的最大值为255。 

--------------------------------------------------------------------------------

lua_pushcfunction
[-0, +1, m] 

void lua_pushcfunction (lua_State *L, lua_CFunction f);

Pushes a C function onto the stack. This function receives a pointer to a C function and pushes onto the stack a Lua value of type function that, when called, invokes the corresponding C function. 

压入一个C函数到栈上。这个函数接收一个指向C函数的指针并且压入一个类型为function的Lua值到栈上，当这个Lua值被调用时，它调用相应的C函数。

Any function to be registered in Lua must follow the correct protocol to receive its parameters and return its results (see lua_CFunction). 

任何要在Lua中注册的函数必须遵循正确的协议以接收它的参数和返回它的结果（见lua_CFunction）。 

lua_pushcfunction is defined as a macro: 

lua_pushcfunction被定义为一个宏： 

     #define lua_pushcfunction(L,f)  lua_pushcclosure(L,f,0)

--------------------------------------------------------------------------------

lua_pushfstring
[-0, +1, m] 

const char *lua_pushfstring (lua_State *L, const char *fmt, ...);

Pushes onto the stack a formatted string and returns a pointer to this string. It is similar to the C function sprintf, but has some important differences: 

压入一个格式化字符串到栈上并返回指向这个字符串的指针。它类似于C函数sprintf，但有一些重要的区别： 

You do not have to allocate space for the result: the result is a Lua string and Lua takes care of memory allocation (and deallocation, through garbage collection). 

你不必为结果分配空间：结果是一个Lua字符串而Lua会负责内存分配（以及解除分配，通过垃圾收集）。 

The conversion specifiers are quite restricted. There are no flags, widths, or precisions. The conversion specifiers can only be '%%' (inserts a '%' in the string), '%s' (inserts a zero-terminated string, with no size restrictions), '%f' (inserts a lua_Number), '%p' (inserts a pointer as a hexadecimal numeral), '%d' (inserts an int), and '%c' (inserts an int as a character). 

转换说明符有很多限制。没有开关，宽度，或精度。转换说明符只可能是'%%'（在字符串中插入一个'%'），'%s'（插入一个以0终结字符串，没有大小限制），'%f'（插入一个lua_Number），'%p'（插入一个指针作为一个十六进制数字），'%d' （插入一个整型），以及'%c'（插入一个整型作为一个字符）。 

--------------------------------------------------------------------------------

lua_pushinteger
[-0, +1, -] 

void lua_pushinteger (lua_State *L, lua_Integer n);

Pushes a number with value n onto the stack. 

压入一个带有值n的数到栈上。

--------------------------------------------------------------------------------

lua_pushlightuserdata
[-0, +1, -] 

void lua_pushlightuserdata (lua_State *L, void *p);

Pushes a light userdata onto the stack. 

压入一个轻量级用户数据到栈上。 

Userdata represent C values in Lua. A light userdata represents a pointer. It is a value (like a number): you do not create it, it has no individual metatable, and it is not collected (as it was never created). A light userdata is equal to "any" light userdata with the same C address. 

在Lua中用户数据代表C值。一个轻量级用户数据代表一个指针。它是一个值（像一个数）：你不创建它，它没有单独的元表，而且它不会被回收（如同它从不被创建）。一个轻量级用户数据等于“任意”带有相同C地址的轻量级用户数据。 

--------------------------------------------------------------------------------

lua_pushliteral
[-0, +1, m] 

void lua_pushliteral (lua_State *L, const char *s);

This macro is equivalent to lua_pushlstring, but can be used only when s is a literal string. In these cases, it automatically provides the string length. 

这个宏等效于lua_pushlstring，但仅当s是一个字面字符串时才可以被使用。在这些情况下，它自动地提供字符串长度。 

--------------------------------------------------------------------------------

lua_pushlstring
[-0, +1, m] 

void lua_pushlstring (lua_State *L, const char *s, size_t len);

Pushes the string pointed to by s with size len onto the stack. Lua makes (or reuses) an internal copy of the given string, so the memory at s can be freed or reused immediately after the function returns. The string can contain embedded zeros. 

压入被s指向的大小为len的字符串到栈上。Lua制造（或重用）给定字符串的内部副本，所以s上的内存可以在函数返回之后立即被释放或重用。字符串可以包含内嵌的0。

--------------------------------------------------------------------------------

lua_pushnil
[-0, +1, -] 

void lua_pushnil (lua_State *L);

Pushes a nil value onto the stack. 

压入一个nil值到栈上。

--------------------------------------------------------------------------------

lua_pushnumber
[-0, +1, -] 

void lua_pushnumber (lua_State *L, lua_Number n);

Pushes a number with value n onto the stack. 

压入一个值为n的数到栈上。

--------------------------------------------------------------------------------

lua_pushstring
[-0, +1, m] 

void lua_pushstring (lua_State *L, const char *s);

Pushes the zero-terminated string pointed to by s onto the stack. Lua makes (or reuses) an internal copy of the given string, so the memory at s can be freed or reused immediately after the function returns. The string cannot contain embedded zeros; it is assumed to end at the first zero. 

压入被s指向的一个零终结字符串到栈上。Lua制造（或重用）给定字符串的一个内部副本，所以在s上的内存可以在函数返回后即时被释放或重用。该字符串不能包含复数个内嵌零；假设它在第一个零处结束。

--------------------------------------------------------------------------------

lua_pushthread
[-0, +1, -] 

int lua_pushthread (lua_State *L);

Pushes the thread represented by L onto the stack. Returns 1 if this thread is the main thread of its state. 

压入L代表的线程到栈上。返回1，如果这个线程是它的状态的主线程。

--------------------------------------------------------------------------------

lua_pushvalue
[-0, +1, -] 

void lua_pushvalue (lua_State *L, int index);

Pushes a copy of the element at the given valid index onto the stack. 

压入给定可用索引上的元素的一个副本到栈上。

--------------------------------------------------------------------------------

lua_pushvfstring
[-0, +1, m] 

const char *lua_pushvfstring (lua_State *L,
                              const char *fmt,
                              va_list argp);

Equivalent to lua_pushfstring, except that it receives a va_list instead of a variable number of arguments. 

等效于lua_pushfstring，除了接收一个va_list而非一些可变个数的参数。

--------------------------------------------------------------------------------

lua_rawequal
[-0, +0, -] 

int lua_rawequal (lua_State *L, int index1, int index2);

Returns 1 if the two values in acceptable indices index1 and index2 are primitively equal (that is, without calling metamethods). Otherwise returns 0. Also returns 0 if any of the indices are non valid. 

返回1，如果可接受索引index1和index2上的值是原始相等的（就是说，不调用元方法）。否则返回0。还返回0，如果任意一个索引非可用。

--------------------------------------------------------------------------------

lua_rawget
[-1, +1, -] 

void lua_rawget (lua_State *L, int index);

Similar to lua_gettable, but does a raw access (i.e., without metamethods). 

类似于lua_gettable，但执行一次原始访问（即，不使用元方法）。 

--------------------------------------------------------------------------------

lua_rawgeti
[-0, +1, -] 

void lua_rawgeti (lua_State *L, int index, int n);

Pushes onto the stack the value t[n], where t is the value at the given valid index. The access is raw; that is, it does not invoke metamethods. 

压入值t[n]到栈上，这里t是所给可用索引上的值。访问是原始的；就是说，不调用元方法。

--------------------------------------------------------------------------------

lua_rawset
[-2, +0, m] 

void lua_rawset (lua_State *L, int index);

Similar to lua_settable, but does a raw assignment (i.e., without metamethods). 

类似lua_settable，但执行一次原始赋值（即，不使用元方法）。 

--------------------------------------------------------------------------------

lua_rawseti
[-1, +0, m] 

void lua_rawseti (lua_State *L, int index, int n);

Does the equivalent of t[n] = v, where t is the value at the given valid index and v is the value at the top of the stack. 

执行t[n] = v的等效操作，这里t是所给可用索引上的值，而v是栈顶的值。 

This function pops the value from the stack. The assignment is raw; that is, it does not invoke metamethods. 

这个函数从栈中弹出该值。赋值是原始的；就是说，不调用元方法。 

--------------------------------------------------------------------------------

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

为了启动一个协程，你首先创建一个新线程（见lua_newthread）；然后你把主函数和任意参数压进它的栈上；然后你调用lua_resume，参数narg为参数的数量。这个调用返回，当协程暂停或完成它的执行。当它返回时，栈包含传给lua_yield的所有值，或者被本体函数返回的所有值。lua_resume返回LUA_YIELD如果协程挂起，返回0如果协程不带错误地完成它的执行，或一个错误码如果有错误发生（见lua_pcall）。在出错的情况下，栈不是展开（注：unwind有解开的意思）的，所以你可以在它上面使用调试API）。错误消息位于栈顶。为了重启一个协程，你只把要被传递作为挂起结果的值放在它的栈上，然后调用lua_resume。

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

状态可能是0表示一个正常线程，可能是一个错误值如果该线程带一个错误地完成它的执行，或者可能是LUA_YIELD如果线程被挂起（注：暂停）。

--------------------------------------------------------------------------------

lua_toboolean
[-0, +0, -] 

int lua_toboolean (lua_State *L, int index);

Converts the Lua value at the given acceptable index to a C boolean value (0 or 1). Like all tests in Lua, lua_toboolean returns 1 for any Lua value different from false and nil; otherwise it returns 0. It also returns 0 when called with a non-valid index. (If you want to accept only actual boolean values, use lua_isboolean to test the value's type.) 

把所给可接受索引上的Lua值转换为一个C布尔值（0或1）。就像Lua中的所有测试（注：这里指比较操作）那样，返回1，对于任何不是false和nil的值；否则它返回0。它还返回0，当用一个不可用索引调用时。（如果你想只接受实际的布尔值，请使用lua_isboolean来测试该值的类型。）（注：lua_isboolean用于判断是否为布尔类型）

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

转换所给可接受索引上的值为带符号整型lua_Integer（注：一个定义在C中的typedef类型）。该Lua值必须是一个数或一个可转换为数的字符串（参考§2.2.1）；否则，lua_tointeger返回0。

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

把所给可接受索引上的值转换为C类型lua_Number（参考lua_Number）（注：lua_Number是一个typedef，默认为double型）。Lua值必须为一个数或可以转换为数的字符串（参考§2.2.1）；否则，lua_tonumber返回0。

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

如果给定可接受索引上的值是一个完全用户数据，返回它的块（注：这里应该是指数据的内存块）地址。如果该值为轻量级用户数据，返回它的指针。否则，返回NULL。 

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

返回被值tp编码（注：这里指把字符串映射为整数）的类型的名称，它必须是被lua_type返回的值之一。 

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

挂起（注：暂停）一个协程 

This function should only be called as the return expression of a C function, as follows: 

这个函数应该只作为C函数的return表达式被调用（注：即尾调用），如下：

     return lua_yield (L, nresults);

When a C function calls lua_yield in that way, the running coroutine suspends its execution, and the call to lua_resume that started this coroutine returns. The parameter nresults is the number of values from the stack that are passed as results to lua_resume. 

当一个C函数以那种方式调用lua_yield时，正在运行的协程暂停它的执行，而启动这个协程的对lua_resume的调用会返回。参数nresults是来自栈的值的数量，它们被传递作为传给lua_resume的结果。

-----------------------------------------

参考自：
1. Lua 5.1 参考手册 （云风译）
http://www.codingnow.com/2000/download/lua_manual.html
2. hshqcn
hshqcn  
