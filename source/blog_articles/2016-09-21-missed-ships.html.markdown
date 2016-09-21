I got confused yesterday about adjustments to the pairing schedule.  I thought Michael was going to be available later in the day, but actually he had to leave early.   As a result our pairing was contracted to an hour, and he started on a new task working solo.  Michael was looking at adjusting the way we managed our 3rd party JavaScript assets in the WebSiteOne Rails app.  We've been storing them in the vendor directory and checking them into our source code repository.  This doesn't make for a smooth upgrade and dependency management strategy.  

We've been hearing that npm is becoming the standard for JavaScript dependency management, with bower falling out of favour.  Although clearly the [conversation is still ongoing](https://www.quora.com/Why-use-Bower-when-there-is-npm).  Anyhow, Michael started some [solo work](https://github.com/AgileVentures/WebsiteOne/pull/1278), adding a package.json and a script to copy files from the node_modules directory into the rails vendor directory.  In our quick pairing session we got his spike working on my machine, and updated the install documentation.  After Michael had to go I made some adjustments to our CI and the heroku build packs to ensure that this would all work with the rest of our development pipeline.  I think I sorted the CI, but not the heroku, which required another solo pass from Michael after I got back, although the fact that I was migrating our servers from group to individual accounts means I'm not sure we've been able to run a final check that the Heroku install works.

Anyhow, it's interesting that I would not have picked this particular task off the backlog if Michael and I were choosing together.  I'd have been pushing for things that might have a more direct impact for our users.  Michael chose this one because he was interested in it, which is a great motivation.  I would also have been worried about opening a can of worms here.  However it seems like we might have been able to sort it in an otherwise chaotic day, and we've explored an important part of our stack, as well as revealing some javascript files that shouldn't have been checked in.

Modern software engineer management often talks about groups like Steam or Github where groups of programmers just experiment with things that interest them and those things turn into "product" as a function of programmer interest.  I'd find it easier to take that attitude if I was independently wealthy and not worrying about whether I'll have to go find a different job or completely pivot in three months or so.  However as I was saying in the heuristics blog yesterday, once you've started a task ... my strong temptation is to try and switch tasks, or rebuild something, but maybe that's just too disruptive.  Strains the social bonds of the people you're working with?  Pick your battles they say :-)

Michael is looking ahead to interesting vistas involving replacing the whole Rails asset pipeline with npm and gulp, which is apparently the in-thing these days.  The thing that was highlighted to me yesterday is how we need to improve and update our installation documentation.  We've migrated the wiki into the repo doc directory, but there's still a lot of cleanup to do.  I'm also reminded that this stuff needs to be tested with installs on fresh machines.  I did an install on C9 yesterday which went smoothly, but made it look like this could be turned into an install script.

I also installed docker and used it to create a vanilla ubuntu instance, that I started installing WSO on.  Docker was great - installing WSO on ubuntu less so, with a series of sticking issues for the upfront install of phantomjs using npm.  I was pleased to find a way round my old friend:

```
root@8c7aad239bc9:~# npm install -g phantomjs
npm WARN deprecated phantomjs@2.1.7: Package renamed to phantomjs-prebuilt. Please update 'phantomjs' package references to 'phantomjs-prebuilt'
npm WARN deprecated tough-cookie@2.2.2: ReDoS vulnerability parsing Set-Cookie https://nodesecurity.io/advisories/130
/root/.nvm/versions/node/v5.10.1/bin/phantomjs -> /root/.nvm/versions/node/v5.10.1/lib/node_modules/phantomjs/bin/phantomjs

> phantomjs@2.1.7 install /root/.nvm/versions/node/v5.10.1/lib/node_modules/phantomjs
> node install.js

sh: 1: node: Permission denied
```

with http://sourcode.net/sh-1-node-permission-denied/

but that only lead me to a follow up error:

````
root@8c7aad239bc9:~# npm install phantomjs
npm WARN deprecated phantomjs@2.1.7: Package renamed to phantomjs-prebuilt. Please update 'phantomjs' package references to 'phantomjs-prebuilt'
npm WARN deprecated tough-cookie@2.2.2: ReDoS vulnerability parsing Set-Cookie https://nodesecurity.io/advisories/130

> phantomjs@2.1.7 install /root/node_modules/phantomjs
> node install.js

PhantomJS not found on PATH
Download already available at /tmp/phantomjs/phantomjs-2.1.1-linux-x86_64.tar.bz2
Verified checksum of previously downloaded file
Extracting tar contents (via spawned process)
Error extracting archive
Phantom installation failed { Error: Command failed: tar jxf /tmp/phantomjs/phantomjs-2.1.1-linux-x86_64.tar.bz2
tar (child): bzip2: Cannot exec: No such file or directory
tar (child): Error is not recoverable: exiting now
tar: Child returned status 2
tar: Error is not recoverable: exiting now
```

and it was getting late.  I did try a few things, but I left that to tinker with later.  I probably should get more into docker and look into not accessing that ubuntu image as root.  I'm intrigued by the idea of publishing a WSO docker image and/or docker install script.  What would be really nice would be if our install scripts (docker or otherwise) could be run via acceptance tests.  It's ironic that our CI is installing the app from scratch a dozen times a day.  We really should have an instantaneous deploy for anyone wanting to get set up with the project.

So anyway, maybe me screwing up the pairing schedule was serendipitous.  I often find myself shy of things that I think will take a long time, but then my intuitions are also wrong sometimes.  I remember when an AV volunteer (Pavel) first set up Travis for us.  Not knowing much about it it felt like a big thing to get into; and then I saw that all Pavel had done was flick a switch and add a .travis.yml file.  Of course there's a lot more to go wrong with CI as your project gets bigger, but sometimes the younger person just giving things a try, reveals that the older person's fears of a briar patch are unfounded.  And then there are other times when the older person's fears are correct and you get stuck in a sticky mess.  If only one could predict reliably which way round it's going to end up ... :-)  

So my moral for today is pair with people of all levels, and don't beat yourself up too much when you screw up the schedule, or start on a task you have misgivings about.  Better to enjoy the process and see what emerges :-)

Related Videos:

* [Pairing on AV main site](https://www.youtube.com/watch?v=OihKyI6SEew)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=hbO9l328TR0)


Related Links:

* [https://github.com/rstacruz/npm-pipeline-rails](https://github.com/rstacruz/npm-pipeline-rails)
* [https://gofore.com/stop-using-bower/](https://gofore.com/stop-using-bower/)
* [https://medium.com/@nickheiner/why-my-team-uses-npm-instead-of-bower-eecfe1b9afcb](https://medium.com/@nickheiner/why-my-team-uses-npm-instead-of-bower-eecfe1b9afcb)

 
