autoscale: true
build-lists: true
theme: Scherzkeks, 7

# *Test Driven Development*
## You've Been Doing it all along

---

# Hi!

![left](avatar.jpg)

^
- Hi. I'm Samuel Mullen and I'm going to be talking to you today about Test
  Driven Development, or TDD
- Background
- I've been writing software for about 20 years
- About half of that I've been practicing TDD
- I tell you this only to let you know 
- 1. I know what it's like to develop w/o it
- 2. I know what it's like to develop w/ it
- In then next 30-45 minutes, we're going to...
- 1. Get an overview of both TDD and non-tdd development processes
- 2. See where they're similar and where they differ
- 3. See how not using TDD is making your programming life 
- 4. Look at the benefts of TDD
- 5. How to get started with TDD
- ...but to get going, let's look at some lies about TDD

---

# Lies, *Damned Lies*, and **TDD**

^
- I'm sure everyone here has heard about TDD
- I'd like to address some inaccurate thoughts about TDD

---

# Lies *About* TDD

* It takes longer to code w/ TDD than without
* Twice the code means twice the maintenance
* TDD only works on small projects
* Our project is different

^
- TDD increases initial dev time by 10-30%. 
- It reduces bugs in production by 40-80%
- Ruby on Rails, Chrysler Comprehensive Compensation System, Windows 7
- Lest anyone think I'm not "fair and balanced"

---

# Lies *for* TDD

* No longer a need for architecture and design
* Your code will be cleaner
* TDD Means Bug-Free code

^
- People are always trying to get out of thinking. TDD won't help you there.
- TDD certainly helps fight complexity, but there are a lot of developers whose
  comfort zone is complexity
- Yeah....

---

# TDD Overview

![left](http://via.placeholder.com/600x1000)

* Write a test for the next bit of functionality you want to add
* Write the functional code until the test passes
* Refactor both new and old code to make it well structured

^ 
- At its simplest...
- TDD is a dev process used to build software by first writing tests describing what the code should do
- and *then* writing _only_ the code necessary to make the tests pass
- More can be said about the process (and has), but that is the core of TDD

---

# Non-TDD Overview

![left](http://via.placeholder.com/600x1000)

* Determine the expectations for the next bit of functionality to add
* Write the functional code until it meets your expectations
* Tweak as necessary as you better understand the problem

^
- The Non-TDD dev process is what most of use from day one
- There's nothing formal about, and definitely nothing automated
- There's a problem, and we create a solution for that problem according to our understanding at the time
- If you were to codify the process, it would look like this

---

# You're Already Doing TDD

| Development Step | TDD | Not TDD |
| :---: | :---: | :---: |
| **Determine Results** | Create test to prove results | Expect specific outcomes |
| **Write Code** | Complete when tests pass | Complete when results meet expectations |
| **Cleanup** | Refactor new and existing code | Refactor new code. Fix any noticed errors in existing code |

--- 

# Benefits of TDD

---

# Getting Started

* It doesn't click the first time you try it

Other Things
------------

* Mention that when I take on new projects I always ask if there are tests
  - Helps me figure out if things are going to work
  - Helps me determine estimates
  - It tells me how hard it's going to be to upgrade/refactor

^
R Jeffries and G Melnik, writing for IEE have reported that TDD helps in decreasing production bugs by 40-80%. Of course, it can add 10-30% to the initial development time and cost but considering the factors like defect fixes, and maintenance it is justified to implement TDD for the first phase of project development. In fact, the benefits of TDD have been tested on real projects by several companies and they found that the process is enormously beneficial.

---

