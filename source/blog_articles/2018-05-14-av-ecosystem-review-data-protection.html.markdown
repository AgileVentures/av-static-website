A new regulation is coming into force in the EU on May 25th called the [General Data Protection Regulation](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) or GDPR, which appears to require some changes on the part of everyone processing any kind of data about individuals.  Pretty much any website where you create an account stores information about you as an individual, so this means that pretty much every organisation with an account creating website needs to take steps.  

We've been aware of the GDPR for a while and have brought it up in a few meetings and taken some steps to allow users to provide explicit consent when signing up for the AgileVentures site.  As I review the GDPR terms in more and more detail it seems like there is an increasing list of things we need to do:

A) Documenting all the data we hold
B) Updating privacy policies
C) Ensuring users can delete and/or access their data
D) Updating consent during sign up
E) Checking age during sign up
F) Security review

We've had a volunteer working on a pull request on the AV site to add some consent terminology and checkbox into our sign up process, but that volunteer has no more time to finish up, so I'm going to need to sort this out myself.

https://github.com/AgileVentures/WebsiteOne/pull/2298

Something we seem to really struggle with is having pull requests include cucumber tests.  It seems like there's quite a few folks who are comfortable with RSpec, but not so comfortable with cucumber.  Two other issues that need to be sorted in this PR are the use of the correct flags in the database, and the precise wording for consent, but I think I'm going to need to start driving this from a fresh cucumber test.  However we have a number of existing cucumber tests that might be affected, e.g. 

```gherkin
  Scenario: Let a visitor register as a site user
    Given I am on the "registration" page
    And I submit "user@example.com" as username
    And I submit "password" as password
    And I click "Sign up" button
    Then I should be on the "getting started" page
    And I should see "Signed up successfully."
    And the page should contain the google adwords conversion code
    And the user "user@example.com" should have karma
    And I should see a successful sign up message
    And I should receive a "Welcome to AgileVentures.org" email
    And replies to that email should go to "info@agileventures.org"
```

```gherkin
 Scenario: User signs up with valid data
    When I sign up with valid user data
    Then I should see a successful sign up message
```

between these two tests we've kind of got the worst of both worlds.  We've got one sure short test that doesn't tell us much more than it's own description, and another one that's so long that it's difficult to read.  Now both of these will fail without the migration on the current branch being enacted, but I don't think we want to use a new `emails_opt_it` boolean field when we already have `receive_site_emails`.  I guess what we really need are tests that check that the boolean is set correctly for the two different cases of giving consent and not giving consent, so let's adapt the shorter test:

```gherkin
 Scenario: User signs up successfully giving no consent for mailings
    When I sign up with valid user data
    Then I should see a successful sign up message
    And I go to my "edit profile" page
    Then "receive mailings" should not be checked
```

I think cucumber scenarios ideally follow the same maxim as methods, that they shouldn't be much longer than five lines.  At the moment this test is not failing in the right way, I need to remove the migration and update the existing code to use the existing boolean.  Doing that allows the test to bite.  It fails, indicating that the checkbox is set to false.  Not the default we want.  I git remove the migration to allow me to explore the site in development mode:

```
git rm db/migrate/20180418182311_add_emails_opt_in_to_user.rb
```

I see the checkbox is ticked on by default.  Precisely what the GDPR says not to do.  It looks like the exising `receive_mailings` boolean on the User model is defaulting to false.  We need a migration to fix that:

```
bundle exec rails generate migration SetUsersReceiveMailingsDefaultFalse
```

```
class SetUsersReceiveMailingsDefaultFalse < ActiveRecord::Migration[5.1]
  def change
    change_column_default :users, :receive_mailings, from: true, to: false
  end
end
```

and now the test passes.  Now we need another test to check that consent can be given:

```gherkin
Scenario: User signs up successfully giving consent for mailings
    When I sign up with valid user data giving consent
    Then I should see a successful sign up message
    And I go to my "edit profile" page
    Then "receive mailings" should be checked
```

I got that to pass after a bit of fiddling about - the usual Capybara malarcky for clicking checkboxes:

```rb
 find(:css, "#user_receive_mailings").set(@visitor[:receive_mailings])
```

But we seem to have failing rspecs on develop that are preventing me from getting this all out.  There are broader GDPR issues to consider.  Slack has a [plan for GDPR compliance](https://slack.com/gdpr) apparently, but it's a little unclear about how MediaWiki (which we use in the NHS project) will comply with GDPR.  There's a presentation online about how to use Semantic MediaWiki to store information about how an organisation is complying with GDPR:

[![MediaWiki and the European GDPR (datencockpit.at)](https://img.youtube.com/vi/59MMWPSIuOk/0.jpg)](https://www.youtube.com/watch?v=59MMWPSIuOk)

but that doesn't really address how the mediawiki software might need to change.  Wikipedia itself has a privacy policy:

https://wikimediafoundation.org/wiki/Privacy_policy

There's a [topic on the mediawiki support desk](https://www.mediawiki.org/wiki/Topic:Ucy8sfl44i6n6i51) but there's nothing there to help really.   I had been hoping that the latest version of mediawiki would be being update to include some GDPR consent options.

We have a privacy policy for the HLP wiki:

https://wiki.healthylondon.org/Social_Prescribing_and_Self_Care_Wiki:Privacy_policy

which was prepared by a legal team; and I'm starting to think that we may need to reach out to them to confirm whether the current privacy policy is sufficient given the changes associated with the GDPR.
