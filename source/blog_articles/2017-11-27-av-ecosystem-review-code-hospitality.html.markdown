---
title: AV EcoSystem Review Code Hospitality
author: Sam Joseph
---

![hospitality](../images/hospitality.jpg)

Pairing with Marian over the weekend we managed to clear up the last piece of junk in the RSpec log, the deprecation warning about the event_repeats boolean column in the events table.  It turned out that we could fix it simply by adjusting the specs, since it was they that were trying to force strings into the boolean column, not any part of the main app.  We drifted over a few other areas where issues could be investigated or things refactored.  We saw the code that was supposed to email notify us when there are 500 errors (which I never receive) and it looked like we could make the events code simpler, by pulling the string --> boolean transformations into the select options.

I wanted to focus this morning on code hospitality a la Nadia Odunayo, but I've been distracted by Slack and Email already, and putting the above items into new of existing issues:

* [500 error notifications](https://github.com/AgileVentures/WebsiteOne/issues/1808#issuecomment-347133830)
* [Event form refactoring](https://github.com/AgileVentures/WebsiteOne/issues/1992)

Anyhow, I found [Nadia's README template](https://gist.github.com/nodunayo/c919477906aab6c1af6065ff8e868d3e), so perhaps I can just quickly do a version for AgileVentures WebSiteOne

# AgileVentures WebSiteOne

This app powers the AgileVentures main developer site, showing lists of active projects, members, upcoming events, past event recordings, as well as all the machinery for Premium membership payments

## Installation

* link to installation doc

## Usage

* ???

## Contributing

* link to contributing guide

## History

* Sam Joseph had the idea for an online pairing community where everyone worked together to use the agile development methodology to deliver solutions to IT charities and non-profits.  Thomas Ochman joined as project manager and led the development of the WebSiteOne codebase with Bryan Yap serving as technical lead.  Initialy Sam was the notional "client", not getting involved in the tech development, and many different volunteers contributed code.  Later Raoul joined as project manager as Thomas and Bryan had less and less time for the project.  Sam switched roles joining Raoul as project manager, and is now the sole project manager as Raoul became unavailable.  

## Approaches

* Agile Development
  * We try to work from user stories in regular sprints, offer daily standups, and get regular feedback from end users
* Behaviour Driven Development (BDD)
  - We use Cucumber and RSpec testing tools that describe the behaviours of the system and its units
  - We try to work outside in, starting with acceptance tests, dropping to unit tests and then writing application code
  - We do spike occasionally to work out what's going on, but then either throw away the spike, or make sure all our tests break before wrapping in tests (by strategically or globally breaking things)
  - Where possible we go for declarative over imperative scenarios in our acceptance tests, trying to boil down the high level features to be easily comprehensible in terms of user intention
* Domain Driven Design (DDD)
  - Sometimes we switch to inside out, trying to adjust the underlying entity schema to better represent the domain model
* Self-documenting code
  - We prefer executable documentation (tests) and relatively short methods where the method and variable names effectively document the code

## Reading material

* [Imperative vs Declarative]()
* [JavaScript Accpetance test trials]()
* [What is DDD?]()


## Walkthroughs

* Some new feature we added ...
  * Here is the original feature request
  * This is the [spec]() that was written
  * Here's the [code]() that fixed that spec
  * And then [we refactored it]()
  
That's a start - more tomorrow ...  
