## More Array Methods: find, some, every, sort

### When to use it
Use `.find()` to get one matching item instead of a whole array. Use
`.some()` and `.every()` to ask a yes-or-no question about the array. Use
`.sort()` to order the items, carefully, since it behaves differently
than the other methods on this list.

### Pattern
```js
const users = [
  { id: 1, name: "Ada", active: true },
  { id: 2, name: "Alan", active: false },
  { id: 3, name: "Grace", active: true },
];

// find: the first matching item, or undefined
const user = users.find((u) => u.id === 2);
// { id: 2, name: "Alan", active: false }

// some: true if at least one item matches
const hasInactiveUser = users.some((u) => !u.active);
// true

// every: true only if all items match
const allActive = users.every((u) => u.active);
// false

// sort: mutates the array in place, so copy it first
const sortedByName = [...users].sort((a, b) => a.name.localeCompare(b.name));

// sort: numbers need a comparator, or it sorts as text
const numbers = [10, 1, 21, 2];
const wrongSort = [...numbers].sort();
// [1, 10, 2, 21], sorted as strings
const correctSort = [...numbers].sort((a, b) => a - b);
// [1, 2, 10, 21]
```

### How it works
`.find()` stops looping as soon as it finds one matching item and returns
that item directly, not an array. `.some()` stops as soon as one item
passes the test and returns `true`, or returns `false` if none do.
`.every()` stops as soon as one item fails the test and returns `false`,
or returns `true` if all of them pass. `.sort()` is different from the
other methods on this page: it changes the original array in place and
also returns it, so you must spread the array first,
`[...users].sort(...)`, to avoid mutating state. Without a comparator
function, `.sort()` converts items to strings and compares them as text,
which sorts numbers in the wrong order.

### Common mistakes
- Calling `.sort()` directly on a piece of React or Redux state, which
  mutates it in place and can cause missed re-renders or broken reducers.
- Sorting numbers without a comparator function and getting a
  text-based order instead of a numeric one.
- Using `.filter()` and taking the first result when `.find()` already
  does that directly, and more efficiently, since `.find()` stops at the
  first match instead of scanning the whole array.
- Confusing `.some()` and `.every()`. `.some()` answers "is there at
  least one," and `.every()` answers "do they all."

### Interview angle
Q: Why does `[10, 1, 21, 2].sort()` return `[1, 10, 2, 21]` instead of
`[1, 2, 10, 21]`?
A: Without a comparator function, `.sort()` converts each item to a
string and compares them character by character, so `"10"` sorts before
`"2"` because `"1"` comes before `"2"`.
