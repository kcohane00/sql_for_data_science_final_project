### SQL for Data Science Final Project
This final project was provided by the University of California at Davis.
The datasets were sourced from the United States Department of Agriculture's (USDA) open data portal and the queries were run using MySQL rather than SQLite. 

The tables were created using [table_creation.sql](https://github.com/kcohane00/sql_for_data_science_final_project/blob/main/table_creation.sql).

The data was cleaned up using the following syntax using the Cheese Production Table as an example:

`
UPDATE cheese_production SET value = REPLACE(value, ',', '');
`

#### _The questions and answers are below._

**Question 1.**
Can you find out the total milk production for 2023? Your manager wants this information for the yearly report.
What is the total milk production for 2023?


```
SELECT SUM(Value)
FROM milk_production
WHERE Year = 2023;
```

**Answer:**`91812000000`



**Question 2.**
Which states had cheese production greater than 100 million in April 2023? The Cheese Department wants to focus their marketing efforts there.
How many states are there?


```
SELECT Year, c.Period, c.State_ANSI, s.State, c.Value
FROM cheese_production c
LEFT JOIN state_lookup s
ON c.State_ANSI = s.State_ANSI
WHERE Year = 2023 AND Period = 'APR' AND Value >100000000;
```

**Answer:**`2 and the states are California and Wisconsin`


**Question 3.**
Your manager wants to know how coffee production has changed over the years.
What is the total value of coffee production for 2011?

```
SELECT SUM(Value) 
FROM coffee_production
WHERE Year = 2011;
```

**Answer:**`7600000`

**Question 4.**
There's a meeting with the Honey Council next week. Find the average honey production for 2022 so you're prepared.

```
SELECT AVG(Value)
FROM honey_production
WHERE Year = 2022;
```

**Answer:**`3128589`

**Question 5.**
The State Relations team wants a list of all states' names with their corresponding ANSI codes. Can you generate that list?
What is the State_ANSI code for Florida?

```
FROM state_lookup;
```

**Answer:**`12`


**Question 6.**
For a cross-commodity report, can you list all states with their cheese production values, even if they didn't produce any cheese in April of 2023?
What is the total for NEW JERSEY?

```
SELECT c.Year, c.Period, c.State_ANSI, s.State, SUM(c.Value)
FROM cheese_production c
LEFT JOIN state_lookup s
ON c.State_ANSI = s.State_ANSI
WHERE Year = 2023 AND c.Period = 'APR' AND s.State_ANSI = 34
GROUP BY c.Year, c.Period, c.State_ANSI, s.State;
```

**Answer:**`4889000`


**Question 7.**
Can you find the total yogurt production for states in the year 2022 which also have cheese production data from 2023? This will help the Dairy Division in their planning.

```
SELECT SUM(y.value) AS Total_Yogurt_Production
FROM yogurt_production y
WHERE y.Year = 2022 AND y.State_ANSI IN (
	SELECT DISTINCT c.State_ANSI
    FROM cheese_production c 
    WHERE c.Year = 2023);
```

**Answer:**`1171095000`

**Question 8.**
List all states from state_lookup that are missing from milk_production in 2023.
How many states are there?


```
SELECT COUNT(*) AS Missing_States
FROM state_lookup s
LEFT JOIN milk_production m 
ON s.State_ANSI = m.State_ANSI
AND m.Year = 2023
WHERE m.State_ANSI IS NULL
```

**Answer:**`26`

**Question 9.**
List all states with their cheese production values, including states that didn't produce any cheese in April 2023.
Did Delaware produce any cheese in April 2023?


```
SELECT s.state,
CASE
WHEN c.Value IS NULL THEN 0
ELSE c.Value
END AS Cheese_Production
FROM state_lookup s 
LEFT JOIN cheese_production c 
ON s.State_ANSI = c.State_ANSI
	AND c.Year = 2023
    AND c.Period = 'APR';

```

**Answer:**`No`

**Question 10.**
Find the average coffee production for all years where the honey production exceeded 1 million.

```
SELECT AVG(c.Value)
FROM coffee_production c 
WHERE c.Year IN (	
	SELECT h.Year FROM honey_production h
    WHERE h.Value > 1000000);
```

**Answer:**`6426666`

