---
layout: post
title:      "Using Local Storage to Persist State"
date:       2020-08-19 20:46:19 +0000
permalink:  using_local_storage_to_persist_state
---


During my final project I had a problem. Well a few problems. But one problem didn't get resolved. And that was displaying data from my Redux strore upon a page refresh. Because React and Redux are created to work by updating the browser without refreshing the page, data is not always persisted. Makes sense. And because my shopping cart total was being displayed through data in my redux store, it would work accurately while navigating around the application, but would reset to 0.00 (the initial state) when I would refresh the page. To fix this, I needed to update my store.

**```localStorage```**
