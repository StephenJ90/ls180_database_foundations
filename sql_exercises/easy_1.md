LS180 Database Foundations > SQL Fundamentals > Easy 1

---

### Create a Database

---

Let's start by creating a database. Create a new database called `animals`.

###### Solution

The wrapper function for CREATE DATABASE, `createdb`.

```sql
StephenJ:lesson_1 stephen$ createdb animals
```
Or

```sql
CREATE DATABASE animals
```

For this exercise, we need to create a new database. With PostgreSQL, the easiest way to do that is to use the Postgres wrapper function, `createdb`. But, if we want to stick with standard SQL, then we can use the `CREATE DATABASE` SQL command. Remember: to use `CREATE DATABASE` (and other SQL), you must use the `psql` console; to use `createdb` (and other wrapper functions), you must use the regular terminal console.

---

### Create a Table

---

Now that we have an `animals` database, we can lay the groundwork needed to add some data to it.  

Make a table called `birds`. It should have the following fields:

* id (a primary key)
* name (string with space for up to 25 characters)
* age (integer)
* species (a string with room for no more than 15 characters)

###### Solution

```sql
CREATE TABLE birds (
id SERIAL PRIMARY KEY,
name VARCHAR(25),
age INTEGER,
species VARCHAR(15)
);
```

Let's start from the top. We want a primary key that auto-increments. For that last part, we'll use the pseudotype `serial`. It auto-increments `id` for us as we create new `bird` rows in the table. This column should be a primary key, which ensures that `id` is `NOT NULL` and `UNIQUE`. (There's some overlap here; `serial` also includes `NOT NULL`.)  

We also want `name` and `species` columns. Those will work as text. Here, we use `varchar`, `character varying`, or `text`, which are aliases, so all will work.  

The `age` column is last; we set it as type `integer`.

---

### Insert Data

---

For this exercise, we'll add some data to our `birds` table. Add five records to this database so that our data looks like:

```
 id |   name   | age | species
----+----------+-----+---------
  1 | Charlie  |   3 | Finch
  2 | Allie    |   5 | Owl
  3 | Jennifer |   3 | Magpie
  4 | Jamie    |   4 | Owl
  5 | Roy      |   8 | Crow
(5 rows)
```

###### Solution

```sql
INSERT INTO birds (name, age, species)
VALUES ('Charlie', 3, 'Finch'),
('Allie', 5, 'Owl'),
('Jennifer', 3, 'Magpie'),
('Jamie', 4, 'Owl'),
('Roy', 8, 'Crow');
```

To add data to our `birds` table, we need to use the `INSERT INTO` SQL statement. Its general form is:

```sql
INSERT INTO table_name [ (column_name [, ...]) ] VALUES (value [, ...])
```

We specify a table, the column names we wish to deal with and the values we wish to insert under each of these column. Order is important here; when listing the VALUES, make sure the order of those values corresponds to the order of the column names.  

To be clear, we're using PostgreSQL documentation syntax here. "(column_name) [, ...]" means that we can have one or more columns delimited by commas. The parentheses are required.

###### Further Exploration

There is a form of `INSERT INTO` that doesn't require the column names. How does that form of `INSERT INTO` work, and when would you use is?

```sql
INSERT INTO birds
VALUES (DEFAULT, 'Charlie', 3, 'Finch'),
(DEFAULT, 'Allie', 5, 'Owl'),
(DEFAULT, 'Jennifer', 3, 'Magpie'),
(DEFAULT, 'Jamie', 4, 'Owl'),
(DEFAULT, 'Roy', 8, 'Crow');
```

---

### Select Data

---

Write an SQL statement to query all data that is currently in our birds table.

###### Solution

```sql
SELECT * FROM birds;

-- or we can specify all the columns we wish to query

SELECT name, age, species FROM birds;
```

---

### WHERE Clause

---

In this exercise, let's practice filtering the data we want to query. Using a `WHERE` clause, `SELECT` records for birds under the age of 5.

###### Solution

```sql
SELECT * FROM birds WHERE age < 5;
```

---

### Update Data

---

It seems there was a mistake when we were inserting data in the `birds` table. One of the rows has a species of 'Crow', but that bird is actually a Raven Update the `birds` table so that a row with a species of 'Crow' now reads 'Raven'.

###### Solution

```sql
UPDATE birds SET species = 'Raven' 
  WHERE species = 'Crow';
```

###### Further Exploration

Oops. Jamie isn't an Owl - he's a Hawk. Correct his information.

```sql
UPDATE birds SET species = 'Hawk'
  WHERE species = 'Owl' AND name = 'Jamie';
```

---

### DELETE Data

---

Write an SQL statement that deletes the record that describes a 3 year-old finch.

###### Solution

```sql
DELETE FROM birds WHERE age = 3 AND species = 'Finch';
```

---

### Add Constraint

---

When we initially created our `birds` table, we forgot to take into account a key factor; preventing certain values from being entered into the database. For instance, we would never find a bird that is negative n years old. We could have included a constraint to ensure that bad data isn't entered for a database record, but we forgot to do so.  

For this exercise, write some code that ensures that only birds with a positive age may be added to the database. Then write and execute an SQL statement to check that this new constraint works correctly.

###### Solution

```sql
ALTER TABLE birds ADD CHECK(age > 0); -- shorthand form where postgres auto-generates a constraint name

-- OR

ALTER TABLE birds ADD CONSTRAINT check_age CHECK (age > 0);
```

```sql
INSERT INTO birds (age, name, species) VALUES (-4, 'Captain', 'Falcon');
```

---

### Drop Table

---

It seems we are done with our `birds` table, and no longer have a need for it in our `animals` database. Write an SQL statement that will drop the `birds` table and all its data from the `animals` database.

###### Solution

```sql
DROP TABLE birds;
```

---

### Drop Database

---

###### Solution

Let's finish things up by dropping the database `animals`. With no tables in it, we don't have much use for it any more. Write a SQL statement or PostgreSQL command to drop the `animals` database.

First, we need to connect to a different database. If we try to drop a database in which we are currently connected, an error will occur.

```sql
\connect sql_course
```

Now we can drop the animals database:

```sql
DROP DATABASE animals;
```
