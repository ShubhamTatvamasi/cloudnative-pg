# connect

Get Secrets:
```bash
kubectl -n cnpg-system \
  get secret postgres-app -o yaml | \
  yq '.data |= with_entries(.value |= @base64d)'

# superuser
kubectl -n cnpg-system \
  get secret postgres-superuser -o yaml | \
  yq '.data |= with_entries(.value |= @base64d)'
```

Connect via `postgres-rw-pooler` service as `super` user:
```bash
PGPASSWORD=$(kubectl get secret postgres-superuser \
  -n cnpg-system \
  -o jsonpath='{.data.password}' | base64 -d)

kubectl run psql-client \
  --rm -it \
  --restart=Never \
  --image=shubhamtatvamasi/psql \
  -n cnpg-system \
  --env="PGPASSWORD=$PGPASSWORD" \
  -- psql -h postgres-rw-pooler -U postgres -d postgres
```

Connect via `postgres-rw-pooler` service as `app` user:
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

