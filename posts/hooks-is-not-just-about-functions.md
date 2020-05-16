# React hooks is not just about functions

<!--
Whenever I hear someone (new) talk about hooks, it's all about - oh, functions are the future - that's why react moved to function components. And maybe a little bit about composition instead of inheritance, wrapper hell and reusing logic.
-->

All the smaller gains or micro-features gets looked over. Let's talk about them.

<br/><br/><br/><br/><br/><br/><br/>

## 1. State Slices (or Partial isolated state)

**Partial**
<br/><br/>
**Isolated**
<br/><br/><br/><br/><br/>

<!--
**Partial** - Instead of one big object, we can create state slices - and update them separately. Initialize it, update it without thinking about other slices.

**Isolated** - WIth reusable logic, you need to make sure that two slice namespaces never collide. Else using hooks will be very hard, because you need to know its internal details.

Good for hooks. But also good for groupby feature within a component - each feature can manage their own state without worrying.
-->

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

<br/><br/>
But... what if you need mergedData in multiple methods? Just copy paste?

<br/><br/>

### Try 2 - create getMergedData method

```js
getMergedData = () => [...props.data, state.ownData]
```

<br/><br/><br/>

But... if mergedData is needed in 3 places, we will end up creating 3 copies. 
What if you need the same array or want to check if it changed?

<br/><br/><br/><br/>

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

<br/><br/><br/><br/>
Or, use a memoization helper. (Which, frankly, no one does.) <!-- Also cleanup on unmount. -->
<br/><br/><br/><br/>

### With hooks
```js
const mergedData = useMemo(
() => [...props.data, state.ownData],
[props.data, state.ownData]);
```
<br/><br/><br/><br/><br/><br/>

## 3. Dependency list (aka detecting props change)

### Level 1
```js
if (nextProps.foo !== this.props.foo) ...
```
<br/><br/><br/><br/>

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
<br/><br/>

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
<br/><br/>

### With hooks
```js
useEffect( () => {...}, [foo, foo + bar])
```
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Level 4 - derived
```js
// <Child dataArray={[...this.props.data, ownData]} />

if (nextProps.dataArray !== props.dataArray) ...
```

*Incentive mismatch

<br/><br/><br/><br/><br/><br/>

## Merged life cycle

Separate method for -
1. Constructor
2. DidMount
3. WillUpdate (or DidUpdate ??)
4. Unmount

*Co-located effect and cleanup code

<br/><br/><br/><br/>

## Groupby feature vs Groupby lifecycle

Humans vs computers
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

## ❌ Organising function components

```js
function ListContainer(props) {
  // 1. Initialize
  // Extract props
  const {x, y, z} = props;
  const [lastPageToken, setLastPageToken] = useState(
    undefined
  );
  const [listData, setListData] = useState<IMessage[]>([]);
  // Init custom hooks
  const {
    isIntersecting: loadMoreData,
    markerRef: bottomMarkerRef
  } = useBottomMarker<HTMLDivElement>();

  // Fetch - initially on mount + when end of list is reached
  useEffect(() => {
    if (loadMoreData) {
      // Prepare fetchURL
      let fetchUrl = `${apiRootURL}/messages`;
      if (lastPageToken !== undefined) {
        fetchUrl = `${fetchUrl}?pageToken=${lastPageToken}`;
      }

      // Fetch and store data
      fetch(fetchUrl)
        .then(res => res.json() as Promise<IAPIResponse>)
        .then(responseObj => {
          // Store pageToken
          setLastPageToken(responseObj.pageToken);

          // Append messages
          setListData(oldMessages => [...oldMessages, ...responseObj.messages]);
        });
    }
  }, [loadMoreData]);

  const removeDataByIndex = useCallback(removeID => {
    setListData(oldListData => {
      return [...oldListData].filter(({ id }) => id !== removeID);
    });
  }, []);

  return (
    <div role="main">
      <ul className="listContainer">
        {listData.map((eachData, index) => (
          <li key={eachData.id}>
            <ListElement
              id={eachData.id}
              prevID={index > 0 ? listData[index - 1].id : undefined}
              nextID={
                index < listData.length - 1 ? listData[index + 1].id : undefined
              }
              {...eachData}
              onSwipeSuccess={() => {
                removeDataByIndex(eachData.id);
              }}
            />
          </li>
        ))}
        <li key="fakeElement">
          <FakeListElement ref={bottomMarkerRef} loading={loadMoreData} />
        </li>
      </ul>
    </div>
  );
}
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzIyNTExMTgxLDIxMTk1ODE5NjgsMTQ1MD
A5NzQyMywtNzg5Mzg4MDY2LC0xOTc4MzQwOTI2LC0xNjMxOTk0
NjQ1LDEzMjQ0NjE4NjEsLTMyNTY2MTY0LC0xODUwMDE1ODgzLC
05MjUzNTM1MzcsLTQ5NDEwOTMxOCwtMTUxMjQ5MjQ2MywtMTQ4
MDgzNTQzNCwxOTkxNjQ1OTA4LDEyMDA0OTk4ODUsLTI5NTMwMj
M2LDM5OTQ2Mjk5OCw1NDU5Njg5MTYsLTg3OTk0MDY3OCwtMjA5
NjQyOTM3Nl19
-->