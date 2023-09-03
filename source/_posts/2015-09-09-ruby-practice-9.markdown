---

title: "Ruby修炼之道(9)"
date: 2015-09-09 23:22:26 +0800
comments: true
categories: Backend
tags: [Ruby]
---

### 异常处理
在Ruby中，抛出异常的信息格式如下：

	文件名：行号：in 方法名：错误信息 (异常类名)
			from 文件名：行号：in 方法名

以from开头的行表示发生错位的位置。
在Ruby中,使用**begin ~ rescue ~ end**语句来做异常处理，其语法如下：

	begin
		可能会发生异常的处理
	rescue
		发生异常时的处理
	end
	
``` ruby
begin
	1 / 0
rescue
	puts "has a exception"				#=> has a exception
end
```
<!-- more -->
通过在**rescue**后指定变量名，可以获得异常对象。异常对象有两个方法，方法名为**class**、**message**、**backtrace**。

- **class**方法可以获取到异常的种类
- **message**方法获取异常信息。
- **backtrace**方法获取异常发生的位置信息

	begin
		可能会发生的异常的处理
	rescue =>	引用异常对象的变量
		发生异常时的处理
	end
	
``` ruby
begin
	1 / 0
rescue => ex								#=> 将异常对象引用到ex对象
	puts ex.class							#=> ZeroDivisionError
	puts ex.message						#=> divided by 0
	puts ex.backtrace					#=> exception.rb:2:in `/'exception.rb:2:in `<main>'
end
```
*Note：此处是将代码直接写在ruby文件里，然后直接运行的，没有写在方法里面。所以看到的信息是文件名和发生错误的行号*

如果不指定变量名，Ruby将会把异常对象赋值给**$!**、**$2**变量

- **$!** 引用到最后发生的异常对象
- **$@** 引用到发生的异常的位置信息

``` ruby
begin
	1 / 0
rescue
	puts $!.class							#=> ZeroDivisionError
	puts $!.message						#=> divided by 0
	puts $!.backtrace					#=> exception.rb:2:in `/'exception.rb:2:in `<main>'
	puts $@								#=> exception.rb:2:in `/'exception.rb:2:in `<main>'
end
```
从上面的运行示例可以看出，其实**$!.backtrace**与**$@**是等价的，都表示异常发生的位置。

在Ruby中可以通过**ensure**来确保无论是否引发异常都将执行处理，类似于Java和C#中得finally。

	begin
		可能发生的异常
	rescue => 变量
		发生异常时的处理
	ensure
		无论是否有异常，都会处理
	end
	
``` ruby
def copy(from to)
	src = File.open(from)
	begin
		dst = File.open(to, 'w')
		data = src.read
		dst.write(data)
		dst.close
	rescue => ex
		print ex.message
	ensure
		src.close
	end
end
```

在**rescue**中使用**retry**后，**begin**以下的处理会再重做一遍。所以需要注意陷入死循环的情况。

``` ruby
count = 0
num = 0
begin
	1 / num						#=> 如果发生异常，则执行rescue中的语句
rescue
	if count >= 5
		num += 1
	end

	count += 1
	
	retry							#=> 重复执行begin后的语句
end

puts count						#=> 6
```

#### 使用rescue做为修饰符

使用rescue作为修饰符可以简化很多代码

	表达式1 rescue 表达式2
	
与下面的写法等价

	begin
		表达式1
	rescue
		表达式2
	end

其意义为如果表达式1中发生异常，表达式2的值就会成为整体表达式的值。

``` ruby
def parseInt(param)
	Integer(param) rescue 0		#=> 如果Integer(param)语句无异常，直接返回该值，否者返回0
end

puts parseInt("123")			#=> 123
puts parseInt("abc")			#=> 0
```
在判断字符串是否为数值形式时，可以使用这种方法。

#### 异常处理的其他语法
*方法内*
如果异常处理的范围是整个方法体，这时可以省略begin以及end关键字，直接使用rescue和ensure。

	def 方法名
		方法体
	rescue => 异常对象
		异常处理
	ensure
		后处理
	end

*类定义中*
在类定义中也可以使用rescue以及ensure。但是，如果类定义途中发生异常，那么异常发生部分后的方法定义就不会再执行了。所以一般很少在类定义中使用。

	class 类名
		类定义
	rescue => 异常对象
		异常处理
	ensure
		后处理
	end
	
#### 指定需要捕捉的异常
当然，Ruby也提供了针对特定异常的捕捉处理，可以使用多个rescue来分开处理

	begin
		可能发生异常的地方
	rescue Exception1, Exception2 =>变量
		对Exception1或Exception2的处理
	rescue Exception3 => 变量
		对Exception3的处理
	rescue
		对上述异常以外的异常的处理
	end
	
``` ruby
def multi_rescue 
	begin
		yield					#块参数在此处，如有异常，将执行后续指定的异常处理
	rescue NameError, NoMethodError => ex
		puts ex.class
	rescue ZeroDivisionError
		puts $!.class
	rescue 
		puts $!.class
	end
end

multi_rescue do 
	1 / 0							#触发ZeroDivisionError异常
end

multi_rescue do
	val.size						#触发NameError异常
end

multi_rescue do 
	val = 4
	sqrt(val)						#触发NoMethodError异常	
end
```
以上代码定义了一个带块的方法，通过此方法可以插入任何你想要得代码执行块。
	
#### 异常类
*注意：ruby中所有异常都是*Exception*类的子类。*
当rescue中不指定异常类时，程序会默认捕捉*StandardError*类及其子类的异常. 所以在定义自己的异常时，可以先定义继承StandardError类的新类，然后再继承这个新类。

``` ruby
MyError = Class.new(StandardError)

class MyError < StandardError
end
```
上面两种写法是等效的，都是表示定义一个继承StandardError类的新类。第一种方式是一种简洁的写法。

``` ruby
MyError = Class.new(StandardError)		#自定义MyError异常

#自定义MyError1异常继承自MyError，并重写message方法
class MyError1 < MyError
	def message
		"the exception message is from MyError1"
	end
end

#自定义MyError2异常继承自MyError，并重写message方法
class MyError2 < MyError
	def message
		"the exception message is from MyError2"
	end
end

#定义带块的处理异常的方法
def raiseException
	yield
rescue MyError1 => ex
	puts ex.message
rescue MyError2 => ex
	puts ex.message
rescue
	puts $!.message
end

#抛出异常MyError1
raiseException do
	raise MyError1		#=> the exception message is from MyError1		
end

#抛出异常MyError2	
raiseException do
	raise MyError2		#=> the exception message is from MyError2
end
```

#### 抛出异常
使用**raise**方法，可以使程序主动抛出异常。

raise方法有一下4中调用方式：

- raise message
抛出*RuntimeError*异常，并把字符串作为message设置给新生成的异常对象

- raise 异常类 
抛出指定的异常

- raise 异常类，message
抛出指定的异常，并把message信息设置给新生成的异常对象

- raise
在rescue外抛出RuntimeError。 在rescue中调用时，会再次抛出最后一次发生的异常。

``` ruby
#自定义MyError3异常
MyError3 = Class.new(MyError

raiseException do
	raise MyError3, "customized message for MyError3"	#=>抛出指定message
end
```