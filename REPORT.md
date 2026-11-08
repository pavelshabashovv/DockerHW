# Отчет

запустил контейнер

```
$ curl http://127.0.0.1:8080/
{"greeting": "aboba", "count": 0, "hostname": "839f2853cf2d", "storage": "sqlite"}

$ curl -X POST http://127.0.0.1:8080/increment
{"greeting": "aboba", "count": 1, "hostname": "839f2853cf2d", "storage": "sqlite"}

$ curl -X POST http://127.0.0.1:8080/increment
{"greeting": "aboba", "count": 2, "hostname": "839f2853cf2d", "storage": "sqlite"}

$ curl http://127.0.0.1:8080/
{"greeting": "aboba", "count": 2, "hostname": "839f2853cf2d", "storage": "sqlite"}
```

логи:

```
$ docker logs app
2026-11-08 15:24:58,355 listening port=8000 storage=sqlite
2026-11-08 15:25:18,227 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-08 15:25:18,243 http 172.17.0.1 "GET /favicon.ico HTTP/1.1" 404 -
2026-11-08 15:26:39,492 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-08 15:26:40,420 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-08 15:26:55,747 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-08 15:28:38,021 http 172.17.0.1 "POST /increment HTTP/1.1" 200 -
2026-11-08 15:28:46,548 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-08 15:28:47,188 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-08 15:30:18,359 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-08 15:31:59,225 http 172.17.0.1 "POST /increment HTTP/1.1" 200 -
2026-11-08 15:33:22,084 http 172.17.0.1 "GET / HTTP/1.1" 200 -
```
сделал .env

проверил что счетчик не зануляется после удаления контейнера

```
$ curl -X POST http://127.0.0.1:8080/increment
{"greeting": "aboba", "count": 1, "hostname": "98b09e7371fc", "storage": "sqlite"}
$ curl -X POST http://127.0.0.1:8080/increment
{"greeting": "aboba", "count": 2, "hostname": "98b09e7371fc", "storage": "sqlite"}
$ docker compose down
$ docker compose up -d
$ curl http://127.0.0.1:8080/
{"greeting": "aboba", "count": 2, "hostname": "b01079b64ab1", "storage": "sqlite"}
```

redis:

проверка 1

```
$ curl -X POST http://127.0.0.1:8080/increment
{"greeting": "aboba", "count": 1, "hostname": "93cd078dd0cb", "storage": "redis"}
$ curl -X POST http://127.0.0.1:8080/increment
{"greeting": "aboba", "count": 2, "hostname": "93cd078dd0cb", "storage": "redis"}
$ curl -X POST http://127.0.0.1:8080/increment
{"greeting": "aboba", "count": 3, "hostname": "93cd078dd0cb", "storage": "redis"}
$ docker compose down
$ docker compose up -d
$ curl http://127.0.0.1:8080/
{"greeting": "aboba", "count": 3, "hostname": "27a837e73f7c", "storage": "redis"}
```

проверка 2

```
$ docker compose stop redis
$ curl -i http://127.0.0.1:8080/healthz
HTTP/1.0 200 OK
{"status": "alive"}
$ curl -i http://127.0.0.1:8080/readyz
HTTP/1.0 503 Service Unavailable
{"error": "storage_unavailable"}
$ curl -i -X POST http://127.0.0.1:8080/increment
HTTP/1.0 503 Service Unavailable
{"error": "storage_unavailable"}
```

проверка 3

```
$ docker compose start redis
$ curl http://127.0.0.1:8080/
{"greeting": "aboba", "count": 3, "hostname": "27a837e73f7c", "storage": "redis"}
$ curl -X POST http://127.0.0.1:8080/increment
{"greeting": "aboba", "count": 4, "hostname": "27a837e73f7c", "storage": "redis"}
```