
Welcome back to the second issue. This is that time of the weekend when I try to put on a writer's hat and spend hours looking for that perfect opening Gif 😀.

And guess what's special today? Happy Mother's Day 👩‍👦‍👦!

![Happy mother's day](https://media.giphy.com/media/xUA7b1YdLklDWnATMQ/giphy.gif)
<br/><br/>

Now, back to our topic -

We will talk about a few separate but interesting bits today. We'll start with control flow statements (like break, return), then look at the wild side of switch-case statements and wrap it up with some animation I built just yesterday.

# Control flow statements
What's common between `continue`, `break`, `return` and `throw`?  
They are all control flow statements and internally, return a [completion record](https://tc39.es/ecma262/#sec-completion-record-specification-type). 

Statements within a block of code are normally run one-by-one and the process ends when the last statement has finished execution. Control flow statements change that normal order of execution. They let us exit prematurely or jump back to a different block of code. This is known as non-local transfer of control.

⬆️ It might be hard to think of `return` statements as "exiting prematurely". After all, that's how we are supposed to return any value from a function.  
But the other way of thinking is that return is a user-specified way of exiting from any part of the function body, with a specific value. `return` can be used in the middle of a function, not just at the end.

**Completion record** is a internal data structure which 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0MjgwMDM1MjcsLTEyMzY2MzY0NzEsMT
IxMjIzODE3MSwtMTAwMTM1ODY5MywtNTM0NTQ0NjMyXX0=
-->