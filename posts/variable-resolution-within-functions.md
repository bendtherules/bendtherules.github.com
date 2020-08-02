# Closure and this - How are variables resolved within functions?

Let's start with a example of closure -

```js
function outer() {
  // scope A
  var text = 'Hello'
  return function inner() {
	// scope B
	console.log(text)  
  }
}

{
  // scope C
  var text = 'World'
  var fn = outer()
  fn();
}
```

Here we have a outer function which returns a inner function, and this inner function is called later (inside another block scope). The scope for each of them is labelled from A to C, and there is also a outermost global scope.

**What will this print? How is the variable `text` resolved within the function?**

## Function creation

A function has 2 distinct phases - function creation and function call.   
Function is created when you define it (with hoisting) and that scope where it is created can be called as lexical scope or creation scope. Now, this function can be stored in some variable and called much later - where it is called from can be called as caller scope. These creation and caller scopes might be different.

All these sounds obvious, but it's easy to forget the difference. So, let's actually see the 2 phases for our `inner` function -

```js
function outer() {
  // scope A
  var text = 'Hello'
  
  // ⭐️ Create phase for `inner`
  // Creation scope = scope A
  return function inner() {
  }
}

{
  // scope C
  var text = 'World'
  var fn = outer()
  
  // ⭐️ Call phase
  // Caller scope = scope C
  fn();
}
```

When a function `F` is being created, it stores the current scope (creation scope) in `F.[[Environment]]` internal property.

## Function call and scope creation

### Variable lookup

## `this` lookup

### Types of environments

When a function is called, it creates a new execution context (say EC). Now EC has a property called LexicalEnvironment (LE) - which is (roughly) the current scope for lookup. Because function should have its own scope, so a new FunctionEnvironment (function scope) is created and set to LE. So, till now - when a function is called, it gets a new execution context, whose LE points to a new scope. This new scope will contain function's own local variables. Now scopes are chained - so, this new function scope (LE) needs to decide what is its parent scope (outerEnv). It has 2 options - caller scope (which was the current scope before this new one was created) or its lexical scope (which it is carrying around in F.[[Environment]] - a internal property). It decides to set this lexical scope as parent, ignoring the caller scope. So, now when a variable lookup happens inside function, it checks current LexicalEnvironment first (which is the new local scope) and if it doesn't find that, it looks up to parent of LE aka F.[[Environment]] aka its closure scope.

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI0MDc4NDUzNywtMTY2MjMxNjk1NiwtOD
k5NjM4MTcxLDIwNzEwNjg2OTUsMTcwMzE1OTc1MiwtMjA3Njkx
MTUwNiwxMjM2NDEyMDU0LC0yMTAyMzk2NzM2LDIwNDc0OTI1OD
BdfQ==
-->