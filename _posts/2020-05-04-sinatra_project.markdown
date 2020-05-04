---
layout: post
title:      "Sinatra Project "
date:       2020-05-04 20:40:08 +0000
permalink:  sinatra_project
---


Sinatra is a light-weight framework used to build web applications. I’ve never built a web app or used Sinatra until recently. Every project seems daunting until I sit down and do the work, so I gathered my resources, watched some helpful videos, and followed these steps to completion.

**To begin**

	First, one must brainstorm. I’ve quickly learned that if I dive into the first strategy that pops into my head it’s very unlikely to be the cleanest, most efficient choice. I decided to tie my project to something simple and interesting (to me) so I decided to build a site that allows hikers to find and create hikes to share with the world. Sort of similar to the website theoutbound.com. 

	File structures have thus far given me trouble, but the number of files and folders in this project compared to the past required me to delve deeper in my understanding of each part. To test my knowledge, I created two project folders; one in which I manually added each file and folder, and one in which I generated the project with the Corneal Gem. Corneal is a Ruby gem that is a Sinatra app generator with Rails-like simplicity. And I wasn’t far off at all with my original manually constructed file structure! Eventually, it came to look like this. 



├── Gemfile
    ├── Gemfile.lock
    ├── README.md
    ├── Rakefile
    ├── app
    │ ├── controllers
    │ │ ├── application_controller.rb
    │ │ ├── comments_controller.rb
    │ │ ├── hikes_controller.rb
    │ │ └── users_controller.rb
    │ ├── models
    │ │ ├── comment.rb
    │ │ ├── hike.rb
    │ │ └── user.rb
    │ └── views
    │ ├── hikes
    │ │ ├── edit_hike.erb
    │ │ ├── new.erb
    │ │ ├── show_hike.erb
    │ │ ├── show_hikes.erb
    │ │ └── show_user_hikes.erb
    │ ├── layout.erb
    │ ├── login.erb
    │ ├── signup.erb

    │ └── welcome.erb
    ├── config
    │ └── environment.rb
    ├── config.ru
    ├── db
    ├── public

And all of this was created for me by the Corneal Gem! I hope every project forever is like this!

**Gem File**

My gem file looked like :

source 'http://rubygems.org'

gem 'sinatra'
gem 'activerecord', '~> 4.2', '>= 4.2.6', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
gem 'rake'
gem 'require_all'
gem 'sqlite3', '~> 1.3.6'
gem 'thin'
gem 'shotgun'
gem 'pry'
gem 'bcrypt'
gem 'tux'

group :test do
  gem 'rspec'
  gem 'capybara'
  gem 'rack-test'
  gem 'database_cleaner', git: 'https://github.com/bmabey/database_cleaner.git'
end



To briefly go down the list:
1. The **Sinatra** Gem is what makes this a Sinatra app! It allows our application to know how to communicate with a database or what HTML files to load in the browser.  And even more importantly, without it, we lose connection to the major components of a web application, and in particular, all the moving pieces of MVC.

2. **Active Record** is the M in MVC - the model - which is the layer of the system responsible for representing data and logic. Active Record facilitates the creation and use of objects whose data requires persistent storage to a database. 

3. **Sinatra-activerecord** extends Sinatra with ActiveRecord helper methods and Rake tasks.

4. **Rake** is a Make-like program implemented in Ruby. Tasks and dependencies are specified in standard Ruby syntax. It gives us commands we need to manage our migrations in Ruby.

5. **Require All** allows us to simply load off our code in one place. 

6. The **Sqlite3** Gem allows us to interact with our database engine Sqlite3, a lite version of SQL.

7. The **Thin** Gem is our simple and fast web server that loads our program.

8. **Shotgun** is a small Ruby gem that makes it easier to develop and test Rack-based Ruby web applications locally by starting Rack with automatic code reloading.

9. **Pry** allows us to pause and “pry” into our code for debugging.

10. **Bcrypt** protects and encrypts our passwords for our users.

11. **Tux** allows us to access our database in terminal to create, read, and delete data.


That seems good for now. 

**Environment File**

I need to load my environment in my config.ru file; it's also the place where we are going to mount our application controllers. My controllers must be mounted so that all inherit from the application controller where I have my helper methods and have sessions enabled.

```
require './config/environment'

if ActiveRecord::Migrator.needs_migration?
  raise 'Migrations are pending. Run `rake db:migrate` to resolve the issue.'
end

use Rack::MethodOverride 

use CommentsController
use HikesController
use UsersController
run ApplicationController
```



**Models **

But before I tackle the controllers, it seems I should create my models first. I built a model for comment, hike, and user. A user has many hikes and many comments, a hike belongs to a user and has many comments, and a comment belongs to a user and a hike. Each class inherits from ActiveRecord::Base. Also, user.rb must contain the key word has_secure_password. This adds methods like ‘authenticate’ to set and authenticate against a BCrypt password.

**DB Folder** 

Now that I have models, it's time to give them attributes. This is done by creating migrations! Before we learned migrations, we manually added attributes to each class by creating methods such as initialize, with attr_accessors to read and write the object’s attributes. However, now using Rake we can create a file as easily as writing ‘rake db:create_migration’ in our terminal. This usually gets created with another command NAME= “  ”. As soon as this is executed we can see in our db → migrate folder that there is a migration to fill out.

We repeat this process several times creating tables for our database. The comments table should be created with attributes of content, and a user_id and hike_id. These are foreign keys. Because comment.rb is their child, it receives the foreign keys. We do this so a comment knows which hike and user it belongs to. 

The hike table was originally created with columns for name, difficulty, rating, and user_id. But after playing around with the app it seemed necessary to add a location and a description column. This is another task made easy by rake commands. But rather than using create_table, we use add_column and specify our new columns. The hike table recieved a foreign key because it belongs to a user. 


Our user table was created with a username, email address, and a password written as password_digest. We must specifically use password_digest because we’re using the Bcrypt Gem. This allows Bcrpyt to recognize the password and work its magic. 

**Views and Controllers**

I decided to have several erb files in the main view directory rather than in a folder like Users. Those erb files are login.erb, layout.erb, signup.erb, and welcome.erb. The layout.erb is where permanent page styling is found and where the app yields to other views. There I have links for ‘the hikes’, ‘login’ and ‘myhikes’; all of which are only visible if a user is logged in.

In my views/hikes folder I had .erb files to edit a hike, display a single hike, display all of the hikes, display a specific user’s hikes, or create a new hike. 4 of the 5 files contain a form. In the following I will describe these views and how they interact with the controllers’ routes.

**Edit.erb** responds to  get '/hikes/:id/edit'  where @hike is set to the hike’s id via params[:id]. A form is loaded and posts to the route patch` “/hikes/<%=@hike.id%>"` .  This route checks for any bad data and saves the hike and any changes to the database. 

**Show_hike.erb** responds to get '/hikes/:id' where again @hike is set to the hike’s id via params[:id]. This view displays all of the hike’s attributes and gives the user two options via a delete form or a link to edit the hike IF the id of the current_user (helper method defined in ApplicationController.rb) matches the hike’s foregin key, user_id. At the bottom all of the comments that belong to the hike are displayed. This too is done by matching the comment’s foreign key called hike_id to the hike’s id. 

**Show_hikes.erb** responds to get '/hikes’ which is also the landing page if a user is logged in, sort of similar to how twitter shows all of the tweets on your homepage of those you follow. Here each hike created by each user is displayed in order or creation, displaying the name, rating, difficulty, and user who created the hike. The names of the hikes are links that trigger `get 'hikes/:id'` and the names of the users are links that trigger `get '/users/username'` to display the hikes created by that user. 

That is where **show_user_hikes.erb** is rendered. Originally, I made this route in the hikes controller. But because my program was having trouble deciphering my routes apart, I moved it to users/username in the users controller instead. And I had no more problems with routes being followed after that. This view displays all of the same information that show_hikes.eb displays, but only those hikes of a specific user. And if the hikes belong to the current_user, then options to edit or delete the hikes are available.

And lastly, **new.erb** responds to get '/hikes/new' and creates a new hike. If there is no bad information given by the user, the route posts to post '/hikes/new' and creates a new hike and shovels it into the current_user ‘s hikes via   `current_user.hikes << Hike.new(params)`. 

**Styling**

Finally after I finished adding all the functionality required, I attempted to style by app. I have maybe 20-30 hours off css practice dating back to 9 months ago, so I thought I’d give it a shot. However I quickly realized that having done this without styling in mind before hand, my options felt limited, especially considering my lack of knowledge. So I moved things around playing with margins and padding, but ultimately gave up in frustration. Certain tagged elements would not respond to even 500px of margin and I could not figure out why. I tidied up the program to the best of my abilities, but hope to learn more soon to make brilliant-looking sites. 

**Conclusion**

And that's it! I was a bit nervous about beginning the project because like most software related things, I felt I had only a tenuous grasp on the MVC pattern, SQL, and other moving parts. But doing is the best way to learn and learn I did. And now we begin the cycle again: me fearing Rails, learning it but feeling like I still don’t know it, then me showing myself that I do know it!


