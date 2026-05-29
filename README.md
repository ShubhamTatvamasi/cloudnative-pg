# cloudnative-pg

Add helm repo:
```bash
helm repo add cnpg https://cloudnative-pg.io/charts/
```

Install cloudnative-pg:
```bash
helm upgrade -i cnpg cnpg/cloudnative-pg \
  --namespace cnpg-system \
  --create-namespace
```

Deploy a postgresql cluster:
```bash
kubectl apply -f - << EOF
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: postgres
  namespace: cnpg-system
spec:
  instances: 3
  enableSuperuserAccess: true
  storage:
    size: 20Gi
EOF
```

Get Secrets:
```bash
kubectl -n cnpg-system \
  get secret postgres-app -o yaml | \
  yq '.data |= with_entries(.value |= @base64d)'
```

Create a pgbouncer pooler:
```bash
kubectl apply -f - << EOF
apiVersion: postgresql.cnpg.io/v1
kind: Pooler
metadata:
  name: postgres-rw-pooler
  namespace: cnpg-system
spec:
  cluster:
    name: postgres
  instances: 3
  type: rw
  pgbouncer:
    poolMode: transaction
    parameters:
      max_client_conn: "1000"
      default_pool_size: "25"
EOF
```

Connect via `postgres-rw-pooler` service:
```bash
PGPASSWORD=$(kubectl get secret postgres-app \
  -n cnpg-system \
  -o jsonpath='{.data.password}' | base64 -d)

kubectl run psql-client \
  --rm -it \
  --restart=Never \
  --image=shubhamtatvamasi/psql \
  -n cnpg-system \
  --env="PGPASSWORD=$PGPASSWORD" \
  -- psql -h postgres-rw-pooler -U app -d app
```

Create a test DB:
```
kubectl apply -f - << EOF
apiVersion: postgresql.cnpg.io/v1
kind: Database
metadata:
  name: test-db
  namespace: cnpg-system
spec:
  cluster:
    name: postgres
  name: test-db
  owner: test-db
EOF
```


