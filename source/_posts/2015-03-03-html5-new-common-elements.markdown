---
layout: post
title: "HTML5 - New Common Elements"
date: 2015-03-03 17:13:53 +0800
comments: true
categories: [HTML5]
---

In `HTML5`, It adds a mount of elements, these elements are more easier to present the document in browser.

###Structure element
Before `HTML5`, we just use `div` tag to act as the `Document Structure Element`, but now the `HTML5` supports more elements like `section`, `article`, `nav`, `aside`, `header`, `footer` and so on.  

<!-- more --> 

#####article
`article` use to present an entire, independent article. We should use it when we need to present a entire, independent document content.   
It can contain `header` tag to define the header of the article.      
It can contain `footer` tag to define the footer of the article.    
It can contain `section` tag to split content to multiple paragraphs.     
Also, it can be nested by itself.   

#####section
`section` splits the page content to multiple blocks, and it usually contains title and content. And here the title stand for `h1`....`h6` tag. `section` can contains `article` and `section` tag.

#####nav
`nav` tag use to define the navigation bar, include the top of page, the left or right side, and the bottom and so on.

#####aside
It's recommend to use it to present the side of page with `CSS`.

#####header
The `header` tag use to define the header of the `article`, and it can contain multiple title tags (`h1`....`h6`) with `hgroup` tag. And also the common tags like `p`, `span` can be nested in it.   

#####hgroup
It's mainaly use for including multiple title tag.

#####footer
The footer of page, use to contains foot information, copy right and author related stuff.

#####figure
`figure` tag express an independant image area, it may contain one or more images， in addition to this tag can contain a new tag `figcaption` to express the title of images.

###Semantic related elemnent
#####mark
It is mark some information need to be noticed.

####time
It presents some content want to be time, date. Setting the `datetime` attribute's value to present which date formatter you want.

####Special function element
####meter
`meter` tag express an as known maxium and minimum counter. It has many attributes to set. `value`, `min`, `max`, `low`, `high`, `optimum`.   
``` html
<div>
  	current speed of car: <meter id="meter" max="200" min="0" low="0" high="180" value="150">kilometre/hour
</div>
```
{% img /assets/images/meter-tag.png 300 80 %}

####progress
`progress` tag use to express an progress bar. It has two attributes, one is `max`, another is `value`.

``` html
<div>
   the progress of un-finished task: <progress id="progress" max="1" value="0.4"/>
</div>
```
{% img /assets/images/progress-tag.png 350 100 %}