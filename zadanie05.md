# Lab5

## Zadanie 1

```docker compose up -d --build```

```[]
[...]
[+] up 6/6
 ✔ Image breach-checker-frontend       Built                                                                        13.4s
 ✔ Image breach-checker-backend        Built                                                                        13.5s
 ✔ Network breach-checker_default      Created                                                                       0.1s
 ✔ Container breach-checker-db-1       Healthy                                                                       6.3s
 ✔ Container breach-checker-backend-1  Healthy                                                                      12.0s
 ✔ Container breach-checker-frontend-1 Started                                                                      12.2s
```

```docker compose ps```

```[]
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS            PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    19 seconds ago   Up 13 seconds(healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         20 seconds ago   Up 19 seconds(healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   19 seconds ago   Up 7 seconds            0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
```

## Zadanie 2

Po ```INSERT```:

```[]
'{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'

{"found":true}
```

Po ```down``` + ```up```

```[]
'{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'

{"found":true}
```

Po ```down -v``` + ```up```

```[]
'{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'

{"found":false}
```

Skrót przetrwał ```down```, ponieważ zapisał się w bazie danych, a nie przetrwał ```down -v```, ponieważ usunięty został wolumen z bazą danych. Po ```down -v``` w bazie znów są jest 89 skrótów, ponieważ baza została na nowo utworzona.

## Zadanie 3

```docker compose ps``` z trzema replikami

```[]
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS           PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    7 minutes ago    Up 7 minutes (healthy)   5000/tcp
breach-checker-backend-2    breach-checker-backend    "python app.py"          backend    10 seconds ago   Up 9 seconds (healthy)   5000/tcp
breach-checker-backend-3    breach-checker-backend    "python app.py"          backend    10 seconds ago   Up 8 seconds (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         7 minutes ago    Up 7 minutes (healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   7 minutes ago    Up 6 minutes           0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
```

```docker compose exec frontend nslookup backend```

```[]
Server:         127.0.0.11
Address:        127.0.0.11:53

Non-authoritative answer:

Non-authoritative answer:
Name:   backend
Address: 172.20.0.5
Name:   backend
Address: 172.20.0.3
Name:   backend
Address: 172.20.0.6
```

```POST /check``` przed restartem:

```[]
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:22] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:22] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:22] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:22] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:22] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:22] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:22] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:23] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:23] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:23] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:17:23] "POST /check HTTP/1.1" 200 -
```

```POST /check``` po restarcie:

```[]
backend-2  | 172.20.0.4 - - [19/Nov/2026 08:24:09] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.20.0.4 - - [19/Nov/2026 08:24:09] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.20.0.4 - - [19/Nov/2026 08:24:09] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.20.0.4 - - [19/Nov/2026 08:24:10] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:24:35] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:24:36] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:24:36] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.20.0.4 - - [19/Nov/2026 08:24:36] "POST /check HTTP/1.1" 200 -
```

**Dlaczego przed restartem frontendu ruch trafiał do jednej repliki?**

Nginx tłumaczy nazwę backend na adres IP tylko raz przy starcie kontenera i zapisuje go w pamięci podręcznej, więc dopiero restart frontendu wymusza ponowne odpytanie DNS i pobranie adresów wszystkich trzech replik.

**Dlaczego skalowanie byłoby niemożliwe, gdyby backend nadal miał ports: "5000:5000"?**

Na jednym interfejsie sieciowym hosta tylko jeden kontener może zająć port 5000, więc uruchomienie kolejnych replik zakończyłoby się błędem konfliktu portów (address already in use).
