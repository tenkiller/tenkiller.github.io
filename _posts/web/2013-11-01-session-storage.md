---
layout: article
image:
  teaser: html5_logo.png
title: Improving Performance with Session Storage
---

I always find it a fun challenge to re-factor code I wrote months ago in an effort to improve it in some way that makes it easier 
to maintain or be more efficient. Having recently become more familiar with local and session storage in the HTML5 specification, 
I went through one of my previous projects to find ways to improve its performance. This project is an ASP.NET MVC 4 WebApi 
application using Durandal and Kendo UI on the client-side.

On my initial pass-through, I found that I had two or modules that loaded the same data from the server used in populating the 
options of a drop-down list or combo box. This data is cached because it rarely changes, which is good, but I wanted to eliminate 
the redundant calls altogether. This is what the code looks like now (shortened for brevity):

```javascript
// viewmodel.js
var activate = function () {
  dataservice.posts().get().done(function (posts) {
    _posts = posts;
  });

  dataservice.categories().get().done(function (categories) {
    _categories = categories;
  });
};
```

There's nothing wrong with this per se, but having the same exact code in more than one location can become a maintenance headache. 
I decided the best solution was to create a module named "storage" and expose function wrappers around the sessionStorage object. 
Then I simply require this module in the viewmodels that need it. Here's how I designed it:

```javascript
// storage.js
define([], function () {
  var getItem = function (key) {
    return sessionStorage.getItem(key);
  },

  setItem = function (key, value) {
    sessionStorage.setItem(key, value);
  },

  getObj = function (key) {
    return JSON.parse(getItem(key));
  },

  setObj = function (key, value) {
    setItem(key, JSON.stringify(value));
  },

  length = function () {
    return sessionStorage.length;
  };

  return {
    getItem: getItem,
    setItem: setItem,
    getObj: getObj,
    setObj: setObj,
    length: length
  };
});
```

I put the dataservice calls within the activate function of the shell.js module. This way, the data is retrieved only once when the 
application is loaded and the responses are saved in session storage.

```javascript
// shell.js
var activate = function () {
  dataservice.posts().get().done(function (posts) {
    storage.setObj('posts', posts);
  });

  dataservice.categories().get().done(function (categories) {
    storage.setObj('categories', categories);
  });

  // configure router
};
```

Then I replaced all of the viewmodel dataservice calls with code that retrieves the posts and categories from session storage.

```javascript
// viewmodel.js
var activate = function () {
  _posts = storage.getObj('posts');
  _categories = storage.getObj('categories');
};
```

For this particular application, I eliminated a handful of HTTP server requests that didn't need to be made. A handful may not seem 
like a lot, but eliminating HTTP requests wherever you can is a big plus in my book.
