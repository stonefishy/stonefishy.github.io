---
layout: post
title: "Ruby修炼之道(17)"
date: 2015-12-17 09:46:29 +0800
comments: true
categories: [Ruby]
---
####正则表达式(Regexp)
Ruby中的正则表达式用**Regexp**类来表示，正则表达式描述的是一种模式，该模式用于匹配字符串。

#####创建
其创建方式有多种：
1.使用**/ /** 把字符串括起来，可以简单地创建出正则表达式

``` ruby
reg1 = /Ruby/
puts reg1.class       #=> Regexp
```
<!-- more -->

2.使用**Regexp.new("字符串")**创建正则表达式

``` ruby
reg = Regexp.new("Ruby")
puts reg.class       #=> Regexp
```

3.使用**%r**创建正则表达式

``` ruby
reg = %r(Ruby)
puts reg.class       #=> Regexp

reg = %r<Ruby>
puts reg.class       #=> Regexp

reg = %r|Ruby|
puts reg.class       #=> Regexp

reg = %r!Ruby!
puts reg.class       #=> Regexp
```

#####匹配
Ruby的正则表达式匹配使用**=~**符号，如果匹配成功返回匹配字符串的位置，否则返回nil。

``` ruby
reg = /R..y/
str = "regexp of Ruby"

matched = reg =~ str
puts matched        #=> 10

unmatched = reg =~ "test"
p unmatched         #=> nil

if matched
  puts "matched"
else
  puts "unmatched"
end
```
此处并不过多的介绍正则表达式的语法规则，具体详细语法请参见[http://regexlib.com/CheatSheet.aspx](http://regexlib.com/CheatSheet.aspx)

#####转义
对特殊字符的转义方式有两种，使用**\\**字符和使用**Regexp#quote**方法

``` ruby
#using \ symbol
reg = /ruby\*regexp/
puts reg =~ "ruby*regexp"     #=> 0
p reg =~ "rubyregexp"          #=> nil

#using Regexp#quote method
reg1 = %r(ruby*regexp)
reg2 = Regexp.new(Regexp.quote("ruby*regexp"))
str = "ruby*regexp"

p reg1 =~ str         #=> nil
p reg2 =~ str         #=> 0
```

#####选项
在正则表达式的后面添加特定的字符如**i**, **x**, **m**等时，匹配后的规则会有点变化
    
**i** 	Regexp::IGNORECASE  忽略字符串大小写    
**x**	Regexp::EXTENDED		忽略字符串中的空白字符     
**m**	Regexp::MULTILINE	匹配多行字符串    

``` ruby
#using i optional symbol to ignore the cap
reg1 = /test String/i
reg2 = Regexp.new("test string", Regexp::IGNORECASE)
puts reg1 =~ "test string"     #=> 0
puts reg1 =~ "test string"     #=> 0

#using x optional symbol to ignore the white space symbol
reg1 = /test string/x
reg2 = Regexp.new("test string", Regexp::EXTENDED)
puts reg1 =~ "teststring"      #=> 0
puts reg1 =~ "teststring"      #=> 0

#using m optional symbol to match multiline
reg1 = /test\nstring/m
reg2 = Regexp.new("test\nstring", Regexp::MULTILINE)
puts reg1 =~ "test\nstring"
puts reg1 =~ "test\nstring"      #=> 0
```

#####捕获
捕获，就是从正则表达式的匹配部分中提取某部分字符，通过"**$数字**"这种形式的变量获取匹配了正则表达式中的用**()**括住的字符。

``` ruby
reg = /(.)(C.)(.)/
str = "ABCDE"
reg =~ str

puts $1   #=> B
puts $2   #=> CD
puts $3   #=> E
```

使用"**(?: )**"可以过滤掉不需要捕获的模式.

``` ruby
reg = /(.)(?:C.)(.)/
str = "ABCDE"
reg =~ str

puts $1   #=> B
puts $2   #=> E
```

Ruby中也可以使用**$`**, **$&**, **$'**分别代表匹配部分前的字符串、匹配部分的字符串、匹配部分后的字符串。

``` ruby
reg = /(C.)/
str = "ABCDE"
reg =~ str

puts $`     #=> AB
puts $&     #=> CD
puts $'     #=> E
```

#####常用方法
**sub**和**gsub**方法的作用是用指定的字符置换字符串中的某部分字符。**sub**方法只置换首次匹配的部分，而**gsub**则会置换所有匹配的部分.

``` ruby
reg = /\s+/
str = "This is test string"

puts str.sub(reg, '-')   #=> "This-is test string"
puts str.gsub(reg, '-')   #=> "This-is-test-string"
```

sub和gsub方法同样支持block块模式，针对匹配到的字符做处理

``` ruby
reg =/i/
str = "This is test string"

result = str.sub(reg) do |matched|
  matched.upcase
end

puts result     #=> ThIs is test string

result = str.gsub(reg) do |matched|
  matched.upcase
end

puts result     #=> ThIs Is test strIng
```

**scan**方法和**gsub**方法类似，都可以获取到所有匹配到的字符，但是不能置换。

``` ruby 
reg = /(r)(.)/
str = "racrtrhrdra"
str.scan(reg) do |matched|
  p matched
end

#["r", "a"]
#["r", "t"]
#["r", "h"]
#["r", "d"]
#["r", "a"]

str.scan(reg) do |val1, val2|
  p val1 + "-" + val2
end

#"r-a"
#"r-t"
#"r-h"
#"r-d"
#"r-a"
```


