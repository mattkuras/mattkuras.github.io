---
layout: post
title:      "Final Project "
date:       2020-08-07 14:42:47 -0400
permalink:  final_project
---


This project marks the end of a long, very intensive 5 months. Although I had my struggles I feel like this course did an excellent job of building up my skills and knowledge to be able to complete this project. 


Being my first time using React and Redux, I decided to keep it simple again. I decided to build a basic e-store with a shopping cart function. The project requirements were broad enough that we had numerous options. I'm excited to begin working on further projects with my newfound knowledge.

**Initialization**

One of the few requirements that do exist is the necessity to use the command ```create-react-app``` to initialize our front end. Like always, I used ``` rails new project-name``` to build my backend. This time, however, I did not use the api flag. I wanted to keep the middleware that allows for sessions in case I wanted to add user login functionality later. I placed both of these file structures under the same directory and began to work on the backend.

**Setting up the Backend**

To start small and keep things simple, I created just three resources. I created models, tables, and controllers and for Cart, Guitar, and Item. The items table is used as a join table linking a cart to a guitar. A cart has many guitars through items. In the future, especially with login functionality, there will be many carts as each user will have a cart. But for now all of the items could receive and a cart_id of 1 as there is only one cart required to hold the user's selected items.

I seeded the database with a list of 8 guitars complete with names, desctiptions, prices, images, and a boolean value showing whether the guitar is in stock. I also seeded the db with a cart available to the user to start shopping. 

I set up controllers for each of the resources in a general manner not sure exactly of how I would need the end points to be set up in the future. The guitars controller only needed a show and index action without any related objects. The items controller uses index, create, and destroy actions. The index actions renders the json with the related guitar object so that the cart can display the details of each item in the cart. Only rendering the item would only provide information from the join table (not very useful). And lastly for the cart controller I only made index and show actions, but turned out to not need to use that endpoint in any requests.

**CORS**

I then realized I forgot to implement the CORS gem. CORS is designed to prevent scripts like fetch() from one origin accessing a resource from a different origin unless that resource specifically states that it expects to share. This provides a solution that allows us to send fetch requests to our server from our browser. So this quickly sorted out my access issues. 

**Setting up the Front End**

Just like how we've been requiring gems for our ruby projects, using javascript and node package manager, we must do the same for our front end. So I began my importing packages like ```   "react": "^16.13.1", "react-redux": "^7.2.0", "react-router-dom": "^5.2.0", "redux": "^4.0.5", "redux-thunk": "^2.3.0" ``` to my json package file. These give me access to features like connecting to the redux store, using components, thunk (to make asynchronous requests), Links, Routers, and several others. 

**File Structure**

For this project, my file structure met the requirements nearly precisely. I used 3 container components and 5 presentational components. Index.js renders my App.js which contains a header and footer component, a container to display guitars, an about page, and the cart. The guitarsContainer component renders the list of guitars seeded in the db upon reaching the home page. It also renders the guitar show page if a guitar name is clicked on. I used the 'react-router-dom' package to utilize the Router feature to meet the requirement of having at least 3 seperate routes in my app, two of which are found in the guitarsContainer, '/' and '/guitar/:id'. The header and footer components are always present.

**Redux Store**

In order to meet the requirement of utilizing Redux middleware, I had to set up the Redux 'store'. Redux handles the state of our application in a centralized object that can be easily accessed by any component, the state. This state is just a plain JavaScript object. In fact, all the pieces of Redux are plain old JavaScript. It is the pattern, the way the information flows that makes Redux awesome. I set up the store in my index.js file with a typical set up that looks like this: 
``` const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

let store = createStore(guitarsReducer, composeEnhancers(applyMiddleware(thunk)))

ReactDOM.render(
  <Provider store={store}>
    <Router>
      <App />
    </Router>
  </Provider>
```
The provider is an imported feature that allows all components wrapped inside of it access to the redux store. And since ```<App/>``` is rendering all of my other components, we wrap it here. Above that bit of code you can see we use the create store function provided by Redux to set a store variable ```store```. And above that we use compose to gain access to in-browser dev tools to use with Redux. Compose is a functional programming utility, and is included in Redux as a convenience. We want to use it to apply several store enhancers in a row. One of which is Thunk. Thunk is middleware that allows one to send asynchronous requests to an external source by allowing the action creator to return a function rather than an object. That returned function receives the store's dispatch function, and with that we are able to dispatch multiple actions: one to place the state in a loading state, and another to update our store with the returned data.


