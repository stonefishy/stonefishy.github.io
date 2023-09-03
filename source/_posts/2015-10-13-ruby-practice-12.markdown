---

title: "Ruby修炼之道(12)"
date: 2015-10-13 10:25:41 +0800
comments: true
categories: Backend
tags: [Ruby]
---

数组(**Array**)也是Ruby中表现一组数据的对象

#### 数组的创建
创建方式有一下几种：   
1.使用**Array.new**   
Array#new方法可带两个可选参数，第1个参数表示数组中得个数，第2个参数表示每个元素初始值。如果没有指定第2个参数，其元素的初始值为**nil**。如果不带参数则会创建一个空数组。

``` ruby
#不带参数c
ary = Array.new
p ary					#=> []

#带1个参数
ary = Array.new(3)
p ary					#=> [nil, nil, nil]

#带2个参数
ary = Array.new(3, 5)
p ary					#=> [5, 5, 5]
```
<!-- more -->
2.使用**%w**与**%i**   
**%w**用于创建不包含空白的字符串数组, 使用此方法创建字符串数组更加简洁。

``` ruby
%w(This is ruby)		#=> ["This", "is", "ruby"]
```

**%i**用于创建符号(Symbol)数组。

``` ruby
%i(This is ruby) 	#=> [:This, :is, :ruby]
```

使用**%w**和**%i**创建数组时用到了(). 其实还可以用很多字符来替代，不过建议使用**()**,**<>**,**||**.

3.使用**to_a**方法

``` ruby
hash={name: "yu", gender: "male"}			#=> {:name=>"yu", :gender=>"male"}
hash.to_a										#=> [[:name, "yu"], [:gender, "male"]]
```

4.使用**split**方法   
对逗号或空白字符间隔的字符串可以使用split方法来创建数组

``` ruby
"this is ruby".split()				#=> ["this", "is", "ruby"]
```

#### 获取元素
获取元素的方法有以下几种：   

1.使用**[]**   
(a) **ary[n]**, 获取索引值为n的元素，当索引值为负数时，从数组的末尾开始获取元素。如果指定的索引值大于元素个数则返回nil。 

``` ruby
ary = [1, 2, 3, 4, 5]				
ary[1]				#=> 2
ary[-1]			#=> 5
```  

(b) **ary[n..m]**, 获取从a[n]到a[m]的元素，并返回新的数组。如果m大于数组的长度，返回的结果与指定数组最后一个元素是一样的。

``` ruby
ary = [1, 2, 3, 4, 5]
ary[1..3]			#=> [2, 3, 4]
ary[1..7]			#=>	 [2, 3, 4, 5]	
```

(c) **ary[n...m]**, 获取从a[n]到a[m-1]的元素，并返回新数组    

``` ruby
ary = [1, 2, 3, 4, 5]
ary[1...3]			#=> [2, 3]
ary[1...7]			#=>	 [2, 3, 4, 5]	
```

(d) **ary[n, len]**, 获取从a[n]开始之后的len个元素，并返回新数组. 如果len的长度大于从n开始后数组的剩余长度，则返回的结果与指定数组最后一个元素是一样的。

``` ruby
ary = [1, 2, 3, 4, 5]
ary[1, 3]				#=> [2, 3, 4]
ary[2, 7]				#=> [3, 4, 5]
```

2.使用**at**方法    
ary.at(n)与ary[n]等价    

``` ruby
ary = [1, 2, 3, 4, 5]
ary.at(1)				#=> 2
ary[1]					#=> 2
```

3.使用**slice**方法   
ary.slice(n..m)与ary[n..m]等价，ary.slice(n...m)与ary[n...m]等价，ary.slice(n, len)与ary[n, len]等价   

``` ruby
ary = [1, 2, 3, 4, 5]
ary.slice(1..3)		#=> [2, 3, 4]
ary.slice(1...3)		#=> [2, 3]
ary.slice(1, 3)		#=> [2, 3, 4]
```

4.使用**values_at**方法
可以通过此方法来获取多个索引的元素, 其用法如下

	ary.values_at(n1, n2, ...)
    

``` ruby
ary = [1, 2, 3, 4, 5]
ary.values_at(0, 2, 4)				#=> [1, 3, 5]
ary.values_at(1, 3, 5)				#=> [2, 4, nil]
```

#### 元素赋值
使用[],at,slice方法还可以给元素赋值

``` ruby
ary = [1, 2, 3, 4, 5]
ary[0] = "a"						#=> ["a", 2, 3, 4, 5]
ary[1...3] = ["b","c"]			#=> ["a", "b", "c", 4, 5]
ary[3..3] = "d"					#=> ["a", "b", "c", "d", 5]
ary[4, 1] = "e"					#=> ["a", "b", "c", "d", "e"]
```

#### 元素插入
插入元素其实就是对0个元素进行赋值，因此指定[n, 0]后，就会在索引值为n的元素前插入新元素。   

``` ruby
ary = [1, 2, 3, 4, 5]
ary[2, 0] = 10					#=> [1, 2, 10, 3, 4, 5]
```

#### 数组作为集合使用
交集：取出同时属于两个集合的元素，并创建新的集合

	ary = ary1 & ary2

``` ruby
ary1 = [1, 2, 3]
ary2 = [1, 3, 5]
ary = ary1 & ary2			#=> [1, 3]
```

并集：取出两个集合中的所有元素，并创建新的集合

	ary = ary1 | ary2

``` ruby
ary1 = [1, 2, 3]
ary2 = [1, 3, 5]
ary = ary1 | ary2			#=> [1, 2, 3, 5]
```

集合的差：把某个集合中属于另外一个集合的元素删除，并创建新的集合

	ary = ary1 - ary2

``` ruby
ary1 = [1, 2, 3]
ary2 = [1, 3, 5]
ary = ary1 - ary2			#=> [2]
```

*注意：连接数组除了可以使用**|**外还可以使用**+**。但是这两种方法返回的结果是不一样的。即使用|相同的元素只有一个，使用+相同的元素会重复存在*

``` ruby
ary1 = [1, 2, 3]
ary2 = [1, 3, 5]
ary = ary1 | ary2			#=> [1, 2, 3, 5]
ary = ary1 + ary2			#=> [1, 2, 3, 1, 3, 5]
```

#### 数组作为队列和栈
|          | 对数组开始元素的操作 | 对数组末尾元素的操作 |
|----------|---------------------:|----------------------|
| 追加元素 | unshift              |     push               |
| 删除元素 | shift                |     pop                |
| 引用元素 | first                |     last               |

使用**push**方法和**shift**方法可以实现队列。使用**push**方法和**pop**方法可以实现栈。

**shift**方法和**pop**方法不只是获取数组元素，还会把该元素从数组中删除。如果只是想单纯地引用元素，应该是用
**first**和**last**方法。

1.ary.**unshift**(item)   
将item元素添加到数组的开头   

``` ruby
ary = [1, 2, 3]
ary.unshift(0)			#=> [0, 1, 2, 3]
```

2.ary.**push**(item), ary **<<** item    
**<<**与**push**是等价的方法，在数组的末尾添加新元素item   

``` ruby
ary = [1, 2, 3]
ary.push(4)				#=> [1, 2, 3, 4]
ary << 5					#=> [1, 2, 3, 4, 5]
```

3.ary1.**concat**(ary2)   
连接数组ary1和数组ary2. concat是具有破坏性的方法。会将ary2的元素直接插入到ary1数组的后面。**+**也是连接两个数组的方法，但是不具破坏性，会生成新的数组。

``` ruby
ary1 = [1, 2, 3]
ary2 = [2, 3, 4]
ary1.concat(ary2)
p ary1						#=> [1, 2, 3, 2, 3, 4]
```

*注：能够改变对象值的方法称为具有破坏性的方法，比如pop方法、shift方法等。有些方法后面加上**!**的也是破坏性方法*

4.ary.**compact** 与ary.**compact!**   
从数组ary中删除所有nil元素     

``` ruby
ary = [nil, 1, nil, 2]
ary.compact				#=> [1, 2]
```

5.ary.**delete**(item)和ary.**delete_at**(n)
从数组a中删除item元素。 第2个方法删除ary[n]元素。   

``` ruby
ary = [1, 2, 3]
ary.delete(2)				
p ary						#=> [1, 3]
ary.delete_at(0)
p ary						#=> [3]
```

6.ary.**delete_if**{ |item| ...}    
ary.**reject**{ |item| ...}   
ary.**reject!**{ |item| ..}    

判断数组ary中的各元素item，如果块的执行结果为真，则从数组ary中删除item。**delete_if**和**reject!**方法都是具有破坏性的方法。

```ruby
ary = [1, 2, 3]
ary.delete_if { |item| item > 2}
p ary				#=> [1, 2]
ary.reject! { |item| item < 2}
p ary				#=> [2]
```

7.ary.**slice**(n)    
ary.**slice**(n..m)    
ary.**slice!**(n, len)    
删除数组ary中指定的部分，并返回删除部分的值。

``` ruby
ary = [1, 2, 3, 4, 5]
p ary.slice!(4)			#=> 5
p ary.slice!(2..3)		#=> [3, 4]
p ary.slice(1, 1)		#=> [2]
```

8.ary.**uniq**, ary.**uniq!**删除数组ary中重复的元素。

``` ruby
ary = [1, 2, 2, 3, 1]
ary.uniq			#=> [1, 2,3]
```

9.ary.**shift**. 删除数组ary开头的元素，并返回删除的值。

``` ruby
ary = [1, 2, 3]j
ary.shift		#=> 1
p ary			#=> [2, 3]
```

10.ary.**pop**. 删除数组ary末尾的元素，并返回删除的值。

``` ruby
ary = [1, 2, 3]
ary.pop		#=> 3
p ary			#=> [1, 2]
```





