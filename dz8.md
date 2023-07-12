Первый прогон после установки

sudo -u postgres pgbench -c8 -P 6 -T 60 -U postgres postgres
pgbench (15.3 (Ubuntu 15.3-1.pgdg22.04+1))
starting vacuum...end.
progress: 6.0 s, 1711.6 tps, lat 4.642 ms stddev 3.328, 0 failed
progress: 12.0 s, 1799.8 tps, lat 4.427 ms stddev 3.123, 0 failed
progress: 18.0 s, 1796.8 tps, lat 4.434 ms stddev 2.998, 0 failed
progress: 24.0 s, 1719.3 tps, lat 4.634 ms stddev 3.174, 0 failed
progress: 30.0 s, 1799.0 tps, lat 4.431 ms stddev 2.898, 0 failed
progress: 36.0 s, 1757.8 tps, lat 4.534 ms stddev 3.163, 0 failed
progress: 42.0 s, 1748.5 tps, lat 4.555 ms stddev 3.581, 0 failed
progress: 48.0 s, 1834.2 tps, lat 4.357 ms stddev 3.219, 0 failed
progress: 54.0 s, 1742.5 tps, lat 4.573 ms stddev 3.180, 0 failed
progress: 60.0 s, 1799.9 tps, lat 4.428 ms stddev 3.640, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 106265
number of failed transactions: 0 (0.000%)
latency average = 4.500 ms
latency stddev = 3.240 ms
initial connection time = 15.158 ms
tps = 1770.875344 (without initial connection time)


starting vacuum...end.
progress: 6.0 s, 1792.6 tps, lat 4.434 ms stddev 2.804, 0 failed
progress: 12.0 s, 1719.5 tps, lat 4.635 ms stddev 3.035, 0 failed
progress: 18.0 s, 1789.2 tps, lat 4.454 ms stddev 2.838, 0 failed
progress: 24.0 s, 1717.8 tps, lat 4.636 ms stddev 2.981, 0 failed
progress: 30.0 s, 1793.2 tps, lat 4.447 ms stddev 2.791, 0 failed
progress: 36.0 s, 1808.0 tps, lat 4.408 ms stddev 2.865, 0 failed
progress: 42.0 s, 1724.7 tps, lat 4.622 ms stddev 3.195, 0 failed
progress: 48.0 s, 1775.6 tps, lat 4.488 ms stddev 3.045, 0 failed
progress: 54.0 s, 1802.7 tps, lat 4.420 ms stddev 3.062, 0 failed
progress: 60.0 s, 1699.9 tps, lat 4.686 ms stddev 3.205, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 105747
number of failed transactions: 0 (0.000%)
latency average = 4.521 ms
latency stddev = 2.987 ms
initial connection time = 13.611 ms
tps = 1762.037419 (without initial connection time)

