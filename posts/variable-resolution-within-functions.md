# Closure and this - How are variables resolved within functions?

Let's start with a example of closure -

```js
function outer() {
  // scope A
  var name = 'Abhas'
  return function inner() {
	// scope B
	console.log(name)  
  }
}

{
  // scope C
  var fn = outer()
  fn();
}
```

Here we have a outer function which returns a inner function, and this inner function is called later inside another block scope

When a function is called, it creates a new execution context (say EC). Now EC has a property called LexicalEnvironment (LE) - which is (roughly) the current scope for lookup. Because function should have its own scope, so a new FunctionEnvironment (function scope) is created and set to LE. So, till now - when a function is called, it gets a new execution context, whose LE points to a new scope. This new scope will contain function's own local variables. Now scopes are chained - so, this new function scope (LE) needs to decide what is its parent scope (outerEnv). It has 2 options - caller scope (which was the current scope before this new one was created) or its lexical scope (which it is carrying around in F.[[Environment]] - a internal property). It decides to set this lexical scope as parent, ignoring the caller scope. So, now when a variable lookup happens inside function, it checks current LexicalEnvironment first (which is the new local scope) and if it doesn't find that, it looks up to parent of LE aka F.[[Environment]] aka its closure scope.

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTUwMDc1Mzk3NSwtMjA3NjkxMTUwNiwxMj
M2NDEyMDU0LC0yMTAyMzk2NzM2LDIwNDc0OTI1ODBdfQ==
-->