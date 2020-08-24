# How does function.name and name binding work?

This is going to be one of those nitpicky articles where I document everything about function name, that I have learned from the spec.  

Let's start with two related but different concepts -
1. `function.name` - When we declare a function, it creates a function object - which has a non-writable property called `name`. `fn.name` typically stores the initial name that the function was defined with.   
This is useful for debugging and readable stack traces.  
	```js
	function foo(){}
	foo.name // "foo"
	```

2. **name binding** - Normally when we declare a function, it also creates/initializes a variable which points back to the function object. For ex -
	```js
	// This creates a variable `foo` in the current scope
	function foo(){}
	// `foo` points to the function object
	// which we can call later
	foo()
	```
	
⚠️  **How are they different?** It just looks like the `name` property stores the name of the variable it created.

Yes, they are somewhat different . Let's look at more examples -
1. You can always store the function in a variable with different name, but `func.name` will NOT change. 
	```js
	// Example 1
	function func1() {}

	var func2 = func1
	func2.name // prints "func1", NOT "func2"
	```
	`name` is only decided based on how the function is created and does not depend on the variable name you use to access it.
2. *Named function expressions* can be bound to a different variable name. Also, any function expression can be used without storing in a variable at all.
	```js
	// A. Diff variable name
	var someVar = function someName(){}
	// Creates only one variable called `someVar`
	// But function.name is `someName`
	
	// B. not stored in any variable, like IIFE
	(function hello(){})()
	(function hello(){}).name // "hello"
	// This DOES NOT create any local variable (aka name binding)
	```
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOlsxODI2MjgyNTAzLC0xNDM4NzY2
OTMwLDE4NjQyNDQ3NTMsOTU5Nzk1MzUyLDQyOTk2NTk2MiwtMj
AwODc3NTcwMCwyMDAxNjY4ODcyLC0yMDgyMTAzMDk1LC0xMjEz
NDY3NDAwLDE2NTg0OTk3MjYsMTgzMDk2Mjg3NCwxMjMwMDI3Nj
I1LDEwNjIxMjM3NzEsMTIyNTg4NjgyMF19
-->