[<-----](https://github.com/s1tcomsfan/knowledge_warehouse/blob/main/databases/SQL/contents.md)

# Реляционная алгебра

Набор операций, которые можно делать с множествами (таблицами), делая запросы к базе.

**Выборка** - выборка элементов из множества по заданному условию:

```
SELECT * FROM customers WHERE age >= 34
```

**Проекция** - отсечение столбцов:

```
SELECT name, age FROM customers
```

**Объединение** - объединение двух множеств (таблиц) с одинаковым набором атрибутов (дупликаты убираются):

```
SELECT * FROM customers
UNION
SELECT * FROM agents
```

**Пересечение** - в результат попадают те записи, которые есть в обоих множествах:

```
SELECT * FROM customers
NATURAL JOIN ignored_customers
```

**Разность** - в результат попадут элементы которые ести в первом множестве но отсутствуют во втором:

```
SELECT * FROM customers
NATURAL LEFT JOIN ignored_customers
WHERE ignored_customers IS NULL
```

**Произведение** - Декартово произведение, каждая запись из первого множества комбинируется с каждой записью из второго множества

```
SELECT * FROM customers, shops
```

**Соединение** - соединение двух множеств по условию

```
SELECT * FROM movies
JOIN channels ON (movies.channel_id = channels.id)
```

**Деление** - не используется на практике, не имеет прямой конструкции в SQL, реализуется комбинацией имеющихся комманд

**Важно** - сложные запросы, состоящие из множества операций, могут преобразовываться СУБД без потери смысла с целью оптимизации.

[<-----](https://github.com/s1tcomsfan/knowledge_warehouse/blob/main/databases/SQL/contents.md)