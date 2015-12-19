8 – Incompatibilities with the Previous Version

Here we list the incompatibilities that you may find when moving a program from Lua 5.1 to Lua 5.2. You can avoid some incompatibilities by compiling Lua with appropriate options (see file luaconf.h). However, all these compatibility options will be removed in the next version of Lua. Similarly, all features marked as deprecated in Lua 5.1 have been removed in Lua 5.2.

8.1 – Changes in the Language

The concept of environment changed. Only Lua functions have environments. To set the environment of a Lua function, use the variable _ENV or the function load.
C functions no longer have environments. Use an upvalue with a shared table if you need to keep shared state among several C functions. (You may use luaL_setfuncs to open a C library with all functions sharing a common upvalue.)

To manipulate the "environment" of a userdata (which is now called user value), use the new functions lua_getuservalue and lua_setuservalue.

Lua identifiers cannot use locale-dependent letters.
Doing a step or a full collection in the garbage collector does not restart the collector if it has been stopped.
Weak tables with weak keys now perform like ephemeron tables.
The event tail return in debug hooks was removed. Instead, tail calls generate a special new event, tail call, so that the debugger can know that there will not be a corresponding return event.
Equality between function values has changed. Now, a function definition may not create a new value; it may reuse some previous value if there is no observable difference to the new function.
8.2 – Changes in the Libraries

Function module is deprecated. It is easy to set up a module with regular Lua code. Modules are not expected to set global variables.
Functions setfenv and getfenv were removed, because of the changes in environments.
Function math.log10 is deprecated. Use math.log with 10 as its second argument, instead.
Function loadstring is deprecated. Use load instead; it now accepts string arguments and are exactly equivalent to loadstring.
Function table.maxn is deprecated. Write it in Lua if you really need it.
Function os.execute now returns true when command terminates successfully and nil plus error information otherwise.
Function unpack was moved into the table library and therefore must be called as table.unpack.
Character class %z in patterns is deprecated, as now patterns may contain '\0' as a regular character.
The table package.loaders was renamed package.searchers.
Lua does not have bytecode verification anymore. So, all functions that load code (load and loadfile) are potentially insecure when loading untrusted binary data. (Actually, those functions were already insecure because of flaws in the verification algorithm.) When in doubt, use the mode argument of those functions to restrict them to loading textual chunks.
The standard paths in the official distribution may change between versions.
8.3 – Changes in the API

Pseudoindex LUA_GLOBALSINDEX was removed. You must get the global environment from the registry (see §4.5).
Pseudoindex LUA_ENVIRONINDEX and functions lua_getfenv/lua_setfenv were removed, as C functions no longer have environments.
Function luaL_register is deprecated. Use luaL_setfuncs so that your module does not create globals. (Modules are not expected to set global variables anymore.)
The osize argument to the allocation function may not be zero when creating a new block, that is, when ptr is NULL (see lua_Alloc). Use only the test ptr == NULL to check whether the block is new.
Finalizers (__gc metamethods) for userdata are called in the reverse order that they were marked for finalization, not that they were created (see §2.5.1). (Most userdata are marked immediately after they are created.) Moreover, if the metatable does not have a __gc field when set, the finalizer will not be called, even if it is set later.
luaL_typerror was removed. Write your own version if you need it.
Function lua_cpcall is deprecated. You can simply push the function with lua_pushcfunction and call it with lua_pcall.
Functions lua_equal and lua_lessthan are deprecated. Use the new lua_compare with appropriate options instead.
Function lua_objlen was renamed lua_rawlen.
Function lua_load has an extra parameter, mode. Pass NULL to simulate the old behavior.
Function lua_resume has an extra parameter, from. Pass NULL or the thread doing the call.
9 – The Complete Syntax of Lua

Here is the complete syntax of Lua in extended BNF. (It does not describe operator precedences.)


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

	exp ::=  nil | false | true | Number | String | ‘...’ | functiondef | 
		 prefixexp | tableconstructor | exp binop exp | unop exp 

	prefixexp ::= var | functioncall | ‘(’ exp ‘)’

	functioncall ::=  prefixexp args | prefixexp ‘:’ Name args 

	args ::=  ‘(’ [explist] ‘)’ | tableconstructor | String 

	functiondef ::= function funcbody

	funcbody ::= ‘(’ [parlist] ‘)’ block end

	parlist ::= namelist [‘,’ ‘...’] | ‘...’

	tableconstructor ::= ‘{’ [fieldlist] ‘}’

	fieldlist ::= field {fieldsep field} [fieldsep]

	field ::= ‘[’ exp ‘]’ ‘=’ exp | Name ‘=’ exp | exp

	fieldsep ::= ‘,’ | ‘;’

	binop ::= ‘+’ | ‘-’ | ‘*’ | ‘/’ | ‘^’ | ‘%’ | ‘..’ | 
		 ‘<’ | ‘<=’ | ‘>’ | ‘>=’ | ‘==’ | ‘~=’ | 
		 and | or

	unop ::= ‘-’ | not | ‘#’

Last update: Fri Mar 6 14:18:03 BRT 2015
