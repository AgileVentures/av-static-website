---
title: Grading the World
date: 2016-07-15
tags: autograding forking threads ruby agile errors coding
author: Sam Joseph
---


I spent a day looking at some issues coming up from the "Agile Development using Ruby on Rails" MOOC autograder.  The MOOC autograder takes code submissions from learners through the edX framework, grades then against specs and other more complex things, and then sends back some output. The more complex "other things" are mainly running mutations against Cucumber features and checking for test coverage.  We have two of these more complex assignments that use a part of the grader that I am less familiar with.  It is called the FeatureGrader.

We had a lot of trouble late last year with the FeatureGrader getting completely stuck.  I managed to fix that with what were ultimately fairly small changes to the timeout settings, but in reviewing that and other FactoryGirl related problems it seems clear that one of the major problems with the FeatureGrader in its current incarnation is that errors running the student code tend not to be propagated back up and students tend to see this [generic error](https://github.com/saasbook/hw-bdd-cucumber/issues/8):


> There was a fatal error with your submission. It either timed out or caused an exception

In some cases the simple solution is just running the application on your local machine, but in the more complex assignments it is possible that all the code will pass on a learner's local machine, but the grader will encounter some kind of issue.  In particular with the mutation grader where the idea is to expose situations where the learner's acceptance tests are not exercising the underlying application rigorously enough.

Yesterday I started work on trying to see what error was behind this particular [learner issue](https://github.com/saasbook/hw-bdd-cucumber/issues/8).  It turned out to be a simple ActiveRecord error that should be discoverable by the student on their local machine, but having got started on that I pushed on to have a look at why that error was not being exposed to the learner.

The autograder runs student code in a sandbox created by a Ruby `fork` operation.  Here's the code from the heart of the grader:

```ruby
    # Takes a Proc object representing the grading behavior of the auto grader and runs it in a separate process.
    # If the subprocess takes longer than @timeout seconds, the function will kill the subprocess and return a score
    # of 0.
    def run_in_subprocess(grading_func)
      begin
        read, write = IO.pipe
        logger.debug('Start subprocess to run student code.')
        @pid = fork do
            read.close
            # $stdout.reopen('stdout_subprocess', 'w')  # Don't clutter the main terminal with subprocess information.  If you are wondering, RSpec writes its output to STDOUT
            # $stderr.reopen('err_subprocess', 'w')  # and you can't redirect it w/o redirecting all of STDOUT.
            output_hash = grading_func.call
            write.puts JSON.generate output_hash
            write.close
        end
        Timeout.timeout(@timeout) do
          Process.wait @pid
        end
        write.close
        subprocess_response = read.gets
        output_hash = HashWithIndifferentAccess.new(JSON.parse(subprocess_response)) unless subprocess_response.nil?
        read.close
      rescue Timeout::Error
        logger.info('Subprocess timed out killed and submission received 0 pts.')
        Process.kill 9, @pid # dunno what signal to put for this
        Process.detach @pid  # express disinterest in process so that OS hopefully takes care of zombie
      end
      output_hash
    end
```

An individual grader like the FeatureGrader extends the base AutoGrader class (of which the above method is a part)

```ruby
  class FeatureGrader < AutoGrader
    
    # other methods removed for brevity

    def grade
      response = run_in_subprocess(method(:runner_block))
      if response
        response
      else
        ERROR_HASH
      end
    end

    private
    def runner_block
      load_description

      ENV['RAILS_ENV'] = 'test'
      ENV['RAILS_ROOT'] = @base_app_path

      start_time = Time.now

      score = Feature.total(@features)   # TODO: integrate Score

      @raw_score, @raw_max = score.points, score.max

      log "Total score: #{@raw_score} / #{@raw_max}"
      log "Completed in #{Time.now - start_time} seconds."
      
      dump_output
      {raw_score: @raw_score, raw_max: @raw_max, comments: @comments}
    end

    # other methods removed for brevity
  end
```

We can see here the FeatureGrader `grade` method that hands off the forking operation to it's parent AutoGrader class.  The running in a separate subprocess is common across the different styles of grader, but the actual running of the grader is not.  We can see above how the method `grade` specified to the `run_in_subprocess` parent method which method should actually be run to grade the learner's submission.  In this case the private method `runner_block` is passed in and we can see that apart from a few bits of station keeping, the key operation is run through the Feature.total method, which runs Cucumber Features in another sandbox.

Here's the Feature total method:

```ruby
      # from the Feature class
      class << self

        # Compute the total +Score+ resulting from the given array
        # of +Feature+s.
        #
        # See +$config[:mt]+
        #
        def total(features=[])

          s = Score.new

          features.each do |f|
            begin
              result = f.run!
              s+= result
            rescue TestFailedError, IncorrectAnswer
              s += -result
            end
          end

          # Dump output. TODO: better way to do this?
          features.each { |f| f.dump_output }
          return s
        end
      end
```

Each of the features in the learner submission is run through the Feature `run!` class method, the heart of which is this:

```ruby
        begin
          raise TestFailedError, "Nonexistent feature file #{h["FEATURE"]}" unless File.readable? h["FEATURE"]
          raise(TestFailedError, "Failed to find test db") unless File.readable? SOURCE_DB
          FileUtils.cp SOURCE_DB, h["TEST_DB"]
          Open3.popen3(h, $CUKE_RUNNER, popen_opts) do |stdin, stdout, stderr, wait_thr|
            #exit_status = wait_thr.value

            lines = stdout.readlines
            lines.each(&:chomp!)
            self.send :process_output, lines
          end

        ##
        # XXX Can only raise StandardError in these rescue clauses.
        # Any other subclass will give incorrect results ("undefined method `-@'").
        ##

        rescue TestFailedError => e
          log "test failed: #{e.inspect}"
          log e.backtrace
          raise StandardError, e.message

        rescue => e
          log "test failed: #{e.inspect}"#.red.bold
          log e.backtrace
          raise StandardError, "test failed to run because #{e.message}"

        ensure
          FileUtils.rm h["TEST_DB"] if File.exists? h["TEST_DB"]

        end
```

and we can start to see the reason why errors in the student code don't get exposed as part of the grader output.  Inside this ```begin ... rescue``` we can see the `CUKE_RUNNER` being started into an external process.  All the errors that are rescued are re-raised as StandardErrors.  There's a comment that says they have to be Standard Errors.  That's a comment from Jonathan Ko from 2012.  If we trace the path of execution back up to the top level of the system, we see that an error raised by running the learner's Cucumber features will we re-raised here in `Feature.run!`, will stop execution of the `Feature.total` method who's rescue clause only checks for TestFailedError and IncorrectAnswer custom errors. Note that this means that we skip the `dump_output` operation at the end of that method. The FeatureGrader's `runner_block` method will halt execution, skipping other `dump_output` operations.

Ultimately what this means to the AutoGrader class is that nothing will be returned from the `runner_block`, so there is no output from the call to the grading function, and nothing to report to the learner, other than the default message about the underlying process having errored.  I managed to change that yesterday by modifying the `Feature.total` method like so:

```ruby
        def total(features=[])

          s = Score.new

          features.each do |f|
            begin
              result = f.run!
              s+= result
            rescue TestFailedError, IncorrectAnswer
              s += -result
            rescue => e
            end
          end
```

This looks like terrible coding practice.  I'm catching an error and not doing anything with it.  However it makes some sense in this case because the error's it is catching are actually re-thrown ones that have already had their output written to a log, which will get passed back up to the autograder as long as we avoid these re-thrown errors derailing the process.

I was able to test this on the staging grader yesterday, and although the output is verbose, and possibly confusing to the learner, it at least contains information about the underlying error.  Frustratingly, while this works for the FeatureGrader for the BDD Cucumber assignment, similar changes to the Acceptance Unit Test Cycle assignment grader do not have the same effect.  Writing this blog, I start to think there are better ways to fix the problem with the BDD Cucumber grader, which might be to see if we can't get away with wrapping the learner's code StandardErrors in the custom TestFailedError and see if we can work round the issue mentioned by Jonathan Ko.

In terms of immediately improving the learner experience, it feels like I should push on to see if I can't get the other feature grader (for the Acceptance Unit Test Cycle assignment) to spill the beans too.  What's frustrating is that the code is complex and pretty smelly, and I don't trust all the tests.  I've just set up a manual testing framework for the staging grader that allows me to test the live system in fairly short order; so I'll take a pop at that later today.

However, while this is an interesting system; pure ruby, forking different processes; I still question the fundamental architecture.  The architecture is predicated on the need to keep the grading strategy and the solutions a secret.  This necessities a centralised architecture with various levels of complexity.  If the code was already well factored and organised that would be one thing.  It's certainly an interesting challenge to get this code into the shape where it would be easily maintainable and the learner experience would be excellent, and we'd learn a lot in the process, but I can't help thinking that it would be much much easier if learner's could submit their code via pull requests to public repos and have their code assessed by industry standard tools such as Semaphore, Travis and CodeClimate.

In particular if the chief concern is that the presence of solutions out there on the web is too tempting for time constrained solutions, let us reflect that all the solutions are already out there via one mechanism or another.  Trying to keep the solutions secret is an expensive arms race.  I think we should focus more on having students follow the process.  Even if you have all the code at hand, actually successfully submitting a pull request is not trivial.  Add a CI process that checked that the code had sensible individual git commits associated with your own GitHub account and checked that appropriate chunks of TDD'd code, and we'd be assessing learners based on their ability to follow an effective coding process rather than just submitting the "magic solution".

The desire to keep the grading strategy and the solutions secret is about trying to force learners to come up with their own solutions, to think for themselves, but I'm not sure this is really the most effective approach.  The reality of "Google" coding today is that there's always lots of other people's code to look at.  Copying and pasting that code without understanding it is very dangerous, but what saves you is having a process to follow; processes like Test first, pair programming driver/navigator rotation, only writing the simplest piece of code to get a test to pass, following style guidelines and paying attention to code metrics ... like the Karate Kid we should be focusing on getting learners to wax-on, wax-off until it's second nature and drop this arms race to keep solutions secret.










