# OSS-DB Training

## PostgreSQLのDockerでのインストール

```bash
$ docker pull postgres:14
$ docker run --name postgres_14 -e POSTGRES_PASSWORD=postgres postgres:14
$ docker exec -it postgres_14 /bin/bash
```

```bash
$ su -
$ su - postgres
```


## Create/Drop User

```bash
$ createuser -P -d -r -e takuyakinoshita
> SELECT pg_catalog.set_config('search_path', '', false);
CREATE ROLE takuyakinoshita PASSWORD 'SCRAM-SHA-256$4096:uakhzmW7x3Fxg5j8xkoInw==$bkntI8l75X/k3YNQ31bFvd6YXgzZRMgyMiU3MlVXt5U=:3XjiIHxJQCeEPZB3RVGZuhLhPGsOqTNPPAuLnBfmAhk=' NOSUPERUSER CREATEDB CREATEROLE INHERIT LOGIN;
```

```bash
$ dropuser -i takuyakinoshita
```

## Create/Drop database
```bash
$ createdb --encoding=UTF8 sampledb
```



```bash
$ dropdb -i sampledb
```

## psql

```bash
# データベースの確認
$ psql -l
```

