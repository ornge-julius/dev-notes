## Hashes

### When to use it
Use a hash to hold key-value pairs, such as a record with named fields
or a lookup table keyed by id. Ruby hashes are the equivalent of a
JavaScript object or a Python dict.

### Pattern
```ruby
user = { name: "Ada", role: "admin" }
# symbol keys, the common style in modern Ruby

legacy_user = { "name" => "Ada", "role" => "admin" }
# string keys, seen in older code and in JSON-parsed data

user[:name]
# "Ada"

user[:role] = "member"
# updates the value for an existing key

user[:age] ||= 30
# sets a value only if the key is missing or nil

user.fetch(:email, "no email on file")
# returns the fallback string instead of raising, since :email is missing

user.each do |key, value|
  puts "#{key}: #{value}"
end

merged = user.merge(role: "owner")
# { name: "Ada", role: "owner", age: 30 }
```

### How it works
A symbol key like `:name` is a lightweight, immutable identifier, and
Ruby reuses the same symbol object everywhere it appears, which makes
symbol keys faster to compare than string keys. Reading a missing key
with `[]` returns `nil` instead of raising an error, which is why
`fetch` is safer when a missing key should be treated as a real problem
or needs an explicit fallback. `||=` is shorthand for "assign only if the
current value is `nil` or `false`," which is a common way to set a
default. `merge` returns a brand new hash with the keys from the
argument overriding matching keys in the original, and it does not
change the original hash.

### Common mistakes
- Mixing symbol keys and string keys for the same conceptual field, such
  as `user[:name]` in one place and `user["name"]` in another, which
  silently reads `nil` because they are different keys.
- Using `[]` to read a key that might not exist and treating the `nil`
  result as if the lookup succeeded.
- Assuming `merge` changes the hash in place. It returns a new hash; use
  `merge!` if the goal is to mutate the original.

### Interview angle
Q: Why does `user["name"]` return `nil` even after
`user = { name: "Ada" }` was set?
A: `{ name: "Ada" }` creates a hash with the symbol key `:name`, not the
string key `"name"`. Symbols and strings are different objects in Ruby,
so `user["name"]` looks up a key that was never set.
