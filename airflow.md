# Airflow

Create a secret for airflow database:
```bash
kubectl create secret generic airflow-basic-auth \
  --type=kubernetes.io/basic-auth \
  --from-literal=username=airflow \
  --from-literal=password=airflow \
  --dry-run=client -o yaml > /tmp/airflow-basic-auth.yaml
```

Seale it:
```
kubeseal \
  --namespace cnpg-system \
  --controller-name=sealed-secrets \
  --controller-namespace=sealed-secrets \
  --format yaml \
  < /tmp/airflow-basic-auth.yaml > /tmp/airflow-basic-auth-sealedsecret.yaml
```


---

```bash
PGPASSWORD=$(kubectl get secret postgres-app \
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
