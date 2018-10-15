autoscale: true
build-lists: true
theme: Sketchnote, 2

# Effective *Code Reviews*

![](images/matrix.jpeg)

^
- In case no one told you, October's lunch-n-learn is about Code Reviews

---

# What is a *Code Review*?

![](images/what_is_a_code_review.jpeg)

---

## **Definition:**

The activity of systematically examining computer source code with the intent of finding mistakes created or overlooked in the development process, and thus improving the overall quality of the software. Reviews can be performed during pair programming, informal walkthroughs, or formal inspections.

---

# Why Do We Need *Code Reviews*?

![](images/why.jpg)

^
- 1 reason, which is mentioned in the definition, is that it reduces the number of bugs

---

## It *Reduces the Number of Bugs* Introduced Into a System

![](images/bug.jpg)

---

In his book, *Code Complete*, McConnel showed evidence that

> … software testing alone has limited effectiveness – the average defect detection rate is only 25 percent for unit testing, 35 percent for function testing, and 45 percent for integration testing. In contrast, **the average effectiveness of design and code inspections are 55 and 60 percent.**

^
- Furthermore, several case studies cited in the book showed that code reviews
  cut errors by 82% at Aetna Insurance, 99% in IBM's Orbit project, 90% from an
  AT&T project with more than 200 people. Finally, NASA's Jet Propulsion
  Laboratories estimated it "saves about $25,000 per **inspection** by finding
  and fixing defects at an early stage".
- Not only does it reduce bugs, but it also speeds up development

---

## It Improves the *Speed of Development*

![](images/performance.jpg)

---

### Isn't this a contradiction?

- It takes time to perform a code review
- That's time the reviewer isn't coding
- Waiting can be a blocker for developers
- There may be significant back-and-forth to finalize the PR

---

### This isn't a contradiction

- Development speed is dramatically increased over the long-term
- High quality code is easier to change
- It's easier/faster to fix bugs before they're in production
- On average “[s]oftware developers … spend approximately 80% of development costs on identifying and correcting defects…”
- It creates opportunities for improved solutions
- Eliminating bugs also reduces opportunity costs

^
- Fixing bugs in prod: expensive because...
  - lost revenue
  - Cost of context switching
  - often results in just getting something out there
- 80% rule
  - Eliminating these bugs ahead of time increases the time devs have to devote
    to new features and improvements
  - It only get worse as a system increases in size
- Improved solutions
  - two heads are better than one
  - A second opinion can provide new solutions
  - May avoid hitting that same code in the future to deal w/ inefficiencies

---

## Encourages a *Healthy Engineering Culture*

![](images/healthy_engineer.jpeg)

---

## How?

- Cross training/knowledge transfer
- Learning
- Increased team awareness
- Sharing ownership

^
- Learning
  - It's hard to stay up to date with changes to the API
  - Everyone knows something you don't
  - There's *always* something to learn from code
- Increased team awareness
  - We see more explicitly what others on the team are doing
  - What's happening in the team as a whole
- Ownership
  - Zoom in on this because a sense of ownership in a project and team plays
    heavily into overall employee engagement in an organization

---

## Sharing *Ownership*

![](images/ownership.png)

^
- Feeling like you have ownership in a project makes you feel like you belong
  and make a difference
- Reference being a freelancer
- Being asked to give a CR is like someone saying, "I respect you. I want your
  honest opinion about my work."
- When you feel ownership in something you want to see it succeed. You have a
  vested interest
- Haven't you had a project where you were occasionally asked to do something,
  but never had any real involvement beyond that? Did you care a/b it?

---

# The *Code Review* Process

![](images/process.jpeg)

---

## First Things First

- Everyone gets code reviewed
- Every PR gets code reviewed
- Every change gets code reviewed

^
- Everyone :: refer to Kyle at 1hq
- Every PR :: This means that no PR should get merged without getting reviewed
  - I recommend to all my clients that PRs must be approved before merge
  - Not doing every PR assumes you know which will have the bugs
- Every Change :: review all the changes, not just what you're comfortable with
  - No assumptions

---

## Submitting a new PR

- Review your own code before you create the PR
- Provide the associated PM ticket
- Provide an articulate description of the changes and why they're needed
- The scope of the change
- Highlight areas which warrant special attention
- Highlight subtleties that need clarification
- Any other details which may help reviewers better understand the patch

^
- reason: May just be a summary of the PM ticket. Gives the reviewer context
- Scope: what is affected. Maybe what isn't

---

## Reviewing a PR

- Know what the problem is
- Take your time
- Review w/i 24 hours
- Request the right people
- Accountability

^
- review the ticket this PR resolves.
- Think about how you would solve it
- 24h: All about that feedback loop
- Accountability: If you approve it, you're accepting responsibility

---

## Things to look for

- Does the code do what it's supposed to do?
- Single Responsibility Principle (SRP)
- Are there tests?
- Is there duplicate code?
- Are things named well?
- Are there potential security issues?
- Are there performance issues?
- YAGNI?
- Is the code left in a better state than it began?

^
- Do it? You can tell from the logic. code reviews aren't QA
- SRP - Does the module, class, function, method have a single purpose?
- tests - reject if no tests
- naming: Is the Class, Module, method, function, variable named well?
- YAGNI: premature optimization
- boy scouts
  - This is part of building an culture of craftmanship

---

# *Giving* Feedback

![](images/giving_feedback.jpeg)

---

## *Giving* Feedback

- Let tools be the pedants
- If it feels nit-picky, it probably is
- Ask questions
- Be explicit in what is "wrong" about the code
- Compliment/reinforce good practices
- Critique the code, not the coder
- It's okay to give it a thumbs up if everything looks good

^
- tools: mix format, eslint, rubocop, etc.
- questions:
  - If there is code you don't understand or need clarification
  - If you don't understand it now, you won't understand it later
- explicitness
  - Provide the reasoning for your comments
  - May require putting some effort into the why
  - It gives context to the receiver
  - It limits back-and-forth (emphasize this). Everyone's short on time, which
    is why you should explain yourself thoroughly.
  - provide a solution if it makes sense to
  - Not an opportunity to play "gotcha"
- Thumbs up: try to find something positive to say about it.

---

# *Receiving* Feedback

![](images/receiving_feedback.jpeg)

---

## *Receiving* Feedback

- It takes humility to accept feedback
- Accept feedback with gratitude
- Assume positive intent

^
- Humility: Growth hurts
  - Don't take it personally
  - Try to understand the review from the reviewer's perspective
- Gratitude:
  - Reviewers are trying to help
  - It's less embarassing to get blamed for a bug here than in production
  - It may be an opportunity to learn somehting new
- positive intent
  - No one wants to insult you
  - We're all focused on the same goal
  - We're all on the same team

---

# *Questions, Comments*, Derogatory Remarks?

![](images/questions.jpg)

---

[.build-lists: false]

## References

- https://blog.alphasmanifesto.com/2016/11/17/how-to-perform-a-good-code-review/
- https://alexgaynor.net/2013/sep/26/effective-code-review/
- https://blog.codinghorror.com/code-reviews-just-do-it/
- https://codeclimate.com/blog/unexpected-outcomes-of-code-reviews/
- https://blog.digitalocean.com/how-to-conduct-effective-code-reviews/
- https://github.com/thoughtbot/guides/blob/master/code-review/README.md
