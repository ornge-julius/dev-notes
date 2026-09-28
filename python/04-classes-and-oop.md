## Classes and OOP

### When to use it
Use a class when you need to group related data and behavior together,
and when you expect to create many similar objects, such as users or
orders, that each hold their own state.

### Pattern
```python
class User:
    def __init__(self, name, role="member"):
        self.name = name
        self.role = role

    def __str__(self):
        return f"User({self.name}, {self.role})"

    def __repr__(self):
        return f"User(name={self.name!r}, role={self.role!r})"

    def __eq__(self, other):
        if not isinstance(other, User):
            return False
        return self.name == other.name and self.role == other.role

    def promote(self):
        self.role = "admin"


class Admin(User):
    def __init__(self, name):
        super().__init__(name, role="admin")

    def promote(self):
        raise ValueError("Admin is already the highest role")


ada = User("Ada")
ada.promote()
print(ada)          # User(Ada, admin), calls __str__
print([ada])        # [User(name='Ada', role='admin')], calls __repr__
```

### How it works
`__init__` runs automatically when you create a new object, and sets up
that object's starting state on `self`. Every instance method takes
`self` as its first parameter, which refers to the specific object the
method was called on. `super().__init__(...)` calls the parent class's
`__init__`, which lets a subclass like `Admin` reuse the parent's setup
logic instead of duplicating it. `__str__` controls what `print(obj)`
and `str(obj)` show, meant for a readable, user-facing description.
`__repr__` controls what shows in a list or in the interactive shell,
meant for an unambiguous, debugging-focused description. `__eq__`
controls what `==` does between two instances, since without it Python
compares objects by identity, not by their field values.

### Common mistakes
- Forgetting to call `super().__init__(...)` in a subclass's `__init__`,
  which skips the parent class's setup and leaves fields unset.
- Defining `__eq__` without also defining `__hash__`, which makes
  instances of that class unusable as dict keys or set members, since
  Python removes the default hash behavior once `__eq__` is customized.
- Relying on the default `__repr__`, such as
  `<User object at 0x7f8a1c0d5a90>`, which gives no useful debugging
  information compared to a custom one.

### Interview angle
Q: What is the practical difference between `__str__` and `__repr__`?
A: `__str__` is for a readable message meant for an end user, shown by
`print()`. `__repr__` is for an unambiguous, developer-facing
representation, shown in a list, a debugger, or the interactive shell,
and Python falls back to `__repr__` when `__str__` is not defined.
