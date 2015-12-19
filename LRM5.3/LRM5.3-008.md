lua_CFunction

typedef int (*lua_CFunction) (lua_State *L);
Type for C functions.

In order to communicate properly with Lua, a C function must use the following protocol, which defines the way parameters and results are passed: a C function receives its arguments from Lua in its stack in direct order (the first argument is pushed first). So, when the function starts, lua_gettop(L) returns the number of arguments received by the function. The first argument (if any) is at index 1 and its last argument is at index lua_gettop(L). To return values to Lua, a C function just pushes them onto the stack, in direct order (the first result is pushed first), and returns the number of results. Any other value in the stack below the results will be properly discarded by Lua. Like a Lua function, a C function called by Lua can also return many results.

As an example, the following function receives a variable number of numeric arguments and returns their average and their sum:

     static int foo (lua_State *L) {
       int n = lua_gettop(L);    /* number of arguments */
       lua_Number sum = 0.0;
       int i;
       for (i = 1; i <= n; i++) {
         if (!lua_isnumber(L, i)) {
           lua_pushliteral(L, "incorrect argument");
           lua_error(L);
         }
         sum += lua_tonumber(L, i);
       }
       lua_pushnumber(L, sum/n);        /* first result */
       lua_pushnumber(L, sum);         /* second result */
       return 2;                   /* number of results */
     }
lua_checkstack

[-0, +0, –]
int lua_checkstack (lua_State *L, int n);
Ensures that the stack has space for at least n extra slots (that is, that you can safely push up to n values into it). It returns false if it cannot fulfill the request, either because it would cause the stack to be larger than a fixed maximum size (typically at least several thousand elements) or because it cannot allocate memory for the extra space. This function never shrinks the stack; if the stack already has space for the extra slots, it is left unchanged.

lua_close

[-0, +0, –]
void lua_close (lua_State *L);
Destroys all objects in the given Lua state (calling the corresponding garbage-collection metamethods, if any) and frees all dynamic memory used by this state. On several platforms, you may not need to call this function, because all resources are naturally released when the host program ends. On the other hand, long-running programs that create multiple states, such as daemons or web servers, will probably need to close states as soon as they are not needed.

lua_compare

[-0, +0, e]
int lua_compare (lua_State *L, int index1, int index2, int op);
Compares two Lua values. Returns 1 if the value at index index1 satisfies op when compared with the value at index index2, following the semantics of the corresponding Lua operator (that is, it may call metamethods). Otherwise returns 0. Also returns 0 if any of the indices is not valid.

The value of op must be one of the following constants:

LUA_OPEQ: compares for equality (==)
LUA_OPLT: compares for less than (<)
LUA_OPLE: compares for less or equal (<=)
lua_concat

[-n, +1, e]
void lua_concat (lua_State *L, int n);
Concatenates the n values at the top of the stack, pops them, and leaves the result at the top. If n is 1, the result is the single value on the stack (that is, the function does nothing); if n is 0, the result is the empty string. Concatenation is performed following the usual semantics of Lua (see §3.4.6).

lua_copy

[-0, +0, –]
void lua_copy (lua_State *L, int fromidx, int toidx);
Copies the element at index fromidx into the valid index toidx, replacing the value at that position. Values at other positions are not affected.

lua_createtable

[-0, +1, m]
void lua_createtable (lua_State *L, int narr, int nrec);
Creates a new empty table and pushes it onto the stack. Parameter narr is a hint for how many elements the table will have as a sequence; parameter nrec is a hint for how many other elements the table will have. Lua may use these hints to preallocate memory for the new table. This preallocation is useful for performance when you know in advance how many elements the table will have. Otherwise you can use the function lua_newtable.

lua_dump

[-0, +0, –]
int lua_dump (lua_State *L,
                        lua_Writer writer,
                        void *data,
                        int strip);
Dumps a function as a binary chunk. Receives a Lua function on the top of the stack and produces a binary chunk that, if loaded again, results in a function equivalent to the one dumped. As it produces parts of the chunk, lua_dump calls function writer (see lua_Writer) with the given data to write them.

If strip is true, the binary representation may not include all debug information about the function, to save space.

The value returned is the error code returned by the last call to the writer; 0 means no errors.

This function does not pop the Lua function from the stack.

lua_error

[-1, +0, v]
int lua_error (lua_State *L);
Generates a Lua error, using the value at the top of the stack as the error object. This function does a long jump, and therefore never returns (see luaL_error).

lua_gc

[-0, +0, e]
int lua_gc (lua_State *L, int what, int data);
Controls the garbage collector.

This function performs several tasks, according to the value of the parameter what:

LUA_GCSTOP: stops the garbage collector.
LUA_GCRESTART: restarts the garbage collector.
LUA_GCCOLLECT: performs a full garbage-collection cycle.
LUA_GCCOUNT: returns the current amount of memory (in Kbytes) in use by Lua.
LUA_GCCOUNTB: returns the remainder of dividing the current amount of bytes of memory in use by Lua by 1024.
LUA_GCSTEP: performs an incremental step of garbage collection.
LUA_GCSETPAUSE: sets data as the new value for the pause of the collector (see §2.5) and returns the previous value of the pause.
LUA_GCSETSTEPMUL: sets data as the new value for the step multiplier of the collector (see §2.5) and returns the previous value of the step multiplier.
LUA_GCISRUNNING: returns a boolean that tells whether the collector is running (i.e., not stopped).
For more details about these options, see collectgarbage.

lua_getallocf

[-0, +0, –]
lua_Alloc lua_getallocf (lua_State *L, void **ud);
Returns the memory-allocation function of a given state. If ud is not NULL, Lua stores in *ud the opaque pointer given when the memory-allocator function was set.

lua_getfield

[-0, +1, e]
int lua_getfield (lua_State *L, int index, const char *k);
Pushes onto the stack the value t[k], where t is the value at the given index. As in Lua, this function may trigger a metamethod for the "index" event (see §2.4).

Returns the type of the pushed value.

lua_getextraspace

[-0, +0, –]
void *lua_getextraspace (lua_State *L);
Returns a pointer to a raw memory area associated with the given Lua state. The application can use this area for any purpose; Lua does not use it for anything.

Each new thread has this area initialized with a copy of the area of the main thread.

By default, this area has the size of a pointer to void, but you can recompile Lua with a different size for this area. (See LUA_EXTRASPACE in luaconf.h.)

lua_getglobal

[-0, +1, e]
int lua_getglobal (lua_State *L, const char *name);
Pushes onto the stack the value of the global name. Returns the type of that value.

lua_geti

[-0, +1, e]
int lua_geti (lua_State *L, int index, lua_Integer i);
Pushes onto the stack the value t[i], where t is the value at the given index. As in Lua, this function may trigger a metamethod for the "index" event (see §2.4).

Returns the type of the pushed value.

lua_getmetatable

[-0, +(0|1), –]
int lua_getmetatable (lua_State *L, int index);
If the value at the given index has a metatable, the function pushes that metatable onto the stack and returns 1. Otherwise, the function returns 0 and pushes nothing on the stack.

lua_gettable

[-1, +1, e]
int lua_gettable (lua_State *L, int index);
Pushes onto the stack the value t[k], where t is the value at the given index and k is the value at the top of the stack.

This function pops the key from the stack, pushing the resulting value in its place. As in Lua, this function may trigger a metamethod for the "index" event (see §2.4).

Returns the type of the pushed value.

lua_gettop

[-0, +0, –]
int lua_gettop (lua_State *L);
Returns the index of the top element in the stack. Because indices start at 1, this result is equal to the number of elements in the stack; in particular, 0 means an empty stack.

lua_getuservalue

[-0, +1, –]
int lua_getuservalue (lua_State *L, int index);
Pushes onto the stack the Lua value associated with the userdata at the given index.

Returns the type of the pushed value.

lua_insert

[-1, +1, –]
void lua_insert (lua_State *L, int index);
Moves the top element into the given valid index, shifting up the elements above this index to open space. This function cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.

lua_Integer

typedef ... lua_Integer;
The type of integers in Lua.

By default this type is long long, (usually a 64-bit two-complement integer), but that can be changed to long or int (usually a 32-bit two-complement integer). (See LUA_INT_TYPE in luaconf.h.)

Lua also defines the constants LUA_MININTEGER and LUA_MAXINTEGER, with the minimum and the maximum values that fit in this type.

lua_isboolean

[-0, +0, –]
int lua_isboolean (lua_State *L, int index);
Returns 1 if the value at the given index is a boolean, and 0 otherwise.

lua_iscfunction

[-0, +0, –]
int lua_iscfunction (lua_State *L, int index);
Returns 1 if the value at the given index is a C function, and 0 otherwise.

lua_isfunction

[-0, +0, –]
int lua_isfunction (lua_State *L, int index);
Returns 1 if the value at the given index is a function (either C or Lua), and 0 otherwise.

lua_isinteger

[-0, +0, –]
int lua_isinteger (lua_State *L, int index);
Returns 1 if the value at the given index is an integer (that is, the value is a number and is represented as an integer), and 0 otherwise.

lua_islightuserdata

[-0, +0, –]
int lua_islightuserdata (lua_State *L, int index);
Returns 1 if the value at the given index is a light userdata, and 0 otherwise.

lua_isnil

[-0, +0, –]
int lua_isnil (lua_State *L, int index);
Returns 1 if the value at the given index is nil, and 0 otherwise.

lua_isnone

[-0, +0, –]
int lua_isnone (lua_State *L, int index);
Returns 1 if the given index is not valid, and 0 otherwise.

lua_isnoneornil

[-0, +0, –]
int lua_isnoneornil (lua_State *L, int index);
Returns 1 if the given index is not valid or if the value at this index is nil, and 0 otherwise.

lua_isnumber

[-0, +0, –]
int lua_isnumber (lua_State *L, int index);
Returns 1 if the value at the given index is a number or a string convertible to a number, and 0 otherwise.

lua_isstring

[-0, +0, –]
int lua_isstring (lua_State *L, int index);
Returns 1 if the value at the given index is a string or a number (which is always convertible to a string), and 0 otherwise.

lua_istable

[-0, +0, –]
int lua_istable (lua_State *L, int index);
Returns 1 if the value at the given index is a table, and 0 otherwise.

lua_isthread

[-0, +0, –]
int lua_isthread (lua_State *L, int index);
Returns 1 if the value at the given index is a thread, and 0 otherwise.

lua_isuserdata

[-0, +0, –]
int lua_isuserdata (lua_State *L, int index);
Returns 1 if the value at the given index is a userdata (either full or light), and 0 otherwise.

lua_isyieldable

[-0, +0, –]
int lua_isyieldable (lua_State *L);
Returns 1 if the given coroutine can yield, and 0 otherwise.

lua_KContext

typedef ... lua_KContext;
The type for continuation-function contexts. It must be a numeric type. This type is defined as intptr_t when intptr_t is available, so that it can store pointers too. Otherwise, it is defined as ptrdiff_t.

lua_KFunction

typedef int (*lua_KFunction) (lua_State *L, int status, lua_KContext ctx);
Type for continuation functions (see §4.7).

lua_len

[-0, +1, e]
void lua_len (lua_State *L, int index);
Returns the length of the value at the given index. It is equivalent to the '#' operator in Lua (see §3.4.7) and may trigger a metamethod for the "length" event (see §2.4). The result is pushed on the stack.

