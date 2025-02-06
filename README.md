**TABLES**

1. Customers
2. Sales
3. Employees
4. Salaries
5. Departments
6. Department Numbers:dept_no


**SQL Case Statements**
* Join Tables
* Aggregation Functions
* Case When
* Subqueries
* Transpose Tables
* Where, Group by, Order by, Alias
* Extract, Currunt_Date




--SQL CASE Statements
-- Overview employees table
SELECT * FROM employees;

-- 1.2: Change M to Male and F to Female in the employees table
SELECT first_name, last_name, hire_date,
case 
	WHEN gender='M' THEN 'Male'
    ELSE 'Female' 
    end as Gender 
from employees

-- 1.3: This gives the same result as 1.2
SELECT first_name, last_name, hire_date,
case gender
	WHEN 'M' THEN 'Male'
    ELSE 'Female' 
    end as Gender 
from employees

-- 2.1: Overview customers table
SELECT * FROM customers

-- 2.2: Create a column called Age_Category that returns Young for ages less than 30,
-- Aged for ages greater than 60, and Middle Aged otherwise
SELECT *,
case 
	when age < 30 then 'Young'
    WHEN age > 60 then 'Aged'
    else 'Middle Aged'
end as Age_Category 
from customers

-- 2.3: Retrieve a list of employees that were employed before 1990, between 1990 and 1995, and 
-- after 1995
SELECT * ,
case 
	when hire_date < '1990-01-01' then 'Before 1990'
    when hire_date BETWEEN '1990-01-01' and '1995-12-31' THEN 'Between 1990-95'
    else 'After 1995' 
    end as hire_date_category 
FROM employees

--Alternative Solution 
SELECT emp_no, hire_date, EXTRACT(year from hire_date) as year_,
case 
	when EXTRACT(year from hire_date) < 1990 then 'Before 1990'
    when EXTRACT(year from hire_date) BETWEEN 1990 and 1995 then 'Between 1990-95'
    else 'After 1995' 
    end as hire_date_category 
FROM employees
    

-- 3.1: Retrieve the average salary of all employees
SELECT * from salaries

SELECT emp_no, avg(salary) as average_salary 
from salaries
GROUP by emp_no
order by 2 DESC

-- 3.2: Retrieve a list of the average salary of employees. If the average salary is more than
-- 80000, return Paid Well. If the average salary is less than 80000, return Underpaid,
-- otherwise, return Unpaid
SELECT emp_no, round(avg(salary)),
case 
	when avg(salary) > 80000 THEN 'Paid Well'
    WHen avg(salary) < 80000 then 'Underpaid' 
    else 'Unpaid' 
    end as salary_status 
FROM salaries
GROUP by emp_no
order by 2 desc
    

-- 3.3: Retrieve a list of the average salary of employees. If the average salary is more than
-- 80000 but less than 100000, return Paid Well. If the average salary is less than 80000, 
-- return Underpaid, otherwise, return Manager
SELECT emp_no, round(avg(salary),2),
case 
	WHEN AVG(salary) BETWEEN 80000 and 100000 THEN 'Paid Well'
    WHEn avg(salary) < 80000 THEN 'Underpaid'
    else 'Manager'
    end as salary_category 
from salaries
GROUP by emp_no
order by 2 desc
LIMIT 1000

-- 3.4: Count the number of employees in each salary category
SELECT S.salary_category, 
	count(*) as number_of_salary_category

from (SELECT emp_no, round(avg(salary),2),
case 
	WHEN AVG(salary) BETWEEN 80000 and 100000 THEN 'Paid Well'
    WHEn avg(salary) < 80000 THEN 'Underpaid'
    else 'Manager'
    end as salary_category 
from salaries
GROUP by emp_no) as S

GROUP by S.salary_category

--Alternative solution
SELECT salary_category, 
	count(*) as number_of_salary_category

from (SELECT emp_no, round(avg(salary),2),
case 
	WHEN AVG(salary) BETWEEN 80000 and 100000 THEN 'Paid Well'
    WHEn avg(salary) < 80000 THEN 'Underpaid'
    else 'Manager'
    end as salary_category 
from salaries
GROUP by emp_no)

GROUP by salary_category


--JOINS

-- 4.1: Retrieve all the data from the employees and dept_emp

SELECT * FROM employees
ORDER BY emp_no DESC;

SELECT * FROM dept_emp;

-- 4.2: Join the employees table to the dept_emp table

SELECT e.emp_no, d.dept_no, e.first_name, e.hire_date
from employees as e 
left JOIN dept_emp as d on e.emp_no = d.emp_no
order by d.emp_no

	
-- 4.3: Join all the records in the employees table to the dept_emp table
-- where the employee number is greater than 109990

SELECT e.emp_no, d.dept_no, e.first_name, e.hire_date
from employees as e 
LEFT join dept_emp as d 
on e.emp_no = d.emp_no
WHERE e.emp_no > 10990


-- 4.4: Obtain a result set containing the employee number, first name, and last name
-- of all employees. Create a 4th column in the query, indicating whether this 
-- employee is also a department name, according to the data in the
-- dept_emp,departments, or a regular employee

SELECT e.emp_no, e.first_name, e.last_name, d.dept_no, dep.dept_name ,
case 
	when d.emp_no is Not NULL then 'Employee'
    ELSE 'Manager'
    end as Status
from employees as e 
left JOIN dept_emp as d on e.emp_no = d.emp_no
left JOIN departments as dep on d.dept_no = dep.dept_no


-- 4.5: Obtain a result set containing the employee number, first name, and last name
-- of all employees with a number greater than '109990'. Create a 4th column in the query,
-- indicating whether this employee is also a manager, according to the data in the
-- dept_emp, departments table, or a regular employee

SELECT e.emp_no, e.first_name, e.last_name, d.dept_no, dep.dept_name ,
case 
	when d.emp_no is Not NULL then 'Employee'
    ELSE 'Manager'
    end as Status
from employees as e 
left JOIN dept_emp as d on e.emp_no = d.emp_no
left JOIN departments as dep on d.dept_no = dep.dept_no 
WHERE e.emp_no >10990 
ORDER by 1 desc 

#############################
-- Task Five: The CASE Statement together with Aggregate Functions and Joins
-- In this task, we will see how to use the CASE clause together with
-- SQL aggregate functions and SQL Joins to retrieve data
#############################

-- 5.1: Retrieve all the data from the employees and salaries tables
SELECT * FROM employees;

SELECT * FROM salaries;

-- 5.2: Retrieve a list of all salaries earned by an employee
SELECT e.emp_no, e.first_name, e.last_name, s.salary, s.from_date
from employees as e
LEFT JOIN salaries as s 
on e.emp_no = s.emp_no

/* 5.3: Retrieve a list of employee number, first name and last name.
Add a column called 'salary difference' which is the difference between the
employees' maximum and minimum salary. Also, add a column called
'salary_increase', which returns 'Salary was raised by more than $30,000' if the difference 
is more than $30,000, 'Salary was raised by more than $20,000 but less than $30,000',
if the difference is between $20,000 and $30,000, 'Salary was raised by less than $20,000'
if the difference is less than $20,000 */

SELECT  s.emp_no, e.first_name, e.last_name, max(s.salary), min(s.salary) ,
case 
	when max(s.salary)-min(s.salary) > 30000 then 'Salary was raised by more than $30,000'
    when max(s.salary)-min(s.salary) BETWEEN 20000 and 30000 then 'Salary was raised by more than $20,000 but less than $30,000'
    else 'Salary was raised by less than $20,000' 
    end as salary_difference
FROM employees as e 
JOIN salaries as s 
on e.emp_no  =s.emp_no 
GROUP BY s.emp_no, e.first_name, e.last_name

--5.4: Number of salary_difference each category

SELECT salary_difference, count(*)
from
(SELECT  s.emp_no, e.first_name, e.last_name, max(s.salary), min(s.salary) ,
case 
	when max(s.salary)-min(s.salary) > 30000 then 'Salary was raised by more than $30,000'
    when max(s.salary)-min(s.salary) BETWEEN 20000 and 30000 then 'Salary was raised by more than $20,000 but less than $30,000'
    else 'Salary was raised by less than $20,000' 
    end as salary_difference
FROM employees as e 
JOIN salaries as s 
on e.emp_no  =s.emp_no 
GROUP BY s.emp_no, e.first_name, e.last_name)

GROUP by salary_difference
order by 2 desc

-- 5.5: Retrieve all the data from the employees and dept_emp tables
SELECT * FROM employees;

SELECT * FROM dept_emp;

/* 5.6: Extract the employee number, first and last name of the first 100 employees, 
and add a fourth column called "current_employee" saying "Is still employed",
if the employee is still working in the company, or "Not an employee anymore",
if they are not more working in the company.
Hint: We will need data from both the 'employees' and 'dept_emp' table to solve this exercise */

SELECT e.emp_no, e.first_name, e.last_name,
case
	when max(d.to_date) > CURRENT_DATE  then 'Is still employed'
    else 'Not an employee anymore'
    end as current_employee
from employees as e 
JOIN dept_emp as d on e.emp_no = d.emp_no 
GROUP by e.emp_no, e.first_name, e.last_name
LIMIT 100

#############################
-- Task Six: Transposing data using the CASE clause

-- 6.1: Retrieve all the data from the sales table
SELECT * FROM sales;

-- 6.2: Retrieve the count of the different profit_category from the sales table

SELECT pc.profit_category, count(*)
from
(SELECT Profit,
case 	
	when profit < 0 then 'No profit' 
    when profit BETWEEN 0 and 500 then 'Low profit' 
    when profit BETWEEN 500 and 1500 THEN 'Good profit'
    else 'High profit'
    end as profit_category
from sales) as pc
GROUP by pc.profit_category 
order by 2 desc

-- 6.3: Transpose 6.2 above
SELECT sum(case WHEN profit < 0 then 1 else 0 end) as No_profit,
sum(case WHEN profit BETWEEN 0 and 500 then 1 else 0 end ) as Low_profit,
sum(case WHEN profit BETWEEN 500 and 1500 then 1 else 0 end ) as Good_profit,
sum(case WHEN profit > 1500 then 1 else 0 end) as High_profit
from sales


-- 6.4: Retrieve the number of employees in the first four departments in the dept_emp table

SELECT * FROM dept_emp;

SELECT dept_no, count(*) 
from dept_emp
WHERE dept_no in ('d001','d002','d003','d004') 
GROUP by dept_no 
order by 1

-- 6.5: Transpose 6.4 above
SELECT sum(case when dept_no='d001' then 1 else 0 end) as dep_1,
		sum(case when dept_no='d002' then 1 else 0 end) as dep_2,
        sum(case when dept_no='d003' then 1 else 0 end) as dep_3, 
        sum(case when dept_no='d004' then 1 else 0 end) as dep_4
from dept_emp


