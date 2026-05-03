# Table Design

## Data Types

<https://www.postgresql.org/docs/current/datatype.html>

- Numeric: smallint, integer, bigint, numeric/decimal, real, double precision, serial
- Monetary: money
- Character: varchar(n), char(n), text
- Binary: bytea
- Date/Time: date, time, timestamp, timestamptz, interval
- Boolean: boolean
- Geometric: point, line, circle, polygon
- Network Address: inet, cidr, macaddr
- JSON: json, jsonb
- Other: uuid, xml, tsvector/tsquery, bit, pg_lsn

## Keys in Tables

- Primary Key (PK)
- Foreign Key (FK)
- Composite Key
- Unique Key
- Candidate Key

## Indexes in Tables

- Single-Column Index
- Composite Index
- Unique Index

## Common Column Constraints

- PRIMARY KEY
- FOREIGN KEY
- UNIQUE
- NOT NULL
- CHECK
- DEFAULT

```sql
CREATE TABLE employees (
    employee_id INTEGER PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone VARCHAR(20),
    hire_date DATE NOT NULL DEFAULT CURRENT_DATE,
    salary DECIMAL(10,2) CHECK (salary >= 0 AND salary <= 1000000),
    department_id INTEGER NOT NULL,
    status VARCHAR(20) DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'on_leave')),
    birth_date DATE CHECK (birth_date < CURRENT_DATE),
    CONSTRAINT fk_department 
        FOREIGN KEY (department_id) 
        REFERENCES departments(department_id)
        ON DELETE RESTRICT
        ON UPDATE CASCADE
);
```
