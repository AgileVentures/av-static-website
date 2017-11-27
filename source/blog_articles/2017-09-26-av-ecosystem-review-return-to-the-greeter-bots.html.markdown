---
title: AV EcoSystem Review Return to the Greeter Bots
date: 2017-09-26
tags: 
author: Sam Joseph
---

![bots](../images/bots2.jpg)

I had no comments on the multiple source repository pull request (apart from my own), so I've merged that to play with on develop.  I want to pop back all the way to up to reviewing the flow of folks through AgileVentures and my experiments with the getting started pages, but there's a couple of stops on the way.   First is to get the new code of conduct more prominent, and sort out the greeter bots; as well as finishing up the changes to the Premium Mob/F2F pages.

So let's start with the greeter bot, which should mention the code of conduct, I think.  First up I'll merge in the simple unit tests I added previously:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (master)]$ 
→ npm test

> greeter_bot@1.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/greeter_bot
> mocha test/**/*.js

  Bot
    ✓ should include linked techtalk channel in greeting
    ✓ should include linked new_members channel in greeting
    ✓ should include linked random channel in greeting

  3 passing (9ms)
```

They're all passing on master, and now I can test drive the addition of the code of conduct link:

```js
  it('should include link to our code of conduct',function(){
    expect(greeting).to.include('<https://github.com/AgileVentures/AgileVentures/blob/master/CODE_OF_CONDUCT.md|code of conduct>')
  })
```

which fails:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (6_add_code_of_conduct)]$ 
→ npm test

> greeter_bot@1.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/greeter_bot
> mocha test/**/*.js

  Bot
    ✓ should include linked techtalk channel in greeting
    ✓ should include linked new_members channel in greeting
    ✓ should include linked random channel in greeting
    1) should include link to our code of conduct


  3 passing (14ms)
  1 failing

  1) Bot should include link to our code of conduct:
     AssertionError: expected 'Welcome and great to have you with us! Please do introduce yourself in <#C02G8J689|new_members> (if you haven\'t already), and if you have any general technical thoughts/issues/questions please ask in <#C02AA0ARR|techtalk> - <#C0285CSUH|random> is for everything else :slightly_smiling_face:\n\n\nbtw, are you interested in React, Elixir or RSpec?  We\'re offering a <https://www.agileventures.org/premium-mob-offer|free Premium mob programming session> to anyone who likes us on facebook.com/agileventures or follows us on twitter.com/agileventures ...' to include '<https://github.com/AgileVentures/AgileVentures/blob/master/CODE_OF_CONDUCT.md|code of conduct>'
      at Context.<anonymous> (test/greeting_test.js:20:25)

```

greeting.js updated:

```js
greeting += "\n\n\nPlease help us make AgileVentures a welcoming place by following our <https://github.com/AgileVentures/AgileVentures/blob/master/CODE_OF_CONDUCT.md|code of conduct>"
```

and the tests pass:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (6_add_code_of_conduct)]$ 
→ npm test

> greeter_bot@1.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/greeter_bot
> mocha test/**/*.js

  Bot
    ✓ should include linked techtalk channel in greeting
    ✓ should include linked new_members channel in greeting
    ✓ should include linked random channel in greeting
    ✓ should include link to our code of conduct

  4 passing (9ms)
```

So now it's a bit of a pain to do a complete manual test of this as it requires creating a new dummy user and signing up to a slack instance where this bot is installed.  So I just push to production from the branch:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (6_add_code_of_conduct)]$ 
→ git push -f azure-production 6_add_code_of_conduct:master
```

A nice feature would be to be able to chat to the bot and get it to spit out the greeting at will, which would be handy for checking the format, if not the complete cycle of the functionality.  I'll add that to the issues as I've been doing with every other thought as it comes up:

* [Support messaging to bot](https://github.com/AgileVentures/greeter_bot/issues/17)
* [Improve Templating](https://github.com/AgileVentures/greeter_bot/issues/16)
* [Follow Standard JS](https://github.com/AgileVentures/greeter_bot/issues/15)

but for now I have to wait for someone to join our slack instance to see the effect of the current change.  In the meantime I should get the link to the code of conduct into our Getting Started ... (also thinking that maybe we should migrate it from GitHub to be on our site), but one thing at a time.

![](https://dl.dropboxusercontent.com/s/49uuoke3uap9kpp/Screenshot%202017-09-26%2009.59.43.png?dl=1)

I'd also love to update the project greeter bot to work with the new shared #wiki_edu_dash_collab channel that links our Slack to the WikiEdu folks.  There also I now have tests that are passing:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (master)]$ 
→ npm test

> project_greeter_bot@0.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/project_greeter_bot
> mocha test/**/*.js

  Project Greeter bot
    ✓ should have LocalSupport channel greeting
    ✓ should have WebSiteOne channel greeting
    ✓ should have Rundfunk Mitbestimmen channel greeting
    ✓ should have Wiki EDU dashboard channel greeting

  4 passing (12ms)

```

A test failed and passed, since I am using the channel ids in tests:

```
tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (25_update_wiki_edu_channel)]$ 
→ npm test

> project_greeter_bot@0.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/project_greeter_bot
> mocha test/**/*.js

  Project Greeter bot
    ✓ should have LocalSupport channel greeting
    ✓ should have WebSiteOne channel greeting
    ✓ should have Rundfunk Mitbestimmen channel greeting
    1) should have Wiki EDU dashboard channel greeting

  3 passing (11ms)
  1 failing

  1) Project Greeter bot should have Wiki EDU dashboard channel greeting:
     AssertionError: expected { Object (C0KK907B5, C029E8G80, ...) } to have own property 'C36MNPWTD'
      at Context.<anonymous> (test/greetings_spec.js:17:35)

npm ERR! Test failed.  See above for more details.

[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (25_update_wiki_edu_channel)]$ 
→ npm test

> project_greeter_bot@0.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/project_greeter_bot
> mocha test/**/*.js

  Project Greeter bot
    ✓ should have LocalSupport channel greeting
    ✓ should have WebSiteOne channel greeting
    ✓ should have Rundfunk Mitbestimmen channel greeting
    ✓ should have Wiki EDU dashboard channel greeting


  4 passing (9ms)

```

Would be good to be [looking those up off the slack api](https://github.com/AgileVentures/project_greeter_bot/issues/27), maybe?  Anyhow, I deployed that branch to production, since the change in the id will only make sense on production, and we'll have to wait for someone to join the new shared channel and see ...

Phew, so that's all the hanging bot stuff deployed (if not checked), and reminds me of all the little other tasks I'd like to do with the bots to make them and their projects friendlier, but I think that's for another week - tomorrow I hope to be on to the Premium pages ...
