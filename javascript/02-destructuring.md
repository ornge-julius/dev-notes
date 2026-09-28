## Destructuring and Default Values

### When to use it
Use destructuring to pull specific fields out of an object or array into
their own variables, instead of writing `object.field` repeatedly. This is
the exact syntax React uses for reading props and for `useState`.

### Pattern
```js
// Object destructuring
const user = { id: 1, name: "Ada", role: "admin" };
const { name, role } = user;

// With a default value when the field might be missing
const { theme = "light" } = user;

// Nested destructuring
const response = { data: { user: { name: "Ada" } } };
const { data: { user: { name: userName } } } = response;

// Array destructuring, used by useState
const [count, setCount] = useState(0);

// Destructuring directly in a function parameter, used by React props
function UserCard({ name, role = "member" }) {
  return <p>{name} - {role}</p>;
}

// Renaming a field while destructuring
const { name: displayName } = user;
```

### How it works
Object destructuring matches variable names to the object's field names.
Array destructuring matches variables to array positions, which is why
`useState()` can return `[value, setterFunction]` and you choose the
names yourself. A default value after `=` only applies when the field is
`undefined`, not when it is `null` or `0`. Destructuring works directly in
a function's parameter list, which is why most React components write
`function UserCard({ name, role })` instead of `function UserCard(props)`
and then `props.name` everywhere in the body.

### Common mistakes
- Destructuring a field that does not exist on the object without a
  default value, which silently produces `undefined` instead of an error.
- Destructuring a nested field before checking that the parent object
  exists, such as `data.user.name` when `data.user` can be `null`, which
  throws a runtime error.
- Renaming a variable while destructuring and forgetting to update every
  place that variable is used later in the function.

### Interview angle
Q: Why does `const { theme = "light" } = user` not apply the default when
`user.theme` is explicitly set to `null`?
A: The default value only kicks in when the destructured field is
`undefined`. `null` is a real, present value, so destructuring keeps it
as `null` instead of substituting the default.
