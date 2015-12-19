lua_setuservalue

[-1, +0, –]
void lua_setuservalue (lua_State *L, int index);
Pops a table or nil from the stack and sets it as the new value associated to the userdata at the given index.

lua_State

typedef struct lua_State lua_State;
An opaque structure that points to a thread and indirectly (through the thread) to the whole state of a Lua interpreter. The Lua library is fully reentrant: it has no global variables. All information about a state is accessible through this structure.

A pointer to this structure must be passed as the first argument to every function in the library, except to lua_newstate, which creates a Lua state from scratch.

lua_status

[-0, +0, –]
int lua_status (lua_State *L);
Returns the status of the thread L.

The status can be 0 (LUA_OK) for a normal thread, an error code if the thread finished the execution of a lua_resume with an error, or LUA_YIELD if the thread is suspended.

You can only call functions in threads with status LUA_OK. You can resume threads with status LUA_OK (to start a new coroutine) or LUA_YIELD (to resume a coroutine).

lua_toboolean

[-0, +0, –]
int lua_toboolean (lua_State *L, int index);
Converts the Lua value at the given index to a C boolean value (0 or 1). Like all tests in Lua, lua_toboolean returns true for any Lua value different from false and nil; otherwise it returns false. (If you want to accept only actual boolean values, use lua_isboolean to test the value's type.)

lua_tocfunction

[-0, +0, –]
lua_CFunction lua_tocfunction (lua_State *L, int index);
Converts a value at the given index to a C function. That value must be a C function; otherwise, returns NULL.

lua_tointeger

[-0, +0, –]
lua_Integer lua_tointeger (lua_State *L, int index);
Equivalent to lua_tointegerx with isnum equal to NULL.

lua_tointegerx

[-0, +0, –]
lua_Integer lua_tointegerx (lua_State *L, int index, int *isnum);
Converts the Lua value at the given index to the signed integral type lua_Integer. The Lua value must be a number or a string convertible to a number (see §3.4.2); otherwise, lua_tointegerx returns 0.

If the number is not an integer, it is truncated in some non-specified way.

If isnum is not NULL, its referent is assigned a boolean value that indicates whether the operation succeeded.

lua_tolstring

[-0, +0, e]
const char *lua_tolstring (lua_State *L, int index, size_t *len);
Converts the Lua value at the given index to a C string. If len is not NULL, it also sets *len with the string length. The Lua value must be a string or a number; otherwise, the function returns NULL. If the value is a number, then lua_tolstring also changes the actual value in the stack to a string. (This change confuses lua_next when lua_tolstring is applied to keys during a table traversal.)

lua_tolstring returns a fully aligned pointer to a string inside the Lua state. This string always has a zero ('\0') after its last character (as in C), but can contain other zeros in its body. Because Lua has garbage collection, there is no guarantee that the pointer returned by lua_tolstring will be valid after the corresponding value is removed from the stack.

lua_tonumber

[-0, +0, –]
lua_Number lua_tonumber (lua_State *L, int index);
Equivalent to lua_tonumberx with isnum equal to NULL.

lua_tonumberx

[-0, +0, –]
lua_Number lua_tonumberx (lua_State *L, int index, int *isnum);
Converts the Lua value at the given index to the C type lua_Number (see lua_Number). The Lua value must be a number or a string convertible to a number (see §3.4.2); otherwise, lua_tonumberx returns 0.

If isnum is not NULL, its referent is assigned a boolean value that indicates whether the operation succeeded.

lua_topointer

[-0, +0, –]
const void *lua_topointer (lua_State *L, int index);
Converts the value at the given index to a generic C pointer (void*). The value can be a userdata, a table, a thread, or a function; otherwise, lua_topointer returns NULL. Different objects will give different pointers. There is no way to convert the pointer back to its original value.

Typically this function is used only for debug information.

lua_tostring

[-0, +0, e]
const char *lua_tostring (lua_State *L, int index);
Equivalent to lua_tolstring with len equal to NULL.

lua_tothread

[-0, +0, –]
lua_State *lua_tothread (lua_State *L, int index);
Converts the value at the given index to a Lua thread (represented as lua_State*). This value must be a thread; otherwise, the function returns NULL.

lua_tounsigned

[-0, +0, –]
lua_Unsigned lua_tounsigned (lua_State *L, int index);
Equivalent to lua_tounsignedx with isnum equal to NULL.

lua_tounsignedx

[-0, +0, –]
lua_Unsigned lua_tounsignedx (lua_State *L, int index, int *isnum);
Converts the Lua value at the given index to the unsigned integral type lua_Unsigned. The Lua value must be a number or a string convertible to a number (see §3.4.2); otherwise, lua_tounsignedx returns 0.

If the number is not an integer, it is truncated in some non-specified way. If the number is outside the range of representable values, it is normalized to the remainder of its division by one more than the maximum representable value.

If isnum is not NULL, its referent is assigned a boolean value that indicates whether the operation succeeded.

lua_touserdata

[-0, +0, –]
void *lua_touserdata (lua_State *L, int index);
If the value at the given index is a full userdata, returns its block address. If the value is a light userdata, returns its pointer. Otherwise, returns NULL.

lua_type

[-0, +0, –]
int lua_type (lua_State *L, int index);
Returns the type of the value in the given valid index, or LUA_TNONE for a non-valid (but acceptable) index. The types returned by lua_type are coded by the following constants defined in lua.h: LUA_TNIL, LUA_TNUMBER, LUA_TBOOLEAN, LUA_TSTRING, LUA_TTABLE, LUA_TFUNCTION, LUA_TUSERDATA, LUA_TTHREAD, and LUA_TLIGHTUSERDATA.

lua_typename

[-0, +0, –]
const char *lua_typename (lua_State *L, int tp);
Returns the name of the type encoded by the value tp, which must be one the values returned by lua_type.

lua_Unsigned

typedef unsigned long lua_Unsigned;
The type used by the Lua API to represent unsigned integral values. It must have at least 32 bits.

By default it is an unsigned int or an unsigned long, whichever can hold 32-bit values.

lua_upvalueindex

[-0, +0, –]
int lua_upvalueindex (int i);
Returns the pseudo-index that represents the i-th upvalue of the running function (see §4.4).

lua_version

[-0, +0, v]
const lua_Number *lua_version (lua_State *L);
Returns the address of the version number stored in the Lua core. When called with a valid lua_State, returns the address of the version used to create that state. When called with NULL, returns the address of the version running the call.

lua_Writer

typedef int (*lua_Writer) (lua_State *L,
                           const void* p,
                           size_t sz,
                           void* ud);
The type of the writer function used by lua_dump. Every time it produces another piece of chunk, lua_dump calls the writer, passing along the buffer to be written (p), its size (sz), and the data parameter supplied to lua_dump.

The writer returns an error code: 0 means no errors; any other value means an error and stops lua_dump from calling the writer again.

lua_xmove

[-?, +?, –]
void lua_xmove (lua_State *from, lua_State *to, int n);
Exchange values between different threads of the same state.

This function pops n values from the stack from, and pushes them onto the stack to.

lua_yield

[-?, +?, –]
int lua_yield (lua_State *L, int nresults);
This function is equivalent to lua_yieldk, but it has no continuation (see §4.7). Therefore, when the thread resumes, it returns to the function that called the function calling lua_yield.

lua_yieldk

[-?, +?, –]
int lua_yieldk (lua_State *L, int nresults, int ctx, lua_CFunction k);
Yields a coroutine.

This function should only be called as the return expression of a C function, as follows:

     return lua_yieldk (L, n, i, k);
When a C function calls lua_yieldk in that way, the running coroutine suspends its execution, and the call to lua_resume that started this coroutine returns. The parameter nresults is the number of values from the stack that are passed as results to lua_resume.

When the coroutine is resumed again, Lua calls the given continuation function k to continue the execution of the C function that yielded (see §4.7). This continuation function receives the same stack from the previous function, with the results removed and replaced by the arguments passed to lua_resume. Moreover, the continuation function may access the value ctx by calling lua_getctx.
