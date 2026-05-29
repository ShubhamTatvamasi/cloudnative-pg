# SQL

List all roles:
```sql
SELECT
    rolname,
    rolsuper,
    rolcreatedb,
    rolcreaterole,
    rolcanlogin
FROM pg_roles
ORDER BY rolname;
```

Create a `test_user`:
```sql
CREATE ROLE test_user
LOGIN
PASSWORD 'test_user';
```
