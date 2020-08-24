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
	
⚠️ **Are they even different?** It just looks like the `name` property holds the name of the variable it created. Also,  `name` is non-writable - so, it's not like you can change it later.  

To understand, let's look at more examples -
1. You can always store the function in a different variable, but `func.name` will NOT change.  `name` is only decided based on how the function is created and does not depend on the variable name you use to access it.
	```js
	// Example 1
	function func1() {}

	var func2 = func1
	func2.name // prints "func1", NOT "func2"
	```
2. sdsd
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOls0MjY1ODExNTksLTIwMDg3NzU3
MDAsMjAwMTY2ODg3MiwtMjA4MjEwMzA5NSwtMTIxMzQ2NzQwMC
wxNjU4NDk5NzI2LDE4MzA5NjI4NzQsMTIzMDAyNzYyNSwxMDYy
MTIzNzcxLDEyMjU4ODY4MjBdfQ==
-->