

sudo -u postgres pgbench -i postgres

**Первый прогон после установки**

sudo -u postgres pgbench -c8 -P 6 -T 60 -U postgres postgres

starting vacuum...end.

progress: 6.0 s, 1372.3 tps, lat 5.788 ms stddev 3.470, 0 failed
progress: 12.0 s, 1369.7 tps, lat 5.823 ms stddev 3.453, 0 failed
progress: 18.0 s, 1375.0 tps, lat 5.799 ms stddev 3.474, 0 failed
progress: 24.0 s, 1343.2 tps, lat 5.936 ms stddev 5.158, 0 failed
progress: 30.0 s, 1362.2 tps, lat 5.853 ms stddev 3.479, 0 failed
progress: 36.0 s, 1277.2 tps, lat 6.245 ms stddev 4.856, 0 failed
progress: 42.0 s, 1333.5 tps, lat 5.984 ms stddev 3.871, 0 failed
progress: 48.0 s, 1139.6 tps, lat 5.888 ms stddev 3.944, 0 failed
progress: 54.0 s, 1143.9 tps, lat 8.089 ms stddev 65.471, 0 failed
progress: 60.0 s, 1345.7 tps, lat 5.926 ms stddev 3.949, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 78381
number of failed transactions: 0 (0.000%)
latency average = 6.105 ms
latency stddev = 19.755 ms
initial connection time = 20.637 ms
tps = 1306.417607 (without initial connection time)

**Применим параметры из приложенного файла и запустим pgbench снова.**

alter system set max_connections=40;

alter system set shared_buffers='1GB';

alter system set effective_cache_size='3GB';

alter system set maintenance_work_mem='512MB';

alter system set checkpoint_completion_target=0.9;

alter system set wal_buffers='16MB';

alter system set default_statistics_target = 500;

alter system set random_page_cost = 4;

alter system set effective_io_concurrency = 2;

alter system set work_mem = '6553kB';

alter system set min_wal_size = '4GB';

alter system set max_wal_size = '16GB';

Прогон после изменеия параметров.

starting vacuum...end.

progress: 6.0 s, 1295.5 tps, lat 6.139 ms stddev 3.605, 0 failed
progress: 12.0 s, 1305.5 tps, lat 6.111 ms stddev 4.586, 0 failed
progress: 18.0 s, 1328.8 tps, lat 6.000 ms stddev 3.554, 0 failed
progress: 24.0 s, 1316.7 tps, lat 6.059 ms stddev 3.843, 0 failed
progress: 30.0 s, 1329.5 tps, lat 5.999 ms stddev 3.441, 0 failed
progress: 36.0 s, 1326.3 tps, lat 6.013 ms stddev 3.604, 0 failed
progress: 42.0 s, 1331.8 tps, lat 5.988 ms stddev 3.541, 0 failed
progress: 48.0 s, 1323.7 tps, lat 6.025 ms stddev 3.488, 0 failed
progress: 54.0 s, 1334.0 tps, lat 5.982 ms stddev 3.515, 0 failed
progress: 60.0 s, 1315.3 tps, lat 6.065 ms stddev 3.671, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 79251
number of failed transactions: 0 (0.000%)
latency average = 6.038 ms
latency stddev = 3.698 ms
initial connection time = 13.547 ms
tps = 1320.680050 (without initial connection time)


Изменились значения tps, после второго прогона стали больше.

Уменьшилась средняя задержка.

Уменьшилась задержка вывода.



**Создать таблицу с текстовым полем и заполнить случайными или сгенерированными данным в размере 1млн строк.**

CREATE TABLE student(id serial,fio char(100));

INTO student(fio) SELECT 'noname' FROM generate_series(1,1000000);

SELECT pg_size_pretty(pg_total_relation_size('student'));

 pg_size_pretty |
----------------|
 135 MB         |


select * from student limit 3;

   id   | fio    |
--------|--------|
 999479 | noname |
 999480 | noname |
 999481 | noname |


 update student set fio = (select fio || 1::text from student limit 1);

 update student set fio = (select fio || 1::text from student limit 1);

 update student set fio = (select fio || 1::text from student limit 1);

 update student set fio = (select fio || 1::text from student limit 1);

 update student set fio = (select fio || 1::text from student limit 1);



 SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%", last_autovacuum FROM pg_stat_user_TABLEs WHERE relname = 'student';

 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum        |
---------|------------|------------|--------|-------------------------------|
 student |    1000000 |          0 |      0 | 2023-08-13 12:20:09.627315+03 |



 Сразу после обновления

 SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%", last_autovacuum FROM pg_stat_user_TABLEs WHERE relname = 'student';

 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum       |
---------|------------|------------|--------|------------------------------|
 student |    1000000 |    1000000 |     99 | 2023-08-13 12:24:08.851127+03|



 SELECT pg_size_pretty(pg_total_relation_size('student'));

  pg_size_pretty |
 ----------------|
  539 MB         |


Через минуту, отработал автовакуум.

SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%", last_autovacuum FROM pg_stat_user_TABLEs WHERE relname = 'student';

 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum        |
---------|------------|------------|--------|-------------------------------|
 student |    1499949 |          0 |      0 | 2023-08-13 12:25:10.405006+03 |


Отключаем фвтовакуум

ALTER TABLE student SET (autovacuum_enabled = off);

Обновляем строки 10 раз.

update student set fio = (select fio || '_11' from student limit 1);

update student set fio = (select fio || '_12' from student limit 1);

update student set fio = (select fio || '_13' from student limit 1);

update student set fio = (select fio || '_14' from student limit 1);

update student set fio = (select fio || '_15' from student limit 1);

update student set fio = (select fio || '_16' from student limit 1);

update student set fio = (select fio || '_17' from student limit 1);

update student set fio = (select fio || '_18' from student limit 1);

update student set fio = (select fio || '_19' from student limit 1);

update student set fio = (select fio || '_20' from student limit 1);


SELECT relname, n_live_tup, n_dead_tup, trunc(100*n_dead_tup/(n_live_tup+1))::float "ratio%", last_autovacuum FROM pg_stat_user_TABLEs WHERE relname = 'student';

-----------------------------------------------------------------------------|
 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum         |
---------+------------+------------+--------+--------------------------------|
 student |    1499949 |    9999023 |    666 | 2023-08-13 12:25:10.405006+03  |
-----------------------------------------------------------------------------|



 SELECT pg_size_pretty(pg_total_relation_size('student'));

 pg_size_pretty |
----------------|
 1482 MB        |


После vacuum рамер файла не поменялся.

vacuum verbose student;

INFO:  vacuuming "postgres.public.student"

INFO:  finished vacuuming "postgres.public.student": index scans: 0

pages: 0 removed, 189636 remain, 189636 scanned (100.00% of total)

tuples: 1000158 removed, 1000000 remain, 0 are dead but not yet removable

removable cutoff: 237977, which was 0 XIDs old when operation ended

new relfrozenxid: 237976, which is 12 XIDs ahead of previous value

index scan not needed: 172398 pages from table (90.91% of total) had 9998675 dead item identifiers removed

avg read rate: 131.318 MB/s, avg write rate: 346.666 MB/s

buffer usage: 320549 hits, 58792 misses, 155205 dirtied

WAL usage: 379288 records, 120676 full page images, 32431278 bytes

system usage: CPU: user: 0.39 s, system: 0.25 s, elapsed: 3.49 s

VACUUM

SELECT pg_size_pretty(pg_total_relation_size('student'));

 pg_size_pretty |
----------------|
 1482 MB        |


И только после vacuum full размер файла таблицы уменьшился. Пустые строки были удалены.

vacuum full student;

VACUUM

postgres=# SELECT pg_size_pretty(pg_total_relation_size('student'));

 pg_size_pretty |
----------------|
 135 MB         |




