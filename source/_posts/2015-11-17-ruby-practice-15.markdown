---
layout: post
title: "Ruby修炼之道(15)"
date: 2015-11-17 16:15:17 +0800
comments: true
categories: [Ruby]
---

#####字符串比较
Ruby中比较字符串是否相同也是用**==**和**!=**等运算符

``` ruby
#whether two strings are equal
puts "aaa" == "aab" 		#=> false
puts "aaa" != "aab"			#=> true
```
如果是判断两字符串是否相似，采用正则表达式匹配更加简单。

``` ruby
#whether two strings are similar
reg = Regexp.new("^a+b$")
p "aaa" =~ reg 			#=> nil (not similar)
p "ab" =~ reg 			#=> 0 (similar)
p "aab" =~ reg 			#=> 0 (similar)
p "acb" =~ reg 			#=> nil (not similar)
```
<!-- more -->
字符串大小由字符编码的顺序决定，可以通过调用**String#ord**方法获取字符编码的码位。

``` ruby 
#compare two strings, which is bigger
puts "aaa" > "aab"		#=> false
puts "aaa" <= "aab"		#=> true
```
*注：Windows平台下的中文字符使用GBK编码，但非Windows平台下的中文字符使用UTF-8编码*

#####字符串的分割
用特定字符分割字符串时可以使用**split**方法。

``` ruby
#split string with a special character
str = "This is a string"
p str.split(" ") 		#=> ["This", "is", "a", "string"]
```

#####字符串的换行符
用**each_line**方法从标准输入读取字符串时，字符串后面肯定有换行符。在操作字符串时，我们可能不需要换行符。此时可以使用**chomp**和**chomp!**方法来删除字符串行末的换行符。

``` ruby
#delete the line break character from a string
str = "hello ruby\n"
p str  					#=> "hello ruby\n"
p str.chomp 			#=> "hello ruby"
p str.chomp!			#=> "hello ruby"
p str 					#=> "hello ruby"
```
如果说只想移除字符串行末的最后一个任意字符，可以通过是用**chop**和**chop!**方法来实现

``` ruby
#delete the last character from a string
str = "hello ruby"
puts str.chop			#=> hello rub
puts str 				#=> hello ruby
puts str.chop!			#=> hello rub
puts str				#=> hello rub
```
#####字符串的索引
使用**index**和**rindex**方法可以获取某个字符串在另一个字符串中得索引位置，如果不在，则返回nil。**index**方法从左向右检索，**rindex**从右向左检索。

``` ruby
#get the index which a string in another string
str1 = "aaabbbbbb"
str2 = "bb"

puts str1.index(str2)   #=> 3
puts str1.rindex(str2)  #=> 7
```
如果只想判断某个字符串中是否包含另一个字符串时，使用**include?**方法更佳。

``` ruby
#a string whether contains another string
str1 = "abc"
str2 = "ab"
puts str1.include?(str2) #=> true
```
#####字符串的置换
字符串的置换可以通过调用**sub**和**gsub**来实现。**sub**方法仅替换第一个出现的匹配字符，而**gsub**则会替换所有的匹配字符。

``` ruby
#the replacing of string
puts "hello".sub('l', '*')		#=> he*lo
puts "hello".gsub('l', '*')		#=> he**o
```
字符串的置换还可以通过像替换数组中元素的方式来实现，即索引

``` ruby
str = "hello"

str[2] = '*'
puts str 						#=> he*lo

str[2..3] = '**'       
puts str      					#=> he**o

str[1, 3]= '***'  		
puts str 						#=> h***o
```

#####字符串与数组相同的方法
字符串的很多方法都与数组相同，主要分为以下三大类：    
- 与索引操作相关的方法   
- 与**Enumerable**模块相关的方法    
- 与连接、反转相关的方法    

删除字符串中的一部分字符并返回，可使用**slice**方法    

**slice(n)**    
**slice(n..m)**    
**slice(n, m)**    
**slice!(n)**    
**slice!(n..m)**
**slice!(n, m)**

``` ruby
#the slice of string
str = "hello ruby"
puts str.slice(1)				#=> e
puts str.slice(1..3)			#=> ell
puts str.slice(2, 3)			#=> llo
```
不带块的情况下，大部分原生的迭代器在调用时都会返回**Enumberable**对象。因此我们可以对**each_line**, **each_byte**， **each_char**等方法的返回值继续使用像**map**， **collect**等方法。

``` ruby
#the enumerable of string
str = <<EOB
hello
ruby
EOB

p str 				#=> "hello\nruby\n"
#return enumerable
p str.each_line 				#=> #<Enumerator: "hello\nruby\n":each_line>

p str.each_line.map { |e| e.chomp  }   #=> ["hello", "ruby"]

p str.each_line.collect { |e| e.chomp.upcase} #=> ["HELLO", "RUBY"]
```

**delete(str)**    
**delete!(str)**   
删除字符串中指定的字符串，并返回删除后的字符

``` ruby
#delete the specific string from another string
str = "hello\n ruby\n"
p str.delete("\n")      #=> "hello ruby"
p str 					#=> "hello\n ruby\n"

p str.delete!("\n")		#=> "hello ruby"
p str 					#=> "hello ruby"
```

**reverse(str)**    
**reverse!(str)**    
反转字符串，包括破坏方法与非破坏方法

``` ruby
#reverse a string
str = "hello ruby"
p str.reverse 			#=> "ybur olleh"
```

**str.strip**    
**str.strip!**    
删除字符串str中行首和行尾的空白字符    

``` ruby
#remove the white space at the begin and end of string
str = " hello ruby "
p str 					#=> " hello ruby"
p str.strip 		    #=> "hello ruby"
```

**str.upcase/str.upcase!**    
**str.downcase/str.downcase!**    
**str.swapcase/str.swapcase!**    
**str.capitalize/str.capitalize!**   
字符串大小写的转换系列方法, **swapcase**方法是将字符串中的大小写相互转换。而**capitalize**方法则是将字符串的首个字符大写，其余的均改为小写    

``` ruby
#text transform
str = "Hello RUBY"
p str.upcase 			#=> "HELLO RUBY"
p str.downcase			#=> "hello ruby"
p str.swapcase          #=> "hELLO ruby"
p str.capitalize		#=> "Hello ruby"
```


