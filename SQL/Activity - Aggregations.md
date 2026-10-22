# \=== SQL AGGREGATIONS ACTIVITY \===

## --- Part 1: Basic Aggregations ---
[Query: Count total rows + output]
```sql
sqlite> SELECT COUNT(*)
   ...> FROM pokedex_raw;
COUNT(*)
--------
      20
```
[Query: Sum of attack + output]
```sql
sqlite> SELECT SUM(attack)
   ...> FROM pokedex_raw;
SUM(attack)
-----------
       1237
```
[Query: Average attack + output]
```sql
sqlite> SELECT AVG(attack)
   ...> FROM pokedex_raw;
AVG(attack)
-----------
      61.85
```
[Query: Minimum HP + output]
```sql
sqlite> SELECT MIN(hp)
   ...> FROM pokedex_raw;
MIN(hp)
-------
     20
```
[Query: Maximum defense + output]
```sql
sqlite> SELECT MAX(defense)
   ...> FROM pokedex_raw;
MAX(defense)
------------
         100
```


## --- Part 2: Grouping Data ---
[Query: Count per type1 + output]
```sql
sqlite> SELECT type1, COUNT(*)
   ...> FROM pokedex_raw
   ...> GROUP BY type1;
 type1    COUNT(*)
--------  --------
Bug              3
Electric         3
Fire             3
Grass            3
Normal           2
Psychic          1
Water            5
```
[Query: Average attack per type1 + output]
```sql
sqlite> SELECT type1, AVG(attack)
   ...> FROM pokedex_raw
   ...> GROUP BY type1;
 type1       AVG(attack)
--------  ------------------
Bug       31.666666666666668
Electric  66.666666666666671
Fire      66.666666666666671
Grass     64.333333333333329
Normal                  55.0
Psychic                110.0
Water                   65.8
```


## --- Part 3: Multiple Aggregations ---
[Query: All four aggregates per type1 + output]
```sql
sqlite> SELECT type1, COUNT(name), AVG(attack), MAX(attack), MIN(attack)
   ...> FROM pokedex_raw
   ...> GROUP BY type1;
 type1    COUNT(name)     AVG(attack)      MAX(attack)  MIN(attack)
--------  -----------  ------------------  -----------  -----------
Bug                 3  31.666666666666668           45           20
Electric            3  66.666666666666671           90           55
Fire                3  66.666666666666671           84           52
Grass               3  64.333333333333329           82           49
Normal              2                55.0           55           55
Psychic             1               110.0          110          110
Water               5                65.8          125           10
```


## --- Part 4: Filtering Groups ---
[Query: Groups with more than 2 Pokémon + output]
```sql
sqlite> SELECT type1, COUNT(name), AVG(attack), MAX(attack), MIN(attack)
   ...> FROM pokedex_raw
   ...> GROUP BY type1
   ...> HAVING COUNT(name) > 2;
 type1    COUNT(name)     AVG(attack)      MAX(attack)  MIN(attack)
--------  -----------  ------------------  -----------  -----------
Bug                 3  31.666666666666668           45           20
Electric            3  66.666666666666671           90           55
Fire                3  66.666666666666671           84           52
Grass               3  64.333333333333329           82           49
Water               5                65.8          125           10
```
[Query: Groups with average attack > 60 + output]
```sql
sqlite> SELECT type1, COUNT(name), AVG(attack), MAX(attack), MIN(attack)
   ...> FROM pokedex_raw
   ...> GROUP BY type1
   ...> HAVING AVG(attack) > 60;
 type1    COUNT(name)     AVG(attack)      MAX(attack)  MIN(attack)
--------  -----------  ------------------  -----------  -----------
Electric            3  66.666666666666671           90           55
Fire                3  66.666666666666671           84           52
Grass               3  64.333333333333329           82           49
Psychic             1               110.0          110          110
Water               5                65.8          125           10
```
[Observation: What changed before and after filtering?]
The result of the part 3 has been filtered using the `HAVING` clause as opposed to `WHERE` clause. 


## --- Part 5: Exploration Tasks ---
[Query: Highest avg attack by type + output]
```sql
sqlite> SELECT type1, AVG(attack)
   ...> FROM pokedex_raw
   ...> GROUP BY 1
   ...> ORDER BY 2 DESC;
 type1       AVG(attack)
--------  ------------------
Psychic                110.0
Fire      66.666666666666671
Electric  66.666666666666671
Water                   65.8
Grass     64.333333333333329
Normal                  55.0
Bug       31.666666666666668
```
[Query: Lowest avg HP by type + output]
```sql
sqlite> SELECT type1, AVG(attack)
   ...> FROM pokedex_raw
   ...> GROUP BY 1
   ...> ORDER BY 2 ASC;
 type1       AVG(attack)
--------  ------------------
Bug       31.666666666666668
Normal                  55.0
Grass     64.333333333333329
Water                   65.8
Electric  66.666666666666671
Fire      66.666666666666671
Psychic                110.0
```
[Observation: Any unusual or unexpected values you noticed]
Physic type pokemons seems to have 2x more attack than most while Bug type pokemons has half attack compared to most. But Physic type only has 1 pokemon entry 


## --- Reflection Questions ---
1. How does grouping change the structure of results?
	- The rows becomes unique values on the column selected in the `GROUP BY` clause
2. What is the difference between filtering rows and filtering groups?
	- When filtering rows, you can use `WHERE` but `WHERE` cant be used on `aggregate functions` and it must also be before `GROUP BY` clause so for filtering groups you need to use the `HAVING` clause
3. How do aggregation functions summarize data?
	- It aggregates the data from all the rows, when you use `GROUP BY`, it will aggregate per unique value of the selected column in the `GROUP BY` clause
4. What happens when grouping is not used with aggregations?
	- It will aggregate from all the rows in the table and returns only 1 row of result 