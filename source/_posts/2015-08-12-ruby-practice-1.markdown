---

title: "Ruby修炼之道(1)"
date: 2015-08-12 16:08:21 +0800
comments: true
categories: Backend
tags: [Ruby]
---
{% img /assets/images/legacy/ruby-logo.png 50 50 %}**Ruby**是一门脚本语言，由日本人松本行弘发明，旨在让开发人员用更简洁的方式快乐的编程。他的语法类似Python脚本语言，比较优雅，而且还提供了一些丰富强大的类库。Ruby的意思是**红宝石**.

### 特点
*脚本语言*   
即动态语言，和其他脚本语言一样，无需编译即可直接运行，而C#、C++和Java等需要通过编译然后才能运行。Ruby减少了开发者的开发流程。

<!-- more -->
	
*完全的面向对象*  
对Ruby来说，纯数字都属于对象，可以直接调用其方法。比如下面代码：   
```
-1.abs   #return 1
```
	
这是其他面向对象编程语言如C#、Java不具备的。同时Ruby也具备有其他面向对象编程语言的特性，如：继承、Mixin等。
	
*跨平台*   
能在类Unix操作系统如：Mac OS X、Linux和windows操作系统上运行。
	
### 安装   
Ruby的安装很简单，以在Mac OS X电脑的安装为例。使用Homebrew包管理器可以得到ruby的最新版本:   
``` 
brew install ruby
```

安装完后，在输入如下命令查看ruby版本:   
``` 
ruby -v
```

进入[Ruby Installation](https://www.ruby-lang.org/en/documentation/installation/)链接可以查看Ruby在其他操作系统上的安装

### HelloWorld
用ruby编写第一个Hello world程序，只需一行代码，比其他的面向对象语言简单多了。   
``` ruby
print("Hello world\n");
```

直接将这行代码写入helloworld.rb文件，然后输入如下指令运行即可看到内容输出在终端控制器上。   
```
ruby helloworld.rb
``` 

当然如果你想直接和ruby交互的话，也有办法，Ruby提供了和Python类似的交互式操作。在命令行输入**irb**进入交互模式。

ruby也提供了字符的转义功能，比如换行(\n), 制表符(\t)等等。如上面的打印Hello world。

*注:上面代码中调用print方法的括号可以省略。ruby尽量的简化了开发人员的工作量*


### puts、print和p的区别
**puts**方法会在每次输出后输出换行符，而**print**则不会。例如以下的输出内容是一致的:
``` ruby
puts "Helle world"

print "Hello world\n"
```

使用**p**方法可以将数据按照原始类型输出来，这是**puts**和**print**所不具备的，直接用代码来解释:
``` ruby
puts "1"   #=> 1
puts 1     #=> 1
print 1    #=> 1
print "1"  #=> 1
p "1"      #=> "1"
p 1        #=> 1
```

所以基于**p**方法的特性，转义字符在其内是不工作的，将会被直接打印出来,如以下代码将会输出"Hello world\n"。还有**p**方法也会输出换行符，并且还会将打印的内容按数据类型返回，而其他两种方法只返回**nil**空值。
``` ruby
p "Hello world\n"  
```

*注:在Ruby中，任何函数均有返回值。**nil**空值就是其中一种常见的返回。*

**print**, **puts**和**p**方法均可传入多个参数，调用方式和返回结果如下，可以体会下用法:  
*print方法*    
``` ruby
print "Hello", " Ruby“
```

	Hello Ruby

*puts方法*   
``` ruby
puts "Hello", " Ruby"
```

	Hello
	Ruby

*p方法*	   
``` ruby
p "Hello", " Ruby"
```

	”Hello"
	"Ruby"


### 变量
在ruby中声明变量也很简单，不需要任何如javascript的*var*等关键字，直接定义赋值。

``` ruby
greet = "Hello world"
```

访问变量也简单:   

``` ruby
puts greet
puts "#{greet}"
```    
以上两种写法均会直接输出Hello world.

### 控制语句
*if 语句*   

	if 条件 then
  		逻辑处理
	end
	
*if else 语句*
	
	if 条件 then
		逻辑处理
	else
		逻辑处理
	end
	
*if elsif 语句*

	if 条件 then
		逻辑处理
	elsif 条件 then
		逻辑处理
	else
		逻辑处理
	end

*注:在上面的if各种控制语句中，**then**关键字是可以省略的.*

### 循环语句
*while 语句*

	while 条件 do
		逻辑处理
	end
	
	
*times迭代器*

当循环的次数已经确定时，可直接使用times方法来循环处理我们需要的逻辑
	I
	循环次数.times do 
		逻辑处理
	end

*注:**times**方法被成为迭代器，除了times方法迭代器，ruby还提供了其他的迭代器，比如**each**方法.*

	
