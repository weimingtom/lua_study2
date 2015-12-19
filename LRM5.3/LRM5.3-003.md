2.6 – Coroutines

Lua supports coroutines, also called collaborative multithreading. A coroutine in Lua represents an independent thread of execution. Unlike threads in multithread systems, however, a coroutine only suspends its execution by explicitly calling a yield function.

You create a coroutine by calling coroutine.create. Its sole argument is a function that is the main function of the coroutine. The create function only creates a new coroutine and returns a handle to it (an object of type thread); it does not start the coroutine.

You execute a coroutine by calling coroutine.resume. When you first call coroutine.resume, passing as its first argument a thread returned by coroutine.create, the coroutine starts its execution by calling its main function. Extra arguments passed to coroutine.resume are passed as arguments to that function. After the coroutine starts running, it runs until it terminates or yields.

A coroutine can terminate its execution in two ways: normally, when its main function returns (explicitly or implicitly, after the last instruction); and abnormally, if there is an unprotected error. In case of normal termination, coroutine.resume returns true, plus any values returned by the coroutine main function. In case of errors, coroutine.resume returns false plus an error message.

A coroutine yields by calling coroutine.yield. When a coroutine yields, the corresponding coroutine.resume returns immediately, even if the yield happens inside nested function calls (that is, not in the main function, but in a function directly or indirectly called by the main function). In the case of a yield, coroutine.resume also returns true, plus any values passed to coroutine.yield. The next time you resume the same coroutine, it continues its execution from the point where it yielded, with the call to coroutine.yield returning any extra arguments passed to coroutine.resume.

Like coroutine.create, the coroutine.wrap function also creates a coroutine, but instead of returning the coroutine itself, it returns a function that, when called, resumes the coroutine. Any arguments passed to this function go as extra arguments to coroutine.resume. coroutine.wrap returns all the values returned by coroutine.resume, except the first one (the boolean error code). Unlike coroutine.resume, coroutine.wrap does not catch errors; any error is propagated to the caller.

As an example of how coroutines work, consider the following code:

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

     co-body 1       10
     foo     2
     main    true    4
     co-body r
     main    true    11      -9
     co-body x       y
     main    true    10      end
     main    false   cannot resume dead coroutine
You can also create and manipulate coroutines through the C API: see functions lua_newthread, lua_resume, and lua_yield.

3 – The Language

This section describes the lexis, the syntax, and the semantics of Lua. In other words, this section describes which tokens are valid, how they can be combined, and what their combinations mean.

Language constructs will be explained using the usual extended BNF notation, in which {a} means 0 or more a's, and [a] means an optional a. Non-terminals are shown like non-terminal, keywords are shown like kword, and other terminal symbols are shown like ‘=’. The complete syntax of Lua can be found in §9 at the end of this manual.

3.1 – Lexical Conventions

Lua is a free-form language. It ignores spaces (including new lines) and comments between lexical elements (tokens), except as delimiters between names and keywords.

Names (also called identifiers) in Lua can be any string of letters, digits, and underscores, not beginning with a digit and not being a reserved word. Identifiers are used to name variables, table fields, and labels.

The following keywords are reserved and cannot be used as names:

     and       break     do        else      elseif    end
     false     for       function  goto      if        in
     local     nil       not       or        repeat    return
     then      true      until     while
Lua is a case-sensitive language: and is a reserved word, but And and AND are two different, valid names. As a convention, programs should avoid creating names that start with an underscore followed by one or more uppercase letters (such as _VERSION).

The following strings denote other tokens:

     +     -     *     /     %     ^     #
     &     ~     |     <<    >>    //
     ==    ~=    <=    >=    <     >     =
     (     )     {     }     [     ]     ::
     ;     :     ,     .     ..    ...
Literal strings can be delimited by matching single or double quotes, and can contain the following C-like escape sequences: '\a' (bell), '\b' (backspace), '\f' (form feed), '\n' (newline), '\r' (carriage return), '\t' (horizontal tab), '\v' (vertical tab), '\\' (backslash), '\"' (quotation mark [double quote]), and '\'' (apostrophe [single quote]). A backslash followed by a real newline results in a newline in the string. The escape sequence '\z' skips the following span of white-space characters, including line breaks; it is particularly useful to break and indent a long literal string into multiple lines without adding the newlines and spaces into the string contents.

Strings in Lua can contain any 8-bit value, including embedded zeros, which can be specified as '\0'. More generally, we can specify any byte in a literal string by its numeric value. This can be done with the escape sequence \xXX, where XX is a sequence of exactly two hexadecimal digits, or with the escape sequence \ddd, where ddd is a sequence of up to three decimal digits. (Note that if a decimal escape sequence is to be followed by a digit, it must be expressed using exactly three digits.)

The UTF-8 encoding of a Unicode character can be inserted in a literal string with the escape sequence \u{XXX} (note the mandatory enclosing brackets), where XXX is a sequence of one or more hexadecimal digits representing the character code point.

Literal strings can also be defined using a long format enclosed by long brackets. We define an opening long bracket of level n as an opening square bracket followed by n equal signs followed by another opening square bracket. So, an opening long bracket of level 0 is written as [[, an opening long bracket of level 1 is written as [=[, and so on. A closing long bracket is defined similarly; for instance, a closing long bracket of level 4 is written as ]====]. A long literal starts with an opening long bracket of any level and ends at the first closing long bracket of the same level. It can contain any text except a closing bracket of the same level. Literals in this bracketed form can run for several lines, do not interpret any escape sequences, and ignore long brackets of any other level. Any kind of end-of-line sequence (carriage return, newline, carriage return followed by newline, or newline followed by carriage return) is converted to a simple newline.

Any byte in a literal string not explicitly affected by the previous rules represents itself. However, Lua opens files for parsing in text mode, and the system file functions may have problems with some control characters. So, it is safer to represent non-text data as a quoted literal with explicit escape sequences for non-text characters.

For convenience, when the opening long bracket is immediately followed by a newline, the newline is not included in the string. As an example, in a system using ASCII (in which 'a' is coded as 97, newline is coded as 10, and '1' is coded as 49), the five literal strings below denote the same string:

     a = 'alo\n123"'
     a = "alo\n123\""
     a = '\97lo\10\04923"'
     a = [[alo
     123"]]
     a = [==[
     alo
     123"]==]
A numeric constant (or numeral) can be written with an optional fractional part and an optional decimal exponent, marked by a letter 'e' or 'E'. Lua also accepts hexadecimal constants, which start with 0x or 0X. Hexadecimal constants also accept an optional fractional part plus an optional binary exponent, marked by a letter 'p' or 'P'. A numeric constant with a fractional dot or an exponent denotes a float; otherwise it denotes an integer. Examples of valid integer constants are

     3   345   0xff   0xBEBADA
Examples of valid float constants are

     3.0     3.1416     314.16e-2     0.31416E1     34e1
     0x0.1E  0xA23p-4   0X1.921FB54442D18P+1
A comment starts with a double hyphen (--) anywhere outside a string. If the text immediately after -- is not an opening long bracket, the comment is a short comment, which runs until the end of the line. Otherwise, it is a long comment, which runs until the corresponding closing long bracket. Long comments are frequently used to disable code temporarily.

3.2 – Variables

Variables are places that store values. There are three kinds of variables in Lua: global variables, local variables, and table fields.

A single name can denote a global variable or a local variable (or a function's formal parameter, which is a particular kind of local variable):

	var ::= Name
Name denotes identifiers, as defined in §3.1.

Any variable name is assumed to be global unless explicitly declared as a local (see §3.3.7). Local variables are lexically scoped: local variables can be freely accessed by functions defined inside their scope (see §3.5).

Before the first assignment to a variable, its value is nil.

Square brackets are used to index a table:

	var ::= prefixexp ‘[’ exp ‘]’
The meaning of accesses to table fields can be changed via metatables. An access to an indexed variable t[i] is equivalent to a call gettable_event(t,i). (See §2.4 for a complete description of the gettable_event function. This function is not defined or callable in Lua. We use it here only for explanatory purposes.)

The syntax var.Name is just syntactic sugar for var["Name"]:

	var ::= prefixexp ‘.’ Name
An access to a global variable x is equivalent to _ENV.x. Due to the way that chunks are compiled, _ENV is never a global name (see §2.2).

