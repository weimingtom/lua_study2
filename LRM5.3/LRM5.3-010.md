lua_Reader

typedef const char * (*lua_Reader) (lua_State *L,
                                    void *data,
                                    size_t *size);
The reader function used by lua_load. Every time it needs another piece of the chunk, lua_load calls the reader, passing along its data parameter. The reader must return a pointer to a block of memory with a new piece of the chunk and set size to the block size. The block must exist until the reader function is called again. To signal the end of the chunk, the reader must return NULL or set size to zero. The reader function may return pieces of any size greater than zero.

lua_register

[-0, +0, e]
void lua_register (lua_State *L, const char *name, lua_CFunction f);
Sets the C function f as the new value of global name. It is defined as a macro:

     #define lua_register(L,n,f) \
            (lua_pushcfunction(L, f), lua_setglobal(L, n))
lua_remove

[-1, +0, –]
void lua_remove (lua_State *L, int index);
Removes the element at the given valid index, shifting down the elements above this index to fill the gap. This function cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.

lua_replace

[-1, +0, –]
void lua_replace (lua_State *L, int index);
Moves the top element into the given valid index without shifting any element (therefore replacing the value at that given index), and then pops the top element.

lua_resume

[-?, +?, –]
int lua_resume (lua_State *L, lua_State *from, int nargs);
Starts and resumes a coroutine in the given thread L.

To start a coroutine, you push onto the thread stack the main function plus any arguments; then you call lua_resume, with nargs being the number of arguments. This call returns when the coroutine suspends or finishes its execution. When it returns, the stack contains all values passed to lua_yield, or all values returned by the body function. lua_resume returns LUA_YIELD if the coroutine yields, LUA_OK if the coroutine finishes its execution without errors, or an error code in case of errors (see lua_pcall).

In case of errors, the stack is not unwound, so you can use the debug API over it. The error message is on the top of the stack.

To resume a coroutine, you remove any results from the last lua_yield, put on its stack only the values to be passed as results from yield, and then call lua_resume.

The parameter from represents the coroutine that is resuming L. If there is no such coroutine, this parameter can be NULL.

lua_rotate

[-0, +0, –]
void lua_rotate (lua_State *L, int idx, int n);
Rotates the stack elements between the valid index idx and the top of the stack. The elements are rotated n positions in the direction of the top, for a positive n, or -n positions in the direction of the bottom, for a negative n. The absolute value of n must not be greater than the size of the slice being rotated. This function cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.

lua_setallocf

[-0, +0, –]
void lua_setallocf (lua_State *L, lua_Alloc f, void *ud);
Changes the allocator function of a given state to f with user data ud.

lua_setfield

[-1, +0, e]
void lua_setfield (lua_State *L, int index, const char *k);
Does the equivalent to t[k] = v, where t is the value at the given index and v is the value at the top of the stack.

This function pops the value from the stack. As in Lua, this function may trigger a metamethod for the "newindex" event (see §2.4).

lua_setglobal

[-1, +0, e]
void lua_setglobal (lua_State *L, const char *name);
Pops a value from the stack and sets it as the new value of global name.

lua_seti

[-1, +0, e]
void lua_seti (lua_State *L, int index, lua_Integer n);
Does the equivalent to t[n] = v, where t is the value at the given index and v is the value at the top of the stack.

This function pops the value from the stack. As in Lua, this function may trigger a metamethod for the "newindex" event (see §2.4).

lua_setmetatable

[-1, +0, –]
void lua_setmetatable (lua_State *L, int index);
Pops a table from the stack and sets it as the new metatable for the value at the given index.

lua_settable

[-2, +0, e]
void lua_settable (lua_State *L, int index);
Does the equivalent to t[k] = v, where t is the value at the given index, v is the value at the top of the stack, and k is the value just below the top.

This function pops both the key and the value from the stack. As in Lua, this function may trigger a metamethod for the "newindex" event (see §2.4).

lua_settop

[-?, +?, –]
void lua_settop (lua_State *L, int index);
Accepts any index, or 0, and sets the stack top to this index. If the new top is larger than the old one, then the new elements are filled with nil. If index is 0, then all stack elements are removed.

lua_setuservalue

[-1, +0, –]
void lua_setuservalue (lua_State *L, int index);
Pops a value from the stack and sets it as the new value associated to the userdata at the given index.

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

lua_stringtonumber

[-0, +1, –]
size_t lua_stringtonumber (lua_State *L, const char *s);
Converts the zero-terminated string s to a number, pushes that number into the stack, and returns the total size of the string, that is, its length plus one. The conversion can result in an integer or a float, according to the lexical conventions of Lua (see §3.1). The string may have leading and trailing spaces and a sign. If the string is not a valid numeral, returns 0 and pushes nothing. (Note that the result can be used as a boolean, true if the conversion succeeds.)

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
Converts the Lua value at the given index to the signed integral type lua_Integer. The Lua value must be an integer, or a number or string convertible to an integer (see §3.4.3); otherwise, lua_tointegerx returns 0.

If isnum is not NULL, its referent is assigned a boolean value that indicates whether the operation succeeded.

lua_tolstring

[-0, +0, m]
const char *lua_tolstring (lua_State *L, int index, size_t *len);
Converts the Lua value at the given index to a C string. If len is not NULL, it sets *len with the string length. The Lua value must be a string or a number; otherwise, the function returns NULL. If the value is a number, then lua_tolstring also changes the actual value in the stack to a string. (This change confuses lua_next when lua_tolstring is applied to keys during a table traversal.)

lua_tolstring returns a pointer to a string inside the Lua state. This string always has a zero ('\0') after its last character (as in C), but can contain other zeros in its body.

Because Lua has garbage collection, there is no guarantee that the pointer returned by lua_tolstring will be valid after the corresponding Lua value is removed from the stack.

lua_tonumber

[-0, +0, –]
lua_Number lua_tonumber (lua_State *L, int index);
Equivalent to lua_tonumberx with isnum equal to NULL.

lua_tonumberx

[-0, +0, –]
lua_Number lua_tonumberx (lua_State *L, int index, int *isnum);
Converts the Lua value at the given index to the C type lua_Number (see lua_Number). The Lua value must be a number or a string convertible to a number (see §3.4.3); otherwise, lua_tonumberx returns 0.

If isnum is not NULL, its referent is assigned a boolean value that indicates whether the operation succeeded.

lua_topointer

[-0, +0, –]
const void *lua_topointer (lua_State *L, int index);
Converts the value at the given index to a generic C pointer (void*). The value can be a userdata, a table, a thread, or a function; otherwise, lua_topointer returns NULL. Different objects will give different pointers. There is no way to convert the pointer back to its original value.

Typically this function is used only for hashing and debug information.

lua_tostring

[-0, +0, m]
const char *lua_tostring (lua_State *L, int index);
Equivalent to lua_tolstring with len equal to NULL.

lua_tothread

[-0, +0, –]
lua_State *lua_tothread (lua_State *L, int index);
Converts the value at the given index to a Lua thread (represented as lua_State*). This value must be a thread; otherwise, the function returns NULL.

lua_touserdata

[-0, +0, –]
void *lua_touserdata (lua_State *L, int index);
If the value at the given index is a full userdata, returns its block address. If the value is a light userdata, returns its pointer. Otherwise, returns NULL.

lua_type

[-0, +0, –]
int lua_type (lua_State *L, int index);
Returns the type of the value in the given valid index, or LUA_TNONE for a non-valid (but acceptable) index. The types returned by lua_type are coded by the following constants defined in lua.h: LUA_TNIL (0), LUA_TNUMBER, LUA_TBOOLEAN, LUA_TSTRING, LUA_TTABLE, LUA_TFUNCTION, LUA_TUSERDATA, LUA_TTHREAD, and LUA_TLIGHTUSERDATA.

lua_typename

[-0, +0, –]
const char *lua_typename (lua_State *L, int tp);
Returns the name of the type encoded by the value tp, which must be one the values returned by lua_type.

lua_Unsigned

typedef ... lua_Unsigned;
The unsigned version of lua_Integer.

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

[-?, +?, e]
int lua_yield (lua_State *L, int nresults);
This function is equivalent to lua_yieldk, but it has no continuation (see §4.7). Therefore, when the thread resumes, it continues the function that called the function calling lua_yield.

lua_yieldk

[-?, +?, e]
int lua_yieldk (lua_State *L,
                int nresults,
                lua_KContext ctx,
                lua_KFunction k);
Yields a coroutine (thread).

When a C function calls lua_yieldk, the running coroutine suspends its execution, and the call to lua_resume that started this coroutine returns. The parameter nresults is the number of values from the stack that will be passed as results to lua_resume.

When the coroutine is resumed again, Lua calls the given continuation function k to continue the execution of the C function that yielded (see §4.7). This continuation function receives the same stack from the previous function, with the n results removed and replaced by the arguments passed to lua_resume. Moreover, the continuation function receives the value ctx that was passed to lua_yieldk.

Usually, this function does not return; when the coroutine eventually resumes, it continues executing the continuation function. However, there is one special case, which is when this function is called from inside a line hook (see §4.9). In that case, lua_yieldk should be called with no continuation (probably in the form of lua_yield), and the hook should return immediately after the call. Lua will yield and, when the coroutine resumes again, it will continue the normal execution of the (Lua) function that triggered the hook.

This function can raise an error if it is called from a thread with a pending C call with no continuation function, or it is called from a thread that is not running inside a resume (e.g., the main thread).

