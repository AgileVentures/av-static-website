So yesterday I found myself in a second interview for a "Ruby developer" job at the UK MOOC provider FutureLearn.  I've been applying for jobs here and there over the last six months as it's nice to keep one's options open.  Having surrendered to the idea that AgileVentures won't provide a sustainable income for me anytime soon, it was nice to get a first interview (over Skype) at FutureLearn and then a tech test, and then a second interview.  I was looking forward to the afternoon which was going to broken up into a discussion of my tech test, a pair programming session and a meeting with some client and frontend specialists.

The tech test was on writing a game of TicTacToe, which I think I've blogged about before, and I used an outside-in approach as I'd done with the VendingMachine tech test example at Makers Academy.  This approach had not produced code that I was wonderfully proud of, but I was pleased with parts of my hand rolled acceptance tests, and how one could generate new ones from written descriptions of game play that matched the user experience.  I felt I'd done a reasonable job of method naming, and had started on refactoring out some different classes.  Late on Friday evening during my week off I'd done another version of TicTacToe starting from inside-out, and on the train on the way into the meeting I pushed that a little further, as I was thinking I was starting to more clearly grasp how these two different approaches intermixed.

In my second version I'd driven from unit-tests focused on domain entities such as board, cell and player.  I was at the point of starting to have these entities communicate with each other to play a turn of TicTacToe, or at least update the board representation to reflect a players move.  In the first session with the various methods of the different objects at the forefront of my mind I had written the following: 

```ruby
move = player.choose_move(TicTacToe.valid_moves(board)) 
board.contents_of(move).hold(player.symbol)
```

Dropping that into the README in the first instance, as a rough sketch of how the domain entities might communicate.  I read that back to myself on the train and felt like I wanted to move the `valid_moves` check into the player object, giving me:

```ruby
move = player.choose_move(board) 
board.contents_of(move).hold(player.symbol)
```

my reasoning being that a player entity might well want to be able to review the state of the rest of the board, rather than just the valid moves, before choosing what to do next.  I got the player spec set up to handle that, and after a couple of rounds of changes, and with a dash of dependency injection I had the player spec like so:

```rb
describe Player do

  subject(:player) { described_class.new(rules_klass) }
  let(:board) { double :board}
  let(:rules_klass) { double :rules_klass}

  it 'can choose a move' do
    allow(rules_klass).to receive(:valid_moves).with(board).and_return(:A1)
    expect(player.choose_move(board)).to eq :A1
  end
  
end
```

and the player thus:

```rb
class Player
  def initialize(rules_klass = TicTacToe)
    @rules_klass = rules_klass
  end

  def choose_move(board)
    Array(rules_klass.valid_moves(board)).sample
  end

  private 

  attr_reader :rules_klass
end
```

before I moved on to being able to update the board.  Now the `board.contents_of(move).hold(player.symbol)` line reflected the properties of the board and cell objects that I had created before.  What would the context be for such a statement?  I created a game class that would have a `handle_move(player)` method that would house this code.  The spec ended up looking like this:

```rb
describe Game do
  
  subject(:game) { described_class.new(board_klass) }
  let(:board_klass) {double(:board_klass)}
  let(:player) { double(:player, symbol: 'X') }
  let(:board) { double(:board) }
  let(:cell) { double(:cell) }

  it 'handles player moves by updating board with player move' do
    allow(board_klass).to receive(:new).and_return(board)
    allow(player).to receive(:choose_move).with(board).and_return :A1
    expect(board).to receive(:contents_of).with(:A1).and_return cell
    allow(cell).to receive(:hold).with('X')
    game.handle_move(player)
  end

end
```

I think what I was seeing here was a mock train wreck that reflected a possible demeter violation in the way I had initially written the code.  I had all the tests passing with the Game class set up like this:

```rb
class Game
  def initialize(board_klass = Board)
    @board = board_klass.new
  end

  def handle_move(player)
    move = player.choose_move(board)
    board.contents_of(move).hold(player.symbol)
  end

  private

  attr_reader :board
end
```

The train journey ended before I could get any further, but I sensed that the way the test was pointing me would look something like this:

```rb
board.register(move, by: player)
```

not sure about that `by:` key - just trying to make things as close to an English sentence as possible, which is always a little tricky when a method takes more than one argument.  What I thought the convolution in the test was telling me was that game knew a little too much (a demeter violation) about the relations that board had with the Cell class.  Changing the code to this last example would mean that the Game class didn't need to have any knowledge of the Cell class, and so testing it would be much simpler, and my domain entities would be loosely coupled.  I think this is the point that's made in Test Driving Object Oriented Design, and it strikes me that this is one of the potential benefits of unit tests, which is to help one evolve one's domain entities into a more loosely coupled configuration.

Ironically this didn't really come up in the discussion of the tech test at future learn, although we did talk a lot about how most folks attempted the TicTacToe test by creating a series of small classes (as I had done in this second attempt) and I quoted Sandi Metz's 100 line limit and notes on the dangers of refactoring before one is clear what the next round of changes are likely to be to justify my decision to refactor out a board class, but not yet a player class in my initial outside-in approach.

It strikes me that being sufficiently familiar with rspec to be able to mock effectively and to be aware how the presence of excessive mocking (or mocking things you don't own) is quite a tall order for the beginning programmer.  Furthermore, this benefit is lost, to a degree, when working in a framework like rails, where the system architecture is laid out for you.  Immediately after the tech test discussion I was pairing with a different FutureLearn person on adding a feature to a slimmed down version of their site.  We used an interesting style of 'Given/When/Then' within a RSpec/Capybara feature test and then dropped to controller and model specs to get the job done.  It was a smooth pairing experience, switching driver/navigator roles when we got the error message to change significantly.  It just underlined how different it is working with Rails as opposed to Plain Old Ruby Objects (PORO).

I could go on about the final section of the day, but with code snippets this blog has already gotten quite lengthy, but suffice to say that I was impressed with FutureLearn's interview process and the effort they were going to have the interview be a reflection of what it was like to work in their team. 







