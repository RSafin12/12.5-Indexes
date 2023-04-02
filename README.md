# 12.5-Indexes

## Задание 1
Задание 1.
```
SELECT (SUM(INDEX_LENGTH) / SUM(DATA_LENGTH))*100
FROM INFORMATION_SCHEMA.TABLES
WHERE table_schema = "sakila";
```
