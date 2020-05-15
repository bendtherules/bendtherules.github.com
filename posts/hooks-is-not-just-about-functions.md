# React hooks is not just about functions

Whenever I hear someone (new) talk about hooks, it's all about - oh, functions are the future - that's why react moved to function components. And maybe a little bit about composition instead of inheritance, wrapper hell and reusing logic.

But all the smaller gains or micro-features gets looked over. Let's talk about them.

## 1. State Slices (or Partial isolated state)

**Partial** - Instead of one big object, we can create and manage state slices separately. Initialize it, update it without thinking about other slices.

**Isolated** - WIth reusable logic, you want to make sure that two slice namespaces never collide.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjMyMDYwMTM5LC0xNDgwNDMwMzYxLDEzMD
I4ODA4MjddfQ==
-->