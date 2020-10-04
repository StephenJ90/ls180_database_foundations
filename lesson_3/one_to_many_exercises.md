##### LS180 Database Foundations > Relational Data and JOINs > One to Many Relationships

---

### Practice Problems

---

1. Write a SQL statement to add the following call data to the database:

   | when                | duration | first_name | last_name | number     |
   | :------------------ | :------- | :--------- | :-------- | :--------- |
   | 2016-01-18 14:47:00 | 632      | William    | Swift     | 7204890809 |


```sql
INSERT INTO calls ("when", duration, contact_id) 
VALUES ('2016-01-18 14:47:00', 632, 6);
```

---

2. Write a SQL statement to retrieve the call times, duration, and first name for all calls **not** made to William Swift.

```sql
SELECT "when", duration, first_name 
  FROM calls 
 INNER JOIN contacts
    ON contact_id = contacts.id
 WHERE first_name != 'William' AND last_name != 'Swift';

--OR

SELECT calls.when, calls.duration, contacts.first_name
  FROM calls 
 INNER JOIN contacts
    ON calls.contact_id = contacts.id
 WHERE (contacts.first_name || ' ' || contacts.last_name) != 'William Swift';
```

---

3. Write SQL statements to add the following call data to the database:

   | when                | duration | first_name | last_name | number     |
   | :------------------ | :------- | :--------- | :-------- | :--------- |
   | 2016-01-17 11:52:00 | 175      | Merve      | Elk       | 6343511126 |
   | 2016-01-18 21:22:00 | 79       | Sawa       | Fyodorov  | 6125594874 |

```sql
INSERT INTO contacts (first_name, last_name, number)
VALUES ('Merve', 'Elk', '6343511126'),
('Sawa', 'Fyodorov', '6125594874');

INSERT INTO calls ("when", duration, contact_id)
VALUES ('2016-01-17 11:52:00', 175, 26),
('2016-01-18 21:22:00', 79, 27);
```

---

4. Add a constraint to **contacts** that prevents a duplicate value being added in the column `number`.

```sql
ALTER TABLE contacts ADD UNIQUE (number);

-- OR longform

ALTER TABLE contacts ADD CONSTRAINT number_unique UNIQUE (number);
```

---

5. Write a SQL statement that attempts to insert a duplicate number for a new contact but fails. What error is shown?

```sql
INSERT INTO contacts (first_name, last_name, number)
VALUES ('Johnny', 'Drama', '6125594874');
```

Which produces:

```
ERROR:  duplicate key value violates unique constraint "contacts_number_key"
DETAIL:  Key (number)=(6125594874) already exists.
```

---

6. Why does "when" need to be quoted in many of the queries in this lesson?

It is a reserved word. For a full list, see http://www.postgresql.org/docs/9.5/static/sql-keywords-appendix.html. Note that there are words on that list that are "non-reserved", which means they are allowed as table or column names but are known to the SQL parser as having a special meaning.  

   As a general rule, if you get spurious parser errors for commands that contain any of the listed key words as an identifier you should try to quote the identifier to see if the problem goes away.

