---
title: Different Strokes for Different Folks
date: 2016-11-01
tags: Cucumber Agile Voting Estimating Stories Features CucumberHS Chai Stripe PayPal
author: Sam Joseph
---



Today was a series of intense work outs.  The clocks went back in the UK meaning that we were back in UTC time, so standups broke the day up at 10:30am and 3:45pm.  The "Martin Fowler" scrum was almost full, with folks from both the MetPlus and AsyncVoter projects.  We had an ["AsyncVoter Catch Up"](https://www.youtube.com/watch?v=AfsoOtqv0eg) immediately after the scrum, which lasted over an hour and we had a good debate over three issues, specifically:

* Should be using Cucumber (difficult setup)
* Do we need more development of the Story schema
* Shouldn't we push on to creating the Vote model?

Various people had been working on adding CucumberJS over the weekend, and it seemed that there was concern that Cucumber was just replicating our existing integration test on the RESTful API for the Story model and that it wasn't moving us forward, particularly to the extent that it was proving difficult to set up.  I had strong feelings here and knew that I could easily end up dominating the conversation.  Having clarified that these were the three issues I went round everyone in the meeting, starting with the least outspoken folks to make sure that we heard their point of view before more outspoken folks like myself - I am guilty as charged! :-) 

There was a variety of viewpoints and I'm really glad we got them all in.  Some people were in favour of Cucumber, others against, and I think everyone was keen to move on.  Michael and I countered the point that Cucumber was pure replication by saying that yes, it was, and even though we didn't have a non-technical client, the key value of the Cucumber stories would be making it easier for new developers and ourselves in the future to understand why particular features of the code were there.  We also agreed that if we couldn't quickly get Cucumber working we'd need to drop it.

I pulled in some of the spikes from the weekend and pretty soon we had something working.  The key confusion had been about what testing model to use in combination with CucumberJS.  The AsyncVoter mob had been coming together and then splintering.  I think between them they had all the things they needed to move forward, but it was partly a confidence issue.  Just to give you a flavour, here's an example of the existing integration test:

```js
    it('GET /stories', function (done) {
        chai.request(server)
            .get('/stories')
            .end(function (err, res) {
                res.should.have.status(200);
                done()
            });
    });
```

Which was paralleled by a Cucumber story like so:

```gherkin
Scenario: Testing Cucumber
    When I make a GET request the response status code should be "200"
```

and we got a "tracer bullet" of this working with a step definition like so:

```js
  this.When(/^I make a GET request the response status code should be "([^"]*)"$/, function (code, callback) {
      chai.request(server)
          .get('/stories')
          .end(function (err, res) {
              if(res.status == code) {
                callback()
              } else {
                callback("res status was " + res.status)
              }
          });
      
     });
```

The crucial thing here was that this would fail correctly.  We weren't yet using nice chai assertions, but there were [libraries](https://github.com/domenic/chai-as-promised) that would help us with that.  

The other thing I emphasised again was that we wanted to be very careful making changes to the functionality and model schema that weren't driven by high level user stories that had gone through the estimation process.  I've burned too many hours in projects suffering from feature creep not to make that point.  That said, I also pointed out that to me the core of Agile is that the team reflects regularly on what works for them, and tries out changes.  BDD, TDD, Cucumber, pairing, mobbing, user stories, estimating are all on the table, and can be removed or tinkered with if the team thinks it's a good idea to try that out.  The way I channel Agile is that there are a lot of group and coding processes that can give us value, so it's great to start with a few tried and tested procedures that you are comfortable with.  At the same time allow the team to evolve its own style, but don't allow the baby to be thrown out with the bathwater.  After a tinker or a change, see how things go.  Reflect after 1 week, 2 weeks, a month.

It was a good session, and after lunch I jumped into a pairing session with Michael on WebsiteOne.  We had a good chat about pairing techniques and how to work together effectively.  There was so much to do on WebsiteOne.  There were performance issues emerging on the events page.  The fixes that I had got in for hangouts needed follow up and new UX flow to match the post Hangout Button API switch off reality.  However we have to be sustainable, and I let the money lead us.  I had one potential Premium signup if we had PayPal in place.  All sorts of other fixes were certainly needed, but if I don't bring in enough money I'll be back to being distracted by a closed-source 9-5 in January; so that's the heuristic I'm working on.  

The AsyncVoter and LocalSupport projects seem to have more interested parties at the moment, meaning that we hadn't yet voted on the [PayPal story](https://waffle.io/AgileVentures/WebsiteOne/cards/580787b7bbf5b55b01565370).  In another context the absence of estimation would stop me from working on the ticket, but I think you also have to know when to ignore guidelines.  Michael and I spent a while exploring the PayPal options, discovering that they support recurring subscription payments.  It became clear pretty quickly that we could actually get a test PayPal button up without anything like the integration we needed for Stripe:

```html
<form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_top">
  <input type="hidden" name="cmd" value="_s-xclick">
  <input type="hidden" name="hosted_button_id" value="N5R9T269YU7XC">
  <input type="image" src="https://www.paypalobjects.com/en_GB/i/btn/btn_subscribe_LG.gif" border="0" name="submit" alt="PayPal – The safer, easier way to pay online!">
  <img alt="" border="0" src="https://www.paypalobjects.com/en_GB/i/scr/pixel.gif" width="1" height="1">
</form>
```

PayPal button tools allow you to create a subscribe button in association with a PayPal business or charity account.  I had already set up an AgileVentures charity account on PayPal, so it was just a question of copying the above code into an appropriate location.  We tried putting it in a static page, but some important tags got filtered out, so we slapped it into an extra endpoint on the already abused `ChargesController`, wrapped it in a cucumber test (checked it failed) and got it out.  Michael was suggesting adding a redirection endpoint, but the next scrum was coming up and a meeting with a sponsor, and I wanted to get to the point where this was up and we could test if it would work, and more importantly, if people would use it.  If we could validate those two, then it would be worth investing more time.

Michael's questioned whether I go too close to the metal here.  It's like extreme Agile.  You could argue that if you're going to take someone's money then you should be doing a lot more work to make everything perfect and just so.  I'd love to have the resources to throw at that.  We started the Stripe integration at absolute bare bones, and as people signed up and made comments about things that looked odd, we fixed those things.  I plan to do the same for PayPal.  Raoul got the new feature out super fast and we got our first sign up.  Now I can be confident that more time investing in redirection end-points and tweaking button colour schemes is time being spent in the context of a revenue stream.

On reflection a great day.  The Paypal addition was super simple, and ironically I think we might have moved faster six months ago if we'd started with Paypal and not Stripe, but now we have both and that's great.  I retrospectively assigned a "1" to the PayPal story because that's what it had been, and resolved to get a CONTRIBUTING.md page up for the AsyncVoter project.  I hope I won't be crucified as a hypocrite, but I really do believe this Agile idea of "no one size fits all".  Every project is different.  Every team needs to adjust its processes to suit circumstances.  I'm going to push AsyncVoter towards more voting and estimating before stories are started to avoid people stepping on each other's toes.  WebSiteOne is quieter also because it's a priority project, so fewer people are eligible to work on it, but if we can get AsyncVoter automation in place maybe we can get more stories estimated faster for WebSiteOne; the two projects will synergise and it will be win-win for everyone! 


### Related Videos:

* ["Martin Fowler" scrum](https://youtu.be/C-E_5uj-YgQ)
* [AsyncVoter Catch Up](https://www.youtube.com/watch?v=AfsoOtqv0eg)
* [Pairing on WebsiteOne](https://www.youtube.com/watch?v=zVS_tyX3Lq8)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=IK8O5U4lmfg)
