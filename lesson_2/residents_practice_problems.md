##### LS180 Database Foundations > Schema, Data, and SQL > More Single Table Queries

---

### More Single Table Queries

---

1. Create a new database called `residents` using the PostgreSQL command line tools.

```
$ createdb residents
```

2. Load [this file](https://raw.githubusercontent.com/launchschool/sql_course_data/master/sql-and-relational-databases/schema-data-and-sql/more-single-table-queries/residents_with_data.sql) into the database created in #1.

```
psql -d residents < residents_with_data.sql
```

3. Write a SQL query to list the ten states with the most rows in the `people` table in descending order.


```sql
SELECT state, count(state)
  FROM people
  GROUP BY state
  ORDER BY count(state) DESC
  LIMIT 10;
```

4. Write a SQL query that lists each domain used in an email address in the `people` table and how many people in the database have an email address containing that domain. Domains should be listed with the most popular first.

```sql
SELECT substr(email, strpos(email, '@') + 1) as domain, COUNT(id) 
  FROM people 
  GROUP BY domain 
  ORDER BY count DESC;
```

5. Write a SQL statement that will delete the person with ID `3399` from the `people` table.

```sql
DELETE FROM people WHERE id = 3399;
```

6. Write a SQL statement that will delete all users that are located in the state of California (`CA`).

```sql
DELETE FROM people WHERE state = 'CA';
```

7. Write a SQL statement that will update the `given_name` values to be all uppercase for all users with an email address that contains `teleworm.us`.

```sql
UPDATE people SET given_name = UPPER(given_name) 
 WHERE email LIKE '%teleworm.us';
```

8. Write a SQL statement that will delete all rows from the `people` table.

   ###### My Response:

   ```sql
   DELETE FROM people;
   ```