4.9 – The Debug Interface

Lua has no built-in debugging facilities. Instead, it offers a special interface by means of functions and hooks. This interface allows the construction of different kinds of debuggers, profilers, and other tools that need "inside information" from the interpreter.

lua_Debug

typedef struct lua_Debug {
  int event;
  const char *name;           /* (n) */
  const char *namewhat;       /* (n) */
  const char *what;           /* (S) */
  const char *source;         /* (S) */
  int currentline;            /* (l) */
  int linedefined;            /* (S) */
  int lastlinedefined;        /* (S) */
  unsigned char nups;         /* (u) number of upvalues */
  unsigned char nparams;      /* (u) number of parameters */
  char isvararg;              /* (u) */
  char istailcall;            /* (t) */
  char short_src[LUA_IDSIZE]; /* (S) */
  /* private part */
  other fields
} lua_Debug;
A structure used to carry different pieces of information about a function or an activation record. lua_getstack fills only the private part of this structure, for later use. To fill the other fields of lua_Debug with useful information, call lua_getinfo.

The fields of lua_Debug have the following meaning:

source: the source of the chunk that created the function. If source starts with a '@', it means that the function was defined in a file where the file name follows the '@'. If source starts with a '=', the remainder of its contents describe the source in a user-dependent manner. Otherwise, the function was defined in a string where source is that string.
short_src: a "printable" version of source, to be used in error messages.
linedefined: the line number where the definition of the function starts.
lastlinedefined: the line number where the definition of the function ends.
what: the string "Lua" if the function is a Lua function, "C" if it is a C function, "main" if it is the main part of a chunk.
currentline: the current line where the given function is executing. When no line information is available, currentline is set to -1.
name: a reasonable name for the given function. Because functions in Lua are first-class values, they do not have a fixed name: some functions can be the value of multiple global variables, while others can be stored only in a table field. The lua_getinfo function checks how the function was called to find a suitable name. If it cannot find a name, then name is set to NULL.
namewhat: explains the name field. The value of namewhat can be "global", "local", "method", "field", "upvalue", or "" (the empty string), according to how the function was called. (Lua uses the empty string when no other option seems to apply.)
istailcall: true if this function invocation was called by a tail call. In this case, the caller of this level is not in the stack.
nups: the number of upvalues of the function.
nparams: the number of fixed parameters of the function (always 0 for C functions).
isvararg: true if the function is a vararg function (always true for C functions).
lua_gethook

[-0, +0, –]
lua_Hook lua_gethook (lua_State *L);
Returns the current hook function.

lua_gethookcount

[-0, +0, –]
int lua_gethookcount (lua_State *L);
Returns the current hook count.

lua_gethookmask

[-0, +0, –]
int lua_gethookmask (lua_State *L);
Returns the current hook mask.

lua_getinfo

[-(0|1), +(0|1|2), e]
int lua_getinfo (lua_State *L, const char *what, lua_Debug *ar);
Gets information about a specific function or function invocation.

To get information about a function invocation, the parameter ar must be a valid activation record that was filled by a previous call to lua_getstack or given as argument to a hook (see lua_Hook).

To get information about a function you push it onto the stack and start the what string with the character '>'. (In that case, lua_getinfo pops the function from the top of the stack.) For instance, to know in which line a function f was defined, you can write the following code:

     lua_Debug ar;
     lua_getglobal(L, "f");  /* get global 'f' */
     lua_getinfo(L, ">S", &ar);
     printf("%d\n", ar.linedefined);
Each character in the string what selects some fields of the structure ar to be filled or a value to be pushed on the stack:

'n': fills in the field name and namewhat;
'S': fills in the fields source, short_src, linedefined, lastlinedefined, and what;
'l': fills in the field currentline;
't': fills in the field istailcall;
'u': fills in the fields nups, nparams, and isvararg;
'f': pushes onto the stack the function that is running at the given level;
'L': pushes onto the stack a table whose indices are the numbers of the lines that are valid on the function. (A valid line is a line with some associated code, that is, a line where you can put a break point. Non-valid lines include empty lines and comments.)
This function returns 0 on error (for instance, an invalid option in what).

lua_getlocal

[-0, +(0|1), –]
const char *lua_getlocal (lua_State *L, lua_Debug *ar, int n);
Gets information about a local variable of a given activation record or a given function.

In the first case, the parameter ar must be a valid activation record that was filled by a previous call to lua_getstack or given as argument to a hook (see lua_Hook). The index n selects which local variable to inspect; see debug.getlocal for details about variable indices and names.

lua_getlocal pushes the variable's value onto the stack and returns its name.

In the second case, ar should be NULL and the function to be inspected must be at the top of the stack. In this case, only parameters of Lua functions are visible (as there is no information about what variables are active) and no values are pushed onto the stack.

Returns NULL (and pushes nothing) when the index is greater than the number of active local variables.

lua_getstack

[-0, +0, –]
int lua_getstack (lua_State *L, int level, lua_Debug *ar);
Gets information about the interpreter runtime stack.

This function fills parts of a lua_Debug structure with an identification of the activation record of the function executing at a given level. Level 0 is the current running function, whereas level n+1 is the function that has called level n (except for tail calls, which do not count on the stack). When there are no errors, lua_getstack returns 1; when called with a level greater than the stack depth, it returns 0.

lua_getupvalue

[-0, +(0|1), –]
const char *lua_getupvalue (lua_State *L, int funcindex, int n);
Gets information about a closure's upvalue. (For Lua functions, upvalues are the external local variables that the function uses, and that are consequently included in its closure.) lua_getupvalue gets the index n of an upvalue, pushes the upvalue's value onto the stack, and returns its name. funcindex points to the closure in the stack. (Upvalues have no particular order, as they are active through the whole function. So, they are numbered in an arbitrary order.)

Returns NULL (and pushes nothing) when the index is greater than the number of upvalues. For C functions, this function uses the empty string "" as a name for all upvalues.

lua_Hook

typedef void (*lua_Hook) (lua_State *L, lua_Debug *ar);
Type for debugging hook functions.

Whenever a hook is called, its ar argument has its field event set to the specific event that triggered the hook. Lua identifies these events with the following constants: LUA_HOOKCALL, LUA_HOOKRET, LUA_HOOKTAILCALL, LUA_HOOKLINE, and LUA_HOOKCOUNT. Moreover, for line events, the field currentline is also set. To get the value of any other field in ar, the hook must call lua_getinfo.

For call events, event can be LUA_HOOKCALL, the normal value, or LUA_HOOKTAILCALL, for a tail call; in this case, there will be no corresponding return event.

While Lua is running a hook, it disables other calls to hooks. Therefore, if a hook calls back Lua to execute a function or a chunk, this execution occurs without any calls to hooks.

Hook functions cannot have continuations, that is, they cannot call lua_yieldk, lua_pcallk, or lua_callk with a non-null k.

Hook functions can yield under the following conditions: Only count and line events can yield and they cannot yield any value; to yield a hook function must finish its execution calling lua_yield with nresults equal to zero.

lua_sethook

[-0, +0, –]
int lua_sethook (lua_State *L, lua_Hook f, int mask, int count);
Sets the debugging hook function.

Argument f is the hook function. mask specifies on which events the hook will be called: it is formed by a bitwise or of the constants LUA_MASKCALL, LUA_MASKRET, LUA_MASKLINE, and LUA_MASKCOUNT. The count argument is only meaningful when the mask includes LUA_MASKCOUNT. For each event, the hook is called as explained below:

The call hook: is called when the interpreter calls a function. The hook is called just after Lua enters the new function, before the function gets its arguments.
The return hook: is called when the interpreter returns from a function. The hook is called just before Lua leaves the function. There is no standard way to access the values to be returned by the function.
The line hook: is called when the interpreter is about to start the execution of a new line of code, or when it jumps back in the code (even to the same line). (This event only happens while Lua is executing a Lua function.)
The count hook: is called after the interpreter executes every count instructions. (This event only happens while Lua is executing a Lua function.)
A hook is disabled by setting mask to zero.

lua_setlocal

[-(0|1), +0, –]
const char *lua_setlocal (lua_State *L, lua_Debug *ar, int n);
Sets the value of a local variable of a given activation record. Parameters ar and n are as in lua_getlocal (see lua_getlocal). lua_setlocal assigns the value at the top of the stack to the variable and returns its name. It also pops the value from the stack.

Returns NULL (and pops nothing) when the index is greater than the number of active local variables.

lua_setupvalue

[-(0|1), +0, –]
const char *lua_setupvalue (lua_State *L, int funcindex, int n);
Sets the value of a closure's upvalue. It assigns the value at the top of the stack to the upvalue and returns its name. It also pops the value from the stack. Parameters funcindex and n are as in the lua_getupvalue (see lua_getupvalue).

Returns NULL (and pops nothing) when the index is greater than the number of upvalues.

lua_upvalueid

[-0, +0, –]
void *lua_upvalueid (lua_State *L, int funcindex, int n);
Returns an unique identifier for the upvalue numbered n from the closure at index funcindex. Parameters funcindex and n are as in the lua_getupvalue (see lua_getupvalue) (but n cannot be greater than the number of upvalues).

These unique identifiers allow a program to check whether different closures share upvalues. Lua closures that share an upvalue (that is, that access a same external local variable) will return identical ids for those upvalue indices.

lua_upvaluejoin

[-0, +0, –]
void lua_upvaluejoin (lua_State *L, int funcindex1, int n1,
                                    int funcindex2, int n2);
Make the n1-th upvalue of the Lua closure at index funcindex1 refer to the n2-th upvalue of the Lua closure at index funcindex2.

