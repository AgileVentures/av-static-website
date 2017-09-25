---
title: Always in Production
date: 2017-01-24
tags: illness, cold, flu, Premium, Python, Adwords, Analytics, meeting, DevOps, traffic, bug hunt, bug fix, hotfix, git, production, staging, development
author: Sam Joseph
---

![production](/images/production.jpg)

My cold is receding.  Does that justify me pushing myself through activities and work over the last three days?  Maybe I'd be better quicker or not feeling quite so exhausted if I'd rested the last three days, but my boys got their football matches, I had a great Premium pairing session with Michael where we scoped out a new data-analysis project in Python, and I managed to get the Google AdWords grant accepted, and we're live with our first campaign, which has apparently put our advert into 5400 search results and led to 221 click throughs:

![this mornings adwords stats](https://www.dropbox.com/s/3lbx4vbpqz3kn8x/Screenshot%202017-01-24%2009.51.59.png?dl=1)

I can see the spike in the traffic in the Google Analytics dashboard:

![increasing numbers of sessions in site according to Google Analytics](https://www.dropbox.com/s/a1b7miuiwzjnm28/Screenshot%202017-01-24%2010.05.59.png?dl=1)

Exciting stuff! Yesterday was a lot of meetings, but also a fair amount of DevOps on WebsiteOne.  Seeing this traffic spike makes me wonder whether we'll have to start throwing more resources (i.e. money) at the WebsiteOne servers?  Or can we switch to drie in time? I'd love to get the main site set up with more of a funnel encouraging folks to sign up for a newsletter, membership and premium/sponsorship packages.  One thing at a time.

The main technical [issue](https://github.com/AgileVentures/WebsiteOne/issues/1517) yesterday was that scrums were staying live too long on the homepage and navbar popup.  This was frustrating as the previously live scrum was blocking the next upcoming one.  Not that we have huge evidence that large numbers of people join our scrums through the links in the website, but it's one of those things like the broken youtube links in the AgileVentures tweets that just feels wrong.  At the very least I using the front page and pop-up scrum reminders to navigate to the right place to actually start the scrums.  Particularly if we are just directing more and more folks to our site, we'd like to avoid glaring inaccuracies.

![scrum countdown popup](https://www.dropbox.com/s/c8sq020s1kc2ww9/Screenshot%202017-01-23%2014.38.51.png?dl=1)

I'd just pushed out a fix for the PayPal currency, but that didn't seem like that would cause this problem.  I could have broken out git bisect, but I had a hunch that this might be related to a recent change that was intended to keep events that were live in the upcoming events list so that they were more discoverable.  A quick review of the code in that pull request and the code that manages what is shown in the countdown popup made it look like my suspicion was correct.  The partial for the popup is as follows:

```erb
<% if event.present? %>
  <p>
    <%= link_to event['name'], event_path(event['id']) %>
    <% start_time = event.next_occurrence_time_attr.to_datetime %>
    <% if start_time < Time.now %>
      is live!
      <% if event.last_hangout.try!(:live?) %>
        <%= link_to 'Click to join!', event.last_hangout.hangout_url %>
      <% end  %>
    <% elsif start_time - 1.minute < Time.now %>
      about to start...
    <% else %>
      in <%= display_countdown(event) %>
  <% end %>
  </p>
<% end %>
```

that event entity comes from the application controller so the popup can be displayed throughout the site:

```rb
  def get_next_scrum
    @next_event = Event.next_occurrence(:Scrum)
  end
```

which in turn is relying on this method:

```rb
  def self.next_occurrence(event_type, begin_time = COLLECTION_TIME_PAST.ago)
    events_with_times = []
    events_with_times = Event.where(category: event_type).map { |event|
      event.next_event_occurrence_with_time(begin_time)
    }.compact
...
```
The pull request to adjust how long the live events stayed in the upcoming events page had modified the COLLECTION_TIME_PAST constant.  The constant was used in another place, where it affected what would be in the upcoming events page.  It had been modified to influence things there, but had this knock on effect in the other place where it was used.

I didn't have proof, but now a very strong hunch.  It's also a great example of how every time you DRY something out, you are also introducing a dependency.  Was this perhaps a variable that might have been better off not becoming a constant?  And we clearly didn't have any acceptance tests covering this situation, since everything had had to be green to get into production.  Of course we've had a trouble free couple of years with the popup working and hindsight here is 2020.  This felt like a bleed on the site, and while I'd pinged the developer who worked on the recent pull request, it was highly possible they would not available to address the issue soon.

I took the slightly risky step of generating a hotfix to push straight into production.  Specifically creating a second constant for the method that the popup and home page were relying on:

```rb
  def self.next_occurrence(event_type, begin_time = FRONT_PAGE_COLLECTION_TIME_PAST.ago)
```

Hacky as hell, but I wanted the site to be displaying sensible defaults overnight.  I got the hotfix on staging, but didn't try to replicate.  The site was still approximately working.  I wanted to know if this hotfix would work to solve our issue on production so I could go and have supper with the family.  The tests were green, so I pushed it out and it fixed everything without apparently introducing any catastrophic side effects.  I pulled the hotfix back into develop so others could work against it and discussed in the ticket how we wanted proper tests wrapping this and perhaps ultimately a whole different approach to the solution.

The site seems to be operating normally today and the developer has submitted a PR with a more comprehensive fix.  Why is it that there still seems to be things that we only encounter in production?  I think the acceptance tests do have some of our back, but we need to thoroughly follow up to wrap every end-user bug in an acceptance test, and keep reviewing the existing tests to purge vacuous ones and keep them up to date with the rest of the codebase, AND remove unecessary dependencies.  Even then I have a feeling that there's still always some issues that we'll only ever encounter in production ... :-)

###Related Videos:

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=E89Eo-k2rE8)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=NiVvxJuo8ik)
