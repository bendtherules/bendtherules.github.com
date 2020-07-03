
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
In this case, Iterable is `someArray` - which has a special method with the key `Symbol.iterator`. Calling this method returns a iterable (`iter`) - which has `.next()` method to return all the values.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNDAyMTU1MzQsLTExMjY1MTg5MTUsLT
g1MTg2NjI1LC0xNTE1OTkzMDgxLC0xNzk0NjU0MzA0LDEwMzYw
OTcxMDQsLTQzOTk5Nzg1OV19
-->