# Cockroach Database Doc




# Create Table


```sql
create table test (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tags STRING[]);

```

```sql

> show create test ;

  table_name |                create_statement
+------------+-------------------------------------------------+
  test       | CREATE TABLE test (
             |     id UUID NOT NULL DEFAULT gen_random_uuid(),
             |     tags STRING[] NULL,
             |     CONSTRAINT "primary" PRIMARY KEY (id ASC),
             |     FAMILY "primary" (id, tags)
             | )
(1 row)

Time: 4.9491ms
```

# Insert Table

```sql
    insert into test value (ARRAY['sky', 'road', 'car']);
    insert into test value (ARRAY['sky', 'road', 'bus']);
    insert into test value (ARRAY['sky']);
    insert into test value (ARRAY['road']);
    insert into test value (ARRAY['bus']);

```

# Search 


```sql

> select * from test;

                   id                  |         tags
+--------------------------------------+----------------------+
  3aab348a-fbd4-47aa-8899-f932ebc897f2 | {"bus"}
  778938c0-6ccb-43bb-b092-ac95ebd50e62 | {"sky","road","bus"}
  a7aa1b1c-cd8d-41e5-8a07-a3f8525f333d | {"sky","road","car"}
  c012409b-1a7c-4f11-a93e-4a38bd72be57 | {"sky"}
  f3dcd25b-a782-4db4-8a6a-de2c70bec7b9 | {"road"}
(5 rows)

```

```sql

> select * , array_length(tags,1) from test ;

                   id                  |         tags         | array_length
+--------------------------------------+----------------------+--------------+
  3aab348a-fbd4-47aa-8899-f932ebc897f2 | {"bus"}              |            1
  778938c0-6ccb-43bb-b092-ac95ebd50e62 | {"sky","road","bus"} |            3
  a7aa1b1c-cd8d-41e5-8a07-a3f8525f333d | {"sky","road","car"} |            3
  c012409b-1a7c-4f11-a93e-4a38bd72be57 | {"sky"}              |            1
  f3dcd25b-a782-4db4-8a6a-de2c70bec7b9 | {"road"}             |            1
(5 rows)

```

```sql
> select * from test where tags = all ( select array['bus']);

                   id                  |  tags
+--------------------------------------+---------+
  3aab348a-fbd4-47aa-8899-f932ebc897f2 | {"bus"}
(1 row)


> select * from test where tags = any ( select array['bus']);

                   id                  |  tags
+--------------------------------------+---------+
  3aab348a-fbd4-47aa-8899-f932ebc897f2 | {"bus"}
(1 row)


```


```sql
// array_position(tags , 'bus') -> 回傳 'bus' 在 tags 陣列出現的位置
> select * from test where array_position(tags , 'bus') > 0;

                   id                  |         tags
+--------------------------------------+----------------------+
  3aab348a-fbd4-47aa-8899-f932ebc897f2 | {"bus"}
  778938c0-6ccb-43bb-b092-ac95ebd50e62 | {"sky","road","bus"}
(2 rows)

Time: 2.3471ms
```

