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

### Try 1 - just create when needed
```js
render() {
  const mergedData = [...props.data, state.ownData];
}
```

But... what if you need mergedData in multiple methods? Just copy paste?

### Try 2 - create getMergedData method

```js
getMergedData = () => [...props.data, state.ownData]
```

But... if mergedData is needed in 3 places, we will end up creating 3 copies on every render. What if you need the same array or want to check if it changed?

### Try 3 - Create instance variable on props change
Well, cache the derived variable value on props or state change.

```js
componentWillUpdate(nextProps, nextState) {
  if (nextProps.data !== props.data || 
      nextState.ownData !== this.state.ownData) {
    this.mergedData = [...props.data, state.ownData];
  }
}
```

Or, use a memoization helper. (Which, frankly, no one does.) Also cleanup on unmount.

### With hooks
```js
const mergedData = useMemo([...props.data, state.ownData]);
```

## 3. Dependency list (aka detecting props change)

### Level 1
```js
if (nextProps.foo !== props.foo) ...
```

### Level 2
```js
if (nextProps.foo !== props.foo ||
  nextProps.bar !== props.bar) ...
```

### Level 3
```js
getFooSquare

componentWillReceiveProps() {
  if (nextProps.foo !== props.foo) {
    // ...
  }
}

```




<!--stackedit_data:
eyJoaXN0b3J5IjpbNjY2NTQ5MTc5LDE5OTE2NDU5MDgsMTIwMD
Q5OTg4NSwtMjk1MzAyMzYsMzk5NDYyOTk4LDU0NTk2ODkxNiwt
ODc5OTQwNjc4LC0yMDk2NDI5Mzc2LC0xNjUwNzgxMDg0LDE2MT
UyMjg1MzQsLTUzOTEzNTA4NCwtMTg1ODM5MzAzNiwtMjEyMjgz
MzU1NiwtMTQ4MDQzMDM2MSwxMzAyODgwODI3XX0=
-->