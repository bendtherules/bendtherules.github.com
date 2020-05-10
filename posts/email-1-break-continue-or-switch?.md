Welcome back to the second issue. This is that time of the weekend when I try to put on a writer's hat and spend hours looking for that perfect opening Gif 😀.

And guess what's special today? Happy Mother's Day 👩‍👦‍👦!

![Happy mother's day](https://media.giphy.com/media/xUA7b1YdLklDWnATMQ/giphy.gif)
<br/><br/>

Now, back to our topic -

We will talk about a few separate but interesting bits today. We'll start with control flow statements (like break, return), then look at the wild side of switch-case statements and wrap it up with some animation I built just yesterday.

# Control flow statements
What's common between `continue`, `break`, `return` and `throw`?  
They are all control flow statements and internally, return a [completion record](https://tc39.es/ecma262/#sec-completion-record-specification-type). 

Why is this called control flow? Well, normally statements in Javascript are run one-by-one till the end of the block. A control flow is something 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY2MzY4NzY4NiwtNTM0NTQ0NjMyXX0=
-->