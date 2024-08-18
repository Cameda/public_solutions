# Установка cert-manager

```
helm repo add jetstack https://charts.jetstack.io --force-update
```
```
helm upgrade --install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.15.2 \
  --set crds.enabled=true
```