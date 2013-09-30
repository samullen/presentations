---
Title: Extending Minitest 5
Author: Samuel Mullen
---

# Extending Minitest
## Samuel Mullen

---

## Extending Minitest 5

* New assertions and expectations
* Plugins
  * Progress Reporters
  * Statistics Reporters

---

## Note:

* I'm assuming some familiarity with testing
* I will be focusing on Minitest::Spec
  * i.e. Expectations

--- 

## Reasons I use Minitest 

* It comes standard with Ruby >= 1.9
* It's easy to learn
  * I never felt like I was using Rspec to it's full potential
* It's fast!
* It's easy to hack

---

# Adding Assertions and Expectations

---

## Why would you want to add new Assertions and Expectations?

* Readability
* It's DRYer
* It's less prone to errors
  * `some_decimal.must_round_to 3`
  * ... is more readable than ...
  * `some_decimal.round.must_equal 3`

---

## Adding the new Assertion
### It's just "monkey patching"

``` ruby
class Minitest::Assertion
  def assert_admin(obj, msg=nil)
    msg = message(msg, "") { diff true, obj.admin? }
    assert true == obj.admin?, msg
  end

  def refute_admin(obj, msg=nil)
    msg = message(msg, "") { diff false, obj.admin? }
    assert false == obj.admin?, msg
  end
end
```

---

## Adding the Expectation
### `infect_an_assertion`

``` ruby
class Minitest::Assertion
  infect_an_assertion :assert_admin, :must_be_admin, :unary

  infect_an_assertion :refute_admin, :wont_be_admin, :unary

end
```

---

## New Usage

``` ruby
User.new.wont_be_admin

User.new(role: "admin").must_be_admin
```

---

## Expectation Flags
### :unary and :reverse

* Most Assertions and Expectations won't use them
* just "truthy" values
  * could be `true`, `1`, or even `:foo` 
* Flips the order of the method call
  * Example: `must_include`

---

# Minitest Plugins

---

## How Minitest 5 Works

--- 

## include "minitest/autorun"

``` ruby
# minitest.rb

require "minitest"
require "minitest/spec"
require "minitest/mock"

Minitest.autorun
```

---

## At Exit

``` ruby
# minitest.rb

def self.autorun
  at_exit {
    next if $! and not $!.kind_of? SystemExit

    exit_code = nil

    at_exit {
      @@after_run.reverse_each(&:call)
      exit exit_code || false
    }

    exit_code = Minitest.run ARGV # <--
  } unless @@installed_at_exit
  @@installed_at_exit = true
end
```

---

## Loading Plugins

``` ruby
# minitest.rb

def self.run args = []
  self.load_plugins # <-- key

  options = process_args args

  reporter = CompositeReporter.new
  reporter << ProgressReporter.new(options[:io], options)
  reporter << SummaryReporter.new(options[:io], options)

  self.reporter = reporter # make it available to plugins
  self.init_plugins options # <-- key

  ...

end
```

--- 

## From Where Does it Load Plugins?

``` ruby
def self.load_plugins # :nodoc:
  return unless self.extensions.empty?

  seen = {}

  Gem.find_files("minitest/*_plugin.rb").each do |plugin_path|
    # find_files reads from $LOAD_PATH
    name = File.basename plugin_path, "_plugin.rb"

    next if seen[name]
    seen[name] = true

    require plugin_path
    self.extensions << name
  end
end
```

--- 

## Summarized

1. include 'minitest/autorun' 
2. starts up via `at_exit`
3. Runs Minitest::run
  1. Read in external plugins
  2. Load Minitest's reporters
  3. Initialize external plugins

---

# Creating Extensions

---

## Conventions

* Files must reside under a `minitest` directory
  * Directory must be part of `$LOAD_PATH`
  * `$LOAD_PATH << path/to/application/lib`
* File names must end with `_plugin.rb`
* Plugins must have at least a `plugin_example_init` method
* A `plugin_example_options` method can be added to handle added options

---

# Progress Reporters

---

## Progress Reporters: The Hard Way 

### Section 1: Appetizers

``` ruby
module Minitest
  def self.plugin_color_reporter_init(options)
    io = ColorReporter.new(options[:io])

    self.reporter.reporters.grep(Minitest::Reporter).each do |rep|
      rep.io = io
    end
  end

  class ColorReporter
    attr_reader :io

    def initialize(io)
      @io = io
    end
```

---

## Progress Reporters: The Hard Way 

### Section 2: Main course

``` ruby
    def print(o)
      case o
      when "." then
        opening = "\e[32m"
      when "E", "F" then
        opening = "\e[31m"
      when "S" then
        opening = "\e[33m"
      end

      io.print opening
      io.print o
      io.print "\e[m"
    end
```

---

## Progress Reporters: The Hard Way 

### Section 3: Leftovers

``` ruby
    def puts(*o)
      io.puts(*o)
    end

    def method_missing(msg, *args) # :nodoc:
      io.send(msg, *args)
    end
  end
end
```

---

## Progress Reporters: The Easy Way

``` ruby
module Minitest
  class ProgressReporter < Reporter
    def record(result)
      case result.result_code
      when "." then
        opening = "\e[32m"
      when "E", "F" then
        opening = "\e[31m"
      when "S" then
        opening = "\e[33m"
      end

      io.print opening
      io.print result.result_code
      io.print "\e[m"
    end
  end
end
```

---

# Summary Reporters

---

## Summary Reporters

* Output the results of the test run
* Run after the `ProgressReporter`
* Generally subclassed from `StatisticsReporter`

---

## Summary Reporters: Example
### Initialization

``` ruby
module Minitest
  def self.plugin_vocal_reporter_init(options)
    self.reporter << VocalReporter.new(options[:io], options)
  end
```

--- 

## Summary Reporters: Example

``` ruby
class VocalReporter < StatisticsReporter
  def report
    super 

    percentage = failures / count.to_f * 100.0

    if self.failures > 0
      message = "#{count} tests run with #{failures} failures."
      if percentage > 50.0
        message << " Are you even trying?"
      else
        message << " That kinda sucks"
      end
    else
      message = "%d tests run with no failures. You rock!" % self.count
    end
    `say #{message}`
  end
end
```

---

## Summary Reporters
### Other Ideas

* Track the results
  * See percentages and who's doing the best with testing
* Output to USB lights or other device
* Create your own Continuous Integration (CI) setup 
* Gamify the results

--- 

# Concluding Thoughts

* Extending Minitest is just as easy as Minitest itself
* It's simplicity makes it fun to play with  
* Read the code. No, really.
