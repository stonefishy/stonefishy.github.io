---

title: "Ruby修炼之道(14)"
date: 2015-11-12 17:09:35 +0800
comments: true
categories: Backend
tags: [Ruby]
---

#### 字符串的创建
##### 普通创建
Ruby中使用**" "**或**' '**符号来创建字符串，在字符串中也可以包含表达式**#{}**，这个表达式被称之为内嵌表达式。如果字符串中需要包含""或者''等字符时，则可以通过转义字符\来实现。

``` ruby 
str = "this is a string"
puts str

str = 'this is also a string'
puts str

args = "string with argument"
puts "this is a #{args}"

str = "Hello 'string'"
puts str

str = "Hello \"string\""
puts str
```
<!-- more -->

##### %Q或%q创建
针对字符串中包含""或''等字符的，建议采用**%Q**或者**%q**来创建字符串，这样更加清晰明了。使用**%Q**相当于**" "**创建字符串，**%q**相当于**' '**创建字符.

``` ruby 
puts %Q(this string created by %Q)

puts %q(this string created by %q)

puts %Q(contains 'special' "character" directly)

puts %q(contains 'special' "character" directly)
```
##### Here Document创建，
对于字符串中包含换行符的字符串，使用Here Document语法来创建极为方便。Here Document是Unix中使用Shell的一种语法，通常用**<<**来创建字符串。其语法结构如下：

	<<"结束标识符"
	字符串内容
	结束标识符
*注意：结束标识符可以使用" "或' '来定义，使用" "时，字符串内可以使用转义字符和内嵌表达式，而' '则不会，只会原封不动的显示所有字符。*   

一般使用*EOF(End of File)*或*EOB(End of Block)*等作为结束标识符。

``` ruby
str = <<EOB
the first string
the second string in a new line
ok, I also get the third string
EOB

print str		#=>the string has three lines, just like defined.
```
如果字符串中存在缩进，可以使用**<<-**来替代**<<**，这样将会保持字符串定义中的缩进。

##### 使用` `

通过使用**\`命令\`**, 可以创建命令的标准输出并将其转换为字符串对象。

``` ruby
str = `ls -l`

puts str 		
#=> 
total 8
-rw-r--r--  1 yushi  staff  634 Nov 16 17:12 string_create.rb
```

##### 使用printf和sprintf
**printf**与**sprintf**均是可以输出指定格式的字符串，不同之处在于printf会将字符串直接输出在控制台上，而sprintf则会将字符串转为为字符串对象。

``` ruby
number = 123
printf("%d\n", number)		#=> 123
printf("%4d\n", number)		#=>  123
printf("%04d\n", number)    #=> 0123
printf("%+d\n", number)		#=> +123

name = "Ruby"
p sprintf("Hello,%s", name)		#=> "Hello,Ruby"
p sprintf("Hello,%8s", name)		#=> "Hello,    Ruby"
p sprintf("Hello,%-8s", name)		#=> "Hello,Ruby    "
```

#### 字符串长度
用**length**方法和**size**方法获取字符串长度。**bytesize**方法可以获取字节长度。

``` ruby
str = "This is a string"
str2 = "字符串"

#get the length of a string object
puts str.length			#=> 16
puts str.size			#=> 16
puts str2.length		#=> 3

#get the bytesize of a string 
puts str.bytesize		#=> 16
puts str2.bytesize		#=> 9
```

判断字符串是否为空，可用**empty?**方法。

``` ruby
#judge the string if is empty
puts "".empty?			#=> true
puts "string".empty?	#=> false
```

*字符串的索引与数组的索引用法一样，可参照使用*

##### 字符串的连接
字符串的连接有以下两种情况：   
- 将字符串合并为一个新的字符串   
- 扩展原有字符串

使用**+**可以合并两个字符串时，会创建一个新的字符串。

``` ruby
#using '+', will create a new string object
str1 = "hello "
str2 = "world"
str3 = str1

puts str1 + str2 			#=> hello world
puts str1					#=> hello

puts str1 = str1 + str2 	#=> hello world
puts str1					#=> hello world
puts str3					#=> hello
```

如果只想扩展原有字符串，可以使用**<<**或**concat**方法。

``` ruby
#using '<<', will change the original string object
str1 = "hello "
str2 = "world"
str3 = str1

puts str1<<str2				#=> hello world
puts str1					#=> hello world
puts str3					#=> hello world

#using 'concat' method, also will change the original string object
str1 = "hello "
str2 = "world"
str3 = str1

puts str1.concat str2		#=> hello world
puts str1					#=> hello world
puts str3					#=> hello world
```
*注意：一般情况下使用**<<**和**concat**方法更有效率，但是也可以根据情况而定*










