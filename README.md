# envoy-testing

Steps to reproduce issue [#7775](https://github.com/envoyproxy/envoy/issues/7775):

Generate custom file of size ~512MB:

```bash
dd if=/dev/zero of=data count=512 bs=1048576
```

Run `docker-compose up` and send request to listener `127.0.0.1:1111`:
```bash
$ docker-compose up -d
$ wget 127.0.0.1:1111/data --limit-rate=1000000
```

You should get similar output sooner or later from `wget` command:
```bash
$ wget 127.0.0.1:1111/data --limit-rate=1000000
--2019-08-12 15:12:42--  http://127.0.0.1:1111/data
Connecting to 127.0.0.1:1111... connected.
HTTP request sent, awaiting response... 200 OK
Length: 536870912 (512M) [application/octet-stream]
Saving to: ‘data.1’

data.1                                                        4%[=====>                                                                                                                                     ]  23,00M  1013KB/s    in 24s     

2019-08-12 15:13:06 (983 KB/s) - Connection closed at byte 24116984. Retrying.

--2019-08-12 15:13:07--  (try: 2)  http://127.0.0.1:1111/data
Connecting to 127.0.0.1:1111... connected.
HTTP request sent, awaiting response... 206 Partial Content
Length: 536870912 (512M), 512753928 (489M) remaining [application/octet-stream]
Saving to: ‘data.1’

data.1                                                        9%[++++++=====>                                                                                                                               ]  47,00M  1015KB/s    in 25s     

2019-08-12 15:13:32 (983 KB/s) - Connection closed at byte 49282502. Retrying.

--2019-08-12 15:13:34--  (try: 3)  http://127.0.0.1:1111/data
Connecting to 127.0.0.1:1111... connected.
HTTP request sent, awaiting response... 206 Partial Content
Length: 536870912 (512M), 487588410 (465M) remaining [application/octet-stream]
Saving to: ‘data.1’

data.1                                                        9%[++++++++++++                                                                                                                               ]  47,58M   976KB/s
```
