# cert-manager

Добавить репозиторий:
``` bash
helm repo add jetstack https://charts.jetstack.io
```

Установка:
``` bash
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.7.1 \
  --set installCRDs=true
```
