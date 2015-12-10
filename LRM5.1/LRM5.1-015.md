【翻译】(LRM5.1-15)字符串操纵(5.4)、模式（5.4.1）

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

5.4 - String Manipulation
This library provides generic functions for string manipulation, such as finding and extracting substrings, and pattern matching. When indexing a string in Lua, the first character is at position 1 (not at 0, as in C). Indices are allowed to be negative and are interpreted as indexing backwards, from the end of the string. Thus, the last character is at position -1, and so on. 

The string library provides all its functions inside the table string. It also sets a metatable for strings where the __index field points to the string table. Therefore, you can use the string functions in object-oriented style. For instance, string.byte(s, i) can be written as s:byte(i). 

The string library assumes one-byte character encodings. 


（注：TODO）
5.4 - 字符串操作
该库为字符串操作提供常规函数，例如查找和抽取子串以及模式匹配。在Lua中索引字符串时，第一个字符在位置1（不像C是在0处）。索引允许为负数，被解释为从字符串末尾往回索引。因此，最后一个字符在-1位置，依此类推。 

字符串库在表string内提供所有函数。它也给字符串设置元表，其中的__index字段指向string表。因此，你可以使用面向对象风格的字符串函数。例如，string.byte(s, i)可写为s:byte(i)。 

字符串库采取单字节字符编码方式。 






--------------------------------------------------------------------------------

string.byte (s [, i [, j]])
Returns the internal numerical codes of the characters s[i], s[i+1], ···, s[j]. The default value for i is 1; the default value for j is i. 
Note that numerical codes are not necessarily portable across platforms. 

--------------------------------------------------------------------------------
（注：TODO）

string.byte (s [, i [, j]])
返回字符s[i], s[i+1], ···, s[j]的内部数字代码。i缺省为1；j缺省为i。 
注意数字代码不一定是跨平台可移植的。 



--------------------------------------------------------------------------------

string.char (···)
Receives zero or more integers. Returns a string with length equal to the number of arguments, in which each character has the internal numerical code equal to its corresponding argument. 
Note that numerical codes are not necessarily portable across platforms. 

--------------------------------------------------------------------------------
（注：TODO）

string.char (···)
接受0获多个整数。返回一个字符串，其长度等于参数个数，其中的每个字符的内部数字代码等于相应的参数。 
注意数字代码不一定是跨平台可移植的。 


--------------------------------------------------------------------------------

string.dump (function)
Returns a string containing a binary representation of the given function, so that a later loadstring on this string returns a copy of the function. function must be a Lua function without upvalues. 

--------------------------------------------------------------------------------
（注：TODO）

string.dump (function)
返回给定函数的二进制表示的字符串，之后在其上应用loadstring返回函数的拷贝。function必须是不带upvalueLua函数。 


--------------------------------------------------------------------------------

string.find (s, pattern [, init [, plain]])
Looks for the first match of pattern in the string s. If it finds a match, then find returns the indices of s where this occurrence starts and ends; otherwise, it returns nil. A third, optional numerical argument init specifies where to start the search; its default value is 1 and can be negative. A value of true as a fourth, optional argument plain turns off the pattern matching facilities, so the function does a plain "find substring" operation, with no characters in pattern being considered "magic". Note that if plain is given, then init must be given as well. 
If the pattern has captures, then in a successful match the captured values are also returned, after the two indices. 

--------------------------------------------------------------------------------
（注：TODO）

string.find (s, pattern [, init [, plain]])
在字符串s中查找pattern的第一个匹配。如果找到则返回它开始和结束处在s中的索引；否则，返回nil。可选的第三参数init是数字，指定从哪儿开始搜索；其缺省值是1并且可为负数。如果真值作为可选的第四参数plain，则关闭模式匹配设备，所以函数执行无格式的“查找子串”操作，pattern中的字符并不被认为是“魔术的（magic）”。注意，如果给出了plain，则init也必须给出。 
如果模式具有捕获（capture），则在成功的匹配中被捕获的值也在两个索引后面返回。 


--------------------------------------------------------------------------------

string.format (formatstring, ···)
Returns a formatted version of its variable number of arguments following the description given in its first argument (which must be a string). The format string follows the same rules as the printf family of standard C functions. The only differences are that the options/modifiers *, l, L, n, p, and h are not supported and that there is an extra option, q. The q option formats a string in a form suitable to be safely read back by the Lua interpreter: the string is written between double quotes, and all double quotes, newlines, embedded zeros, and backslashes in the string are correctly escaped when written. For instance, the call 
     string.format('%q', 'a string with "quotes" and \n new line')

will produce the string: 

     "a string with \"quotes\" and \
      new line"

The options c, d, E, e, f, g, G, i, o, u, X, and x all expect a number as argument, whereas q and s expect a string. 

This function does not accept string values containing embedded zeros, except as arguments to the q option. 

--------------------------------------------------------------------------------
（注：TODO）

string.format (formatstring, ···)
Returns a formatted version of its variable number of arguments following the description given in its first argument (which must be a string). 格式字符串遵循同printf族标准C函数同样的规则。仅有的区别是不支持*、l、L、n、p和h等选项/修饰符，而且有个额外选项q。q选项以可安全地为Lua解释器读取的适当形式格式化字符串：字符串被写在双引号之间，而且字符串中的所有双引号、换行、内嵌的0和反斜杠被恰当地转义。例如，调用 
     string.format('%q', 'a string with "quotes" and \n new line')

产生字符串： 

     "a string with \"quotes\" and \
      new line"

选项c、d、E、e、f, g、G、i、o、u、X和x都预期得到数字作为参数，然而q和s期望得到字符串。 

该函数不接受含有内嵌的0的字符串值，除了作为q选项的参数。 


--------------------------------------------------------------------------------

string.gmatch (s, pattern)
Returns an iterator function that, each time it is called, returns the next captures from pattern over string s. If pattern specifies no captures, then the whole match is produced in each call. 
As an example, the following loop 

     s = "hello world from Lua"
     for w in string.gmatch(s, "%a+") do
       print(w)
     end

will iterate over all the words from string s, printing one per line. The next example collects all pairs key=value from the given string into a table: 

     t = {}
     s = "from=world, to=Lua"
     for k, v in string.gmatch(s, "(%w+)=(%w+)") do
       t[k] = v
     end

For this function, a '^' at the start of a pattern does not work as an anchor, as this would prevent the iteration. 

--------------------------------------------------------------------------------
（注：TODO）

string.gmatch (s, pattern)
返回一个迭代器函数，每次调用返回来自pattern的下一个捕获，从字符串s开头直到结尾。如果pattern没指定捕获则每次调用产生整个匹配。 
作为例子，下面的循环 

     s = "hello world from Lua"
     for w in string.gmatch(s, "%a+") do
       print(w)
     end

将迭代来自字符串s的所有单词，每行打印一个。下一个例子从给定的字符串收集所有的键=值对放在表中： 

     t = {}
     s = "from=world, to=Lua"
     for k, v in string.gmatch(s, "^(%w+)=(%w+)") do
       t[k] = v
     end

对于该函数，模式起始处的‘^’不能作为锚点，因为这会阻止迭代。 




--------------------------------------------------------------------------------

string.gsub (s, pattern, repl [, n])
Returns a copy of s in which all (or the first n, if given) occurrences of the pattern have been replaced by a replacement string specified by repl, which can be a string, a table, or a function. gsub also returns, as its second value, the total number of matches that occurred. 
If repl is a string, then its value is used for replacement. The character % works as an escape character: any sequence in repl of the form %n, with n between 1 and 9, stands for the value of the n-th captured substring (see below). The sequence %0 stands for the whole match. The sequence %% stands for a single %. 

If repl is a table, then the table is queried for every match, using the first capture as the key; if the pattern specifies no captures, then the whole match is used as the key. 

If repl is a function, then this function is called every time a match occurs, with all captured substrings passed as arguments, in order; if the pattern specifies no captures, then the whole match is passed as a sole argument. 

If the value returned by the table query or by the function call is a string or a number, then it is used as the replacement string; otherwise, if it is false or nil, then there is no replacement (that is, the original match is kept in the string). 

Here are some examples: 

     x = string.gsub("hello world", "(%w+)", "%1 %1")
     --> x="hello hello world world"
     
     x = string.gsub("hello world", "%w+", "%0 %0", 1)
     --> x="hello hello world"
     
     x = string.gsub("hello world from Lua", "(%w+)%s*(%w+)", "%2 %1")
     --> x="world hello Lua from"
     
     x = string.gsub("home = $HOME, user = $USER", "%$(%w+)", os.getenv)
     --> x="home = /home/roberto, user = roberto"
     
     x = string.gsub("4+5 = $return 4+5$", "%$(.-)%$", function (s)
           return loadstring(s)()
         end)
     --> x="4+5 = 9"
     
     local t = {name="lua", version="5.1"}
     x = string.gsub("$name-$version.tar.gz", "%$(%w+)", t)
     --> x="lua-5.1.tar.gz"

--------------------------------------------------------------------------------
（注：TODO）

string.gsub (s, pattern, repl [, n])
返回s的拷贝，其中出现的所有（或前n个，如果指定）pattern被替换为repl——可以是字符串、表或函数，指定的替换串。gsub也返回出现的匹配的总数作为第二个值。 
如果repl是字符串，它的值被用作替换式。字符%用作转义字符：repl中的任何形如%n的序列代表第n个捕获的子串（见下面），其中n在1和9之间。序列%0代表整个匹配。序列%%代表单个%。 

如果repl是表，则对于每个匹配，用第一个捕获作为键查询表；如果模式未指定捕获，则整个匹配被用作键。 

如果repl是函数，则每次匹配发生时都按顺序传入所有捕获的子串作为参数调用该函数；如果模式没指定捕获，则整个匹配作为单个参数传入。 

如果表查询或函数调用返回的结果是个字符串或数字，则被用作替换串；否则，如果是false或nil，则不发生替换（即原始匹配被保持在字符串中）。 

这里有一些例子： 

     x = string.gsub("hello world", "(%w+)", "%1 %1")
     --> x="hello hello world world"
     
     x = string.gsub("hello world", "%w+", "%0 %0", 1)
     --> x="hello hello world"
     
     x = string.gsub("hello world from Lua", "(%w+)%s*(%w+)", "%2 %1")
     --> x="world hello Lua from"
     
     x = string.gsub("home = $HOME, user = $USER", "%$(%w+)", os.getenv)
     --> x="home = /home/roberto, user = roberto"
     
     x = string.gsub("4+5 = $return 4+5$", "%$(.-)%$", function (s)
           return loadstring(s)()
         end)
     --> x="4+5 = 9"
     
     local t = {name="lua", version="5.1"}
     x = string.gsub("$name-$version.tar.gz", "%$(%w+)", t)
     --> x="lua-5.1.tar.gz"



--------------------------------------------------------------------------------

string.len (s)
Receives a string and returns its length. The empty string "" has length 0. Embedded zeros are counted, so "a\000bc\000" has length 5. 

--------------------------------------------------------------------------------
（注：TODO）

string.len (s)
接受字符串并返回其长度。空串""长度为0。内嵌的0被计算在内，所以"a\000bc\000"长度为5。 

--------------------------------------------------------------------------------

string.lower (s)
Receives a string and returns a copy of this string with all uppercase letters changed to lowercase. All other characters are left unchanged. The definition of what an uppercase letter is depends on the current locale. 

--------------------------------------------------------------------------------
（注：TODO）

string.lower (s)
接受字符串并返回其所有大写字母变为小写的拷贝。所有其他字符不变。大写字母的定义依赖于当前locale。 


--------------------------------------------------------------------------------

string.match (s, pattern [, init])
Looks for the first match of pattern in the string s. If it finds one, then match returns the captures from the pattern; otherwise it returns nil. If pattern specifies no captures, then the whole match is returned. A third, optional numerical argument init specifies where to start the search; its default value is 1 and can be negative. 

--------------------------------------------------------------------------------
（注：TODO）

string.match (s, pattern [, init])
在字符串s中查找pattern的首次匹配。如果找到一个，则返回来自模式的捕获；否则返回nil。如果pattern未指定捕获则返回整个匹配。可选的第三个参数init是数字，指定从哪儿开始搜索；其缺省值是1并且可为负。 



--------------------------------------------------------------------------------

string.rep (s, n)
Returns a string that is the concatenation of n copies of the string s. 

--------------------------------------------------------------------------------
（注：TODO）

string.rep (s, n)
返回字符串s的n个拷贝拼接字符串。 


--------------------------------------------------------------------------------

string.reverse (s)
Returns a string that is the string s reversed. 

--------------------------------------------------------------------------------
（注：TODO）

string.reverse (s)
返回字符串s的颠倒的字符串。 


--------------------------------------------------------------------------------

string.sub (s, i [, j])
Returns the substring of s that starts at i and continues until j; i and j can be negative. If j is absent, then it is assumed to be equal to -1 (which is the same as the string length). In particular, the call string.sub(s,1,j) returns a prefix of s with length j, and string.sub(s, -i) returns a suffix of s with length i. 

--------------------------------------------------------------------------------
（注：TODO）

string.sub (s, i [, j])
返回s的子串，它起始于i并延续到j；i和j可为负数。如果省略j，则它被假定为-1（同字符串长度一样）。特别地，调用string.sub(s,1,j)返回s长为j的前缀，而且string.sub(s, -i)返回s长为i的后缀。 


--------------------------------------------------------------------------------

string.upper (s)
Receives a string and returns a copy of this string with all lowercase letters changed to uppercase. All other characters are left unchanged. The definition of what a lowercase letter is depends on the current locale. 


--------------------------------------------------------------------------------
（注：TODO）

string.upper (s)
接受字符串并返回其所有小写字母变为大写的拷贝。所有其他字符不变。小写字母的定义依赖于当前locale。 






5.4.1 - Patterns
Character Class:
A character class is used to represent a set of characters. The following combinations are allowed in describing a character class: 

x: (where x is not one of the magic characters ^$()%.[]*+-?) represents the character x itself. 
.: (a dot) represents all characters. 
%a: represents all letters. 
%c: represents all control characters. 
%d: represents all digits. 
%l: represents all lowercase letters. 
%p: represents all punctuation characters. 
%s: represents all space characters. 
%u: represents all uppercase letters. 
%w: represents all alphanumeric characters. 
%x: represents all hexadecimal digits. 
%z: represents the character with representation 0. 
%x: (where x is any non-alphanumeric character) represents the character x. This is the standard way to escape the magic characters. Any punctuation character (even the non magic) can be preceded by a '%' when used to represent itself in a pattern. 
[set]: represents the class which is the union of all characters in set. A range of characters can be specified by separating the end characters of the range with a '-'. All classes %x described above can also be used as components in set. All other characters in set represent themselves. For example, [%w_] (or [_%w]) represents all alphanumeric characters plus the underscore, [0-7] represents the octal digits, and [0-7%l%-] represents the octal digits plus the lowercase letters plus the '-' character. 
The interaction between ranges and classes is not defined. Therefore, patterns like [%a-z] or [a-%%] have no meaning. 

[^set]: represents the complement of set, where set is interpreted as above. 
For all classes represented by single letters (%a, %c, etc.), the corresponding uppercase letter represents the complement of the class. For instance, %S represents all non-space characters. 

The definitions of letter, space, and other character groups depend on the current locale. In particular, the class [a-z] may not be equivalent to %l. 

Pattern Item:
A pattern item can be 

a single character class, which matches any single character in the class; 
a single character class followed by '*', which matches 0 or more repetitions of characters in the class. These repetition items will always match the longest possible sequence; 
a single character class followed by '+', which matches 1 or more repetitions of characters in the class. These repetition items will always match the longest possible sequence; 
a single character class followed by '-', which also matches 0 or more repetitions of characters in the class. Unlike '*', these repetition items will always match the shortest possible sequence; 
a single character class followed by '?', which matches 0 or 1 occurrence of a character in the class; 
%n, for n between 1 and 9; such item matches a substring equal to the n-th captured string (see below); 
%bxy, where x and y are two distinct characters; such item matches strings that start with x, end with y, and where the x and y are balanced. This means that, if one reads the string from left to right, counting +1 for an x and -1 for a y, the ending y is the first y where the count reaches 0. For instance, the item %b() matches expressions with balanced parentheses. 
Pattern:
A pattern is a sequence of pattern items. A '^' at the beginning of a pattern anchors the match at the beginning of the subject string. A '$' at the end of a pattern anchors the match at the end of the subject string. At other positions, '^' and '$' have no special meaning and represent themselves. 

Captures:
A pattern can contain sub-patterns enclosed in parentheses; they describe captures. When a match succeeds, the substrings of the subject string that match captures are stored (captured) for future use. Captures are numbered according to their left parentheses. For instance, in the pattern "(a*(.)%w(%s*))", the part of the string matching "a*(.)%w(%s*)" is stored as the first capture (and therefore has number 1); the character matching "." is captured with number 2, and the part matching "%s*" has number 3. 

As a special case, the empty capture () captures the current string position (a number). For instance, if we apply the pattern "()aa()" on the string "flaaap", there will be two captures: 3 and 5. 

A pattern cannot contain embedded zeros. Use %z instead. 



（注：TODO）

5.4.1 - 模式
字符类（Character Class）：
一个字符类被用于表示一组字符。允许用下面的组合描述字符类： 

x: （此处x不是魔术字符^$()%.[]*+-?中的一个）表示字符x本身。 
.: （一个点）表示所有字符。 
%a: 表示所有字母。 
%c: 表示所有控制字符。 
%d: 表示所有十进制数字。 
%l: 表示所有小写字母。 
%p: 表示所有标点符号。 
%s: 表示所有空白字符。 
%u: 表示所有大写字母。 
%w: 表示所有字母数字字符。 
%x: 表示所有十六进制数字。 
%z: 表示0值字符。 
%x: （此处x是任何非字母数字字符）表示字符x。这是转义魔术字符的标准方式。当被用于在模式中表示自身时，任何标点符号（甚至非魔术的）都能前缀一个‘%’。 
[set]: 表示set中的所有字符的联合构成的分类。通过用‘-’分隔截止字符可以指定某个范围的字符。上面描述的所有种类的%x都可用作set的部件。set中的所有其他字符表示它们自身。例如[%w_]（或[_%w]）表示所有字母数字字符和下划线，[0-7]表示八进制数字，[0-7%l%-]表示八进制数字和小写字母以及‘-’字符。 
字符范围和字符类之间的相互作用是未定义的。因此类似[%a-z]或[a-%%]的模式没有意义。 

[^set]: 表示set的补集，其中的set在上面解释了。 
所有单字母表示的字符类（%a、%c，等等），相应的大写字母表示该字符类的补集。例如，%S表示所有非空白符。 

字母、空白和其他字符组合的定义依赖于当前locale。特别地，字符类[a-z]可能不等于%l。 

模式项（Pattern Item）:
模式项可为 

单个字符类，它匹配该类中的任意单个字符； 
后跟‘*’的单个字符类，它匹配该类中的0或多个字符。这些重复项将总是匹配最长的可能序列； 
后跟‘+’的单个字符类，它匹配该类中的1或多个字符。这些重复项将总是匹配最长的可能序列； 
后跟‘-’的单个字符类，它也匹配该类中的0或多个字符。与‘*’不同，这些重复项将总是匹配最短的可能序列； 
后跟‘?’的单个字符类，它匹配出现0或1次该类中的字符； 
%n，其中n在1和9之间；这种项匹配一个等价于捕获的字符串的第n个子串（见下面）； 
%bxy其中x和y是两个不同的字符；这种项匹配始于x终于y的字符串，并且x和y是对称的。这表示，如果一个人从左到右读字符串，对x计数为+1，对y计数为-1，结尾的y是第一个遇到计数为0的y 。例如，项%b()匹配带有平衡的圆括号的表达式。 
模式（Pattern）:
模式是一系列的模式项。在模式开头的‘^’将匹配固定在源串的开头。 在模式结尾的‘$’将匹配固定在源串的结尾。在其他位置上，‘^’和‘$’没有特殊含义，表示它们自身。 

捕获（Captures）:
模式可以含有括在圆括号内的子模式；它们描述捕获。当成功进行一个匹配，源串中匹配捕获的子串被存储（捕获）以便将来使用。捕获根据它们的左圆括号进行编号。例如，在模式"(a*(.)%w(%s*))"中，字符串的匹配"a*(.)%w(%s*)"的部分作为第一个捕获被存储（因此被编号为1）；匹配“.”的字符被捕获并编号为2，匹配“%s*”的部分被编号为3。 

作为一种特殊情况，空捕获()捕获当前字符串位置（一个数字）。例如，如果我们把模式"()aa()"用于字符串"flaaap"，将有两个捕获：3和5。 

模式不能含有内嵌的0。使用%z代替。 


