---

title: "Ruby修炼之道(7)"
date: 2015-09-01 09:06:53 +0800
comments: true
categories: Backend
tags: [Ruby]
---
### 模块
Ruby中的类是表现事物的属性和行为，那么模块呢？，在Ruby中*模块*表现的只是事物的行为。

模块与类的区别于下：

- 模块不能拥有实例
- 模块不能被继承

如何访问模块内的方法？有两种方式可以访问

1.使用**模块名.方法名**的形式来直接调用模块中定义的方法。

``` ruby
Math.sqrt(4)			#=> 2.0
```
<!-- more -->
2.通过**include 模块名**的形式预先将模块引入到当前的命名空间，然后就可直接访问模块内的方法和常量。

``` ruby
include Math
sqrt(4)				#=> 2.0
```

第二种方式也就是**Mix-in**扩展类的功能，将模块混合到类中。在定义类时使用**include**引入指定的模块后，此时该类就可以使用模块里的所有方法和常量，从而增加了该类的功能。

**Mix-in**可以灵活地解决以下问题：

- 虽然两个类拥有相似的功能，但是不希望把他们作为相同的种类（Class）来考虑的时候，此时可将相似功能封装为模块供这两个类使用
- Ruby不支持父类的多重继承，因此无法对已经继承的类添加共同的功能的时候

``` ruby
class Calculator
	include Math
	def my_sqrt(a, b)
		sqrt(a) + sqrt(b)			#=> 调用Math模块中得sqrt方法
	end
	
	@@PI = PI
end

cal = Calculator.new
cal.my_sqrt(1, 4)					#=> 3.0
Calculator::PI						#=> 3.141592653589793
```

#### 创建模块
使用**module**关键来创建模块，模块名的首字母必须大写

	module 模块名
		模块定义
	end

``` ruby
module HelloModule
	Version = "1.0"

	def say_hello(name)
		puts "Hello #{name}"
	end
	
	module_function :say_hello
end

p HelloModule::Version					#=> "1.0"
HelloModule.say_hello("Ruby")			#=> Hello Ruby

include HelloModule						#=> 包含模块
Version									#=> "1.0"
say_hello("Ruby")						#=> Hello Ruby
```
在上面代码中，我们可以看到**module_function**方法，此方法主要用于将需要把模块函数公开给外部使用，其参数是表示方法方法名的符号，可以跟多个参数。

*注：不建议在定义为模块函数的方法中使用self*

#### Mix-in
前面提到的，在类中使用*include*引入模块，此时类的功能将得到增强。

如果想知道类是否包含某个模块，可以使用**include？**方法，如之前的Calculator类中。

``` ruby
Calculator.include?(Math)				#=> true
```

Ruby中类的实例在调用方法时的顺序为，该类>包含的模块>该类的父类等这个顺序查找该方法。

``` ruby
module HelloModule
	Version = "1.0"

	def say_hello(name)
		puts "Hello #{name}"
	end
end

class HelloClass
	include HelloModule
end

hello = HelloClass.new
hello.say_hello("Ruby")					#=> Hello Ruby
```
以上代码中调用的是module中的方法。

用*ancestors*方法和*superclass*方法调查类的继承关系。*superclass*方法可直接返回类的父类

``` ruby
p HelloClass.ancestors					#=> [HelloClass, HelloModule, Object, Kernel, BasicObject]
p HelloClass.superclass					#=> Object
```

*Kenerl*是Ruby内部的一个核心模块，Ruby程序运行时所需的共通函数都封装在此模块中。

单一继承的优点就是简单，不会因为过多的继承而导致类之间的关系变得复杂。所以我们需要灵活使用单一继承和Mix-in。

#### Mix-in查找方法的规则
- 优先使用原来中定义的同名方法。
- 在同一个类中包含多个模块时，优先使用最后一个包含的模块。
- 嵌套include时，查找顺序也是线性的。
- 相同的模块被包含两次也上的，第2次以后的会被省略。

#### Object#extend方法
利用Object#extend方法可以使单例类包含模块，并把模块的功能扩展到对象中。

``` ruby
module Welcome
	def welcome(name)
		"#{self} Welcome #{name}"
	end
end

str = "World"
str.extend(Welcome)						#=> 将Welcome模块扩展到str对象上
puts str.welcome("Ruby")				#=> World Welcome Ruby
```

**include**可以帮助我们突破继承的限制，通过模块扩展类的功能；而**extend**则可以帮助我们跨过类，直接通过模块扩展对象的功能.

所有类本身都是Class类的对象，类方法就是类对象的实例方法。可以把类方法理解为下:

- **Class**类的实例方法
- 类对象的单例方法

使用**extend**方法为类对象追加类方法，使用**include**方法追加实例方法。

``` ruby
module ClassMethod
	def class_method
		"class method"
	end
end

module InstanceMethod
	def instance_method
		"instance method"
	end
end

class MyClass
	extend ClassMethod 					#使用extend方法定义类方法
	include InstanceMethod 				#使用include定义实例方法
end

p MyClass.class_method					#=> "class method"
p MyClass.new.instance_method			#=> "instance method"
```