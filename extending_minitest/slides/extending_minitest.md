---
Title: Extending Minitest 5
Author: Samuel Mullen
---

# Extending Minitest
## Samuel Mullen

--- 

## Reasons I use Minitest 

* It comes standard with Ruby >= 1.9
* It's easy to learn
  * I never felt like I was using Rspec to it's full potential
* It's fast!
* It's easy to hack

---

# Minitest 5
## April 24, 2013 - Oh God... here we go

9a57c520ceac76abfe6105866f8548a94eb357b6

---

# How Minitest 5 Works

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
