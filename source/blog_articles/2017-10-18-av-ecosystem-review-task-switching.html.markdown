They say that task-switching is very inefficient and that while we think we're multi-tasking we're not actually being really efficient.  Ironically I'm task switching all day long, and sometimes that's all about managing motivation.  Yesterday I couldn't decide between making a middleman landing page for the AV main site and investigating the performance issues we have with the site on Heroku.  So I started off on both ...

[https://github.com/tansaku/av-landing-page](https://github.com/tansaku/av-landing-page)

So on the middleman side I think the key things I need to check are:

1. deployment to github pages
2. CSS pipeline

while for heroku performance it's just reading docs.  I pull the docs up on my big monitor.  I install loader.io on the staging site, but find it is already installed.  Although it hasn't ever been used by the look of it ...

![getting started tips from loader.io](https://dl.dropbox.com/s/reu4c637j16izcc/Screenshot%202017-10-18%2009.58.50.png?dl=0)

and set up an initial load test:

![intial load test form](https://dl.dropbox.com/s/lx5yei9gm1udy6w/Screenshot%202017-10-18%2010.00.06.png?dl=0)

but I can't seem to test against our custom endpoint - I send a help request for that.  In the meantime I have to get a static file served on staging in order to start the load test ... so I get a pull request in for that.  I check that it works locally, but I'll need that to pass CI to get it onto staging.  I could force it up there, but it gives me a reasonable excuse to task switch and check the middleman items.  If I recall correctly we used a middleman-gh-pages plugin for the static nonprofit site in order to get middleman on github pages, but I wonder if that will work with the latest version of middleman?

[MiddleManGHPages](https://github.com/edgecase/middleman-gh-pages) hasn't been updated in a couple of years and there is also now [MiddleManDeploy](https://github.com/middleman-contrib/middleman-deploy) which has been more recently updated and has more stars.  A [2017 blog](https://ashfurrow.com/blog/building-static-sites-with-middleman/) mentions successfully using middleman-gh-pages and it is working for us on the static site with a middleman 4.1.7.  Stick with what I know? Try something new?  The following code in the README of middleman-deploy makes me want to try it:

```rb
# Rakefile
namespace :deploy do
  def deploy(env)
    puts "Deploying to #{env}"
    system "TARGET=#{env} bundle exec middleman deploy"
  end

  task :staging do
    deploy :staging
  end

  task :production do
    deploy :production
  end
end
```
```
$ rake deploy:staging
$ rake deploy:production
```

I like having staging and production endpoints - something I've been missing in the static nonprofit site.  In the meantime the pull request for the loaderio static page passed, so I pull that in and will need to wait for it to deply to develop before I can then move it on to staging for testing (or I test on develop ...), back to middleman deploy I find I need to upgrade to a newer version of middleman-deploy to get things working:

https://github.com/middleman-contrib/middleman-deploy/issues/93

but so the current README is out of date and the PR that fixes this has been hanging around for a year ... then there's another issue to fix:

https://github.com/middleman-contrib/middleman-deploy/issues/100

and then `build` and `deploy` both work, and I would appear to have successfully deployed to:

https://tansaku.github.io/av-landing-page/

although I'd feel better if the middleman-deploy 2.0 gem was out of alpha ... and I notice that the default middleman site assets are not loading on gh-pages.  Something to multi-task on tomorrow as I start the load tests ... :-)
