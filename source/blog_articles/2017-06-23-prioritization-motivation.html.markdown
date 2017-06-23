Prioritization and motivation is complicated.  Each day I'm sitting down and trying to have myself focus on the things that will actually generate revenue.  That said there are things that I'm motivated to do (or have fallen into a pattern of doing) that are several steps removed from any revenue generation.  Some things are a pattern that I at least partially enjoy, e.g. blogging, publishing blogs, jogging, listening to podcasts, preparing Japanese language tests for my kids.  Doing them makes me feel centered and organised, but none of them are likely to lead to any revenue generation.

The late night on Wednesday put paid to any jogging and podcast-listening on Thursday morning.  I thought I'd recover for Friday, but then I ended up staying up late after my regular late night blog publishing.  The thing that caught me was fixing up the InVision app that we'd created as part of the design sprint so that it would run on GitHub pages.  You can see it there now at [http://why.agileventures.org](http://why.agileventures.org).  We'd been redirecting the URL to the InvisionApp itself, but in the marketing meeting yesterday Lara raised the issue that Google Adwords might have a problem with the redirection and it might raise an issue of trust with our users.  She asked if we couldn't export the app, but over lunch I googled and discovered that we could export a zip of the app in order to run it offline.  I'd quickly uploaded the app into the GitHub pages site [https://github.com/AgileVentures/why](https://github.com/AgileVentures/why) and saw that we could host it directly so no need to redirect away from the [http://why.agileventures.org](http://why.agileventures.org) domain.

Great, but a half an hour of kicking things about at the end of the day hadn't resolved one part of the app that didn't work.  The final "sign up" button external link to the main AgileVentures sign up page at [https://www.agileventures.org/users/sign_up](https://www.agileventures.org/users/sign_up) was attempting to load the following URL `/redirect?url=https%3A%2F%2Fwww.agileventures.org%2Fusers%2Fsign_up`, which is presumably an endpoint on the InVision app, which doesn't exist in our GitHub Pages app.  I tried a few tricks to get round this such as just adding a file called `redirect` with the contents `<meta http-equiv="refresh" content="0; url=https://www.agileventures.org/users/sign_up" />`.  That didn't quite work with clicking the sign up link leading to the browser downloading a file rather than redirecting.  The problem was that GitHub pages (Jekyll) wasn't serving the file with the correct media type.  I tried using `redirect.html` and some under the hood jekyll redirection, but it still suffered from the same problem.

I emailed InVision support and GitHub support, and InVision came back saying that the downloaded app didn't support the full functionality, and GitHub were asking for clarification on what I was trying to achieve.  I tried MarvelApp (an Invision competitor) and saw that I could quickly recreate the app there, but couldn't download without upgrading.  So I'd kind of given up for the time being, but after my late night blog publishing session I couldn't help tinkering.  The late night blog publishing session is also a strange prioritization/motivation conundrum.  In days gone by I used to try and publish the previous days blog after having just written the new days one, i.e. in the morning each day.  That had the advantage of keeping all the blog action within my working day, but I was finding that having written one blog I was struggling to do another blog relate activity, and the blog publishing was being put off and forgotten.  Then I started doing the publishing component late in the evening, often in parallel with giving my eldest son a little time on his iPad before I read him a story.  Then I decided it wasn't so good for my eldest boy to be getting that iPad stimulation before bed (all the boys get 20 minutes iPad in the morning if they've finished getting completely ready), and so I ended up shifting to doing the blog publishing after all the boys were asleep, i.e. in the my own winding time before going to sleep.

Reasonably efficient, but sometimes I am too tired to get to it, and on occasion I get distracted because I've opened up Slack to post by blog, or like last night where just thinking about work brings me back to a problem like this redirection issue.  As it happens another thirty minutes of fiddling fixed things up.  The problem seems to be an issue in the Jekyll redirection syntax.  I'd been experimenting with Jekyll syntax in `redirect.html` like so:

```yaml
---
redirect_from: /redirect
redirect_to: https://www.agileventures.org/users/sign_up
---
```

but it seems that trailing slashes are necessary to ensure that Jekyll serves things in the right media type, so the following worked:

```yaml
---
redirect_from: /redirect/
redirect_to: https://www.agileventures.org/users/sign_up/
---
```

It was finding issues like [https://github.com/jekyll/jekyll-redirect-from/issues/37](https://github.com/jekyll/jekyll-redirect-from/issues/37) that helped me fix things up.  So now, whatever the shortcomings of the prototype app, we have all the functionality working at [http://why.agileventures.org](http://why.agileventures.org).  The question now was whether this was good enough to start testing, which prompted a little discussion on Slack:

Sam Joseph:
> question is whether this it good enough to start testing (@joaopereira @mtc2013 @mlindsey @lara-t)

Federico:
> @tansaku by “this” you mean what’s up now at http://why.agileventures.org ?

Sam Joseph:
> right - I downloaded the invisionapp and hosted it on github pages so the user stays on why.agileventures.org the whole time - that was the concern that @lara-t raised in the meeting - clearly the app could still be improved in many ways - I know you don't like the font, the buttons could be more clickable etc.

Federico:
> the button colors, all the tester feedback. :grimacing:
> It’s good enough to test, but it’s serving one purpose: a larger sample size *test* of the prototype.

Sam Joseph: 
> Traditionally the design sprint prototype is supposed to be thrown away, and a better one rebuilt

> But we are operating on a shoestring and in principle the content test could redirect 10% of our clickthroughs to why.av.org

> And we can increase as we improve it

> But at the moment are clickthroughs are not driving any revenue so in principle there's nothing too much to lose ...

Federico:
> It’ll just be additional data to reinforce the verbal feedback from the testers. *That’s obviously appealing. I’m not opposed to it.* I’m excited, however, to help build a *slick coded version, that takes into account the tester feedback. Please let me know when that starts.*

Matt:
> I think why.av.org looks pretty good now and we should start 50/50 A/B test whenever @lara-t is able to do it.

Lara:
> @here great job, I will get everything setup this weekend for AdWords A/B Testing. Well done everyone I really love the new page :slightly_smiling_face:

So there we go.  I failed to get up for my jog or podcast listening this morning :-( I got my kids Japanese tests written up, and I'm overtime on my blogging.  Funny thing is about the moment that motivation siezes you.  It siezed me several times yesterday trying to get this fixed up.  It's slowly running out now as I spend too long on this blog and my motivation to make all the points I was originally thinking about gradually drains away.  I stopped to email GitHub to tell them how I solved the issue and how their redirection documentation needs updating.  I was trying to make a point about motivation and prioritization.  There are at least two items I should get to now that directly relate to getting paid and there's the MOOV/AV102 emails I've been putting off all week.  I used to be motivated to do them and now ... I guess I've got sick of the little dance I do to get the old updates from the previous MOOC and put them into the new one.  There's a fiddly updating of the links and no particular positive feedback - as opposed to the likes I get when I tweet about other people's podcasts ...

Motivation and prioritization.  Right must finish up blog, leave the A/B testing to Lara and make sure that I get they key revenue generating work done this AM (mainly paperwork), get those MOOC posts out, and keep going with balancing what I have motivation for in the moment, and what really has to get done to pay the bills ...
