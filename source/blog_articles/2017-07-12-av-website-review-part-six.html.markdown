So my spike of adding ReCaptcha to the sign up page went pretty well.  It all seems to be passing in CI.  I was expecting lots of the sign up tests to break, but they all passed.  I guess because they don't have javascript enabled?  It tempts me to get this merged in and into the flow.  Or should I be strict and get more detailed tests around them?  I switched on JavaScript and got this error:

```
    Given I am not logged in      # features/step_definitions/user_steps.rb:59
      undefined method `submit' for #<Capybara::Poltergeist::Driver:0x007fc4b81becf0> (NoMethodError)
```

which appears to derive from Poltergeist not supporting the sign out step below

```rb
When /^I sign out$/ do
  page.driver.submit :delete, destroy_user_session_path, {}
end
```

So one could spend time converting the tests for the logout, directly (at some test running time cost), and/or investigating how the Poltergeist driver might do the same thing.  It reminds me of a conversation I had with Lokesh the other day about what we want from our tests, that they:

1. run fast
2. are reliable
3. catch bugs
4. are quick to put together

The difficulty is getting all these four together.  There's also the question about getting testing at the different levels of acceptance, integration and unit, and the level of black-boxiness they operate with.  Of course my new resolution is to focus my mornings on actually making changes to the website (and surrounding ecosystem), rather than just blogging about the generalities.  I think there's probably a concise blog post on the subject (and maybe I could generate it), but priorities.  Of course my mind is scattered from the intense discussions about microservices for the AsyncVoter project, and so I had resolved this morning to look at another part of the AgileVentures ecosystem, which is the little greeting bot that I have running in node on an Amazon EC2 instance.  Thinking about the funnel of folks coming into AgileVentures, we have something like:

1. See Google AdWord or hear about us on podcast or through MOOC
2. Land on AV website home page
3. Some set of navigation steps
4. Land on the AV Sign up page (hopefully soon with captcha)
5. Sign up

Now after the sign up point, the member is redirected to the Getting Started page, which I just think should mention that a Slack invite has been sent.  Perhaps a flash notice to let them know to check their email for the Slack invite?  One that doesn't dissappear to fast.  Maybe the sign up page itself should say that a slack invite will be sent?  I created an issue for that:

https://github.com/AgileVentures/WebsiteOne/issues/1724

But is that just re-arranging deck chairs?  So then there's the ongoing flow of how and whether the member works through the "getting started" page.  There's also the experience they have in Slack.  It's frustrating that we can't have an integrated experience where the Slack "chat" and website "documentation" are both available in parallel.  Anyhow, what we do have is this automated greeting when the user joins the Slack instance, which comes from my avatar and looks like this:

![automated greeting](https://dl.dropbox.com/s/uibnox5ijldnhz8/Screenshot%202017-07-12%2009.50.29.png?dl=1)

Now it's not ideal that again we're directing folks to a third party site (GitHub), but that partly reflected my frustration with editing in Mercury.  Part of me wants to tear Mercury out of WSO and replace it with a markdown system, or have pages in WSO pull in from GitHub or something, but is the simpler thing to just try getting that page into Mercury?  Or would it be better to be pointing the new members directly to our AV "Getting Started" page?  So I did [that](https://www.agileventures.org/joining-a-project), and it looks okay, but the contents section still points to GitHub.  I don't know how well we support in page anchors.  It's these little things that make GitHub's markdown so appealing.  Do we replicate?  Point to?  Not sure.  Either way, I'm struck by "LOTS OF TEXT" which is what our design sprint is pointing us away from ...

Anyway, we're also paying Amazon, monthly, a small amount for running this bot when we could be doing it cheaper on Azure, so I want to move that over.  Also Federico is keen for me to add the special offer about a free Mob session for liking us on Facebook or following us on Twitter.  I also want to get it set up so others can edit it ... let me at least try and remember how to access that code.  It's in this repo:

https://github.com/AgileVentures/greeter_bot

Technically the bot is completely trivial.  I can't immediately access it due to my IP address having changed - so it's time to add the new one to EC2 security; and I'm in.  It's just a git repo on a linux box and trivial to update.

What's interesting is that this has been running for six months--no trouble and no tests.  So anyway, I just ended up attempting to deploy the greeter bot on Azure/Dokku, which basically involved:

```
git remote add azure-production dokku@agileventures.eastus.cloudapp.azure.com:greeterbot-production
ssh dokku@agileventures.eastus.cloudapp.azure.com apps:create greeterbot-production
ssh dokku@agileventures.eastus.cloudapp.azure.com config:set greeterbot-production GREETER_SLACK_BOT_TOKEN=XXXXXXX
git remote 
git pull origin master
git push azure-production master
```

in the greeter bot directory locally. And maybe that's working?  The logs look promising:

```
→ ssh dokku@agileventures.eastus.cloudapp.azure.com logs greeterbot-production
2017-07-12T09:14:08.250109458Z app[web.1]: 
2017-07-12T09:14:08.250159913Z app[web.1]: > greeter_bot@1.0.0 start /app
2017-07-12T09:14:08.250165992Z app[web.1]: > node server.js
2017-07-12T09:14:08.250169031Z app[web.1]: 
2017-07-12T09:14:08.938145069Z app[web.1]: info: ** No persistent storage method specified! Data may be lost when process shuts down.
2017-07-12T09:14:08.964844990Z app[web.1]: info: ** Setting up custom handlers for processing Slack messages
2017-07-12T09:14:08.978923602Z app[web.1]: info: ** API CALL: https://slack.com/api/rtm.start
2017-07-12T09:14:09.788834373Z app[web.1]: notice: ** BOT ID: tansaku ...attempting to connect to RTM!
2017-07-12T09:14:09.853519358Z app[web.1]: notice: RTM websocket opened
```

of course, we may have the same issue that we had on drie that the container sleeps and the websocket dies, and maybe I should have gone for the (simpler?) option of just deploying an ubuntu box on Azure and copying the thing over there ...  I've shut down the EC2 bot and so we'll run this experiment.  The greeter bot is just waiting for team_join events, which I'm not sure how to simulate, and really the question is will docker/azure work in full round trip with real users on Slack.   Of course I could do a direct test on a secondary Slack instance and create a new user to see - actually I could create a new user with some random email address on the main instance, although that means messy test users cluttering up the main Slack. 

I guess I just wait for someone new to join - I can always post them the message myself if the Azure/Docker deploy doesn't work.  I've burned enough time this morning and it's time to move on to admin/Slack/Emails.  Would have been cleaner to start in test Slack instance, but now the experiment's in progress ...

If it works Federico and others can submit PRs to change the greeter bot message and even deploy themselves ...
