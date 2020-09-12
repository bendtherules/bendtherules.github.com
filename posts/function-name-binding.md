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

To be more precise, OUTSIDE/outer = lexical scope and INSIDE/inner = callee scope.  
(I think the term lexical scope is widely misused and used as a magical phrase to explain anything and everything. So, hopefully a more common term like inner/outer scope will be easier to understand.)

Now, to answer the above two questions -  
1. When we declare `function foo`, js internally defines a variable in the OUTSIDE scope with the same name `"foo"`  - and sets its value to the function object.  So, calling `hello()` works there.
    
    > Actually, this name binding happens alongwith hoisting. Hoisting works using a sort of "pre-evaluation" step where- 
    > 1.  it only looks at function declarations,
    > 2. evaluates the declaration to create a function object, and
    > 3. adds a binding in lexical scope with name="foo" and value=\<function object\>.
    >
    > The actual "evaluation" of all statements happen after this "pre-evaluation" step.  
    > When js engine again reaches the function statement in evaluation step - it simply skips over it, to stop it from getting evaluated twice.


2. What about INSIDE scope? From the previous snippet, it seems like we are indeed able to call it using `hello()` within the function. But how is it working?
    
    There are 2 possible ways this can work -
    
    A. **Closure access** - There is no `hello` defined within the function (INSIDE), but its closure scope (OUTSIDE ) has the variable `hello`. So, when we try to access `hello` within the function, it just returns the value from outer scope.  
    That means- if you change the value of the variable `hello` in outer scope, it will also change within inner scope. Example -
    ```js
    function hello() {
      hello() // will this work?
    }
    hello = 123
    ```
    B. **Redefined during call** - `hello` is redefined within INSIDE scope, whenever we call the function. If this is the case, then it won't get affected by what happens to `hello` in the OUTSIDE scope.
 
In this case, name binding in inner scope works using the first method (**Closure access**). Yes, this also means that modifying `hello` in outer scope will affect its value in the inner scope. 
```js
function hello() {
  hello() // ❌ TypeError: hello is not a function
  // Here, hello = 123. It got modified.
}
hello = 123
```

Add snippet here

# Func expression and Arrow function

# Method and object literal

Todos -
1. if name is symbol? (for expressions)
2. if anonymous, name=?
3. prefix in name (bound)
4. default export
5. name available inside/outside
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOlszMTgwOTI1MzEsMTQ5NDQ4MzAx
Miw5NTM4ODQ2Nyw4NzU1ODEyNTQsLTIwOTQ4MDgyMTQsLTM5OT
Q2ODEzNiwxNzIwMjQ2NzIxLDIxMjkzODU0ODgsODMzNzg5NTIz
LDExMDgzNzg5ODUsLTEzMjY2MjgxMTQsOTU5ODQ0MjcwLDE1ND
A4MjI2NSwtMTIzOTE3MzI5MywxNjU2MTIwNTQwLC0xOTU4MDQ4
NzY4LC0xMDcxNTUwNTk2LDEwOTk1NjYsLTEyODE4MDUyMDEsLT
IwMjg2NzIxODZdfQ==
-->