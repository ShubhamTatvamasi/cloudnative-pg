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
  storage:
    size: 20Gi
EOF
```

Get Secrets:
```bash
kubectl get secret postgres-app -o yaml | \
yq '.data |= with_entries(.value |= @base64d)'
```

