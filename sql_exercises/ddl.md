LS180 Database Foundations > SQL Fundamentals > DDL (Data Definition Language)

---

### Create and Extrasolar Planetary Database

---

For this exercise set, we will be working with the DDL statements to create and modify tables in a database that tracks planets around stars other than our Sun. To get started, first create a postgresql database named "extrasolar", and then create two tables in the database as follows:

`stars` table

- `id`: a unique serial number that auto-increments and serves as a primary key for this table.
- `name`: the name of the star,e,g., "Alpha Centauri A". Allow room for 25 characters. Must be unique and non-null.
- `distance`: the distance in light years from Earth. Define this as a whole number (e.g., 1, 2, 3, etc) that must be non-null and greater than 0.
- `spectral_type`: the spectral type of the star: O, B, A, F, G, K, and M. Use a one character string.
- `companions`: how many companion stars does the star have? A whole number will do. Must be non-null and non-negative.

`planets` table

- `id`: a unique serial number that auto-increments and serves as a primary key for this table.
- `designation`: a single alphabetic character that uniquely identifies the planet in its star system ('a', 'b', 'c', etc.)
- `mass`: estimated mass in terms of Jupiter masses; use an integer for this value.

###### Solution

```sql
CREATE DATABASE extrasolar

\c extrasolar

CREATE TABLE stars (
id SERIAL PRIMARY KEY,
name VARCHAR(25) UNIQUE NOT NULL,
distance INTEGER NOT NULL CHECK(distance > 0),
spectral_type CHAR(1),
companions INTEGER NOT NULL CHECK(companions >= 0),
);

CREATE TABLE planets (
id SERIAL PRIMARY KEY,
designation CHAR(1),
mass INTEGER
);
```

---

### Relating Stars and Planets

---

You may have noticed that, when we created the `stars` and `planets` tables, we did not do anything to establish a relationship between the two tables. They are simply standalone tables that are related only by the fact that they both belong to the `extrasolar` database. However, there is no relationship between the rows of each table.

To correct this problem, add a `star_id` column to the `planets` table; this column will be used to relate each planet in the `planets` table to its home star in the `stars` table. Make sure the row is defined in such a way that it is required and must have a value that is present as an `id` in the `stars` table.

###### Solution

```sql
ALTER TABLE planets ADD COLUMN star_id INTEGER NOT NULL REFERENCES stars (id);
```

---

### Increase Star Name Length

---

Hmm... it turns out that 25 characters isn't enough room to store a star's complete name. For instance, the star "Epsilon Trianguli Australis A" requires 30 characters. Modify the column so that it allows star names as long as 50 characters.

###### Solution

```sql
ALTER TABLE stars
ALTER COLUMN name TYPE VARCHAR(50);
```

##### Futher Exploration

Assume the `stars` table already contains one or more rows of data. What will happen when you try to run the above command? To test this, revert the modification and add a row or two of data.  


###### Solution

Since we are increase the range of characters allowed in the `name` column, we will see no change in running the command again. However, if we were to decrease the range to VARCHAR(10), we would see an error:
`ERROR:  value too long for type character varying(10)` because 'Alpha b Centauri' is more than 10 characters.

---

### Stellar Distance Precision

---

For many of the closest stars, we know the distance from Earth fairly accurately; for instance, Proxima Centauri is roughly 4.3 light years away. Our database, as currently defined, only allows integer distances, so the most accurate value we can enter is 4. Modify the `distance` column in the `stars` table so that it allows fractional light years to any degree of precision required.

###### Solution

```sql
ALTER TABLE stars 
ALTER COLUMN distance TYPE NUMERIC;
```

##### Further Exploration

Assume the `stars` table already contains one or more rows of data. What will happen when you try to run the above command? To test this, revert the modification and add a row or two of data. 

###### Solution

The above command will still work since an ingteger can be stored as a numeric. However, if we were to go the other way and change the column from `numeric` to `integer` and there were decimal values stored in the column, these values would be truncated to integers (no decimals). 

---

### Check Values in List

---

The `spectral_type` column in the `stars` table is currently defined as a one-character string that contains one of the following 7 values: `'O'`, `'B'`, `'A'`, `'F'`, `'G'`, `'K'`, and `'M'`. However, there is currently no enforcement on the values that may be entered. Add a constraint to the table `stars` that will enforce the requirement that a row must hold one of the 7 listed values above. Also, make sure that a value is required for this column.

###### Solution

```sql
ALTER TABLE stars
ADD CHECK (spectral_type IN ('O', 'B', 'A', 'F', 'G', 'K', 'M')),
ALTER COLUMN spectral_type SET NOT NULL;

-- Or

ALTER TABLE stars
ADD CHECK (spectral_type ~ '[OBAFGKM]'),
ALTER COLUMN spectral_type SET NOT NULL;
```

##### Further Exploration

Assume the `stars` table contains one or more rows that are missing a `spectral_type` value, or that have an illegal value. What will happen when you try to alter the table schema? How would you go about adjusting the data to work around this problem. To test this, revert the modification and add some data.

```sql
ALTER TABLE stars
DROP CONSTRAINT stars_spectral_type_check,
ALTER COLUMN spectral_type DROP NOT NULL;

INSERT INTO stars (name, distance, companions)
           VALUES ('Epsilon Eridani', 10.5, 0);

INSERT INTO stars (name, distance, spectral_type, companions)
           VALUES ('Lacaille 9352', 10.68, 'X', 0);

ALTER TABLE stars
ADD CHECK (spectral_type IN ('O', 'B', 'A', 'F', 'G', 'K', 'M')),
ALTER COLUMN spectral_type SET NOT NULL;
```

###### Solution

```sql
ALTER TABLE stars
ADD CHECK (spectral_type IN ('O', 'B', 'A', 'F', 'G', 'K', 'M'));
```

the following error is raised:

```
ERROR:  check constraint "stars_spectral_type_check" is violated by some row
```

And for the `NOT NULL` constraint,

```sql
ALTER TABLE stars
ALTER COLUMN spectral_type SET NOT NULL;
```

the following error is raised:

```
ERROR:  column "spectral_type" contains null values
```

We could update the rows with data invalid values to `?` to represent these unknown values. We could also update with valid values, but we do not know if these would be the correct values for those stars.

```sql
UPDATE stars SET spectral_type = '?' WHERE spectral_type IS NULL;
```

The above will update a row that has a NULL value in the spectral_type. We set it to `?` cause we don't know what the actual type is.

We then have to add the check back to the table. One of our rows of data contains a `?` and the other contains `X`. But of these characters are invalid considering the valid spectral types above. We will have to update the row that has `X` in the spectral_type before adding in the `CHECK CONSTRAINT`.

```sql
UPDATE stars SET spectral_type = '?' WHERE spectral_type = 'X';
```

Now we can add the constraints back to the table:

```sql
ALTER TABLE stars
ADD CHECK (spectral_type ~ '[OBAFGKM?'),
ALTER COLUMN spectral_type SET NOT NULL;
```

---

### Enumerated Types

---

In the previous exercise, we added a `CHECK` constraint to the `stars` table to enforce that the value stored in the `spectral_type` column for each row is valid. In this exercise, we will use an alternate approach to the same problem.

PostgreSQL provides what is called an enumerated data type; that is, a data type that must have one of a finite set of values. For instance, a traffic light can be red, green, or yellow: these are enumerate values for the color of the currently lit signal light.

Modify the `stars` table to remove the `CHECK` constraint on the `spectral_type` column, and then modify the `spectral_type` column so it becomes an enumerated type that restricts it to one of the following 7 values: `'O'`, `'B'`, `'A'`, `'F'`, `'G'`, `'K'`, and `'M'`.

###### Solution

```sql
ALTER TABLE stars
DROP CONSTRAINT stars_spectral_type_check;

CREATE TYPE spectral_type_enum AS ENUM ('O', 'B', 'A', 'F', 'G', 'K', 'M', '?');

ALTER TABLE stars
ALTER COLUMN spectral_type TYPE spectral_type_enum
                           USING spectral_type::spectral_type_enum;
```

The reason for this `USING` clause is that there is no defined way in PostgreSQL to convert `char` values to an enumerated type: the `USING` clause tells PostgreSQL to simply use the existing values from `spectral_type` as though they are the enumerated values.

---

### Planetary Mass Precision

---

We will measure Planetary masses in terms of the mass of Jupiter. As such, the current data type of `integer` is inappropriate; it is only really useful for planets as large as Jupiter or larger. These days, we know of many extrasolar planets that are smaller than Jupiter, so we need to be able to record fractional parts for the mass. Modify the `mass` column in the `planets` table so that it allows fractional masses to any degree of precision required. In addition, make sure the mass is required and positive.

While we're at it, also make the `designation` column required.

###### Solution

```sql
ALTER TABLE planets
ALTER COLUMN mass TYPE numeric,
ALTER COLUMN mass SET NOT NULL,
ADD CHECK (mass > 0.0),
ALTER COLUMN designation SET NOT NULL;
```

---

### Add a Semi-Major Axis Column

---

Add a `semi_major_axis` column for the semi-major axis of each planet's orbit; the semi-major axis is the average distance from the planet's star as measured in astronomical units (1 AU is the average distance of the Earth from the Sun). Use a data type of `numeric`, and require that each planet have a value for the `semi_major_axis`.

###### Solution

```sql
ALTER TABLE planets
ADD COLUMN semi_major_axis NUMERIC NOT NULL;
```

##### Further Exploration

Assume the `planets` table already contains one or more rows of data. What will happen when you try to run the above command? What will you need to do differently to obtain the desired result? To test this, delete the `semi_major_axis` column and then add a row or two of data (note: your `stars` table will also need some data that corresponds to the `star_id` values):

```sql
ALTER TABLE planets
DROP COLUMN semi_major_axis;

DELETE FROM stars;
INSERT INTO stars (name, distance, spectral_type, companions)
           VALUES ('Alpha Centauri B', 4.37, 'K', 3);
INSERT INTO stars (name, distance, spectral_type, companions)
           VALUES ('Epsilon Eridani', 10.5, 'K', 0);

INSERT INTO planets (designation, mass, star_id)
           VALUES ('b', 0.0036, 1); -- check star_id; see note below
INSERT INTO planets (designation, mass, star_id)
           VALUES ('c', 0.1, 2); -- check star_id; see note below

ALTER TABLE planets
ADD COLUMN semi_major_axis numeric NOT NULL;
```

NOTE: The `semi_major_axis` for Alpha Centauri B planet b is 0.04 AU while that for Epsilon Eridani planet c is about 40 AU. Note also that the `star_id` values shown above may be different from what is in your database. Check your `stars` table to see which `star_id` values to use.

###### Solution

```sql
ALTER TABLE planets
  ADD COLUMN semi_major_axis numeric;
  
UPDATE planets
   SET semi_major_axis = 0.04
 WHERE star_id = 8;
 
UPDATE planets
   SET semi_major_axis = 40
 WHERE star_id = 9;
 
ALTER TABLE planets
ALTER COLUMN semi_major_axis SET NOT NULL;
```

---

### Add a Moons Table

---

Someday in the future, technology will allow us to start observing the moons of extrasolar planets. At that point, we're going to need a `moons` table in our `extrasolar` database. For this exercise, your task is to add that table to the database. The table should include the following data:

- `id`: a unique serial number that auto-increments and serves as a primary key for this table.
- `designation`: the designation of the moon. We will assume that moon designations will be numbers, with the first moon discovered for each planet being moon 1, the second moon being moon 2, etc. The designation is required.
- `semi_major_axis`: the average distance in kilometers from the planet when a moon is farthest from its corresponding planet. This field must be a number greater than 0, but is not required; it may take some time before we are able to measure moon-to-planet distances in extrasolar systems.
- `mass`: the mass of the moon measured in terms of Earth Moon masses. This field must be a numeric value greater than 0, but is not required.

Make sure you also specify any foreign keys necessary to tie each moon to other rows in the database.

###### Solution

```sql
CREATE TABLE moons (
  id SERIAL PRIMARY KEY,
  designation INTEGER NOT NULL CHECK (designation > 0),
  semi_major_axis NUMERIC CHECK (semi_major_axis > 0.0),
  mass NUMERIC CHECK (mass > 0.0),
  planet_id INTEGER NOT NULL REFERENCES planets (id)
);
```

---

### Delete the Database

---

Delete the `extrasolar` database. Use the `psql` console -- don't use the terminal level commands.

###### Solution

Switch to another database:

```sql
\c sql_course
```

Then drop the `extrasolar` database:

```sql
DROP DATABASE extrasolar;
```

Note that `DROP DATABASE` is extremely destructive: there is no confirmation prompt, and no backups are made of the database. Use this statement with extreme care. In particular, it's a good idea to make a backup of your database before you drop it (also called a database dump). You can make a backup like this from the terminal:

```
$ pg_dump --inserts extrasolar > extrasolar.dump.sql
```
