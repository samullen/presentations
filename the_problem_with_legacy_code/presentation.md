autoscale: true
build-lists: true
theme: Scherzkeks, 7

# **Legacy** Isn't a Bad Word

---

# Hi!

^ Background
^ Started running Linux in '97 the same time I started programming professionally
^ Started web development in '99
^ Used open source languages and technologies that entire time
^ Currenty run Pixelated web development shop focused on rescuing legacy projects
^ Tell you these things...
^ 1. to keep from getting run out on a rail for running this obviously heretical machine
^ 2. to let you know I've run across legacy code before. maybe once or twice
^ In then net 30-45 minutes, we're going to look at 
^ 1. what legacy code is
^ 2. why it's bad
^ 3. the costs associated with it
^ 4. the opportunities legacy code presents

^ The first rule of legacy code is...

---

![](dont_panic.jpg)

---

# Legacy Code
## What is it?

^ As a programmer, you'll eventually have to deal with legacy code
^ but what is it?
^ If I were to ask you about legacy code, which of these pictures comes to mind?

---

![](here_be_dragons.jpg)

---

![](spaghetti.jpg)

---

![](shelobs_lair.jpg)

---

![](madhouse.jpg)

---

# Why Do We Hate Legacy Code?

* Focused on what's next
* It doesn't "feel" like contributing
* Arrogance

^ Why do these images come to mind?
^ Working with what's next allows us to play with new technologies. Legacy code doesn't.
^ Just keeping somehting working doesn't feel like making a difference.
^ it feels like we've been sidelined because our "idiot boss" doesn't recognize our potential and ability
^ As you may have gathered from the  title of this presentation, I have a different perspective
^ But before I get into that, let's get a working definition of what legacy code is

---

# Legacy Code
## Definition

^ When we talk about legacy code, there are two definitions we're working with 
^ Literal, and a more practical 

---

# The Literal Definition

> Legacy code is source code that relates to a no-longer supported or manufactured operating system or other computer technology. The term was first used by Computer Scientist George Olivetti to describe code maintained by an administrator that did not develop the code.  
-- Wikipedia - Legacy Code

^ While this is literally what legacy tech is, it's not what we think of when we think of legacy code. 

---

# The Practical Definition

> Code without tests is bad code. It doesn’t matter how well written it is; it doesn’t matter how pretty or object-oriented or well-encapsulated it is. With tests, we can change the behavior of our code quickly and verifiably. Without them, we really don’t know if our code is getting better or worse. 
-- Michael C. Feathers, Working Effectively with Legacy Code

^ this can be boiled down into something simpler

---

> Legacy code is simply code without tests. 
-- Michael C. Feathers, Working Effectively with Legacy Code

^ You may disagree with his definition 
^ consider: when code is covered by a suite of tests, it allows us to make changes and see what in our project is affected (i.e., what breaks). 
^ Without test coverage, making changes can produce unexpected results
^ It can also produce unexpected results in code elsewhere in the project.
^ so really...

--- 

> Legacy code is code which is difficult or unsafe to change.

^ Because you have no real idea how that change is going to affect the rest of the system

---

# The Problem with
# Legacy Code

^ I thinnk there's a lot of opportunity in legacy code
^ But before we can talk about that
^ We need to look at why it's bad

---

# Legacy Code is Complicated

![](its_complicated.jpg)

---

^ Show a complicated block of code

![](example.jpb)

^ This is a single method
^ Don't worry about reading it. I just want to show you the shape
^ There are n if statements, y loops, and it's z levels deep at deepest nesting
^ Who here think they can keep this in their head at once?
^ Who even wants to try?
^ Oh, and here's all the other methods it's calling. You have to keep those in
your head too.

---

^ Show it is connected to/from other blocks

![](example.jpg)

---

# Aside: Cyclomatic Complexity

|Score|Meaning|
|---|---|
|1-10|Structured and well written code<br/>High Testability</br>Cost and Effort is less|
|10-20|Complex Code<br/>Medium Testability<br/>Cost and effort is Medium|
|20-40|Very complex Code<br>Low Testability<br/>Cost and Effort are high|
|>40|Not at all testable<br/>Very high Cost and Effort|

^ Is a simple heuristic to measure code complexity
^ It's calculated by adding 1 for each branch of logic
^ Each 'if', 'else', 'while', 'case', etc. adds 1 to the complexity
^ This code has a score of x
^ What happens if you try to make a change to a block of code like that?

---

# Legacy Code is Brittle

![](brittle.jpg)

^ It's going to break
^ because "legacy code is code which is difficult or unsafe to change"

---

# About Brittle Code

* The code (probably) works
* Changes will require new logic
* Changes will (likely) have unexpected consequences
* Changes will make it more brittle

---

# Wouldn't it be nice
## If your code had a<br/>nervous system?

![left fit](nervous_system.png)

^ So it could tell you when part of it was damaged?
^ Or wasn't communicating to other areas correctly?
^ we'll talk about that in a bit
^ If you're code is complicated and brittle, what's going to happen to your development speed?

---

# Legacy Code Slows Development

![](slomo_punch.jpg)

^ Am I telling you anything you didn't already know?

---

# It Takes Time

* To understand the code
* To make the necessary changes
* To ensure the changes work 
* To ensure the changes don’t break existing product

^ Okay, who here hates legacy code a little bit more than before I started?
^ But the time it takes you to do all these things has its own costs

---

# Legacy Code is Expensive


![](money_pit.jpg)

---

# Legacy Code Costs

* Maintenance Costs

> In 2002, the National Institute of Standards and Technology (NIST) estimated that software bugs cost the U.S. economy nearly $60 billion dollars

> [s]oftware developers ... spend approximately 80% of development costs on identifying and correcting defects..."

^ 0.6% of the GDP
^ The GDP for 2016 was $109 billion

---

# Legacy Code Costs

* Maintenance Costs
* Support Costs

1. Customer base
2. Code quality
3. Product usefulness

^ Of these three, the quality of your product is the only thing your team can have a direct impact upon, and the one area which is guaranteed to affect support costs.

---

# Legacy Code Costs

* Maintenance Costs
* Support Costs
* Legal/Compliance Costs

^ Failure to be HIPAA or PCI compliant can result in costs anywhere from $100 to $1.5M
^ These costs are time based.
^ Legacy code slows you down from being able to comply

---

# Legacy Code Costs

* Maintenance Costs
* Support Costs
* Legal/Compliance Costs
* Talent Costs

^ Who here likes working on legacy code?
^ Who here would take a job knowing they'd be primarily working in legac code?
^ 'nuff said

---

# Legacy Code Costs

* Maintenance Costs
* Support Costs
* Legal/Compliance Costs
* Talent Costs
* Opportunity Costs

^ The way we were taught about opportunity costs in school
^ Opportunity costs work exactly the same way IRL.
^ Legacy code and technical debt frame your decisions.
^ Every decision you make must first address dealing with the existing code
^ The added complexity and time it takes to deal with the code only increase the opportunity cost.
^ In reality, legacy code robs you of choice.

---

> If you can get today’s work done today, but you do it in such a way that you can’t possibly get tomorrow’s work done tomorrow, then you lose.
> – Martin Fowler, Refactoring: Improving the Design of Existing Code

---

# Legacy Code is Demoralizing

![](sad_keanu.jpg)

^ Again. Am I telling you anything you didn't already know?
^ I've been there
^ I know what it's like to face the Gordian Knot of logic
^ To know that the changes I make are going to be small and amount to very little
^ Or even still, make things just a bit worse.
^ To know that there are demands (internal and external) on your to succeed, but only be set up for failure.
^ Have I convinced everyone that legacy code is bad yet?

---

# How did we get in this mess?

???

---

# The Truth About
# Legacy Code

![](lady_justice.jpg)

---

# Legacy Code
# Isn't a Problem

---

# Legacy Code
# Is an Opportunity

---

* Invest in your culture
* Invest in your code base
* The value/opportunity
* Questions?

