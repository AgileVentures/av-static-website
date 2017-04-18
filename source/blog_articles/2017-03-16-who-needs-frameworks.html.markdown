I listen to a different podcast every morning.  Last Friday it was ["Greater than Code" with Ra’Shaun Stovall (Snuggs)](http://www.greaterthancode.com/podcast/episode-014-rashaun-stovall-snuggs/).  It was pretty mind expanding with quotes and questions like the following:

> seniors often try to hold on to the complexity they love

> if you are presenting a problem and not suggesting a solution then you are part of the problem

> why is open source so closed?

Snuggs comes from a US college football background and brings huge energy and lots of analogies.  Another idea he was introducing was that maybe we didn't need so many frontend frameworks.  I posted to that effect in the GreaterThanCode Slack and got a response that was worthy of a short blog post from Snuggs.  To paraphrase his response, Snuggs thinks no one is being accountable for hand holding the huge numbers of bootcamp grads from bootcamp to junior.  I'd certainly love to see AgileVentures fulfilling that role by having teams of bootcamp grads working on open source projects for charities and non-profits around the world.  We do have graduates from a number of bootcamps involved, but there's a way to go, to perfect that support process.  It would be great if we could collaborate more with NYC.rb and RailsLink.  

Snuggs runs NYC.rb and the associated RailsLink slack which just reached it's 5000'th member.  He says:

> It's to the point now where they are all teaching themselves and truth be told 3 juniors pairing with the proper guidance will become senior in months not years. I've seen it with my own eyes. And they are relentless at learning best practices. 

I'm trying to get my head round the DevPunks and RailsLink communities that Snuggs is at the heart on.  I've been in the RailsLink Slack, but so far it seems like lots of Q&A.  I'm not quite following where to see the pairing and mentoring that Snuggs talks about, but I'm going to keep asking there.  I just watched Snuggs' video on ["Why is Open Source so Closed?"](https://youtu.be/A5ad52AogJ8).  He makes a great call out to travel and opening your mind.  Snuggs is so full of ideas and energy that I sometimes find it difficult to follow, but the way I'm hearing it is that Snuggs was surprised how there are so many cool things going on outside the Ruby world, e.g. in the JavaScript world, and is railing against the cliques that develop around individual programming languages, where for example Ruby devs might look down on JavaScript and PHP developers.  That's a great message.

And I think that's where the idea that maybe we don't need quite so many frontend frameworks comes from.  I couldn't find a blog post expressing the same sentiment as Snuggs, but I posted this ["You might not need React"](https://hackernoon.com/you-might-not-need-react-e5fd54611111#.ywyol38o9) post, and we had some discussion there, but I wasn't sure that I was properly articulating Snuggs' point.  In his >Code post, Snuggs referred me to his repo [https://github.com/snuggs/snuggsi](https://github.com/snuggs/snuggsi) which includes polyfills that mirror parts of jQuery, e.g. 

```js
// Polyfill for Sizzle CSS selection

const $ = selector => // always returns a collection. Just like jQuery
  ('string' === typeof selector)
    ? document.querySelectorAll (selector)
    : selector // identity function

$('body').length // 1

let element = document.body
$(element) === document.body  // true
```

What I'm hearing here is that modern JavaScript is evolving to the point that some parts of some frontend frameworks might be redundant.  It would be great to see some open source projects developed using that approach.  Of course there's fear with change.  "If it's not broken don't fix it" can be a powerful mantra when you've been burnt by complex coding dependencies ...

###Related Videos:

* [Snuggs on "Why is Open Source so Closed?"](https://youtu.be/A5ad52AogJ8)

###My Podcast Playlist:

* [Ruby Rogues]()
* [Soft Skills Engineering]()
* [JavaScript Jabber]()
* [The Changelog]()
* [Freelancer Show]()
* [Software Engineering Daily]()
* [Greater than Code]()
* [Ruby Book Club]()
