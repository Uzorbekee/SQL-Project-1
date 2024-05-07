# SQL-Project-1
It's a project containing a company's employee data queried using SQL to make some findings
SELECT *
FROM employees
;

/*QUESTION 1:The total number of employees in the company*/
SELECT COUNT(*) AS total_employees
FROM employees;


/*QUESTION 2:The total number of employees in each department*/
SELECT DEPARTMENT_ID, COUNT(employee_id) AS num_employee
FROM employees
GROUP BY DEPARTMENT_ID
;


/*QUESTION 3:The three departments with the highest number of employees*/
SELECT TOP 3 DEPARTMENT_ID, COUNT(employee_id) AS num_employees
FROM employees
GROUP BY DEPARTMENT_ID
ORDER BY num_employees DESC
;


/*QUESTION 4:Who are the oldest and newest employee. Also retrieve the date they
joined the company and all information about them.*/
SELECT *
FROM employees 
WHERE hire_date = (SELECT MIN(hire_date) FROM employees)
   OR hire_date = (SELECT MAX(hire_date) FROM employees)
ORDER BY hire_date;


/*QUESTION 5:The oldest and newest employee in each department.
Hint: Use Row_number Function and CTE.*/
	WITH RankedEmployees AS (
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY DEPARTMENT_ID ORDER BY HIRE_Date ASC) AS OldestRank,
        ROW_NUMBER() OVER (PARTITION BY DEPARTMENT_ID ORDER BY HIRE_Date DESC) AS NewestRank
    FROM 
        Employees)
SELECT 
    RE1.DEPARTMENT_ID,
    RE1.EMPLOYEE_ID AS OldestEmployeeID,
    CONCAT(RE1.FIRST_NAME,' ',RE1.LAST_NAME) AS OldestEmployeeName,
    RE1.HIRE_DATE AS OldestHireDate,
    RE2.EMPLOYEE_ID AS NewestEmployeeID,
    CONCAT (RE2.FIRST_NAME,' ',RE2.LAST_NAME) AS NewestEmployeeName,
    RE2.HIRE_DATE AS NewestHireDate
FROM 
    RankedEmployees RE1
JOIN 
    RankedEmployees RE2 ON RE1.DEPARTMENT_ID = RE2.DEPARTMENT_ID
WHERE 
    RE1.OldestRank = 1 AND RE2.NewestRank = 1;


/*QUESTION 6:The total number of information technology programmers in the company.
Hint: Use the join statement*/
SELECT COUNT(*) AS total_programmers
FROM employees e
JOIN job j ON e.JOB_ID = j.JOB_ID
WHERE e.JOB_ID = 'IT_PROG' 
AND j.job_title ='Information technology Programmer';


/*QUESTION 7:The year difference between the date the oldest and newest employee
joined the company.*/
SELECT 
    DATEDIFF(YEAR, 
             (SELECT MIN(hire_date) FROM employees), 
             (SELECT MAX(hire_date) FROM employees)) AS year_difference;


/*QUESTION 8:Who are the employees that earn below average? Retrieve their salaries
and also their job title.*/
SELECT *
FROM employees
WHERE SALARY < (
 SELECT AVG(SALARY)
 FROM employees)
;


/*QUESTION 9:Update the strings of the email column to lower case letter.*/
UPDATE employees
SET EMAIL = LOWER(EMAIL);

/*QUESTION 10:You noticed that the commission column is empty. Delete the column from
the table.*/
ALTER TABLE employees
DROP COLUMN COMMISSION_PCT;


SELECT 
    DEPARTMENT_ID,
    EMPLOYEE_ID,
    CONCAT(FIRST_NAME, ' ', LAST_NAME) AS EmployeeName,
    HIRE_DATE
FROM 
    Employees
ORDER BY 
    DEPARTMENT_ID, HIRE_DATE;

	WITH RankedEmployees AS (
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY DEPARTMENT_ID ORDER BY HIRE_Date ASC) AS OldestRank,
        ROW_NUMBER() OVER (PARTITION BY DEPARTMENT_ID ORDER BY HIRE_Date DESC) AS NewestRank
    FROM 
        Employees
)

SELECT 
    DEPARTMENT_ID,
    MIN(CASE WHEN OldestRank = 1 THEN EMPLOYEE_ID END) AS OldestEmployee,
    MIN(CASE WHEN NewestRank = 1 THEN EMPLOYEE_ID END) AS NewestEmployee
FROM 
    RankedEmployees
GROUP BY 
    DEPARTMENT_ID;
