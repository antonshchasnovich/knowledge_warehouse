# Масштабирование

## Вертикальное

**Вертикальный шардинг (Партицирование)** - одна большая таблица разделяется на много маленьких на той же машине. Разбиение на партиции производится по выбранному правилу для одного из полей таблицы.

Например разбить таблицу с новостями на много таблиц используя поле category_id и наследуясь от исходной таблицы news
```
CREATE TABLE news_1 (
    CHECK ( category_id = 1 )
) INHERITS (news)

CREATE TABLE news_2 (
    CHECK ( category_id = 2 )
) INHERITS (news)
```

Затем добавить правило для основной таблицы что бы при вставке элементов в нее, они добавлялись в унаследованные таблицы, например по полю category_id:

```
CREATE RULE news_insert_to_1 AS ON INSERT TO news
WHERE ( category_id = 1 )
DO INSTEAD INSERT INTO news_1 VALUES (NEW .*)

CREATE RULE news_insert_to_2 AS ON INSERT TO news
WHERE ( category_id = 2 )
DO INSTEAD INSERT INTO news_2 VALUES (NEW .*)
```

Выбирать данные можно используя как основную таблицу:

```
SELECT * FROM news WHERE category_id = 1
```

Так и партиции:

```
SELECT * FROM news_1
```

Для UPDATE и DELETE нужно создать аналогичные правила.

Индексы, созданные в основной таблице не будут унаследованы партициями поэтому придется завести одинаковые индексы на всех партициях, например для поля rate:

```
CREATE INDEX news_rate_idx ON news(rate)

CREATE INDEX news_1_rate_idx ON news_1(rate)

CREATE INDEX news_2_rate_idx ON news_2(rate)
```

## Горизонтальное

**Горизонтальный шардинг** - одна большая таблица разделяется на много маленьких и они разносятся по разным машинам. Идея аналогична вертикальному шардингу но с особенностями реализации.

Пример с тремя серверами, на первом основная таблица news, на втором и третьем шарды news_1 и news_2

Создание таблицы шарда с category_id = 1

```
CREATE TABLE news (
    id bigint not null,
    category_id int not null
    CONSTRAINT category_id_check CHECK (category_id = 1)
    author character varying not null,
    rate int not null,
    title character varying
)
```

Создаем экстеншн

```
CREATE EXTENSION postgres_fdw
```

Создаем удаленный сервер

```
CREATE SERVER news_1_server
FOREIGN DATA WRAPPER postgres_fdw
OPTIONS (host '127.0.0.1', port '5432', dbname 'news_1');
```

Создаем маппинг для юзера с логином и паролем

```
CREATE USER MAPPING FOR postgres
SERVER news_1_server
OPTIONS (user 'postgres', password 'postgres');
```

Подключаем таблицы-шарды на основном сервере используя созданные серверы:

```
CREATE FOREIGN TABLE news_1 (
    id bigint not null,
    category_id int not null,
    author character varying not null,
    rate int not null,
    title character varying
)
SERVER news_1_server
OPTIONS (schema_name 'public', table_name 'news')
```

Аналогично подключаем остальные шарды.

Создаем склеенную таблицу из шардов используя вью:

```
CREATE VIEW news AS
    SELECT * FROM news_1
        UNION ALL
    SELECT * FROM news_2
```
Заводим правила для основной таблицы на INSERT, UPDATE, DELETE которые сработают если ни одна проверка не сработает чтобы ничего не происходило:

```
CREATE RULE news_insert AS ON INSERT TO news
DO INSTEAD NOTHING

CREATE RULE news_update AS ON UPDATE TO news
DO INSTEAD NOTHING

CREATE RULE news_delete AS ON DELETE TO news
DO INSTEAD NOTHING
```

Заводим правила для распределения по шардам:

```
CREATE RULE news_insert_to_1 AS ON INSERT TO news
    WHERE ( category_id = 1 )
DO INSTEAD INSERT INTO news_1 VALUES (NEW.*);

CREATE RULE news_insert_to_2 AS ON INSERT TO news
    WHERE ( category_id = 2 )
DO INSTEAD INSERT INTO news_2 VALUES (NEW.*);
```

### Репликация