## Optional Chaining and Nullish Coalescing

### When to use it
Use optional chaining (`?.`) when reading a nested value that might not
exist yet, such as data that has not loaded. Use nullish coalescing
(`??`) to supply a fallback value only when the original value is `null`
or `undefined`.

### Pattern
```js
const user = { profile: { address: { city: "Boston" } } };
const guest = {};

// Optional chaining: stops and returns undefined instead of throwing
const city = user?.profile?.address?.city; // "Boston"
const guestCity = guest?.profile?.address?.city; // undefined

// Optional chaining on a function that might not exist
onSave?.(formData);

// Optional chaining with array access
const firstTag = user?.tags?.[0];

// Nullish coalescing: fallback only for null or undefined
const pageSize = user?.settings?.pageSize ?? 10;

// Why ?? is safer than || when 0 or "" are valid values
const count = 0;
const withOr = count || 10;   // 10, which is wrong here
const withNullish = count ?? 10; // 0, which is correct
```

### How it works
`?.` checks whether the value on its left is `null` or `undefined`
before trying to read the next property. If it is, the whole expression
short-circuits to `undefined` instead of throwing a "cannot read property
of undefined" error. `onSave?.(formData)` uses the same idea to call a
function only if it exists. `??` returns its right-hand side only when
the left-hand side is exactly `null` or `undefined`. This differs from
`||`, which falls back on any falsy value, including `0`, `""`, and
`false`, even when those are legitimate values you want to keep.

### Common mistakes
- Reaching for `||` to set a default when the real value could
  legitimately be `0`, `""`, or `false`, which silently replaces a valid
  value with the fallback.
- Chaining `?.` so far that a real bug, such as a wrong field name deep in
  the data, gets hidden as a quiet `undefined` instead of a visible error
  during development.
- Using `?.` on a value you already know exists, which adds unnecessary
  noise and can hide the fact that the value should never be missing.

### Interview angle
Q: Why does `count || 10` behave differently from `count ?? 10` when
`count` is `0`?
A: `||` falls back to `10` for any falsy value, and `0` is falsy, so it
returns `10`. `??` only falls back for `null` or `undefined`, so it
correctly keeps `0`.
