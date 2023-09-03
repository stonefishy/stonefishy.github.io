---

title: "Ruby修炼之道(18)"
date: 2015-12-28 10:59:48 +0800
comments: true
categories: Backend
tags: [Ruby]
---

#### IO
Ruby程序在启动后会预先分配3个**IO**对象:    
标准输入 - 预定义常量(**STDIN**), 全局变量(**\$stdin**)        
标准输出 - 预定义常量(**STDOUT**), 全局变量(**\$stdout**)   
标准错误输出 - 预定义常量(**STDERR**), 全局变量(**\$stderr**)


``` ruby
$stdout.print "output something from $stdout\n"
STDERR.print "output something from $stderr\n"
```

IO对象是否与控制台关联，我们可以通过**tty?**方法来判断。

``` ruby
if $stdin.tty?
  print "stdin is a tty"
else
  print "stdin is not a tty"
end
```
<!-- more -->
##### 文件操作
通过File.open方法或open方法打开文件并获取新的IO对象，

	io = File.open(file, mode)
	io = open(file, mode)

mode为文件的读写方法，缺省情况下为只读模式(r).

r  - 只读模式    
r+ - 读写模式   
w  - 只写模式，如果文件不存在则创建新文件，如果文件存在，则清空文件   
w+ - 读写模式，其余同w    
a  - 追加模式，文件不存在则创建新文件    
a+ - 读取、追加模式，文件不存在则创建新文件    

io.**close**
使用**close**方法关闭已打开的文件。File.open方法使用块，则文件会在使用完毕后自动关闭，io变量会被作为块变量传递给块。并且使用io.closed?方法可以检查是否关闭。

``` ruby
File.open("tty.rb") do |io|
  while line = io.gets
    print line
  end
end

```

使用**File.read**可以一次性读取文件的内容。

``` ruby
data = File.read("tty.rb")
```

##### 基本输入
io.**gets**(rs)   
io.**each**(rs)   
io.**each_line**(rs)   
io.**readlines**(rs)   
从IO类的对象中读取一行数据，用参数**rs**的字符串分行，缺省模式下为换行符。

使用**eof?**可以检查输入是否已经完毕。

``` ruby
io = File.open("tty.rb")
while line = io.gets
  line.chomp!
  print line
end
p io.eof?     #=> true
io.close      #=> close file
p io.closed?   #=> true

```

用**readlines**方法可以一次性读取所有数据，并返回将每行数据作为元素封装的数组.

``` ruby
io = File.open("tty.rb")
ary = io.readlines
io.close
ary.each do |line|
  line.chomp!
  print line
end
```

io.**lineno**   
使用gets方法，each_line方法逐行读取文件时，io会自动记录读取的行数，可以通过**io.lineno**来获取当前行数。   

io.**each_char**    
逐个字符读取io对象中的数据,并启动块。   

io.**each_byte** 
逐个字节读取io对象中的数据，并启动块。    

io.**getc**    
只读取io对象中的一个字符。
io.**ungetc(ch)**    
将参数ch指定的字符退回到io得缓冲流中   

``` ruby
File.open("hello.txt", 'w') do |io|
  io.write("Hello Ruby")
end
File.open("hello.txt") do |io|
  p io.getc       #=> H
  io.ungetc('H')
  p io.gets       #=> Hello Ruby
end

File.delete("hello.txt")
```

io.**getbyte**, io.**ungetbyte**的使用方法同上，不同之处在于此处是字节。    

io.**read**(size)    
读取参数size指定大小的数据，如果没指定，则一次性读取所有数据。    

##### 输出操作
io.**puts**(str0, str1, ...)    
指定多个字符串时，会在每个字符串后面添加换行符。   

``` ruby
$stdout.puts "hi", "hello", "hey"			#=> hi\nhello\nhey\n
```

io.**putc**(ch)    
输出指定参数ch字符，如果参数为字符串，则输出首个字符    

``` ruby
$stdout.putc('H')       #=> H
$stdout.putc('Hello')   #=> H
$stdout.putc('\n')
```

io.**print**(str0, str1, ...)   
输出参数指定的字符串，不会在每个字符串后面追加换行符    

io.**printf**(fmt, arg0, arg1, ...)    
按照指定格式输出字符串。    

io.**write**(str)    
输出参数str指定的字符串，方法返回值为输出的字节数    

``` ruby
size = $stdout.write("Hello")		#=> Hello
p size			#=> 5
```

##### 文件指针
用文件指针或者当前文件偏移量来表示IO对象指向文件的位置。    

io.**pos**   
io.**pos**=position    
通过**pos**方法获取文件指针位置，改变文件指针使用**pos=**方法。

``` ruby
File.open("hello.txt", 'w') do |io|
  io.write("Hello Ruby")
end
File.open("hello.txt") do |io|
  p io.read(5)      #=> Hello
  io.pos = 0
  p io.gets         #=> Hello Ruby
end
```

io.**seek**(offset, whence)   
offset为指定位置的整数，whence为指定offset如何移动， 以下为whence的值:     
IO::SEEK_SET - 将文件指针移动到指定offset指定的位置    
IO::SEEK_CUR - 将offset视为相对于当前位置的偏移位置来移动文件指针    
IO::SEEK_END - 将offset视为相对于文件末尾的偏移位置  

``` ruby
File.open("hello.txt", 'w') do |io|
  io.write("Hello Ruby")
end
File.open("hello.txt") do |io|
  p io.read(5)      #=> Hello
  io.seek(-5, IO::SEEK_CUR)
  p io.gets         #=> Hello Ruby
  io.seek(0, IO::SEEK_SET)
  p io.gets         #=> Hello Ruby
  io.seek(-4, IO::SEEK_END)
  p io.gets         #=> Ruby
end
```

io.**rewind**   
将文件指针重置到文件开始处，结果同以下两个操作:    

	io.pos = 0   
	io.seek(0, IO::SEEK_SET)
	
io.**truncate**(size)    
按照参数size指定的大小截断文件。

*注，新的IO对象默认为文本模式，可以通过io.**binmode**将其转为二进制模式*   

##### 缓冲
IO对象在输出数据时，结果并不一定会马上输出出来，程序会在内部开辟一个缓冲空间来缓存输出，当空间满的时候再输出来。但是标准错误输出不会使用缓冲，如果有错误信息，则立刻输出。所以标准错误输出一般用于输出警告、错误之类的信息，能够立刻输出数据    

io.**flush**    
强制输出缓冲中的数据。

io.**sync**    
io.**sync**=state   
通过io.sync = true, 程序写入缓冲时flush方法会被自动调用。    

##### 与命令行交互
IO.**popen**(command, mode)   
command为相关的命令    

``` ruby
pattern = Regexp.new(ARGV[0])
filename = ARGV[1]

if /.gz$/ =~ filename
  file = IO.popen("gunzip -c #{filename}")
else
  file = File.open(filename)
end
file.close
```    

**open**("|command", mode)   
将带有管道符号的命令传递给open方法的效果与使用IO.popen方法是一样的

``` ruby
filename = ARGV[0]
open("|gunzip -c #{filename}") do |io|
  io.each_line do |line|
    print line
  end
end
```


  


