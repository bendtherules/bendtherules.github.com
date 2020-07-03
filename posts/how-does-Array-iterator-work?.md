# How does in-built Array iterator work?

We use `[...someArray]` or `array.values()` to get multiple values out of a array.  And we kind of know how it works - basically it gives us all the values from the array.  Internally, both of them uses the iterator protocol which is already defined on arrays.

But do we really know the details of how in-built Array iterator works? Can you write a precise polyfill to implement iterator protocol on Array?

## A little about the iterator protocol

When we use spread operator, like `[...someArray]` - it treats someArray as a general iterable, not specifically as an array. It tries to get a iterator from the iterable, using `iter = someArray[Symbol.iterator()]`
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQzODE1NjUxNywtMTc5NDY1NDMwNCwxMD
M2MDk3MTA0LC00Mzk5OTc4NTldfQ==
-->