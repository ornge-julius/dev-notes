## Query Methods and the N+1 Problem

### When to use it
Use `where`, `find_by`, and scopes to build database queries in Ruby
instead of raw SQL. Watch for the N+1 problem any time you loop over a
collection and access an association inside that loop.

### Pattern
```ruby
class Post < ApplicationRecord
  belongs_to :author
  scope :published, -> { where(published: true) }
end

Post.where(published: true)
Post.find_by(title: "Hello World")
Post.published.order(created_at: :desc).limit(10)

# N+1 problem: one query for the posts, then one extra query
# per post to load its author.
Post.all.each do |post|
  puts post.author.name
end

# Fixed with includes: one query for the posts, one query that
# loads every needed author at once.
Post.includes(:author).each do |post|
  puts post.author.name
end
```

### How it works
`where` builds a SQL `WHERE` clause and returns a lazy, chainable
relation, so `Post.published.order(...)` combines into one query only
when the result is actually used. `find_by` is a shortcut for `where`
that returns the first match, or `nil`, instead of a relation. A scope
wraps a common query into a named method you can chain like any other.
The N+1 problem happens because `post.author` triggers its own query
every time it runs inside the loop, one query per post, on top of the
one query that loaded the posts. `includes(:author)` tells Rails to load
every post's author ahead of time in one extra query, so the loop reuses
already-loaded data instead of querying again for each post.

### Common mistakes
- Looping over a collection and reading an association inside the loop
  without `includes`, which silently creates one extra query per row and
  slows down as the data grows.
- Calling `.where(...).to_a.select { ... }` to filter in Ruby after
  loading every row, when a second `.where` clause could filter in the
  database and load far less data.
- Using `includes` for an association that is never actually accessed in
  the loop, which loads data the request does not need.

### Interview angle
Q: How would you find and fix an N+1 query problem in a Rails app?
A: Tools like the Bullet gem, or watching the server log for repeated
near-identical `SELECT` queries, reveal the pattern. The fix is adding
`includes(:association_name)` to the original query so Rails loads the
association in one batch instead of once per row.
