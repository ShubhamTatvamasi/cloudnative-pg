# Airflow

```
export PGPASSWORD=$(kubectl get secret postgres-app \
  -n cnpg-system \
  -o jsonpath='{.data.password}' | base64 -d)
```

```
kubectl create secret generic airflow-metadata-secret \
  --from-literal=connection="postgresql+psycopg2://app:${PGPASSWORD}@postgres-rw-pooler.cnpg-system:5432/airflow_db" \
  --dry-run=client -o yaml > /tmp/airflow-metadata-secret.yaml
```
