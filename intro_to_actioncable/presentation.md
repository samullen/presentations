build-lists: true
autoscale: true

# **Introduction** to *ActionCable* in **Rails 5**

---

# Hi!

![original](images/avatar.jpg)

^ I'm Samuel

^ I own Pixelated, a rails consultancy here in KC

---

# **ActionCable**

* **Was**: originally controversial
* **Now**: the next step in Rails' evolution

^ A lot of you were there when they released Rails 5 this past spring

^ New improvements: AdequateRecord, Rails Api

^ Controversial: ApplicationRecord

---

# What is **ActionCable**?

### A framework for using *WebSockets* in your Rails applications

“It allows for real-time features to be written in Ruby in the same style and form as the rest of your Rails application, while still being performant and scalable.” – Rails Guides

^ This "framework" includes 

^ The means to connect

^ The means to publish (written in Ruby) 

^ and a means to subscribers (written in JS)

---

# **Websockets**

### In the *Olden Days*

* Browser visits a page
* The server returns the necessary HTML, CSS, JS, and other assets
* The browser combines all the components and displays the output
* The end

^ Before websockets

---

# **Websockets**

### In the *Nowen Days*

* Browser visits a page
* The server returns the necessary HTML, CSS, JS, and other assets
* The browser combines all the components and displays the output
* The connection remains open between the browser and the server
* The client **and** server continue to interact along the existing connection

^ Benefits: more performant because the connect is already there. Smaller payloads between pub & sub (minimal headers). Two way communication.

^ cons: That connection has to remain open

^ Now, with the inclusion of the websocket protocol, a webpage can continue to both send and receive data along a "channel" which remains open between the browser and the web server. This open channel allows the server to respond more quickly to the client, but more importantly, it allows the client (i.e. browser) to receive data without the need for refreshing the page.

^ As I was researching this, I found a lot of chat room examples, but..

--- 

# **More** than just *chat rooms*

Drawing on our understanding of chatrooms ...

* Helpdesk chats
* Live comments for blogs and discussion sites 
* Seeing live updates from social media (likes, images, reposts)
* Stock tickers, live poll results, notifications, log feeds, etc.

^ If you *ever* research real-time anything, 99% of the examples are chatroom

--- 

# **ActionCable** Lifecycle


1. A user loads an ActionCable enabled page
2. The consumer (i.e. browser) informs the server it can take connections via `/app/assets/javascripts/cable.js`
3. The server determines authentication of the consumer: `/app/channels/application_cable/connection.rb`
4. A “connection” (i.e. cable) is established when the consumer subscribes to any of the available channels: `/app/assets/javascripts/cable/subscriptions/*.js`
5. Data is broadcast through “channels” (`/app/channels/*Channel.rb`) within the connection to the appropriate subscribers
6. Data is received on those channels for specific subscribers (see 4.) and an action is triggered.
7. Finally, the connection is closed when the consumer is refreshed, changes location, or the tab or window is closed.

---

# The *Lifecycle* as summaried by **Nate Berkopec**


* A “Cable” or “Connection”, a single WebSocket connection from client to server. It’s worthwhile to note that Action Cable assumes you will only have one WebSocket connection, and you’ll send all the data from your application along different…
* “Channels” - basically subdivisions of the “Cable”. A single “Cable” connection has many “Channels”.
* A “Broadcaster” - Action Cable provides its own server. Yes, you’re going to be running another server process now. Essentially, the Action Cable server just uses Redis’ pubsub functions to keep track of what’s been broadcasted on what cable and to whom.

---

# Example Time
## **Pollish**

The three features we want to implement

1. **Authentication** - only signed in users see the results
2. **Voting** - Everyone can vote, but only once
3. **Results** - Results page shows what's in the DB + new results coming in

^ Live updating polls

^ Not showing tests

^ Explain why I created Pollish in the first place

---

# **Polls**
## Controller :: PollsController

```ruby
# /app/controllers/polls_controller.rb

class PollsController < ApplicationController
  def index
    @polls = Poll.all
  end

  def show
    @poll = Poll.find(params[:id])
    @vote = @poll.votes.build

    if session[@poll.code].present?
      redirect_to polls_path, notice: "Don't ruin the fun, You've already voted."
    end
  end
end
```

^ Assume you know how to set up authenticatoin with Devise or some other solution.

^ Creating the Polls resource

^ guards against voting more than once

---

# **Polls**
## Views :: index.html.erb

```erb
<ul>
  <% @polls.each do |poll| %>
    <li><%= link_to poll.name, poll %>
    
    <% if user_signed_in? %>
      [<%= link_to "results", poll_results_path(poll) %>]
    <% end %>
    </li>
  <% end %>
</ul>
```

---

# **Polls**
## Views :: show.html.erb

```erb
<h1><%= @poll.name %> <small>:: <%= @poll.code %></small></h1>

<%= render partial: @poll.code %>
```

^ renders the partial named after the poll's code value from the DB

^ Yes, this is a bad idea

---

# **Polls**
## Views :: \_example.html.erb

```erb
<p><%= @poll.description %></p>

<%= form_for [@poll, @vote] do |f| %>
  <%= f.radio_button :value, "yes" %>
  <%= f.label :value, "Yes", value: "yes" %>
  <br/>

  <%= f.radio_button :value, "no" %>
  <%= f.label :value, "No", value: "no" %>
  <br/>

  <%= f.radio_button :value, "maybe" %>
  <%= f.label :value, "Maybe", value: "maybe" %>
  <br/>

  <%= f.submit %>
<% end %>
```

^ Voting sends you to votes/create

---

# **Votes**
## Controller :: VotesController

```ruby
class VotesController < ApplicationController
  def create
    @poll = Poll.find(params[:poll_id])
    @vote = @poll.votes.build(vote_params)

    if @vote.save
      session[@poll.code] = true
      redirect_to polls_path, notice: "Thanks for your vote!"
    else
      flash.now[:alert] = "Are you sure you pressed the button correctly?"
      render partial: "polls/show"
    end
  end

  private

  def vote_params
    params.require(:vote).permit(:value)
  end
end
```

^ Sets the session to keep from voting more than once

^ Redirects back to polls/index

---

# **Results**
## Controller :: ResultsController

```ruby
class ResultsController < ApplicationController
  before_action :authenticate_user!

  def index
    @poll = Poll.includes(:votes).find(params[:poll_id])
  end
end
```

^ Straightforward. getting the poll and including the votes for counts later on

---

# **Results**
## Views :: index.html.erb

```erb
<#= index.html.erb %>

<h1><%= @poll.name %> <small>:: <%= @poll.code %></small></h1>

<h2>Results</h2>

<div style="width: 600px; height: 400px">
<canvas id="chart" width="600" height="400"></canvas>
</div>

<%= render partial: @poll.code %>
```

^ Nothing to see here except the canvas

---

# **Results** :: \_example.html.erb

```js
var ctx = document.getElementById("chart");
var myChart = new Chart(ctx, {
  type: 'bar',
  data: {
    labels: ["Yes", "No", "Maybe"],
    datasets: [{
      label: '# of Votes',
      data: [
        <%= @poll.votes.find_all {|v| v.value == "yes"}.size %>,
        <%= @poll.votes.find_all {|v| v.value == "no"}.size %>,
        <%= @poll.votes.find_all {|v| v.value == "maybe"}.size %>
      ],
      backgroundColor: [
        'rgba(255, 99, 132, 0.2)',
        'rgba(54, 162, 235, 0.2)',
        'rgba(255, 206, 86, 0.2)',
      ],
      borderColor: [
        'rgba(255,99,132,1)',
        'rgba(54, 162, 235, 1)',
        'rgba(255, 206, 86, 1)',
      ],
      borderWidth: 1
    }]
  },
  options: {
    scales: {
      yAxes: [{
        ticks: {
          beginAtZero:true
        }
      }]
    }
  }
});
```

^ Point out where the votes are being counted

---

# The End?

^ We have a working app

---

# Connecting **ActionCable**

---

## Creating the *Consumer*

```javascript
// /app/assets/javascripts/cable.js

(function() {
  this.App || (this.App = {});

  App.cable = ActionCable.createConsumer();

}).call(this);
```

^ From step 2 of the lifecycle

^ This block is responsible for initiating the websocket connection

^ defaults to `/cable`

^ If you wanted to point it at a different endpoint, it would look like this.

---

## Creating the *Consumer* (cont'd)

```javascript
// /app/assets/javascripts/cable.js

(function() {
  this.App || (this.App = {});

  App.cable = ActionCable.createConsumer("ws://cable.pollish.com");

}).call(this);
```

^ Now we need a place for our consumer to connect

^ we do that in the connection.rb file

---

## Providing the *Connection*

```ruby
# app/channels/application_cable/connection.rb

module ApplicationCable
  class Connection < ActionCable::Connection::Base
    identified_by :current_user

    def connect
      self.current_user = find_verified_user
    end

    protected

    def find_verified_user
      if verified_user = env['warden'].user
        verified_user
      else
        reject_unauthorized_connection
      end
    end
  end
end
```

^ “a connection identifier that can be used to find the specific connection later.“

^ In this example, we're using the "warden" environment variable

^ With our connect established, the ActionCable server is able send data across
the correct channel to the correct users

---

## Creating the *Subscriber*

```javascript
App.cable.subscriptions.create(
  { channel: "PollingChannel", code: "example" },

  {
    connected: function() { console.log("connected"); },

    disconnected: function() { console.log("disconnected"); },

    rejected: function() { console.log("rejected"); },

    received: function(data) {
      myChart.data.datasets[0].data = [
        _.find(data, function(vote) { 
          return vote.value == "yes";
        }).vote_count,
        _.find(data, function(vote) { 
          return vote.value == "no";
        }).vote_count,
        _.find(data, function(vote) { 
          return vote.value == "maybe";
        }).vote_count
      ];
      myChart.update();
    }
  }
);
```

^ /app/assests/javascripts/cable/subscriptions/polling.js

^ two pieces: The args we send the server, what we do with that response

^ "received" is where  we get counts for the votes, and update the chart

^ The last piece of the equation is the Polling Channel

---

## The *PollingChannel*

```ruby
class PollingChannel < ApplicationCable::Channel
  def subscribed
    poll = Poll.where(code: params[:code]).first
    stream_for poll
  end
end
```

^ From the RailsGuide: "If you have a stream that is related to a model, then the broadcasting used can be generated from the model and channel."

^ The stream is now open for thsi poll, now we just need to broadcast

^ Brings us back to the VotesController

---

## *VotesController* Revisted

```ruby
# /app/controllers/votes_controller.rb

class VotesController < ApplicationController

...

    if @vote.save
      PollingChannel.broadcast_to(@poll, @poll.results)
      session[@poll.code] = true
      redirect_to polls_path, notice: message
    else

...
end

```

---

# **Pollish** Demo

---

# Rake and Console

```yaml
# /config/cable.yml

development:
  adapter: redis
  url: redis://localhost:6379/1

test:
  adapter: async

production:
  adapter: redis
  url: redis://localhost:6379/1
```

^ in dev by default, neither Rake nor the console interact with ActionCable

^ to fix that (if you need to), you need to change the adapter to redis

---

# Should you use **ActionCable**?

* Not every app benefits from real-time-data
* Every new feature is added complexity
* Not every website can afford the strain
* Alternatives may be a better fit

^ No. Just kidding.

^ Do you really need real-time, or are you just playing with the new toy?

^ Can your project afford the added complexity? There is a lot going on with ActionCable, does your project have the resources to afford it?

^ Can your site and server afford the added load?

^ polling, long polling

^ It's not perfect yet. Just like AR wasn't perfect when it was introduced, but
it's the future, and it's going to continue to get better.

---

# Questions?

---

# Useless Information

@samullen
samuel@pixelatedworks.com
http://pixelatedworks.com

--- 

# **Pinsight** is hiring

