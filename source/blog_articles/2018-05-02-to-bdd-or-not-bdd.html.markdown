So inspired by listening to BDD creator Dan North on the >Code podcast this morning, and pausing only briefly to send email apologies to all the project leads we are emailing about new members joining their projects (apologies due to some missing details and inability to unsubscribe), I'm now at the blog face and pushing myself to get some acceptance tests describing the project functionality we're working on for the private coding project that I described yesterday.

Seeing the [spectron-cucumber-example](https://github.com/ericyahhh/spectron-cucumber-example/blob/master/features/app.feature) I write the following gherkin sketch:

```gherkin
Feature: Project Management 

  Scenario: See list of existing projects
    Given two projects exist
    When I open the app
    Then I should see those two projects

  Scenario: Clicking on a project opens it and closing it returns us to the main project list
    Given two projects exist
    When I open the app
    And I click on the first project
    Then I should see the project view for the first project
    When I close the project
    Then I should see those two projects and no more
```

Now, radically, what I'm not going to do is burn several hours hooking this all up.  Of course who knows, it might just take a few seconds, but the key thing I wanted to get straight in my mind was what we are trying to achieve in a simple English language summary.  Currently what is happening is that the existing projects are displayed, but that opening and/or closing a project is creating a clone of the project so we end up with an ever increasing list of projects ...

Given the great debugging support in vscode, I hook the current system on the debugger and see that the projectFilename is currently being reported as null, so that each time we click a project it goes on to a create a new one.  I think that means that the way I have the click on projects hooked up is out of date ... I'm currently passing over the name of the document to be opened, when what we want to be doing now is passing over the project file name ...

So I switch that over, and hook things up so that the project load pathway is now being used when we are trying to open an existing project, but ... opening the project leads to a blank screen and some kind of parsing error that I can't immediately unearth.  The front end just displays "No sentences found in file 'undefined'", and the debug log doesn't show any more detail.  I step through the code path in the debugger but don't get to that point, so search for that error message in the codebase, and it's just beyond the project loading and creating code, where we check if the document parser has any sentences.  That's something else I can hook on the debugger ...

Okay, so it seems like we have a timing issue.  We hit the check for sentences before the project file is actually loaded.  Maybe a situation where we could use the `await` keyword ..., but superficially not. I guess it's time to split up some functions and connect some promises ...
