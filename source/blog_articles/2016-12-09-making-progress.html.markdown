---
title: Making Progress (for the Greater Good)
date: 2016-12-09
tags: blog, volunteer opportunities, organisation owner, project channel, code, people
author: Sam Joseph
---

So I went over time for yesterday's blogging.  I'd tried to cap it at 20 minutes, but I got into a fair bit of detail about the options for testing/mocking the Twitter API before getting on to the meat of the matter I was trying to focus on; Agile DevOps.  I guess there are two kinds of blogs, those with a succinct focus on a particular topic and those that are a mixed bag.  The process of profiling my days in blogs recently have probably tipped me more towards the latter, with the blog being a reflection of all the things I did on the previous day.  I'm acutely aware that the length, lack of focus, lack of images and high code content probably make my blogs less readable for many.  I am writing these things partly for others to read, but also partly just to get my thoughts straight, and stop them from being lost in an ever dissappearing slack history.

Maybe writing one really focused blog a week would have more impact in terms of creating content that drew people into AgileVentures, that got across key concepts about Agile Development and Software Engineering.  However I'm also testing myself.  Can I document my process every day for a year?  Are people's diaries not meant for publication?  I committed to doing a year of this and I like to keep my commitments.  Maybe after that year I'll switch to doing one blog a week, still writing every morning and revising the following day, but doing at least five revisions where I delete stuff and increase focus. Let's see.

Despite running over on the previous day's blog, I got a fair bit of admin work done, although it was clear that the Zapier automation wasn't working as expected.  The attempt to get it to post as non-bot had failed, so I've set it up for a simpler bot post today, although it's clear that it can't easily post the link to the precise blog file - the closest it can get to that is the pull request URL.  I'm also not sure how much I'll get charged ... It's just one thing in my list of "automate to create time" that I generated from profiling my days a week or so back:

* [ ] Automation
  - [ ] try to fix up lower 3rd thing --> no response from github repo post ...
  - [ ] automate more of blogging TRYing Zapier
  - [ ] automate greeters
  - [ ] automate project management (slackbot)
  - [ ] automate twitter connections
  - [x] automatically resize blog images for fixed width
  
There's a lot outstanding.  At least the css image settings in the blog meant I wasn't having to resize images I put in the blog.  Getting my hangout lower third banner fixed seems like we'd have to create our own hangout tool box and release it as a hangout plugin, assuming we can even get some working source code.  That doesn't seems appealing since we're unclear about the future of Google's support for hangout plugins.   That said, sponsors keep commenting positively on my lower third banner when I have it on the hangouts, and I kind of feel naked without it :-)

The Slack is hotting up and there are lots of people to greet, so need to automate that.  At the moment I'm just doing in snatches of time between other things. Most of the day was taken up with pull request reviews and our third premium mob programming session.  We're getting deeper into Avdi Grimm's Confident Ruby and the Premium Mob members are starting to get more confident about typing out code in the shared C9 space, although it's still early days.  I've assigned "homeworks" for each member to develop their own domain models that use techniques from the sessions.

We had pull requests from several premium members on LocalSupport and WebSiteOne, as well as from some MOOC students.  Off the back of the "Martin Fowler" scrum we reviewed a pull request relating to search engine optimization (SEO).  It seemed a little convoluted and we fixed it up with the Rails `prepend_before_action` method, and then the PR author jumped in to a later LocalSupport session that Michael and I ran to review the other PRs.  That was a great example of AgileVentures working as I imagined.  The MOOC student hadn't been in the original scrum, but had seen our comments on the PR and I'd posted a link to the video of the scrum.  I don't know if they had watched it, but they saw the LocalSupport pairing session link ping into the #localsupport channel when I started that, and we were able to have a nice connect up and resolve some other outstanding issues on the pull request.  If I'd just started a private session with Michael, or if our AgileBot hadn't worked the student would not have so easily been able to drop in to chat.

This makes me think we really want to get the slack channel settings on the AgileBot sorted so that every project channel gets notifications of appropriate pairing sessions.  Michael and I discussed that in an overview session on WebSiteOne, and we could automate most of the thing with calls to the slack API to get project managers to select their project channel from a dropdown when setting up a project, or just ensure that the project slugs on WSO exactly matched the channel names on Slack.  That'd be another epic and I deemed further PayPal integration to be the thing we should focus on first.  All the project support goes into the free tier, and while we want project managers and teams to have the best support possible, I'm going to be no use to them next year if I can't get us on a stable financial footing.

Before we'd got on to WSO, Michael and I had spent some time bashing out a refactoring of another PR that was addressing complexity issues flagged by Code Climate on LocalSupport.  We maybe spent too much time wrestling out the code together, but I was pretty pleased with the solution we came up with.  We worked out that the complexity was due to a combination of nested and non-nested routes in the Volunteer Opportunities controller:

```rb
  resources :volunteer_ops, :only => [:index, :edit, :show, :update, :destroy] do
    get 'search', on: :collection
  end
  resources :organisations do
    resources :volunteer_ops, :only => [:new, :create]
  end
```

This had lead to code like this:

```
  def org_owner?
    if params[:organisation_id].present? && current_user_has_organisation?
      current_user.organisation.friendly_id == params[:organisation_id]
    elsif current_user_has_organisation?
      current_user.organisation == VolunteerOp.find(params[:id]).organisation
    end
  end
```

Now I know that maybe we should be using the pundit or cancan gems to manage roles and persmissions, but we staying focused on the refactoring. The proposed alternate had ended up creating overlap between two `before_action`s, which we unravelled with a set of method names that tried to connect up with the reasons for the split:

```rb
  def org_owner?
    current_user.present? && (current_user.can_edit? org_independent_of_route)
  end
  
  def org_independent_of_route
    organisation_set_for_nested_route? || organisation_for_simple_route
  end

  def organisation_set_for_nested_route?
    @organisation
  end

  def organisation_for_simple_route
    VolunteerOp.find(params[:id]).organisation
  end

  def organisation_for_nested_route
    Organisation.friendly.find(params[:organisation_id])
  end

  def set_organisation
    @organisation = get_organisation_for_nested_route
  end
```

At least the next folks in might have a fighting chance of seeing the relation to the routes up front.  Although maybe they'll tear their hair out in the multiple steps of redirection in the code.  Probably the better thing in the long term will be to unwind the routes.  We could remove the nesting if we insisted that all the other controller methods moved into the nesting (i.e. :edit, :show, :update, :destroy), but that would be a bigger refactoring for another time.

Anar who'd been working on this PR dropped in to our planning session and was able to participate in a manual vote on the further paypal integration, and in the background I used the Async slack bot to run three other votes in sequence in LocalSupport, WebsiteOne and AsyncVoter itself.  Gotta get multi-channel parallel voting enabled!  But again that's free tier ... hmm although maybe we should be offering extras like that as part of premium project support plans?

In summary--if there can be a summary for a mixed bag blog like this--it feels like there's progress. The whole concept of Premium members paying for priority code reviews on pull requests is that there's huge learning value from that process, and the quicker you get the PR review the more valuable it potentially is.  Also, our mentors' time is a limited resource.  Probably the individual features and bug-fixes could be delivered faster if our most experienced mentors just worked on the code themselves rather than reviewing submissions from less experienced developers.  However the hope, or the gamble, is that the slow-down on delivery can be compensated for by the Premium payments that allow the whole enterprise to sustain itself, and that the projects move forward and deliver value through the process.  Also that once people are paying/sponsoring/donating to something, that they become more invested in it, and we can ride the win-win cyle up to a scale where more and more people are compensated for their time, and they are all working on worthwhile projects for the greater good!

###Related Videos:

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=PjpUk3fBBDs)
* [Pull Request Reviews on LocalSupport](https://www.youtube.com/watch?v=-PeG-5Egd2E)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=m4uOCp6ZfyE)
* [WebsiteOne project planning](https://www.youtube.com/watch?v=kpk5yNiQox8)

  
