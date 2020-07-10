---
layout: page
title: Singleton classes in Ruby
---

Understainding Singleton classes is extremely important for anyone programming in Ruby. Singleton classes explain how the concept of class/static method works in Ruby. It also explains how you can attach standalone methods to a particular object.

There are certain methods (like `methods`, `singleton_methods`, `instance_methods`, `object_id`) which prove extremely useful in getting the details of the class or understanding what actually happens in the background.

Firstly, how are class methods implemented in Ruby?

class/static methods are nothing but the methods that reside in the singleton class of any instance. For example consider the class below:

```ruby
class User
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
User.singleton_methods ## returns [:is_human?]
```

Whenever you add a static method in the class, it gets attached to a singleton class in ruby

On a side note, there are three ways to add singleton methods. This is why it is generally told that there are multiple ways to do a certain thing in Ruby.

```ruby
 # method 1
class User
  def self.add_singleton_method1
    "added"
  end
end


 # method 2
def User.add_singleton_method2
  "added"
end

 # method 3
class User
  class << self
    def add_singleton_method3
      "added"
    end
  end
end
```
```ruby
User.singleton_methods # returns [:add_singleton_method1, :add_singleton_method2, :add_singleton_method3]
```

You can validate the presence of singleton class of `User` by executing:

```ruby
User.object_id
User.singleton_class.object_id ## both the ids are different
```

Similarly, you can add singleton methods to the instance of the `User` class. And those methods can only be accessed by that particular instance of the class and no other. Singleton class should ideally be shown when you call `ancestors` on any instance, but it doesn't appear as it is anonymous by default and can't be accessed by any method so easily.