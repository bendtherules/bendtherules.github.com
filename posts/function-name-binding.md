# How does function.name and name binding work?

This is going to be one of those nitpicky articles where I document everything about function name, that I have learned from the spec.  

Let's start with two related but different concepts -
1. `function.name` - When we declare a function, it creates a function object - which has a non-writable property called `name`. `fn.name` typically stores the initial name that the function was defined with.   
This is useful for debugging and readable stack traces.  
	```js
	function foo() {}
	foo.name // "foo"
	```

2. **name binding** - When we declare a function, normally it also creates/sets a variable which stores the function.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg5Mzk4NjQ5OCwxODMwOTYyODc0LDEyMz
AwMjc2MjUsMTA2MjEyMzc3MSwxMjI1ODg2ODIwXX0=
-->