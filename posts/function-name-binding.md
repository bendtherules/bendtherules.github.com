# How does function.name and name binding work?

This is going to be one of those nitpicky articles where I document everything about function name, that I have learned from the spec.  

Let's start with two related but different concepts -
1. `function.name` - When we declare a function, it creates a function object - which has a non-writable property called `name`. `fn.name` typically stores the initial name that the function was defined with.   
This is useful for debugging and readable stack traces.  
	```js
	(function foo(){}).name // "foo"
	```

2. **name binding** - Normally when we declare a function, it also creates/initializes a variable which points back to the function object. For ex -
	```js
	// This creates a variable `foo` in the current scope
	function foo(){}
	// which we can use later
	foo()
	```
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOlstMjA4MjEwMzA5NSwtMTIxMzQ2
NzQwMCwxNjU4NDk5NzI2LDE4MzA5NjI4NzQsMTIzMDAyNzYyNS
wxMDYyMTIzNzcxLDEyMjU4ODY4MjBdfQ==
-->