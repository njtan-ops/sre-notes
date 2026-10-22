- There's a problem when you want to filter out rows with null characters
	- it could be '' (empty string)
	- it could be ' ' (whitespace)
```sql
sqlite> SELECT name, hp, attack, evolves_to
   ...> FROM pokedex_raw
   ...> WHERE evolves_to NOTNULL AND attack > 50
   ...> ORDER BY attack DESC;
name        hp   attack  evolves_to
----------  ---  ------  ----------
Gyarados    95   125
Mewtwo      106  110
Raichu      60   90
Charizard   78   84
Blastoise   79   83
Venusaur    80   82
Charmeleon  58   64      Charizard
Wartortle   59   63      Blastoise
Ivysaur     60   62      Venusaur
Pikachu     35   55      Raichu
Pikachu     35   55      Raichu
Eevee       55   55
Eevee       55   55
Charmander  39   52      Charmeleon
sqlite> SELECT name, hp, attack, evolves_to
   ...> FROM pokedex_raw
   ...> WHERE evolves_to IS NOT '' AND attack > 50
   ...> ORDER BY attack DESC;
name        hp   attack  evolves_to
----------  ---  ------  ----------
Mewtwo      106  110
Charmeleon  58   64      Charizard
Wartortle   59   63      Blastoise
Ivysaur     60   62      Venusaur
Pikachu     35   55      Raichu
Pikachu     35   55      Raichu
Charmander  39   52      Charmeleon
```

- One of the fix is to COALESCE and TRIM the column and do `!= ''`
```sql
sqlite> SELECT name, hp, attack, evolves_to
   ...> FROM pokedex_raw
   ...> WHERE TRIM(COALESCE(evolves_to, '')) != ''
   ...>   AND attack > 50
   ...> ORDER BY attack DESC
   ...> LIMIT 5;
name        hp  attack  evolves_to
----------  --  ------  ----------
Charmeleon  58  64      Charizard
Wartortle   59  63      Blastoise
Ivysaur     60  62      Venusaur
Pikachu     35  55      Raichu
Pikachu     35  55      Raichu
```

- Or you can cleanup the database first
```sql
sqlite> UPDATE pokedex_raw
   ...> SET evolves_to = NULL
   ...> WHERE TRIM(evolves_to) = '';
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

sqlite> SELECT * FROM pokedex_raw
   ...> WHERE evolves_to NOTNULL;
pokemon_id  name        type1     type2   hp  attack  defense  evolution_stage  evolves_to
----------  ----------  --------  ------  --  ------  -------  ---------------  ----------
1           Bulbasaur   Grass     Poison  45  49      49       1                Ivysaur
2           Ivysaur     Grass     Poison  60  62      63       2                Venusaur
4           Charmander  Fire              39  52      43       1                Charmeleon
5           Charmeleon  Fire              58  64      58       2                Charizard
7           Squirtle    Water             44  48      65       1                Wartortle
8           Wartortle   Water             59  63      80       2                Blastoise
10          Caterpie    Bug               45  30      35       1                Metapod
11          Metapod     Bug               50  20      55       2                Butterfree
13          Pikachu     Electric          35  55      40       2                Raichu
14          Pikachu     Electric          35  55      40       2                Raichu
20          Magikarp    Water             20  10      55       1                Gyarados
```



- When query did not return anything
```sql
SELECT name
  CASE
    WHEN genre IN ('romande', 'comedy') THEN 'Chill'
    ELSE 'Intense'
  END AS 'Mood'
FROM movies;
```
- This will not return anything
- Thats because you need to add a comma after the column name
```sql
SELECT name,
  CASE
    WHEN genre IN ('romande', 'comedy') THEN 'Chill'
    ELSE 'Intense'
  END AS 'Mood'
FROM movies;
```
