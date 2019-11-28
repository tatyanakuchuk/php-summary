# SQL - язык запросов к БД
_Основная задача -> предоставление простого способа считывать и записывать инфу в БД_

1. [Создание новой БД и кодировка](#1)
2. [Создание таблицы](#2)
3. [Запись данных: INSERT](#3)


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
	id		INT AUTO_INCREMENT PRIMARY KEY, -- id - первичный ключ
	email	CHAR(128) NOT NULL UNIQUE,
	dt_add	TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
Ограничения полей:
- NOT NULL - не позволяет полю быть пустым;
- UNIQUE - значение должно быть уникальным;
- DEFAULT - устанавливает значение по умолчанию;
- CHAR(128) - тип данных с ограничением размерности.

<a name="3"><h2>Запись данных: INSERT</h2></a>

Для добавления новых записей в таблицы используются запросы типа INSERT.

            1 вариант 				|    			2 вариант			  |
------------------------------------|-------------------------------------|	
```sql								|```sql								  |
INSERT INTO <название таблицы>		|INSERT INTO <название таблицы>	  	  |
SET <имя столбца 1> = <значение 1>, |(<имя столбца 1>, <имя столбца 2>)	  |
	<имя столбца 2> = <значение 2>  |VALUES (<значение 1>, <значение 2>)  |
```									|```								  |
------------------------------------|-------------------------------------|
```sql								|```sql								  |
INSERT INTO users					|INSERT INTO users					  |
SET email = 'vasya@mail.ru', 		|(email, password) 					  |
	password = 'secret';			||VALUES ('vasya@mail.ru', 'secret')  |
```									|```								  |
------------------------------------|-------------------------------------|






