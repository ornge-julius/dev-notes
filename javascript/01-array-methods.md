## Array Methods: map, filter, reduce

These three methods show up in almost every React component, in Redux
selectors, and in nearly every JavaScript coding interview. All three
return a new array or value, and none of them changes the original array.

### map: transform each item

#### When to use it
Use `.map()` when you need a new array with the same number of items, but
with each item changed in some way, such as turning raw API data into the
shape a component needs.

#### Pattern
```js
const users = [
  { id: 1, firstName: "Ada", lastName: "Lovelace" },
  { id: 2, firstName: "Alan", lastName: "Turing" },
];

const fullNames = users.map((user) => `${user.firstName} ${user.lastName}`);
// ["Ada Lovelace", "Alan Turing"]
```

#### How it works
`.map()` calls the given function once for every item in the array, in
order, and collects the return values into a new array. The new array
always has the same length as the original array, because `.map()` does
not skip items.

### filter: keep some items

#### When to use it
Use `.filter()` when you need a shorter array that contains only the
items matching a condition, such as showing only completed todos or only
users older than 18.

#### Pattern
```js
const todos = [
  { id: 1, text: "Buy milk", done: true },
  { id: 2, text: "Walk dog", done: false },
  { id: 3, text: "Write code", done: true },
];

const completed = todos.filter((todo) => todo.done);
// [{ id: 1, ... }, { id: 3, ... }]

// Removing one item by id, a common Redux reducer pattern:
function removeTodo(todos, idToRemove) {
  return todos.filter((todo) => todo.id !== idToRemove);
}
```

#### How it works
`.filter()` calls the given function once for every item and keeps the
item in the new array only when the function returns a truthy value. The
new array can be shorter than the original, or even empty, but it is
never longer.

### reduce: collapse into one value

#### When to use it
Use `.reduce()` when you need to turn a whole array into a single value,
such as a total, a count, or a lookup object built from a list.

#### Pattern
```js
const cart = [
  { name: "Book", price: 12, quantity: 2 },
  { name: "Pen", price: 3, quantity: 5 },
];

const total = cart.reduce((sum, item) => {
  return sum + item.price * item.quantity;
}, 0);
// 39

// Building a lookup object keyed by id, common when normalizing Redux state:
const users = [
  { id: 1, name: "Ada" },
  { id: 2, name: "Alan" },
];

const usersById = users.reduce((lookup, user) => {
  lookup[user.id] = user;
  return lookup;
}, {});
// { 1: { id: 1, name: "Ada" }, 2: { id: 2, name: "Alan" } }
```

#### How it works
`.reduce()` calls the given function once for every item, and passes the
result forward as the accumulator for the next call. The second argument
to `.reduce()`, here `0` or `{}`, is the starting value of the
accumulator before the first item runs. The function must return the
updated accumulator on every call, or the next item receives the wrong
value.

### Chaining them together

#### Pattern
```js
const cart = [
  { name: "Book", price: 12, quantity: 2, inStock: true },
  { name: "Pen", price: 3, quantity: 5, inStock: false },
  { name: "Notebook", price: 8, quantity: 3, inStock: true },
];

const totalForAvailableItems = cart
  .filter((item) => item.inStock)
  .map((item) => item.price * item.quantity)
  .reduce((sum, lineTotal) => sum + lineTotal, 0);
// 48
```

#### How it works
Each method returns a new array (or value), so you can call the next
method directly on that result. Reading the chain top to bottom describes
the steps in plain language: keep the in-stock items, turn each one into
its line total, then add up all the line totals.

### Common mistakes
- Forgetting to `return` inside the callback for `.map()` or `.reduce()`,
  which produces `undefined` for every item.
- Forgetting the starting value for `.reduce()`. Without it, `.reduce()`
  uses the array's first item as the starting accumulator and skips
  calling the function for that first item, which breaks the calculation
  when the array holds objects instead of numbers.
- Using `.map()` when the goal is actually to filter or reduce, which
  leaves `undefined` entries in the result for the items that should
  have been skipped.
- Mutating an object inside `.map()` instead of returning a new object,
  such as `user.active = true; return user;`. This changes the original
  array's objects, and it breaks the "no mutation" rule that Redux
  reducers depend on.

### Interview angle
Q: What happens if you leave out the starting value in
`cart.reduce((sum, item) => sum + item.price)`?
A: `.reduce()` uses `cart[0]` as the starting accumulator and begins
calling the function from the second item onward. Since `cart[0]` is an
object, not a number, `sum + item.price` produces a broken result such as
string concatenation or `NaN` instead of a correct total.
