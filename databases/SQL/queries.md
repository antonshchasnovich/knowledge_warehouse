[<-----](https://github.com/s1tcomsfan/knowledge_warehouse/blob/main/databases/SQL/contents.md)

# Запросы

## Выборка

```
SELECT selected_elements // что
FROM from_item // откуда
WHERE condition // условие фильтрации
GROUP BY grouping_element // элемент группировки
HAVING condition // условие по которому отсекается результат группировки
UNION | INTERSECT | EXCEPT // объединение
ORDER BY expression ASC | DESX | USING // упорядочивание
LIMIT count | all // количество возвращаемых элементов
OFFSET // выдавать страницами
FETCH // аналог лимита
FOR UPDATE // собираемся менять данный (нужна блокировка)
```

## Добавление

```
INSERT INTO table_name {AS alias} // куда
VALUES (first_column_value, second_column_value...), (first_column_value, second_column_value...) // что
ON CONFLICT [conflict_target] conflict_action // действия при конфликте
RETURNING field_one, field_two | * // возвращать добавленные поля
```

## Обновление

```
UPDATE table_name {AS alias} // где
column_name_one = value // имя колонки, значение для колонки
column_name_two = value // имя колонки, значение для колонки
FROM ... // откуда взять значения для обновления
WHERE... // условия обновления
RETURNING ... // вернуть обновленные значения
```

## Удаление

```
DELETE FROM table_name {AS alias} // откуда
USING using_lis // аналог WHERE
WHERE ... // условие удаления
RETURNING ... // вернуть удаленные элементы
```

При **каскадном удалении** удаляются все элементы с каскадными ссылками. Например есть запись "фильм" с полем "сеансы" каскадно ссылающимся на таблицу сеансов. При удалении фильма удалятся все его сеансы.

[<-----](https://github.com/s1tcomsfan/knowledge_warehouse/blob/main/databases/SQL/contents.md)