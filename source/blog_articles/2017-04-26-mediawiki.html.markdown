Having estsablished that mediawiki's default and visual editors both worked smoothly on the NHS laptops on the NHS intranet, it seemed pretty much wrapped up that we'd be using mediawiki for the NHS Social Prescribing project.  The question then became should we host it ourselves or use a third party mediawiki hosting service.  The main mediawiki site provides a [list of hosting services](https://www.mediawiki.org/wiki/Hosting_services) which are sortable in terms of different features.  I pulled out all the offerings that included visual editor support:

* [Miraheze](http://meta.miraheze.org/)
* [MyWikis](http://www.mywikis.com/)
* [Wiki Valley](https://wiki-valley.com/)
* [Wikibase solutions](https://www.wikibase.nl/Diensten/Managed_hosting)

The list also included Wikia and Gamepedia, both free with advertising and both targeting the video-game/entertainment market.  Possible, but long shots I thought; for healthcare we'd want a more serious tone.  I contacted all four of the above organisations to try and get set up with a test wiki with a visual editor installed.  Miraheze's site didn't immediately load so I got on to the others

MyWikis gave me access to their testing wiki sandbox which had the visual editor but it got stuck loading on all browsers I've tried and they responded to my help request reasonably promptly, but only to say that I should try browser refreshes until it worked, which was not very helpful.

Wikivalley responded to my request to say they would create a dedicated demo wiki for us as well as giving us access to a sandbox, but I still haven't managed to edit a page anywhere despite several attempts.  The notification emails and the site are in French.

Wikibase didn't want to provide a demo environment and say they only minimally support the visual editor. Instead they apparently focus on semantic wiki and page forms as that's what they're saying their users wants.  Sounds like an interesting future possibility (submitting data to wikis via forms) but not the best short term option.

Conversely Miraheze is a volunteer run system receiving donations to pay for hosting and have no guarantees in their t&c but they have actually been the fastest to set up and the only mediawiki hosting service to get us a working visual editor.  I had to log into a separate feature requesting system (Phabricator) to get the visual editor enabled, and their site has been responsive all week since that outage I experienced the first time I went to their main site.

In parallel I used [Bitnami](https://bitnami.com/stack/mediawiki) has allowed simple deployto deploy our own mediawiki instance to azure where we can config the media wiki directly, and I was able to adjust the main logo and do some preliminary testing.

We've got a set of features we need sorted:

* WYSIWYG editor (optional initially, but might be crucial down the line ...)
* Can disable anonymous edits
* File upload
* Custom domain
* Can we insert custom HLP header to make it look like part of HLP site ...?
* HTTPS ...
* moderation support

I think my next priority must be to request all the additional features enabled we need in the hlptest.miraheze.org mediawiki, and then see how quickly I can set them up in parallel on our own server.  I started by [requesting the disabling of anonymous edits](https://phabricator.miraheze.org/T1720):

![requesting disabling of anonymous edits on miraheze via phabricator](https://www.dropbox.com/s/ecfs2s49des8xmj/Screenshot%202017-04-26%2009.46.45.png?dl=1)

So let's see how quickly I can do this on our bitnami/azure system.  First I need to log in to azure.bitnami.com, which gives me a screen showing both the link to our running instance, and the ssh credentials I need to ssh directly into the box where I can edit the mediawiki settings.  I'd previously updated the logo by editing `/apps/mediawiki/htdocs/LocalSettings.php`.  So I went back there to try to disable anonymous edits:

https://www.mediawiki.org/wiki/Manual:Preventing_access#Restrict_anonymous_editing

This time I needed to go to `includes/DefaultSettings.php` and set the following:

```
$wgGroupPermissions['*']['edit'] = false;
$wgGroupPermissions['*']['createpage'] = false;
```

and I had successfully disabled the ability of anonymous users to edit existing pages or create new pages.  Meanwhile I'm waiting for miraheze to process my request.  I might rue the day since it may well be a total pain to set up the visual editor or SSL on our bitnami/azure hosted system, but I don't think we can use miraheze for anything other than testing purposes, since we'll need server side access to fix things ourselves in a hurry if we have a specific request from the NHS team.

Maybe ideally I'd now spend the rest of the day working through all the other features in turn.  In the first instance I think I'll just request them all from miraheze and then see if I can knock off a couple of other low hanging fruit.  Although as I look I see that file upload appears to be enabled by default, and there are just restrictions about which sorts of files can be uploaded.  HLP is asking about uploading Excel files, which sounds like something we can easily add later as needed.

Miraheze has a [whole page on how to request custom domains](https://meta.miraheze.org/wiki/Custom_domains).  I don't know when an HLP subdomain will be created for us, so it's expedient to see if we can set up subdomains on av.org and see if we can get our bitnami/azure box hooked up like that.  That reminds me we will really need SSL (something miraheze has out of the box), and we need to up our Azure admin skills.  If only AWS or DO had given us such credits ... guess I could be bitnami deploying to AWS and taking the financial hit, but we need to skill up in Azure admin for the AsyncVoter and to take advantage of the credits from MicroSoft.

So I've set up http://hlp-wiki.agileventures.org to point to our Azure/Bitnami system, and we'll see if that works, but we'll have to wait for the zone file to propagate.  For stability we'll need to reserve the IP address which requires powershell installation.  I also see an article for [ssl setup](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-configure-ssl-certificate).  It might be painful but I think we can get all that done.  The other things to impale ourselves on are setting up the wysiwyg editor, working out the moderation strategy and whether we're going to have the HLP banner across the top of the wiki.
