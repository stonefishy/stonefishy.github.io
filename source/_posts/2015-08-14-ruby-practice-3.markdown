---

title: "Ruby修炼之道(3)"
date: 2015-08-14 17:47:01 +0800
comments: true
categories: Backend
tags: [Ruby]
---

在Ruby中任何表现数据的基本单位都称为对象(object), 而Ruby的类(class)表示的就是这些对象的种类。

### 变量
和其他面向对象语言一样，Ruby也有多种类型的变量，但是在Ruby中，变量的类型是由变量的命名方式决定的。

*局部变量(local variable)*
以英文字母或者_开头

``` ruby
words = "Hello Ruby"
_words = "Hello Ruby"
```
<!--more-->
*全局变量(global variable)*
以$开头

``` ruby
$words="Hello Ruby"
```

*实例变量(instance variable)*
以@开头

``` ruby
@words="Hello Ruby"
```

*类变量(class variable)*

以@@开头
``` ruby
@@words = "Hello Ruby"
```

*伪变量(pseudo variable)*
伪变量是Ruby预先定义好的代表某个特定值的特殊变量，因此这个值是无法改变的。如，nil、true、false、self等。

*注：Ruby不需要在意变量名的长度，对于多个单词组合的变量名，使用_隔开各个单词，或者单词以大写字母开头，如：my_words,或者myWords。但是在Ruby中的变量名和方法名一般使用前者，而后者主要用于类名和模块名。*

### 常量
顾名思义，即不可修改，对已经赋值的常量再进行赋值时，Ruby会做出警告。常量均以大写字母来命名。如Ruby中预先定义好的RUBY_VERSION(运行版本)、RUBTY_PLATFORM(运行平台)、ARGV(命令行参数数组).

### 多重赋值
多重赋值是Ruby中的简化代码的一种方式，通过多重赋值可以将多个变量同时赋值。

``` ruby
>> a, b, c = 1, 2, 3	##同时赋值a, b, c
>> p [a, b, c]			##=> [1, 2, 3]
```
这样a, b, c均被赋值了1, 2, 3。
*注：在使用多重赋值时，建议对彼此相关变量进行多重赋值，避免程序难读懂。*

即使等号(=)左右两边列表的数量不相等，Ruby也不会报错。
*左边变量部分数量多时*

``` ruby
>> a, b, c, d = 1, 2
>> p [a, b, c, d]			##=> [1, 2, nil, nil]
```
当左边变量部分数量多时，Ruby会自动将**nil**赋值给未被分配值的变量。如上的c, d变量。

*左边变量部分数量少时*

``` ruby
>> a, b, c = 1, 2, 3, 4
>> p [a, b, c]				##=> [1, 2, 3]
```
当变量部分比较少时，Ruby会忽略掉该值，不会分配多余的值，如上的值4。

*将未被分配的值赋给指定的变量*
当给一个变量前加上*，表示Ruby会将未分配的值封装为数组赋值给该变量。通过这种方式，Ruby会将变量与右边的值一一匹配。

``` ruby
>> a, b, *c = 1, 2, 3, 4
>> p [a, b, c]					##=> [1, 2, [3, 4]]

>> a, *b, c = 1, 2, 3, 4, 5
>> p [a, b, c]					##=> [1, [2, 3, 4], 5]
```

使用多重赋值简化变量值交换：

``` ruby
>> a, b = 0, 1
>> a, b = b, a					##置换变量a、b的值
>> p [a, b]						##=> [1, 0]
```

使用多重赋值获取数组的元素:

``` ruby
>> arr = [1, 2]
>> a, b = arr
>> p a								##=> 1
>> p b								##=> 2


>> arr = [1, [2, 3], 4]
>> a, b, c = arr
>> p a								##=> 1
>> p b								##=> [2, 3]
>> p c								##=> 4


>> arr = [1, [2, 3], 4]
>> a, (b, c), d = arr
>> p a								##=> 1
>> p b								##=> 2
>> p c								##=> 3
>> p d								##=> 4
```
只要等号左边的变量结构与数组的结构一致，即使再复杂的结构，多重赋值以可以轻松对应。

### 条件判断
Ruby中条件判断有三种，if语句，unless语句，case语句。If语句我们之前讲过，这里主要涉及剩余两种。

*unless语句*
这个判断语句刚好与if语句相反，当条件为假时才执行

	unless 条件为假 then
		逻辑处理
	end
	
	unless 条件为假
		逻辑处理1
	else
		逻辑处理2
	end
	
*then*关键字可以省略。

*case语句*
如果需要比较的对象只有一个，根据这个对象值的不同，执行不同的逻辑处理。

	case 比较对象
	when 值1 then
		逻辑处理1
	when 值2 then
		逻辑处理2
	when 值3 then
		逻辑处理3
	else
		逻辑处理4
	end

当然，*then*关键字也可以省略。
*when*关键字也可以一次指定多个值。

	case 比较对象
	when 值1，值2，值3 then
		逻辑处理1
	else
		逻辑处理2
	end
	
在*case*语句中，*when*判断值是否相等时，实际是使用**===**运算符来判断的。当左边是数值或者字符串时，**===**与**==**的意义是一样的。

*注:Ruby中只有**nil**和**false**为假，除此之外的值全为真.*

在Ruby中，所有的对象都是唯一的，可以通过**object_id**（或者**__id__**)方法获取对象的标识。

``` ruby
>> arr1 = []
>> arr2 = []
>> p arr1.object_id			##=> 70115564029000
>> p arr2.__id__				##=> 70115559585120
```

如果判断两个对象是否是同一个对象，即是否引用的是同一个ID的对象。Ruby提供了**equal?**方法来判断。

``` ruby
>> arr1 = []
>> arr2 = []
>> arr1.equal? arr2			##=> false
>> arr3 = arr1
>> arr1.equal? arr3			##=> true
```

除了**==**以外，Ruby还提供了**eql?**方法用来判断对象的值是否相等。大部分情况下 **==**与**eql?**执行结果都是一样的。但针对数值类，**eql?**方法会被重定义。

``` ruby
p 1.0 == 1					##=> true
p 1.0.eql? 1					##=> false
```