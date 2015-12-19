3.4.11 – Function Definitions

The syntax for function definition is

	functiondef ::= function funcbody
	funcbody ::= ‘(’ [parlist] ‘)’ block end
The following syntactic sugar simplifies function definitions:

	stat ::= function funcname funcbody
	stat ::= local function Name funcbody
	funcname ::= Name {‘.’ Name} [‘:’ Name]
The statement

     function f () body end
translates to

     f = function () body end
The statement

     function t.a.b.c.f () body end
translates to

     t.a.b.c.f = function () body end
The statement

     local function f () body end
translates to

     local f; f = function () body end
not to

     local f = function () body end
(This only makes a difference when the body of the function contains references to f.)

A function definition is an executable expression, whose value has type function. When Lua precompiles a chunk, all its function bodies are precompiled too. Then, whenever Lua executes the function definition, the function is instantiated (or closed). This function instance (or closure) is the final value of the expression.

Parameters act as local variables that are initialized with the argument values:

	parlist ::= namelist [‘,’ ‘...’] | ‘...’
When a function is called, the list of arguments is adjusted to the length of the list of parameters, unless the function is a vararg function, which is indicated by three dots ('...') at the end of its parameter list. A vararg function does not adjust its argument list; instead, it collects all extra arguments and supplies them to the function through a vararg expression, which is also written as three dots. The value of this expression is a list of all actual extra arguments, similar to a function with multiple results. If a vararg expression is used inside another expression or in the middle of a list of expressions, then its return list is adjusted to one element. If the expression is used as the last element of a list of expressions, then no adjustment is made (unless that last expression is enclosed in parentheses).

As an example, consider the following definitions:

     function f(a, b) end
     function g(a, b, ...) end
     function r() return 1,2,3 end
Then, we have the following mapping from arguments to parameters and to the vararg expression:

     CALL            PARAMETERS
     
     f(3)             a=3, b=nil
     f(3, 4)          a=3, b=4
     f(3, 4, 5)       a=3, b=4
     f(r(), 10)       a=1, b=10
     f(r())           a=1, b=2
     
     g(3)             a=3, b=nil, ... -->  (nothing)
     g(3, 4)          a=3, b=4,   ... -->  (nothing)
     g(3, 4, 5, 8)    a=3, b=4,   ... -->  5  8
     g(5, r())        a=5, b=1,   ... -->  2  3
Results are returned using the return statement (see §3.3.4). If control reaches the end of a function without encountering a return statement, then the function returns with no results.

There is a system-dependent limit on the number of values that a function may return. This limit is guaranteed to be larger than 1000.

The colon syntax is used for defining methods, that is, functions that have an implicit extra parameter self. Thus, the statement

     function t.a.b.c:f (params) body end
is syntactic sugar for

     t.a.b.c.f = function (self, params) body end
3.5 – Visibility Rules

Lua is a lexically scoped language. The scope of a local variable begins at the first statement after its declaration and lasts until the last non-void statement of the innermost block that includes the declaration. Consider the following example:

     x = 10                -- global variable
     do                    -- new block
       local x = x         -- new 'x', with value 10
       print(x)            --> 10
       x = x+1
       do                  -- another block
         local x = x+1     -- another 'x'
         print(x)          --> 12
       end
       print(x)            --> 11
     end
     print(x)              --> 10  (the global one)
Notice that, in a declaration like local x = x, the new x being declared is not in scope yet, and so the second x refers to the outside variable.

Because of the lexical scoping rules, local variables can be freely accessed by functions defined inside their scope. A local variable used by an inner function is called an upvalue, or external local variable, inside the inner function.

Notice that each execution of a local statement defines new local variables. Consider the following example:

     a = {}
     local x = 20
     for i=1,10 do
       local y = 0
       a[i] = function () y=y+1; return x+y end
     end
The loop creates ten closures (that is, ten instances of the anonymous function). Each of these closures uses a different y variable, while all of them share the same x.

4 – The Application Program Interface

This section describes the C API for Lua, that is, the set of C functions available to the host program to communicate with Lua. All API functions and related types and constants are declared in the header file lua.h.

Even when we use the term "function", any facility in the API may be provided as a macro instead. Except where stated otherwise, all such macros use each of their arguments exactly once (except for the first argument, which is always a Lua state), and so do not generate any hidden side-effects.

As in most C libraries, the Lua API functions do not check their arguments for validity or consistency. However, you can change this behavior by compiling Lua with the macro LUA_USE_APICHECK defined.

4.1 – The Stack

Lua uses a virtual stack to pass values to and from C. Each element in this stack represents a Lua value (nil, number, string, etc.).

Whenever Lua calls C, the called function gets a new stack, which is independent of previous stacks and of stacks of C functions that are still active. This stack initially contains any arguments to the C function and it is where the C function pushes its results to be returned to the caller (see lua_CFunction).

For convenience, most query operations in the API do not follow a strict stack discipline. Instead, they can refer to any element in the stack by using an index: A positive index represents an absolute stack position (starting at 1); a negative index represents an offset relative to the top of the stack. More specifically, if the stack has n elements, then index 1 represents the first element (that is, the element that was pushed onto the stack first) and index n represents the last element; index -1 also represents the last element (that is, the element at the top) and index -n represents the first element.

4.2 – Stack Size

When you interact with the Lua API, you are responsible for ensuring consistency. In particular, you are responsible for controlling stack overflow. You can use the function lua_checkstack to ensure that the stack has enough space for pushing new elements.

Whenever Lua calls C, it ensures that the stack has space for at least LUA_MINSTACK extra slots. LUA_MINSTACK is defined as 20, so that usually you do not have to worry about stack space unless your code has loops pushing elements onto the stack.

When you call a Lua function without a fixed number of results (see lua_call), Lua ensures that the stack has enough space for all results, but it does not ensure any extra space. So, before pushing anything in the stack after such a call you should use lua_checkstack.

4.3 – Valid and Acceptable Indices

Any function in the API that receives stack indices works only with valid indices or acceptable indices.

A valid index is an index that refers to a position that stores a modifiable Lua value. It comprises stack indices between 1 and the stack top (1 ≤ abs(index) ≤ top) plus pseudo-indices, which represent some positions that are accessible to C code but that are not in the stack. Pseudo-indices are used to access the registry (see §4.5) and the upvalues of a C function (see §4.4).

Functions that do not need a specific mutable position, but only a value (e.g., query functions), can be called with acceptable indices. An acceptable index can be any valid index, but it also can be any positive index after the stack top within the space allocated for the stack, that is, indices up to the stack size. (Note that 0 is never an acceptable index.) Except when noted otherwise, functions in the API work with acceptable indices.

Acceptable indices serve to avoid extra tests against the stack top when querying the stack. For instance, a C function can query its third argument without the need to first check whether there is a third argument, that is, without the need to check whether 3 is a valid index.

For functions that can be called with acceptable indices, any non-valid index is treated as if it contains a value of a virtual type LUA_TNONE, which behaves like a nil value.

4.4 – C Closures

When a C function is created, it is possible to associate some values with it, thus creating a C closure (see lua_pushcclosure); these values are called upvalues and are accessible to the function whenever it is called.

Whenever a C function is called, its upvalues are located at specific pseudo-indices. These pseudo-indices are produced by the macro lua_upvalueindex. The first upvalue associated with a function is at index lua_upvalueindex(1), and so on. Any access to lua_upvalueindex(n), where n is greater than the number of upvalues of the current function (but not greater than 256, which is one plus the maximum number of upvalues in a closure), produces an acceptable but invalid index.

4.5 – Registry

Lua provides a registry, a predefined table that can be used by any C code to store whatever Lua values it needs to store. The registry table is always located at pseudo-index LUA_REGISTRYINDEX. Any C library can store data into this table, but it must take care to choose keys that are different from those used by other libraries, to avoid collisions. Typically, you should use as key a string containing your library name, or a light userdata with the address of a C object in your code, or any Lua object created by your code. As with variable names, string keys starting with an underscore followed by uppercase letters are reserved for Lua.

The integer keys in the registry are used by the reference mechanism (see luaL_ref) and by some predefined values. Therefore, integer keys must not be used for other purposes.

When you create a new Lua state, its registry comes with some predefined values. These predefined values are indexed with integer keys defined as constants in lua.h. The following constants are defined:

LUA_RIDX_MAINTHREAD: At this index the registry has the main thread of the state. (The main thread is the one created together with the state.)
LUA_RIDX_GLOBALS: At this index the registry has the global environment.
4.6 – Error Handling in C

Internally, Lua uses the C longjmp facility to handle errors. (Lua will use exceptions if you compile it as C++; search for LUAI_THROW in the source code for details.) When Lua faces any error (such as a memory allocation error, type errors, syntax errors, and runtime errors) it raises an error; that is, it does a long jump. A protected environment uses setjmp to set a recovery point; any error jumps to the most recent active recovery point.

If an error happens outside any protected environment, Lua calls a panic function (see lua_atpanic) and then calls abort, thus exiting the host application. Your panic function can avoid this exit by never returning (e.g., doing a long jump to your own recovery point outside Lua).

The panic function runs as if it were a message handler (see §2.3); in particular, the error message is at the top of the stack. However, there is no guarantee about stack space. To push anything on the stack, the panic function must first check the available space (see §4.2).

Most functions in the API can raise an error, for instance due to a memory allocation error. The documentation for each function indicates whether it can raise errors.

Inside a C function you can raise an error by calling lua_error.

