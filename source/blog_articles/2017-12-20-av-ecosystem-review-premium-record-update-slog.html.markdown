---
title: AV EcoSystem Review Premium Record Update Slog
author: Sam Joseph
---

![slog](../images/slog.bmp)

Another day, another five or so Premium records to review - let's get started.  I update the starting date of another early Premium sign up and email them to see how they are doing:

```
irb(main):001:0> u = User.find_by email: '...'
=> #<User ...>
irb(main):002:0> u.subscription
=> #<Premium id: .., type: "Premium", started_at: "2016-10-25 16:46:56", ended_at: nil, user_id: ..., plan_id: 1, sponsor_id: nil>
irb(main):003:0> u.subscription.started_at = DateTime.parse("2016-10-15 11:37:08")
=> Sat, 15 Oct 2016 11:37:08 +0000
irb(main):004:0> u.save
=> true
```

I do pretty much the same thing for three more members, and reach out to each of them as well.  One of them is a member who pays their own way for Premium and is sponsored for an upgrade to Mob and to F2F - not sure how we represent that, but the first subscription I update is just their Premium account, so leave that as is I guess.

Blurgh, I am not getting very far each morning - distracted by Slack/Email, early Premium F2F trial sessions eating into my mornings ...
