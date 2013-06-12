!SLIDE

# The Problem with Rails Callbacks #

## Samuel Mullen

!SLIDE

# I Hate Callbacks #

!SLIDE

# Stuff Like This #
	@@@ ruby small
	class Unit < ActiveRecord::Base
	  ...
	  after_update do
	    if self.initiated?
	      notify_admin if self.admin.emails.first.email.present?
	    elsif self.processing?
	      notify_recipients if self.recipients.all? {|r| r.present?}
	    elsif self.complete?
	      notify_admin if self.admin.emails.first.email.present?
	      notify_recipients if self.recipients.all? {|r| r.present?}
	    end
	  end
	  ...
	end

!SLIDE bullets incremental

# There's Worse #

* That one's completely fictional
* There's production code like that
* There are callbacks which trigger callbacks
* I'm responsible for some of it

!SLIDE

# Can We Get A Definition? #

![JoshSusser](images/josh_susser.jpg)

!SLIDE

## "Callbacks are hooks into the life cycle of an ActiveRecord object that allow you to trigger logic before or after an alteration  of the object state."  
##  -- Rails API

!SLIDE bullets

# What that Means #

* You can trigger actions `before`, `after`, or `around`
  * Validations
  * Saves - Updates and Creates
  * Commits
* Also `after`
  * Find and Initialize

!SLIDE

# Callbacks are<br/>"The Rails Way"&#x2122; #

!SLIDE center

# Where Trouble Begins #

![The Trouble with Tribbles](images/tribble_trouble.jpg)

!SLIDE

# In the Tests #

!SLIDE 

# Pretend Code to Make a Point #

	@@@ ruby medium
	class Post < ActiveRecord::Base
	  belongs_to :author
	  after_create :notify_followers

	  def publish!
	    self.published_at = Time.now
	    save
	  end

	  private

	  def notify_followers
	    Notifier.post_mailer(self).deliver
	  end
	end

!SLIDE 

# Pretend Code Cont'd #

	@@@ ruby medium
	describe "publishing the article" do
	  it "saves with published_at defined" do
	    Post.any_instance.stub(:notify_followers)

	    post = Post.new(:title => "I Like Tribbles")
	    post.publish!

	    expect(post).to be_published
	  end
	end

!SLIDE bullets incremental
.notes you'll have to monkeypatch the class if you're using minitest

# The Problem #

* Callbacks run every time their condition is met
* Stubs must be added in order for tests to pass
* We have to do it every time callbacks would fire
* Outside of tests, how do you avoid the callback?

!SLIDE

# The Real Problem #

!SLIDE
.notes in our example we have two: database changes, email changes

# Single Responsibility Principle (SRP)

## There should never be more than one reason for a class to change

!SLIDE

# Solutions? #

![What Would MacGyver Do?](images/wwmd.jpg)

!SLIDE bullets

# Observers #

## The most commonly chosen solution

!SLIDE 

# Observers: Before #

	@@@ ruby medium
	class Comment < ActiveRecord::Base
	  belongs_to :user
	  belongs_to :post

	  after_create :notify_author

	  private

	  def notify_author
	    Notifier.inform_author(comment).deliver
	  end
	end

!SLIDE

# Observers: After #

	@@@ ruby medium
	class Comment < ActiveRecord::Base
	  belongs_to :user
	  belongs_to :post
	end

	class CommentObserver < ActiveModel::Observer
	  def after_create(comment)
	    Notifier.inform_author(comment).deliver
	  end
	end

!SLIDE 

# Observers: Tests #

	@@@ ruby
	describe Comment do
	  it "needs to create the comment" do
	    Comment.observers.disable :all

	    # ... rest of the test
	  end
	end

!SLIDE bullets incremental

# Problems #
## Just Like Callbacks

* Observers run every time their condition is met
* Stubs must be added in order for tests to pass
* We have to do it every time observers would fire
* We're really just hiding the problem

!SLIDE bullets incremental
.notes reminder about the purpose of AR models

# When do Callbacks Cause the Most Problems? #

* `_before` callbacks are primarily used to prepare an object to be saved
* `_after` callbacks are responsible for what happens outside of persisting the
  data
  * i.e. Single Responsibility Principle violation
* Callbacks (and Observers) are implicit

!SLIDE bullets incremental
.notes Jonathan Wallace from the Big Nerd Ranch

# Rule #

## Use a callback only when the logic refers to state internal to the object.

* i.e. when it doesn't violate SRP

!SLIDE

# Solution #

!SLIDE

# Service Objects #
## i.e. Plain Old Ruby Objects (PORO)

![PORO](images/poro.png)
!SLIDE

# Example: Before #

!SLIDE

# The Model #

	@@@ ruby medium
	class Order < ActiveRecord::Base
	  belongs_to :user
	  has_many :line_items
	  has_many :products, :through => :line_items

	  after_create :purchase_completion_notification

	  private

	  def purchase_completion_notification
	    Notifier.purchase_notifier(self).deliver
	  end
	end

!SLIDE

# The Mailer #

	@@@ ruby
	class Notifier < ActionMailer::Base
	  def purchase_notifier(order)
	    @order = order
	    @user = order.user
	    @products = order.products
	
	    # rest of the action mailer logic
	  end
	end

!SLIDE

# Example: After #

!SLIDE 

# The Model #

	@@@ ruby medium
	class Order < ActiveRecord::Base
	  belongs_to :user
	  has_many :line_items
	  has_many :products, :through => :line_items
	end

!SLIDE

# The Service Object #

	@@@ ruby medium
	class OrderCompletion
	  attr_accessor :order

	  def initialize(order)
	    @order = order
	  end

	  def create
	    if self.order.save
	      Notifier.purchase_notifier(self.order).deliver
	    end
	  end
	end

!SLIDE

# In The Controller #

	@@@ ruby medium
	def create
	  @order = Order.new(params[:order])
	  order_completion = OrderCompletion.new(@order)

	  if order_completion.create
	    redirect_to root_path, 
	      notice: 'Your order is being processed.'
	  else
	    render action: "new"
	  end
	end

!SLIDE bullets incremental

# What This Gets Us #

* No longer dependent upon stubbing out methods in our tests
* No longer violating SRP
* Explicit, rather than implicit, Logic
  * We call what we want, when we want
* Our classes are decoupled, therefore easier to understand and maintain

!SLIDE

# Wrapping Up #

!SLIDE

# Callbacks are Evil #

!SLIDE

# Don't Be This Guy #

![Doof](images/doof.jpg)

!SLIDE

# Questions? #
