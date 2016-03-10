---
layout: article
image:
  teaser: ajax_logo_sm.png
title: A Dynamic Ajax Queue
---

I came across an interesting problem while working on one of my projects today. On the surface, the requirement I was coding 
against seemed pretty straightforward. The user is editing a form that includes links to uploaded files. The user is allowed 
to delete these links from the form and save the changes. Uploaded files may be images, word processing documents, or other 
media. Since the files are stored on the server, a request has to be made to delete the resource from the server.

> How can we add the ajax request to the queue without firing it off?

For performance reasons, I didn't want to make a server request each time the user clicks to delete a file. Instead, I opted to 
queue each ajax request in an array, and when the user saves changes, then make the server requests. Below is my first run at 
the implementation. Assume any variables not defined within the function itself are defined elsewhere within scope. I've 
shortened it for brevity.

```javascript
function saveChanges() {
  var queue = [];

  for (var i = 0; i < files.length; i++) {
    // push ajax request into the queue - it returns a promise
    queue.push(
      dataservice.filesRepository.remove(files[i].id)
    );
  }

  $.when.apply($, queue)
  .done(function() {
    console.log('File changes saved');
  });
}
```

At first glance, you would think this works as intended. Better yet, when you run the application, it works as you'd expect. 
However, if you dig a little deeper and pay attention to when the ajax requests are made, the timing doesn't seem right. If you 
set a break-point at the for loop and step through the code, you'll notice that the ajax request fires off as soon as its added 
to the array, rather than at the `$.when()` call later on. This is not what was intended, so, I had to figure out how to change 
this behavior.

Now, I couldn't very well add each Ajax call inside the `$.when()` function because the number of ajax calls we're going to make 
is unknown. It could be zero or more. We need to have a queue. But, how can we add the ajax request to the queue without firing 
it off? It took me a couple of hours, but I managed to implement a solution that I was satisfied with.

What I ended up doing was storing the ajax function a different way. Rather than storing the entire function call, I stored a 
reference to the function and the arguments to the function as separate elements. Then, when it was time to execute the requests, 
I mapped the elements in the queue into an array of ajax requests and inserted that within the `$.when()` function.

```javascript
function saveChanges() {
  var queue = [];

  for (var i = 0; i < files.length; i++) {
    // push ajax request into the queue - it returns a promise
    queue.push({ 
      fn: dataservice.filesRepository.remove, 
      params: files[i].id
    );
  }

  $.when.apply($, $.map(queue, function(obj) {
    return obj.fn(obj.params);
  }))
  .done(function() {
    console.log('File changes saved');
  });
}
```

I ended up refactoring this again and moving all the queue-related code into its own reusable module. I have a feeling I will be 
needing this again. Now, call me paranoid, but I'm still not entirely sure the $.when() function is doing what I assume it's 
supposed to be doing, but the timing is correct now. I feel like I should dig a litter deeper. :)
