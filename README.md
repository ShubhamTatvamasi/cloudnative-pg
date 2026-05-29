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

