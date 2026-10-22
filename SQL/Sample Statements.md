```sql
CREATE TABLE table_name (
   column_1 data_type, 
   column_2 data_type, 
   column_3 data_type
);
```

```sql
INSERT INTO celebs (id, name, age) 
VALUES (1, 'Justin Bieber', 29);
```

```sql
ALTER TABLE celebs 
ADD COLUMN twitter_handle TEXT;
```

```sql
UPDATE celebs 
SET twitter_handle = '@taylorswift13' 
WHERE id = 4; 
```

```sql
DELETE FROM celebs 
WHERE twitter_handle IS NULL;
```

```sql
CREATE TABLE celebs (
   id INTEGER PRIMARY KEY, 
   name TEXT UNIQUE,
   date_of_birth TEXT NOT NULL,
   date_of_death TEXT DEFAULT 'Not Applicable'
);
```

```sql
CREATE TABLE IF NOT EXISTS friends (
  id INTEGER,
  name TEXT,
  birthday DATE
);

INSERT INTO friends (name, birthday) 
VALUES ('Ororo Munroe', 'May 30th, 1940');

SELECT * FROM friends;

INSERT INTO friends (name, birthday) 
VALUES ('John Doe', 'May 30th, 1940');

INSERT INTO friends (name, birthday) 
VALUES ('Jane Doe', 'May 30th, 1940');

UPDATE friends
SET name = 'Storm'
WHERE name = 'Ororo Munroe';

SELECT * FROM friends;

ALTER TABLE friends
ADD COLUMN email TEXT;

UPDATE friends
SET email = 'storm@codecademy.com'
WHERE name = 'Storm';

UPDATE friends
SET email = 'john@codecademy.com'
WHERE name = 'John Doe';

UPDATE friends
SET email = 'jane@codecademy.com'
WHERE name = 'Jane Doe';

SELECT * FROM friends;

DELETE FROM friends
WHERE name = 'Storm';

SELECT * FROM friends;
```

```sql
SELECT DISTINCT year
FROM movies;
```

```sql
SELECT * 
FROM movies
WHERE year > 2014;
```

```sql
SELECT *
FROM movies
WHERE name LIKE 'Se_en'
```

```sql
SELECT *
FROM movies
WHERE name LIKE 'The %';
```

```sql
SELECT name
FROM movies
WHERE imdb_rating ISNULL
```

```sql
SELECT * 
FROM movies
WHERE year BETWEEN 1970 AND 1979;
```

```sql
SELECT *
FROM movies
WHERE year < 1985
  AND genre = 'horror';
```

```sql
SELECT *
FROM movies
WHERE genre = 'romance'
   OR genre = 'comedy';
```

```sql
  SELECT *
  FROM movies
  ORDER BY imdb_rating DESC
  LIMIT 3;
```

```sql
SELECT name,
 CASE
  WHEN imdb_rating > 8 THEN 'Fantastic'
  WHEN imdb_rating > 6 THEN 'Poorly Received'
  ELSE 'Avoid at All Costs'
 END AS 'Review'
FROM movies;
```

```sql
SELECT name,
  CASE
    WHEN genre = 'comedy' THEN 'Chill'
    WHEN genre = 'romance' THEN 'Chill'
    ELSE 'Intense'
  END AS 'Mood'
FROM movies;
```