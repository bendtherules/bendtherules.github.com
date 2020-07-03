# How does in-built Array iterator work?

We use `[...someArray]` or `array.values()` to get multiple values out of a array.  And we kind of know how it works - basically it gives us all the values from the array.  Internally, both of them uses the iterator protocol which is already defined on arrays.

But do we really know the details of how in-built Array iterator works? Can you write a precise polyfill to implement iterator protocol on Array?

## A little about the iterator protocol

When we use spread operator, like `[...someArray]` - it treats someArray as a general iterable, not specifically as an array.  
1. It tries to get a iterator from the iterable, using `iter = someArray[Symbol.iterator]()`. 
2. Then it consumes all the values from the iterable by calling `iter.next()` repeatedly. 
3. This method `.next()` returns response in this structure `{value: "something", done: true|false}`.  While values are available, it returns `d`
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjY1NDIwNDc4LC04NTE4NjYyNSwtMTUxNT
k5MzA4MSwtMTc5NDY1NDMwNCwxMDM2MDk3MTA0LC00Mzk5OTc4
NTldfQ==
-->