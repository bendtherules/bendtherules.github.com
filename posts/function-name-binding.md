# How does function.name and name binding work?

This is going to be one of those nitpicky articles where I document everything that I have learned about function name, from the spec.  

Let's start with two terms, which are related but not exactly the same -
1. `function.name` - When we declare a function, it creates a function object - which has a non-writable property called `name`. `name` typically stores the initial name that the function was created with.  
```js
function foo() {}
foo.name // "foo"
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA2MjEyMzc3MSwxMjI1ODg2ODIwXX0=
-->