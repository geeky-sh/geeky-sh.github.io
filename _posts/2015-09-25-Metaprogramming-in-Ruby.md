---
layout: page
title: Metaprogramming in Ruby
---


One of the amazing things about writing code in Ruby is metaprogramming. Metaprogramming refers to writing code that writes code. Metaprogramming is extremely useful when you want to add custom methods in your class/module which depends on external parameters/variables. A class/module can have different set of methods based on the environment in which the code is executed. You can define methods that can in turn define methods, constants, or variables.

Below are some of the code constructs which are normally used in metaprogramming.

* `define_method`
* `class_eval` and `instance_eval`
* `obj.send`
* `instance_method_get`, `instance_method_set`

Code Examples:

```ruby
class User
  @@methods = [:name, :surname, :email, :phone, :address]

  @@methods.each do |name|
    define_method name do
      "Method called for #{name}"
    end
  end
end


user = User.new
user.name
user.surname
user.email
```

The above example is not a good example to show the depict the usage of `define_method`, but it helps you to understand the power of the function. You can define the method based on the value of the constants or variables defined at that point of time.


```ruby
User = Class.new

User.instance_eval do
  def add_method
    'Added method via instance_eval'
  end
end

User.class_eval do
  def add_method
    'Added method via class_eval'
  end
end
```

```ruby
User.add_method # prints 'Added method via instance_eval'
User.new.add_method # prints 'Added method via class_eval'
```

`class_eval` and `instance_eval` helps to add methods after the class is defined


```ruby
College = Class.new

vars = ['full_name', 'address', 'phone']

college = College.new
vars.each do |n|
  college.instance_variable_set("@#{n}", "variable name is #{n}")
end
vars.each do |n|
  puts college.instance_variable_get("@#{n}")
end

```

This is one of the frequently used methods while programming in Ruby. It allows you to pass symbols of strings to invoke methods.


```ruby
class User
  attr_accessor :name, :email

  def initialize(name, email)
    self.name = name
    self.email = email
  end
end

user = User.new("aash", "aash.discover@gmail.com")

user.send(:name) ## get name

user.send("name=".to_sym, "new name") ## set name

user.send(:name)
```
You can also invoke private methods via `send`

```ruby
User.class_eval do
  private
  def add_private_method
    "private method added"
  end
end

user = User.new("aash", "aash.discover@gmail.com")
user.add_private_method ##throws exception
user.send(:add_private_method) ## works!
```