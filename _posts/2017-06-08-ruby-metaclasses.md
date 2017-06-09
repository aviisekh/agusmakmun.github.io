---
layout: post
title:  "Ruby Metaclasses"
date:   2017-06-08 12:34:10 +0545
categories: [ruby, metaprogramming]
---

* Ruby, everything is a object. Even a class is an object of Class

* metaclass of a Class as a object

* metaclass of an instance of a Class as an object both different,

* getting metaclass of an instance

 [Blog followed](http://yehudakatz.com/2009/11/15/metaprogramming-in-ruby-its-all-about-the-self/)


**Example:**

```ruby
class Person  
 
end

a = Person.new
def a.height 
 "10"
end 

class<<a #metaclass of object a
  def p_name
    "Prakash"
  end
end

class<<Person #metaclass of object Person
  def p_name
    "Homo Sapiens" 
  end
end


puts a.p_name #=> Prakash
puts Person.p_name #=> Homo Sapiens
```

**Remarks**

```ruby
class Person
end

class<<Person
  def p_name
    "Homo Sapiens"
  end
end

#Same as
class Person
  class<<self
    def p_name
      "Homo Sapiens"
    end
  end 
end
```

