# How does function.name and name binding work?

# Intro

As a javascript developer, I have used functions for a long time - but I felt uneasy whenever I have to implement a recursive function - how can a function call itself? First, I learned about `arguments.callee` - which seemed like a neat trick, but then I read its [not allowed in strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/callee). So apparently, now I have to know exactly what variable name a function creates and whether that variable is available within the function, to be able to call itself? Nah, I had enough!

And thus began my years of ignorance and denial about function names. I knew how to call a function I created, but none of that recursive mumbo-jumbo. Years passed by. Only recently I thought I would revisit Javascript concepts and try to understand them from the Ecmascript specification. So, this is that article where I document everything I wish I knew about function names.

> Having read some parts of the specification, I strongly believe now that - Some parts of Javascript which feel confusing for developers, are like that, because the specification *literally* has special cases for them. 
> A lot of the explanations we read are too dumbed down and hide what is happening internally. So, as users we only have a fuzzy, hand-waving understanding of things - instead of proper concrete documentation.

# Function.name and name binding

Let's start with two related but different concepts -

1. `function.name` - When we declare a function, it creates a function object - which has a non-writable property called `name`. `fn.name` is a string, which typically stores the initial name that the function was defined with.
	```javascript
	function foo(){}
	foo.name // "foo"
	```
   This is useful for debugging and readable stack traces.  
 
2. **name binding** - Normally when we declare a function, it also creates a variable in the current scope which points to the function object. For ex -
	```js
	// This creates a variable `foo` in the current scope
	function foo(){ }
	// This creates variable `foo`, which points to the function object
	// So, we can use it to call the function
	foo()
	```
	This can be called as name binding.
	
## ⚠️  **How are they different?** 
It's easy to get confused here. Right now, it looks like `fn.name` just stores the name of the variable which name binding created. (That is, `foo.name` is just the variable name `"foo"` as string). So, are they really different?

Yes, they are. Let's look at more examples -

1. You can always store the function in a variable with different name, but `func.name` will NOT change. 
	```js
	// Example 1
	function func1() {}

	var func2 = func1;
	func2.name // prints "func1", NOT "func2"
	```
	`fn.name` is only decided based on how the function is created and DOES NOT depend on the variable name you use to access it.
	
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
	foo // DOESN'T exist
	```

⭐️ So, even though `function.name` and name binding is related, they are not always the same thing. In other words, creating a named function doesn't necessarily create a variable with the same name (in the current scope).

(From now on, I'll use the terms `.name` and bound name to distinguish between these two.)

Now, let's actually look at how both the names behave for different function syntaxes.


# Function statement

This is the simplest form of function -
```js
function hello() {}
// In generic form,
// function identifier(paramList){ body } 
```

## .name
 For function statements, **`.name` is simply string form of the identifier `hello` - that is "hello"**.  
And it's **always** going to be a string, because you can't use a expression or symbol in the name part (identifier) of function statements. Ex - `function Symbol("abc"){}` - this is NOT valid.

## bound name
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
1. When we declare `function hello`, js internally defines a variable in the OUTSIDE scope with the same name `"hello"`  - and sets its value to the function object.  So, calling `hello()` works here.
    
    > Actually, this name binding happens alongwith hoisting. Hoisting works using a sort of "pre-evaluation" step, where - 
    > 1.  it only looks at function declarations,
    > 2. evaluates the declaration to create a function object, and
    > 3. adds a binding in lexical scope with name="foo" and value=\<function object\>.
    >
    > The actual "evaluation" of all statements happen after this "pre-evaluation" step.  
    > When js engine again reaches the function statement in evaluation step - it simply skips over it, to stop it from getting evaluated twice.


2. What about INSIDE scope? From the previous snippet, it seems like we are indeed able to call it using `hello()` within the function. But how is it working?
    
    There are 2 possible ways this can work -
    
    A. **Closure access** - There is no `hello` defined within the function (INSIDE), but its closure scope (OUTSIDE ) has the variable `hello`. So, when we try to access `hello` within the function, it just returns the value from outer scope.  
    To visualize it -
    ```js
   	// OUTSIDE scope
   	// VARS =["hello"] ✅<--|
						    |
    function hello() {	    |
      // INSIDE scope	    |
      // VARS = []    ❌<---|
					        |  
      // Lookup "hello"     |
      hello() >-------------|
      
    }
	
    ```
    That means- if you change the value of the variable `hello` in outer scope, it will also change within inner scope. Example -
    ```js
    function hello() {
      hello() // will this work?
    }
    hello = 123
    ```
    B. **Redefined during call** - `hello` is redefined within INSIDE scope, every time we call the function. So, INSIDE will have its own variable/binding called "hello" which points to the function.  
    If this is the case, then it won't get affected by what happens to `hello` in the OUTSIDE scope.
 
⭐️ For function declarations, name binding in inner scope works using the first method (**Closure access**). So, modifying `hello` in outer scope will affect its value in the inner scope. 
```js
function hello() {
  hello() // ❌ TypeError: hello is not a function
  // Here, hello = 123. It got modified.
}

var newHello = hello
hello = 123
newHello()
```


# Func expression and Arrow function

Function expressions and arrow functions can look like this -
```js
// FE = function expression

// A1. Anonymous FE
console.log( function(){} )
// A2. Named FE
console.log( function hello(){} )

// A3. Assignment + Anonymous FE
hello = function(){}
// A4. Assignment + Named FE
newHello = function hello(){}

//--------

// B1. Arrow function
console.log( () => {} )
// B2. Assignment + Arrow function
hello = () => {}
```

So, function expressions can be either *named or anonymous*, whereas arrow functions are *always anonymous*. Also, both of them can be assigned to a variable during creation - whether using `var`/`let`/`const` or simple assignment like `hello = () => {}`.

## .name

For **named** function expression, `fn.name` is simple - it's just string form of the identifiier.  
(applies to A2 and A4 above)   

```js
(function hello(){}).name // "hello`
(newHello = function hello(){}).name // "hello"
```

For **anonymous** function expression and arrow function, `fn.name` is just the **empty string  `""`**. This is expected, because JS has no way of inferring its name or coming up with a reasonable name.  
(applies to A1 and B1 above)  

```js
(function() {}).name // ""
(() =>{}).name // ""
```
So as of now, it seems like arrow functions can *never* have a proper `.name`? Arrow function does not have a named version, it is always anonymous. Is there some way to **name** a *anonymous* function?

For **assignment + anonymous** functions (FE and AF), JS tries to infer the function name from the variable name on left side.  

```js
hello = function() {}
hello.name // "hello"

var hello = () => {} ✅
hello.name // "hello"

var hello2 = hello; ❌
hello2.name // "hello"
```
This is a interesting case, because here we are not directly providing a name for the function. To javascript engine, if it **looks like a *assignment* and RHS is a anonymous function syntax**, then it does **NamedEvaluation** of the function (with name = \<string form of LHS\>). NamedEvaluation is just like normal evaluation, but it also sets `fn.name` = input string.  So, effectively `(function hello(){}).name` and `(hello = function(){}).name` acts similarly. 

Because there is **no named arrow function syntax**, this is very useful to create a arrow function which has a name. That means, to create a named arrow function, you CAN'T do this - `hello() => {}`, only way is to define arrow function with some sort of assignment - `hello = () => {}`.

Also, if you are thinking that "*ehh, I will just set fn.name to whatever i want* after creating the function", that is not possible. `.name` property descriptor has `writeable: false`. So, if you try to set `.name` to some value, it will simply NOT affect the `name` (non-strict) or throw `TypeError` (in strict mode). That is why you need to be aware of how each syntax decides the name.

# Method and object literal

Todos -
1. if name is symbol? (for expressions)
2. if anonymous, name=?
3. prefix in name (bound)
4. default export
5. name available inside/outside
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOlsxNTUzMjMxNDA5LDk3NDc5OTkx
MywxNjQ3MDgzNzI5LC0yMDg1ODgxNDczLC03NzM2NjA2ODgsMT
E0NTg0MzMwMSwtMTc2MjkzMjk0MCwtNTg0Mzc1Nzg5LDE0MDQ3
MTgyOTUsMzgxMDg0NDM0LDE2MTU2MDMyOTgsMTI1MzU4NTQxNC
w1NTk0MzQ0MzgsLTU5MjA3NDMzNCwxNDczMDQxODgwLDE0MTE0
MzA4NTMsLTEzNzcyMTI4MiwtMjAwODc3ODAxMCwxODc2MDMxMD
UyLDE3MTA1ODAyNDddfQ==
-->