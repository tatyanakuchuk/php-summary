# SQL - язык запросов к БД
_Основная задача -> предоставление простого способа считывать и записывать инфу в БД_

1. [Создание новой БД и кодировка](#1)
2. [Создание таблицы](#2)
3. [Запись данных INSERT](#3)
4. [Обновление записей UPDATE](#4)
5. [Удаление записей DELETE](#5)
6. [Чтение записей SELECT](#6)
7. [Сортировка записей ORDER BY](#7)
8. [Поиск записей по условию WHERE](#8)
9. [Ограничение числа записей LIMIT](#9)
10. [Объединения JOIN](#10)
11. [Связь "многие ко многим"](#11)
12. [Добавление индекса полю](#12)

<a name="1"><h2>Создание новой БД и кодировка</h2></a>

```sql
CREATE DATABASE dbname -- dbname - имя базы данных
DEFAULT CHARACTER SET utf8 -- utf8 - кодировка по умолчанию
DEFAULT COLLATE utf_general_ci; -- utf_general_ci - представление
```
**Кодировка** - набор используемых символов.  
**Представление** - набор правил для сравнения символов в наборе.  

После создания новой БД её нужно сделать активной:

```sql
USE dbname;
```

Все последующие запросы будут выполняться только для активной(выбранной) БД.

<a name="2"><h2>Создание таблицы</h2></a>

Определение новой таблицы состоит из:

- названия таблицы;
- перечисления полей(столбцов):
	- название поля;
	- тип данных;
	- значение по умолчанию;
	- первичный ключ (да/нет);
- свойств таблицы.

**Первичный ключ** - это особенное поле, в котором сохраняется уникальный идентификатор записи. Первичный ключ используется в отношениях - на него ссылается ведомая таблица.  
_Первичные ключи не перезаписываются!_

```sql
CREATE TABLE users (
	id	INT AUTO_INCREMENT PRIMARY KEY, -- id - первичный ключ
	email	CHAR(128) NOT NULL UNIQUE,
	dt_add	TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
Ограничения полей:
- NOT NULL - не позволяет полю быть пустым;
- UNIQUE - значение должно быть уникальным;
- DEFAULT - устанавливает значение по умолчанию;
- CHAR(128) - тип данных с ограничением размерности.

<a name="3"><h2>Запись данных INSERT</h2></a>

Для добавления новых записей в таблицы используются запросы типа INSERT.

```sql
INSERT INTO <название таблицы>
SET <имя столбца 1> = <значение 1>,
	<имя столбца 2> = <значение 2>;
```
Пример:
```sql
INSERT INTO users
SET email = 'vasya@mail.ru',
	password = 'secret';
```

Альтернативный синтаксис у INSERT:
	
```sql
INSERT INTO <название таблицы>(<имя столбца 1>, <имя столбца 2>)
VALUES (<значение 1>, <значение 2>);
```	
Пример:
```sql
INSERT INTO users(email, password)
VALUES ('vasya@mail.ru', 'secret');
```

<a name="4"><h2>Обновление записей UPDATE</h2></a>

Для изменения инф-ции в существующих записях используется оператор UPDATE с условием поиска:

```sql
UPDATE <таблица> SET <имя столбца> = <значение>
WHERE <имя столбца> = <значение>;
```

Пример:
```sql
UPDATE users SET password = 'topsecret'
WHERE email = 'vasya@mail.ru';
```

<a name="5"><h2>Удаление записей DELETE</h2></a>

Используется оператор DELETE с условием удаления:

```sql
DELETE FROM <таблица> WHERE <имя столбца> = <значение>;
```
Пример:
```sql
DELETE FROM users WHERE email = 'vasya@mail.ru';
```

<a name="6"><h2>Чтение записей SELECT</h2></a>

MySQL позволяет применять фильтр к записям, указывая ограничение для значений одного или нескольких столбцов.
```sql
SELECT <перечисление полей> FROM <таблица>;
```
Пример:
```sql
SELECT id, email, password FROM users;
```

<a name="7"><h2>Сортировка записей ORDER BY</h2></a>

По умолчанию результаты выдаются без сортировки.  

Чтобы отсортировать список записей существует выражение ORDER BY:

- ASC - от меньшего к большему;
- DESC - от большего к меньшему.

```sql
SELECT * FROM categories ORDER BY name ASC;
```

<a name="8"><h2>Поиск записей по условию WHERE</h2></a>

```sql
SELECT <перечисление полей> FROM <таблица>
WHERE <имя поля><оператор><значение>;
```
Например, найти все гифки от одного автора:
```sql
SELECT id, email, password FROM users
WHERE email = 'vasya@mail.ru';
```

Найти всех пользователей без аватаров:
```sql
SELECT * FROM users
WHERE avatar_path IS NULL;
```

Найти гифки, которые лайкали больше 5 раз:
```sql
SELECT * FROM gifs
WHERE like_count > 5;
```

<a name="9"><h2>Ограничение числа записей LIMIT</h2></a>

Без явного задания ограничения возвращаются все записи из таблицы.

С помощью оператора LIMIT можно задать максимальное число записей, которые вернёт MySQL.  

LIMIT всегда идет самым последним в записи.

```sql
SELECT <перечисление полей> FROM <таблица>
LIMIT <число записей>;
```
Пример:
```sql
SELECT id, email, password FROM users
LIMIT 3;
```

<a name="10"><h2>Объединения JOIN</h2></a>

Для "связывания" двух таблиц вместе в одном запросе используется оператор объединения JOIN:

```sql
SELECT <перечисление полей> FROM <таблица 1>
JOIN <таблица 2> ON <условие объединения>;
```
Пример:
```sql
SELECT c.id, email, content FROM comments c
JOIN users u
ON c.users_id = u.id;
```

Особенности использования JOIN:
- обязательно указывать условие объединения в ON;
- если в перечислении полей есть поля с одинаковыми именами, то указывать как имя_таблицы.имя_поля.

<a name="11"><h2>Связь "многие ко многим"</h2></a>

 Записи из двух таблиц могут ссылаться друг на друга не напрямую, а через промежуточную таблицу:  
 ![таблица](../master/img/img-1.png)

Пример:
 ```sql
 SELECT title, name FROM users u
 INNER JOIN gifs_fav gf ON u.id = gf.users_id
 INNER JOIN gifs g ON gf.gif_id = g.id;
 ```

Результат:  
![таблица](../master/img/img-2.png)


<a name="12"><h2>Добавление индекса полю</h2></a>

Для быстрого поиска по определенному полю ему   следует добавить индекс:
```sql
CREATE INDEX <имя индекса> ON <таблица>(<поле>);
```
Пример:
```sql
CREATE INDEX c_text ON comments(content);
```
**Добавление индекса уникальности**

Чтобы обеспечить уникальность значений для определенного   поля в таблице используйте специальный вид индекса - уникальный:
```sql
CREATE UNIQUE INDEX <имя индекса> ON <таблица>(<поле>) ;
```
Пример:
```sql
CREATE UNIQUE INDEX email ON users(email);
```

**Как работают индексы**

Индекс — это структура данных, которая хранится отдельно от содержимого таблицы.  
При поиске информации по полю с индексом, MySQL вначале выполнит поиск по  индексу, чтобы найти в нём ссылку на запись в таблице.


