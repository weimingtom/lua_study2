﻿6 – Standard Libraries

The standard Lua libraries provide useful functions that are implemented directly through the C API. Some of these functions provide essential services to the language (e.g., type and getmetatable); others provide access to "outside" services (e.g., I/O); and others could be implemented in Lua itself, but are quite useful or have critical performance requirements that deserve an implementation in C (e.g., table.sort).

All libraries are implemented through the official C API and are provided as separate C modules. Currently, Lua has the following standard libraries:

basic library (§6.1);
coroutine library (§6.2);
package library (§6.3);
string manipulation (§6.4);
basic UTF-8 support (§6.5);
table manipulation (§6.6);
mathematical functions (§6.7) (sin, log, etc.);
input and output (§6.8);
operating system facilities (§6.9);
debug facilities (§6.10).
Except for the basic and the package libraries, each library provides all its functions as fields of a global table or as methods of its objects.

To have access to these libraries, the C host program should call the luaL_openlibs function, which opens all standard libraries. Alternatively, the host program can open them individually by using luaL_requiref to call luaopen_base (for the basic library), luaopen_package (for the package library), luaopen_coroutine (for the coroutine library), luaopen_string (for the string library), luaopen_utf8 (for the UTF8 library), luaopen_table (for the table library), luaopen_math (for the mathematical library), luaopen_io (for the I/O library), luaopen_os (for the operating system library), and luaopen_debug (for the debug library). These functions are declared in lualib.h.

6.1 – Basic Functions

The basic library provides core functions to Lua. If you do not include this library in your application, you should check carefully whether you need to provide implementations for some of its facilities.

assert (v [, message])

Calls error if the value of its argument v is false (i.e., nil or false); otherwise, returns all its arguments. In case of error, message is the error object; when absent, it defaults to "assertion failed!"

collectgarbage ([opt [, arg]])

This function is a generic interface to the garbage collector. It performs different functions according to its first argument, opt:

"collect": performs a full garbage-collection cycle. This is the default option.
"stop": stops automatic execution of the garbage collector. The collector will run only when explicitly invoked, until a call to restart it.
"restart": restarts automatic execution of the garbage collector.
"count": returns the total memory in use by Lua in Kbytes. The value has a fractional part, so that it multiplied by 1024 gives the exact number of bytes in use by Lua (except for overflows).
"step": performs a garbage-collection step. The step "size" is controlled by arg. With a zero value, the collector will perform one basic (indivisible) step. For non-zero values, the collector will perform as if that amount of memory (in KBytes) had been allocated by Lua. Returns true if the step finished a collection cycle.
"setpause": sets arg as the new value for the pause of the collector (see §2.5). Returns the previous value for pause.
"setstepmul": sets arg as the new value for the step multiplier of the collector (see §2.5). Returns the previous value for step.
"isrunning": returns a boolean that tells whether the collector is running (i.e., not stopped).
dofile ([filename])

Opens the named file and executes its contents as a Lua chunk. When called without arguments, dofile executes the contents of the standard input (stdin). Returns all values returned by the chunk. In case of errors, dofile propagates the error to its caller (that is, dofile does not run in protected mode).
error (message [, level])

Terminates the last protected function called and returns message as the error object. Function error never returns.
Usually, error adds some information about the error position at the beginning of the message, if the message is a string. The level argument specifies how to get the error position. With level 1 (the default), the error position is where the error function was called. Level 2 points the error to where the function that called error was called; and so on. Passing a level 0 avoids the addition of error position information to the message.

_G

A global variable (not a function) that holds the global environment (see §2.2). Lua itself does not use this variable; changing its value does not affect any environment, nor vice versa.
getmetatable (object)

If object does not have a metatable, returns nil. Otherwise, if the object's metatable has a "__metatable" field, returns the associated value. Otherwise, returns the metatable of the given object.

ipairs (t)

Returns three values (an iterator function, the table t, and 0) so that the construction

     for i,v in ipairs(t) do body end
will iterate over the key–value pairs (1,t[1]), (2,t[2]), ..., up to the first nil value.

load (chunk [, chunkname [, mode [, env]]])

Loads a chunk.

If chunk is a string, the chunk is this string. If chunk is a function, load calls it repeatedly to get the chunk pieces. Each call to chunk must return a string that concatenates with previous results. A return of an empty string, nil, or no value signals the end of the chunk.

If there are no syntactic errors, returns the compiled chunk as a function; otherwise, returns nil plus the error message.

If the resulting function has upvalues, the first upvalue is set to the value of env, if that parameter is given, or to the value of the global environment. Other upvalues are initialized with nil. (When you load a main chunk, the resulting function will always have exactly one upvalue, the _ENV variable (see §2.2). However, when you load a binary chunk created from a function (see string.dump), the resulting function can have an arbitrary number of upvalues.) All upvalues are fresh, that is, they are not shared with any other function.

chunkname is used as the name of the chunk for error messages and debug information (see §4.9). When absent, it defaults to chunk, if chunk is a string, or to "=(load)" otherwise.

The string mode controls whether the chunk can be text or binary (that is, a precompiled chunk). It may be the string "b" (only binary chunks), "t" (only text chunks), or "bt" (both binary and text). The default is "bt".

Lua does not check the consistency of binary chunks. Maliciously crafted binary chunks can crash the interpreter.

loadfile ([filename [, mode [, env]]])

Similar to load, but gets the chunk from file filename or from the standard input, if no file name is given.

next (table [, index])

Allows a program to traverse all fields of a table. Its first argument is a table and its second argument is an index in this table. next returns the next index of the table and its associated value. When called with nil as its second argument, next returns an initial index and its associated value. When called with the last index, or with nil in an empty table, next returns nil. If the second argument is absent, then it is interpreted as nil. In particular, you can use next(t) to check whether a table is empty.

The order in which the indices are enumerated is not specified, even for numeric indices. (To traverse a table in numerical order, use a numerical for.)

The behavior of next is undefined if, during the traversal, you assign any value to a non-existent field in the table. You may however modify existing fields. In particular, you may clear existing fields.

pairs (t)

If t has a metamethod __pairs, calls it with t as argument and returns the first three results from the call.

Otherwise, returns three values: the next function, the table t, and nil, so that the construction

     for k,v in pairs(t) do body end
will iterate over all key–value pairs of table t.

See function next for the caveats of modifying the table during its traversal.

pcall (f [, arg1, ···])

Calls function f with the given arguments in protected mode. This means that any error inside f is not propagated; instead, pcall catches the error and returns a status code. Its first result is the status code (a boolean), which is true if the call succeeds without errors. In such case, pcall also returns all results from the call, after this first result. In case of any error, pcall returns false plus the error message.

print (···)

Receives any number of arguments and prints their values to stdout, using the tostring function to convert each argument to a string. print is not intended for formatted output, but only as a quick way to show a value, for instance for debugging. For complete control over the output, use string.format and io.write.
rawequal (v1, v2)

Checks whether v1 is equal to v2, without invoking any metamethod. Returns a boolean.
rawget (table, index)

Gets the real value of table[index], without invoking any metamethod. table must be a table; index may be any value.
rawlen (v)

Returns the length of the object v, which must be a table or a string, without invoking any metamethod. Returns an integer.
rawset (table, index, value)

Sets the real value of table[index] to value, without invoking any metamethod. table must be a table, index any value different from nil and NaN, and value any Lua value.
This function returns table.

select (index, ···)

If index is a number, returns all arguments after argument number index; a negative number indexes from the end (-1 is the last argument). Otherwise, index must be the string "#", and select returns the total number of extra arguments it received.

setmetatable (table, metatable)

Sets the metatable for the given table. (To change the metatable of other types from Lua code, you must use the debug library (§6.10).) If metatable is nil, removes the metatable of the given table. If the original metatable has a "__metatable" field, raises an error.

This function returns table.

tonumber (e [, base])

When called with no base, tonumber tries to convert its argument to a number. If the argument is already a number or a string convertible to a number, then tonumber returns this number; otherwise, it returns nil.

The conversion of strings can result in integers or floats, according to the lexical conventions of Lua (see §3.1). (The string may have leading and trailing spaces and a sign.)

When called with base, then e must be a string to be interpreted as an integer numeral in that base. The base may be any integer between 2 and 36, inclusive. In bases above 10, the letter 'A' (in either upper or lower case) represents 10, 'B' represents 11, and so forth, with 'Z' representing 35. If the string e is not a valid numeral in the given base, the function returns nil.

tostring (v)

Receives a value of any type and converts it to a string in a human-readable format. (For complete control of how numbers are converted, use string.format.)
If the metatable of v has a "__tostring" field, then tostring calls the corresponding value with v as argument, and uses the result of the call as its result.

type (v)

Returns the type of its only argument, coded as a string. The possible results of this function are "nil" (a string, not the value nil), "number", "string", "boolean", "table", "function", "thread", and "userdata".
_VERSION

A global variable (not a function) that holds a string containing the running Lua version. The current value of this variable is "Lua 5.3".

xpcall (f, msgh [, arg1, ···])

This function is similar to pcall, except that it sets a new message handler msgh.

