---
title: Agile Dev Ops
date: 2016-12-08
tags: acceptance test, YouTube activity, production mode, pairing sessions, VCR cache, Paypal integration, Cucumber step, Scrum meeting 
author: Sam Joseph
---

So my project manager partner for WebsiteOne Raoul has got really busy recently, so we negotiated that I would take on some of the PR merging and release responsibility this week.  After the usual blogging and admin and "Martin Fowler" scrum I jumped into a hangout with Matt and Michael, and tried to use the opportunity to debug the YouTube hookup problems Matt had been having the day before.  This time Matt was getting a new error that he couldn't even start a Hangout On Air, so we couldn't get far.  Burned 20 minutes on that, although it's a fairly common issue our users have, and if we could fix it then we could get more video broadcasts and streaming of other pairing sessions.  Then again I don't know that our YouTube activity is driving much traffic to our site; it doesn't register in Google analytics.  Although people might just be typing in searches direct for "AgileVentures", but I've yet to hear anyone say "Oh, I saw your pairing video on YouTube and wanted to get involved".  The viewing figures on our videos are spiking (the pair programming session I started with Matt and Michael has 333 views today, 5 thumbs up AND 5 thumbs down!).  However, even anecdotally the Ruby Rogues podcast has generated more traffic than all our YouTube activity combined.  Still the YouTube recordings are at least partly for the benefit of the existing community, to allow them to refer back to previous pairing sessions.

Alot of the WebsiteOne infrastructure is set up around supporting Google Hangouts and YouTube streams.  Matt and Michael had got in a Work in Progress pull request that added a characterisation test to the tweeting of YouTube links that was missing an acceptance test.  This was related to trying to fix a long standing issue that our system tweets video links even when the videos are missing.  We'd previously backed off the bigger fix to ensure the current functionality was described in an acceptance test.  I needed to get on to WSO devops and needed to give Matt and Michael feedback without jumping in and trying to fix lots of things.  At least that was my strategy as I reviewed their code.  They had filtered the sensitive Twitter tokens from the VCR cache, which was great, but I had two concerns with the new acceptance test below:

```gherkin
@vcr
Feature: Tweeting Live Events
  As a site admin
  In order to increase participation in events
  I would like live events to generate twitter notifications

  Background:
    Given following events exist:
      | name       | description             | category        | start_datetime          | duration | repeats | time_zone | project | repeats_weekly_each_days_of_the_week_mask | repeats_every_n_weeks |
      | Scrum      | Daily scrum meeting     | Scrum           | 2014/02/03 07:00:00 UTC | 150      | never   | UTC       |         | And an event "Scrum"                      |                       |
    And an event "Scrum"

  Scenario: Event going live causes tweets of hangout link and youtube link to be sent
    When the HangoutConnection has pinged to indicate the event start, appropriate tweets will be sent
```    

One was that we had all the work being done in a single Cucumber step, which was also bound quite tightly to the underlying implementation:

```rb
When(/^the HangoutConnection has pinged to indicate the event start, appropriate tweets will be sent$/) do
  yt_tweet = "Alejandro just hosted an online #scrum Missed it? Catch the recording at youtu.be/11 #CodeForGood #opensource"
  hangout_tweet = "#Scrum meeting with our #distributedteam is live on http://hangout.test Join in and learn about our #opensource #projects!"
   expect(TwitterService.twitter_client).to receive(:update).with(hangout_tweet).and_call_original
  expect(TwitterService.twitter_client).to receive(:update).with(yt_tweet).and_call_original

  participants = {"0"=>{"id"=>"hangout2750757B_ephemeral.id.google.com^a85dcb4670", "hasMicrophone"=>"true", "hasCamera"=>"true", "hasAppEnabled"=>"true", "isBroadcaster"=>"true", "isInBroadcast"=>"true", "displayIndex"=>"0", "person"=>{"id"=>"108533475599002820142", "displayName"=>"Alejandro Babio", "image"=>{"url"=>"https://lh4.googleusercontent.com/-p4ahDFi9my0/AAAAAAAAAAI/AAAAAAAAAAA/n-WK7pTcJa0/s96-c/photo.jpg"}, "na"=>"false"}, "locale"=>"en", "na"=>"false"}}
  header 'ORIGIN', 'a-hangout-opensocial.googleusercontent.com'
  put "/hangouts/@google_id", {title: @event.name, host_id: '3', event_id: @event.id,
                               participants: participants, hangout_url: 'http://hangout.test',
                               hoa_status: 'live', project_id: '1', category: 'Scrum',
                               yt_video_id: '11'}
end
```

The challenge we had here was that we wanted to check the Tweets were sent, but that we were locking our acceptance test to the specific implementation of our TwitterService object.  A reasonable compromise for the time being perhaps, but could we use an RSpec spy on TwitterService to allow us to break the step into two and into a readable order?  Or was there some way to assert that the VCR cache had been hit in the appropriate fashion?  Both, things that could be investigated immediately.  I almost had the Rails console out, but pulled myself back, suggested to Matt and Michael that they just clean up some stray steps and scenarios and get this PR in.  We didn't have unlimited time right now to look for a perfect test.  Some test was better than no test, which was the state we had been in before.

Matt and Michael went off to another hangout and I got on with WSO devops.  I had been hoping to review the waffle board (project management) but only got so far as reviewing all the outstanding PRs.  I merged in the Premium controller refactoring PR that Michael and I had worked on, and checked that on the develop server - all good.  I looked to see what was in the hopper for deploying to production, and it wasn't so much, so I decided to try and pull in the new PayPal integration as well.  I fell foul of merge conflicts and clobberings that I had anticipated when we broke out the separate PR for the refacotoring.  Frustrating, although I think I only burned an additional 30 minutes there.   Not sure if that was time well spent.

I was making a mess on my local system, but the CI was passing, and it looked like we were ready to go, but then the controller specific stylesheets that I had been struggling with the day before were not working on production.  It seemed like they were not playing well with the asset pipeline.  All I was trying to do was to isolate the style for centering and add margin to the payment option wells.  Without they looked like this:

![](https://www.dropbox.com/s/3juob156frv89te/Screenshot%202016-12-08%2010.09.19.png?dl=1)

With they looked like this:

![](https://www.dropbox.com/s/o2tgyq1df0bls9y/Screenshot%202016-12-08%2010.09.57.png?dl=1)

I'd spent an hour struggling to get that to work the previous day.  What should I do?  Just push out what we had?  Burn time fixing them up? I tried to recreate the issue locally by running the local server in production mode, fighting through the nest of environment variables that production needed to run: 

```
SECRET_KEY_BASE=asdfggfhdgs SECRET_TOKEN=12342ewwefdsaf AIRBRAKE_API_KEY=asdffgfhdgsf AIRBRAKE_PROJECT_ID=2342 be rails s -e production
```

but even then the local stylesheets wouldn't load at all.  I'd spent 20 minutes and couldn't replicate the bug locally.  In frustration I shiv'd in the necessary styles, because I anticipated the debug cycle checking for fixes in production mode on the develop server would be prohibitive:

```html
  <div id="card_section" class="col-lg-5 well" style="margin: 10px; text-align: center;"> <!-- shiv because controller stylesheets now working in production mode see https://github.com/AgileVentures/WebsiteOne/issues/1450-->

    <div style="margin-bottom: 15px;">Get Premium via Credit/Debit Card:</div>
    <%= form_tag subscriptions_path(plan: 'premium') do %>
        <script src="https://checkout.stripe.com/checkout.js" class="stripe-button"
                data-key="<%= Rails.configuration.stripe[:publishable_key] %>"
                data-description="A month's subscription"
                data-amount="1000"
                data-currency="GBP"
                data-locale="en-US"
                data-name="Premium Membership"
                data-label="Subscribe"></script>
    <% end %>

  </div>
```

At least I linked the ticket in to the comment, and hope we can circle back to fix that properly, although what it really inspires me to do is move the whole system to a static middleman app where we take a fresh start on the styling.  I pushed on.  There were still some [problems on staging](https://github.com/AgileVentures/WebsiteOne/issues/1451) that I think are related to the data there, and brittleness in parts of the app.  That needs fixing, since it interferes with our ability to do manual checks on staging; but I pushed on and was ultimately rewarded with the newly styled PayPal endpoint on production, and I hurredly fixed up all the static pages to ensure that they pointed to the new 'subscriptions' endpoint.

Agile DevOps?  I'd managed to negotiate a handover from my project-manager pair and get the first part of a bigger epic of PayPal integration out onto production.  I'd had to make several compromises along the way, but at least I'd flagged them with tickets, and thought I'd got a good insight into some of the problems facing us.  I still wonder if we really have performance issues?  The site feels sluggish, but perhaps the 150% memory error warnings from Heroku are just part of their sales strategy.  I am leaning towards an alternate approach where we have a middleman site for the main system, and things like events and projects can be pulled in via JavaScript of separate RESTful API endpoints.  This would mean that heavy load in one part of the site would not affect simple loading of about pages, and also allow the back ends of the different elements of the site to be compartmentalized and chopped and changed as necessary.  It would be a big change, but we're already creating the individual microservices - let's see how we go.  If we can move to the real model I think we really will have done Agile DevOps!

###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=dxImOJLvadE)
* [Pair Programming and DevOps on WSO](https://www.youtube.com/watch?v=VK9qIJwXG1g)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=uQErOajwgt4)
