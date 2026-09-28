## Comprehensions and Generator Expressions

### When to use it
Use a comprehension to build a new list, dict, or set from an existing
iterable in one readable line, instead of writing a `for` loop with
`.append()`. Use a generator expression instead of a list comprehension
when the data is large and you only need to process items one at a time.

### Pattern
```python
numbers = [1, 2, 3, 4, 5, 6]

# List comprehension
squares = [n * n for n in numbers]
# [1, 4, 9, 16, 25, 36]

# With a filter condition
even_squares = [n * n for n in numbers if n % 2 == 0]
# [4, 16, 36]

# Dict comprehension
square_lookup = {n: n * n for n in numbers}
# {1: 1, 2: 4, 3: 9, ...}

# Set comprehension
unique_remainders = {n % 3 for n in numbers}
# {0, 1, 2}

# Generator expression: same syntax as a list comprehension, but with
# parentheses, and it does not build the whole list in memory at once.
sum_of_squares = sum(n * n for n in numbers)

large_range = range(10_000_000)
total = sum(n * n for n in large_range)  # processes one item at a time
```

### How it works
A comprehension runs a loop and an optional filter condition, and
collects every result into a new list, dict, or set immediately, holding
the whole result in memory. A generator expression looks almost
identical, but uses `()` instead of `[]`, and produces items lazily, one
at a time, as something iterates over it, instead of building the entire
sequence up front. This makes a generator expression far more
memory-efficient for large data, at the cost of only being usable once,
since it does not keep the values around after they are consumed.

### Common mistakes
- Building a large list comprehension only to loop over it once and
  discard it, when a generator expression would use far less memory.
- Nesting too many conditions or loops inside one comprehension, which
  becomes harder to read than a plain `for` loop with clear variable
  names.
- Trying to reuse a generator expression a second time, such as calling
  `sum()` on it twice, which returns `0` the second time because the
  generator is already exhausted.

### Interview angle
Q: Why does `sum(squares_gen)` return `0` the second time it is called on
the same generator expression, even though the data has not changed?
A: A generator produces its values once, on demand, and does not store
them. After the first full iteration, the generator is exhausted, so any
later iteration produces no items at all.
