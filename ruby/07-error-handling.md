## Error Handling

### When to use it
Use `begin`/`rescue` when code might raise an error you can recover
from, such as a failed network call or invalid input. Use a custom
exception class when the built-in error types do not describe the
problem clearly enough for the rest of the app to handle it correctly.

### Pattern
```ruby
def parse_age(input)
  begin
    Integer(input)
  rescue ArgumentError
    raise "Invalid age: #{input}"
  end
end

# A more complete begin/rescue/ensure block
def load_config(path)
  file = File.open(path)
  file.read
rescue Errno::ENOENT
  puts "Config file not found, using defaults"
  "{}"
ensure
  file&.close
end

# A custom exception class
class InsufficientFundsError < StandardError
  def initialize(balance)
    super("Balance too low: #{balance}")
  end
end

def withdraw(balance, amount)
  raise InsufficientFundsError, balance if amount > balance
  balance - amount
end

begin
  withdraw(50, 100)
rescue InsufficientFundsError => e
  puts e.message
end
```

### How it works
`rescue` catches an error matching the given class (or `StandardError` if
none is given) and runs its block instead of letting the error crash the
program. `ensure` runs no matter what, whether the code succeeded, raised
an error, or the error was rescued, which is why it is the right place
to close a file or a connection. A custom exception subclasses
`StandardError`, not the base `Exception` class, since `StandardError` is
the branch of the error hierarchy meant for problems an application
should normally handle. `raise` can create and throw that custom error in
one line, passing extra data (like `balance` above) into its
`initialize` method.

### Common mistakes
- Rescuing the base `Exception` class instead of `StandardError`, which
  also catches serious system-level errors, such as
  `SystemExit`, that the program should not silently swallow.
- Writing a broad `rescue` with no class at all around a large block of
  code, which hides the real error and makes debugging much harder.
- Forgetting `ensure` for a resource like a file or a database
  connection, which can leak that resource if an error is raised before
  it gets closed.

### Interview angle
Q: Why should a custom exception subclass `StandardError` instead of
`Exception`?
A: `Exception` also covers critical, low-level errors that a normal
`rescue` should not catch. `StandardError` is the branch meant for
recoverable application errors, and rescuing it does not accidentally
also catch things like a program exit signal.
