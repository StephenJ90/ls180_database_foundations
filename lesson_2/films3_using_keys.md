##### LS180 Database Foundations > Schema, Data, and SQL > Using Keys

---

### Using Keys

---

Import [this file](https://raw.githubusercontent.com/launchschool/sql_course_data/master/sql-and-relational-databases/schema-data-and-sql/using-keys/films3.sql) into a PostgreSQL database.

```
sql-course=# SELECT * FROM films;
           title           | year |   genre   |       director       | duration
---------------------------+------+-----------+----------------------+----------
 Die Hard                  | 1988 | action    | John McTiernan       |      132
 Casablanca                | 1942 | drama     | Michael Curtiz       |      102
 The Conversation          | 1974 | thriller  | Francis Ford Coppola |      113
 1984                      | 1956 | scifi     | Michael Anderson     |       90
 Tinker Tailor Soldier Spy | 2011 | espionage | Tomas Alfredson      |      127
 The Birdcage              | 1996 | comedy    | Mike Nichols         |      118
(6 rows)
```

Make the following changes

```sql
INSERT INTO films(title, year, genre, director, duration) VALUES ('Godzilla', 1998, 'scifi', 'Roland Emmerich', 139);
INSERT INTO films(title, year, genre, director, duration) VALUES ('Godzilla', 2014, 'scifi', 'Gareth Edwards', 123);

UPDATE films SET duration = 92 WHERE title = '1984';

UPDATE films SET duration = 141 WHERE title = 'Godzilla' AND year = 2014;
```

---

##### Practice Problems

1. Write a SQL statement that makes a new sequence called "counter".

```sql
CREATE SEQUENCE counter;
```

---

2. Write a SQL statement to retrieve the next value from the sequence created in #1.

```sql
SELECT nextval('counter');
```

---

3. Write a SQL statement that removes a sequence called "counter".

```sql
DROP SEQUENCE counter;
```

---

4. Is it possible to create a sequence that returns only even numbers? [The documentation for sequence might be useful](https://www.postgresql.org/docs/9.5/sql-createsequence.html).

Yes.

```sql
CREATE SEQUENCE even_counter INCREMENT BY 2 START WITH 2;

-- OR

CREATE SEQUENCE even_counter INCREMENT BY 2 MINVALUE 2;
```

---

5. What will the name of the sequence created by the following SQL statement be?

```sql
CREATE TABLE regions (id serial PRIMARY KEY, name text, area integer);
```

`regions_id_seq`.

---

6. Write a SQL statement to add an auto-incrementing integer primary key column to the `films` table.

```sql
ALTER TABLE films ADD COLUMN id SERIAL PRIMARY KEY;
```

---

7. What error do you receive if you attempt to update a row to have a value for `id` that is used by another row?

```sql
UPDATE films SET id = 8 WHERE title = 'Die Hard';
```

```
ERROR:  duplicate key value violates unique constraint "films_pkey"
DETAIL:  Key (id)=(8) already exists.
```

---

8. What error do you receive if you attempt to add another primary key column to the `films` table?

```
ERROR:  multiple primary keys for table "films" are not allowed
```

---

9. Write a SQL statement that modifies the table `films` to remove its primary key while preserving the `id` column and the values it contains.

```sql
ALTER TABLE films DROP CONSTRAINT films_pkey;
```
