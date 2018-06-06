So yesterday I had by first private projects tuesday, and I paired with a colleague on getting them set up with the private project and wrangling through a chunk of issues related to getting the project working on windows.  That itself could be the topic of a whole blog.  I then nipped off to a digital charities meetup on GDPR, which could be another entire blog again.  This morning I've sat down and tweeted the couple of tech podcasts I listened to on the last two mornings jogs, and got distracted by email and slack to discover that our Google AdWords account has been suspended and that our HarrowCN Google Maps API key seems to have been deleted.  It's all kicking off, leaving me wondering what to focus on right now ...

Part of me wants to work on another couple of hours of private project related items, but my brain is being strangled by all the different administrative things I keep remembering I need to do.  I think rather than firefighting right now I'm going to start the day by looking up one thing for the private project; how to set tags on mocha tests so that they can be excluded or focused on.  That was easy, there's a nice page in mocha's wiki:

https://github.com/mochajs/mocha/wiki/Tagging

```js
describe('app', function(){
  describe('GET /login', function(){
    it('should respond with the login form @fast', function(){
      
    })
  })

  describe('GET /download/:file', function(){
    it('should respond with the file @slow', function(){
      
    })
  })
})
```

then run with `--grep @fast` or `--grep @slow`.  Let's try that out ... hmm, superficially not working for me as I try in combination by passing the flags into the yarn commands, place them in the package.json, or even run via the debugger.  I feel like I need a simpler example codebase (or just smaller set of tests) to work with so that I can quickly get to the bottom of this.  Maybe I should just create one from scratch - I notice this wiki page I'm looking at was last modified in 2012.  I create a little mocha testing suite and it all works as described:

```
→ npm run test:not:slow

> mocha_test@1.0.0 test:not:slow /Users/tansaku/Documents/GitHub/tansaku/mocha_test
> mocha --grep @slow --invert test/**/*.js



  app
    GET /login
      ✓ should respond with the login form @fast


  1 passing (5ms)
```

So with the private project pretty much all the "save document" tests are failing from the console, but none of those are tagged with @osx_fail.  Checking I see that the individual tests I have exluded are not running, but the problem is that the individual tests being excluded are not independent.  The way these acceptance tests are set up, we have a series of it blocks and each of them is like a step in a cucumber tests, i.e. intended to be run in sequence.  By skipping some of them we are causing the others to fail.  Perhaps the short term solution is to add the tags to the describe blocks?  I do that, but it doesn't help with the two test failures in the vscode, where neither the it blocks nor the describe blocks are being excluded.  
