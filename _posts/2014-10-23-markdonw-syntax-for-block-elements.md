---
layout: post
title: "markdonw syntax for block elements"
description: "markdown syntax"
category: Technique
tags: [Markdown]
=======
---
{% include JB/setup %}
#Markdown syntax for block elements

---
###Line breaks
Markdown can't break line automatically in paragraph. The break line is a break tag `< br />` in HTML. If you want to break line in paragraph, you need to `end a line with two or more spaces`, then type return.  

###Heads
Markdown supports two styles for heads, one style is Setext, another is atx.  

- Setext style headers are "underlined" using equal signs `=` (for `first-level-headers`) and using dashed signs `-` (for `second-level-headers`).  

```
This is an H1
====
This is an H2
----
```

- Atx style headers use 1 to 6 hash characters `#` at the start of the line, corresponding to header levels 1-6.  

```
#This is an H1
##This is an H2
######This is an H6
```

###Blockquotes
Just put a `>` symbol before each line, like this:  

```
> This is a blockquote line 
```

You can put the symbol `>` before the first line of a hard-wrapped paragraph.  
And also blockquotes can be nested (i.e. a blackquote-in-a-blackquote) by adding additional levels of the symbol `>`  

```
> This is first level blockquote
> > This is nested blockquote 
```

Blockquotes can contains other Markdown elements, including header, list, and codeblocks.  

```
> # This is an H1
> - This is first list item
> - This is second list item
```

###Lists
Markdown supports ordered (numbered) and unordered (bulleted) lists.  

* Unordered lists use asterisks `*`, pluses `+`, hyphens `-` before each list line.  

```
* item1
* item2
+ item3
- item4
```

* Ordered list user numbers followed by each line:  

```
1. item1
2. item2
3. item3
```

>***Note:***
>If you want a ordered list, you need use ordinal numbers in you ordered Markdown lists, and start 1 at first item.  

###Code blocks
Pre-formatted code blocks are used fro programming or other markup source code, rather than forming normal paragraph.  

Its' very simple to produce code blocks, just indent every line of block at least 4 spaces or 1 tab.  

	This codeblock use 1 tab to generate

###Horizontal rules
The horizontal rule tag < hr /> is used to divide up and down content.  and you can place at least three asterisks `*`, hyphens `-`, or underscores `_` on a line by themselves.  

	-----
	*****
	_____