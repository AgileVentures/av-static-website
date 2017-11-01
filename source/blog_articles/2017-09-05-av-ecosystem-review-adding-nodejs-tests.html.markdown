---
title: AV EcoSystem Review Adding NodeJS Tests
date: 2017-09-05
tags: 
author: Sam Joseph
---

Argh, so I got distracted by Slack and blogging and twitter and even though I sat down at the computer at 9am it's 10am.  At least I got a few important emails and things out of the way (I think), but so after making a slew of changes to the Slack greeter bots, I feel I can't put off adding test frameworks any longer.  There's some bugs going out that I can't catch with simple automated tests (the way messages get formatted in a real slack instance), but I've definitely wasted a little time on some silly bugs that automated tests would have caught.  In the meantime I can see that the new changes to the syntax for the main greeter bot are all in place:

![](https://user-images.githubusercontent.com/30216/30053660-798a6e8c-9221-11e7-927d-ea20f462cc55.png)

and similarly for usernames in the project greeter bot:

![](https://user-images.githubusercontent.com/30216/30053820-fc5adbee-9221-11e7-8287-2934a0cd501c.png)

although I note that even the bot mention of my own name in a channel does not appear to have created the kind of highlight I would like to see in the channel notifications (i.e. the number in the dot to indicate one mention of one's name)

![](https://user-images.githubusercontent.com/30216/30053947-5a4bb106-9222-11e7-96de-eb3a1b66fddc.png)

but at least we've got an automated way of giving new users the message that they should flag the maintainer's name, in order to get attention.  I was hoping that simply mentioning the maintainer in the bot message might have this effect, but not so.  If the user follows the instruction to flag the maintainer in any question, it should be fine, but some people don't understand that part of flagging in Slack - I'm looking to ensure that any question from a new person joining the channel gets noticed, but I guess I'm pushing too hard on a corner case ...

Anyway, as the number of features increases the need for tests mounts, and I think part of the problem here is that I haven't yet mentally automatized the setup of tests in a NodeJS project.  I've done it so many times for Ruby that I can set up an RSpec or Cucumber suite from scratch without referring to any docs.  I have done it for NodeJS, but not enough that it's second nature and it's funny how a small thing like that can get in the way of getting something done.  Maybe I'll need to athletic repeat that a few more times - I did do it every day for a couple of weeks in 2015, but not enough apparently ... :-)

What's so hard--where do I start?  Get a branch checked out for the issue I have on adding tests:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (master)]$ 
→ git checkout -b 4_add_tests
Switched to a new branch '4_add_tests'
```

install mocha:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (4_add_tests)]$ 
→ npm install --save-dev mocha
```

make a test directory and create a test:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (4_add_tests)]$ 
→ mkdir test
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (4_add_tests)]$ 
→ touch test/server_test.js
```

create a test file:

```js
var assert = require('assert');

describe('Bot', function(){
  it('should include channel names in message',function(){
    assert.equal(0,0);
  });
});
```

update package.json

```json
{
  "name": "greeter_bot",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "dependencies": {
    "botkit": "^0.4.3"
  },
  "devDependencies": {
    "mocha": "^3.5.0"
  },
  "scripts": {
    "test": "mocha test/**/*.js"
  },
  "author": "",
  "license": "ISC"
}
```

and that works:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/greeter_bot (4_add_tests)]$ 
→ npm test

> greeter_bot@1.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/greeter_bot
> mocha test/**/*.js



  Bot
    ✓ should include channel names in message


  1 passing (6ms)

```

I know how to do this - why was I hesitating? :-) But so now there's not much to test in this project yet, at least not without some sophisticated mocking, which I'll leave for later.  Let me at least check some trivial thing about the message being sent with the following code:

```
var chai = require('chai');
var expect = chai.expect;

var greeting = require('./../lib/greeting');

describe('Bot', function(){
  it('should include linked techtalk channel in greeting',function(){
    expect(greeting).to.include('<#C02AA0ARR|techtalk>');
  });

  it('should include linked new_members channel in greeting',function(){
    expect(greeting).to.include('<#C02G8J689|new_members>');
  });
  
  it('should include linked random channel in greeting',function(){
    expect(greeting).to.include('<#C0285CSUH|random>');
  });
});
```

Okay, so I got a bit fancy and installed chai so I could work with the expect/include syntax I like, and then I got all like "this project needs a proper structure" and moved things around.  I think it still works.  With only a couple of unit tests and no acceptance tests, I may have completely broken the project - my little local test that it starts up may conceal other issues ... but at least I've broken down some little mental barriers.  Starting node tests from scratch and reminding myself how we go through re-structuring a node app.  Some progress today - on to the continuing review tomorrow ... or add tests to project_greeter_bot ... jump from level to level, enjoy, code!



