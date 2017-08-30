---
title: Tech Tests
date: 2017-02-10
tags: FutureLearn, GitHub, comments, tests, unit, acceptance, integration, RSpec, cucumber, codewars, kata
author: Sam Joseph
---

So I'm doing a tech test for FutureLearn and they've very kindly said that I can post my work publicly on GitHub, which I have promptly done:

[https://github.com/tansaku/tictactoe](https://github.com/tansaku/tictactoe)

I think I've spent about 3 hours on it so far, mostly in different leisure centers, churches and halls waiting for my children to finish sporting activities.   I get 45 minutes on Tuesdays, Wednesdays and Thursdays while I wait for them to finish WoodCraft, football and Karate respectively; during which I can focus on my laptop without too many interruptions.  In a bit of tech test flurry I created a calculator in Ruby for CodeMentor yesterday, which got approved this morning, and I was reminded of the period last year where I got pneumonia and spent three weeks in bed doing almost nothing but kata on [CodeWars](http://www.codewars.com/). Interestingly, I'd encountered variations on the CodeMentor tech test in CodeWars.  If you want to get good at TechTests you could do worse than spending a while hacking through the kata on CodeWars and other sites like HackerRank.

Other Makers folks were overtaking my CodeWars score recently, but I think CodeWars just adjusted their calculations and I seem to have jumped back to the top of the list:

![codewars ranking for my allies](https://www.dropbox.com/s/xzq8o3mdiyqawmh/Screenshot%202017-02-10%2009.49.57.png?dl=1)

which gives me ridiculous childish pleasure.  I'm so shallow ... (hangs head).  I'm still frustrated not to get to 1-kyu on CodeWars, even after I solved all the 1-kyu katas (1-kyus are the hardest).  Looks like there are now some new 1-kyu and 2-kyu katas that would allow me to get to 1-kyu without doing umpteen easier katas.  Maybe I'll get to that if I have another major illness :-)  Anyhow, tech tests are a strange beast - little coding puzzles that companies hand out to potential hires to test their coding skills.

I got into CodeWars because it was being recommended when I was a coach at MakersAcademy, and at the bootcamp we were focused on helping training folks up to be able to handle the tech tests that companies would give them after they graduated.  I previously did a worked tech test for the students there, the code for which you can see here:

[https://github.com/tansaku/vending-machine-kata](https://github.com/tansaku/vending-machine-kata)

I also recorded screencasts of myself doing the entire thing to try and reveal the thought processes I was having as I went through it.  I didn't have such fantastic feedback about it.  One junior staff member told me he thought the code made him "sad" and despite many other disucssions, I never got to discover what made him sad about it.  Another more senior staff member said that one student listening to the videos felt that it was too stop/start with me saying "umm" to much, which I can totally believe.  I think that lead to some scripted coding screencasts for the weekly challenges at Makers that ultimately were much more appreciated by the students.  

So it'd been fun to do this TicTacToe tech test for FutureLearn, and I took an Outside-In approach, driving the code with acceptance tests, dropping down to some integration and unit tests. I got something that works more or less, in a single monolithic file, just pushing Sandi Metz' 100 line limit.  There's something nerve-wracking for me, an academic by training, working with these industry tech tests.  I keep seeking that industry "special-sauce".  The thing that makes the great coders.  My best guess at the moment is that actually it's giving ground rather than fighting for a particular code style that is the key skill.

The thing that confuses me is that we have directions like "do the simplest thing possible", "always write your test first", "red-green-refactor", "you ain't gonna need it" and others, but when you take any of these to their logical extremes people seem to get uncomfortable.  This TicTacToe problem is clearly highly constrained, so an alternate approach to take would be to build a TicTacToe engine, driving the design with unit tests, and then wrapping acceptance tests around later (if at all).  I've never been a big fan of that sort of approach as I worry I'm going to burn too much time writing stuff I don't need, and they'd said only spend a couple of hours on this.

I chose to do the whole thing driven from RSpec to avoid burning time on Cucumber, and was interested to see if I could write complete acceptance tests for the input/output on the command line, so I drove in that way and got my single monolithic class.  At the moment I have a roughly working system, but only for a few hard-coded games.  The computer's current strategy is fixed and I don't handle collisions for markers, so the game is not fully generic.  The tech test is currently a reasonable demonstration of my ability to craft different kind of tests, get a roughly working system, but I'm suddenly paranoid about whether this code is going to make more people "sad" in some indeterminate way and I still won't be able to get an example of a version that would make people "happy".

I can imagine doing it again, driving from unit tests (with the benefit of the initial experience) to make a nicely factored engine.  I could also layer on some acceptance tests to make sure the other high level failures are addressed, e.g. collisions handled etc., or I could continue refactoring the code with the existing tests.  I've used the existing test suite to eliminate some unneeded code and make the method names more self-explanatory.  But how will people feel about the lack of comments?  I like to focus on making the method and variable names self-documenting.  Having got this far I feel like I won't be happy until it plays a fully generic game.  I know I'll be spending more time on it today before I send FutureLearn the link :-)

###Related Videos

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=eUhIV1c9CpU)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=yFIOYN8SisA)
