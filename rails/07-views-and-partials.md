## Views and Partials

### When to use it
Use ERB tags to mix Ruby into HTML. Use a partial any time the same
chunk of markup repeats across more than one view, such as a single post
card shown on both an index page and a search results page.

### Pattern
```erb
<!-- app/views/layouts/application.html.erb -->
<!DOCTYPE html>
<html>
  <head><title>My App</title></head>
  <body>
    <%= yield %>
  </body>
</html>

<!-- app/views/posts/index.html.erb -->
<h1>All Posts</h1>
<% @posts.each do |post| %>
  <%= render partial: "post", locals: { post: post } %>
<% end %>

<!-- app/views/posts/_post.html.erb -->
<div class="post-card">
  <h2><%= post.title %></h2>
  <p><%= post.body %></p>
</div>
```

### How it works
`<%= %>` evaluates Ruby and inserts the result into the HTML.
`<% %>` evaluates Ruby without inserting anything, used for control flow
like `each` and `if`. Every view renders inside the layout at the point
where the layout calls `<%= yield %>`, which is how the same
`<head>` and page shell wrap every page automatically. A partial is a
view file whose name starts with an underscore, such as `_post.html.erb`,
and `render partial: "post", locals: { post: post }` renders it with the
given local variable available inside. Rails also has a shorthand,
`render post`, that infers the partial name and the collection loop for
common cases.

### Common mistakes
- Putting real business logic inside a view, such as calculating a total
  price with several conditionals, instead of moving it to a model
  method or a helper.
- Forgetting the leading underscore on a partial's filename, which
  causes a "missing template" error when `render` looks for it.
- Repeating the same block of markup across several view files instead
  of extracting it into a partial, which means every future change must
  be made in each copy.

### Interview angle
Q: What is the difference between `<% %>` and `<%= %>` in ERB?
A: `<% %>` runs Ruby code without outputting anything, used for loops and
conditionals. `<%= %>` runs Ruby code and inserts its return value
directly into the rendered HTML.
