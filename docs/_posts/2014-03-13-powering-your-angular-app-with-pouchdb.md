---
layout: post

title: Powering your Angular.js app with PouchDB
sub_title: PouchDB goes together with Angular like PB & J

excerpt: One of the advantages of Angular.js is that all your data models are represented by plain old JavaScript objects.

author: Nolan Lawson
---

# Powering your Angular.js app with PouchDB

One of the advantages of Angular.js is that all your data models are represented by plain old JavaScript objects.  Unlike Backbone or Ember.js, there's no need to extend fancy classes or add observable methods &mdash; your data can be structured in any way you please.

This turns out to be a perfect pairing for PouchDB, since PouchDB stores all its objects as standard JSON.  You can easily write Angular.js controllers, services, and directives that directly read and write to PouchDB, without any conversion layer.

To kick off the first blog post in (what will hopefully be) a series of practical guides to PouchDB, we'll use Angular.js and PouchDB to make a simple webapp that syncs and stores a non-trivial amount of user data, and that automatically refreshes the user's view of that data.

First off, a personal confession: I'm a huge board game geek. And although I consider myself more of a Euro game connoisseur, I've recently been drawn (somewhat reluctantly) into the collectible card game, Magic the Gathering.  Apparently, the game is even bigger in 2014 than it was when it first appeared in 1993, and people still take it just as seriously.  Cards are bought and traded, tournaments are won and lost with much fanfare, and a lot of money gets siphoned to the good folks at Wizards of the Coast.

So, let's write an app to help fans of Magic the Gathering (or MtG, as the pros call it) keep track of their collection of cards.  We'll want to show some stats for those cards (name, cost, type), as well as their rarity and how many copies of that card the user owns.  Since there are 237823 cards in the 2014 set (most people only play with the latest), we'll want to sync that data to the client with PouchDB, to avoid having to read it every time they open the app.

For this tutorial, we'll use the Yeoman Angular generator, since it gives us a nice starting template.  Let's install the generator:

    npm install -g yo
    npm install -g generator-angular

Then let's name our app `my-mtg`:

    yo angular my-mtg

During the interactive generator process, feel free to say "yes" to every question.

Then, cd to the app, install, and run:

    cd my-mtg
    npm install
    grunt serve

(If you don't have Grunt installed, run `npm install -g grunt-cli`.)

At this point we've got a pretty-looking app, but it doesn't do much.  So let's get some data in there!

For the purposes of this app, I wrote a simple scraper to pull data from Wizards' own Gatherer site.  Wizards has given me permission to distribute this as a big JSON file, which you 