---

title: "Ruby修炼之道(4)"
date: 2015-08-17 17:01:35 +0800
comments: true
categories: Backend
tags: [Ruby]
---

### 循环
对于任何一种程序设计语言，都有循环处理的方法，Ruby也一样，但是在Ruby中有两种实现循环的方法。

- 使用循环语句，即利用Ruby提供的现有循环语句
- 使用方法实现循环，其具体做法为，将块传给方法，然后在块里面写上需要循环的处理。

在Ruby中，有6种循环语句或方法：
<!-- more -->

- times方法
- while语句
- each方法
- for语句
- until语句
- loop方法

*times方法*
用于已知道执行的次数，具体用法如下:

	循环次数.times do
		逻辑处理
	end
	
	#do ~ end部分可以用 { ~ }代替.
	
	循环次数.times {
		逻辑处理
	}

``` ruby times方法
>> 3.times do
>> 		print "hello "
>> end
hello hello hello => 3				#=> 打印3个hello，并返回循环次数
```
如果想知道当前的循环次数，则需要添加一个当前的循环次数变量:

	循环次数.times do |i|
		逻辑处理
	end
	
``` ruby 带循环次数的times方法
>> 3.times do |i|
>> 		print "hello#{i} "
>> end
hello0 hello1 hello2 => 3			#=> 打印3个带有当前第几次循环的hello,并返回循环次数
```
*注：**times**方法会返回循环次数*

*for语句*
与**times**方法不同，**for**并不是方法，而是循环控制语句。

	for 变量 in 开始时的数值..结束时的数值 do
		循环处理
	end
	
	#遍历对象的for语句
	for 变量 in 对象 do
		循环处理
	end

**for**语句的循环开始值和结束值可以任意指定。

``` ruby for语句
>> for i in 1..3
>> 		print "hello "
>> end
hello hello hello => 1..3			#=> 打印3个hello，返回1..3的range


>> language = ["Python", "JavaScript", "Ruby"]
>> for lan in language
>> 		print "#{lan} "
>> end
Python JavaScript Ruby => ["Python", "JavaScript", "Ruby"]			#=> 一次打印数组中的元素，并返回数组
```
*注：..或者...都是创建范围对象时所需的符号*

*while语句*
之前了解到，语法如下，十分简单

	while 条件 do
		循环处理
	end
	
``` ruby while语句
>> i, sum = 1, 0
>> while sum < 50 do
>> 		sum += i
>> 		i += 1
>> end
>> puts sum					#=> 55
```

*until语句*
和**while**语句刚刚相反，**until**语句不满足条件时才执行循环处理，知道条件满足终止。

	until 条件 do
		循环处理
	end


``` ruby until语句
>> i, sum = 1, 0
>> until sum >=50 do
>> 		sum += i
>> 		i += 1
>> end
>>  puts sum					#=> 55
```

*each方法*
**each**方法将对象集合里的对象逐个取出，这与**for**语句循环取出数组元素非常相似。

	对象.each do |变量|
		循环处理
	end
	
	#与下面的for语句处理一样
	for 变量 in 对象
		循环处理
	end
		
``` ruby each方法
>> language = ["Python", "JavaScript", "Ruby"]
>> language.each do |lan|
>> 		print "#{lan} "
>> end
Python JavaScript Ruby => ["Python", "JavaScript", "Ruby"]			#=> 返回对象
```

*注：在Ruby内部，**for**语句是用**each**方法来实现的。可以使用**each**方法的对象，也可以用**for**语句*

*loop方法*
此循环方法没用终止循环条件，需要用得循环控制语句来控制此循环

	loop do
		循环处理
	end

### 循环控制命令
- break 终止程序，跳出循环
- next 跳到下一次循环，和其他类似Java，C#的continue一样
- redo 在相同的条件下重复刚才的处理

**redo**与**next**非常像，与**next**的不同之处是，**redo**会再执行一次相同的循环。所以要注意**redo**的使用方法，很容易陷入死循环中。



*注： **do ~ end** 和 **{ ~ }** 是等价的语句块。 只不过从编码规范上来说，如果程序是跨行写的时候使用**do ~ end**，程序写在1行的时候用**{ ~ }**.

``` ruby
>> 3.times do |i|
>>		puts i
>> end

>> 3.times { |i| puts i }
```
