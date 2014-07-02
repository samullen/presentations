# Going Further in ActiveRecord 
# with Arel

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

---

## Reliable

---

### What's wrong with this?

``` ruby
Post.joins(:comments).where("updated_at > ?", 1.year.ago)
```

^ I'll give you and few moments

---

## Reliable

^ * Column names are used across tables. (id, created\_at, and updated\_at)
* unless they are properly qualified, it could result in an “ambiguous column reference” error. 
* Arel mitigates this possibility.
* catches many instances where Rails aliases tables

---

## Simple

^ * The object oriented nature of Arel allows queries to be more easily broken up into smaller more manageable chunks.
* Clear delineation for joins, unions, and constraints

---

## Flexible

^ * It is easier and more consistent to build up your query using Arel methods rather than using string interpolation and concatenation.

---

# Arel Foundations

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

Add this private class method to access the `arel_table` more conveniently

```ruby
def self.post
  self.arel_table
end
# Force the class method to be private (optional)
private_class_method :post 
```

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
* Primary usage:
  * Specify fields to act upon
  * Add conditions to your query

---

## Retrieving an Arel::Attribute

Access a table's attribute by passing its symbol to the Arel table

``` ruby
post[:updated_at] # i.e. Post.arel_table[:updated_at]

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

post[:title].matches("Lorem ipsum%").to_sql
# => comments.flagged ILIKE 'Lorem ipsum%'

post[:created_at].lt(post[:updated_at).to_sql
# => posts.created_at < posts.updated_at

Post.where(post[:published_at].lt(2.weeks.ago)
# => SELECT "posts".* 
#      FROM posts 
#     WHERE posts.published_at < '2014-06-09 09:56:14.101954'

```

---

## Combining Predicates
### OR and AND

``` ruby
post[:published_at].gt(1.year.ago).or(
  post[:created_at].lt(post[:updated_at)).to_sql

# => posts.published_at > '2013-06-23 09:56:14.101954' 
#      OR posts.created_at < posts.updated_at

post[:created_at].gt(1.year.ago).and(
  post[:updated_at].gt(1.week.ago)).to_sql

# => posts.created_at > '2013-06-23 09:56:14.101954' 
#      OR posts.updated_at > '2014-06-16 09:56:14.101999'

```
---

# Named Functions

^ * database specific function calls
^ * Generally used with the #select method

---

## Syntax

``` ruby
Arel::Nodes::NamedFunction.new(<function>, <Array>, [alias])

# Example

Arel::Nodes::NamedFunction.new(
  "md5", [Post.arel_table[:title]]
)
 # => md5(posts.title)

Arel::Nodes::NamedFunction.new(
  "md5", [Post.arel_table[:title]], "md5_title"
)
 # => md5(posts.title) AS md5_title

```

--- 

## Named Functions
### Example

``` ruby
Post.select(
  post[Arel.star],
  Arel::Nodes::NamedFunction.new("md5", [post[:title], "md5_title")
)
```
---

# Unions

^ Used to combine or intersect two sets of data
^ completed in two steps: defining the union, and executing it
^ usually done from two different tables, but with the same column types

---

## Defining the Union

``` ruby
new_and_updated = Post.where(:published_at => nil).
          union(Post.where(:draft => true))
```
^ get all unpublished and all drafts
^ You would normally use an "OR" here 

---

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

^ Documentation and examples are getting better
^ using the convenience method helps readability a lot
^ Especially useful when combined with Query Objects
^ See CodeClimate's "7 Patterns to Refactor Fat ActiveRecord Models"

---

# Contact

t: samullen
g: samullen
w: samuelmullen.com
