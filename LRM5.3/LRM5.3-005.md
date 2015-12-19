3.4.1 – Arithmetic Operators

Lua supports the following arithmetic operators:

+: addition
-: subtraction
*: multiplication
/: float division
//: floor division
%: modulo
^: exponentiation
-: unary minus
With the exception of exponentiation and float division, the arithmetic operators work as follows: If both operands are integers, the operation is performed over integers and the result is an integer. Otherwise, if both operands are numbers or strings that can be converted to numbers (see §3.4.3), then they are converted to floats, the operation is performed following the usual rules for floating-point arithmetic (usually the IEEE 754 standard), and the result is a float.

Exponentiation and float division (/) always convert their operands to floats and the result is always a float. Exponentiation uses the ISO C function pow, so that it works for non-integer exponents too.

Floor division (//) is a division that rounds the quotient towards minus infinity, that is, the floor of the division of its operands.

Modulo is defined as the remainder of a division that rounds the quotient towards minus infinity (floor division).

In case of overflows in integer arithmetic, all operations wrap around, according to the usual rules of two-complement arithmetic. (In other words, they return the unique representable integer that is equal modulo 264 to the mathematical result.)

3.4.2 – Bitwise Operators

Lua supports the following bitwise operators:

&: bitwise and
|: bitwise or
~: bitwise exclusive or
>>: right shift
<<: left shift
~: unary bitwise not
All bitwise operations convert its operands to integers (see §3.4.3), operate on all bits of those integers, and result in an integer.

Both right and left shifts fill the vacant bits with zeros. Negative displacements shift to the other direction; displacements with absolute values equal to or higher than the number of bits in an integer result in zero (as all bits are shifted out).

3.4.3 – Coercions and Conversions

Lua provides some automatic conversions between some types and representations at run time. Bitwise operators always convert float operands to integers. Exponentiation and float division always convert integer operands to floats. All other arithmetic operations applied to mixed numbers (integers and floats) convert the integer operand to a float; this is called the usual rule. The C API also converts both integers to floats and floats to integers, as needed. Moreover, string concatenation accepts numbers as arguments, besides strings.

Lua also converts strings to numbers, whenever a number is expected.

In a conversion from integer to float, if the integer value has an exact representation as a float, that is the result. Otherwise, the conversion gets the nearest higher or the nearest lower representable value. This kind of conversion never fails.

The conversion from float to integer checks whether the float has an exact representation as an integer (that is, the float has an integral value and it is in the range of integer representation). If it does, that representation is the result. Otherwise, the conversion fails.

The conversion from strings to numbers goes as follows: First, the string is converted to an integer or a float, following its syntax and the rules of the Lua lexer. (The string may have also leading and trailing spaces and a sign.) Then, the resulting number (float or integer) is converted to the type (float or integer) required by the context (e.g., the operation that forced the conversion).

The conversion from numbers to strings uses a non-specified human-readable format. For complete control over how numbers are converted to strings, use the format function from the string library (see string.format).

3.4.4 – Relational Operators

Lua supports the following relational operators:

==: equality
~=: inequality
<: less than
>: greater than
<=: less or equal
>=: greater or equal
These operators always result in false or true.

Equality (==) first compares the type of its operands. If the types are different, then the result is false. Otherwise, the values of the operands are compared. Strings are compared in the obvious way. Numbers are equal if they denote the same mathematical value.

Tables, userdata, and threads are compared by reference: two objects are considered equal only if they are the same object. Every time you create a new object (a table, userdata, or thread), this new object is different from any previously existing object. Closures with the same reference are always equal. Closures with any detectable difference (different behavior, different definition) are always different.

You can change the way that Lua compares tables and userdata by using the "eq" metamethod (see §2.4).

Equality comparisons do not convert strings to numbers or vice versa. Thus, "0"==0 evaluates to false, and t[0] and t["0"] denote different entries in a table.

The operator ~= is exactly the negation of equality (==).

The order operators work as follows. If both arguments are numbers, then they are compared according to their mathematical values (regardless of their subtypes). Otherwise, if both arguments are strings, then their values are compared according to the current locale. Otherwise, Lua tries to call the "lt" or the "le" metamethod (see §2.4). A comparison a > b is translated to b < a and a >= b is translated to b <= a.

Following the IEEE 754 standard, NaN is considered neither smaller than, nor equal to, nor greater than any value (including itself).

3.4.5 – Logical Operators

The logical operators in Lua are and, or, and not. Like the control structures (see §3.3.4), all logical operators consider both false and nil as false and anything else as true.

The negation operator not always returns false or true. The conjunction operator and returns its first argument if this value is false or nil; otherwise, and returns its second argument. The disjunction operator or returns its first argument if this value is different from nil and false; otherwise, or returns its second argument. Both and and or use short-circuit evaluation; that is, the second operand is evaluated only if necessary. Here are some examples:

     10 or 20            --> 10
     10 or error()       --> 10
     nil or "a"          --> "a"
     nil and 10          --> nil
     false and error()   --> false
     false and nil       --> false
     false or nil        --> nil
     10 and 20           --> 20
(In this manual, --> indicates the result of the preceding expression.)

3.4.6 – Concatenation

The string concatenation operator in Lua is denoted by two dots ('..'). If both operands are strings or numbers, then they are converted to strings according to the rules described in §3.4.3. Otherwise, the __concat metamethod is called (see §2.4).

3.4.7 – The Length Operator

The length operator is denoted by the unary prefix operator #. The length of a string is its number of bytes (that is, the usual meaning of string length when each character is one byte).

A program can modify the behavior of the length operator for any value but strings through the __len metamethod (see §2.4).

Unless a __len metamethod is given, the length of a table t is only defined if the table is a sequence, that is, the set of its positive numeric keys is equal to {1..n} for some non-negative integer n. In that case, n is its length. Note that a table like

     {10, 20, nil, 40}
is not a sequence, because it has the key 4 but does not have the key 3. (So, there is no n such that the set {1..n} is equal to the set of positive numeric keys of that table.) Note, however, that non-numeric keys do not interfere with whether a table is a sequence.

3.4.8 – Precedence

Operator precedence in Lua follows the table below, from lower to higher priority:

     or
     and
     <     >     <=    >=    ~=    ==
     |
     ~
     &
     <<    >>
     ..
     +     -
     *     /     //    %
     unary operators (not   #     -     ~)
     ^
As usual, you can use parentheses to change the precedences of an expression. The concatenation ('..') and exponentiation ('^') operators are right associative. All other binary operators are left associative.

3.4.9 – Table Constructors

Table constructors are expressions that create tables. Every time a constructor is evaluated, a new table is created. A constructor can be used to create an empty table or to create a table and initialize some of its fields. The general syntax for constructors is

	tableconstructor ::= ‘{’ [fieldlist] ‘}’
	fieldlist ::= field {fieldsep field} [fieldsep]
	field ::= ‘[’ exp ‘]’ ‘=’ exp | Name ‘=’ exp | exp
	fieldsep ::= ‘,’ | ‘;’
Each field of the form [exp1] = exp2 adds to the new table an entry with key exp1 and value exp2. A field of the form name = exp is equivalent to ["name"] = exp. Finally, fields of the form exp are equivalent to [i] = exp, where i are consecutive integers starting with 1. Fields in the other formats do not affect this counting. For example,

     a = { [f(1)] = g; "x", "y"; x = 1, f(x), [30] = 23; 45 }
is equivalent to

     do
       local t = {}
       t[f(1)] = g
       t[1] = "x"         -- 1st exp
       t[2] = "y"         -- 2nd exp
       t.x = 1            -- t["x"] = 1
       t[3] = f(x)        -- 3rd exp
       t[30] = 23
       t[4] = 45          -- 4th exp
       a = t
     end
The order of the assignments in a constructor is undefined. (This order would be relevant only when there are repeated keys.)

If the last field in the list has the form exp and the expression is a function call or a vararg expression, then all values returned by this expression enter the list consecutively (see §3.4.10).

The field list can have an optional trailing separator, as a convenience for machine-generated code.

3.4.10 – Function Calls

A function call in Lua has the following syntax:

	functioncall ::= prefixexp args
In a function call, first prefixexp and args are evaluated. If the value of prefixexp has type function, then this function is called with the given arguments. Otherwise, the prefixexp "call" metamethod is called, having as first parameter the value of prefixexp, followed by the original call arguments (see §2.4).

The form

	functioncall ::= prefixexp ‘:’ Name args
can be used to call "methods". A call v:name(args) is syntactic sugar for v.name(v,args), except that v is evaluated only once.

Arguments have the following syntax:

	args ::= ‘(’ [explist] ‘)’
	args ::= tableconstructor
	args ::= LiteralString
All argument expressions are evaluated before the call. A call of the form f{fields} is syntactic sugar for f({fields}); that is, the argument list is a single new table. A call of the form f'string' (or f"string" or f[[string]]) is syntactic sugar for f('string'); that is, the argument list is a single literal string.

A call of the form return functioncall is called a tail call. Lua implements proper tail calls (or proper tail recursion): in a tail call, the called function reuses the stack entry of the calling function. Therefore, there is no limit on the number of nested tail calls that a program can execute. However, a tail call erases any debug information about the calling function. Note that a tail call only happens with a particular syntax, where the return has one single function call as argument; this syntax makes the calling function return exactly the returns of the called function. So, none of the following examples are tail calls:

     return (f(x))        -- results adjusted to 1
     return 2 * f(x)
     return x, f(x)       -- additional results
     f(x); return         -- results discarded
     return x or f(x)     -- results adjusted to 1
     
