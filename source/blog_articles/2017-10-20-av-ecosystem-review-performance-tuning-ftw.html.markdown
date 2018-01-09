Late to the blogface yada, yada, yada.  I'm really enjoying performance tuning.  Let's take another look at what's going on.  I can see from yesterday's test runs that I quickly pushed the AV site into to heavy memory error territory.  I also restarted the server after the tests and what's interesting is to see that we actually start encountering memory errors on the staging server in the absence of performance testing:

![heroku metrics](https://dl.dropbox.com/s/532rzq46emgci3h/Screenshot%202017-10-20%2010.19.45.png?dl=0)

The staging server is not coming under any particular load, which implies that we've got some sort of memory leak just in the resting state of the server.  Not ideal.  It's also clear that the simple throw money a the problem would be to double the size of the memory, and help Heroku with their bottom line, but it would be far more satisfying to actually solve the problem.  I've had no follow up on my question in the issues of `derailed_benchmarks` about why we get an error running `perf:mem_over_time` on the production app locally.  However the Heroku metrics indicate we do have a memory leak, so perhaps we can skip that and move on the analysis phase.  All the `perf:mem_over_time` operation tells us is that we are using 211MiB on startup, which I can see matches the restart value for memory usage on heroku.  Let's try the "Dissecting a Memory Leak" suggestion: `perf:objects`.  That may just hit the same error, and in fact is does. 

I had two quick ideas for fixes - turning off https locally and checking I can see the app in a browser running locally in production mode.  I did the former and was able to do the latter, and didn't see an improvement.  But then realised I was turning off https in LocalSupport, not WebSiteOne.  I switched that off and we were in business:

```
→ AIRBRAKE_PROJECT_ID=6 AIRBRAKE_API_KEY=b681e09 bundle exec derailed exec perf:mem_over_time
Booting: production
Endpoint: "/"
/Users/tansaku/.rvm/gems/ruby-2.3.1/gems/derailed_benchmarks-1.3.2/lib/derailed_benchmarks/tasks.rb:72: warning: already initialized constant DERAILED_APP
/Users/tansaku/.rvm/gems/ruby-2.3.1/gems/derailed_benchmarks-1.3.2/lib/derailed_benchmarks/tasks.rb:23: warning: previous definition of DERAILED_APP was here
PID: 92587
213.2734375
237.4296875
247.52734375
252.6953125
257.9375
260.125
262.21875
265.7578125
272.25
274.1640625
274.796875
275.80078125
276.546875
277.171875
277.60546875
278.09375
278.6015625
278.8359375
279.28125
281.1171875
281.26953125
281.390625
281.57421875
281.67578125
281.859375
281.03125
270.57421875
270.9296875
271.6640625
271.984375
272.671875
250.19921875
251.328125
242.16015625
240.8203125
241.41796875
241.546875
...
```

So what we are waiting to see here is if the memory never levels off.  Another waiting activituy.  And actually sometimes the memory level is dropping before gradually creeping up again - hmmm.  And nowhere near the levels we're seeing running this on Heroku.  Of course this test is kind of profiling what's happening locally on the OSX machine, not whatever virtual machine is in use on Heroku.  Here's our puma config:

```
workers Integer(ENV['PUMA_WORKERS'] || 3)
threads Integer(ENV['MIN_THREADS']  || 1), Integer(ENV['MAX_THREADS'] || 16)

preload_app!

rackup      DefaultRackup
port        ENV['PORT']     || 3000
environment ENV['RACK_ENV'] || 'development'

on_worker_boot do
  # worker specific setup
  ActiveSupport.on_load(:active_record) do
    config = ActiveRecord::Base.configurations[Rails.env] ||
                Rails.application.config.database_configuration[Rails.env]
    config['pool'] = ENV['MAX_THREADS'] || 16
    ActiveRecord::Base.establish_connection(config)
  end
end
```

So things we could try include:

1. reducing the number of puma workers (although we have 2 on staging and 1 on production already)
2. try a puma worker killer


Other things that spring to mind are adjusting the number of threads (MAX_THREADS = 3 on staging, 2 on production), but I think we had a round of tweaking puma workers and threads to no avail.   The rest of this Puma config looks okay.  RACK_ENV must be production ... but now I can run `perf:objects` and I get the following info about which gems are allocating the most memory:

```
allocated memory by gem
-----------------------------------
   7874184  ice_cube-0.11.1
   7862554  activesupport-4.2.10
   1372984  tzinfo-1.2.3
    760790  ruby-2.3.1/lib
    751451  activerecord-4.2.10
    121937  WebsiteOne/app
    114186  newrelic_rpm-4.1.0.333
```

and the retained memory by location:

```
retained memory by location
-----------------------------------
     23609  /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/app/views/visitors/index.html.erb:1
      5200  /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/newrelic_rpm-4.1.0.333/lib/new_relic/agent/transaction/trace_node.rb:24
      5016  /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/newrelic_rpm-4.1.0.333/lib/new_relic/agent/transaction/trace.rb:54
      3384  /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/rack-1.6.8/lib/rack/mock.rb:92
      2240  /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/newrelic_rpm-4.1.0.333/lib/new_relic/agent/transaction/trace_node.rb:35
```

and various other stuff that makes it feel like it's the event scheduling time and timezone stuff that's causing the biggest memory headaches.  The Puma worker killer is a stop gap.  I guess the best looking options are:

1. try removing the `nearest_time_zone` gem since it's the 
2. dissect the visitors index.html.erb file to work out where it's snagging on memory

I tried removing the timing section and re-running the memory performance over time, but it just jumped up.  I run the perf:objects again and that knocks the index.html.erb file out of the "retained memory by location" list.  I notice that the home page is not using the application layout for some reason.  Time with zone from active_support is still being heavily used.  It seems like ice_cube is being heavily activated, even when I think I've removed the time/scheduling stuff from the home page.  I wonder if that's just its resting state ...?  I run the analysis again removing everything from the home page and yes the icecube stuff disappears.  It looks like we are rendering other sub-elements that are pulling in info on event scheduling hmmm ... so I guess I should analyze the different partials:

1) _head.html.erb: javascript and stylesheet include tags - nothing suspicious
2) _navbar.html.erb: checks if user is signed in, user presentation, checking for next event (avoided on home page?)
3) _require_users_profile.html.erb:  some stuff from user - rarely displayed
4) _footer.html.erb: static page paths and not much else
5) _round_banners.html.erb: next event stuff

`nearest_time_zone` is tied up with various things for the User and looks like it will be tricky to understand, let alone remove ... re-running it seems like it's only the event portion of the round banners that's giving us memory overhead here.  Weirdly I can't even work out where the event object is coming from ... ah, we have some before_filters in the ApplicationController:

```
  before_filter :get_next_scrum, :store_location, unless: -> { request.xhr? }
  
  def get_next_scrum
    @next_event = Event.next_occurrence(:Scrum)
  end
```

which I guess brings us to the main issue for the loading of the home page, which is that we want to display the time to the next event, and that requires working out when the next event is, and we are generating the full schedule from IceCube on the fly each time.  In principle it would be far more efficient to have it so that all events, and their repeats, were stored (say in a db) and then fetched read-only when needed.  They could then be updated on write operations, which would be far less frequent ... but that would be a major refactoring of the system.  If the homepage still wants to include the countdown, then a static site is not going to help much ... hmmm.
