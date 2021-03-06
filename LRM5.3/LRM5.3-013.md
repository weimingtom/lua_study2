﻿luaL_loadfilex

[-0, +1, e]
int luaL_loadfilex (lua_State *L, const char *filename,
                                            const char *mode);
Loads a file as a Lua chunk. This function uses lua_load to load the chunk in the file named filename. If filename is NULL, then it loads from the standard input. The first line in the file is ignored if it starts with a #.

The string mode works as in function lua_load.

This function returns the same results as lua_load, but it has an extra error code LUA_ERRFILE if it cannot open/read the file or the file has a wrong mode.

As lua_load, this function only loads the chunk; it does not run it.

luaL_loadstring

[-0, +1, –]
int luaL_loadstring (lua_State *L, const char *s);
Loads a string as a Lua chunk. This function uses lua_load to load the chunk in the zero-terminated string s.

This function returns the same results as lua_load.

Also as lua_load, this function only loads the chunk; it does not run it.

luaL_newlib

[-0, +1, m]
void luaL_newlib (lua_State *L, const luaL_Reg l[]);
Creates a new table and registers there the functions in list l.

It is implemented as the following macro:

     (luaL_newlibtable(L,l), luaL_setfuncs(L,l,0))
The array l must be the actual array, not a pointer to it.

luaL_newlibtable

[-0, +1, m]
void luaL_newlibtable (lua_State *L, const luaL_Reg l[]);
Creates a new table with a size optimized to store all entries in the array l (but does not actually store them). It is intended to be used in conjunction with luaL_setfuncs (see luaL_newlib).

It is implemented as a macro. The array l must be the actual array, not a pointer to it.

luaL_newmetatable

[-0, +1, m]
int luaL_newmetatable (lua_State *L, const char *tname);
If the registry already has the key tname, returns 0. Otherwise, creates a new table to be used as a metatable for userdata, adds to this new table the pair __name = tname, adds to the registry the pair [tname] = new table, and returns 1. (The entry __name is used by some error-reporting functions.)

In both cases pushes onto the stack the final value associated with tname in the registry.

luaL_newstate

[-0, +0, –]
lua_State *luaL_newstate (void);
Creates a new Lua state. It calls lua_newstate with an allocator based on the standard C realloc function and then sets a panic function (see §4.6) that prints an error message to the standard error output in case of fatal errors.

Returns the new state, or NULL if there is a memory allocation error.

luaL_openlibs

[-0, +0, e]
void luaL_openlibs (lua_State *L);
Opens all standard Lua libraries into the given state.

luaL_optinteger

[-0, +0, v]
lua_Integer luaL_optinteger (lua_State *L,
                             int arg,
                             lua_Integer d);
If the function argument arg is an integer (or convertible to an integer), returns this integer. If this argument is absent or is nil, returns d. Otherwise, raises an error.

luaL_optlstring

[-0, +0, v]
const char *luaL_optlstring (lua_State *L,
                             int arg,
                             const char *d,
                             size_t *l);
If the function argument arg is a string, returns this string. If this argument is absent or is nil, returns d. Otherwise, raises an error.

If l is not NULL, fills the position *l with the result's length. If the result is NULL (only possible when returning d and d == NULL), its length is considered zero.

luaL_optnumber

[-0, +0, v]
lua_Number luaL_optnumber (lua_State *L, int arg, lua_Number d);
If the function argument arg is a number, returns this number. If this argument is absent or is nil, returns d. Otherwise, raises an error.

luaL_optstring

[-0, +0, v]
const char *luaL_optstring (lua_State *L,
                            int arg,
                            const char *d);
If the function argument arg is a string, returns this string. If this argument is absent or is nil, returns d. Otherwise, raises an error.

luaL_prepbuffer

[-?, +?, m]
char *luaL_prepbuffer (luaL_Buffer *B);
Equivalent to luaL_prepbuffsize with the predefined size LUAL_BUFFERSIZE.

luaL_prepbuffsize

[-?, +?, m]
char *luaL_prepbuffsize (luaL_Buffer *B, size_t sz);
Returns an address to a space of size sz where you can copy a string to be added to buffer B (see luaL_Buffer). After copying the string into this space you must call luaL_addsize with the size of the string to actually add it to the buffer.

luaL_pushresult

[-?, +1, m]
void luaL_pushresult (luaL_Buffer *B);
Finishes the use of buffer B leaving the final string on the top of the stack.

luaL_pushresultsize

[-?, +1, m]
void luaL_pushresultsize (luaL_Buffer *B, size_t sz);
Equivalent to the sequence luaL_addsize, luaL_pushresult.

luaL_ref

[-1, +0, m]
int luaL_ref (lua_State *L, int t);
Creates and returns a reference, in the table at index t, for the object at the top of the stack (and pops the object).

A reference is a unique integer key. As long as you do not manually add integer keys into table t, luaL_ref ensures the uniqueness of the key it returns. You can retrieve an object referred by reference r by calling lua_rawgeti(L, t, r). Function luaL_unref frees a reference and its associated object.

If the object at the top of the stack is nil, luaL_ref returns the constant LUA_REFNIL. The constant LUA_NOREF is guaranteed to be different from any reference returned by luaL_ref.

luaL_Reg

typedef struct luaL_Reg {
  const char *name;
  lua_CFunction func;
} luaL_Reg;
Type for arrays of functions to be registered by luaL_setfuncs. name is the function name and func is a pointer to the function. Any array of luaL_Reg must end with a sentinel entry in which both name and func are NULL.

luaL_requiref

[-0, +1, e]
void luaL_requiref (lua_State *L, const char *modname,
                    lua_CFunction openf, int glb);
If modname is not already present in package.loaded, calls function openf with string modname as an argument and sets the call result in package.loaded[modname], as if that function has been called through require.

If glb is true, also stores the module into global modname.

Leaves a copy of the module on the stack.

luaL_setfuncs

[-nup, +0, m]
void luaL_setfuncs (lua_State *L, const luaL_Reg *l, int nup);
Registers all functions in the array l (see luaL_Reg) into the table on the top of the stack (below optional upvalues, see next).

When nup is not zero, all functions are created sharing nup upvalues, which must be previously pushed on the stack on top of the library table. These values are popped from the stack after the registration.

luaL_setmetatable

[-0, +0, –]
void luaL_setmetatable (lua_State *L, const char *tname);
Sets the metatable of the object at the top of the stack as the metatable associated with name tname in the registry (see luaL_newmetatable).

luaL_Stream

typedef struct luaL_Stream {
  FILE *f;
  lua_CFunction closef;
} luaL_Stream;
The standard representation for file handles, which is used by the standard I/O library.

A file handle is implemented as a full userdata, with a metatable called LUA_FILEHANDLE (where LUA_FILEHANDLE is a macro with the actual metatable's name). The metatable is created by the I/O library (see luaL_newmetatable).

This userdata must start with the structure luaL_Stream; it can contain other data after this initial structure. Field f points to the corresponding C stream (or it can be NULL to indicate an incompletely created handle). Field closef points to a Lua function that will be called to close the stream when the handle is closed or collected; this function receives the file handle as its sole argument and must return either true (in case of success) or nil plus an error message (in case of error). Once Lua calls this field, it changes the field value to NULL to signal that the handle is closed.

luaL_testudata

[-0, +0, m]
void *luaL_testudata (lua_State *L, int arg, const char *tname);
This function works like luaL_checkudata, except that, when the test fails, it returns NULL instead of raising an error.

luaL_tolstring

[-0, +1, e]
const char *luaL_tolstring (lua_State *L, int idx, size_t *len);
Converts any Lua value at the given index to a C string in a reasonable format. The resulting string is pushed onto the stack and also returned by the function. If len is not NULL, the function also sets *len with the string length.

If the value has a metatable with a "__tostring" field, then luaL_tolstring calls the corresponding metamethod with the value as argument, and uses the result of the call as its result.

luaL_traceback

[-0, +1, m]
void luaL_traceback (lua_State *L, lua_State *L1, const char *msg,
                     int level);
Creates and pushes a traceback of the stack L1. If msg is not NULL it is appended at the beginning of the traceback. The level parameter tells at which level to start the traceback.

luaL_typename

[-0, +0, –]
const char *luaL_typename (lua_State *L, int index);
Returns the name of the type of the value at the given index.

luaL_unref

[-0, +0, –]
void luaL_unref (lua_State *L, int t, int ref);
Releases reference ref from the table at index t (see luaL_ref). The entry is removed from the table, so that the referred object can be collected. The reference ref is also freed to be used again.

If ref is LUA_NOREF or LUA_REFNIL, luaL_unref does nothing.

luaL_where

[-0, +1, m]
void luaL_where (lua_State *L, int lvl);
Pushes onto the stack a string identifying the current position of the control at level lvl in the call stack. Typically this string has the following format:

     chunkname:currentline:
Level 0 is the running function, level 1 is the function that called the running function, etc.

This function is used to build a prefix for error messages.

