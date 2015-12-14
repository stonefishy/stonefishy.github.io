---
layout: post
title: "Markdown syntax for span elements"
description: ""
category: Markdown
---

###Links
`Markdown` supports two styles for Links:  

- *inline* 
	> ***Syntax:***  
	> `[inline link text](www.inlinelink.com "optional title")`

    > ***Effect:***  
    >[inline link text](www.inlinelink.com "optional title")
	
<!--more-->
- *reference*
   > ***Syntax:***  
   > `[reference link text][id]`
   > `[id]: www.referencelink.com "optional title"`

   >***Effect:***  
   >[reference link text][1]

>***Note:***  
>The `optional title` is not necessary.  You can input it for your demand.
>Link definitions can be placed anywhere in your `Markdown` document.

[1]: www.referencelink.com "optional title" 


###Emphasis
`Markdown` uses asterisk(*) and underscore(_) symbol to wraps the text that you want to emphasis.  

- Use one symbol asterisk`*` or underscore`_` to wrap text, it will produce the `< em>` tag of HTML Tags.  
	>***Syntax:***  
	>`*Sample Text*`  or `_Sample Text_`

	>___Effect:___  
	>*Sample Text* or _Sample Text_

- Use double symbols asterisk`*` or underscore`_`, it will produce the `< strong>` tag of HTML Tags.  
	>***Syntax:***  
	>`**Sample Text**` or `__Sample Text__`

	>***Effect:***  
	>**Sample Text** or __Sample Text__

###Code
`Markdown` uses backtick quotes`` ` `` to wrap the code text. It will produce the `< code>` tag of HTML tags.
>***Syntax:***  
>`` `Code statement` ``

>***Effect:***  
>`Code statement`

And if you want to wrap the backtick quote`` ` `` symbol. you can use double backticks as the opening and closing delimiter.  
>***Syntax:***  
> \` \` There is a literal backtick(\`) here \` \`

>***Effect:***  
>`` There is a literal backtick(`) here``

###Image
`Markdown` uses an image syntax that is intended to resemble the syntax for links, allowing *inline* and *reference*  

- *inline*
    >***Syntax:***  
	>`![alt text](http://daringfireball.net/graphics/logos/ "optional title")`  
	
    >***Effect:***  
	>![alt text](http://daringfireball.net/graphics/logos/ "optional title")

- *reference*
	> ***Syntax:***  
	>`![alt text][id]`
	>`[id]:http://daringfireball.net/graphics/logos/ "optional title"`  
	
    >***Effect:***  
	>![alt text][id]
	
[id]:http://daringfireball.net/graphics/logos/ "optional title"

>***Note:***  
>`Markdown` has no syntax for specifying the dimensions of an image; if this is important to you, you can simply use regular HTML `< img>` tags.

	
	

