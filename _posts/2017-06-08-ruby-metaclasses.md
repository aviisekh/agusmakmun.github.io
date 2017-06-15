---
layout: post
title:  "Ruby Metaclasses"
date:   2017-06-08 12:34:10 +0545
categories: [ruby, metaprogramming]
---

Before diving into metaclasses, let us understand the very basic concept of Ruby programming language. Ruby is a dynamic, object-oriented programming language. Even all the classes in ruby  are the instance of ```Class```. See the following code snippet.
```ruby
class Player
 def self.get_class
  self.class
 end
end
puts Player.get_class #=> Class
```
We see that class ```Player``` is an instance of class **Class**. So if we define any method in the **Class** the method can be accessed by its instance at any time. See the example below.
```ruby
class Class 
  def team_name
    "Arsenal"
  end
end
puts Player.team_name #=> Arsenal
```
But it is really a bad idea to define the object specific methods in the class of it since the method is passed to all of its instances. Consider the following example.
```ruby
class Fruit
end
puts Fruit.team_name #=> Arsenal
```

So the role of  **metaclass** come in existence. In Ruby each object is also an instance of its metaclass, a Class that can have methods, but is only attached to the object itself.


### Metaclass

Metaclass also known as **eigenclass** or **singleton class** is the class that stores the singleton methods of an instance where singleton methods are the methods that are specific to the object. When you declare a singleton method on an object, Ruby automatically creates a metaclass to hold just that method. All subsequent singleton methods of this object goes to its metaclass. Whenever you send a message to the object, it first looks to see whether the method exists in its metaclass. If it is not there, it gets propagated to the actual class of the object and if it is not found there, the message traverses the inheritance hierarchy.

Let us look at an example:    
```ruby
class Player end
messi = Player.new
tiger_woods = Player.new
def messi.team_name 
  "FC Barcelona"
end

puts messi.team_name #=> FC Barcelona
puts tiger_woods.team_name #=> undefined method `team_name'

puts messi.class #=> Player 
puts messi.singleton_class #=> #<Class:#<Player:0x0055fbe5352be0>>

puts messi.singleton_class.instance_methods.grep(/team_name/)  #=> team_name
puts tiger_woods.singleton_class.instance_methods.grep(/team_name/) #=> 
```

In the above example, the two instances of ```Player```; ```messi``` and ```tiger_woods``` are created and singleton method ```team_name``` is created for ```messi``` instance at runtime. This space(class) where the method ```team_name``` for ```messi``` is defined dynamically is known as the **metaclass** of ```messi```. We can see that singleton class of messi contains ```team_name``` method whild that of tiger_woods doesn't.

There is a neat trick to define the metaclass of an object. The above program can be rewritten in the following way

```ruby
class Player end
messi = Player.new
tiger_woods = Player.new

class<<messi
  def team_name
    "FC Barcelone"
  end
end

puts messi.team_name #=> FC Barcelona
puts tiger_woods.team_name #=> undefined method `team_name'
```


### Examples

#### Example 1:
The example below shows the variations in the implementation of metaclass. 

```ruby
class Person end
class<<Person #metaclass of a class
  def family
    "I am Homo Sapiens"
  end
end
puts Person.family #=> I am Homo Sapiens

#is same as
class Person
  class<<self
    def family
      "I am also Homo Sapiens" 
    end
  end 
end
puts Person.family #=> I am also Homo Sapiens

#is same as
def Person.family
  "I am also the same" 
end
puts Person.family #=> I am also the same

p1 = Person.new #metaclass of an instance 
class<<p1
  def family
    "I am Poudels" 
  end
end
puts p1.family #=> I am Poudels
```

#### Example 2:

Suppose you have two string objects ```a``` and ```b```. ```a``` may refer to the currency while ```b``` may not. So some money methods can be implemented in string ```a```. Follow the example. 

```ruby
a= "$100"
b= "1000000"

def a.in_dollars 
   "$"+(self.gsub("$","").to_f).to_s
end

def a.in_pounds
  "£"+(0.78*self.gsub("$","").to_f).to_s
end

def a.in_nepalese_rupee
  "NRs " + (102.85*self.gsub("$","").to_f).to_s
end

puts a.in_dollars #=> $100.0
puts a.in_pounds #=> £78.0
puts a.in_nepalese_rupee #=> NRs 10285.0
puts b.in_nepalese_rupee #=> undefined method 'in_nepalese_rupee'
```

The above example can be implemented  in the following way as well.

```ruby
a= "$100"
b= "1000000"

class<<a
  def in_dollars 
     "$"+(self.gsub("$","").to_f).to_s
  end

  def in_pounds
    "£"+(0.78*self.gsub("$","").to_f).to_s
  end

  def in_nepalese_rupee
    "NRs " + (102.85*self.gsub("$","").to_f).to_s
  end
end

puts a.in_dollars #=> $100.0
puts a.in_pounds #=> £78.0
puts a.in_nepalese_rupee #=> NRs 10285.0
puts b.in_nepalese_rupee #=> undefined method 'in_nepalese_rupee'

```

The above example only defines the method for only one string object i.e. object ```a```. But we may need to define same methods for other money strings instances. We can generalize defining the metaclass for the object in the following way. 

```ruby
a= "$100"
b= "1000000"

class String 
  def moneytize_me
    class<<self
      def in_dollars 
        "$"+(self.gsub("$","").to_f).to_s
      end

      def in_pounds
        "£"+(0.78*self.gsub("$","").to_f).to_s
      end

      def in_nepalese_rupee
        "NRs " + (102.85*self.gsub("$","").to_f).to_s
      end
    end
  end 
end

a.moneytize_me
puts a.in_dollars #=> $100.0
puts a.in_pounds #=> £78.0
puts a.in_nepalese_rupee #=> NRs 10285.0
puts b.in_nepalese_rupee #=> undefined method 'in_nepalese_rupee'

b.moneytize_me
puts b.in_nepalese_rupee #=> NRs 102850000.0
```

Since ```moneytize_me```  is an instance method, it can be called only by the instance of the String. So whenever ```moneytize_me``` method is invoked by the string instance, it gets the methods that perform the moneterial actions.


