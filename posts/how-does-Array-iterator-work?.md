
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
(Remember “this” within the iterator is the iterator object itself, not the actually array. So, it stores this link during creation)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkwNDQzMDA5NiwtMjA0MDIxNTUzNCwtMT
EyNjUxODkxNSwtODUxODY2MjUsLTE1MTU5OTMwODEsLTE3OTQ2
NTQzMDQsMTAzNjA5NzEwNCwtNDM5OTk3ODU5XX0=
-->