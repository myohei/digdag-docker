digdag on docker
-------------------------------

digdag in Docker. We have only confirmed that the digdag workflow runs within Docker.

## Local

```bash
$ docker-compose -f docker-compose.yml -f docker-compose.local.yml up --build
```

for testing

```bash 
$ digdag sessions
```

```bash
$ cd ${digdag-workflows}/test-docker
$ digdag push hoge 
$ digdag sessions
```

## Production

```bash
$ docker-compose -f docker-compose.yml -f docker-compose.prod.yml up --build -d 
```

You can check the session with the following command if the client is on the same server.

```bash
$ digdag session -e http://127.0.0.1:65432
```
