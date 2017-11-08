The new slack update message notifications for scrum hangouts and youtube links is out and working:

![](https://dl.dropbox.com/s/fam5pv49ug99lcc/Screenshot%202017-11-08%2010.35.44.png?dl=0)

and at least two users are happy with the change.  So now I really must push on to the peformance issues.  I've merged the new feature flag that allows us to turn off the pre-loading of the next scrum, but I need that on staging to test it against production data ... I've got my load test of the homepage all ready to go on heroku via loader.io.

In the meantime I guess I should check that the new flag is working as expected on https://develop.agileventures.org/, but I go there and the next scrum is still on the home page:

![](https://dl.dropbox.com/s/5bpzaknnj99icn1/Screenshot%202017-11-08%2010.46.43.png?dl=0)

Hmm, no notification of deployment to develop - I guess I'll force it:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git push develop develop:master
Counting objects: 13, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (13/13), done.
Writing objects: 100% (13/13), 1.86 KiB | 1.86 MiB/s, done.
Total 13 (delta 10), reused 0 (delta 0)
remote: Compressing source files... done.
remote: Building source:
remote: 
remote: -----> Node.js app detected
...
remote:        https://websiteone-develop.herokuapp.com/ deployed to Heroku
remote: 
remote: Verifying deploy... done.
To heroku.com:websiteone-develop.git
   da240d16..f1e4c750  develop -> master
```

And now the upcoming scrum is removed from the develop server.  I set the feature flag via the Heroku settings interface:

![](https://dl.dropbox.com/s/hi2fsemtzjjhgbo/Screenshot%202017-11-08%2010.51.57.png?dl=0)

and the upcoming scrum is back with us.  Okay, feature flag is working as expected, but no sign of the build completing on staging.  I guess I will force it up so I can at least kick off the test before my next meeting, which I do ... and I get emailed the results - greater than 50% improvement:

![](https://dl.dropbox.com/s/a0qsfp7jv17uf30/Screenshot%202017-11-08%2015.47.33.png?dl=1)

Some decisions to be made here ...
