So I've got in my [PR for updating the premium/sponsor model](https://github.com/AgileVentures/WebsiteOne/pull/2039) to have the system maintain records of the different subscriptions a user has over time.  The PR is green and there are is at least one refactorings that could be done, but I think I sensibly open that as a [refactoring ticket](https://github.com/AgileVentures/WebsiteOne/issues/2103) rather than slowing the release of this.  In the process of doing so I end up closing the 20 or so airbrake errors about overrunning requests, that seem to have been fixed with the performance fix I released (taking the countdown to next scrum off the homepage).

Right, so I want to get that deployed today if I can.  In the meantime I'm trying to finish up the monthly report for the AgileVentures trustees.  What I'd love to be able to do is get this change deployed and then review all the premium accounts as I did before Xmas and ensure that we have all the subscription history changes sorted, and then I can have the report include some sort of up to date stats for the trustees.  

I'm kind of struggling with the reports, trying to make the stats reported be something that's actually of use to us.  Is it sensible for me to be reporting data like this about the WebSiteOne coordination meetings:

![](https://dl.dropbox.com/s/r4fsvru82gmpu38/Screenshot%202018-01-17%2009.57.57.png?dl=0)

It's relevant is that part of my responsibility to the trustees is to ensure that the WebSiteOne coordination meetings are held.  I guess it's not an unhelpful summary for me to remind me who's attended the meetings over the course of the last month.  I also report various google analytics and new relic stats for the month.  Keeping my eye on these is good, but I've got into this habit (in both AV and NHS reports) of creating little paragraphs like:

> This month: From Dec 17 to Jan 15 we had 294 unique visitors to the site, of which 70% were new visitors.  The average session length was 3 minutes and 31 seconds and the bounce rate was ~47%.  Pages per session was 3.39. 

Which are slightly fiddly to pull out of the Google Analytics interface and I wonder if I shouldn't just stick in the graphic that Analytics provides, or even whether these are statisics of consequence.  I know that most of the trustees are not interested in the real specific numbers here, and that part of the point of the report is that someone is keeping an eye on these things.  It would be great if the report was totally automated.  At least I'd be interested in reading it ...  I'd be even more interested in better understanding what viewers of the website were really thinking as they viewed the site.

I had meant to come back and finish this during the day, but no rest for the wicked - okay, onto the next one ...
  
