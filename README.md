# OSS-DB Training

## PostgreSQLのDockerでのインストール

```bash
$ docker pull postgres:14
$ docker run --name postgres_14 -e POSTGRES_PASSWORD=postgres postgres:14
$ docker exec -it postgres_14 /bin/bash
```

```bash
$ docker run --rm -P -p 127.0.0.1:5432:5432 -e POSTGRES_PASSWORD=password --name oss_db postgres:latest
$ docker ps # port forwardingを確認
$ psql postgresql://postgres:password@localhost:5432/postgres
# postgresql://[user[:password]@][netloc][:port][/dbname][?param1=value1&...]
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



#### ビュー
```sql
# CREATE VIEW user_view (id, name) AS SELECT * FROM users WHERE id > 100;
# SELECT * FROM user_view;
# DROP VIEW user_view;
```

#### マレリアリズドビュー
```sql
# CREATE MATERIALIZED VIEW user_view (id, name) AS SELECT * FROM users;
```
定義を変更する場合は、`ALTER MATERIALIZED VIEW`を使う。




#### インデックス
```sql
# CREATE INDEX idx ON users((id || '-' || name));
```



## トランザクション

### トランザクション分離レベル
|レベル|特徴|
|---|---|
|Read uncommitted|ダーティーリード、反復不能読み取り、ファントムリード、直列化異常が起こる|
|Read committed|反復不能読み取り、ファントムリード、直列化異常が起こる|
|Repeatable read|ファントムリード、直列化異常が起こる|
|Serializable|ダーティーリード、反復不能読み取り、ファントムリード、直列化異常のいずれも起こらない|


PostgreSQLでは、Read committedをデフォルトで使用している。









