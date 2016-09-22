---
title: New Horizons
date: 2016-08-26
tags: elixir phoenix premium plus ruby javascript python
author: Sam Joseph
---

A bit of a red letter day yesterday - what does that mean actually? Calendars use a red font on days that are holidays?  Anyway, yesterday we did a PremiumPlus pairing session on Elixir/Phoenix.  A new tech stack, and a first for AgileVentures in terms of the new charity business model.  It was pretty good fun.  Kudos to Federico for being our PremiumPlus beta-tester.  Elixir itself is interesting.  The basic syntax is Ruby-esque.  I'd say it feels superficially as different from Ruby as, say, Python or JavaScript.  Of course we're only scratching the surface at the moment, but I liked how maps immediately let you access keys with the dot syntax;

```elixir
iex> map = %{a: 1, b: 2}
%{a: 1, b: 2}

iex> map.a
1
```

That makes me think of JavaScript, and it's an aspect of both JavaScript and Elixir that I like, and I'd love Ruby to support that.  For some reason putting a new key in a map in Elixir is a little more complex, requiring:

```elixir
iex(3)> Map.put(map, :c, 3)
%{a: 1, b: 2, c: 3}
```

while I think Ruby's syntax is easier here;

```rb
map[:c] = 3
```

but JavaScript wins hands down over both languages:

```js
map.c = 3
```

Anyhow, Elixir may evolve a simpler syntax over time, although it uses the assignment operator `=` in subtly different ways from JavaScript and Ruby, so perhaps it's more complex than I imagine.  Actually thinking about it, a simple `method_missing` on Hash in Ruby would allow that simpler JS style access, but I guess that would lead to some weird situations if you had keys in your hash that corresponded to other methods you would call on a Hash, hmmm ... there are some gotchas with that power in JS aren't there ... :-)

So Elixir syntax looks okay and Ruby-ish.  I'm not particularly enamoured with having to concatenate Strings and List with different operators (`<>` and `++`).  One of the things I love about Ruby is just using `+` for all these things.  However whatever I feel about the syntax the process of getting Phoenix up and running had relatively little to do with the syntax in the first instance, and that was the main focus of our session yesterday.  We got Phoenix installed on a few boxes, two OSX and one Ubuntu.  The main confusion came from searching for "Phoenix getting started" in Google and clicking on the first hit, which is actually an out of date link to v0.10, when Phoenix is currently on 1.2, so watch out for that.  Make sure you get started on the up to date documentation:

[http://www.phoenixframework.org/docs/up-and-running](http://www.phoenixframework.org/docs/up-and-running)

Having got to the correct documentation (and assuming Elixir and few other things were set up), getting started with Phoenix was as simple as:

```sh
$ mix archive.install https://github.com/phoenixframework/archives/raw/master/phoenix_new.ez
$ mix phoenix.new hello_phoenix
$ cd hello_phoenix
$ mix ecto.create # set up the database
$ mix phoenix.server
```

There were some bumps on Ubuntu as we needed to install and configure Postgres, but before you could say "new technology stack" we were looking at the Phoenix startup page on running Phoenix servers, and thinking wistfully back to 2006 and a similar looking page from Rails (at least I was thinking wistfully back).  Of course that start up page from Rails was just a static one, as is the Phoenix one, so the critical next step was to see how challenging it would be to set up some custom functionality.  We started in to adding some new pages, but ran out of time for the session, but have a new one scheduled for next Tuesday to see how deep we can get.

I can piece together the different elements that led to this session.  Me hearing about Elixir/Phoenix on RubyRogues, my main pair partner Michael talking about the same, and PremiumPlus BetaTester Extraordinaire Federico expressing an interest in it.  New tech stacks are kind of a gamble though.  A good portion of my brain at AgileVentures is focused on avoiding bells and whistles - what's the absolute simplest path we can take to deliver the simplest piece of functionality that will allow our clients to assess whether we are heading in the right direction?

The attraction of Elixir/Phoenix (apart from the new and shiny) is that it will scale better than Ruby/Rails, but massive scaling is not a critical problem for AgileVentures or our charity clients, yet.  Of course if we wait till it becomes a huge problem it may be too late to scale.  Premature optimisation?  Sensible advance scouting and foraging activity?  Only hind-sight will tell us, and then how can we be sure if the way things turned out was just luck (or lack of it)? :-)

Anyway, AgileVentures the charity enterprise now has a new sort of client/customer - the Premium and PremiumPlus member, and if they're interested in a particular tech stack, then exploring that tech stack is part of the process of meeting the needs of our clients, so the Agile tiger in my brain is pacified.  So now the trick is, can we get enough Premium and PremiumPlus members of our charity to make this sustainable?  The problem with listening to Ruby Rogues is hearing about all the great tech salaries and listening to advertising about reverse auctions for developers and so on.  A recruiter hit me up for a well paid job involving Machine Learning and Ruby on Rails yesterday and it's damn tempting.  I worry about money a fair amount recently.  We're slowly scaling the AgileVentures experience, but we need an order of magnitude more paying members, or two or three rich charities paying us for product before I'll sleep easy ...

As my cash reserves run down should I take a break for 6 months to earn money working closed-source; but if I do so, will I be missing the critical chance to take AgileVentures to the next level?  I love coding open source for non-profits and with other professionals who are levelling up their tech skills.  Can it be made sustainable?





