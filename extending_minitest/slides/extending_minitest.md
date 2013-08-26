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

Note:

* I will be focusing on Minitest::Spec
  * i.e. Expectations
* I'm assuming some familiarity with testing

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
  * `some_decimal.must_round_to 3`
  * ... is more readable than ...
  * `some_decimal.round.must_equal 3`
* It's DRYer

---

## Adding the new Assertion
### It's just "monkypatching"

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

## Minitest 5
### April 24, 2013 - Oh God... here we go

9a57c520ceac76abfe6105866f8548a94eb357b6

---

## How Minitest 5 Works

--- 

## include "minitest/autorun"

``` ruby
require "minitest"
require "minitest/spec"
require "minitest/mock"

Minitest.autorun
```

---

## At Exit (minitest.rb)

``` ruby
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

## Summarized

1. include 'minitest/autorun' 
2. starts up via `at_exit`
3. Runs Minitest::run
  1. Load the plugins
  2. Load the default reporters
  3. Initialize the plugins

---

# Example Ruby

``` ruby
class Foo
  def initialize
    @bar = 'something'
  end
end
```

---

# The how and why of minitest's design (i.e. history)

---

# How minitest works:


---

# Plugins

* Progress
* Reporters
  * Export results to:

---

Assertions and Expectations

Read the Code
* It's very approachable
* It's very funny
