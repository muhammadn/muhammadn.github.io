---
layout: default
title:  "Fat Models, Skinny Controller vs Separation of concerns, Part 2"
date:   2017-03-20 16:36:00 +0800
---
In this part 2 of Fat Models and Skinny Controller vs Separation of Concern, i am going to focus more on getting code from "fat models" to concern.

For example, for omniauth-facebook with login gem Devise.

In a "fat model" configuration, the business logic code sits in the model, like below, naming the method by adding `self.` to `from_omniauth`.

```
class User < ApplicationRecord

    def self.from_omniauth(auth)
     if user = find_by_email(auth.info.email)  # search your db for a user with email coming from fb
       return user  #returns the user so you can sign him/her in
     else
       user = create(provider: auth.provider,    # Create a new user if a user with same email not present
                          uid: auth.uid,
                          email: auth.info.email,
                          password: Devise.friendly_token[0,20])
       user.create_account(name: auth.info.name, # you need to check how to access these attributes from auth hash by using a debugger or pry
                           address: auth.info.location,
                           image: auth.info.image
                           )
       return user
     end
    end
end
```

So you would access this method (usually in the controller) by `User.from_omniauth` as the code is in the *User* class (model).

To move this code to the concern you will have to add a new file, in this example in `models/concerns/omniauth.rb`.

```
module Omniauth
  extend ActiveSupport::Concern

  module ClassMethods
    def from_omniauth(auth)
     if user = find_by_email(auth.info.email)  # search your db for a user with email coming from fb
       return user  #returns the user so you can sign him/her in
     else
       user = create(provider: auth.provider,    # Create a new user if a user with same email not present
                          uid: auth.uid,
                          email: auth.info.email,
                          password: Devise.friendly_token[0,20])
       user.create_account(name: auth.info.name, # you need to check how to access these attributes from auth hash by using a debugger or pry
                           address: auth.info.location,
                           image: auth.info.image
                           )
       return user
     end
  end
  end
end

```

while in the model the code is refactored to include only one line which is `include Omniauth`.

```
class User < ApplicationRecord
  include Omniauth
end

```

You can still call `User.from_omniauth` from the controller as normal and now we had moved from having "fat models" setup to concerns.
