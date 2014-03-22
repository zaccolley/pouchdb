---
layout: post

title: The rise, fall, and inevetable triumph of HTML5 storage
sub_title: Or, why PouchDB is the jQuery of databases

excerpt: 

author: Nolan Lawson
---

In 2008, the web had a problem.  In the longstanding tug-of-war between native and web applications, which the web had been handily winning for nearly a decade, native was finally starting to gain ground.  Users had begun migrating en masse to mobile devices, a streamlined world full of [chiclet-shaped applications][batelle-chiclets] that bucked the web's principles of openness, standards, and decentralization.

In contrast to the web, mobile applications &mdash; or "apps," as we began fondly calling them &mdash; were (typically) closed-source, non-standards-based, and centrally distributed.  And they were winning.

Of course, native apps must have been doing something right to earn their popularity, and web standards bodies weren't too proud to sit down and take notes. One area where they noticed the web was particularly weak was in client-side storage.

On mobile platforms like Android and iOS, this problem had been solved by [SQLite][]. Web developers, though, were still getting by with cookies and localStorage, which are handy for small bits of data but toys compared to the rich expressiveness provided by SQLite.  The solution was clear: the web needed its own database.

One of the obvious candidates was SQLite itself, being an open-source, battle-tested tool that at least two browser vendors, Apple and Google, could agree on.  However, the [Web SQL Database][websql] spec, which was hammered out in 2009 and integrated into Chrome and Safari's shared WebKit codebase in 2010, was not long for this world.  Mozilla and Microsoft balked, with [Moz pointing out][moz-websql] that SQL was not a standard, and far too high-level for web developers. It was eventually scrapped in favor of [IndexedDB][idb], which as of 2014 is implemented in every major browser except Safari.

If the Web SQL Database spec was too high-level, though, [many felt][idb-portmortem] that IndexedDB had swung too hard in the opposite direction &mdash; and missed by a mile.  The IndexedDB API has been called [clumsy and awkward][idb-awkward], a hopeless mess brought about by [deisgn-by-committee][mikeal-idb], with many [longing for a return to Web SQL][websql-painful].  The AppCache API, another storage standard with similar aims that appeared around the same time, was met with similar criticism. 

Suffice it to say, there's been a lot of mud slung back and forth over the issue. Overall, though, considering the quick death of Web SQL, the hostile reaction to IndexedDB, and the fact that the oft-repeated [last word on AppCache][appcache-douchebag] is an epithet, it's hard to say that the push for HTML5 storage has been a success.

And yet, within all the debate and finger-pointing, there's [one quote][bateman] that renews my hopes for HTML5 storage:

> The goal of IndexedDB was to allow multiple different query processors to be built in JS. We expected few people to use it directly.

This is where PouchDB comes in.

PouchDB is jQuery for databases
--------------------

PouchDB, and similar libraries like [Lawnchair][] and [IDBWrapper][], ease the pain for web developers by abstracting away the underlying database.  Browser quirks, inconsistencies, and blemishes are smoothed out with no small amount of makeup (and often a bit of pixie dust), and the user is presented with a nice pristine API that "just works" in a variety of environments.

Of course, as web developers we're familiar with this situation. jQuery gave us a DOM API that made sense, underscore gave us functional programming, and now MVC frameworks like AngularJS and Ember are reinventing HTML itself. The web developer's most powerful tool, although blunt, is still the `script` tag.

To me, this flowering of frameworks is something to be celebrated, a testament to the web's banner principles of openness, pluralism, and collaboration.  What perplexes me is the popular refrain that this situation should be considered an embarrassment.  *Why couldn't standards bodies get the API right the first time?*, the argument goes.  To which I'd respond: Who cares? Clearly they made an API rich enough for library authors to make something useful out of. And as long as web developers have their choice of libraries, it doesn't seem to have limited their ability to make amazing apps out of nothing.

Also, even if the standards bodies "got things right", are you sure you'd want to use bare JavaScript?  `querySelectorAll` doesn't seem to have slowed the popularity of jQuery.  If anything, the current thinking is that even jQuery is too verbose, so we need the rich data-binding of frameworks like Angular and Ember.  The web keeps innovating, standards grow and adapt, and web developers reap the windfall.

This, I think, is the main advantage the web still holds over mobile.  When [Google decides][kitkat-sdcard] to remove an API that your app depended on, or when [Apple suddenly decrees][ios7.0] that all your graphics should be flat, you don't have much recourse. One of the benefits of browser vendors' maniacal focus on backwards-compatibility, unsurprisingly, is that you can be reasonably sure your app will still work [20 years later][spacejam].

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