---
layout: post

title: The failure and inevitable triumph of HTML5 storage
sub_title: Why PouchDB is jQuery for databases

excerpt: In 2009, the web had a problem.  In the longstanding tug-of-war between native and web applications, which the web had been handily winning for almost a decade&hellip;

author: Nolan Lawson
---

In 2009, the web had a problem.  In the longstanding tug-of-war between native and web applications, which the web had been handily winning for almost a decade, native was finally starting to gain ground.  What had changed, of course, was the emergence of smartphones and the ensuing mass migration to mobile devices.

The appeal was obvious. Compared to the web, mobile was a more streamlined world full of chiclet-shaped applications [1], unburdened by the web's traditional principles of openness, standards, and decentralization. In contrast to the web, mobile applications &mdash; or "apps," as we began fondly calling them &mdash; were closed-source (usually), non-standards-based, and centrally distributed.  And they were winning.

Of course, native apps must have been doing something right to earn their popularity, and the W3C wasn't too proud to sit down and take note. One area where they noticed the web was particularly weak was in client-side storage.

Storage is a big concern on mobile devices, since there's no guarantee of a permanent Internet connection. Phones need to remain useful even when their connection to the server is cut off, and they need to do so with limited memory and battery life.  On platforms like iOS, Android, and later BlackBerry, this problem was solved with [SQLite][].

Web developers, though, were still making do with cookies and [localStorage][], which are handy for small bits of data but toys compared to the rich expressiveness provided by SQLite.  The solution was clear: the web needed its own database.

One of the obvious candidates was SQLite itself, being an open-source, battle-tested tool that at least two browser vendors, Apple and Google, could agree on.  However, the [Web SQL Database][websql] spec, which was hammered out in 2009 and integrated into Chrome and Safari's shared WebKit codebase soon after, was not long for this world.  Mozilla and Microsoft balked, with [Moz pointing out][moz-websql] that SQL is not a standard, and far too high-level for web developers. It was eventually scrapped in favor of the NoSQL-inspired [IndexedDB][idb], which as of 2014 is implemented in every major browser except Safari.

If the Web SQL Database spec was too high-level, though, many felt that IndexedDB had swung too hard in the opposite direction &mdash; and missed by a mile.  The IndexedDB API has been called [clumsy and awkward][idb-portmortem], a hopeless mess brought about by [design-by-committee][mikeal-idb], with many [longing for a return to Web SQL][websql-painful].  The AppCache API, a related standard that appeared in 2010, [was also panned][appcache-douchebag] by developers.

Suffice it to say, there's been a lot of mud slung back and forth over the issue of web databases. However, considering the quick death of Web SQL, the lukewarm response to IndexedDB, and the fact that the last word on AppCache is an epithet, it's hard to say that the push for HTML5 storage has been a resounding success.

And yet, within all the debate and finger-pointing, there's one quote that renews my hope for HTML5 storage:

> The goal of IndexedDB was to allow multiple different query processors to be built in JS. We expected few people to use it directly.
>
> *[&ndash; Ade Bateman][bateman]*

This is where PouchDB comes in.

PouchDB is jQuery for databases
--------------------

PouchDB, and similar libraries like [Lawnchair][] and [IDBWrapper][], ease the pain for web developers by abstracting away the underlying database.  Browser quirks, API verbosities, and other blemishes are smoothed over with a fine layer of makeup (and often some pixie dust), and the user is presented with a nice pristine API that "just works" in a variety of browsers.

Of course, as web developers, we're familiar with this situation. jQuery gave us a DOM API that made sense, Underscore gave us functional programming, and now MVC frameworks like AngularJS and Ember are reinventing HTML itself. The web developer's most powerful weapon, although blunt, is still the humble `script` tag.

To me, this flowering of frameworks is something to be celebrated, a vindication for the web's founding principles of openness, pluralism, and collaboration.  What perplexes me is the popular refrain that this should be cause for embarrassment.  *Why couldn't standards bodies get the API right the first time?*, the argument goes.  To which I'd respond: *Who cares?* Clearly they made a good enough API for library authors to make something useful out of. And as long as web developers have their choice of libraries, it doesn't seem to limit their ability to make amazing apps out of nothing.

Also, even if the standards bodies "got things right," are you sure you'd want to use bare JavaScript?  `querySelectorAll` doesn't seem to have slowed the popularity of jQuery.  If anything, the current thinking is that even jQuery is too verbose, so we need the rich data-binding of frameworks like Angular and Ember instead.  The web keeps innovating, standards grow and adapt, and web developers reap the benefits.

This, I think, is the main advantage the web still holds over mobile.  When [Google decides][kitkat-sdcard] to remove an Android API that your app depended on, or when [Apple suddenly decrees][ios7.0] that all your graphics should be flat, you don't have much recourse. One of the benefits of browser vendors' maniacal focus on backwards-compatibility, unsurprisingly, is that you can be reasonably sure your app will still work [20-odd years later][spacejam].

PouchDB in practice
-------------

Let's see some examples of what PouchDB can give us that the bare JavaScript can't. Here's a sample todo list from [HTML5rocks][idb-awkward], written using the raw IndexedDB API (bear with me, it's kind of a mouthful):

```js
html5rocks.indexedDB.open = function() {
  var version = 1;
  var request = indexedDB.open("todos", version);

  // We can only create Object stores in a versionchange transaction.
  request.onupgradeneeded = function(e) {
    var db = e.target.result;

    // A versionchange transaction is started automatically.
    e.target.transaction.onerror = html5rocks.indexedDB.onerror;

    if(db.objectStoreNames.contains("todo")) {
      db.deleteObjectStore("todo");
    }

    var store = db.createObjectStore("todo",
      {keyPath: "timeStamp"});
  };

  request.onsuccess = function(e) {
    html5rocks.indexedDB.db = e.target.result;
    html5rocks.indexedDB.getAllTodoItems();
  };

  request.onerror = html5rocks.indexedDB.onerror;
};

html5rocks.indexedDB.addTodo = function(todoText) {
  var db = html5rocks.indexedDB.db;
  var trans = db.transaction(["todo"], "readwrite");
  var store = trans.objectStore("todo");
  var request = store.put({
    "text": todoText,
    "timeStamp" : new Date().getTime()
  });

  request.onsuccess = function(e) {
    // Re-render all the todo's
    html5rocks.indexedDB.getAllTodoItems();
  };

  request.onerror = function(e) {
    console.log(e.value);
  };
};
```

And here's the equivalent code using PouchDB:

```js
var db = new PouchDB('todos');

function addTodo(todoText) {
  var date = new Date();
  db.put({
    _id       : date.toJSON(), // sort on date strings
    text      : todoText, 
    timestamp : date.getTime()
  }).then(getAllTodoItems, function (err) {
    console.log(err);
  });
}

```

At this point, if you aren't having flashbacks of replacing all your bloated `xmlHttpRequest.onreadystatechanged` calls with `$.ajax()`, you haven't been doing web development for very long.

So PouchDB is obviously giving us much prettier API to interact with IndexedDB.  But a pretty face is not enough, and even beauty pagents have their talent round, so let's see what kind of smarts PouchDB is hiding behind that pretty face.

### Surfin' Safari, Cruisin' Couch

The most impressive trick that PouchDB is pulling off under the hood, of course, is that the above code will work on Safari, iOS, and pre-KitKat Android, even though those browsers only support Web SQL, not IndexedDB.

Additionally, since PouchDB supports CouchDB as an alternative backend, you can also extend support to even older browsers that don't support either Web SQL or IndexedDB, such as IE 9 and lower.  Assuming you've set up a CouchDB (or IrisCouch, or Cloudant, or whatever), you can do the following:

```js
var db;
if ((PouchDB.adapters.idb && PouchDB.adapters.id.valid()) || (PouchDB.adapters.websql && PouchDB.adapters.webql.valid())) {
  db = new PouchDB('mydb');
  // sync bidirectionally with CouchDB
  db.replicate.sync('http://mysite.com:5984/mydb', {live : true});
} else {
  // just use CouchDB directly
  db = new PouchDB('http://mysite.com:5984/mydb');
}
```

Your IE9 users will have to make more round-trips to the server, and they can't use your app offline, but you can use the `db` object exactly the same on all browsers.

### Other quirks

Of course, as with most everything in the web, the browser inconsistencies don't stop there.

Now, normally I wouldn't subject the reader to a list of browser oddities, since it's mostly only interesting in a "cabinet of curiosities" kind of way, and also our own Dale Harvey [has already written extensively about it][dale-idb].  But just in case you're not already sold on PouchDB's abstraction over the native APIs, let's lift up the tentflap and have a quick look at the freak show:

* **Chrome** does not support storing blobs in IndexedDB.
* **IE 10 and 11** do not support complex keys in IndexedDB.
* **Android 2.3** ships with obsolete version of the IndexedDB spec on some Samsung devices.
* **Older Android browsers** crash on IndexedDB's `continue()` method, since it's a keyword.
* **WebKit browsers** do not retrieve blobs correctly in SQLite.
* **Safari** goes one step further by adding extraneous characters.

I could go on and on, but hopefully I've shocked you enough that we can go back to the world of pretty APIs, and you can allow PouchDB to continue shielding you from the horrors of working with the native databases.

##### Notes

[1] Full disclosure: [I write chiclet-shaped apps][nolan] for a living. I also stole the "chiclet" line from [John Batelle][batelle-chiclets].

[kitkat-sdcard]: http://www.androidcentral.com/kitkat-sdcard-changes
[spacejam]: http://www2.warnerbros.com/spacejam/movie/jam.htm
[ios7.0]: https://developer.apple.com/library/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS7.html
[bateman]: https://twitter.com/adrianba/status/236249951049502720
[websql-painful]: http://lists.w3.org/Archives/Public/public-webapps/2011JanMar/1114.html
[moz-websql]: https://hacks.mozilla.org/2010/06/beyond-html5-database-apis-and-the-road-to-indexeddb/
[idb-awkward]: http://www.html5rocks.com/en/tutorials/indexeddb/todo/#toc-step2
[dale-idb]: http://arandomurl.com/2013/02/21/thoughts-on-indexeddb.html
[idbs-vs-websql]: http://blog.harritronics.com/2011/03/on-web-sql-database-and-indexeddb.html
[idb-portmortem]: http://lists.w3.org/Archives/Public/www-tag/2013Feb/0003.html
[appcache-douchebag]: http://alistapart.com/article/application-cache-is-a-douchebag
[idbwrapper]: https://github.com/jensarps/IDBWrapper
[ydn-db]: https://github.com/yathit/ydn-db
[lawnchair]: http://brian.io/lawnchair/
[sqlite]: https://sqlite.org/
[websql]: http://www.w3.org/TR/webdatabase/
[idb]: http://www.w3.org/TR/IndexedDB/
[mikeal-idb]: https://twitter.com/mikeal/status/236185527026585600
[batelle-chiclets]: http://battellemedia.com/archives/2011/12/on-this-whole-web-is-dead-meme.php
[chrome-websql]: http://blog.chromium.org/2010/01/more-resources-for-developers.html
[tobie-appcache]: http://www.w3.org/2011/web-apps-ws/papers/Facebook.html
[localStorage]: https://developer.mozilla.org/en-US/docs/Web/Guide/API/DOM/Storage
[nolan]: http://nolanlawson.com/apps