# How does in-built Array iterator work?

We use `[…someArray]` or `array.values()` to get multiple values out of a array.  And we kind of know how it works - basically it gives us all the values from the array.  Internally, both of them uses the iterator protocol which is already defined on arrays.

But do we really know the details of how in-built Array iterator works? Can you write a precise polyfill to implement iterator protocol on Array?
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExMzY5ODA4MzgsLTQzOTk5Nzg1OV19
-->