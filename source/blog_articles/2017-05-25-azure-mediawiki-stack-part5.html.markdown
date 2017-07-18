So, after feeling lethargic and getting distracted by only a small amount of Slack and email, it's time to see if I can repeat the trick of getting the MediaWiki VisualEditor to work with https.  Maybe this is all just ridiculous Yak-shaving.  I'm getting a steady flow of spam on the wiki and I need to try out some solutions without damaging the production server.  I want to try out various things on staging before they are in production.  So yes, coherent argument for getting the staging server setup, but then the newly deployed box is on a slightly different version of MediaWiki and installing the letsencrypt SSL all suddenly went more smoothly, so for all my efforts to create a clone of production, will there still be differences creeping in? With technology, trivial differences in versions can lead things to break; however, driving to get that 100% match can also suck up ever increasing amounts of time ...

Heroku's deploying via git with something like bundler to ensure that we have precisely the same set of libraries really does feel like a beautifully clear approach, and we can get the same from dokku.  I wonder if there's some similar way to deploy mediawiki that would allow us to version all our configuration.  And how long would it take to find it and set it up? :-)

Task in hand - let's continue.  Got to log into four ssh terminals for start up ... so I got distracted there (for 15mins?) and I used `ssh-copy-id` to set it up so I can log in with my private key rather than having to use the password every time.  That should save some time in the long run.

What I lack is a good record of exactly what changes I made to the apache config on the production box, although comparing `/opt/bitnami/apache2/conf/httpd.conf`, I don't see any differences.  The version of certbot that I've used this second time around has put the server certificate and key in `/opt/bitnami/apache2/conf`, but the main changes are in `/opt/bitnami/apache2/conf/bitnami`, where I did make a copy of the file `bitnami.conf`.  The initial change required is to ensure that the site is no longer served over insecure port 80:

```
<IfVersion < 2.3 >
  NameVirtualHost *:80 
  NameVirtualHost *:443
</IfVersion>

<VirtualHost _default_:80>
  DocumentRoot "/opt/bitnami/apache2/htdocs"
  Redirect permanent / https://hlp-wiki-develop.agileventures.org/ # <-- add this
  # ... unchanged
</VirtualHost>
```
and that whenever anyone tries to access via http over port 80 that they are redirected to the secured https on port 443.  Then restart Apache:

```
$ sudo /opt/bitnami/ctlscript.sh restart apache
```
and the site can only be accessed over https, but the problem is now that the VisualEditor won't work, or at least it didn't the first time around for me where I seemed to need to set up all sorts of port redirection, but something about this second install means I don't seem to have encountered this issue.  Fascinating.  I don't know if I screwed something up the first time around and made lots more work for myself, OR if the bitnami stack/certbot I used has been updated.  Definitely there was a bump to the mediawiki version and I have gone in with a different certbot command.

So where I'm confused now is how good a clone I have of the first box, and the best way to upgrade the first box.  I have another layer of indirection with the first box using the wiki.healthylondon.org domain name, but I don't think that should affect things too seriously ... is it possible that I was completely misdiagnosing the VisualEditor failures?  It's possible, because we had that very tricky bug where only pages with outstanding edits held in the moderation queue were blocking the editor, but I thought that was only on IE.

I checked the chrome developer tools network panel and all the requests to the VisualEditor are going across https.  I'll check on IE and then I think its time to get down to work.  I want to:

1. clone across the data from the production server
2. run backups
3. experiement with a restore from backup
4. experiment with the spam filters suggested by the moderation extension maintainer
5. use this as a base to test out other things like defaulting create page to the VisualEditor, etc.

What I'd really like to do is have a mechanism for "switching" the production server to the newly created box and retiring the old one, so that I can avoid having to upgrade the boxes in place.  It seems like that switching would require DNS changes that take a long time to propagate.  I wonder if Azure has some mechanism for switching between virtual machines ...?  What I'd really like is a broader team to do this all with, but that'd require another level of funding ...
