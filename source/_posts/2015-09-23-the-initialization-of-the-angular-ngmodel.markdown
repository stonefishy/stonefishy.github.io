---
layout: post
title: "The initialization of the angular ngModel"
date: 2015-09-23 10:59:23 +0800
comments: true
categories: [AngularJs, Javascript]
---

Recently, I've encoutered a weird problem in the project, it's about the value of the ngModel. Let me explain the problem. see the code

``` html
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<link rel="stylesheet" href="../../bower_components/bootstrap/dist/css/bootstrap.min.css"/>
	<script src="../../bower_components/angular/angular.min.js"></script>
    <script src="../../js/ngModel.js"></script>
</head>
<body ng-app="ModelApp">
	<div class="page-header">
		<h1 class="text-center">ng-model initialize example</h1>
	</div>
	<div class="panel panel-danger" ng-controller="ModelController1">
		<div class="panel-heading">the angular model without initialize:</div>
		<div class="panel-body">
			<div>
				<input type="text" id="myInput1" ng-model="myModel1" value="A"/>
				<label for=""> -> This input tag bundles the ngModel which name is 'myModel1'</label>
			</div>
			<div>
				<input disabled="true" id="displayModelValue1" type="text" value="{{myModel1}}"/>
				<label for=""> -> This input tag displays the value of the above ngModel</label>
			</div>
		</div>
	</div>
</body>
</html>
```
<!-- more -->
In the code, there is a input which id is 'myInput1' has bundled a ngModel which name is 'myModel1'. The value of the input we give it 'A'. And we put this input tag into the ModelController1 angular controller. Also, there is another input tag which id is 'displayModelValue1', the purpose of it is to display the value of the 'myModel1'.

Let's see the controller code.

``` javascript
var ModelApp = angular.module("ModelApp", []);

ModelApp.controller('ModelController1', function($scope) {
	
});
```
You will notice that there is nothing in the ModelController1. Now, running this page. let's see the result.

![](/assets/images/angular-ngModel-1.png)

From the result, the value of the myInput1 is empty, but we have set the default value 'A' to it. So we guess maybe the value has changed. But when we inspect the element, we found the value has been set to 'A' in the html source. so far, I'm so confused. To find the reason, I open the console of the browser, put below javascript code to see the value. It still empty.

``` javascript
$('#myInput1').value
```

![](/assets/images/angular-ngModel-2.png)

So I guess that maybe it is ngModel affect it, the value has been changed by ngModel, but the html source didn't changed. So let's remove the ngModel from that input tag.

``` html
<input type="text" id="myInput1" value="A"/>
```

And now，the value has displayed. it proves our suppose is correct.

Ok, Let's get started to find the reason use a new example.

``` html
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<link rel="stylesheet" href="../../bower_components/bootstrap/dist/css/bootstrap.min.css"/>
	<script src="../../bower_components/angular/angular.min.js"></script>
    <script src="../../js/ngModel.js"></script>
</head>
<body ng-app="ModelApp">
	<div class="page-header">
		<h1 class="text-center">ng-model initialize example</h1>
	</div>
	<div class="panel panel-success" ng-controller="ModelController2">
		<div class="panel-heading">the angular model with initialize:</div>
		<div class="panel-body">
			<div>
				<input type="text" id="myInput2" ng-model="myModel2" value="A"/>
				<label for=""> -> This input tag bundles the ngModel which name is 'myModel2'</label>
			</div>
			<div>
				<input disabled="true" id="displayModelValue2" type="text" value="{{myModel2}}"/>
				<label for=""> -> This input tag displays the value of the above ngModel</label>
			</div>
		</div>
	</div>
</body>
</html>
```
The above html source code is same with before, just the id and ngModel name are different. And there is another different is that we initialize the ngModel in the controller.

``` javascript
ModelApp.controller('ModelController2', function($scope) {
	$scope.myModel2 = "B";
	
});
```
We have set the myModel2 value to 'B' in the ModelController2. But in the html source code, we set the default value to 'A' and bundle it to ngModel. So let's see the result:

![](/assets/images/angular-ngModel-3.png)

The result is 'B' not 'A'. That means the ngModel is initialized in the controller. And then render it to the browser. So this can explain the weird problem. 

The finial reason is that the ngModel is initialized in the controller. If it is not initialize, the value will be empty, like the first example. So if we initialize it in the controller, the ngModel value will be changed with initialzed value.

And also, you can use the `ng-init` directive to initialize the ngModel value which you want.

``` html
<input type="text" id="myInput2" ng-model="myModel2" ng-init="myModel2 = 'A'"/>
```


