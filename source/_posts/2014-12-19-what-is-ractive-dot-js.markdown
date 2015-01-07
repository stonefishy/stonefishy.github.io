---
layout: post
title: "What is Ractive.js"
date: 2014-12-19 22:18:45 +0800
comments: true
categories: [Ractive.js, JS] 
---

`Ractive.js` is a `template-driven` UI library, it transforms your templates into blueprints for application that are interative by default. `Ractive.js` is very similar with `AngularJS`, and it supports `Two-way binding`, `animations` as well. Another powerfull feature is `SVG` support.   

###Template + Data = UI
You just need to prepare your html template and datas, and the `Ractive.js` will automatically to combine them then give you what you want. Whe you change your datas, it intelligently updates the real DOM.   

###Usage
First, you need to include `Ractive.js` to your application. Just like include other javascript librarys easily. You can download it to local and then include it. And if you want include latest version, you just put below codes to your project.   
``` javascript
    <script src='http://cdn.ractivejs.org/latest/ractive.js'></script>
```
<!-- more -->
Second, we define a container that we want to render. Let's give it id container.
``` html
	<div id="container"></div>
```
And third, just need to define a template. Load template in many ways, for example we can use ajax call to get the template string. and here, we use script tag to include the template.  
``` html
    <script id="template" type="text/ractive">
		<svg width="400" height="200">
    		<rect fill="lightblue" width="100%" height="100%"/>
    		<circle cx="25%" cy="50%" r="\{\{10+temperature}}" fill="gold" stroke="yellow" 
                    opacity='\{\{0.4+temperature/100}}'/>
    		<text x="50%" y="65%" font-size="80" fill="white">\{\{temperature}}℃</text>
		</svg>
	</script>
```
*Note:* don't mind reverse slash symbol. we just use it to transfer meaning. And when you data binding you don't need to add it. remember that!   

As above. We define a svg tag and it contains rectangle,circle shape and text. The circle's radius bind the data \{\{10 + temperature}}，so the radius will update with ß10 plus temperature when the temperature changed. As we can see the temperature has binded the data as well.   

And last step, Let's combine the template and data with `Ractive.js`. the code as below:   

``` javascript
    var ractive = new Ractive({
	 	// The el option can be a node, an ID, or a CSS selector.
    	el:'container',
    	// We could pass in a string, but for the sake of convenience
    	// we're passing the ID of the <script> tag above.
    	template:'#template',
    	// Here, we're passing in some initial data
    	data: {
       	 temperature:50
    	}
    });
```  

So the style as we can see below:   
{% img /assets/images/ractive-svg-test.png 400 200 %}

Now, we can change the temperature data and let `Ractive.js` to update the DOM automatically. So there we increase the temperature by 1 per 100 millisecond, and when the temperature larger than 60 then reset it to 0.   
``` javascript
	 setInterval(function(){
    	var temp = ractive.get('temperature');
    	temp = temp >= 60 ? 0 : ++temp; 
    	ractive.set('temperature',temp);
	 },100);
```
So the radius of circle and the text will automatically changed. This is `Ractive.js` simple usage. Hopeful you like it.
 