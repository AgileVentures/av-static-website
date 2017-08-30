---
title: Hosting Slack Bots
date: 2016-11-29
tags: AWS, BeepBoop, Lambda, autograders, tmux, docker, timeout
author: Sam Joseph
---

![bots](/images/bots.png)

The slackbot had stayed live for most of the weekend on drie push, but on Monday morning it was unresponsive, and although we'd completed a vote or two, not all the notifications had come through.  The bot would not restart from fresh pushes of the code.  I couldn't get it back online without ssh-ing in and starting it directly.  drie push is not explicitly designed for hosting slack bots, and there wasn't any functionality to put the bot into a background job that would persist after logging out of ssh.  Actually I'd been surprised that the slack bot had worked on drie push at all; I'd pushed it up there as an experiment and seen that it worked partially, but now we needed a more consistent hosting solution.

Now that I'd shown the #websiteone channel the promise of a bot that could run an asynchronous vote, there was less appetite for starting manual ones, and not having the stable hosted bot was starting to feel like a blocker.  Slack has a [page dedicated to different bot hosting solutions](https://api.slack.com/docs/hosting). In parallel with chatting on Slack with the team about the options, I spent a little while looking at what AWS Lambda had to offer, and then Matt suggested that BeepBoop looked like it had a great free offering.  I signed up for a BeepBoop account and got through most of pulling our repo asyncbot directly into beepboop, but then it was failing due to lack of a DockerFile:

```
unable to prepare context: Cannot locate Dockerfile: absDockerfile: "/tmp/b54c5a30aa584533b027af837beeef64683410820/repo/Dockerfile"
```

There was probably a simple solution, but I couldn't be sure.  I felt like I could have this bot up and running on AWS pretty quickly and I wouldn't be beholden to some other service that was going to start squeezing me on price like Heroku has been doing recently.  The only reason I hadn't immediately spun up an AWS instance was that whenever I tried to go to the AWS console in Chrome it was auto-switching to the saasbook team account, and I didn't want to create some instance that Armando would start paying for.  I went to Safari (I know you can have chrome profiles, but ...) and created a new AgileVentures AWS account and created a free-tier eligible tiny Ubuntu instance and had that up in a matter of minutes.  In the meantime Arreche was telling me he could get the planning poker script up in hubot pretty fast, but I really wanted to see how far I could get with AWS.

I set up the ssh config:

```
# ~/.ssh/config

Host AsyncVoterBot
HostName ec2-54-999-999-999.us-west-2.compute.amazonaws.com
User ubuntu
IdentityFile "~/.ssh/SuperSecretKey.pem"
```
I needed to make it super easy to log into the box:

```
$ ssh AsyncVoterBot
```

where I installed git, nodejs and npm using `apt-get install` (although I burned some time mistakenly installing `node` and then trying to build node from source) before cloning our async slack bot project, setting the Slack bot token in the environment and running the bot in a tmux session. 

```
$ tmux
```

```
# in tmux environment
$ nodejs server.js &
# Ctrl-B D to detach
```

```
$ tmux attach # to get back in
```

I was imagining I would have to do some security configuration to get it all to work, but no such thing.  The bot was live, and I ran some test votes in a private channel.  I had the tmux session (just as we have on the autograders) so that the bot can stay live and running even after I log out of the machine, and I checked that the bot stayed live over a few ins and outs.  It's still running today, and although my attempts to ssh into it appear to be [timing out](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html#TroubleshootingInstancesConnectionTimeout), I assume that's a temporary AWS issue (fixed with adding an inbound rule for my new IP).  There's no cost for the bot so far, but that will be the real question, how much does this cost us?  I was able to link the new AgileVentures AWS account to the new AgileVentures bank account.  I sort of assume that this will come in less than the $7 a month that Heroku want for an always-on instance, but let's see.

I'm not sure if this is the right overall approach, but I was pleased to get something up on AWS that has a chance of being an affordable solution, and I'm pretty confident in my ability to manage AWS boxes given my experience with the autograders.  We managed two or three bot scaffolded votes on #websiteone and there were others to do in parallel in #async_voter so I did a manual one there which pushes us towards new features like supporting multiple votes in parallel, and getting a test harness round this hacky botkit code.  More voting and coding to be done!

###Related Videos:

* ["Martin Fowler" Scrum](https://youtu.be/WWesZmC_BRU)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=AmREoIUHw4c)
 
