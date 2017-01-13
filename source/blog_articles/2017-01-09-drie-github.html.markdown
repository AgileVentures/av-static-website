Friday I took a trip down to the drie offices in Hammersmith.  We'd agreed before the break that we needed to coordinate our collaboration for the new year.  We'd previously agreed a sliding scale of sponsorship support based on outcomes for drie in terms of the number of users adopting drie.  In preparation for the meeting I prepared the following list:

* What I need
  - ability to track the number of people using drie
* What I need to do
  - check whether moving drie servers to Ireland is getting the performance increase we hoped for
  - migrate more of LocalSupport to drie
  - prepare for the devops conference talk
  - improve on the document outlining the needs of the different AgileVentures projects
  - get budget details for drie hosting MetPlus
  - detailed technical plan for the following year
  
Talking through the above with Ben and Tom at drie and the things they wanted to get through we came up with the following agenda:

* drie to show new features for drie-push
  - GitHub Integration
  - versioned config
  - appdirect
* look at drie pricing models
* look at performance
* conference talk
* generate clear features to de-risk the collaboration going forward
  - acceptance criteria
  - roadmap
* geek out about the new proxy model (to help understand drie push startup times)

Working through the agenda it became clear that at the moment there wasn't an easy way to track which AgileVentures members were using drie, as all that drie records of drie-push users is ssh keys.  However this would change with the introduction of a new GitHub integration which was the first thing Ben and Tom showed me.  Rather than building an entire team's management framework to compete with Heroku and others, Tom came up with the idea of using a [GitHub integration](https://github.com/integrations) to leverage the existing GitHub teams framework.  

AgileVentures already uses a lot of Github integrations such as TravisCI, Semaphore and CodeClimate.  GitHub themselves are [encouraging more and more developers to create integrations](https://github.com/blog/2226-build-an-integration-for-github), and the drie GitHub integration looks very similar in that it can be configured and installed via a GitHub interface, and little ticks and crosses are added to the GitHub interface when operations succeed and fail.  [Heroku Review Apps](https://github.com/integrations/heroku-review-apps) are also a GitHub integration and work in a similar fashion, albeit requiring a complete separate Heroku app set up for them to riff off.  However, we've had to turn off Heroku review apps as being too expensive.

drie push GitHub integration goes one step beyond Heroku review apps, by making it possible to set up a repository to just deploy on any push to GitHub.  It's like Heroku Review apps without the overhead of having to deal with Heroku or their new pricing model.  Tom was also showing me other tricks on the backend such as versioned config and reverse proxy tricks that will allow their system to scale effectively.

We went through the new drie pricing models which I need to pass on to the MetPlus project who are talking with their client today, and today I'm torn between trying to spend the whole day on pushing the LocalSupport app further onto drie (still haven't checked the new performance data), reviewing material for my upcoming devops conference talk, and generating this new roadmap for our drie collaboration, which we did at least agree should follow this format:

```
Dates #free-users #paid-users

include features ...
```

The danger that Ben kept us focused on was whether developing the drie push GitHub integration would suck up time that could be spent polishing the ssh only access to drie push, and slow down our overall timeframe.  It's almost certain that that will happen to some extent, but the problem with the ssh only approach is that the main differentiation from Heroku is on price which will be difficult to compete on in the long term.  Other drie push features such as deployment per branch won't make much of an impact unless the configuration model is sorted out, and to be honest the big win for AgileVentures members will come from ease of use.  Avoiding the need to go to the command line at all, and just make your app deploy directly from GitHub, will remove a major stumbling block. 

Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=MrUF68V4WMw)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=SttRB9A2bQ0)
* ["Bob Martin" Scrum](https://www.youtube.com/watch?v=YPtN3dQd6Z4)

p.s. this blog took 33 minutes to write
