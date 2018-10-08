---
layout: post
title:      "Rails Project"
date:       2018-10-07 21:15:29 -0400
permalink:  projectmanageable_-_rails_project
---




## App Overview
“Project Manageable” is a simple Rails application that allows users manage and share project with others. Creating this Rails app was a fun and challenging exercise. It helped me understand the fundamentals of how to build the app following RESTful Rails principles and design patterns. You can check out the repo [here](https://github.com/JulJen/project_manageable_app/)!

For understanding how to organize my rails app files, it helped to review how a request/response cycle works. 

### Request/Response cycle:

When submitting a HTTP (URL) request with a specified method/verb (GET, POST, PATCH, PUT, DELETE):
*  the **request** hits the **router** (config/routes.rb),
* the **router** maps the URL request to the correct ** controller** and action
* the **action** receives the **request**
* based on the **request**, the **model** fetches the requested data from the **database**
* the data requested from the **database** is returned to the **model**
* the **model** returns the data to the **controller** in the form of **objects**
* **Objects** get tasked to **view** - the controller action sends the data to the correct view 
* the **view** renders the page as **HTML** and passes the HTML back to **controller**
* the **controller** sends the HTML back to the **browser**
* Browser - page loads and the user can see the rendered info


### [What is REST?](https://www.youtube.com/watch?v=a2igBE6oRhE)

> REST stands for REpresentational State Transfer and describes resources (in our case URLs) on which we can perform actions. REST is an architectural style for applications. REST resources can be the result of different database queries, and neither the client nor the REST server really needs to know how these queries are constructed or where the data comes from.  - [RESTful Rails Development by Silvia Puglisi](https://www.oreilly.com/library/view/restful-rails-development/9781491910849/ch04.html)
> 

Rails uses the RESTful architectural design pattern, a resourceful route by default create seven different application routes (index, show, new, create, edit, update, and destroy), which provide a direct connection between the HTTP verb request and the controller action. 

Previously when working on my Sinatra CRUD (index, show, new, create, and destroy) application, the RESTful routes were declared seperately for each HTTP action/method in the controller. In Rails, I can reduce the amount of code needed for routing by using a resourceful route. Using resourced routes and getting into the habit of running `rake routes` was a good way to see how my URLs would communicate with a user. From there, I was able to fine-tune my resourced routes and remove the routes I didn't need using the `:only `and `:except ` options. In certain cases, it was better to use single custom routes that specify the route and the action to be matched.

* User sign-up and log-in
```
  get '/signin' => 'sessions#new'
  post '/signin' => 'sessions#create'
  get '/signup' => 'users#new'
  post '/signup' => 'users#create'
```

* User main page
```
  get '/dashboard' => 'users#index', :as => 'main'
```

* Public projects
```
  get '/dashboard/categories' => 'categories#index', :as => 'categories'
  get '/categories/:id' => 'categories#show', :as => 'categories_teams'
```


### ActiveRecord model associations:


Stubbing out your project is key.  Thinking about user functionality, I wanted the user to have a homepage, an account/profile page, projects,  project posts,  and categories for organizing projects by interest/industry groups. Next was setting up the model associations. Using the ruby console (`rails c`) to play around with has-many-through model associations is a great way to see how active record uses the built-in Object Relational Mapping system to manage model relationships. Using the[ rails-erd gem](https://github.com/voormedia/rails-erd) was helpful to visually review my model associations through a entity-relationship diagram (ERD).



![Imgur](https://i.imgur.com/nKdY2DYl.jpg)

### Overview for model associations:

* Users -  **has_many** memberships and **has_many** projects *through* memberships. With the *has many through association*, the user has access to projects through the membership table. 

* Projects -  **has_many** memberships and **has_many** users *through* memberships, and **belongs_to** category. With the *has many through association*, the project has access to users through the membership table. 

* Memberships - **belongs_to** users and **belongs_to**projects. The join table connects the Users and Projects table with a foreign key of user_id and project_id, which references/points to the primary key of both these tables respectively. The Membership join table also has a submittable boolean attribute for admin which returns a value of true or false. So when the user creates and saves a project, this also creates a membership join table that returns a value of 'true' for admin. Using controller logic, if the join attribute of admin returns true, the user has administrative access for update and destroy actions for the newly created project. 

* Posts - **belongs_to** project. Using user logic flow in the controller actions, if a user is admin (admin attribute for the membership's table returns true), the user can also create and manage their project's posts using the create, update, and destroy actions. Users that do not have admin status can only see the show and index actions for a project. 

* Categories - **has_many** projects. I created this table to organize projects based on their area of interests. 

* Comments - This would be an additional join table that I would like to add and work on. Comments   **belongs_to** post and **belongs_to** user. Once a user becomes a project member (has a membership_id that points to the project_id and user_id), they can also view and comment on project posts. 


### Gems
* For third-party authentication, I used the google authentication strategy - [omniauth-google-oauth2 ](https://github.com/zquestz/omniauth-google-oauth2). 
* I used [Faker](https://github.com/stympy/faker) gem to quickly generate random and unique user data in my seeds.rb file. 
* I also played around with the [Simple Form](https://github.com/plataformatec/simple_form) gem. Previously, to have form validations rendered in my view, for example, for user sign-up, I would include a block of code like this:
```
form_for @user, url: signup_path  do |f|
  <% if @user.errors.any? %>
    <ul>
      <% @user.errors.full_messages.each do |msg| %>
	      <li><%= msg %></li>
	    <% end %>
    </ul>
  <% end %>
<% end %>
```
With the simple_form gem, I can write fewer lines of code and still render my form validations:
```
<%= simple_form_for @user do |f| %>
  <%= f.error_notification %>
<% end %>
```

### Goals (for future projects) :

* For my next app, I would like to learn more about the BDD process get into TDD, and also work on some CSS styling. 

###### Helpful resources:
* [When to use has_and_belongs_to_many (hint: never)](https://flatironschool.com/blog/why-you-dont-need-has-and-belongs-to-many/)
* [Rails Request-Response Cycle]( https://www.codecademy.com/articles/request-response-cycle-dynamic
])
* [Disciplined Rails: Routes & Controllers, the Rails Way](https://medium.com/@jaryl/disciplined-rails-models-controllers-the-rails-way-bbc940822136)
* [REST vs CRUD: What’s The Difference?](https://www.bmc.com/blogs/rest-vs-crud-whats-the-difference/)





