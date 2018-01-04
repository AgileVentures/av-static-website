---
title: AV EcoSystem Emotional Hurdles
tags: 
author: Sam Joseph
---

![hurdles](../images/hurdles.jpg)

Late to the blogface due to feelings of lethargy and accidentally reading all my Slack and Emails.  Not so many of them as I was checking them pretty late last night as well - blurgh.  However the mixed strategy of investigating performance and a new static landing page site in parallel seems to be going reasonably well.  At least I'm making some progress on both fronts.  I've got the loaderio tag on staging:

[https://staging.agileventures.org/loaderio-bd60fb88537b106821044fd9098f271c.html](https://staging.agileventures.org/loaderio-bd60fb88537b106821044fd9098f271c.html)

And the helpful folks at loaderio showed me how I could get the load test to hit [https://staging.agileventures.org](https://staging.agileventures.org) rather than the herokuapp URL, so time to kick off a load test.  But actually no, because now we have a new hash code lookup, blargh.  So I could try and make this dynamic, but perhaps it won't change now that we have fixed to staging.agileventures.org, but then again perhaps it will change for every new test we create.  Verification via DNS is now an option ... either way it's going to take an hour or two to actually get to the load test.  So I set up a dynamic approach with the following in the routes file:

```rb
def loaderio_token
  (ENV['LOADERIO_TOKEN'] || "loaderio-296a53739de683b99e3a2c4d7944230f")
end

WebsiteOne::Application.routes.draw do

  get loaderio_token => 'static_pages#loaderio'
```

and this in the static pages controller

```rb
  def loaderio
    render plain: ENV['LOADERIO_TOKEN'] || "loaderio-296a53739de683b99e3a2c4d7944230f", layout: false
  end
```

And I get a pull request in, but now I have to wait for the CI to pass.  So I have to task swap somewhat.  It's all about the emotional hurdles.  I was de-motivated to look into performance testing as I thought it would be a pain to set up and difficult to get to the bottom of the problem.  I'm reinvigorated by having found loaderio, even though I haven't actually got it working yet.  Conversely while I was more excited about getting a middleman landing page set up, I've now taken an emotional knock due to the challenges I encountered yesterday with middleman-deploy.  And that's even when I got it all working, but the knock is from finding that both middleman-gh-pages and middleman-deploy are looking a little under-maintained.  Middleman-deploy is marginally more active, and does have some nice functionality.  My next task there is sorting out the asset pathways, and actually maybe they will just start working when we deploy to a custom domain ... although I was pestering myself with instead staying focused on the performance issue and reading more about Ruby memory management.  Here's what Heroku says:

> If you’re getting R14 - Memory quota exceeded errors, it means your application is using swap memory. Swap uses the disk to store memory instead of RAM. Disk speed is significantly slower than RAM, so page access time is greatly increased. This leads to a significant degradation in application performance. An application that is swapping will be much slower than one that is not. No one wants a slow application, so getting rid of R14 Memory quota exceeded errors on your application is very important.

The suggested solutions include:

* [Upgrading to at least Ruby 2.1](https://devcenter.heroku.com/articles/ruby-memory-use#ruby-2-0-upgrade)
* [Looking for memory leaks](https://devcenter.heroku.com/articles/ruby-memory-use#memory-leaks)
* [Avoiding too many webserver workers](https://devcenter.heroku.com/articles/ruby-memory-use#too-many-workers)
* [Forking behavior of Puma worker processes](https://devcenter.heroku.com/articles/ruby-memory-use#forking-behavior-of-puma-worker-processes)
* [Too much memory used at runtime](https://devcenter.heroku.com/articles/ruby-memory-use#too-much-memory-used-at-runtime)
* [GC tuning](https://devcenter.heroku.com/articles/ruby-memory-use#gc-tuning)
* [Dyno size and performance](https://devcenter.heroku.com/articles/ruby-memory-use#dyno-size-and-performance)

So we're already on Ruby 2.3.1, but I don't think we spent any time looking for memory leaks yet, and the project [derailed_benchmarks](https://github.com/schneems/derailed_benchmarks) appears to be something we can run locally to check, so we can quickly get into that while we wait for the load test environment to be ready ... I follow the install instructions and kick off `bundle exec derailed bundle:mem` to profile our gems.  I think must have done this before as I recognise the output:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1826_derailed_benchmarks)]$ 
→ bundle exec derailed bundle:mem
TOP: 120.8359 MiB
  nearest_time_zone: 48.3281 MiB
  rails/all: 17.0625 MiB
    active_record/railtie: 7.7188 MiB
```
and the identification of `nearest_time_zone` as the most memory consuming gem, more even than Rails itself.  After Rails the following three are the biggest:

```
  pivotal-tracker-api: 9.3438 MiB
  bootstrap-sass: 6.1289 MiB
  octokit: 5.1992 MiB
```

what I don't think we ran before was the check for leaking memory ...

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (1826_derailed_benchmarks)]$ 
→ bundle exec derailed exec perf:mem_over_time
Booting: production
/Users/tansaku/.rvm/gems/ruby-2.3.1/gems/airbrake-ruby-2.2.2/lib/airbrake-ruby/notifier.rb:33:in `initialize': :project_id is required (Airbrake::Error)
	from /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/airbrake-ruby-2.2.2/lib/airbrake-ruby.rb:135:in `new'
	from /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/airbrake-ruby-2.2.2/lib/airbrake-ruby.rb:135:in `configure'
	from /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/config/initializers/airbrake.rb:2:in `<top (required)>'
```

which gets stuck because trying to run in production we need an airbrake id, amongst many other things ... so in a flurry of firing and forgetting I get an AIRBRAKE token and id in and create a production database:

```
$ pg_dump -U postgres websiteone_development | psql -U postgres websiteone_production
```

and also deploy the loaderio dynamic token to staging and also set up a landing.agileventures.org domain and configure that ... now the deploy to staging won't finish till the CI passes so I can't actually fire and forget that.  I can try [http://landing.agileventures.org](http://landing.agileventures.org) and in fact that does exactly what I suspect - it's working and the CSS/JS assets are being pulled in correctly.  So that's a nice unblocked feeling ... but with derailed_benchmarks I'm stuck on:

```
→ AIRBRAKE_PROJECT_ID=6 AIRBRAKE_API_KEY=XXXX bundle exec derailed exec perf:mem_over_time
Booting: production
Endpoint: "/"
/Users/tansaku/.rvm/gems/ruby-2.3.1/gems/derailed_benchmarks-1.3.2/lib/derailed_benchmarks/tasks.rb:72: warning: already initialized constant DERAILED_APP
/Users/tansaku/.rvm/gems/ruby-2.3.1/gems/derailed_benchmarks-1.3.2/lib/derailed_benchmarks/tasks.rb:23: warning: previous definition of DERAILED_APP was here
PID: 79590
211.39453125
/Users/tansaku/.rvm/gems/ruby-2.3.1/gems/derailed_benchmarks-1.3.2/lib/derailed_benchmarks/tasks.rb:92:in `call_app': Bad request:  (RuntimeError)
	from /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/derailed_benchmarks-1.3.2/lib/derailed_benchmarks/tasks.rb:174:in `block (3 levels) in <top (required)>'
	from /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/derailed_benchmarks-1.3.2/lib/derailed_benchmarks/tasks.rb:173:in `times'
	from /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/derailed_benchmarks-1.3.2/lib/derailed_benchmarks/tasks.rb:173:in `block (2 levels) in <top (required)>'
```

so after a few searches I open an issue:

[https://github.com/schneems/derailed_benchmarks/issues/110](https://github.com/schneems/derailed_benchmarks/issues/110)

and call it a day ... but no I see that staging has depoyed so I kick off a test:

![config the homepage test](https://dl.dropbox.com/s/f5e6tu71lctah7o/Screenshot%202017-10-19%2010.53.45.png?dl=0)

which gives these results:

![](https://dl.dropbox.com/s/5han2hh44m7n1bx/Screenshot%202017-10-19%2010.54.38.png?dl=0)

which I now need to work out how to interpret - apparently the test was aborted due to reaching an error threshold.  I think what we are seeing here is the requests timing out as the server comes under heavy load.  I need to run this again while watching the logs ... and when I do I see the memory errors:

```
2017-10-19T09:58:58.775050+00:00 heroku[web.1]: Process running mem=680M(133.0%)
2017-10-19T09:58:58.775050+00:00 heroku[web.1]: Error R14 (Memory quota exceeded)
```

so at least now we have a way to test possible solutions to the problem ...
