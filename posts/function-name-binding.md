# How does function.name and name binding work?


# Intro

Whenever you are writing a recursive function, there is a small question you have to answer mentally - **how can this function call itself**?  

To start off, there is `arguments.callee` - which seems like a decent option, but it is considered a legacy artifact - which is [not allowed in strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/callee) and doesn't work within arrow functions. So, when you declare a function - now you have to know exactly what variable javascript creates for this function and in which scope - so that you can call it correctly.

When you write a function, you are (often) not declaring a var/let/const yourself. So, how exactly does the engine create this variable, which points back to the function? That's all this article is about.

# Function.name and name binding

Let's start with two related but different concepts -

1. `function.name` - When we declare a function, it creates a function object - which has a non-writable property called `name`. `fn.name` is a string, which typically stores the initial name that the function was defined with.
  
    ```javascript
    function foo(){}
    foo.name // "foo"
    ```
  
    This is useful for debugging and readable stack traces.  
 
2. **name binding** - Normally when we declare a function, it also creates a variable in the current scope which points to the function object. For ex -
    ```javascript
    // Creates variable `foo` here
    function foo(){ }
    // So, we can call it
    foo()
    ```
  
    This can be called as name binding.
  
## ⚠️ **How are they different?** 
It's easy to get confused here. Right now, it looks like `fn.name` just stores the name of the variable which name binding created. (That is, `foo.name` is just the variable name `"foo"` as string). But, are they really different?

Yes. Let's look at more examples -

1. You can always store the function in a variable with different name, but `func.name` will NOT change. 
 
    ```js
    function func1() {}

    var func2 = func1;
    func2.name // "func1", NOT "func2"
    ```
  
    `fn.name` is only decided based on how the function is created and DOES NOT depend on the variable name you use to access it.
  
2. *Named function expression* can be bound to a variable name which is different from it's `function.name`.  
    Also, function expressions can be used without storing in any variable.
  
    ```js
    // A. Diff variable name
    var someVar = function someName(){}
    // Creates variable `someVar`
    // But function.name is `someName`
  
    // B. not stored in any variable, ex - IIFE
    (function foo(){})()
    ```

⭐️ So, even though `function.name` and name binding is related, they are not always the same thing. In other words, creating a named function doesn't necessarily create a variable with the same name.

(From now on, I'll use the terms `.name` and bound name to distinguish between these two.)

Now, let's actually look at how both the names behave for different function syntaxes.


# Function statement

This is the simplest form of function -
```js
function hello() {}
// function identifier(paramList) {body} 
```

## .name
 For function statements, **`.name` is simply string form of the identifier `hello`** - that is "hello".  

> Because you can't use a symbol in the name part of function statements, the name part is always a identifier/variable name - which can be easily converted to a string. Ex - `function Symbol("abc"){}` - this is NOT valid. If it was valid, then we would need some way to convert a symbol to a string. (Hint: This will happen for object literals.)

## bound name
```js
function hello() {
  // available INSIDE
  hello() ✅
}

// also OUTSIDE
hello() ✅
```

We want to look at two things -
1. Can I call the function using its name in the OUTSIDE scope?  
2. Can the function call itself using its own name? We are calling this as the INSIDE scope. This is useful for recursion.

To be more precise, OUTSIDE/outer = lexical scope and INSIDE/inner = callee scope.  

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
     // has ["hello"] ✅<---
                           |
    function hello() {     |
      // INSIDE scope      |
      // has  []     ❌<----
                           |
      // Lookup "hello"    |
      hello() >------------|
      
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

For **named** function expression, `fn.name` is simple - it's just string form of the identifier.  
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
So as of now, it seems like arrow functions can *never* have a proper `.name`? Arrow function does not have a named syntax, the syntax is anonymous (always written without a name). Is there still some way to **name** a *anonymous* function? 👇

For **assignment + anonymous** functions (FE and AF), JS tries to infer the function name from the variable name on left side.  

```js
hello = function() {}
hello.name // "hello"

var hello = () => {} ✅
hello.name // "hello"

var hello2 = hello; ❌
hello2.name // "hello"
```
This is a interesting case, because here we are not directly providing a name for the function. To javascript engine, if it **looks like a *assignment* and RHS is a anonymous function syntax**, then it does **NamedEvaluation** of the function with name = \<string form of LHS\>. NamedEvaluation is just like normal evaluation, but it also sets `fn.name` = input string.  So, effectively `(function hello(){}).name` and `(hello = function(){}).name` acts similarly. 

Because there is **no named arrow function syntax**, this is very useful to create a arrow function which has a name. So, to create a named arrow function, you CAN'T do this - `hello() => {}`, only way is to define arrow function with some sort of assignment like this - `hello = () => {}`.

Also, if you are thinking that "*ehh, I will just set fn.name to whatever i want* after creating the function", that is not possible. `.name` property descriptor is `writeable: false`. So, if you try to set `.name` to some value, it will simply NOT affect the `name` (in non-strict) or throw `TypeError` (in strict mode). That is why you need to be aware of how each syntax decides the function name.

## bound name

1. Can I call the function using its name in the OUTSIDE scope?

  Because these are all function expressions, they DON'T automatically create any name binding in the outer scope. It really depends on its surrounding statement/expression and what that does.  
  For ex, if the function is situated on RHS of assignment expression (`hello = function(){}`), the assignment expression will create a binding for `hello`, while the function itself won't do anything special.
  In the opposite case, like IIFE - where a function is created and consumed immediately, it will not create any name binding. Ex - `(function(){})()` doesn't create any binding.

2. Can the function call itself in the INSIDE scope?
  
  **Redefined during call** - 
  For `named` function expression, wenever the named function expression is called, js engine creates a **special scope between its outer lexical scope and function's local scope** - which contains binding for \<function name\>. 
    We can ignore the difference between special scope and local scope for practical purposes. So, in simple words - whenever this function is called, js creates a new binding for the function name in its local scope. This will not be affected by any change in the OUTSIDE scope.

  Example -
  ```js
  someName = function hello() {
    hello() // ✅ works
  };

  var newName = someName
  someName = 123
  newName()
  ```

  To visualize,
  ```js
  // OUTSIDE scope
  // has ["someName"]

  someName = function hello() {
    // SPECIAL scope {
    // has ["hello"] ✅<------
      // INSIDE scope {      |
      // has []     ❌<-------
                             |
      // Lookup "hello"      |
      hello()    >-----------|
      // }
    // }
  }

  ```

  For all function expressions (including named ones), **closure access** also applies. This is simple to understand, so here are a few examples -

  ```js
  someName = function hello() {
    hello()
    someName() // ✅
  }
  ------
  someName = () => {
    someName() // ✅
  }
  ------
  someName = function() {
    newName() // ✅
  }
  var newName = someName
  ```

# Method and object literal

Object literals have a few diff variety, so look at the examples below -
```js
obj = {
  // A1. property assignment + anonymous
  fn1: function(){},
  // A2. property assignment + named
  fn2: function hello(){},
  
  // A3. key can be symbol
  [Symbol("fn3")]: function(){},
  
  // A4. method
  fn4(){ }
}
```

Case A1 and A2 - `fn: function(){}` works similar to what we have read till now. Think of `fn1: function(){}` as a assignment, similar to `fn1 = function(){}`.  
That means, anonymous functions will be subjected to NamedEvaluation and get a automatic name (`obj.fn1.name = "fn1"`).  
They also have normal closure access, which means they have to start from the `obj` variable to reach themselves.

```js
obj = {
  fn1: function(){
    fn1() // ❌
    obj.fn1() // ✅
  },
  fn2: function hello(){
    fn2() // ❌
    
    hello() // ✅
  },
}

obj.fn1.name // "fn1"
obj.fn2.name // "hello"
```

Case A3 - `[Symbol("fn2")]: function(){}` is also quite similar, but only difference is that the key itself is a `Symbol`, instead of a normal variable name.  
So, **what happens in NamedEvaluation when the name is a symbol**? Well, it gets converted to string by concatenating "[" + description of symbol + "]". So, `Symbol("abc")` becomes `"[abc]"`.

```js
sym = Symbol("fn3")
obj = {
  [sym]: function(){},
}

obj[sym].name // "[fn3]"
```

Case A4  - Method syntax `{ fn4(){}, }` is also quite interesting, because it looks somewhere between a named function expression and a function declaration. We can safely conclude that `obj.fn4.name = "fn4`.  
But, what about name binding? Well, turns out it does NOT get any special internal name binding ("Redefined during call"), only closure access. So, nothing special there.

```js
obj = {
  fn4(){
    fn4() // ❌
    obj.fn4() // ✅
  }
}

obj.fn4.name // "fn4"
```

# Misc

There are a few other special cases for `.name`, included for completion -

```js
function hello(){}

// 1. Bound function
tmp = hello.bind()
tmp.name // "bound hello" (= "bound "+ hello.name)

// 2.Default export

// In file A
export default function(){} 

// In file B
import tmp from "./fileA";
tmp.name // "default"

// 3. getter, setter
obj = {
  get foo() {},
  set foo() {}
}

desc = Object.getOwnPropertyDescriptor(obj, "foo")

desc.get.name // "get foo"
desc.set.name // "set foo"

```


That's all. Hopefully, now you know more about function names than you'll possibly ever need. The 2 ways of inner name binding are useful to know. In general, consider this as a reference that you can look back to or just a interesting long read.

Thanks.

<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOlsyMDg3NDM1NDQzLDEwMjYyOTc3
NTEsNDk2MjYzNzg2LC0xMDgyMDk4MDg0LC0xMjM0NjU0NjMwLC
0xMjczOTk1MzkwLC0zMTY2Nzg2NCwtMzYxMDY3NTksMzU1MDIw
ODEyLC0yMDYyMDMzNTg1LC02OTg4NDY4OTUsLTE5OTk2NDMxOT
YsMjAzMDQxMDQ1MiwxMTQ3MTE5MzYzLDQ3NTM4MTc1NiwtMTg0
MTUyNjc3OCwxOTUyMDYwNDU0LC04NjMwNTc2NywtNzYyMDk5Mj
M4LDI5MDMyNjEzNF19
-->