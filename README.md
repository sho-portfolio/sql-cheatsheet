# SQL Cheatsheet

## Joins
* (inner) join
* left (outer) join
* right (outer) join
* full (outer) join

![join-types](misc/sql-join-types.png)



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
CASE
    WHEN condition1 THEN result1
    
    WHEN condition2 THEN result2
    
    WHEN conditionN THEN resultN
    
    ELSE result
END;

SELECT CustomerName, City, Country
FROM Customers
ORDER BY
(CASE
    WHEN City IS NULL THEN Country
    ELSE City
END);


## Running Totals
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

# Useful resources and references
https://www.w3schools.com/sql/
https://www.toptal.com/designers/htmlarrows/punctuation/
https://codingsight.com/calculating-running-total-with-over-clause-and-partition-by-clause-in-sql-server/

