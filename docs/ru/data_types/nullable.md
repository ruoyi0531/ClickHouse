# Nullable(TypeName) {#data_type-nullable}

Позволяет работать как со значением типа `TypeName` так и с отсутствием этого значения ([NULL](../query_language/syntax.md)) в одной и той же переменной, в том числе хранить `NULL` в таблицах вместе со значения типа `TypeName`. Например, в столбце типа `Nullable(Int8)` можно хранить значения типа `Int8`, а в тех строках, где значения нет, будет храниться `NULL`.

В качестве `TypeName` нельзя использовать составные типы данных [Array](array.md#data_type-array) и [Tuple](tuple.md). Составные типы данных могут содержать значения типа `Nullable`, например `Array(Nullable(Int8))`.

Поле типа `Nullable` нельзя включать в индексы.

`NULL` — значение по умолчанию для типа `Nullable`, если в конфигурации сервера ClickHouse не указано иное.

## Особенности хранения {#osobennosti-khraneniia}

Для хранения значения типа `Nullable` ClickHouse использует:

-   Отдельный файл с масками `NULL` (далее маска).
-   Непосредственно файл со значениями.

Маска определяет, что лежит в ячейке данных: `NULL` или значение.

В случае, когда маска указывает, что в ячейке хранится `NULL`, в файле значений хранится значение по умолчанию для типа данных. Т.е. если, например, поле имеет тип `Nullable(Int8)`, то ячейка будет хранить значение по умолчанию для `Int8`. Эта особенность увеличивает размер хранилища.

!!! info "Info"
    Почти всегда использование `Nullable` снижает производительность, учитывайте это при проектировании своих баз.

## Пример использования {#primer-ispolzovaniia}

``` sql
CREATE TABLE t_null(x Int8, y Nullable(Int8)) ENGINE TinyLog
```

``` sql
INSERT INTO t_null VALUES (1, NULL), (2, 3)
```

``` sql
SELECT x + y from t_null
```

``` text
┌─plus(x, y)─┐
│       ᴺᵁᴸᴸ │
│          5 │
└────────────┘
```

[Оригинальная статья](https://clickhouse.tech/docs/ru/data_types/nullable/) <!--hide-->