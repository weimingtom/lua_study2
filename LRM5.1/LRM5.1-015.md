【翻译】(LRM5.1-15)协程操纵(5.2)、模块(5.3)

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

5.2 - Coroutine Manipulation

5.2 - 协程操纵

The operations related to coroutines comprise a sub-library of the basic library and come inside the table coroutine. See §2.11 for a general description of coroutines. 

与协程相关的操作由基础库的一个子库组成且来自表coroutine内。见§2.11获取协程的一般描述。

--------------------------------------------------------------------------------

coroutine.create (f)

Creates a new coroutine, with body f. f must be a Lua function. Returns this new coroutine, an object with type "thread". 

一个新协程，带函数体f。f必须是一个Lua函数。返回这个新的协程，一个类型为"thread"的对象。

--------------------------------------------------------------------------------

coroutine.resume (co [, val1, ···])

Starts or continues the execution of coroutine co. The first time you resume a coroutine, it starts running its body. The values val1, ··· are passed as the arguments to the body function. If the coroutine has yielded, resume restarts it; the values val1, ··· are passed as the results from the yield. 

启动或继续协程co的执行。你第一次恢复一个协程时，它开始运行它的函数体。值val1，……被传递作为主体函数的参数。如果协程被挂起，resume重新启动它；值val1，……被传递作为来自yield的结果。 

If the coroutine runs without any errors, resume returns true plus any values passed to yield (if the coroutine yields) or any values returned by the body function (if the coroutine terminates). If there is any error, resume returns false plus the error message. 

如果协程不带任意错误地运行，resume返回true和被传递到yield的任意值（如果协程挂起）或被主体函数返回的任意值（如果协程终结）。如果有任意错误，resume返回false和错误消息。 

--------------------------------------------------------------------------------

coroutine.running ()

Returns the running coroutine, or nil when called by the main thread. 

返回正在运行的协程，或nil当被主线程调用时。

--------------------------------------------------------------------------------

coroutine.status (co)

Returns the status of coroutine co, as a string: "running", if the coroutine is running (that is, it called status); "suspended", if the coroutine is suspended in a call to yield, or if it has not started running yet; "normal" if the coroutine is active but not running (that is, it has resumed another coroutine); and "dead" if the coroutine has finished its body function, or if it has stopped with an error. 

返回协程co的状态，作为一个字符串："running"如果该协程正在运行（就是说，它调用了status）；"suspended"，如果该协程被暂停在一个对yield的调用，或者它还没有开始运行；"normal"如果协程是激活的但不是正在运行（就是说，它已经恢复另一个协程）；以及"dead"如果该协程已经完成它的主体函数，或者如果它已经带一个错误地停止。 

--------------------------------------------------------------------------------

coroutine.wrap (f)

Creates a new coroutine, with body f. f must be a Lua function. Returns a function that resumes the coroutine each time it is called. Any arguments passed to the function behave as the extra arguments to resume. Returns the same values returned by resume, except the first boolean. In case of error, propagates the error. 

创建一个新的协程，使用一个函数体f。f必须是一个Lua函数。返回一个函数，每当它被调用时它恢复该协程。被传递给该函数的任意参数的行为如同传给resume的额外参数。返回被resume所返回的相同值，除了第一个boolean值。在出错的情况下，传播该错误。 

--------------------------------------------------------------------------------

coroutine.yield (···)

Suspends the execution of the calling coroutine. The coroutine cannot be running a C function, a metamethod, or an iterator. Any arguments to yield are passed as extra results to resume. 

暂停调用方协程的执行。协程不能正在运行一个C函数，一个元方法，或一个迭代器。传给yield的任意参数被传递作为给resume的额外结果。 

5.3 - Modules

5.3 - 模块

The package library provides basic facilities for loading and building modules in Lua. It exports two of its functions directly in the global environment: require and module. Everything else is exported in a table package. 

包库提供加载和创建Lua中模块的基础工具。它直接地导出它的其中两个函数在全局环境中：require和module。其它所有东西被导出在一个表package中。 

--------------------------------------------------------------------------------

module (name [, ···])

Creates a module. If there is a table in package.loaded[name], this table is the module. Otherwise, if there is a global table t with the given name, this table is the module. Otherwise creates a new table t and sets it as the value of the global name and the value of package.loaded[name]. This function also initializes t._NAME with the given name, t._M with the module (t itself), and t._PACKAGE with the package name (the full module name minus last component; see below). Finally, module sets t as the new environment of the current function and the new value of package.loaded[name], so that require returns t. 

创建一个模块。如果在package.loaded[name]中存在一个表，那么这个表是该模块。否则，如果存在一个全局表t带有给定的名字，那么这个表是该模块。否则，创建一个新表t并设置它作为全局变量name的值和package.loaded[name]的值。这个函数还用给定名称初始化t._NAME，用该模块（t自身）初始化t._M，且用包名初始化t._PACKAGE（完全模块名减去最后部分；见下）。最后，module设置t作为当前函数的新环境和package.loaded[name]的新值，致使require返回t。 

If name is a compound name (that is, one with components separated by dots), module creates (or reuses, if they already exist) tables for each component. For instance, if name is a.b.c, then module stores the module table in field c of field b of global a. 

如果name是个一个复合名字（就是说，被点号分隔成几个部分的名称），那么module为每个部分创建（或重用，如果它们已经存在）表。例如，如果name是a.b.c，那么module在全局变量a的域b的域c中存储模块表。 

This function can receive optional options after the module name, where each option is a function to be applied over the module. 

这个函数可以在模块名后接收可选的选项，其中每个选项是要被应用在模块上的一个函数。 

--------------------------------------------------------------------------------

require (modname)

Loads the given module. The function starts by looking into the package.loaded table to determine whether modname is already loaded. If it is, then require returns the value stored at package.loaded[modname]. Otherwise, it tries to find a loader for the module. 

加载给定模块。这个函数开始的时候查看package.loaded表的内容以确认modname是否已经被加载。如果是，那么require返回存储在package.loaded[modname]中的值。否则，它尝试为该模块寻找一个加载器。 

To find a loader, require is guided by the package.loaders array. By changing this array, we can change how require looks for a module. The following explanation is based on the default configuration for package.loaders. 

为了找到一个加载器，require被package.loaders数组指引。通过改变这个数组，我们可以改变require如何寻找一个模块。以下解释是基于package.loaders的默认配置。 

First require queries package.preload[modname]. If it has a value, this value (which should be a function) is the loader. Otherwise require searches for a Lua loader using the path stored in package.path. If that also fails, it searches for a C loader using the path stored in package.cpath. If that also fails, it tries an all-in-one loader (see package.loaders). 

首先require查询package.preload[modname]。如果它拥有一个值，这个值（它应该是一个函数）是该加载器。否则，require搜索一个Lua加载器，通过使用存储在package.path中的路径。如果那也失败了，它搜索一个C加载器，通过使用存储在package.cpath中的路径。如果那也失败了，它尝试一个一体化加载器（见package.loaders）。 

Once a loader is found, require calls the loader with a single argument, modname. If the loader returns any value, require assigns the returned value to package.loaded[modname]. If the loader returns no value and has not assigned any value to package.loaded[modname], then require assigns true to this entry. In any case, require returns the final value of package.loaded[modname]. 

一旦一个加载器被找到，require用一个单一参数，modname，调用该加载器。如果该加载器返回任意值，那么require赋予返回的值给package.loaded[modname]。如果该加载器不返回值，且不曾赋任意值给package.loaded[modname]，那么require赋予true给这个记录。在任意情况下，require返回package.loaded[modname]的最终值。

If there is any error loading or running the module, or if it cannot find any loader for the module, then require signals an error. 

如果加载或运行该模块出现任意错误，或者如果它无法为该模块找到任意加载器，那么require发出一个错误。 

--------------------------------------------------------------------------------

package.cpath

The path used by require to search for a C loader. 

路径，被require用于搜索一个C加载器。 

Lua initializes the C path package.cpath in the same way it initializes the Lua path package.path, using the environment variable LUA_CPATH or a default path defined in luaconf.h. 

Lua以它初始化Lua路径package.path的相同方式初始化C路径package.cpath，通过使用环境变量LUA_CPATH或被定义在luaconf.h中的一个默认路径。 

--------------------------------------------------------------------------------

package.loaded

A table used by require to control which modules are already loaded. When you require a module modname and package.loaded[modname] is not false, require simply returns the value stored there. 

一个表，被require用于控制哪些模块已经被加载。当你包含一个模块modname而package.loaded[modname]不是false时，require简单地返回存储在那里的值。 

--------------------------------------------------------------------------------

package.loaders

A table used by require to control how to load modules. 

一个被require用于控制如何加载模块的表。 

Each entry in this table is a searcher function. When looking for a module, require calls each of these searchers in ascending order, with the module name (the argument given to require) as its sole parameter. The function can return another function (the module loader) or a string explaining why it did not find that module (or nil if it has nothing to say). Lua initializes this table with four functions. 

这个表的每一个记录是一个搜索器函数。当查找一个模块时，require以升序调用这些搜索器中的每个，使用模块名（传给require的参数）作为它的唯一参数。该函数可以返回另一个函数（模块加载器）或一个解释它为什么没找到那个模块的字符串（或nil，如果没东西要说）。Lua用四个函数初始化这个表。 

The first searcher simply looks for a loader in the package.preload table. 

第一个搜索器简单地在package.preload表中查找一个加载器。

The second searcher looks for a loader as a Lua library, using the path stored at package.path. A path is a sequence of templates separated by semicolons. For each template, the searcher will change each interrogation mark in the template by filename, which is the module name with each dot replaced by a "directory separator" (such as "/" in Unix); then it will try to open the resulting file name. So, for instance, if the Lua path is the string 

第二个搜索器查找加载器作为一个Lua库，通过使用存储在package.path中的路径。一个路径是被分号分隔的一连串模板。对于每个模板，搜索器将用filename改变模板中的每个问号，它是其每个点号被一个“目录分隔符”（诸如Unix中的"/"）替换的模块名；然后，它将尝试打开结果文件名。因此，例如，如果Lua路径是字符串 

     "./?.lua;./?.lc;/usr/local/?/init.lua"

the search for a Lua file for module foo will try to open the files ./foo.lua, ./foo.lc, and /usr/local/foo/init.lua, in that order. 

那么对模块foo的一个Lua文件的搜索将尝试以那个顺序打开文件./foo.lua，./foo.lc，和/usr/local/foo/init.lua。 

The third searcher looks for a loader as a C library, using the path given by the variable package.cpath. For instance, if the C path is the string 

第三个搜索器查找加载器作为一个C库，通过使用被变量package.cpath给定的路径。例如，如果C路径是字符串 

     "./?.so;./?.dll;/usr/local/?/init.so"

the searcher for module foo will try to open the files ./foo.so, ./foo.dll, and /usr/local/foo/init.so, in that order. Once it finds a C library, this searcher first uses a dynamic link facility to link the application with the library. Then it tries to find a C function inside the library to be used as the loader. The name of this C function is the string "luaopen_" concatenated with a copy of the module name where each dot is replaced by an underscore. Moreover, if the module name has a hyphen, its prefix up to (and including) the first hyphen is removed. For instance, if the module name is a.v1-b.c, the function name will be luaopen_b_c. 

那么模块foo的搜索器将尝试以那个顺序打开文件./foo.so，./foo.dll和/usr/local/foo/init.so。一旦它找到一个C库，这个搜索器首先用一个动态链接工具来链接应用程序和该库。然后它尝试在该库内找到一个C函数以被使用作为加载器。这个C函数的名称是字符串"luaopen_"拼接模块名的副本，其中每个点号被下划线替换。此外，如果模块名有一个连字符，它的直至（且包括）第一个连字符的前缀被移除。例如，如果模块名是a.v1-b.c，那么函数名将是luaopen_b_c。 

The fourth searcher tries an all-in-one loader. It searches the C path for a library for the root name of the given module. For instance, when requiring a.b.c, it will search for a C library for a. If found, it looks into it for an open function for the submodule; in our example, that would be luaopen_a_b_c. With this facility, a package can pack several C submodules into one single library, with each submodule keeping its original open function. 

第四个搜索器尝试一个一体化加载器。它为一个给定模块的根名称的库查找C路径。例如，当包含a.b.c时，它将为a搜索一个C库。如果找到的话，它在其中为子模块查找一个open函数；在我们的例子中，那将是luaopen_a_b_c。使用这个工具，一个包可以打包几个C子模块进一个单一库中，它的每个子模块保持有它原来的open函数。 

--------------------------------------------------------------------------------

package.loadlib (libname, funcname)

Dynamically links the host program with the C library libname. Inside this library, looks for a function funcname and returns this function as a C function. (So, funcname must follow the protocol (see lua_CFunction)). 

动态地链接宿主程序与C库libname。在该库内，查找一个函数funcname并返回这个函数作为一个C函数。（所以，funcname必须遵循协议（见lua_CFunction））。 

This is a low-level function. It completely bypasses the package and module system. Unlike require, it does not perform any path searching and does not automatically adds extensions. libname must be the complete file name of the C library, including if necessary a path and extension. funcname must be the exact name exported by the C library (which may depend on the C compiler and linker used). 

这是一个低层次函数。它完全绕过包和模块系统。不像require，它不执行任意路径搜索并且不自动地增加扩展名。libname必须是C库的完整文件名，如果需要的话还包括一个路径和扩展名。funcname必须是被C库导出的精确名称（它可能依赖于所使用的C编译器和链接器）。 

This function is not supported by ANSI C. As such, it is only available on some platforms (Windows, Linux, Mac OS X, Solaris, BSD, plus other Unix systems that support the dlfcn standard). 

这个函数不被ANSI C支持。因此，它只在一些平台上可用（Windows，Linux，Mac OS X，Solaris，BSD，以及支持dlfcn标准的其它Unix系统）。 （注：这里的dlfcn应该是指dlfcn.h，它包含dlopen等函数的声明）

--------------------------------------------------------------------------------

package.path

The path used by require to search for a Lua loader. 

路径，被require用于搜索一个Lua加载器。 

At start-up, Lua initializes this variable with the value of the environment variable LUA_PATH or with a default path defined in luaconf.h, if the environment variable is not defined. Any ";;" in the value of the environment variable is replaced by the default path. 

在启动时，Lua用环境变量LUA_PATH的值初始化该变量，或者使用定义在luaconf.h中的一个缺省路径，如果该环境变量不被定义。在该环境变量的值中的任意";;"都被默认路径替换。 

--------------------------------------------------------------------------------

package.preload

A table to store loaders for specific modules (see require). 

一个表，存储特定模块的加载器（见require）。 

--------------------------------------------------------------------------------

package.seeall (module)

Sets a metatable for module with its __index field referring to the global environment, so that this module inherits values from the global environment. To be used as an option to function module. 

为一个模块设置一个元表，它的__index域引用全局环境，致使这个模块继承自全局变量的值。被用于函数module的一个选项。 

-----------------------------------------

参考自：
1. Lua 5.1 参考手册 （云风译）
http://www.codingnow.com/2000/download/lua_manual.html
2. hshqcn
hshqcn  
