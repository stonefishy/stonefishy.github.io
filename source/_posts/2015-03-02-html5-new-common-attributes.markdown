---
layout: post
title: "HTML5 - New Common Attributes"
date: 2015-03-02 20:33:14 +0800
comments: true
categories: [HTML5]
---
`HTML5` adds new common attributes for original `HTML` Tag, and these attributes enhances the `HTML` elements's function.   

###contentEditable
The `contentEditable` attribute supports most tags. The browser allows user edits the content of element if this attribute is set to true. These elements is not like input or textarea tags. They are not support editable content like table, div, span and so on these tags.   

<!-- more -->

`contentEditable` attribute can be inherited by children tags. If the contentEditable is true, all children tags's contentEditable will be true except we define it to false.   

``` html
<!DOCTYPE html>
<head>
    <title>contentEditable</title>
    <style>
        div {
            width: 240px;
            height: 150px;
        }
        table {
            width:inherit;
            height: 100px;
            border: 1px solid black;
        }
        
        table > tr {
            background-color: blue;
        }
        
        .myDiv {
            width:inherit;
            height: 30px;
            line-height: 30px;
            margin-top:10px;
            border: 1px solid black;
        }
    </style>
    <script type="text/javascript">
        var turnOnContentEditable = function() {
            var target = document.getElementById("target");
            target.contentEditable = true;
        };
    </script>
</head>
<body>
   <div id="target" contentEditable="false">
       <table border="1">
           <tr>
               <td>Java</td>
               <td>CSharp</td>
           </tr>
           <tr>
               <td>Javascript</td>
               <td>Python</td>
           </tr>
       </table>
       <div class="myDiv">
           This is Div Tag
       </div>
   </div>
    <input type="button" onclick="turnOnContentEditable();"value="Turn On Edit Mode"></input>
</body>
```

The root div is can't be edited by default. when we click button to turn on contentEditable attribute. all children tags of this tag can be edited.   

`contentEditable` attribute is false:   
{% img /assets/images/contentEditable-before.png 300 350 %}  

`contentEditable` attribute is true:   
{% img /assets/images/contentEditable-after.png 300 350 %}  

###designMode
`designMode` attribute is actually entire document's `contentEditable`. If we turn on the `designMode` attribute of the entire page, all the elements which supports `contentEditable` attributes can be edited. It is false by default.    

We can just use javascript to turn on the document's `designMode`.   

``` javascript
document.designMode = "on";
```

###hidden
`HTML5` supports `hidden` attribute for all elements. The `hidden` attribute can be set to 'false' and 'true' both values. The browser doesn't display the element when `hidden = 'true'`.   

The `hidden = 'true'` actually like we write `display:none` is `CSS` file. And also we can get its value by `hidden`.

###spellcheck
`HTML5` add new attributes `spellcheck` for editable elements like `input`,`textarea`, ... The `spellcheck` can be set to 'true', 'false' both values. The browser will check the content user input and prompt error message for spell error words when it is set to 'true'. 

``` html
<input type="text" spellcheck="true"/>
```   
{% img /assets/images/spellcheck.png 300 80 %}
>*Note: Supports `spellcheck` attribute's browsers include `Chrome`, `Opera`, `Safari`, the `IE`, `FireFox` doesn't support it for now.*
