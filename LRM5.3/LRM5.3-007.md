4.7 – Handling Yields in C

Internally, Lua uses the C longjmp facility to yield a coroutine. Therefore, if a C function foo calls an API function and this API function yields (directly or indirectly by calling another function that yields), Lua cannot return to foo any more, because the longjmp removes its frame from the C stack.

To avoid this kind of problem, Lua raises an error whenever it tries to yield across an API call, except for three functions: lua_yieldk, lua_callk, and lua_pcallk. All those functions receive a continuation function (as a parameter named k) to continue execution after a yield.

We need to set some terminology to explain continuations. We have a C function called from Lua which we will call the original function. This original function then calls one of those three functions in the C API, which we will call the callee function, that then yields the current thread. (This can happen when the callee function is lua_yieldk, or when the callee function is either lua_callk or lua_pcallk and the function called by them yields.)

Suppose the running thread yields while executing the callee function. After the thread resumes, it eventually will finish running the callee function. However, the callee function cannot return to the original function, because its frame in the C stack was destroyed by the yield. Instead, Lua calls a continuation function, which was given as an argument to the callee function. As the name implies, the continuation function should continue the task of the original function.

As an illustration, consider the following function:

     int original_function (lua_State *L) {
       ...     /* code 1 */
       status = lua_pcall(L, n, m, h);  /* calls Lua */
       ...     /* code 2 */
     }
Now we want to allow the Lua code being run by lua_pcall to yield. First, we can rewrite our function like here:

     int k (lua_State *L, int status, lua_KContext ctx) {
       ...  /* code 2 */
     }
     
     int original_function (lua_State *L) {
       ...     /* code 1 */
       return k(L, lua_pcall(L, n, m, h), ctx);
     }
In the above code, the new function k is a continuation function (with type lua_KFunction), which should do all the work that the original function was doing after calling lua_pcall. Now, we must inform Lua that it must call k if the Lua code being executed by lua_pcall gets interrupted in some way (errors or yielding), so we rewrite the code as here, replacing lua_pcall by lua_pcallk:

     int original_function (lua_State *L) {
       ...     /* code 1 */
       return k(L, lua_pcallk(L, n, m, h, ctx2, k), ctx1);
     }
Note the external, explicit call to the continuation: Lua will call the continuation only if needed, that is, in case of errors or resuming after a yield. If the called function returns normally without ever yielding, lua_pcallk (and lua_callk) will also return normally. (Of course, instead of calling the continuation in that case, you can do the equivalent work directly inside the original function.)

Besides the Lua state, the continuation function has two other parameters: the final status of the call plus the context value (ctx) that was passed originally to lua_pcallk. (Lua does not use this context value; it only passes this value from the original function to the continuation function.) For lua_pcallk, the status is the same value that would be returned by lua_pcallk, except that it is LUA_YIELD when being executed after a yield (instead of LUA_OK). For lua_yieldk and lua_callk, the status is always LUA_YIELD when Lua calls the continuation. (For these two functions, Lua will not call the continuation in case of errors, because they do not handle errors.) Similarly, when using lua_callk, you should call the continuation function with LUA_OK as the status. (For lua_yieldk, there is not much point in calling directly the continuation function, because lua_yieldk usually does not return.)

Lua treats the continuation function as if it were the original function. The continuation function receives the same Lua stack from the original function, in the same state it would be if the callee function had returned. (For instance, after a lua_callk the function and its arguments are removed from the stack and replaced by the results from the call.) It also has the same upvalues. Whatever it returns is handled by Lua as if it were the return of the original function.

4.8 – Functions and Types

Here we list all functions and types from the C API in alphabetical order. Each function has an indicator like this: [-o, +p, x]

The first field, o, is how many elements the function pops from the stack. The second field, p, is how many elements the function pushes onto the stack. (Any function always pushes its results after popping its arguments.) A field in the form x|y means the function can push (or pop) x or y elements, depending on the situation; an interrogation mark '?' means that we cannot know how many elements the function pops/pushes by looking only at its arguments (e.g., they may depend on what is on the stack). The third field, x, tells whether the function may raise errors: '-' means the function never raises any error; 'm' means the function may raise memory errors; 'e' means the function may raise errors; 'v' means the function may raise an error on purpose.

lua_absindex

[-0, +0, –]
int lua_absindex (lua_State *L, int idx);
Converts the acceptable index idx into an equivalent absolute index (that is, one that does not depend on the stack top).

lua_Alloc

typedef void * (*lua_Alloc) (void *ud,
                             void *ptr,
                             size_t osize,
                             size_t nsize);
The type of the memory-allocation function used by Lua states. The allocator function must provide a functionality similar to realloc, but not exactly the same. Its arguments are ud, an opaque pointer passed to lua_newstate; ptr, a pointer to the block being allocated/reallocated/freed; osize, the original size of the block or some code about what is being allocated; and nsize, the new size of the block.

When ptr is not NULL, osize is the size of the block pointed by ptr, that is, the size given when it was allocated or reallocated.

When ptr is NULL, osize encodes the kind of object that Lua is allocating. osize is any of LUA_TSTRING, LUA_TTABLE, LUA_TFUNCTION, LUA_TUSERDATA, or LUA_TTHREAD when (and only when) Lua is creating a new object of that type. When osize is some other value, Lua is allocating memory for something else.

Lua assumes the following behavior from the allocator function:

When nsize is zero, the allocator must behave like free and return NULL.

When nsize is not zero, the allocator must behave like realloc. The allocator returns NULL if and only if it cannot fulfill the request. Lua assumes that the allocator never fails when osize >= nsize.

Here is a simple implementation for the allocator function. It is used in the auxiliary library by luaL_newstate.

     static void *l_alloc (void *ud, void *ptr, size_t osize,
                                                size_t nsize) {
       (void)ud;  (void)osize;  /* not used */
       if (nsize == 0) {
         free(ptr);
         return NULL;
       }
       else
         return realloc(ptr, nsize);
     }
Note that Standard C ensures that free(NULL) has no effect and that realloc(NULL,size) is equivalent to malloc(size). This code assumes that realloc does not fail when shrinking a block. (Although Standard C does not ensure this behavior, it seems to be a safe assumption.)

lua_arith

[-(2|1), +1, e]
void lua_arith (lua_State *L, int op);
Performs an arithmetic or bitwise operation over the two values (or one, in the case of negations) at the top of the stack, with the value at the top being the second operand, pops these values, and pushes the result of the operation. The function follows the semantics of the corresponding Lua operator (that is, it may call metamethods).

The value of op must be one of the following constants:

LUA_OPADD: performs addition (+)
LUA_OPSUB: performs subtraction (-)
LUA_OPMUL: performs multiplication (*)
LUA_OPDIV: performs float division (/)
LUA_OPIDIV: performs floor division (//)
LUA_OPMOD: performs modulo (%)
LUA_OPPOW: performs exponentiation (^)
LUA_OPUNM: performs mathematical negation (unary -)
LUA_OPBNOT: performs bitwise negation (~)
LUA_OPBAND: performs bitwise and (&)
LUA_OPBOR: performs bitwise or (|)
LUA_OPBXOR: performs bitwise exclusive or (~)
LUA_OPSHL: performs left shift (<<)
LUA_OPSHR: performs right shift (>>)
lua_atpanic

[-0, +0, –]
lua_CFunction lua_atpanic (lua_State *L, lua_CFunction panicf);
Sets a new panic function and returns the old one (see §4.6).

lua_call

[-(nargs+1), +nresults, e]
void lua_call (lua_State *L, int nargs, int nresults);
Calls a function.

To call a function you must use the following protocol: first, the function to be called is pushed onto the stack; then, the arguments to the function are pushed in direct order; that is, the first argument is pushed first. Finally you call lua_call; nargs is the number of arguments that you pushed onto the stack. All arguments and the function value are popped from the stack when the function is called. The function results are pushed onto the stack when the function returns. The number of results is adjusted to nresults, unless nresults is LUA_MULTRET. In this case, all results from the function are pushed. Lua takes care that the returned values fit into the stack space, but it does not ensure any extra space in the stack. The function results are pushed onto the stack in direct order (the first result is pushed first), so that after the call the last result is on the top of the stack.

Any error inside the called function is propagated upwards (with a longjmp).

The following example shows how the host program can do the equivalent to this Lua code:

     a = f("how", t.x, 14)
Here it is in C:

     lua_getglobal(L, "f");                  /* function to be called */
     lua_pushliteral(L, "how");                       /* 1st argument */
     lua_getglobal(L, "t");                    /* table to be indexed */
     lua_getfield(L, -1, "x");        /* push result of t.x (2nd arg) */
     lua_remove(L, -2);                  /* remove 't' from the stack */
     lua_pushinteger(L, 14);                          /* 3rd argument */
     lua_call(L, 3, 1);     /* call 'f' with 3 arguments and 1 result */
     lua_setglobal(L, "a");                         /* set global 'a' */
Note that the code above is balanced: at its end, the stack is back to its original configuration. This is considered good programming practice.

lua_callk

[-(nargs + 1), +nresults, e]
void lua_callk (lua_State *L,
                int nargs,
                int nresults,
                lua_KContext ctx,
                lua_KFunction k);
This function behaves exactly like lua_call, but allows the called function to yield (see §4.7).

