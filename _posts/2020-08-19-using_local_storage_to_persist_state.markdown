---
layout: post
title:      "Using Local Storage to Persist State"
date:       2020-08-19 16:46:20 -0400
permalink:  using_local_storage_to_persist_state
---


During my final project I had a problem. Well a few problems. But one problem didn't get resolved. And that was displaying data from my Redux strore upon a page refresh. Because React and Redux are created to work by updating the browser without refreshing the page, data is not always persisted. Makes sense. And because my shopping cart total was being displayed through data in my redux store, it would work accurately while navigating around the application, but would reset to 0.00 (the initial state) when I would refresh the page. One way to fix this is to use ```localStorage```.

**```localStorage```**

According to Nosa Obaseki, ```localStorage``` is "a type of web storage that allows JavaScript sites and apps to store and access data right in the browser with no expiration date. This means the data stored in the browser will persist even after the browser window has been closed." It behaves quite like ```sessionStorage``` however in ```localStorage``` the data is persisted even after the window is closed. In order to use this, I'll need to update my store. 

**Updating the Store**

It take only a few steps to get connect to the browser's local storage. First we need to write a saveState function. 
```
 function saveState(state) {
  try {
    const serializedState = JSON.stringify(state);
    localStorage.setItem('state', serializedState);
  } catch(e) {
      console.log(e)
  }
};
```
This function takes in state as an argument and wrap our code in a ```try``` block. We use ```try catch``` because it is very possible to encounter errors saving data to local storage, often due to things like browser privacy settings. So in our function, we want to save the current state of our store to a stringified JSON object in a variable called ```serializedState``` . We then use the ```setItem``` function to set that variable to the key 'state' in our localStorage. If it can't be saved, then our ```catch``` throws an error. 

That function will be implemented to save our state to our browser, but we need another function to load it.
```
 function loadState() {
  try {
    const serializedState = localStorage.getItem('state');
    if (serializedState === null) {
      return undefined;
    }
    return JSON.parse(serializedState);
  } catch (err) {
    console.log(err)
    return undefined;
  }
}; 
``` 

This ```try catch``` block works similarly, however now were using ```getItem``` to get the ```state``` from ```localStorage``` rather than ```setItem```. In our conditional if the key 'state' equals ```null``` then we have it return undefined, as to not crash our program. If there is data persisted, then it will return our serialized stated parsed from JSON. 

**Final Step**

Now that we have our functions written, we must implement them. 

```
const persistedState = loadState()
const store = createStore(guitarsReducer, persistedState, composeWithDevTools(applyMiddleware(...middleware)))
```

Here, we are saving our ```loadState``` function to a variable and adding it as an argument in our ```createStore``` method. It comes after our reducer function and will overwrite any keys in the store that are different using data persisted in ```localStorage```. And lastly our ```saveState``` function gets implemented just below that 
``` store.subscribe(() => saveState(store.getState())) ```
We use the subscribe method as a listener for any changes in our store, which then automatically calls ```saveState()``` with ```store.getState()``` being passed as an argument.

**Conclusion**

And thats it! There are numerous ways to persist data depending on whether your data is in the redux store, or a component's local state, but using ```localStorage``` is a good way to persist data from your store. 

