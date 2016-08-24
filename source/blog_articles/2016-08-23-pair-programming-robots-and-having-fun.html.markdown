---
title: Pair Programming Robots and having Fun
date: 2016-08-23
tags: pair programming robots games letterpress
author: Sam Joseph
---


I've had this idea for a lightweight pair programming app for a while now.  One of the main inspirations is the [iOS letterpress app](https://itunes.apple.com/gb/app/letterpress-word-game/id526619424?mt=8).  If you don't know the game it's a letter square, and you try to make the longest word you can from the letter square.  Pretty straightforward stuff, except that you can play and challenge people all round the world.  You take turns to make the longest word you can. What's really cool about the app is that you can have a little game with a total stranger.  At least I assume you are actually playing with someone rather than against a robot.  I'm not sure how drop outs are managed.  When I think about it, playing against robots would be a more reliable experience, but I don't know the stats for drop outs.  I've certainly dropped out of games, but perhaps I'm a sociopath and 99% of people carry on playing with each other till the end?

Anyway, these days there are lots of games where you compete and even team up with strangers, e.g. Splatoon, League of Legends (LoL) and so on.  I'd love to learn more about how these games match people up to try and maximise the user experience as I think we have a related problem with matching people up for pairing sessions in our "Agile Development using Ruby on Rails" MOOC.  If Splatoon/LoL are the gaming equivalent of full screenshare pairing, then simpler games like letterpress would correspond to a sort of micro-pairing experience on a small toy problem.

Ever since I've looked into the different styles of [ping-pong pairing](http://www.agileventures.org/remote-pair-programming/pair-programming-protocols) I've been fascinated how protocols like "one undermanship" have a game like feel.  They remind me somehow of turn based games like chess.  So I keep thinking of a letterpress style micro-pair programming game where you are involved in light-weight ping-pong pairing sessions with people all round the world.

Maybe there's nowhere near the number of people interested in pair programming as there are in playing word games, so maybe there would never be the critical mass to make it fun, ... unless, robots?  I finally spent some time on a micro-pairing robot on a plane recently.  There are a couple of challenges; one is working out the rules of this ping-pong pairing game I'm imagining, and another is getting a robot to pair program sensibly with you.   

An instance of a pairing game might run like this:

Spec involves input/output, e.g. 2 => 4, 3 => 6, 4 => 8  (in this case a numeric doubler).  The "one undermanship" protocol involves writing the absolute minimum amount of code (which we could perhaps measure in terms of characters? code-complexity?)


Pair A writes:

```rb
describe 'doubler' do

  it 'doubles a number' do
    expect(double(2)).to eq 4
  end

end
```

Initial failing test.  So Pair B works to write the absolute minimum code to make this test pass, e.g. 

```rb
def double(number)
  4
end
```

and then writes a new test that will fail

```rb
describe 'doubler' do

  it 'doubles a number' do
    expect(double(2)).to eq 4
  end

  it 'doubles a number' do
    expect(double(3)).to eq 6
  end

end
```

So pair A writes the minimum to make this pass, in this case being intentionally obtuse (they could write `number*2` which would be fewer characters, but perhaps we can give them points for passing only the existing tests and not others?):


```rb
def double(number)
  4 if number == 2
  6
end
```

then pair A adds another failing test:

```rb
describe 'doubler' do

  it 'doubles a number' do
    expect(double(2)).to eq 4
  end

  it 'doubles a number' do
    expect(double(3)).to eq 6
  end

  it 'doubles a number' do
    expect(double(4)).to eq 8
  end

end
```

And finally pair B writes the general solution:

```rb
def double(number)
  number * 2
end
```

Of course pair B could write:

```rb
def double(number)
  4 if number == 2
  6 if number == 3
  8
end
```

But it would be nice if we could somehow end on the more general case, with a stronger set of tests (for edge cases etc.? could build those into the initial input/outputs).  The thing about making this an enjoyable game might be a scoring system? So that you get points for one-undermanship obtuseness to an extent, but that past a certain point there's a refactoring bonus.  Maybe the sensible approach is to only score a single round of hard coding where the complexity is actually less than the general solution?

So there's also the issue of coming up with a range of simple coding problems that make this more interesting than the most trivial cases - I guess there's enough complexity in a few basic arithmetic problems, and we can collect more over time - there are great repositories like code wars.  Anyway, with any multi-player game we have the classic bootstrap problem that if we had a great game that lots of people were playing, then there would be lots of people to play with and it would be great; but initially there are no people playing it.  So in the meantime can we scaffold the gameplay with pretend people?  Can we write a robot pairer than can make a test pass, and generate a new chunk of code to move the ping pong on?

For a restricted set of cases I think the answer is yes.  At least what I started on the plane was a chunk of code that would take and analyse a ruby exception and write the necessary code to make it pass.  It's not very complex at the moment, it's basically this:

```rb
def fix_code(e)
  if e.class == NoMethodError
    /undefined method \`(.*)\' for main\:Object/ =~ e.message
    eval "def #{$1}; 'robot method' ; end "
  elsif e.class == ArgumentError
    /wrong number of arguments \(given (.*), expected \d\)/ =~ e.message
    num_args = $1.to_i # use class or arg to auto-infer an approprate name?
    arg_string = (0..num_args-1).map {|i| "arg#{i}"}.join(',') 
    /\(eval\)\:1\:in \`(.*)\'/ =~ e.backtrace.first
    method_name = $1
    eval "def #{method_name}(#{arg_string}); 'robot method' ; end"  
  else
    puts "cannot handle error class #{e.class}; #{e.message}"
  end

end
```

What it does at the moment is take NoMethodErrors and ArgumentErrors and fix things up so the specified method is created with the correct number of arguments.  Assuming that the game involves working through a set of input/output values on a range of basic arithmetic problems I can imagine it being fairly easy to extend to make the majority of failing tests pass.  Given an input/output pair, generating an RSpec test is pretty trivial.  So a little more work here and one could have a basic ping pong pairing partner.  I don't fool myself that it wouldn't break fairly quickly, but I think rounds of polishing could make it work reasonable well for a range of introductory problems.  Would it create a fun game that many people would want to play?  Probably not ... Might it be a good learning experience for some people? ... maybe?  I think the process of stack-trace/error analysis is quite interesting and a nice feature would be to have the robot be able to explain why it does what it does - they would be canned explanations, but they could highlight how the stacktrace/error has been analysed in order to work out what to do next.

I guess the best initial interface would be to make it a command line game that you could play and the robot would edit the file that you are both working on perhaps?   Having started it I'm kind of interested in extending it; we'll see if anyone else thinks this is anything other than mindless naval gazing :-)




