/* Part 2 */
--1
SELECT * FROM Customers;
INSERT INTO CUSTOMERS (USERNAME ,FNAME, LNAME, PHONE)
VALUES ("eymendez", "Eric", "Mendez", "(713) 987-1722");

--2
SELECT * FROM HORSES;
Update HORSES SET OWNER = 'eymendez'
WHERE Name ='Flash';

--3
SELECT * FROM COURSECUSTOMER;
INSERT INTO COURSECUSTOMER (FKUSERNAME, FKCOURSEID)
VALUES ('eymendez', 'Sum24Race1-1');
INSERT INTO COURSECUSTOMER (FKUSERNAME, FKCOURSEID)
VALUES ('eymendez', 'Fall24Jump1-1');

/* Part 3 */
--1
SELECT Name, Title, SALARY FROM WORKERS
WHERE salary > 100000;

--2
SELECT * FROM Horses 
WHERE WEIGHT >= 1800 AND SEX = 'F';

--3
SELECT name, title FROM WORKERS
WHERE title LIKE 'Director%';

--4
SELECT * FROM horses
WHERE name LIKE '_r%'

--5
SELECT Name, Title FROM WORKERS
WHERE TERMINATIONDATE IS NOT null;

/* Part 4 */
--1
SELECT title, count(DISTINCT empID) AS "Number Of Emps" FROM WORKERS
Group BY title
ORDER BY Title ASC;

--2
SELECT Sex, round(avg(WEIGHT),1) AS "Average Weight"  FROM HORSES
GROUP BY Sex;

--3
SELECT Color, count(*) FROM Horses
GROUP BY color 
ORDER BY count(*) desc;

--4
SELECT Color, Sex, Count(*) FROM Horses
GROUP BY color, sex
Order BY color, Count(*) desc;

--5
SELECT Color, Count(*) FROM HORSES
WHERE Sex = 'F'
AND Spots = 'No'
Group BY COLOR 
ORDER BY count(*) desc;

--6
SELECT Title, Count(empID)AS "Number Of Emps" From Workers
WHERE TERMINATIONDATE IS null
GROUP BY Title
ORDER  BY title asc;

--7 
SELECT FKBARNID, count(*) AS "MaintCount" FROM STABLES
WHERE STATUS = 'Maintenance'
GROUP BY FKBARNID;

--8
SELECT LNAME, count(*) FROM Customers
GROUP BY LNAME
HAVING count(*) > 1
ORDER BY count(*) desc;

--9
SELECT Title, Floor(avg(salary)) AS AvgSalary, 
min(salary) AS MinSalary, max(salary) AS MaxSalary, 
count(DISTINCT empID) AS Employees
FROM WORKERS
GROUP BY Title
ORDER BY AVGSALARY desc;

--10
SELECT count(name) AS "Total Horses", count(OWNER) AS "Owned Horses" FROM Horses
ORDER by count(name);

/* Part 5 */
--1
SELECT Name, FNAME, LNAME, PHONE
FROM Horses JOIN CUSTOMERS ON OWNER = USERNAME
WHERE Phone LIKE '_615%'
AND SEX = 'F'
ORDER BY Name;

--2
SELECT COURSENAME, NAME, Term, "YEAR" 
FROM COURSES JOIN Workers ON FKEMPID = EMPID 
JOIN Topics ON FKTOPICCODE = TOPICCODE
WHERE term = 'Summer'
AND YEAR = '2024';

--3
SELECT username, FNAME, LNAME, count(*)
FROM horses JOIN customers ON Owner = USERNAME
GROUP BY username, FNAME, LNAME
HAVING count(*) > 4
ORDER BY count(*) desc;

--4
SELECT certName, name, Title 
FROM CERTIFICATIONS LEFT JOIN WORKERCERT ON FKCERTID = CERTID 
LEFT JOIN Workers ON FKEMPID = EMPID
ORDER BY CERTNAME, Title;

--5 
SELECT mgr.NAME AS "Manager Name", emp.NAME AS "Employee"
FROM WORKERS mgr, Workers emp
WHERE mgr.EMPID = emp.SUPERVISOR
AND emp.TERMINATIONDATE IS NULL
ORDER BY mgr.name, emp.name;

--6
SELECT Name, MEDNAME, DOSE, FREQUENCY 
FROM Horses JOIN orders ON NAME = ORD_HORSENAME 
JOIN MEDICATIONS ON ORD_MEDCODE = MEDCODE
WHERE CLASSIFICATION = 'Dewormer'
ORDER BY MEDNAME, NAME;

--7
SELECT DISTINCT w.name 
FROM COURSES c JOIN Workers w ON EMPID = FKEMPID 
JOIN WORKERBARN wb ON wb.FKEMPID = w.EMPID
ORDER BY name;

/* Part 6 */
--1
SELECT NAME, SPOTS, COLOR, OWNER
FROM HORSES 
WHERE color = 'Red'
AND spots = 'Yes';

--2
SELECT NAME, SPOTS, COLOR, OWNER
FROM HORSES 
WHERE color = 'Red'
INTERSECT 
SELECT NAME, SPOTS, COLOR, OWNER 
FROM horses
WHERE spots = 'Yes';

--3 
SELECT Name, Spots, Color, Owner  
FROM Horses
WHERE COLOR = 'Red'
AND NAME IN (SELECT name FROM horses WHERE spots = 'Yes')

--4
SELECT name FROM workers 
WHERE EMPID IN  
(SELECT c.FKEMPID FROM COURSES c)
AND EMPID NOT IN   
(SELECT w.FKEMPID FROM WORKERCERT w WHERE w.FKCERTID = 'TEA' )
ORDER BY Name;

--5 
SELECT name FROM WORKERS
WHERE EMPID IN 
(SELECT FKEMPID FROM Courses)
MINUS
SELECT name FROM workers 
WHERE EmpID IN 
(SELECT FKEMPID FROM workercert 
WHERE FKCERTID = 'TEA')
ORDER BY Name;

--6
SELECT USERNAME, FNAME, LNAME, PHONE 
FROM customers
WHERE Username NOT IN 
(SELECT FKUSERNAME FROM coursecustomer);

--7 
SELECT c.USERNAME, c.FNAME, c.LNAME, c.PHONE 
FROM Customers c LEFT JOIN COURSECUSTOMER cc ON c.USERNAME = cc.FKUSERNAME
WHERE cc.FKUSERNAME IS NULL;

--8
SELECT USERNAME FROM customers
INTERSECT
SELECT Username FROM customers
WHERE Username NOT IN 
(SELECT FKUSERNAME FROM coursecustomer);

/* Part 7 */
--1
SELECT Name, substr(name, INSTR(name, ' ') + 1) || ', ' || 
substr(name, 1, INSTR(name, ' ') - 1) AS "Formatted Name" FROM Workers
WHERE TITLE = 'Ranch Hand';

--2 
SELECT substr(phone, INSTR(phone, '(') + 1, Instr(phone, ')') - INSTR(phone, '(') -1 ) AS "AREACODE",
count(*) FROM CUSTOMERS
GROUP BY substr(phone, INSTR(phone, '(') + 1, Instr(phone, ')') - INSTR(phone, '(') -1 )
ORDER BY count(*) desc;

--3 
SELECT Name, ('(' || substr(phone, 1, 3) || ')' || ' ' || substr(phone, 4, 3)
|| '-' || substr(phone, 7)) AS "FormattedPhone"
FROM WORKERS;

--4 
SELECT substr(location, INSTR(location, ',') + 1) AS "State", count(*) AS "NUMRACES"
FROM RACES
GROUP BY substr(location, INSTR(location, ',') + 1)
ORDER BY count(*);

--5 
SELECT FNAME, LNAME, '(***) ***-' || substr(phone, LENGTH(phone) - 3) AS MASK
FROM customers
WHERE LNAME LIKE 'A%'
OR LNAME LIKE 'R%';

/* Part 8 Bonus */
--1
SELECT b.DESCRIPTION, Round(AVG("Worker Count"),2) AS "AvgWorkers" FROM 
(SELECT w.FKBARNID, Count(w.FKEMPID) AS "Worker Count" FROM WORKERBARN w GROUP BY w.FKBARNID)
JOIN BARNS b ON FKBARNID = b.BARNID
GROUP BY b.DESCRIPTION
ORDER BY "AvgWorkers" desc; 

--2
SELECT LPAD(' ', (LEVEL - 1) * 4) || NAME AS Org, TITLE FROM WORKERS
WHERE TERMINATIONDATE IS NULL
START WITH SUPERVISOR IS NULL
CONNECT BY PRIOR EMPID = SUPERVISOR
ORDER BY LEVEL, SUPERVISOR, EMPID

--3
SELECT EMPID, NAME, TO_Char(HIREDATE, 'MM/DD/YYYY')  AS "Hire on", ROUND((SYSDATE - HIREDATE) / 365 ,1) AS "Years Worked"
FROM WORKERS
WHERE TERMINATIONDATE IS NULL;

--4
SELECT t.COURSENAME, w.NAME || ' (' || w.TITLE || ')' AS Instructor, 
c.TERM || ' ' || c.YEAR AS Term , c.SIZELIMIT, Count(cc.FKUSERNAME) AS Enrollment,
round(count(cc.FKUSERNAME) / c.SIZELIMIT * 100 , 0) || '%' AS "PercentFull"  
FROM TOPICS t JOIN COURSES c ON TOPICCODE = FKTOPICCODE 
JOIN WORKERS w ON w.EMPID = c.FKEMPID LEFT OUTER JOIN COURSECUSTOMER cc ON c.COURSEID = cc.FKCOURSEID
GROUP BY t.COURSENAME, w.NAME, w.TITLE, c.TERM, c."YEAR", c.SIZELIMIT
ORDER BY TERM, t.COURSENAME;

--5 
SELECT h.NAME, c.FNAME || ' ' || c.LNAME AS Owner, hr."YEAR", hr.PLACE, r.RACENAME FROM Horses h LEFT JOIN CUSTOMERS c ON OWNER = c.USERNAME JOIN HORSERACES hr ON hr.FKHORSENAME = h.NAME JOIN RACES r ON hr.FKRACEID = r.RACEID
WHERE hr.PLACE IN ('1', '2', '3')
AND hr."YEAR" = '2021'
ORDER  BY r.RACENAME, hr.PLACE;

--6 
SELECT c.FNAME || ' ' || c.LNAME AS Owner, count(hr.PLACE) AS "Top 10 Finishes"
FROM (SELECT * FROM HORSERACES WHERE Place IS NOT NULL) hr JOIN Horses h ON hr.FKHORSENAME = h.NAME 
JOIN CUSTOMERS c ON h.OWNER = c.USERNAME
GROUP BY c.FNAME, c.LNAME
ORDER BY Count(hr.place) DESC;











