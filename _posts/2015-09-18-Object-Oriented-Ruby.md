---
layout: post
title: Ruby - A Pure Object-Oriented Language
categories: Technical
---

I will start with defining classes in ruby, which can be done in two ways:

```ruby
class User1
  attr_accessor :name, :email
  
  def initialize(name, email)
    self.name = name
    self.email = email
  end
  
  def details
    "#{name} - #{email}"
  end
  
  def self.is_human?
    true
  end
end

User2 = Class.new do
  attr_accessor :name, :email
  
  def initialize(name, email)
    self.name = name
    self.email = email
  end
  
  def details
    "#{name} - #{email}"
  end
  
  def self.is_human?
    true
  end
end
```

```ruby
  User1.new("aash", "aash.discover@gmail.com").methods.include?(:details)
  User1.singleton_methods.include?(:is_human?)
  
  User2.new("aash", "aash.discover@gmail.com").methods.include?(:details)
  User2.singleton_methods.include?(:is_human?)
```

The primary intention to write the above code is to depict that both `User1` and `User2` are essentially the same. If one looks closely at how `User2` is instantiated, one will see that `User2` is an instance of the class `Class`. This explains the basic crux of ruby conceptual modal that says that **everything is an object** - even the class. This can be further validated by calling the method `class` on every object that you intend to look at. For example:

```ruby
User1.class ## returns Class

User1.new("aash", "aash.discover@gmail.com").class ## returns User
User1.new("aash", "aash.discover@gmail.com").class.class ## returns Class
```

Now, every class which is an instance of `Class` has an ancestral chain which contains the list of classes it will look into when the method lookup is performed. 

```ruby
User1.class ## returns Class (instance of Class)
User1.ancestors # returns [User1, Object, Kernel, BasicObject]
 
Class.class ## returns Class (instance of Class)
Class.ancestors # return [Class, Module, Object, Kernel, BasicObject]

Module.class ## returns Class (instance of Class)
Module.ancestors # return [Module, Object, Kernel, BasicObject]

Object.class ## returns Class (instance of class) - (surprise!)
Object.ancestors # return [Object, Kernel, BasicObject]
```

Surprisingly, none of the ancestors contain `Class` in the list. It is because, `Class` is primarily used to create object instances. It is not found anywhere in the lookup chain. 

Below is the image that explains the ruby method chaining via a diagram:
![Ruby Object Model](/images/ruby_object_model.png)

(source - https://thesocietea.org/2015/08/metaprogramming-in-ruby-part-1/)

In my [next post](2015-09-18-Singleton-Classes-Ruby), I intend to explain singleton classes in Ruby.