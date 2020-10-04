##### LS180 Database Foundations > Relational Data and JOINs > Using Foreign Keys

---

### Practice Problems

---

1. Import [this file](https://raw.githubusercontent.com/launchschool/sql_course_data/master/sql-and-relational-databases/relational-data-and-joins/foreign-keys/orders_products1.sql) into a new database.

```
$ createdb foreign-keys
$ psql -d foreign-keys < orders_product1.sql
```

---

2. Update the `orders` table so that referential integrity will be preserved for the data between `orders` and `products`.

```sql
ALTER TABLE orders ADD FOREIGN KEY (product_id) REFERENCES products (id);

-- OR long form 

ALTER TABLE orders ADD CONSTRAINT orders_product_id_fkey FOREIGN KEY (product_id) REFERENCES products(id);
```

---

3. Use `psql` to insert the data shown in the following table into the database:

   | Quantity | Product    |
   | :------- | :--------- |
   | 10       | small bolt |
   | 25       | small bolt |
   | 15       | large bolt |

```sql
INSERT INTO products (name) VALUES ('small bolt'), ('large bolt');

INSERT INTO orders (product_id, quantity) VALUES (1, 10), (1, 25), (2, 15);
```

---

4. Write a SQL statement that returns a result like this:

   ```
    quantity |    name
   ----------+------------
          10 | small bolt
          25 | small bolt
          15 | large bolt
   (3 rows)
   ```

```sql
SELECT quantity, name FROM orders
 INNER JOIN products ON product_id = products.id;
```

---

5. Can you insert a row into `orders` without a `product_id`? Write a SQL statement to prove your answer.
```
\d orders
                              Table "public.orders"
   Column   |  Type   | Collation | Nullable |              Default               
------------+---------+-----------+----------+------------------------------------
 id         | integer |           | not null | nextval('orders_id_seq'::regclass)
 product_id | integer |           |          | 
 quantity   | integer |           | not null | 
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Foreign-key constraints:
    "orders_product_id_fkey" FOREIGN KEY (product_id) REFERENCES products(id)
```

Based on the above we see that there is NO `not null` constraint on product_id. We may get an order for a quantity, but not of what. This seems plausible. So yes I believe that we can enter an order with no product_id.

```sql
INSERT INTO orders (quantity) VALUES (30);

SELECT * FROM orders;

 id | product_id | quantity 
----+------------+----------
  1 |          1 |       10
  2 |          1 |       25
  3 |          2 |       15
  4 |            |       30
(4 rows)
```

And we can.

---

6. Write a SQL statement that will prevent NULL values from being stored in `orders.product_id`. What happens if you execute that statement?

```sql
ALTER TABLE orders ALTER COLUMN product_id SET NOT NULL;
ERROR:  column "product_id" contains null values
```

We get an error because of the previous question when we inserted an order with no `product_id`. 

---

7. Make any changes needed to avoid the error message encountered in #6.

There are two options; delete the row with NULL value or update the row with a valid product_id.

```sql
DELETE FROM orders WHERE product_id IS NULL;
```

OR

```sql
UPDATE orders SET product_id = 2 WHERE product_id IS NULL;
```

Now execute the statement from the previous question

```sql
ALTER TABLE orders ALTER COLUMN product_id SET NOT NULL;
```

---

8. Create a new table called `reviews` to store the data shown below. This table should include a primary key and a reference to the `products` table.

   product  | review 
 -----------+-------------------------
 small bolt | a little small
 small bolt | very round
 large bolt | could have been smaller 

```sql
CREATE TABLE reviews (
id SERIAL PRIMARY KEY,
body TEXT NOT NULL,
product_id INTEGER REFERENCES products(id)
);
```

---

9. Write SQL statements to insert the data shown in the table in #8.

```sql
INSERT INTO reviews (review, product_id)
VALUES ('a little small', 1),
('very round!', 1),
('could have been smaller', 2);
```

---

10. **True** or **false**: A foreign key constraint prevents NULL values from being stored in a column.

False. As we saw above, foreign key columns allow NULL values. As a result, it is often necessary to use NOT NULL and a foreign key constraint together.
