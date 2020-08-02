
# Closure and this - How are variables resolved within functions?

Let's start with a example of closure -

```js
function outer() {
  // scope A
  var name = 'Hello'
  return function inner() {
	// scope B
	console.log(name)
  }
}

{
  // scope C
  let name = 'World'
  let fn = outer()
  fn();
}
```

Here we have a outer function, which returns a inner function, and this inner function is called later inside another block scope. The scope for each of them is labelled from A to C, and there is also a outermost global scope. 


**What is the output above? How is the variable `name` resolved within the function?**

In the same note, we'll also look at **how is `this` resolved within the function body?**

## Creating and calling function is different

A function has 2 distinct phases - function creation and function call.   
Function is created when you define it (with hoisting) and that scope where it is created can be called as lexical scope or creation scope. Now, this function can be stored in some variable and called much later - where it is called from can be called as caller scope. These creation and caller scopes might be different.

All these sounds obvious, but it's easy to forget the difference. So, let's actually see the 2 phases for our `inner` function -

```js
function outer() {
  // scope A
  var name = 'Hello'
  
  // ⭐️ Create phase for `inner`
  // Creation scope = scope A
  return function inner() {
	console.log(name)
  }
}

{
  // scope C
  let name = 'World'
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
  var name = 'Hello'
  // inner.[[Environment]].[[LE]] = scopeA
  return function inner() {
	// new scope = scopeB
	// ⭐️ scopeB -> scopeA -> global
	// ❌ Does NOT have scopeC in chain
	console.log(name)
  }
}

{
  // scope C
  let name = 'World'
  let fn = outer()
  fn();
}
```

### Variable lookup

Now, when we use something like `console.log(name)` in the function body - it needs to resolve the value of the variable `name`. For variable lookup, 
a. it will check in the current LexicalEnvironment (i.e. the new local scope) first, and  
b. if it doesn't find the variable there, it will check in it's parent scope (parent of LE = F.[[Environment]] = lexical/closure scope),
c. and so on.

## `this` lookup

### Types of environments


<!--stackedit_data:
eyJoaXN0b3J5IjpbMTU4MDk1NzI0Niw3NzA4NDkxOTYsLTkyMj
g3MzcwOCwtMjA5NzM0MjMzNiw0MTI1Njc1NTYsLTM4MDM1Mjk1
MywxOTgyODMzNjcsLTg4NjI4Mjg1NSwxNzkyOTcyNDU0LDE0Mz
MxNzA4OTQsLTk4NjUwMzc2OSwtNTU3NTUzNDIwLDE0Nzk4NzIx
NTcsODAwNzgzMjkxLDE3NTE2NDYzNTYsLTE3ODY0ODc0MjAsNT
c5ODQxMzUyLC0xOTc1MDcyNjk2LC0xNjYyMzE2OTU2LC04OTk2
MzgxNzFdfQ==
-->