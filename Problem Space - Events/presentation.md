# Problem Space

## Calendar Events

![](http://cdn.webfail.com/upl/img/522fe0c4168/post2.jpg)

---

# Setup

---

# Setup

* Task / Todo feature
	* It can have a reminder set to trigger prior to the due time
	* Tasks are not currently repeatable

---

# Setup

* Task / Todo feature
	* It can have a reminder set to trigger prior to the due time
	* Tasks are not currently repeatable
* Daily emails list tasks for the day
	* If the user wants alerts based on the reminder, they need to export to an external application 

---

# Problem

---

# Problem

* Send notifications according to the reminder time

---

# Problem

* Send notifications according to the reminder time
* Add repeating tasks
	* Would only need to be monthly, weekly, or daily

---

# Concerns

---

# Concerns

* The events have to fire

---

# Concerns

* The events have to fire
* If they don't fire, I need to know when it fails and why

---

# Concerns

* The events have to fire
* If they don't fire, I need to know when it fails and why
* If the alert is set for two minutes from now, it still must fire

---

# Potential Solutions

---

# Potential Solutions
## non-repeating tasks

* Post the task to a delayed job sort of service on creation
	* If the table gets deleted (e.g. Redis), it has to get reloaded

---

# Potential Solutions
## non-repeating tasks

* Post the task to a delayed job sort of service on creation
	* If the table gets deleted (e.g. Redis), it has to get reloaded
* Run cron every 5-10 minutes to post to the delayed job daemon

---

# Potential Solutions
## repeating tasks

* Brute force - Generate the repeating tasks out to a specific date via cron

---

# Potential Solutions
## repeating tasks

* Brute force - Generate the repeating tasks out to a specific date via cron
* Algorithm - I have no idea what this looks like

---

# Thoughts?

* What are some solutions?
* What are things I'm not thinking about?
* What have you done in the past?

---

# Thank you!
