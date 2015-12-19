6.5 – Table Manipulation

This library provides generic functions for table manipulation. It provides all its functions inside the table table.

Remember that, whenever an operation needs the length of a table, the table should be a proper sequence or have a __len metamethod (see §3.4.6). All functions ignore non-numeric keys in tables given as arguments.

For performance reasons, all table accesses (get/set) performed by these functions are raw.

table.concat (list [, sep [, i [, j]]])

Given a list where all elements are strings or numbers, returns the string list[i]..sep..list[i+1] ··· sep..list[j]. The default value for sep is the empty string, the default for i is 1, and the default for j is #list. If i is greater than j, returns the empty string.

table.insert (list, [pos,] value)

Inserts element value at position pos in list, shifting up the elements list[pos], list[pos+1], ···, list[#list]. The default value for pos is #list+1, so that a call table.insert(t,x) inserts x at the end of list t.

table.pack (···)

Returns a new table with all parameters stored into keys 1, 2, etc. and with a field "n" with the total number of parameters. Note that the resulting table may not be a sequence.

table.remove (list [, pos])

Removes from list the element at position pos, returning the value of the removed element. When pos is an integer between 1 and #list, it shifts down the elements list[pos+1], list[pos+2], ···, list[#list] and erases element list[#list]; The index pos can also be 0 when #list is 0, or #list + 1; in those cases, the function erases the element list[pos].

The default value for pos is #list, so that a call table.remove(t) removes the last element of list t.

table.sort (list [, comp])

Sorts list elements in a given order, in-place, from list[1] to list[#list]. If comp is given, then it must be a function that receives two list elements and returns true when the first element must come before the second in the final order (so that not comp(list[i+1],list[i]) will be true after the sort). If comp is not given, then the standard Lua operator < is used instead.

The sort algorithm is not stable; that is, elements considered equal by the given order may have their relative positions changed by the sort.

table.unpack (list [, i [, j]])

Returns the elements from the given table. This function is equivalent to

     return list[i], list[i+1], ···, list[j]
By default, i is 1 and j is #list.

6.6 – Mathematical Functions

This library is an interface to the standard C math library. It provides all its functions inside the table math.

math.abs (x)

Returns the absolute value of x.

math.acos (x)

Returns the arc cosine of x (in radians).

math.asin (x)

Returns the arc sine of x (in radians).

math.atan (x)

Returns the arc tangent of x (in radians).

math.atan2 (y, x)

Returns the arc tangent of y/x (in radians), but uses the signs of both parameters to find the quadrant of the result. (It also handles correctly the case of x being zero.)

math.ceil (x)

Returns the smallest integer larger than or equal to x.

math.cos (x)

Returns the cosine of x (assumed to be in radians).

math.cosh (x)

Returns the hyperbolic cosine of x.

math.deg (x)

Returns the angle x (given in radians) in degrees.

math.exp (x)

Returns the value ex.

math.floor (x)

Returns the largest integer smaller than or equal to x.

math.fmod (x, y)

Returns the remainder of the division of x by y that rounds the quotient towards zero.

math.frexp (x)

Returns m and e such that x = m2e, e is an integer and the absolute value of m is in the range [0.5, 1) (or zero when x is zero).

math.huge

The value HUGE_VAL, a value larger than or equal to any other numerical value.

math.ldexp (m, e)

Returns m2e (e should be an integer).

math.log (x [, base])

Returns the logarithm of x in the given base. The default for base is e (so that the function returns the natural logarithm of x).

math.max (x, ···)

Returns the maximum value among its arguments.

math.min (x, ···)

Returns the minimum value among its arguments.

math.modf (x)

Returns two numbers, the integral part of x and the fractional part of x.

math.pi

The value of π.

math.pow (x, y)

Returns xy. (You can also use the expression x^y to compute this value.)

math.rad (x)

Returns the angle x (given in degrees) in radians.

math.random ([m [, n]])

This function is an interface to the simple pseudo-random generator function rand provided by Standard C. (No guarantees can be given for its statistical properties.)

When called without arguments, returns a uniform pseudo-random real number in the range [0,1). When called with an integer number m, math.random returns a uniform pseudo-random integer in the range [1, m]. When called with two integer numbers m and n, math.random returns a uniform pseudo-random integer in the range [m, n].

math.randomseed (x)

Sets x as the "seed" for the pseudo-random generator: equal seeds produce equal sequences of numbers.

math.sin (x)

Returns the sine of x (assumed to be in radians).

math.sinh (x)

Returns the hyperbolic sine of x.

math.sqrt (x)

Returns the square root of x. (You can also use the expression x^0.5 to compute this value.)

math.tan (x)

Returns the tangent of x (assumed to be in radians).

math.tanh (x)

Returns the hyperbolic tangent of x.

6.7 – Bitwise Operations

This library provides bitwise operations. It provides all its functions inside the table bit32.

Unless otherwise stated, all functions accept numeric arguments in the range (-251,+251); each argument is normalized to the remainder of its division by 232 and truncated to an integer (in some unspecified way), so that its final value falls in the range [0,232 - 1]. Similarly, all results are in the range [0,232 - 1]. Note that bit32.bnot(0) is 0xFFFFFFFF, which is different from -1.

bit32.arshift (x, disp)

Returns the number x shifted disp bits to the right. The number disp may be any representable integer. Negative displacements shift to the left.

This shift operation is what is called arithmetic shift. Vacant bits on the left are filled with copies of the higher bit of x; vacant bits on the right are filled with zeros. In particular, displacements with absolute values higher than 31 result in zero or 0xFFFFFFFF (all original bits are shifted out).

bit32.band (···)

Returns the bitwise and of its operands.

bit32.bnot (x)

Returns the bitwise negation of x. For any integer x, the following identity holds:

     assert(bit32.bnot(x) == (-1 - x) % 2^32)
bit32.bor (···)

Returns the bitwise or of its operands.

bit32.btest (···)

Returns a boolean signaling whether the bitwise and of its operands is different from zero.

bit32.bxor (···)

Returns the bitwise exclusive or of its operands.

bit32.extract (n, field [, width])

Returns the unsigned number formed by the bits field to field + width - 1 from n. Bits are numbered from 0 (least significant) to 31 (most significant). All accessed bits must be in the range [0, 31].

The default for width is 1.

bit32.replace (n, v, field [, width])

Returns a copy of n with the bits field to field + width - 1 replaced by the value v. See bit32.extract for details about field and width.

bit32.lrotate (x, disp)

Returns the number x rotated disp bits to the left. The number disp may be any representable integer.

For any valid displacement, the following identity holds:

     assert(bit32.lrotate(x, disp) == bit32.lrotate(x, disp % 32))
In particular, negative displacements rotate to the right.

bit32.lshift (x, disp)

Returns the number x shifted disp bits to the left. The number disp may be any representable integer. Negative displacements shift to the right. In any direction, vacant bits are filled with zeros. In particular, displacements with absolute values higher than 31 result in zero (all bits are shifted out).

For positive displacements, the following equality holds:

     assert(bit32.lshift(b, disp) == (b * 2^disp) % 2^32)
bit32.rrotate (x, disp)

Returns the number x rotated disp bits to the right. The number disp may be any representable integer.

For any valid displacement, the following identity holds:

     assert(bit32.rrotate(x, disp) == bit32.rrotate(x, disp % 32))
In particular, negative displacements rotate to the left.

bit32.rshift (x, disp)

Returns the number x shifted disp bits to the right. The number disp may be any representable integer. Negative displacements shift to the left. In any direction, vacant bits are filled with zeros. In particular, displacements with absolute values higher than 31 result in zero (all bits are shifted out).

For positive displacements, the following equality holds:

     assert(bit32.rshift(b, disp) == math.floor(b % 2^32 / 2^disp))
This shift operation is what is called logical shift.

6.8 – Input and Output Facilities

The I/O library provides two different styles for file manipulation. The first one uses implicit file descriptors; that is, there are operations to set a default input file and a default output file, and all input/output operations are over these default files. The second style uses explicit file descriptors.

When using implicit file descriptors, all operations are supplied by table io. When using explicit file descriptors, the operation io.open returns a file descriptor and then all operations are supplied as methods of the file descriptor.

The table io also provides three predefined file descriptors with their usual meanings from C: io.stdin, io.stdout, and io.stderr. The I/O library never closes these files.

Unless otherwise stated, all I/O functions return nil on failure (plus an error message as a second result and a system-dependent error code as a third result) and some value different from nil on success. On non-Posix systems, the computation of the error message and error code in case of errors may be not thread safe, because they rely on the global C variable errno.

io.close ([file])

Equivalent to file:close(). Without a file, closes the default output file.

io.flush ()

Equivalent to io.output():flush().

io.input ([file])

When called with a file name, it opens the named file (in text mode), and sets its handle as the default input file. When called with a file handle, it simply sets this file handle as the default input file. When called without parameters, it returns the current default input file.

In case of errors this function raises the error, instead of returning an error code.

io.lines ([filename ···])

Opens the given file name in read mode and returns an iterator function that works like file:lines(···) over the opened file. When the iterator function detects the end of file, it returns nil (to finish the loop) and automatically closes the file.

The call io.lines() (with no file name) is equivalent to io.input():lines(); that is, it iterates over the lines of the default input file. In this case it does not close the file when the loop ends.

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

