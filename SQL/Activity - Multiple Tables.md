# \=== SQL MULTIPLE TABLES ACTIVITY\===

## --- Part 1: Create a Second Table ---
[CREATE TABLE statement for your second table]
```sql
sqlite> CREATE TABLE pokedex_categories(
(x1...> 'id' INTEGER PRIMARY KEY,
(x1...> 'name' TEXT,
(x1...> 'category' TEXT,
(x1...> FOREIGN KEY(name) REFERENCES pokedex_raw(name)
(x1...> );
sqlite> .schema
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
CREATE TABLE pokedex_categories(
'id' INTEGER PRIMARY KEY,
'name' TEXT,
'category' TEXT,
FOREIGN KEY(name) REFERENCES pokedex_raw(name)
);
```
[INSERT statements used to populate it]
```sql
sqlite> INSERT INTO pokedex_categories (name, category) VALUES ('Bulbasaur', 'Starter');
INSERT INTO pokedex_categories (name, category) VALUES ('Venusaur', 'Starter');
INSERT INTO pokedex_categories (name, category) VALUES ('Charmander', 'Starter');
INSERT INTO pokedex_categories (name, category) VALUES ('Charizard', 'Starter');
INSERT INTO pokedex_categories (name, category) VALUES ('Squirtle', 'Starter');
INSERT INTO pokedex_categories (name, category) VALUES ('Blastoise', 'Starter');
INSERT INTO pokedex_categories (name, category) VALUES ('Pikachu', 'Mascot');
INSERT INTO pokedex_categories (name, category) VALUES ('Eevee', 'Mascot');
INSERT INTO pokedex_categories (name, category) VALUES ('Mewtwo', 'Legendary');
INSERT INTO pokedex_categories (name, category) VALUES ('Magikarp', 'Common');
```
[Output of SELECT * FROM your second table]
```sql
sqlite> SELECT * FROM pokedex_categories;
id     name     category
--  ----------  ---------
 1  Bulbasaur   Starter
 2  Venusaur    Starter
 3  Charmander  Starter
 4  Charizard   Starter
 5  Squirtle    Starter
 6  Blastoise   Starter
 7  Pikachu     Mascot
 8  Eevee       Mascot
 9  Mewtwo      Legendary
10  Magikarp    Common
```

## --- Part 2: Understand the Relationship ---
[Connecting column identified]
```sql
sqlite> .schema
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
CREATE TABLE pokedex_categories(
'id' INTEGER PRIMARY KEY,
'name' TEXT,
'category' TEXT,
FOREIGN KEY(name) REFERENCES pokedex_raw(name)
);
```
Connecting column is the name column
[Type of relationship and explanation]
The type or relationship is one to one as there's only one entry from one table to another table with their connecting column


## --- Part 3: INNER JOIN ---
[Query: INNER JOIN with columns from both tables + output]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> JOIN pokedex_categories
   ...>   ON pokedex_raw.name = pokedex_categories.name;
pokemon_id     name      type1    type2   hp   attack  defense  evolution_stage  evolves_to  id     name     category
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------  --  ----------  ---------
         1  Bulbasaur   Grass     Poison   45      49       49  1                Ivysaur      1  Bulbasaur   Starter
         3  Venusaur    Grass     Poison   80      82       83  3                             2  Venusaur    Starter
         4  Charmander  Fire               39      52       43  1                Charmeleon   3  Charmander  Starter
         6  Charizard   Fire      Flying   78      84       78  3                             4  Charizard   Starter
         7  Squirtle    Water              44      48       65  1                Wartortle    5  Squirtle    Starter
         9  Blastoise   Water              79      83      100  3                             6  Blastoise   Starter
        13  Pikachu     Electric           35      55       40  2                Raichu       7  Pikachu     Mascot
        14  Pikachu     Electric           35      55       40  2                Raichu       7  Pikachu     Mascot
        16  Eevee       Normal             55      55       50  1                             8  Eevee       Mascot
        17  Eevee       Normal             55      55       50  1                             8  Eevee       Mascot
        18  Mewtwo      Psychic           106     110       90                                9  Mewtwo      Legendary
        20  Magikarp    Water              20      10       55  1                Gyarados    10  Magikarp    Common

sqlite> SELECT pokedex_raw.name, category, hp, attack, defense
   ...> FROM pokedex_raw
   ...> JOIN pokedex_categories
   ...>   ON pokedex_raw.name = pokedex_categories.name;
   name     category   hp   attack  defense
----------  ---------  ---  ------  -------
Bulbasaur   Starter     45      49       49
Venusaur    Starter     80      82       83
Charmander  Starter     39      52       43
Charizard   Starter     78      84       78
Squirtle    Starter     44      48       65
Blastoise   Starter     79      83      100
Pikachu     Mascot      35      55       40
Pikachu     Mascot      35      55       40
Eevee       Mascot      55      55       50
Eevee       Mascot      55      55       50
Mewtwo      Legendary  106     110       90
Magikarp    Common      20      10       55
```

## --- Part 4: LEFT JOIN ---
[Query: LEFT JOIN with pokedex_raw as left table + output]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw
   ...> LEFT JOIN pokedex_categories
   ...>   ON pokedex_raw.name = pokedex_categories.name;
pokemon_id     name      type1    type2   hp   attack  defense  evolution_stage  evolves_to  id     name     category
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------  --  ----------  ---------
         1  Bulbasaur   Grass     Poison   45      49       49  1                Ivysaur      1  Bulbasaur   Starter
         2  Ivysaur     Grass     Poison   60      62       63  2                Venusaur
         3  Venusaur    Grass     Poison   80      82       83  3                             2  Venusaur    Starter
         4  Charmander  Fire               39      52       43  1                Charmeleon   3  Charmander  Starter
         5  Charmeleon  Fire               58      64       58  2                Charizard
         6  Charizard   Fire      Flying   78      84       78  3                             4  Charizard   Starter
         7  Squirtle    Water              44      48       65  1                Wartortle    5  Squirtle    Starter
         8  Wartortle   Water              59      63       80  2                Blastoise
         9  Blastoise   Water              79      83      100  3                             6  Blastoise   Starter
        10  Caterpie    Bug                45      30       35  1                Metapod
        11  Metapod     Bug                50      20       55  2                Butterfree
        12  Butterfree  Bug       Flying   60      45       50  3
        13  Pikachu     Electric           35      55       40  2                Raichu       7  Pikachu     Mascot
        14  Pikachu     Electric           35      55       40  2                Raichu       7  Pikachu     Mascot
        15  Raichu      Electric           60      90       55  3
        16  Eevee       Normal             55      55       50  1                             8  Eevee       Mascot
        17  Eevee       Normal             55      55       50  1                             8  Eevee       Mascot
        18  Mewtwo      Psychic           106     110       90                                9  Mewtwo      Legendary
        19  Gyarados    Water     Flying   95     125       79  2
        20  Magikarp    Water              20      10       55  1                Gyarados    10  Magikarp    Common
```
[Observation: Which rows showed NULL and why]
The rows that has no match name from the pokemon_categories table had null values on the columns of pokemon_categories, its the documented/intended behavior from using LEFT JOIN

## --- Part 5: Compare JOIN Types ---
[Observation: Row count difference between INNER JOIN and LEFT JOIN]
There seems to be less rows on the new table compared to the first table
[Observation: Which Pokémon appeared in LEFT JOIN but not INNER JOIN]
I said part 2 that its one to one relationship and I've inserted 10 rows in the second table but the inner join confirms 12 counts, upon looking at the original table, there are duplicated rows. 
Pokemons in LEFT JOIN but not in INNER JOIN:
```sql
sqlite> SELECT r.name
   ...> FROM pokedex_raw AS r
   ...> LEFT JOIN pokedex_categories AS c
   ...> ON r.name = c.name
   ...> WHERE c.id ISNULL;
   name
----------
Ivysaur
Charmeleon
Wartortle
Caterpie
Metapod
Butterfree
Raichu
Gyarados
```

## --- Part 6: Use Aliases ---
[Rewritten INNER JOIN query with aliases]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw AS r
   ...> JOIN pokedex_categories AS c
   ...>   ON r.name = c.name;
pokemon_id     name      type1    type2   hp   attack  defense  evolution_stage  evolves_to  id     name     category
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------  --  ----------  ---------
         1  Bulbasaur   Grass     Poison   45      49       49  1                Ivysaur      1  Bulbasaur   Starter
         3  Venusaur    Grass     Poison   80      82       83  3                             2  Venusaur    Starter
         4  Charmander  Fire               39      52       43  1                Charmeleon   3  Charmander  Starter
         6  Charizard   Fire      Flying   78      84       78  3                             4  Charizard   Starter
         7  Squirtle    Water              44      48       65  1                Wartortle    5  Squirtle    Starter
         9  Blastoise   Water              79      83      100  3                             6  Blastoise   Starter
        13  Pikachu     Electric           35      55       40  2                Raichu       7  Pikachu     Mascot
        14  Pikachu     Electric           35      55       40  2                Raichu       7  Pikachu     Mascot
        16  Eevee       Normal             55      55       50  1                             8  Eevee       Mascot
        17  Eevee       Normal             55      55       50  1                             8  Eevee       Mascot
        18  Mewtwo      Psychic           106     110       90                                9  Mewtwo      Legendary
        20  Magikarp    Water              20      10       55  1                Gyarados    10  Magikarp    Common
```
[Rewritten LEFT JOIN query with aliases]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw AS r
   ...> LEFT JOIN pokedex_categories AS c
   ...>   ON r.name = c.name;
pokemon_id     name      type1    type2   hp   attack  defense  evolution_stage  evolves_to  id     name     category
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------  --  ----------  ---------
         1  Bulbasaur   Grass     Poison   45      49       49  1                Ivysaur      1  Bulbasaur   Starter
         2  Ivysaur     Grass     Poison   60      62       63  2                Venusaur
         3  Venusaur    Grass     Poison   80      82       83  3                             2  Venusaur    Starter
         4  Charmander  Fire               39      52       43  1                Charmeleon   3  Charmander  Starter
         5  Charmeleon  Fire               58      64       58  2                Charizard
         6  Charizard   Fire      Flying   78      84       78  3                             4  Charizard   Starter
         7  Squirtle    Water              44      48       65  1                Wartortle    5  Squirtle    Starter
         8  Wartortle   Water              59      63       80  2                Blastoise
         9  Blastoise   Water              79      83      100  3                             6  Blastoise   Starter
        10  Caterpie    Bug                45      30       35  1                Metapod
        11  Metapod     Bug                50      20       55  2                Butterfree
        12  Butterfree  Bug       Flying   60      45       50  3
        13  Pikachu     Electric           35      55       40  2                Raichu       7  Pikachu     Mascot
        14  Pikachu     Electric           35      55       40  2                Raichu       7  Pikachu     Mascot
        15  Raichu      Electric           60      90       55  3
        16  Eevee       Normal             55      55       50  1                             8  Eevee       Mascot
        17  Eevee       Normal             55      55       50  1                             8  Eevee       Mascot
        18  Mewtwo      Psychic           106     110       90                                9  Mewtwo      Legendary
        19  Gyarados    Water     Flying   95     125       79  2
        20  Magikarp    Water              20      10       55  1                Gyarados    10  Magikarp    Common
```

## --- Part 7: Exploration Tasks ---
[Query: Pokémon in pokedex_raw with no match in second table + output]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw AS r
   ...> LEFT JOIN pokedex_categories AS c
   ...>   ON r.name = c.name
   ...> WHERE c.id ISNULL;
pokemon_id     name      type1    type2   hp  attack  defense  evolution_stage  evolves_to  id  name  category
----------  ----------  --------  ------  --  ------  -------  ---------------  ----------  --  ----  --------
         2  Ivysaur     Grass     Poison  60      62       63  2                Venusaur
         5  Charmeleon  Fire              58      64       58  2                Charizard
         8  Wartortle   Water             59      63       80  2                Blastoise
        10  Caterpie    Bug               45      30       35  1                Metapod
        11  Metapod     Bug               50      20       55  2                Butterfree
        12  Butterfree  Bug       Flying  60      45       50  3
        15  Raichu      Electric          60      90       55  3
        19  Gyarados    Water     Flying  95     125       79  2
```
[Query: COUNT comparison between JOIN types + output]
```sql
sqlite> SELECT COUNT(*)
   ...> FROM pokedex_raw AS r
   ...> JOIN pokedex_categories AS c
   ...>   ON r.name = c.name;
COUNT(*)
--------
      12
      
sqlite> SELECT COUNT(*)
   ...> FROM pokedex_raw AS r
   ...> LEFT JOIN pokedex_categories AS c
   ...>   ON r.name = c.name;
COUNT(*)
--------
      20
```
[Query: Combined data with filter + output]
```sql
sqlite> SELECT *
   ...> FROM pokedex_raw AS r
   ...> LEFT JOIN pokedex_categories AS c
   ...>   ON r.name = c.name
   ...> WHERE defense > 50;
pokemon_id     name      type1    type2   hp   attack  defense  evolution_stage  evolves_to  id    name     category
----------  ----------  --------  ------  ---  ------  -------  ---------------  ----------  --  ---------  ---------
         2  Ivysaur     Grass     Poison   60      62       63  2                Venusaur
         3  Venusaur    Grass     Poison   80      82       83  3                             2  Venusaur   Starter
         5  Charmeleon  Fire               58      64       58  2                Charizard
         6  Charizard   Fire      Flying   78      84       78  3                             4  Charizard  Starter
         7  Squirtle    Water              44      48       65  1                Wartortle    5  Squirtle   Starter
         8  Wartortle   Water              59      63       80  2                Blastoise
         9  Blastoise   Water              79      83      100  3                             6  Blastoise  Starter
        11  Metapod     Bug                50      20       55  2                Butterfree
        15  Raichu      Electric           60      90       55  3
        18  Mewtwo      Psychic           106     110       90                                9  Mewtwo     Legendary
        19  Gyarados    Water     Flying   95     125       79  2
        20  Magikarp    Water              20      10       55  1                Gyarados    10  Magikarp   Common
```

## --- Reflection Questions ---
1. What is the difference between `INNER JOIN` and `LEFT JOIN`?
	- INNER JOIN will only return the rows that had match on the connecting column
2. When would you use each type?
	- INNER JOIN for when you only want rows that had relations on both tables 
	- LEFT JOIN for only when you want to see a table and if it has relationship from another table
3. What happens when there is no matching record in a JOIN?
	- INNER JOIN will return nothing
	- LEFT JOIN will return all rows from first table with the columns on the second table having null values
4. Why are table aliases useful in queries with multiple tables?
	- The usefulness becomes more apparent the more complicated your queries are, with alias you don't have to type as much which in turn lessens the possibility of mistyping