autoscale: true
build-lists: true
theme: Ostrich, 5

# Is Ruby/Rails **Dead**?

---

# **Development** Performance

---

# Development Performance: Pros

- Ruby is designed for **developer happiness**
- Massive collection of Gems
- Ruby is powerful
- It takes far less time to develop a full-blown application in Ruby than other languages

^ 
- Elixir doesn't have these
- JS does, but they're made up of 1,000 smaller 3-line modules
- Java has it, but ewwww
- You can do more in a handful of lines of Ruby than you can in pages of other languages (including JS)
- Functional languages are also terse, but they're hard to reason
- Provides a "framework" to lay out your application

---

# Development Performance: Cons

- Ruby is slow

an application that takes 2 seconds to boot compared to one that takes 10 seconds has very different effects on the developer experience." — Jose Valim

"A slow feedback cycle during development hurts your team’s productivity and affects their morale." —Jose Valim

^ 
- You can feel it. Even when you're not running Rails.
- How many articles have we seen that show how to speed up our testing suite?
  - Focusing on mocks
  - Avoiding ActiveRecord
  - Spork/Spring
  - The latest is ignoring logging altogether

---

# Performance

---

# Performance
## Pros

- Every new version of Ruby comes w/ a 5+% speed improvement
- Rails has a solid caching system to help in this area
- Several different worker tools exist

---

# Performance
## Cons

- Ruby is slow
- Ruby is a memory hog
- Green threads
- Can't do parallel operations

---

# Performance
## Other thoughts

- Regardless of technology, you will eventually have to focus on performance
- Huge performance gains can be made with clean code, better queries, and indexing your database
- rack-mini-profiler is your friend
- bullet is your friend

^ 
- It just comes sooner w/ Ruby

---

# Security

---

# Security
## Pros

- Ruby and Rails are both great about getting security patches out there
- Lot of built in security features out of the box

---

# Security
## Cons

- Wasn't able to find any

---

# Security
## Other thoughts

- Rails is doing a lot of work other frameworks don't
- CSRP
- hiding passwords
`rake middleware`

---

# Will rewriting help?

> Halving our application’s latency means we need about half the amount of servers we needed before. So, congratulations: you rewrote your application (or chose your framework) to save $3,000/month. The load on the relational database backing this application won’t change, so those costs will remain the same. When your application is big enough to be doing 20,000 RPM, you will have anywhere from a half-dozen to even fifty engineers, depending on your application’s domain. A single software engineer costs a company at least $10,000/month in employee benefits and salary. So we’re choosing our frameworks based on saving one-third of an engineer per month? And if that framework caused your development cycles to slow down by even one third of a mythical man-month, you’ve increased your costs, not decreased them. Choosing a web framework based on server costs is clearly a sucker’s game.

---

# Final thoughts

- most questions about "is x dead" are driven by *fear*
- Developers are looking for a **single/simple** solution to use everywhere
- Pick the technology that fits the problem

^ 
- Ruby isn't going to solve that, and neither is any other language.
- Web development is hard
- Systems work is hard too
- You're drinking from the firehose
- It's hard to pick the tech that fits the problem
