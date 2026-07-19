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

## ประเภทข้อมูล (Data Types)
```sql
-- ตัวเลข
INT / INTEGER          -- จำนวนเต็ม (4 bytes)
SMALLINT               -- จำนวนเต็มเล็ก (2 bytes)
BIGINT                 -- จำนวนเต็มใหญ่ (8 bytes)
DECIMAL(10,2)          -- ทศนิยม (ความแม่นยำสูง)
NUMERIC(10,2)          -- ทศนิยม (เทียบเท่า DECIMAL)
FLOAT                  -- ทศนิยม (ความแม่นยำต่ำ)
DOUBLE                 -- ทศนิยม (ความแม่นยำปานกลาง)
REAL                   -- ทศนิยม (ขึ้นอยู่กับ DBMS)

-- ข้อความ
CHAR(10)               -- ข้อความคงที่ความยาว 10
VARCHAR(100)           -- ข้อความเปลี่ยนความยาวสูงสุด 100
TEXT                   -- ข้อความยาวมาก (65,535 chars)
MEDIUMTEXT             -- ข้อความยาวมาก (16MB)
LONGTEXT               -- ข้อความยาวมาก (4GB)

-- วันที่และเวลา
DATE                   -- วันที่ (YYYY-MM-DD)
TIME                   -- เวลา (HH:MM:SS)
DATETIME               -- วันที่และเวลา
TIMESTAMP              -- วันที่และเวลา (auto-update ได้)
YEAR                   -- ปี (YYYY)

-- ประเภทอื่นๆ
BOOLEAN / BOOL         -- ค่าจริง/เท็จ (TRUE/FALSE)
BLOB                   -- ข้อมูลไบนารี (65,535 bytes)
ENUM('a','b','c')      -- ค่าจากตัวเลือกที่กำหนด
JSON                   -- ข้อมูล JSON (MySQL 5.7+, PostgreSQL)
UUID                   -- รหัส UUID (PostgreSQL)
ARRAY                  -- อาร์เรย์ (PostgreSQL)
```

## การแสดงข้อมูลตาราง (Show Table Info)
```sql
SHOW TABLES;                                -- ดูตารางทั้งหมด (MySQL)
SHOW DATABASES;                             -- ดู database ทั้งหมด (MySQL)
DESCRIBE users;                             -- ดูโครงสร้างตาราง (MySQL)
DESC users;                                 -- ดูโครงสร้างตาราง (MySQL, สั้น)
SHOW COLUMNS FROM users;                    -- ดูคอลัมน์ทั้งหมด (MySQL)
SHOW CREATE TABLE users;                    -- ดูคำสั่ง CREATE TABLE (MySQL)
SHOW TABLE STATUS;                          -- ดูสถานะตาราง (MySQL)

-- PostgreSQL
\dt                                         -- ดูตารางทั้งหมด (psql)
\d users                                    -- ดูโครงสร้างตาราง (psql)
SELECT column_name, data_type               -- ดูคอลัมน์ (information_schema)
FROM information_schema.columns
WHERE table_name = 'users';

-- SQLite
.schema users                               -- ดูโครงสร้างตาราง
.tables                                     -- ดูตารางทั้งหมด
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

## ข้อจำกัด (Constraints)
```sql
-- สร้างตารางพร้อม constraints ทั้งหมด
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(50) NOT NULL,
  email VARCHAR(100) NOT NULL,
  age INT,
  status VARCHAR(10) DEFAULT 'active',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

  -- UNIQUE: ห้ามซ้ำ
  CONSTRAINT uq_email UNIQUE (email),

  -- CHECK: เงื่อนไขที่คอลัมน์ต้องผ่าน
  CONSTRAINT chk_age CHECK (age >= 0 AND age <= 150),
  CONSTRAINT chk_status CHECK (status IN ('active', 'inactive', 'banned'))
);

-- Composite Primary Key (PK ที่ประกอบด้วยหลายคอลัมน์)
CREATE TABLE order_items (
  order_id INT,
  product_id INT,
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);

-- เพิ่ม constraints ด้วย ALTER TABLE
ALTER TABLE users ADD CONSTRAINT uq_email UNIQUE (email);
ALTER TABLE users ADD CONSTRAINT chk_age CHECK (age >= 0);
ALTER TABLE users DROP CONSTRAINT uq_email;                -- ลบ constraint
ALTER TABLE users DROP CHECK chk_age;                      -- ลบ CHECK (MySQL)
ALTER TABLE users ADD CONSTRAINT fk_dept                   -- เพิ่ม foreign key
  FOREIGN KEY (dept_id) REFERENCES departments(id);

-- ON DELETE / ON UPDATE actions
CREATE TABLE orders (
  id INT PRIMARY KEY,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id)
    ON DELETE CASCADE       -- ลบ parent → ลบ child ด้วย
    ON UPDATE CASCADE       -- เปลี่ยน PK parent → เปลี่ยน FK child
);
-- ON DELETE SET NULL     -- ลบ parent → FK เป็น NULL
-- ON DELETE SET DEFAULT  -- ลบ parent → FK เป็นค่าเริ่มต้น
-- ON DELETE NO ACTION    -- ห้ามลบถ้ามี child (ค่าเริ่มต้น)
-- ON DELETE RESTRICT     -- เหมือน NO ACTION
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

## การเพิ่มหรืออัปเดตข้อมูล (Upsert)
```sql
-- MySQL: INSERT ถ้าไม่มี, UPDATE ถ้ามีแล้ว
INSERT INTO users (username, email, age)
VALUES ('john', 'john@email.com', 25)
ON DUPLICATE KEY UPDATE age = VALUES(age), email = VALUES(email);

-- PostgreSQL: INSERT ถ้าไม่มี, UPDATE ถ้ามีแล้ว
INSERT INTO users (username, email, age)
VALUES ('john', 'john@email.com', 25)
ON CONFLICT (username) DO UPDATE
SET email = EXCLUDED.email, age = EXCLUDED.age;

-- PostgreSQL: INSERT ถ้าไม่มี, ไม่ทำอะไรถ้ามีแล้ว
INSERT INTO users (username, email, age)
VALUES ('john', 'john@email.com', 25)
ON CONFLICT (username) DO NOTHING;

-- MySQL: REPLACE INTO (ลบแล้วเพิ่มใหม่ถ้ามี)
REPLACE INTO users (username, email, age)
VALUES ('john', 'john@email.com', 25);
```

## การอัปเดตข้อมูลหลายตาราง (Multi-table Update)
```sql
-- MySQL: อัปเดตโดยอ้างอิงตารางอื่น
UPDATE users u
INNER JOIN orders o ON u.id = o.user_id
SET u.last_order_date = o.order_date
WHERE o.amount > 100;

-- PostgreSQL: อัปเดตโดยอ้างอิงตารางอื่น
UPDATE users u
SET last_order_date = o.order_date
FROM orders o
WHERE u.id = o.user_id AND o.amount > 100;
```

## การลบข้อมูลหลายตาราง (Multi-table Delete)
```sql
-- MySQL: ลบโดยอ้างอิงตารางอื่น
DELETE u FROM users u
INNER JOIN orders o ON u.id = o.user_id
WHERE o.amount < 10;

-- PostgreSQL: ลบโดยอ้างอิงตารางอื่น
DELETE FROM users
USING orders
WHERE users.id = orders.user_id AND orders.amount < 10;
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
SELECT * FROM users WHERE age <> 25;           -- ไม่เท่ากับ (อีกรูปแบบ)
SELECT * FROM users WHERE age > 18;            -- มากกว่า
SELECT * FROM users WHERE age >= 18;           -- มากกว่าหรือเท่ากับ
SELECT * FROM users WHERE age < 30;            -- น้อยกว่า
SELECT * FROM users WHERE age <= 30;           -- น้อยกว่าหรือเท่ากับ
SELECT * FROM users WHERE age BETWEEN 18 AND 30; -- อยู่ในช่วง (รวมค่าขอบ)
SELECT * FROM users WHERE age NOT BETWEEN 18 AND 30; -- ไม่อยู่ในช่วง
SELECT * FROM users WHERE username IN ('john', 'jane'); -- อยู่ในรายการ
SELECT * FROM users WHERE username NOT IN ('john', 'jane'); -- ไม่อยู่ในรายการ
SELECT * FROM users WHERE username LIKE 'john%';  -- ตรงกับรูปแบบ
SELECT * FROM users WHERE email LIKE '%@gmail.com'; -- ลงท้ายด้วย
SELECT * FROM users WHERE username LIKE '_ohn';    -- ตรงกับ 1 ตัวอักษร
SELECT * FROM users WHERE username LIKE 'j[oa]hn'; -- ตรงกับ o หรือ a
SELECT * FROM users WHERE phone IS NULL;       -- ค่าว่าง
SELECT * FROM users WHERE phone IS NOT NULL;   -- ไม่ใช่ค่าว่าง

-- AND / OR / NOT
SELECT * FROM users WHERE age > 18 AND status = 'active';
SELECT * FROM users WHERE age < 18 OR age > 65;
SELECT * FROM users WHERE NOT (age < 18);

-- ANY / ALL: เปรียบเทียบกับ subquery
SELECT * FROM users
WHERE age > ANY (SELECT age FROM users WHERE department = 'IT');
-- มากกว่าค่าใดค่าหนึ่งใน subquery

SELECT * FROM users
WHERE age > ALL (SELECT age FROM users WHERE department = 'IT');
-- มากกว่าทุกค่าใน subquery
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
SELECT TRUNCATE(3.14159, 2);                   -- ตัดเศษ (MySQL)
SELECT CEIL(3.1);                              -- ปัดขึ้น
SELECT FLOOR(3.9);                             -- ปัดลง

-- GROUP_CONCAT (MySQL) / STRING_AGG (PostgreSQL): รวมค่าเป็นสตริงเดียว
SELECT department,
  GROUP_CONCAT(username SEPARATOR ', ') AS employees  -- MySQL
FROM users GROUP BY department;

SELECT department,
  STRING_AGG(username, ', ') AS employees              -- PostgreSQL
FROM users GROUP BY department;

-- PERCENTILE_CONT / PERCENTILE_DISC: หาค่าเปอร์เซ็นต์
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median_salary
FROM employees;
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

-- ROLLUP: สรุปย่อยและรวมทุกกลุ่ม
SELECT department, SUM(salary) AS total
FROM employees
GROUP BY ROLLUP(department);
-- ผลลัพธ์จะมีแถว NULL เพิ่มมาซึ่งเป็นยอดรวมทั้งหมด

-- CUBE: ทุก combination ของกลุ่ม
SELECT department, role, SUM(salary)
FROM employees
GROUP BY CUBE(department, role);

-- GROUPING SETS: กำหนดกลุ่มที่ต้องการ sum เอง
SELECT department, role, SUM(salary)
FROM employees
GROUP BY GROUPING SETS (
  (department),
  (role),
  ()
);
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

-- NATURAL JOIN: เชื่อมโยงด้วยคอลัมน์ที่มีชื่อเดียวกันอัตโนมัติ
SELECT * FROM users NATURAL JOIN orders;
-- จะเชื่อมโดยอัตโนมัติตามคอลัมน์ที่มีชื่อเดียวกัน (user_id, id)

-- LATERAL JOIN: subquery ที่อ้างอิงคอลัมน์จากตารางนอก (PostgreSQL)
SELECT u.username, top_orders.product
FROM users u
CROSS JOIN LATERAL (
  SELECT product FROM orders o
  WHERE o.user_id = u.id
  ORDER BY o.amount DESC
  LIMIT 3
) AS top_orders;

-- USING: ย่อการเขียน JOIN (ใช้ได้เมื่อคอลัมน์ชื่อเดียวกัน)
SELECT username, product
FROM users
INNER JOIN orders USING (id);
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

-- WHERE NOT IN
SELECT * FROM users
WHERE id NOT IN (SELECT user_id FROM orders WHERE amount > 100);

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

-- NOT EXISTS: เลือกข้อมูลที่ไม่มีในตารางอื่น
SELECT * FROM users u
WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id);
```

## Index
```sql
CREATE INDEX idx_username ON users(username);              -- สร้าง index
CREATE UNIQUE INDEX idx_email ON users(email);            -- สร้าง unique index
CREATE INDEX idx_name_age ON users(username, age);        -- composite index
CREATE INDEX idx_email_lower ON users((LOWER(email)));    -- functional index
DROP INDEX idx_username ON users;                         -- ลบ index
DROP INDEX idx_username;                                  -- ลบ (PostgreSQL)
SHOW INDEX FROM users;                                    -- ดู index (MySQL)
\d users                                                  -- ดู index (PostgreSQL)

-- Partial Index: index เฉพาะข้อมูลบางส่วน (PostgreSQL)
CREATE INDEX idx_active ON users(email) WHERE status = 'active';

-- Hash Index: เร็วกว่าสำหรับ equality (PostgreSQL)
CREATE INDEX idx_username_hash ON users USING HASH (username);

-- B-Tree Index: ใช้ได้ทั่วไป (ค่าเริ่มต้น)
CREATE INDEX idx_age ON users(age);
```

## View
```sql
CREATE VIEW active_users AS                              -- สร้าง view
SELECT id, username, email FROM users WHERE status = 'active';

CREATE VIEW active_users AS                              -- สร้าง view พร้อม CHECK OPTION
SELECT id, username, email FROM users WHERE status = 'active'
WITH CHECK OPTION;
-- ป้องกันการ INSERT/UPDATE ที่ทำให้ข้อมูลไม่ตรงเงื่อนไข

SELECT * FROM active_users;                              -- ใช้งาน view
DROP VIEW IF EXISTS active_users;                        -- ลบ view
DROP VIEW IF EXISTS active_users CASCADE;                -- ลบ view ที่ depend (PostgreSQL)

-- Materialized View: view ที่เก็บผลลัพธ์จริง (PostgreSQL)
CREATE MATERIALIZED VIEW mv_active_users AS
SELECT id, username, email FROM users WHERE status = 'active';

REFRESH MATERIALIZED VIEW mv_active_users;               -- รีเฟรชข้อมูล
DROP MATERIALIZED VIEW IF EXISTS mv_active_users;         -- ลบ
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

-- SELECT ... FOR UPDATE: ล็อกแถวเพื่อแก้ไข (ป้องกัน race condition)
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE NOWAIT;   -- ล็อกไม่ได้ → error ทันที
SELECT * FROM accounts WHERE id = 1 FOR UPDATE SKIP LOCKED; -- ข้ามแถวที่ถูกล็อก

-- LOCK TABLE: ล็อกตารางทั้งหมด
LOCK TABLE users IN EXCLUSIVE MODE;                      -- ล็อกแบบเขียน
LOCK TABLE users IN SHARE MODE;                          -- ล็อกแบบอ่าน
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

-- NTILE: แบ่งเป็นกลุ่มเท่าๆ กัน
SELECT username, age,
  NTILE(4) OVER (ORDER BY age) AS quartile  -- แบ่งเป็น 4 กลุ่ม
FROM users;

-- FIRST_VALUE / LAST_VALUE / NTH_VALUE: ดึงค่าจาก window
SELECT username, salary, department,
  FIRST_VALUE(salary) OVER (PARTITION BY department ORDER BY salary DESC) AS highest,
  LAST_VALUE(salary) OVER (PARTITION BY department ORDER BY salary DESC
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS lowest,
  NTH_VALUE(salary, 2) OVER (PARTITION BY department ORDER BY salary DESC
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS second_highest
FROM employees;

-- PARTITION BY: จัดกลุ่มใน window
SELECT username, department, salary,
  ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM employees;

-- LAG / LEAD: ค่าก่อนหน้า / ถัดไป
SELECT username, age,
  LAG(age, 1) OVER (ORDER BY age) AS prev_age,
  LEAD(age, 1) OVER (ORDER BY age) AS next_age
FROM users;

-- SUM / AVG window: ผลรวม/ค่าเฉลี่ยแบบ running
SELECT username, amount,
  SUM(amount) OVER (ORDER BY order_date) AS running_total,
  AVG(amount) OVER (PARTITION BY user_id) AS user_avg
FROM orders;

-- PERCENT_RANK / CUME_DIST
SELECT username, age,
  PERCENT_RANK() OVER (ORDER BY age) AS pct_rank,
  CUME_DIST() OVER (ORDER BY age) AS cumulative_dist
FROM users;

-- Window frame: กำหนดช่วงของ window
SELECT username, salary,
  SUM(salary) OVER (
    ORDER BY hire_date
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW  -- 2 แถวก่อนหน้า + ปัจจุบัน
  ) AS moving_sum
FROM employees;
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
SELECT CURRENT_TIMESTAMP;                        -- วันเวลาปัจจุบัน
SELECT LOCALTIMESTAMP;                           -- วันเวลา local
SELECT DATE('2024-01-15 10:30:00');             -- ดึงวันที่
SELECT YEAR(NOW()), MONTH(NOW()), DAY(NOW());   -- ดึงส่วนประกอบ
SELECT HOUR(NOW()), MINUTE(NOW()), SECOND(NOW()); -- ดึงเวลา
SELECT DAYOFWEEK(NOW());                         -- วันในสัปดาห์ (MySQL)
SELECT EXTRACT(YEAR FROM NOW());                 -- ดึงส่วนประกอบ (PostgreSQL)
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY);          -- บวกวัน
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH);        -- ลบเดือน
SELECT ADDDATE(NOW(), INTERVAL 30 DAY);          -- บวกวัน (MySQL)
SELECT DATEDIFF('2024-12-31', '2024-01-01');    -- จำนวนวันระหว่าง
SELECT TIMESTAMPDIFF(MONTH, '2024-01-01', '2024-12-31'); -- จำนวนเดือน (MySQL)
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d');           -- จัดรูปแบบ (MySQL)
SELECT TO_CHAR(NOW(), 'YYYY-MM-DD');             -- จัดรูปแบบ (PostgreSQL)
SELECT STR_TO_DATE('15-01-2024', '%d-%m-%Y');   -- แปลงสตริงเป็นวันที่ (MySQL)
SELECT TO_DATE('15-01-2024', 'DD-MM-YYYY');     -- แปลงสตริงเป็นวันที่ (PostgreSQL)
SELECT LAST_DAY(NOW());                          -- วันสุดท้ายของเดือน
SELECT WEEK(NOW());                              -- สัปดาห์ของปี
SELECT QUARTER(NOW());                           -- ไตรมาส
```

## JSON Functions
```sql
-- MySQL
SELECT JSON_EXTRACT('{"name": "john", "age": 25}', '$.name');  -- ดึงค่า
SELECT '{"name": "john"}' -> '$.name';                        -- ดึงค่า (MySQL 5.7+)
SELECT JSON_SET('{"name": "john"}', '$.age', 25);             -- ตั้งค่า
SELECT JSON_INSERT('{"name": "john"}', '$.age', 25);          -- เพิ่มค่า
SELECT JSON_REPLACE('{"age": 20}', '$.age', 25);              -- แทนที่ค่า
SELECT JSON_REMOVE('{"age": 20, "name": "john"}', '$.age');   -- ลบค่า
SELECT JSON_KEYS('{"name": "john", "age": 25}');              -- ดึง key ทั้งหมด
SELECT JSON_LENGTH('[1, 2, 3]');                               -- ความยาว array
SELECT JSON_VALID('{"name": "john"}');                         -- ตรวจสอบ JSON

-- PostgreSQL
SELECT '{"name": "john", "age": 25}'::json -> 'name';         -- ดึงค่าเป็น text
SELECT '{"name": "john", "age": 25}'::json ->> 'name';        -- ดึงค่าเป็น text
SELECT '{"name": "john"}'::jsonb @? '$.name';                 -- มี key หรือไม่
SELECT jsonb_build_object('name', 'john', 'age', 25);         -- สร้าง JSON
SELECT jsonb_array_elements('[1,2,3]'::jsonb);                -- แตก array

-- SQLite
SELECT json_extract('{"name": "john"}', '$.name');             -- ดึงค่า
SELECT json_type('{"name": "john"}', '$.name');                -- ดึงประเภท
SELECT json_array_length('[1, 2, 3]');                          -- ความยาว array
```

## Utility Functions
```sql
SELECT COALESCE(NULL, 'default');                -- ค่าเริ่มต้นถ้า NULL
SELECT IFNULL(email, 'N/A');                    -- MySQL version
SELECT NVL(email, 'N/A');                       -- Oracle version
SELECT CAST(age AS VARCHAR);                     -- แปลงประเภท
SELECT CONVERT(age, VARCHAR);                    -- แปลงประเภท (MySQL/SQL Server)
SELECT TRY_CAST(age AS INT);                     -- แปลงแบบปลอดภัย (ไม่ error)
SELECT GREATEST(10, 20, 30);                     -- ค่าสูงสุดจากหลายค่า
SELECT LEAST(10, 20, 30);                        -- ค่าต่ำสุดจากหลายค่า

SELECT CASE                                      -- เงื่อนไข
  WHEN age < 18 THEN 'Minor'
  WHEN age >= 18 AND age < 65 THEN 'Adult'
  ELSE 'Senior'
END AS age_group
FROM users;

SELECT IF(age >= 18, 'Adult', 'Minor');          -- MySQL shorthand
SELECT NULLIF(a, b);                             -- คืนค่า NULL ถ้าเท่ากับ

-- Mathematical Functions
SELECT ABS(-10);                                 -- ค่าสัมบูรณ์
SELECT MOD(10, 3);                               -- หารเอาเศษ
SELECT 10 % 3;                                   -- หารเอาเศษ (MySQL)
SELECT POWER(2, 3);                              -- ยกกำลัง
SELECT SQRT(16);                                 -- รากที่สอง
SELECT TRUNCATE(3.14159, 2);                     -- ตัดเศษ
SELECT ROUND(3.14159, 2);                        -- ปัดเศษ
```

## คอลัมน์คำนวณ (Generated Columns)
```sql
-- เก็บผลลัพธ์อัตโนมัติ (MySQL / PostgreSQL)
CREATE TABLE products (
  id INT PRIMARY KEY,
  price DECIMAL(10,2),
  quantity INT,
  total DECIMAL(10,2) GENERATED ALWAYS AS (price * quantity) STORED
);
-- STORED: เก็บจริงในดิ스크 (เร็วในการอ่าน)
-- VIRTUAL: คำนวณตอนอ่าน (ไม่ใช้พื้นที่เก็บข้อมูล)

-- PostgreSQL: Expression Index
CREATE INDEX idx_total ON products((price * quantity));
```

## Sequence
```sql
-- PostgreSQL: สร้าง sequence
CREATE SEQUENCE order_seq START 1 INCREMENT 1;

SELECT nextval('order_seq');                     -- ดึงค่าถัดไป
SELECT currval('order_seq');                     -- ดึงค่าปัจจุบัน
SELECT setval('order_seq', 100);                 -- ตั้งค่า

-- ใช้ sequence ในตาราง
CREATE TABLE orders (
  id INT DEFAULT nextval('order_seq') PRIMARY KEY,
  product VARCHAR(100)
);

-- MySQL: AUTO_INCREMENT (ใช้ในตาราง)
CREATE TABLE orders (
  id INT AUTO_INCREMENT PRIMARY KEY,
  product VARCHAR(100)
);

-- Reset AUTO_INCREMENT
ALTER TABLE orders AUTO_INCREMENT = 1;
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
EXPLAIN SELECT * FROM users WHERE age > 25;              -- ดู query plan
EXPLAIN ANALYZE SELECT * FROM users WHERE age > 25;      -- ดู query plan + runtime (PostgreSQL)
EXPLAIN FORMAT=JSON SELECT * FROM users WHERE age > 25;  -- ดู query plan เป็น JSON
EXPLAIN SELECT * FROM users WHERE age > 25\G             -- ดู query plan แนวตั้ง (MySQL)
ANALYZE TABLE users;                                     -- วิเคราะห์ตาราง (MySQL)
SHOW PROCESSLIST;                                        -- ดู queries ที่กำลังรัน (MySQL)
SHOW ENGINE INNODB STATUS\G                              -- ดูสถานะ InnoDB
KILL <process_id>;                                       -- หยุด query ที่ค้าง
SHOW WARNINGS;                                           -- ดูคำเตือน
SHOW ERRORS;                                             -- ดูข้อผิดพลาด
SHOW STATUS LIKE 'Slow_queries';                         -- ดูจำนวน slow queries
SHOW STATUS LIKE 'Threads_connected';                    -- ดูจำนวน connection

-- Optimize Table (MySQL)
OPTIMIZE TABLE users;                                    -- ปรับปรุงตาราง
ANALYZE TABLE users;                                     -- วิเคราะห์ index

-- Partitioning (PostgreSQL)
CREATE TABLE orders (
  id INT,
  order_date DATE,
  amount DECIMAL
) PARTITION BY RANGE (order_date);

CREATE TABLE orders_2024 PARTITION OF orders
FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

-- List Partitioning (MySQL)
CREATE TABLE employees (
  id INT,
  department VARCHAR(50)
) PARTITION BY LIST COLUMNS(department) (
  PARTITION p_it VALUES IN ('IT'),
  PARTITION p_hr VALUES IN ('HR'),
  PARTITION p_sales VALUES IN ('Sales')
);
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

## Pivot / Unpivot
```sql
-- Pivot (PostgreSQL)
SELECT * FROM crosstab(
  'SELECT department, role, salary FROM employees ORDER BY 1, 2'
) AS ct(department VARCHAR, developer INT, designer INT, manager INT);

-- Pivot (SQL Server)
SELECT * FROM (
  SELECT department, salary FROM employees
) AS src
PIVOT (
  AVG(salary)
  FOR department IN ('IT', 'HR', 'Sales')
) AS pivot_table;

-- Pivot (MySQL ใช้ CASE)
SELECT department,
  AVG(CASE WHEN role = 'Developer' THEN salary END) AS developer,
  AVG(CASE WHEN role = 'Designer' THEN salary END) AS designer,
  AVG(CASE WHEN role = 'Manager' THEN salary END) AS manager
FROM employees
GROUP BY department;

-- Unpivot (SQL Server)
SELECT department, role, salary
FROM employees
UNPIVOT (
  salary FOR role IN (developer, designer, manager)
) AS unpivot_table;

-- Unpivot (MySQL ใช้ UNION ALL)
SELECT department, 'developer' AS role, developer AS salary FROM employees
UNION ALL
SELECT department, 'designer', designer FROM employees
UNION ALL
SELECT department, 'manager', manager FROM employees;
```
