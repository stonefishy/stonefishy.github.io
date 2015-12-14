---
layout: post
title: "AngularJs - angular.extend()"
date: 2015-05-16 11:08:31 +0800
comments: true
categories: [AngularJs, Javascript]
---
The `angular.extend()` function has very powerful to copy object to another object, including all of properties from the source object(s).

###Usage
> `angular.extend(dst, src);`
> 
> dst:        **destination object**  
> src:        **source object(s)**   
> 
> return:     **refercen to dst object**

<!-- more -->
###Example
Let's understand how to use it with example instance. Create a normal controller which contains user's name, gender and age. And create a method to get the full description of the user.

``` javascript
angular.Module('app',[])

.app.controller('UserController', ['$scope', function ($scope) {
	$scope.name = 'angular';
  	$scope.gender = 'male';
  	$scope.age = '2';

  	$scope.getDescription = function() {
    	return "My name is " + $scope.name + " and I am " + $scope.gender + " " + $scope.age + 	" years old";
  	}
}]);
```

We can use angular.extend function to do like above functionality, see below:

``` javascript
app.controller('UserController', ['$scope', function($scope) {
  angular.extend($scope, {
    name : 'angular',
    gender : 'male',
    age : '2'
  });

  angular.extend($scope, {
    getDescription : function() {
      return "My name is " + $scope.name + " and I am " + $scope.gender + " " + $scope.age + " years old";
    }
  });
}])
```
And also we can use angular.extend() function to implement mixins. Let's consider the below contrived Logging class.

``` javascript
var debug = true;
var Logger = {
  log: function (msg) {
    console.log(debug ? msg : '');      
  }
};
```

If we set debug variable to true, so it will print message in the browser console window when we invoke the log function. Otherwise nothing will be printed.

Okay, now we create a controler called LoggerController, and "mixin" Logger class to it.

``` javascript
app.controller('LoggerController', ['$scope', function($scope) {
  angular.extend($scope, Logger);

  $scope.print = function (msg) {
    this.log(msg);
  }
}])

``` 

In LoggerController, we "mixin" the Logger class to the $scope, and this will inovke the log function of the Logger class when the print function was called.

