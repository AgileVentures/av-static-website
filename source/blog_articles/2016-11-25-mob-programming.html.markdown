---
title: Mob Programming
date: 2016-11-25
tags: struct hash Ruby power string boolean automation data structure DRY
author: Sam Joseph
---

We ran our first AV Premium Mob Programming session looking at code from the book "Confident Ruby" by Avdi Grimm.  We created a Cloud9 workspace and gave everyone access to the enviroment.   We had four Premium members join the hangout, and I introduced some of the concepts from "Confident Ruby" before playing with some of the code samples.  All the members in the hangout had read and write access to the Cloud9 workspace, but on this first occasion I did most of the typing.  Most of the members had Avdi's book or had been listening to the Ruby Book Club podcasts that cover it, but it was the first time mob programming for all of them, and hopefully we'll get them doing more typing in future sessions.

We focused on Avdi's "Use built-in conversion protocols" *Collecting Input* pattern with chats about the use of Structs in Ruby, monkey patching (duck punching), duck typing and whether the implicit and explicit conversion operators (e.g. `to_int` vs `to_i`) should be more actively documented in the Ruby core classes.  I also touched on the concept of "Power Level" in response to the question of when to use Structs vs using Classes.  In my experience Structs are an often over-looked aspect of Ruby that allow a developer to create a data structure that has less power than a full blown class.  In parallel with the security guideline that you should only ever provide the minimum level of access necessary to get a job done, there's a similar argument to say that you should only ever use the minimally flexible data structure or entity in order to acheive a given task.

Flexibility sounds great, but it actually equates with complexity.  More powerful entities can do more things, and they are more complex to interact with as a result.  I often write out a simplified power sequence for Ruby as follows from low power to high power:

1. nil
2. boolean ( true/false )
3. integer
4. float
5. string
6. array
7. struct
8. hash
9. class

This is a massive over simplification as the functionality of each of these entities varies along many different dimensions.  However in general we should prefer to use, say, a boolean (true/false) to represent something that has two possible values (e.g. receive email updates preference), rather than using a String to store the values "true" and "false".  The Struct in Avdi's example looks like this:

```rb
Place = Struct.new(:index, :name, :prize)
first = Place.new(0, "first", "Peasant's Quest game")
second = Place.new(1, "second", "Limozeen Album")
third = Place.new(2, "third", "Butter-da")
```

although he could have used hashes:

```rb
first = { index: 0, name: "first", prize: "Peasant's Quest game" }
second = { index: 1, name: "second", prize: "Limozeen Album" }
third = { index: 2, name: "third", prize: "Butter-da" } 
```

The hash is more flexible. We can add arbitrary additional key/value pairs to the individual hash objects, e.g.

```rb
first[:amount] = 1000
```

That flexibility is extra power, that maybe we don't need at this stage.  You can argue we might need it later, but the hashes are also rather unDRY in that the key names have to be repeated over and over, and if we need an `:amount` key we can add it to the Struct, which is arguably DRYer than the long form hashes.  The Struct also provides us a nicer syntax for accessing its key/value contents:

```rb
first.index
```

There are pros and cons to both approaches.  DRYing out the keys into the Struct means that we have increased the dependency between the three entities, so that if we add an `:amount` to the Struct, all the instances of the Struct will also sprout an `:amount` key.  Maybe we want that and maybe we don't; it all depends on the domain model and the behaviours we are trying to support.  Ruby Structs and Hashes differ in various ways, and the real challenge when choosing one over the other is getting the right match, from the various features of Struct and Hash, to the problem we are trying to solve.  It's also sensible to avoid agonising and choose whatever seems simplest to start with and then change to more powerful or complex entities as the required behaviour starts to generate pain points in the code.

The feedback from the mob session was positive, and we had great suggestions for more group problem solving in the future to get everyone's fingers dirty with the code.  It was a good day, and after lunch I started knocking off the automation tasks identified from the previous couple of days profiling.  I started by pushing the slack bot code onto `drie push` where remarkably the bot stayed live, and so I ran some votes.  Michael joined me in a hangout as I updated the static website so that blog images would have a max-width of 700px (to save me re-sizing), and then worked on WebSiteOne to have members' titles update when they upgrade to Premium.  That was a nice set of three things to automate small tasks that are leaching away at my time.

Going forward I hope to keep iterating on automating away simple tasks so I can have more time for interacting with Premium members in Mob, F2F and Plus programming sessions!

###Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=5rQ-SLPjKVs)
* [AsyncVoter Coding](https://www.youtube.com/watch?v=dordkH0qlF0)
* [Kent Beck" scrum](https://www.youtube.com/watch?v=TI3JhhZHmyo)




