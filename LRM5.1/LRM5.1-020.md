【翻译】(LRM5.1-20)与以前版本的不兼容(7)、语言的改变(7.1)、库的改变(7.2)、API的改变(7.3)、Lua的完整语法(8)  

See also:
http://www.lua.org/manual/5.1/manual.html

原文见
http://www.lua.org/manual/5.1/manual.html

-----------------------------------------

Lua 5.1 Reference Manual 

Lua 5.1参考手册

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes 

作者：Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes

Copyright ? 2006-2008 Lua.org, PUC-Rio. Freely available under the terms of the Lua license. 

版权所有 (c) 2006-2008 Lua.org, PUC-Rio. 根据Lua许可证自由地（注：免费）可用

-----------------------------------------

7 - Incompatibilities with the Previous Version
Here we list the incompatibilities that you may find when moving a program from Lua 5.0 to Lua 5.1. You can avoid most of the incompatibilities compiling Lua with appropriate options (see file luaconf.h). However, all these compatibility options will be removed in the next version of Lua. 



7 - 与以前版本的不兼容 
在这里，我们列举不兼容性，你会在把lua 5.0的代码迁移到Lua 5.1时发现。
你可以通过使用合适的选项（见文件luaconf.h）编译Lua来避免大部分不兼容性。
然而，所有这些兼容性选项都将在Lua的下一个版本中删除。







7.1 - Changes in the Language
The vararg system changed from the pseudo-argument arg with a table with the extra arguments to the vararg expression. (See compile-time option LUA_COMPAT_VARARG in luaconf.h.) 
There was a subtle change in the scope of the implicit variables of the for statement and for the repeat statement. 
The long string/long comment syntax ([[string]]) does not allow nesting. You can use the new syntax ([=[string]=]) in these cases. (See compile-time option LUA_COMPAT_LSTR in luaconf.h.) 



7.1 - 语言的改变 
变长参数系统从带额外参数的表的伪参数arg改为变长参数表达式。
（见luaconf.h的编译期选项LUA_COMPAT_VARARG。）
在for语句的显式变量作用域和repeat语句中存在微妙的改变。
长字符串和长注释语法（[[string]]）不允许嵌套。
在这些场合下你可以使用新的语法（[=[string]=]）。
（见luaconf.h的编译期选项LUA_COMPAT_LSTR。）





7.2 - Changes in the Libraries
Function string.gfind was renamed string.gmatch. (See compile-time option LUA_COMPAT_GFIND in luaconf.h.) 
When string.gsub is called with a function as its third argument, whenever this function returns nil or false the replacement string is the whole match, instead of the empty string. 
Function table.setn was deprecated. Function table.getn corresponds to the new length operator (#); use the operator instead of the function. (See compile-time option LUA_COMPAT_GETN in luaconf.h.) 
Function loadlib was renamed package.loadlib. (See compile-time option LUA_COMPAT_LOADLIB in luaconf.h.) 
Function math.mod was renamed math.fmod. (See compile-time option LUA_COMPAT_MOD in luaconf.h.) 
Functions table.foreach and table.foreachi are deprecated. You can use a for loop with pairs or ipairs instead. 
There were substantial changes in function require due to the new module system. However, the new behavior is mostly compatible with the old, but require gets the path from package.path instead of from LUA_PATH. 
Function collectgarbage has different arguments. Function gcinfo is deprecated; use collectgarbage("count") instead. 



7.2 - 库的改变 
函数string.gfind更名为string.gmatch。
（见luaconf.h的编译期选项LUA_COMPAT_GFIND。） 
当string.gsub以一个函数作为它的第三参数，不管这个函数返回nil还是false，替换字符串都是完全匹配，而不是一个空字符串。
函数table.setn被废弃。
函数table.getn对应新的长度运算符（#）；使用运算符取代函数。
（见luaconf.h的编译期选项LUA_COMPAT_GETN。） 
函数loadlib更名为package.loadlib。
（见luaconf.h的编译期选项LUA_COMPAT_LOADLIB。） 
函数math.mod更名为math.fmod。
（见luaconf.h的编译期选项LUA_COMPAT_MOD。） 
函数table.foreach和table.foreachi已废弃。
你可以使用带pairs或ipairs的for循环代替它们。
由于新的模块系统，require函数有很大的改变。
然而，新的行为与旧的大部分兼容，但require得到的是来自package.path的路径而不是来自LUA_PATH的路径。 
函数collectgarbage有不同的参数。
函数gcinfo已经废弃；用collectgarbage("count")代替。 




7.3 - Changes in the API
The luaopen_* functions (to open libraries) cannot be called directly, like a regular C function. They must be called through Lua, like a Lua function. 
Function lua_open was replaced by lua_newstate to allow the user to set a memory-allocation function. You can use luaL_newstate from the standard library to create a state with a standard allocation function (based on realloc). 
Functions luaL_getn and luaL_setn (from the auxiliary library) are deprecated. Use lua_objlen instead of luaL_getn and nothing instead of luaL_setn. 
Function luaL_openlib was replaced by luaL_register. 
Function luaL_checkudata now throws an error when the given value is not a userdata of the expected type. (In Lua 5.0 it returned NULL.) 



7.3 - API的改变
luaopen_*函数（用于打开库）想普通的C函数那样不能直接调用。
它们必须通过Lua来调用，就像一个Lua函数。
函数lua_open改为lua_newstate，允许用户设置一个内存分配函数。
你可以使用标准库的luaL_newstate创建具有一个具有标准分配函数的状态（基于realloc）。 
函数luaL_getn和luaL_setn（出自辅助库）已废弃。
使用lua_objlen代替luaL_getn，没有函数代替luaL_setn。 
函数luaL_openlib改为luaL_register。
当给定的值不是所期望类型的userdata时，函数luaL_checkudata现在会抛出一个错误。（在Lua 5.0中它返回NULL。）














8 - The Complete Syntax of Lua
Here is the complete syntax of Lua in extended BNF. (It does not describe operator precedences.) 


	chunk ::= {stat [`;′]} [laststat [`;′]]

	block ::= chunk

	stat ::=  varlist `=′ explist | 
		 functioncall | 
		 do block end | 
		 while exp do block end | 
		 repeat block until exp | 
		 if exp then block {elseif exp then block} [else block] end | 
		 for Name `=′ exp `,′ exp [`,′ exp] do block end | 
		 for namelist in explist do block end | 
		 function funcname funcbody | 
		 local function Name funcbody | 
		 local namelist [`=′ explist] 

	laststat ::= return [explist] | break

	funcname ::= Name {`.′ Name} [`:′ Name]

	varlist ::= var {`,′ var}

	var ::=  Name | prefixexp `[′ exp `]′ | prefixexp `.′ Name 

	namelist ::= Name {`,′ Name}

	explist ::= {exp `,′} exp

	exp ::=  nil | false | true | Number | String | `...′ | function | 
		 prefixexp | tableconstructor | exp binop exp | unop exp 

	prefixexp ::= var | functioncall | `(′ exp `)′

	functioncall ::=  prefixexp args | prefixexp `:′ Name args 

	args ::=  `(′ [explist] `)′ | tableconstructor | String 

	function ::= function funcbody

	funcbody ::= `(′ [parlist] `)′ block end

	parlist ::= namelist [`,′ `...′] | `...′

	tableconstructor ::= `{′ [fieldlist] `}′

	fieldlist ::= field {fieldsep field} [fieldsep]

	field ::= `[′ exp `]′ `=′ exp | Name `=′ exp | exp

	fieldsep ::= `,′ | `;′

	binop ::= `+′ | `-′ | `*′ | `/′ | `^′ | `%′ | `..′ | 
		 `<′ | `<=′ | `>′ | `>=′ | `==′ | `~=′ | 
		 and | or

	unop ::= `-′ | not | `#′



8 - Lua的完整语法 
这里是以扩展巴科斯范式（扩展BNF）描述的Lua完整语法。
（它不描述操作的优先级。）
chunk ::= {stat [';']} [laststat [';']]
block ::= chunk
stat ::=  varlist '=' explist | 
	 functioncall | 
	 do block end | 
	 while exp do block end | 
	 repeat block until exp | 
	 if exp then block {elseif exp then block} [else block] end | 
	 for Name '=' exp ',' exp [',' exp] do block end | 
	 for namelist in explist do block end | 
	 function funcname funcbody | 
	 local function Name funcbody | 
	 local namelist ['=' explist] 
laststat ::= return [explist] | break
funcname ::= Name {'.' Name} [':' Name]
varlist ::= var {',' var}
var ::=  Name | prefixexp '[' exp ']' | prefixexp '.' Name 
namelist ::= Name {',' Name}
explist ::= {exp ','} exp
exp ::=  nil | false | true | Number | String | '...' | function | 
	 prefixexp | tableconstructor | exp binop exp | unop exp 
prefixexp ::= var | functioncall | '(' exp ')'
functioncall ::=  prefixexp args | prefixexp ':' Name args 
args ::=  '(' [explist] ')' | tableconstructor | String 
function ::= function funcbody
funcbody ::= '(' [parlist] ')' block end
parlist ::= namelist [',' '...'] | '...'
tableconstructor ::= '{' [fieldlist] '}'
fieldlist ::= field {fieldsep field} [fieldsep]
field ::= '[' exp ']' '=' exp | Name '=' exp | exp
fieldsep ::= ',' | ';'
binop ::= '+' | '-' | '*' | '/' | '^' | '%' | '..' | 
	 '<' | '<=' | '>' | '>=' | '==' | '~=' | 
	 and | or
unop ::= '-' | not | '#'




--------------------------------------------------------------------------------
Last update: Mon Aug 18 13:25:46 BRT 2008 

--------------------------------------------------------------------------------
（注：TODO）

最近更新：巴西利亚时间2008/08/18 周一 13:25:46  
