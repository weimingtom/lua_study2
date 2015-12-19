3.3.4 – Control Structures

The control structures if, while, and repeat have the usual meaning and familiar syntax:

	stat ::= while exp do block end
	stat ::= repeat block until exp
	stat ::= if exp then block {elseif exp then block} [else block] end
Lua also has a for statement, in two flavors (see §3.3.5).

The condition expression of a control structure can return any value. Both false and nil are considered false. All values different from nil and false are considered true (in particular, the number 0 and the empty string are also true).

In the repeat–until loop, the inner block does not end at the until keyword, but only after the condition. So, the condition can refer to local variables declared inside the loop block.

The goto statement transfers the program control to a label. For syntactical reasons, labels in Lua are considered statements too:

	stat ::= goto Name
	stat ::= label
	label ::= ‘::’ Name ‘::’
A label is visible in the entire block where it is defined, except inside nested blocks where a label with the same name is defined and inside nested functions. A goto may jump to any visible label as long as it does not enter into the scope of a local variable.

Labels and empty statements are called void statements, as they perform no actions.

The break statement terminates the execution of a while, repeat, or for loop, skipping to the next statement after the loop:

	stat ::= break
A break ends the innermost enclosing loop.

The return statement is used to return values from a function or a chunk (which is a function in disguise). Functions can return more than one value, so the syntax for the return statement is

	stat ::= return [explist] [‘;’]
The return statement can only be written as the last statement of a block. If it is really necessary to return in the middle of a block, then an explicit inner block can be used, as in the idiom do return end, because now return is the last statement in its (inner) block.

3.3.5 – For Statement

The for statement has two forms: one numeric and one generic.

The numeric for loop repeats a block of code while a control variable runs through an arithmetic progression. It has the following syntax:

	stat ::= for Name ‘=’ exp ‘,’ exp [‘,’ exp] do block end
The block is repeated for name starting at the value of the first exp, until it passes the second exp by steps of the third exp. More precisely, a for statement like

     for v = e1, e2, e3 do block end
is equivalent to the code:

     do
       local var, limit, step = tonumber(e1), tonumber(e2), tonumber(e3)
       if not (var and limit and step) then error() end
       while (step > 0 and var <= limit) or (step <= 0 and var >= limit) do
         local v = var
         block
         var = var + step
       end
     end
Note the following:

All three control expressions are evaluated only once, before the loop starts. They must all result in numbers.
var, limit, and step are invisible variables. The names shown here are for explanatory purposes only.
If the third expression (the step) is absent, then a step of 1 is used.
You can use break to exit a for loop.
The loop variable v is local to the loop; you cannot use its value after the for ends or is broken. If you need this value, assign it to another variable before breaking or exiting the loop.
The generic for statement works over functions, called iterators. On each iteration, the iterator function is called to produce a new value, stopping when this new value is nil. The generic for loop has the following syntax:

	stat ::= for namelist in explist do block end
	namelist ::= Name {‘,’ Name}
A for statement like

     for var_1, ···, var_n in explist do block end
is equivalent to the code:

     do
       local f, s, var = explist
       while true do
         local var_1, ···, var_n = f(s, var)
         if var_1 == nil then break end
         var = var_1
         block
       end
     end
Note the following:

explist is evaluated only once. Its results are an iterator function, a state, and an initial value for the first iterator variable.
f, s, and var are invisible variables. The names are here for explanatory purposes only.
You can use break to exit a for loop.
The loop variables var_i are local to the loop; you cannot use their values after the for ends. If you need these values, then assign them to other variables before breaking or exiting the loop.
3.3.6 – Function Calls as Statements

To allow possible side-effects, function calls can be executed as statements:

	stat ::= functioncall
In this case, all returned values are thrown away. Function calls are explained in §3.4.9.

3.3.7 – Local Declarations

Local variables can be declared anywhere inside a block. The declaration can include an initial assignment:

	stat ::= local namelist [‘=’ explist]
If present, an initial assignment has the same semantics of a multiple assignment (see §3.3.3). Otherwise, all variables are initialized with nil.

A chunk is also a block (see §3.3.2), and so local variables can be declared in a chunk outside any explicit block.

The visibility rules for local variables are explained in §3.5.

3.4 – Expressions

The basic expressions in Lua are the following:

	exp ::= prefixexp
	exp ::= nil | false | true
	exp ::= Number
	exp ::= String
	exp ::= functiondef
	exp ::= tableconstructor
	exp ::= ‘...’
	exp ::= exp binop exp
	exp ::= unop exp
	prefixexp ::= var | functioncall | ‘(’ exp ‘)’
Numbers and literal strings are explained in §3.1; variables are explained in §3.2; function definitions are explained in §3.4.10; function calls are explained in §3.4.9; table constructors are explained in §3.4.8. Vararg expressions, denoted by three dots ('...'), can only be used when directly inside a vararg function; they are explained in §3.4.10.

Binary operators comprise arithmetic operators (see §3.4.1), relational operators (see §3.4.3), logical operators (see §3.4.4), and the concatenation operator (see §3.4.5). Unary operators comprise the unary minus (see §3.4.1), the unary not (see §3.4.4), and the unary length operator (see §3.4.6).

Both function calls and vararg expressions can result in multiple values. If a function call is used as a statement (see §3.3.6), then its return list is adjusted to zero elements, thus discarding all returned values. If an expression is used as the last (or the only) element of a list of expressions, then no adjustment is made (unless the expression is enclosed in parentheses). In all other contexts, Lua adjusts the result list to one element, either discarding all values except the first one or adding a single nil if there are no values.

Here are some examples:

     f()                -- adjusted to 0 results
     g(f(), x)          -- f() is adjusted to 1 result
     g(x, f())          -- g gets x plus all results from f()
     a,b,c = f(), x     -- f() is adjusted to 1 result (c gets nil)
     a,b = ...          -- a gets the first vararg parameter, b gets
                        -- the second (both a and b can get nil if there
                        -- is no corresponding vararg parameter)
     
     a,b,c = x, f()     -- f() is adjusted to 2 results
     a,b,c = f()        -- f() is adjusted to 3 results
     return f()         -- returns all results from f()
     return ...         -- returns all received vararg parameters
     return x,y,f()     -- returns x, y, and all results from f()
     {f()}              -- creates a list with all results from f()
     {...}              -- creates a list with all vararg parameters
     {f(), nil}         -- f() is adjusted to 1 result
Any expression enclosed in parentheses always results in only one value. Thus, (f(x,y,z)) is always a single value, even if f returns several values. (The value of (f(x,y,z)) is the first value returned by f or nil if f does not return any values.)

3.4.1 – Arithmetic Operators

Lua supports the usual arithmetic operators: the binary + (addition), - (subtraction), * (multiplication), / (division), % (modulo), and ^ (exponentiation); and unary - (mathematical negation). If the operands are numbers, or strings that can be converted to numbers (see §3.4.2), then all operations have the usual meaning. Exponentiation works for any exponent. For instance, x^(-0.5) computes the inverse of the square root of x. Modulo is defined as

     a % b == a - math.floor(a/b)*b
That is, it is the remainder of a division that rounds the quotient towards minus infinity.

3.4.2 – Coercion

Lua provides automatic conversion between string and number values at run time. Any arithmetic operation applied to a string tries to convert this string to a number, following the rules of the Lua lexer. (The string may have leading and trailing spaces and a sign.) Conversely, whenever a number is used where a string is expected, the number is converted to a string, in a reasonable format. For complete control over how numbers are converted to strings, use the format function from the string library (see string.format).

3.4.3 – Relational Operators

The relational operators in Lua are

     ==    ~=    <     >     <=    >=
These operators always result in false or true.

Equality (==) first compares the type of its operands. If the types are different, then the result is false. Otherwise, the values of the operands are compared. Numbers and strings are compared in the usual way. Tables, userdata, and threads are compared by reference: two objects are considered equal only if they are the same object. Every time you create a new object (a table, userdata, or thread), this new object is different from any previously existing object. Closures with the same reference are always equal. Closures with any detectable difference (different behavior, different definition) are always different.

You can change the way that Lua compares tables and userdata by using the "eq" metamethod (see §2.4).

The conversion rules of §3.4.2 do not apply to equality comparisons. Thus, "0"==0 evaluates to false, and t[0] and t["0"] denote different entries in a table.

The operator ~= is exactly the negation of equality (==).

The order operators work as follows. If both arguments are numbers, then they are compared as such. Otherwise, if both arguments are strings, then their values are compared according to the current locale. Otherwise, Lua tries to call the "lt" or the "le" metamethod (see §2.4). A comparison a > b is translated to b < a and a >= b is translated to b <= a.

