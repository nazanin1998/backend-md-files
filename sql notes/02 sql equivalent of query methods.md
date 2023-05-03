# SQL equivalent of query methods


## _save()_ for insert
```
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);

//Or

INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```
## _save()_ for update
```
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

## _findById()_
```
SELECT *
FROM table_name
WHERE table_name.id=?;
```

## _deleteById()_
```
DELETE FROM table_name 
WHERE table_name.id=?;
```

## _count()_
```
SELECT COUNT(column_name)
FROM table_name
WHERE condition;
```

## _existById()_
```
SELECT *
FROM table_name
WHERE EXISTS
    (SELECT * FROM table_name 
    WHERE id=?);
```

## _findBy...()__
```
SELECT *
FROM table_name
WHERE column_name = ?";
```

## _findBy...Or...()__
```
SELECT *
FROM table_name
WHERE column_name1 = ? or column_name2 = ??;
```

## _findBy...And...()__
```
SELECT *
FROM table_name
WHERE column_name1 = ? and column_name2 = ??;
```

## _findDistinctBy...()__
```
SELECT distinct *
FROM table_name
WHERE column_name = ?;
```

## _findBy...GreaterThan()__
```
SELECT *
FROM table_name
WHERE column_name > ?;
```

## _findBy...GreaterThanEqual()__
```
SELECT *
FROM table_name
WHERE column_name >= ?;
```

## _findBy...LessThan()__
```
SELECT *
FROM table_name
WHERE column_name < ?;
```

## _findBy...LessThanEqual()__
```
SELECT *
FROM table_name
WHERE column_name <= ?;
```

## _findBy...Containing()__ or _findBy...Like()
```
SELECT *
FROM table_name
WHERE column_name like ?;
```

## _findBy...Between()__
```
SELECT *
FROM table_name
WHERE column_name between ? and ??;
```

## _findBy...In()__
```
SELECT *
FROM table_name
WHERE column_name in (? , ?? , ...)
```

## _findFirstBy...()__
```
SELECT *
FROM table_name
WHERE column_name in (? , ?? , ...)
```

## _findTop5By...()__
```
SELECT TOP 5 *
FROM table_name
WHERE column_name = ?;
```
