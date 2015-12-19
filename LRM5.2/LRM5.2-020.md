6.10 – The Debug Library

This library provides the functionality of the debug interface (§4.9) to Lua programs. You should exert care when using this library. Several of its functions violate basic assumptions about Lua code (e.g., that variables local to a function cannot be accessed from outside; that userdata metatables cannot be changed by Lua code; that Lua programs do not crash) and therefore can compromise otherwise secure code. Moreover, some functions in this library may be slow.

All functions in this library are provided inside the debug table. All functions that operate over a thread have an optional first argument which is the thread to operate over. The default is always the current thread.

debug.debug ()

Enters an interactive mode with the user, running each string that the user enters. Using simple commands and other debug facilities, the user can inspect global and local variables, change their values, evaluate expressions, and so on. A line containing only the word cont finishes this function, so that the caller continues its execution.

Note that commands for debug.debug are not lexically nested within any function and so have no direct access to local variables.

debug.gethook ([thread])

Returns the current hook settings of the thread, as three values: the current hook function, the current hook mask, and the current hook count (as set by the debug.sethook function).

debug.getinfo ([thread,] f [, what])

Returns a table with information about a function. You can give the function directly or you can give a number as the value of f, which means the function running at level f of the call stack of the given thread: level 0 is the current function (getinfo itself); level 1 is the function that called getinfo (except for tail calls, which do not count on the stack); and so on. If f is a number larger than the number of active functions, then getinfo returns nil.

The returned table can contain all the fields returned by lua_getinfo, with the string what describing which fields to fill in. The default for what is to get all information available, except the table of valid lines. If present, the option 'f' adds a field named func with the function itself. If present, the option 'L' adds a field named activelines with the table of valid lines.

For instance, the expression debug.getinfo(1,"n").name returns a table with a name for the current function, if a reasonable name can be found, and the expression debug.getinfo(print) returns a table with all available information about the print function.

debug.getlocal ([thread,] f, local)

This function returns the name and the value of the local variable with index local of the function at level f of the stack. This function accesses not only explicit local variables, but also parameters, temporaries, etc.

The first parameter or local variable has index 1, and so on, until the last active variable. Negative indices refer to vararg parameters; -1 is the first vararg parameter. The function returns nil if there is no variable with the given index, and raises an error when called with a level out of range. (You can call debug.getinfo to check whether the level is valid.)

Variable names starting with '(' (open parenthesis) represent internal variables (loop control variables, temporaries, varargs, and C function locals).

The parameter f may also be a function. In that case, getlocal returns only the name of function parameters.

debug.getmetatable (value)

Returns the metatable of the given value or nil if it does not have a metatable.

debug.getregistry ()

Returns the registry table (see §4.5).

debug.getupvalue (f, up)

This function returns the name and the value of the upvalue with index up of the function f. The function returns nil if there is no upvalue with the given index.

debug.getuservalue (u)

Returns the Lua value associated to u. If u is not a userdata, returns nil.

debug.sethook ([thread,] hook, mask [, count])

Sets the given function as a hook. The string mask and the number count describe when the hook will be called. The string mask may have any combination of the following characters, with the given meaning:

'c': the hook is called every time Lua calls a function;
'r': the hook is called every time Lua returns from a function;
'l': the hook is called every time Lua enters a new line of code.
Moreover, with a count different from zero, the hook is called also after every count instructions.

When called without arguments, debug.sethook turns off the hook.

When the hook is called, its first parameter is a string describing the event that has triggered its call: "call" (or "tail call"), "return", "line", and "count". For line events, the hook also gets the new line number as its second parameter. Inside a hook, you can call getinfo with level 2 to get more information about the running function (level 0 is the getinfo function, and level 1 is the hook function).

debug.setlocal ([thread,] level, local, value)

This function assigns the value value to the local variable with index local of the function at level level of the stack. The function returns nil if there is no local variable with the given index, and raises an error when called with a level out of range. (You can call getinfo to check whether the level is valid.) Otherwise, it returns the name of the local variable.

See debug.getlocal for more information about variable indices and names.

debug.setmetatable (value, table)

Sets the metatable for the given value to the given table (which can be nil). Returns value.

debug.setupvalue (f, up, value)

This function assigns the value value to the upvalue with index up of the function f. The function returns nil if there is no upvalue with the given index. Otherwise, it returns the name of the upvalue.

debug.setuservalue (udata, value)

Sets the given value as the Lua value associated to the given udata. value must be a table or nil; udata must be a full userdata.

Returns udata.

debug.traceback ([thread,] [message [, level]])

If message is present but is neither a string nor nil, this function returns message without further processing. Otherwise, it returns a string with a traceback of the call stack. An optional message string is appended at the beginning of the traceback. An optional level number tells at which level to start the traceback (default is 1, the function calling traceback).

debug.upvalueid (f, n)

Returns an unique identifier (as a light userdata) for the upvalue numbered n from the given function.

These unique identifiers allow a program to check whether different closures share upvalues. Lua closures that share an upvalue (that is, that access a same external local variable) will return identical ids for those upvalue indices.

debug.upvaluejoin (f1, n1, f2, n2)

Make the n1-th upvalue of the Lua closure f1 refer to the n2-th upvalue of the Lua closure f2.

7 – Lua Standalone

Although Lua has been designed as an extension language, to be embedded in a host C program, it is also frequently used as a standalone language. An interpreter for Lua as a standalone language, called simply lua, is provided with the standard distribution. The standalone interpreter includes all standard libraries, including the debug library. Its usage is:

     lua [options] [script [args]]
The options are:

-e stat: executes string stat;
-l mod: "requires" mod;
-i: enters interactive mode after running script;
-v: prints version information;
-E: ignores environment variables;
--: stops handling options;
-: executes stdin as a file and stops handling options.
After handling its options, lua runs the given script, passing to it the given args as string arguments. When called without arguments, lua behaves as lua -v -i when the standard input (stdin) is a terminal, and as lua - otherwise.

When called without option -E, the interpreter checks for an environment variable LUA_INIT_5_2 (or LUA_INIT if it is not defined) before running any argument. If the variable content has the format @filename, then lua executes the file. Otherwise, lua executes the string itself.

When called with option -E, besides ignoring LUA_INIT, Lua also ignores the values of LUA_PATH and LUA_CPATH, setting the values of package.path and package.cpath with the default paths defined in luaconf.h.

All options are handled in order, except -i and -E. For instance, an invocation like

     $ lua -e'a=1' -e 'print(a)' script.lua
will first set a to 1, then print the value of a, and finally run the file script.lua with no arguments. (Here $ is the shell prompt. Your prompt may be different.)

Before starting to run the script, lua collects all arguments in the command line in a global table called arg. The script name is stored at index 0, the first argument after the script name goes to index 1, and so on. Any arguments before the script name (that is, the interpreter name plus the options) go to negative indices. For instance, in the call

     $ lua -la b.lua t1 t2
the interpreter first runs the file a.lua, then creates a table

     arg = { [-2] = "lua", [-1] = "-la",
             [0] = "b.lua",
             [1] = "t1", [2] = "t2" }
and finally runs the file b.lua. The script is called with arg[1], arg[2], ... as arguments; it can also access these arguments with the vararg expression '...'.

In interactive mode, if you write an incomplete statement, the interpreter waits for its completion by issuing a different prompt.

In case of unprotected errors in the script, the interpreter reports the error to the standard error stream. If the error object is a string, the interpreter adds a stack traceback to it. Otherwise, if the error object has a metamethod __tostring, the interpreter calls this metamethod to produce the final message. Finally, if the error object is nil, the interpreter does not report the error.

When finishing normally, the interpreter closes its main Lua state (see lua_close). The script can avoid this step by calling os.exit to terminate.

To allow the use of Lua as a script interpreter in Unix systems, the standalone interpreter skips the first line of a chunk if it starts with #. Therefore, Lua scripts can be made into executable programs by using chmod +x and the #! form, as in

     #!/usr/local/bin/lua
(Of course, the location of the Lua interpreter may be different in your machine. If lua is in your PATH, then

     #!/usr/bin/env lua
is a more portable solution.)

