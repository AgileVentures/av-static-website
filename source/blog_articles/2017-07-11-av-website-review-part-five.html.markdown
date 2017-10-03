---
title: AV Website Review Part 5
date: 2017-07-11
tags: 
author: Sam Joseph
---


So what I really meant to do yesterday was to look at the flows going through various pages in the site via Google Analytics.  Here's a view that compares June with May this year:

![behaviour flow around sign up page, may vs jun](https://dl.dropbox.com/s/hf47so53v8lnhyw/Screenshot%202017-07-11%2009.23.15.png?dl=1)

It's perhaps even more revealing to compare June this year with June last year:

![behaviour flow around sign up page, jun 16 vs jun 17](https://dl.dropbox.com/s/j1yzvlujhm0qttm/Screenshot%202017-07-11%2009.27.31.png?dl=1)

What we can see clearly here is the big increase from those viewing '/code' and '/learn' (I shortened those and added sign up call to actions at the end of them recently) and the massize increase in those going to '/getting-started' (a few months back we adjusted it so that you go to "getting started" after signing in).

Of the 1200 people landing on the sign_up page in June, we had 380 just stop there and go no further.  I'm wondering if that figure could be improved by the deployment of a privacy policy and re-enabling email signups?  

What's perhaps more concerning is the 299 drop offs out of the 793 sessions on the 'getting started' page.  Of course maybe the reality is that we can't change either of these drop off rates significantly.   I've just experiemented with changes to the 'getting started' page and so it seems sensible to leave that for a while.  Although maybe we should be A/B testing rather than comparing longitudinally?  Anyhow, I've got the privacy statement all linked up into the sign up page:

![sign up page with privacy link](https://dl.dropbox.com/s/ggj4vgtqytlm5jp/Screenshot%202017-07-11%2009.34.22.png?dl=1)

Overall I think the page looks pretty good.  We previously fixed up the icons and made both signup methods work, so the only garish thing remaining is this email signup disabled.  I found there's a [plugin for devise to add Google's re-captcha](https://github.com/plataformatec/devise/wiki/How-To:-Use-Recaptcha-with-Devise).  Working through it I got stuck with the configuration, but fixed it and put in a PR to the repo with the fix:

https://github.com/ambethia/recaptcha/pull/231

and so was rewarded with seeing the following in my local spike:

![first pass at adding recaptcha](https://dl.dropbox.com/s/fxj5vfx9wzj5sb3/Screenshot%202017-07-11%2009.54.23.png?dl=1)

but while I could click on the I'm not a robot, I got "verification failed" after signup:

![verification failed](https://dl.dropbox.com/s/bieuh1rayk3l9i7/Screenshot%202017-07-11%2009.55.37.png?dl=1)

All the log was telling me was: 

```
Filter chain halted as :check_captcha rendered or redirected
```

I added byebug to the check_captcha method I'd added to the registrations controller:

```rb
  prepend_before_action :check_captcha, only: [:create] 

  def create
    super
    unless @user.new_record?
      session[:omniauth] = nil
      flash[:user_signup] = 'Signed up successfully.'
      Mailer.send_welcome_message(@user).deliver_now if Features.enabled?(:welcome_email)
    end
  end


  private

  def check_captcha
    byebug
    unless verify_recaptcha
      self.resource = resource_class.new sign_up_params
      resource.validate # Look for any other validation errors besides Recaptcha
      respond_with_navigational(resource) { render :new }
    end
  end
```

Stepping through the code there made it look like the verification was failing - nothing else wrong.  The `verify_recaptcha` method was returning false ... Not sure what's going wrong there and I have the sinking feeling that adding the captcha as I have may screw up the other signup methods.  We'll see.  First I just need proof positive that we can get it work at all ... It may be that I just can't run it locally like this - I added the `localhost` domain to the domains associated with keys to run locally, but maybe there's more needed to run locally.  I can see that there's a domain name verification setting in the reCaptcha settings:

![recaptcha settings](https://www.dropbox.com/s/53j9mi92omzrls9/Screenshot%202017-07-11%2010.04.42.png?dl=1)

I'm just trying a re-start and another step through ... which seemed to work without any changes.  Hmm, maybe the restart did it ... I also played with the layout to make it all look a little better:

![new layout](https://www.dropbox.com/s/la5kden3qy8zsb1/Screenshot%202017-07-11%2010.12.41.png?dl=1)

which I assume didn't actually change things.  Test again without the byebug ... and that works although I note I get two "signed up successfully messages" - tried to screen shot, but they fade out a little fast, would like to fix that.  Also I get nicely redirected to the "Getting Started" page.  Okay, well that's the spike - sure it breaks lots of things, but might as well push that up as a spike PR so see how it all runs on CI.  Guess we'll need sandboxing ...?  Look at that tomorrow.
