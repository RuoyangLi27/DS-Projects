
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



**User Activity for the Past 30 Days II | use DISTINCE A B to choose a special combination of A B to do count etc.**

*https://leetcode.com/problems/user-activity-for-the-past-30-days-ii/*

*2019--01-25*

```
SELECT CASE
WHEN ROUND(COUNT(DISTINCT user_id, session_id)/COUNT(DISTINCT(user_id)), 2)  is Null THEN 0
ELSE ROUND(COUNT(DISTINCT user_id, session_id)/COUNT(DISTINCT(user_id)), 2) 
END AS 'average_sessions_per_user'
FROM Activity
WHERE DATEDIFF('2019-07-27', activity_date) <30
```


**buyers who have bought S8 but not iPhone | Follow the logic; use IN/ NOT IN to SELECT the right people**

*https://leetcode.com/problems/sales-analysis-ii/submissions/*

*2019--01-25*
```
SELECT DISTINCT S1.buyer_id
FROM Product P1
INNER JOIN Sales S1
ON 
P1.product_id=S1.product_id
WHERE P1.product_name = 'S8' AND 
S1.buyer_id NOT IN
(
SELECT S1.buyer_id
FROM Product P1
INNER JOIN Sales S1
ON 
P1.product_id=S1.product_id
WHERE P1.product_name='iphone')
```

**All People Report to the Given Manager | use join to link the reporters; manager~employee; use the last table employee as the final reporter and select original table's employee**

*https://leetcode.com/problems/all-people-report-to-the-given-manager/*

*2019--02-03*
```
SELECT E1.employee_id
FROM Employees AS E1
INNER JOIN Employees AS E2 ON E1.manager_id=E2.employee_id
INNER JOIN Employees AS E3 ON E2.manager_id=E3.employee_id
INNER JOIN Employees AS E4 ON E3.manager_id=E4.employee_id
WHERE E1.employee_id !=1 and E4.employee_id=1
```

*Customers Who Bought All Products | fairly easy.**

*https://leetcode.com/problems/customers-who-bought-all-products/submissions/*

*2019--02-04*
```
SELECT T1.customer_id
FROM
(
SELECT customer_id, COUNT(DISTINCT product_key) AS CT
FROM Customer
GROUP BY customer_id
    ) AS T1
WHERE 
T1.CT=
(SELECT COUNT(product_key)
FROM Product)
```

*User Activity for the Past 30 Days I | fairly easy.**

*https://leetcode.com/problems/user-activity-for-the-past-30-days-i/submissions/*

*2019--02-10*
```
SELECT DISTINCT activity_date as day, COUNT(DISTINCT(user_id)) AS active_users
FROM Activity
GROUP BY activity_date
HAVING ABS(DATEDIFF('2019-07-27', activity_date)) <30
ORDER BY day
```

*Second Degree Follower | Cases like the foloweers' follower always use self join to find the correct relationship between followers**

*https://leetcode.com/problems/second-degree-follower/submissions/*

*2019--02-11*
```
SELECT F1.follower, COUNT(DISTINCT(F2.follower)) AS num
FROM follow AS F1
INNER JOIN follow AS F2
ON F1.follower=F2.followee
GROUP BY F1.follower
HAVING(COUNT(F2.followee>0))
```

*active-business | use join and compare to find certain people meet certain standard**

*https://leetcode.com/problems/active-businesses/submissions/*

*2019--02-12*
```
# Write your MySQL query statement below
SELECT T2.business_id
FROM
(
SELECT event_type, AVG(occurences) AS average
FROM Events
GROUP BY event_type
    ) AS T1
    INNER JOIN Events AS T2
ON T1.event_type=T2.event_type
WHERE T2.occurences > T1.average
GROUP BY business_id
HAVING COUNT(business_id)>=2
```

*Unpopular Books | This is a tricky one. Be careful when using where because that may filter out '0' numbers.*

*when consider id with number<=10, sometimes consiter id with bumber >10 and use NOT IN to find the thing you want.*

*https://leetcode.com/problems/unpopular-books/submissions/*

*2019--02-12*
```
# Write your MySQL query statement below

SELECT book_id, name
FROM Books
WHERE DATEDIFF('2019-06-23', available_from) >=31
AND book_id NOT IN
(SELECT book_id
FROM Orders
WHERE dispatch_date BETWEEN '2018-06-23' AND '2019-06-23'
GROUP BY book_id
HAVING sum(quantity) >=10)

```
