---

title: "Analyze call function of javascript"
date: 2015-05-20 19:24:35 +0800
comments: true
categories: Frontend
tags: [JavaScript]
---

The `call()` function is a fantastic method in javascript. It makes some people confusion some times, like me. Here let us to make it clear.    

### Syntax
`call([thisObj[,arg1[, arg2[, [,.argN]]]]])`   
 - **thisObj** (optional): it's object which can be act as currently `this` object.   
 - **arg1, arg2, argN** (optional) : the parameters which can be passed to the method.

The *call* method can be used to instead of another object to invoke a method. It can change the object context of a function to a new object which *thisObj* referenced.    

<!-- more --> 

**Note**: *The thisObj is a optional parameter, it will reference to Global object if it is not provided. And the `call` method is only used by `Function` object.*  

### Example
Let's use fake code to explain it firstly. see below code:     

``` javascript
obj1.method1.call(obj2, arg1, arg2);
```
The functionality of *call* method is that *obj2* is able to invoke the *method1* of *obj1* with *arg1*, *arg2* parameters.      

Let's see a simple example:

``` javascript
var add = function(a, b) {
	return a + b;
};

var sub = function(a, b) {
	return a - b;
};

var result = add.call(sub, 1, 2);

```
The value of *result* variable is 3, not -1. Because the *sub* function object has been insteaded by *add* function object. So the `add.call(sub, 1, 2)` actually is `add(1, 2)`.  
   
**Note**: *The *function* actually is a *object* in javascript, and the function name is a reference of `Fuction` object.   

Okay, so let's see a little complex example:
 
``` javascript
function ClassOne() {
	this.message = "class one";
	this.showMessage = function () {
		console.log(this.message);
	};
};

function ClassTwo() {
	this.message = "class two";
};

var classOne = new ClassOne();
var classTwo = new ClassTwo();
classOne.showMessage.call(classTwo);
```
This will print the 'class two' message in the console window of browser. You can execute above code fragment to check it.    
In this example, the *classTwo* object doesn't have *showMesage* function, but he want to display his message, so he can invoke *classOne*'s with `call` method.   

So far, maybe you will think we can use the `call` function to simulate the `Inheritance` of `OOP`. You are right, let's get the example.

``` javascript
function BaseClass() {
	this.message = "base class";
	this.showMessage = function () {
		console.log(this.message);
	};
};

function MyClass() {
	BaseClass.call(this);
};

var myClass = new MyClass();
myClass.showMessage();
``` 
So the *MyClass* inherites the *BaseClass*. The `BaseClass.call(this)` that means using the object of *BaseClass* to instead of *this* object. So the *MyClass* can access the property and function of *BaseClass*.   



