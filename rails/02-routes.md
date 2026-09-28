## Routes

### When to use it
Use `resources` to generate a full set of RESTful routes for a model in
one line, instead of writing each route by hand.

### Pattern
```ruby
# config/routes.rb
Rails.application.routes.draw do
  resources :posts

  resources :posts do
    resources :comments, only: [:create, :destroy]
  end
end
```

```erb
<%= link_to "View post", post_path(@post) %>
<%= link_to "New comment", new_post_comment_path(@post) %>
```

### How it works
`resources :posts` generates seven routes in one line: `index`, `show`,
`new`, `create`, `edit`, `update`, and `destroy`, each mapped to a
matching action in `PostsController`. Every generated route also gets a
named helper, such as `post_path(@post)` for `/posts/1` or `posts_path`
for `/posts`, which you use instead of writing raw URL strings, so the
links stay correct if the route pattern ever changes. Nesting
`resources :comments` inside `resources :posts` produces URLs like
`/posts/1/comments`, and the helper name picks up both parent and child,
here `post_comments_path(@post)`.

### Common mistakes
- Writing raw URL strings, such as `href="/posts/1"`, instead of route
  helpers, which breaks silently if the route path ever changes.
- Generating all seven RESTful actions with `resources :posts` when the
  controller only needs a few, instead of limiting them with
  `only: [:index, :show]`, which leaves unused routes exposed.
- Nesting resources more than one level deep, which produces long,
  awkward URLs and helper names. Rails guides recommend nesting only one
  level.

### Interview angle
Q: What is the difference between `post_path(@post)` and
`post_url(@post)`?
A: `post_path` returns a relative path, such as `/posts/1`. `post_url`
returns the full address including the protocol and host, such as
`https://example.com/posts/1`, which is needed for things like emails or
redirects to an external domain.
