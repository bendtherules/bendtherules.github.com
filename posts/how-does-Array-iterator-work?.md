
# How does in-built Array iterator work?

We use `[...someArray]` or `array.values()` to get multiple values out of a array.  And we kind of know how it works - basically it gives us all the values from the array.  Internally, both of them uses the iterator protocol which is already defined on arrays.

But do we really know the details of how in-built Array iterator works? Can you write a precise polyfill to implement iterator protocol on Array?

## A little about the iterator protocol

When we use spread operator, like `[...someArray]` - it treats someArray as a general iterable, not specifically as an array.

1. It tries to get a iterator from the iterable, using `iter = someArray[Symbol.iterator]()`. 

2. Then it consumes all the values from the iterable by calling `iter.next()` repeatedly.  
    This method `.next()` returns response in this structure `{value: "something", done: true|false}`.
    
3. While values are available, it returns `done: true`; after all values are finished, it returns `done: false`. That is when the consumer (i.e. spread operator) stops asking for more values.

This is how the iterable/iterator stuff works.  
In this case, Iterable is `someArray` - which has a special property `Symbol.iterator` whose value is a method. Calling this method returns a iterable (`iter`), which has `.next()` method. Calling this method returns all the values one-by-one.

Normally, you have to support this protocol by writing code for these methods, but Array already has definition for these.  

## On arrays

Array has in-built support for iterable/iterator protocol, through prototype. Array.prototype has this method on `Symbol.iterator`, which creates a ArrayIterator. 

ArrayIterator internally contains these properties -
* [[ IteratedArrayLike ]] - which points back to the actual array.  
( Remember, “this” within the ArrayIterator is the iterator object itself, not the actual array. So, it has to store a link to the array during creation )

* [[ ArrayLikeNextIndex ]] - the next index whose value should be returned. Default is 0.  
  
* [[ ArrayLikeIterationKind ]] - For normal purpose (like incase of `...someArray`), it is “value”.  
In general, it can be “key”, “value” or “key+value”. This is what allows reusing the same iterator mechanism for array.keys(), .values() and .entries().

| syntax        | kind        |
|---------------|-------------|
| `...arr`        | "value"     |
| `arr.keys()`    | "key"       |
| `arr.values()`  | "value"     |
| `arr.entries()` | "key+value" |
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQyNzY2Njc4NiwtMjA0MDIxNTUzNCwtMT
EyNjUxODkxNSwtODUxODY2MjUsLTE1MTU5OTMwODEsLTE3OTQ2
NTQzMDQsMTAzNjA5NzEwNCwtNDM5OTk3ODU5XX0=
-->