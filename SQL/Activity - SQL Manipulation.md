# === SQL MANIPULATION ACTIVITY ===

## --- Part 3: Create Table ---
[CREATE TABLE statement you used]
```sql
sqlite> CREATE TABLE "pokedex_test" (
(x1...>     "pokemon_id" TEXT PRIMARY KEY,
(x1...>     "name" STRING NOT NULL,
(x1...>     "hp" INTEGER, "attack" INTEGER);

sqlite> .schema
CREATE TABLE IF NOT EXISTS "pokedex_raw"(
"pokemon_id" TEXT, "name" TEXT, "type1" TEXT, "type2" TEXT,
 "hp" TEXT, "attack" TEXT, "defense" TEXT, "evolution_stage" TEXT,
 "evolves_to" TEXT);
CREATE TABLE IF NOT EXISTS "pokedex_test" (
    "pokemon_id" TEXT PRIMARY KEY,
    "name" STRING NOT NULL,
    "hp" INTEGER, "attack" INTEGER);
```

## --- Part 4: Insert Data ---
[INSERT INTO statements you used]
```sql
sqlite> INSERT INTO "pokedex_test" (pokemon_id, name, hp, attack) VALUES (2, 'Venusaur', 80, 82);
sqlite> INSERT INTO "pokedex_test" (pokemon_id, name, hp, attack) VALUES (3, 'Charmeleon', 58, 64);
sqlite> INSERT INTO "pokedex_test" (pokemon_id, name, hp, attack) VALUES (4, 'Caterpie', 45, 30);
sqlite> INSERT INTO "pokedex_test" (pokemon_id, name, hp, attack) VALUES (5, 'Magikarp', 20, 10);
```

[Output of SELECT * FROM pokedex_test after inserting]
```sql
sqlite> SELECT * FROM pokedex_test;
pokemon_id  name        hp  attack
----------  ----------  --  ------
1           Bulbasaur   45  49
2           Venusaur    80  82
3           Charmeleon  58  64
4           Caterpie    45  30
5           Magikarp    20  10
```

## --- Part 5: Update Data ---
[UPDATE statement you used]
```sql
sqlite> UPDATE pokedex_test
   ...> SET hp = 50, attack = 60
   ...> WHERE pokemon_id = 1;
```
[Output of SELECT * FROM pokedex_test after updating]
```sql
sqlite> SELECT * FROM pokedex_test;
pokemon_id  name        hp  attack
----------  ----------  --  ------
1           Bulbasaur   50  60
2           Venusaur    80  82
3           Charmeleon  58  64
4           Caterpie    45  30
5           Magikarp    20  10
```

## --- Part 6: Delete Data ---
[DELETE statement you used]
```sql
sqlite> DELETE FROM pokedex_test
   ...> WHERE name = 'Magikarp';
```
[Output of SELECT * FROM pokedex_test after deleting]
```sql
sqlite> SELECT * FROM pokedex_test;
pokemon_id  name        hp  attack
----------  ----------  --  ------
1           Bulbasaur   50  60
2           Venusaur    80  82
3           Charmeleon  58  64
4           Caterpie    45  30
```

## --- Part 7: Constraint Exploration ---
[INSERT statement that violated the identifier constraint + error output]
```sql
sqlite> INSERT INTO pokedex_test (pokemon_id, name, hp, attack) VALUES (3, 'Squirtle', 44, 48);
Runtime error: UNIQUE constraint failed: pokedex_test.pokemon_id (19)
```
[INSERT statement that violated the NOT NULL constraint + error output]
```sql
sqlite> INSERT INTO pokedex_test (pokemon_id, hp, attack) VALUES (5, 44, 48);
Runtime error: NOT NULL constraint failed: pokedex_test.name (19)
```

## --- Post-activity ---
[Output of SELECT * FROM pokedex_test]
```sql
sqlite> SELECT * FROM pokedex_test;
pokemon_id  name        hp  attack
----------  ----------  --  ------
1           Bulbasaur   50  60
2           Venusaur    80  82
3           Charmeleon  58  64
4           Caterpie    45  30
```

## --- Reflection Questions ---
1. What happens when you insert a duplicate value in the identifier column?
	- It will violate the UNIQUE constraint so it will go into runtime error, the table will not be modified.
2. What happens when you insert a record with a missing name value?
	- It will violate the NOT NULL constraint so it will go into runtime error, the table will not be modified.
3. Why are constraints important when designing database tables?
	- If we allow no constraint, then the data inside the table will not be predictable, you will see string values in the column that one would expect to be a number like price or ratings. So we need this constraints to make sure the database will not have bad data. 