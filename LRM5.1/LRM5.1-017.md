【翻译】(LRM5.1-17)表操纵(5.5)、数学运算函数(5.6)

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

5.5 - Table Manipulation
This library provides generic functions for table manipulation. It provides all its functions inside the table table. 

Most functions in the table library assume that the table represents an array or a list. For these functions, when we talk about the "length" of a table we mean the result of the length operator. 


（注：TODO）
5.5 - 表操作
该库为表操作处理提供常规函数。它在表table内提供其所有函数。 

表中的多数函数假定给定的表表示数组或列表。对于这些函数，当我们谈到表的“长度”时，我们意指取长操作符的结果。 




--------------------------------------------------------------------------------

table.concat (table [, sep [, i [, j]]])
Given an array where all elements are strings or numbers, returns table[i]..sep..table[i+1] ··· sep..table[j]. The default value for sep is the empty string, the default for i is 1, and the default for j is the length of the table. If i is greater than j, returns the empty string. 

--------------------------------------------------------------------------------
（注：TODO）

table.concat (table [, sep [, i [, j]]])
给定一个数组，其所有元素是字符串或数字，返回table[i]..sep..table[i+1] ··· sep..table[j]。sep的缺省值是空字符串，i的缺省值是1，j的缺省值是表的长度。如果i比j大，返回空字符串。 


--------------------------------------------------------------------------------

table.insert (table, [pos,] value)
Inserts element value at position pos in table, shifting up other elements to open space, if necessary. The default value for pos is n+1, where n is the length of the table (see §2.5.5), so that a call table.insert(t,x) inserts x at the end of table t. 

--------------------------------------------------------------------------------
（注：TODO）

table.insert (table, [pos,] value)
在表table的pos位置插入元素value，如果需要将其他元素上移到开放空间。pos的缺省值是n+1，其中n是表的长度（见§2.5.5），所以调用table.insert(t,x)在表t结尾插入x。 


--------------------------------------------------------------------------------

table.maxn (table)
Returns the largest positive numerical index of the given table, or zero if the table has no positive numerical indices. (To do its job this function does a linear traversal of the whole table.) 

--------------------------------------------------------------------------------
（注：TODO）

table.maxn (table)
返回给定表的最大正数索引，如果没有正数索引则返回0。（该函数执行一次整个表的线性遍历来做这个工作。） 


--------------------------------------------------------------------------------

table.remove (table [, pos])
Removes from table the element at position pos, shifting down other elements to close the space, if necessary. Returns the value of the removed element. The default value for pos is n, where n is the length of the table, so that a call table.remove(t) removes the last element of table t. 

--------------------------------------------------------------------------------
（注：TODO）

table.remove (table [, pos])
从table中删除在位置pos处的元素，如果需要会下移其他元素以缩紧空格。返回被删除的值。pos的缺省值是表的长度n，所以调用table.remove(t)删除表t的最后一个元素。 


--------------------------------------------------------------------------------

table.sort (table [, comp])
Sorts table elements in a given order, in-place, from table[1] to table[n], where n is the length of the table. If comp is given, then it must be a function that receives two table elements, and returns true when the first is less than the second (so that not comp(a[i+1],a[i]) will be true after the sort). If comp is not given, then the standard Lua operator < is used instead. 
The sort algorithm is not stable; that is, elements considered equal by the given order may have their relative positions changed by the sort. 

--------------------------------------------------------------------------------
（注：TODO）

table.sort (table [, comp])
从table[1]到table[n]将表元素排序为给定顺序的适当位置，其中n是表的长度。如果给出了comp，它必须是个函数，接受两个表元素并当第一个小于第二个时返回true（所以排序后not comp(a[i+1],a[i])将为true）。如果未给出comp，则使用标准的Lua操作符<代替。 
排序算法不稳定；就是说，被指定顺序认为相等的元素可能被排序改变相对位置。 










5.6 - Mathematical Functions
This library is an interface to the standard C math library. It provides all its functions inside the table math. 


（注：TODO）

5.6 - 数学运算函数
该库是标准C数学库的接口。它在表math中提供所有函数。 



--------------------------------------------------------------------------------

math.abs (x)
Returns the absolute value of x. 

--------------------------------------------------------------------------------
（注：TODO）

math.abs (x)
返回x的绝对值。 


--------------------------------------------------------------------------------

math.acos (x)
Returns the arc cosine of x (in radians). 

--------------------------------------------------------------------------------
（注：TODO）

math.acos (x)
返回x（弧度）的反余弦值。 


--------------------------------------------------------------------------------

math.asin (x)
Returns the arc sine of x (in radians). 

--------------------------------------------------------------------------------
（注：TODO）

math.asin (x)
返回x（弧度）的反正弦值。 


--------------------------------------------------------------------------------

math.atan (x)
Returns the arc tangent of x (in radians). 

--------------------------------------------------------------------------------
（注：TODO）

math.atan (x)
返回x（弧度）的反正切值。 


--------------------------------------------------------------------------------

math.atan2 (y, x)
Returns the arc tangent of y/x (in radians), but uses the signs of both parameters to find the quadrant of the result. (It also handles correctly the case of x being zero.) 


--------------------------------------------------------------------------------
（注：TODO）

math.atan2 (y, x)
返回y/x（弧度）的反正切值，但用两个参数的正负号找到结果的象限。（它也能正确地处理x为0的情况。） 



--------------------------------------------------------------------------------

math.ceil (x)
Returns the smallest integer larger than or equal to x. 

--------------------------------------------------------------------------------

math.ceil (x)
返回大于等于x的最小整数。


--------------------------------------------------------------------------------

math.cos (x)
Returns the cosine of x (assumed to be in radians). 

--------------------------------------------------------------------------------

math.cos (x)
返回x的余弦（假设以弧度为单位）。


--------------------------------------------------------------------------------

math.cosh (x)
Returns the hyperbolic cosine of x. 

--------------------------------------------------------------------------------

math.cosh (x)
返回x的双曲余弦。


--------------------------------------------------------------------------------

math.deg (x)
Returns the angle x (given in radians) in degrees. 

--------------------------------------------------------------------------------

math.deg (x)
返回角度x（以弧度为单位）为角度值。


--------------------------------------------------------------------------------

math.exp (x)
Returns the value ex. 

--------------------------------------------------------------------------------

math.exp (x)
返回值e^x


--------------------------------------------------------------------------------

math.floor (x)
Returns the largest integer smaller than or equal to x. 

--------------------------------------------------------------------------------

math.floor (x)
返回小于等于x的最大整数。 


--------------------------------------------------------------------------------

math.fmod (x, y)
Returns the remainder of the division of x by y that rounds the quotient towards zero. 

--------------------------------------------------------------------------------

math.fmod (x, y)
Returns the remainder of the division of x by y that rounds the quotient towards zero. 
返回x除于y的余数使商偏向于0。


--------------------------------------------------------------------------------

math.frexp (x)
Returns m and e such that x = m2e, e is an integer and the absolute value of m is in the range [0.5, 1) (or zero when x is zero). 

--------------------------------------------------------------------------------

math.frexp (x)
返回满足x = m*2^e的m和e，e是一个整数且绝对值m在范围[0.5, 1)（或者当x为0时m为0）


--------------------------------------------------------------------------------

math.huge
The value HUGE_VAL, a value larger than or equal to any other numerical value. 

--------------------------------------------------------------------------------

math.huge
值HUGE_VAL，一个大于等于任意数字值。 


--------------------------------------------------------------------------------

math.ldexp (m, e)
Returns m2e (e should be an integer). 

--------------------------------------------------------------------------------

math.ldexp (m, e)
返回m*2^e（e应该为一个整数）


--------------------------------------------------------------------------------

math.log (x)
Returns the natural logarithm of x. 

--------------------------------------------------------------------------------

math.log (x)
返回x的自然对数。


--------------------------------------------------------------------------------

math.log10 (x)
Returns the base-10 logarithm of x. 

--------------------------------------------------------------------------------

math.log10 (x)
返回以10为底x的对数


--------------------------------------------------------------------------------

math.max (x, ···)
Returns the maximum value among its arguments. 

--------------------------------------------------------------------------------

math.max (x, ···)
返回参数中的最大值。


--------------------------------------------------------------------------------

math.min (x, ···)
Returns the minimum value among its arguments. 

--------------------------------------------------------------------------------

math.min (x, ···)
返回参数中的最小值。


--------------------------------------------------------------------------------

math.modf (x)
Returns two numbers, the integral part of x and the fractional part of x. 

--------------------------------------------------------------------------------

math.modf (x)
返回两个数，x的整数部分和x的分数部分。


--------------------------------------------------------------------------------

math.pi
The value of pi. 

--------------------------------------------------------------------------------

math.pi
圆周率的值


--------------------------------------------------------------------------------

math.pow (x, y)
Returns xy. (You can also use the expression x^y to compute this value.) 

--------------------------------------------------------------------------------

math.pow (x, y)
返回x的y次方。（你也可以使用表达式x^y计算这个值）


--------------------------------------------------------------------------------

math.rad (x)
Returns the angle x (given in degrees) in radians. 

--------------------------------------------------------------------------------

math.rad (x)
返回角度x（以度数为单位）的弧度值。


--------------------------------------------------------------------------------

math.random ([m [, n]])
This function is an interface to the simple pseudo-random generator function rand provided by ANSI C. (No guarantees can be given for its statistical properties.) 

When called without arguments, returns a uniform pseudo-random real number in the range [0,1). When called with an integer number m, math.random returns a uniform pseudo-random integer in the range [1, m]. When called with two integer numbers m and n, math.random returns a uniform pseudo-random integer in the range [m, n]. 

--------------------------------------------------------------------------------
（注：TODO）

math.random ([m [, n]])
该函数是ANSI C提供的简单的伪随机产生器函数rand的接口。（不担保其统计特性。） 

当不带参数调用时，返回[0,1)区间内的一致的伪随机实数。当带一个整数m调用时，返回[1, m]区间内的一致的伪随机实数。当带两个整数m和n调用时，返回[m, n]区间内的一致的伪随机实数。 



--------------------------------------------------------------------------------

math.randomseed (x)
Sets x as the "seed" for the pseudo-random generator: equal seeds produce equal sequences of numbers. 


--------------------------------------------------------------------------------

math.randomseed (x)
设置x为伪随机数生成器的“种子”：相同的种子产生相同的数字序列。


--------------------------------------------------------------------------------

math.sin (x)
Returns the sine of x (assumed to be in radians). 

--------------------------------------------------------------------------------

math.sin (x)
返回x的正弦（假设以弧度为单位）


--------------------------------------------------------------------------------

math.sinh (x)
Returns the hyperbolic sine of x. 

--------------------------------------------------------------------------------

math.sinh (x)
返回x的双曲正弦


--------------------------------------------------------------------------------

math.sqrt (x)
Returns the square root of x. (You can also use the expression x^0.5 to compute this value.) 

--------------------------------------------------------------------------------

math.sqrt (x)
返回x的开方根。（你也可以使用表达式x^0.5计算此值）


--------------------------------------------------------------------------------

math.tan (x)
Returns the tangent of x (assumed to be in radians). 

--------------------------------------------------------------------------------

math.tan (x)
返回x的正切（假设以弧度为单位）

--------------------------------------------------------------------------------

math.tanh (x)
Returns the hyperbolic tangent of x. 

--------------------------------------------------------------------------------

math.tanh (x)
返回x的双曲正切。

