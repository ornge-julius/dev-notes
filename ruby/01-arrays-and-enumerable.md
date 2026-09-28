## Arrays and Enumerable Methods

### When to use it
Use an array to hold an ordered list of items. Use the Enumerable
methods (`map`, `select`, `reject`, `reduce`, `each`, `sort`) to
transform, filter, or total that list without writing a manual loop.
This is the Ruby side of the same idea as the JavaScript
`map`/`filter`/`reduce` file.

### Pattern
```ruby
numbers = [1, 2, 3, 4, 5]

doubled = numbers.map { |n| n * 2 }
# [2, 4, 6, 8, 10]

evens = numbers.select { |n| n.even? }
# [2, 4]

odds = numbers.reject { |n| n.even? }
# [1, 3, 5]

total = numbers.reduce(0) { |sum, n| sum + n }
# 15
# same thing, using inject and a symbol shortcut
total = numbers.inject(:+)
# 15

numbers.each { |n| puts n }

sorted_desc = numbers.sort { |a, b| b <=> a }
# [5, 4, 3, 2, 1]
```

### How it works
`map` runs the block once for every item and collects the return values
into a new array of the same length. `select` keeps only the items where
the block returns a truthy value, and `reject` keeps the opposite. `each`
also runs the block once per item, but it always returns the original
array, not the block's return values, so it is meant for side effects
like `puts`, not for building a new list. `reduce` (also called `inject`)
carries an accumulator through every item, starting from the value passed
in, and returns that accumulator's final value. `sort` accepts a block
that returns `-1`, `0`, or `1` (through the `<=>` spaceship operator) to
decide the order, or sorts with the default order when no block is
given.

### Common mistakes
- Using `each` when the goal is to build a new array, then wondering why
  the result is the original array instead of the transformed one. Use
  `map` for that.
- Forgetting the starting value for `reduce`. Without it, Ruby uses the
  first item as the starting accumulator and begins the block from the
  second item, which breaks the calculation on an array of non-numeric
  objects.
- Mutating the item in place inside a `map` block, such as
  `user.active = true; user`, instead of returning a new object. This
  changes the original array's objects as a side effect.

### Interview angle
Q: What is the difference between `each` and `map` in Ruby?
A: `each` runs the block for every item and returns the original,
unchanged array. `map` runs the block for every item and returns a new
array made from the block's return values.
