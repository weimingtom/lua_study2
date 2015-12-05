## 【翻译】(LRM5.1-4)可见性规则(2.6)、错误处理(2.7)、元表(2.8)  

See also:  
http://www.lua.org/manual/5.1/manual.html  

原文见  
http://www.lua.org/manual/5.1/manual.html  

-----------------------------------------  

## Lua 5.1 Reference Manual   

## Lua 5.1参考手册  

by Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes   

作者：Roberto Ierusalimschy, Luiz Henrique de Figueiredo, Waldemar Celes  

Copyright ? 2006-2008 Lua.org, PUC-Rio. Freely available under the terms of the Lua license.   

版权所有 (c) 2006-2008 Lua.org, PUC-Rio. 根据Lua许可证自由地**（注：免费）**可用  

-----------------------------------------  

## 2.6 - Visibility Rules  

## 2.6 - 可见性规则  

Lua is a lexically scoped language. The scope of variables begins at the first statement after their declaration and lasts until the end of the innermost block that includes the declaration. Consider the following example:   

Lua是一个词法上拥有作用域限制的语言。变量的作用域，开始于其声明后的第一个语句，直到包含声明的最内层block块的结束。考虑下面的例子：  

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

（注：翻译如下：  
x = 10                -- 全局变量  
do                    -- 新的block块  
	local x = x       -- 新变量'x', 值为10  
	print(x)          --> 10  
	x = x+1  
	do                -- 另一个block块  
		local x = x+1 -- 另一个变量 'x'  
		print(x)      --> 12  
	end  
	print(x)          --> 11  
end  
print(x)              --> 10  （那个全局变量x）  
）  

Notice that, in a declaration like local x = x, the new x being declared is not in scope yet, and so the second x refers to the outside variable.   

注意在像local x = x这样的声明中，被定义的新的x还不是在作用域内，所以第二个x引用的是外部变量。  

Because of the lexical scoping rules, local variables can be freely accessed by functions defined inside their scope. A local variable used by an inner function is called an upvalue, or external local variable, inside the inner function.   

因为词法上的作用域规则，局部变量可以被定义在它作用域内的函数自由访问。一个被内部函数使用的局部变量称为upvalue，或者称为内部函数内的外部**（注：导入）**局部变量。  

Notice that each execution of a local statement defines new local variables. Consider the following example:   

注意，每一个local语句的执行定义新的局部变量。考虑下面的例子：  

     a = {}  
     local x = 20  
     for i=1,10 do  
       local y = 0  
       a[i] = function () y=y+1; return x+y end  
     end  

The loop creates ten closures (that is, ten instances of the anonymous function). Each of these closures uses a different y variable, while all of them share the same x.   

循环创建了10个闭包（即10个匿名函数实例）。这个闭包每个都使用**（注：引用）**不同的y变量，但都共享**（注：引用）**相同的x。  

## 2.7 - Error Handling  

## 2.7 - 错误处理   

Because Lua is an embedded extension language, all Lua actions start from C code in the host program calling a function from the Lua library (see lua_pcall). Whenever an error occurs during Lua compilation or execution, control returns to C, which can take appropriate measures (such as printing an error message).   

因为Lua是一个嵌入的扩展语言，Lua的所有操作在宿主程序调用Lua库函数（见lua_pcall）的C代码处开始工作。每当在Lua编译或执行期间发生错误，控制将返回到C，进行适当的处理（如打印错误消息）。  

Lua code can explicitly generate an error by calling the error function. If you need to catch errors in Lua, you can use the pcall function.   

Lua代码可以显式地通过调用error函数产生错误。如果你需要在Lua代码中捕获错误，你可以使用pcall函数。  

## 2.8 - Metatables  

## 2.8 - 元表   

Every value in Lua can have a metatable. This metatable is an ordinary Lua table that defines the behavior of the original value under certain special operations. You can change several aspects of the behavior of operations over a value by setting specific fields in its metatable. For instance, when a non-numeric value is the operand of an addition, Lua checks for a function in the field "__add" in its metatable. If it finds one, Lua calls this function to perform the addition.   

Lua中每个值都可以有一个元表。这个元表是一个普通的Lua表，定义某些特定操作下原始值的行为。你可以通过把元表的特定字段设置为一个值来改变操作行为的某些角度**（注：切面，相位）**例如，当非数值的值是加法的操作数，Lua用它的元表的"__add"字段的函数检查。如果找到，Lua调用这个函数来执行加法。  

We call the keys in a metatable events and the values metamethods. In the previous example, the event is "add" and the metamethod is the function that performs the addition.   

我们调用在元表事件中和在值的元方法中的键。在前一个例子中，事件是"add"而元方法是执行加法的函数。  

You can query the metatable of any value through the getmetatable function.   

你可以通过getmetatable函数查询任意值的元表。  

You can replace the metatable of tables through the setmetatable function. You cannot change the metatable of other types from Lua (except by using the debug library); you must use the C API for that.   

你可以通过setmetatable函数替换表的元表。你不可以在Lua中改变其它类型的元表（除非使用debug库）；你必须使用C API来完成。  

Tables and full userdata have individual metatables (although multiple tables and userdata can share their metatables). Values of all other types share one single metatable per type; that is, there is one single metatable for all numbers, one for all strings, etc.   

表和完全userdata拥有各自的元表（虽然多重表和userdata可以共享它们的元表）。其它所有类型的值对每个类型共享一个单独的元表；即所有数有一个单一元表，所有字符串有一个单一元表，等等。  

A metatable controls how an object behaves in arithmetic operations, order comparisons, concatenation, length operation, and indexing. A metatable also can define a function to be called when a userdata is garbage collected. For each of these operations Lua associates a specific key called an event. When Lua performs one of these operations over a value, it checks whether this value has a metatable with the corresponding event. If so, the value associated with that key (the metamethod) controls how Lua will perform the operation.   

元表控制一个对象在算术操作，书序比较，连接，长度操作和索引时的行为方式。元表还能定义一个函数，当一个userdata被垃圾回收时调用它。Lua把每个操作关联到一个特定的键，称为事件。当Lua用一个值来执行这些操作时，它会检查这个值是否有相应事件的元表。如果有，关联那个键的值（元方法）控制Lua执行该操作的方式。  

Metatables control the operations listed next. Each operation is identified by its corresponding name. The key for each operation is a string with its name prefixed by two underscores, '__'; for instance, the key for operation "add" is the string "__add". The semantics of these operations is better explained by a Lua function describing how the interpreter executes the operation.   

元表控制下面列举的操作。每个操作通过它相应的名字来标识。每个操作的键是一个带有双下划线'__'前缀的名称的字符串。例如，"add"操作的键是字符串"__add"。通过一个描述解释器如何执行操作的Lua函数可以更好地解释这些操作的语义。  

The code shown here in Lua is only illustrative; the real behavior is hard coded in the interpreter and it is much more efficient than this simulation. All functions used in these descriptions (rawget, tonumber, etc.) are described in §5.1. In particular, to retrieve the metamethod of a given object, we use the expression   

这里展示的Lua代码只是为了说明；真实的行为时被硬编码进解释器使之比这个模拟更为有效。这些描述中使用的所有函数（rawget，tonumber，等等）在§5.1中描述。特别地，为了获得给定对象的元方法，我们使用表达式  

     metatable(obj)[event]  

This should be read as   

这个表达式可以读作  

     rawget(getmetatable(obj) or {}, event)  

That is, the access to a metamethod does not invoke other metamethods, and the access to objects with no metatables does not fail (it simply results in nil).   

即对一个元方法的访问不会调用其它元方法，而对不带元表的对象的访问不会失败（只是简单地返回nil）。  

"add": the + operation.   

"add"：加号操作。   

The function getbinhandler below defines how Lua chooses a handler for a binary operation. First, Lua tries the first operand. If its type does not define a handler for the operation, then Lua tries the second operand.   

下面的函数getbinhandler定义Lua如何为一个二元操作选择处理句柄。首先，Lua尝试第一个操作数。如果它的类型没有定义一个操作的处理句柄，那么Lua尝试第二操作数。   

     function getbinhandler (op1, op2, event)  
       return metatable(op1)[event] or metatable(op2)[event]  
     end  

By using this function, the behavior of the op1 + op2 is   

通过使用这个函数，op1 + op2的行为如下   

     function add_event (op1, op2)  
       local o1, o2 = tonumber(op1), tonumber(op2)  
       if o1 and o2 then  -- both operands are numeric?  
         return o1 + o2   -- '+' here is the primitive 'add'  
       else  -- at least one of the operands is not numeric  
         local h = getbinhandler(op1, op2, "__add")  
         if h then  
           -- call the handler with both operands  
           return (h(op1, op2))  
         else  -- no handler available: default behavior  
           error(···)  
         end  
       end  
     end  

（注：翻译如下：  
function add_event (op1, op2)  
	local o1, o2 = tonumber(op1), tonumber(op2)  
	if o1 and o2 then  -- 操作数都是数值吗？  
		return o1 + o2   -- 这里的'+'是原始的'add'  
	else  -- 至少有一个操作数不是数值  
		local h = getbinhandler(op1, op2, "__add")  
		if h then  
			-- 用两个操作数调用处理句柄  
			return (h(op1, op2))  
		else  -- 没有可用的处理句柄：默认行为  
			error(···)  
		end  
	end  
end  
）  

"sub": the - operation. Behavior similar to the "add" operation.   

"sub"：减号操作。行为类似于"add"操作。  

"mul": the * operation. Behavior similar to the "add" operation.   

"mul"：乘号操作。行为类似于"add"操作。  

"div": the / operation. Behavior similar to the "add" operation.   

"div"：除号操作。行为类似于"add"操作。   

"mod": the % operation. Behavior similar to the "add" operation, with the operation o1 - floor(o1/o2)*o2 as the primitive operation.   

"mod"：取模操作。行为类似于"add"操作，以操作o1 - floor(o1/o2)*o2作为原始操作。  

"pow": the ^ (exponentiation) operation. Behavior similar to the "add" operation, with the function pow (from the C math library) as the primitive operation.   

"pow"：乘幂（指数）操作。 行为类似于"add"操作，以函数pow（出自C的数学库）作为原始操作。   

"unm": the unary - operation.   

"unm"：一元负号操作.   

     function unm_event (op)  
       local o = tonumber(op)  
       if o then  -- operand is numeric?  
         return -o  -- '-' here is the primitive 'unm'  
       else  -- the operand is not numeric.  
         -- Try to get a handler from the operand  
         local h = metatable(op).__unm  
         if h then  
           -- call the handler with the operand  
           return (h(op))  
         else  -- no handler available: default behavior  
           error(···)  
         end  
       end  
     end  

（注：翻译如下：  
function unm_event (op)  
	local o = tonumber(op)  
	if o then  -- 操作数是数字吗？  
		return -o  -- 这里的'-'是原始的'unm'  
	else  -- 操作数不是数字  
		-- 尝试从操作数中获得一个处理句柄  
		local h = metatable(op).__unm  
		if h then  
		-- 用操作数调用处理句柄  
			return (h(op))  
		else  -- 没有可用的句柄：默认行为  
			error(···)  
		end  
	end  
end  
）  

"concat": the .. (concatenation) operation.   

"concat": 双点号（拼接）操作.   

     function concat_event (op1, op2)  
       if (type(op1) == "string" or type(op1) == "number") and  
          (type(op2) == "string" or type(op2) == "number") then  
         return op1 .. op2  -- primitive string concatenation  
       else  
         local h = getbinhandler(op1, op2, "__concat")  
         if h then  
           return (h(op1, op2))  
         else  
           error(···)  
         end  
       end  
     end  

（注：翻译如下：  
function concat_event (op1, op2)  
	if (type(op1) == "string" or type(op1) == "number") and  
		(type(op2) == "string" or type(op2) == "number") then  
		return op1 .. op2  -- 原始字符串连接  
	else  
		local h = getbinhandler(op1, op2, "__concat")  
		if h then  
			return (h(op1, op2))  
		else  
			error(···)  
		end  
	end  
end  
 ）  

"len": the # operation.   

"len": 取长度操作。  

     function len_event (op)  
       if type(op) == "string" then  
         return strlen(op)         -- primitive string length  
       elseif type(op) == "table" then  
         return #op                -- primitive table length  
       else  
         local h = metatable(op).__len  
         if h then  
           -- call the handler with the operand  
           return (h(op))  
         else  -- no handler available: default behavior  
           error(···)  
         end  
       end  
     end  

（注：翻译如下：  
function len_event (op)  
	if type(op) == "string" then  
		return strlen(op)         -- 原始字符串长度  
	elseif type(op) == "table" then  
		return #op                -- 原始表长度  
	else  
		local h = metatable(op).__len  
		if h then  
			-- 用操作数调用处理句柄  
			return (h(op))  
		else  -- 没有可用的处理句柄：默认行为  
			error(···)  
		end  
	end  
end
）  

See §2.5.5 for a description of the length of a table.   

见§2.5.5关于表长度的描述。  

"eq": the == operation. The function getcomphandler defines how Lua chooses a metamethod for comparison operators. A metamethod only is selected when both objects being compared have the same type and the same metamethod for the selected operation.   

"eq": 等号操作。函数getcomphandler定义Lua如何为比较操作符选择一个元方法。当相同类型的对象比较并且选择操作具有相同的元方法时才会选择那个元方法。  

     function getcomphandler (op1, op2, event)  
       if type(op1) ~= type(op2) then return nil end  
       local mm1 = metatable(op1)[event]  
       local mm2 = metatable(op2)[event]  
       if mm1 == mm2 then return mm1 else return nil end  
     end  

The "eq" event is defined as follows:   

"eq"事件定义如下：  

     function eq_event (op1, op2)  
       if type(op1) ~= type(op2) then  -- different types?  
         return false   -- different objects   
       end  
       if op1 == op2 then   -- primitive equal?  
         return true   -- objects are equal  
       end  
       -- try metamethod  
       local h = getcomphandler(op1, op2, "__eq")  
       if h then  
         return (h(op1, op2))  
       else  
         return false  
       end  
     end  

（注：翻译如下：  
function eq_event (op1, op2)  
	if type(op1) ~= type(op2) then  -- 是不同类型吗？  
		return false     -- 不同的对象  
	end  
	if op1 == op2 then   -- 是原始相等吗？  
		return true   	 -- 对象相等  
	end  
	-- 尝试元方法  
	local h = getcomphandler(op1, op2, "__eq")  
	if h then  
		return (h(op1, op2))  
	else  
		return false  
	end  
end  
）  

a ~= b is equivalent to not (a == b).   

a ~= b 等效于not (a == b).   

"lt": the < operation.   

"lt": 小于操作。   

     function lt_event (op1, op2)  
       if type(op1) == "number" and type(op2) == "number" then  
         return op1 < op2   -- numeric comparison  
       elseif type(op1) == "string" and type(op2) == "string" then  
         return op1 < op2   -- lexicographic comparison  
       else  
         local h = getcomphandler(op1, op2, "__lt")  
         if h then  
           return (h(op1, op2))  
         else  
           error(···)  
         end  
       end  
     end  

（注：翻译如下：  
function lt_event (op1, op2)  
	if type(op1) == "number" and type(op2) == "number" then  
		return op1 < op2   -- 数值比较  
	elseif type(op1) == "string" and type(op2) == "string" then  
		return op1 < op2   -- 字典序比较  
	else  
		local h = getcomphandler(op1, op2, "__lt")  
		if h then  
			return (h(op1, op2))  
		else  
			error(···)  
		end  
	end  
end  
）  

a > b is equivalent to b < a.   

a > b等效于b < a.   

"le": the <= operation.   

"le": 小于等于操作。  

     function le_event (op1, op2)  
       if type(op1) == "number" and type(op2) == "number" then  
         return op1 <= op2   -- numeric comparison  
       elseif type(op1) == "string" and type(op2) == "string" then  
         return op1 <= op2   -- lexicographic comparison  
       else  
         local h = getcomphandler(op1, op2, "__le")  
         if h then  
           return (h(op1, op2))  
         else  
           h = getcomphandler(op1, op2, "__lt")  
           if h then  
             return not h(op2, op1)  
           else  
             error(···)  
           end  
         end  
       end  
     end  

（注：翻译如下：  
function le_event (op1, op2)  
	if type(op1) == "number" and type(op2) == "number" then  
		return op1 <= op2   -- 数值比较  
	elseif type(op1) == "string" and type(op2) == "string" then  
		return op1 <= op2   -- 字典序比较  
	else  
		local h = getcomphandler(op1, op2, "__le")  
		if h then  
			return (h(op1, op2))  
		else  
			h = getcomphandler(op1, op2, "__lt")  
			if h then  
				return not h(op2, op1)  
			else  
				error(···)  
			end  
		end  
	end  
end  
）  

a >= b is equivalent to b <= a. Note that, in the absence of a "le" metamethod, Lua tries the "lt", assuming that a <= b is equivalent to not (b < a).   

a >= b等效于b <= a。注意，缺少"le"元方法时, Lua尝试"lt"，假设a <= b等效于not (b < a)。  

"index": The indexing access table[key].   

"index"：当索引访问table[key]时被调用。  

     function gettable_event (table, key)  
       local h  
       if type(table) == "table" then  
         local v = rawget(table, key)  
         if v ~= nil then return v end  
         h = metatable(table).__index   
         if h == nil then return nil end  
       else  
         h = metatable(table).__index  
         if h == nil then  
           error(···)  
         end  
       end  
       if type(h) == "function" then  
         return (h(table, key))     -- call the handler  
       else return h[key]           -- or repeat operation on it  
       end  
     end  

（注：翻译如下：  
function gettable_event (table, key)  
	local h  
	if type(table) == "table" then  
		local v = rawget(table, key)  
		if v ~= nil then return v end  
		h = metatable(table).__index  
		if h == nil then return nil end  
	else  
		h = metatable(table).__index  
		if h == nil then  
			error(···)  
		end  
	end  
	if type(h) == "function" then  
		return (h(table, key))     -- 调用处理句柄  
	else return h[key]             -- 或者重复对它操作  
	end  
end  
）  

"newindex": The indexing assignment table[key] = value.   

"newindex"：当索引赋值table[key] = value执行时调用。   

     function settable_event (table, key, value)  
       local h  
       if type(table) == "table" then  
         local v = rawget(table, key)  
         if v ~= nil then rawset(table, key, value); return end  
         h = metatable(table).__newindex  
         if h == nil then rawset(table, key, value); return end  
       else  
         h = metatable(table).__newindex  
         if h == nil then  
           error(···)  
         end  
       end  
       if type(h) == "function" then  
         h(table, key,value)           -- call the handler  
       else h[key] = value             -- or repeat operation on it  
       end  
     end  

（注：翻译如下：  
function settable_event (table, key, value)  
	local h  
	if type(table) == "table" then  
		local v = rawget(table, key)  
		if v ~= nil then rawset(table, key, value); return end  
		h = metatable(table).__newindex  
		if h == nil then rawset(table, key, value); return end  
	else  
		h = metatable(table).__newindex  
		if h == nil then  
			error(···)  
		end  
	end  
	if type(h) == "function" then  
		h(table, key,value)           -- 调用处理句柄  
	else h[key] = value               -- 或者重复对它操作  
	end  
end  
）  

"call": called when Lua calls a value.   

"call": 当Lua调用一个值时被调用。  

     function function_event (func, ...)  
       if type(func) == "function" then  
         return func(...)   -- primitive call  
       else  
         local h = metatable(func).__call  
         if h then  
           return h(func, ...)  
         else  
           error(···)  
         end  
       end  
     end  

（注：翻译如下：  
function function_event (func, ...)  
	if type(func) == "function" then  
		return func(...)   -- 原始调用  
	else  
		local h = metatable(func).__call  
		if h then  
			return h(func, ...)  
		else  
			error(···)  
		end  
	end  
end  
）  

-----------------------------------------  

## 参考自：
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  

2. hshqcn  
http://hshqcn.iteye.com/blog/284901  
