lua_pop

[-n, +0, –]
void lua_pop (lua_State *L, int n);
Pops n elements from the stack.

lua_pushboolean

[-0, +1, –]
void lua_pushboolean (lua_State *L, int b);
Pushes a boolean value with value b onto the stack.

lua_pushcclosure

[-n, +1, e]
void lua_pushcclosure (lua_State *L, lua_CFunction fn, int n);
Pushes a new C closure onto the stack.

When a C function is created, it is possible to associate some values with it, thus creating a C closure (see §4.4); these values are then accessible to the function whenever it is called. To associate values with a C function, first these values should be pushed onto the stack (when there are multiple values, the first value is pushed first). Then lua_pushcclosure is called to create and push the C function onto the stack, with the argument n telling how many values should be associated with the function. lua_pushcclosure also pops these values from the stack.

The maximum value for n is 255.

When n is zero, this function creates a light C function, which is just a pointer to the C function. In that case, it never throws a memory error.

lua_pushcfunction

[-0, +1, –]
void lua_pushcfunction (lua_State *L, lua_CFunction f);
Pushes a C function onto the stack. This function receives a pointer to a C function and pushes onto the stack a Lua value of type function that, when called, invokes the corresponding C function.

Any function to be registered in Lua must follow the correct protocol to receive its parameters and return its results (see lua_CFunction).

lua_pushcfunction is defined as a macro:

     #define lua_pushcfunction(L,f)  lua_pushcclosure(L,f,0)
Note that f is used twice.

lua_pushfstring

[-0, +1, e]
const char *lua_pushfstring (lua_State *L, const char *fmt, ...);
Pushes onto the stack a formatted string and returns a pointer to this string. It is similar to the ISO C function sprintf, but has some important differences:

You do not have to allocate space for the result: the result is a Lua string and Lua takes care of memory allocation (and deallocation, through garbage collection).
The conversion specifiers are quite restricted. There are no flags, widths, or precisions. The conversion specifiers can only be '%%' (inserts a '%' in the string), '%s' (inserts a zero-terminated string, with no size restrictions), '%f' (inserts a lua_Number), '%p' (inserts a pointer as a hexadecimal numeral), '%d' (inserts an int), and '%c' (inserts an int as a byte).
lua_pushglobaltable

[-0, +1, –]
void lua_pushglobaltable (lua_State *L);
Pushes the global environment onto the stack.

lua_pushinteger

[-0, +1, –]
void lua_pushinteger (lua_State *L, lua_Integer n);
Pushes a number with value n onto the stack.

lua_pushlightuserdata

[-0, +1, –]
void lua_pushlightuserdata (lua_State *L, void *p);
Pushes a light userdata onto the stack.

Userdata represent C values in Lua. A light userdata represents a pointer, a void*. It is a value (like a number): you do not create it, it has no individual metatable, and it is not collected (as it was never created). A light userdata is equal to "any" light userdata with the same C address.

lua_pushliteral

[-0, +1, e]
const char *lua_pushliteral (lua_State *L, const char *s);
This macro is equivalent to lua_pushlstring, but can be used only when s is a literal string. It automatically provides the string length.

lua_pushlstring

[-0, +1, e]
const char *lua_pushlstring (lua_State *L, const char *s, size_t len);
Pushes the string pointed to by s with size len onto the stack. Lua makes (or reuses) an internal copy of the given string, so the memory at s can be freed or reused immediately after the function returns. The string can contain any binary data, including embedded zeros.

Returns a pointer to the internal copy of the string.

lua_pushnil

[-0, +1, –]
void lua_pushnil (lua_State *L);
Pushes a nil value onto the stack.

lua_pushnumber

[-0, +1, –]
void lua_pushnumber (lua_State *L, lua_Number n);
Pushes a number with value n onto the stack.

lua_pushstring

[-0, +1, e]
const char *lua_pushstring (lua_State *L, const char *s);
Pushes the zero-terminated string pointed to by s onto the stack. Lua makes (or reuses) an internal copy of the given string, so the memory at s can be freed or reused immediately after the function returns.

Returns a pointer to the internal copy of the string.

If s is NULL, pushes nil and returns NULL.

lua_pushthread

[-0, +1, –]
int lua_pushthread (lua_State *L);
Pushes the thread represented by L onto the stack. Returns 1 if this thread is the main thread of its state.

lua_pushunsigned

[-0, +1, –]
void lua_pushunsigned (lua_State *L, lua_Unsigned n);
Pushes a number with value n onto the stack.

lua_pushvalue

[-0, +1, –]
void lua_pushvalue (lua_State *L, int index);
Pushes a copy of the element at the given index onto the stack.

lua_pushvfstring

[-0, +1, e]
const char *lua_pushvfstring (lua_State *L,
                              const char *fmt,
                              va_list argp);
Equivalent to lua_pushfstring, except that it receives a va_list instead of a variable number of arguments.

lua_rawequal

[-0, +0, –]
int lua_rawequal (lua_State *L, int index1, int index2);
Returns 1 if the two values in indices index1 and index2 are primitively equal (that is, without calling metamethods). Otherwise returns 0. Also returns 0 if any of the indices are non valid.

lua_rawget

[-1, +1, –]
void lua_rawget (lua_State *L, int index);
Similar to lua_gettable, but does a raw access (i.e., without metamethods).

lua_rawgeti

[-0, +1, –]
void lua_rawgeti (lua_State *L, int index, int n);
Pushes onto the stack the value t[n], where t is the table at the given index. The access is raw; that is, it does not invoke metamethods.

lua_rawgetp

[-0, +1, –]
void lua_rawgetp (lua_State *L, int index, const void *p);
Pushes onto the stack the value t[k], where t is the table at the given index and k is the pointer p represented as a light userdata. The access is raw; that is, it does not invoke metamethods.

lua_rawlen

[-0, +0, –]
size_t lua_rawlen (lua_State *L, int index);
Returns the raw "length" of the value at the given index: for strings, this is the string length; for tables, this is the result of the length operator ('#') with no metamethods; for userdata, this is the size of the block of memory allocated for the userdata; for other values, it is 0.

lua_rawset

[-2, +0, e]
void lua_rawset (lua_State *L, int index);
Similar to lua_settable, but does a raw assignment (i.e., without metamethods).

lua_rawseti

[-1, +0, e]
void lua_rawseti (lua_State *L, int index, int n);
Does the equivalent of t[n] = v, where t is the table at the given index and v is the value at the top of the stack.

This function pops the value from the stack. The assignment is raw; that is, it does not invoke metamethods.

lua_rawsetp

[-1, +0, e]
void lua_rawsetp (lua_State *L, int index, const void *p);
Does the equivalent of t[k] = v, where t is the table at the given index, k is the pointer p represented as a light userdata, and v is the value at the top of the stack.

This function pops the value from the stack. The assignment is raw; that is, it does not invoke metamethods.

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
Moves the top element into the given valid index without shifting any element (therefore replacing the value at the given index), and then pops the top element.

lua_resume

[-?, +?, –]
int lua_resume (lua_State *L, lua_State *from, int nargs);
Starts and resumes a coroutine in a given thread.

To start a coroutine, you push onto the thread stack the main function plus any arguments; then you call lua_resume, with nargs being the number of arguments. This call returns when the coroutine suspends or finishes its execution. When it returns, the stack contains all values passed to lua_yield, or all values returned by the body function. lua_resume returns LUA_YIELD if the coroutine yields, LUA_OK if the coroutine finishes its execution without errors, or an error code in case of errors (see lua_pcall).

In case of errors, the stack is not unwound, so you can use the debug API over it. The error message is on the top of the stack.

To resume a coroutine, you remove any results from the last lua_yield, put on its stack only the values to be passed as results from yield, and then call lua_resume.

The parameter from represents the coroutine that is resuming L. If there is no such coroutine, this parameter can be NULL.

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

