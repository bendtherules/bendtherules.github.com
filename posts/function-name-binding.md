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
	// `foo` points to the function object
	// which we can call later
	foo()
	```
⚠️ Are they even different? It just looks like that `name` property holds the name of the variable. (Specially given that `name` is non-writable.)

<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOls4NDQzMjQ3NTYsMjAwMTY2ODg3
MiwtMjA4MjEwMzA5NSwtMTIxMzQ2NzQwMCwxNjU4NDk5NzI2LD
E4MzA5NjI4NzQsMTIzMDAyNzYyNSwxMDYyMTIzNzcxLDEyMjU4
ODY4MjBdfQ==
-->