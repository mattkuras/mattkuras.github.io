---
layout: post
title:      "First Rails Project"
date:       2020-06-08 17:33:56 -0400
permalink:  first_rails_project
---


My Flatiron cohort just completed our first Rails projects. The assignment was to "build a complete Ruby on Rails application that manages related data through complex forms and RESTful routes. The goal of the application is to build a Content Management System, whether the content being managed is Blog Posts, Recipes, a Library of Resources, or any domain model that lends itself to these requirements (the majority of ideas you could come up with would probably meet the requirements)." 

My idea was to build a sort of social media platform with basic functions like posting, liking, commenting, but with a little bit of a twist. Upon sign up, users will choose "identities". These are roles in life ranging from writer, athlete, musician, mother, etc. And by choosing identities, users have access to "communities" which are like alternate newsfeeds for posts that belong to each community. I was going to incorporate a friend or follower type of model as well but decided to exclude that due to time.

Here are the steps I followed 

**1. Create the app**

To create files with Rails just type on the terminal:
$ rails new app_name

**2. Install gems**

```
gem 'bcrypt', '~> 3.1.7'
gem 'omniauth'
gem 'omniauth-facebook'
gem 'dotenv-rails'
gem 'figaro' 

```

Bcrypt gives us access to methods like ``` has_secure_password``` that protects our users password in the db.
The next two gems gives us access to user signup via Omniauth and Facebook
The last two are to load environment variables, which omniauth sign in's will require

**3. Resource generators**

In order to get the most accurate files that I would need, I used the resource generator. This provides us with:

* A migration file that will create a new database table for the attributes passed to it in the generator
* A model file that inherits from ApplicationRecord (as of Rails 5; see Note above)
* A controller file that inherits from ApplicationController
* A view directory, but no view template files
* A view helper file
* A Coffeescript file for specific JavaScripts for that controller
* A scss file for the styles for the controller
* A full resources call in the routes.rb file

I used this generator to produce models and migrations (among other things) for users, posts, likes, comments, and communities. I also have a model and table for Identities. The schema is as follows:
``` 
create_table "comments", force: :cascade do |t|
    t.integer "user_id", null: false
    t.integer "post_id", null: false
    t.string "content"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.index ["post_id"], name: "index_comments_on_post_id"
    t.index ["user_id"], name: "index_comments_on_user_id"
  end

  create_table "communities", force: :cascade do |t|
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.string "name"
  end

  create_table "identities", force: :cascade do |t|
    t.string "name"
    t.string "progress"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.string "standard"
    t.integer "user_id"
    t.integer "community_id"
  end

  create_table "likes", force: :cascade do |t|
    t.integer "user_id"
    t.integer "post_id"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
  end

  create_table "posts", force: :cascade do |t|
    t.text "content"
    t.integer "user_id", null: false
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.integer "community_id"
    t.index ["user_id"], name: "index_posts_on_user_id"
  end

  create_table "users", force: :cascade do |t|
    t.string "first_name"
    t.string "last_name"
    t.string "email"
    t.string "password_digest"
    t.datetime "created_at", precision: 6, null: false
    t.datetime "updated_at", precision: 6, null: false
    t.integer "followed_id"
    t.string "uid"
    t.string "provider"
  end

  add_foreign_key "comments", "posts"
  add_foreign_key "comments", "users"
  add_foreign_key "posts", "users"
end
```
A few examples of the relationships are 

User 
* has many communities through identities
* has many posts
* has many comments
* has many likes 

validations such as ```    validates :first_name, presence: true, length: { minimum: 2 }```
(Most validations were added at the end to not interrupt work flow)

Post
* has many comments
* belongs to a community (optional)
* belongs to user
* has many likes 

Like
* belongs to post
* belongs to user

One of the most important tasks was implementing the identities table because it worked as a join table for comunities and users. The only attributes were community_id and user_id.

After I tested out the relationships in the console and got (most of) them working, I began to work from my user story. Before starting I had plans for functionality in the form of a user story, so I used this as well as the [project requirements](https://learn.co/tracks/online-software-engineering-structured/rails/rails-project-mode/rails-portfolio-projecthttp://) as guidance as I worked. 

**Upon Signup**

If no user is logged in, going to the homepage redirects to the login page where there is a link to sign up or login with facebook as an Oauth strategy. 

As a user is directed to /users/new to signup, they fillout fields for a first_name, last_name, password, email, and then select identities in a nested form. Ten communities were seeded into the database prior to signup, and the 10 community names are iterated over. If an identity is selected, it receives the community id and the user id, and the user has access to the community through the identity. They may select up to ten. In a grander app this list would be expanded upon. 

**Homepage**

After signup the user is directed to the homepage at posts#index, sort of like a twitter homepage. There was a lot to do on this page so ill explain some of it. My index action for posts looks like this:
```
def index 
    @like = Like.new
    if params[:community_id] 
        
      @posts = Community.find_by(id: params[:community_id]).posts
    else
      @posts = Post.all.reg_posts
      @user = current_user
    end
  end
	```
	
	``` if params[:community_id]``` serves the purpose of checking whether its being called inside a nested route. If so, it is to index the posts with a community id matching the id in the params. There are two nested routes. "/community/:id/posts" and "/community/:id/posts/:id". The nested routes took quite a while to get right, and in some places needed to settle with linking the route with interpolation rather than using a url helper. 
	
	One may also notice the line of code ```@posts = Community.find_by(id:params[:community_id]).posts``` 
	This is the required scope method that uses ActiveRecord queries, defined in the post model as ```scope :reg_posts, -> {where(community_id: nil)}```
	If the community_id is not found in the params, then we want all of the "regular" posts displayed. Ones without a community id because they are meant to be generic.
	
	If the community id is absent from the params, then you may see that @user is not defined. This serves its purpose in the posts#index view here:
	```<%if @user%>
    <ul>
    <% @user.identities.each do |i|%>
    <%id = i.community_id%>
    <li><%= link_to i.community.name, community_path(id) %></li>
    <% end %>
    <br>
    </ul>
<%end %>
```

We only want to display links to the communites if we are in the homepage index. However if we follow the link it will take us to the community#show page, where we may follow another link to the posts#index nested inside community#show. And now the communities are gone thanks to the above logic. We didn't hit ```else``` in the index controller so the communities are absent. 

**Likes**

Similar to other social media platforms, we have a like button. Like is its own class and each time the button is hit the post gains another like with a user id and post id. The likes#create action is one of the more complex actions in this app, as there are many different places to redirect to and I wanted to prevent users from liking the same post twice.
```
def create
    @post = Post.find_by(id: params[:like][:post_id])
    if @post.likes.any? {|like| like.user.id == current_user.id}
      redirect_to posts_path 
    else

    @like = @post.likes.build(like_params)
      if @like.save
        
        if !params[:like][:post_id]
        redirect_to posts_path
        else 
          redirect_to post_path(@post)
        end
      else
        redirect_to posts_path
      end
    end
  end
	```
	After the post is found, the likes belonging to the post (likes with the post id) are iterated over and checked against the current user's id. if they match, the user is redirected to the posts#index page. Otherwise the action checks if we are in a nested route and redirects us accordingly. 
	
	**Omniauth**
	
	On the login page there is a link to sign up via third party, facebook. It was quite a challenge to get it work as I have little to no experience navigating .env files. But I followed a [walkthrough](https://medium.com/@euniceschoi/facebook-oauth-with-rails-33fc8c1a4e58http://) that uses the Figaro gem to handle the environment. The information coming back from facebook was not perfect, so the user is directed toward the users/:id/edit page to correct any info they would like. 
	
	**In the future I would like to**

1. 	Style the project. I still have very little experience using css and did not have time to learn during the process of building the app.
2. 	Add follower function. Right now upon logging in a user is directed to posts#index where they see *all* of the posts. It doesnt matter if they are friends or not. And that might be annoying.
3. 	More identities.
4. 	Progress attributes for users. It would have been quite challenging to incorportate, but if this were a real app, I would like to prompt users to answer questions whether they've been participating in their chosen identities, to monitor if they keep to their standard and progress. 

**Conclusion**

And thats mostly it! This was quite challenging and really drilled in the Rails knowlege that I was tenuously grasping at the beginning of project week. I now understand how much Rails can do and also where its limited, and where JS and React fit in. 

