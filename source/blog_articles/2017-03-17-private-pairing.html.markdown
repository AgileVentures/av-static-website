Where to start? Intense week.  Just finished a one hour private pairing session on the [tennis kata](http://codingdojo.org/kata/Tennis/) with a Ruby programmer I met on the [RailsLink Slack](http://www.rubyonrails.link/).  They were suggesting Vim/Tmux, but we ended up using Hangouts and GitHub pong.  It was a good session and I wish I'd recorded it.  It was very smooth ping pong back and forth, with some good discussion.  I'd asked my pair about recording, but they'd demurred and I didn't push it.  I beat myself up internally, because I feel like it could have been a really great video for bootcamp grads and new AV members to watch.  Unlike other meandering sessions where we're slogging through a legacy codebase, this was more likely something folks could get their head around--a video we might have been able to promote.  However I think we've got to respect people's preferences.  Not everyone wants their activities vidoed and played back, and perhaps that's part of the crux of the issue at AgileVentures.  Maybe the Google Hangout plugins going offline on April 25th will be a blessing rather than a curse, as starting manually from YouTube live actually gives the Hangout creator control over whether to make the video private, unlisted, or public on YouTube.

Anyway, all the code from the session is in a [repo on github](https://github.com/tansaku/tennis-kata) where you can see the nice git pong commit history:

![commit history](https://www.dropbox.com/s/ybsik7662m3dsg1/Screenshot%202017-03-17%2010.37.31.png?dl=1)

I picked up some good tips from the pairing, using a `let` instead of a named subject `subject(:tennis) {described_class.new}
`:

```rb
require 'tennis'

describe Tennis do
  let(:tennis) { subject }

  specify "players are love-all when game starts" do
    expect(tennis.score).to eq "0:0"
  end
end
```

I was pulled back from excessive self-encapsulation, being referred to a [Martin Fowler article on the subject](https://martinfowler.com/bliki/SelfEncapsulation.html).  I'd worked hard with Ruby to work out how to use private `attr_writers` internally

```rb
class Tennis

  attr_reader :score

  def initialize
    self.score = "0:0"
  end
  
  private
  
  attr_writer :score
end
```

It's interesting how a pairing session can be alot about how you give ground.  I think my problem in the past has been my tendency to love joining a good debate about going either way with a particular approach; and I get the sense that a lot of people would rather have much less talk and a lot more coding going on.  Fair enough.  I tried that in this session and in the Elixir pairing session with Ryder on Thursday that was recorded.  That was great fun too.  I immediately want to try out this Tennis kata in Elixir, as the really interesting thing is that the first way we reach to do the kata in Ruby is to start introducing state.

Pairing on Kata and other people's personal projects (like Ryder's text adventure game) is a new direction for me.  I'm usually so desparate to make every coding second count towards contributing to solving a charity or nonprofit problem.  I'm running out of time and money and unless I land a contract very soon it's going to be full time job search for me, and I won't have the time to just do random pairing.  Gosh, I wish all this had been available before I had kids.  I could have learnt so many valuable lessons about coding and communicating with folks.

At least at the end of the Tennis Kata session I got the chance to show what AgileVentures was about, and at the end of the session with Ryder I was able to explain my still unrealized vision of a massive online pair programming community.  Games like LeagueOfLegends, Splatoon and OverWatch allow anyone around the world to log in and hang out in gaming lobbies, until they've got enough folks together for a team game.  It just seems like it's totally possible to do the same thing for pair programming.  You could have a video wall of all the different ongoing sessions with people browsing what they'd like to focus on (à la twitch.tv) but actually able to participate.

I've been wanting that for the last five years and channelled it through the MOOC, bootcamps, AgileVentures, but it still doesn't exist in the form that I imagine.  Perhaps we have to build our own screensharing teleconferencing system.  As they say "Markets can stay irrational longer than you can stay solvent".  I think once people get over their initial resistance to pairing with strangers online and being video'd, they get so much out of it.  If only we had a properly staggered system that allowed people to do some initial private pairing "free tasters" and then an escalator that introduced them to the possibilities of online pairing.

I think the interface has just got to be smooth.  Like playing a video game - just click a few buttons and you're in.  None of these off-putting warning messages from Google Hangouts.  The interface updating in realtime as other people come on and offline.  Perahps our new AgileVentures Electron project will help - or some other kind of Slack integration.  Slack seems like the perfect lobby system to me.  We just need the ability to have recordable pairing sessions launched from Slack and archived and then be browsable by technology.  Actually I think the streaming/recording is most useful as an activity trace for others to see what's happening and see that it can happen and realise that they can do it themselves ...

We'll get there, although knowing reality, someone else will do it first.  FreeCodeCamp is part way there, but they're JavaScript only and the pairing is adhoc (I think) - the online bootcamps have it for a select rich few - if you're inspired come help us make it a reality.  I think it should be open for everyone in the world to participate, just like an online computer game.  Maybe we need a design sprint?

Related Videos:
--------------

* [Elixir Pairing with Ryder](https://youtu.be/kYQP9sRgXRY)
 
