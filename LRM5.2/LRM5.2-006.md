3.4.4 – Logical Operators

The logical operators in Lua are and, or, and not. Like the control structures (see §3.3.4), all logical operators consider both false and nil as false and anything else as true.

The negation operator not always returns false or true. The conjunction operator and returns its first argument if this value is false or nil; otherwise, and returns its second argument. The disjunction operator or returns its first argument if this value is different from nil and false; otherwise, or returns its second argument. Both and and or use short-cut evaluation; that is, the second operand is evaluated only if necessary. Here are some examples:

     10 or 20            --> 10
     10 or error()       --> 10
     nil or "a"          --> "a"
     nil and 10          --> nil
     false and error()   --> false
     false and nil       --> false
     false or nil        --> nil
     10 and 20           --> 20
(In this manual, --> indicates the result of the preceding expression.)

3.4.5 – Concatenation

The string concatenation operator in Lua is denoted by two dots ('..'). If both operands are strings or numbers, then they are converted to strings according to the rules mentioned in §3.4.2. Otherwise, the __concat metamethod is called (see §2.4).

3.4.6 – The Length Operator

The length operator is denoted by the unary prefix operator #. The length of a string is its number of bytes (that is, the usual meaning of string length when each character is one byte).

A program can modify the behavior of the length operator for any value but strings through the __len metamethod (see §2.4).

Unless a __len metamethod is given, the length of a table t is only defined if the table is a sequence, that is, the set of its positive numeric keys is equal to {1..n} for some non-negative integer n. In that case, n is its length. Note that a table like

     {10, 20, nil, 40}
is not a sequence, because it has the key 4 but does not have the key 3. (So, there is no n such that the set {1..n} is equal to the set of positive numeric keys of that table.) Note, however, that non-numeric keys do not interfere with whether a table is a sequence.

3.4.7 – Precedence

Operator precedence in Lua follows the table below, from lower to higher priority:

     or
     and
     <     >     <=    >=    ~=    ==
     ..
     +     -
     *     /     %
     not   #     - (unary)
     ^
As usual, you can use parentheses to change the precedences of an expression. The concatenation ('..') and exponentiation ('^') operators are right associative. All other binary operators are left associative.

3.4.8 – Table Constructors

Table constructors are expressions that create tables. Every time a constructor is evaluated, a new table is created. A constructor can be used to create an empty table or to create a table and initialize some of its fields. The general syntax for constructors is

	tableconstructor ::= ‘{’ [fieldlist] ‘}’
	fieldlist ::= field {fieldsep field} [fieldsep]
	field ::= ‘[’ exp ‘]’ ‘=’ exp | Name ‘=’ exp | exp
	fieldsep ::= ‘,’ | ‘;’
Each field of the form [exp1] = exp2 adds to the new table an entry with key exp1 and value exp2. A field of the form name = exp is equivalent to ["name"] = exp. Finally, fields of the form exp are equivalent to [i] = exp, where i are consecutive numerical integers, starting with 1. Fields in the other formats do not affect this counting. For example,

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
If the last field in the list has the form exp and the expression is a function call or a vararg expression, then all values returned by this expression enter the list consecutively (see §3.4.9).

The field list can have an optional trailing separator, as a convenience for machine-generated code.

3.4.9 – Function Calls

A function call in Lua has the following syntax:

	functioncall ::= prefixexp args
In a function call, first prefixexp and args are evaluated. If the value of prefixexp has type function, then this function is called with the given arguments. Otherwise, the prefixexp "call" metamethod is called, having as first parameter the value of prefixexp, followed by the original call arguments (see §2.4).

The form

	functioncall ::= prefixexp ‘:’ Name args
can be used to call "methods". A call v:name(args) is syntactic sugar for v.name(v,args), except that v is evaluated only once.

Arguments have the following syntax:

	args ::= ‘(’ [explist] ‘)’
	args ::= tableconstructor
	args ::= String
All argument expressions are evaluated before the call. A call of the form f{fields} is syntactic sugar for f({fields}); that is, the argument list is a single new table. A call of the form f'string' (or f"string" or f[[string]]) is syntactic sugar for f('string'); that is, the argument list is a single literal string.

A call of the form return functioncall is called a tail call. Lua implements proper tail calls (or proper tail recursion): in a tail call, the called function reuses the stack entry of the calling function. Therefore, there is no limit on the number of nested tail calls that a program can execute. However, a tail call erases any debug information about the calling function. Note that a tail call only happens with a particular syntax, where the return has one single function call as argument; this syntax makes the calling function return exactly the returns of the called function. So, none of the following examples are tail calls:

     return (f(x))        -- results adjusted to 1
     return 2 * f(x)
     return x, f(x)       -- additional results
     f(x); return         -- results discarded
     return x or f(x)     -- results adjusted to 1
     
     
3.4.10 – Function Definitions

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


