【翻译】(LRM5.1-3)表达式(2.5)

See also:
http://www.lua.org/manual/5.1/manual.html

原文见
http://www.lua.org/manual/5.1/manual.html

-----------------------------------------

Lua 5.1 Reference Manual 

Lua 5.1参考手册

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes 

作者：Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes

Copyright ? 2006-2008 Lua.org, PUC-Rio. Freely available under the terms of the Lua license. 

版权所有 (c) 2006-2008 Lua.org, PUC-Rio. 根据Lua许可证自由地（注：免费）可用

-----------------------------------------

2.5 - Expressions

2.5 - 表达式

The basic expressions in Lua are the following: 

Lua基本表达式如下：

	exp ::= prefixexp
	exp ::= nil | false | true
	exp ::= Number
	exp ::= String
	exp ::= function
	exp ::= tableconstructor
	exp ::= `...′
	exp ::= exp binop exp
	exp ::= unop exp
	prefixexp ::= var | functioncall | `(′ exp `)′

Numbers and literal strings are explained in §2.1; variables are explained in §2.3; function definitions are explained in §2.5.9; function calls are explained in §2.5.8; table constructors are explained in §2.5.7. Vararg expressions, denoted by three dots ('...'), can only be used when directly inside a vararg function; they are explained in §2.5.9. 

数和字符串字面值在§2.1中解释；变量在§2.3中解释；函数定义在§2.5.9中解释；函数调用在§2.5.8中解释；表构造式在§2.5.7中解释；变长参数表达式，指三点运算符（'...'），只能直接在变长参数函数中使用；它们在§2.5.9中解释。

Binary operators comprise arithmetic operators (see §2.5.1), relational operators (see §2.5.2), logical operators (see §2.5.3), and the concatenation operator (see §2.5.4). Unary operators comprise the unary minus (see §2.5.1), the unary not (see §2.5.3), and the unary length operator (see §2.5.5). 

二元运算符包括算术运算符（见§2.5.1），关系运算符（见§2.5.2），逻辑运算符（见§2.5.3），一元not（见§2.5.3），以及一元长度操作符（见§2.5.5）。

Both function calls and vararg expressions can result in multiple values. If an expression is used as a statement (only possible for function calls (see §2.4.6)), then its return list is adjusted to zero elements, thus discarding all returned values. If an expression is used as the last (or the only) element of a list of expressions, then no adjustment is made (unless the call is enclosed in parentheses). In all other contexts, Lua adjusts the result list to one element, discarding all values except the first one. 

函数调用和变长参数表达式都可以返回多个值。如果一个表达式用作语句（只可以用于函数调用（见§2.4.6）），那么它的返回列表调整为0个元素，从而丢弃所有返回值。如果一个表达式用于表达式列表的最后（或者唯一）的元素，那么不会进行调整（除非调用被括号括起）。在所有其它场合中，Lua把返回列表调整为一个元素，丢弃第一个值以外的所有值。

Here are some examples: 

这里是一些例子：

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

（翻译如下：
f()                -- 调整为0个结果
g(f(), x)          -- f()调整为1个结果
g(x, f())          -- g获得x和f()的所有结果
a,b,c = f(), x     -- f()调整为1个结果(c获得nil)
a,b = ...          -- a获得第一个变长参数的参数, b获得第二个（如果没有相应的变长参数表的参数，a和b可能获得nil）
a,b,c = x, f()     -- f()调整为2个结果
a,b,c = f()        -- f()调整为3个结果
return f()         -- 返回f()的所有结果
return ...         -- 返回所有接受的变长参数表的参数
return x,y,f()     -- 返回x，y，和所有f()的结果
{f()}              -- 创建一个f()结果的列表。
{...}              -- 创建一个变长参数表的所有参数的列表
{f(), nil}         -- f()被调整为1个结果
）

Any expression enclosed in parentheses always results in only one value. Thus, (f(x,y,z)) is always a single value, even if f returns several values. (The value of (f(x,y,z)) is the first value returned by f or nil if f does not return any values.) 

任意用括号括起的表达式总是返回一个值。因此，(f(x,y,z))总是一个值，即使f返回几个值。（(f(x,y,z))是f返回的第一个值，如果f不返回任何值，则是nil。）

2.5.1 - Arithmetic Operators

2.5.1 - 算术运算符 

Lua supports the usual arithmetic operators: the binary + (addition), - (subtraction), * (multiplication), / (division), % (modulo), and ^ (exponentiation); and unary - (negation). If the operands are numbers, or strings that can be converted to numbers (see §2.2.1), then all operations have the usual meaning. Exponentiation works for any exponent. For instance, x^(-0.5) computes the inverse of the square root of x. Modulo is defined as 

Lua支持普通的算术运算符：二元+（加），-（减），*（乘），/（除），％（取模），和^（指数幂）和一元-（负号）。如果操作数是数字或可转换为数字的字符串（见§2.2.1），那么所有操作拥有普通的含义。幂适用于任何指数。例如x ^（-0.5）计算出x的平方根。取模定义为

     a % b == a - math.floor(a/b)*b

That is, it is the remainder of a division that rounds the quotient towards minus infinity. 

就是说，它是除法的余数，使商靠近负无穷。

2.5.2 - Relational Operators

2.5.2 - 关系运算符 

The relational operators in Lua are 

Lua的关系运算符有

     ==    ~=    <     >     <=    >=

These operators always result in false or true. 

这些运算符总是返回false或者true。

Equality (==) first compares the type of its operands. If the types are different, then the result is false. Otherwise, the values of the operands are compared. Numbers and strings are compared in the usual way. Objects (tables, userdata, threads, and functions) are compared by reference: two objects are considered equal only if they are the same object. Every time you create a new object (a table, userdata, thread, or function), this new object is different from any previously existing object. 

相等（==）首先比较操作数的类型。
如果类型不同，那么结果为false。
否则比较操作数的值。
数字和字符串以通常的方式比较。
对象（表，用户数据，线程和函数）用引用进行比较：两个对象仅在它们引用相同对象时才被视为相等。
每次创建一个新的对象（表，用户数据，线程，或功能），这个新的对象是从任何以前存在的对象不同。 

You can change the way that Lua compares tables and userdata by using the "eq" metamethod (see §2.8). 

你可以通过使用"eq"元方法改变Lua比较表和userdata的方式。

The conversion rules of §2.2.1 do not apply to equality comparisons. Thus, "0"==0 evaluates to false, and t[0] and t["0"] denote different entries in a table. 

§2.2.1的转换规则并不适用于相等比较。因此，"0"==0的计算结果为false，T[0]和T["0"]表示表的不同条目。 

The operator ~= is exactly the negation of equality (==). 

运算符~=是等号（==）的准确否定。

The order operators work as follows. If both arguments are numbers, then they are compared as such. Otherwise, if both arguments are strings, then their values are compared according to the current locale. Otherwise, Lua tries to call the "lt" or the "le" metamethod (see §2.8). A comparison a > b is translated to b < a and a >= b is translated to b <= a. 

顺序运算符工作方式如下。如果参数都为数，它们以原本方式比较。否则，如果参数都是字符串，那么它们的值根据当前的本地环境进行比较。否则，Lua尝试调用"lt"和"le"元方法（见§2.8）比较式a > b被翻译为b < a，而a >= b被翻译为b <= a。

2.5.3 - Logical Operators

2.5.3 - 逻辑运算符 

The logical operators in Lua are and, or, and not. Like the control structures (see §2.4.4), all logical operators consider both false and nil as false and anything else as true. 

Lua的逻辑运算符有and，or，和not。像控制结构那样（见§2.4.4），所有逻辑运算符把false和nil视为false，其它视为true。 

The negation operator not always returns false or true. The conjunction operator and returns its first argument if this value is false or nil; otherwise, and returns its second argument. The disjunction operator or returns its first argument if this value is different from nil and false; otherwise, or returns its second argument. Both and and or use short-cut evaluation; that is, the second operand is evaluated only if necessary. Here are some examples: 

否定运算符不总是返回false或true。如果第一个参数的值是false或nil，组合运算符and会返回这个值；否则，and返回第二个参数。如果第一个参数的值不是nil或false，分离运算符or会返回这个值；否则，or返回第二个参数。and和or使用短路计算；即第二操作数只在必要时计算。这里有一些例子：

     10 or 20            --> 10
     10 or error()       --> 10
     nil or "a"          --> "a"
     nil and 10          --> nil
     false and error()   --> false
     false and nil       --> false
     false or nil        --> nil
     10 and 20           --> 20

(In this manual, --> indicates the result of the preceding expression.) 

（在本手册中，-->表示前面表达式的结果。）（注：同时也是注释的写法）

2.5.4 - Concatenation

2.5.4 - 连接 

The string concatenation operator in Lua is denoted by two dots ('..'). If both operands are strings or numbers, then they are converted to strings according to the rules mentioned in §2.2.1. Otherwise, the "concat" metamethod is called (see §2.8). 

在Lua中字符串连接运算符是指两个点（'..'）。如果两个操作数都是字符串或数，那么它们都会按照§ 2.2.1中提到的规则转换为字符串。否则调用"concat"元方法（见§2.8）。

2.5.5 - The Length Operator

2.5.5 - 长度运算符 

The length operator is denoted by the unary operator #. The length of a string is its number of bytes (that is, the usual meaning of string length when each character is one byte). 

长度运算符是指一元运算符#。字符串的长度是它的字节数（即字符串长度的通常意义，每个字符是一个字节）。 

The length of a table t is defined to be any integer index n such that t[n] is not nil and t[n+1] is nil; moreover, if t[1] is nil, n can be zero. For a regular array, with non-nil values from 1 to a given n, its length is exactly that n, the index of its last value. If the array has "holes" (that is, nil values between other non-nil values), then #t can be any of the indices that directly precedes a nil value (that is, it may consider any such nil value as the end of the array). 

表t的长度是指任何整数索引n使得T[n]不是nil而T[n+1]是nil；而且，如果t[1]是nil，n可以为零。对于常规数组，从1到一个给定的n的元素都是非空值，则其长度正好是n，即它最后的值的索引。如果数组有“洞”（即在其他非空值之间的nil值），则#T可以是在一个非空值前一个值的任何一个索引（即可以把任何一个这种nil值视为数组的结束）。

2.5.6 - Precedence

2.5.6 - 优先级

Operator precedence in Lua follows the table below, from lower to higher priority: 

Lua运算符的优先级从低到高如下表所示：

     or
     and
     <     >     <=    >=    ~=    ==
     ..
     +     -
     *     /     %
     not   #     - (unary)
     ^
（注：
     or（逻辑或）
     and（逻辑与）
     <（小于）     >（大于）     <=（小于等于）    >=（大于等于）    ~=（不等于）    ==（等于）
     ..（拼接）
     +（加）     -（减）
     *（乘）    /（除）     %（取模）
     not（否）   #（取长度）     - （一元）（负号）
     ^（乘幂）
）

As usual, you can use parentheses to change the precedences of an expression. The concatenation ('..') and exponentiation ('^') operators are right associative. All other binary operators are left associative. 

通常你可以使用括号来改变表达式的优先级。连接（'..'）和指数（'^'）操作符为右结合。其他所有的二元运算符为左结合。

2.5.7 - Table Constructors

2.5.7 - 表构造式 

Table constructors are expressions that create tables. Every time a constructor is evaluated, a new table is created. A constructor can be used to create an empty table or to create a table and initialize some of its fields. The general syntax for constructors is 

表构造式是创建表的表达式。每次构造式计算时创建一个新表。构造式可以用于创建一个空表或者创建一个表并初始化它的一些字段。关于构造式的一般语法是

	tableconstructor ::= `{′ [fieldlist] `}′
	fieldlist ::= field {fieldsep field} [fieldsep]
	field ::= `[′ exp `]′ `=′ exp | Name `=′ exp | exp
	fieldsep ::= `,′ | `;′

Each field of the form [exp1] = exp2 adds to the new table an entry with key exp1 and value exp2. A field of the form name = exp is equivalent to ["name"] = exp. Finally, fields of the form exp are equivalent to [i] = exp, where i are consecutive numerical integers, starting with 1. Fields in the other formats do not affect this counting. For example, 

每个以[exp1] = exp2形式的字段以键exp1和值exp2添加到新表中的一个条目中。以name = exp的形式的字段等效于["name"] = exp。最后，exp形式的字段等效于[i] = exp，其中i是连续累加整数，以1开始。其它形式的字段不会影响这个计数。例如，

     a = { [f(1)] = g; "x", "y"; x = 1, f(x), [30] = 23; 45 }

is equivalent to 

等效于

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

（注：翻译如下
do
   local t = {}
   t[f(1)] = g
   t[1] = "x"         -- 第1个表达式
   t[2] = "y"         -- 第2个表达式
   t.x = 1            -- t["x"] = 1
   t[3] = f(x)        -- 第3个表达式
   t[30] = 23
   t[4] = 45          -- 第4个表达式
   a = t
end
）

If the last field in the list has the form exp and the expression is a function call or a vararg expression, then all values returned by this expression enter the list consecutively (see §2.5.8). To avoid this, enclose the function call or the vararg expression in parentheses (see §2.5). 

如果列表最后一个字段的形式exp和表达式是一个函数调用或一个变长参数表达式，则这个表达式返回的所有值连续地加入到列表中（见§2.5.8）。要避免这种情况，请用括号括起函数调用或变长参数表达式（见§2.5）。

The field list can have an optional trailing separator, as a convenience for machine-generated code. 

字段列表可以有一个可选的结尾分隔符，方便机器生成代码。 

2.5.8 - Function Calls

2.5.8 - 函数调用 

A function call in Lua has the following syntax: 

一个Lua函数调用的语法如下：

	functioncall ::= prefixexp args

In a function call, first prefixexp and args are evaluated. If the value of prefixexp has type function, then this function is called with the given arguments. Otherwise, the prefixexp "call" metamethod is called, having as first parameter the value of prefixexp, followed by the original call arguments (see §2.8). 

在一个函数调用中，prefixexp和args首先被计算。如果prefixexp的值是函数类型，那么这个函数使用给定的参数进行调用。否则，prefixexp的"call"元方法被调用，第一个参数是prefixexp的值，然后是原本的调用参数（见§2.8）。

The form 

形式

	functioncall ::= prefixexp `:′ Name args

can be used to call "methods". A call v:name(args) is syntactic sugar for v.name(v,args), except that v is evaluated only once. 

可以用来调用“方法”。调用v:name(args)是v.name(v,args)的语法糖，除了v仅被计算一次。

Arguments have the following syntax: 

参数的语法如下：

	args ::= `(′ [explist] `)′
	args ::= tableconstructor
	args ::= String

All argument expressions are evaluated before the call. A call of the form f{fields} is syntactic sugar for f({fields}); that is, the argument list is a single new table. A call of the form f'string' (or f"string" or f[[string]]) is syntactic sugar for f('string'); that is, the argument list is a single literal string. 

所有参数表达式在调用前计算。调用形式f{fields}是f({fields})的语法糖；其中参数表是一个单一的新表。调用形式f'string'（或f"string"或f[[string]]）是f('string')的语法糖；其中参数表式一个单一的字符串字面值。

As an exception to the free-format syntax of Lua, you cannot put a line break before the '(' in a function call. This restriction avoids some ambiguities in the language. If you write 

作为Lua自由格式语法的例外，你不能在函数调用的'('前插入换行。这个限制避免语言的一些歧义。如果你写

     a = f
     (g).x(a)

Lua would see that as a single statement, a = f(g).x(a). So, if you want two statements, you must add a semi-colon between them. If you actually want to call f, you must remove the line break before (g). 

Lua会把它视为一个单一语句a = f(g).x(a)。所以，如果你希望是两个语句，你必须在它们之间添加分号。如果你真的想调用f，你必须删除(g)前的换行。

A call of the form return functioncall is called a tail call. Lua implements proper tail calls (or proper tail recursion): in a tail call, the called function reuses the stack entry of the calling function. Therefore, there is no limit on the number of nested tail calls that a program can execute. However, a tail call erases any debug information about the calling function. Note that a tail call only happens with a particular syntax, where the return has one single function call as argument; this syntax makes the calling function return exactly the returns of the called function. So, none of the following examples are tail calls: 

调用形式return functioncall被称为尾调用。Lua实现合适的尾调用（或者说尾递归）：在一个尾调用中，被调用的函数重用调用方函数的堆栈入口。因此，一个程序可以执行的嵌套尾调用数目没有限制。然而，尾调用清除任何关于调用方函数的调试信息。注意尾调用只出现在特定语法，其中return只以一个单一函数调用作为参数（注：这里return关键字被看成是函数调用）；这种语法使调用方函数准确地返回被调用函数的返回值。所以，下面的例子都不是尾调用：

     return (f(x))        -- results adjusted to 1
     return 2 * f(x)
     return x, f(x)       -- additional results
     f(x); return         -- results discarded
     return x or f(x)     -- results adjusted to 1

（注：翻译如下：
return (f(x))        -- 结果调整为一个
return 2 * f(x)
return x, f(x)       -- 额外的结果
f(x); return         -- 结果被丢弃
return x or f(x)     -- 结果调整为一个
）

2.5.9 - Function Definitions

2.5.9 - 函数定义 

The syntax for function definition is 

函数定义的语法是

	function ::= function funcbody
	funcbody ::= `(′ [parlist] `)′ block end

The following syntactic sugar simplifies function definitions: 

下面的语法糖简化函数定义：

	stat ::= function funcname funcbody
	stat ::= local function Name funcbody
	funcname ::= Name {`.′ Name} [`:′ Name]

The statement 

语句

     function f () body end

translates to 

翻译为

     f = function () body end

The statement 

语句

     function t.a.b.c.f () body end

translates to 

翻译为

     t.a.b.c.f = function () body end

The statement 

语句

     local function f () body end

translates to 

翻译为

     local f; f = function () body end

not to 

而不是

     local f = function () body end

(This only makes a difference when the body of the function contains references to f.) 

（仅当函数体中包含有f的引用时这两种写法才会产生区别）

A function definition is an executable expression, whose value has type function. When Lua pre-compiles a chunk, all its function bodies are pre-compiled too. Then, whenever Lua executes the function definition, the function is instantiated (or closed). This function instance (or closure) is the final value of the expression. Different instances of the same function can refer to different external local variables and can have different environment tables. 

一个函数定义是一个可执行的表达式，其值拥有function类型。当Lua预编译chunk块时，它的所有函数体都会被预编译。然后，每当执行Lua的函数定义时，函数会被实例化（或被关闭（注：这里的关闭可能是指闭包定义））。这个函数的实例（或闭包）是表达式的最终值。相同函数的不同实例可以引用不同的外部局部变量和拥有不同的环境表。（注：可能指upvalue）

Parameters act as local variables that are initialized with the argument values: 

作为局部变量性质工作的参数使用参数值进行初始化：

	parlist ::= namelist [`,′ `...′] | `...′

When a function is called, the list of arguments is adjusted to the length of the list of parameters, unless the function is a variadic or vararg function, which is indicated by three dots ('...') at the end of its parameter list. A vararg function does not adjust its argument list; instead, it collects all extra arguments and supplies them to the function through a vararg expression, which is also written as three dots. The value of this expression is a list of all actual extra arguments, similar to a function with multiple results. If a vararg expression is used inside another expression or in the middle of a list of expressions, then its return list is adjusted to one element. If the expression is used as the last element of a list of expressions, then no adjustment is made (unless that last expression is enclosed in parentheses). 

当一个函数被调用时，实参列表被调整为形参列表的长度，除非该函数是一个可变参数或在其参数列表结尾用三点('...')表示的变长参数函数。一个变长参数函数不调整其参数列表，相反它收集所有额外参数和并且把它们通过变长参数表达式传递给函数，它们也被写成三点运算符。这个表达式的值是所有额外实参的列表，类似于一个具有多个结果的函数。如果一个变长参数表达式是在另一个表达式或在一个表达式列表中使用，那么它的返回列表被调整为一个元素。如果表达式是作为一个表达式列表的最后一个元素使用，则不进行调整（除非最后一个表达式是用括号括起）。 

As an example, consider the following definitions: 

作为一个例子，考虑以下定义：

     function f(a, b) end
     function g(a, b, ...) end
     function r() return 1,2,3 end

Then, we have the following mapping from arguments to parameters and to the vararg expression: 

然后我们拥有以下实参（argument）到形参（parameter）的映射以及到变长参数表达式的映射。

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

（注：翻译如下：
调用            形参
f(3)             a=3, b=nil
f(3, 4)          a=3, b=4
f(3, 4, 5)       a=3, b=4
f(r(), 10)       a=1, b=10
f(r())           a=1, b=2
g(3)             a=3, b=nil, ... -->  (无对象)
g(3, 4)          a=3, b=4,   ... -->  (无对象)
g(3, 4, 5, 8)    a=3, b=4,   ... -->  5  8
g(5, r())        a=5, b=1,   ... -->  2  3
）

Results are returned using the return statement (see §2.4.4). If control reaches the end of a function without encountering a return statement, then the function returns with no results. 

使用return语句返回结果（见§2.4.4）。如果控制没有遇到一个return语句，就到达函数的结束处，那么函数不以任何值返回。

The colon syntax is used for defining methods, that is, functions that have an implicit extra parameter self. Thus, the statement 

冒号语法用于定义方法，即函数有一个隐含的额外参数self。因此，语句

     function t.a.b.c:f (params) body end

is syntactic sugar for 

是下面写法的语法糖

     t.a.b.c.f = function (self, params) body end

-----------------------------------------

参考自：
1. Lua 5.1 参考手册 （云风译）
http://www.codingnow.com/2000/download/lua_manual.html
2. hshqcn
http://hshqcn.iteye.com/blog/284901

（TODO：待修改）
