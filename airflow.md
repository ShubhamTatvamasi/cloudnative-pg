# Airflow

```bash
export PGPASSWORD=$(kubectl get secret postgres-app \
  -n cnpg-system \
  -o jsonpath='{.data.password}' | base64 -d)
```

```bash
kubectl create secret generic airflow-metadata-connection \
  --from-literal=connection="postgresql+psycopg2://app:${PGPASSWORD}@postgres-rw-pooler.cnpg-system:5432/airflow_db" \
  --dry-run=client -o yaml > /tmp/airflow-metadata-connection.yaml
```

```bash
cat /tmp/airflow-metadata-connection.yaml | \
  yq '.data |= with_entries(.value |= @base64d)'
```
