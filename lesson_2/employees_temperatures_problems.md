##### LS180 Database Foundations > Schema, Data, and SQL > NOT NULL and Default Values

---

### NOT NULL and Default Values

---

##### Practice Problems

1. What is the result of using an operator on a NULL value?

The resulting value will also be NULL, which signifies an unknown value.

---

2. Set the default value of column `department` to "unassigned". Then set the value of the `department` column to "unassigned" for any rows where it has a NULL value. Finally, add a NOT NULL constraint to the `department` column.

```sql
ALTER TABLE employees ALTER COLUMN department SET DEFAULT 'unassigned';
UPDATE employees SET department = 'unassigned' WHERE department IS NULL;
ALTER TABLE employees ALTER COLUMN department SET NOT NULL;
```

---

3. Write the SQL statement to create a table called **temperatures** to hold the following data:

   ```
       date    | low | high
   ------------+-----+------
    2016-03-01 | 34  | 43
    2016-03-02 | 32  | 44
    2016-03-03 | 31  | 47
    2016-03-04 | 33  | 42
    2016-03-05 | 39  | 46
    2016-03-06 | 32  | 43
    2016-03-07 | 29  | 32
    2016-03-08 | 23  | 31
    2016-03-09 | 17  | 28
   ```

   Keep in mind that all rows in the table should always contain all three values.

```sql
CREATE TABLE temperatures (
"date" DATE NOT NULL,
low INTEGER NOT NULL,
high INTEGER NOT NULL
);
```

---

4. Write the SQL statements needed to insert the data shown in #3 into the **temperatures** table.

```sql
INSERT INTO temperatures
VALUES ('2016-03-01', 34, 43),
('2016-03-02', 32, 44),
('2016-03-03', 31, 47),
('2016-03-04', 33, 42),
('2016-03-05', 39, 46),
('2016-03-06', 32, 43),
('2016-03-07', 29, 32),
('2016-03-08', 23, 31),
('2016-03-09', 17, 28);
```

---

5. Write a SQL statement to determine the average (mean) temperature -- divide the sum of the high and low temperatures by two) for each day from March 2, 2016 through March 8, 2016. Make sure to round each average value to one decimal place.

```sql
SELECT date, ROUND((high + low) / 2.0, 1) as average
  FROM temperatures
 WHERE date BETWEEN '2016-03-02' AND '2016-03-08';
```

---

6. Write a SQL statement to add a new column, `rainfall`, to the **temperatures** table. It should store millimetres of rain as integers and have a default value of `0`.

```sql
ALTER TABLE temperatures
ADD COLUMN rainfall INTEGER DEFAULT 0;
```

---

7. Each day, it rains one millimeter for every degree the average temperature goes above 35. Write a SQL statement to update the data in the table **temperatures** to reflect this.

```sql
UPDATE temperatures
   SET rainfall = (high + low) / 2 - 35
 WHERE (high + low) / 2 > 35;
```

---

8. A decision has been made to store rainfall data in inches. Write the SQL statements required to modify the `rainfall` column to reflect these new requirements.

```sql
ALTER TABLE temperatures ALTER COLUMN rainfall TYPE numeric(4,3);
UPDATE temperatures SET rainfall = rainfall * 0.039;
```

--- 

9. Write a SQL statement that renames the **temperatures** table to **weather**.

```sql
ALTER TABLE temperatures RENAME TO weather;
```

---

11. What PostgreSQL program can be used to create a SQL file that contains the SQL commands needed to recreate the current structure and data of the **weather** table?

```
$ pg_dump -d sql-course -t weather --inserts > dump.sql
```

The resulting `dump.sql` file will contain:

    ```sql
    --
    -- PostgreSQL database dump
    --
    
    SET statement_timeout = 0;
    SET lock_timeout = 0;
    SET client_encoding = 'UTF8';
    SET standard_conforming_strings = on;
    SET check_function_bodies = false;
    SET client_min_messages = warning;
    
    SET search_path = public, pg_catalog;
    
    SET default_tablespace = '';
    
    SET default_with_oids = false;
    
    --
    -- Name: weather; Type: TABLE; Schema: public; Owner: instructor; Tablespace:
    --
    
    CREATE TABLE weather (
        date date NOT NULL,
        low integer NOT NULL,
        high integer NOT NULL,
        rainfall numeric(6,3) DEFAULT 0
    );
    
    ALTER TABLE weather OWNER TO instructor;
    
    --
    -- Data for Name: weather; Type: TABLE DATA; Schema: public; Owner: instructor
    --
    
    INSERT INTO weather VALUES ('2016-03-07', 29, 32, 0.000);
    INSERT INTO weather VALUES ('2016-03-08', 23, 31, 0.000);
    INSERT INTO weather VALUES ('2016-03-09', 17, 28, 0.000);
    INSERT INTO weather VALUES ('2016-03-01', 34, 43, 0.117);
    INSERT INTO weather VALUES ('2016-03-02', 32, 44, 0.117);
    INSERT INTO weather VALUES ('2016-03-03', 31, 47, 0.156);
    INSERT INTO weather VALUES ('2016-03-04', 33, 42, 0.078);
    INSERT INTO weather VALUES ('2016-03-05', 39, 46, 0.273);
    INSERT INTO weather VALUES ('2016-03-06', 32, 43, 0.078);
    
    --
    -- PostgreSQL database dump complete
    --
    ```

    If you leave off the `--inserts` argument to `pg_dump`, the resulting SQL statements will still restore the table and its data. The format will just be slightly different, using a `COPY FROM stdin` statement instead of multiple `INSERT` statements:

    ```sql
    COPY weather (date, low, high, rainfall) FROM stdin;
    2016-03-07  29  32  0.000
    2016-03-08  23  31  0.000
    2016-03-09  17  28  0.000
    2016-03-01  34  43  0.117
    2016-03-02  32  44  0.117
    2016-03-03  31  47  0.156
    2016-03-04  33  42  0.078
    2016-03-05  39  46  0.273
    2016-03-06  32  43  0.078
    \.
    ```

    `COPY FROM` is the default for a reason (it is more efficient on large data sets), but using `INSERTS` is also valid and creates SQL statements that are typically the same as those that a user of the database might write themselves when adding data to the table manually.

