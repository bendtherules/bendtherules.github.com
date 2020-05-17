
Welcome back to the second issue. This is that time of the weekend when I try to put on a writer's hat and spend hours looking for that perfect opening Gif 😀.

And guess what's special today? Happy Mother's Day 👩‍👦‍👦!

![Happy mother's day](https://media.giphy.com/media/xUA7b1YdLklDWnATMQ/giphy.gif)
<br/><br/>

Now, back to our topic -

We will talk about a few separate but interesting bits today. We'll start with control flow statements (like break, return), then look at how function and block scopes are maintained internally and wrap it up with a animation I built last week.

# Control flow statements
What's common between `continue`, `break`, `return` and `throw`?  They are all control flow statements and internally return a [completion record](https://tc39.es/ecma262/#sec-completion-record-specification-type). 

Statements within a block of code are normally run one-by-one till it reaches the last statement. Control flow statements change that normal order of execution. They let us exit prematurely or jump back to a different block of code. This is known as non-local transfer of control.

<!--
⬆️ It might be hard to think of `return` statements as "exiting prematurely". After all, that's how we are supposed to return any value from a function.  

But the other way of thinking is that return is a user-specified way of exiting from any part of the function body, with a specific value. `return` can be used in the middle of a function, not just at the end.
-->

**Completion record** is a internal data structure (called as record) which holds the following fields and their values -  

a. `[[Type]]` - Possible values are normal, break, continue, return, or throw. Ex - when we use `break;`, it returns a completion record with `{ [[Type]]: 'break' }`.

b. `[[Value]]` - Completion records can contain a value to store what data was returned. If value is not provided, it defaults to `undefined`.  
Ex - When we use `throw foo;`, it returns a completion record with `{ [[Type]]: 'throw',  [[Value]]: foo }`.

c. `[[Target]]` - This is a lesser used construct. continue and break statements can have a optional label - which can be used like `break foo;`. This label 'foo' is stored in the [[Target]] field.

### Little note about "normal" completions and "abrupt" completions

Return statements also create a completion record - where `[[Value]]` stores the returned value. So, `return 5;` creates `{ [[Type]]: 'return', [[Value]]: 5 }`.   
☝️ That is rather expected, but what happens when your function body doesn't return anything?
 
Well, it returns a "normal" completion record (`[[Type]]` is normal). Infact, not just functions - but almost everything returns a normal completion record. For ex, if you write any expression like `2 + 3`, that returns a normal completion with `[[Value]]: 5` .   All expressions return a normal completion record with their value. And all statements other than return, break, continue or throw also return a normal completion.  

In short, normal completion is the de-facto completion type unless the user explicitly uses one of those control flow statements. Normal completion indicates that everything is fine and the language can continue with the rest of the algorithm.  
All non-normal completions are called as abrupt completions. They are handled specially inside the algorithm.  
⭐️ For ex - when you use a return statement deep within a function, all inner constructs stop their algorithm immediately and forward the same completion record to their parent. This happens till it hits the parent function. Now, a function knows how to "handle" a return completion - so instead of forwarding it again, it returns a normal completion with the same value.  
So, different types of abrupt completions have their own handlers. If a construct can't handle a particular type of abrupt completion, it stops itself and allows the abrupt completion to bubble up. For ex., functions don't know how to handle "throw" completions - so if an error happens within a function, it bubbles up till the closest `try/catch` statement. Now the handler can decide to stop the bubbling and return a normal completion record.  
Interestingly, there are some conditional handlers which will only "sometimes" stop the bubbling. Let's talk about that next.

### How does `break label;` work? 

#### Example
First off, why is it even useful?  Let's think of - searching for a value in a 2D array. We will need nested for loops to navigate the structure and want to break out of **both** loops as soon as we find the element.
```js
// This is the label
outer:
for (var i = 0; i < arr.length; i++){
  for (var j = 0; j < arr[i].length; j++){
    if (arr[i][j] === value) {
	  
	  // break till it reaches "outer"
      break outer;
      
    }
  }
}
```
As you can see, using **break with a label** lets us exit out of multiple loops at once. Using `break;` will only exit from the current loop.  
Also, it doesn't just have to be simple for loops. `break` is handled by *iteration statements* as well as *switch-case statements*. So, this will also work -

```js
outside:
switch (1) {
  case 1:
    console.log("in case 1");
    for (var i = 0; i < 5; i++) {
      // break out of for-loop + switch-case
      break outside;
    }
    console.log("after for");
  case 2:
    console.log("in case2");
}
// > only prints "in case 1"
```

We can also add multiple labels for the same for loop or switch case and break using any of the labels. Probably not very practical, but good to note for the algo. Example -
```js
label2:
label1:
for (;;) {
  for (;;) {
    // both labels work
    break label2;
  }
}
```

`continue` is also used very similar to `break` - it can have multiple labels, move up till outer loop - **but** it only works within iteration statements, not within switch-case. `continue` is used to "skip" rest of the current iteration and continue with the next iteration. Example -

```js
for (var i = 1; i <= 5; i++) {
  if (i == 3) { continue; }
  console.log(i);
}
// > will print "1 2 4 5"
```

### Algo

Now let's talk about the algo. Quick reminder to for loops - in every iteration, if some condition is satisfied then it executes the body and then increments some variable. It also has a internal variable called `labelset` - which contains the list of labels for this for loop.  

So, what happens when for loop executes the body? The body runs each statement one-by-one. If any of the statements return *abrupt completion*, then it skips the rest of the statements and returns the same abrupt completion. Else, it returns normal completion.  
Then for-loop looks at body's completion record and decides whether to continue with the rest of the iteration.  

* If completion record is `normal`, then continue as is.
* If completion record is `throw`, then stop and return same record.
* If completion record is `continue` and -
	*  if its `[[Target]]` is empty or present in `labelset`, **then continue as is**
	*  else, stop and return same record.
* If completion record is `break` and -
	*  if its `[[Target]]` is empty or present in `labelset`, **then stop  and return normal completion**
	*  else, stop and return same record.

So, if **break** has no label or has a label which marks this for loop, then it acts as a "handler" and returns a normal completion record. Similarly, if **continue** has no label or matching label, it still acts as a handler - **but** just continues with the rest of the iteration logic instead of exiting out of it.
And for both - if the label is not part of its `labelset`, then it returns the same `break` or `continue` completion record. This will again bubble up and get handled by one of its parent for loops (or switch-case).

Just to repeat, for `continue;`- First, the loop body skips the rest of the statements because it is a abrupt completion. Then, for loop continues with the rest of the iteration. So, effectively `continue` skips the rest of the body for current iteration, but the algo will still execute the full body for next iterations.

### To summarize, who handles what?

| Completion type | Handler    |
|-----------------|------------|
| return          | Function   |
| continue        | Iteration (matching label) |
| break           | switch-case, Iteration (matching label) |
| throw           | try/catch  |

(Iteration statements = for, for-in, for-of, for-await-of, while, do-while)

💛 Also, continue and break statements are not valid across function boundaries. So, you can't write -
```js
function test() {
  // syntax error
  break;
}
for (var i = 0; i < 5; i++) {
  test();
}
```

We mentioned earlier that you can also write `break` inside switch case statements. So, naturally it's also worth taking a look at how it works internally.

# How are function and block scopes maintained? 

Let's actually start with a different question - something that it easier to visualize.

**What is your mental model for declaring a variable using `var`?**

The way I think is - 

1. Find closest function scope. That is, go to closest scope and repeatedly look for parent scope till the scope type is "function".
2. In that scope, add a entry for the variable name.

That is also what I expected to see in the spec. When it talks about declaring a variable, specially using `var` - it must do some kind of repeated lookup, right? Turns out, it doesn't. The spec says to add an entry for that variable name in `<current execution context>.VariableEnvironment.EnvironmentRecord`.  

This was a little bit odd and not what I expected. So, I want to explain what I understand about the process now -

First of all, both `var` and `let` declarations are added to the EnvironmentRecord of a Declaration environment. But this Declaration environment doesn't store the scope type as "function" or "block". So, you can't really do a lookup and check the scope type.

🌟 Roughly speaking, 
`Environment` = Scope 
`Environment.EnvironmentRecord` = Scope data. This stores the actual variable names and their values within the scope.

`execution context` = Call stack.
`current execution context` = Latest call frame in the call stack.

### About Environment -
So, what we think of as scope is formally called a `Environment`. It stores multiple type of information and not just variable data. The variables and their values are stored in the `EnvironmentRecord` section of a `Environment`.  

Now, Environment can be of multiple types - like `DeclarationEnvironment`, `ObjectEnvironment` and `GlobalEnvironment`.  
What we deal with mostly is `DeclarationEnvironment`.

### About execution context

Execution context has two properties - `VariableEnvironment` and `LexicalEnvironment`.  VariableEnvironment (VE) always points to the closest function scope and LexicalEnvironment (LE) always points to the closest block scope. This is carefully maintained by the algorithm of each construct.

### Algo

**When a enter a function -**

2. saves current value of VE and LE as oldVE and oldLE.
1. it creates a new execution context and sets that as the `current execution context`.







<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyODQ4ODkxMSwtNDk2ODA2MzU0LDY1OD
M0MDk0NiwxNjAxMDgzODk0LC0xMTk5MzIwODQ3LC0xNTk2MTI3
NjAsMTc5Mzg1MTQzNCwtNjYyNjMxNzgzLC02NjI2MzE3ODMsNT
g5NTM2MTY5LC02MzM0OTQ2MjMsMjc2NTI0Njg5LC0xOTYxNTUx
MTc4LDE0OTI5NjQxODAsLTI4NDAzMTY4LC0xMDk0MTM4OTc0LC
0xMDQ1NzY5OTQyLDk2MjUwMTE1OCwxMTM5NDA4NDkwLDQ3ODUx
Mzg0Ml19
-->