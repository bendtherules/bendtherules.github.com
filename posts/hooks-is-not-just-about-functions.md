# React hooks is not just about functions

<!--
Whenever I hear someone (new) talk about hooks, it's all about - oh, functions are the future - that's why react moved to function components. And maybe a little bit about composition instead of inheritance, wrapper hell and reusing logic.
-->

All the smaller gains or micro-features gets looked over. Let's talk about them.

<br/><br/><br/><br/><br/><br/>

## 1. State Slices (or Partial isolated state)

**Partial** - Instead of one big object, we can create state slices - and update them separately. Initialize it, update it without thinking about other slices.

**Isolated** - WIth reusable logic, you need to make sure that two slice namespaces never collide. Else using hooks will be very hard, because you need to know its internal details.

Good for hooks. But also good for groupby feature within a component - each feature can manage their own state without worrying.

*Coupling
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
if (nextProps.foo !== this.props.foo) ...
```

### Level 2
```js
if (nextProps.foo !== this.props.foo ||
  nextProps.bar !== this.props.bar ||
  nextProps.baz !== this.props.baz ||
  nextProps.foo2 !== this.props.foo2) {
  // ... do something
} else if (nextProps.xyz !== this.props.xyz) {
  // ... do something else
}
```

### Level 3
```js
getFooPlusBar() {
  return this.props.foo + this.props.bar;
}
/*
getFooPlusBar(props) {
  return props.foo + props.bar;
}
*/

componentWillReceiveProps() {
  if (getFooPlusBar(nextProps) !== getFooPlusBar(this.props)) {
    // ...
  }
}
```

### With hooks
```js
useEffect( () => {...}, [foo, foo + bar])
```

### Level 4 - derived
```js
// <Child dataArray={[...this.props.data, ownData]} />

if (nextProps.dataArray !== props.dataArray) ...
```

*Incentive mismatch

## 

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1MDA0NzM2MDIsLTE5NzgzNDA5MjYsLT
E2MzE5OTQ2NDUsMTMyNDQ2MTg2MSwtMzI1NjYxNjQsLTE4NTAw
MTU4ODMsLTkyNTM1MzUzNywtNDk0MTA5MzE4LC0xNTEyNDkyND
YzLC0xNDgwODM1NDM0LDE5OTE2NDU5MDgsMTIwMDQ5OTg4NSwt
Mjk1MzAyMzYsMzk5NDYyOTk4LDU0NTk2ODkxNiwtODc5OTQwNj
c4LC0yMDk2NDI5Mzc2LC0xNjUwNzgxMDg0LDE2MTUyMjg1MzQs
LTUzOTEzNTA4NF19
-->