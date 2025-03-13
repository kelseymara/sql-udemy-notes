# Overview
These are notes from a SQL course on Udemy. udemy.com/course/sql-intro/learn/lecture/10729428#overview 
## SELECT Statement
```SQL
SELECT last_name from people; // gets all the last names from people DB
SELECT last_name, first_name from people; // gets both columns
SELECT * from people; // gets all the data
SELECT * FROM people WHERE state='CA'; // gets rows who's state is CA
SELECT first_name, last_name FROM people WHERE shirt_or_hat='shirt'; // gets the first name and last name where it has shirt
```
- tells database what to get me, where to find it (from), and the condition for filtering respones. order: SELECT WHERE FROM

## More Criteria
These two are the same: 
```SQL
SELECT first_name, last_name
FROM people
WHERE state='CA' AND shirt_or_hat='shirt';


SELECT first_name, last_name 
FROM PEOPLE
WHERE state='CA' AND shirt_or_hat is shirt;
```
Or Statement:
```SQL
SELECT first_name, last_name
FROM people
WHERE state='CA' OR state='CO' AND shirt_or_hat='shirt'
// using parantheses
// you need the parantheses here to do it correctly. without it will just pair state='CO' AND shirt_or_hat='shirt' as one term
SELECT first_name, last_name
FROM people
WHERE (state='CA' OR state='CO') AND shirt_or_hat='shirt';
```
% 
```SQL
/// this gives any state that starts with a C
SELECT first_name,last_name, state
FROM people
WHERE state LIKE 'C%';
any state ending with C
SELECT first_name,last_name, state
FROM people
WHERE state LIKE '%C';

// middle of the field, any with letter C
SELECT first_name,last_name, state
FROM people
WHERE state LIKE '%C%';

SELECT first_name,last_name, state
FROM people
WHERE state LIKE '%C%'
LIMIT 5; // first 5 results

SELECT first_name,last_name, state
FROM people
WHERE state LIKE '%C%'
LIMIT 10 OFFSET 5; // skip 5 rows but give first 10 after skipped
```
## Organize responses by ORDER BY
```SQL
SELECT first_name, last_name FROM people
ORDER BY first_name ASC; // orders by first name ascending

SELECT first_name, last_name FROM people
ORDER BY first_name DESC;

//you can also combine more than one:
SELECT state, last_name, first_name
FROM people
ORDER BY state ASC, last_name DESC; // this sorts by state ascending first then last name

//length function: gives you length of  field (first name here) instead of field itself
SELECT first_name, LENGTH (first_name) FROM people;

//DISTINCT function: only one result for each unique name
SELECT DISTINCT(first_name) FROM people
ORDER BY first_name;

//counting how many ppl are from california
SELECT COUNT(*) FROM people WHERE state='CA';
```

## Join Keyword (to join tables)
```SQL
SELECT first_name, state
FROM people
JOIN states
ON people.state=states.state_abbrev; // use the states value from people table and match it to state abbreviation value from states table (need to be identical, this is inner join)

// being more specific
SELECT people.first_name, people.state, states.division // 2 columns from people table and 1 col from states
FROM people
JOIN states
ON people.state=states.state_abbrev; // use the states value from people table and match it to state abbreviation value from states table (need to be identical)

// adding more
SELECT people.first_name, people.state, states.division // 2 columns from people table and 1 col from states
FROM people // people table on left
JOIN states // states table on right
ON people.state=states.state_abbrev
WHERE people.first_name LIKE 'j%' AND states.region='South';
```
Inner Join
```SQL
SELECT * FROM people JOIN states ON
people.state=states.abbrv;
```
- inner join asks for records that overlap

-- outer: see ALL of the data from one table or another and the matches where there's a match happening
Left Outer Join
```SQL
SELECT * FROM people LEFT JOIN states
ON  people.state=states.abbrv;
```
-- see all values from left table (left goes first), with null that has no data
Right Outer Join
```SQL
SELECT * FROM people RIGHT JOIN states
ON people.state=states.abbrv;
```
-- see all values from right table (right goes first), with null that has no data
FULL OUTER JOIN
```SQL
SELECT * FROM people FULL OUTER JOIN states
ON people.state=states.abbrv;
```

## Group By Keyword
```SQL
SELECT first_name, COUNT(first_name)
FROM people
GROUP BY first_name;
```
- if you didn't have GROUP BY, it would give you count of last first name in db
- this lists all the first names and then how many of that first name is in db

## Math in SQL
```SQL
SELECT 4+2; // returns
SELECT 1/3; // gives 0 because it asssumes it's an int

SELECT MAX(quiz_points), MIN (quiz_points)
FROM people; // gives max and min

SELECT SUM(quiz_points)
FROM people; // returns sum
```
## COMPOUND SELECT
```SQL
SELECT firt_name, last_name, quiz_points
FROM people
WHERE quiz_points=(SELECT MAX(quiz_points) FROM people;

SELECT *
FROM people
WHERE state=(SELECT state_abbrev FROM states WHERE state = 'Minnesota'); // when you don't know the abbreviation of a state
```

## Transform Data
```SQL
SELECT LOWER(first_name),UPPER(last_name) // lowercases the first name and capitalizes the last name
FROM people;

SELECT LOWER(first_name),SUBSTR(last_name,1,5) // start at 1st character and proceed 5 characters after that
FROM people;

SELECT LOWER(first_name),SUBSTR(last_name,-2) // last 2 characters
FROM people;

SELECT LOWER(first_name),REPLACE(last_name,'a' , '_') // replace 'a' character with an underscore
FROM people;

SELECT CAST(quiz_points as CHAR); // change value to chars

```

## Adding Data
```SQL
INSERT INTO people (first_name,last_name,state,city)
VALUES('John','Doe','VA','NFK');

INSERT INTO people (first_name,last_name)
VALUES('John','Doe'),('Jenn','Smith'); // adding multiple values
```

## Modifying Data
```SQL
UPDATE people
SET first_name='Martha'
WHERE first_name='George' AND last_name='White'; // make sure you include where clause and be specific

DELETE FROM people
WHERE first_name='Martha' AND last_name='White';

DELETE FROM people
WHERE id_number=NULL;
```


