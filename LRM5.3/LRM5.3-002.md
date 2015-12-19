2.4 – Metatables and Metamethods

Every value in Lua can have a metatable. This metatable is an ordinary Lua table that defines the behavior of the original value under certain special operations. You can change several aspects of the behavior of operations over a value by setting specific fields in its metatable. For instance, when a non-numeric value is the operand of an addition, Lua checks for a function in the field "__add" of the value's metatable. If it finds one, Lua calls this function to perform the addition.

The keys in a metatable are derived from the event names; the corresponding values are called metamethods. In the previous example, the event is "add" and the metamethod is the function that performs the addition.

You can query the metatable of any value using the getmetatable function.

You can replace the metatable of tables using the setmetatable function. You cannot change the metatable of other types from Lua code (except by using the debug library (§6.10)); you should use the C API for that.

Tables and full userdata have individual metatables (although multiple tables and userdata can share their metatables). Values of all other types share one single metatable per type; that is, there is one single metatable for all numbers, one for all strings, etc. By default, a value has no metatable, but the string library sets a metatable for the string type (see §6.4).

A metatable controls how an object behaves in arithmetic operations, bitwise operations, order comparisons, concatenation, length operation, calls, and indexing. A metatable also can define a function to be called when a userdata or a table is garbage collected (§2.5).

A detailed list of events controlled by metatables is given next. Each operation is identified by its corresponding event name. The key for each event is a string with its name prefixed by two underscores, '__'; for instance, the key for operation "add" is the string "__add". Note that queries for metamethods are always raw; the access to a metamethod does not invoke other metamethods.

For the unary operators (negation, length, and bitwise not), the metamethod is computed and called with a dummy second operand, equal to the first one. This extra operand is only to simplify Lua's internals (by making these operators behave like a binary operation) and may be removed in future versions. (For most uses this extra operand is irrelevant.)

"add": the + operation. If any operand for an addition is not a number (nor a string coercible to a number), Lua will try to call a metamethod. First, Lua will check the first operand (even if it is valid). If that operand does not define a metamethod for the "__add" event, then Lua will check the second operand. If Lua can find a metamethod, it calls the metamethod with the two operands as arguments, and the result of the call (adjusted to one value) is the result of the operation. Otherwise, it raises an error.
"sub": the - operation. Behavior similar to the "add" operation.
"mul": the * operation. Behavior similar to the "add" operation.
"div": the / operation. Behavior similar to the "add" operation.
"mod": the % operation. Behavior similar to the "add" operation.
"pow": the ^ (exponentiation) operation. Behavior similar to the "add" operation.
"unm": the - (unary minus) operation. Behavior similar to the "add" operation.
"idiv": the // (floor division) operation. Behavior similar to the "add" operation.
"band": the & (bitwise and) operation. Behavior similar to the "add" operation, except that Lua will try a metamethod if any operand is neither an integer nor a value coercible to an integer (see §3.4.3).
"bor": the | (bitwise or) operation. Behavior similar to the "band" operation.
"bxor": the ~ (bitwise exclusive or) operation. Behavior similar to the "band" operation.
"bnot": the ~ (bitwise unary not) operation. Behavior similar to the "band" operation.
"shl": the << (bitwise left shift) operation. Behavior similar to the "band" operation.
"shr": the >> (bitwise right shift) operation. Behavior similar to the "band" operation.
"concat": the .. (concatenation) operation. Behavior similar to the "add" operation, except that Lua will try a metamethod if any operand is neither a string nor a number (which is always coercible to a string).
"len": the # (length) operation. If the object is not a string, Lua will try its metamethod. If there is a metamethod, Lua calls it with the object as argument, and the result of the call (always adjusted to one value) is the result of the operation. If there is no metamethod but the object is a table, then Lua uses the table length operation (see §3.4.7). Otherwise, Lua raises an error.
"eq": the == (equal) operation. Behavior similar to the "add" operation, except that Lua will try a metamethod only when the values being compared are either both tables or both full userdata and they are not primitively equal. The result of the call is always converted to a boolean.
"lt": the < (less than) operation. Behavior similar to the "add" operation, except that Lua will try a metamethod only when the values being compared are neither both numbers nor both strings. The result of the call is always converted to a boolean.
"le": the <= (less equal) operation. Unlike other operations, the less-equal operation can use two different events. First, Lua looks for the "__le" metamethod in both operands, like in the "lt" operation. If it cannot find such a metamethod, then it will try the "__lt" event, assuming that a <= b is equivalent to not (b < a). As with the other comparison operators, the result is always a boolean. (This use of the "__lt" event can be removed in future versions; it is also slower than a real "__le" metamethod.)
"index": The indexing access table[key]. This event happens when table is not a table or when key is not present in table. The metamethod is looked up in table.
Despite the name, the metamethod for this event can be either a function or a table. If it is a function, it is called with table and key as arguments. If it is a table, the final result is the result of indexing this table with key. (This indexing is regular, not raw, and therefore can trigger another metamethod.)

"newindex": The indexing assignment table[key] = value. Like the index event, this event happens when table is not a table or when key is not present in table. The metamethod is looked up in table.
Like with indexing, the metamethod for this event can be either a function or a table. If it is a function, it is called with table, key, and value as arguments. If it is a table, Lua does an indexing assignment to this table with the same key and value. (This assignment is regular, not raw, and therefore can trigger another metamethod.)

Whenever there is a "newindex" metamethod, Lua does not perform the primitive assignment. (If necessary, the metamethod itself can call rawset to do the assignment.)

"call": The call operation func(args). This event happens when Lua tries to call a non-function value (that is, func is not a function). The metamethod is looked up in func. If present, the metamethod is called with func as its first argument, followed by the arguments of the original call (args).
It is a good practice to add all needed metamethods to a table before setting it as a metatable of some object. In particular, the "__gc" metamethod works only when this order is followed (see §2.5.1).

2.5 – Garbage Collection

Lua performs automatic memory management. This means that you do not have to worry about allocating memory for new objects or freeing it when the objects are no longer needed. Lua manages memory automatically by running a garbage collector to collect all dead objects (that is, objects that are no longer accessible from Lua). All memory used by Lua is subject to automatic management: strings, tables, userdata, functions, threads, internal structures, etc.

Lua implements an incremental mark-and-sweep collector. It uses two numbers to control its garbage-collection cycles: the garbage-collector pause and the garbage-collector step multiplier. Both use percentage points as units (e.g., a value of 100 means an internal value of 1).

The garbage-collector pause controls how long the collector waits before starting a new cycle. Larger values make the collector less aggressive. Values smaller than 100 mean the collector will not wait to start a new cycle. A value of 200 means that the collector waits for the total memory in use to double before starting a new cycle.

The garbage-collector step multiplier controls the relative speed of the collector relative to memory allocation. Larger values make the collector more aggressive but also increase the size of each incremental step. You should not use values smaller than 100, because they make the collector too slow and can result in the collector never finishing a cycle. The default is 200, which means that the collector runs at "twice" the speed of memory allocation.

If you set the step multiplier to a very large number (larger than 10% of the maximum number of bytes that the program may use), the collector behaves like a stop-the-world collector. If you then set the pause to 200, the collector behaves as in old Lua versions, doing a complete collection every time Lua doubles its memory usage.

You can change these numbers by calling lua_gc in C or collectgarbage in Lua. You can also use these functions to control the collector directly (e.g., stop and restart it).

2.5.1 – Garbage-Collection Metamethods

You can set garbage-collector metamethods for tables and, using the C API, for full userdata (see §2.4). These metamethods are also called finalizers. Finalizers allow you to coordinate Lua's garbage collection with external resource management (such as closing files, network or database connections, or freeing your own memory).

For an object (table or userdata) to be finalized when collected, you must mark it for finalization. You mark an object for finalization when you set its metatable and the metatable has a field indexed by the string "__gc". Note that if you set a metatable without a __gc field and later create that field in the metatable, the object will not be marked for finalization.

When a marked object becomes garbage, it is not collected immediately by the garbage collector. Instead, Lua puts it in a list. After the collection, Lua goes through that list. For each object in the list, it checks the object's __gc metamethod: If it is a function, Lua calls it with the object as its single argument; if the metamethod is not a function, Lua simply ignores it.

At the end of each garbage-collection cycle, the finalizers for objects are called in the reverse order that the objects were marked for finalization, among those collected in that cycle; that is, the first finalizer to be called is the one associated with the object marked last in the program. The execution of each finalizer may occur at any point during the execution of the regular code.

Because the object being collected must still be used by the finalizer, that object (and other objects accessible only through it) must be resurrected by Lua. Usually, this resurrection is transient, and the object memory is freed in the next garbage-collection cycle. However, if the finalizer stores the object in some global place (e.g., a global variable), then the resurrection is permanent. Moreover, if the finalizer marks a finalizing object for finalization again, its finalizer will be called again in the next cycle where the object is unreachable. In any case, the object memory is freed only in a GC cycle where the object is unreachable and not marked for finalization.

When you close a state (see lua_close), Lua calls the finalizers of all objects marked for finalization, following the reverse order that they were marked. If any finalizer marks objects for collection during that phase, these marks have no effect.

2.5.2 – Weak Tables

A weak table is a table whose elements are weak references. A weak reference is ignored by the garbage collector. In other words, if the only references to an object are weak references, then the garbage collector will collect that object.

A weak table can have weak keys, weak values, or both. A table with weak values allows the collection of its values, but prevents the collection of its keys. A table with both weak keys and weak values allows the collection of both keys and values. In any case, if either the key or the value is collected, the whole pair is removed from the table. The weakness of a table is controlled by the __mode field of its metatable. If the __mode field is a string containing the character 'k', the keys in the table are weak. If __mode contains 'v', the values in the table are weak.

A table with weak keys and strong values is also called an ephemeron table. In an ephemeron table, a value is considered reachable only if its key is reachable. In particular, if the only reference to a key comes through its value, the pair is removed.

Any change in the weakness of a table may take effect only at the next collect cycle. In particular, if you change the weakness to a stronger mode, Lua may still collect some items from that table before the change takes effect.

Only objects that have an explicit construction are removed from weak tables. Values, such as numbers and light C functions, are not subject to garbage collection, and therefore are not removed from weak tables (unless their associated values are collected). Although strings are subject to garbage collection, they do not have an explicit construction, and therefore are not removed from weak tables.

Resurrected objects (that is, objects being finalized and objects accessible only through objects being finalized) have a special behavior in weak tables. They are removed from weak values before running their finalizers, but are removed from weak keys only in the next collection after running their finalizers, when such objects are actually freed. This behavior allows the finalizer to access properties associated with the object through weak tables.

If a weak table is among the resurrected objects in a collection cycle, it may not be properly cleared until the next cycle.

