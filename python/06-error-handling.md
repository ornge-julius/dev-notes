## Error Handling: try, except, else, finally

### When to use it
Use `try`/`except` when code might fail in a way you can anticipate and
recover from, such as a missing file, bad user input, or a failed
network call. Use a custom exception when the built-in exception types do
not describe the specific failure clearly enough.

### Pattern
```python
def load_config(path):
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError:
        print(f"Config file not found: {path}")
        return None
    except PermissionError:
        print(f"No permission to read: {path}")
        return None
    else:
        print("Config loaded successfully")
    finally:
        print("Finished attempting to load config")


class InsufficientFundsError(Exception):
    def __init__(self, balance, amount):
        self.balance = balance
        self.amount = amount
        super().__init__(
            f"Cannot withdraw {amount}, balance is only {balance}"
        )


def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(balance, amount)
    return balance - amount


try:
    withdraw(50, 100)
except InsufficientFundsError as e:
    print(f"Withdrawal failed: {e}")
```

### How it works
Python runs the `try` block first. If a line raises an exception, Python
looks for a matching `except` clause, checked in order, and runs the
first one that matches the exception's type. The `else` block runs only
when the `try` block completed with no exception at all. The `finally`
block always runs, whether an exception happened or not, which makes it
the right place for cleanup that must happen either way, such as closing
a resource. A custom exception is a class that inherits from `Exception`
(or a more specific built-in exception), and calling `super().__init__(...)`
sets the message that appears when the exception is printed or logged.

### Common mistakes
- Writing a bare `except:` with no exception type, which catches every
  possible error, including ones you did not anticipate, and hides real
  bugs.
- Catching a broad exception type like `Exception` when a more specific
  one, like `FileNotFoundError`, would make the failure mode clear and
  avoid accidentally swallowing unrelated errors.
- Putting cleanup code only at the end of the `try` block instead of in
  `finally`, which skips the cleanup whenever an exception is raised
  before reaching that line.

### Interview angle
Q: What is the difference between code placed in `else` after a
`try`/`except`, versus code placed right after the whole
`try`/`except` block?
A: The `else` block only runs if the `try` block raised no exception at
all. Code placed after the whole block runs regardless, once an
exception has already been caught and handled, so `else` is stricter
about confirming full success.
