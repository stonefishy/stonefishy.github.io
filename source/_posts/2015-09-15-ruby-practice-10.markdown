---
layout: post
title: "Ruby修炼之道(10)"
date: 2015-09-15 15:18:16 +0800
comments: true
categories: [Ruby]
---

###块
**块(block)**，有时也称代码块，能与参数一起传递的多个处理的集合。像之前的*each*，*time*就是带块的方法。

``` ruby
(1..5).each do |i|
	puts i
end
```
上面代码中do和end之间的部分就是所谓的块。这样的方法称为“带块的方法”或“调用块”，其调用方式如下：

	对象.方法名(参数列表) do |变量|
		希望的处理
	end
	
	或者
	
	对象.方法名(参数列表) { |变量|
		希望的处理
	}

<!-- more -->
each方法可以将数组的元素一个一个取出来然后处理，散列也可以，但与数组不同的是，散列会将*[key,value]的组合作为数组来提取元素。

``` ruby
outcome = {"breakfast"=>10.00, "lunch"=>20.00, "dinner"=>15.00 }
sum_outcome = 0
outcome.each do |pair|			#pair是key，value组成的数组，pair[0]为key，pair[1]为value
	sum_outcome += pair[1]
end

puts sum_outcome					#=> 45.0
```
在接收块变量时，多重赋值规则也同样适用。

``` ruby
outcome.each { |key, value|
	puts "#{key} : #{value}"	#=> 打印对应的outcome信息
}
```

块除了应用于迭代器以外，还被广泛用于其他地方，其中一个就是确保后处理被执行。看代码

- (1)未使用块做后处理
``` ruby file_open.rb
file = File.open("each_hash_data.rb")
file.each_line do |line|
	puts line
end
file.close
```
- (2)使用块做后处理
``` ruby block_file_open.rb
File.open("each_hash_data.rb") do |file|
	file.each_line do |line|
		puts line
	end
end
```
仔细观察代码，你会发现在第种方式中，File#open方法接收了块，然后将file对象作为块变量，并且执行完毕后，并没有调用close方法去关闭文件。用这种方式会使程序出问题吗？答案是不会，因为块内部其实执行了如下代码的处理。

``` ruby
file = File.open("each_hash_data.rb")
begin
	file.each_line do |line|
		puts line
	end
ensure
	file.close
end
```
块还有一种常见的用法，用于替换部分算法，以Array类的sort方法为例

``` ruby
ary = ["ruby", "pathon", "c#", "javascript"]

sorted_ary = ary.sort
print sorted_ary			#=> ["c#", "javascript", "pathon", "ruby"]

sorted_ary = ary.sort { |a, b| a.length <=> b.length }
print sorted_ary			#=> ["c#", "ruby", "pathon", "javascript"]

sorted_ary = ary.sort { |a, b| a.reverse <=> b.reverse}
print sorted_ary			#=> ["c#", "pathon", "javascript", "ruby"]
```
将排序算法作为块传递给sort方法，然后返回按指定排序方式排序后的数组。

*注意：块中最后一个表达式的值就是块的执行结果*

####定义带块的方法
使用**yield**关键字代替块，如下

``` ruby
def my_block
	yield
end

my_block do 
	puts "this is execution in my block"
end
```

#####传递块参数，获取块的值
``` ruby
def total(from, to)
	result = 0
	from.upto(to) do |num|
		if block_given?					# 如果有快的话
			result += yield(num)		# 将经过块处理后的值累加
		else
			result += num
		end
	end
	result
end

print total(1, 10)							#=> 55
print total(1, 10) { |num| num * 2}		#=> 110
```
**block_given?**方法被用于判断当调用方法时是否有块被传递，有则返回true，否则false

*yield*参数的个数可以与块变量的个数不一样，当块变量的个数多时，多出的参数会被赋值为nil，当块变量不足时，则不能接受多余的参数值。

*注：**break**和**next**方法可指定参数，如果没有指定任何参数，则返回nil，如果指定了参数则返回参数，如：break 0 和next 0 都会返回0。

#####将块封装为对象
Ruby还可以将块当作对象处理。把块当作对象处理后，就可以在接收块的方法之外的其他地方执行块，或者把块交给其他方法执行。如果要实现这样的功能，就需要**Proc**对象，**Proc**对象是能让块作为对象在程序中使用的类。

通过调用**Proc.new**这个带块的方法，可以定义Proc对象，然后再调用Proc对象的**call**方法执行块。

``` ruby
hello = Proc.new do |name|			# 使用Proc.new方法定义块对象hello
	puts "Hello #{name}"
end

hello.call("world")					# 使用Proc.call方法调用块对象
hello.call("ruby")				
```

在方法定义时，如果末尾的参数使用**&参数名**的形式，Ruby就会自动把调用方法时传进来的块封装为Proc对象。

``` ruby
def total(from, to, &block)
	result = 0
	from.upto(to) do |num|
		if block								#如果有块对象
			result += block.call(num)		#执行快对象
		else
			result += num
		end
	end
	result
end

puts total(1, 10)							#=> 55
puts total(1, 10) { |num| num * 2}		#=> 110
```
在变量名前添加**&**的参数被成为**Proc参数**. 而且Proc参数一定要在所有参数之后，也就是方法的最后一个参数。

将Proc对象作为块传递给其他方法处理，在调用方法时，用**&Proc对象**的形式定义参数就可以了。

``` ruby
def my_each(ary, &block)	
	ary.each(&block)			#将proc对象以块的形式传递给数组的each方法
end

my_each([1, 2, 3]) do |i|
	print "#{i} "
end
```


