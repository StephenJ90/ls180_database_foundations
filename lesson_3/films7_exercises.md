##### LS180 Database Foundations > Relational Data and JOINs > Converting a 1:M Relationship to a M:M Relationship

---

### Practice Problems

---

1. Import [this file](https://raw.githubusercontent.com/launchschool/sql_course_data/master/sql-and-relational-databases/relational-data-and-joins/converting-a-1m-relationship-to-a-mm-relationship/films7.sql) into a database using `psql`.


```
$ createdb om-to-mm
$ psql -d om-to-mm < films7.sql
```

---

2. Write the SQL statement needed to create a join table that will allow a film to have multiple directors, and directors to have multiple films. Include an `id` column in this table, and add foreign key constraints to the other columns.

```sql
CREATE TABLE directors_films (
  id SERIAL PRIMARY KEY,
  director_id INTEGER REFERENCES directors (id),
  film_id INTEGER REFERENCES films (id)
);
```

---

3. Write the SQL statements needed to insert data into the new join table to represent the existing one-to-many relationships.

```sql
INSERT INTO directors_films (film_id, director_id)
VALUES (1, 1),
(2, 2),
(3, 3),
(4, 4),
(5, 5),
(6, 6),
(7, 3),
(8, 7),
(9, 8),
(10, 4);
```

---

4. Write a SQL statement to remove any unneeded columns from `films`.

```sql
ALTER TABLE films DROP COLUMN director_id;
```

---

5. Write a SQL statement that will return the following result:

   ```psql
              title           |         name
   ---------------------------+----------------------
    12 Angry Men              | Sidney Lumet
    1984                      | Michael Anderson
    Casablanca                | Michael Curtiz
    Die Hard                  | John McTiernan
    Let the Right One In      | Michael Anderson
    The Birdcage              | Mike Nichols
    The Conversation          | Francis Ford Coppola
    The Godfather             | Francis Ford Coppola
    Tinker Tailor Soldier Spy | Tomas Alfredson
    Wayne's World             | Penelope Spheeris
   (10 rows)
   ```

```sql
SELECT films.title, directors.name
  FROM films
    INNER JOIN directors_films ON directors_films.film_id = films.id
    INNER JOIN directors ON directors.id = directors_films.director_id
  ORDER BY films.title ASC;
```

---

6. Write SQL statements to insert data for the following films into the database:

   | Film                   | Year | Genre   | Duration | Directors                      |
   | :--------------------- | :--- | :------ | :------- | :----------------------------- |
   | Fargo                  | 1996 | comedy  | 98       | Joel Coen                      |
   | No Country for Old Men | 2007 | western | 122      | Joel Coen, Ethan Coen          |
   | Sin City               | 2005 | crime   | 124      | Frank Miller, Robert Rodriguez |
   | Spy Kids               | 2001 | scifi   | 88       | Robert Rodriguez               |

```sql
INSERT INTO films (title, year, genre, duration)
VALUES ('Fargo', 1996, 'comedy', 98),
('No Country for Old Men', 2007, 'western', 122),
('Sin City', 2005, 'crime', 124),
('Spy Kids', 2001, 'scifi', 88);

INSERT INTO directors (name)
VALUES ('Joel Coen'),
'Ethan Coen'),
('Frank Miller'),
('Robert Rodriguez');

INSERT INTO directors_films (director_id, film_id)
   VALUES (9, 11),
   (9, 12),
   (10, 12),
   (11, 13),
   (12, 13),
   (12, 14);
```

---

7. Write a SQL statement that determines how many films each director in the database has directed. Sort the results by number of films (greatest first) and then name (in alphabetical order).

```sql
SELECT directors.name AS director, COUNT(directors_films.film_id) AS films
  FROM directors
    INNER JOIN directors_films ON directors.id = directors_films.director_id
  GROUP BY directors.id
  ORDER BY films DESC, directors.name ASC;
```
