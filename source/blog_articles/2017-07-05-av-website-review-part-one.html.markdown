Right, new dawn.  I could be blogging about all the conversations we had about testing yesterday, but new resolution is to get the AV website sorted out in terms of usability, readability and user flow one day at a time.  Immediately I'm torn between two sections.  There's the "Getting Started" page that people either browse to, or get redirected to after sign in.  After discussions in the AV community meeting on Monday I removed the first two sections of that document:

![](images/Screenshot%202017-07-05%2009.29.00.png?dl=1)

These two sections are all things we'd like users to do, i.e. get set up with GitHub and Slack (and other things) but the danger is that the very first thing we are doing is directing folks away from our site.  The likelihood is that they signed up via GitHub and they've received a Slack invite.  It's all very well asking folks to work through a set of administrative tasks when they are a "captive" audience, such as having already paid to take a class, but really we need to get people as quickly as possible to the higher engagement, fun activities, such as looking through the different active projects.  Anyway, I shouldn't belabour the point, but it's interesting to look at the flow through from Google Analytics for this page:

![](images/Screenshot%202017-07-04%2012.01.38.png?dl=1)

What we see there is a lot of drop offs and not many people clicking through on individual project pages.  The text associated with the first two "administrative" sections was forcing the table of active projects "below the fold", i.e. making it something that people would have to scroll to get to.  Even so, the Google Analytics plugin for Chrome shows us what percentage of people are clicking through on which projects:

![](images/Screenshot%202017-07-05%2009.35.10.png?dl=1)

So anyway, I removed the first two sections and we'll see if there is any change.  I think there's still room to reduce the amount of text, improve the layout and add graphics through this document, and so I'm torn about making more significant changes.  Particularly since it will take some weeks before we can really tell if there's a difference.  We hadd 168 visits to the "Getting Started" page in the last month.  In addition I also want to make progress on the Premium page.  Previously Matt and I had worked on things to that when signed in users clicked on "Upgrade to Premium for additional support" on the home page, that instead of going to the payment page (with no information about premium) that it went to the description page for Premium, which made a lot more sense to me.

There's functionality in the Google Analytics Chrome plugin to compare a period with the previous month, and I can see that there's been a small increase in people clicking on the "Upgrade to Premium for additional support".  We just redesigned the banner, but that only just went live, so we don't have a clear indication for that increase:

![](images/Screenshot%202017-07-05%2009.48.57.png?dl=1)

Looking through at the Premium description page we can now see some changes.  We adjusted the flow on the [18th May](https://github.com/AgileVentures/WebsiteOne/commit/1af6bfe440133e4ade2b08e6fc7b8703825ec2b6), and we can now see that the click through on "Get Started with Premium" has increased from 11% to 15% compared with the same period previously:

![](images/Screenshot%202017-07-05%2009.55.37.png?dl=1)

Now the difficulty is that that change could just be due to a trend that we would have seen anyway, and also we don't know that all those clicks are coming in via the new flow.  Users can reach this page by coming in through the pricing page, but having more people click on "Getting started with Premium" certainly feels like we are going in the right direction.  What's frustrating is that we don't have more info about what's happening on this page:

![](images/Screenshot%202017-07-05%2009.59.16.png?dl=1)

The Google analytics plugin is silent on the numbers of folks clicking on the subscribe buttons.  Paypal redirects to another site, while Stripe uses JavaScript to popup a credit card form.  I spent a chunk of time trying to see if I could see the flow around that page in the main Google Analytics site, but this was as close as I got:

![](images/Screenshot%202017-07-05%2010.15.30.png?dl=1)

Some of the exits could be people going to the PayPal page, but it's unclear.  It makes me want to put the Chatlio "any questions" element in the site so that there's an opportunity for people to tell us what they're thinking as they browse the page.  Hmm, well I've burnt a lot of time reviewing the analytics and not a lot of time getting on to what I thought was my main thrust today, which is this rewording of the Premium description ...

I'd posted some thoughts on that into a [WSO issue](https://github.com/AgileVentures/WebsiteOne/issues/1583#issuecomment-310992734) where Ashley had made a great point about how these new re-wordings didn't include the term "Agile".  What's interesting is that in breaking out separate items to give support on, we lose sight of the bigger agile picture.  Michael had mentioned that getting quick feedback on pull requests is crucial for Agile, but it's also the case that the main "Agile" things that we offer, such as working with a team, doing scrums, retros, kick-offs, sprints etc. are not something that the premium offering particularly supports - or at least not in its current form.

It makes me think about things like having specific features like Sprint or retrospective support.  Of course that's not as easy to break out as something that can be offered to Premium's on a 121 basis without the associated selection of a project, introduction to the team etc. etc.  I have ideas about people signing up for a one off "sprint support package" at some higher price point, but there's a ways to go there for that to make sense ...

So anyhow, here's another pass at questions for the Premium page:

* Want to find great developer jobs? Get help on the job search process?
* Want to ace technical tests required to get interviews?
* Want to accelerate your professional development?
* Want to improve your coding and development skills?
* Want to really understand Agile software development?
* Want to put paid coding projects on your resume?
* Want to make the world the better place?
* Want to feel confident asking technical questions online?
* Want to get discounts on important software and services for developers?

and I've now gone ahead and updated the Premium page using them and reworded the descriptions to make sense.  I think I could also be adjusting the full details to be popping up like we have in the FAQ, but I think this is enough for today.  The interesting thing will be to see if this has any noticeable impact on the click through rates or the number of people signing up ...

p.s. Side note, since shortening the text in the "code" page and adding a call to action "get started now" button we've seen a good third of the people landing on the page clicking through on the "get started now" button.  Not that that says very much - what would be good to be seeing is month by month stats of footfall and percentage of people on the site actually signing up ...
