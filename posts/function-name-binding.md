# How does function.name and name binding work?

This is going to be one of those nitpicky articles where I document everything about function name, that I have learned from the spec.  

# Intro

Let's start with two related but different concepts -
1. `function.name` - When we declare a function, it creates a function object - which has a non-writable property called `name`. `fn.name` typically stores the initial name that the function was defined with.
	```javascript
	function foo(){}
	foo.name // "foo"
	```
   This is useful for debugging and readable stack traces.  
 
2. **name binding** - Normally when we declare a function, it also creates a variable in the current scope which points to the function object. For ex -
	```js
	// This creates a variable `foo` in the current scope
	function foo(){}
	// `foo` points to the function object
	// So, we can use it to call the function
	foo()
	```
	This can be called as name binding.
	
## ⚠️  **How are they different?** 
It's easy to get confused here. Right now, it looks like `.name` just stores the name of the variable which name binding created. (That is, `foo.name` is just the variable name `"foo"` as string). Then, are they really different?

Yes, they are. Let's look at more examples -

1. You can always store the function in a variable with different name, but `func.name` will NOT change. 
	```js
	// Example 1
	function func1() {}

	var func2 = func1
	func2.name // prints "func1", NOT "func2"
	```
	`name` is only decided based on how the function is created and does not depend on the variable name you use to access it.
	
2. *Named function expression* can be bound to a variable name which is different from it's `function.name`. Also, function expressions can be used without storing in a variable at all.
	```js
	// A. Diff variable name
	var someVar = function someName(){}
	// Creates only one variable called `someVar`
	// But function.name is `someName`
	
	// B. not stored in any variable, ex - IIFE
	(function foo(){})()
	(function foo(){}).name // "foo"
	// This DOES NOT create any variable in this scope
	hello // DOESN'T exist
	```

⭐️ So, even though `function.name` and name binding is related, they are not always the same thing. In other words, creating a named function doesn't necessarily create a variable with the same name (in the current scope).

(From now on, I'll use the terms `.name` and bound name to distinguish between these two.)

Now, let's actually look at how both the names behave for different function syntaxes (hint: they are different).


# Function statement

```js
function hello() {}
// In generic form,
// function identifier(paramList) {body} 
```

For function statements, `.name` is simply string form of the identifier `hello` - that is "hello".  
And it's always going to be a string, because you can't use a expression or symbol in the identifier/name part of function statements. (Ex - `function Symbol("abc"){}` - this is NOT valid) 

Llhe

# Func expression and Arrow function


# Method and object literal

Todos -
1. if name is symbol? (for expressions)
2. if anonymous, name=?
3. prefix in name (bound)
4. default export
5. name available inside/outside?
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOlstMTQ4NDk3ODA4NiwtMTIzOTE3
MzI5MywxNjU2MTIwNTQwLC0xOTU4MDQ4NzY4LC0xMDcxNTUwNT
k2LDEwOTk1NjYsLTEyODE4MDUyMDEsLTIwMjg2NzIxODYsMjA1
NjMxNzkxMywyMDU2NDcyMzQ3LC0xNDk5Mzg2NDA1LDI0OTkyMz
I3MiwzNzE1MzE1OTYsLTkyMjE2NjQyLDM2NDE2Mzc3Miw0NTY2
MDgyOTgsLTEyOTE3NzA4ODEsMTI3NTUwNzUzOCwtMTE2ODY0Mj
k5LDE3MzQwOTQ0NjhdfQ==
-->