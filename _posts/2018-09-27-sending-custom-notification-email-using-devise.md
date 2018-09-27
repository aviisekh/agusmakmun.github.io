---
layout: post
title:  "Sending Custom Notification Email Using Devise"
date:   2018-09-27 15:51:10 +0545
categories: [ruby]
---

in directory  ```/lib/core_ext/devise_mailer.rb```

```ruby
Devise::Mailer.class_eval do
  def otp_instructions(record, token, opts={})
    @token = token
    devise_mail(record, :otp_instructions, opts)
  end
end
```


in initializers add

```ruby
Dir[File.join(Rails.root, "lib", "core_ext", "*.rb")].each {|l| require l }
```

In directory  ```app/views/devise/mailer``` add template same the name as the method defined 
i.e. for this case  ```otp_instructions.html.erb```

And in model user.rb or any devise integrated model add the method that sends the custom email
e.g. 

```ruby
  def send_two_factor_authentication_code(code)
    devise_mailer.send(:otp_instructions, self, [code, {}]).deliver_later
  end
```


and now you are all set.

Cheers!




