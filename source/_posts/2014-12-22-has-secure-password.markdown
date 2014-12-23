---
layout: post
title: "has_secure_password"
date: 2014-12-22 21:33:45 -0500
comments: true
categories: Password
---
**How do we save password to the database in rails?**

First, add `has_secure_password` method to the `User` model which will allow us to save the password through "one-way hash", meaning the hash of the password strings will each turn into long, undecipherable tockens.

Second, add a new column `password_digest` to the `users` table to save the password token. The password string should never be stored in the application and they can only be digested. If you register at a site and they send you your password in plain text, do not trust the site. 

Then make sure gem `bcrypt-ruby` installed. 

After the three steps, virtual attributes `password` and `password_confirmation` and `authenticate()` method can be used in our app. 

When `user.authenticate(password)` is true, it will return the `user` object. Otherwise, it will return `false`. 