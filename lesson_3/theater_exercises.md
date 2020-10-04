##### LS180 Database Foundations > Relational Data and JOINs > Working with Multiple Tables

---

### Working with Multiple Tables

---

1. Import [this file](https://raw.githubusercontent.com/launchschool/sql_course_data/master/sql-and-relational-databases/relational-data-and-joins/working-with-multiple-tables/theater_full.sql) into an empty PostgreSQL database. Note: the file contains a lot of data and may take a while to run; your terminal should return to the command prompt once the import is complete.

```
\i theater_full.sql

OR 

psql -d entertainment < theater_full.sql
```

---

2. Write a query that determines how many tickets have been sold.

  **Expected Output**

   ```
   count
   -------
   3783
   (1 row)
   ```


```sql
SELECT COUNT(id) FROM tickets;

-- OR

SELECT COUNT(*) FROM tickets;
```

---

3. Write a query that determines how many different customers purchased tickets to at least one event.

```sql
SELECT COUNT(DISTINCT customer_id) FROM tickets;
```

---

4. Write a query that determines what percentage of the customers in the database have purchased a ticket to one or more of the events.

```sql
SELECT COUNT(DISTINCT tickets.customer_id)
       / COUNT(DISTINCT customers.id)::float * 100
       AS percent
  FROM customers
  LEFT OUTER JOIN tickets
    ON tickets.customer_id = customers.id;
```

---

5. Write a query that returns the name of each event and how many tickets were sold for it, in order from popular to least popular.

```sql
SELECT events.name, COUNT(tickets.id) AS popularity 
  FROM events
  LEFT JOIN tickets 
    ON events.id = tickets.event_id 
  GROUP BY events.name 
  ORDER BY popularity DESC;
```

---

6. Write a query that returns the user id, email address, and number of events for all customers that have purchased tickets to three events.

   **Expected Output**

   ```
     id   |                email                 | count
   -------+--------------------------------------+-------
     141  | isac.hayes@herzog.net                |     3
     326  | tatum.mraz@schinner.org              |     3
     624  | adelbert.yost@kleinwisozk.io         |     3
     1719 | lionel.feeney@metzquitzon.biz        |     3
     2058 | angela.ruecker@reichert.co           |     3
     3173 | audra.moore@beierlowe.biz            |     3
     4365 | ephraim.rath@rosenbaum.org           |     3
     6193 | gennaro.rath@mcdermott.co            |     3
     7175 | yolanda.hintz@binskshlerin.com       |     3
     7344 | amaya.goldner@stoltenberg.org        |     3
     7975 | ellen.swaniawski@schultzemmerich.net |     3
     9978 | dayana.kessler@dickinson.io          |     3
   (12 rows)
   ```

```sql
SELECT c.id, c.email, COUNT(DISTINCT t.event_id) FROM customers AS c
  JOIN tickets AS t
    ON t.customer_id = c.id
 GROUP BY c.id
HAVING COUNT(DISTINCT t.event_id) = 3;
```

---

7. Write a query to print out a report of all tickets purchased by the customer with the email address 'gennaro.rath@mcdermott.co'. The report should include the event name and starts_at and the seat's section name, row, and seat number.

   **Expected Output**

   ```
           event        |      starts_at      |    section    | row | seat
    --------------------+---------------------+---------------+-----+------
     Kool-Aid Man       | 2016-06-14 20:00:00 | Lower Balcony | H   |   10
     Kool-Aid Man       | 2016-06-14 20:00:00 | Lower Balcony | H   |   11
     Green Husk Strange | 2016-02-28 18:00:00 | Orchestra     | O   |   14
     Green Husk Strange | 2016-02-28 18:00:00 | Orchestra     | O   |   15
     Green Husk Strange | 2016-02-28 18:00:00 | Orchestra     | O   |   16
     Ultra Archangel IX | 2016-05-23 18:00:00 | Upper Balcony | G   |    7
     Ultra Archangel IX | 2016-05-23 18:00:00 | Upper Balcony | G   |    8
   (7 rows)
   ```

```sql
SELECT e.name AS event,
       e.starts_at,
       sec.name AS section,
       seats.row,
       seats.number AS seat 
  FROM events AS e                                   
  JOIN tickets AS t 
    ON e.id = t.event_id
  JOIN seats
    ON seats.id = t.seat_id 
  JOIN sections AS sec
    ON sec.id = seats.section_id
  JOIN customers AS c 
    ON c.id = t.customer_id
 WHERE c.email = 'gennaro.rath@mcdermott.co';
```
