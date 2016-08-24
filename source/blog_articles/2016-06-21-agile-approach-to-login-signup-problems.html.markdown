---
title: Agile Approach to Login/SignUp Problems
date: 2016-06-21
tags: agile login signup
author: Sam Joseph
---

Nobody likes passwords.  Nobody enjoys signing up and logging in, but we need authentication, don't we?  The AgileVentures site is actually very open as regards who can edit what, the site is really one giant wiki; not that many people actually notice.  No one expects to be able to edit a site that isn't explicitly labelled as a wiki.

However we do want to be able to see who does the editing, and have a list of people participating in our community.  Hence signup and login (or signin).  Originally WebSiteOne (WSO - the codename for the [AgileVentures](http://www.agileventures.org/) site software) had three ways to signup:

1. signup via email address
2. signup via GitHub
3. signup via Google+

SignUp via a third party is a great convenience in that if avoids having to manage yet another password.  [AgileVentures](http://www.agileventures.org/) is a pair programming community and so it makes sense that most users would have a GitHub account to manage their code, and a Google+ account in order to use Google Hangouts, the default video conferencing and pair programming framework.

All was not rosy however.  We had to turn off the pure email sign up because the spambots caught us and starting creating hundreds of dummy accounts.

The GitHub signup (always?) had the problem that if the user's GitHub profile does not have their email set to public we get an error.  This is partly because our system is set to use email as the [unique identifier](https://github.com/AgileVentures/WebsiteOne/issues/1093) of the user, and also because we'd like to email users a welcome message when they sign up, support secure password reset etc.  A quick fix for this issue was a message to alert the user their GitHub email needs to be public, but complexities of the GitHub interface means that a non-trivial number of users get stuck.  

The Google+ signup/in used to work fine, but some change in the system added dynamic elements to the redirect URL making it [impossible to sign up](https://github.com/AgileVentures/WebsiteOne/issues/1063) (or sign in?) via Google+.  We were contemplating removing Google+ entirely, but that's complicated since users who previously signed up via G+ wouldn't be able to sign in and can't create GitHub accounts due to email conflicts with their old accounts - although this can be fixed with a password reset.  Working out all of the above was sucking up valuable resources, not to mention the negative reflection on the site.  We also wanted to be able to track who's pairing with who in which hangout which requires connecting Google+ ids to AgileVentures users.

Analysis of the sites' user footfall suggested that a clear majority of users were successfully signing up and logging in; making this whole issue an annoyance rather than a crisis.

![](https://www.dropbox.com/s/r59glrb1wg3qroe/google_analytics_wso_signup.png?dl=1)

That appeared to reduce the priority of fixing these issues, relative to other minor bugs currently that were being reported about the site, e.g. some timezones not being correctly detected, and working on new developments such as the static site and the premium members page which might lead to sustainability of AgileVentures in the long run.

You might say, well hey, let's add a captcha to prevent the spam sign ups and re-enable the pure email signup?  Yes, but do we really want to managing that tech?  That's adding a somewhat complex tech solution that GitHub and G+ have already handled for us.  Not to mention that we don't yet have the resources to pay for the SSL certificates that would completely secure pure email login.  AgileVentures is a community for everyone, but unlike FreeCodeCamp we're not focused on total beginners.  We assume you've got a modicum of programming experience. Although that said, we'd love to get more marketing and project management people involved - but they can login through Google+ - the absolute minimum requirement is that you can at least join a hangout.

So we focused on taking some simple steps, e.g. [removing the G+ signup button](https://github.com/AgileVentures/WebsiteOne/pull/1099), and adding [more instructions about how to handle GitHub settings for public emails](https://github.com/AgileVentures/WebsiteOne/pull/1120).  In my book *this* is the Agile approach at its heart - do the simplest possible thing to improve the situation and see if that helps before rushing into a more complex intervention like a [captcha](https://github.com/AgileVentures/WebsiteOne/issues/1067) or [adding a form for non-public GitHub users to add their email](https://github.com/AgileVentures/WebsiteOne/issues/1093#issuecomment-225562997).  Not that we rule out taking those steps at some point, but it's sensible to start with small steps to explore the issue to see if the resources to support the more complex solutions are going to be a wise investment.

Having taken those first steps we got a lid on the issue, i.e. no more complaints about Google+ sign up failure since it is no longer an option; and clearer documentation to help those getting stuck on GitHub email publicity settings.  The desire to support better tracking of pair partners led us to try and get to the bottom of the Google+ redirect url problem.  Turns out that [upgrading the Omniauth-oauth2](https://github.com/samdunne/omniauth-gplus/issues/25) gem introduced a change to the redirect_url that broke Google+ authentication, but not Github authentication.

The Omniauth test mode acceptance tests we had in place did not catch the change to the redirect\_url so we had no idea until users started encountering the problem - far from ideal.  Having manually tested that downgrading the Omniauth-oauth2 gem to version 1.3.1 did actually fix the problem, we spent a chunk of time looking for a more realistic acceptance test.  The problem is that there was no test that the redirect_url being generated was correct - something that can't be achieved with our acceptance test supporting tool Capybara; at least not without [hacking up the underlying Rails system itself](http://makandracards.com/makandra/15217-test-redirects-to-an-external-url-with-cucumber-capybara).

The compromise seemed to be creating a request spec, a sort of unit test for the Rails authentication controller; to check that the redirect url is formatted correctly.  This test should fail if someone were to accidentally upgrade the gem again - although we've locked it to the working version in the Gemfile.

We decided to push out the [fix](https://github.com/AgileVentures/WebsiteOne/pull/1126) ASAP and create a [chore](https://github.com/AgileVentures/WebsiteOne/issues/1127) for the request spec.  Partly because it would be great to have the fix out to support the legacy users who signed up with Google+ in the past, but also because experience tells us that we may have a further testing insight in a few days or weeks.

In summary, it's a complex web we weave, and to navigate it we need to take tiny steps, be careful about taking on maintenance burdens, or doing things that won't support our long term goals.  We've also got to be pragmatic about what we can actually test and balance the time spent searching for testing solutions with delivering needed features to users.
