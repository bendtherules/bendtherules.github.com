# React hooks is not just about functions

Whenever I hear someone (new) talk about hooks, it's all about - oh, functions are the future - that's why react moved to function components. And maybe a little bit about composition instead of inheritance, wrapper hell and reusing logic.

But all the smaller gains or micro-features gets looked over. Let's talk about them.

## 1. State Slices (or Partial isolated state)

**Partial** - Instead of one big object, we can create and manage state slices separately. Initialize it, update it without thinking about other slices.

**Isolated** - WIth reusable logic, you need to make sure that two slice namespaces never collide. Else using hooks will be very hard, because you need to know its internal details.

Good for hooks. But also good for groupby feature within a component - each feature can manage their own state without worrying.


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NjY2NDQ2MjIsLTE0ODA0MzAzNjEsMT
MwMjg4MDgyN119
-->