# \=== SQL QUERIES ACTIVITY \===

```sql
CREATE TABLE pokedex_raw (
	'pokemon_id' INTEGER,
	'name' TEXT,
	'type1' TEXT,
	'type2' TEXT,
	'hp' INTEGER,
	'attack' INTEGER,
	'defense' INTEGER,
	'evolution_stage' TEXT,
	'evolves_to' TEXT
);

.mode csv
.import 'pokedex_raw.csv' --skip 1 pokedex_raw
.headers on
.mode column

SELECT * FROM pokedex_raw
```

## --- Part 2: Basic Retrieval ---
[Query: Retrieve all data]
```sql
sqlite> SELECT * FROM pokedex_raw;
pokemon_id  name        type1     type2   hp   attack  defense  evolution_stage  evolves_to
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------
1           Bulbasaur   Grass     Poison  45   49      49       1                Ivysaur
2           Ivysaur     Grass     Poison  60   62      63       2                Venusaur
3           Venusaur    Grass     Poison  80   82      83       3
4           Charmander  Fire              39   52      43       1                Charmeleon
5           Charmeleon  Fire              58   64      58       2                Charizard
6           Charizard   Fire      Flying  78   84      78       3
7           Squirtle    Water             44   48      65       1                Wartortle
8           Wartortle   Water             59   63      80       2                Blastoise
9           Blastoise   Water             79   83      100      3
10          Caterpie    Bug               45   30      35       1                Metapod
11          Metapod     Bug               50   20      55       2                Butterfree
12          Butterfree  Bug       Flying  60   45      50       3
13          Pikachu     Electric          35   55      40       2                Raichu
14          Pikachu     Electric          35   55      40       2                Raichu
15          Raichu      Electric          60   90      55       3
16          Eevee       Normal            55   55      50       1
17          Eevee       Normal            55   55      50       1
18          Mewtwo      Psychic           106  110     90
19          Gyarados    Water     Flying  95   125     79       2
20          Magikarp    Water             20   10      55       1                Gyarados
```
[Query: Retrieve name and type1 only]
```sql
sqlite> SELECT name, type1
   ...> FROM pokedex_raw;
name        type1
----------  --------
Bulbasaur   Grass
Ivysaur     Grass
Venusaur    Grass
Charmander  Fire
Charmeleon  Fire
Charizard   Fire
Squirtle    Water
Wartortle   Water
Blastoise   Water
Caterpie    Bug
Metapod     Bug
Butterfree  Bug
Pikachu     Electric
Pikachu     Electric
Raichu      Electric
Eevee       Normal
Eevee       Normal
Mewtwo      Psychic
Gyarados    Water
Magikarp    Water
```
## --- Part 3: Filtering Data ---
[Query: Exact match — Fire type]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> WHERE type1 IS 'Fire';
pokemon_id  name        type1  type2   hp  attack  defense  evolution_stage  evolves_to
----------  ----------  -----  ------  --  ------  -------  ---------------  ----------
4           Charmander  Fire           39  52      43       1                Charmeleon
5           Charmeleon  Fire           58  64      58       2                Charizard
6           Charizard   Fire   Flying  78  84      78       3
```
[Query: Multiple values — Water or Grass]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> WHERE type1 IN ('Water', 'Grass');
pokemon_id  name       type1  type2   hp  attack  defense  evolution_stage  evolves_to
----------  ---------  -----  ------  --  ------  -------  ---------------  ----------
1           Bulbasaur  Grass  Poison  45  49      49       1                Ivysaur
2           Ivysaur    Grass  Poison  60  62      63       2                Venusaur
3           Venusaur   Grass  Poison  80  82      83       3
7           Squirtle   Water          44  48      65       1                Wartortle
8           Wartortle  Water          59  63      80       2                Blastoise
9           Blastoise  Water          79  83      100      3
19          Gyarados   Water  Flying  95  125     79       2
20          Magikarp   Water          20  10      55       1                Gyarados
```
[Query: Pattern match — names starting with B]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> WHERE name LIKE 'B%';
pokemon_id  name        type1  type2   hp  attack  defense  evolution_stage  evolves_to
----------  ----------  -----  ------  --  ------  -------  ---------------  ----------
1           Bulbasaur   Grass  Poison  45  49      49       1                Ivysaur
9           Blastoise   Water          79  83      100      3
12          Butterfree  Bug    Flying  60  45      50       3
```
[Query: Numeric range — attack between 50 and 100]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> WHERE attack BETWEEN 50 AND 100;
pokemon_id  name        type1     type2   hp  attack  defense  evolution_stage  evolves_to
----------  ----------  --------  ------  --  ------  -------  ---------------  ----------
2           Ivysaur     Grass     Poison  60  62      63       2                Venusaur
3           Venusaur    Grass     Poison  80  82      83       3
4           Charmander  Fire              39  52      43       1                Charmeleon
5           Charmeleon  Fire              58  64      58       2                Charizard
6           Charizard   Fire      Flying  78  84      78       3
8           Wartortle   Water             59  63      80       2                Blastoise
9           Blastoise   Water             79  83      100      3
13          Pikachu     Electric          35  55      40       2                Raichu
14          Pikachu     Electric          35  55      40       2                Raichu
15          Raichu      Electric          60  90      55       3
16          Eevee       Normal            55  55      50       1
17          Eevee       Normal            55  55      50       1
```
## --- Part 4: Sorting Data ---
[Query: Basic sort by attack]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> ORDER BY attack;
pokemon_id  name        type1     type2   hp   attack  defense  evolution_stage  evolves_to
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------
20          Magikarp    Water             20   10      55       1                Gyarados
11          Metapod     Bug               50   20      55       2                Butterfree
10          Caterpie    Bug               45   30      35       1                Metapod
12          Butterfree  Bug       Flying  60   45      50       3
7           Squirtle    Water             44   48      65       1                Wartortle
1           Bulbasaur   Grass     Poison  45   49      49       1                Ivysaur
4           Charmander  Fire              39   52      43       1                Charmeleon
13          Pikachu     Electric          35   55      40       2                Raichu
14          Pikachu     Electric          35   55      40       2                Raichu
16          Eevee       Normal            55   55      50       1
17          Eevee       Normal            55   55      50       1
2           Ivysaur     Grass     Poison  60   62      63       2                Venusaur
8           Wartortle   Water             59   63      80       2                Blastoise
5           Charmeleon  Fire              58   64      58       2                Charizard
3           Venusaur    Grass     Poison  80   82      83       3
9           Blastoise   Water             79   83      100      3
6           Charizard   Fire      Flying  78   84      78       3
15          Raichu      Electric          60   90      55       3
18          Mewtwo      Psychic           106  110     90
19          Gyarados    Water     Flying  95   125     79       2
```
[Query: Sort by attack ascending]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> ORDER BY attack ASC;
pokemon_id  name        type1     type2   hp   attack  defense  evolution_stage  evolves_to
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------
20          Magikarp    Water             20   10      55       1                Gyarados
11          Metapod     Bug               50   20      55       2                Butterfree
10          Caterpie    Bug               45   30      35       1                Metapod
12          Butterfree  Bug       Flying  60   45      50       3
7           Squirtle    Water             44   48      65       1                Wartortle
1           Bulbasaur   Grass     Poison  45   49      49       1                Ivysaur
4           Charmander  Fire              39   52      43       1                Charmeleon
13          Pikachu     Electric          35   55      40       2                Raichu
14          Pikachu     Electric          35   55      40       2                Raichu
16          Eevee       Normal            55   55      50       1
17          Eevee       Normal            55   55      50       1
2           Ivysaur     Grass     Poison  60   62      63       2                Venusaur
8           Wartortle   Water             59   63      80       2                Blastoise
5           Charmeleon  Fire              58   64      58       2                Charizard
3           Venusaur    Grass     Poison  80   82      83       3
9           Blastoise   Water             79   83      100      3
6           Charizard   Fire      Flying  78   84      78       3
15          Raichu      Electric          60   90      55       3
18          Mewtwo      Psychic           106  110     90
19          Gyarados    Water     Flying  95   125     79       2
```
[Query: Sort by attack descending]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> ORDER BY attack DESC;
pokemon_id  name        type1     type2   hp   attack  defense  evolution_stage  evolves_to
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------
19          Gyarados    Water     Flying  95   125     79       2
18          Mewtwo      Psychic           106  110     90
15          Raichu      Electric          60   90      55       3
6           Charizard   Fire      Flying  78   84      78       3
9           Blastoise   Water             79   83      100      3
3           Venusaur    Grass     Poison  80   82      83       3
5           Charmeleon  Fire              58   64      58       2                Charizard
8           Wartortle   Water             59   63      80       2                Blastoise
2           Ivysaur     Grass     Poison  60   62      63       2                Venusaur
13          Pikachu     Electric          35   55      40       2                Raichu
14          Pikachu     Electric          35   55      40       2                Raichu
16          Eevee       Normal            55   55      50       1
17          Eevee       Normal            55   55      50       1
4           Charmander  Fire              39   52      43       1                Charmeleon
1           Bulbasaur   Grass     Poison  45   49      49       1                Ivysaur
7           Squirtle    Water             44   48      65       1                Wartortle
12          Butterfree  Bug       Flying  60   45      50       3
10          Caterpie    Bug               45   30      35       1                Metapod
11          Metapod     Bug               50   20      55       2                Butterfree
20          Magikarp    Water             20   10      55       1                Gyarados
```
## --- Part 5: Limiting Results ---
[Query: Return 5 rows only]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> ORDER BY attack DESC
   ...> LIMIT 5;
pokemon_id  name       type1     type2   hp   attack  defense  evolution_stage  evolves_to
----------  ---------  --------  ------  ---  ------  -------  ---------------  ----------
19          Gyarados   Water     Flying  95   125     79       2
18          Mewtwo     Psychic           106  110     90
15          Raichu     Electric          60   90      55       3
6           Charizard  Fire      Flying  78   84      78       3
9           Blastoise  Water             79   83      100      3
```
[Query: Top 5 by attack — name and attack columns only]
```sql
sqlite> SELECT name, attack
   ...> FROM pokedex_raw
   ...> ORDER BY attack DESC
   ...> LIMIT 5;
name       attack
---------  ------
Gyarados   125
Mewtwo     110
Raichu     90
Charizard  84
Blastoise  83
```
## --- Part 6: Combined Query ---
[Your combined query]
```sql
sqlite> SELECT name, hp, attack
   ...> FROM pokedex_raw
   ...> WHERE hp > 50 AND attack > 50
   ...> ORDER BY attack DESC
   ...> LIMIT 3;
```
[Output]
```sql
name      hp   attack
--------  ---  ------
Gyarados  95   125
Mewtwo    106  110
Raichu    60   90
```

## --- Part 7: Exploration Tasks ---
[Query: Attack > 80 + output]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> WHERE attack > 80;
pokemon_id  name       type1     type2   hp   attack  defense  evolution_stage  evolves_to
----------  ---------  --------  ------  ---  ------  -------  ---------------  ----------
3           Venusaur   Grass     Poison  80   82      83       3
6           Charizard  Fire      Flying  78   84      78       3
9           Blastoise  Water             79   83      100      3
15          Raichu     Electric          60   90      55       3
18          Mewtwo     Psychic           106  110     90
19          Gyarados   Water     Flying  95   125     79       2
```
[Query: HP < 50 + output]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> WHERE hp < 50;
pokemon_id  name        type1     type2   hp  attack  defense  evolution_stage  evolves_to
----------  ----------  --------  ------  --  ------  -------  ---------------  ----------
1           Bulbasaur   Grass     Poison  45  49      49       1                Ivysaur
4           Charmander  Fire              39  52      43       1                Charmeleon
7           Squirtle    Water             44  48      65       1                Wartortle
10          Caterpie    Bug               45  30      35       1                Metapod
13          Pikachu     Electric          35  55      40       2                Raichu
14          Pikachu     Electric          35  55      40       2                Raichu
20          Magikarp    Water             20  10      55       1                Gyarados
```
[Query: Fire type + row count observed]
```sql
SELECT COUNT(*)
   ...> FROM pokedex_raw
   ...> WHERE type1 IS 'Fire';
COUNT(*)
--------
3
```
[Query: Grass type + row count observed]
```sql
sqlite> SELECT COUNT(*)
   ...> FROM pokedex_raw
   ...> WHERE type1 IS 'Grass';
COUNT(*)
--------
3
```

## --- Reflection Questions ---
1. How does filtering affect the number of rows returned?
	- Filtering only return rows that match the condition, so it almost always return less than the total amount of rows in the table
2. How does sorting affect how results are viewed?
	- Sorting lets us expect that the data will be presented in order by the column we select it to sort from 
3. What is the purpose of limiting results?
	- The necessity and requirement of LIMIT clause becomes more apparent the larger the database grows. There are also other use cases like TOP 10 rankings but most of the time its used as an optimization.
4. How do multiple conditions affect query results?
	- Multiple conditions narrows the result further down to orders of magnitude depending on the conditions