# Oracle

### Commands

Если база данных не доступна:

Выполняем по порядку:
``` sql
sqlplus / as sysdba
startup
```

Запустить listner:

``` bash
lsnrctl start
```

Проверить доступна ли база:

``` sql
select status from v$instance;
```

Создать пользователя:

``` sql
sqlplus / as sysdba
create user USER_NAME identified by 'PASSWORD';
```

### Prometheus exporter

Создаем пользователя и выдаём права:

``` sql
sqlplus / as sysdba
create user exporter identified by 'PASSWORD';
grant sysdba to exporter;
grant create session to exporter;
```

Необходимые права для работы Prometheus Exporter (пользователь exporter):

``` sql
GRANT SELECT ON sys.dba_temp_files TO exporter; 
GRANT SELECT ON sys.dba_tablespaces TO exporter;  
GRANT SELECT ON sys.dba_tablespace_usage_metrics TO exporter;  
GRANT SELECT ON sys.dba_data_files TO exporter; 
GRANT SELECT ON sys.dba_free_space TO exporter; 
GRANT SELECT ON sys.v_$process TO exporter; 
GRANT SELECT ON sys.v_$session TO exporter; 
GRANT SELECT ON sys.v_$session_longops TO exporter; 
GRANT SELECT ON sys.v_$sysstat TO exporter; 
GRANT SELECT ON sys.v_$parameter TO exporter; 
GRANT SELECT ON sys.v_$instance TO exporter; 
GRANT SELECT ON sys.v_$datafile TO exporter; 
GRANT SELECT ON sys.v_$system_wait_class TO exporter; 
GRANT SELECT ON sys.v_$resource_limit TO exporter; 
GRANT SELECT ON sys.v_$waitclassmetric TO exporter; 
GRANT SELECT ON sys.v_$asm_file TO exporter; 
GRANT SELECT ON sys.v_$asm_diskgroup TO exporter; 
GRANT SELECT ON sys.v_$asm_diskgroup_stat TO exporter; 
GRANT SELECT ON sys.v_$asm_disk_stat TO exporter; 
GRANT SELECT ON sys.v_$asm_alias TO exporter; 
GRANT SELECT ON sys.gv_$sort_segment TO exporter;
```

### Extra

``` sql
SELECT sys_context('userenv','instance_name') FROM dual;
```
