# Postgresql

Показать базы данных:
```
\l
or
\l+
```

Показать пользователей баз данных:
```
\du
or
\du+
```

```
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA public FROM ryan;
REVOKE ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public FROM ryan;
REVOKE ALL PRIVILEGES ON ALL FUNCTIONS IN SCHEMA public FROM ryan;
REVOKE ALL PRIVILEGES ON DATABASE <my_db> FROM <my_user>;


```

ALTER USER user_name WITH PASSWORD 'new_password';


``` sql
GRANT USAGE ON SCHEMA rdmrwa_outrrm TO rwa_user;
GRANT ALL ON ALL TABLES IN SCHEMA rdmrwa_outrrm TO rwa_user;
GRANT ALL ON ALL SEQUENCES IN SCHEMA rdmrwa_outrrm TO rwa_user;
ALTER DEFAULT PRIVILEGES FOR ROLE rwa_user IN SCHEMA rdmrwa_outrrm GRANT ALL ON TABLES TO rwa_user;
ALTER DEFAULT PRIVILEGES FOR ROLE rwa_user IN SCHEMA rdmrwa_outrrm GRANT ALL ON SEQUENCES TO rwa_user;
```
