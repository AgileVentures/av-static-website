---
title: Eliminate, Automate, Delegate
date: 2016-09-06
tags: eliminate automate delegate ruby kindle publishing textbook process 
author: Sam Joseph
---

[Ruby Rogues](https://devchat.tv/ruby-rogues) is a stream of interesting ideas.  One I heard recently was a mention of the importance of "Eliminate, Automate, Delegate".  Everything you're doing, check does it need to be done? Could it be done automatically, or could you get someone else to do it?  The idea is to not waste time on things that could be done more efficiently, and spend as much time on those things that only you can do, which presumably is the stuff that you're really good at, is enjoyable, delivers value etc.  I'm not sure who first came up with this idea, but there are a few blogs with variations on the theme:

* [Rory Vaden on EAD](http://roryvaden.com/blog/eliminate-automate-and-delegate/)
* [Marc Winn on ESAD](http://theviewinside.me/eliminate-simplify-automate-delegate-the-four-steps-to-freedom/)

And a quick google generates a set of related posts on how to interpret or get the best out of this kind of approach.  To add my own spin on this, the dangers of automating are that it can sometimes take longer to automate a task that it takes to get it done, and if that tasks ends up being a one off, or infrequently repeated, then the value of automation drops.  Similarly with delegating, sometimes the process of getting another person, or people, to understand the task that you want done takes longer than doing it yourself, and again if it's a one off that communication overhead can leech the value out of the delegation.

It thus makes eminent sense to eliminate, or indeed simplify, tasks before trying to go the route of automation or delegation.  Then there's the question of which to do first.  I think that partly depends on whether you are a people person or shall we say a 'computer' person :-)  Of course all this presumes that we've bought into this whole narrative of achieving goals efficiently.  The process of attempting to automate or delegate something that doesn't reap dividends is potentially a great learning experience, and isn't the pinnacle of mindfulness to just appreciate all these experiences for the process they are, rather than worrying about the end result?

Since we're unlikely to resolve that question convincingly in this blog, let me tell you about an automation process I went through yesterday that I feel quite pleased with.  One of my tasks as editor of the ["Engineering Software as a Service" textbook](http://www.saasbook.info) is to manage the incoming requests from instructors around the world for evaluation copies of the textbook.  This is a fairly normal part of the academic-publisher relationship.  Publishing houses generally make free copies of their textbooks available to instructors at established educational institutions as a "loss-leader".  The hope of the publishing companies is that instructors will adopt their textbooks on the basis of the evaluation copy, and then students will purchase the textbook.

Armando Fox and Dave Patterson decided to cut out the middleman and publish their "Engineering Software as a Service" textbook by self-publishing it through StrawberryCanyon, the enterprise they set up to deliver the textbook far more cheaply and efficiently than through a traditional publishing company.  Standard software engineering textbooks often run to a couple of hundred dollars, while the kindle version of ESaaS is just $10 (and comes with $10 of AWS credit to boot).  Long story short, there is no big publishing house infrastructure behind the ESaaS textbook.  Armando used to do the evaluation copy distribution and delegated that to me as part of my role of editor and community manager.

So currently we have requests for evaluation copies come in through a google form, which puts requests in a google spreadsheet that I have access to.  The process of distributing an evaluation copy involves the following steps for me:

1) check that the evaluation request is from a legitimate educational institution
2) generate a watermarked mobi format version of the textbook for that instructor
3) send the appropriate instructor an email with a link to where they can download their copy of the textbook
4) add the instructor to the textbook mailing list

Now of course I'd love to delegate this, but I don't think there is anyone else particularly incentivised to take on the task.  Armando incentivised me with a small percentage of the textbook profits.  It's not much, since as I mentioned above the textbook is designed to be affordable, but every little helps.  As Armando has pointed out repeatedly he's not in this for the money, but for trying to revolutionise the education of software engineering, something which also inspires me.  Eliminate the task? well not without giving up on that goal :-) Simplify the task?  Well we could just make the textbook available for free download, but then there'd not be any incentive for people to pay the $10 a copy that provides the funds to pay for professional indexing of the textbook, translation into other languages etc.  The sales of the textbook generate very small amounts of profits after Amazon's cut and that's all re-invested in trying to improve the offering.

Some books seems to operate a free HTML, pay for PDF model that perhaps we should consider, but there's also an aspect of fitting into the existing instructional model.  Instructors expect to get a free textbook that their students then pay for ... although that sounds less convincing as I type it out.  However, part of what instructors are "buying" into when they select a textbook is that their students purchasing power will be directed back into the improvement of the textbook and so on.

So, automation.  Steps 2, 3 and 4 seem promising in terms of automation.  I had automated steps 2 and 3 in the past when we used to distribute gift copies via Amazon.  I used RSpec/Capybara/Selenium to automate the process of stepping through the Amazon interface to send out a gift copy of the textbook.  That [code](https://github.com/saasbook/SPOC/blob/master/spec/send_textbook_spec.rb), was difficult to maintain as the Amazon web interface shifted, and ultimately had to be discarded when we decided to drop distribution of sample copies through Amazon (since it cost us $3 each time), and switch to generating watermarked copies from the command line.  Getting that to work on my system reliably was quite a struggle in itself.  The mobi generation process is run by Latex, takes a while, and for quite some time seemed to require 2 or 3 runs with me needing to hit return several times.

More recently the mobi generation process stabilised and I had almost sort of automated the process of constructing the command to generate the mobi and move the file to dropbox, using formulas in google spreadsheets.  You could ask, why didn't you just create a script straight off the bat?  Lack of emotional energy?  The fact that the data was already in google spreadsheets?  That the process itself was unstable and prone to change?  However yesterday I got to that threshold of thinking the process had stabilised.  I'd been using the spreadsheet formulas fairly regularly to set up the necessary commands to generate the textbooks.  I'd been using templates in the Thunderbird mail client to make the process of sending out emails easier, but doing it was still error-prone and time consuming.  Now I found I could generate a compose window in Thunderbird from [command line options](http://kb.mozillazine.org/Command_line_arguments_\(Thunderbird\)), so rather than going through the manual process again I wrote the following script in Ruby:

```rb
MAKE_MOBI_PREFIX = 'make mobi WATERMARK="'
MOBI_ESCAPE_CHARS = '\\\\\\\\\\\\\\\\'
MAKE_MOBI_SUFFIX = '" ; mv saasbook.mobi ~/Dropbox/Public/saasbook_prof_'
MOBI_FILE_EXTENSION = '.mobi'

LASTNAME_INDEX = 0
FIRSTNAME_INDEX = 1
EMAIL_INDEX = 2
LANGUAGE_INDEX = 3

def generate_mobis
  requestors = CSV.read("requests.ssv", { col_sep: "\s", skip_lines: ';' })
  Dir.chdir('/Users/tansaku/Documents/GitHub/armandofox/saasbook') do
    requestors.each do |requestor|
      lastname = requestor[LASTNAME_INDEX]
      firstname = requestor[FIRSTNAME_INDEX]
      email = requestor[EMAIL_INDEX]
      language = requestor[LANGUAGE_INDEX]
      `#{MAKE_MOBI_PREFIX}#{firstname} #{lastname}#{MOBI_ESCAPE_CHARS}#{email}#{MAKE_MOBI_SUFFIX}#{lastname.downcase}#{MOBI_FILE_EXTENSION}`
    end
  end 
end
```

This is ugly smelly code, and you should have seen it before I cleaned it up a bit :-)  I even started writing bits of it in the RSpec files I was using to kick it off before migrating it out into a Ruby file.  Bad practice?  Perhaps, but I wanted to quickly determine if I could get things I wanted working, and I know that checking that the mobi file is generated with the correct watermark is something I'm unlikely to be able to automate, since it requires a visual check.  The bottom line is that I need to be able to open that file in Kindle software and see the right things.  Automating that would burn way too much time, for something that I can check manually. I won't look every time perhaps but frequently enough to ensure things are working.

I can just copy and paste the necessary data from the google spreadsheet into a local file ("requests.ssv") and pasting from Google into my sublime editor generates space separated values, so I used an ssv file abbreviation.  So that allowed me to generate a load of personalised evaluation copies of the the textbook in batch; already quite a big saving. There's so much room for refactoring - I could make classes etc., but I didn't want to dive into that, I wanted to see if I could semi-automate the emailing process:

```rb
THUNDERBIRD = '/Applications/Thunderbird.app/Contents/MacOS/thunderbird'

def send_textbook_email
  preselectid = 'id2'
  subject = 'Engineering Software as a Service Community Welcome!'
  attachment = '/Users/tansaku/Documents/Documents/AgileVentures/LocalSupport/WelcomeLetter.docx.pdf'

  requestors = CSV.read("requests.ssv", { col_sep: "\s", skip_lines: ';' })
  requestors.each do |requestor|
    
    lastname = requestor[LASTNAME_INDEX]
    language = requestor[LANGUAGE_INDEX]
    email = requestor[EMAIL_INDEX]
    to = email
    template = language == 'Español' ? 'email_spanish.erb' : 'email.erb'
    file_path = File.join(File.dirname(__FILE__),template)
    link = "#{DROPBOX_LINK}/saasbook_prof_#{lastname.downcase}.mobi"
    namespace = OpenStruct.new(lastname: lastname, link: link)
    body = ERB.new(File.read(file_path)).result(namespace.instance_eval { binding })
    options = %Q{"to='#{to}',preselectid='#{preselectid}',bcc='#{bcc}',subject='#{subject}',body='#{body}',attachment='#{attachment}'"}
    command = "#{THUNDERBIRD} -compose #{options}"
    puts command
    `#{command}`
  end
end
```

I was pretty pleased with the above code, not in stylistic terms, but as a spike, a proof of concept that I could bring up a Thunderbird compose window with the correct text, to the correct person, and from the correct email account, and with an attachment.  Ultimately it all worked.  The process is not quite perfect as I [posted to mozillazine](http://forums.mozillazine.org/viewtopic.php?f=39&t=3022463&e=0), since there is an extra newline at the start of the email, and running in batch I have to click 'send' on the compose window and then ⌘Q to quit the Thunderbird instance, before the next compose window will come up.  This is because there can only be one running Thunderbird instance at a time, so I also have to quit Thunderbird before I run the batch script.

Still, I can live with those issues - a visual check of the email in the compose window before sending it is actually quite good for quality control.  I can now take a set of requests for evaluation copies of the textbook, run one command to generate the watermarked copies - grab a cuppa (UK slang "cup of tea") while that runs, and when it finishes I can run another command that will pop up the emails I need to send and they will have the correct outgoing email address and link for downloading (as well as a couple of other minor variations based on the original request).  I can now check each and do a little dance of:

a) check email
b) hit ⌘Enter to send
c) hit ⌘Q once the send has completed to exit Thunderbird
d) repeat

Whereas it might have previously taken me 10 minutes to get through pasting together the necessary emails for sending out 10 emails, I can now do that in about a minute, with an increase in reliability.  Writing the code took about two hours in total with me testing each of the processes manually, and I could have got yesterdays evaluation copies out in say 20 minutes if I hadn't bothered with the automation.

Over the course of a few months I should get that time back (assuming we don't massively change the process again), and I had a lot more fun coding than I would have done operating things manually.  I also have some code to share, which can be progressively cleaned up and maybe support other things like getting the instructors on the google mailing list - hmmm captchas ... Anyway, I think the thing to ask yourself is, do you want to be an automaton?  A people person?  A developer?  I'm still working on the necessary people skills to delegate and more importantly inspire.  In the meantime I'll choose being a developer over an automaton any day. I enjoy that process much more :-)
