# Zadanie04

## Zadanie 1

Odpowiedź `/health`:
`{"status":"ok"}`

Linia z `docker logs backend`:
`172.20.0.1 - - [12/Nov/2026 07:57:29] "GET /health HTTP/1.1" 200 -`

## Zadanie 2

`docker run --rm --network breach-net healthcheck-probe`:
`backend: OK`

`docker run --rm healthcheck-probe`:
`backend: UNREACHABLE`

`docker run --rm --network breach-net healthcheck-probe`(po `docker stop backend`):
`backend: UNREACHABLE`

Bez tagu `--network` nazwa `backend` nie działa, ponieważ backend działa w sieci breach-net, więc bez jej tagu healthckeck-probe nie ma do niego dostępu, pomimo że jest uruchomiony.

## Zadanie 3

`docker exec backend cat /tmp/notatka.txt`(po restarcie):
`notatka`

`docker exec backend cat /tmp/notatka.txt`(po `rm` + `run`):
`cat: /tmp/notatka.txt: No such file or directory`

`docker run --rm -v breach-dane:/dane alpine sh -c "date >> /dane/historia.txt; cat /dane/historia.txt"`:

```[]
Thu Nov 12 08:23:33 UTC 2026
Thu Nov 12 08:23:40 UTC 2026
```

Plik w `/tmp` przetrwał restart, ponieważ zapisał się w kontenerze, który restart po prostu uruchomił ponownie, a nie przetrwał `rm`, ponieważ został usunięty wraz z kontenerem.

`historia.txt` przetrwała, ponieważ została zapisana w zewnętrznym wolumenie, a nie w środku kontenera.

Lista wycieków przetrwałaby `docker rm`, ponieważ jest ona montowana do kontenera, a nie zapisywana w nim.
