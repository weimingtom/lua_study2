## 【翻译】(LRM5.1-5)环境(2.9)、垃圾回收(2.10)、协程(2.11)  

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

## 2.9 - Environments  

## 2.9 - 环境  

Besides metatables, objects of types thread, function, and userdata have another table associated with them, called their environment. Like metatables, environments are regular tables and multiple objects can share the same environment.   

除了元表以外，线程、函数和用户数据类型的对象还有另一个与它们相关联的表，称为环境。像元表那样，环境是个常规表，多个对象可以共享相同的环境。  

Threads are created sharing the environment of the creating thread. Userdata and C functions are created sharing the environment of the creating C function. Non-nested Lua functions (created by loadfile, loadstring or load) are created sharing the environment of the creating thread. Nested Lua functions are created sharing the environment of the creating Lua function.   

被创建的线程共享创建方线程的环境。被创建的userdata和C函数共享创建方C函数的环境。被创建的非嵌套Lua函数（由loadfile，loadstring或load创建的函数）共享创建方线程的环境。被创建的嵌套的Lua函数共享创建方Lua函数的环境。  

Environments associated with userdata have no meaning for Lua. It is only a convenience feature for programmers to associate a table to a userdata.   

与userdata关联的环境对于Lua代码来说没有意义。这只是让程序员把表关联到userdata的便利特性。  

Environments associated with threads are called global environments. They are used as the default environment for threads and non-nested Lua functions created by the thread and can be directly accessed by C code (see §3.3).   

与线程关联的环境被称为全局环境。它们被用作线程和被该线程创建的非嵌套Lua函数的默认环境，并且可以被C代码直接访问（见§3.3）。  

The environment associated with a C function can be directly accessed by C code (see §3.3). It is used as the default environment for other C functions and userdata created by the function.   

与C函数关联的环境可以被C代码直接地访问（见§3.3）。它被用作被该函数创建的其它C函数和用户数据的默认环境。  

Environments associated with Lua functions are used to resolve all accesses to global variables within the function (see §2.3). They are used as the default environment for nested Lua functions created by the function.   

Lua函数关联的环境习惯用于解决在函数内部对全局变量的访问问题。（见§2.3）它们被用作这个函数所创建的嵌套Lua函数的默认环境。  

You can change the environment of a Lua function or the running thread by calling setfenv. You can get the environment of a Lua function or the running thread by calling getfenv. To manipulate the environment of other objects (userdata, C functions, other threads) you must use the C API.   

你可以通过调用setfenv改变一个Lua函数或者运行中的线程的环境。你可以通过调用getfenv获得一个Lua函数或者运行中的线程的环境。为了操纵其它对象的环境（userdata，C函数，其它线程），你必须使用C API。   

## 2.10 - Garbage Collection  

## 2.10 - 垃圾回收  

Lua performs automatic memory management. This means that you have to worry neither about allocating memory for new objects nor about freeing it when the objects are no longer needed. Lua manages memory automatically by running a garbage collector from time to time to collect all dead objects (that is, objects that are no longer accessible from Lua). All memory used by Lua is subject to automatic management: tables, userdata, functions, threads, strings, etc.   

Lua执行自动内存管理。这意味着你不必担心新对象的内存分配，也不必担心不再需要的对象如何释放。Lua通过间或地运行垃圾回收器自动管理内存来回收所有死亡的对象（即在Lua中不再可以访问的对象）。Lua使用的所有内存都受制于自动管理下：表，用户数据，函数，线程，字符串，等等。  

Lua implements an incremental mark-and-sweep collector. It uses two numbers to control its garbage-collection cycles: the garbage-collector pause and the garbage-collector step multiplier. Both use percentage points as units (so that a value of 100 means an internal value of 1).   

Lua实现一个增量标记清除回收器。它使用两个数来控制它的垃圾回收周期：垃圾回收器暂停和垃圾回收器步长倍数。两者都使用百分点作为单位。（所以一个100的值代表内部数值1的意思）  

The garbage-collector pause controls how long the collector waits before starting a new cycle. Larger values make the collector less aggressive. Values smaller than 100 mean the collector will not wait to start a new cycle. A value of 200 means that the collector waits for the total memory in use to double before starting a new cycle.   

垃圾回收器暂停控制着回收器在一个新周期前等待的时间长度。较大的值使回收器拥有较少的侵入性。小于100的值意味着回收器将不会等待开始新的周期中。一个200的值意味着回收器等待所有使用中的内存翻一倍后才开始新的周期。  

The step multiplier controls the relative speed of the collector relative to memory allocation. Larger values make the collector more aggressive but also increase the size of each incremental step. Values smaller than 100 make the collector too slow and can result in the collector never finishing a cycle. The default, 200, means that the collector runs at "twice" the speed of memory allocation.   

步长倍数控制回收器相对于内存分配器的相对速度。较大的值会使回收器更具侵入性，但同时增加了每次增量步长的大小。小于100的值使回收器更慢从而致使回收器永远都不能完成一次周期。默认值200意味着回收器以内存分配器“两倍”的速度运行。  

You can change these numbers by calling lua_gc in C or collectgarbage in Lua. With these functions you can also control the collector directly (e.g., stop and restart it).   

你可以通过在C中调用lua_gc或者在Lua中调用collectgarbage来改变这些值。使用这些函数你也可以直接控制回收器（例如停止或重启它）。  

## 2.10.1 - Garbage-Collection Metamethods  

## 2.10.1 - 垃圾回收的元方法   

Using the C API, you can set garbage-collector metamethods for userdata (see §2.8). These metamethods are also called finalizers. Finalizers allow you to coordinate Lua's garbage collection with external resource management (such as closing files, network or database connections, or freeing your own memory).   

使用C API，您可以设置userdata的垃圾回收的元方法（见§2.8）。这些元方法也被称为回收函数**（注：终结函数）**。回收函数允许你使用外部资源管理协调Lua的垃圾回收（如关闭文件，网络或数据库连接，或者释放你自己的内存）。   

Garbage userdata with a field __gc in their metatables are not collected immediately by the garbage collector. Instead, Lua puts them in a list. After the collection, Lua does the equivalent of the following function for each userdata in that list:   

在元表中带字段__gc的垃圾用户数据不会马上被垃圾回收器回收。相反，Lua把它们放在一个列表中，在回收后，Lua对那个列表中的每一个userdata执行以下函数的等效操作：  

     function gc_event (udata)  
       local h = metatable(udata).__gc  
       if h then  
         h(udata)  
       end  
     end  

At the end of each garbage-collection cycle, the finalizers for userdata are called in reverse order of their creation, among those collected in that cycle. That is, the first finalizer to be called is the one associated with the userdata created last in the program. The userdata itself is freed only in the next garbage-collection cycle.   

在每个垃圾回收周期的最后，在那个周期回收的那些用户数据的回收函数会以它们相反的创建顺序被调用。即第一个被调用的回收函数是关联着那个在程序中最后创建的用户数据。用户数据自身只会在下一次垃圾回收周期里被释放。  

## 2.10.2 - Weak Tables  

## 2.10.2 - 弱表  

A weak table is a table whose elements are weak references. A weak reference is ignored by the garbage collector. In other words, if the only references to an object are weak references, then the garbage collector will collect this object.   

弱表是元素为弱引用的表。弱引用被垃圾收集器忽略**（注：这里的忽略是指即使它的引用数大于0仍然可以回收它，不是指永远不回收）**。换言之，如果指向一个对象的唯一引用是弱引用，那么垃圾回收器将回收这个对象。  

A weak table can have weak keys, weak values, or both. A table with weak keys allows the collection of its keys, but prevents the collection of its values. A table with both weak keys and weak values allows the collection of both keys and values. In any case, if either the key or the value is collected, the whole pair is removed from the table. The weakness of a table is controlled by the __mode field of its metatable. If the __mode field is a string containing the character 'k', the keys in the table are weak. If __mode contains 'v', the values in the table are weak.   

一个弱表可以有弱键，弱值，或两者都有。一个带弱键的表允许其键的回收，但阻止其值的回收。拥有弱键和弱值的表允许键和值的回收。在任何情况下，如果键或值被回收，整个映射对会从表中删除。表的弱引用属性由它的元表__mode字段控制。如果__mode字段是包含'k'的字符串，表的键为弱引用。如果__mode包含'v'，表的值为弱引用。  

After you use a table as a metatable, you should not change the value of its __mode field. Otherwise, the weak behavior of the tables controlled by this metatable is undefined.   

在你使用一个表作为元表后，你不应该改变它的__mode字段的值。否则，受这个元表控制的表的弱引用行为是不确定的。  

## 2.11 - Coroutines  

## 2.11 - 协程  

Lua supports coroutines, also called collaborative multithreading. A coroutine in Lua represents an independent thread of execution. Unlike threads in multithread systems, however, a coroutine only suspends its execution by explicitly calling a yield function.   

Lua支持协程，它也被称为协同多线程。Lua中的协程代表一个独立执行的线程。然而不同于多线程系统的线程，协程只能通过yield函数的显式调用挂起它的执行。  

You create a coroutine with a call to coroutine.create. Its sole argument is a function that is the main function of the coroutine. The create function only creates a new coroutine and returns a handle to it (an object of type thread); it does not start the coroutine execution.   

你调用coroutine.create创建一个协程。它唯一的参数是一个作为协程主函数的函数。create函数只创建一个新协程，并返回它的句柄（一个thread类型对象），它不启动协程的执行。  

When you first call coroutine.resume, passing as its first argument a thread returned by coroutine.create, the coroutine starts its execution, at the first line of its main function. Extra arguments passed to coroutine.resume are passed on to the coroutine main function. After the coroutine starts running, it runs until it terminates or yields.   

当你第一次调用coroutine.resume，把coroutine.create返回的一个线程作为第一个参数传递给它，则协程从主函数的第一行开始执行。传递给coroutine.resume的额外参数传递给协程的主函数。协程开始运行后，它将一直运行直至结束或者挂起。   

A coroutine can terminate its execution in two ways: normally, when its main function returns (explicitly or implicitly, after the last instruction); and abnormally, if there is an unprotected error. In the first case, coroutine.resume returns true, plus any values returned by the coroutine main function. In case of errors, coroutine.resume returns false plus an error message.   

协同程序可以用两种方式结束它的执行：通常情况下，它的主函数返回（显式或者隐式，在最后一个指令之后）；以及异常，如果发生一个不受保护的错误。对于第一种情况，coroutine.resume返回true，以及协程主函数的所有返回值。对于错误的情况，coroutine.resume返回false，以及一个错误消息。  

A coroutine yields by calling coroutine.yield. When a coroutine yields, the corresponding coroutine.resume returns immediately, even if the yield happens inside nested function calls (that is, not in the main function, but in a function directly or indirectly called by the main function). In the case of a yield, coroutine.resume also returns true, plus any values passed to coroutine.yield. The next time you resume the same coroutine, it continues its execution from the point where it yielded, with the call to coroutine.yield returning any extra arguments passed to coroutine.resume.   

通过调用coroutine.yield挂起（暂停）协程。当一个协程挂起，相应的coroutine.resume会立即返回，即使挂起发生在嵌套函数调用的内部（即不是在主函数中而是在一个直接或间接被主函数调用的函数中）。在挂起的情况下，coroutine.resume也返回true，以及传递给coroutine.yield的任何值。下次你恢复相同的协程时，它会继续从它挂起的地方开始执行，在那里coroutine.yield的调用会返回（执行resume时）传递给coroutine.resume的所有值。  

Like coroutine.create, the coroutine.wrap function also creates a coroutine, but instead of returning the coroutine itself, it returns a function that, when called, resumes the coroutine. Any arguments passed to this function go as extra arguments to coroutine.resume. coroutine.wrap returns all the values returned by coroutine.resume, except the first one (the boolean error code). Unlike coroutine.resume, coroutine.wrap does not catch errors; any error is propagated to the caller.   

像coroutine.create那样，coroutine.wrap函数也能创建协同程序，但不是返回协程自身，而是返回一个函数，当调用它时，恢复协程。传递给此函数的任何参数会作为coroutine.resume的额外参数。coroutine.wrap返回coroutine.resume所返回的所有值，除了第一个返回值（布尔型的错误码）。不同于coroutine.resume，coroutine.wrap不捕获错误，任何错误会传播给调用者。  

As an example, consider the following code:   

作为例子，考虑以下代码：  

     function foo (a)  
       print("foo", a)  
       return coroutine.yield(2*a)  
     end  
     
     co = coroutine.create(function (a,b)  
           print("co-body", a, b)  
           local r = foo(a+1)  
           print("co-body", r)  
           local r, s = coroutine.yield(a+b, a-b)  
           print("co-body", r, s)  
           return b, "end"  
     end)  
            
     print("main", coroutine.resume(co, 1, 10))  
     print("main", coroutine.resume(co, "r"))  
     print("main", coroutine.resume(co, "x", "y"))  
     print("main", coroutine.resume(co, "x", "y"))  

When you run it, it produces the following output:   

当你运行它时，它产生如下输出：  

     co-body 1       10  
     foo     2  
     
     main    true    4  
     co-body r  
     main    true    11      -9  
     co-body x       y  
     main    true    10      end  
     main    false   cannot resume dead coroutine  

-----------------------------------------  

## 参考自：  
1. Lua 5.1 参考手册 （云风译）  
http://www.codingnow.com/2000/download/lua_manual.html  

2. hshqcn  
hshqcn  

