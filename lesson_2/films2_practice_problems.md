##### LS180 Database Foundations > Schema, Data, and SQL > More Constraints

---

### More Constraints

---

1. Import [this file](https://raw.githubusercontent.com/launchschool/sql_course_data/master/sql-and-relational-databases/schema-data-and-sql/more-constraints/films2.sql) into a PostgreSQL database. (films2.sql)

```
\i films2.sql

-- OR 

$ psql -d sql-course < films2.sql
```

---

2. Modify all of the columns to be `NOT NULL`.

```sql
ALTER TABLE films
ALTER COLUMN title SET NOT NULL,
ALTER COLUMN year SET NOT NULL,
ALTER COLUMN genre SET NOT NULL,
ALTER COLUMN director SET NOT NULL,
ALTER COLUMN duration SET NOT NULL;
```

---

3. How does modifying a column to be `NOT NULL` affect how it is displayed by `\d films`?

not null will be included in that column's Modifiers column:

```sql
sql-course=# \d films
             Table "public.films"
  Column  |          Type          | Modifiers
----------+------------------------+-----------
 title    | character varying(255) | not null
 year     | integer                | not null
 genre    | character varying(100) | not null
 director | character varying(255) | not null
 duration | integer                | not null
```

OR for what I'm guessing is a more recent version of postgresql, it will look like this:

```sql
   sql-course=# \d films
                           Table "public.films"
     Column  |          Type          | Collation | Nullable | Default 
   ----------+------------------------+-----------+----------+---------
    title    | character varying(255) |           | not null | 
    year     | integer                |           | not null | 
    genre    | character varying(100) |           | not null | 
    director | character varying(255) |           | not null | 
    duration | integer                |           | not null | 
```

---

4. Add a constraint to the table **films** that ensures that all films have a unique title.

```sql
ALTER TABLE films ALTER COLUMN title ADD UNIQUE;
```

---

5. How is the constraint added in #4 displayed by `\d films`?

It appears as an index:

```
Indexes:
    "title_unique" UNIQUE CONSTRAINT, btree (title)
```

---

6. Write a SQL statement to remove the constraint added in #4.

```sql
ALTER TABLE films DROP CONSTRAINT title_unique;
```

---

7. Add a constraint to **films** that requires all rows to have a value for `title` that is at least 1 character long.

```sql
ALTER TABLE films ADD CONSTRAINT title_length CHECK (LENGTH(title) >= 1);

-- OR

ALTER TABLE films ADD CHECK (LENGTH(title) >= 1);
```

---

8. What error is shown if the constraint created in #7 is violated? Write a SQL `INSERT` statement that demonstrates this.

```sql
INSERT INTO films VALUES ('', 2000, 'scifi', 'Joe Bush', 87);
```

```
ERROR:  new row for relation "films" violates check constraint "films_title_check"
DETAIL:  Failing row contains (, 2000, scifi, Joe Bush, 87).
```

---

9. How is the constraint added in #7 displayed by `\d films`?

```
Check constraints:
    "films_title_check" CHECK (length(title::text) >= 1)
```

---

10. Write a SQL statement to remove the constraint added in #7.

```sql
ALTER TABLE films DROP CONSTRAINT films_title_check;
```

---

11. Add a constraint to the table **films** that ensures that all films have a year between 1900 and 2100.

```sql
ALTER TABLE films ADD CHECK ("year" BETWEEN 1900 AND 2100);
```

---

12. How is the constraint added in #11 displayed by `\d films`?

```
Check constraints:
    "films_year_check" CHECK (year >= 1900 AND year <= 2100)
```

---

13. Add a constraint to **films** that requires all rows to have a value for `director` that is at least 3 characters long and contains at least one space character (` `).

```sql
ALTER TABLE films 
  ADD CHECK (LENGTH(director) >= 3 AND strpos(director, ' ') > 0);

-- OR

ALTER TABLE films ADD CONSTRAINT director_name
    CHECK (length(director) >= 3 AND position(' ' in director) > 0);
```

---

14. How does the constraint created in #13 appear in `\d films`?

```
Check constraints:
    "films_director_check" CHECK (length(director::text) >= 3 AND strpos(director::text, ' '::text) > 0)
```

---

15. Write an `UPDATE` statement that attempts to change the director for "Die Hard" to "Johnny". Show the error that occurs when this statement is executed.

```sql
UPDATE films SET director = 'Johnny' WHERE title = 'Die Hard';
```

```
ERROR:  new row for relation "films" violates check constraint "films_director_check"
DETAIL:  Failing row contains (Die Hard, 1988, action, Johnny, 132).
```

---

16. List three ways to use the schema to restrict what values can be stored in a column.

  1. Data type (which can include a length limitation)
  2. NOT NULL Constraint
  3. Check Constraint

---

17. Is it possible to define a default value for a column that will be considered invalid by a constraint? Create a table that tests this.

Yes, but anytime you attempt to `INSERT` a value with the default value, an error will be raised indicating that the constraint has been violated.

```sql
CREATE TABLE shoes (name text, size numeric(3,1) DEFAULT 0);
ALTER TABLE shoes ADD CONSTRAINT shoe_size CHECK (size BETWEEN 1 AND 15);

/* insert data with default value for size column */
INSERT INTO shoes (name) VALUES ('blue sneakers');
```

Raises this error:

```
ERROR:  new row for relation "shoes" violates check constraint "shoe_size"
DETAIL:  Failing row contains (blue sneakers, 0.0).
```

---

18. How can you see a list of all the constraints on a table?

Using `\d $tablename`, where `$tablename` is the name of the table.
