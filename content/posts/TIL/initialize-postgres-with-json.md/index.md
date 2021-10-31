---
title: "TIL: Initialize Postgres Database with JSON"
date: 2021-10-31
draft: true
---

{{< resize-image src="postgresql.jpg" alt="PostgreSQL Elephant logo">}}

# Today I Learned

Today I Learned how to load, process and insert data into a PostgreSQL database.

## Inserting data from JSON to PostgreSQL

I have prepared a file with one object per line.

```json
{"name": "Scrambled tofu-egg", "categories": ["BREAKFAST"], "ingredients": ["carrot", "potato", "avocado"]}
{"name": "Pancakes", "categories": ["SUPPER"], "ingredients": ["carrot", "potato"]}
{"name": "Meatballs", "categories": ["DINNER"], "ingredients": ["avocado"]}
```

now all I have to do is to run three commands in a transaction.

```sql
BEGIN;

CREATE TABLE recipes (
    id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    user_id bigint,
    name TEXT NOT NULL,
    categories TEXT [],
    ingredients TEXT []
);

-- remove table on COMMIT
CREATE TEMPORARY TABLE temp_import (doc JSON) ON COMMIT DROP;

-- copy json file to our temporary table
COPY temp_import
FROM
    '/migrations/000003_bootstrap_recipes.json';

-- copy data from temporary table to destined one
INSERT INTO
    recipes (name, categories, ingredients)
SELECT
    p.name,
    p.categories,
    p.ingredients
FROM
    temp_import l
    CROSS JOIN lateral json_populate_record(NULL :: temp_recipes, doc) AS p;

COMMIT;
```

As you can see it is quite easy to do. More issues appear when you want to do some data processing in the middle. In my case I needed to map categories and ingredients from user friendly string versions to IDs.

## Power of functions

I have two tables with `ingredient - ID` and `category - ID` pairs:

```sql
CREATE TABLE categories (
    id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    category TEXT NOT NULL
);

INSERT INTO
    categories (category)
VALUES
    ('UNKNOWN'),
    ('BREAKFAST'),
    ('LUNCH'),
    ('BRANCH'),
    ('DINNER'),
    ('SUPPER'),
    ('DESERT');

-----------------------------

CREATE TABLE ingredients (
    id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    ingredient TEXT NOT NULL
);

INSERT INTO
    ingredients (ingredient)
VALUES
    ('carrot'),
    ('potato'),
    ('avocado');
```

To map ingredients to its ids I used postgres functions

```sql
CREATE FUNCTION map_ingredient_names_to_ids(ingredient_names TEXT[]) RETURNS BIGINT[] AS $$
    declare
        ingredient_ids BIGINT [];
        ingredient_name TEXT;
        ingredient_id BIGINT;
    BEGIN
        FOREACH ingredient_name IN array ingredient_names LOOP
            ingredient_id = (SELECT id from ingredients WHERE ingredient = ingredient_name);
            -- Check for typos in ingredients. If name do not match to any from db, raise exception
            IF ingredient_id IS NULL THEN
                RAISE EXCEPTION 'Nonexistent ingredient %', ingredient_name;
            END IF;
            -- || is a postgres syntax for appending data to an array
            ingredient_ids = ingredient_ids || ingredient_id;
        END LOOP;
        RETURN ingredient_ids;
    END;
$$ LANGUAGE plpgsql;
```

I also created a similar function for mapping categories.

## Final solution

```sql
BEGIN;

-- Destined table I want to use in my service.
-- Notice that I am using BIGINT, no TEXT anymore.
CREATE TABLE recipes (
    id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    user_id bigint,
    name TEXT NOT NULL,
    categories bigint [],
    ingredients bigint []
);

-- Import recipes from JSON to db
CREATE FUNCTION map_category_names_to_ids(category_names TEXT[]) RETURNS BIGINT[] AS $$
    declare
        category_ids BIGINT [];
        category_name TEXT;
        category_id BIGINT;
    BEGIN
        FOREACH category_name IN array category_names LOOP
            category_id = (SELECT id from categories WHERE category = category_name);
            IF category_id IS NULL THEN
                RAISE EXCEPTION 'Nonexistent category %', category_name;
            END IF;
            category_ids = category_ids || category_id;
        END LOOP;
        RETURN category_ids;
    END;
$$ LANGUAGE plpgsql;

CREATE FUNCTION map_ingredient_names_to_ids(ingredient_names TEXT[]) RETURNS BIGINT[] AS $$
    declare
        ingredient_ids BIGINT [];
        ingredient_name TEXT;
        ingredient_id BIGINT;
    BEGIN
        FOREACH ingredient_name IN array ingredient_names LOOP
            ingredient_id = (SELECT id from ingredients WHERE ingredient = ingredient_name);
            IF ingredient_id IS NULL THEN
                RAISE EXCEPTION 'Nonexistent ingredient %', ingredient_name;
            END IF;
            ingredient_ids = ingredient_ids || ingredient_id;
        END LOOP;
        RETURN ingredient_ids;
    END;
$$ LANGUAGE plpgsql;
---

CREATE TEMPORARY TABLE temp_import (doc JSON) ON COMMIT DROP;

COPY temp_import
FROM
    '/migrations/000003_bootstrap_recipes.json';

-- Special temporary table with TEXT arrays instead of BIGINT.
-- This is needed to map to proper types in the next step
CREATE TEMPORARY TABLE temp_recipes (
    name TEXT NOT NULL,
    categories TEXT [],
    ingredients TEXT []
) ON COMMIT DROP;

INSERT INTO
    recipes (name, categories, ingredients)
SELECT
    p.name,
    map_category_names_to_ids(p.categories),
    map_ingredient_names_to_ids(p.ingredients)
FROM
    temp_import l
    CROSS JOIN lateral json_populate_record(NULL :: temp_recipes, doc) AS p;

COMMIT;

```

## Summary

Loading data into JSON is easy, but if you need to do some processing in the middle it gets a little bit more tricky. Hope this article helped you a bit with your issue ^^

#### Read more about Postgres

- [How to import data from JSON and use postgres ARRAY with Golang](https://tech.ingrid.com/postgres-til-datatypes-array-json/)
- https://www.postgresql.org/docs/9.5/sql-createfunction.html