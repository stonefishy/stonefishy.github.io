---

title: "Ruby修炼之道(6)"
date: 2015-08-25 10:12:07 +0800
comments: true
categories: Backend
tags: [Ruby]
---
### Ruby的类
和其他面向对象语言一样，Ruby的类也表示对象的种类。通过**class**方法可以知道某个对象属于哪个类。

``` ruby
"hello".class				#=> String
1.class					#=> Fixnum
```
判断某个对象时候属于某个类时，可以使用**instance_of?**方法

``` ruby
"hello".instance_of?(String)			#=> true
1.instance_of? Fixnum					#=> true
```
<!-- more -->
通过扩展已定义的类来创建新类成为*继承*，通过继承，我们可以实现以下事情：

- 在不影响原有功能的前提下追加新功能
- 重定义原有功能，使名称相同的方法产生不同的效果
- 在已有功能的基础上追加处理，扩展已有共更能

**BasicObject**类是Ruby中所有类的父类，它定义了Ruby对象的最基本功能。普通对象所需要的类一般都被定义为**Object**类。字符串、数组等都是Object类的子类。

子类与父类的关系称为"is-a"关系。我们可以通过类的继承关系使用**is_a?**方法反向追查对象是否属于某个类

``` ruby
"hello".is_a?(String)				#=> true
"hello".is_a?(Object)				#=> true
```

*注：**instance_of?**方法与**is_a?**方法已经在Object类中定义过，因此普通的对象可以直接使用者两个方法。

#### 类的定义
使用**class**关键字定义类，类名的首字母必须大写

	class 类名
		类的定义
	end
类定义中有个特别的方法名为**initialize**方法，当我们使用**new**方法生成新的对象时，initialize方法会被调用，同时new方法的参数也会被原封不动的传递给initialize方法。

定义一个Greeter类，当对象被创建时，会将new方法中得参数传递给initialize方法中得实例变量@name，如果不传参数，则使用默认的值Ruby传给实例变量，在say_hello实例方法中，将其打印出来。

``` ruby
class Greeter
	def initialize(name = "Ruby")
		@name = name
	end

	def say_hello
		puts "Hello #{@name}"
	end
end
```
利用Greeter类的对象来问候，其执行结果如下

``` ruby
default_greeter = Greeter.new
default_greeter.say_hello					#=> Hello Ruby

world_greeter = Greeter.new("World")
world_greeter.say_hello						#=> Hello World
```
*注：引用未初始化的实例变量时的返回值为nil*

在上面的Greeter类中，@name实例变量是不能被外部直接访问或赋值的，需要通过方法来访问对象的内部。我们可以在Greeter类内部定义两个方法来解决这个问题。

```
class Greeter
	#other codes in here
	
	def name					#获取@name
		@name
	end

	def name=(value)			#修改@name
		@name = value
	end
end
```
有了这这两个方法，现在我们可以访问@name实例变量了。

``` ruby
default_greeter.name					#=> Ruby
default_greeter.name = "Python"
default_greeter.say_hello				#=> Hello Python
```
但是，如果一个类里面有多个实例变量需要被访问时，定义这么多得方法会使得代码量过多，有没有像C#中的属性访问器get/set之类的呢。其实Ruby也为我们提供了这个，称为存取器。使用**attr_reader**, **attr_writer**, **attr_accessor**，只要指定实例变量名的符号（symbol）,注意，此处是实例变量的符号，Ruby就会自动定义相应的存取器。

	attr_reader :name			只读 (定义name方法)
	attr_writer :name			只写 (定义name=方法)
	attr_accessor :name			读写 (定义以上两个方法)
	
通过使用他们，我们的Greeter类可以简化为如下

``` ruby
class Greeter
	attr_accessor :name
	
	def initialize(name = "Ruby")
		@name = name
	end

	def say_hello
		puts "Hello #{@name}"
	end
end
```
Ruby中还提供了特殊变量**self**.使用**self**这个特殊变量可以引用方法的接受者。

``` ruby
class Greeter
	attr_accessor :name

	def initialize(name = "Ruby")
		@name = name
	end

	def say_hello
		puts "Hello #{@name}"
	end

	def say_bye
		puts "Bye #{self.name}"
	end
end
```
使用在实例方法内部使用@name和使用self.name是一样的意思，只不过self调用的是name方法。**self**即使对像的本身，相当于C#、Java中的**this**对象。所以上面的self.name可以省略self，直接调用name方法。

但是，在调用像*name=*方法这样的以*=*结束的方法时，有一点我们需要注意。因为在Ruby中，定义局部变量也是用*=*

``` ruby
class Greeter
	def test_name
		name = "Ruby"					#定义局部变量name并赋值
		self.name = "ruby"			#调用Greeter类的name=方法
	end
end
```
所以此时省略self后，含义是不一样的。

#### 类方法的定义
如果方法的接受者是类本身（类对象）的方法成为类方法。其定义方式主要有以下两种

1. 在**class << 类名 ~ end**这个特殊的类中定义实例方法的形式

		class << 类名
			方法的定义
		end

为Greeter类定义类方法*good_morning*

``` ruby
class << Greeter
	def good_morning(name)
		puts "Good morning #{name}"
	end
end
```

2. 使用**def 类名.方法名 ~ end**这样的形式来定义类方法。

		def 类名.方法名
			方法的定义
		end

``` ruby
def Greeter.good_morning(name)
	puts "Good morning #{name}"
end
```
注意以上两种定义形式都在类的外部定义，如果在类的内部定义，可以将类名用**self**，这时self引用的对象是该类本身。

``` ruby
class Greeter
	class << self
		def good_morning(name)
			puts "Good morning #{name}"
		end
	end
end

class Greeter
	def self.good_morning(name)
		puts "Good morning #{name}"
	end
end
```

*注：**class << 类名 ~ end** 这种写法的类定义称为单例类定义，单例类定义中定义的方法称为单例方法*

#### 常量
在class中定义常量，通过使用**类名::常量名**形式来实现外部访问.常量是不可修改的。

``` ruby
class Greeter
	Hello = "Hello"
end

p Greeter::Hello				#=> "Hello"
```

#### 类变量
以**@@**开头的变量成为类变量，类变量是该类中所有实例的共享变量，这点和常量类似，不同的是这个类变量可以被修改。当然，从类的外部访问类变量时也需要存取器，但是不能使用*attr_accessor*,它使属于实例变量的，所以我们需要自己定义，使用类方法来获取类变量是个好的形式

``` ruby
class Greeter
	@@greeter_count = 0
	attr_accessor :name

	def initialize(name = "Ruby")
		@name = name
	end

	def say_hello
		puts "Hello #{@name}"
		@@greeter_count += 1
	end
	
	def Greeter.greeter_count
		@@greeter_count
	end
end

Greeter.greeter_count					#=> 0
greeter = Greeter.new("world")
greeter.say_hello
greeter = Greeter.new
greeter.say_hello
Greeter.greeter_count					#=> 2
```

#### 限制方法的调用
和其他面向对象语言一样，Ruby也有方法的访问修饰符，分别为**public**，**protected**，**private**。其表达的含义也一样。

- public 以实例的方法的形式向外部公开
- protect 仅向同一个类或子类公开
- private 仅内部类可调用

定义方式，有两种，一种是访问修饰符后面带方法符号

	public :方法1, :方法2
	
另外一种是后面不带参数，那么下面所有定义的方法均在这个访问修饰符下，直到有新的访问修饰符。

``` ruby
class Greeter
	attr_accessor :name

	def initialize(name = "Ruby")
		@name = name
	end

	public
	def say_hello
		puts "Hello #{@name}"
	end

	def say_bye
		puts "Bye #{self.name}"
	end
	
	def say_haha
		puts "Haha #{@name}"
	end
	
	private :say_haha
end
```
如以上方法的定义，public修饰符下定义了3个方法，这个3个方法都可以在外部访问，但是最后我们又将最后一个方法标记为private。所以如果在外部企图访问这个方法，将跑出如下错误信息

	class_definition.rb:47:in `<main>': private method `say_haha' called for #<Greeter:	0x007fb57c101510 @name="Ruby"> (NoMethodError)

如果没有指定访问级别的方法默认为public，但是**initialize**方法是个例外，它通常被定义为private

### 扩展类
Ruby允许我们在已经定义好的类中添加方法。即为扩展方法。如在String类中定义一个计算字符串单词数的方法

``` ruby
class String
	def count_word
		ary = self.split(/\s+/)	#用空格分割接收者
		ary.size
	end
end

str = "Hello Ruby"
p str.count_word				#=> 2
```

#### 继承
使用class关键字指定类名的同时指定父类名

	class 类名 < 父类名
		类定义
	end
	
定义类时没有指定父类的情况下，Ruby会默认该类为**Object**类的子类。通过类对象调用**instance_methods方法，可以得到以符号形式返回的该类的实例方法

``` ruby
	Greeter.instance_methods
```

如果想定义**BasicObject**的子类，我们需要明确指定BasicObject类为父类；而Object类为父类时不需要。

#### alias
**alias**即别名，在Ruby中我们可以给方法设置别名，设置别名也两种方式，一种alias后面带方法名参数，第二种后面带方法符号名参数

	alias 别名	原名		#使用方法名
	
	alias :别名 :原名		#使用符号名
	
``` ruby
class Greeter
	def say_hello
		puts "Hello"
	end
end

class RubyGreeter < Greeter
	alias old_say_hello say_hello  #将继承的say_hello方法设置别名为old_say_hello
	
	def say_hello
		puts "Hello Ruby"
	end
end

rubyGreeter = RubyGreeter.new
p rubyGreeter.old_say_hello		#=> "Hello"
p rubyGreeter.say_hello				#=> "Hello Ruby"
```

#### undef
**undef**用于删除已有方法的定义。与alias一样，参数可以指定方法名或者符号名。

	undef 方法名		#使用方法名
	
	undef :方法名		#使用符号名
	
在子类中希望删除父类的方法时可以直接使用undef。试图访问已删除的方法将会引发*NoMethodError*错误。

``` ruby
class Greeter
	def say_hello
		puts "Hello"
	end
end

class RubyGreeter < Greeter
	undef say_hello  #删除继承的say_hello
end

rubyGreeter = RubyGreeter.new
p rubyGreeter.say_hello						#=>抛出异常，undefined method `say_hello' for #<RubyGreeter:0x007fbff4167220> (NoMethodError)
```

#### 单例类
利用单例类定义，可以给对象添加方法（单例方法）。单列类定义被用于定义对象的专属实例方法。

```
str1 = "Ruby"
str2 = "Ruby"

class << str1
	def hello
		puts "Hello #{self}"
	end
end

p str1.hello 						#=> "Hello Ruby"
p str2.hello						#=>引发NoMethodError错误
```
