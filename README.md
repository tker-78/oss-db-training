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

## RemoteでPostgresコンテナに接続する

- dev containerで起動中のpostgresコンテナに接続する
- docker環境内で`hostname`コマンドでホストを確認する
- remote connection側でPostgreSQL拡張機能の`+`ボタンを押して接続する
  



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
# データベースの作成
$ createdb --encoding=UTF8 sampledb

# local=Cに指定
$ createdb --endoding=UTF8 --locale=C --template=template0 testdb
```



```bash
# データベースの削除
$ dropdb -i sampledb
```

## psql

```bash
# データベースの確認
$ psql -l
```

```bash
$ psql -U takuyakinoshita sampledb
```

### バッチ処理

```bash
$ echo "SELECT * FROM users;" >> data/sample.sql
$ psql -f data/sample.sql -U takuyakinoshita sampledb
```


## 設定ファイル

### postgresql.conf
PostgreSQLのパラメータを設定するファイル。  
initdbで生成される。  

下記が重要な設定項目

- listen_addresses
- port
- max_connections
- search_path
- default_transaction_isolation
- client_encoding
- log_distination
- logging_collector
- log_directory
- log_filename
- log_min_messages
- log_line_prefix
- 

### pg_hba.conf
クライアント認証を設定するファイル。  
initdbで生成される。  


## バックアップとリストア

### 稼働中のバックアップ
```bash
$ pg_dump sampledb
```

### 停止中のバックアップ
```bash
$ cd $PGDATA/..
$ tar cvf backup.tar data
```

### PITR(Point In Time Recovery)

```bash
$ pg_basebackup
```

```postgres
# SELECT pg_start_backup('label', false, false);
```

### COPYと\copy
```postgres
-- テーブルをcsvでファイルに書き出し
# \copy users to 'data/users.txt' (format csv, header)
```

```postgres
-- テキストファイルから読み込み
# \copy users from 'data/users.txt' (format csv, header)
```

または、バッチ処理として、
```postgres
# COPY users(id,name,age) FROM 'users.txt' DELIMITER ',' CSV HEADER;
COPY 4
```
これの実行には、書き込み権限が必要。 



## 運用管理

### ユーザー操作
```bash
# ユーザーの追加
$ createuser -P user1

# ユーザーの削除
$ dropuser user1

```

```postgres
-- ユーザーの確認
# \du
```

```postgres
-- ユーザーの変更
# ALTER USER user1 WITH PASSWORD 'password';
-- \password user1 と同じ

-- SUPERUSER権限の付与
# ALTER USER user1 WITH SUPERUSER;
```

### VACUUM

不要領域の回収の操作。

```postgres
-- database ownerまたはtable ownerで実行すること
# VACUUM users;
```

### ANALYZE

```postgres
# ANALYZE users;
```

### 自動バキューム

postgres.conf
```conf
autovacuum=on
```

### システム情報関数

```postgres
-- バージョン情報
# SELECT version();

-- 現在のデータベース
# SELECT current_database();

-- 現在のユーザー
# SELECT current_user;

```


### 情報スキーマ、システムカタログ

```postgres
-- 現在有効なユーザー
# SELECT * FROM information_schema.enabled_roles;

-- すべてのテーブル
# SELECT * FROM infromation_schema.tables;

```

```postgres
-- システムカタログ
# SELECT * FROM pg_roles WHERE rolcanlogin IS TRUE;

```

> tips: 
> `\x`で出力を見やすく整形してくれる。


```postgres
-- データベースの情報を確認
# SELECT * FROM pg_database;

-- テーブル情報を確認
# SELECT * FROM pg_tables;

```


### ユーザー権限

```postgres
-- PUBLICに変更
# GRANT SELECT ON users TO PUBLIC;

-- user1に権限を付与
# GRANT SELECT, UPDATE ON users TO user1;
# \dp
```

```postgres
-- 権限の取り消し
# REVOKE ALL ON users FROM user1;
-- 権限の取り消し
```


### SQLとオブジェクト


#### ALTER TABLE

```sql
# ALTER TABLE users ADD COLUMN address VARCHAR;
```







