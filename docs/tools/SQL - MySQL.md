# SQL - MySQL

## 1. **Database Commands**

### Create Database

Creates a new database.

```sql
CREATE DATABASE school;
```

Example: Creates a database named `school`.

---

### Show Databases

Lists all available databases.

```sql
SHOW DATABASES;
```

---

### Use Database

Switches to a specific database.

```sql
USE school;
```

Example: Now all operations will be done inside `school`.

---

### Drop Database

Deletes a database permanently.

```sql
DROP DATABASE school;
```

---

## 2. **Table Commands**

### Create Table

Creates a new table with fields and datatypes.

```sql
CREATE TABLE students (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    age INT,
    grade CHAR(2)
);
```

Example: Creates a `students` table with ID, Name, Age, and Grade.

---

### Show Tables

Lists all tables inside the selected database.

```sql
SHOW TABLES;
```

---

### Describe Table

Displays table structure (columns, types, keys).

```sql
DESCRIBE students;
```

---

### Alter Table

Used to modify table structure.

```sql
ALTER TABLE students ADD email VARCHAR(100);
```

Example: Adds a new column `email`.

```sql
ALTER TABLE students MODIFY name VARCHAR(100);
```

Example: Changes column `name` size.

---

### Drop Table

Deletes a table permanently.

```sql
DROP TABLE students;
```

---

## 3. **CRUD Operations**

### Insert Data

Inserts new records.

```sql
INSERT INTO students (id, name, age, grade) 
VALUES (1, 'John Doe', 20, 'A');
```

---

### Select Data

Retrieves data from a table.

```sql
SELECT * FROM students;
```

Example: Selects all records.

```sql
SELECT name, age FROM students WHERE grade = 'A';
```

Example: Shows only name & age of students with grade 'A'.

---

### Update Data

Updates existing records.

```sql
UPDATE students 
SET grade = 'B' 
WHERE id = 1;
```

Example: Changes grade of student with ID=1 to B.

---

### Delete Data

Deletes records from a table.

```sql
DELETE FROM students WHERE id = 1;
```

Example: Removes student with ID=1.

---

## 4. **Clauses**

### Distinct

Removes duplicate values.

```sql
SELECT DISTINCT grade FROM students;
```

Example: Returns unique grades.

---

### Group By

Groups rows with same values and used with aggregate functions.

```sql
SELECT grade, COUNT(*) 
FROM students 
GROUP BY grade;
```

Example: Counts number of students in each grade.

---

### Order By

Sorts results in ascending (ASC) or descending (DESC).

```sql
SELECT * FROM students ORDER BY age ASC;
```

Example: Lists students by increasing age.

---

### Having

Filters results after grouping.

```sql
SELECT grade, COUNT(*) 
FROM students 
GROUP BY grade 
HAVING COUNT(*) > 2;
```

Example: Shows only grades that have more than 2 students.

---

## 5. **Operators**

### Logical Operators

- **LIKE** → Pattern matching with wildcards.
    

```sql
SELECT * FROM students WHERE name LIKE 'J%';
```

Example: Names starting with J.

- **AND** → Both conditions must be true.
    

```sql
SELECT * FROM students WHERE age > 18 AND grade = 'A';
```

- **OR** → Either condition can be true.
    

```sql
SELECT * FROM students WHERE age < 18 OR grade = 'B';
```

- **NOT** → Negates condition.
    

```sql
SELECT * FROM students WHERE NOT grade = 'F';
```

- **BETWEEN** → Range of values.
    

```sql
SELECT * FROM students WHERE age BETWEEN 18 AND 25;
```

---

### Comparison Operators

- `=` : Equal
    

```sql
SELECT * FROM students WHERE grade = 'A';
```

- `!=` : Not Equal
    

```sql
SELECT * FROM students WHERE grade != 'F';
```

- `<, >` : Less / Greater
    

```sql
SELECT * FROM students WHERE age > 20;
```

- `<=, >=` : Less or Equal / Greater or Equal
    

```sql
SELECT * FROM students WHERE age >= 18;
```

---

With these commands, you can **create, modify, and query databases** effectively.

---
