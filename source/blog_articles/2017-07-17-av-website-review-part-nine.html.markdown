---
title: AV Website Review Part 9
date: 2017-07-17
tags: 
author: Sam Joseph
---

![times up](/images/expired.jpg)

So no one joined the LocalSupport channel over the weekend and thus there's no immediate chance to see the new project greeting bot in action.  Other project maintainers have come back to me with mixed responses.  Some enthusiastic, some more cautious.  I also saw there are some [special keys for testing "reCaptcha"](https://developers.google.com/recaptcha/docs/faq), which makes me feel like I should put some more effort into that spike.

However, I think what I really want is to update the project greeting bot so that it supports multiple projects, mentions the person by name and even has a pause before it posts.  Force of habit--I just dropped a load of issues in the repo for the bot:

* [mention name of member who joins channel](https://github.com/tansaku/project_greeter_bot/issues/1)
* [add pause to post so it's not quite so fast](https://github.com/tansaku/project_greeter_bot/issues/2)
* [support multiple projects](https://github.com/tansaku/project_greeter_bot/issues/3)
* [could this all be done with a slash command?](https://github.com/tansaku/project_greeter_bot/issues/4)
* [tests](https://github.com/tansaku/project_greeter_bot/issues/5)
* [easier in ruby?](https://github.com/tansaku/project_greeter_bot/issues/6)

So it's tempting when thinking about supporting multiple projects to put a database or caching system in there, but a hash in the code will do for now.  And I start to want to put a testing framework in there, but I know I won't be able to easily round-trip the whole thing, so I do a manual test on the low level stuff in node, and on the high level stuff in a second Slack instance.  It all seems to work.  I could deploy.  I've got one free-tier member asking for a simple project to get set up on - it could be this one ...

I've got to get the autograders working on EC2 and so many other things.  I can't allow myself more than another 15 minutes on this.  I think I deploy.  Again by force of habit I submit a PR:

[https://github.com/tansaku/project\_greeter\_bot/pull/7](https://github.com/tansaku/project_greeter_bot/pull/7)

There's no CI yet, but it allows me to attach the ticket to the issue.  I could deploy that branch directly ...?  That actually provides a great route for getting out of trouble.  Will do that.  While that builds I could quickly make a test for the reCaptcha stuff ...?  I satisfy myself with creating a ticket:

[https://github.com/AgileVentures/WebsiteOne/issues/1735](https://github.com/AgileVentures/WebsiteOne/issues/1735)

and the new bot is deployed, and I'm thinking of other features:

* [avoid re-posting to the same person](https://github.com/tansaku/project_greeter_bot/issues/8)
* [avoid posting if we've recently done so](https://github.com/tansaku/project_greeter_bot/issues/9)
* [should we prefer to DM people the intro](https://github.com/tansaku/project_greeter_bot/issues/10)
* [allow people to request instructions](https://github.com/tansaku/project_greeter_bot/issues/11)

so many things that might or might not be useful ... anyway, times up ...
