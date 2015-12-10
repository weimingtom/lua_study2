## 【翻译】(LRM5.1-7)函数和类型(3.7)(lua_Number - lua_rawseti)

See also:
http://www.lua.org/manual/5.1/manual.html  

原文见
http://www.lua.org/manual/5.1/manual.html  

-----------------------------------------  

## Lua 5.1 Reference Manual   

## Lua 5.1参考手册  

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes   

作者：Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes  

Copyright ? 2006-2008 Lua.org, PUC-Rio. Freely available under the terms of the Lua license.   

版权所有 (c) 2006-2008 Lua.org, PUC-Rio. 根据Lua许可证自由地**（注：免费）**可用  

-----------------------------------------  

lua_Number  

typedef double lua_Number;  

The type of numbers in Lua. By default, it is double, but that can be changed in luaconf.h.   

Lua中的数的类型。缺省，它是double**（注：双精型）**，但在luaconf.h中可以改变它。  

Through the configuration file you can change Lua to operate with another type for numbers (e.g., float or long).   

通过配置文件你能改变Lua用另一个类型操作数（例如，float**（注：浮点型）**或long**（注：长整型）**）。   

--------------------------------------------------------------------------------  

lua_objlen  
[-0, +0, -]   

size_t lua_objlen (lua_State *L, int index);  

Returns the "length" of the value at the given acceptable index: for strings, this is the string length; for tables, this is the result of the length operator ('#'); for userdata, this is the size of the block of memory allocated for the userdata; for other values, it is 0.   

返回给定可接受索引上的值的“长度”：对于字符串，这是字符串长度；对于表，这是长度操作符（"#"）的结果；对于用户数据，这是为该用户数据分配的内存块的大小；对于其它类型，它是0。  

--------------------------------------------------------------------------------  

lua_pcall  
[-(nargs + 1), +(nresults|1), -]   

int lua_pcall (lua_State *L, int nargs, int nresults, int errfunc);  

Calls a function in protected mode.   

在保护模式下调用一个函数。   

Both nargs and nresults have the same meaning as in lua_call. If there are no errors during the call, lua_pcall behaves exactly like lua_call. However, if there is any error, lua_pcall catches it, pushes a single value on the stack (the error message), and returns an error code. Like lua_call, lua_pcall always removes the function and its arguments from the stack.   

nargs和nresults与lua_call中的拥有相同的意思。如果在该调用期间没有错误，那么lua_pcall的行为完全像lua_call。然而，如果有任意错误，那么lua_pcall捕捉它，压入一个单一值到栈上（错误消息），并返回一个错误码。像lua_call那样，lua_pcall总是从栈中移除该函数及它的参数。  

If errfunc is 0, then the error message returned on the stack is exactly the original error message. Otherwise, errfunc is the stack index of an error handler function. (In the current implementation, this index cannot be a pseudo-index.) In case of runtime errors, this function will be called with the error message and its return value will be the message returned on the stack by lua_pcall.   

如果errfunc是0，那么在栈上返回的错误消息正好是原始的错误消息。否则，errfunc是一个错误句柄函数的栈索引。（在当前实现中，这个索引不能是伪索引。）在发生运行时错误的情况下，这个函数将用错误消息被调用，而它的返回值将是在栈上被lua_pcall返回的消息。  

Typically, the error handler function is used to add more debug information to the error message, such as a stack traceback. Such information cannot be gathered after the return of lua_pcall, since by then the stack has unwound.   

通常，错误句柄函数被用于添加更多调试信息到错误消息，诸如一个栈回溯。这些信息无法在lua_pcall返回后被收集，因为到那时候栈已经被展开。   

The lua_pcall function returns 0 in case of success or one of the following error codes (defined in lua.h):   

lua_pcall函数返回0如果成功，或返回以下其中一个错误码（定义在lua.h中）：   

LUA_ERRRUN: a runtime error.   

LUA_ERRRUN：一个运行时错误。  

LUA_ERRMEM: memory allocation error. For such errors, Lua does not call the error handler function.   

LUA_ERRMEM：内存分配错误。对于这些错误，Lua不调用错误句柄函数。   

LUA_ERRERR: error while running the error handler function.   

LUA_ERRERR：错误，当运行错误句柄函数时。   

--------------------------------------------------------------------------------  

lua_pop  
[-n, +0, -]   

void lua_pop (lua_State *L, int n);  

Pops n elements from the stack.   

从栈中弹出n个元素。   

--------------------------------------------------------------------------------  

lua_pushboolean  
[-0, +1, -]   

void lua_pushboolean (lua_State *L, int b);  

Pushes a boolean value with value b onto the stack.   

压入带值b的一个布尔值到栈上。  

--------------------------------------------------------------------------------  

lua_pushcclosure  
[-n, +1, m]   

void lua_pushcclosure (lua_State *L, lua_CFunction fn, int n);  

Pushes a new C closure onto the stack.   

压入新的C闭包到栈上。  

When a C function is created, it is possible to associate some values with it, thus creating a C closure (see §3.4); these values are then accessible to the function whenever it is called. To associate values with a C function, first these values should be pushed onto the stack (when there are multiple values, the first value is pushed first). Then lua_pushcclosure is called to create and push the C function onto the stack, with the argument n telling how many values should be associated with the function. lua_pushcclosure also pops these values from the stack.   

当一个C函数被创建时，有可能关联一些值到它，因此创建一个C闭包（见§3.4）；然后这些值对于该函数是可访问的，不管它合适被调用。为了关联值与一个C函数，首先这些值应该被压入到栈上（当有多个值时，第一个值被首先压入）。然后lua_pushcclosure被调用以创建和压入C函数到栈上，用参数n告知有多少值应该与该函数关联。lua_pushcclosure还从栈中弹出这些值。  

The maximum value for n is 255.   

n的最大值为255。   

--------------------------------------------------------------------------------  

lua_pushcfunction  
[-0, +1, m]   

void lua_pushcfunction (lua_State *L, lua_CFunction f);  

Pushes a C function onto the stack. This function receives a pointer to a C function and pushes onto the stack a Lua value of type function that, when called, invokes the corresponding C function.   

压入一个C函数到栈上。这个函数接收一个指向C函数的指针并且压入一个类型为function的Lua值到栈上，当这个Lua值被调用时，它调用相应的C函数。  

Any function to be registered in Lua must follow the correct protocol to receive its parameters and return its results (see lua_CFunction).   

任何要在Lua中注册的函数必须遵循正确的协议以接收它的参数和返回它的结果（见lua_CFunction）。   

lua_pushcfunction is defined as a macro:   

lua_pushcfunction被定义为一个宏：   

     #define lua_pushcfunction(L,f)  lua_pushcclosure(L,f,0)  

--------------------------------------------------------------------------------  

lua_pushfstring  
[-0, +1, m]   

const char *lua_pushfstring (lua_State *L, const char *fmt, ...);  

Pushes onto the stack a formatted string and returns a pointer to this string. It is similar to the C function sprintf, but has some important differences:   

压入一个格式化字符串到栈上并返回指向这个字符串的指针。它类似于C函数sprintf，但有一些重要的区别：   

You do not have to allocate space for the result: the result is a Lua string and Lua takes care of memory allocation (and deallocation, through garbage collection).   

你不必为结果分配空间：结果是一个Lua字符串而Lua会负责内存分配（以及解除分配，通过垃圾收集）。   

The conversion specifiers are quite restricted. There are no flags, widths, or precisions. The conversion specifiers can only be '%%' (inserts a '%' in the string), '%s' (inserts a zero-terminated string, with no size restrictions), '%f' (inserts a lua_Number), '%p' (inserts a pointer as a hexadecimal numeral), '%d' (inserts an int), and '%c' (inserts an int as a character).   

转换说明符有很多限制。没有开关，宽度，或精度。转换说明符只可能是'%%'（在字符串中插入一个'%'），'%s'（插入一个以0终结字符串，没有大小限制），'%f'（插入一个lua_Number），'%p'（插入一个指针作为一个十六进制数字），'%d' （插入一个整型），以及'%c'（插入一个整型作为一个字符）。   

--------------------------------------------------------------------------------  

lua_pushinteger  
[-0, +1, -]   

void lua_pushinteger (lua_State *L, lua_Integer n);  

Pushes a number with value n onto the stack.   

压入一个带有值n的数到栈上。  

--------------------------------------------------------------------------------  

lua_pushlightuserdata  
[-0, +1, -]   

void lua_pushlightuserdata (lua_State *L, void *p);  

Pushes a light userdata onto the stack.   

压入一个轻量级用户数据到栈上。   

Userdata represent C values in Lua. A light userdata represents a pointer. It is a value (like a number): you do not create it, it has no individual metatable, and it is not collected (as it was never created). A light userdata is equal to "any" light userdata with the same C address.   

在Lua中用户数据代表C值。一个轻量级用户数据代表一个指针。它是一个值（像一个数）：你不创建它，它没有单独的元表，而且它不会被回收（如同它从不被创建）。一个轻量级用户数据等于“任意”带有相同C地址的轻量级用户数据。   

--------------------------------------------------------------------------------  

lua_pushliteral  
[-0, +1, m]   

void lua_pushliteral (lua_State *L, const char *s);  

This macro is equivalent to lua_pushlstring, but can be used only when s is a literal string. In these cases, it automatically provides the string length.   

这个宏等效于lua_pushlstring，但仅当s是一个字面字符串时才可以被使用。在这些情况下，它自动地提供字符串长度。   

--------------------------------------------------------------------------------  

lua_pushlstring  
[-0, +1, m]   

void lua_pushlstring (lua_State *L, const char *s, size_t len);  

Pushes the string pointed to by s with size len onto the stack. Lua makes (or reuses) an internal copy of the given string, so the memory at s can be freed or reused immediately after the function returns. The string can contain embedded zeros.   

压入被s指向的大小为len的字符串到栈上。Lua制造（或重用）给定字符串的内部副本，所以s上的内存可以在函数返回之后立即被释放或重用。字符串可以包含内嵌的0。  

--------------------------------------------------------------------------------  

lua_pushnil  
[-0, +1, -]   

void lua_pushnil (lua_State *L);  

Pushes a nil value onto the stack.   

压入一个nil值到栈上。  

--------------------------------------------------------------------------------  

lua_pushnumber  
[-0, +1, -]   

void lua_pushnumber (lua_State *L, lua_Number n);  

Pushes a number with value n onto the stack.   

压入一个值为n的数到栈上。  

--------------------------------------------------------------------------------  

lua_pushstring  
[-0, +1, m]   

void lua_pushstring (lua_State *L, const char *s);  

Pushes the zero-terminated string pointed to by s onto the stack. Lua makes (or reuses) an internal copy of the given string, so the memory at s can be freed or reused immediately after the function returns. The string cannot contain embedded zeros; it is assumed to end at the first zero.   

压入被s指向的一个零终结字符串到栈上。Lua制造（或重用）给定字符串的一个内部副本，所以在s上的内存可以在函数返回后即时被释放或重用。该字符串不能包含复数个内嵌零；假设它在第一个零处结束。  

--------------------------------------------------------------------------------  

lua_pushthread  
[-0, +1, -]   

int lua_pushthread (lua_State *L);  

Pushes the thread represented by L onto the stack. Returns 1 if this thread is the main thread of its state.   

压入L代表的线程到栈上。返回1，如果这个线程是它的状态的主线程。  

--------------------------------------------------------------------------------  

lua_pushvalue  
[-0, +1, -]   

void lua_pushvalue (lua_State *L, int index);  

Pushes a copy of the element at the given valid index onto the stack.   

压入给定可用索引上的元素的一个副本到栈上。  

--------------------------------------------------------------------------------  

lua_pushvfstring  
[-0, +1, m]   

const char *lua_pushvfstring (lua_State *L,  
                              const char *fmt,  
                              va_list argp);  

Equivalent to lua_pushfstring, except that it receives a va_list instead of a variable number of arguments.   

等效于lua_pushfstring，除了接收一个va_list而非一些可变个数的参数。  

--------------------------------------------------------------------------------  

lua_rawequal  
[-0, +0, -]   

int lua_rawequal (lua_State *L, int index1, int index2);  

Returns 1 if the two values in acceptable indices index1 and index2 are primitively equal (that is, without calling metamethods). Otherwise returns 0. Also returns 0 if any of the indices are non valid.   

返回1，如果可接受索引index1和index2上的值是原始相等的（就是说，不调用元方法）。否则返回0。还返回0，如果任意一个索引非可用。  

--------------------------------------------------------------------------------  

lua_rawget  
[-1, +1, -]   

void lua_rawget (lua_State *L, int index);  

Similar to lua_gettable, but does a raw access (i.e., without metamethods).   

类似于lua_gettable，但执行一次原始访问（即，不使用元方法）。   

--------------------------------------------------------------------------------  

lua_rawgeti  
[-0, +1, -]   

void lua_rawgeti (lua_State *L, int index, int n);  

Pushes onto the stack the value t[n], where t is the value at the given valid index. The access is raw; that is, it does not invoke metamethods.   

压入值t[n]到栈上，这里t是所给可用索引上的值。访问是原始的；就是说，不调用元方法。  

--------------------------------------------------------------------------------  

lua_rawset  
[-2, +0, m]   

void lua_rawset (lua_State *L, int index);  

Similar to lua_settable, but does a raw assignment (i.e., without metamethods).   

类似lua_settable，但执行一次原始赋值（即，不使用元方法）。   

--------------------------------------------------------------------------------  

lua_rawseti  
[-1, +0, m]   

void lua_rawseti (lua_State *L, int index, int n);  

Does the equivalent of t[n] = v, where t is the value at the given valid index and v is the value at the top of the stack.   

执行t[n] = v的等效操作，这里t是所给可用索引上的值，而v是栈顶的值。   

This function pops the value from the stack. The assignment is raw; that is, it does not invoke metamethods.   

这个函数从栈中弹出该值。赋值是原始的；就是说，不调用元方法。   


-----------------------------------------

## 参考自：  
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  

2. hshqcn  
hshqcn  
