# How does in-built Array iterator work?

We use `[...someArray]` or `array.values()` to get multiple values out of a array.  And we kind of know how it works - basically it gives us all the values from the array.  Internally, both of them uses the iterator protocol which is already defined on arrays.

But do we really know the details of how in-built Array iterator works? Can you write a precise polyfill to implement iterator protocol on Array?

## A little about the iterator protocol

When we use spread operator, like `[...someArray]` - it treats someArray as a general iterable, not specifically as an array.  
It tries to get a iterator from the iterable, using `iter = someArray[Symbol.iterator]()`. Then it consumes all the values from the iterable by calling `iter.next()` repeatedly. `.next()` returns response in this structure 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA1NjIyMzkzOSwtODUxODY2MjUsLTE1MT
U5OTMwODEsLTE3OTQ2NTQzMDQsMTAzNjA5NzEwNCwtNDM5OTk3
ODU5XX0=
-->