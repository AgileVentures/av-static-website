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

Something we seem to really struggle with is having pull requests include cucumber tests.  It seems like there's quite a few folks who are comfortable with RSpec, but not so comfortable with cucumber.  Two other issues that need to be sorted in this PR are the use of the correct flags in the database, and the precise wording for consent, but I think I'm going to need to start driving this from a fresh cucumber test.

...

Slack has a [plan for GDPR compliance](https://slack.com/gdpr) apparently

It's a little unclear about how MediaWiki will comply with GDPR.  There's a presentation online about how to use Semantic MediaWiki to store information about how an organisation is complying with GDPR

[![MediaWiki and the European GDPR (datencockpit.at)](https://img.youtube.com/vi/59MMWPSIuOk/0.jpg)](https://www.youtube.com/watch?v=59MMWPSIuOk)

but that doesn't really address how the mediawiki software might need to change.  Wikipedia itself has a privacy policy:

https://wikimediafoundation.org/wiki/Privacy_policy

There's a [topic on the mediawiki support desk](https://www.mediawiki.org/wiki/Topic:Ucy8sfl44i6n6i51) but there's nothing there to help really.   I had been hoping that the latest version of mediawiki would be being update to include some GDPR consent options.

We have a privacy policy for the HLP wiki:

https://wiki.healthylondon.org/Social_Prescribing_and_Self_Care_Wiki:Privacy_policy

which was prepared by a legal team; and I'm starting to think that we may need to reach out to them to confirm whether the current privacy policy is sufficient given the changes associated with the GDPR.
