## 0. The one about for loops

<sub> (You are viewing a email which was sent out earlier. So, some details might not match) </sub>  

🌤 Hello and good afternoon. You will probably receive this email around 5:45 pm, while wrapping up today's work 😀.

![coffee time gif](https://media.giphy.com/media/6NlmBQLhWy2QM/giphy.gif) 

Let's start with a small update - our mini mailing list is now 15 people strong (including my test email 🤖). Yay!

<!-- TOC -->

Here is a small TOC for the rest of the email -  
1. [the problem](#the-problem)  
2. [syntax of for loop](#syntax)  
3. [for + var is simple](#how-var)  
4. [for + let is... little complicated](#how-let)  
5. [Meh! Why should I care?](#how-let-implications)  
6. [further discussions](#related-topics)

<!-- The problem -->

# for loop + let
<a id="the-problem" name="the-problem"> </a>
This is the well known **for loop with setTimout** problem. We try to print `i` (the counter) inside setTimeout.

The code looks like this -

```js
// for + let - ✅
for (let i = 0; i < 10; i++) {
  setTimeout(() => console.log(i));
}

// for + var - ❌ always prints 10
for (var i = 0; i < 10; i++) {
  setTimeout(() => console.log(i));
}
```
 
If `i` is declared using **let**, it works correctly. But if you use **var**, it always prints 10.

**So, why is that?**  

a. let has block scoping.  
But that shouldn't matter. There is no outer block scope here.  

b. We have declared let only once, just like var.  
But we can see that each setTimeout callback is creating its own closure with the correct value of `i`. Does that mean `i` is somehow getting declared 10 times instead of just one?

Does for loop treat **let** differently from **var**? 🤔

<!-- Syntax + formal definition -->

## Let's start with the basics

<a id="syntax" name="syntax"> </a>
Think about the the syntax of a for loop.  

It has four parts - initializer, test condition (or checker), incrementer and the body. The first 3 parts are optional, but the body is required.  
(We'll refer to the first 3 as top part.)

![for loop syntax - initilizer, checker, incrementer](https://buttondown.s3.us-west-2.amazonaws.com/images/80d06446-68f1-4206-ba8b-101a7e4c18e8.jpeg)

<a id="formal-definition" name="formal-definition"> </a>
⭐️ More formally, it has to match one of these grammars -

1. for (Expression; Expression; Expression) statement
2. for (var DeclarationList; Expression; Expression) statement
3. for (let/const DeclarationList; Expression; Expression) statement

<!-- Term definitions -->

Take a moment to understand these terms. <a href="#after-term-definition">Skip term definitions ></a>

**Expression** is anything that can be used as part of a statement. Something as simple as `i < 10` or more complex like `fetchData(i/2)`  

**Statement** usually means a single logical sentence, like `var foo = 123`. But more importantly, it can also be a block statement which contains other statements.  Ex -
```js
{
  var foo;  // line 1
  foo = 123;  // line 2
}
```
This is exactly why **we can write multiple sentences** inside the body of a for loop, instead of putting everything in one line.  
Another type of statement is **EmptyStatement**. It is literally just a semicolon `;`.  

These are all valid for loops -
```js
// Empty statement
for (i=0; i<10; i++);

// Single statement
for (i=0; i<10; i++) console.log(i);

// Block statement
for (i=0; i<10; i++) {
  console.log(i);
}
```

**DeclarationList** is intuitive - it is the list of variables that  are getting declared alongwith their initial values. Just remember that you can declare multiple variables at once.
Ex - `var a = 1, b, c;`

<!-- What happens for var ?? -->

<a href="#formal-definition" id="after-term-definition" name="after-term-definition">Remember the formal definitions?</a>
The initializer can be one of 3 types. It can be a -  
a. expression,  
b. `var` declaration,  
c. `let` or `const` declaration (collectively called Lexical declaration)

The type of the initializer determines the rest of the flow.

<a id="how-var" name="how-var"> </a>

## If initializer is a expression or var declaration -

Then follow the simpler flow. It works just as you would expect, no surprises here.  
No extra scope is created internally.

<sub>(outer scope = scope surrounding for)</sub>

### In details -

1. Evaluate **initializer**, which is a expression or `var` declaration. <sub>(scope = outer scope)</sub>
2. For each iteration -

    a. If **test condition** is present -

     1. Evaluate **test condition** <sub>(scope = outer scope)</sub>  
     2. Convert its result [to boolean](https://tc39.es/ecma262/#sec-toboolean).  
     3. If result is `false`, stop iteration.

    b. Evaluate **body** statement. <sub>(scope = outer scope)</sub>

    c. Evaluate **incrementer** expression, if present. <sub>(scope = outer scope)</sub>

(Note - In any Evaluate step, the scope where it is evaluated (current scope) is very important. For ex., here the initializer runs in the outer scope - so `i` is available after for loop is over.)

One interesting thing here is that the **test condition runs at start of every iteration**, not in the end. So, the first iteration has to also pass the test condition. Ex -

```js
// This is will never run,
// not even the first time
 for (var i = 0; i < 0; i++)  {
    console.log(i)
}
```

<br/>

<!-- What happens for let ?? -->
<a id="how-let" name="how-let"> </a>

## If initializer is a Lexical declaration (let or const) -

Then follow the complex flow. This flow creates **(n + 1)** total block scopes - 1 for the whole for loop (say, **overall scope**) and one for each of the succesful iterations.  
**Overall scope** acts as the parent scope and every iteration creates a sibling scope within it. Parent of overall scope is the **outer scope**.

In gist, the algorithm is -  

1. Create overall scope  
2. Evaluate initializer in overall scope. (let i = 0)  
3. For each scope,  
    a. Create a new block scope  
    b. Declare variable `i` in this scope  
    c. Copy the value of `i` from previous scope  
      (For the first iteration, previous scope is parent overall scope. For others, its their previous sibling)

    d. Evaluate incrementer i++. Skip this step for first iteration. 

    e. Evaluate test condition, i < 10. If false, stop iteration.

    f. Evaluate body (setTimeout...)


[Skip overly detailed version >](#how-let-implications)

Definitions -  
A. **boundnames** of a declaration is simply the variable names that are being declared. Ex., boundnames of `let i=1, j=2` is (i, j).
  
B. **perIterationLets** - If intializer uses `let`, it is same as **boundnames** of the let declaration. If initializer uses `const`, it is empty.  

So, perIterationLets of `let i, j` is (i, j), but perIterationLets of `const i, j` is empty.


### In details -

1. Create a new block scope and set it as current scope. This is the **overall scope**.

2. Evaluate **initializer**, which is a `let`/`const` declaration (`let i = 0`). <sub>(scope = overall scope)</sub>
3. For the first iteration -  
    a. Create a new block scope (say, scope0) with parent=**overall scope**  
    b. For each variable name in **perIterationLets** -  
      1. declare the same variable in scope0 (using `let`), and  
      2. initialize it with its existing value in current scope (overall scope).  

    c. Set scope0 as current scope

4. For every iteration (say, with index i) -  

    a. If **test condition** is present -

     1. Evaluate **test condition** <sub>(scope = scope&lt;i&gt;)</sub>  
     2. Convert its result [to boolean](https://tc39.es/ecma262/#sec-toboolean).  
     3. If result is `false`, stop iteration.

    b. Evaluate **body** statement. <sub>(scope = scope&lt;i&gt;)</sub>  
    c. [Similar to step 3] Create next scope, i.e.-

      1. Create a new block scope (say, scope&lt;i+1&gt;) with parent=**overall scope**  
      2. For each variable name in **perIterationLets** -  
        1. declare the same variable in scope&lt;i+1&gt; (using `let`), and  
        2. initialize it with its existing value in current scope (scope&lt;i&gt;).  

      3. Set scope&lt;i+1&gt; as current scope  

    d. Evaluate **incrementer** expression, if present. <sub>(scope = scope&lt;i+1&gt;)</sub>


<!-- Insights from let algo ?? -->
<a id="how-let-implications" name="how-let-implications"> </a>
### Why are these details important?

A. **initializer** runs in the overall block scope. None of the iterations modify this overall scope.  
  
  - So, variables created here are not available outside the for loop.
  - If the initializer tries to access its own value after some time, it will still get the initial values. Even the body of the for loop cannot affect this scope.  
    Example -

```js
for (
  // This will print 0, NOT 10 or 1
  let i = (setTimeout(() => console.log(i)), 0);
  i < 10;
  i++
) {
  i++;
}
```

B. **incrementer** expression runs in the next scope (scope for next iteration).  

  - So, setTimeout for i-th iteration prints i and not i+1. Let me illustrate -

```js
    // 4th iteration
    {
        // i is still 3, from last iteration
        i++; // i becomes 4

        // now run body
        {
          // sync - i is 4
          log(i); 
          // async - i is 4
          setTimeout(() => log(i));
        }

        // i is never modified after body is run

        // IF i++ was moved here,
        // sync will print 4, but async will print 5
    }
```
  - **incrementer** doesn't have to be a simple expression which just increments `i`. It can be a complex expression with other side-effects.  
    The idea is that - **incrementer** should not be able to able to modify the scope of the body after the body is already evaluated. So, it is evaluated on the new scope with values copied from the last iteration.

C. **for** only creates block scopes internally. 

  - So, if you declare some variables inside **for body** using `var`, they will be available outside for loop.  
    Example -

```js
for (let i=0; i<1; i++) {
    var hello = "world";
}

// works
console.log(hello);
```

  - Some basic polyfill for `for+let` uses function scope. In that case, the above example will not work.  
    Example -

```js
for (var i = 0; i < 10; ++i) {
  (function (i) {

    // body goes here
    var hello = "world"
    
  })(i);
}

// Nope! hello is not defined
console.log(hello); 
```

  - Just to clarify, no production-grade polyfill will do this mistake. This is just one of those hand-written simplifications that we blurt out in interviews.

## ❎ One common misconception

One common misconception is that the **top part only accepts expressions**. Even the [youtube video](https://youtu.be/Nzokr6Boeaw?t=56) kind of says that. But as we have seen, the initializer accepts either a variable declaration or a expression.  
Declaration is **not** a expression. For ex, if you write `log(var i = 0)` - it will throw a syntax error.  

This is a important distinction. If for loop didn't allow declarations in the initializer, this whole discussion would be unnecessary.



<!-- Related topics -->
<a id="related-topics" name="related-topics"> </a>

## Related topics

I simplified and skipped a few things here. We should really discuss about these things later -

1. Technically, there is no separate "block scope" and "function scope".  
 The concept of block and function scope is a very useful mental model. But according to the spec, they are both just Declaration Environments.  
Then, how is this distinction maintained? It uses two pointers in the execution context - LexicalEnvironment, VariableEnvironment and the recursive algorithm to set the pointers to the correct environment.  
 <br/>
 Ex. What happens when we write `{ let a = 1; a = 2; }`?

2. `continue`, `break`, `return`, `throw`  
These are important control flow statements, which we promptly ignored in today's discussion.  
From today's discussion, it seems like a **for loop will only "stop" when the test expression returns false**. But that is not correct. test expression is optional - so if you don't provide one, it will normally create an infinite loop.  
You can always **break** out of the loop using these statements inside the body.  
<br/>
How does that work?

## That's all

Here is a cute puppy for you -

![cute puppy](https://media.giphy.com/media/QFSD9tGuryBHy/giphy-downsized-large.gif)

<br/><br/>

Now that I have your attention -

**Let me explain why I am doing this email thing**. I always had some doubt about bits and pieces of the language - so I wanted to understand them for myself. A lot of the "advanced answers" on Javascript hide away some critical details. So, we are left guessing how it REALLY works.  
So, about a month back - I started reading some parts of the Ecmascript specification, following this [awesome guide](https://timothygu.me/es-howto/#navigating-the-spec). The spec is not very hard to read, but kinda boring and tedious. So, I wanted to have something to look upto; a place to share the interesting details to make it worth the effort.

🌀 **Feedback** - What did you NOT like about the explanation? Did you learn something? Did I go into too much details?  
Please hit reply and tell me. Your feedback can really help me write better.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2MTY4MzUzMjRdfQ==
-->