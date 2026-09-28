## Symbols and Strings

### When to use it
Use a string when the text itself is the data, such as user input or a
message to display. Use a symbol when the value is really a fixed name
or label, such as a hash key, a status value, or a method name.

### Pattern
```ruby
name = "Ada"
greeting = "Hello, #{name}!"
# "Hello, Ada!", string interpolation

status = :active
# a symbol, written with a leading colon

user = { name: "Ada", status: :active }
# symbol keys and a symbol value

"Ada".upcase
# "ADA"
"  Ada  ".strip
# "Ada"
"Ada,Alan,Grace".split(",")
# ["Ada", "Alan", "Grace"]
["Ada", "Alan"].join(", ")
# "Ada, Alan"

:active == :active
# true, and always the same object in memory
"active".equal?("active")
# false, two separate String objects even though the text matches
```

### How it works
A string is mutable and creates a new object in memory every time you
write the same text again. A symbol is immutable, and Ruby only ever
creates one copy of a given symbol, such as `:active`, no matter how many
times it appears in the code. This makes symbols cheaper to compare,
since Ruby can check if two symbols are equal by checking if they are the
exact same object, instead of comparing characters. That is why symbols
are the standard choice for hash keys and for identifying fixed states
like `:active` or `:pending`, while strings are the right choice for
actual text content that a user might read, type, or edit.

### Common mistakes
- Treating a symbol as if it were a string, such as trying to call
  `.upcase` and expecting it to behave the same, or trying to use string
  methods that mutate the value, since symbols are immutable.
- Using a string as a status flag, such as `status == "active"`, then
  introducing a typo like `"Active"` that silently fails to match.
- Converting between symbols and strings unnecessarily, such as calling
  `.to_s` and back `.to_sym` repeatedly instead of picking one and
  staying consistent.

### Interview angle
Q: Why do Ruby developers prefer symbols over strings for hash keys?
A: Symbols are immutable and Ruby reuses the same object for every
occurrence of a given symbol, which makes symbol comparison and hash
lookups faster than comparing string contents character by character.
