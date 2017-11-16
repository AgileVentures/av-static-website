So I'm on the train into london for a meeting with the NHS, but unlike in my last year of blogging it's not time to agonize about how everything's going, but time to focus on finishing up this feature for multiple project repositories.  Yesterday I checked that I could get the basic functionality that we need using the [Cocoon gem](https://github.com/nathanvda/cocoon).

Now I need to hook it up to the acceptance tests, consider whether we need unit tests, and work on the styling of the form.  A bit of kicking around with CSS and I got it to look like this:

![](Screenshot 2017-09-19 09.41.43.png)

Now to get the acceptance test working the first issue I have is that I've removed the field that we used to use for adding GitHub links, so the test fails to find it, and still fails to find it even after I rename the field ...

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (761_multiple_source_repository)]$ 
→ be cucumber features/projects/create_projects.feature:64
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
    And I fill in "GitHub link" with "http://www.github.com/new"           # features/step_definitions/basic_steps.rb:140
      Unable to find field "GitHub link" (Capybara::ElementNotFound)
      ./features/step_definitions/basic_steps.rb:141:in `/^I fill in "([^"]*)" with "([^"]*)"$/'
      features/projects/create_projects.feature:70:in `And I fill in "GitHub link" with "http://www.github.com/new"'
```

The actual name of the field is now:

```
project[source_repositories_attributes][0][url]
```

Strangely despite having capitalized the H in GitHub, the label is rendered in the page as follows:

```
<label for="project_source_repositories_attributes_0_GitHub Link">Github link</label>
```

that's from:

```erb
<div class='nested-fields form-group'>
  <div class='field form-group'>
    <%= f.label 'GitHub Link' %>
    <%= f.text_field :url, placeholder: 'https://github.com/projectname', class: 'form-control input-lg' %>
  </div>
  <%= link_to_remove_association 'Delete repo', f, class: 'btn btn-danger' %>
</div>
```

For some reason it's being downcased? Well I'll just go with the downcase then.  Hmm, I guess I'm restricted as to how I use the label method on the Rails form.  I thought I could just replace `:url` with the text that I wanted, but no go.  I switch back to url and try to fill in the field "Url", but no and also no joy for `project[source_repositories_attributes][0][url]` or `project_source_repositories_attributes_0_url`.

What I can see when I debug and inspect the html that capybara is seeing is that even though I have JavaScript enabled, it does not seem like the dynamic form fields are being created.  Instead I see this:

```html
<div class=\"links form-group\">
          
  <a class=\"btn btn-default add_fields\" data-association=\"source_repository\" data-associations=\"source_repositories\" data-association-insertion-template=\"&lt;div class='nested-fields form-group'&gt;
    &lt;div class='field form-group'&gt;
      &lt;label title=&quot;GitHub url&quot; for=&quot;project_source_repositories_attributes_new_source_repositories_url&quot;&gt;Url&lt;/label&gt;
      &lt;input placeholder=&quot;https://github.com/projectname&quot; class=&quot;form-control input-lg&quot; type=&quot;text&quot; name=&quot;project[source_repositories_attributes][new_source_repositories][url]&quot; id=&quot;project_source_repositories_attributes_new_source_repositories_url&quot; /&gt;
    &lt;/div&gt;
    &lt;input type=&quot;hidden&quot; name=&quot;project[source_repositories_attributes][new_source_repositories][_destroy]&quot; id=&quot;project_source_repositories_attributes_new_source_repositories__destroy&quot; value=&quot;false&quot; /&gt;&lt;a class=&quot;btn btn-danger remove_fields dynamic&quot; href=&quot;#&quot;&gt;Delete repo&lt;/a&gt;
  &lt;/div&gt;\" href=\"#\">Add more repos</a>
  </div>
</div>
```

which looks like the raw HTML that Cocoon delivers before JavaScript adjusts it.  I wonder if that's because I'm doing this on the train without internet connection (and that's affecting loading some needed js); but I can see the Cocoon js being loaded in chrome when I test manually ... ah, I think I see the problem.  The project doesn't have a default first repo - we have to click 'Add repos' once first ... or we can build one by default:

```rb
  def new
    @project = Project.new
    @project.source_repositories.build
  end
```
and leaves me wondering if there is some way to have the different fields be numbered 1, 2, 3 etc. That would make the Cucumber/Capybara testing easier and it looks like there might be an approach that does that here:

https://github.com/nathanvda/cocoon/issues/374

Something for tomorrow ...





