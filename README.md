# SQL Cheatsheet

## Joins

### Join Types
* (inner) join
* left (outer) join
* right (outer) join
* full (outer) join

![join-types](misc/sql-join-types.png)

* You can access the code for the join examples below here: 
  * For MySQL: http://sqlfiddle.com/#!9/be4457/6/0
  * For SQLServer: http://sqlfiddle.com/#!18/cd870/3/0

### Join Examples

* tablePerson
----------------    
PersonId | Name
---------|-------
1        | Adam
2        | Sue
3        | Bill
4        | Anne

* tableGrade
--------------------------- 
GradeId | PersonId | Grade
--------|----------|-------
1       | 1        | 5
2       | 1        | 7
3       | 2        | 1
4       | 4        | 0
5       | 4        | 9
6       | 5        | 3


* INNER JOIN EXAMPLE
```SQL
SELECT A.PersonId, A.Name, B.GradeId, B.PersonId, B.Grade
FROM tablePerson A
INNER JOIN tableGrade B ON A.PersonId = B.PersonId
```

-----------------------------------------------    
PersonId | Name  | GradeId | PersonId | Grade |
---------|-------|---------|----------|-------|
1        | Adam  | 1       | 1        | 5     |
1        | Adam  | 2       | 1        | 7     |
2        | Sue   | 3       | 2        | 1     |
4        | Anne  | 4       | 4        | 0     |
4        | Anne  | 5       | 4        | 9     |

Note that Bill does not appear in the results and nor does PersonId 5 (from the tableGrade table)


* LEFT OUTER JOIN EXAMPLE
```SQL
SELECT A.PersonId, A.Name, B.GradeId, B.PersonId, B.Grade
FROM tablePerson A
LEFT OUTER JOIN tableGrade B ON A.id = B.id
```
-----------------------------------------------    
PersonId | Name  | GradeId | PersonId | Grade |
---------|-------|---------|----------|-------|
1        | Adam  | 1       | 1        | 5     |
1        | Adam  | 2       | 1        | 7     |
2        | Sue   | 3       | 2        | 1     |
4        | Anne  | 4       | 4        | 0     |
4        | Anne  | 5       | 4        | 9     |
3        | Bill  | (null)  | (null)   | (null)|


* RIGHT OUTER JOIN EXAMPLE
```SQL
SELECT A.PersonId, A.Name, B.GradeId, B.PersonId, B.Grade
FROM tablePerson A
RIGHT OUTER JOIN tableGrade B ON A.id = B.id
```
-----------------------------------------------    
PersonId | Name  | GradeId | PersonId | Grade |
---------|-------|---------|----------|-------|
1        | Adam  | 1       | 1        | 5     |
1        | Adam  | 2       | 1        | 7     |
2        | Sue   | 3       | 2        | 1     |
4        | Anne  | 4       | 4        | 0     |
4        | Anne  | 5       | 4        | 9     |
(null)   | (null)| 6       | 5        | 3     |

* FULL OUTER JOIN EXAMPLE (NOT AVAILABLE IN MY-SQL)
```SQL
SELECT A.PersonId, A.Name, B.GradeId, B.PersonId, B.Grade
FROM tablePerson A
FULLL OUTER JOIN tableGrade B ON A.id = B.id
```
```SQL
--EQUIVALENT CODE FOR MYSQL
SELECT A.PersonId, A.Name, B.GradeId, B.PersonId, B.Grade
FROM tablePerson A
LEFT OUTER JOIN tableGrade B ON A.PersonId = B.PersonId
UNION
SELECT A.PersonId, A.Name, B.GradeId, B.PersonId, B.Grade
FROM tablePerson A
RIGHT OUTER JOIN tableGrade B ON A.PersonId = B.PersonId;
```
-----------------------------------------------    
PersonId | Name  | GradeId | PersonId | Grade |
---------|-------|---------|----------|-------|
1        | Adam  | 1       | 1        | 5     |
1        | Adam  | 2       | 1        | 7     |
2        | Sue   | 3       | 2        | 1     |
3        | Bill  | (null)  | (null)   | (null)|
4        | Anne  | 4       | 4        | 0     |
4        | Anne  | 5       | 4        | 9     |
(null)   | (null)| 6       | 5        | 3     |



## Wildcards
* &ast; represents zero or more characters (bl&ast; finds black, blue, blight)
* ? represents a single character (h?t finds hot, hat, hit)
* [] represents a single character (h[oa]t finds hot and hat, but not hit)
* ! represents any character not in the brackets (h[!oa]t finds hit, but not hat and hot)
* &hyphen; represents a range of charcters (c[a-b]t finds cat and cbt)
* &num; represents any single numeric character (2#5 finds 205, 215, 225, 235, 245, 255, 265, 275, 285, and 295)
* SQL example: SELECT * FROM Customers WHERE City LIKE 'ber%';
* SQL example: SELECT * FROM Customers WHERE City LIKE '[bp]%'; (returns all cities that start with the letter b or p)

## Case Statement
```SQL
CASE
 WHEN condition1 THEN result1
 WHEN condition2 THEN result2
 WHEN conditionN THEN resultN
 ELSE result
END;
```
```SQL
SELECT CustomerName, City, Country
FROM Customers
ORDER BY
(CASE
    WHEN City IS NULL THEN Country
    ELSE City
END);
```

## Running Totals (Window Function)
[![sql fiddle running total video](misc/sql-fiddle-image.jpeg)](https://youtu.be/qDddVDDPf_w)
<br>[click image to watch a video demonstration (code is below)]

```SQL
CREATE TABLE Students
(
  Id INT IDENTITY,
  Name VARCHAR (50),
  Gender VARCHAR (50),
  Age INT,
  PRIMARY KEY (Id)
);

INSERT INTO Students (Name, Gender, Age) VALUES ('Sally', 'Female', 14 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Edward', 'Male', 12 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Jon', 'Male', 13 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Liana', 'Female', 10 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Ben', 'Male', 11 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Elice', 'Female', 12 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Nick', 'Male', 9 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Josh', 'Male', 12 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Liza', 'Female', 10 );
INSERT INTO Students (Name, Gender, Age) VALUES ('Wick', 'Male', 15 );

(shows a running total along with a running average)
SELECT Id, Name, Gender, Age,
SUM (Age) OVER (ORDER BY Id) AS RunningAgeTotal,
AVG (Age) OVER (ORDER BY Id) AS RunningAgeAVERAGE
FROM Students;

(shows a running total for each gender)
SELECT Id, Name, Gender, Age,
SUM (Age) OVER (PARTITION BY Gender ORDER BY Id) AS RunningAgeTotal
FROM Students
```

## Rank (Window Function)

(http://sqlfiddle.com/#!18/05a09/8/0)

```SQL
create table olympics (
  sport varchar(20), 
  athlete varchar(20), 
  position int
)

insert into olympics select '100m Sprint', 'Tom A', '5'
insert into olympics select '100m Sprint', 'Pete A', '4'
insert into olympics select '100m Sprint', 'Andy A', '3'
insert into olympics select '100m Sprint', 'Jack A', '2'
insert into olympics select '100m Sprint', 'Jon A', '1'
insert into olympics select 'Fencing', 'Tom A', '5'
insert into olympics select 'Fencing', 'Pete A', '4'
insert into olympics select 'Fencing', 'Andy A', '3'
insert into olympics select 'Fencing', 'Jack A', '2'
insert into olympics select 'Fencing', 'Jon A', '1'
```

```SQL
SELECT * FROM olympics;

--get rank for all athletes (notice that pos_rank is the same 
--for athletes in different sports and it goes from 1 to 3
SELECT *,
RANK() OVER (ORDER BY position ASC) pos_rank
FROM olympics;

--get rank for all athletes by sport
SELECT *,
RANK() OVER (PARTITION BY sport ORDER BY position ASC) pos_rank
FROM olympics;

--get rank for all athletes by sport, but only the top 3 athletes 
select * from
(
  SELECT *,
  RANK() OVER (PARTITION BY sport ORDER BY position ASC) pos_rank
  FROM olympics
) ranked_qry
WHERE pos_rank <= 3;

```



## List of Window Funtions
- VALUE: first_value(), lag(), last_value(), lead()
- AGGREGATE: avg(), count(), max(), min()
- RANK: rank(), row_num(), cume_dist(), dense_rank(), percent_rank(), ntile()

## Common Table Expressions (CTE)
```SQL
WITH TableExpressionName (Column1, Column2, …, ColumnN)
AS
(Query Definition)
```
### Non-Recursive Common Table Expression Example
```SQL
WITH   PersonCTE (BusinessEntityID, FirstName, LastName)
AS     (SELECT Person.BusinessEntityID,
               FirstName,
               LastName
        FROM   Person.Person
        WHERE  LastName LIKE 'C%'),
PhoneCTE (BusinessEntityID, PhoneNumber)
AS     (SELECT BusinessEntityID,
               PhoneNumber
        FROM   Person.PersonPhone)
SELECT FirstName,
       LastName,
       PhoneNumber
FROM   PersonCTE
INNER JOIN
PhoneCTE
ON PersonCTE.BusinessEntityID = PhoneCTE.BusinessEntityID;
```

### Recursive Common Table Expression Example
![join-types](misc/sql-recursive-CTE-Image.png)
<br/>[Sourced from artcle by Kris Wenzel (see resources below)]
<br/><br/> To try this in sqlfiddle.com put the first block of code (below) into the schema window and the second block into the 'run sql' window (make sure you're using SQL Server as the DBMS).  (Note that the only reason we insert into a table in these examples is becuase sqlfiddle does not all you to mix DDL and DML - dont worry if you dont understand this).

* EXAMPLE 1
```SQL
WITH vCTE
AS (SELECT 1 AS N
  UNION ALL
  SELECT N+1
  FROM vCTE
  WHERE N < 50
)
SELECT N INTO tblCTE FROM vCTE;
```

```SQL
SELECT * FROM tblCTE
```

* EXAMPLE 2
```SQL
WITH vCTE (cntr)
AS (
  SELECT 1
  UNION ALL 
  SELECT cntr + 1 FROM vCTE WHERE cntr < 10
)
SELECT cntr INTO tblCTE FROM vCTE;
```
```SQL
SELECT * FROM tblCTE
```
* EXAMPLE 3 (MYSQL example - won't work in SQLFiddle as the current version of MYSQL is 5.6 which doesn't support CTE)
```SQL
WITH RECURSIVE my_cte AS
(
  SELECT 1 AS n
  UNION ALL
  SELECT 1+n FROM my_cte WHERE n<10
)
SELECT * FROM my_cte;
'''


# To do
- [ ] TURN THIS INTO A MEDIUM ARTICLE
- [ ] INDEXING
- [ ] REGEX
- [ ] BCP
- [ ] STRING MANIPULATION
- [ ] TXT / CSV FILE UPLOAD TO MYSQL
- [ ] TRANSACTION COMMIT / ROLLBACK
- [ ] TXT / CSV FILE UPLOAD TO MYSQL
- [ ] TYPES OF SQL (DDL (CREATE TABLE), DML (SELECT, JOIN, UPDATE), DCL (GRANT ACCESS TO), TCL (TRANSACTION, COMMIT)
- [ ] NORMALIZATION (1NF, 2NF, 3NF, BCNF) [https://www.youtube.com/watch?v=-WEpWH1NHGU&list=PL-iyibqkl8u22OzA-sbXeTg2Z6nqOKT_l&index=11&t=0s] - SEE Q22
- [ ] ROLLUP
- [ ] CROSS JOIN

# Useful resources and references
- https://www.w3schools.com/sql/
- https://codingsight.com/calculating-running-total-with-over-clause-and-partition-by-clause-in-sql-server/
- https://www.essentialsql.com/introduction-common-table-expressions-ctes/
- https://www.essentialsql.com/non-recursive-ctes/
- https://www.essentialsql.com/recursive-ctes-explained/
- http://www.sqlservertutorial.net/sql-server-window-functions/
- https://www.toptal.com/designers/htmlarrows/punctuation/
- https://help.github.com/en/articles/basic-writing-and-formatting-syntax
- https://github.com/tchapi/markdown-cheatsheet/blob/master/README.md
- https://www.youtube.com/watch?v=2Fn0WAyZV0E&list=PL-iyibqkl8u22OzA-sbXeTg2Z6nqOKT_l&index=10&t=0s

