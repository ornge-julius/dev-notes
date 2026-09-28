## Context Managers: the with Statement

### When to use it
Use `with` any time you open a resource, such as a file, a database
connection, or a lock, that must be closed or released afterward, even if
an error happens in between.

### Pattern
```python
# The common case: reading a file
with open("data.txt") as f:
    contents = f.read()
# f is automatically closed here, even if read() raised an error

# Writing a custom context manager with a class
class Timer:
    def __enter__(self):
        import time
        self.start = time.time()
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        import time
        elapsed = time.time() - self.start
        print(f"Elapsed: {elapsed:.4f}s")
        return False  # do not suppress exceptions

with Timer():
    total = sum(range(1_000_000))


# The same idea, written more simply with contextlib
from contextlib import contextmanager

@contextmanager
def timer():
    import time
    start = time.time()
    yield
    elapsed = time.time() - start
    print(f"Elapsed: {elapsed:.4f}s")

with timer():
    total = sum(range(1_000_000))
```

### How it works
`with open("data.txt") as f` calls `f.__enter__()` at the start of the
block and guarantees `f.__exit__()` runs at the end, whether the block
finished normally or raised an exception. This replaces a manual
`try`/`finally` around `f.close()`. A class-based context manager
implements `__enter__`, which returns the value assigned to `as`, and
`__exit__`, which receives details about any exception that occurred and
returns `True` to suppress it or `False` to let it propagate.
`@contextmanager` from `contextlib` builds the same behavior from a
single generator function: code before `yield` runs as `__enter__`, and
code after `yield` runs as `__exit__`, which is shorter to write for
simple cases.

### Common mistakes
- Opening a file or connection without `with`, and forgetting to call
  `.close()` manually, which leaks the resource if an exception happens
  before the close line runs.
- Returning `True` from `__exit__` by accident, which silently swallows
  every exception raised inside the `with` block instead of letting it
  surface.
- Putting code that can raise an exception after the `yield` in a
  `@contextmanager` function without a `try`/`finally` around it, which
  skips the cleanup code if the block above raises.

### Interview angle
Q: What does returning `True` from a context manager's `__exit__` method
actually do?
A: It tells Python to suppress the exception that occurred inside the
`with` block, so the exception does not propagate further up the call
stack, as if it never happened.
