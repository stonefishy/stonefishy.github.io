---
layout: post
title: "Ruby修炼之道(11)"
date: 2015-09-28 10:33:41 +0800
comments: true
categories: [Ruby]
---

Ruby中所有表现数据的类型都称为数值类，他们都是**Numeric**的子类。起详尽的类继承结构如下：

![assets/images/numberic-inheritance.png](assets/images/numeric-inheritance.png)
<!-- more -->
Ruby中一般用到得整数都是在**Fixnum**类范围内的，如果超过了Fixnum的范围，Ruby会自动将其转换为**Bignum**类。

正如上图所示，Ruby可以处理有理数和复数，有理数用**Rational**类表示，复数用**Complex**类表示。其定义形式如下：

1.Rational对象

	Rational(分子，分母)
	
``` ruby
a = Rational(1, 3)		#=> (1/3)
a.to_f						#=> 0.3333333
```
通过*Rational#to_f*方法可将有理数转为Float对象

2.Complex对象

	Complex(实数，虚数)
	
``` ruby
a = Complex(1, 2)		#=> (1+2i)
```

单纯的数字罗列表示10进制整数。以0b开头的数值表示2进制数，以0或者0o开头的数值表示8进制数，以0d开头的数值表示10进制数，以0x开头的数值表示16进制数。字面量中的_会被自动忽略。

``` ruby
p 123			#=> 123
p 1_23			#=> 123
p 0123 		#=> 83
p 0o123		#=> 83
p 0d123		#=> 123
p 0x123		#=> 291
```

Integer对象与Float对象的计算结果为Float对象。

x.**div**(y)
返回x除以y后的商的整数

``` ruby
p 5.div(2)	#=> 2
p 5.div(2.2)	#=> 2
```

x.**quo**(y)
返回x除以y后的商，如果x、y都是整数则返回Rational对象

``` ruby
p 5.quo(2)	#=> (5/2)
p 5.quo(2.2)	#=> 2.27272727
```

x.**modulo**(y)
返回x除以y后的余数，与*x % y*等价

``` ruby
p 5.modulo(2)			#=> 1
p 5 % 2				#=> 1
```

x.**divmod**(y)
将x除以y后的商和余数作为数组返回,余数的符号与y的符号一致。

``` ruby
p 5.divmod(2)			#=> [2, 1]
p -5.divmod(2)		#=> [-3, 1]
p 5.divmod(-2)		#=> [-3, -1]
```

x.**remainder**(y)
返回x除以y的余数，结果的符号与x的符号一致

``` ruby
p 5.remainder(2)		#=> 1
p 5.remainder(-2)	#=> 1
p -5.remainder(2)	#=> -1
```

*注：当除数为0时，Integer类会返回错误，而Float类则会返回Infinity（无限大）或者NaN（Not a Number）。*

``` ruby
p 1 / 0			#=> ZeroDivisionError
p 1.0 / 0 		#=> Infinity
p 0 / 0.0			#=> NaN
```

针对数值类型，Ruby提供**Math**模块增强数据的处理功能，包括三角函数，对数函数等常用的函数运算方法。通过调用如下方法可得到**Math**模块提供的方法

``` ruby
Math.methods
```

**to_f**方法可将Integer对象转换为Float对象，**to_i**方法可将Float对象转换为Integer对象，to_i方法会将小数点以后的值去掉。通过使用**round**方法可对小数进行四舍五入的处理.**ceil**方法返回比比原数值大得最小整数，**floor**方法返回比原数值小的最大整数。通过**to_r**和**to_c**方法可将数值转换为Rational对象和Complex对象。

``` ruby
p 1.to_f			#=> 1.0
p 1.23.to_i		#=> 1
p 1.23.round		#=> 1
p 1.50.round		#=> 2
p 1.23.ceil		#=> 2
p 1.23.floor		#=> 1
p 1.5.to_r		#=> (3/2)
p 1.5.to_c		#=> (1.5+0i)
```
Integer类按照指定次数循环处理逻辑

执行指定的n次逻辑

	n.times do |i|
		逻辑
	end

``` ruby
ary = []
5.times do |i|
	ary << i
end
p ary				#=> [0, 1, 2, 3, 4]
```
从*from*开始循环加对*i*进行加1处理，直到*i*等于*to*

	from.upto(to) do |i|
		逻辑
	end
	
``` ruby
ary = []
1.upto(5) do |i|
	ary << i
end
p ary				#=> [1, 2, 3, 4, 5]
```

从*from*开始循环对*i*进行减1处理，直到*i*等于*to*

	from.downto(to) do |i|
		逻辑
	end
	
``` ruby
ary = []
5.downto(1) do |i|
	ary << i
end
p ary				#=> [5, 4, 3, 2, 1]
```

从*from*开始循环对*i*进行加*step*处理，直到*i*等于*to*

	from.step(to, step) do |i|
		逻辑
	end
	
``` ruby
ary = []
1.step(5, 2) do |i|
	ary << i
end
p ary				#=> [1, 3, 5]
```




