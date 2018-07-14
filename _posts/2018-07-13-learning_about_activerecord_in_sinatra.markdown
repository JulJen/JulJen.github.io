---
layout: post
title:      "Building a Sinatra app"
date:       2018-07-13 21:02:58 -0400
permalink:  learning_about_activerecord_in_sinatra
---


For my second project, I built a functional Sinatra MVC (Model View Controller) Application, using ActiveRecord. The 'Locavore Kitchen' application allows a user with an account to create recipes with a name including a headline title,  recipe directions, and list of ingredients. 

Check out  my project on GitHub:  [Locavore Kitchen](http://https://github.com/JulJen/locavore-kitchen-app) 

In this app, I demonstrate:

* New user signup
* Adding a new recipe 
* Adding new ingredients to the recipe 
* Editing a recipe and ingredients
* Deleting a recipe
* Logging out
* Logging in as another user
* Seeing someone else’s recipe
 
## ** Models**
It was really helpful to draw out a model relationship diagram to better understand the belongs_to and has_many associations. I decided to have 4 models - user, recipe, ingredient, and recipe_ingredient model. 
* the user has_many recipes and recipes belong_to a user
* recipes has_many recipe ingredients and has_many ingredients through recipe_ingredients
* ingredient has_many recipe_ingredients and has_many recipes through recipe_ingredients
* recipe_ingredient belongs_to a recipe and belongs_to a user

![locavore-kitchen-app_modelrelationship_diagram](https://www.lucidchart.com/publicSegments/view/3f6125df-84b7-437f-b656-cfa216328703/image.png)



## ** Controllers**

I started off with just an application_controller.rb file which inherits from Sinatra::Base. 
```
class ApplicationController < Sinatra::Base
end 
```
Once I reached a point where I had a grouped set of routes for different functions (user login, adding recipes, adding ingredients),  I split my application_controller into controller subclasses (users, recipes, ingredients)  each inheriting from a common application_controller. From there,  I continued on with creating the CRUD actions in each controller. 
```
class UsersController < ApplicationController
end 
```
```
class RecipesController < ApplicationController
end 
```
```
class IngredientsController < ApplicationController
end 
```

## **Views**
I started with an initial landing page, a root.erb for when the user is not logged in. It helped to visualize the user process by creating a user flow diagram. 

![Relationship diagram](https://www.lucidchart.com/publicSegments/view/efbe797a-244d-4848-bcd8-66690f3c4106/image.png) 


### **Gems**

One fun little gem that I enjoyed using is ['ffaker](https://github.com/ffaker/ffaker)' , a library to generate fake data (creates and saves instances) of the models. 
* Declare the dependency in your gemfile and run `$ bundle install`

```
group :development, :test do
	gem 'ffaker', '~> 2.2'
end
```

* Add to your db/seed.rb file and run rake db:seed.  In this example, the following code loops over all of the users and recipes to creates twenty users and twenty recipes, to belong to each one. You can use `.unique` to generate unique values for each method. 

```
require 'FFaker'

User.destroy_all
20.times do |index|
  User.create!(fname: FFaker::Name.unique.first_name, #=> "Kaci"
    lname: FFaker::Name.unique.last_name, #=> "Ernser"
    username: FFaker::Internet.unique.user_name, #=> "alexie"
    password_digest: FFaker::Internet.unique.password, #=> "*%NkOnJsH4"
    email: FFaker::Internet.unique.email,  #=> "eliza@mann.net"
    state: FFaker::AddressUS.state_abbr, #=> "AP"
    bio: FFaker::Lorem.phrase #=> "Accusantium tantillus dolorem"
  )
end

Recipe.destroy_all
20.times do |index|
  Recipe.create!(name: FFaker::Lorem.word, #=> "Caesar Salad"
    content: FFaker::Lorem.phrase, #=> "Accusantium tantillus dolorem"
    directions: FFaker::Lorem.phrases,  #=> "Three eggs with cilantro, tomatoes, onions, avocados and melted Emmental cheese. With a side of roasted potatoes, and your choice of toast or croissant."
    created_at: FFaker::Time.between(4.months.ago, 1.month.ago),
    updated_at: FFaker::Time.between(4.months.ago, 1.month.ago)
  )
end
```




I tested this application in the browser with the help of gems  'shotgun' and 'pry'. I was able to check line by line of code and also refresh the page to see changes without having to restart the local server.

Overall this project was a great learning experience. There were a lot of learning curves on this project, and now I feel more confident creating a sinatra app from the ground up. Thanks for reading this blog post, looking forward to sharing the next chapter of my journey which will be Ruby on Rails.

**Sources**


[Writing Extenstions](http://sinatrarb.com/extensions.html)

[Sinatra: Up and Running](https://www.oreilly.com/library/view/sinatra-up-and/9781449306847/ch04.html)

[important-sinatra-concepts](https://wixelhq.com/blog/important-sinatra-concepts)

[First Steps with Sinatra as a Rails Developer](https://www.netguru.co/codestories/first-steps-sinatra-1)
