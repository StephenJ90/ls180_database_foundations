LS180 Database Foundations > SQL Fundamentals > DML, DDL, DCL

---

### DML/DDL/DCL Part 1

---

SQL consists of 3 different sublanguages. For example, one of these sublanguages is called the Data Control Language (DCL) component of SQL. It is used to control access to a database; it is responsible for defining the rights and roles granted to individual users. Common SQL DCL statements include:

```sql
GRANT
REVOKE
```

Name and define the remaining 2 sublanguages, and give at least 2 examples of each.

###### Solution

The Data Manipulation Language (DML) component of SQL is used to create, read, update, and delete the actual data in a database. 

```sql
SELECT
UPDATE
```

The Data Definition Language (DDL) component of SQL is used to create, modify, and delete databases and tables; that is, the DDL is responsible for describing how data is structured. 

```sql
ALTER
CREATE
```

---

### DML/DDL/DCL Part 2

---

Does the following statement use the Data Definition Language (DDL) or the Data Manipulation Language (DML) component of SQL?

```sql
SELECT column_name FROM my_table;
```

###### Solution

DML. `SELECT` merely queries (reads) the data in a database. Since it manipulates the data and not the structure of the data, `SELECT` is part of the DML sublanguage.

---

### DML/DDL/DCL Part 3

---

Does the following statement use the DDL or DML component of SQL?

```sql
CREATE TABLE things (
id serial PRIMARY KEY,
item text NOT NULL UNIQUE,
material text NOT NULL
);
```

###### Solution

DDL. `CREATE TABLE` defines the type of information stored in a database table by describing the data and its attributes. It does not actually manipulate any data, but instead manipulates the data definitions. As such, `CREATE TABLE` statements are part of the DDL sublanguage.

---

### DML/DDL/DCL Part 4

---

Does the following statement use the DDL or DML component of SQL?

```sql
ALTER TABLE things
DROP CONSTRAINT things_item_key;
```

###### Solution

DDL. `ALTER TABLE` modifies the characteristics and attributes of a table. It does not actually manipulate any data, but instead manipulates the data definitions. As such, `ALTER TABLE` statements are part of the DDL sublanguage.

---

### DML/DDL/DCL Part 5

---

Does the following statement use the DDL or DML component of SQL?

```sql
INSERT INTO things VALUES (3, 'Scissors', 'Metal');
```

###### Solution

DML. `INSERT` adds new rows of data into a database. Therefore, it is part of the DML sublanguage since it manipulates the data and not the structure of the data.

---

### DML/DDL/DCL Part 6

---

Does the following statement use the DDL or DML component of SQL?

```sql
UPDATE things
SET material = 'plastic'
WHERE items = 'Cup';
```

###### Solution

DML. `UPDATE` modifies specific rows of data in a database. Therefore, it is part of the DML sublanguage since it manipulates the data and not the structure of the data.

---

### DML/DDL/DCL Part 7

---

Does the following statement use the DDL, DML, or DCL component of SQL?

```sql
\d things
```

###### Solution

None of the above. This is a trick question. `\d` is a `psql` console command, not part of SQL. As such, it is not part of any SQL sublanguage. However, it does act something like a DDL statement -- it displays the schema of the named table.

---

### DML/DDL/DCL Part 8

---

Does the following statement use the DDL or DML component of SQL?

```sql
DELETE FROM things WHERE item = 'Cup';
```

###### Solution

DML. `DELETE` statements delete specific rows of data in a database. Therefore, it is part of the DML sublanguage since it manipulates the data and not the structure of the data.

---

### DML/DDL/DCL Part 9

---

Does the following statement use the DDL or DML component of SQL?

```sql
DROP DATABASE xyzzy;
```

###### Solution

DDL. Depending on how you look at `DROP DATABASE`, you might think it is part of the DML, part of the DDL, or both.

`DROP DATABASE` removes all data from the database, including any and all tables in the database. In this respect, it manipulates data, so some people think of it as part of DML. However, `DROP DATABASE` also deletes everything about the structure of the database and its tables. Furthermore, all variants of the `DROP` statement are generally treated as DDL. In these respects, `DROP DATABASE` manipulates how the data is structured, so it is considered part of the DDL sublanguage. `DROP DATABASE` is DDL since its main purpose is to operate on data definitions; the deletion of data is merely a side-effect.

---

### DML/DDL/DCL Part 10

---

Does the following statement use the DDL or DML component of SQL?

```sql
CREATE SEQUENCE part_number_sequence;
```

###### Solution

`CREATE SEQUENCE` statements modify the characteristics and attributes of a database by adding a sequence object to the database structure. It does not actually manipulate any data, but instead manipulates the data definitions. As such, `CREATE SEQUENCE` statements are part of the DDL sublanguage.

It could also be argued that `CREATE SEQUENCE` is DML; the sequence object it creates is a bit of data that is used to keep track of a sequence of automatically generated values, so it can be thought of as being part of the data instead of a characteristic of the data. However, all `CREATE` statements (not just `CREATE SEQUENCE`) are generally thought of as DDL.
