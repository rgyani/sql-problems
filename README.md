# SQL Questions and Answers from LeetCode

### Second Highest Salary

Write a SQL query to get the **second highest** salary from the Employee table.

#### Solution 1:
```
SELECT Max(Salary) SecondHighestSalary
FROM Employee 
WHERE Salary < (SELECT MAX(Salary) FROM Employee)
```

#### Solution 2:
```
WITH CTE AS  
(  
    SELECT Salary, DENSE_RANK() 
    OVER (ORDER BY Salary DESC) AS DENSERANK   
    FROM Employee
)  
SELECT Salary SecondHighestSalary  
FROM CTE  
WHERE DENSERANK = 2;  
```

### Nth Highest Salary

Write a SQL query to get the nth highest salary from the Employee table.

```
SELECT DISTINCT Salary FROM Employee ORDER BY Salary DESC
LIMIT 1 OFFSET N-1
```

### Rank Scores 
Write a SQL query to rank scores. If there is a tie between two scores, both should have the same ranking. Note that after a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no “holes” between ranks.

```
SELECT score, DENSE_RANK() OVER (ORDER By Score DESC) AS "Rank"
FROM Scores;
```

### Consecutive Numbers 
Write an SQL query to find all numbers that appear at least three times consecutively.

```text
Logs table:
+----+-----+
| Id | Num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+

Result table:
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
1 is the only number that appears consecutively for at least three times.
```

#### Solution
```
SELECT a.Num as ConsecutiveNums
FROM Logs a
JOIN Logs b
ON a.id = b.id+1 AND a.num = b.num
JOIN Logs c
ON a.id = c.id+2 AND a.num = c.num;
```

### Employees Earning More Than Their Managers
Given the Employee table, write a SQL query that finds out employees who earn more than their managers.

```
SELECT E.Name as "Employee"
FROM Employee E
JOIN Employee M
ON E.ManagerId = M.Id
AND E.Salary > M.Salary;
```

### Duplicate Emails
Write a SQL query to find all duplicate emails in a table named Person.

#### Solution 1
```
SELECT Email
FROM Person
GROUP BY Email
HAVING count(*) > 1
```

#### Solution 2

```
WITH CTE AS(
SELECT Email, ROW_NUMBER() OVER(PARTITION BY Email ORDER BY Email) AS RN
    FROM Person
)

SELECT Email
FROM CTE
WHERE RN > 1;
```


### Customers Who Never Order
Suppose that a website contains two tables, the Customers table and the Orders table. Write a SQL query to find all customers who never order anything.

### Solution 1

```
SELECT Name AS Customers
FROM Customers
LEFT JOIN Orders
ON Customers.Id = Orders.CustomerId
WHERE CustomerId IS NULL;
```

#### Solution 2
```
SELECT Name as Customers
FROM Customers
WHERE Id NOT IN(
    SELECT CustomerId
    FROM Orders
)
```

### Department Highest Salary
Write a SQL query to find employees who have the highest salary in each of the departments.
```
SELECT Department.Name AS Department, Employee.Name AS Employee, Salary
FROM Employee
JOIN Department
ON Employee.DepartmentId = Department.Id
WHERE (DepartmentId, Salary) IN(
        SELECT  DepartmentId, MAX(Salary) AS Salary
        FROM Employee
        GROUP BY DepartmentId
        );
```

### Department Top Three Salaries
Write a SQL query to find employees who earn the top three salaries in each of the department.

```
WITH department_ranking AS (
SELECT Name AS Employee, Salary ,DepartmentId
  ,DENSE_RANK() OVER (PARTITION BY DepartmentId ORDER BY Salary DESC) AS rnk
FROM Employee
)

SELECT d.Name AS Department, r.Employee, r.Salary
FROM department_ranking AS r
JOIN Department AS d
ON r.DepartmentId = d.Id
WHERE r.rnk <= 3
ORDER BY d.Name ASC, r.Salary DESC;
```

### Delete Duplicate Emails
Write a SQL query to delete all duplicate email entries in a table named Person, keeping only unique emails based on its smallest Id.

```
DELETE p2
FROM Person p1
JOIN Person p2
ON p1.Email = p2.Email
AND p1.id < p2.id
```

### Rising Temperature
Write an SQL query to find all dates’ id with higher temperature compared to its previous dates (yesterday).

```
SELECT t.Id
FROM Weather AS t, Weather AS y
WHERE DATEDIFF(t.RecordDate, y.RecordDate) = 1
AND t.Temperature > y.Temperature;
```

