---
title: MySQL的常用查询语句集锦
date: 2024-11-13 20:46:55
tags:
- MySQL
categories:
- MySQL
---

最近参加了一些传统企业的面试，通常都会写一份笔试题，和大厂不一样，他们是真的笔试题（纸质试卷，考察技术基础题：比如语言特性、计算机体系结构、SQL等），虽然说是技术基础，但是很多题目难度还是很高的，比如MySQL的查询语句，不仅考察基础知识，还考察如何解决复杂的业务问题。故此，我翻阅了一些博客和书籍，记录了一些典型的MySQL查询场景，提供建表语句和解答，而且难度是依次递增，非常有趣。



## 1. 查询条件和排序

### 题目 1：查找价格大于 50 的产品，并按照创建日期降序排列

#### 建表语句

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(50),
    price DECIMAL(10, 2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO products (product_name, price) VALUES 
('Product A', 55.50), 
('Product B', 45.00), 
('Product C', 60.00);
```

#### 查询语句

```sql
SELECT product_name, price 
FROM products 
WHERE price > 50 
ORDER BY created_at DESC;
```

## 2. 复杂条件查询

### 题目 2：查询在2023年内购买的订单，且订单金额在100到500之间的订单记录

#### 建表语句

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    order_date DATE,
    amount DECIMAL(10, 2)
);

INSERT INTO orders (order_date, amount) VALUES 
('2023-02-15', 120.00), 
('2023-07-19', 300.00), 
('2022-10-10', 250.00),
('2023-05-10', 95.00);
```

#### 查询语句

```sql
SELECT order_id, amount 
FROM orders 
WHERE YEAR(order_date) = 2023 
AND amount BETWEEN 100 AND 500;
```

## 3. 分组和聚合函数

### 题目 3：按客户统计订单数量，并筛选出订单数量超过 2 的客户

#### 建表语句

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_name VARCHAR(50)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

INSERT INTO customers (customer_name) VALUES 
('Alice'), 
('Bob'), 
('Charlie');

INSERT INTO orders (customer_id, order_date) VALUES 
(1, '2023-05-01'), 
(1, '2023-06-15'), 
(2, '2023-07-20'), 
(2, '2023-08-10'), 
(1, '2023-09-01');
```

#### 查询语句

```sql
SELECT customer_id, COUNT(order_id) AS order_count 
FROM orders 
GROUP BY customer_id 
HAVING order_count > 2;
```

## 4. 联表查询（JOIN）

### 题目 4：查询所有订单信息，包括每个订单的客户姓名

#### 建表语句

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_name VARCHAR(50)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

INSERT INTO customers (customer_name) VALUES 
('Alice'), 
('Bob');

INSERT INTO orders (customer_id, order_date) VALUES 
(1, '2023-05-01'), 
(2, '2023-06-15');
```

#### 查询语句

```sql
SELECT o.order_id, o.order_date, c.customer_name 
FROM orders o 
JOIN customers c ON o.customer_id = c.customer_id;
```

## 5. 子查询和嵌套查询

### 题目 5：查询订单总金额排名第二的客户姓名

#### 建表语句

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_name VARCHAR(50)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    amount DECIMAL(10, 2)
);

INSERT INTO customers (customer_name) VALUES 
('Alice'), 
('Bob'), 
('Charlie');

INSERT INTO orders (customer_id, amount) VALUES 
(1, 120.00), 
(2, 300.00), 
(1, 200.00), 
(3, 250.00);
```

#### 查询语句

```sql
SELECT customer_name 
FROM customers 
WHERE customer_id = (
    SELECT customer_id 
    FROM orders 
    GROUP BY customer_id 
    ORDER BY SUM(amount) DESC 
    LIMIT 1 OFFSET 1
);
```

## 6. 窗口函数

### 题目 6：对于每个客户，按订单日期排序，获取每个订单的累计金额

#### 建表语句

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    order_date DATE,
    amount DECIMAL(10, 2)
);

INSERT INTO orders (customer_id, order_date, amount) VALUES 
(1, '2023-05-01', 120.00), 
(1, '2023-06-15', 200.00), 
(2, '2023-07-20', 300.00), 
(1, '2023-09-01', 150.00);
```

#### 查询语句

```sql
SELECT customer_id, order_date, amount,
       SUM(amount) OVER(PARTITION BY customer_id ORDER BY order_date) AS cumulative_amount
FROM orders;
```

注意，以上是累计金额。



以上就是关于MySQL查询语句的部分面试题示例。每道题都从不同的场景入手，涵盖了条件查询、排序、分组、联表、子查询、窗口函数等内容，希望能帮助你加深对MySQL复杂查询的理解。



继续进阶

## 7. 窗口函数排名

### 题目 7：查找每个部门中薪资排名前 2 的员工

#### 建表语句

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    employee_name VARCHAR(50),
    department_id INT,
    salary DECIMAL(10, 2)
);

INSERT INTO employees (employee_name, department_id, salary) VALUES 
('Alice', 1, 8000.00), 
('Bob', 1, 7500.00), 
('Charlie', 1, 9000.00),
('Dave', 2, 6000.00), 
('Eve', 2, 8500.00), 
('Frank', 2, 7000.00);
```

#### 查询语句

```sql
SELECT employee_name, department_id, salary 
FROM (
    SELECT employee_name, department_id, salary,
           ROW_NUMBER() OVER(PARTITION BY department_id ORDER BY salary DESC) AS rank
    FROM employees
) AS ranked_employees 
WHERE rank <= 2;
```

---

## 8. 递归查询（层级结构）

### 题目 8：查询员工的所有上级，要求从直接上级到最高上级按顺序排列

#### 建表语句

```sql
CREATE TABLE hierarchy (
    employee_id INT PRIMARY KEY,
    manager_id INT
);

INSERT INTO hierarchy (employee_id, manager_id) VALUES 
(1, NULL),   -- CEO (最高管理层)
(2, 1),      -- 直接上级为 CEO
(3, 2),      -- 上级为 2
(4, 2), 
(5, 3);
```

#### 查询语句

```sql
WITH RECURSIVE managers AS (
    SELECT employee_id, manager_id, 0 AS level
    FROM hierarchy
    WHERE employee_id = 5  -- 假设要查询员工 ID 为 5 的所有上级
    UNION ALL
    SELECT h.employee_id, h.manager_id, m.level + 1
    FROM hierarchy h
    JOIN managers m ON h.employee_id = m.manager_id
)
SELECT employee_id, manager_id, level
FROM managers
ORDER BY level;
```

---

## 9. 复杂联表查询和条件过滤

### 题目 9：查找所有项目中参与人数超过 5 人，且项目完成时间少于 3 个月的项目

#### 建表语句

```sql
CREATE TABLE projects (
    project_id INT PRIMARY KEY,
    project_name VARCHAR(100),
    start_date DATE,
    end_date DATE
);

CREATE TABLE project_assignments (
    project_id INT,
    employee_id INT,
    PRIMARY KEY (project_id, employee_id)
);

INSERT INTO projects (project_id, project_name, start_date, end_date) VALUES 
(1, 'Project Alpha', '2023-01-01', '2023-03-01'), 
(2, 'Project Beta', '2023-02-01', '2023-05-01');

INSERT INTO project_assignments (project_id, employee_id) VALUES 
(1, 1), (1, 2), (1, 3), (1, 4), (1, 5), (1, 6), 
(2, 1), (2, 3), (2, 5);
```

#### 查询语句

```sql
SELECT p.project_name
FROM projects p
JOIN project_assignments pa ON p.project_id = pa.project_id
GROUP BY p.project_id, p.project_name
HAVING COUNT(pa.employee_id) > 5
   AND DATEDIFF(p.end_date, p.start_date) < 90;
```

---

## 10. 组合查询与聚合函数

### 题目 10：统计每个月新增客户数和订单总额，显示月份、客户数、订单金额，并按月份排序

#### 建表语句

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_name VARCHAR(50),
    registration_date DATE
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    order_date DATE,
    amount DECIMAL(10, 2)
);

INSERT INTO customers (customer_name, registration_date) VALUES 
('Alice', '2023-01-10'), 
('Bob', '2023-02-20'), 
('Charlie', '2023-02-25'), 
('Dave', '2023-03-01');

INSERT INTO orders (customer_id, order_date, amount) VALUES 
(1, '2023-01-20', 150.00), 
(2, '2023-02-25', 200.00), 
(3, '2023-02-27', 250.00),
(4, '2023-03-15', 300.00);
```

#### 查询语句

```sql
SELECT DATE_FORMAT(registration_date, '%Y-%m') AS month,
       COUNT(DISTINCT customer_id) AS new_customers,
       COALESCE(SUM(amount), 0) AS total_orders
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id 
AND DATE_FORMAT(c.registration_date, '%Y-%m') = DATE_FORMAT(o.order_date, '%Y-%m')
GROUP BY month
ORDER BY month;
```

---

## 11. 复杂子查询和条件过滤

### 题目 11：查询每位员工在所有项目中的平均工资排名

#### 建表语句

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    employee_name VARCHAR(50)
);

CREATE TABLE salaries (
    employee_id INT,
    project_id INT,
    salary DECIMAL(10, 2),
    PRIMARY KEY (employee_id, project_id)
);

INSERT INTO employees (employee_name) VALUES 
('Alice'), 
('Bob'), 
('Charlie');

INSERT INTO salaries (employee_id, project_id, salary) VALUES 
(1, 1, 5000.00), 
(1, 2, 5500.00), 
(2, 1, 6000.00), 
(3, 1, 4500.00), 
(3, 2, 4800.00);
```

#### 查询语句

```sql
SELECT employee_id, 
       AVG(salary) AS avg_salary,
       RANK() OVER(ORDER BY AVG(salary) DESC) AS salary_rank
FROM salaries
GROUP BY employee_id;
```

---

## 12. 使用 JSON 函数的复杂查询

### 题目 12：查找包含某个特定技能的员工及其技能等级

#### 建表语句

```sql
CREATE TABLE employee_skills (
    employee_id INT PRIMARY KEY,
    skills JSON
);

INSERT INTO employee_skills (employee_id, skills) VALUES 
(1, '{ "Java": "Intermediate", "Python": "Advanced" }'), 
(2, '{ "Java": "Beginner", "SQL": "Expert" }'), 
(3, '{ "Python": "Intermediate", "SQL": "Advanced" }');
```

#### 查询语句

```sql
SELECT employee_id, JSON_UNQUOTE(skills->"$.Python") AS python_level
FROM employee_skills
WHERE JSON_CONTAINS_PATH(skills, 'one', '$.Python');
```

这些题目涵盖了窗口函数、递归查询、条件过滤、组合查询、子查询、JSON数据查询等多种复杂查询情境，能够有效检验MySQL查询技能的广度和深度。



继续进阶

## 13. 公共表表达式（CTE）与递归查询

### 题目 13：查询某员工及其所有直接和间接下属，要求层级显示（层级越高，值越大）

#### 建表语句

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(50),
    manager_id INT
);

INSERT INTO employees (employee_id, employee_name, manager_id) VALUES 
(1, 'CEO', NULL), 
(2, 'VP1', 1), 
(3, 'VP2', 1), 
(4, 'Manager1', 2), 
(5, 'Manager2', 2), 
(6, 'Staff1', 4), 
(7, 'Staff2', 4);
```

#### 查询语句

```sql
WITH RECURSIVE employee_hierarchy AS (
    SELECT employee_id, employee_name, manager_id, 1 AS level
    FROM employees
    WHERE employee_id = 2  -- 假设查询 VP1 的所有下属
    UNION ALL
    SELECT e.employee_id, e.employee_name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT employee_name, level
FROM employee_hierarchy
ORDER BY level DESC;
```

---

## 14. 复杂的时间间隔查询

### 题目 14：查询每位客户最近两次下单的时间间隔（以天为单位）

#### 建表语句

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE
);

INSERT INTO orders (customer_id, order_date) VALUES 
(1, '2023-01-10'), 
(1, '2023-02-15'), 
(1, '2023-03-20'), 
(2, '2023-02-25'), 
(2, '2023-03-10');
```

#### 查询语句

```sql
WITH customer_orders AS (
    SELECT customer_id, order_date,
           ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY order_date DESC) AS order_rank
    FROM orders
)
SELECT c1.customer_id,
       DATEDIFF(c1.order_date, c2.order_date) AS days_between
FROM customer_orders c1
JOIN customer_orders c2 ON c1.customer_id = c2.customer_id
WHERE c1.order_rank = 1 AND c2.order_rank = 2;
```

---

## 15. 多表自关联查询

### 题目 15：查找同一部门中工资差距最大的两位员工及其差距

#### 建表语句

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(50),
    department_id INT,
    salary DECIMAL(10, 2)
);

INSERT INTO employees (employee_name, department_id, salary) VALUES 
('Alice', 1, 7000.00), 
('Bob', 1, 8000.00), 
('Charlie', 1, 9500.00), 
('Dave', 2, 6000.00), 
('Eve', 2, 7200.00);
```

#### 查询语句

```sql
SELECT e1.department_id, e1.employee_name AS emp1, e2.employee_name AS emp2,
       ABS(e1.salary - e2.salary) AS salary_difference
FROM employees e1
JOIN employees e2 ON e1.department_id = e2.department_id 
AND e1.employee_id < e2.employee_id
ORDER BY salary_difference DESC
LIMIT 1;
```

---

## 16. 组合查询：统计类和动态窗口查询

### 题目 16：查询所有员工中每月的工资发放总额，按月份排序

#### 建表语句

```sql
CREATE TABLE payroll (
    payroll_id INT PRIMARY KEY,
    employee_id INT,
    payment_date DATE,
    amount DECIMAL(10, 2)
);

INSERT INTO payroll (employee_id, payment_date, amount) VALUES 
(1, '2023-01-15', 5000.00), 
(2, '2023-01-15', 6000.00), 
(1, '2023-02-15', 5000.00), 
(3, '2023-02-15', 4500.00), 
(2, '2023-02-15', 6000.00);
```

#### 查询语句

```sql
SELECT DATE_FORMAT(payment_date, '%Y-%m') AS month,
       SUM(amount) AS total_payout
FROM payroll
GROUP BY month
ORDER BY month;
```

---

## 17. JSON 操作与条件过滤

### 题目 17：查找有技能“Python”且等级在“Advanced”或以上的员工姓名及其技能等级

#### 建表语句

```sql
CREATE TABLE employee_skills (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(50),
    skills JSON
);

INSERT INTO employee_skills (employee_id, employee_name, skills) VALUES 
(1, 'Alice', '{ "Java": "Intermediate", "Python": "Advanced" }'), 
(2, 'Bob', '{ "Python": "Expert", "SQL": "Advanced" }'), 
(3, 'Charlie', '{ "Python": "Intermediate" }');
```

#### 查询语句

```sql
SELECT employee_name, JSON_UNQUOTE(skills->"$.Python") AS python_level
FROM employee_skills
WHERE JSON_EXTRACT(skills, '$.Python') IN ('"Advanced"', '"Expert"');
```

---

## 18. 复杂条件过滤和计算

### 题目 8：统计所有产品每个季度的销售总额，只统计销售额超过1000的季度记录

#### 建表语句

```sql
CREATE TABLE sales (
    sale_id INT PRIMARY KEY,
    product_id INT,
    sale_date DATE,
    sale_amount DECIMAL(10, 2)
);

INSERT INTO sales (product_id, sale_date, sale_amount) VALUES 
(1, '2023-01-15', 500.00), 
(1, '2023-03-20', 600.00), 
(2, '2023-04-10', 1200.00), 
(2, '2023-05-15', 800.00),
(1, '2023-07-25', 900.00);
```

#### 查询语句

```sql
SELECT product_id,
       QUARTER(sale_date) AS quarter,
       SUM(sale_amount) AS total_sales
FROM sales
GROUP BY product_id, quarter
HAVING total_sales > 1000;
```



这些题目进一步考察了公共表表达式（CTE）、自关联、JSON 数据处理、动态窗口查询等技巧。