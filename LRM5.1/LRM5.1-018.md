【翻译】(LRM5.1-18)输入输出工具(5.7)、操作系统工具(5.8)

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

5.7 - Input and Output Facilities
The I/O library provides two different styles for file manipulation. The first one uses implicit file descriptors; that is, there are operations to set a default input file and a default output file, and all input/output operations are over these default files. The second style uses explicit file descriptors. 

When using implicit file descriptors, all operations are supplied by table io. When using explicit file descriptors, the operation io.open returns a file descriptor and then all operations are supplied as methods of the file descriptor. 

The table io also provides three predefined file descriptors with their usual meanings from C: io.stdin, io.stdout, and io.stderr. The I/O library never closes these files. 

Unless otherwise stated, all I/O functions return nil on failure (plus an error message as a second result and a system-dependent error code as a third result) and some value different from nil on success. 


5.7 - 输入输出工具
输入输出库提供两种不同操作文件的风格。
第一种是隐式文件描述符；有操作默认输入文件和默认输出文件的操作，以及所有输入输出操作作用于这些默认文件之上。
第二种风格是使用显式文件描述符。
当使用隐式文件描述符时，所有操作由表io提供。
当使用显式文件描述符时，操作io.open返回一个文件描述符，并且所有操作会作为这个文件描述符的方法来提供。
表io也提供三个带有C的普遍意义的预定义文件描述符：io.stdin，io.stdout和io.stderr。
输入输出库从不关闭这些文件。
除了特别说明，所有输入输出库在失败时返回nil（外加作为第二结果的错误信息和一个作为第三结果的依赖系统的错误码），成功时则返回非nil的值。


--------------------------------------------------------------------------------

io.close ([file])
Equivalent to file:close(). Without a file, closes the default output file. 

--------------------------------------------------------------------------------

io.close ([file])
等效于file:close()。
不指定file时，关闭默认的输出文件。 

--------------------------------------------------------------------------------

io.flush ()
Equivalent to file:flush over the default output file. 

--------------------------------------------------------------------------------

io.flush ()
等效于对默认输出文件执行file:flush 

--------------------------------------------------------------------------------

io.input ([file])
When called with a file name, it opens the named file (in text mode), and sets its handle as the default input file. When called with a file handle, it simply sets this file handle as the default input file. When called without parameters, it returns the current default input file. 

In case of errors this function raises the error, instead of returning an error code. 

--------------------------------------------------------------------------------
（注：TODO）

io.input ([file])
用文件名调用时，它打开该命名文件（以文本模式），并设置其句柄为缺省的输入文件。用文件句柄调用时，它只是设置该文件句柄为缺省的输入文件。不带参数调用时，它返回当前的缺省的输入文件。 

在错误的情况下，该函数抛出错误而不是返回错误代码。 



--------------------------------------------------------------------------------

io.lines ([filename])
Opens the given file name in read mode and returns an iterator function that, each time it is called, returns a new line from the file. Therefore, the construction 

     for line in io.lines(filename) do body end

will iterate over all lines of the file. When the iterator function detects the end of file, it returns nil (to finish the loop) and automatically closes the file. 

The call io.lines() (with no file name) is equivalent to io.input():lines(); that is, it iterates over the lines of the default input file. In this case it does not close the file when the loop ends. 

--------------------------------------------------------------------------------
（注：TODO）

io.lines ([filename])
以读取模式打开给定的文件名并返回一个迭代函数，每次调用会返回来自文件的新行。因此，结构 

     for line in io.lines(filename) do body end

会迭代文件的所有行。当迭代函数监测到文件结尾时，返回nil（以结束循环）并自动关闭文件。 

调用io.lines()（不带参数）等价于io.input():lines()；即迭代缺省输入文件的所有行。该情况下当循环结束时不会关闭文件。 



--------------------------------------------------------------------------------

io.open (filename [, mode])
This function opens a file, in the mode specified in the string mode. It returns a new file handle, or, in case of errors, nil plus an error message. 

The mode string can be any of the following: 

"r": read mode (the default); 
"w": write mode; 
"a": append mode; 
"r+": update mode, all previous data is preserved; 
"w+": update mode, all previous data is erased; 
"a+": append update mode, previous data is preserved, writing is only allowed at the end of file. 
The mode string can also have a 'b' at the end, which is needed in some systems to open the file in binary mode. This string is exactly what is used in the standard C function fopen. 


--------------------------------------------------------------------------------

io.open (filename [, mode])
这个函数以字符串mode所指定的模式打开一个文件。
它返回一个新的文件句柄，如果出错则返回nil外加一个错误信息。
模式字符串可以是下列任意：
"r": 读模式（默认）； 
"w": 写模式；
"a": 追加模式；
"r+": 更新模式，所有以前的数据被保留；
"w+": 更新模式，所有以前的数据被删除； 
"a+": 追加更新模式，以前的数据被保留,只允许在文件结尾写. 
模式字符串可以在结束加上'b'，某些系统需要用二进制模式打开文件。
这个字符串正好就是标准C函数fopen所用的。


--------------------------------------------------------------------------------

io.output ([file])
Similar to io.input, but operates over the default output file. 


--------------------------------------------------------------------------------

io.output ([file])
类似于io.input，但操作在默认的输出文件。


--------------------------------------------------------------------------------

io.popen (prog [, mode])
Starts program prog in a separated process and returns a file handle that you can use to read data from this program (if mode is "r", the default) or to write data to this program (if mode is "w"). 

This function is system dependent and is not available on all platforms. 

--------------------------------------------------------------------------------

io.popen (prog [, mode])
以一个单独地进程启动程序prog并且返回一个文件句柄，你可以用它从这个程序中读取数据
（如果模式为"r"，缺省下），或者写数据到程序（如果模式为"w"）。
这个函数依赖于系统，不是所有平台都可用。

--------------------------------------------------------------------------------

io.read (···)
Equivalent to io.input():read. 

--------------------------------------------------------------------------------

io.read (···)
等效于io.input():read. 


--------------------------------------------------------------------------------

io.tmpfile ()
Returns a handle for a temporary file. This file is opened in update mode and it is automatically removed when the program ends. 

--------------------------------------------------------------------------------

io.tmpfile ()
返回一个临时文件的句柄。
这个文件以update模式打开并且在程序结束时自动删除。


--------------------------------------------------------------------------------

io.type (obj)
Checks whether obj is a valid file handle. Returns the string "file" if obj is an open file handle, "closed file" if obj is a closed file handle, or nil if obj is not a file handle. 

--------------------------------------------------------------------------------

io.type (obj)
检查obj是否合法的文件句柄。
如果obj是一个打开的文件句柄则返回字符串"file"，如果obj是关闭的文件句柄则返回"closed file"，如果obj不是一个文件句柄则返回nil。


--------------------------------------------------------------------------------

io.write (···)
Equivalent to io.output():write. 

--------------------------------------------------------------------------------

io.write (···)
等效于io.output():write. 


--------------------------------------------------------------------------------

file:close ()
Closes file. Note that files are automatically closed when their handles are garbage collected, but that takes an unpredictable amount of time to happen. 


--------------------------------------------------------------------------------

file:close ()
关闭文件。
注意当他们的句柄被垃圾回收时，文件会被自动关闭，但这会花费一段不可预测的时间才会发生。

--------------------------------------------------------------------------------

file:flush ()
Saves any written data to file. 

--------------------------------------------------------------------------------

file:flush ()
保存所有写的数据到文件。

--------------------------------------------------------------------------------

file:lines ()
Returns an iterator function that, each time it is called, returns a new line from the file. Therefore, the construction 

     for line in file:lines() do body end

will iterate over all lines of the file. (Unlike io.lines, this function does not close the file when the loop ends.) 

--------------------------------------------------------------------------------
file:lines ()
返回一个迭代器函数，每次调用它，返回文件的一个新行。
因此，结构
for line in file:lines() do body end
将迭代文件的所有行。
（不同于io.lines，这个函数不会在循环结束时关闭文件）


--------------------------------------------------------------------------------

file:read (···)
Reads the file file, according to the given formats, which specify what to read. For each format, the function returns a string (or a number) with the characters read, or nil if it cannot read data with the specified format. When called without formats, it uses a default format that reads the entire next line (see below). 

The available formats are 

"*n": reads a number; this is the only format that returns a number instead of a string. 
"*a": reads the whole file, starting at the current position. On end of file, it returns the empty string. 
"*l": reads the next line (skipping the end of line), returning nil on end of file. This is the default format. 
number: reads a string with up to this number of characters, returning nil on end of file. If number is zero, it reads nothing and returns an empty string, or nil on end of file. 

--------------------------------------------------------------------------------
（注：TODO）

file:read (···)
依照给定格式读取文件file，该格式制定要读取什么。对于每种格式，该函数返回读取得字符串（或数字），或者如果不能读取指定格式的数据则返回nil。当不带格式调用时，它用读取整个下一行的缺省格式（见下面）。 

可用格式是 

"*n": 读取一个数字；这是返回数字而非字符串的唯一格式。 
"*a": 从当前位置开始读取整个文件。在文件结尾时返回空串。 
"*l": 读取下一行（跳过行尾），在文件结尾时返回nil。这是缺省格式。 
number: 读取字符串直到该数量的字符串，在文件结尾时返回nil。如果数字是0，它什么也不读并返回空串，或在文件结尾时返回nil。 


--------------------------------------------------------------------------------

file:seek ([whence] [, offset])
Sets and gets the file position, measured from the beginning of the file, to the position given by offset plus a base specified by the string whence, as follows: 

"set": base is position 0 (beginning of the file); 
"cur": base is current position; 
"end": base is end of file; 
In case of success, function seek returns the final file position, measured in bytes from the beginning of the file. If this function fails, it returns nil, plus a string describing the error. 

The default value for whence is "cur", and for offset is 0. Therefore, the call file:seek() returns the current file position, without changing it; the call file:seek("set") sets the position to the beginning of the file (and returns 0); and the call file:seek("end") sets the position to the end of the file, and returns its size. 

--------------------------------------------------------------------------------
（注：TODO）

file:seek ([whence] [, offset])
设置和获取由文件开头开始量度的文件位置，目标位置由offset加字符串whence指定的基点给出。如下： 

"set": 基点是位置0（文件开头）； 
"cur": 基点是当前位置； 
"end": 基点是文件结尾； 
成功返回由文件开头开始亮度以字节为单位的最终文件位置。失败则返回nil以及一个描述错误的字符串。 

whence的缺省值是"cur"，offset是0。因此调用file:seek()返回当前文件位置而不改变它；调用file:seek("set")设置位置到文件开头（并返回0）；调用file:seek("end")设置位置到文件结尾，并返回其尺寸。 




--------------------------------------------------------------------------------

file:setvbuf (mode [, size])
Sets the buffering mode for an output file. There are three available modes: 

"no": no buffering; the result of any output operation appears immediately. 
"full": full buffering; output operation is performed only when the buffer is full (or when you explicitly flush the file (see io.flush)). 
"line": line buffering; output is buffered until a newline is output or there is any input from some special files (such as a terminal device). 
For the last two cases, size specifies the size of the buffer, in bytes. The default is an appropriate size. 

--------------------------------------------------------------------------------
（注：TODO）

file:setvbuf (mode [, size])
设置输出文件的缓冲模式。有三种可用模式： 

"no": 无缓冲；任何输出操作的结果立刻出现。 
"full": 完全缓冲；只在缓冲区满时（或者当你显式flush文件（见io.flush））执行输出操作。 
"line": 行缓冲；缓冲输出直到输出了换行或有来自某些特定文件（例如终端设备）的任何输入。 
对于最后两种情况，size指定缓冲区大小，以字节为单位。缺省是个适当的大小。 




--------------------------------------------------------------------------------

file:write (···)
Writes the value of each of its arguments to the file. The arguments must be strings or numbers. To write other values, use tostring or string.format before write. 

--------------------------------------------------------------------------------
（注：TODO）

file:write (···)
将每个参数的值写入file。参数必须是字符串或数字。要写其他值，在write之前使用tostring或string.format。 





5.8 - Operating System Facilities
This library is implemented through table os. 


5.8 - 操作系统工具
这个库通过表os实现

--------------------------------------------------------------------------------

os.clock ()
Returns an approximation of the amount in seconds of CPU time used by the program. 

--------------------------------------------------------------------------------

os.clock ()
返回一个程序所用CPU时间秒数的近似值。


--------------------------------------------------------------------------------

os.date ([format [, time]])
Returns a string or a table containing date and time, formatted according to the given string format. 

If the time argument is present, this is the time to be formatted (see the os.time function for a description of this value). Otherwise, date formats the current time. 

If format starts with '!', then the date is formatted in Coordinated Universal Time. After this optional character, if format is the string "*t", then date returns a table with the following fields: year (four digits), month (1--12), day (1--31), hour (0--23), min (0--59), sec (0--61), wday (weekday, Sunday is 1), yday (day of the year), and isdst (daylight saving flag, a boolean). 

If format is not "*t", then date returns the date as a string, formatted according to the same rules as the C function strftime. 

When called without arguments, date returns a reasonable date and time representation that depends on the host system and on the current locale (that is, os.date() is equivalent to os.date("%c")). 

--------------------------------------------------------------------------------

os.date ([format [, time]])
返回一个包含日期和时间的字符串或表，根据所给字符串格式进行格式化。
如果参数time给定，这就是要被格式化的时间（参考os.time函数关于这个值的描述）。
否则，date格式化当前时间。
如果格式以'!'开始，那么日期以协调世界时（注：即UTC）方式格式化。
在这个可选字符后面，如果格式是字符串"*t"，那么日期返回带有以下域的一个表：
year（四位数字），month（1到12）,day（1到31）,hour（0到23），min（0到59），sec（0到61），wday（星期，星期日为1），yday（一年内第几日），以及isdst（夏令时标志，一个布尔型）
如果格式不是"*t"，那么date返回一个和C函数strftime相同的规则格式化的日期字符串，
当不带参数调用，date返回一个含义依赖于宿主系统和在当前语言环境的合理日期和时间（即，os.date()等效于os.date("%c")）。


--------------------------------------------------------------------------------

os.difftime (t2, t1)
Returns the number of seconds from time t1 to time t2. In POSIX, Windows, and some other systems, this value is exactly t2-t1. 


--------------------------------------------------------------------------------

os.difftime (t2, t1)
返回从事件t1到时间t2的秒数。
在POSIX，Windows，和一些其它系统，这个值就是t2-t1。

--------------------------------------------------------------------------------

os.execute ([command])
This function is equivalent to the C function system. It passes command to be executed by an operating system shell. It returns a status code, which is system-dependent. If command is absent, then it returns nonzero if a shell is available and zero otherwise. 

--------------------------------------------------------------------------------

os.execute ([command])
这个函数等效于C函数system。
它传递command被操作系统shell执行。
它返回一个依赖系统的状态值。
如果没有指定command，它在shell可用时返回非0值，否则返回0。


--------------------------------------------------------------------------------

os.exit ([code])
Calls the C function exit, with an optional code, to terminate the host program. The default value for code is the success code. 

--------------------------------------------------------------------------------

os.exit ([code])
调用C函数exit，带有一个可选的code，以结束宿主程序。code的默认值是表示成功的代码。

--------------------------------------------------------------------------------

os.getenv (varname)
Returns the value of the process environment variable varname, or nil if the variable is not defined. 

--------------------------------------------------------------------------------

os.getenv (varname)
Returns the value of the process environment variable varname, or nil if the variable is not defined. 
返回进程环境变量变量varname的值，如果这个变量没有定义则返回nil。

--------------------------------------------------------------------------------

os.remove (filename)
Deletes the file or directory with the given name. Directories must be empty to be removed. If this function fails, it returns nil, plus a string describing the error. 

--------------------------------------------------------------------------------

os.remove (filename)
删除所给名称的文件或目录。
目录必须为空才可删除。
如果这个函数失败。它返回nil，外加一个描述错误的字符串。


--------------------------------------------------------------------------------

os.rename (oldname, newname)
Renames file or directory named oldname to newname. If this function fails, it returns nil, plus a string describing the error. 

--------------------------------------------------------------------------------

os.rename (oldname, newname)
重命名文件或目录的名称oldname为newname。
如果这个函数失败，它返回nil，外加一个描述错误的字符串。


--------------------------------------------------------------------------------

os.setlocale (locale [, category])
Sets the current locale of the program. locale is a string specifying a locale; category is an optional string describing which category to change: "all", "collate", "ctype", "monetary", "numeric", or "time"; the default category is "all". The function returns the name of the new locale, or nil if the request cannot be honored. 

If locale is the empty string, the current locale is set to an implementation-defined native locale. If locale is the string "C", the current locale is set to the standard C locale. 

When called with nil as the first argument, this function only returns the name of the current locale for the given category. 

--------------------------------------------------------------------------------

os.setlocale (locale [, category])
设置当前程序的本地环境。
locale是一个指定本地环境的字符串；category是一个描述哪些分类被改变的可选字符串："all", "collate", "ctype", "monetary", "numeric", or "time"；
缺省分类为"all"。
函数返回新的本地环境的名称，如果请求未被兑现则返回nil。
如果本地环境是空字符串，当前本地环境被设置为一种由实现定义的原生本地环境。
如果local是字符串"C"，当前本地环境设置为标准C本地环境。
当第一参数为nil调用时，这个函数只会返回所给分类下的当前本地环境的名称。


--------------------------------------------------------------------------------

os.time ([table])
Returns the current time when called without arguments, or a time representing the date and time specified by the given table. This table must have fields year, month, and day, and may have fields hour, min, sec, and isdst (for a description of these fields, see the os.date function). 

The returned value is a number, whose meaning depends on your system. In POSIX, Windows, and some other systems, this number counts the number of seconds since some given start time (the "epoch"). In other systems, the meaning is not specified, and the number returned by time can be used only as an argument to date and difftime. 

--------------------------------------------------------------------------------

os.time ([table])
当不带参数时调用则返回当前时间，否则是一个被所给表指定的代表日期和时间一个时间值。
这个表必须拥有域year, month, 和day, 以及可能有的域hour, min, sec 以及isdst（对于这些域的描述，参考os.date函数）。
返回值是一个数，其含义依赖于你的系统。
在POSIX, Windows, 以及其它一些系统，这个数是从某个给定的开始时间算起的秒数（“纪元”）。在其它系统，这个意思并不规范，并且返回的表示时间的数只是作为一个传给date和difftime的参数。


--------------------------------------------------------------------------------

os.tmpname ()
Returns a string with a file name that can be used for a temporary file. The file must be explicitly opened before its use and explicitly removed when no longer needed. 

On some systems (POSIX), this function also creates a file with that name, to avoid security risks. (Someone else might create the file with wrong permissions in the time between getting the name and creating the file.) You still have to open the file to use it and to remove it (even if you do not use it). 

When possible, you may prefer to use io.tmpfile, which automatically removes the file when the program ends. 


--------------------------------------------------------------------------------

os.tmpname ()
返回一个带文件名的字符串，可用于一个临时文件。
文件必须在使用前显式打开并且在不再需要的时候显式删除。
在一些系统（POSIX），这个函数还创建一个带有那个名称的文件，以避免安全风险。
（其它人可能在获得名称和创建文件之间的时间内创建带错误权限的文件。）
你仍必须打开该文件以使用它并且删除它（就算你没有用它）。
如果可以用，你可能更倾向于用io.tmpfile，它会在程序结束的时候自动删除临时文件。

