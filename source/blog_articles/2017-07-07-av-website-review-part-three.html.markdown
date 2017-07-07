Still struggling to get back for former health and energy levels.  Pleased to have reviewed and made some changes to the website.  Let's continue.  Looking at "Getting Started" yesterday made me think about the projects page again.  Effectively the new "step 1" of "Getting Started" is a view of the projects that we don't have, i.e. one with project maintainers, tech stacks and links to relevant Slack channels.  There have been recent improvements to the project page, specifically that the main list is now ordered by most recently active on Github, so there is a sense in which the main ordering of the projects is by most active (although it's not clear that that's the ordering) and we've adjusted the smaller list on the left to be alphabetical (and so consistently ordered), as well as filtering for inactive projects. 

That said I'm not sure that people arriving on the page have much clarity about what's going on.  We also have the issue that projects with attached images have those images very large and overly dominate the page:

![](https://www.dropbox.com/s/3p6c4ginnxghgdo/Screenshot%202017-07-07%2010.13.22.png?dl=1)

It looks like just setting a smaller width on that could address that issue ... and yes, done that, added pull request.  Also noticed that actually we didn't need to crimp the width per se - the bootstrap row/column setting was out of alignment - the column values adding up to 22 rather than 12.  So with a few changes the main project page for Wiki Edu now looks like this:

![](https://www.dropbox.com/s/873yfege210c0n0/Screenshot%202017-07-07%2010.31.40.png?dl=1)

I guess what I really wanted to do while blogging this was to work through all the projects in the left hand list and de-activate those that aren't really doing anything at the moment.  I guess I will just blast through that. The other thing I wanted to address was how this layout doesn't really have much precedence in other sites.  I don't know that we are using the layout language that people are familiar with from other sites.  Of course it was when I just took over the project management that the previous maintainer was trying to get in the project page redesign that had been under way for a long time, but he backed out of that, getting busy with other things and perhaps frustrated that I was asking him to submit pull requests that were super-focused on specific changes, rather than rolling in a collection of things that were unrelated to the main ticket.

Perhaps that was a major mistake on my part.  I thought I was bringing in best-practices from other projects that I had worked on, but perhaps we lost a lot of work that could have leapt us forward in terms of the project page layout?  Michael and I chose to focus on the events pages for the main work we did when we first got involved.  To me there were screaming inconsistencies there that needed to be fixed first, but who knows what was the "right" thing to have done.  We did what we did, making our best judgements given the information available at the time.

A layout that was a series of boxes, or rows in the table would seem to make more sense than two separate lists where the ordering is somewhat unclear. Berkeley's [past project showcase](http://projects.saas-class.org/projects) has a more comprehensible layout in some ways:

![](https://www.dropbox.com/s/tzf8s3mfpeqzujc/Screenshot%202017-07-07%2010.02.14.png?dl=1)

They had another system for future projects that also had a layout that made more sense.  Just looking around I found a great [guide they have for external "customers"](http://cs169.saas-class.org/faq/external-customer), and a group of Berkeley students called ["BluePrint"](https://www.calblueprint.org/) doing the same thing as AgileVentures.  Frustratingly I can't find the portal that they the CS169 class uses to allow students to select upcoming projects.  I've emailed Armando to ask if that's still around.

I note we've got a [project improvements epic ticket](https://github.com/AgileVentures/WebsiteOne/issues/1089).  Lots of things to keep trying and improving ...

p.s. just went and set all projects to "inactive" and am working through re-activating the ones I know have activity ...
