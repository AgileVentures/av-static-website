---
title: AV EcoSystem Review One Thing After Another
author: Sam Joseph
---

![typewriter](../images/typewriter.jpg)

Some days it's just one thing after another.  As Wednesday was drawing to a close I was feeling pretty pleased to have fixes out for our Slack invites (confirmed as all the recent sign ups had already been invited this morning), and for one-off events not showing up.  I knew we also had some kind of javascript error affecting the calendar popups on the create event page, but it turns out that some kind of failure in our Google Analytics code is systematically breaking JavaScript througout the site.  I managed to fix that last night by turning off the analytics, but that's a short term fix as we need that working.

Am I just fire-fighting crazily here?  I'm thinking about the requests that Federico put in for RSVPs, alerts when members join projects, Fred asking for the jitsi server ...  Anyway this is systemic and we need to report analytics to the trustees.  I was able to replicate the failure on staging - perhaps I can replicate it locally.  I can trigger the error locally by running the server like so:

```
ENABLE_GOOGLE_ANALYTICS=true be rails s
```

The Jasmine tests work fine even with analytics enabled:

```
→ ENABLE_GOOGLE_ANALYTICS=true bej
[35019] Puma starting in cluster mode...
[35019] * Version 3.10.0 (ruby 2.3.1-p112), codename: Russell's Teapot
[35019] * Min threads: 1, max threads: 16
[35019] * Environment: development
[35019] * Process workers: 3
[35019] * Preloading application
[35019] * Listening on tcp://0.0.0.0:51775
[35019] Use Ctrl-C to stop
Waiting for jasmine server on 51775...
[35019] - Worker 0 (pid: 35027) booted, phase: 0
[35019] - Worker 1 (pid: 35028) booted, phase: 0
[35019] - Worker 2 (pid: 35029) booted, phase: 0
jasmine server started
.............................................****..........................
Pending:
	eventDatePicker hides event repeat options by default
	  awaiting refactor of related form for use in fixture

	eventDatePicker shows event repeat options when event is set to repeat
	  awaiting refactor of related form for use in fixture

	eventDatePicker shows event repeat optional ending when event is set to end
	  awaiting refactor of related form for use in fixture

	eventDatePicker hides event repeat optional ending when event is set to never end
	  awaiting refactor of related form for use in fixture

75 specs, 0 failures, 4 pending specs
Coverage report generated for RSpec to /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/coverage. 122 / 3516 LOC (3.47%) covered.
[35029] ! Detected parent died, dying
[35028] ! Detected parent died, dying
[35027] ! Detected parent died, dying
```

The local error is easier to understand:

![](https://dl.dropbox.com/s/xzo0oe3pgnquepo/Screenshot%202017-11-16%2009.52.32.png?dl=0)

and it seems that we can't use the variable `ga` in the modified sense in the current google analytics code:

```js
WebsiteOne.define('GoogleAnalytics', function() {
  window._gaq = [];
  window._gaq.push(['_setAccount', 'UA-47795185-1'])
  var ga = document.createElement('script'); ga.type = 'text/javascript'; ga.async = true;
  ga.src = ('https:' == document.location.protocol ? 'https://ssl' : 'http://www') + '.google-analytics.com/ga.js';
  var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(ga, s);

  function init() {
    window._gaq.push(['_trackPageview']);
  }

  var GA_LOCAL_STORAGE_KEY = 'ga:clientId';

  // the code below leads to the following error
  // Uncaught TypeError: ga is not a function
  //
  if (window.localStorage) {
    ga('create', 'UA-47795185-1', {
      'storage': 'none',
      'clientId': localStorage.getItem(GA_LOCAL_STORAGE_KEY)
    });
    ga(function(tracker) {
      localStorage.setItem(GA_LOCAL_STORAGE_KEY, tracker.get('clientId'));
    });
  }
  else {
    ga('create', 'UA-47795185-1', 'auto');
  }

  ga('send', 'pageview');

  return {
    init: init
  };
});
```

I think what we have here is a mix of old and new GA tracking code, and the problem that none of it actually gets run until we go intro production where google analytics is switched on.  Looking up both old and new code I can't immediately see a way to reconcile them, so I've commented out the offending code, and so will get that up until we can work out an effective combined approach.

I think rather than switching GA code on and off globally we should have different account ids for different instances and set that via ENV vars ...
