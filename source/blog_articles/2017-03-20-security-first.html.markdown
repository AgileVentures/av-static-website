We're in the process of rolling out "https" support on the main AgileVentures site.  We've had "https" or "ssl" encoding set up on the LocalSupport project for a couple of years.  LocalSupport users are not a technical community so we don't have facebook, twitter or GitHub login.  We're storing a lot of personal address and phone number details there, and it was clear that without the right security we'd be sending user passwords and data back and forth in the clear.  During the initial launch of LocalSupport we switched from where we had been developing (Heroku) to another service provider (NineFold) who offered us SSL free.  So the initial LocalSupport launch was fully secure.

NineFold eventually folded and we moved back to Heroku where we now pay for the expedited SSL package for LocalSupport.  The AgileVentures site (WebSiteOne) was, and is, targeted at a more technical crowd.  We've had GitHub and Google login via OAuth since close to the start.  By their nature these are secure.  We used to offer email signup, but shut that down a couple of years back, due to a proliferation of spam accounts, and haven't re-opened it.  In principle, users can still log in insecurely via emails they signed up with at the start, but I'm not sure if anyone still does this, and new users all register via OAuth.  AgileVentures, the site, maintains all its data open for sharing, so security has seemed less of an issue, and the cost of setting up the SSL security always seemed to push that down the to-do list.

In the last year we added payment support for Premium plans, first via Stripe and then via PayPal.  These are both secure in that user credit card details never touch our server - going directly to secure Stripe and PayPal servers.  So while in some ways that is sufficient security, Federico pointed out recently that the perception of a lack of security could be damaging.  We discussed in a WSO kickoff and got an [issue ticket](https://github.com/AgileVentures/WebsiteOne/issues/1584) and I started working on it.  Federico provided a great screen shot of what a user might see while making a Stripe payment:

![insecure http in backdrop of stripe payment](https://camo.githubusercontent.com/8a1104bf54461a478267e35477ec4cd24e8af3b6/68747470733a2f2f7777772e64726f70626f782e636f6d2f732f7034363764347773686833616276322f696e7365637572652d41562d7072656d69756d2d7369676e75702d66697265666f782e706e673f646c3d31)

Now no potential Premium member has complained about this, but then again you wouldn't expect that kind of feedback.  Will fixing this unleash a wave of subscriptions? Probably not :-)  But it should get sorted, and while our whole community is based on openness, there are users who choose to de-activiate their accounts, and we need to respect their wishes, ensuring their data is secure when they want it to be hidden.

Also, there's now LetsEcrypt, which promises us the ability to generate SSL certs that can be used in tandem with the SSL endpoints that are now bundled with Heroku's paid plans.  We have to have the AV site servers on Heroku paid plans for resources and also to have them part of Heroku's group plan.  Maybe one day we can migrate to Dokku on Azure as part of a cost saving exercise ... we'll see how that goes with the AsynVoter project first.  In the meantime it seems sensible to get SSL all set up, and last week I managed to get it working on both the develop and staging servers.  I was initially distracted by the [letsencrypt-heroku](https://github.com/substrakt/letsencrypt-heroku) project, but that only works for certain domain hosting solutions.  However a couple of blog posts set me straight:

* https://collectiveidea.com/blog/archives/2016/01/12/lets-encrypt-with-a-rails-app-on-heroku
* https://medium.com/@franxyzxyz/setting-up-free-https-with-heroku-ssl-and-lets-encrypt-80cf6eac108e#.gx52z5rvt

Even if I had to combine elements of both to get us sorted.  The process was to install a program called `certbot`:

```
$ brew install certbot
```

Then I needed to generate a certificate manually:

```
$ sudo certbot certonly --manual
```

agree to have my IP logged and then adjust WSO so that it could respond to a security challenge.  I had to leave the certbot operation hanging while I deployed the following code:

```rb
class StaticPagesController < ApplicationController
  before_filter :authenticate_user!, except: [:show, :letsencrypt]

  def letsencrypt
    render text: "#{params[:id]}.ENV['CERTBOT_SSL_CHALLENGE']", :layout => false
  end
``` 

which then allowed the certbot challenge to be answered and various keys to be generated locally on my computer.  In the background I had also set the Rails config to force SSL:

```rb
config.force_ssl = true
```

and now I could deploy the locally generated keys via the Heroku CLI:

```
$ sudo heroku certs:add /etc/letsencrypt/live/develop.agileventures.org/fullchain.pem /etc/letsencrypt/live/develop.agileventures.org/privkey.pem -r develop
```

and we had SSL working!  There was more work though, as our Google and GitHub OAuth redirect URLs had to be updated to now refer to the HTTPS versions.  Once that was done, login and signup were working again, although I am slighltly suspicious that we may still encounter some problems.  Michael and I both experienced some issues on staging trying to log in, to get redirected to a sign up page indicating that our email already existed.  I have a intuition that previously generated OAuths may get invalidated and there may be something else we need to do.  I'm not clear how seriously this will affect production ... I can imagine that existing users may require resets.  I think the error message is a Devise one:

![](https://www.dropbox.com/s/eb2b4453pjo8mpc/Screenshot%202017-03-20%2010.23.52.png?dl=1)

Looking at the code, it's not clear what set of circumstances causes that.  Just reviewing on staging, I see I can log in via GitHub, but if I try to log in using Google with my tansaku email then I get email already taken (redirected to sign up page) and if I try with my NeuroGrid email I get "csrf detected" first time around (nothing in the JS console), but then second time it works.  I guess I could be trying to wrap tests around this, but I doubt my ability to fully replicate the actual production instance and I'm just not sure how many others will experience this.  I held off deploying over the weekend - I guess I should ask more folks to try it out on staging ...

Either way I didn't actually manage to complete any of the WSO tickets I committed to in the last sprint, partly because of the SSL but also because I think I overcommitted.  I've not taken on any new ones this week and am committing to trying to finish the ones I've already started; even though there are loads of new exciting ones we have voted on.  Let's see how this week goes.  I really hope I can get the new security setup on production.

### Related Videos:

* ["Martin Fowler" scrum](https://www.youtube.com/edit?o=U&video_id=bjbdQ9L-KHw)
* ["Kent Beck" scrum and WSO kickoff](https://www.youtube.com/edit?o=U&video_id=bi85l56Tcqg)
* ["Bob Martin" scrum](https://www.youtube.com/edit?o=U&video_id=Egf8LH_pRK0)



