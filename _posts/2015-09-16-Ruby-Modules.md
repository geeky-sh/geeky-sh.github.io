---
layout: post
title: Ruby - Modules
categories: Technical
---

When one starts using Ruby, one frequently hears about the power of modules. Modules are the something through which you can group together chunks of code which you feel is logically different and is prone to be used at multiple places. One can either *include* or *extend* a module in a class. By including the module all the methods get added as instance methods for that class, while extending the module adds all the methods as class methods. For example:

```ruby
module Vehicle
  def has_wheels?
  	true
  end
  
  def has_gear?
  	true
  end
end

class Car
  include Vehicle
end

class NewCar
  extend Vehicle
end
```

```ruby
Car.new.has_wheels? ## methods of the modules are added in the instance of the class
NewCar.has_wheels? ## methods are added as static methods
```

There is something called ancestral chain which is looked whenever ruby tries to locate a method in a class or a module. This chain contains the list of referencees to the class or the module. For example if one wants to look into the method chain of the above classes, one can do this:

```ruby
Car.ancestors # [Car, Vehicle, Object, Kernel, BasicObject]
NewCar.ancestors # [NewCar, Object, Kernel, BasicObject] 
```

As we can see, *Vehicle* is added in the method chain of the class *Car*. This also means that whenever you include a module in a class, it is just added in the ancestral chain of class contrary to the idea that every method is copied in that class.

One can also *include* or *extend* a module in a Module. 

###Adding both class and instance methods via Module

But what if one wants to add both instance methods and class methods via a module? The first way that comes to mind is this:

```ruby
module MixMe
  def instance_included?
  	true
  end
  
  def self.singleton_included?
  	true
  end
end

class TestMe
  include MixMe
end
```

```ruby
TestMe.new.instance_included?
TestMe.singleton_included? ## raises exception
```
One can't include class method using *self* keyword via a module as *self* refers to the module and not the class.

For this, the following can be done. 

Whenever a module is included in a class, an *included* function of the module is called with the class reference passed as the parameters. One can add instance and class methods through this function. Here is an example:

```ruby
module MixMe
  def self.included(base)
  	base.send :include, InstanceMethods
	  base.send :extend, ClassMethods
  end
  
  module InstanceMethods
  	def instance_included?
	    'yes'
	  end
  end
  
  module ClassMethods
  	def singleton_included?
	    'yes'
	  end
  end
end

class TestMe
  include MixMe
end
``` 

```ruby
TestMe.new.instance_included?
TestMe.singleton_included?
```

This is what `ActiveSupport::Concern` - a module that comes by default in rails provides you with. [Here](http://www.fakingfantastic.com/2010/09/20/concerning-yourself-with-active-support-concern/) is an extremely nice article that explains how `ActiveSupport::Concern` works.