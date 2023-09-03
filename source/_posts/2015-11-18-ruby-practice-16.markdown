---

title: "Ruby修炼之道(16)"
date: 2015-11-18 10:49:41 +0800
comments: true
categories: Backend
tags: [Ruby]
---

#### 散列类
散列(**Hash**)和数组一样，都是表示对象集合的对象，不同之处在于，数组使用索引来取得元素，而散列则是用键key，key可以是任何对象，而索引则不能，只能是整数。

##### 散列的创建
1.使用 **{}**


		{key=>value}
		或
		{key:value}

<!-- more -->
``` ruby
hash1 = {"key1"=>"value1", "key2"=>"value2"}
p hash1["key1"]			#=> "value1"

hash2 = {key1:"value1", key2:"value2"}
p hash2[:key1]			#=> "value1"
```

2.使用**Hash.new**

Hash.new()可带参数，当带参数的时候表示该参数为默认值，即当键不存在时，所得的值为该默认值；不带参数情况下，其默认值为nil

``` ruby
hash1 = Hash.new
hash2 = Hash.new("ruby")

p hash1["key"]			#=> nil
p hash2["key"]			#=> "ruby"

```
散列的键可以是任意对象，一般推荐使用下列类型的对象来作为键：字符串(String), 数值(Numberic), 符号(Symbol), 日期(Date).

##### 散列值的设置和获取
一般情况下，我们大多使用最普通的方法来给散列赋值或取值, 如果键不存在，则返回nil

``` ruby
#common set and get
hash = Hash.new
hash["a"] = "first"

p hash["a"]			#=> "first"
p hash["abc"]		#=> nil
```

Ruby还提供了**store**方法来存储值，使用**fetch**方法来获取值

``` ruby
#store and fetch
hash = Hash.new

hash.store("key", "ruby")

p hash.fetch("key")		#=> "ruby"
```
但**fetch**方法与[]不同之处在于，如果hash对象中不存在键时，使用此方法将会出错

``` ruby
hash = Hash.new

hash.store("key", "ruby")

hash.fetch("abc")		#=> KeyError
```

**fetch**方法同样可以返回一个默认值，当它指定第2个参数时或者带块时

``` ruby
hash = Hash.new

p hash.fetch("abc", "default value")	#=> "default value"

p hash.fetch("abc") {"a_"+ String.new("bc")}	#=>"a_bc"
```

通过**keys**和**values**方法可以得到所有的键和值，而且**to_a**方法可以键值组合的数组。

``` ruby
get all keys and values
hash = {"a"=>"b", "c"=>"d"}

p hash.keys		#=> ["a", "c"]
p hash.values		#=> ["b", "d"]
p hash.to_a		#=> [["a", "b"], ["c", "d"]]
```

Ruby也支持了Hash对象的迭代遍历

``` ruby
hash = {"a"=>"b", "c"=>"d"}

hash.each_key do |key|
	puts key
end

hash.each_value do |value|
	puts value
end

hash.each do |key, value|
	puts "#{key}=>#{value}"
end
```

##### 散列的默认值
散列的默认值设置有三种方式
1.使用Hash.new带参数作为默认值

``` ruby
hash = Hash.new("ruby")
p hash["a"] 		#=> "ruby"
```

2.使用块指定默认值

``` ruby
hash = Hash.new do |hash, key|
	hash[key] = key.upcase
end

p hash["a"]			#=> "A"
p hash["B"]			#=> "B"
```

3.使用fetch方法指定默认值

``` ruby
hash = Hash.new do |hash, key|
	hash[key] = key.upcase
end

p hash.fetch("a", "abc")			#=> "abc"
```
*注：当Hash.new方法指定了默认值或块时，fetch方法的第二个参数指定的默认值的优先级是最高的，且看上例*

##### 散列其他方法
1.查看指定对象是否为散列的键    
h.**key?**(key)    
h.**has_key?**(key)    
h.**include?**(key)    
h.**member?**(key)   

``` ruby
hash = {"a" => "b", "c"=> "d"}

p hash.key?("a")    #=> true
p hash.key?("x")    #=> false
p hash.has_key?("c")  #=> true
p hash.include?("a")  #=> true
p hash.member?("x")   #=> false
```

2.查看指定对象是否为散列的值    
h.**value?**(val)    
h.**has_value?**(val)    

``` ruby
hash = {"a"=>"b", "c"=>"d"}

p hash.value?("a")        #=> true
p hash.has_value?("x")    #=> false
```

3.获取散列的大小    
hash.**length**    
hash.**size**    

``` ruby
hash = {:a => "b", :c => "d"}

p hash.length   #=> 2
p hash.size   #=> 2
```

4.判断散列是否为空      
hash.**empty?**    

``` ruby
hash = {:a=>"b"}
p hash.empty?     #=> false

hash = Hash.new
p hash.empty?     #=> true
```

6.删除键值    
h.**delete**(key)   
h.**delete_if**{|key,val| ...}    
h.**reject!**{|key, val| ...}    

``` ruby
hash = {:a => "b"}
p hash[:a]    #=> "b"

hash.delete(:a)
p hash[:a]    #=> nil

hash = {:a => "b"}
p hash.delete(:x) { |key| "no #{key} in this hash object"}   #=>"no x in this hash object"

hash = {:a => "b", :c => "d"}
p hash.delete_if {|key, val| key == :a }     #=> {:c=>"d"}
p hash.delete_if {|key, val| key == :b }     #=> {:c=>"d"}

hash = {:a => "b", :c => "d"}
p hash.reject! {|key, val| key == :a}       #=> {:c=>"d"}
p hash.reject! {|key, val| key == :b}       #=> nil
```
delete方法带块时，如果不存在指定的键, 则返回块中的结果。**delete_if**方法和**reject!**功能一样，区别在于如果不满足条件是，delete_if返回的是散列对象本身，而reject！则返回nil。

7.初始化散列    
h.**clear**    
用**clear**方法清空使用过的散列

``` ruby
hash = {a:"b", c:"d"}
p hash.size     #=> 2

hash.clear
p hash.size     #=> 0
```

##### 关于散列的键

``` ruby
hash = Hash.new
key1 = 1
key2 = 1.0

p key1 == key2      #=> true

hash[key1] = "ruby"
p hash.key?(key1)     #=> true
p hash.has_key?(key2)   #=> false
```
注：在散列内部，程序会将散列获取值时指定的键，与将值保存到散列时指定的键做比较，判断两者是否一致。具体来说就是，如果两个键key1，key2，当key1.hash与key2.hash得到的整数值相同，并且key1.eql?(key2)为true时，就会认为这两个键是一致的。
