---
Title: Extending Minitest 5
Author: Samuel Mullen
---

# Extending Minitest&nbsp;5
## Samuel Mullen

---

## Reasons I use Minitest 

* It comes standard with Ruby >= 1.9
* It's easy to learn
  * I never felt like I was using Rspec to it's full potential
* It's fast!
* The code is "easy" to read
* It's easy to hack

---

## What we'll cover

* New assertions and expectations
* Plugins
  * Progress Reporters
  * Statistics Reporters

---

# Adding Assertions and Expectations

---

## What is an Assertion or Expectation?

* A construct used in testing to determine if results match what is expected
* Assertion examples:
  * `assert_equal`, `assert_includes`, `assert_nil`, `assert_match`
  * `refute_equal`, `refute_includes`, `refute_nil`, `refute_match`
* Expectation examples:
  * `must_equal`, `must_include`, `must_be_nil`, `must_match`
  * `wont_equal`, `wont_include`, `wont_be_nil`, `wont_match`

--- 

## Why would you want to add new Assertions and Expectations?

* Readability
* It points out specifically what is being tested
  * In the case below it's `some_decimal` not `round`
* It's DRYer
* It's less prone to errors

``` ruby
some_decimal.must_round_to 3

# ... is more readable than ...

some_decimal.round.must_equal 3
```

---

## Adding a New Assertion
### Step 1: Positive and Negative Check

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

## Adding a New Assertion
### Step 2: Add the Expectation

``` ruby
class Minitest::Assertion

  infect_an_assertion :assert_admin, :must_be_admin, :unary

  infect_an_assertion :refute_admin, :wont_be_admin, :unary

end
```

---

## Adding a New Assertion
### New Usage

``` ruby
class User < ActiveRecord::Base
  def admin?
    role == "admin"
  end
end

admin = User.new(role: "admin")
user = User.new

assert_admin admin 

refute_admin user

admin.must_be_admin

user.wont_be_admin
```

---

## Expectation Flags
### `:unary` and `:reverse`

* Most Assertions and Expectations won't use them
* just "truthy" values
  * could be `true`, `1`, or even `:foo` 
* Flips the argument order in the method's logic
  * Example: `must_include`, `must_be_empty`, `must_be_nil`

---

# Minitest Plugins

---

## Conventions

* Files must reside under a directory named `minitest`
  * That directory must be part of `$LOAD_PATH`
    * `$LOAD_PATH << path/to/application/dir`
* File names must end with `_plugin.rb`
* Plugins must have at least a `plugin_example_init` method
* A `plugin_example_options` method can be added to handle added options
* Ignore everything above if you are overriding Minitest methods and classes

---

# Progress Reporters

---

## Progress Reporters: The Hard Way 

### Setup

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

### Main focus

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

### Leftovers

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
* Run after the `Minitest::ProgressReporter`
* Generally subclassed from `Minitest::StatisticsReporter`

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
* Gamify the results
* Output to USB lights or other devices
* Create your own Continuous Integration (CI) setup 

--- 

## Concluding Thoughts

* Extending Minitest is just as easy as Minitest itself
* Minitest simplicity makes it fun to play with  
* Read the code. No, really.

---

# Questions?
