## Decorators

### When to use it
Use a decorator to add behavior around a function or method, such as
logging, timing, or access checks, without changing the function's own
code. Use the built-in decorators `@property`, `@staticmethod`, and
`@classmethod` to control how a method behaves on a class.

### Pattern
```python
import time
from functools import wraps

def log_time(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper


@log_time
def slow_add(a, b):
    time.sleep(0.1)
    return a + b

slow_add(2, 3)  # prints timing, then returns 5


class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14159 * self.radius ** 2

    @staticmethod
    def unit_circle():
        return Circle(1)

    @classmethod
    def from_diameter(cls, diameter):
        return cls(diameter / 2)


c = Circle(2)
print(c.area)                  # 12.566..., called like an attribute
Circle.unit_circle()           # no access to self or cls
Circle.from_diameter(10)       # cls is Circle, returns Circle(5.0)
```

### How it works
`@log_time` above `slow_add` is shorthand for
`slow_add = log_time(slow_add)`. `log_time` receives the original
function, defines an inner `wrapper` function that runs code before and
after calling it, and returns `wrapper` in place of the original.
`@wraps(func)` copies the original function's name and docstring onto
`wrapper`, so tools and error messages still show the real function name
instead of `wrapper`. `@property` turns a method into something you read
without parentheses, like a plain attribute. `@staticmethod` marks a
method that does not need access to the instance or the class, and takes
no automatic first argument. `@classmethod` marks a method that receives
the class itself as `cls` instead of an instance, which is why
`from_diameter` can build and return a new instance of whatever class it
is called on.

### Common mistakes
- Writing a decorator without `*args, **kwargs` in the inner wrapper,
  which breaks any decorated function that takes arguments.
- Forgetting `@wraps(func)`, which makes every decorated function report
  its name as `wrapper` in tracebacks and introspection tools.
- Using `@staticmethod` when the method actually needs to read or set
  data on the class, which should instead be a `@classmethod`.

### Interview angle
Q: What does `@wraps(func)` actually fix if the decorator works
correctly without it?
A: Without `@wraps`, the decorated function's `__name__` and docstring
become the wrapper's, not the original function's, which breaks
debugging, documentation tools, and anything that inspects the function
by name.
