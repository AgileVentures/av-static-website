---
title: Azure MediaWiki Stack Part 4
date: 2017-05-24
tags: MediaWiki
author: Sam Joseph
---

![mediawiki](/images/MediaWiki.svg)

Well I totally failed to get down to my blogging today, checking Slack and Email first.  And blogging this week has become a series on getting the Azure MediaWiki stack up, which arguably should be a single document ... but then lots of people break complex things over multiple blogs.  It will need a lot of polishing back and forth as my stream of consciousness gets in the way of documenting things.  At least yesterday I managed to feel like I was making progress by getting the banner in there so that the cloned site looks like this:

![cloned site with banner](https://dl.dropbox.com/s/cze89vat8f09xs5/Screenshot%202017-05-24%2009.50.00.png?dl=1)

So the remaining hurdle is just setting up SSL, which as I was saying I'd partially documented in some comments in a post on the bitnami community:

[https://community.bitnami.com/t/installing-certbot-for-lets-encrypt-ssl-certificate/46431/2](https://community.bitnami.com/t/installing-certbot-for-lets-encrypt-ssl-certificate/46431/2)

The original post points us to the bitnami documentation on using LetsEncrypt:

[https://docs.bitnami.com/aws/components/apache/#how-to-install-the-lets-encrypt-client](https://docs.bitnami.com/aws/components/apache/#how-to-install-the-lets-encrypt-client)

Which walks us through installing something called certbot, during which I got/get the following error:

```
Failed to find executable apache2ctl in PATH: /opt/bitnami/sqlite/bin:/opt/bitnami/php/bin:/opt/bitnami/mysql/bin:/opt/bitnami/apache2/bin:/opt/bitnami/common/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

and it was google searches for that error that lead me to the bitnami community post.  The post suggests the following fix:

```
sudo ln -s /opt/bitnami/apache2/bin/apachectl /usr/bin/apache2ctl
```

and that allowed me to generate a certificate:

```
bitnami@bitnami-mediawiki-74f7:/tmp/certbot$ ./certbot-auto certonly --webroot -w /opt/bitnami/apps/mediawiki/htdocs/ -d hlp-wiki-develop.agileventures.org
Requesting root privileges to run certbot...
  /home/bitnami/.local/share/letsencrypt/bin/letsencrypt certonly --webroot -w /opt/bitnami/apps/mediawiki/htdocs/ -d hlp-wiki-develop.agileventures.org
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel):hlpwikiadmin@agileventures.org

-------------------------------------------------------------------------------
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf. You must agree
in order to register with the ACME server at
https://acme-v01.api.letsencrypt.org/directory
-------------------------------------------------------------------------------
(A)gree/(C)ancel: A

-------------------------------------------------------------------------------
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about EFF and
our work to encrypt the web, protect its users and defend digital rights.
-------------------------------------------------------------------------------
(Y)es/(N)o: Y
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for hlp-wiki-develop.agileventures.org
Using the webroot path /opt/bitnami/apps/mediawiki/htdocs for all unmatched domains.
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/hlp-wiki-develop.agileventures.org/fullchain.pem.
   Your cert will expire on 2017-08-22. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot-auto
   again. To non-interactively renew *all* of your certificates, run
   "certbot-auto renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le

```

which then needs to be installed in the Apache webserver according to the docs, but when I try the next step:

```sh
sudo ln -s /etc/letsencrypt/live/hlp-wiki-develop.agileventures.org/fullchain.pem /opt/bitnami/apache2/conf/server.crt
sudo ln -s /etc/letsencrypt/live/hlp-wiki-develop.agileventures.org/privkey.pem /opt/bitnami/apache2/conf/server.key
```

I'm told that the files already exist:

```
ln: failed to create symbolic link ‘/opt/bitnami/apache2/conf/server.key’: File exists
```

and so I start to wonder if I'm following different instructions from last time.  I restarted the server

```sh
sudo /opt/bitnami/ctlscript.sh restart apache
```

but the https was not working.  My history from doing this the last time around shows me making various modifications to the Apache config

```
  379  nano httpd.conf 
  380  ls
  381  ls bitnami/
  382  cd ..
  383  ls
  384  sudo mkdir sites-available
  385  ls sites-enabled/
  386  ls -la
  387  chown bitnami sites-available/
  388  sudo chown bitnami sites-available/
  389  sudo chown bitnami sites-enabled
  390  more conf/bitnami/bitnami.conf 
  391  cd conf/bitnami/
  392  pwd
  393  ls
  394  more httpd.conf 
  395  ls
  396  pwd
  397  more bitnami.conf 
  398  more bitnami-apps-prefix.conf
  399  more bitnami-apps-vhosts.conf
  400  ls
  401  more le-redirect-wiki.healthylondon.org.conf
  402  nano le-redirect-wiki.healthylondon.org.conf 
  403  sudo nano le-redirect-wiki.healthylondon.org.conf 
  404  history | grep restart
  405  history | grep apache
  406  sudo /opt/bitnami/ctlscript.sh restart apache
  407  sudo /opt/bitnami/ctlscript.sh restart
  408  sudo nano le-redirect-wiki.healthylondon.org.conf 
  409  sudo /opt/bitnami/ctlscript.sh restart
  410  sudo /opt/bitnami/ctlscript.sh restart apache
  411  sudo nano httpd.conf 
  412  sudo bitnami.conf
  413  sudo nano bitnami.conf 
  414  sudo /opt/bitnami/ctlscript.sh restart apache
```

but I can tell I'm not getting the full history as I don't see any reference to letsencrypt and I was definitely running the following at some point:

```sh
./letsencrypt-auto --apache --apache-server-root /opt/bitnami/apache2 --apache-challenge-location /opt/bitnami/apache2 --apache-handle-sites FALSE --apache-vhost-root /opt/bitnami/apache2/conf/bitnami -d <domain-name>
```

I had a poke around in the Apache config and saw differences that made me wonder if the new Bitnami stack had changed, or if I had just run a different certbot command this time around.  Https wasn't working, but I saw the cert was from example.com, so I backed up the existing cert and key files and got the link command to work:

```sh
$ sudo mv  /opt/bitnami/apache2/conf/server.crt /opt/bitnami/apache2/conf/server-original.crt
$ sudo mv  /opt/bitnami/apache2/conf/server.key /opt/bitnami/apache2/conf/server-original.key
$ sudo ln -s /etc/letsencrypt/live/hlp-wiki-develop.agileventures.org/fullchain.pem /opt/bitnami/apache2/conf/server.crt
$ sudo ln -s /etc/letsencrypt/live/hlp-wiki-develop.agileventures.org/privkey.pem /opt/bitnami/apache2/conf/server.key
$ sudo /opt/bitnami/ctlscript.sh restart apache
```

and then it worked. So either I made it harder than it actually was last time, or something got easier, or I just found a different command that was easier to work with out of the box.  However the trick is now enforcing https and then making sure the VisualEditor can still work over https, but I think that will be for tomorrow's blog i.e. [part 5!](http://nonprofits.agileventures.org/2017/05/25/azure-mediawiki-stack-part5/) ...