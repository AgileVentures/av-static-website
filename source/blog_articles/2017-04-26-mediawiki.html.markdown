---
title: MediaWiki
date: 2017-04-26
tags: mirahese, mywikis, wikivalley, wikibase solutions
author: Sam Joseph
---

![mediawiki](/images/MediaWiki.svg)

Having estsablished that MediaWiki's default and visual editors both worked smoothly on the NHS laptops on the NHS intranet, it seemed pretty much wrapped up that we'd be using MediaWiki for the NHS Social Prescribing project.  The question then became whether we should host it ourselves or use a third party hosting service.  The main MediaWiki site provides a [list of hosting services](https://www.mediawiki.org/wiki/Hosting_services) which are sortable in terms of different features.  I pulled out all the offerings that included visual editor support:

* [Miraheze](http://meta.miraheze.org/)
* [MyWikis](http://www.mywikis.com/)
* [Wiki Valley](https://wiki-valley.com/)
* [Wikibase solutions](https://www.wikibase.nl/Diensten/Managed_hosting)

The list also included Wikia and Gamepedia, both free with advertising and both targeting the video-game/entertainment market.  Possible, but long shots I thought; for healthcare we'd want a more serious tone.  I contacted all four of the organisations on the list above to try and get set up with a test wiki with a visual editor installed.  Miraheze's site didn't immediately load so I got on to the others.

MyWikis gave me access to their testing wiki sandbox which had the visual editor; but it got stuck loading on all the browsers I tried and although they responded to my help request reasonably promptly, their suggestion that I should try browser refreshes until it worked was not very helpful.  Wiki Valley responded to my request to say they would create a dedicated demo wiki for us as well as giving us access to a sandbox, but I still haven't managed to edit a page anywhere despite several attempts.  The notification emails and the site are in French and they're not filling me with confidence.

Wikibase didn't want to provide a demo environment and said that they only minimally support the visual editor. Instead they focus on semantic wiki and page forms as that's what they're saying their users want.  Sounds like an interesting future possibility (submitting data to wikis via forms) but not the best short term option.  Conversely, Miraheze is a volunteer-run system receiving donations to pay for hosting and have no guarantees of service in their terms and conditions, but they have actually been the fastest to set up a working test site for us and the only MediaWiki hosting service to get us a working visual editor so far.  I had to log into a separate feature requesting system (Phabricator) to get the visual editor enabled, and their site has been responsive all week since that outage I experienced the first time I went to their main site.

In parallel I used [Bitnami](https://bitnami.com/stack/mediawiki) which allowed a very simple deploy to our own MediaWiki instance on Azure where we can config the MediaWiki directly, and I was able to adjust the main logo and do some preliminary testing.

I'm honing in on a set of technical features we need to support the higher level project requirements:

* WYSIWYG editor (optional initially, but might be crucial down the line ...)
* Can disable anonymous edits
* File upload
* Custom domain
* Can we insert custom HLP header to make it look like part of HLP site ...?
* HTTPS ...
* moderation support

I think my next priority must be to request all the additional features enabled we need in the hlptest.miraheze.org MediaWiki, and then see how quickly I can set them up in parallel on our own server.  I started by [requesting the disabling of anonymous edits](https://phabricator.miraheze.org/T1720):

![requesting disabling of anonymous edits on miraheze via phabricator](https://www.dropbox.com/s/ecfs2s49des8xmj/Screenshot%202017-04-26%2009.46.45.png?dl=1)

So let's see how quickly I can do this on our Bitnami/Azure system.  First I need to log in to [azure.bitnami.com](azure.bitnami.com), which gives me a screen showing both the link to our running instance, and the ssh credentials I need to ssh directly into the box where I can edit the mediawiki settings.  I'd previously updated the logo by editing `/apps/mediawiki/htdocs/LocalSettings.php`.  So I went back there to try to disable anonymous edits:

[https://www.mediawiki.org/wiki/Manual:Preventing_access#Restrict_anonymous_editing](https://www.mediawiki.org/wiki/Manual:Preventing_access#Restrict_anonymous_editing)

This time I needed to go to `includes/DefaultSettings.php` and set the following:

```
$wgGroupPermissions['*']['edit'] = false;
$wgGroupPermissions['*']['createpage'] = false;
```

and I had successfully disabled the ability of anonymous users to edit existing pages or create new pages.  Meanwhile I'm waiting for Miraheze to process my request.  I might rue the day since it may well be a total pain to set up the visual editor or SSL on our Bitnami/Azure hosted system, but I don't think we can use Miraheze for anything other than testing purposes, since we'll need server side access to fix things ourselves in a hurry if we have a specific request from the NHS team.

Maybe ideally I'd now spend the rest of the day working through all the other features in turn.  In the first instance I think I'll just request them all from Miraheze and then see if I can knock off a couple of other low hanging fruit on Azure/Bitnami.  Although as I look I see that file upload appears to be enabled by default, and there are just restrictions about which sorts of files can be uploaded.  HLP is asking about uploading Excel files, which sounds like something we can easily add later as needed.

Miraheze has a [whole page on how to request custom domains](https://meta.miraheze.org/wiki/Custom_domains).  I don't know when an HLP subdomain will be created for us, so it's expedient to see if we can set up subdomains on av.org and see if we can get our Bitnami/Azure box hooked up like that.  That reminds me we will really need SSL (something Miraheze has out of the box), and in general we need to improve our Azure admin skills.  If only AWS or DO had given us similar nonprofit credit schemes ... guess I could try a Bitnami deploy to AWS and taking the financial hit, but we do need to skill up in Azure admin for the AsyncVoter project and to take advantage of the credits from MicroSoft.

So I've set up [http://hlp-wiki.agileventures.org](http://hlp-wiki.agileventures.org) to point to our Azure/Bitnami system, and we'll see if that works, but we'll have to wait for the zone file to propagate.  For stability we'll need to reserve the IP address which requires powershell installation.  I also see an article for [ssl setup](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-configure-ssl-certificate).  It might be painful but I think we can get that all done.  The other things to impale ourselves on are setting up the WYSIWYG editor, working out the moderation strategy and whether/how we can have an HLP banner across the top of the wiki ... stay tuned for all those fun and frolics in the coming days.
