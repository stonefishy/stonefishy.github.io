---

title: "Conditional Stylesheets for IE version"
date: 2015-01-28 17:35:06 +0800
comments: true
categories: Frontend
tags: [CSS, IE]
---

The tool of choice for fighting IE problems is the `conditional stylesheet`. IE provides comment tags, supported all the way up to the current IE 8 to target specific versions, as well as greater-than/less-than stuff for targeting multiple versions at once.

### Why use conditional stylesheets?   
*  You got problems, they need fixin'
*  Keeps your code hack-free and valid
*  Keeps your main stylesheet clean
*  Perfectly acceptable technique, sanctioned by Microsoft   

<!-- more -->
And remember, these conditional tags don't have to be used only for CSS. You could load JavaScript, or even use them down in the content of your site to display special IE-specific messages.

### The Code

This would go in your <head> with all the other regular CSS \<link>ed CSS files. The opening and closing tags should be familiar, that's just regular ol' HTML comments. Then between the brackets, "IF" and "IE" should be fairly obvious. The syntax to note is "!" stand for "not", so !IE means "not IE". gt means "greater than", gte means "greater than or equal", lt means "less than", lte means "less than or equal."

> **Note that IE 10 and up DO NOT support conditional comments at all.**

Target ALL VERSIONS of IE
``` html
<!--[if IE]>
	<link rel="stylesheet" type="text/css" href="all-ie-only.css" />
<![endif]-->
```
Target everything EXCEPT IE
``` html
<!--[if !IE]><!-->
	<link rel="stylesheet" type="text/css" href="not-ie.css" />
 <!--<![endif]-->
```
Target IE 7 ONLY
``` html
<!--[if IE 7]>
	<link rel="stylesheet" type="text/css" href="ie7.css">
<![endif]-->
```
Target IE 6 ONLY
``` html
<!--[if IE 6]>
	<link rel="stylesheet" type="text/css" href="ie6.css" />
<![endif]-->
```
Target IE 5 ONLY
``` html
<!--[if IE 5]>
	<link rel="stylesheet" type="text/css" href="ie5.css" />
<![endif]-->
```
Target IE 5.5 ONLY
``` html
<!--[if IE 5.5000]>
<link rel="stylesheet" type="text/css" href="ie55.css" />
<![endif]-->
```
Target IE 6 and LOWER
``` html
<!--[if lt IE 7]>
	<link rel="stylesheet" type="text/css" href="ie6-and-down.css" />
<![endif]-->
<!--[if lte IE 6]>
	<link rel="stylesheet" type="text/css" href="ie6-and-down.css" />
<![endif]-->
```
Target IE 7 and LOWER
``` html
<!--[if lt IE 8]>
	<link rel="stylesheet" type="text/css" href="ie7-and-down.css" />
<![endif]-->
<!--[if lte IE 7]>
	<link rel="stylesheet" type="text/css" href="ie7-and-down.css" />
<![endif]-->
```
Target IE 8 and LOWER
``` html
<!--[if lt IE 9]>
	<link rel="stylesheet" type="text/css" href="ie8-and-down.css" />
<![endif]-->
<!--[if lte IE 8]>
	<link rel="stylesheet" type="text/css" href="ie8-and-down.css" />
<![endif]-->
```
Target IE 6 and HIGHER
``` html
<!--[if gt IE 5.5]>
	<link rel="stylesheet" type="text/css" href="ie6-and-up.css" />
<![endif]-->
<!--[if gte IE 6]>
	<link rel="stylesheet" type="text/css" href="ie6-and-up.css" />
<![endif]-->
```
Target IE 7 and HIGHER
``` html
<!--[if gt IE 6]>
	<link rel="stylesheet" type="text/css" href="ie7-and-up.css" />
<![endif]-->
<!--[if gte IE 7]>
	<link rel="stylesheet" type="text/css" href="ie7-and-up.css" />
<![endif]-->
```
Target IE 8 and HIGHER
``` html
<!--[if gt IE 7]>
	<link rel="stylesheet" type="text/css" href="ie8-and-up.css" />
<![endif]-->
<!--[if gte IE 8]>
	<link rel="stylesheet" type="text/css" href="ie8-and-up.css" />
<![endif]-->
```

*Note: The original artical you can see [here](http://css-tricks.com/how-to-create-an-ie-only-stylesheet/)*