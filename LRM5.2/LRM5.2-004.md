3 每 The Language

This section describes the lexis, the syntax, and the semantics of Lua. In other words, this section describes which tokens are valid, how they can be combined, and what their combinations mean.

Language constructs will be explained using the usual extended BNF notation, in which {a} means 0 or more a's, and [a] means an optional a. Non-terminals are shown like non-terminal, keywords are shown like kword, and other terminal symbols are shown like ＆=＊. The complete syntax of Lua can be found in ∫9 at the end of this manual.

3.1 每 Lexical Conventions

Lua is a free-form language. It ignores spaces (including new lines) and comments between lexical elements (tokens), except as delimiters between names and keywords.

Names (also called identifiers) in Lua can be any string of letters, digits, and underscores, not beginning with a digit. Identifiers are used to name variables, table fields, and labels.

The following keywords are reserved and cannot be used as names:

     and       break     do        else      elseif    end
     false     for       function  goto      if        in
     local     nil       not       or        repeat    return
     then      true      until     while
Lua is a case-sensitive language: and is a reserved word, but And and AND are two different, valid names. As a convention, names starting with an underscore followed by uppercase letters (such as _VERSION) are reserved for variables used by Lua.

The following strings denote other tokens:

     +     -     *     /     %     ^     #
     ==    ~=    <=    >=    <     >     =
     (     )     {     }     [     ]     ::
     ;     :     ,     .     ..    ...
Literal strings can be delimited by matching single or double quotes, and can contain the following C-like escape sequences: '\a' (bell), '\b' (backspace), '\f' (form feed), '\n' (newline), '\r' (carriage return), '\t' (horizontal tab), '\v' (vertical tab), '\\' (backslash), '\"' (quotation mark [double quote]), and '\'' (apostrophe [single quote]). A backslash followed by a real newline results in a newline in the string. The escape sequence '\z' skips the following span of white-space characters, including line breaks; it is particularly useful to break and indent a long literal string into multiple lines without adding the newlines and spaces into the string contents.

A byte in a literal string can also be specified by its numerical value. This can be done with the escape sequence \xXX, where XX is a sequence of exactly two hexadecimal digits, or with the escape sequence \ddd, where ddd is a sequence of up to three decimal digits. (Note that if a decimal escape is to be followed by a digit, it must be expressed using exactly three digits.) Strings in Lua can contain any 8-bit value, including embedded zeros, which can be specified as '\0'.

Literal strings can also be defined using a long format enclosed by long brackets. We define an opening long bracket of level n as an opening square bracket followed by n equal signs followed by another opening square bracket. So, an opening long bracket of level 0 is written as [[, an opening long bracket of level 1 is written as [=[, and so on. A closing long bracket is defined similarly; for instance, a closing long bracket of level 4 is written as ]====]. A long literal starts with an opening long bracket of any level and ends at the first closing long bracket of the same level. It can contain any text except a closing bracket of the proper level. Literals in this bracketed form can run for several lines, do not interpret any escape sequences, and ignore long brackets of any other level. Any kind of end-of-line sequence (carriage return, newline, carriage return followed by newline, or newline followed by carriage return) is converted to a simple newline.

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
A numerical constant can be written with an optional fractional part and an optional decimal exponent, marked by a letter 'e' or 'E'. Lua also accepts hexadecimal constants, which start with 0x or 0X. Hexadecimal constants also accept an optional fractional part plus an optional binary exponent, marked by a letter 'p' or 'P'. Examples of valid numerical constants are

     3     3.0     3.1416     314.16e-2     0.31416E1
     0xff  0x0.1E  0xA23p-4   0X1.921FB54442D18P+1
A comment starts with a double hyphen (--) anywhere outside a string. If the text immediately after -- is not an opening long bracket, the comment is a short comment, which runs until the end of the line. Otherwise, it is a long comment, which runs until the corresponding closing long bracket. Long comments are frequently used to disable code temporarily.

3.2 每 Variables

Variables are places that store values. There are three kinds of variables in Lua: global variables, local variables, and table fields.

A single name can denote a global variable or a local variable (or a function's formal parameter, which is a particular kind of local variable):

	var ::= Name
Name denotes identifiers, as defined in ∫3.1.

Any variable name is assumed to be global unless explicitly declared as a local (see ∫3.3.7). Local variables are lexically scoped: local variables can be freely accessed by functions defined inside their scope (see ∫3.5).

Before the first assignment to a variable, its value is nil.

Square brackets are used to index a table:

	var ::= prefixexp ＆[＊ exp ＆]＊
The meaning of accesses to table fields can be changed via metatables. An access to an indexed variable t[i] is equivalent to a call gettable_event(t,i). (See ∫2.4 for a complete description of the gettable_event function. This function is not defined or callable in Lua. We use it here only for explanatory purposes.)

The syntax var.Name is just syntactic sugar for var["Name"]:

	var ::= prefixexp ＆.＊ Name
An access to a global variable x is equivalent to _ENV.x. Due to the way that chunks are compiled, _ENV is never a global name (see ∫2.2).

3.3 每 Statements

Lua supports an almost conventional set of statements, similar to those in Pascal or C. This set includes assignments, control structures, function calls, and variable declarations.

3.3.1 每 Blocks

A block is a list of statements, which are executed sequentially:

	block ::= {stat}
Lua has empty statements that allow you to separate statements with semicolons, start a block with a semicolon or write two semicolons in sequence:

	stat ::= ＆;＊
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
Explicit blocks are useful to control the scope of variable declarations. Explicit blocks are also sometimes used to add a return statement in the middle of another block (see ∫3.3.4).

3.3.2 每 Chunks

The unit of compilation of Lua is called a chunk. Syntactically, a chunk is simply a block:

	chunk ::= block
Lua handles a chunk as the body of an anonymous function with a variable number of arguments (see ∫3.4.10). As such, chunks can define local variables, receive arguments, and return values. Moreover, such anonymous function is compiled as in the scope of an external local variable called _ENV (see ∫2.2). The resulting function always has _ENV as its only upvalue, even if it does not use that variable.

A chunk can be stored in a file or in a string inside the host program. To execute a chunk, Lua first precompiles the chunk into instructions for a virtual machine, and then it executes the compiled code with an interpreter for the virtual machine.

Chunks can also be precompiled into binary form; see program luac for details. Programs in source and compiled forms are interchangeable; Lua automatically detects the file type and acts accordingly.

3.3.3 每 Assignment

Lua allows multiple assignments. Therefore, the syntax for assignment defines a list of variables on the left side and a list of expressions on the right side. The elements in both lists are separated by commas:

	stat ::= varlist ＆=＊ explist
	varlist ::= var {＆,＊ var}
	explist ::= exp {＆,＊ exp}
Expressions are discussed in ∫3.4.

Before the assignment, the list of values is adjusted to the length of the list of variables. If there are more values than needed, the excess values are thrown away. If there are fewer values than needed, the list is extended with as many nil's as needed. If the list of expressions ends with a function call, then all values returned by that call enter the list of values, before the adjustment (except when the call is enclosed in parentheses; see ∫3.4).

The assignment statement first evaluates all its expressions and only then are the assignments performed. Thus the code

     i = 3
     i, a[i] = i+1, 20
sets a[3] to 20, without affecting a[4] because the i in a[i] is evaluated (to 3) before it is assigned 4. Similarly, the line

     x, y = y, x
exchanges the values of x and y, and

     x, y, z = y, z, x
cyclically permutes the values of x, y, and z.

The meaning of assignments to global variables and table fields can be changed via metatables. An assignment to an indexed variable t[i] = val is equivalent to settable_event(t,i,val). (See ∫2.4 for a complete description of the settable_event function. This function is not defined or callable in Lua. We use it here only for explanatory purposes.)

An assignment to a global variable x = val is equivalent to the assignment _ENV.x = val (see ∫2.2).

