# React hooks is not just about functions

Whenever I hear someone (new) talk about hooks, it's all about - oh, functions are the future - that's why react moved to function components. And maybe a little bit about composition instead of inheritance, wrapper hell and reusing logic.

But all the smaller gains or micro-features gets looked over. Let's talk about them.

## 1. State Slices (or Partial isolated state)

**Partial** - Instead of one big object, we can create state slices - and update them separately. Initialize it, update it without thinking about other slices.

**Isolated** - WIth reusable logic, you need to make sure that two slice namespaces never collide. Else using hooks will be very hard, because you need to know its internal details.

Good for hooks. But also good for groupby feature within a component - each feature can manage their own state without worrying.

No need to create big manually accumulated setStates. ex- 
```jsx
componentWillReceiveProps(newProps) {
  // Create "newState" based on old (?? ❌)
  const newState = {
      showX: !this.state.showX,
      selectedType: null,
      isAuthorized: false,
  };

  // Aggregate changes
  if (newProps.selectedType !== this.state.selectedType) {
      newState.showX = true;
      newState.selectedType = value;
  }

  if (newProps.status !== "FAILURE") {
    newState.isAuthorized = true;
  }

  // Finally, set aggregated newState
  this.setState(newState);
}
```

## 2. Usememo for derived variables

What we want - Merge props.data and state.ownData into mergedData.

### Try 1
```js

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk4OTM4NDE0NSwtMjA5NjQyOTM3NiwtMT
Y1MDc4MTA4NCwxNjE1MjI4NTM0LC01MzkxMzUwODQsLTE4NTgz
OTMwMzYsLTIxMjI4MzM1NTYsLTE0ODA0MzAzNjEsMTMwMjg4MD
gyN119
-->