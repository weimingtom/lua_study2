3.3 – Statements

Lua supports an almost conventional set of statements, similar to those in Pascal or C. This set includes assignments, control structures, function calls, and variable declarations.

3.3.1 – Blocks

A block is a list of statements, which are executed sequentially:

	block ::= {stat}
Lua has empty statements that allow you to separate statements with semicolons, start a block with a semicolon or write two semicolons in sequence:

	stat ::= ‘;’
Function calls and assignments can start with an open parenthesis. This possibility leads to an ambiguity in Lua's grammar. Consider the following fragment:

     a = b + c
     (print or io.write)('done')
The grammar could see it in two ways:

     a = b + c(print or io.write)('done')
     
     a = b + c; (print or io.write)('done')
The current parser always sees such constructions in the first way, interpreting the open parenthesis as the start of the arguments to a call. To avoid this ambiguity, it is a good practice to always precede with a semicolon statements that start with a parenthesis:

     ;(print or io.write)('done')
A block can be explicitly delimited to produce a single statement:

	stat ::= do block end
Explicit blocks are useful to control the scope of variable declarations. Explicit blocks are also sometimes used to add a return statement in the middle of another block (see §3.3.4).

3.3.2 – Chunks

The unit of compilation of Lua is called a chunk. Syntactically, a chunk is simply a block:

	chunk ::= block
Lua handles a chunk as the body of an anonymous function with a variable number of arguments (see §3.4.11). As such, chunks can define local variables, receive arguments, and return values. Moreover, such anonymous function is compiled as in the scope of an external local variable called _ENV (see §2.2). The resulting function always has _ENV as its only upvalue, even if it does not use that variable.

A chunk can be stored in a file or in a string inside the host program. To execute a chunk, Lua first loads it, precompiling the chunk's code into instructions for a virtual machine, and then Lua executes the compiled code with an interpreter for the virtual machine.

Chunks can also be precompiled into binary form; see program luac and function string.dump for details. Programs in source and compiled forms are interchangeable; Lua automatically detects the file type and acts accordingly (see load).

3.3.3 – Assignment

Lua allows multiple assignments. Therefore, the syntax for assignment defines a list of variables on the left side and a list of expressions on the right side. The elements in both lists are separated by commas:

	stat ::= varlist ‘=’ explist
	varlist ::= var {‘,’ var}
	explist ::= exp {‘,’ exp}
Expressions are discussed in §3.4.

Before the assignment, the list of values is adjusted to the length of the list of variables. If there are more values than needed, the excess values are thrown away. If there are fewer values than needed, the list is extended with as many nil's as needed. If the list of expressions ends with a function call, then all values returned by that call enter the list of values, before the adjustment (except when the call is enclosed in parentheses; see §3.4).

The assignment statement first evaluates all its expressions and only then the assignments are performed. Thus the code

     i = 3
     i, a[i] = i+1, 20
sets a[3] to 20, without affecting a[4] because the i in a[i] is evaluated (to 3) before it is assigned 4. Similarly, the line

     x, y = y, x
exchanges the values of x and y, and

     x, y, z = y, z, x
cyclically permutes the values of x, y, and z.

The meaning of assignments to global variables and table fields can be changed via metatables. An assignment to an indexed variable t[i] = val is equivalent to settable_event(t,i,val). (See §2.4 for a complete description of the settable_event function. This function is not defined or callable in Lua. We use it here only for explanatory purposes.)

An assignment to a global name x = val is equivalent to the assignment _ENV.x = val (see §2.2).

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

The return statement is used to return values from a function or a chunk (which is an anonymous function). Functions can return more than one value, so the syntax for the return statement is

	stat ::= return [explist] [‘;’]
The return statement can only be written as the last statement of a block. If it is really necessary to return in the middle of a block, then an explicit inner block can be used, as in the idiom do return end, because now return is the last statement in its (inner) block.

3.3.5 – For Statement

The for statement has two forms: one numerical and one generic.

The numerical for loop repeats a block of code while a control variable runs through an arithmetic progression. It has the following syntax:

	stat ::= for Name ‘=’ exp ‘,’ exp [‘,’ exp] do block end
The block is repeated for name starting at the value of the first exp, until it passes the second exp by steps of the third exp. More precisely, a for statement like

     for v = e1, e2, e3 do block end
is equivalent to the code:

     do
       local var, limit, step = tonumber(e1), tonumber(e2), tonumber(e3)
       if not (var and limit and step) then error() end
       var = var - step
       while true do
         var = var + step
         if (step >= 0 and var > limit) or (step < 0 and var < limit) then
           break
         end
         local v = var
         block
       end
     end
Note the following:

All three control expressions are evaluated only once, before the loop starts. They must all result in numbers.
var, limit, and step are invisible variables. The names shown here are for explanatory purposes only.
If the third expression (the step) is absent, then a step of 1 is used.
You can use break and goto to exit a for loop.
The loop variable v is local to the loop body. If you need its value after the loop, assign it to another variable before exiting the loop.
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
In this case, all returned values are thrown away. Function calls are explained in §3.4.10.

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
	exp ::= Numeral
	exp ::= LiteralString
	exp ::= functiondef
	exp ::= tableconstructor
	exp ::= ‘...’
	exp ::= exp binop exp
	exp ::= unop exp
	prefixexp ::= var | functioncall | ‘(’ exp ‘)’
Numerals and literal strings are explained in §3.1; variables are explained in §3.2; function definitions are explained in §3.4.11; function calls are explained in §3.4.10; table constructors are explained in §3.4.9. Vararg expressions, denoted by three dots ('...'), can only be used when directly inside a vararg function; they are explained in §3.4.11.

Binary operators comprise arithmetic operators (see §3.4.1), bitwise operators (see §3.4.2), relational operators (see §3.4.4), logical operators (see §3.4.5), and the concatenation operator (see §3.4.6). Unary operators comprise the unary minus (see §3.4.1), the unary bitwise not (see §3.4.2), the unary logical not (see §3.4.5), and the unary length operator (see §3.4.7).

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

