
**Use AVG with GROUP BY Function to calculate 'rate'**


*https://leetcode.com/problems/trips-and-users/submissions/*

*2019--01-20*

```
SELECT 

A.Request_at as Day, 

ROUND(AVG(CASE WHEN A.Status !='completed' THEN 1

ELSE 0  END) , 2) AS 'Cancellation Rate'

FROM Trips AS A

INNER JOIN Users AS B

ON A.Client_id= B.Users_Id

INNER JOIN Users AS 

ON A.Driver_Id= C.Users_Id

WHERE B.Banned= 'No' AND C.Banned='No' AND
A.Request_at BETWEEN DATE('2013-10-1') AND DATE('2013-10-3')

GROUP BY A.Request_at;
```




**Use INNER JOIN ON A < B to decide rank#Calculate top 3 salary by department**
*https://leetcode.com/problems/department-top-three-salaries/submissions/*
*2019--01-21*

```
SELECT T3.Name AS Department, T2.Name AS Employee, T2.Salary
FROM
(
SELECT T1.DepartmentId, T1.Name, T1.Salary
FROM
(
SELECT E1.DepartmentId, E1.Name, E1.Salary, COUNT(DISTINCT(E2.Salary)) AS RANK
FROM Employee AS E1 
INNER JOIN Employee AS E2
ON E1.Salary <=E2.Salary AND E1.DepartmentId=E2.DepartmentId
GROUP BY E1.Name
    ) AS T1
WHERE T1.RANK <=3
    ) AS T2
    INNER JOIN Department AS T3
WHERE T2.DepartmentId=T3.Id
```





