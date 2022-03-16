# Add Partitioning Mysql Example

## Link documents: https://dev.mysql.com/doc/mysql-partitioning-excerpt/8.0/en/partitioning-types.html

## Procedure create table with partition by range example
```
DROP PROCEDURE IF EXISTS  proc_create_table_demo;
create PROCEDURE proc_create_table_demo()
BEGIN
	DROP TABLE IF EXISTS demo;
	CREATE TABLE demo (
	  `id` varchar(50) NOT NULL,
	  `datetime` int unsigned NOT NULL
	)
	PARTITION BY RANGE( datetime )(
		PARTITION partition_1 VALUES LESS THAN (1601510400));
END;
call  proc_create_table_demo();
```

## Procedure add partition by range example
```
DROP PROCEDURE IF EXISTS proc_add_partition; 
create PROCEDURE proc_add_partition(in tablename varchar(30), in date_time int unsigned)
Begin
	DECLARE  date_str varchar(20);
	DECLARE exit handler for SQLEXCEPTION
	BEGIN
		GET DIAGNOSTICS CONDITION 1 @sqlstate = RETURNED_SQLSTATE, 
		@errno = MYSQL_ERRNO, @text = MESSAGE_TEXT;
		SET @full_error = CONCAT("ERROR ", @errno, " (", @sqlstate, "): ", @text);
		SELECT @full_error;
	END;
	set date_str = from_unixtime(date_time,'%d_%m_%Y');
	set @sql=CONCAT('ALTER TABLE ', tablename ,' ADD PARTITION ( PARTITION p', date_str ,' VALUES LESS THAN (', date_time ,'))');
	PREPARE stmt from @sql;
	EXECUTE stmt;
end;


```

### Call procedure add partition to table demo
```
call proc_add_partition('demo', UNIX_TIMESTAMP()-UNIX_TIMESTAMP() mod 86400 + 86400);

call proc_add_partition('demo', UNIX_TIMESTAMP()-UNIX_TIMESTAMP() mod 86400 + 86400*2);
```