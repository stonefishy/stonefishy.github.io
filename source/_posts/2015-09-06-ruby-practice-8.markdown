---
layout: post
title: "Ruby修炼之道(8)"
date: 2015-09-06 20:36:07 +0800
comments: true
categories: [Ruby]
---

###运算符

以下两种运算符是等价的, op即代表操作运算符

	value1 op= value2
	value1 = value1 op value2


利用运算符**||**的特性，可以简化相关的赋值代码，如下是等价的
``` ruby 赋值方式1
name = "Ruby"
if var				#当var不是nil或false时
	name = var
end
```

``` ruby 赋值方式2
name = var || "Ruby"
```
<!-- more -->
而利用**&&**运算符，可以避免不必要要得程序错误

``` ruby 隐藏的错误
name = ary[0]          #如果ary为nil，则此时将引发程序错误
```

``` ruby 使用&&避免错误
name = ary && ary[0]	#避免当ary为nil时，读取ary[0]时的错误
```
使用**&&**可以在确定对象存在后再调用方法。而且代码更加简洁。

####范围运算符
相比其他的语言如Java等，范围运算符算是一个比较特殊的运算符，范围运算符有**..**和**...**两种，其区别在于如下

- **x..y**的范围是从x到y
- **x...y**的范围是从x到y得前一个元素

``` ruby
(1..5).to_a				#=> [1, 2, 3, 4, 5]
("a".."d").to_a			#=> ["a", "b", "c", "d"]

(1...5).to_a				#=> [1, 2, 3, 4]
("a"..."d").to_a			#=> ["a", "b", "c"]
```

Ruby中有范围对象(Range)来表示范围对象，以下两个写法是等价的

``` ruby
ary = Range.new(1, 5)	#=> 1..5
ary.class					#=> Range


ary = 1..5				#=> 1..5
ary.class					#=> Range
```
在Range对象内部，可以使用**succ**方法根据起点值逐个生成下一值

``` ruby
a = 1
a = a.succ					#=> 2
a = a.succ					#=> 3
a.succ.succ					#=> 5
```

####运算符重定义
在Ruby中有以下运算符不能重定义
::, &&, ||, .., ..., ?:, not, =, and

*二元运算符重定义*,如加号+、减号-

``` ruby
class Point
	attr_reader :x, :y

	def initialize(x, y)
		@x, @y = x, y
	end

	def +(other)
		self.class.new(x + other.x, y + other.y)
	end

	def -(other)
		self.class.new(x - other.x, y - other.y)
	end

	def display
		"(#{x}, #{y})"
	end
end

p1 = Point.new(1, 2)
p2 = Point.new(3, 4)
p = p1 + p2
puts p.display					#=> (4, 6)

p = p2 - p1
puts p.display					#=> (2, 2)
```

*一元运算符*，如正号+，负号-等，其重定义与二元运算符不同。可定义的一元运算符有**+**, **-**, **~**, **!**4个。分别以+@， -@， ~@， !@为方法名进行方法定义。而且一元运算符都是没有参数的。

``` ruby
class Point
	attr_reader :x, :y

	def initialize(x, y)
		@x, @y = x, y
	end

	def +@
		dup
	end

	def -@
		self.class.new(-x ,-y)
	end

	def ~@
		self.class.new(-y, x)
	end

	def display
		"(#{x}, #{y})"
	end
end

p1 = Point.new(1, 2)
puts (-p1).display				#=> (-1, -2)
puts (+p1).display				#=> (1, 2)
```

####下标方法
数组、散列中的*obj[i]*以及*obj[i]=x*这样的方法，称为下标方法。定义下标方法时的方法名分别为**[]**和**[]=**.

``` ruby
class Point
	attr_reader :x, :y

	def initialize(x, y)
		@x, @y = x, y
	end

	def [](index)
		case index
		when 0
			x
		when 1
			y
		else
			raise ArgumentError, "out of range (#{index})"
		end
	end

	def []=(index, val)
		case index
		when 0
			self.x = val
		when 1
			self.y = val
		else
			raise ArgumentError, "out of range (#{index})"
		end
	end


	def display
		"(#{x}, #{y})"
	end
end
```