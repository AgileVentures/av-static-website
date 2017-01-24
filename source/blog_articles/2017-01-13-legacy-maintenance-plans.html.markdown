When does an app become legacy?  I'd say pretty much as soon as you have anything deployed that anyone is using.  In my experience development on projects can stall and restart, but as long as people are deriving value from the application it needs to be maintained.  We hear things like 60% of the effort in a project is during the maintenance phase.  We have various projects in AgileVentures that could make the claim to be legacy codebases that need to be maintained.  Let me list them in the order of their creation:

* [AutoGraders](https://github.com/saasbook/rag)
* LocalSupport
* WebSiteOne
* OSRA
* [MetPlus](https://github.com/AgileVentures/MetPlus_PETS)
* ProjectScope
* AsyncVoter
* SHF

Apologies for any I'm missing here.  There are other projects like VisitMeet and WikiEduDashboard which might also be put in the same set, but let's use the above set to think about the ongoing dance between development and maintanence.  The AutoGraders system was originally developed by Armando Fox and his students at UCBerkeley.  It continues to be used several years after its creation supporting the grading functionality in the "Agile Development using Ruby on Rails" MOOC.  Over the last five years there've been some periods of development work interspersed with longer periods of maintenance only:

![Graph of AutoGraders GitHub commits](https://www.dropbox.com/s/xrvi8kz6fcb22o8/Screenshot%202017-01-13%2010.04.12.png?dl=1)

In contrast, development on (or at least commits to) LocalSupport has been steadier:

![Graph of LocalSupport GitHub commits](https://www.dropbox.com/s/ro02xadm97z56q1/Screenshot%202017-01-13%2010.05.43.png?dl=1)

WebSiteOne on the other hand started with a very high number of commits and has gone through a couple of periods of development inactivity through its lifetime:

![Graph of WebSiteOne GitHub commits](https://www.dropbox.com/s/rmk1xa5p17jcjse/Screenshot%202017-01-13%2010.06.44.png?dl=1)

OSRA has seen a recent resurgence:

![Graph of OSRA GitHub commits](https://www.dropbox.com/s/lzqb77smnux7hc8/Screenshot%202017-01-13%2010.08.13.png?dl=1)

While MetPlus has seen some spikes, but has been pretty consistently active:

![Graph of MetPlus GitHub commits](https://www.dropbox.com/s/fj2a6hk0a0esliz/Screenshot%202017-01-13%2010.09.39.png?dl=1)

Note that the range of the above graphs is changing as we go through these projects, having started with the oldest.  

AutoGraders, LocalSupport and WebSiteOne have been in near constant use since they were created.  They all have ongoing maintanence burdens, as well as the need for ongoing development.  They all have lots of room for improvement, but are arguably serving at least some people's needs.  MetPlus is the odd one out in that even after eighteen months of development the proposed end users are not yet using the system.  The client has insisted he wants the MVP ready before he can put users on it.  The client has been giving regular feedback on the developing system, but it's a slightly odd set up from an Agile development point of view.

We're into interesting territory with all the projects, particularly given the charges they incur in terms of hosting.  AutoGraders is hosted on AWS and UCBerkeley picks up the bills there.  LocalSupport is hosted on Heroku and Voluntary Action Harrow Cooperative pays those bills.  WebSiteOne is also on Heroku and our AgileVentures charity is picking up the increasing bills there.  OSRA and MetPlus are also on Heroku, but the free tier appears to be sufficient for the moment.  Every year, Armando Fox at UCBerkeley has 100's of students generating systems that charities start to use, but the students are not always available later to maintain the applications.  Armando suggested AgileVentures could charge maintanence plans and so I put together a [draft landing page](http://www.agileventures.org/nonprofit-basic-support), which outlines some different levels of support that a non-profit could get for having AgileVentures maintain their application.

The costs in there are based on time of AgileVentures folks restarting apps, responding to issues and so forth.  The interesting discussion we're now having with the MetPlus project team as they look to take their system live (and potentially incurring costs) is bundling the costs of hosting (on Heroku, drie, AWS etc.) as part of AV maintenance plans.  We're imagining that non-profit administrators would rather pay a single bill to AgileVentures for maintainenance and hosting rather than several separate bills for different hosting charges.  The big unknown is what sort of traffic MetPlus will incur.  Yesterday we agreed to offer the client everything free for the first three months, with all the systems on free infrastructure, with the indication that hosting/maintanence would cost on the order of $200 a month going forward, as an all-in, where we would do our best to use the most affordable hosting packages and provide the best level of maintanence support.

There are lots of details to work out here; lots of unknowns.  Small non-profits and charities generally don't seem to be in a position to pay for development work, but they seem to have some limited funds for maintenance and hosting.  Being able to pay AgileVentures for maintanence and hosting in a single package sounds appealing in principle, but there's the opportunity for us to lose money if performance spikes and we don't have the right agreements and expectations set up.

We'll just keep applying the Agile process of pushing out ideas, getting feedback from our clients, modifying our offering slightly, and keep trying to improve.  Keep following that path to sustainability.

###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=MvMgbBya2Bk)
* [MetPlus Planning meeting ](https://www.youtube.com/watch?v=Olz0s99GhVQ)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=dEanyrCUjOQ)

p.s. this blog took 38 minutes to write.

p.p.s. managed to clear several things off my desk today
