It's another six week chunk before my next break (as determined by the cycle of my children's school holidays) and I have a couple of new plans.  One is having a private project tuesday where I try to work exclusively on private paid projects so that I can afford my children's music, sports and club expenses.  The other is to try and get someone setup to be able to the tech startup for all the meetings that I currently run for AgileVentures.  I still want to attend those meetings but I want to make sure that whether they run or not is not a function of my availability.

Anyway, I've started gettting committments from people to be available to start particular scrums, and today is Tuesday and time for focus on the private computer-assisted translation (CAT) project that I'm working on.  I've also gotten another programmer involved so that at least some part of the day can be spent pair programming.  I'm hoping that this will improve my motivation and focus.  The lead developer is not sold on the pros of pair programming but so in the first instance I'll be splitting some of my fee with the other developer.  This might seem like a retrograde step in terms of income, but I'm hoping it will work out to better output and more income in the long run.

So in preparation for onboarding the new developer I should get bang up to date with the project's latest code and issues.  There's no new code on the develop branch, so the first thing is to check the tests there ... although I stop to upgrade to the latest verison of yarn (1.7.0).  I have to delete the config directory to get the acceptance tests running.  Some of the usual culprits fail - in particular I notice that the clipboard related acceptance test grabs the yarn upgrade related command that I had just copied and pasted a moment previously, indicating that there's something fishy about the way in which the OSX clipboard related tests are operating.  Otherwise there are seven failures; I know they are the usual suspects as I have annotated them with "OSX fail".  The lead developer is not keen on that; I should probably adjust those to tags like we've done on some of the open source projects ... I was experiencing better behaviour from the tests by running them from within vscode, so I guess I try that although I feel like I burn time on these test runs where it's unclear to me if I can use my computer in parallel ...

Running in vscode I only get two failures, and given then I know there is intermittency here I am not super-excited to dive into them.  Part of the reason I was trying to get these sorted was so that I could properly evaluate the project_manager branch where we are refactoring and adding new functionality.  I guess I check what the status of the tests is there ... three failures on a run there - and I think these runs are taking a minute or two, I should check.  Great gaps to check Email and Slack on my phone or other computer given that using my main development machine in parallel interferes with the acceptance tests; or does it?  Is that my imagination.  It certainly did when I originally created them, but the lead developer has improved them.  Could we run them headless?  Gosh, so much devops/testing stuff to sort out.  Or not sort out?  Try to focus on the domain model and unit tests instead?

The difficulty with backing out into the tests and focusing on test-support and test-consistency is that in a part time project one loses focus on the actual feature you are trying to develop ... although maybe that's just as much an issue in a full-time project.  I guess if you had the support of a full-time testing team that would be another matter.  What to work on:

1) adding some tags to allow exlcusion (or focus) on particular OSX failing acceptance tests
2) reviewing where we were up to with the project manager
3) trying to get cucumber working in order to allow pithier descriptions of the system functionality
4) further banging of head against acceptance test fails ...

My little cucumber experiment is stuck on an "Application not running" error in the AfterAll tear down step.  I tried following the cucumber electron example from this [spectron-cucumber-example](https://github.com/ericyahhh/spectron-cucumber-example), which works locally for me.  I've adapted the support files to our CAT electron app, and it occurs to me that I'm just screwing up the Promise chain ...  I read the following docs:

* https://github.com/cucumber/cucumber-js/blob/61f01c47d2850a95727d27684dbf5f4268ef6620/docs/support_files/hooks.md#beforeall--afterall
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all
* https://stackoverflow.com/questions/33073509/promise-all-then-resolve
* https://stackoverflow.com/questions/35805603/are-nested-promises-normal-in-node-js

and postulated that I could unpack the nested promises that I have in the BeforeAll hook, but I'm not sure that would make any difference, as maybe the app is not starting correctly due to some other problem; or is it even just that the AfterAll is being too aggressive in its failure mode?  That said I'm not actually seeing the electron app pop up, which makes me think it's not starting ...

Ah, fixed it.  It seems that I had to return the result of `app.start` explicitly - there's no proprietary code here so I think I can share this fix, which was taking this:

```js
BeforeAll(() => {
  // other setup code
  const promises = []
  // other setup code
  return Promise.all(promises)
    .then(() => { app.start().then(() => {
      setWorldConstructor(function () {
        global.app = app
      })
    })
  })
})
```

and adding a return statement before `app.start()`

```js
BeforeAll(() => {
  // other setup code
  const promises = []
  // other setup code
  return Promise.all(promises)
    .then(() => { return app.start().then(() => {
      setWorldConstructor(function () {
        global.app = app
      })
    })
  })
})
```

and it all works, electron app popping up, cukes passing.  I couldn't really tell you why that makes such a difference.  I was just about to start unpacking the promise chain and it occured to me that this would be a quick thing to test.  Well that's a nice way to have started the day off.  Time for coffee before our main pairing session.



