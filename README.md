# Financial-Audit-Profitability-Analysis-System-MySQL-

CREATE DATABASE audit_analysis;
USE audit_analysis;

CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(100) NOT NULL,
    manager_name VARCHAR(100)
);

INSERT INTO departments VALUES
(1, 'Sales', 'Rahul Mehta'),
(2, 'Marketing', 'Ananya Sharma'),
(3, 'Operations', 'Vikram Singh'),
(4, 'HR', 'Neha Kapoor'),
(5, 'IT', 'Amit Verma');

CREATE TABLE revenue (
    revenue_id INT PRIMARY KEY,
    dept_id INT,
    revenue_date DATE,
    amount DECIMAL(12,2),
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);

INSERT INTO revenue VALUES
(101, 1, '2025-01-05', 450000),
(102, 1, '2025-02-10', 520000),
(103, 2, '2025-01-15', 300000),
(104, 2, '2025-02-20', 280000),
(105, 3, '2025-01-08', 600000),
(106, 3, '2025-02-18', 620000),
(107, 4, '2025-01-25', 100000),
(108, 4, '2025-02-12', 120000),
(109, 5, '2025-01-30', 250000),
(110, 5, '2025-02-22', 270000);

CREATE TABLE expenses (
    expense_id INT PRIMARY KEY,
    dept_id INT,
    expense_date DATE,
    category VARCHAR(100),
    amount DECIMAL(12,2),
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);

INSERT INTO expenses VALUES
(201, 1, '2025-01-07', 'Travel', 80000),
(202, 1, '2025-01-18', 'Client Entertainment', 60000),
(203, 1, '2025-02-15', 'Salary', 200000),
(204, 2, '2025-01-10', 'Advertising', 150000),
(205, 2, '2025-02-05', 'Digital Marketing', 120000),
(206, 2, '2025-02-20', 'Salary', 180000),
(207, 3, '2025-01-12', 'Equipment', 250000),
(208, 3, '2025-02-08', 'Maintenance', 90000),
(209, 3, '2025-02-25', 'Salary', 220000),
(210, 4, '2025-01-14', 'Recruitment', 50000),
(211, 4, '2025-02-10', 'Training', 40000),
(212, 4, '2025-02-18', 'Salary', 70000),
(213, 5, '2025-01-09', 'Software Licenses', 100000),
(214, 5, '2025-02-15', 'Infrastructure', 130000),
(215, 5, '2025-02-28', 'Salary', 150000);

-- Total Revenue Per Department
SELECT d.dept_name,
       SUM(r.amount) AS total_revenue
FROM departments d
JOIN revenue r ON d.dept_id = r.dept_id
GROUP BY d.dept_name;

-- Total Expense Per Department
SELECT d.dept_name,
       SUM(e.amount) AS total_expense
FROM departments d
JOIN expenses e ON d.dept_id = e.dept_id
GROUP BY d.dept_name;

-- Profit Calculation
SELECT d.dept_name,
       SUM(r.amount) - SUM(e.amount) AS profit
FROM departments d
JOIN revenue r ON d.dept_id = r.dept_id
JOIN expenses e ON d.dept_id = e.dept_id
GROUP BY d.dept_name;

-- High Expense Detection
SELECT dept_id, SUM(amount) AS total_expense
FROM expenses
GROUP BY dept_id
HAVING SUM(amount) > (
    SELECT AVG(total_exp)
    FROM (
        SELECT SUM(amount) AS total_exp
        FROM expenses
        GROUP BY dept_id
    ) AS avg_table
);

-- Expense Category Analysis
SELECT category,
       SUM(amount) AS total_spent
FROM expenses
GROUP BY category
ORDER BY total_spent DESC;

-- Audit Summary View
CREATE VIEW department_audit_summary AS
SELECT d.dept_name,
       SUM(r.amount) AS total_revenue,
       SUM(e.amount) AS total_expense,
       SUM(r.amount) - SUM(e.amount) AS profit
FROM departments d
JOIN revenue r ON d.dept_id = r.dept_id
JOIN expenses e ON d.dept_id = e.dept_id
GROUP BY d.dept_name;

-- One Loss-Making Department
INSERT INTO expenses VALUES
(216, 2, '2025-02-27', 'Campaign Failure Loss', 200000);
