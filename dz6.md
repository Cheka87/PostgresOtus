**Устаеновлен postgres 15**

Создана таблица с одним полем.

до смены каталога:

postgres=# \dt
        List of relations
 Schema | Name | Type  |  Owner
--------+------+-------+----------
 public | test | table | postgres
(1 row)

postgres=# select * from test;
 c1
----
 1
(1 row)

Создан и примонтирован к ВМ Ubuntu диск на 10GB.

На него перенесен каталог /var/lib/postgresql/15.

При выполнении команды sudo pg_ctlcluster 15 main13 start возникает ошибка:

*/var/lib/postgresql/15/main13 is not accessible or does not exist*, что вполне закономерно, т.к. мы перенесли каталог с данными, а в

файле *postgres.conf* ссылка на параметр **"data_directory"** не была изменена.

После изменения параметра  **"data_directory"** кластер запустился. Все данные доступны.

После смены каталога

postgres=# \dt
        List of relations
 Schema | Name | Type  |  Owner
--------+------+-------+----------
 public | test | table | postgres
(1 row)

postgres=# select * from test;
 c1
----
 1
(1 row)

