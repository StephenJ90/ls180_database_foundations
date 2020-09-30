##### LS180 Database Foundations > Schema, Data, and SQL > Loading Database Dumps

---

### Practice Problems

---

1. Load [this file](https://raw.githubusercontent.com/launchschool/sql_course_data/master/sql-and-relational-databases/schema-data-and-sql/loading-database-dumps/films1.sql) into your database.

```
stephen$ psql -d sql-course < films1.sql
```

##### OR

```sql
sql-course=# \i films1.sql
```

  1. What does the file do?

  The file contains SQL statements, when imported the statements are executed.

  2. What is the output of the command? What does each line in this output mean?

  ```sql
NOTICE:  table "films" does not exist, skipping
DROP TABLE IF EXISTS public.films; # there is no table called `films` in the database so this command is skipped
CREATE TABLE # Creates a table
INSERT 0 1 # Inserts one record into a table
INSERT 0 1 # Inserts one record into a table
INSERT 0 1 # Inserts one record into a table
  ```

  3. Open up the file and look at its contents. What does the first line do?

  Drops the table `films` if it already exists.

---

2. Write a SQL statement that returns all rows in the **films** table.

```sql
SELECT * FROM films;
```

---

3. Write a SQL statement that returns all rows in the **films** table with a title shorter than 12 letters.

```sql
SELECT * FROM films WHERE LENGTH(title) < 12;
```

---

4. Write the SQL statements needed to add two additional columns to the **films** table: `director`, which will hold a director's full name, and `duration`, which will hold the length of the film in minutes.

```sql
ALTER TABLE films
ADD COLUMN director VARCHAR(100),
ADD COLUMN duration INTEGER;
```

5. Write SQL statements to update the existing rows in the database with values for the new columns:

   | title            | director             | duration |
   | :--------------- | :------------------- | -------: |
   | Die Hard         | John McTiernan       |      132 |
   | Casablanca       | Michael Curtiz       |      102 |
   | The Conversation | Francis Ford Coppola |      113 |


```sql
UPDATE films 
   SET director = 'John McTiernan', 
       duration = 132 
 WHERE title = 'Die Hard';
UPDATE films 
   SET director = 'Michael Curtiz',
       duration = 102 
 WHERE title = 'Casablanca';
UPDATE films 
   SET director = 'Francis Ford Coppola',
       duration = 113
 WHERE title = 'The Conversation';
```

---

6. Write SQL statements to insert the following data into the **films** table:

```sql
INSERT INTO films 
VALUES ('1984', 1956, 'scifi', 'Michael Anderson', 90),
('Tinker Tailor Soldier Spy', 2011, 'espionage', 'Tomas Alfredson', 127),
('The Birdcage', 1996, 'comedy', 'Mike Nichols', 118);
```

---

7. Write a SQL statement that will return the title and age in years of each movie, with newest movies listed first:

```sql
SELECT title, (2020 - "year") AS age
  FROM films
  ORDER BY age ASC;

-- OR LS solution

SELECT title, extract("year" from current_date) - "year" AS age
  FROM films
  ORDER BY age ASC;
```

8. Write a SQL statement that will return the title and duration of each movie longer than two hours, with the longest movies first.

```sql
SELECT title, duration FROM films WHERE duration > 120 ORDER BY duration DESC;
```

---

9. Write a SQL statement that returns the title of the longest film.

```sql
SELECT title FROM films ORDER BY duration DESC LIMIT 1;

-- OR

SELECT title FROM films WHERE duration = (SELECT max(duration) FROM films);
```