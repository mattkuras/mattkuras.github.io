---
layout: post
title:      "Javascript Project"
date:       2020-07-02 13:01:42 -0400
permalink:  javascript_project
---


By far the most challenging project thus far, but also the most informative. I personally found it a bit challenging to piece together all of the different components of jacascript that we had learned, but this project did a good job of reinforcing most of the material. 

Due to the challenge of piecing together knowledge with a shallow understanding, I decided to keep it simple and make another blog-like app. It's summertime here in New Jersey, so I wanted to design an app to create and d[](http://)isplay trips to take in NJ. 

Like my last project, I added models and tables that weren't utilized but are there for further development as I continue to work on my projects. These are the steps I took. 

**Initialization**

Unlike our last project, this required that we use Rails as an API only. There are numerous benefits to using Rails as an API application that can be found [here](https://guides.rubyonrails.org/api_app.htmlhttp://). To begin, I ran ``` rails new js-project --api```. This flag is used due to the fact that Rails includes all of the sub-frameworks (ActiveRecord, ActionMailer, etc) by default. Rails will remove a lot of default features and middleware, mostly related to the browser, since it won't be needed.

I then created two directories in my repo.sitory One to handle the front end and one to handle the backend. Some students create two different repos.

**Models and Migrations**

To achieve the technical requirement of having one ```has_many``` relationship, it made most sense (at the time) to build a user model and a trips model. A user has many trips. At the time I was not aware of the fact that part of the middleware that Rails removes when initializing an application with an ```--api``` flag is that which configures the ```sessions```  features that worked so seemlessly in our Rail projects. More on this later. 

I used the ```rails g resource``` generator to create models and migrations for user, trips, activities, saves, comments, and locations. Because I have yet to incorporate all of the models, I needed to use ```optional: true``` and ```:dependent => :destroy``` methods in the model files for locations and trips in order to safely create certain objects.

I then went into my trips and users controllers and made simple index functions rendering the saved variables as json, using the include option to also render the activities objects to json at the localhost/3000/trips end point and to also render the seeded trips with the users at the localhost/3000/trips end point. 

Using Rails console I checked to see that my models' relationships were in order, and I turned to the front end. 

**CORS**

CORS is designed to prevent scripts like fetch() from one origin accessing a resource from a different origin unless that resource specifically states that it expects to share. This provides a solution that allows us to send fetch requests to our server from our browser.

**Rendering Objects and Forms**

Using code for nice html "cards" once sent to me by a friend, I built my trips into those using javascript. I had never rendered html with javascript before, so it hadn't occurred to me how much more code is required to render a form with javascript commands rather than simply placing a form or card in the html file. But having a clean, bare html file to work with was quite appealing so I did this for all my forms! For example this is the code that it took to render my new user form:
```static renderForm(){
        const container = document.getElementById('container')

        const form = document.createElement('form')
        form.classList.add("hide")
        const label = document.createElement('h2')
        label.innerText = "Log In!"
        container.appendChild(label)
        label.addEventListener("click", () => {
            form.classList.remove("hide")
        })
        form.id = "user-form"
        container.appendChild(form)
        const inputName = document.createElement('input')
        inputName.type = "text"
        inputName.name = "name"
        inputName.placeholder = "name"
        form.appendChild(inputName)

        const inputUsername = document.createElement('input')
        inputUsername.type = "text"
        inputUsername.name = "username"
        inputUsername.placeholder = "username"
        form.appendChild(inputUsername)

        const inputPassword = document.createElement('input')
        inputPassword.type = "password"
        inputPassword.name = "password"
        inputPassword.placeholder = "password"
        form.appendChild(inputPassword)

        const subButton = document.createElement('input')
        subButton.type = "submit"
        subButton.value = "Submit"
        form.appendChild(subButton)
    } 
		``` 
		
		One can see a pattern. Use some sort of selector to select a ```div``` (or wherever you'd like to display your form; create elements using ``` document.createElement('element') ```;  add attributes, innertext, classes, etc to the created element; and use the ```appendChild``` method to add the element to the container or form. I repeated a similar process for rendering user objects, trip objects, and a form to create a trip. 
		
		The act of rendering our form like this keeps our html code cleaner, and addresses the concern of the seperation of concerns. A larger, more complex version of such an app could get messy quickly. So it seems appropriate for the JS User class to display code concerning a user. That is not to say that my app does that as efficiently as possible. There was not a large focus on JS file structure in this unit (or any focus really), hence why it wasn't mentioned in the project requirements. But after speaking with a former flatiron student and seeing his project, I can see that there is a lot to learn about possible ways to structure an application with javascript. 


**Trip Creation Work Around**

As I mentioned earlier, after days of working I encountered an obstacle that threw a wrench in my project. I had planned on using a ``` has_many ``` relationship of User has many Trips. However, since I initalized my app to use Rails as an API, the middleware that allows the sessions hash to function normally was disabled. And I wasted a lot of time trying to sort out why my user would not stay logged in. Using byebugs and byebugs searching for the disconnect, I found that a user was being created and persisted, but as soon as the program exited the the create action, the ``` current_user``` method would return nil. Google searches eventually led me to what was going on. This was quite an issue. Because a trip belongs to a user, it needed to be initialized with a user_id, so there were not many seemless solutions left to make this work.

What I decided to do was to render the user's attributes somewhere on the screen, including the user_id that received an HTML id in order to be able to grab that user's id. So after a user signed in, the program automatically ran the function ```renderUser()``` so that when the user rendered the form to make a new trip, the value for user_id on the screen was automatically inserted into the form's user_id field that was then sent to the controller to create a new user. So even though no user was technically signed in, the app was able to know who was making a trip.

Because of my lack of foresight into this problem, I spent far too much time trying to sort it out, which prevented me from incorporating many other features into the app before submission. But alas the project requirements were met. 

		
