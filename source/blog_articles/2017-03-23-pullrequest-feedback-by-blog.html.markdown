---
title: Pull Request Feedback by Blog
date: 2017-03-23
tags: continuous integration, VCR, PuffingBilly, gitignore
author: Sam Joseph
---

![feedback](/images/feedback2.jpg)

So in the blogs over the last two days I managed to get to the bottom of the problems on one LocalSupport pull request from a Premium member (Zmago).  It's clear from that experience that I've got to adjust the develop branch so that VCR/PuffingBilly caches are all up to date and correct, or no other developers are going to get a correct read on what's going on with network connections.  I've also got another pull request on a complex three point feature, that I need to spend some concentrated review time on.

Maybe I should have been opening another PR to fix up precisely that issue but I started by pulling down Marouen's "Post Volunteer Opportunities to DoIt" branch locally and re-running it with the caches in place - I opened a PR on that basis - but it went to my old tansaku fork. I really need to disconnect that.

Then I got down to reviewing Marouen's PR, file by file, using the "compare with develop branch" feature in RubyMine, which is maybe a funny way to review, but well - start at the beginning.  I made the following notes as I worked through:

* doit_volunteer_ops.js file being loaded on every page
* doit_volunteer_ops.js hits local `/doit_organisations` end point but does nothing with result?
* bootstrap datepicker being incorporated
* DoitOrganisationsController providing us local? endpoint to new service `Doit::GetManagedOrganisations.call`
* new DoitVolunteerOpsController to handle creation of new op on DoIt
* VolunteerOpsController has check for whether to display publish button (could be in helper or separate method)
* long running job PostToDoitJob
* new model DoitTrace to store DoIt publication status (VolunteerOp has_one) (in db)
* new model DoitVolunteerOp to store more data about doit op? (not in db - stored temporarily)
* Doit::PostToDoit service to do posting
* dropdown_list partial to allow for selection of doit orgs ...
* doit_orgs index.js.erb - not sure how this fits in
* new form for creating DoitVolunteerOps
* volunteer ops show page broken up into more partials
* sucker punch async job handling added
* doit_volunteer ops nested in volop URL

Marouen had a good summary in his PR description:

* add new model for tracing published vol ops to Doit
* add 2 services to interact with Doit API
* add form object to manage posting to Doit
* implement active_job with sucker_punch as backend to avoid extra cost from Heroku

but it took me going through the code, making my own notes, to really work out what was going on.  Then I ran the system locally to review how the UI looked:

![submitting a DoIt Volop](https://www.dropbox.com/s/90viie9yi9r7knu/Screenshot%202017-03-23%2014.20.56.png?dl=1)

It was great to see a complete solution from Marouen, but we hadn't done a UI mockup of how this should all work in advance.  Marouen was pushing up the code and driving from tests, but I hadn't given him wireframes, which should have been the first step.  I had just assumed that publishing a doit opportunity would be done through the existing volunteer op form with a checkbox.  A checkbox that when checked might reveal the other possible fields. Zmago had done something like that in the past, but of course Marouen hadn't necessarily seen that.

I notice that we've lost some of the layout on the forms, presumably as a consequence of the bootstrap upgrade.  Gah!  I think the user experience would probably make more sense if we just had a single form.  I'm thinking about a user wanting to update the description at the point that they are published to DoIt:

![existing form](https://www.dropbox.com/s/g9rj5plfli4zz2o/Screenshot%202017-03-23%2014.28.05.png?dl=1)

I made this scrappy mockup to show Marouen what I mean:

![mockup for merging two forms](https://www.dropbox.com/s/9a02yw28ypm7qte/Screenshot%202017-03-23%2018.24.47.png?dl=1)

I guess I should get input from the client on this, but my intuition is that this will be simpler in terms of our URLs and the workflow for the end user.  The risk is that I'm pushing for changes that will just exhaust Marouen.  The buck stops with me here of course - I should have ensured we provided a mockup earlier, or made sure to check out the PR earlier.  So many PRs, so little time ...

p.s. we won the nhs-wiki contract!
