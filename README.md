This is a practical implementation in MySQL of Jeff Atwood's article [A Visual Explanation of SQL Joins](http://blog.codinghorror.com/a-visual-explanation-of-sql-joins/).

It steps you through the creation of the database tables and data as well as providing the MySQL equivalent queries to reproduce the same results. The article mentions using **FULL OUTER JOIN**  which is [not possible in MySQL](http://stackoverflow.com/questions/4796872/full-outer-join-in-mysql) so an emulation of this join is used instead.

#Database Table and Data Creation

##Create database

```sql
# Create and use database.
CREATE schema join_exercise;
USE join_exercise;
```

##Create Tables and Insert Data.

```sql
# Create TableA.
CREATE table TableA (
id INT(1) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(20));

# Insert data into TableA.
INSERT INTO TableA (name)
VALUES ('Pirate'), ('Monkey'), ('Ninja'), ('Spaghetti');
```

```sql
# Create TableB.
CREATE table TableB (
id INT(1) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(20));

# Insert data into TableB.
INSERT INTO TableB (name)
VALUES ('Rutabaga'), ('Pirate'), ('Darth Vader'), ('Ninja');
```

#MySQL Queries

Inner join produces only the set of records that match in both Table A and Table B.

```sql
SELECT * FROM TableA
INNER JOIN TableB
ON TableA.name = TableB.name;
```

Full outer join produces the set of all records in Table A and Table B, with matching records from both sides where available. If there is no match, the missing side will contain null.

```sql
SELECT * FROM TableA
LEFT JOIN TableB ON TableA.name = TableB.name
UNION
SELECT * FROM TableA
RIGHT JOIN TableB ON TableA.name = TableB.name;
```

Left outer join produces a complete set of records from Table A, with the matching records (where available) in Table B. If there is no match, the right side will contain null.

```sql
SELECT * FROM TableA
LEFT OUTER JOIN TableB
ON TableA.name = TableB.name;
```

To produce the set of records only in Table A, but not in Table B, we perform the same left outer join, then exclude the records we don't want from the right side via a where clause.

```sql
SELECT * FROM TableA
LEFT OUTER JOIN TableB
ON TableA.name = TableB.name
WHERE TableB.id IS NULL;
```

To produce the set of records unique to Table A and Table B, we perform the same full outer join, then exclude the records we don't want from both sides via a where clause.

```sql
SELECT * FROM TableA
LEFT JOIN TableB ON TableA.name = TableB.name
WHERE TableA.id IS NULL OR TableB.id IS NULL
UNION
SELECT * FROM TableA
RIGHT JOIN TableB ON TableA.name = TableB.name
WHERE TableA.id IS NULL OR TableB.id IS NULL;
```
