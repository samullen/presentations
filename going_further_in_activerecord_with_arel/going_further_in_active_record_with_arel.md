# Going Further in ActiveRecord 
# with Arel

---

![fit](nomemes.jpg)

--- 

# In the Beginning

^ We managed the DB connections
^ handcrafted all the SQL
^ Lots of duplicated code
^ Dynamically constructing queries was hard
^ Had to create tables yourself
  ^ Remember to do it in production

---

# Along Came Rails

^ With ActiveRecord, everything just worked
^ Migrations kept environments in sync
^ Our code DRYed up
^ Dynamic queries are now just part and parcel

---

# The Abstraction Leaks

^ define leaky abstraction

^ For really complex queries, we have to drop down to SQL
^ You know, like if a query has an OR in it
^ or a function call

---

# Arel

^ With arel, we can plug some of those holes

---

## An Abstract Syntax Tree (AST)

It’s the “engine” ActiveRecord uses to change method calls into actual SQL.

1. Simplif[ying] the generation of complex SQL queries
2. Adapt to various RDBMSes

---

## In Other Words

It can change this

```ruby
Post.joins(:comments).
  where(:published => true).
  where(:comments => {:flagged => true})
```

Into this

```sql
SELECT "posts".*
  FROM "posts" 
       INNER JOIN "comments" ON "posts"."id" = "comments"."post_id"
 WHERE "posts"."published" = true
   AND "comments"."flagged" = true
```

---

# Why Should I Use Arel?

* Reliable

---

## What's wrong with this?

``` ruby
Post.joins(:comments).
  where("updated_at > ?", 1.year.ago)
```

^ I'll give you and few moments

---

# Why Should I Use Arel?

* Reliable

^ * Column names are used across tables. (id, created\_at, and updated\_at)
* unless they are properly qualified, it could result in an “ambiguous column reference” error. 
* Arel mitigates this possibility.
* catches many instances where Rails aliases tables

---

# Why Should I Use Arel?

* Reliable
* Simple

^ * The object oriented nature of Arel allows queries to be more easily broken up into smaller more manageable chunks.
* Clear delineation for joins, unions, and constraints

---

# Why Should I Use Arel?

* Reliable
* Simple
* Flexible

^ * It is easier and more consistent to build up your query using Arel methods rather than using string interpolation and concatenation.

---

# Arel Foundations

---

## Arel::Table

* The primary class you will act upon and through

---

## Arel::Table

* The primary class you will act upon and through
* Represents a database table 

---

## Retreiving the Arel::Table

* Retrieve the instance with `::arel_table`

``` ruby
Post.arel_table

=> #<Arel::Table:0x007fdd17f44ad8
 @aliases=[],
 @columns=nil,
 @engine=
  Post(id: integer, user_id: integer, title: string, content: text, 
       created_at: datetime, updated_at: datetime),
 @name="posts",
 @primary_key=nil,
 @table_alias=nil>
```

---

## Convenience Method

```ruby
def self.post
  self.arel_table
end
# Force the class method to be private (optional)
private_class_method :post 
```

^ Add methods like this to your models to access the arel\_table more easily

---

### Allows us to write

```ruby
post[:updated_at]

# instead of...

Post.arel_table[:updated_at]
```

--- 

## Arel Attribute

* Represents a field within a table

---

## Arel Attribute

* Represents a field within a table
* Primary usage:
  * Specify fields to act upon

---

## Arel Attribute

* Represents a field within a table
* Primary usage:
  * Specify fields to act upon
  * Add conditions to your query

---

## Retrieving an Arel::Attribute

Access a table's attribute by passing its symbol to the Arel table

``` ruby
Post.arel_table[:updated_at] # convenience: post[:updated_at]

=> #<struct Arel::Attributes::Attribute
 relation=
  #<Arel::Table:0x007fdd17f44ad8
   @aliases=[],
   @columns=nil,
   @engine=
    Post(id: integer, user_id: integer, title: string, content: text, 
         created_at: datetime, updated_at: datetime),
   @name="posts",
   @primary_key=nil,
   @table_alias=nil>,
 name=:name>
```

---

# Predicates 
## i.e. Constraints

---

## Frequently Used Predicates

https://github.com/rails/arel/blob/master/lib/arel/predications.rb

* `#eq`, `#not_eq`
* `#in`, `#not_in`
* `#matches`
* `#lt`, `#lteq`
* `#gt`, `#gteq`

---

## Examples

^ use #to\_sql to see the constraint which is being built

``` ruby
post[:published_at].gt(1.year.ago).to_sql
# => posts.published_at > '2013-06-23 09:56:14.101954'

post[:title].matches("Lorem ipsum%")
# => posts.title ILIKE 'Lorem ipsum%'

post[:created_at].lt(post[:updated_at])
# => posts.created_at < posts.updated_at
```

---

## Examples (cont'd)

``` ruby
Post.where(post[:published_at].lt(2.weeks.ago))
# => SELECT "posts".* 
#      FROM posts 
#     WHERE posts.published_at < '2014-06-09 09:56:14.101954'

```

---

## Combining Predicates
### OR and AND

---

### Examples

``` ruby
post[:published_at].gt(1.year.ago).or(
  post[:created_at].lt(post[:updated_at))

# => posts.published_at > '2013-06-23 09:56:14.101954' 
#      OR posts.created_at < posts.updated_at

post[:created_at].gt(1.year.ago).and(
  post[:updated_at].gt(1.week.ago))

# => posts.created_at > '2013-06-23 09:56:14.101954' 
#      AND posts.updated_at > '2014-06-16 09:56:14.101999'

```
---

# Named Functions

^ * database specific function calls
* Generally used with the #select method

---

## Syntax

``` ruby
Arel::Nodes::NamedFunction.new(<function>, <Array>, [alias])
```

---

## Example

``` ruby
Arel::Nodes::NamedFunction.new(
  "md5", [post[:title]]
)
 # => md5(posts.title)

Arel::Nodes::NamedFunction.new(
  "md5", [post[:title]], "md5_title"
)
 # => md5(posts.title) AS md5_title

```

--- 

## Example (cont'd)

``` ruby
posts = Post.select(
  post[Arel.star],
  Arel::Nodes::NamedFunction.new("md5", [post[:title], "md5_title")
)

# SELECT posts.*, md5(posts.title) AS md5_title
#   FROM posts

posts.first.md5_title

# => "2260b669d1c515bc489704f350fe023f"

```

^ Now we can access md5\_title as if it were an attribute

---

# Unions

^ ^ Used to combine or intersect two sets of data
^ completed in two steps: defining the union, and executing it
^ usually done from two different tables, but with the same column types

---

## Defining the Union

``` ruby
new_and_updated = Post.where(:published_at => nil).
          union(Post.where(:draft => true))
```
^ ^ get all unpublished and all drafts
^ You would normally use an "OR" here 

---

## Defining the Union

``` ruby
new_and_updated = Post.where(:published_at => nil).
          union(Post.where(:draft => true))
```

## Executing the Union

``` ruby
Post.from(post.create_table_alias(new_and_updated, :posts))
```

---

## Other Union Features

**UNION ALL**

``` ruby
Post.where(:published_at => nil).
  union(:all, Post.where(:draft => true))
```

**INTERSECT**

``` ruby
Post.where(:published_at => nil).
  intersect(Post.where(:draft => true))
```

---

# Joins

^ Last topic
^ Performed in three steps: definition, construction, execution

--- 

## Defining Joins

``` ruby
author = Author.arel_table
post = Post.arel_table
 
constraints = post.create_on(
  post[:author_id].eq(author[:id])
)
```

^ Can chain multiple conditions together using AND and OR

---

## Constructing Joins

``` ruby
# Inner join
join = post.create_join(author, constraints)
 
# Outer join for anonymous posts
join = post.create_join(author, constraints, Arel::Nodes::OuterJoin)
```

---

## Executing Joins

``` ruby
Post.joins(join)

# Joining multiple tables)
Post.joins(author_join, comment_join, ...)
```

--- 

# Final Thoughts

^ I wanted to cover the 90% of what we use
^ Docs and examples are getting better
^ use the convenience method
^ Especially useful with Query Objects
^ See CodeClimate's "7 Patterns to Refactor Fat ActiveRecord Models"

---

![fit](ilie.jpg)

---

# Contact

t: samullen
g: samullen
w: samuelmullen.com
p: github.com/samullen/presentations
