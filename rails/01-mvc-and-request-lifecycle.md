## MVC and the Request Lifecycle

### When to use it
Use this mental model any time you trace a bug or plan a new feature in a
Rails app. Every request passes through the same fixed path, from the
router to the final HTML response.

### Pattern
```ruby
# config/routes.rb
Rails.application.routes.draw do
  resources :posts, only: [:show]
end

# app/controllers/posts_controller.rb
class PostsController < ApplicationController
  def show
    @post = Post.find(params[:id])
  end
end
```

```erb
<!-- app/views/posts/show.html.erb -->
<h1><%= @post.title %></h1>
<p><%= @post.body %></p>
```

### How it works
A browser requests a URL, such as `/posts/1`. The router matches that URL
against the routes in `config/routes.rb` and picks a controller and
action, here `PostsController#show`. The controller action runs Ruby
code, usually asking a model to load data from the database, and stores
the result in an instance variable, here `@post`. Rails then looks for a
view file matching the action name, `show.html.erb`, and renders it. Any
instance variable set in the controller action is available inside that
view. The finished HTML goes back to the browser as the response.

### Common mistakes
- Putting business logic directly in the view instead of the controller
  or model, which makes that logic hard to test and hard to reuse.
- Assuming a controller action always renders a view of the same name.
  You can call `render` or `redirect_to` explicitly to send a different
  response.
- Forgetting that an instance variable not set in the controller action
  is simply `nil` in the view, not a missing-variable error.

### Interview angle
Q: What are the three main pieces in Rails' MVC pattern, and what does
each one own?
A: The model owns data and business rules, usually backed by the
database. The view owns how that data displays as HTML. The controller
connects the two: it reads the request, asks the model for data, and
picks which view to render.
