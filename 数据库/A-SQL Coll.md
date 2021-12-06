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

--创建普通数据库
CREATE DATABASE dbname;
--给数据库添加空间扩展,切换刚才创建的数据库下，运行下面的命令即可：
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION postgis_tiger_geocoder;
CREATE EXTENSION address_standardizer;


--根据名字，查找重复
select * from egis_publish_wrms where name in (select name from egis_publish_wrms group by name having count(name) > 1) ;

--查找重复的name
SELECT * FROM ( (SELECT name FROM egis_publish_wrms GROUP BY name HAVING COUNT(name)>1) ) a

--查找name重复的数据的id最小的一条
SELECT * FROM ( (SELECT MIN(id) ids FROM egis_publish_wrms GROUP BY NAME HAVING COUNT(name)>1) ) b

--删除name重复项，保留其中id最小的一条
SELECT * FROM egis_publish_wrms
WHERE 1=1
    AND name in (SELECT * FROM ( (SELECT name FROM egis_publish_wrms GROUP BY name HAVING COUNT(name)>1) ) a)
    AND id not in (SELECT * FROM ( (SELECT MIN(id) ids FROM egis_publish_wrms GROUP BY NAME HAVING COUNT(name)>1) ) b)
	
	
-- 一张表插入另一张表	
INSERT INTO TABLE1(
id,
name,
note
)
SELECT
id,
name,
'i AM NOTE'
FROM TABLE2 WHERE TABLE2.NAME LIKE '100%';

--查看schem下所有表，
select relname, pg_size_pretty(pg_total_relation_size(relid)) from pg_stat_user_tables 
where schemaname='standard' 
order by pg_relation_size (relid) desc;

--设置坐标参考
select * from geometry_columns;
 
select UpdateGeometrySRID('bas_shelter','shape',4490);

--查询主键信息
SELECT
    pg_constraint.conname AS pk_name
FROM
    pg_constraint
INNER JOIN pg_class ON pg_constraint.conrelid = pg_class.oid
WHERE
    pg_class.relname = 'app_account'
AND pg_constraint.contype = 'p';
--查询主键的详细信息
SELECT
    pg_constraint.conname AS pk_name,
    pg_attribute.attname AS colname,
    pg_type.typname AS typename
FROM
    pg_constraint
INNER JOIN pg_class ON pg_constraint.conrelid = pg_class.oid
INNER JOIN pg_attribute ON pg_attribute.attrelid = pg_class.oid
AND pg_attribute.attnum = pg_constraint.conkey [ 1 ]
INNER JOIN pg_type ON pg_type.oid = pg_attribute.atttypid
WHERE
    pg_class.relname = 'app_account'
AND pg_constraint.contype = 'p';

--批量改schema下的所有表的所有者
select 'ALTER TABLE ' || table_name || ' OWNER TO basedb;' from information_schema.tables where table_schema='basedb';

--查schame下所有表
SELECT table_name from information_schema.tables where table_schema='sdata';

---shape
SELECT shape,longitude,latitude,updatetime,st_astext(shape)
from shl_shelter where shape is null and  longitude is not null ;

SELECT shape,longitude,latitude,updatetime,'POINT('||longitude||' '||latitude||')'
from sch_school where shape is null and longitude is not null and updatetime > '2020-12-04 ';

update shl_shelter set shape = st_geomfromtext('POINT('||longitude||' '||latitude||')',4490)
where shape is null and longitude is not null ;


```
### 备份|回复
```sql
--schema导出导入
pg_dump --host 127.0.0.1 --port 5432 --username "postgres" --role "postgres"  --format custom --blobs  --encoding UTF8 -W --verbose --file "/home/postgres/pg_backup/stdss_emergencydev_0512.backup" --schema "emergencydev" "stdss"

pg_restore --host 127.0.0.1 --port 5432 --username "postgres" --dbname "stdss" --role "postgres" --schema="emergencydev" --password --verbose "/home/postgres/pg_backup/ytmisdb_emergencydev_0512.backup"
```
```shell
一、备份
1.切换到postgres用户
$ sudo su - postgres

2.根据需求不同，选取不同的备份方式
(1).备份数据库（包含创建数据库）
$ pg_dump -C db_name > db_bak.sql

(2).备份数据库内容（不包含创建数据库）
$ pg_dump db_name > db_content_bak.sql

(3).备份schema（命名空间/模式）和数据（包含创建schema）
$ pg_dump -n "schema_name" db_name > schema_bak.sql

(4).备份表和数据（包含建表）
$ pg_dump -t "schema_name.table_name" db_name > table_bak.sql

(5).备份表内数据（不包建表语句）
$ pg_dump -a -t "schema_name.table_name" db_name > table_content_bak.sql

二、恢复
1.切换到postgres用户
$ sudo su - postgres

2.恢复数据
(1).恢复数据库及其内容（数据库不存在）
$ psql -e < db_bak.sql
(2).恢复数据库内容（数据库必须已存在，且库中不存在备份文件中将要的创建的对象）
psql -e db_name < bak.sql

3.免去交互输入密码
PGPASSWORD=123456 psql -U username -e db_name < bak.sql
```

### 循环
```sql
CREATE 
	OR REPLACE FUNCTION insert_record ( ) RETURNS void AS $$ DECLARE
	temprow RECORD;
tempdata RECORD;
BEGIN
		FOR temprow IN ( SELECT * FROM emap_user_org )
		LOOP
		FOR tempdata IN ( SELECT * FROM gat_gymnasium_p WHERE "pac" = temprow.district_code )
		LOOP
		INSERT INTO emap_operate_record ( ID, CONTENT, effect, node_id, node_name, org_code, org_name, user_id, user_name, create_time, update_time, pac, upt )
	VALUES
		(
			tempdata.ID,
			concat_ws ( ',', tempdata.NAME, tempdata.address ),
			'1',
			'ca9e9623-d7ff-4dbd-9815-b2f596dcf039',
			'体育',
			temprow.org_code,
			temprow.full_org_name,
			temprow.user_id,
			temprow.login_name,
			'2021-01-11',
			'2021-01-11',
			tempdata.pac,
			'0' 
		);
	
END LOOP;

END LOOP;

END;
$$ LANGUAGE plpgsql;

SELECT insert_record();


SELECT
	insert_user_roles ( );;
```

