 1. Настройте выполнение контрольной точки раз в 30 секунд.

 alter system set checkpoint_timeout='30s';

 2. 10 минут c помощью утилиты pgbench подавайте нагрузку.

 sudo -u postgres -p 5432  pgbench -c8 -P 6 -T 600 -U postgres postgres
