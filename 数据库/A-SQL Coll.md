## PostgreSQL数据库常用操作

```sql
--查看索引
select * from pg_indexes where tablename='tbname';     
--或者    
select * from pg_statio_all_indexes where relname='tbname';

--批量修改姓名
select 'ALTER TABLE ' || table_name || ' OWNER TO yourowner;' from information_schema.tables where table_schema='public';

--查看空间参考
SELECT * from geometry_columns;
--修改空间参考
select UpdateGeometrySRID('tablename', 'geom', 4326);
```

