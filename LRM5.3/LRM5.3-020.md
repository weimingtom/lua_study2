debug.setmetatable (value, table)

Sets the metatable for the given value to the given table (which can be nil). Returns value.

debug.setupvalue (f, up, value)

This function assigns the value value to the upvalue with index up of the function f. The function returns nil if there is no upvalue with the given index. Otherwise, it returns the name of the upvalue.

debug.setuservalue (udata, value)

Sets the given value as the Lua value associated to the given udata. udata must be a full userdata.

Returns udata.

debug.traceback ([thread,] [message [, level]])

If message is present but is neither a string nor nil, this function returns message without further processing. Otherwise, it returns a string with a traceback of the call stack. The optional message string is appended at the beginning of the traceback. An optional level number tells at which level to start the traceback (default is 1, the function calling traceback).

debug.upvalueid (f, n)

Returns a unique identifier (as a light userdata) for the upvalue numbered n from the given function.

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
After handling its options, lua runs the given script. When called without arguments, lua behaves as lua -v -i when the standard input (stdin) is a terminal, and as lua - otherwise.

When called without option -E, the interpreter checks for an environment variable LUA_INIT_5_3 (or LUA_INIT if the versioned name is not defined) before running any argument. If the variable content has the format @filename, then lua executes the file. Otherwise, lua executes the string itself.

When called with option -E, besides ignoring LUA_INIT, Lua also ignores the values of LUA_PATH and LUA_CPATH, setting the values of package.path and package.cpath with the default paths defined in luaconf.h.

All options are handled in order, except -i and -E. For instance, an invocation like

     $ lua -e'a=1' -e 'print(a)' script.lua
will first set a to 1, then print the value of a, and finally run the file script.lua with no arguments. (Here $ is the shell prompt. Your prompt may be different.)

Before running any code, lua collects all command-line arguments in a global table called arg. The script name goes to index 0, the first argument after the script name goes to index 1, and so on. Any arguments before the script name (that is, the interpreter name plus its options) go to negative indices. For instance, in the call

     $ lua -la b.lua t1 t2
the table is like this:

     arg = { [-2] = "lua", [-1] = "-la",
             [0] = "b.lua",
             [1] = "t1", [2] = "t2" }
If there is no script in the call, the interpreter name goes to index 0, followed by the other arguments. For instance, the call

     $ lua -e "print(arg[1])"
will print "-e". If there is a script, the script is called with parameters arg[1], ···, arg[#arg]. (Like all chunks in Lua, the script is compiled as a vararg function.)

In interactive mode, Lua repeatedly prompts and waits for a line. After reading a line, Lua first try to interpret the line as an expression. If it succeeds, it prints its value. Otherwise, it interprets the line as a statement. If you write an incomplete statement, the interpreter waits for its completion by issuing a different prompt.

In case of unprotected errors in the script, the interpreter reports the error to the standard error stream. If the error object is not a string but has a metamethod __tostring, the interpreter calls this metamethod to produce the final message. Otherwise, the interpreter converts the error object to a string and adds a stack traceback to it.

When finishing normally, the interpreter closes its main Lua state (see lua_close). The script can avoid this step by calling os.exit to terminate.

To allow the use of Lua as a script interpreter in Unix systems, the standalone interpreter skips the first line of a chunk if it starts with #. Therefore, Lua scripts can be made into executable programs by using chmod +x and the #! form, as in

     #!/usr/local/bin/lua
(Of course, the location of the Lua interpreter may be different in your machine. If lua is in your PATH, then

     #!/usr/bin/env lua
is a more portable solution.)

8 – Incompatibilities with the Previous Version

Here we list the incompatibilities that you may find when moving a program from Lua 5.2 to Lua 5.3. You can avoid some incompatibilities by compiling Lua with appropriate options (see file luaconf.h). However, all these compatibility options will be removed in the future.

Lua versions can always change the C API in ways that do not imply source-code changes in a program, such as the numeric values for constants or the implementation of functions as macros. Therefore, you should not assume that binaries are compatible between different Lua versions. Always recompile clients of the Lua API when using a new version.

Similarly, Lua versions can always change the internal representation of precompiled chunks; precompiled chunks are not compatible between different Lua versions.

The standard paths in the official distribution may change between versions.

8.1 – Changes in the Language

The main difference between Lua 5.2 and Lua 5.3 is the introduction of an integer subtype for numbers. Although this change should not affect "normal" computations, some computations (mainly those that involve some kind of overflow) can give different results.
You can fix these differences by forcing a number to be a float (in Lua 5.2 all numbers were float), in particular writing constants with an ending .0 or using x = x + 0.0 to convert a variable. (This recommendation is only for a quick fix for an occasional incompatibility; it is not a general guideline for good programming. For good programming, use floats where you need floats and integers where you need integers.)

The conversion of a float to a string now adds a .0 suffix to the result if it looks like an integer. (For instance, the float 2.0 will be printed as 2.0, not as 2.) You should always use an explicit format when you need a specific format for numbers.
(Formally this is not an incompatibility, because Lua does not specify how numbers are formatted as strings, but some programs assumed a specific format.)

The generational mode for the garbage collector was removed. (It was an experimental feature in Lua 5.2.)
8.2 – Changes in the Libraries

The bit32 library has been deprecated. It is easy to require a compatible external library or, better yet, to replace its functions with appropriate bitwise operations. (Keep in mind that bit32 operates on 32-bit integers, while the bitwise operators in Lua 5.3 operate on Lua integers, which by default have 64 bits.)
The Table library now respects metamethods for setting and getting elements.
The ipairs iterator now respects metamethods and its __ipairs metamethod has been deprecated.
Option names in io.read do not have a starting '*' anymore. For compatibility, Lua will continue to accept (and ignore) this character.
The following functions were deprecated in the mathematical library: atan2, cosh, sinh, tanh, pow, frexp, and ldexp. You can replace math.pow(x,y) with x^y; you can replace math.atan2 with math.atan, which now accepts one or two parameters; you can replace math.ldexp(x,exp) with x * 2.0^exp. For the other operations, you can either use an external library or implement them in Lua.
The searcher for C loaders used by require changed the way it handles versioned names. Now, the version should come after the module name (as is usual in most other tools). For compatibility, that searcher still tries the old format if it cannot find an open function according to the new style. (Lua 5.2 already worked that way, but it did not document the change.)
The call collectgarbage("count") now returns only one result. (You can compute that second result from the fractional part of the first result.)
8.3 – Changes in the API

Continuation functions now receive as parameters what they needed to get through lua_getctx, so lua_getctx has been removed. Adapt your code accordingly.
Function lua_dump has an extra parameter, strip. Use 0 as the value of this parameter to get the old behavior.
Functions to inject/project unsigned integers (lua_pushunsigned, lua_tounsigned, lua_tounsignedx, luaL_checkunsigned, luaL_optunsigned) were deprecated. Use their signed equivalents with a type cast.
Macros to project non-default integer types (luaL_checkint, luaL_optint, luaL_checklong, luaL_optlong) were deprecated. Use their equivalent over lua_Integer with a type cast (or, when possible, use lua_Integer in your code).

9 – The Complete Syntax of Lua

Here is the complete syntax of Lua in extended BNF. As usual in extended BNF, {A} means 0 or more As, and [A] means an optional A. (For operator precedences, see §3.4.8; for a description of the terminals Name, Numeral, and LiteralString, see §3.1.)


	chunk ::= block

	block ::= {stat} [retstat]

	stat ::=  ‘;’ | 
		 varlist ‘=’ explist | 
		 functioncall | 
		 label | 
		 break | 
		 goto Name | 
		 do block end | 
		 while exp do block end | 
		 repeat block until exp | 
		 if exp then block {elseif exp then block} [else block] end | 
		 for Name ‘=’ exp ‘,’ exp [‘,’ exp] do block end | 
		 for namelist in explist do block end | 
		 function funcname funcbody | 
		 local function Name funcbody | 
		 local namelist [‘=’ explist] 

	retstat ::= return [explist] [‘;’]

	label ::= ‘::’ Name ‘::’

	funcname ::= Name {‘.’ Name} [‘:’ Name]

	varlist ::= var {‘,’ var}

	var ::=  Name | prefixexp ‘[’ exp ‘]’ | prefixexp ‘.’ Name 

	namelist ::= Name {‘,’ Name}

	explist ::= exp {‘,’ exp}

	exp ::=  nil | false | true | Numeral | LiteralString | ‘...’ | functiondef | 
		 prefixexp | tableconstructor | exp binop exp | unop exp 

	prefixexp ::= var | functioncall | ‘(’ exp ‘)’

	functioncall ::=  prefixexp args | prefixexp ‘:’ Name args 

	args ::=  ‘(’ [explist] ‘)’ | tableconstructor | LiteralString 

	functiondef ::= function funcbody

	funcbody ::= ‘(’ [parlist] ‘)’ block end

	parlist ::= namelist [‘,’ ‘...’] | ‘...’

	tableconstructor ::= ‘{’ [fieldlist] ‘}’

	fieldlist ::= field {fieldsep field} [fieldsep]

	field ::= ‘[’ exp ‘]’ ‘=’ exp | Name ‘=’ exp | exp

	fieldsep ::= ‘,’ | ‘;’

	binop ::=  ‘+’ | ‘-’ | ‘*’ | ‘/’ | ‘//’ | ‘^’ | ‘%’ | 
		 ‘&’ | ‘~’ | ‘|’ | ‘>>’ | ‘<<’ | ‘..’ | 
		 ‘<’ | ‘<=’ | ‘>’ | ‘>=’ | ‘==’ | ‘~=’ | 
		 and | or

	unop ::= ‘-’ | not | ‘#’ | ‘~’

Last update: Fri Nov 27 18:43:18 BRST 2015
