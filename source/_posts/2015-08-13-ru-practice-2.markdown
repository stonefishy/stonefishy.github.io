---
layout: post
title: "Ruby修炼之道(2)"
date: 2015-08-13 13:35:15 +0800
comments: true
categories: [Ruby] 
---

###数组

Ruby中数组的大小是按实际情况自动调整的，即所谓的**动态数组**

``` ruby
>> rubyArray = ["data1", "data2"]   ##定义两个数据的数组
>> rubyArray[2] = "data3"           ##新增第三个数据
>> print rubyArray                  ##打印["data1", "data2", "data3"]
```
Ruby数组还支持多种不同对象的混合保存, 通过**数组.size**可获取数组的大小

``` ruby
>> mixedArray = [1, "data", 2, "time", 3]
>> mixedArray.size     #=> 5
```
<!-- more -->

遍历数组也很方便，利用each方法，ruby提供了两种书写方式。*each方法会返回原数组的值*

``` ruby do/end代码块方式
>> mixedArray = [1, "data", 2, "time",3]
>> mixedArray.each do |value|
>> print "#{value} "
>> end

##将会打印出:1 data 2 time 3,并且返回mixedArray。
```

``` ruby {}代码块方式
>> mixedArray.each {
>> |value|
>> print "#{value} "
>> }
```
上面两种方式的输出结果和返回值都一样。建议采用第一种，更加符合Ruby的风格。

*注:通过在终端输入**irb --simple-promot**，可以简化ruby交互式的提示输入，如上面的Ruby的提示输入符'>>'*

###散列
Ruby中的键值(key-value pair)数据结构是用散列(hash)来表示，一般以字符串或者符号(Symbol)来作为键，值来保存对应的对象。

*符号(symbol)*与字符串对象很相似，符号也是对象，一般都作为名称标签来使用。我们可以将符号简单理解为轻量级的字符串。

符号的定义只需在标识符的开头加上':'即可

``` ruby
>> sym1 = :text
>> sym2 = :"text"
```
以上两行代码都是表示符号":foo".

使用符号比使用字符串更有效率，而且符号与字符串可以互相任意转换, 用**to_s**和**to_sym**方法

``` ruby
>> sym = :text
=> :text
>> sym.to_s			##将符号转为话字符串
=> "text"
>> "text".to_sym    ##将字符串转为符号
=> :text
>>
```

如何定义一个散列，定义一个散列有下列两种写法:

``` ruby 散列定义方式一
>> details = {:name => "yu", :gender => "male", :alias => "stonefishy"}
=> {:name=>"yu", :gender=>"male", :alias=>"stonefishy"}
```

``` ruby 散列定义方式二
>> details = {name: "yu", gender: "male", alias: "stonefishy"}
=> {:name=>"yu", :gender=>"male", :alias=>"stonefishy"}
```
第二种方式和*json*数据的格式相似。

操作散列也很简单，直接看下面执行示例

``` ruby
>> details[:age] = 18				##新增一个age值
=> 18
>> details
=> {:name=>"yu", :gender=>"male", :alias=>"stonefishy", :age=>18}
>> details[:name] 					##获取指定键的值
=> "yu"
>> details[:age] = 20				##修改指定键的值
=> 20
>> details
=> {:name=>"yu", :gender=>"male", :alias=>"stonefishy", :age=>20}
```
*注: =>符号表示ruby交互式模式的输出命令*

使用each方法遍历散列：

``` ruby
>> details.each do |key, value|
?> puts "#{key} - #{value}"
>> end
name - yu
gender - male
alias - stonefishy
age - 20
=> {:name=>"yu", :gender=>"male", :alias=>"stonefishy", :age=>20}   ##each方法同样会返回散列
```

###正则表达式
正则表达式(Regular Expression)的语法如下，

	/模式/

使用运算符=~来匹配正则表达式和字符串, 若匹配成功返回匹配部分的位置，匹配失败返回nil值。

	/模式/ =~ 希望匹配的字符串

``` ruby
>> /ruby/ =~ "hello, ruby"			##包含‘ruby’字符串
=> 7
>> /^ruby$/ =~ "hello, ruby"		##完全匹配‘ruby’字符串
=> nil
>> /ruby$/ =~ "hello, ruby"		##以‘ruby’字符串结尾
=> 7
```

正则表达式右边的/后面添加i表示不区分大小写匹配:

``` ruby
>> /RUby/i =~ "hello, ruby"
=> 7
```

定义正则表达式还有另外一种方式，直接用**Regexp**类来创建:

``` ruby
pattern = Regexp.new("ruby")		##定义一个正则表达式
=> /ruby/
>> pattern =~ "hello, ruby"
=> 7
```
*注: 如果想更加详细的了解正则表达式的语法，可以查看使用[Rubular](http://rubular.com/)*

###命令行参数
Ruby中使用**ARGV**这个Ruby预定义好的数组来获取从命令行传递过来的数据。在命令行指定多个脚本参数时，各个参数之间用空格隔开。

使用**each_with_index**方法可以获取遍历的index，

``` ruby argv.rb
ARGV.each_with_index do |arg, index|
puts "第#{index + 1}个参数: #{arg}"
end
```
执行和输出结果如下:

	> ruby argv.rb hello world and ruby	##命令行输入参数
	=> 第1个参数: hello
	=> 第2个参数: world
	=> 第3个参数: and
	=> 第4个参数: ruby

###文件读取
Ruby中通过File类来读取文件，两种读取方式，一次性读取和逐行读取，示例如下,

``` ruby read_all_text.rb
file = File.open(ARGV[0])		##打开文件
text = file.read					##读取文件中得所有文本数据
print text
file.close						##关闭文件句柄
```

``` ruby read_each_line_text.rb
file = File.open(ARGV[0])
file.each_line do |line|		 ##逐行读取文件
	print line
end
file.close
```

###方法的定义
Ruby方法的定义和Python一样简答，

	def 方法名
		希望处理的逻辑
	end
	
我们现在将上面读取文件的代码定义在一个方法中，

``` ruby read_file.rb
def read_file(file_name)
	file = File.open(file_name)
	file.each_line do |line|
		print line
	end
end
```

现在我们想在其他文件里面使用上面定义的文件读取方法，那么该如何引用了，且看下，

###引用文件
Ruby使用**require**方法来引用库或文件

	require 希望使用的库名

*库名可以省略后缀.rb*

``` ruby invoke_read_file.rb
require "./read_file"			##引入读取文件的库
read_file("argv.rb");			##直接调用库中的方法
```

执行示例：

	> ruby invoke_read_file.rb
	=> ARGV.each_with_index do |arg, index|
	=> puts "第#{index + 1}个参数: #{arg}"
	=> end

###p与pp方法
如前我们所介绍的打印数据到终端的有**p**, **puts**, **print**等方法，现在再介绍一种按数据排版打印的方法**pp**, 其全名为 pretty print。但是如果想使用该方法，需要使用require方法引入**pp**库。直接看示例

``` ruby p_and_pp.rb
require "pp"

details = [{content1: "This is content1", 
			content2: "This is content2", 
			content3: "This is content3"}]

puts "p的输出格式如下:"
p details

puts "pp的输出格式如下:"
pp details
```

执行示例:

	p的输出格式如下:
	[{:content1=>"This is content1", :content2=>"This is content2", :content3=>"This is content3"}]
	pp的输出格式如下:
	[{:content1=>"This is content1",
  	  :content2=>"This is content2",
  	  :content3=>"This is content3"}]
  	  
与**p**方法的不同之处在于，**pp**方法在输出对象的结果时，为了更容易看懂，会适当的换行以调整输出结果。如上所示。
  	
  




