2.4 – Metatables and Metamethods

Every value in Lua can have a metatable. This metatable is an ordinary Lua table that defines the behavior of the original value under certain special operations. You can change several aspects of the behavior of operations over a value by setting specific fields in its metatable. For instance, when a non-numeric value is the operand of an addition, Lua checks for a function in the field "__add" of the value's metatable. If it finds one, Lua calls this function to perform the addition.

The keys in a metatable are derived from the event names; the corresponding values are called metamethods. In the previous example, the event is "add" and the metamethod is the function that performs the addition.

You can query the metatable of any value using the getmetatable function.

You can replace the metatable of tables using the setmetatable function. You cannot change the metatable of other types from Lua (except by using the debug library); you must use the C API for that.

Tables and full userdata have individual metatables (although multiple tables and userdata can share their metatables). Values of all other types share one single metatable per type; that is, there is one single metatable for all numbers, one for all strings, etc. By default, a value has no metatable, but the string library sets a metatable for the string type (see §6.4).

A metatable controls how an object behaves in arithmetic operations, order comparisons, concatenation, length operation, and indexing. A metatable also can define a function to be called when a userdata or a table is garbage collected. When Lua performs one of these operations over a value, it checks whether this value has a metatable with the corresponding event. If so, the value associated with that key (the metamethod) controls how Lua will perform the operation.

Metatables control the operations listed next. Each operation is identified by its corresponding name. The key for each operation is a string with its name prefixed by two underscores, '__'; for instance, the key for operation "add" is the string "__add".

The semantics of these operations is better explained by a Lua function describing how the interpreter executes the operation. The code shown here in Lua is only illustrative; the real behavior is hard coded in the interpreter and it is much more efficient than this simulation. All functions used in these descriptions (rawget, tonumber, etc.) are described in §6.1. In particular, to retrieve the metamethod of a given object, we use the expression

     metatable(obj)[event]
This should be read as

     rawget(getmetatable(obj) or {}, event)
This means that the access to a metamethod does not invoke other metamethods, and access to objects with no metatables does not fail (it simply results in nil).

For the unary - and # operators, the metamethod is called with a dummy second argument. This extra argument is only to simplify Lua's internals; it may be removed in future versions and therefore it is not present in the following code. (For most uses this extra argument is irrelevant.)

"add": the + operation.
The function getbinhandler below defines how Lua chooses a handler for a binary operation. First, Lua tries the first operand. If its type does not define a handler for the operation, then Lua tries the second operand.

     function getbinhandler (op1, op2, event)
       return metatable(op1)[event] or metatable(op2)[event]
     end
By using this function, the behavior of the op1 + op2 is

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
"sub": the - operation. Behavior similar to the "add" operation.
"mul": the * operation. Behavior similar to the "add" operation.
"div": the / operation. Behavior similar to the "add" operation.
"mod": the % operation. Behavior similar to the "add" operation, with the operation o1 - floor(o1/o2)*o2 as the primitive operation.
"pow": the ^ (exponentiation) operation. Behavior similar to the "add" operation, with the function pow (from the C math library) as the primitive operation.
"unm": the unary - operation.
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
"concat": the .. (concatenation) operation.
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
"len": the # operation.
     function len_event (op)
       if type(op) == "string" then
         return strlen(op)      -- primitive string length
       else
         local h = metatable(op).__len
         if h then
           return (h(op))       -- call handler with the operand
         elseif type(op) == "table" then
           return #op              -- primitive table length
         else  -- no handler available: error
           error(···)
         end
       end
     end
See §3.4.6 for a description of the length of a table.

"eq": the == operation. The function getequalhandler defines how Lua chooses a metamethod for equality. A metamethod is selected only when both values being compared have the same type and the same metamethod for the selected operation, and the values are either tables or full userdata.
     function getequalhandler (op1, op2)
       if type(op1) ~= type(op2) or
          (type(op1) ~= "table" and type(op1) ~= "userdata") then
         return nil     -- different values
       end
       local mm1 = metatable(op1).__eq
       local mm2 = metatable(op2).__eq
       if mm1 == mm2 then return mm1 else return nil end
     end
The "eq" event is defined as follows:

     function eq_event (op1, op2)
       if op1 == op2 then   -- primitive equal?
         return true   -- values are equal
       end
       -- try metamethod
       local h = getequalhandler(op1, op2)
       if h then
         return not not h(op1, op2)
       else
         return false
       end
     end
Note that the result is always a boolean.

"lt": the < operation.
     function lt_event (op1, op2)
       if type(op1) == "number" and type(op2) == "number" then
         return op1 < op2   -- numeric comparison
       elseif type(op1) == "string" and type(op2) == "string" then
         return op1 < op2   -- lexicographic comparison
       else
         local h = getbinhandler(op1, op2, "__lt")
         if h then
           return not not h(op1, op2)
         else
           error(···)
         end
       end
     end
Note that the result is always a boolean.

"le": the <= operation.
     function le_event (op1, op2)
       if type(op1) == "number" and type(op2) == "number" then
         return op1 <= op2   -- numeric comparison
       elseif type(op1) == "string" and type(op2) == "string" then
         return op1 <= op2   -- lexicographic comparison
       else
         local h = getbinhandler(op1, op2, "__le")
         if h then
           return not not h(op1, op2)
         else
           h = getbinhandler(op1, op2, "__lt")
           if h then
             return not h(op2, op1)
           else
             error(···)
           end
         end
       end
     end
Note that, in the absence of a "le" metamethod, Lua tries the "lt", assuming that a <= b is equivalent to not (b < a).

As with the other comparison operators, the result is always a boolean.

"index": The indexing access table[key]. Note that the metamethod is tried only when key is not present in table. (When table is not a table, no key is ever present, so the metamethod is always tried.)
     function gettable_event (table, key)
       local h
       if type(table) == "table" then
         local v = rawget(table, key)
         -- if key is present, return raw value
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
"newindex": The indexing assignment table[key] = value. Note that the metamethod is tried only when key is not present in table.
     function settable_event (table, key, value)
       local h
       if type(table) == "table" then
         local v = rawget(table, key)
         -- if key is present, do raw assignment
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
"call": called when Lua calls a value.
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
