---
title: AV EcoSystem Review Late Start
date: 2017-10-02
tags: 
author: Sam Joseph
---

So Monday morning was slow and I got to the computer late, and immediately got distracted by listening to the end of the Tech Done Right podcast, which mentioned the closing of DevBootcamp and IronYard coding bootcamps.  Shake up in the industry.  I want my focus for this week to be sorting out my ruby-fication spike of the AgileBot, but now I'm low on time.  I decided to focus on the clear-up from last week where I was updating the Premium Mob and Premium F2F pages on staging.  I have no comments on my PRs or in Slack, so I'm going to assume no one has any issues here.  I update those pages via the Mercury editor in the live site and take a stab at merging the backup copies into the main repo, but there's branch updating and merging to sort first ..., conflicts between my two branches.

So while they're building, can I make some super-quick progress on the agile-bot rubyfication to get the week started?  I update the branch there, pull up the set of changes.  I think the set of things we need is:

1) new tests of the SlackService
2) checking all existing tests pass
3) extracting the channel config to depend on deployment instance

In the meantime I used my admin privileges to force merge the Premium mob page backup PRs - they don't affect the codebase so waiting for the semaphore build is silly.  That gives us a nice clean "Please Check" column on the waffle board:

![](https://dl.dropbox.com/s/zpg8yqzayk08t8z/Screenshot%202017-10-02%2010.36.22.png?dl=1)

where we can also see my agile-bot spike in progress.  I really want to do another high level overview, but the AV Community meeting on Friday reinforced my notion that we have to clear up the bot messaging in Slack #general.  At the moment, all the pings about meeting starting with Google hangout hash ids in urls etc. looks really messy. 

![](https://dl.dropbox.com/s/5tbperpnc3nqsvu/Screenshot%202017-10-02%2010.42.54.png?dl=1)

A new member in the AV Community meeting was saying we should do our best to make everything as welcoming as possible.  What I really want is hyperlinks to the meetings where we say something like "Meeting starting soon, newcomers welcome :-)"  And this rubyfication of the agilebot could get that for us this week if I'm lucky.  The existing SlackService failing tests give me a good entry point for tomorrow AM if I can get to the computer earlier:

```sh
Failures:

  1) SlackService.post_yt_link sends a post request to the agile-bot with the proper data
     Failure/Error: elsif hangout.type == "PairProgramming"
     
     NoMethodError:
       undefined method `type' for #<EventInstance:0x007f9a4d36c5b0>
     # /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/activemodel-4.2.8/lib/active_model/attribute_methods.rb:433:in `method_missing'
     # ./app/services/slack_service.rb:62:in `post_yt_link'
     # ./spec/services/slack_service_spec.rb:52:in `block (3 levels) in <top (required)>'

  2) SlackService.post_hangout_notification does not fail when event has no associated project
     Failure/Error: CHANNELS[project.try(:slug).to_sym]
     
     NoMethodError:
       undefined method `to_sym' for nil:NilClass
       Did you mean?  to_s
     # ./app/services/slack_service.rb:51:in `channel_for_project'
     # ./app/services/slack_service.rb:13:in `post_hangout_notification'
     # ./spec/services/slack_service_spec.rb:27:in `block (3 levels) in <top (required)>'

  3) SlackService.post_hangout_notification sends a post request to the agile-bot with the proper data
     Failure/Error: send_slack_message client, CHANNELS[:general], "#{hangout.title}: #{hangout.link}", hangout.user
     
     NoMethodError:
       undefined method `link' for #<EventInstance:0x007f9a4d983458>
     # /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/activemodel-4.2.8/lib/active_model/attribute_methods.rb:433:in `method_missing'
     # ./app/services/slack_service.rb:25:in `post_hangout_notification'
     # ./spec/services/slack_service_spec.rb:19:in `block (3 levels) in <top (required)>'

Finished in 1 minute 3.18 seconds (files took 12.25 seconds to load)
899 examples, 3 failures, 1 pending
```

### Related Videos:

* [AV Community Meeting](https://youtu.be/ufWc1Gg3hQ4)






