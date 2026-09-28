## Spread and Rest

### When to use it
Use the spread operator (`...`) to copy an array or object, most often
when updating React or Redux state without mutating the original value.
Use rest syntax, which looks the same but appears in a different
position, to collect the remaining items into one variable.

### Pattern
```js
// Spread: copying and updating an object immutably
const user = { id: 1, name: "Ada", role: "member" };
const updatedUser = { ...user, role: "admin" };
// { id: 1, name: "Ada", role: "admin" }

// Spread: copying and adding to an array immutably
const todos = ["Buy milk", "Walk dog"];
const newTodos = [...todos, "Write code"];
// ["Buy milk", "Walk dog", "Write code"]

// Spread: merging two objects, later keys win
const defaults = { theme: "light", pageSize: 10 };
const overrides = { theme: "dark" };
const settings = { ...defaults, ...overrides };
// { theme: "dark", pageSize: 10 }

// Rest: collecting the remaining props to forward to a DOM element
function Button({ label, ...rest }) {
  return <button {...rest}>{label}</button>;
}

// Rest: collecting extra function arguments into an array
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
sum(1, 2, 3); // 6
```

### How it works
Spread expands an array or object's own items into a new array or object,
which is how you build a new value that looks almost the same as the
original, except for the one field you changed. This matters in React and
Redux because state must never be mutated directly, so
`{ ...user, role: "admin" }` creates a brand new object instead of
changing `user.role` in place. When `...` appears in a function's
parameter list or in a destructuring pattern, it works the opposite way:
it gathers the leftover values into one array or object, which is why
`{ label, ...rest }` collects every prop except `label` into `rest`.

### Common mistakes
- Assuming spread makes a deep copy. `{ ...user }` copies only the
  top-level fields. A nested object inside `user` is still the same
  reference, so mutating it after the spread still affects the original.
- Putting the override object before the defaults when merging, such as
  `{ ...overrides, ...defaults }`, which makes the defaults win instead
  of the overrides.
- Confusing spread and rest because they use the same three dots. The
  position tells you which one it is: spread expands an existing value,
  rest collects into a new one.

### Interview angle
Q: Does `{ ...user, address: { ...user.address, city: "Boston" } }` fully
protect the original `user` object from mutation?
A: Yes, as long as every nested level you might change is spread as well.
Spreading only the top level leaves nested objects shared by reference,
so each level that could change must be spread on its own.
