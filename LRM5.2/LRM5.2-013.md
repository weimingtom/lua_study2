5 – The Auxiliary Library

The auxiliary library provides several convenient functions to interface C with Lua. While the basic API provides the primitive functions for all interactions between C and Lua, the auxiliary library provides higher-level functions for some common tasks.

All functions and types from the auxiliary library are defined in header file lauxlib.h and have a prefix luaL_.

All functions in the auxiliary library are built on top of the basic API, and so they provide nothing that cannot be done with that API. Nevertheless, the use of the auxiliary library ensures more consistency to your code.

Several functions in the auxiliary library use internally some extra stack slots. When a function in the auxiliary library uses less than five slots, it does not check the stack size; it simply assumes that there are enough slots.

Several functions in the auxiliary library are used to check C function arguments. Because the error message is formatted for arguments (e.g., "bad argument #1"), you should not use these functions for other stack values.

Functions called luaL_check* always throw an error if the check is not satisfied.

5.1 – Functions and Types

Here we list all functions and types from the auxiliary library in alphabetical order.

luaL_addchar

[-?, +?, e]
void luaL_addchar (luaL_Buffer *B, char c);
Adds the byte c to the buffer B (see luaL_Buffer).

luaL_addlstring

[-?, +?, e]
void luaL_addlstring (luaL_Buffer *B, const char *s, size_t l);
Adds the string pointed to by s with length l to the buffer B (see luaL_Buffer). The string can contain embedded zeros.

luaL_addsize

[-?, +?, e]
void luaL_addsize (luaL_Buffer *B, size_t n);
Adds to the buffer B (see luaL_Buffer) a string of length n previously copied to the buffer area (see luaL_prepbuffer).

luaL_addstring

[-?, +?, e]
void luaL_addstring (luaL_Buffer *B, const char *s);
Adds the zero-terminated string pointed to by s to the buffer B (see luaL_Buffer). The string cannot contain embedded zeros.

luaL_addvalue

[-1, +?, e]
void luaL_addvalue (luaL_Buffer *B);
Adds the value at the top of the stack to the buffer B (see luaL_Buffer). Pops the value.

This is the only function on string buffers that can (and must) be called with an extra element on the stack, which is the value to be added to the buffer.

luaL_argcheck

[-0, +0, v]
void luaL_argcheck (lua_State *L,
                    int cond,
                    int arg,
                    const char *extramsg);
Checks whether cond is true. If not, raises an error with a standard message.

luaL_argerror

[-0, +0, v]
int luaL_argerror (lua_State *L, int arg, const char *extramsg);
Raises an error with a standard message that includes extramsg as a comment.

This function never returns, but it is an idiom to use it in C functions as return luaL_argerror(args).

luaL_Buffer

typedef struct luaL_Buffer luaL_Buffer;
Type for a string buffer.

A string buffer allows C code to build Lua strings piecemeal. Its pattern of use is as follows:

First declare a variable b of type luaL_Buffer.
Then initialize it with a call luaL_buffinit(L, &b).
Then add string pieces to the buffer calling any of the luaL_add* functions.
Finish by calling luaL_pushresult(&b). This call leaves the final string on the top of the stack.
If you know beforehand the total size of the resulting string, you can use the buffer like this:

First declare a variable b of type luaL_Buffer.
Then initialize it and preallocate a space of size sz with a call luaL_buffinitsize(L, &b, sz).
Then copy the string into that space.
Finish by calling luaL_pushresultsize(&b, sz), where sz is the total size of the resulting string copied into that space.
During its normal operation, a string buffer uses a variable number of stack slots. So, while using a buffer, you cannot assume that you know where the top of the stack is. You can use the stack between successive calls to buffer operations as long as that use is balanced; that is, when you call a buffer operation, the stack is at the same level it was immediately after the previous buffer operation. (The only exception to this rule is luaL_addvalue.) After calling luaL_pushresult the stack is back to its level when the buffer was initialized, plus the final string on its top.

luaL_buffinit

[-0, +0, –]
void luaL_buffinit (lua_State *L, luaL_Buffer *B);
Initializes a buffer B. This function does not allocate any space; the buffer must be declared as a variable (see luaL_Buffer).

luaL_buffinitsize

[-?, +?, e]
char *luaL_buffinitsize (lua_State *L, luaL_Buffer *B, size_t sz);
Equivalent to the sequence luaL_buffinit, luaL_prepbuffsize.

luaL_callmeta

[-0, +(0|1), e]
int luaL_callmeta (lua_State *L, int obj, const char *e);
Calls a metamethod.

If the object at index obj has a metatable and this metatable has a field e, this function calls this field passing the object as its only argument. In this case this function returns true and pushes onto the stack the value returned by the call. If there is no metatable or no metamethod, this function returns false (without pushing any value on the stack).

luaL_checkany

[-0, +0, v]
void luaL_checkany (lua_State *L, int arg);
Checks whether the function has an argument of any type (including nil) at position arg.

luaL_checkint

[-0, +0, v]
int luaL_checkint (lua_State *L, int arg);
Checks whether the function argument arg is a number and returns this number cast to an int.

luaL_checkinteger

[-0, +0, v]
lua_Integer luaL_checkinteger (lua_State *L, int arg);
Checks whether the function argument arg is a number and returns this number cast to a lua_Integer.

luaL_checklong

[-0, +0, v]
long luaL_checklong (lua_State *L, int arg);
Checks whether the function argument arg is a number and returns this number cast to a long.

luaL_checklstring

[-0, +0, v]
const char *luaL_checklstring (lua_State *L, int arg, size_t *l);
Checks whether the function argument arg is a string and returns this string; if l is not NULL fills *l with the string's length.

This function uses lua_tolstring to get its result, so all conversions and caveats of that function apply here.

luaL_checknumber

[-0, +0, v]
lua_Number luaL_checknumber (lua_State *L, int arg);
Checks whether the function argument arg is a number and returns this number.

luaL_checkoption

[-0, +0, v]
int luaL_checkoption (lua_State *L,
                      int arg,
                      const char *def,
                      const char *const lst[]);
Checks whether the function argument arg is a string and searches for this string in the array lst (which must be NULL-terminated). Returns the index in the array where the string was found. Raises an error if the argument is not a string or if the string cannot be found.

If def is not NULL, the function uses def as a default value when there is no argument arg or when this argument is nil.

This is a useful function for mapping strings to C enums. (The usual convention in Lua libraries is to use strings instead of numbers to select options.)

luaL_checkstack

[-0, +0, v]
void luaL_checkstack (lua_State *L, int sz, const char *msg);
Grows the stack size to top + sz elements, raising an error if the stack cannot grow to that size. msg is an additional text to go into the error message (or NULL for no additional text).

luaL_checkstring

[-0, +0, v]
const char *luaL_checkstring (lua_State *L, int arg);
Checks whether the function argument arg is a string and returns this string.

This function uses lua_tolstring to get its result, so all conversions and caveats of that function apply here.

luaL_checktype

[-0, +0, v]
void luaL_checktype (lua_State *L, int arg, int t);
Checks whether the function argument arg has type t. See lua_type for the encoding of types for t.

luaL_checkudata

[-0, +0, v]
void *luaL_checkudata (lua_State *L, int arg, const char *tname);
Checks whether the function argument arg is a userdata of the type tname (see luaL_newmetatable) and returns the userdata address (see lua_touserdata).

luaL_checkunsigned

[-0, +0, v]
lua_Unsigned luaL_checkunsigned (lua_State *L, int arg);
Checks whether the function argument arg is a number and returns this number cast to a lua_Unsigned.

luaL_checkversion

[-0, +0, –]
void luaL_checkversion (lua_State *L);
Checks whether the core running the call, the core that created the Lua state, and the code making the call are all using the same version of Lua. Also checks whether the core running the call and the core that created the Lua state are using the same address space.

luaL_dofile

[-0, +?, e]
int luaL_dofile (lua_State *L, const char *filename);
Loads and runs the given file. It is defined as the following macro:

     (luaL_loadfile(L, filename) || lua_pcall(L, 0, LUA_MULTRET, 0))
It returns false if there are no errors or true in case of errors.

luaL_dostring

[-0, +?, –]
int luaL_dostring (lua_State *L, const char *str);
Loads and runs the given string. It is defined as the following macro:

     (luaL_loadstring(L, str) || lua_pcall(L, 0, LUA_MULTRET, 0))
It returns false if there are no errors or true in case of errors.

luaL_error

[-0, +0, v]
int luaL_error (lua_State *L, const char *fmt, ...);
Raises an error. The error message format is given by fmt plus any extra arguments, following the same rules of lua_pushfstring. It also adds at the beginning of the message the file name and the line number where the error occurred, if this information is available.

This function never returns, but it is an idiom to use it in C functions as return luaL_error(args).

luaL_execresult

[-0, +3, e]
int luaL_execresult (lua_State *L, int stat);
This function produces the return values for process-related functions in the standard library (os.execute and io.close).

luaL_fileresult

[-0, +(1|3), e]
int luaL_fileresult (lua_State *L, int stat, const char *fname);
This function produces the return values for file-related functions in the standard library (io.open, os.rename, file:seek, etc.).

luaL_getmetafield

[-0, +(0|1), e]
int luaL_getmetafield (lua_State *L, int obj, const char *e);
Pushes onto the stack the field e from the metatable of the object at index obj. If the object does not have a metatable, or if the metatable does not have this field, returns false and pushes nothing.

luaL_getmetatable

[-0, +1, –]
void luaL_getmetatable (lua_State *L, const char *tname);
Pushes onto the stack the metatable associated with name tname in the registry (see luaL_newmetatable).

luaL_getsubtable

[-0, +1, e]
int luaL_getsubtable (lua_State *L, int idx, const char *fname);
Ensures that the value t[fname], where t is the value at index idx, is a table, and pushes that table onto the stack. Returns true if it finds a previous table there and false if it creates a new table.

luaL_gsub

[-0, +1, e]
const char *luaL_gsub (lua_State *L,
                       const char *s,
                       const char *p,
                       const char *r);
Creates a copy of string s by replacing any occurrence of the string p with the string r. Pushes the resulting string on the stack and returns it.
