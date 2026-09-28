## Functions: Default Arguments, *args, and **kwargs

### When to use it
Use a default argument to make a parameter optional. Use `*args` when a
function must accept any number of positional arguments. Use `**kwargs`
when it must accept any number of named arguments.

### Pattern
```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

greet("Ada")              # "Hello, Ada!"
greet("Ada", "Hi")         # "Hi, Ada!"


def total(*numbers):
    return sum(numbers)

total(1, 2, 3)  # 6


def build_user(**fields):
    return fields

build_user(name="Ada", role="admin")
# {"name": "Ada", "role": "admin"}


def create_report(title, *sections, **metadata):
    print(title, sections, metadata)

create_report("Q1", "intro", "summary", author="Ada", year=2026)


# The mutable-default-argument trap
def add_item(item, items=[]):
    items.append(item)
    return items

add_item("a")  # ["a"]
add_item("b")  # ["a", "b"], not ["b"] as most people expect


# The fix: use None and create the list inside the function
def add_item_safe(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

### How it works
A default argument value is evaluated once, when Python defines the
function, not each time the function runs. `*args` collects any extra
positional arguments into a tuple inside the function. `**kwargs`
collects any extra named arguments into a dict inside the function. Both
let a function accept a flexible number of inputs, which is common in
wrapper functions and decorators. Because a default value is created only
once at definition time, a mutable default such as `[]` or `{}` is
shared across every call that does not pass its own value, so changes
made in one call leak into the next.

### Common mistakes
- Using a mutable object, such as `[]` or `{}`, as a default argument,
  which creates one shared object reused across every call.
- Mixing up the order of `*args` and `**kwargs` in a function signature.
  `*args` must come before `**kwargs`, and both come after regular
  positional and default parameters.
- Forgetting that `*args` is a tuple and `**kwargs` is a dict inside the
  function body, and trying to index `kwargs` by position.

### Interview angle
Q: Why does calling `add_item("a")` then `add_item("b")` return
`["a", "b"]` on the second call instead of `["b"]`?
A: The default value `items=[]` is created once, when the function is
defined, and that same list object is reused across every call that does
not supply its own `items` argument, so items from earlier calls persist.
