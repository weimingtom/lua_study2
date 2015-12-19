lua_getctx

[-0, +0, –]
int lua_getctx (lua_State *L, int *ctx);
This function is called by a continuation function (see §4.7) to retrieve the status of the thread and a context information.

When called in the original function, lua_getctx always returns LUA_OK and does not change the value of its argument ctx. When called inside a continuation function, lua_getctx returns LUA_YIELD and sets the value of ctx to be the context information (the value passed as the ctx argument to the callee together with the continuation function).

When the callee is lua_pcallk, Lua may also call its continuation function to handle errors during the call. That is, upon an error in the function called by lua_pcallk, Lua may not return to the original function but instead may call the continuation function. In that case, a call to lua_getctx will return the error code (the value that would be returned by lua_pcallk); the value of ctx will be set to the context information, as in the case of a yield.

lua_getfield

[-0, +1, e]
void lua_getfield (lua_State *L, int index, const char *k);
Pushes onto the stack the value t[k], where t is the value at the given index. As in Lua, this function may trigger a metamethod for the "index" event (see §2.4).

lua_getglobal

[-0, +1, e]
void lua_getglobal (lua_State *L, const char *name);
Pushes onto the stack the value of the global name.

lua_getmetatable

[-0, +(0|1), –]
int lua_getmetatable (lua_State *L, int index);
Pushes onto the stack the metatable of the value at the given index. If the value does not have a metatable, the function returns 0 and pushes nothing on the stack.

lua_gettable

[-1, +1, e]
void lua_gettable (lua_State *L, int index);
Pushes onto the stack the value t[k], where t is the value at the given index and k is the value at the top of the stack.

This function pops the key from the stack (putting the resulting value in its place). As in Lua, this function may trigger a metamethod for the "index" event (see §2.4).

lua_gettop

[-0, +0, –]
int lua_gettop (lua_State *L);
Returns the index of the top element in the stack. Because indices start at 1, this result is equal to the number of elements in the stack (and so 0 means an empty stack).

lua_getuservalue

[-0, +1, –]
void lua_getuservalue (lua_State *L, int index);
Pushes onto the stack the Lua value associated with the userdata at the given index. This Lua value must be a table or nil.

lua_insert

[-1, +1, –]
void lua_insert (lua_State *L, int index);
Moves the top element into the given valid index, shifting up the elements above this index to open space. This function cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.

lua_Integer

typedef ptrdiff_t lua_Integer;
The type used by the Lua API to represent signed integral values.

By default it is a ptrdiff_t, which is usually the largest signed integral type the machine handles "comfortably".

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

lua_len

[-0, +1, e]
void lua_len (lua_State *L, int index);
Returns the "length" of the value at the given index; it is equivalent to the '#' operator in Lua (see §3.4.6). The result is pushed on the stack.

lua_load

[-0, +1, –]
int lua_load (lua_State *L,
              lua_Reader reader,
              void *data,
              const char *source,
              const char *mode);
Loads a Lua chunk (without running it). If there are no errors, lua_load pushes the compiled chunk as a Lua function on top of the stack. Otherwise, it pushes an error message.

The return values of lua_load are:

LUA_OK: no errors;
LUA_ERRSYNTAX: syntax error during precompilation;
LUA_ERRMEM: memory allocation error;
LUA_ERRGCMM: error while running a __gc metamethod. (This error has no relation with the chunk being loaded. It is generated by the garbage collector.)
The lua_load function uses a user-supplied reader function to read the chunk (see lua_Reader). The data argument is an opaque value passed to the reader function.

The source argument gives a name to the chunk, which is used for error messages and in debug information (see §4.9).

lua_load automatically detects whether the chunk is text or binary and loads it accordingly (see program luac). The string mode works as in function load, with the addition that a NULL value is equivalent to the string "bt".

lua_load uses the stack internally, so the reader function should always leave the stack unmodified when returning.

If the resulting function has one upvalue, this upvalue is set to the value of the global environment stored at index LUA_RIDX_GLOBALS in the registry (see §4.5). When loading main chunks, this upvalue will be the _ENV variable (see §2.2).

lua_newstate

[-0, +0, –]
lua_State *lua_newstate (lua_Alloc f, void *ud);
Creates a new thread running in a new, independent state. Returns NULL if cannot create the thread or the state (due to lack of memory). The argument f is the allocator function; Lua does all memory allocation for this state through this function. The second argument, ud, is an opaque pointer that Lua passes to the allocator in every call.

lua_newtable

[-0, +1, e]
void lua_newtable (lua_State *L);
Creates a new empty table and pushes it onto the stack. It is equivalent to lua_createtable(L, 0, 0).

lua_newthread

[-0, +1, e]
lua_State *lua_newthread (lua_State *L);
Creates a new thread, pushes it on the stack, and returns a pointer to a lua_State that represents this new thread. The new thread returned by this function shares with the original thread its global environment, but has an independent execution stack.

There is no explicit function to close or to destroy a thread. Threads are subject to garbage collection, like any Lua object.

lua_newuserdata

[-0, +1, e]
void *lua_newuserdata (lua_State *L, size_t size);
This function allocates a new block of memory with the given size, pushes onto the stack a new full userdata with the block address, and returns this address. The host program can freely use this memory.

lua_next

[-1, +(2|0), e]
int lua_next (lua_State *L, int index);
Pops a key from the stack, and pushes a key–value pair from the table at the given index (the "next" pair after the given key). If there are no more elements in the table, then lua_next returns 0 (and pushes nothing).

A typical traversal looks like this:

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
While traversing a table, do not call lua_tolstring directly on a key, unless you know that the key is actually a string. Recall that lua_tolstring may change the value at the given index; this confuses the next call to lua_next.

See function next for the caveats of modifying the table during its traversal.

lua_Number

typedef double lua_Number;
The type of numbers in Lua. By default, it is double, but that can be changed in luaconf.h. Through this configuration file you can change Lua to operate with another type for numbers (e.g., float or long).

lua_pcall

[-(nargs + 1), +(nresults|1), –]
int lua_pcall (lua_State *L, int nargs, int nresults, int msgh);
Calls a function in protected mode.

Both nargs and nresults have the same meaning as in lua_call. If there are no errors during the call, lua_pcall behaves exactly like lua_call. However, if there is any error, lua_pcall catches it, pushes a single value on the stack (the error message), and returns an error code. Like lua_call, lua_pcall always removes the function and its arguments from the stack.

If msgh is 0, then the error message returned on the stack is exactly the original error message. Otherwise, msgh is the stack index of a message handler. (In the current implementation, this index cannot be a pseudo-index.) In case of runtime errors, this function will be called with the error message and its return value will be the message returned on the stack by lua_pcall.

Typically, the message handler is used to add more debug information to the error message, such as a stack traceback. Such information cannot be gathered after the return of lua_pcall, since by then the stack has unwound.

The lua_pcall function returns one of the following codes (defined in lua.h):

LUA_OK (0): success.
LUA_ERRRUN: a runtime error.
LUA_ERRMEM: memory allocation error. For such errors, Lua does not call the message handler.
LUA_ERRERR: error while running the message handler.
LUA_ERRGCMM: error while running a __gc metamethod. (This error typically has no relation with the function being called. It is generated by the garbage collector.)
lua_pcallk

[-(nargs + 1), +(nresults|1), –]
int lua_pcallk (lua_State *L,
                int nargs,
                int nresults,
                int errfunc,
                int ctx,
                lua_CFunction k);
This function behaves exactly like lua_pcall, but allows the called function to yield (see §4.7).

