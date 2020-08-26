# How does function.name and name binding work?

This is going to be one of those nitpicky articles where I document everything about function name, that I have learned from the spec.  

# Intro

Let's start with two related but different concepts -
1. `function.name` - When we declare a function, it creates a function object - which has a non-writable property called `name`. `fn.name` typically stores the initial name that the function was defined with.
	```js
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


## Function statement

```js
function hello() {}
// In generic form,
// function identifier(paramList) {body} 
```

For function statements, `.name` is simply string form of the identifier `hello` - that is "hello".  
And it's always going to be a string, because you can't use a expression or symbol in the identifier/name part of function statements. (Ex - `function Symbol("abc"){}` - is not valid) 

> Sidenote - 
> If you are looking at the spec and find [evaluation step for function declarations](https://tc39.es/ecma262/#sec-function-definitions-runtime-semantics-evaluation), you might be a little disappointed. It just says 'Return NormalCompletion(empty)' - which basically means, when you are evaluating statements line-by-line and reach a func declaration, DON'T do anything for that line. Just move on to the next line.
> That's odd, right? For function *expression* evaluation, it tells you exactly how a function is created, but not for func *declaration*. Then, when/how will a function declaration get evaluated?
>  
> The short answer is, **hoisting**. There is a (sort of) *"pre-evaluation"* step and then actual *"evaluation"* step for all statements. During pre-evaluation, 
> 
> 


## Func expression and Arrow function


## Method and object literal

Todos -
1. if name is symbol? (for expressions)
2. if anonymous, name=?
3. prefix in name (bound)
4. default export
5. name available inside/outside?
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOlsyMDU2MzE3OTEzLDIwNTY0NzIz
NDcsLTE0OTkzODY0MDUsMjQ5OTIzMjcyLDM3MTUzMTU5NiwtOT
IyMTY2NDIsMzY0MTYzNzcyLDQ1NjYwODI5OCwtMTI5MTc3MDg4
MSwxMjc1NTA3NTM4LC0xMTY4NjQyOTksMTczNDA5NDQ2OCwtMj
AwODA2MTYzLDExOTE3ODE4NCwtMTUyMTUwMjM0MiwtMTcyNzM1
ODEzNywxODI2MjgyNTAzLC0xNDM4NzY2OTMwLDE4NjQyNDQ3NT
MsOTU5Nzk1MzUyXX0=
-->