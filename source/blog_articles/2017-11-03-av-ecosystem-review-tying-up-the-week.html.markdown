So I didn't manage to get the `updated_at` -> `created_at` fix deployed in advance of the Kent Beck scrum yesterday, so I still can't say for certain that I have fixed the bug where the old youtube link gets posted on daily events.  I believe everything is deployed now, so we'll have a proper test of that today.  Gosh I really hope there isn't some other concealed bug.  I'd hoped to bash out a fix and quickly move on to other things, but it seems to have take all week.

That said I have got a "Premium Mob" ad added to the AdWords and although it's not live yet, I showed it in the marketing meeting along with a slew of other ideas that seemed to go down quite well.  Lara's going to configure the keywords and ad groups and get that out, so I can focus on other things.  Thanks Lara!  I haven't encountered any 500 errors trying to load events pages this week, but I also don't see any big improvement in the aggregate stats, weird.  Still that brings me back to the set of things I thought of from reviewing the waffle board earlier in the week:

* tawk.to site integrated chat system
* replacing the mercury editor so we can move to Rails 5
* supporting private events
* looking at long running cukes
* hosting our own jitsi
* making main page counter dynamic
* notifying project creator when people join project
* having getting started page tick off progress
* better modelling of premium users
* allowing premium users to change payment methods
* start hangout button not working ...

The "Start hangout" button feels particularly problematic as students from the MOOC will try to use it and I think it will just not work.  Perhaps a quick disabling of it is in order ... I'm also full of thoughts from the LocalSupport meeting yesterday and wonder if AV ecosystem review blogs should extend to cover the operational flow of projects like LocalSupport?

I think I'll get a quick something on it started ... checking the #websiteone channel I notice some voting has gone on in async votes I started during the week - that's cool.  I update the tickets with the votes (would be great if that happened automatically), and add some labels for "beginner-friendly" and "up-for-grabs", as well as assigning myself to the "remove start hangout on air button ticket".  I create a branch and hack out the button to replace it with:

```erb
 <%= button_to 'Start Hangout Instructions', 'https://support.google.com/youtube/answer/7083786' %>
```

which gives us a bit of a boilerplate start:

![](https://dl.dropbox.com/s/ailmb73picj4oxh/Screenshot%202017-11-03%2010.37.37.png?dl=0)

but at least it's a start ... will need some styling - actually that's not too difficult to add:

```erb
<%= button_to 'Start Hangout Instructions', 'https://support.google.com/youtube/answer/7083786', class: "btn btn-primary" %>
```

and I can also make it open in a new tab:

```erb
<%= button_to 'Start Hangout Instructions', 'https://support.google.com/youtube/answer/7083786', class: "btn btn-primary", form: {target: '_blank'} %>
```

So now we'll have to see what tests that all breaks, but really this needs to get out, since we've currently got a dangerous broken feature in production ...


