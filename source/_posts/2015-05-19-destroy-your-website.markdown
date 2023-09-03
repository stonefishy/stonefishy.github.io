---

title: "Destroy your website"
date: 2015-05-19 12:16:00 +0800
comments: true
categories: Frontend
tags: [JavaScript]
---

Ha, Confusion? Don't, it's true. Let me introduce to you how to destroy the website you want.   
Click the below button:  
<button type="button" onclick="javascript:var s = document.createElement('script');s.type='text/javascript';document.body.appendChild(s);s.src='/javascripts/libs/asteroids.min.js';void(0);">Destroy this website</button>    
Use the **Arrow** key to move the rocket, and press the **Space** key to fire. And also Press and hold **B** to find targets which can be destroy.  
<!-- more -->
Visit [http://erkie.github.com/](http://erkie.github.com/) and drag the graphic onto your bookmarks bar to use this on whatever webpage you want to destory.

Actually, it's very easy to do this with javascript.   
1. Download this javascript library from [here](), and put it in your project directory.   
2. Adding below code snippet to your element target you want to triggle   

``` javascript
javascript:var jst = document.createElement('script');
jst.type='text/javascript';
document.body.appendChild(jst);
jst.src='/javascripts/libs/asteroids.min.js';void(0);
```

Note: you need to change the src of the variable **jst** to your javascript library's location, like below:   

``` javascript
	jst.src = 'YOUR LIBRARY LOCATION';
```

So, it's quite simple.