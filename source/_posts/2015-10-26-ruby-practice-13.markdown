---
layout: post
title: "Ruby修炼之道(13)"
date: 2015-10-26 22:13:32 +0800
comments: true
categories: [Ruby]
---

####替换数组内的元素
根据指定规则替换数组中的元素，Ruby也提供了以下方法:

ary.**collect** { |item| ...}   
ary.**collect!** { |item| ...}   
ary.**map** { |item| ...}   
ary.**map!** { |item| ...}   

将数组ary中的各元素item传递给块做处理。

``` ruby
ary = [1, 2, 3]
ary1 = ary.collect do |item|
    item*2
end

p ary1					#=> [ 2, 4, 6]

ary2 = ary.map do |item|
    if item % 2 == 0
        0
    else
        1
    end
end

p ary2					#=> [1, 0, 1]
```
<!-- more -->
ary.**fill**(value)    
ary.**fill**(value, begin)   
ary.**fill**(value, begin, len)   
ary.**fill**(vlaue, n..m)    
将数组ary的元素替换为value。

``` ruby
p [1, 2, 3].fill(0)			#=> [0, 0, 0]
p [1, 2, 3].fill(0, 1)		#=> [1, 0, 0]
p [1, 2, 3].fill(0, 0, 2)		#=> [0, 0 ,3]
p [1, 2, 3].fill(0, 1..2)		#=> [1, 0, 0]

ary = [1, 2, 3]
ary.fill(0)
p ary						#=> [0, 0, 0]
```
*注意，此方法会修改原对象的值。即为破坏方法。

ary.**flatten**   
ary.**flatten**   
平坦化数组ary，所谓平坦化是指展开嵌套数组变为一个大数组。

``` ruby
ary = [1, [2, 3], [4, [5, 6]]]
p ary.flatten	 	 #=> [1, 2, 3, 4, 5, 6]
```

ary.**reverse**    
ary.**reverse!**    
反转数组ary的元素顺序

``` ruby
ary = [1, 2, 3]
p ary.reverse		#=> [3, 2, 1]
```

ary.**sort**   
ary.**sort!**   
ary.**sort**{|i, j| ... }   
ary.**sort!**{|i, j| ...}   
ary.**sort_by** {|i| ...}   

对数组ary中进行排序，排序方法可以由块指定。没有块时，使用*<=>*运算符比较。其中**sort_by**方法中排序是根据块的运行结果来对数组的所有元素进行排序。

``` ruby
ary = [4, 9, 5]
p ary.sort			#=> [4, 5, 9]
ary.sort! { |i, j|
	i % 3 <=> j % 3
}
p ary 				#=> [9, 4, 5]

p [2, 4, 3, 1].sort_by { |i| -i }  #=> [4, 3, 2, 1]
```

####数组的迭代

**each**方法并不知道元素的索引值。当需要指定索引值时，可以使用**each_with_index**方法

``` ruby
ary = [23, 54, 14]
ary.each { |item| print "#{item} " }	#=> 23 54 14

ary.each_with_index do |item, index|
	print "The ", index+1, " element is :", item, "\n"
end
```

如果数组内各元素全部处理完毕后该数组就不需要了，此时可以通过逐个删除数组元素使数组变空这样的手段来实现循环。

``` ruby
while item = ary.pop
	## the handle of the item
end
```

####多维数组
数组中的各个元素也可以是数组，也就是数组的数组。可以用它来表示矩阵。

``` ruby
ary = [[1, 2], [3, 4], [5, 6]]

p ary[1][1]		#=> 4

#3个元素引用的是同一个数组对象
ary = Array.new(3, [0, 0])
ary[0][1] = 1
p ary  			#=> [[0, 1], [0, 1], [0, 1]]

#避免引用同一个数组对象，在块中，重复创建新的对象
ary = Array.new(3) do
	[0, 0]
end		

ary[0][1] = 1
p ary  			#=> [[0, 1], [0, 0], [0, 0]]	
```
使用*Array.new(length, array)*创建二维数组。创建的所有元素引用的都是同一个对象array，修改其中一个元素的值，其他元素也会变化。如上示例代码。如何避免？可在创建数组的块中重复创建新对象。

ary.**zip**(ary1, ary2, ...)   
**zip**方法会将接收器和参数传来的数组元素逐一取出，而且每次都会启动块。参数可以是一个或多个。

``` ruby
ary1 = [1, 2, 3]
ary2 = [10, 20, 30]
ary3 = [100, 200, 300]

result = []
ary1.zip(ary2, ary3) do |a, b, c|
	result << a + b + c
end

p result			#=> [111, 222, 333]
```


