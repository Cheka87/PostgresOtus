sudo -u postgres pgbench -c8 -P 6 -T 60 -U postgres postgres
pgbench (15.3 (Ubuntu 15.3-1.pgdg22.04+1))
starting vacuum...end.
progress: 6.0 s, 1049.8 tps, lat 7.548 ms stddev 6.779, 0 failed
progress: 12.0 s, 1094.8 tps, lat 7.280 ms stddev 6.268, 0 failed
progress: 18.0 s, 1099.5 tps, lat 7.249 ms stddev 6.354, 0 failed
progress: 24.0 s, 1079.1 tps, lat 7.386 ms stddev 6.278, 0 failed
progress: 30.0 s, 1076.1 tps, lat 7.407 ms stddev 6.343, 0 failed
progress: 36.0 s, 1047.6 tps, lat 7.599 ms stddev 6.873, 0 failed
progress: 42.0 s, 1045.6 tps, lat 7.625 ms stddev 6.598, 0 failed
progress: 48.0 s, 1079.3 tps, lat 7.379 ms stddev 6.326, 0 failed
progress: 54.0 s, 1123.0 tps, lat 7.107 ms stddev 6.478, 0 failed
progress: 60.0 s, 1079.3 tps, lat 7.381 ms stddev 6.331, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 64653
number of failed transactions: 0 (0.000%)
latency average = 7.395 ms
latency stddev = 6.468 ms
initial connection time = 28.783 ms
tps = 1077.363275 (without initial connection time)
