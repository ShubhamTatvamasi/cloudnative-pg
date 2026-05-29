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

Connect to DB:
```bash
kubectl run psql-client \
  --rm -it \
  --restart=Never \
  --image=shubhamtatvamasi/psql \
  -n cnpg-system \
  --env="PGPASSWORD=test_user" \
  -- psql -h postgres-rw-pooler -U test_user -d test_db
```
