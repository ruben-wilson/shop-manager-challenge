# ITEMS Model and Repository Classes Design Recipe

## 1. Design and create the Table


```

Table: ITEMS
| Record                | Properties          |
| --------------------- | ------------------  |
| orders                 | price, item_name, quantity
| items                  customer_name, orderdate, 'item_key'
```

## 2. Create Test SQL seeds

Your tests will depend on data stored in PostgreSQL to run.

If seed data is provided (or you already created it), you can skip this step.

```sql

TRUNCATE TABLE items, items  RESTART IDENTITY;

INSERT INTO items (item_name, price, quantity) 
VALUES ('GOLD WATCH', 3350, 5);
INSERT INTO items (customer_name, order_date, item_key) VALUES ('Anna', 'May 2022', 1);
```

```bash
psql -h 127.0.0.1 shop-manager_test < seeds_items_orders.sql
```

## 3. Define the class names

Usually, the Model class name will be the capitalised table name (single instead of plural). The same name is then suffixed by `Repository` for the Repository class name.

```ruby
# EXAMPLE
# Table name: items

# Model class
# (in lib/order.rb)
class item
end

# Repository class
# (in lib/items_Repository.rb)
class itemsRepository
end
```

## 4. Implement the Model class

Define the attributes of your Model class. You can usually map the table columns to the attributes of the class, including primary and foreign keys.

```ruby
# EXAMPLE
# Table name: items

# Model class
# (in lib/item.rb)

class Item

  
  attr_accessor :id, :item_name, :price, :quantity
end

```

*You may choose to test-drive this class, but unless it contains any more logic than the example above, it is probably not needed.*

## 5. Define the Repository Class interface

Your Repository class will need to implement methods for each "read" or "write" operation you'd like to run against the database.

Using comments, define the method signatures (arguments and return value) and what they do - write up the SQL queries that will be used by each method.

```ruby
# EXAMPLE
# Table name: items

# Repository class
# (in lib/items_Repository.rb)

class itemsRepository

  def all

  end

  def find(id)
    
  end

  def create(item)
  end

  def update(item)
  end

  def delete(item)
  end
end
```

## 6. Write Test Examples

Write Ruby code that defines the expected behaviour of the Repository class, following your design from the table written in step 5.

These examples will later be encoded as RSpec tests.

```ruby
# EXAMPLES

# 1
# Get all itemss

repo = ItemsRepository.new

items = repo.all

items.length # =>  2

items[0].id # =>  1
items[0].item_name # =>  'David'
items[0].price # =>  330
items[0].quantity #=> 4

items[1].id # =>  2
items[1].item_name # =>  'Anna'
items[1].price # =>  330
items[1].quantity #=> 5

# 2
# Get a single item

repo = ItemsRepository.new

items = repo.find(1)

items.id # =>  1
items.item_name # =>  'David'
items.price # =>  "23"
items.quantity # =>  "3"

# 3
# create a single order 
repo = ItemsRepository.new
item = Item.new
item.id # => "??"
item.item_name # =>  'rings_new'
item.price # =>  34
item.quantity # =>  6

repo.create(item)
items = repo.all


items[2].id # => "3"
items[2].item_name # =>  'rings_new'
items[2].price # =>  34
items[2].quantity # =>  6

#4
# update an item
repo = ItemsRepository.new
item = repo.find(1)

item.item_name # =>  'rubies_new'
repo.update(item)

items = repo.all


items[2].id # => "1"
items[2].item_name # =>  'rubies_new'
items[2].price # => 800
items[2].quantity # => 19

#5
# delete an item
repo = ItemsRepository.new
item = repo.find(1)

repo.delete(item)

items = repo.all
items.length #=> 1

```

Encode this example as a test.

## 7. Reload the SQL seeds before each test run


```ruby
# EXAMPLE

# file: spec/items_Repository_spec.rb

def reset_items_items_table
  seed_sql = File.read('spec/seeds_items_items.sql')
  connection = PG.connect({ host: '127.0.0.1', dbname: 'shop_manager_test' })
  connection.exec(seed_sql)
end

describe itemsRepository do
  before(:each) do 
    reset_items_items_table
  end

  # (your tests will go here).
end
```

## 8. Test-drive and implement the Repository class behaviour
