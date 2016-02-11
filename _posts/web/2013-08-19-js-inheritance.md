---
layout: article
title: Javascript Inheritance
---

I do realize that this subject has been talked to death by many, many other smart people that have come before me. 

> I find it fascinating to build objects and establish relationships between them using properties and behaviors.

Yet, I can't resist taking a moment to comment on it. I'm a huge fan of programming languages and the features built 
into them. I especially have a kindred connection with object-oriented approaches to software development. For some 
reason, I find it fascinating to build objects and establish relationships between them using properties and behaviors. 
Terms like abstraction, modularization, encapsulation, polymorphism, and inheritance define my professional lexicon. It 
is with the latter term, inheritance, which I am having fun with in one of my favorite languages - JavaScript. 

For the past month, I have been working on re-factoring a previous web project. My goal is to convert it into a 
single-page application (SPA). I decided to use [Durandal](http://durandaljs.com/) as the MVVM framework because it's 
easy to understand and configure due to the convention-based approach it uses in binding view-models to views. It's 
also built on using promises in its composition and binding control-flow, which I am very impressed with.

Almost every one of my views in this project contains an event that triggers the display of a modal popup dialog. After 
coding the view-models for a few of these modal dialogs, I found myself repeating code. This is the point where I 
stopped and looked for a way to modularize it so that I could reuse it. Inheritance almost immediately came to 
mind. Why not create a base class that contains all of this boilerplate code and have each dialog inherit from this 
class? That would eliminate the problem of me repeating myself and make my code easier to maintain and understand. 
Huzzah!

Inheritance in javascript, the first time you come across it, can seem a little foreign, especially if you came from 
languages like C++ or C#, like I did. However, it doesn't take long to get the hang of it. Anyways, back to what I was 
talking about. For the base class of my modal dialog views, I decided on a typical prototypical class structure.

```javascript
function Modal() {
  this.viewModel = {
    name: 'Modal'
  };
}

Modal.prototype = {
  activate: function() {
    log.append(this.viewModel.name + ' activating');
  },
  attached: function(view) {
    log.append(this.viewModel.name + ' attached');
  },
  deactivated: function() {
    log.append(this.viewModel.name + ' deactivating');
  },
  detached: function(view, parent) {
    log.append(this.viewModel.name + ' detached');
  }
};
```

Each prototype method correspond to the composition lifecycle event that occurs in Durandal as it binds the viewmodel to 
its view and the DOM. This is a contrived example, I know, but one worthy of illustrating my objective. I use the 
activate method to prepare data for the dialog prior to it being attached to the DOM. When the dialog is attached, I 
configure UI widgets and bind events within the attached method. Both the deactivated and detached methods are used for 
cleanup. So, when I need a new dialog, I create a new class and inherit from the Modal class.

```javascript
function ChildModal() {
  this.viewModel.name = 'ChildModal';
  this.viewModel.version = 'v2.0';
}

ChildModal.prototype = new Modal();
ChildModal.prototype.constructor = ChildModal;
ChildModal.prototype._super = Modal.prototype;

ChildModal.prototype.activate = function() {
  this._super.activate.call(this);
  log.append(this.viewModel.version + ' activating');
};
```

You'll notice that I like to define a `_super` property on the child class' prototype. I do this for instances where I 
need to call the parent method of the same name. For instance, if my child dialog view needs to perform cleanup of its 
unique objects in its deactivate method, but also needs to clean up inherited objects. I can call the parent class' 
deactivate method to handle the last part for me. It gives me a little added flexibility in certain scenarios.

Here's a [jsFiddle](http://jsfiddle.net/EthtE/) of this example, with a little more added to it. For me, it works out 
really well. I'm pleased with the results so far. Eventually, I'll have this project re-factored and then I can ask my 
users to take it for a spin and let me know what they think of the improvements. Cheers!
