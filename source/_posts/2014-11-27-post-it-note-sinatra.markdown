---
layout: post
title: "Post-it Note: Sinatra"
date: 2014-11-27 21:13:31 -0500
comments: true
categories: 
---
As mentioned in my previous blog, I just built up a simple online Blackjack game based on Sinatra. Here is the [link](https://blackjack-sinatra-jw.herokuapp.com) and you can play with it. 
Sinatra is a [DSL](http://en.wikipedia.org/wiki/Domain-specific_language) to build up web applications quickly in Ruby with minimal efforts. It's such a great tool that I want to write a post-it note.

Include the gem `require 'sinatra'` and then `gem install sinatra`

**Routes**
```ruby
get '/' do
  ...code...
end

post '/' do
  ...code...
end
```

**Render template**
```ruby
erb :index
# Disable default layout
erb :index, layout: false
```

**Helpers**
```ruby
helpers do
  ...code...
end
```

**Before filter**
```ruby
before do
  ...code...
end
```

**Halt the request**
```
halt erb(:index)
```

For more information, check out its [official intro](http://www.sinatrarb.com/intro.html).