---
title: Distracted
date: 2017-03-02
tags: ERB, Slack, Email, Phone, Views, Rails, Refactoring
author: Sam Joseph
---

![distracted](/images/distracted.jpg)

So after taking a full week away from Slack and Email I managed to start work again without too much obsessive checking of Slack and Email on my phone.  At least for the last ten days I'm not checking it when I first wake up and, instead, leaving it till after my morning blog.  I'm pretty much avoiding looking at it during the evenings, although I tend to have a last Slack check around 9:30pm when I post my blog.  Pretty good for a week and a half, but last night I ended up looking at my email while my eldest was reading to me, and then this morning I ended up looking at it while I was getting dressed, as I tried to send myself a reminder email.  There wasn't anything urgent to deal with, but in both cases I did manage to load chunks of work worry into my mind.  And then just now I seem to have reviewed all my Email and Slack before getting down to the blog.  There wasn't so much to deal with, but I feel a little unsettled.

Maybe I need to move the Slack and Email icons on my phone deeper into a nested directory and make sure that I shut down Slack and Email when I finish on my laptop each day, so that I don't accidentally look at them before blogging.  My tweet/slacking of the podcast I've listened to in the morning interferes with that.  I guess I should get into the habit of tweeting and then re-posting that to Slack after the morning blog is completed.  Not that I'm sure that I'm achieving much with the blogging.  It may be an interesting historical record to look back on for me.  Not sure about anyone else :-)   I continue to post out older blogs on [Medium](https://medium.com/@tansakuu).  My blogs from last October were much more coding focused, and my rambling blogs don't compare favourably (in my mind) with the ultra-focused "learn this!" blogs that others stamp out.

It's March and I committed to myself to keep daily blogging to June and I like to stick to my commitments.  I am getting a bit of coding done this week, following the WebSiteOne kick off with Michael and Raoul last Friday.  I've got three pull-requests open, one for each of the three tickets I committed to working on.

* [spikes some example tooltips](https://github.com/AgileVentures/WebsiteOne/pull/1564)
* [less intimidating invitations to scrums for non-logged in users](https://github.com/AgileVentures/WebsiteOne/pull/1566)
* [adjust intial messaging](https://github.com/AgileVentures/WebsiteOne/pull/1568)

They're all relatively simple view changes.  Most of the work so far has been fixing failing regression tests (i.e. other tests that were relying on the view looking a certain way).  Some of it is a bit spikey.  I've created acceptance tests in Cucumber for the last two, and the middle one above has some logic in the view that I'm tempted to pull out.  The blog from early Feb that I was reviewing last night also spiked something with a fair amount of code in the view.  Early February I was focused on getting the escalating call to action out, in the hope that it might impact the number of people upgrading to Premium plans, but as far as I can see it's had no impact.  We've had a new Premium Mob member join, but I think that's come entirely through Slack interaction.  Maybe there's a value in the long run with the name of the next upgrade being repeated to folks whenever they are coming to the site, but then again we're not really tracking how often the different types of member view the home page ...

I just created a [refactoring ticket](https://github.com/AgileVentures/WebsiteOne/issues/1569) to pull code out of the "escalating call to action" view.  I've heard Armando say "Don't put code in your views" umpteen times as I've re-viewed the MOOC videos. Clearly coding in ERB isn't pretty - moving the code into a helper would reduce the number of symbols to look at.  It could go in a presenter, or be refactored into a model.  The code in the current feature I'm working on is less heinous.  That's just the addition of:

```erb
<% event_name = current_user ? event['name'] : 'Want to learn more? Listen in. Next projects review meeting '%>
```

While I'm experimenting with the look and feel of the site, I find it easier just to be focusing on a single file.  The code in the escalating call for action view is more heinous:

```erb
<% if current_user %>
    <% if current_user.membership_type == 'Basic' %>
        <% path = new_subscription_path %>
        <% message = 'Upgrade to Premium for additional support' %>
    <% elsif current_user.membership_type == 'Premium' %>
        <% path = new_subscription_path(plan: 'premiummob') %>
        <% message = 'Upgrade to Premium Mob for group sessions with a Mentor' %>
    <% end %>
<% else %>
    <% path = new_user_registration_path %>
    <% message = 'Get started now to begin coding!' %>
<% end %>
```

I don't really want to pull the logic from either into the controller.  The code could be going into the Event or User models, but that feels like overloading them.  What we have here is sort of business logic about how we communicate with the different types of user.  Would it help to get the strings into the I18n setup so that we can refer to them via a constant string, the meaning of which can be looked up in a text file?  Or is that getting distracted from the business logic experiments we are trying to carry out?

Is refactoring these things for code cleanliness pulling time that could be spent on measuring their impact more accurately?  My temptation is to deploy the new language and see if the Scrum masters conclude that more new folks are coming into online hangouts.  That's effectively what I've done for the "escalating call to action".  I'm set up to be alerted whenever anyone signs up for a Premium plan and I'm in regular contact with all the Scrum masters.  Setting up extra code for measurement seems excessive at this stage.  I'm confused; what's the distraction and what's the focus? :-)

I'm glad we had this chat though - I'm thinking a presenter object is the way to go and that it might make the business logic clearer for the point when we want to focus on that without being distracted :-)

###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=1nWmG1f529c)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=29Y3Chv8WjE)



