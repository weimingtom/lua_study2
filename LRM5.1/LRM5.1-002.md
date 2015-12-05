﻿【翻译】(LRM5.1-2)语句(2.4)

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

2.4 - Statements

2.4 - 语句 

Lua supports an almost conventional set of statements, similar to those in Pascal or C. This set includes assignments, control structures, function calls, and variable declarations. 

Lua支持一个大多数都很常规的语句集合，类似于在Pascal或C中使用的语句集合。这个集合包括赋值，控制结构，函数调用以及变量声明。

2.4.1 - Chunks

2.4.1 - chunk块（注：隐式块）

The unit of execution of Lua is called a chunk. A chunk is simply a sequence of statements, which are executed sequentially. Each statement can be optionally followed by a semicolon: 

Lua的执行单元称为chunk块。chunk块简单来说就是一连串顺序执行的语句序列，每个语句后面可以可选地跟着一个分号：

	chunk ::= {stat [`;′]}

There are no empty statements and thus ';;' is not legal. 

没有空语句，因此';;'是不合法的。

Lua handles a chunk as the body of an anonymous function with a variable number of arguments (see §2.5.9). As such, chunks can define local variables, receive arguments, and return values. 

Lua把一个块处理成一个带可变数目参数的匿名函数体（见§2.5.9）。因此，块可以定义局部变量，接收参数，以及返回值。

A chunk can be stored in a file or in a string inside the host program. To execute a chunk, Lua first pre-compiles the chunk into instructions for a virtual machine, and then it executes the compiled code with an interpreter for the virtual machine. 

块可以存储在一个文件中或在宿主程序内的字符串中。要执行块，Lua首先会把块预编译成一种虚拟机的指令，然后用带虚拟机的解析器执行编译好的编码（注：即字节码）。

Chunks can also be pre-compiled into binary form; see program luac for details. Programs in source and compiled forms are interchangeable; Lua automatically detects the file type and acts accordingly. 

块也可以被预编译成二进制形式；详见程序luac。源代码形式的程序和编译好的形态是可互换的；Lua会自动检测文件类型并执行相应的处理。

2.4.2 - Blocks

2.4.2 - block块（注：显式块）

A block is a list of statements; syntactically, a block is the same as a chunk: 

一个block块是一列语句；语法上block块与chunk块相同：

	block ::= chunk

A block can be explicitly delimited to produce a single statement: 

一个block块可以被显式划定成一个单独的语句： 

	stat ::= do block end

Explicit blocks are useful to control the scope of variable declarations. Explicit blocks are also sometimes used to add a return or break statement in the middle of another block (see §2.4.4). 

显式block块有利于控制变量声明的作用域。显式block块有时也用于另一个块中添加一个return或break语句（见§2.4.4）。

2.4.3 - Assignment

2.4.3 - 赋值 

Lua allows multiple assignments. Therefore, the syntax for assignment defines a list of variables on the left side and a list of expressions on the right side. The elements in both lists are separated by commas: 

Lua允许多重赋值。因此，赋值语法定义左侧是一列变量，右侧是一列变量。两个队列的元素用逗号分隔：

	stat ::= varlist `=′ explist
	varlist ::= var {`,′ var}
	explist ::= exp {`,′ exp}

Expressions are discussed in §2.5. 

表达式在§2.5中讨论。

Before the assignment, the list of values is adjusted to the length of the list of variables. If there are more values than needed, the excess values are thrown away. If there are fewer values than needed, the list is extended with as many nil's as needed. If the list of expressions ends with a function call, then all values returned by that call enter the list of values, before the adjustment (except when the call is enclosed in parentheses; see §2.5). 

在赋值前，值列表根据变量列表（注：即左值列表）大小调整长度。如果值个数超过所需个数，超出个数的值被丢弃。如果值个数少于所需个数，列表会用所需个数的nil扩充。如果表达式列表以函数调用结束，那么该调用返回的所有值在调整前进入值列表（除非该调用被括号括起，见§2.5）。

The assignment statement first evaluates all its expressions and only then are the assignments performed. Thus the code 

赋值语句首先计算它所有的表达式，然后才执行赋值。因此，代码

     i = 3
     i, a[i] = i+1, 20

sets a[3] to 20, without affecting a[4] because the i in a[i] is evaluated (to 3) before it is assigned 4. Similarly, the line 

把a[3]设置为20，而不是把a[r]设置为20，因为在a[i]中的i在赋值为4之前被计算（为3）。类似地，代码行

     x, y = y, x

exchanges the values of x and y, and 

交换x和y的值，而

     x, y, z = y, z, x

cyclically permutes the values of x, y, and z. 

循环地交换x，y和z的值

The meaning of assignments to global variables and table fields can be changed via metatables. An assignment to an indexed variable t[i] = val is equivalent to settable_event(t,i,val). (See §2.8 for a complete description of the settable_event function. This function is not defined or callable in Lua. We use it here only for explanatory purposes.) 

全局变量的赋值和表字段的赋值含义可以通过元表来改变。一个对带索引变量的赋值t[i] = val等效于settable_event(t,i,val)。
（见§2.8关于settable_event的完整描述。这个函数不是用Lua定义或者在Lua中可访问。我们在这里用它只是便于解释。）

An assignment to a global variable x = val is equivalent to the assignment _env.x = val, which in turn is equivalent to 

对一个全局变量的赋值x = val等效于赋值_env.x = val，还等效于 

     settable_event(_env, "x", val)

where _env is the environment of the running function. (The _env variable is not defined in Lua. We use it here only for explanatory purposes.) 

这里_env是正在运行的函数的环境。（_env变量不是用Lua定义。我们在这里用它只是为了方便解释。）

2.4.4 - Control Structures

2.4.4 - 控制结构 

The control structures if, while, and repeat have the usual meaning and familiar syntax: 

控制结构if，while，和repeat拥有通常和熟悉的语法： 

	stat ::= while exp do block end
	stat ::= repeat block until exp
	stat ::= if exp then block {elseif exp then block} [else block] end

Lua also has a for statement, in two flavors (see §2.4.5). 

Lua还有for语句，有两种（见§2.4.5）。

The condition expression of a control structure can return any value. Both false and nil are considered false. All values different from nil and false are considered true (in particular, the number 0 and the empty string are also true). 

控制结构的条件表达式可以返回任何值。false和nil都被视是false。所有不同于nil和false的值被视为true（特别的，数字0和空字符串也是true）。（注：在C的条件表达式中0被视为false，但在Lua中被视为true）

In the repeat–until loop, the inner block does not end at the until keyword, but only after the condition. So, the condition can refer to local variables declared inside the loop block. 

在repeat-until循环中，内部的block块不是在until关键字结束，而是在该条件判断后结束。因此，条件可以引用循环block块内的当地循环内声明的变量块。

The return statement is used to return values from a function or a chunk (which is just a function). Functions and chunks can return more than one value, and so the syntax for the return statement is 

return语句用于从一个函数或一个chunk块（仅仅是一个函数）中返回值。函数和chunk块可以返回多于一个值，所以return语句的语法是

	stat ::= return [explist]

The break statement is used to terminate the execution of a while, repeat, or for loop, skipping to the next statement after the loop: 

break语句是用来终止了while，repeat，或者for循环，跳到循环之后的下一个语句：

	stat ::= break

A break ends the innermost enclosing loop. 

break结束最内层的闭合循环。

The return and break statements can only be written as the last statement of a block. If it is really necessary to return or break in the middle of a block, then an explicit inner block can be used, as in the idioms do return end and do break end, because now return and break are the last statements in their (inner) blocks. 

return和break语句只可以作为block块的最后语句。如果真的有必要从block块的中间返回或者结束循环，那么可以使用显式的内部block块，即作为习惯用语的do return end以及do break end，因为现在return和break是它们（内部）block块的最后语句。

2.4.5 - For Statement

2.4.5 - for语句

The for statement has two forms: one numeric and one generic. 

for语句有两种形式：数字形式和泛型形式。

The numeric for loop repeats a block of code while a control variable runs through an arithmetic progression. It has the following syntax: 

数字形式的循环当控制变量以算术级数变化时重复执行一段代码块。它具有以下语法：

	stat ::= for Name `=′ exp `,′ exp [`,′ exp] do block end

The block is repeated for name starting at the value of the first exp, until it passes the second exp by steps of the third exp. More precisely, a for statement like 

block块对name以第一个表达式的值开始循环，直至它通过第三个表达式的值的步进值，到达第二个表达式的值。更确切地说，一个for语句像这样：

     for v = e1, e2, e3 do block end

is equivalent to the code: 

等效于这样的代码：

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

注意如下：

All three control expressions are evaluated only once, before the loop starts. They must all result in numbers. 

这三个控制表达式都只会在循环开始之前计算一次。它们必须返回数字。

var, limit, and step are invisible variables. The names shown here are for explanatory purposes only. 

var，limit，和step是不可见变量。这里展示的名字只是为了方便解释。

If the third expression (the step) is absent, then a step of 1 is used. 

如果缺少第三个表达式（step），那么使用步长值1。

You can use break to exit a for loop. 

你可以使用break退出一个for循环。

The loop variable v is local to the loop; you cannot use its value after the for ends or is broken. If you need this value, assign it to another variable before breaking or exiting the loop. 

循环变量v对于循环来说是局部的；你不能再for结束后或者跳出之后使用它的值。如果你需要这个值，在跳出或退出循环之前把它的值赋给另一个变量。

The generic for statement works over functions, called iterators. On each iteration, the iterator function is called to produce a new value, stopping when this new value is nil. The generic for loop has the following syntax: 

泛型for语句工作于被称为迭代器的函数上。在每次迭代时，调用迭代器函数产生新的值，当新的值是nil时停止循环。泛型for循环拥有以下语法：

	stat ::= for namelist in explist do block end
	namelist ::= Name {`,′ Name}

A for statement like 

一个像这样的for语句

     for var_1, ···, var_n in explist do block end

is equivalent to the code: 

等效于代码：

     do
       local f, s, var = explist
       while true do
         local var_1, ···, var_n = f(s, var)
         var = var_1
         if var == nil then break end
         block
       end
     end

Note the following: 

注意如下：

explist is evaluated only once. Its results are an iterator function, a state, and an initial value for the first iterator variable. 

explist只计算一次。其结果是一个迭代函数，一个状态，以及第一个迭代器变量的初始值。

f, s, and var are invisible variables. The names are here for explanatory purposes only. 

f，s，和var是不可见变量。这里的名字只是为了方便解释。

You can use break to exit a for loop. 

你可以使用break退出for循环。

The loop variables var_i are local to the loop; you cannot use their values after the for ends. If you need these values, then assign them to other variables before breaking or exiting the loop. 

循环变量var_i在循环中局部可见，你不能在for循环结束后使用它们的值。如果你需要这些值，那么在跳出或者退出循环之前把它们的值赋给其它变量。

2.4.6 - Function Calls as Statements

2.4.6 - 作为语句的函数调用 

To allow possible side-effects, function calls can be executed as statements: 

为了允许可能的副作用，函数调用可以以语句的形式执行： 

	stat ::= functioncall

In this case, all returned values are thrown away. Function calls are explained in §2.5.8. 

在这种情况下，所有的返回值被丢弃。函数调用在§2.5.8中解释。 

2.4.7 - Local Declarations

2.4.7 - 局部变量声明

Local variables can be declared anywhere inside a block. The declaration can include an initial assignment: 

局部变量可以在block块内的任意位置声明。该声明可以包含一个初始化赋值：

	stat ::= local namelist [`=′ explist]

If present, an initial assignment has the same semantics of a multiple assignment (see §2.4.3). Otherwise, all variables are initialized with nil. 

如果存在，初始化赋值具有和多重赋值相同的语义（见§2.4.3）。否则，所有变量都被初始化为nil。

A chunk is also a block (see §2.4.1), and so local variables can be declared in a chunk outside any explicit block. The scope of such local variables extends until the end of the chunk. 

chunk块也属于block块（见§2.4.1），因此局部变量可以在任何显式block块外的chunk块中声明。这种局部变量的作用域延伸至chunk块结束。

The visibility rules for local variables are explained in §2.6. 

局部变量的可见性规则在§2.6中解释。

-----------------------------------------

参考自：
1. Lua 5.1 参考手册 （云风译）
http://www.codingnow.com/2000/download/lua_manual.html
2. hshqcn
http://hshqcn.iteye.com/blog/284901

（TODO：待修改）
