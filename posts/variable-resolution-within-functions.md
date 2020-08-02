

# Closure and this - How are variables resolved within functions?

Let's start with a example of closure -

```js
function outer() {
  // scope A
  var myText = 'Hello'
  return function inner() {
	// scope B
	console.log(myText)
  }
}

{
  // scope C
  let myText = 'World'
  let fn = outer()
  fn();
}
```

Here we have a outer function, which returns a inner function, and this inner function is called later inside another block scope. The scope for each of them is labelled from A to C, and there is also a outermost global scope. 


**What is the output above? How is the variable `myText` resolved within the function?**

In the same note, we'll also look at **how is `this` resolved within the function body?**

## Creating and calling function is different

A function has 2 distinct phases - function creation and function call.   
Function is created when you define it (with hoisting) and that scope where it is created can be called as lexical scope or creation scope. Now, this function can be stored in some variable and called much later - where it is called from can be called as caller scope. These creation and caller scopes might be different.

All these sounds obvious, but it's easy to forget the difference. So, let's actually see the 2 phases for our `inner` function -

```js
function outer() {
  // scope A
  var myText = 'Hello'
  
  // ⭐️ Create phase for `inner`
  // Creation scope = scope A
  return function inner() {
	console.log(myText)
  }
}

{
  // scope C
  let myText = 'World'
  let fn = outer()
  
  // ⭐️ Call phase
  // Caller scope = scope C
  fn();
}
```
Above, function inner will get *created* when outer() is called, and creation scope is `scope A`. But it is *called* later in `scope C`.


## When function is created

When a function `F` is being created, it stores a few internal properties. Two of them are important for this understanding.

* It stores the current scope (creation scope) in `F.[[Environment]]`. This is useful for implementing closure behaviour.

*  `[[ThisMode]]` - For normal functions, it is either `global` (default) or `strict` (in strict mode). For arrow functions, it is `lexical`. For our discussion, what matters is whether it is `lexical` or `non-lexical` (the other two).

In this article, we'll only look at how `this` is resolved - not the actual value of this. For that, please read [my previous post](https://blog.bendtherul.es/what-is-this-inside-foobar-ck8dzlitm01atxjs1322jz9a2).

## When function is called

### Scope creation

When a function is called, it creates a new execution context. Execution context has a property called LexicalEnvironment (LE). This LE points to the current scope which should be used for lookup. We know that a function has its own new scope. So, a new FunctionEnvironment (function scope) is created and set to LE.  
✅ So, till now - when a function is called, it gets a new execution context, whose LE points to a new function scope. This new scope will contain function's own local variables.

✅ Now, scopes are chained - so, this new function scope (LE) needs to decide what is its parent scope (outerEnv). It has 2 options -  
1. caller scope (which was the current scope before this new one was created) or,
2. its lexical scope (which it is carrying around in F.[[Environment]] ).

It decides to **set the lexical scope as parent**, ignoring the caller scope. 

Looking back at our code, that means -
```js
function outer() {
  // scopeA
  var myText = 'Hello'
  // inner.[[Environment]].[[LE]] = scopeA
  return function inner() {
	// new scope = scopeB
	// ⭐️ scopeB -> scopeA -> global
	// ❌ Does NOT have scopeC in chain
	console.log(myText)
  }
}

{
  // scope C
  let myText = 'World'
  let fn = outer()
  fn();
}
```

### Variable lookup

Now, when we use something like `console.log(myText)` in the function body - it needs to resolve the value of the variable `myText`.

For variable lookup, 
a. it will check in the current LexicalEnvironment (i.e. the new local scope) first, and  
b. if it doesn't find the variable there, it will check in it's parent scope (parent of LE = F.[[Environment]] = lexical/closure scope),
c. and so on.

The lookup will finally end when it reaches the global scope (doesn't have parent scope). If the variable is still not found, it will throw a `ReferenceError`.

If you remember the scope chain of `inner` func, this means that the variable will never be looked up in caller scope (scopeC).  
Even if there is no definition for `myText` in the lexical scope (scopeA), it will NOT use the value from scopeC - and indeed throw a error. Example ⤵️

```js
function outer() {
  // scopeA
  return function inner() {
	// scopeB
	// ⭐️ throws ReferenceError
	console.log(myText)
  }
}

{
  // scopeC
  let myText = 'World' // always ignored
  let fn = outer()
  fn();
}
```

## `this` lookup

Earlier, we looked at how normal variables get resolved in a function. But whenever you write `this.someThing`, the value of `this` does NOT get resolved in the same way. It is called as a `ThisExpression`, which gets resolved specially.

Now, remember the new function scope (`FunctionEnvironment`) and `F.[[ThisMode]]`? Well, this FunctionEnvironment also has 2 internal properties - `env.[[ThisBindingStatus]]` and `env.[[ThisValue]]`. Their values are set based on `F.[[ThisMode]]`.  

If `[[ThisMode]]` is lexical, set `[[ThisBindingStatus]]` to 'lexical' and no need of setting `[[ThisValue]]`.
If `[[ThisMode]]` is non-lexical, set `[[ThisBindingStatus]]` to 'initialized' and set `[[ThisValue]]` to the actual `this` that the function was called with.

This FunctionEnvironment has 2 more helper methods -


Now, back to the resolving logic -

### Types of environments


<!--stackedit_data:
eyJoaXN0b3J5IjpbNzE0MzgxMjA0LDE2MjE2MTE2OTQsLTExMj
UyMDI2NiwxOTcwMDg1Njk0LC0zNzk2MTAyODQsLTE5Mjc5ODY3
OTMsMTU4MDk1NzI0Niw3NzA4NDkxOTYsLTkyMjg3MzcwOCwtMj
A5NzM0MjMzNiw0MTI1Njc1NTYsLTM4MDM1Mjk1MywxOTgyODMz
NjcsLTg4NjI4Mjg1NSwxNzkyOTcyNDU0LDE0MzMxNzA4OTQsLT
k4NjUwMzc2OSwtNTU3NTUzNDIwLDE0Nzk4NzIxNTcsODAwNzgz
MjkxXX0=
-->