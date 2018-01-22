Still under the weather. Wondering if I need some sort of calendar/schedule booker to allow people to grab chunks of my time.  Had been trying to get several sets of people booked for paid mobs and F2F sessions in either email and slack and its all being a bit of mess.  Chuck from Ruby Rogues uses a booking system to organise when he has skype calls with people.  I'm loathe to give up executive control of who I'm talking to when.  

Anyhow, I'm finally getting the fix for the broken hangout link out on production for websiteone along with the modified messaging format for the slack notifications.  Thank goodness.  I've also got folks from the MOOC reporting not being able to find pair partners, which I think is due to the nastily broken hangout on air button.  Must post something to the forums and in the chat about that ...

What I really want to get on to is running this performance check on staging.  Ah, what I need is a feature flag that allows me to turn off the next scrum time banner on the home page ... although it's actually the pre-loading of the event that's the resource consuming component (I think) so I try with:

```rb
  def get_next_scrum
    @next_event = Event.next_occurrence(:Scrum) if ENV['GET_NEXT_SCRUM']
  end
```

in the application controller, and a quick check shows that that turns off the "next event" notifications not just on the homepage, but throughout the site.  Well definitely worth getting on staging for a test to see if removing that would give us a significant performance improvement.  However as a standalone change it will break some tests - I see the visitors controller spec is failing.  Path of least resistance is to probably move to the Rails config setup we have and mark it on by default for tests?

```rb
  def get_next_scrum
    @next_event = Event.next_occurrence(:Scrum) if Features.get_next_scrum.enabled
  end
```

and then in `config/settings/test.yml`:

```yml
features:
  get_next_scrum:
    enabled: true
```

but the following in `config/settings/production.yml` and `config/settings/development.yml`:

```yml
features:
  get_next_scrum:
    enabled: <%= ENV['GET_NEXT_SCRUM'] %>
```    

Quick tests with ENV var on and off locally make it seem like this is all working.  Okay, let's get it on staging and run some load tests ...

