---
title: Mixing and Matching Guidelines
date: 2016-11-10
tags: pull requests node cucumber features feedback refactoring architecture rearchitecting domain driven design DDD rails mocha
author: Sam Joseph
---

It was another mix of the AsyncVoter and WebSiteOne projects today.  Overnight Arreche had come up with a [slimmed down version](https://github.com/AgileVentures/AsyncVoter/compare/master...arreche:7_cast_vote_feature_mvp) of Raphael's [pull request](https://github.com/AgileVentures/AsyncVoter/pull/50) on vote casting, so I focused on João's [pull request](https://github.com/AgileVentures/AsyncVoter/pull/45) for listing currently voting stories.   I really wanted to get something merged in as we'd had these two pull requests open for five days or so, and I also wanted to practice deploying a new feature onto the drie servers.

The async_voter slack channel was active and I was conflicted about posting comments in Github and in slack.  João showed up and said he had some time for programming and what should he change.  I suggested he take out the sorting functionality that wasn't covered by the acceptance test and he got right to it.  My original plan was that there might have been some AsyncVoter folks in the "Martin Fowler" scrum, but they weren't and I ended up helping out some Premiums fix up issues on the LocalSupport and WebSiteOne projects.

In the meantime João had pushed changes and we had a slimmed down pull request.  I still wasn't entirely sure about the mapping we had set up from `GET /stories?state=active` to search for `size=0` stories.  João was very keen on the state=active flag and seemed to be mollifying me by saying that `GET /stories?size=0` would also work.  I investigated and found that it didn't quite and that `GET /stories?state=voting` or indeed any other state would return all the size 0 stories.

I pointed that out to João not sure if he was in a position to fix it up.  I started adjusting the code locally and made my own [pull request](https://github.com/AgileVentures/AsyncVoter/pull/57).  In parallel João made further updates.  Perhaps we should have been waiting, but he was commuting.  Maybe I should have just gone to lunch, but I was pleasantly surprised to make some good progress on my own branch.  In particular discovering a [method to specify running a single test in a mocha suite](http://jaketrent.com/post/run-single-mocha-test/), where you insert a `.only` into the test you want to focus on:

```js
it("found one active", function (done) { ... });
```

becomes:

```js
it.only("found one active", function (done) { ... });
```

and you actually don't have to make any changes to the command line argument.  Making the above change, I can run `npm test` and I get precisely the focus I want.  Eat your heart out RSpec and Cucumber where I have to construct commands like:

```
be cucumber features/hangouts/edit_hangout_url.feature:31
```

which get derailed when the line numbers change.  So anyhow, on reflection I'm glad I did spend some time tinkering with my own PR.  Contrary to previous experiences, node seemed stable and I was able to jump from Cucumber acceptance tests to Mocha Unit tests and make good progress.  The mocking error messages were not as precise as I would have liked, but I think that may be something we can improve.

Of course I then had the quandary of whether to go with my PR or with João's new one.  He had now filled out the filtering functionality and added checks that only `state=active` would return size 0 stories.  I was now in a hangout with Michael who was asking good questions about what all this would do.  I started the server and used POSTman to hit the new endpoints.  The `state=active` filtering was working, but not the general filtering.  I think there were key conversion issues or something.  The key point here was the the general filtering functionality wasn't being hit by any tests.

Different guidelines were as usual wrestling in my head.  My concerns were as follows:

* was locking to size=0 to represent currently voted stories going to bite us when we decided we needed size=0 stories in the future?
* Wasn't it bad to have functionality not covered by tests, and particularly functionality unrelated to the feature the PR was addressing?
* Should we be adjusting the story model to have a state that could be active, do reflect the domain language that João was keen to use?
* Shouldn't we be going with my simplifying assumption that there is only ever a single story to vote on (for the time being)?
* Shouldn't we be sanitising inputs coming over the wire? although this is search rather than edit so ...

I wanted to do the right thing for the project, and also not offend João who had been putting in lots of great work.  All credit to him and the others who've got the Cucumber and Mocha set up all working so smoothly, along with a clean core of app code.  

I resolved to go with João's PR but I make some tweaks.  I stuck with João's preferred `state=active` but pulled out the generic filtering code because it didn't work, and wasn't covered by the tests.  So the core logic in the model class is:

```js
schema.statics.findBy = function(filter, callback) {
  let filterObject = {};
  if(filter['state'] && filter['state'] === 'active') {
    filterObject['size'] = 0;
  } 
  return this.model('Story').find(filterObject).sort('name').exec(callback);
}
```

My two biggest concerns remaining were that now the `findBy` method was named incorrectly and that we had quite a lot of mockup setup replication in the mocha test suite for the story model.  I created refactoring tickets for these two and got the PR merged in.  From a days distance I think it was a reasonable compromise.  Let's see how we feel in a week and a month and a year!

This also allowed me to try pushing a new feature to the drie server.  This failed with a 502 and super fast feedback from the drie team allowed us to identify that as a node versioning issue which we can hopefully fix today.  It also highlighted the need for a staging server as I had broken production in the process.  Although that's no disaster here as we only have a single test deploy that no one's using yet so it's not really `production`.

Michael and I snapped in some progress on WebSiteOne at the end of the session, finally wrapping in a cucumber test the bug we had been hunting the previous day.  Lots of refactoring imperative Cucumber steps to declarative needed there.  It's an interesting perspective; maintaining and evolving a Rails project, while shepherding the creation of a new Node micro service.  Stay tuned for more hair raising tails :-)

###Related Videos

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=rVfDq6YB9D4)
* [WebsiteOne and LocalSupport Triage](https://www.youtube.com/watch?v=dfmTQuYbwVU)
* [Pairing on AsyncVoter](https://www.youtube.com/watch?v=W1GPjCwBzNc)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=DlS0zvtKqhU)
* [Pairing on WebSiteOne](https://www.youtube.com/watch?v=My04-8l_INc)
* [More Pairing on WebSiteOne ](https://www.youtube.com/watch?v=Zb29w0BwtEw)


