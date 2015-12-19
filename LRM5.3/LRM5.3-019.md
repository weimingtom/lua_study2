6.9 – Operating System Facilities

This library is implemented through table os.

os.clock ()

Returns an approximation of the amount in seconds of CPU time used by the program.

os.date ([format [, time]])

Returns a string or a table containing date and time, formatted according to the given string format.

If the time argument is present, this is the time to be formatted (see the os.time function for a description of this value). Otherwise, date formats the current time.

If format starts with '!', then the date is formatted in Coordinated Universal Time. After this optional character, if format is the string "*t", then date returns a table with the following fields: year, month (1–12), day (1–31), hour (0–23), min (0–59), sec (0–61), wday (weekday, Sunday is 1), yday (day of the year), and isdst (daylight saving flag, a boolean). This last field may be absent if the information is not available.

If format is not "*t", then date returns the date as a string, formatted according to the same rules as the ISO C function strftime.

When called without arguments, date returns a reasonable date and time representation that depends on the host system and on the current locale. (More specifically, os.date() is equivalent to os.date("%c").)

On non-POSIX systems, this function may be not thread safe because of its reliance on C function gmtime and C function localtime.

os.difftime (t2, t1)

Returns the difference, in seconds, from time t1 to time t2 (where the times are values returned by os.time). In POSIX, Windows, and some other systems, this value is exactly t2-t1.

os.execute ([command])

This function is equivalent to the ISO C function system. It passes command to be executed by an operating system shell. Its first result is true if the command terminated successfully, or nil otherwise. After this first result the function returns a string plus a number, as follows:

"exit": the command terminated normally; the following number is the exit status of the command.
"signal": the command was terminated by a signal; the following number is the signal that terminated the command.
When called without a command, os.execute returns a boolean that is true if a shell is available.

os.exit ([code [, close]])

Calls the ISO C function exit to terminate the host program. If code is true, the returned status is EXIT_SUCCESS; if code is false, the returned status is EXIT_FAILURE; if code is a number, the returned status is this number. The default value for code is true.

If the optional second argument close is true, closes the Lua state before exiting.

os.getenv (varname)

Returns the value of the process environment variable varname, or nil if the variable is not defined.

os.remove (filename)

Deletes the file (or empty directory, on POSIX systems) with the given name. If this function fails, it returns nil, plus a string describing the error and the error code.

os.rename (oldname, newname)

Renames file or directory named oldname to newname. If this function fails, it returns nil, plus a string describing the error and the error code.

os.setlocale (locale [, category])

Sets the current locale of the program. locale is a system-dependent string specifying a locale; category is an optional string describing which category to change: "all", "collate", "ctype", "monetary", "numeric", or "time"; the default category is "all". The function returns the name of the new locale, or nil if the request cannot be honored.

If locale is the empty string, the current locale is set to an implementation-defined native locale. If locale is the string "C", the current locale is set to the standard C locale.

When called with nil as the first argument, this function only returns the name of the current locale for the given category.

This function may be not thread safe because of its reliance on C function setlocale.

os.time ([table])

Returns the current time when called without arguments, or a time representing the local date and time specified by the given table. This table must have fields year, month, and day, and may have fields hour (default is 12), min (default is 0), sec (default is 0), and isdst (default is nil). Other fields are ignored. For a description of these fields, see the os.date function.

The values in these fields do not need to be inside their valid ranges. For instance, if sec is -10, it means -10 seconds from the time specified by the other fields; if hour is 1000, it means +1000 hours from the time specified by the other fields.

The returned value is a number, whose meaning depends on your system. In POSIX, Windows, and some other systems, this number counts the number of seconds since some given start time (the "epoch"). In other systems, the meaning is not specified, and the number returned by time can be used only as an argument to os.date and os.difftime.

os.tmpname ()

Returns a string with a file name that can be used for a temporary file. The file must be explicitly opened before its use and explicitly removed when no longer needed.

On POSIX systems, this function also creates a file with that name, to avoid security risks. (Someone else might create the file with wrong permissions in the time between getting the name and creating the file.) You still have to open the file to use it and to remove it (even if you do not use it).

When possible, you may prefer to use io.tmpfile, which automatically removes the file when the program ends.

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

For instance, the expression debug.getinfo(1,"n").name returns a name for the current function, if a reasonable name can be found, and the expression debug.getinfo(print) returns a table with all available information about the print function.

debug.getlocal ([thread,] f, local)

This function returns the name and the value of the local variable with index local of the function at level f of the stack. This function accesses not only explicit local variables, but also parameters, temporaries, etc.

The first parameter or local variable has index 1, and so on, following the order that they are declared in the code, counting only the variables that are active in the current scope of the function. Negative indices refer to vararg parameters; -1 is the first vararg parameter. The function returns nil if there is no variable with the given index, and raises an error when called with a level out of range. (You can call debug.getinfo to check whether the level is valid.)

Variable names starting with '(' (open parenthesis) represent variables with no known names (internal variables such as loop control variables, and variables from chunks saved without debug information).

The parameter f may also be a function. In that case, getlocal returns only the name of function parameters.

debug.getmetatable (value)

Returns the metatable of the given value or nil if it does not have a metatable.

debug.getregistry ()

Returns the registry table (see §4.5).

debug.getupvalue (f, up)

This function returns the name and the value of the upvalue with index up of the function f. The function returns nil if there is no upvalue with the given index.

Variable names starting with '(' (open parenthesis) represent variables with no known names (variables from chunks saved without debug information).

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

