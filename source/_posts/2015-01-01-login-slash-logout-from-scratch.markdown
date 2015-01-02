---
layout: post
title: "Login/Logout from scratch"
date: 2015-01-01 23:03:21 -0500
comments: true
categories: 
---

In general, we can use authentication gems like [Devise](https://github.com/plataformatec/devise) and [OmniAuth](https://github.com/intridea/omniauth) to build up the login and logout process. But here, we will start from scratch and implement it in our [postit](https://postit-jw.herokuapp.com/). 

First we will add `has_secure_password` to the `user` model. In the `user.rb` file, 

``` ruby
has_secure_password validates: false
validates :password, on: :create, length: {minimum: 5}
```

The above code adds virtual attribute `password` and its validations only happen when the user is created. 

Then `password_digest` column should be added to `users` table to save the digested password string from the user input. 

User new form will be modified to add the password field. 

``` ruby
<div class="content-group">
  <%= f.label :password %>
  <%= f.password_field :password %>
</div>
```

To change the route `/users/new` to `/register`, 

``` ruby
get '/register', to: 'users#new'
```

Now we are ready to add the login/logout route, non-model backed form and sessions controller. 

In the `route.rb`, we will add the following routes,

``` ruby
get '/login', to: 'sessions#new'
post '/login', to: 'sessions#create'
get 'logout', to: 'sessions#destroy'
```

Then we will add the `sessions_controller.rb`,

``` ruby
class SessionsController < ApplicationController
  def new
  end

  def create
    user = User.find_by(username: params[username])
    if user && user.authenticate(params[:password])
      session[:user_id] = user.id
      flash[:notice] = "You've logged in!"
      redirect_to root_path
    else
      flash[:error] = "There were something wrong with your username or password."
      render :new
    end
  end

  def destroy
    session[:user_id] = nil
    flash[:notice] = "You've logged out!"
    redirect_to root_path
  end 
end
```

Following the actions in `sessions#controller`, we will need `new.html.erb`. 

``` ruby
<%= render "shared/content_title", title: "Log in" %>

<div class="well">
  <%= form_tag "/login" do %>
    <div class="content-group">
      <%= label_tag :username %>
      <%= text_field_tag :username, params[:username] || "" %>
    </div>
    <div class="content-group">
      <%= label_tag :password %>
      <%= password_field_tag :password, params[:password] || "" %>
    </div>

    <%= submit_tag "Submit", class: "btn btn-primary" %>
  <% end %>
</div>
```

Now the login and logout structure is ready to use and in order to make the life easier to modify the view templates, we need several helper methods. 

``` ruby
def current_user
  @current_user ||= User.find_by(sessions[:user_id]) if session[:user_id]
end

def logged_in?
  !!current_user
end

def require_user
  unless logged_in?
    flash[:error] = "This action is not allowed."
    redirect_to root_path
  end
end
```

Voilà! Next step will be to customize the view templates to logged_in user and not. 