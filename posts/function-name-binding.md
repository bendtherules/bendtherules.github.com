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

⭐️ For function statements, `.name` is simply string form of the identifier `hello` - that is the string "hello".  
And it's **always** going to be a string, because you can't use a expression or symbol in the name section (identifier) of function statements. Ex - `function Symbol("abc"){}` - this is NOT valid.

⭐️ What about name binding then? 
```js
function hello() {
  // I can use it here, INSIDE
  hello() ✅
}

// And also here, OUTSIDE
hello() ✅
```

We want to look at two things -
1. Can I call the function using its name in the OUTSIDE scope?  
2. Can the function call itself using its own name? We are calling this as the INSIDE scope. This is useful for recursion.

For a more precise definition, OUTSIDE = lexical scope and INSIDE = callee scope

`hello` gets defined as a variable in the lexical/creation scope of the function - that is, wherever this `function hello(){}`  is defined
These are the 2 cases that we'll look at for all the function constructs.



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
BnZm1cbiIsImhpc3RvcnkiOlsyMTE2MTExMDIwLC0xMzI2NjI4
MTE0LDk1OTg0NDI3MCwxNTQwODIyNjUsLTEyMzkxNzMyOTMsMT
Y1NjEyMDU0MCwtMTk1ODA0ODc2OCwtMTA3MTU1MDU5NiwxMDk5
NTY2LC0xMjgxODA1MjAxLC0yMDI4NjcyMTg2LDIwNTYzMTc5MT
MsMjA1NjQ3MjM0NywtMTQ5OTM4NjQwNSwyNDk5MjMyNzIsMzcx
NTMxNTk2LC05MjIxNjY0MiwzNjQxNjM3NzIsNDU2NjA4Mjk4LC
0xMjkxNzcwODgxXX0=
-->