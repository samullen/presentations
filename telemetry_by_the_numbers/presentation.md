autoscale: true
build-lists: true
theme: Work, 1

# Telemetry: By the Numbers

![](images/launch2.jpg)

^
You've probably heard this quote before

---

> What gets **measured** gets **managed**.

^
- It's a great quote
- It's short, understandable, and oozes common sense
- Example:
  - If we measure how much resources our systems require, we can manage allocation of new servers
  - If we know where a customer gets hung up in the checkout process, we can fix the problem and improve the experience
- It's a great quote; it's just not all of it.

---

> What gets measured gets managed - even when it’s **pointless** to measure and manage it, and even if it **harms** the purpose of the organisation to do so.

^
- And therein lies the rub
- If we're watching the wrong data, we’ll make the wrong decisions.
  - We'll do it with the purest intentions
  - and we'll do it in an effort to _help_ our organization
  - But we'll still be doing it, wasting time, human effort, and resources
- Who here knows what I'm talking about?
  - Who has monitoring set up that no one's paying attention to?
  - What about monitoring that's just wrong?
  - For those of you who didn't raise your hands, which is it: you have it all figured out? or do you just not have monitoring in place?
- Today we'll going to look at how to think about capturing metrics
  - We'll do so by looking at monitoring

---

# Telemetry: By the Numbers

- Monitoring
- Measurements and Metrics
- What makes a good metric
- The Telemetry library

^
- Monitoring: what it is and why it's important
- M&M: What we mean by measurements and metrics
- Good v Bad: What makes a good metric and how to spot bad ones
- Telemetry: A brief summary of how to use Telemetry and Telemetry Metrics
- Now that we have that covered...

---

# Hi!

![](images/samuel.jpg)

^
- For those who don't know me (which is most of you)
- My name is Samuel
- I write the occasional article about Elixir
- And now, I guess, speak at the occasional conference
- And I work here...

---

![left fit](images/ActiveProspect_Full.png)

# What we do

- Capture, filter, and validate sales leads
- Document consumers' consent to be contacted
- Enable companies to comply with regulations (e.g TCPA, CASL, CCPA)
- Improve conversion rates

^
- We're one of the platinum sponsors
- And before you ask: no, that's not how I got this speaking slot
- Bruce and Maggie made that mistake on their own without any financial incentives
- And of course, we're hiring
- let's talk about monitoring

---

# Monitoring

![](images/soundwave.jpg)

^
- Monitoring, as I'm sure you're aware, is watching the state of computer systems
  - May involve watching ...
  - network traffic
  - CPU / Memory load
  - Errors
  - Database performance
  - response times
  - and more
- So why should we monitor?

---

# Why Should We **Monitor**?

- Helps us *understand* our system
- Preemptively *Catch issues*
- Provide *benchmarks* to measure against
- Enable you to *make good decisions*

^
- Understanding:
  - In the beginning, projects are small enough to understand w/o monitoring
    - but it doesn't take long before we realize we have little idea what's going on inside
      - Why is this process taking so much memory?
      - Why is the disk running out of space?
      - What pages are loading slowly?
  - So adding monitoring to the system helps us to understand it better
- Catch issues:
  - When we start monitoring and if we monitor the correct things...
    - Then it helps us catch issues prior to our customers finding them (hopefully)
    - As you can imagine, this is harder than it sounds
- Benchmarking:
  - Monitoring also gives us historical data which we can use to benchmark our changes
    - Otherwise, how do you know that the new algorithm is better than the previous one?
- Decisions:
  - Lastly, and this summarizes them all, it's to help us make good decisions
- If you are monitoring your system just so you can impress people on Twitter with your graphs, you're doing it wrong.

---

# Measurements and Metrics

![](images/devastator.jpg)

---

[.build-lists: false]

# Measurement

A unit-specific value for something

- Count
- Duration
- Quantity
- Current value

^
- By itself, a measurement is almost useless
  - It's a single point on a graph
  - it's data, not information
- A metric on the other hand provides context...

---

[.build-lists: false]

# Metric

Used to track and assess measurements

- Summaries (i.e. roll up)
- Ratios or Rates
- Percentile
- Distribution

---

# What Makes a **Good** Metric?

![](images/autobot.jpg)

---

## A **good** metric is **comparable**

^
- This is probably obvious to you
- what's less obvious is that we need to compare related metrics
- Before and after benchmarks are a good example
- while lines of code or commits by two different developers

---

# A **good** metric is **understandable**

> If people can’t remember [the metric] and discuss it, it’s much harder to turn a change in the data into a change in the culture.
>
> – Alistair Croll _Lean Analytics_

^
- we understand terms like “code complexity”, “technical debt”, and “refactoring”
- Most people - non-developers – don't
- Furthermore, only complexity can be quantified. The others must be compared to an unknowable ideal
- When choosing a metric to track, choose those which can be reasoned about and understood.

---

# A **good** metric is a **ratio** or a **rate**

- Ratios are easier to act on
- Ratios are inherently comparative
- Ratios are good for comparing seemingly opposing factors

^
- Action: It tells you what you need to do to achieve your goals (example: performance)
  - Do you need to continue working to improve, or can you switch gears
- Comparative: Comparing a daily metric over the course of the month enables you to see if something is a trend or just a spike
- Examples of this last one
  - errors per request
  - Signups vs. deactivations
  - Jokes told by presenters vs. audience laughter

---

# A **good** metric **changes behaviour**

The metrics you track must...

- enable you to make good decisions
- be aligned with your goals

^
- Now that we know what makes a metric good, let's look at what to avoid

---

# Lies,
# **Damned** Lies,
# and **Vanity Metrics**

![](images/decepticon.jpg)

^
- If you're not already familiar with vanity metrics...

---

# Lies, Damned Lies, and Vanity Metrics

> Vanity metrics might make you feel good, but they don’t change how you act.

– Alistair Croll, _Lean Analytics_

^
- Examples:
  - Page Views
  - Memory usage
  - Error counts
  - Even orders and sales
- Why is that?
  - Let's look at this last one: sales
  - Let's say your site increased signups by 20%. Is that good or bad?
    - on the surface it appears like a good thing
    - But what questions does it answer?
      - Does it explain WHY conversions increased?
        - Was it from a marketing push, a new UI, placement in the app store?
      - What about how many customers you lost in the same period (i.e. churn)?
      - Are customers using the product/service?
    - Based on sales alone, what decisions does it help you make?
  - By themselves vanity metrics are easy to get excited about, or even afraid of
  - But they don't provide real value: they don't help you make decisions.
  - They're like junk food. It tastes good, but doesn't provide any real value.
- Now that we know about monitoring, measurements, and metrics, let's look at Telemetry and how we can start capturing useful data

---

# Telemetry

![](images/telemetry.jpg)

---

# What is **Telemetry**?

> Telemetry is a dynamic dispatching library for metrics and instrumentations.
>
> – The Telemetry Docs

^
- Telemetry's a library which provides a simple, unified interface for capturing measurements from monitored events.

---

# Three steps to adding Telemetry

1. *Attach* the Telemetry Supervisor
2. *Handle* Telemetry Events
3. *Executing* on Events

^
- The idea in showing you the code isn't so you'll remember it, but to show you the basic steps, and how simple it is

---

# Attach the **Telemetry Supervisor**

```elixir
defmodule MyApp.Application do
  use Application

  def start(_type, _args) do
    children = [ ... ]

    events = [
      [:web, :request, :start],
      [:web, :request, :success],
      [:web, :request, :failure],
    ]

    # Start the Telemetry supervisor
    :telemetry.attach_many("handler-id", events, &handle_event/4, nil)

    opts = [strategy: :one_for_one, name: MyApp.Supervisor]
    Supervisor.start_link(children, opts)
  end
end
```

^
- Handler_id examples: app-reporter, instrumenter, monitorer
  - Useful to avoid conflicts with 3rd party libraries

---

# **Handle** Telemetry Events

```elixir
def handle_event(
  [:web, :request, :success],
  %{measurement_name: measurement},
  %{metadata_name: metadata},
  _config) do

  # Logic to handle event

end
```

^
- You'll create a handler with...
  - an ID (ex. app_name, report, subsecion, run to get an avg of how long it takes to display each section of a report)
    - and help you find out what the slow queries are
  - a measurement
    - examples: duration, count, current value, some number, etc.
  - Maybe some metadata
    - Ecto shows the query, the params provided, table name, and results
    - While Plug shows the %Conn{} struct
  - Maybe some configuration (but probably not)
- You'll do this for each event you want to monitor
- If that sounds like it can get hairy really quickly, you're right
- Which is why we'll look at ways to simplify gathering data in a moment

---

# **Executing** on Events

```elixir
:telemetry.execute(
  [:web, :request, :success],
  %{time_in_milliseconds: 42},
  %{
    request_path: conn.request_path,
    status_code: conn.status
  }
)
```

---

# Don't **Reinvent** the Wheel

- Plug
- Ecto
- Absinthe
- Broadway
- Others

---

# Telemetry.Metrics

![](images/shockwave.jpg)

^
- Now that I've shown you how to set up Telemetry to handle events
- Your first thoughts might be:
  - "how do I organize these?"
- The Telemetry team has you covered on this with Telemetry.Metrics

---

# What is **Telemetry.Metrics**

> Common interface for defining metrics based on `:telemetry` events.
> – The Telemetry.Metrics docs

^
- Telemetry Metrics is just a spec
- It isn't a data accumulator
- it provides and validates the different metric types
- it does offer unit conversions for time-based measurements
- It makes sense: how could the Telemetry team know the different places we pull data from and the many ways we would want to aggregate data?
- Reporters is what they need to focus on

---

# **Telemetry.Metrics** Types

- **Telemetry.Metrics.Counter**: Keep a running count of events
- **Telemetry.Metrics.Sum**: Track the sum of specific measurements
- **Telemetry.Metrics.LastValue**: Hold the most recent event's measurement
- **Telemetry.Metrics.Summary**: Track statistics of the selected measurement
- **Telemetry.Metrics.Distribution**: Group measurements into buckets

^
- Counter: n/a
- Sum: Example - total time of all requests
- LastValue: n/a
- Summary: ex: min/max, averages, percentiles
- Distribution: n/a

---

# **Telemetry.Metrics** Reporters

![](images/reporter.jpg)

^
- Handle data accumulation
- You just define the event names
- And execute on them or configure a telemetry enabled library to

---

```elixir
import Telemetry.Metrics

metrics = [
  counter("myapp.plug.stop.duration"),

  sum("myapp.plug.stop.duration"),

  last_value("myapp.plug.stop.duration", unit: {:native, :millisecond}),

  summary("myapp.plug.stop.duration", unit: {:native, :millisecond}),

  distribution("myapp.plug.stop.duration",
    buckets: [200, 500, 1000], unit: {:native, :millisecond}),
]

children = [

  {TelemetryMetricsStatsd, metrics: metrics}

]
```

^
- Notice this is very similar to how we attached events in Telemetry
  - We have a list of metrics we're watching
  - Instead of using an `event_handler`s to handle events, we're using a reporter (either one we create, or a 3rd party lib)
  - All you have to do is define which events you want to capture, and execute on them
- You can make your own reporters, but that's really a presentation in itself

---

# Takeaways

- You **need** to be *monitoring* your system
- Monitor *good* metrics
  - comparable
  - understandable
  - ratio or a rate
  - changes behaviour
- Use *Telemetry* and *Telemetry.Metrics*

^
- So what do I want you to take away from this?
- Monitoring
- Good metrics
  - While we didn't talk about specifics of WHAT to monitor,
  - We did look at how to choose good metrics to monitor

---

[.build-lists: false]

# Thank You!

*Samuel Mullen*

- @samullen
- samuelmullen.com

![right](images/thanks.jpg)

^
- Maggie and Bruce for making Lone Star Elixir possible
- If you have questions, you can find me in the hallway or online
