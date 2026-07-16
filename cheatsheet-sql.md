# SQL Cheatsheet

## การตั้งค่าและเชื่อมต่อ (Setup & Connection)
```sql
-- MySQL
mysql -u root -p

-- PostgreSQL
psql -U username -d database_name

-- SQLite
sqlite3 database.db
```

## การสร้างฐานข้อมูล (Database)
```sql
CREATE DATABASE mydb;                          -- สร้าง database ใหม่
DROP DATABASE mydb;                            -- ลบ database
USE mydb;                                      -- เลือก database
SHOW DATABASES;                                -- ดู database ทั้งหมด (MySQL)
SELECT current_database();                     -- ดู database ปัจจุบัน (PostgreSQL)
```

## การสร้างตาราง (Create Table)
```sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,           -- คอลัมน์ ID หลัก
  username VARCHAR(50) NOT NULL UNIQUE,        -- ชื่อผู้ใช้ ห้ามซ้ำ
  email VARCHAR(100) NOT NULL,                 -- อีเมล
  age INT DEFAULT 0,                           -- อายุ ค่าเริ่มต้น 0
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- สร้างตารางพร้อม foreign key
CREATE TABLE orders (
  id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT,
  product VARCHAR(100),
  amount DECIMAL(10,2),
  order_date DATE,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## การแก้ไขโครงสร้างตาราง (Alter Table)
```sql
ALTER TABLE users ADD phone VARCHAR(20);       -- เพิ่มคอลัมน์
ALTER TABLE users DROP COLUMN phone;           -- ลบคอลัมน์
ALTER TABLE users MODIFY email VARCHAR(200);   -- เปลี่ยนประเภทข้อมูล
ALTER TABLE users CHANGE phone tel VARCHAR(20);-- เปลี่ยนชื่อคอลัมน์
ALTER TABLE users ADD PRIMARY KEY (id);        -- เพิ่ม primary key
ALTER TABLE users DROP PRIMARY KEY;            -- ลบ primary key
ALTER TABLE users RENAME TO members;           -- เปลี่ยนชื่อตาราง
DROP TABLE users;                              -- ลบตาราง
TRUNCATE TABLE users;                          -- ลบข้อมูลทั้งหมด (เร็วกว่า DELETE)
```

## การเพิ่มข้อมูล (Insert)
```sql
INSERT INTO users (username, email, age)       -- เพิ่มข้อมูล 1 แถว
VALUES ('john', 'john@email.com', 25);

INSERT INTO users (username, email, age)       -- เพิ่มข้อมูลหลายแถว
VALUES
  ('john', 'john@email.com', 25),
  ('jane', 'jane@email.com', 30),
  ('bob', 'bob@email.com', 28);

INSERT INTO users (username, email)            -- เพิ่มจากตารางอื่น
SELECT username, email FROM temp_users;
```

## การอัปเดตข้อมูล (Update)
```sql
UPDATE users SET age = 26 WHERE username = 'john';  -- อัปเดตข้อมูล
UPDATE users SET age = age + 1;                     -- อัปเดตทุกแถว
UPDATE users SET age = 25, email = 'new@email.com'  -- อัปเดตหลายคอลัมน์
WHERE id = 1;
```

## การลบข้อมูล (Delete)
```sql
DELETE FROM users WHERE username = 'john';     -- ลบข้อมูลเฉพาะ
DELETE FROM users WHERE age < 18;              -- ลบตามเงื่อนไข
DELETE FROM users;                             -- ลบข้อมูลทั้งหมด (保留โครงสร้าง)
```

## การเลือกข้อมูล (Select)
```sql
SELECT * FROM users;                           -- เลือกทุกคอลัมน์
SELECT username, email FROM users;             -- เลือกเฉพาะคอลัมน์
SELECT DISTINCT age FROM users;                -- เลือกค่าที่ไม่ซ้ำ
SELECT username AS name, email AS contact      -- เปลี่ยนชื่อคอลัมน์
FROM users;
SELECT 1 + 1 AS result;                        -- คำนวณค่า
```

## เงื่อนไข (Where)
```sql
SELECT * FROM users WHERE age = 25;            -- เท่ากับ
SELECT * FROM users WHERE age != 25;           -- ไม่เท่ากับ
SELECT * FROM users WHERE age > 18;            -- มากกว่า
SELECT * FROM users WHERE age >= 18;           -- มากกว่าหรือเท่ากับ
SELECT * FROM users WHERE age < 30;            -- น้อยกว่า
SELECT * FROM users WHERE age BETWEEN 18 AND 30; -- อยู่ในช่วง
SELECT * FROM users WHERE username IN ('john', 'jane'); -- อยู่ในรายการ
SELECT * FROM users WHERE username LIKE 'john%';  -- ตรงกับรูปแบบ
SELECT * FROM users WHERE email LIKE '%@gmail.com'; -- ลงท้ายด้วย
SELECT * FROM users WHERE username LIKE '_ohn';    -- ตรงกับ 1 ตัวอักษร
SELECT * FROM users WHERE phone IS NULL;       -- ค่าว่าง
SELECT * FROM users WHERE phone IS NOT NULL;   -- ไม่ใช่ค่าว่าง
```

## การเรียงลำดับ (Order By)
```sql
SELECT * FROM users ORDER BY age ASC;          -- เรียงจากน้อยไปมาก
SELECT * FROM users ORDER BY age DESC;         -- เรียงจากมากไปน้อย
SELECT * FROM users ORDER BY age DESC, username ASC; -- เรียงหลายคอลัมน์
```

## การจำกัดผลลัพธ์ (Limit / Offset)
```sql
SELECT * FROM users LIMIT 10;                  -- เลือก 10 แถวแรก
SELECT * FROM users LIMIT 10 OFFSET 20;       -- ข้าม 20 แถว แล้วเลือก 10 แถว
SELECT * FROM users LIMIT 20, 10;              -- แบบ MySQL (skip, count)
SELECT TOP 10 * FROM users;                    -- SQL Server
SELECT * FROM users FETCH FIRST 10 ROWS ONLY; -- Oracle / PostgreSQL 12+
```

## ฟังก์ชัน (Aggregate Functions)
```sql
SELECT COUNT(*) FROM users;                    -- นับจำนวนแถว
SELECT COUNT(DISTINCT age) FROM users;         -- นับค่าที่ไม่ซ้ำ
SELECT SUM(amount) FROM orders;                -- รวมค่า
SELECT AVG(age) FROM users;                    -- ค่าเฉลี่ย
SELECT MAX(age) FROM users;                    -- ค่าสูงสุด
SELECT MIN(age) FROM users;                    -- ค่าต่ำสุด
SELECT ROUND(AVG(age), 2) FROM users;          -- ปัดเศษ 2 ตำแหน่ง
```

## การจัดกลุ่ม (Group By)
```sql
SELECT age, COUNT(*) FROM users                -- จัดกลุ่มและนับ
GROUP BY age;

SELECT department, AVG(salary)                 -- จัดกลุ่มพร้อม aggregate
FROM employees
GROUP BY department;

SELECT department, AVG(salary)                 -- กรองหลังจัดกลุ่ม
FROM employees
GROUP BY department
HAVING AVG(salary) > 50000;

SELECT department, COUNT(*) as emp_count       -- ตั้งชื่อ aggregate column
FROM employees
GROUP BY department
HAVING COUNT(*) > 5
ORDER BY emp_count DESC;
```

## การเชื่อมโยงตาราง (Joins)
```sql
-- INNER JOIN: เฉพาะข้อมูลที่ตรงกันทั้งสองตาราง
SELECT users.username, orders.product
FROM users
INNER JOIN orders ON users.id = orders.user_id;

-- LEFT JOIN: ทุกข้อมูลจากตารางซ้าย + ข้อมูลที่ตรงกัน
SELECT users.username, orders.product
FROM users
LEFT JOIN orders ON users.id = orders.user_id;

-- RIGHT JOIN: ทุกข้อมูลจากตารางขวา + ข้อมูลที่ตรงกัน
SELECT users.username, orders.product
FROM users
RIGHT JOIN orders ON users.id = orders.user_id;

-- FULL OUTER JOIN: ทุกข้อมูลจากทั้งสองตาราง
SELECT users.username, orders.product
FROM users
FULL OUTER JOIN orders ON users.id = orders.user_id;

-- CROSS JOIN: ทุก combination ของทั้งสองตาราง
SELECT users.username, products.name
FROM users
CROSS JOIN products;

-- SELF JOIN: เชื่อมโยงตารางกับตัวเอง
SELECT a.username AS employee, b.username AS manager
FROM users a
INNER JOIN users b ON a.manager_id = b.id;
```

## ตัวดำเนินการ Combine (Set Operations)
```sql
-- UNION: รวมผลลัพธ์ (ไม่ซ้ำ)
SELECT username FROM users1
UNION
SELECT username FROM users2;

-- UNION ALL: รวมผลลัพธ์ (ซ้ำได้)
SELECT username FROM users1
UNION ALL
SELECT username FROM users2;

-- INTERSECT: ข้อมูลที่มีร่วมกัน
SELECT username FROM users1
INTERSECT
SELECT username FROM users2;

-- EXCEPT / MINUS: ข้อมูลที่ต่างกัน
SELECT username FROM users1
EXCEPT
SELECT username FROM users2;
```

## Subquery (ย่อย)
```sql
-- WHERE subquery
SELECT * FROM users
WHERE id IN (SELECT user_id FROM orders WHERE amount > 100);

-- FROM subquery (derived table)
SELECT avg_amount FROM (
  SELECT user_id, AVG(amount) AS avg_amount
  FROM orders
  GROUP BY user_id
) AS user_avg
WHERE avg_amount > 500;

-- SELECT subquery (scalar)
SELECT username, (SELECT COUNT(*) FROM orders WHERE orders.user_id = users.id) AS order_count
FROM users;

-- EXISTS
SELECT * FROM users
WHERE EXISTS (SELECT 1 FROM orders WHERE orders.user_id = users.id);
```

## Index
```sql
CREATE INDEX idx_username ON users(username);              -- สร้าง index
CREATE UNIQUE INDEX idx_email ON users(email);            -- สร้าง unique index
CREATE INDEX idx_name_age ON users(username, age);        -- composite index
DROP INDEX idx_username ON users;                         -- ลบ index
SHOW INDEX FROM users;                                    -- ดู index (MySQL)
```

## View
```sql
CREATE VIEW active_users AS                              -- สร้าง view
SELECT id, username, email FROM users WHERE status = 'active';

SELECT * FROM active_users;                              -- ใช้งาน view
DROP VIEW IF EXISTS active_users;                        -- ลบ view
```

## Transaction
```sql
START TRANSACTION;                                       -- เริ่ม transaction
BEGIN;                                                   -- เริ่ม (PostgreSQL)
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;                                                  -- ยืนยันการเปลี่ยนแปลง
ROLLBACK;                                                -- ยกเลิกการเปลี่ยนแปลง
SAVEPOINT sp1;                                           -- สร้าง savepoint
ROLLBACK TO SAVEPOINT sp1;                              -- ย้อนกลับไป savepoint
RELEASE SAVEPOINT sp1;                                   -- ลบ savepoint

-- Transaction Isolation Levels
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;        -- ต่ำสุด (最快的)
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;          -- ค่าเริ่มต้น
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;         -- ป้องกัน dirty read
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;            -- สูงสุด (ช้าที่สุด)
```

## Stored Procedure
```sql
-- สร้าง stored procedure (MySQL)
DELIMITER //
CREATE PROCEDURE GetUser(IN userId INT)
BEGIN
  SELECT * FROM users WHERE id = userId;
END //
DELIMITER ;

-- เรียกใช้
CALL GetUser(1);

-- ลบ
DROP PROCEDURE IF EXISTS GetUser;

-- สร้าง stored procedure (PostgreSQL)
CREATE FUNCTION GetUser(userId INT)
RETURNS TABLE(id INT, username VARCHAR)
AS $$
BEGIN
  RETURN QUERY SELECT u.id, u.username FROM users u WHERE u.id = userId;
END;
$$ LANGUAGE plpgsql;
```

## Trigger
```sql
-- MySQL
CREATE TRIGGER before_insert_user
BEFORE INSERT ON users
FOR EACH ROW
BEGIN
  SET NEW.created_at = NOW();
END;

-- PostgreSQL
CREATE FUNCTION update_timestamp()
RETURNS TRIGGER AS $$
BEGIN
  NEW.created_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER before_insert_user
BEFORE INSERT ON users
FOR EACH ROW
EXECUTE FUNCTION update_timestamp();
```

## Window Functions
```sql
-- ROW_NUMBER: ลำดับที่ไม่ซ้ำ
SELECT username, age,
  ROW_NUMBER() OVER (ORDER BY age DESC) AS rank_num
FROM users;

-- RANK: ลำดับที่ซ้ำได้ (ข้ามอันดับ)
SELECT username, age,
  RANK() OVER (ORDER BY age DESC) AS rank_num
FROM users;

-- DENSE_RANK: ลำดับที่ซ้ำได้ (ไม่ข้ามอันดับ)
SELECT username, age,
  DENSE_RANK() OVER (ORDER BY age DESC) AS rank_num
FROM users;

-- PARTITION BY: จัดกลุ่มใน window
SELECT username, department, salary,
  ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM employees;

-- LAG / LEAD: ค่าก่อนหน้า / ถัดไป
SELECT username, age,
  LAG(age, 1) OVER (ORDER BY age) AS prev_age,
  LEAD(age, 1) OVER (ORDER BY age) AS next_age
FROM users;

-- SUM / AVG window
SELECT username, amount,
  SUM(amount) OVER (ORDER BY order_date) AS running_total,
  AVG(amount) OVER (PARTITION BY user_id) AS user_avg
FROM orders;
```

## String Functions
```sql
SELECT CONCAT('Hello', ' ', 'World');          -- ต่อสตริง
SELECT LENGTH('Hello');                         -- ความยาวสตริง
SELECT UPPER('hello');                           -- ตัวพิมพ์ใหญ่
SELECT LOWER('HELLO');                           -- ตัวพิมพ์เล็ก
SELECT TRIM('  hello  ');                        -- ตัดช่องว่าง
SELECT SUBSTRING('Hello', 1, 3);                -- ตัดสตริง
SELECT REPLACE('Hello World', 'World', 'SQL');  -- แทนที่สตริง
SELECT REVERSE('Hello');                         -- กลับสตริง
SELECT LEFT('Hello', 3);                        -- ดึงซ้าย
SELECT RIGHT('Hello', 3);                       -- ดึงขวา
SELECT LPAD('5', 3, '0');                       -- เติมซ้าย
SELECT RPAD('5', 3, '0');                       -- เติมขวา
```

## Date Functions
```sql
SELECT NOW();                                    -- วันเวลาปัจจุบัน
SELECT CURRENT_DATE;                             -- วันที่ปัจจุบัน
SELECT CURRENT_TIME;                             -- เวลาปัจจุบัน
SELECT DATE('2024-01-15 10:30:00');             -- ดึงวันที่
SELECT YEAR(NOW()), MONTH(NOW()), DAY(NOW());   -- ดึงส่วนประกอบ
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY);          -- บวกวัน
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH);        -- ลบเดือน
SELECT DATEDIFF('2024-12-31', '2024-01-01');    -- จำนวนวันระหว่าง
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d');           -- จัดรูปแบบ (MySQL)
SELECT TO_CHAR(NOW(), 'YYYY-MM-DD');             -- จัดรูปแบบ (PostgreSQL)
```

## Utility Functions
```sql
SELECT COALESCE(NULL, 'default');                -- ค่าเริ่มต้นถ้า NULL
SELECT IFNULL(email, 'N/A');                    -- MySQL version
SELECT NVL(email, 'N/A');                       -- Oracle version
SELECT CAST(age AS VARCHAR);                     -- แปลงประเภท
SELECT CASE                                      -- เงื่อนไข
  WHEN age < 18 THEN 'Minor'
  WHEN age >= 18 AND age < 65 THEN 'Adult'
  ELSE 'Senior'
END AS age_group
FROM users;

SELECT IF(age >= 18, 'Adult', 'Minor');          -- MySQL shorthand
SELECT NULLIF(a, b);                             -- คืนค่า NULL ถ้าเท่ากับ
```

## User Management (MySQL)
```sql
CREATE USER 'newuser'@'localhost'               -- สร้าง user
IDENTIFIED BY 'password';

GRANT ALL PRIVILEGES ON mydb.*                  -- ให้สิทธิ์ทั้งหมด
TO 'newuser'@'localhost';

GRANT SELECT, INSERT ON mydb.users              -- ให้สิทธิ์เฉพาะ
TO 'newuser'@'localhost';

REVOKE INSERT ON mydb.users                     -- ถอนสิทธิ์
FROM 'newuser'@'localhost';

SHOW GRANTS FOR 'newuser'@'localhost';          -- ดูสิทธิ์
FLUSH PRIVILEGES;                               -- รีเฟรชสิทธิ์
DROP USER 'newuser'@'localhost';                -- ลบ user
```

## Backup & Restore
```sql
-- MySQL backup
mysqldump -u root -p mydb > backup.sql

-- MySQL restore
mysql -u root -p mydb < backup.sql

-- PostgreSQL backup
pg_dump -U username mydb > backup.sql

-- PostgreSQL restore
psql -U username mydb < backup.sql

-- SQLite backup
sqlite3 mydb.db .dump > backup.sql

-- SQLite restore
sqlite3 mydb.db < backup.sql
```

## Performance Tips
```sql
EXPLAIN SELECT * FROM users WHERE age > 25;     -- ดู query plan
ANALYZE TABLE users;                            -- วิเคราะห์ตาราง (MySQL)
SHOW PROCESSLIST;                               -- ดู queries ที่กำลังรัน (MySQL)
SHOW ENGINE INNODB STATUS\G                     -- ดูสถานะ InnoDB
KILL <process_id>;                              -- หยุด query ที่ค้าง

-- Partitioning (PostgreSQL)
CREATE TABLE orders (
  id INT,
  order_date DATE,
  amount DECIMAL
) PARTITION BY RANGE (order_date);

CREATE TABLE orders_2024 PARTITION OF orders
FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
```

## Common Table Expressions (CTE)
```sql
-- CTE พื้นฐาน
WITH active_users AS (
  SELECT id, username FROM users WHERE status = 'active'
)
SELECT * FROM active_users WHERE username LIKE 'john%';

-- Recursive CTE
WITH RECURSIVE category_tree AS (
  SELECT id, name, parent_id, 0 AS depth
  FROM categories WHERE parent_id IS NULL
  UNION ALL
  SELECT c.id, c.name, c.parent_id, ct.depth + 1
  FROM categories c
  INNER JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree;
```

## Pivot (MySQL 8.0+)
```sql
SELECT * FROM (
  SELECT department, salary
  FROM employees
) AS src
PIVOT (
  AVG(salary)
  FOR department IN ('IT', 'HR', 'Sales')
) AS pivot_table;
```
