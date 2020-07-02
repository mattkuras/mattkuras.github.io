---
layout: post
title:      "Javascript Project"
date:       2020-07-02 17:01:41 +0000
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

**Rendering Trips**

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
    }```
		
		
		
