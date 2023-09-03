---

title: "Implicit submission of form when pressing enter key"
date: 2015-06-30 15:08:03 +0800
comments: true
categories: Frontend
tags: [HTML, HTML5]
---
As we know, if we want to submit data in the form, we just need to create a *input* or *button* element in the form and give them the *submit* value to the type attribute. Or using javascript to call `form.submit()`.

But there is third way to submit form. That's the implicit submission of form when pressing the enter key under some situations. The form implicit subbmission supported by all browsers, and html spec strongly recommand do this.
<!-- more -->
#### Implicit submission situation 1
The form will be submitted automatically when there is only one input element in the form and pressing the enter key in the input element.

``` html
<form action="/abc.txt">
	<label for="name">Name:</label>
	<input id="name" name="name" type="text" />
</form>
```
The form will be submitted and get the data from *abc.txt* file when we press the enter key in the input element.

But if the form has multiple input elements, the form can not be submitted automatically. like below codes.

``` html
<form action="/abc.txt">
	<label for="name">Name:</label>
	<input type="text" id="name" name="name">
	<label for="age">Age:</label>
	<input type="text" id="age" name="age">
</form>
```

#### Implicit submission situation 2
For multiple input elements in the form situation, the form can not be submitted automatically. So how to make it implicit submission. This is second situation we are introducing.

If the form has *submit* type element(input, button), the form will be submitted when pressing the enter key in the input element regardless of how many input elements in this form.

``` html 
<form action="/abc.txt">
	<label for="name">Name:</label>
	<input type="text" id="name" name="name">
	<label for="age">Age:</label>
	<input type="text" id="age" name="age">
	<input type="submit">
</form>
```

If you need to run some JavaScript before the form is submitted (validation, data manipulation, etc), do it in a `submit` event handler on the form, not a `click` handler on a button.

#### How to suppress it
The solution is very simple, just prevent the event trigger when pressing the enter key. So we can deal with it in the `keypress` event handler.

``` html
	<form action="/abc.txt">
		<label for="name">Name:</label>
		<input type="text" id="name" name="name">
		<label for="age">Age:</label>
		<input type="text" id="age" name="age">
		<input type="submit">
	</form>
	<script>
		(function(){
			var age = document.getElementById('age');
			age.addEventListener('keypress',function(event){
				if(event.keyCode == 13) {
					event.preventDefault();
				}
			});
		}());
	</script>
```
Note, the code of enter key is 13. Using `event.preventDefault()` to stop the form submission when the keycode is 13. And for above codes, we only suppress the age input element, so when pressing enter key in the name input element, the form still is submitted.