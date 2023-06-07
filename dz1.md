**Создать новый проект в Яндекс облако**

Создано.

**Далее создать инстанс виртуальной машины с дефолтными параметрами**

Создано

**Добавить свой ssh ключ в metadata ВМ**

Ключ добавлен, настоен доступ по ключу.

**Зайти удаленным ssh (первая сессия), не забывайте про ssh-add**

**поставить PostgreSQL**

otus@otus-db-pg-vm-1:
psql (15.3 (Ubuntu 15.3-1.pgdg22.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)

**Зайти вторым ssh (вторая сессия)**

**запустить везде psql из под пользователя postgres**

*psql -h 158.160.69.234 -U postgres*

**выключить auto commit**

*\set AUTOCOMMIT off*

**сделать в первой сессии новую таблицу и наполнить ее данными**

*create table persons(id serial, first_name text, second_name text);*
CREATE TABLE
*commit;*
COMMIT

*insert into persons(first_name, second_name) values('ivan', 'ivanov'),('petr', 'petrov');*
INSERT 0 2
*commit;*
COMMIT

**посмотреть текущий уровень изоляции**

*show transaction isolation level;*
transaction_isolation
-----------------------
 read committed

**начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции**

*begin;*
BEGIN

**в первой сессии добавить новую запись**

*insert into persons(first_name, second_name) values('sergey', 'sergeev');*
INSERT 0 1

**сделать во второй сессии**

*select * from persons;*
id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov

**Видите ли вы новую запись и если да то почему?**

Не видим, сессия не завершена, уровень read commited смотрит на момент начала выполнения запроса.

**Завершить первую транзакцию**

*commit;*
COMMIT

**Cделать во второй сессии:**

*select * from persons;*
id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev

**Видите ли вы новую запись и если да то почему?**

Да, первая транзакция завешена в первой сессии, select во второй выполнялся после завершения сессии, соответственно, во второй сессии мы увидим изменившиеся данные.

**Завершите транзакцию во второй сессии.**

*commit;*
COMMIT

**Начать новые но уже repeatable read транзации**

**set transaction isolation level repeatable read;**

*begin transaction isolation level repeatable read;*
BEGIN

**В первой сессии добавить новую запись**

*insert into persons(first_name, second_name) values('sveta', 'svetova');*
INSERT 0 1

**Сделать во второй сессии**

**1 чтение**

*select * from persons;*
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev

**Видите ли вы новую запись и если да то почему?**

Нет, транзакция не завершена.

**Завершить первую транзакцию**
*commit;*
COMMIT

**Сделать во второй сессии**

*select * from persons;*
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev

**Видите ли вы новую запись и если да то почему?**

Нет,на уровне repeatable read снимок строится на момент начала первого оператора транзакции, т.е на уровне 1 чтения. И пока транзакция не завершится, данные будут неизменны.

**Завершить вторую транзакцию**

*commit;*
COMMIT

**Сделать во второй сессии**

*select * from persons;*

 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
  4 | sveta      | svetova

**Ввидите ли вы новую запись и если да то почему?**

Да, видим. Select выполнялся после закрытия транзакции. Т.к. во второй транзакции данные не менялись, а только выполнялся select, вне транзакции мы увидим последние данные, поскольку все транзакции отработали.



