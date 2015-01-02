---
layout: post
title: "ActiveRecord and Association Review"
date: 2014-12-07 18:42:23 -0500
comments: true
categories: [ActiveRecord, Association, Migration]
---
This is a review on what I have been through in Week 6 of TeaLeaf Academy. 

Rails follow Active Record pattern to perform ORM duties. What's the *Active Record pattern*?

- classes to tables
- objects to rows of data within that table
- getters/setters(attributes) to columns in that table

We can create, retrieve, update, and delete the object instances by altering the database table.

To create a table in the database, we can use migrations, `rails generate migration create_posts`. In the migration file, 
``` ruby
class CreatePosts < ActiveRecord::Migration
  def change
    create_table :posts do |t|
      t.string :url
      t.string :title
      t.text :description

      t.timestamps
    end
  end
end
```

After we run `rake db:migrate` in the command line, it will create six columns in the database,

1. primary key: id
2. url
3. title
4. description
5. created_at
6. updated_at

Then in the app/models folder, we will create the corresponding model,
``` ruby
class Post < ActiveRecord::Base
end
```
The file name will be in singular form, `post.rb`.

*Tips on table name: use the tableize method on the class name. For example, `'Post'.tableize` => "posts".*

If we have a Comment model and there is a 1:M association between Post and Comment, a `foreign_key` column - `post_id` need to be added to the comments table and it will point to the primary key column in the posts table. Then in the Post and Comment model file, we will do the following changes. 
``` ruby
class Post < ActiveRecord::Base
  has_many :comments
end

class Comment < ActiveRecord::Base 
  belongs_to :post
end
```

If there is a Category model and it has a M:M association with Post, a joint model and table will be needed to build up the association. The model file name will be `post_category.rb` and class name will be `PostCategory`. To get the table name, `'PostCategory'.tableize` and the output is 'post_categories'. There will be two foreign key columns in the table, `post_id` and `category_id`. In the model file, we will set up the `has_many :through`.

``` ruby
class Category < ActiveRecord::Base
  has_many :post_categories
  has_many :posts, through: :post_categories
end

class Post < ActiveRecord::Base
  has_many :comments
  has_many :post_categories
  has_many :categories, through: :post_categories
end
```
To change the default association name, 

``` ruby
class Post < ActiveRecord::Base
  belongs_to :creator, foreign_key: 'user_id', class_name: 'User'
end
```
This will make `post.creator` work. 

Keyword `resources` can be used to generate routes. 
`resources :posts` will make the following routes available,

``` ruby
   Prefix | Verb  | URI Pattern               | Controller#Action
   -------|-------|---------------------------|------------------
    posts | GET   | /posts(.:format)          | posts#index
          | POST  | /posts(.:format)          | posts#create
 new_post | GET   | /posts/new(.:format)      | posts#new
edit_post | GET   | /posts/:id/edit(.:format) | posts#edit
     post | GET   | /posts/:id(.:format)      | posts#show
          | PATCH | /posts/:id(.:format)      | posts#update
          | PUT   | /posts/:id(.:format)      | posts#update
```
To create nested routes like `/posts/:id/comments/:id`, we can use nested resources,

``` ruby
resources :posts, except: [:destroy] do 
  resources :comments, only: [:create]
end
```
`except` and `only` keywords can be used to add constrains on the routes. 