
1 создайте новый кластер PostgresSQL 14

2 зайдите в созданный кластер под пользователем postgres

3 создайте новую базу данных testdb

4 зайдите в созданную базу данных под пользователем postgres

5 создайте новую схему testnm

6 создайте новую таблицу t1 с одной колонкой c1 типа integer

7 вставьте строку со значением c1=1

8 создайте новую роль readonly

9 дайте новой роли право на подключение к базе данных testdb

10 дайте новой роли право на использование схемы testnm

11 дайте новой роли право на select для всех таблиц схемы testnm

12 создайте пользователя testread с паролем test123

13 дайте роль readonly пользователю testread

14 зайдите под пользователем testread в базу данных testdb

*Выполнение пп 1-14*

Установмили postgresql 14

Создаем БД:

postgres=# create database testdb;

CREATE DATABASE

postgres=# \c testdb

testdb=# create schema testnm;

CREATE SCHEMA

testdb=# create table t1 (c1 int);

CREATE TABLE

testdb=# insert into  testnm.t1 values (1),(2),(3);

INSERT 0 3

testdb=# create role readonly;

CREATE ROLE

testdb=# grant connect on database testdb to readonly;

GRANT

testdb=# grant usage on schema testnm to readonly;

GRANT

testdb=# grant select on all tables in schema testnm to readonly;

GRANT

testdb=# create user testread with password 'test123';

CREATE ROLE

testdb=# grant readonly to testread;

GRANT ROLE

После создания таблицы нужно прописать для пользователя testread search_path, чтобы выполнить п.15

testdb=# alter user testread in database testdb set search_path to 'public';


Тогда п.15 -
You are now connected to database "testdb" as user "testread".

testdb=> select *  from t1;

 c1 |
----|
  1 |
  2 |
  3 |

(3 rows)

C п.23 и по п.28 - есть более интересное решение, чем удаление таблицы. Её можно перенести в другую схему, соответственно, перепрописав search_path.

alter user testread in database testdb set search_path = testnm;

п.29 - вообще-то такие вещи происываются сразу, при назначении пользователю прав на определенную схему в БД.

ALTER default privileges in SCHEMA testnm grant SELECT on TABLES to readonly;

Да, не булет перепрописывания прав на вновь созданную таблицу. Но если создать новую таблицу в схеме public, а потом сменить схему для этой таблицы на testnm,

пользоватенль testread сможет ее увидеть, т.к. стоит alter default..
