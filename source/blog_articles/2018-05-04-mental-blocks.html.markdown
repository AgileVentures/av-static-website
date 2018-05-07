Struggling to make progress this morning.  Slack/Email is all cleared though :-/ Am trying to knuckle down.  Sometimes I get overwhelmed by all the different things I want to get done.  Sometimes motivation collapses, sometimes it just getting stuck on decision making.  On Wednesday I indentified the issue with the closed source private project erroring out before it had managed to complete the loading of a new project file from the filesystem.  I was looking at the code and wondering whether it made sense to use the new await/async syntax.  The main developer read my blog draft and wanted to jump in to fix the existing code.  They explained they had avoied await/async throughout the codebase and preferred using Promises due to some odd experiences with await/async.  I keep having odd experiences with node/js which I think must be mostly down to various older models of programming that I'm still having trouble shaking.  I also feel more comfortable with Promises, and was happy to have the main developer fix up the `initialiseProject`.

In the meantime I got on with creating some more cucumber scenarios to cover the different user experiences we wanted, e.g. 

```gherkin
  Scenario: Creating a new project from file
    Given no projects exist
    When I open the app
    And I click "Open Project from File"
    And select "sam.txt"
    Then I should see the project view for "sam.txt"
    When I close the project
    Then I should see a new project for "sam.txt"
```
I also reflected that I was generating imperative rather than declarative scenarios, and so I wrote an abbreviated version in a more declarative form:

```
    Given no projects exist
    When I open a project from a file
    And I close the project
    Then I should see a project for that file
```

Updated code from the main developer had fixed the timing bug, and the UI was feeling stable.  There was a slight niggle however that I was trying to capture with the above scenario, which was that newly created projects wouldn't immediately show up in the list when the project was closed.  I played briefly with getting the elements needed to execute these cukes, but as my brain turned over the problem I came up with the solution.  The pure BDD/TDD practitioner would want to see the failing tests first, but I wanted to check if my reasoning about the underlying system was correct.  It was.  The operation to close a project returned a promise, so fixing the issue simply required placing the call to the re-open the project manager on file close in a `then(()=>{})` function chained onto the file close operation.

On the way back from the Agile Workshop on Wednesday I was tinkering on the train again and thinking about what other UI changes were needed.  I had some ideas, but realized that the main developer wanted a load of changes to the menu-ing and other things that were written in GitHub tickets and I didn't have with me locally.  In the absence of internet access I checked that I could get the example cucumber electron system running and passing green.  Yes, but I got stuck trying to do the same for cukes in main app.  And this is where the time can get burnt to shreds ... I got a little further adjusting the way the app was being loaded, but still stuck on this issue:

```
→ yarn test:cukes
yarn run v1.6.0
$ ./node_modules/.bin/cucumber-js test/features
.F-

Failures:

1) Scenario: See list of existing projects # test/features/project_management.feature:3
   ✔ Given two projects exist # test/features/step-definitions/app-steps.js:34
   ✖ When I open the app # test/features/step-definitions/app-steps.js:5
       TypeError: Cannot read property 'isMinimized' of undefined
           at Object.Given (/Users/tansaku/Documents/GitHub/qqtrend/sentience/app/test/features/step-definitions/app-steps.js:11:23)
   - Then I should see those two projects # test/features/step-definitions/app-steps.js:39

1 scenario (1 failed)
3 steps (1 failed, 1 skipped, 1 passed)
0m00.000s
```

This isMinimized check is coming from the boilerplate taken from the example setup.  It's trying to get the `browserWindow` from the electron `app.client`, and the equivalent test passes in the example ...  I'm also getting a "parsing failed" error in the electron app (which does appear, and hangs around after the cukes finish), and there's an "illegal operation on a directory" message in the log.  I suspect that this could all be fixed with appropriate tuning of the cuke setup, but how much time can go on that?

Last year I was contracted in part to add the spectron test framework to the system.  I got something pretty basic working, and the main developer took it and made it much more stable.  I had gotten quite irritated with it on OSX, but the main developer has it stable on linux, and although we have some failing acceptance tests on my OSX still, it seems to be generating some value.  It's making my development experience odd though.  The absence of a green baseline pushes me towards more spiking and means that I tend not to trust the existing acceptance tests.  Would it have been better to really eliminate those niggles before starting on this new project feature?

Anyhow, I tinker further with the way the cukes open the electron app, trying to learn as much as I can from the spectron helper setup that the main developer had knocked into reliable shape.  I'm still getting this in the log:

```
2018-05-04T09:33:22.362Z - error: Only one file can be opened at a time. Will only load "/Users/tansaku/Documents/GitHub/qqtrend/sentience/app/"
2018-05-04T09:33:23.367Z - warn:  Error: EISDIR: illegal operation on a directory, read
```

One thing I had struggled with regarding the spectron tests was having the electron app hang around after testing finishes, which the main developer seems to have reliably solved.  The only key difference I can see is a promise to remove the system config after shutdown ... this is the problem for me working with this app - there is so much stuff that isn't really in my head.  I suspect a lot of it is the main the developers head and that I am less efficient at getting things done than they would be.  If we'd been pair programming on this that might be another story, but funds for pair programming?  Feels to me like the ideal world is a full time team with at least four people where you can pair rotate regularly and keep everyone in the loop and learning and co-evolving the codebase, but that sort of ideal world requires money for four folks salaries ...

Interestingly the cucumber electron example shuts down cleanly.  So much more for me to learn about what the main electron application is doing.  Anyway, referring back to the tickets in GitHub, we've managed to spike the four tasks that I broke out of the Project epic:

* list existing projects
* each project clickable to open
* open project from file button
* open project from clipboard

The other elements requested are menu name changes, commandline changes and good integration with the simple editor for adjusting source files before they become part of a project.  So options here include:

* spiking the rest of the requested changes
* trying to get a green baseline in the specton tests on OSX to improve my development confidence
* writing some spectron tests for these four spiked elements
* getting cucumber working so I can have those tests in an executable higher level specification

To be honest I'm tempted to spend at least a little time on trying to get the green baseline on OSX for the code before the introduction of the projects feature, so that I can understand better what we might be breaking with the new projects code ...
