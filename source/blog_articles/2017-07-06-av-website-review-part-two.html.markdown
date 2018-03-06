---
title: AV Website Review Part 2
date: 2017-07-06
tags: 
author: Sam Joseph
---

So I'm deeply tempted to continue with the Premium page review.  I think making all the new questions have their explanations in pop-out form would probably be an improvement, but I know that's a sticky hour or so of manipulating HTML.  Very unDRY, something we could, or perhaps should, be doing with a DB backed loop through a template, but setting up (and deploying that) would be another few hours and days.  I'm leaning towards further review of the "Getting Started" page, although we did make a change to that on Monday.

It's too early to tell if those changes have had any impact.  We can look for changes via Google Analytics:

![](https://dl.dropbox.com/s/yyyf9ykhy0tc68u/Screenshot%202017-07-06%2009.17.13.png?dl=1)

and compare Monday to Wednesday last week, with Monday to Wednesday this week.  The number of clicks through WebSiteOne has gone down, although we can see it went up for some other projects:

![](https://dl.dropbox.com/s/39tiao9ajhshqib/Screenshot%202017-07-06%2009.18.11.png?dl=1)

Far too early to discern any trend.  I guess the question is whether to leave that single change (of removing the first two administrative steps) or to keep on "improving" the page.  Same quandry with the Premium page, I guess.  Just reviewing that now, I made some minor changes to the top of the page to remove the seemingly redundant link to "AgileVentures Premium" and an explanation text saying "the benefits are as follows" that seem to make no sense now that we have a list of questions.  Let's at least do a sanity check on what we have in the "Getting Started" page.  So, step 1 currently says:

> Browse the list of AgileVentures projects on agileventures.org, or the active projects listed in the table below, and learn more about the projects that interest you.

> You can always set-up your own project, but we would recommend joining an existing one at first. This way you can meet other members, learn about our culture, principles and tools.

Now the odd thing is that we say browse the list of projects, but we don't link there, and I could impulsively link that up, but would we rather the user focuses on the list of projects shown below?  The key thing we have in this list is the technologies used by the projects, the current maintainers and direct links to the relevant Slack channels.  Of course it would be great if the main projects page showed all these things.  I'm thinking that a slimmed down more focused version of the above text would be:

> Find a project that interests you from the table below:

Then the table, and then notes on "can't find a project you like, see our complete list" and "want to set up your own project" after the table itself and also we could improve the table formatting somehow ... so I pushed the width out to 100%, increased the font size, bolded the headings and added a couple of other active projects:

![](https://dl.dropbox.com/s/66ss4bwklwf5may/Screenshot%202017-07-06%2009.52.21.png?dl=1)

as well as further cleaning up the text a little.  Reading through the rest of the getting started I feel like I'm reading a description of how some of the original developers intended the entire site to work.  There are cryptic references to WSO and other things that I think will make little sense.  Even after a few tidy ups what we have in step 2 is:

> Learn how we communicate and coordinate projects:
* source code is kept in Github repos
* user stories (feature descriptions) are kept in project's Pivotal Tracker (or other tracking systems)
* other documentation and detailed feature descriptions are kept in project's section on agileventures.org
* real time discussion are done in Slack channels, in the specific channel for the project
* long-term discussions (where it is important to preserve history) are done on Pivotal Tracker, or in Github pull requests/issues or by commenting under specific documents in project's section on agileventures.org 
* optionally recordings of PairProgramming sessions and standups are stored on YouTube. The list of videos for a specific project is under project's section on agileventures.org
* technical information on the project is kept in documents under project's section on agileventures.org

This is all information that we want new members to absorb, but I think it's unlikely that many people will read and absorb.  We probably need to break this up into multiple steps (just like FreeCodeCamp) and have an activity associated with each.  Again it would be lovely to track people's progress through this - I recall FreeCodeCamp has a separate page with an animated gif for each action, so they'll be able to track with Google Analytics how far people are getting through the process.  Giving the user themselves a sense of progress through the task would also be great, as would having buttons that made it trivial to post a question to a project maintainer in the relevant slack channel (a bit like LinkedIn does to make it easy to send messages of a particular format).

Of course with all these different ideas there's the question of which are the ones that are really worth pursuing.  I did get set up a few months back with being able to make nice animated gifs.   A nice animated gif of reaching out to a project maintainer and having them respond in a friendly fashion would probably be much more asorbable by lots of people than the current text of:

> The "Contacts" column contains the "Slack name" of one or more people that can help orient you to the project.  
> Use this name to alert that person to a specific message when you post to the project slack channel.  For example, say you want to post a message to the MetPlus project channel, and make sure that "patrick" is aware of that message.  Here, you would go to the channel (and join the channel), and send a message like this:
> "@patrick - hi, I am new to agile ventures and I would like to learn more about this project."
> "patrick" will then be alerted to your message and respond back.

I guess it's all question of where to expend the effort ...?  Once people have left the getting started page will they come back to follow through the steps?  Perhaps not, unless they are marking off progress.  It makes me want to split the getting-started pages into Step 1 (of 5), Step 2 (of 5), ... Step 5 (of 5) so that there's much less text per individual page, and so that we can see the flow of people from page to page.  Of course a lot of this points to the need for a re-design of the project page, which we've thought we've needed for a long time.  There's also the automated greeter bot in Slack, which I need to move to Azure, and make sure others can deploy to and change.  That greeter bot would ideally point people back to the "Getting Started" page, rather than off to GitHub as it does currently ...

Well, I think that's the course I wanted to be on with these blogs.  Careful review of various pages in the site.  Set up an experiment on each, come back and review in a month to see what impact there's been.  There's certainly a good few other pages I can review over the course of the next month, e.g. the home page, the about page, the projects page, the event page, the opportunities page, the past scrums page, all the individual project pages ... the list goes on ...
