6.6 – Table Manipulation

This library provides generic functions for table manipulation. It provides all its functions inside the table table.

Remember that, whenever an operation needs the length of a table, the table must be a proper sequence or have a __len metamethod (see §3.4.7). All functions ignore non-numeric keys in the tables given as arguments.

table.concat (list [, sep [, i [, j]]])

Given a list where all elements are strings or numbers, returns the string list[i]..sep..list[i+1] ··· sep..list[j]. The default value for sep is the empty string, the default for i is 1, and the default for j is #list. If i is greater than j, returns the empty string.

table.insert (list, [pos,] value)

Inserts element value at position pos in list, shifting up the elements list[pos], list[pos+1], ···, list[#list]. The default value for pos is #list+1, so that a call table.insert(t,x) inserts x at the end of list t.

table.move (a1, f, e, t [,a2])

Moves elements from table a1 to table a2. This function performs the equivalent to the following multiple assignment: a2[t],··· = a1[f],···,a1[e]. The default for a2 is a1. The destination range can overlap with the source range. The number of elements to be moved must fit in a Lua integer.

table.pack (···)

Returns a new table with all parameters stored into keys 1, 2, etc. and with a field "n" with the total number of parameters. Note that the resulting table may not be a sequence.

table.remove (list [, pos])

Removes from list the element at position pos, returning the value of the removed element. When pos is an integer between 1 and #list, it shifts down the elements list[pos+1], list[pos+2], ···, list[#list] and erases element list[#list]; The index pos can also be 0 when #list is 0, or #list + 1; in those cases, the function erases the element list[pos].

The default value for pos is #list, so that a call table.remove(l) removes the last element of list l.

table.sort (list [, comp])

Sorts list elements in a given order, in-place, from list[1] to list[#list]. If comp is given, then it must be a function that receives two list elements and returns true when the first element must come before the second in the final order (so that, after the sort, i < j implies not comp(list[j],list[i])). If comp is not given, then the standard Lua operator < is used instead.

Note that the comp function must define a strict partial order over the elements in the list; that is, it must be asymmetric and transitive. Otherwise, no valid sort may be possible.

The sort algorithm is not stable; that is, elements not comparable by the given order (e.g., equal elements) may have their relative positions changed by the sort.

table.unpack (list [, i [, j]])

Returns the elements from the given list. This function is equivalent to

     return list[i], list[i+1], ···, list[j]
By default, i is 1 and j is #list.

6.7 – Mathematical Functions

This library provides basic mathematical functions. It provides all its functions and constants inside the table math. Functions with the annotation "integer/float" give integer results for integer arguments and float results for float (or mixed) arguments. Rounding functions (math.ceil, math.floor, and math.modf) return an integer when the result fits in the range of an integer, or a float otherwise.

math.abs (x)

Returns the absolute value of x. (integer/float)

math.acos (x)

Returns the arc cosine of x (in radians).

math.asin (x)

Returns the arc sine of x (in radians).

math.atan (y [, x])

Returns the arc tangent of y/x (in radians), but uses the signs of both parameters to find the quadrant of the result. (It also handles correctly the case of x being zero.)

The default value for x is 1, so that the call math.atan(y) returns the arc tangent of y.

math.ceil (x)

Returns the smallest integral value larger than or equal to x.

math.cos (x)

Returns the cosine of x (assumed to be in radians).

math.deg (x)

Converts the angle x from radians to degrees.

math.exp (x)

Returns the value ex (where e is the base of natural logarithms).

math.floor (x)

Returns the largest integral value smaller than or equal to x.

math.fmod (x, y)

Returns the remainder of the division of x by y that rounds the quotient towards zero. (integer/float)

math.huge

The float value HUGE_VAL, a value larger than any other numeric value.

math.log (x [, base])

Returns the logarithm of x in the given base. The default for base is e (so that the function returns the natural logarithm of x).

math.max (x, ···)

Returns the argument with the maximum value, according to the Lua operator <. (integer/float)

math.maxinteger

An integer with the maximum value for an integer.
math.min (x, ···)

Returns the argument with the minimum value, according to the Lua operator <. (integer/float)

math.mininteger

An integer with the minimum value for an integer.
math.modf (x)

Returns the integral part of x and the fractional part of x. Its second result is always a float.

math.pi

The value of π.

math.rad (x)

Converts the angle x from degrees to radians.

math.random ([m [, n]])

When called without arguments, returns a pseudo-random float with uniform distribution in the range [0,1). When called with two integers m and n, math.random returns a pseudo-random integer with uniform distribution in the range [m, n]. (The value n-m cannot be negative and must fit in a Lua integer.) The call math.random(n) is equivalent to math.random(1,n).

This function is an interface to the underling pseudo-random generator function provided by C.

math.randomseed (x)

Sets x as the "seed" for the pseudo-random generator: equal seeds produce equal sequences of numbers.

math.sin (x)

Returns the sine of x (assumed to be in radians).

math.sqrt (x)

Returns the square root of x. (You can also use the expression x^0.5 to compute this value.)

math.tan (x)

Returns the tangent of x (assumed to be in radians).

math.tointeger (x)

If the value x is convertible to an integer, returns that integer. Otherwise, returns nil.

math.type (x)

Returns "integer" if x is an integer, "float" if it is a float, or nil if x is not a number.

math.ult (m, n)

Returns a boolean, true if integer m is below integer n when they are compared as unsigned integers.

6.8 – Input and Output Facilities

The I/O library provides two different styles for file manipulation. The first one uses implicit file handles; that is, there are operations to set a default input file and a default output file, and all input/output operations are over these default files. The second style uses explicit file handles.

When using implicit file handles, all operations are supplied by table io. When using explicit file handles, the operation io.open returns a file handle and then all operations are supplied as methods of the file handle.

The table io also provides three predefined file handles with their usual meanings from C: io.stdin, io.stdout, and io.stderr. The I/O library never closes these files.

Unless otherwise stated, all I/O functions return nil on failure (plus an error message as a second result and a system-dependent error code as a third result) and some value different from nil on success. On non-POSIX systems, the computation of the error message and error code in case of errors may be not thread safe, because they rely on the global C variable errno.

io.close ([file])

Equivalent to file:close(). Without a file, closes the default output file.

io.flush ()

Equivalent to io.output():flush().

io.input ([file])

When called with a file name, it opens the named file (in text mode), and sets its handle as the default input file. When called with a file handle, it simply sets this file handle as the default input file. When called without parameters, it returns the current default input file.

In case of errors this function raises the error, instead of returning an error code.

io.lines ([filename, ···])

Opens the given file name in read mode and returns an iterator function that works like file:lines(···) over the opened file. When the iterator function detects the end of file, it returns no values (to finish the loop) and automatically closes the file.

The call io.lines() (with no file name) is equivalent to io.input():lines("*l"); that is, it iterates over the lines of the default input file. In this case it does not close the file when the loop ends.

In case of errors this function raises the error, instead of returning an error code.

io.open (filename [, mode])

This function opens a file, in the mode specified in the string mode. It returns a new file handle, or, in case of errors, nil plus an error message.

The mode string can be any of the following:

"r": read mode (the default);
"w": write mode;
"a": append mode;
"r+": update mode, all previous data is preserved;
"w+": update mode, all previous data is erased;
"a+": append update mode, previous data is preserved, writing is only allowed at the end of file.
The mode string can also have a 'b' at the end, which is needed in some systems to open the file in binary mode.

io.output ([file])

Similar to io.input, but operates over the default output file.

io.popen (prog [, mode])

This function is system dependent and is not available on all platforms.

Starts program prog in a separated process and returns a file handle that you can use to read data from this program (if mode is "r", the default) or to write data to this program (if mode is "w").

io.read (···)

Equivalent to io.input():read(···).

io.tmpfile ()

Returns a handle for a temporary file. This file is opened in update mode and it is automatically removed when the program ends.

io.type (obj)

Checks whether obj is a valid file handle. Returns the string "file" if obj is an open file handle, "closed file" if obj is a closed file handle, or nil if obj is not a file handle.

io.write (···)

Equivalent to io.output():write(···).

file:close ()

Closes file. Note that files are automatically closed when their handles are garbage collected, but that takes an unpredictable amount of time to happen.

When closing a file handle created with io.popen, file:close returns the same values returned by os.execute.

file:flush ()

Saves any written data to file.

file:lines (···)

Returns an iterator function that, each time it is called, reads the file according to the given formats. When no format is given, uses "l" as a default. As an example, the construction

     for c in file:lines(1) do body end
will iterate over all characters of the file, starting at the current position. Unlike io.lines, this function does not close the file when the loop ends.

In case of errors this function raises the error, instead of returning an error code.

file:read (···)

Reads the file file, according to the given formats, which specify what to read. For each format, the function returns a string or a number with the characters read, or nil if it cannot read data with the specified format. (In this latter case, the function does not read subsequent formats.) When called without formats, it uses a default format that reads the next line (see below).

The available formats are

"n": reads a numeral and returns it as a float or an integer, following the lexical conventions of Lua. (The numeral may have leading spaces and a sign.) This format always reads the longest input sequence that is a valid prefix for a numeral; if that prefix does not form a valid numeral (e.g., an empty string, "0x", or "3.4e-"), it is discarded and the function returns nil.
"a": reads the whole file, starting at the current position. On end of file, it returns the empty string.
"l": reads the next line skipping the end of line, returning nil on end of file. This is the default format.
"L": reads the next line keeping the end-of-line character (if present), returning nil on end of file.
number: reads a string with up to this number of bytes, returning nil on end of file. If number is zero, it reads nothing and returns an empty string, or nil on end of file.
The formats "l" and "L" should be used only for text files.

file:seek ([whence [, offset]])

Sets and gets the file position, measured from the beginning of the file, to the position given by offset plus a base specified by the string whence, as follows:

"set": base is position 0 (beginning of the file);
"cur": base is current position;
"end": base is end of file;
In case of success, seek returns the final file position, measured in bytes from the beginning of the file. If seek fails, it returns nil, plus a string describing the error.

The default value for whence is "cur", and for offset is 0. Therefore, the call file:seek() returns the current file position, without changing it; the call file:seek("set") sets the position to the beginning of the file (and returns 0); and the call file:seek("end") sets the position to the end of the file, and returns its size.

file:setvbuf (mode [, size])

Sets the buffering mode for an output file. There are three available modes:

"no": no buffering; the result of any output operation appears immediately.
"full": full buffering; output operation is performed only when the buffer is full or when you explicitly flush the file (see io.flush).
"line": line buffering; output is buffered until a newline is output or there is any input from some special files (such as a terminal device).
For the last two cases, size specifies the size of the buffer, in bytes. The default is an appropriate size.

file:write (···)

Writes the value of each of its arguments to file. The arguments must be strings or numbers.

In case of success, this function returns file. Otherwise it returns nil plus a string describing the error.

