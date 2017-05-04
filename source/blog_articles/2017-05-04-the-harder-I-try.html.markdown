Sometimes it feels like my life is one of those dreams where you're running as hard as you can, but you're not moving forward.  Yesterday I fixed two sticky technical issues that have been bugging us for a while, I got out an updated video for the AV102 course and enjoyed an elixir mob run by two of our Premium Mob members, but still didn't feel like I'd made much of a dent in the old ToDo list.  Let me tell you about the first bug in our AgileVentures WebsiteOne Rails code.  Ideally I wouldn't be spending two hours tracking down and fixing that bug, as the impending release of the MOOC next week meant that the final week of the AV102 TA training course needed to go live sharpish.  However one of the videos in the final week was still talking about the Hangout plugins and they've been discontinued and there really needed to be an explanation of how to take AV events live manually.

However, for the past couple of weeks on the odd day here and there we'd been getting 500 errors on the main events listing page.  It tended to come and go, and seemed more likely to occur on Mondays and Tuesdays.  Part of re-recording the video involved showing things appearing in the event list, and it seemed to me that not having the events index page working was a block on recording the video which should probably show that event list working.  That's not to say that the events list bug wasn't also a priority fix, but I'm kind of drowning in high priority items at the moment.  Anyway, I resolved to have a good stab at getting the bug fixed.

I'd previously hypothesized that the bug might be related to some third party website we were relying on for timezone conversions, based on some of the different error messages we were seeing in the logs associated with the 500 errors.  We were getting different errors in different parts of the code base, but they all seemed to be related to time conversion:

 ```
/app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.7.1/lib/active_support/core_ext/time/zones.rb:56:in `find_zone!'
```

and:

```
/app/vendor/bundle/ruby/2.3.0/gems/activerecord-4.2.7.1/lib/active_record/attribute_methods/time_zone_conversion.rb:24:in `convert_time_to_time_zone'`
```

The fact that they were intermittent and changing started to make me think (as Michael had suggested) that this was a resource issue.  I wasn't getting any Google hits for these kinds of errors, so I tried upgrading our plan on Heroku, but that didn't have much impact.   We'd just cloned the production data to staging and I saw we were getting similar errors there, but not on develop.  I was considering cloning to develop, when I ran locally against an older version of the production data - things were okay.  I cloned the production data locally and the events page was taking >20 seconds to load.  Whatever the issue was, something about the data was influencing the problem.  We have 20second timeouts on Heroku after which web queries are just shut down.  I speculated that some individual events might have corrupt timezones or something, but deleting a few had no impact on the time locally.

I thought maybe I should do more to replicate the production setup locally, but I knew that was a pain, and I had basically the same issue locally, although without the error being thrown since I have longer timeouts for http requests on my local machine.  The Rails logs on my local machine were showing that lots of time was being spent in the request, but actually the time spent constructing the view and doing activerecord queries were relatively small:

```
Completed 200 OK in 35596ms (Views: 359.4ms | ActiveRecord: 80.0ms)
```

Looking more closely I could see that there appeared to be a 2 to 3 second wait after every query on the event instances table, which I traced into this method:

```rb
  def self.remove_past_events(events)
    events.delete_if {|event| !event[:event].event_instances.last.try(:live?) &&
                           (event[:time] + event[:event].duration.minutes) < Time.current}
  end
```

playing with the query on the console I saw that as the number of eventinstances increased for an individual event, this method would get slower and slower. Some of the scrums had over 400 event_instances, and the daily scrums appear multiple times in the list so we were repeating many slow operations.  I experimented with replacing `delete_if` with `reject` but the method just got slower.  Then, looking more closely at the boolean statement I saw that the db query was in the first portion, and not the second, and actually the time check in the second half of the boolean would probably be false most of the time.  I tried switching the order of the boolean like so:

```rb
  def self.remove_past_events(events)
    events.delete_if {|event| (event[:time] + event[:event].duration.minutes) < Time.current &&
                           !event[:event].event_instances.last.try(:live?)}
  end
```

and suddenly the problem evaporated.  Most queries were now not checking for event instances at all, and in relatively short order I had the fix deployed and the main site event lists page was loading in just a few seconds.  It's still not as fast as I would like, but it got us out of the woods and unblocked me for re-recording the AV102 video.

Then I was into an Elixir mob, and since two Premium Mobbers had taken the lead on running the session it was less intense effort from me and I'd been able to skip preparation the previous day.  We talked about starting a React Mob the following day and the question came up about whether Premium Mob membership covers attending more than one Mob per week.  My response that we've been somewhat flexible with that perhaps shows that I'm not really cut out for being a businessperson.  I just want everyone to have access to all materials :-(  It's weird, because even with payment not every Premium Mob member shows up to the sessions they have in principle paid for.  There seems like a higher level of committment from the Premium Mobbers than overall in the rest of the community, but we have the conundrum that their activity is less visible as we're running the Mobs in private events.  Gosh, just what is the model that will make this all work and be sustainable?  Having Premium Mob events show in our events stream, but only be accessible by those who've paid, with the stream of every fifth session being available for others to see what goes on?

So after lunch (and React Mob prep with Stephen Grider's kindly donated React/Redux course) and besides the AV102 pivotal tracker voting sessions (which is another blog post), I was impaling myself on the set up for the Slack slash command for AsyncVoter on Azure/Dokku.  We'd spent multiple sessions struggling with this and kept hitting a 503 error with no extra information.  The system was all working fine on Digital Ocean with dokku (where Arreche had set it up), but even after getting SSL working on the Azure/Dokku system we were still stuck.  No one showed up for the AsyncVoter kick off so I spent the half hour quietly deleting the previous app in Slack and creating a new one.  The whole setup is rather involved, and I think I got it fully documented at https://github.com/AgileVentures/asyncvoter-slack-command/wiki but I think ultimately the sticking point was how many layers you have to dig down to find the key URL for a slack app.  Here's the first screen:

![Slack App basic information](https://www.dropbox.com/s/2eveqtmb4waywis/Screenshot%202017-05-04%2010.13.48.png?dl=1)  

Now previously I'd worked out what we need to click on things like "interactive messages", "OAuth & Permissions" etc. on the left nav bar.  I find it odd that there is configuration hidden behind those links, that look like to me like they would just be descriptions of the "features".  I usually expect configuration to across the vertical nav, or to look more like buttons, but anyway, we'd previously ensured all the "interactive messages", "OAuth & Permissions" URLs were updated to SSL, but there's another.  Clicking on the "Building Apps for Slack", reveals another control panel:

![revealed control panel](https://www.dropbox.com/s/9zqr9ncli5umymu/Screenshot%202017-05-04%2010.17.07.png?dl=1)

and all of this is with wording about doing things, not configuring them.  It was through the process of recreating the app from scratch that I discovered the other place that the URL needed to be updated.  Inside the slash command setting:

![slash commands](https://www.dropbox.com/s/ay2gjyn4sfvuidg/Screenshot%202017-05-04%2010.18.36.png?dl=1)

there's the option to edit, which reveals another URL that needed to be edited:

![](https://www.dropbox.com/s/qzbwmqxiprjz2wq/Screenshot%202017-05-04%2010.19.13.png?dl=1)

Unless you know it's there, it's not very discoverable.  Just writing this blog I see I can navigate to the top page via the "Slash Commands" on the left "Features" navigation links.  In our previous group sessions I think we'd been to that page repeatedly, but never realised that by editing the slash command we'd reveal another URL that needed updating.  I'd say the key usability issue here is that the summary of the slash command before you edit, does not show that there are another three variables open for editing there, i.e. the RequestURL, ShortDescription and UsageHint.

Of course now I know and I've blogged about it, but there we go.  The next step is now to ensure we can set up subdomains so that we can have a staging instance and a production instance; I have an idea about how to do that, but it might take 5 minutes, or I might get stuck on it for two hours.  I have to say that moment to moment I do prefer coding and debugging to trying to record videos for courses, or even just setting up and organising courses.  Now it's May 4th and soft launch for the NHS wiki and I've got to work out which of the list of fixes and changes to prioritise:

* SSL setup for Azure with bitnami mediawiki stack
* Getting the Watchlist emails working
* Getting the full banner/logo in there

While fighting for my attention are

* Getting the rest of the MOOC accessibility fixes done
* Getting all the AV102 TA trainees access to the course as betatesters

and further follow up on WebsiteOne, LocalSupport, AsyncVoter; catching up with our sponsor drie etc. etc.  Like I say, the harder I try to run, the more it feels like I'm just staying in the same place :-)

### Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/edit?o=U&video_id=4DAU-nFUP-U)
* [Starting Hangouts via AV](https://www.youtube.com/edit?o=U&video_id=kS3ttK6Wuxw)
* ["Kent Beck" scrum](https://www.youtube.com/edit?o=U&video_id=JK9i1NCuHno)
