# Ping-T メモ

## Foreign key
下記のどちらでもOK.

```sql
CREATE TABLE sample (
    no INTEGER REFERENCES sample_1 (id),
    name TEXT 
);
```

```sql
CREATE TABLE sample (
    no INTEGER,
    name TEXT,
    FOREIGN KEY (no) REFERENCES sample_1 (id)
);
```

