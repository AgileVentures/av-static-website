---
title: AV EcoSystem Review Why is Acceptance Testing so Time Consuming?
date: 2017-09-20
tags: 
author: Sam Joseph
---

![acceptance testing](../images/acceptance_testing.jpg)

I think I'm almost there with acceptance testing this multiple repo feature, but it makes me wonder why acceptance testing always seems to be so time consuming.  I'm just wrapping up a private project where acceptance testing of electron apps was a real pain, and I really question if it was time well spent since the resulting acceptance tests are slow and unreliable.  Perhaps all that time would have been better spent on unit tests and getting a really coherent domain model sorted out.  The acceptance testing framework I'm using for Rails is a bit more stable, although it's all a matter of degree; and already I've spent more time on the acceptance testing than on the feature itself for the multiple repo feature.  Although in the process I've learnt more precisely about the way the feature works and the nuances of the Cocoon gem.  Anyway, to attempt to wrap that up I'm going to try and get the dynamic repo form fields to be labelled numerically to make the acceptance testing easier, and try and avoid some of the issues brought up in this SO post:

[https://stackoverflow.com/questions/23184690/testing-fields-added-dynamically-by-cocoon-using-rspec-and-capybara](https://stackoverflow.com/questions/23184690/testing-fields-added-dynamically-by-cocoon-using-rspec-and-capybara)

I modified the code from [https://github.com/nathanvda/cocoon/issues/374](https://github.com/nathanvda/cocoon/issues/374) like so:

```js
  $(document).on('ready', function(){
    $('#source_repositories').on('cocoon:after-insert', function(e, added_repo) {
      var sourceRepositories = $('#project_form').find('.nested-fields');
      var sourceRepositoriesSize = $(sourceRepositories).size();

      $(sourceRepositories[0]).find('.repo_field_label').html('GitHub url (primary)');

      if (sourceRepositoriesSize > 1) {
        for(var i = 1; i < sourceRepositoriesSize; i++){
          $(sourceRepositories[i]).find('.repo_field_label').html(`GitHub url (${i+1})`)
        }
      }
    });
  })
  ```
  
with the idea to adjust the label names dynamically, but in the first instance the script didn't seem to run at all - I set a breakpoint in the chrome debugger, and loading the page didn't stop there, making me think something wasn't hooked up properly.  At least I thought that for a moment, and then I re-ran and it seemed like it did hook up. Hmm.  Well that gives me what I was hoping for:

![](https://dl.dropbox.com/s/404eit68ham28ow/Screenshot%202017-09-20%2009.35.59.png)

I then spent 15 minutes trying to refactor to use `forEach` or `for ... in` all to no avail as the jQuery is returning an object with numeric keys rather than an array, and there are other enumerable properties on the object, so let's see if the acceptance test can distinguish these form fields ... and no. Clicking on the "add more repos" link in the test does not appear to be generating the additional repo text field.  And maybe I never had that working in the test.  I worked out how to display an initial default field, but that doesn't require any JavaScript.   Ugh, I'm really regretting the time I just spent failing to refactor the JavaScript.  It feels like the JavaScript is just not even running for this test.

So now I feel stuck.  The functionality is working beautifully in the browser as I test it manually, but not in the test.  I have the @javascript tag on this test - other javascript tests run.  What I'm unclear about here is how can I go about checking that JavaScript is actually working in the test.  Looking at our hook definitions I see the following:

```rb
After '@javascript' do
  Capybara.send('session_pool').each do |_, session|
    next unless session.driver.is_a?(Capybara::Poltergeist::Driver)
    session.driver.restart
  end
end
```

According to the [Capybara docs](https://github.com/teamcapybara/capybara#using-capybara-with-cucumber) simply tagging with `@javascript` should be sufficient ... and when I hook on the debugger I can see that Capybara is using the expected poltergeist_billy javascript_driver:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (761_multiple_source_repository)]$ 
→ be cucumber features/projects/create_projects.feature:63

[Coveralls] Using SimpleCov's 'rails' settings.
Using the default profile...
@vcr
Feature: Create projects
  As a member of AgileVentures
  So that I can create a focal point for an AgileVentures project
  I would like to create a new project profile

  @javascript
  Scenario: Saving a new project with multiple repositories: success       # features/projects/create_projects.feature:64
    Given I am logged in                                                   # features/step_definitions/user_steps.rb:63
    And I am on the "Projects" page                                        # features/step_definitions/basic_steps.rb:84
    When I click the very stylish "New Project" button                     # features/step_definitions/basic_steps.rb:291
    When I fill in "Title" with "multiple repo project"                    # features/step_definitions/basic_steps.rb:140
    And I fill in "Description" with "has lots of code"                    # features/step_definitions/basic_steps.rb:140
    And I fill in "GitHub url (primary)" with "http://www.github.com/new"  # features/step_definitions/basic_steps.rb:140
    And I click "Add more repos"                                           # features/step_definitions/basic_steps.rb:88
Return value is: nil

[390, 399] in /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/features/step_definitions/basic_steps.rb
   390: end
   391: 
   392: 
   393: And(/^I debug$/) do
   394:   byebug
=> 395: end
   396: 
   397: 
   398: And(/^the window size is wide$/) do
   399:   Capybara.page.current_window.resize_to(1300,400)
(byebug) 
(byebug) Capybara.current_driver
:poltergeist_billy
```

I dug through the documentation and found various things of interest:

* [https://github.com/teamcapybara/capybara#asynchronous-javascript-ajax-and-friends]()

* [http://www.jonathanleighton.com/articles/2012/poltergeist-0-6-0/](http://www.jonathanleighton.com/articles/2012/poltergeist-0-6-0/)

* [https://github.com/teampoltergeist/poltergeist/issues/755](https://github.com/teampoltergeist/poltergeist/issues/755)

* [https://github.com/thoughtbot/capybara-webkit#configuration](https://github.com/thoughtbot/capybara-webkit#configuration)

* [https://github.com/teampoltergeist/poltergeist#remote-debugging-experimental](https://github.com/teampoltergeist/poltergeist#remote-debugging-experimental)

and the upshot of all this and some other hacking is that I now have remote debugging setup, which makes it look like JavaScript is running, in as much as the second field has been generated, but that it hasn't been renamed by the embedded script:

![](https://dl.dropbox.com/s/n2m1i3skeu12szp/Screenshot%202017-09-20%2010.25.54.png)

So I'm guessing I need to move this code to somewhere it might more reliably be run.  It clearly runs in the browser - ah, it looks like the ES6 interpolation I used might not be working ... yes, that was it - okay - this is a great step forward;  being able to see JS errors in capybara tests, yay!

And so now the interactive parts of the test passed, and I am onto errors that are all about me making sure that the data has been stored and updated correctly.  Tomorrow's work is clear ...
