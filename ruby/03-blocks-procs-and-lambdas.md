## Blocks, Procs, and Lambdas

### When to use it
Use a block when calling a method that runs some code for each item, or
that needs a chunk of code to run at a specific point, such as
`numbers.each { |n| puts n }`. Reach for a `Proc` or a `lambda` when that
chunk of code needs to be stored in a variable and passed around or
reused.

### Pattern
```ruby
# A block using curly braces, for a short one-liner
[1, 2, 3].each { |n| puts n }

# A block using do...end, for a multi-line body
[1, 2, 3].each do |n|
  doubled = n * 2
  puts doubled
end

# A method that yields to whatever block the caller passes in
def repeat_twice
  yield
  yield
end

repeat_twice { puts "hello" }
# prints "hello" twice

# A Proc: lenient about argument count, and return exits the enclosing method
add = Proc.new { |a, b| a + b }
add.call(2, 3)
# 5

# A lambda: strict about argument count, and return only exits the lambda
multiply = ->(a, b) { a * b }
multiply.call(2, 3)
# 6
multiply.(2, 3)
# 6, alternate call syntax
```

### How it works
A block is not an object. It is code attached directly to a method call,
and the method receives it implicitly, then runs it with `yield`. A
`Proc` turns that same idea into a real object you can store in a
variable, pass as an argument, and call later with `.call`. A `lambda`
is a stricter kind of `Proc`: it raises an error if you call it with the
wrong number of arguments, while a plain `Proc` just ignores extra
arguments or fills missing ones with `nil`. The other key difference is
`return`. Inside a `lambda`, `return` exits only the lambda, like a
normal method. Inside a `Proc`, `return` exits the entire enclosing
method, which can cause a confusing error if that method has already
finished running.

### Common mistakes
- Using `return` inside a `Proc` defined inside a method that has
  already returned by the time the `Proc` runs, which raises a
  `LocalJumpError`.
- Assuming a `Proc` and a `lambda` behave identically. They differ on
  argument-count strictness and on what `return` does.
- Forgetting `yield` inside a method that is supposed to run a block the
  caller passed in, which silently does nothing with that block.

### Interview angle
Q: What is the practical difference between a `Proc` and a `lambda`?
A: A `lambda` checks the number of arguments strictly and only returns
from itself. A `Proc` is lenient about argument count and, if it
contains `return`, exits the method that defined it, not just the
`Proc`.
