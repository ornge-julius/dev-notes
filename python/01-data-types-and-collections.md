## Data Types and Collections: list, tuple, dict, set

### When to use it
Use a list for an ordered, changeable sequence. Use a tuple for an
ordered sequence that must not change. Use a dict for key-value lookup.
Use a set when you need unique items and do not care about order. These
four cover the same ground as the Ruby array and hash files, and the JS
array-methods file, but Python splits "array" into a mutable list and an
immutable tuple.

### Pattern
```python
# list: ordered, mutable
fruits = ["apple", "banana", "cherry"]
fruits.append("date")
fruits[0] = "avocado"

# tuple: ordered, immutable
point = (3, 7)
x, y = point  # unpacking

# dict: key-value pairs
user = {"id": 1, "name": "Ada", "role": "admin"}
user["role"] = "member"
role = user.get("role", "guest")  # safe read with a default

# set: unique items, no guaranteed order
tags = {"python", "web", "python"}  # {"python", "web"}
tags.add("api")
has_web = "web" in tags
```

### How it works
A list stores items in order and lets you change, add, or remove items
after creation. A tuple looks the same but has no methods to change its
contents, which is why it is often used for fixed groupings like
coordinates or for dict keys, since dict keys must be immutable. A dict
maps each key to one value and gives near-instant lookup by key instead
of by scanning every item. `dict.get(key, default)` reads a key without
raising an error if it is missing, unlike `dict[key]`. A set stores only
unique values and removes duplicates automatically, and checking
membership with `in` is much faster on a set than on a list for large
data.

### Common mistakes
- Using a list when a set would remove needed duplicate checks, or using
  a list for membership checks (`item in my_list`) on large data, which
  scans every item instead of using a set's fast lookup.
- Trying to use a list as a dict key, which raises a `TypeError` because
  lists are mutable and dict keys must be hashable and immutable.
- Reading a dict with `user["role"]` when the key might not exist,
  instead of `user.get("role", default)`, which raises a `KeyError`.

### Interview angle
Q: Why can a tuple be used as a dict key, but a list cannot?
A: Dict keys must be hashable, and hashability requires the value to
never change after creation. A tuple is immutable, so its hash stays
stable. A list is mutable, so Python does not allow it as a key.
