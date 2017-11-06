So I'm conflicted - what direction next?  On the table are:

1) review the process of people coming in from adwords
2) fix the email SPF setting
3) more work on greeter bots
4) search for other areas of ecosystem that are blocked up
5) preparing that video for dropbox to debug github image embeds

I finally managed to see one of our adverts that Lara shared:

![](../images/Screenshot%202017-09-11%2009.00.27.png)

and I'm still concerned about the message we're sending, i.e. the absence of any mention of team skills or agile in that ad.  It's so frustrating that I can't immediately get a simple view of our actual ads from the Google Analytics interface.  We also got the concern that our ads seem to be showing up particularly heavily in India, and we're not sure why.

Over the weekend I got the following from Elastic Email:

> Your Account sam@agileventures.org has one or more domains that were validated at one time but are no longer for some reason. How the domains became invalidated is not known but it is likely a configuration issue on your DNS for the domain(s) in question. It is important to take this step to ensure your emails are sending directly from your domain. Below is the list of domains that are not currently validated anymore: 

> agileventures.org

So it seems like that the recent SPF change in the Gandi settings is the issue here.  I passed that on to Federico, who suggested the following:

```
@ 600 IN TXT "v=spf1 include:_mailcust.gandi.net include:_spf.elasticemail.com ~all”
```

> Notice the change of Time To Live to 10 minutes instead of 3 hours, and the switch back to “~all”—the recommended policy statement. "?all" means neutral policy.

So I replaced the current:

```
@ 10800 IN TXT "v=spf1 include:_mailcust.gandi.net ?all"
```

with the suggested change, but I got this error

> Line 5: Error while parsing rdata: newline in quoted string

which was due to a non-ascii quote - switching that out and the DNS file saved.  Federico had shortened the TTL, so in principle we could check sooner if it was having the effect we wanted.

While I wait for that the frustration of wanting to embed an image made me want to record the video for dropbox.  I did that using the preview tab for creating this blog.  Here's what I sent them:

> Hi Marco,

> Thanks for this - here's a video:

> https://www.dropbox.com/s/2rjda5vuqaj1b0g/DropboxLinksNotWorkingForImagesOnGithub.mov?dl=0

> It shows me trying to preview an image embed from the following dropbox link:

> https://www.dropbox.com/s/nmurrdqxj76oq07/Screenshot%202017-09-11%2009.00.27.png?dl=1

> You can see that the image does not show up in preview - also note that for the last year links like these were showing up fine as images, so something somewhere changed recently.  As you'lls ee from the video GitHub grabs the image from you at dropbox and stores it on their own servers.  Here's what Steve Guntrip at GitHub said:

> > When ?dl=1 is specified, we get redirected to the image file with a non-image Content-Type header, Content-Type: application/binary. For your image to load, this Content-Type should be an image, like Content-Type: image/png.
> >
> > In this case, I would suggest reaching out to Dropbox's support to see if they can maybe support embedding images if the user agent is github-camo (our service that downloads these images to githubusercontent.com), or if they have another solution.

> Many thanks in advance

> Best, Sam

So it's almost time to test the email, but maybe time for a [quick node test rep](https://gist.github.com/tansaku/c311c996cc1521dceea0c0852849e9ca) and then I can check the email stuff by going to http://tools.wordtothewise.com/spf/check/AgileVentures.org and clicking on the DNS tab (as Federico suggested), where I see:

![](../images/Screenshot%202017-09-11%2009.25.55.png)

which I guess is telling me it's working?  I can also send an email from my account that was previously marked as spam ... and that's still coming through normally, so I guess that's sorted?  We'll see what happens with Elastic Email ... I guess that's enough progress for one day.  If I'm super lucky, I'll get back the easy image embed from dropbox/GitHub that'll make reviewing everything else a lot easier ...



