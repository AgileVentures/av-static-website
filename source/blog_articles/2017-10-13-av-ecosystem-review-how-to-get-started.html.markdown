So too much food and a late night means I'm late to the blog-face again.  There's also the Slack and email messages that somehow distracted me too ... a:

1) Google Adwords
2) Twitter
3) Facebook
4) LinkedIn
5) Medium

Google Adwords is dropping a lot of people on our site, and a fair number of them are signing up and giving us their email address.  Far fewer are getting into slack and getting active.  We're running social media campaigns that are giving us likes on Twitter and Facebook, but according to analytics they're miniscule in terms of getting folks signed up on our site.  About 20 users between them compared to over 10,000 from Google adwords and organic Google search.  I'm posting my own materials on Twitter and Facebook as well, and we're running campaigns to get free mob programming and f2f sessions if folks follow us on twitter and like us on facebook.  From my own experience that's got at least one member signed up to Premium Mob and just upgraded to F2F.  The last few Premium sign ups seem to have come from folks who came in through the MOOC or from one of my podcast appearances.  Our most recent paid project came in through chatlio, but how did that charity find our nonprofits site?

I think about re-enabling chatlio (or something similar) on just our premium pages on the main site - and putting it back on the nonprofits site.  Maybe complete the charity contract first.  The other idea I have here is what about we put our Premium Mob and F2F campaigns in Google AdWords and advertise them directly?

The other thing I said I was going to do today was look at our new getting started pages and how they are fairing in Google Analytics.  So what's strange is that the behaviour flow doesn't seem to show anyone clicking through on the second page of "getting started":

![](https://dl.dropbox.com/s/0bnhelyc4qni9xf/Screenshot%202017-10-13%2010.27.19.png?dl=1)

Even the "30 more pages" shown, none of them are any of the subsequent getting-started page sequence I previously created.  However if I look at the analytics via the chrome plugin I see 11% clicking through on step 2.

![](https://dl.dropbox.com/s/6ekiiw9ayjf1vxy/Screenshot%202017-10-13%2010.29.22.png?dl=1)

If I open the sequence of pages I can see a steady drop off in the number of visitors reaching each page:

* getting-started: 705 unique page-views
* getting-started-2: 102 unique page-views
* getting-started-3: 72 unique page-views
* getting-started-4: 64 unique page-views
* getting-started-5: 60 unique page-views
* getting-started-6: 54 unique page-views
* getting-started-7: 48 unique page-views

So at least we can clearly see folks progressing along the funnel there, but what we don't have is a strong call to action; a conversion step.  Even just getting folks to click "join project" on the project pages to register their interest in a project could be a good goal.  We also see a fairly strong trend that on the first page a lot of folks are clicking through to see the individual project pages, with the top project "WebSiteOne" receiving the lion's share.  Maybe we could mix up that list a bit, and also take a look at all the project pages and how inviting and action focused they are ...

So there are lots of things we could be doing here:

a) replacing some text instructions with animations (e.g. how to say hello in Slack)  
b) adding more images throughout  
c) deciding on a clear goal state ("joining" a project, saying hello in Slack)  
d) dynamic interventions such as saving progress, or modifying the contents based on a selected project?  
e) allowing the user to search for project via tech stack  

I also start to think if there's some way we could just drop folks in a fresh C9 instance with the project of their choice and a simple chore to work on?

Or would it be better to focus earlier in the flow on the landing page itself.  I also think about doing some landing pages in middleman that would be more performant and allow us to test getting the concept of AgileVentures across more strongly.  I'm encountering performance issues using the site.  According to New Relic it's our static pages in Visitors Controller and Documents Controller that are performing the worst:

![](https://dl.dropbox.com/s/78z9ugrvabo8f7h/Screenshot%202017-10-13%2010.44.57.png?dl=1)

I'm sure there are a series of Rails caching operations that we could use to improve these, but tuning the current monolith while trying to work out what's happening with really slow feedback about whether site performance is improving does not fill me with excitement ... well I'll think about it over the weekend.  I guess next week I could spend the morning av ecosystem review blogs setting up a middleman site and seeing how easily I can re-create the current home page in it ... Or is there something I should tweak in getting started first?  Maybe just encouraging folks to click join on projects?

