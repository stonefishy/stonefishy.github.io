---
layout: post
title: "CSS tricks for IE6,IE7,IE8,IE9"
date: 2015-01-28 16:35:34 +0800
comments: true
categories: [CSS, IE] 
---

As web developer, we usually to fix the style for special version of IE. Here are the newest CSS tricks for IE6, IE7, IE8, IE9. This is very helpful for you.   

``` css
#ie-tricks{
 
	/* All browsers */
	color:red;

	/* All browsers but IE6 */
	color:red !important;

	/* Only works in IE6 */
	_color:red;

	/* IE6, IE7 */ 
	*color:red; 

	/* Only works in IE7*/
	+color:red;

	/* Only works in IE7 */
	*+color:red;

	/* IE6, IE7, IE8, IE9 */ 
	color:red\9;

	/* IE8, IE9 */ 
	color:red\0; 

	/*Only works in IE9*/
	color:red\9\0;

}
```
