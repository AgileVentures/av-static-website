I am having trouble getting started this morning.  "Death warmed up" is hyperbole, but despite my best efforts it's 10am before I've hit the blogface.  Various things interfered.  A 2.5 km jog with my twin boys, although to be honest they weren't slowing me down.  Doing extra long Japanese tests for my older boy now (he's 12); five Karate kata's - my morning routine is starting to take a fair chunk of time.  I felt lethargic independently of all that - my stomach is a bit off.  I've been experimenting with reducing the amount of dairy in my diet.  Also I gave up coffee for the last three weeks.  This really is the situation I'd self-medicate with caffeine to get going, but I have this idea that that's not good for me in the long run.

Also there's the never-ending sets of chores.  Ordering new contact lenses this morning (should I get laser eye-surgery? or am I too attached to my look with glasses? I only use contact lenses for sport), driving to three supermarkets last night to finally find small bottles of carbonated water (my vice) and to the hardware store to buy equipment to help with redoing the front yard during my time off next week (kid's half term).  And then I'm fielding quotes from builders to re-do our upstairs bathroom.  So many things suck up time :-)

I'd had the idea this week to start building a middleman site to begin prototyping new front pages for the main AgileVentures website.  I did make some tweaks to the Getting Started pages yesterday, although Federico had got an animation in there and made other changes.  The blog I pushed out last night had more stats on the Getting Started pages from a few months back, but right now I've got no head for stats.  What's fun?  What's the priority for the community?  I guess it's a toss up between the two things we were looking at in our design sprint.  Adjusting the home pages to try and better explain what we're all about, or a structured guide that gets folks really engaged in projects (attending scrums, submitting PRs etc.), and maybe tracks their progress to boot.

I'm fearful of adding more complexity to the WebSiteOne Rails monolith, and so I think I'd like to experiment with a middleman front end that I have high confidence won't run into performance issues.  Maybe the time should be spent on investigating the Rails performance issues we have, but I'm not suspicious that that will take more time for an uncertain result.

The tricky bit (or just the bit we have to be careful with) for the middleman approach will be the switch over to point http://www.agileventure.org to the GitHub pages, and then creating https://projects.agileventures.org or something to point to the Rails app ... or I just start reading the Heroku notes on memory errors again.

* https://devcenter.heroku.com/articles/optimizing-dyno-usage
* https://devcenter.heroku.com/articles/ruby-memory-use

The difficulty here is with being able to test the impact of changes that we make.  Unless we get some load testing set up.  Heroku has guidelines on that, and an add on to get started:

* https://devcenter.heroku.com/articles/load-testing-guidelines
* https://elements.heroku.com/addons/loaderio

I'm not going to get much done today, but I guess I can throw up a quick middleman app and perhaps alternate these two approaches to feel out which one is going to reap the biggest reward:

https://github.com/middleman/middleman

because I can't bear just reading docs and slow turnarounds for debugging - I want to be making something and encountering issues in real time! :-) Just installing the latest middleman I realise that maybe I should upgrade Ruby.  Ruby 2.4.2 wouldn't install so I backed out to Ruby 2.4.1.  This was the error from Ruby 2.4.2 on OSX:

```
/usr/local/Homebrew/Library/Homebrew/brew.rb:12:in `<main>': Homebrew must be run under Ruby 2.3! (RuntimeError)
```
Hmmm ... 2.4.1 installed and I set it to default:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures ]$ 
→ rvm use 2.4.1 --default
Using /Users/tansaku/.rvm/gems/ruby-2.4.1
```

Although I note c9 is on 2.4.0, so I guess I should install that too; ah, but I have it already ... so then I install middleman in the 2.4.1 space.  Although I'm actually in the 2.4.0 - maybe I need the ruby version on the command prompt ... anyway, in fairly short order I have boiler plate middleman code at:

https://github.com/tansaku/av-landing-page


