---
title: Show me the Code
date: 2017-03-15
tags: elixir, udemy, ruby, Stephen Grider, Avdi Grimm, lambdas, instance variables, state manipulation, object oriented, functional
author: Sam Joseph
---

![code](/images/code.jpg)


Federico made a comment on one of my recent blogs:

> code_in_blog == good

I wish I was coding more.  I am getting to do some code in the Premium mobbing sessions.  My productive coding efforts are snatched between meetings with me throwing up spikes to give myself bugs (in my feature branches) to fix and drive myself forward.  I have got quite excited about starting to code in Elixir.  Stephen Grider very kindly made his Udemy course available to Premium Mob members, and it's super high quality.   It's what I would love to see more MOOCs evolving towards.  Stephen has all the code in his [repo on GitHub](https://github.com/StephenGrider/ElixirCode), where we can see code like this from his initial example Elixir app for playing Cards:

```ex
  def load(filename) do
    case File.read(filename) do
      {:ok, binary} -> :erlang.binary_to_term binary
      {:error, _reason} -> "That file does not exist"
    end
  end
```

The snippet is a little chunk that will load a deck of cards from a file.  I was struck by some parallels with a pattern in Avdi Grimm's "Confident Ruby" that we are covering in our Ruby Mob:

```rb
def delete_files(files, options={})
  error_policy = options.fetch(:on_error) { ->(file, error) { raise error } }
  symlink_policy = options.fetch(:on_symlink) { ->(file) { File.delete(file) } }
  
  files.each do |file|
    begin
      if File.symlink?(file)
        symlink_policy.call(file)
      else
        File.delete(file)
      end
    rescue => error
      error_policy.call(file, error)
    end
  end
end
```

The `delete_files` method about is doing something more complex than the Elixir snippet.  Here we're seeing a method that has some default "policies", that can be called in such a way that new policies are passed in on the fly:

```rb
delete_files(files, 
             on_error: ->(file, error) { }, 
             on_symlink: ->(file) { File.delete(File.realpath(file)) })
```

The interesting thing was that having refactored this far in his "receive policies instread of data" pattern Avdi said:

> At this point this example is clearly getting a little bit strained. And in general, code like this is probably a smell. There are some programming languages in which it is perfectly normal to pass lots of lambdas into methods, but in Ruby we typically try to Wnd more object-oriented approaches to composing behavior.

Now admittedly the connection I'm making between the Elixir and Ruby code is strained.  In some ways I was just picking up on what looks like a similarity in some of the symbols in the two languages.  Actually here what might seem like a similarity is in fact two different things.  Elixir's `case` statement is using `->` to indicate the cases, whereas Ruby is using it to specify anonymous lambdas.  That said, elements of case statements are perhaps best described as anonymous lambdas?  Confused, well, let me transcribe the Elixir code into Ruby:


```rb
  def load(filename) 
    binary = File.read(filename)
    binary.unpack
  rescue
    "That file does not exist"
  end
```
 
Let's look at that side by side with the Elixir snippet again:

```ex
  def load(filename) do
    case File.read(filename) do
      {:ok, binary} -> :erlang.binary_to_term binary
      {:error, _reason} -> "That file does not exist"
    end
  end
```

Elixir is using some interesting techniques like "pattern matching" here.  There's a [great blog on getting pattern matching in Ruby](http://katafrakt.me/2016/02/13/quest-for-pattern-matching-in-ruby/) with [noaidi](https://github.com/katafrakt/noaidi).  The pattern matching in use here is that the case statement is checking for matches returned from `File.read(filename)` to see if they are either in the form `{:ok, binary}` or `{:error, _reason}` with the bonus that if part of the pattern is an undeclared variable, then it gets assigned so that we can then refer to that variable in the body of the individual case condition, e.g. `:erlang.binary_to_term binary`.

Part of what got me connecting all this up was that Ruby was throwing some of the same error types that Elixir returns, e.g.

```
2.3.1 :004 > f = File.read('.gitignoreasda')
Errno::ENOENT: No such file or directory @ rb_sysopen - .gitignoreasda
```

When we were playing with the Elixir code in the mob, I was seeing that the unused `_reason` variable was being set to :enoent when the file didn't exist, which gave me an idea for refactoring this snippet of Elixir code like so:

```ex
  def load(filename) do
    case File.read(filename) do
      {:ok, binary} -> :erlang.binary_to_term binary
      {:error, :enonet} -> "The file named '#{filename}' does not exist"
      {:error, reason} -> "Trying to load the file we got stuck with: #{reason}"
    end
  end
```

which in Ruby would be:

```rb
  def load(filename) 
    binary = File.read(filename)
    binary.unpack
  rescue Errno::ENOENT
    "The file named '#{filename}' does not exist"
  rescue
    "Trying to load the file we got stuck with: #{$!}"
  end
```

Anyway, all good fun, but the thing that my brain is really grooving to is Avdi's comment about Ruby having better OO ways to manage anonymous lambdas and the thought that Elixir has eschewed the setup of having objects associating state with methods/functions, so that what might be a code smell in Ruby, might actually be the way to go in Elixir.  The really strange mental nexus for me is the next example from Stephen's course where he introduces Elixir structs, which are data structures of the type I am familiar from my C days - just structures of data, not associated with any methods/functions, e.g.

```ex
defmodule Identicon.Image do
  defstruct hex: nil, color: nil, grid: nil, pixel_map: nil
end
```

in almost the inverse fashion that Ruby modules are collections of methods divorced from any object state; although they can manipulate state when they get mixed in, but still ... anyhow, we get Elixir methods in a different module like so:

```ex
  def build_grid(%Identicon.Image{hex: hex} = image) do
    grid =
      hex
      |> Enum.chunk(3)
      |> Enum.map(&mirror_row/1)
      |> List.flatten
      |> Enum.with_index

    %Identicon.Image{image | grid: grid}
  end
```

This Elixir method is designed to work with a particular Elixir struct; specifically the Identicon.Image defined above, but there are no instance variables in Elixir so all data comes in from the method arguments and then gets passed out the return statement.  I know there's lots of strange new syntax here if you're not familiar with Elixir, in which case I strongly recommend Stephen's course, but the thing I want to focus on here is the the process of assignment from the argument struct to a local method variable:

```ex
%Identicon.Image{hex: hex} = image
```

where we're using pattern matching to extract the hex part of the struct (the `image` argument) and assign it to the `hex` variable.  After processing and generating the `grid` variable in the body of the method, we then combine that with a copy of the incoming image argument to return a new struct that is the incoming struct plus some additional data:

```ex
%Identicon.Image{image | grid: grid}
```

What really strikes me here (apart from a possible refactoring) that I'll go into in another blog, is that instance variables just seem completely unecessary.  I've known for a while that the functional programmers eschew them, and we have heuristics in OO languages to avoid allowing unecessary state manipulation (e.g. preferring instance variables set up in an initializer rather than via accessors), but the breakthrough for me here is to see that we can still do all the OO domain modelling with structs.  We can even create a set of methods and procedures that are tied to working with those structs, but we don't seem to lose anything we really need as a result of losing instance variables.  This does seem extraordinarily powerful.  Maybe I'm drinking the Kool-Aid, but I can't now think of a circumstance where we have to use an instance variable.

Also, the fact that in Rails, active record models have this complexity, where they can get out of sync with the database due to their instance state, is tricky and hugely confusing for learning developers.  I suspect that problem completely disappears in Elixir/Phoenix.  Not that Rails isn't great in so many ways, but I'd love someone to show me a real world coding problem that couldn't be addressed without instance variables (assuming no memory constraints).  It also seems to be you could code the Elixir way in Ruby by just refraining from using instance variables.  I start to wonder if parts of our CS educational OO edifice are just completely unnecessary and surplus to requirement, specifically "instance variables".  [Tweet me](https://twitter.com/tansakuu) with counter examples!   Let's get to the bottom of this! :-)





